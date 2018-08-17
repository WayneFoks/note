# 网络协议

## 推荐阅读

> [腾讯技术工程官方号 - 科普：QUIC 协议原理分析](https://cloud.tencent.com/developer/article/1017235)

## 概览

![](.gitbook/assets/image%20%2820%29.png)

**Google的SPDY和QUIC** 

![](.gitbook/assets/image%20%2828%29.png)

## 传输层

### TCP

### UDP

## 应用层

### SSL/TLS

### HTTP

## 网络层

## 新技术

### QUIC - Quick UDP Internet Connection

参考阅读：

> [腾讯技术工程官方号 - 科普：QUIC 协议原理分析](https://cloud.tencent.com/developer/article/1017235) [如何看待谷歌 Google 打算用 QUIC 协议替代 TCP/UDP？](https://www.zhihu.com/question/29705994)

* TCP往往直接内置到了操作系统内核当中，QUIC直接基于客户端实现，可以快速迭代
* 基于UDP的多路复用
* 极低的等待时延（类似于TCP Fast Open，O RTT）
* 前向纠错：TCP采用重传机制\(重传歧义\)，而QUIC采用纠错机制
* 连接迁移：一条 TCP 连接是由四元组标识的（源 IP，源端口，目的 IP，目的端口），QUIC由一个随机数标识，客户端切换网络可以不重连

### IPv6

