---
layout: post
title:  "Nginx 的 Connection 的生命周期"
date:   2018-09-20 10:03:25 +0800
author: hdu.yang
---

### 我们尝试从一个完整的 Http 处理来解读 Nginx 的 Connection 的生命周期。每个 Http 大致会经过以下几个步骤：
* `ngx_event_accept` 接受客户端的 TCP 连接。这个处理阶段是对监听 socket 的读请求的响应，并且会在该阶段创建 `ngx_connection_t` 对象，然后每个 Nginx 的监听器会将新的连接对象告知给下一个处理阶段。对于 HTTP 连接来说就是 `ngx_http_init_connection(c)`

* `ngx_http_init_connection` 执行 HTTP 连接早期的初始化工作，并且创建 `ngx_http_connection_t` 对象，并将该对象作为 `ngx_connetion_t` 的 data 成员赋值。代理协议解析以及 `SSL` 握手也是在这个阶段开始进行的。

* `ngx_http_wait_request_handler` 是在 socket 接收到客户端发送数据后触发的，并且在该阶段创建了 `ngx_http_request_t` 对象并替换 `ngx_connection_t` 的 `data` 成员。

* 接下来是 `ngx_http_process_request_line` 阶段，该阶段尝试读取请求行数据，并作为 `ngx_connection_t` 的 `buffer` 成员赋值。事实上，该 `buffer` 也将请求头的数据大小考虑在内, 该 buffer 的大小初始化是由 `client_header_buffer_size` 指令控制的，如果不够大，则由 `large_client_header_buffers` 指令分配更大的空间

* 接下来自然是 `ngx_http_process_request_header` 阶段。该阶段是由 `ngx_http_process_request_line` 设置的下一个处理阶段。

* `ngx_http_core_run_phases` 阶段是在请求行和请求头都解析完成后执行的。该阶段负责 `ngx_http_post_read_phase` 到 `ngx_http_content_phase` 的执行过程，最后将请求阶段传递到过滤器中。该阶段还没必要将响应数据吐给客户端，而是将响应数据通过 `buffer` 存起来，等待 `ngx_http_final_request` 再吐出去。

* 当产生所有的响应数据或者错误数据后，`ngx_http_final_request` 阶段开始执行，如果在该阶段数据没有完全发送给客户端，那么还需要设置 `ngx_http_writer` 来完成最后的数据发送。

* 当所有的响应数据都发送给客户端后，`ngx_http_final_connection` 开始执行。对于设置有 `keepalive` 特性的执行 `ngx_http_set_keepalive` 来结束整个请求，并等待下一个请求的到来，否则执行 `ngx_http_close_connection` 来结束整个请求和整个连接。
