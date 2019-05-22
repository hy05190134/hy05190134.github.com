---
layout: post
title:  "RTMP 消息介绍"
date:   2019-05-22 11:13:25 +0800
author: hdu.yang
---

### 消息
* 消息 (Message) 是 Rtmp 协议中的基本数据单元，但在实际传输过程中消息会以更小的消息块（Message Stream Chunk）单元进行传输
* Rtmp 协议规定了多种消息类型，每种消息类型对应一个类型编号 (Message Type ID)，其中 1-6 用于协议控制，8-9 用于音视频数据，15-20 用于发送AMF编码的命令，负责用户与服务器之间的交互，比如 Connect, Play, Pause 等消息

### 消息格式
* 消息由消息首部和消息内容两部分组成
* 消息首部又由四部分组成：标识消息类型的 Message Type ID，标识消息长度的 Payload Length，标识时间戳的 Timestamp，标识消息所属媒体流的 Stream ID。消息的报文结构如下图所示。

|Message Type ID|Payload Length|Timestamp|Stream ID|
| --------------|:------------:| -------:| ------: |
|1 byte         | 3 bytes      | 4 bytes | 3 bytes |

### 消息块格式
* 消息块也是由消息块首部和消息块内容两部分组成
* 消息块首部由三部分组成：Chunk Basice Header, Chunk Message Header, Extended Timestamp

|Chunk Basic Header|Chunk Message Header|Extended Timestamp|
| -----------------|:------------------:| ---------------: |
| 1-3 byte         | 0,3,5,11 bytes     | 0 or 4 bytes     |

* Rtmp 协议这样设计的目的是为了减少实际传输的数据大小，具体情况需要查看 Rtmp 协议文档

### 客户端播放过程
* 客户端播放过程主要包括 Dial（tcp 的三次握手），Handshake（客户端与服务端的三次握手），Connect（发送 Connect 命令）， CreateStream(建立网络流)，Play（在网络流上传输数据）

### 参考
* [RTMP 协议文档](http://wwwimages.adobe.com/content/dam/Adobe/en/devnet/rtmp/pdf/rtmp_specification_1.0.pdf)


