---
layout: post
title:  "lua-resty-lrucache 添加 flush_all 历程"
date:   2018-01-04 10:03:25 +0800
author: hdu.yang
---

## 起因
前段时间想给 openresty 开源做点贡献，就去找一些 `lua-resty-xxx` 的 `todo list` 实践，顺便也可以看下项目本身的源码，这样就能带着目的去看源码了。于是顺手找到了 `lua-resty-lrucache` 的工程，这个工程我们之前一直在使用，它是用于 `worker` 内部的 `key-value` 缓存，并且采用简单的 `lru` 策略进行淘汰。

## 具体任务
add new method **flush_all** for flushing out everything in the cache.

## 曲折历程
首先还是需要先看懂源码。该工程的实现包括设计了两个双向循环队列，分布用来表示 `cache_queue` 和 `free_queue`，并且设计了`key2node` 以及 `node2key` 两个 `hash` 表，用来查找相应的 `node` 或者 `key`。由于当删除一个 `node` 的时候，对于 `lua` 的实现来说只是简单的 `key2node[key] = nil` 以及 `node2key[node] = nil` 的操作，而不是真正意义上的物理释放内存，这样一来，频繁的淘汰删除操作会导致内存增大，于是作者后来又设计了针对这个问题的 `ffi` 实现。不管是纯 `lua` 实现还是 `ffi` 的实现，总体的设计都是一致的。

由于作者之前已经实现了删除一个 `node` 的操作，对于 `flush_all` 的实现，我自然会采用一个 `while` 循环，然后从 `cache_queue` 中循环取出 `node` 并删除，然后加入 `free_queue` 列表。这其中包括修复了一些格式问题，这说明提交 `patch` 的代码需要严格规范。

后来另一个开发者觉得我只实现了纯 `lua` 的版本，于是在这的基础上帮我实现了 `ffi` 版本上的 `flush_all` 并且提交了相应的 `case`，我把它的代码也就顺便合并到了我的 `patch` 中。

原本以为可以顺利的合并进主分支了，结果一位大牛提出了一个将 `O(n)` 改进到 `O(1)` 的建议，这还真是我之前没仔细想的，直接将 `cache_queue` 链接到 `free_queue` 上。在改的时候我发现之前对两个队列的理解还不是很到位导致改的不对，经过多次查看源码我才真正理解作者的设计理念。
