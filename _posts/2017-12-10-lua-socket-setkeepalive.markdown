## 记一次 set_keepalive 的分析过程

### 起因
当我们将代码中打印的日志梳理后，我们发现 `error.log` 中存在类似这样的的日志，从打印的日志的意思上看是说连接处于可疑的状态，那究竟是什么连接呢，又为什么说可疑呢，带着这些疑问，我准备深入去了解下。
`2017/12/08 16:02:19 [error] 67954#0: *28897 [lua2]http.lua:809: request_uri(): connection in dubious state, client: 10.10.7.222, server:`

### 逻辑层代码
 ``` lua
 local ok, err = self:set_keepalive()                                                                                                       
 if not ok then                                                                                                                               
     ngx_log(ngx_ERR, err)                                                                                                                   
 end
 ```
根据 `http.lua` 的行号我发现这是连接在做完完整的请求发送接收后尝试 `set_keepalive` 不成功时报的错误。于是我先尝试 google 了下这个错误，结果搜到论坛里一个[帖子](https://groups.google.com/forum/#!topic/openresty/2CaQSKGPsWw)，大致看了下，了解了 connection 在进行 set_keepalive 前会进行状态的检查，对于 socket 中还有数据未读的情况会报这个错误，但这个只是报这个错误的可能原因，具体是什么原因还需要打开 `nginx` 的 `debug` 日志才可以定位，于是沿着这个思路，我首先开启了 `debug` 日志，重复之前必现该问题的步骤，`debug` 日志中多了一些详细日志如下：

![日志](http://bftest.b0.upaiyun.com/debug_set_keepalive.png)

第一眼我看到了 `unused`，所以我的第一反应是为什么 `free` 的时候有 `unused` 的数据，后来继续了解了下发现 `free` 的时候有 `unused` 是正常的，因为内存池分配的时候是以一定大小对齐分配的，而使用的内存未必刚好是这么多，因此回收的时候就可能会出现 `unused` 的内存，所以这个是正常现象。在这句日志之前还有两条日志意思是说 `set_keepalive` 前需要检查状态以及关闭 `fd` 的操作，既然都分析到这里了，也只能带着这些日志去源代码里搜索了。

``` cpp
ngx_log_debug0(NGX_LOG_DEBUG_HTTP, ev->log, 0,
"lua tcp socket keepalive close handler check stale events");
    n = recv(c->fd, buf, 1, MSG_PEEK);
if (n == -1 && ngx_socket_errno == NGX_EAGAIN) {
/* stale event */
if (ngx_handle_read_event(c->read, 0) != NGX_OK) {
goto close;
        }
return NGX_OK;
    }
close:
ngx_log_debug1(NGX_LOG_DEBUG_HTTP, ev->log, 0,
"lua tcp socket keepalive close handler: fd:%d", c->fd);
```
从代码中我们可以看到，所谓的检查就是尝试去 `receive` 下 1 个字节，如果连接中没有数据可读并且对方没有关闭连接的情况下，`set_keepalive` 就会返回 `NGX_OK`，否则就会出现之前的两条打印日志。所以说当连接是脏连接或者是对方关闭连接的时候就会出现最开始的那条日志。于是我们通过抓包来确认下究竟是连接中还有数据还是对方关闭了连接。最后，我们通过抓包发现是因为服务端主动关闭了连接才导致 `set_keepalive` 失败，于是真相大白。

![交互](http://bftest.b0.upaiyun.com/wireshrk_set_keepalive.png)
