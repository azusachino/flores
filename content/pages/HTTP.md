---
title: HTTP协议解密
created: 2022-08-21 16:00:00
modified: 2023-03-26 11:19:47
tags: [CS, Network, Protocol]
---

## Structure

![[../images/http-and-more-layers.png]]

### Why Do We Need HTTP/3

- The **Real** Problems with HTTP/2
	- [server push doesn't really work](https://www.ctrl.blog/entry/http2-push-chromium-deprecation.html) in practice
	- [streams and prioritization are often badly implemented](https://github.com/andydavies/http2-prioritization-issues)
- TCP is the main protocol that provides crucial services such as **reliability and in-order delivery** to other protocols such as HTTP.
- The downside of TCP
	- QUIC is needed because TCP, which has been around since the early days of the Internet, **was not really built with maximum efficiency** in mind.
	- TCP also takes a **full network round trip** to complete before anything else can be done on a connection.
	- TCP sees all of the data it transports as a **single "file" or byte stream**, even if we're actually using it to transfer several files at the same time.
- ![image.png](../assets/image_1660101106192_0.png)
- 我们需要TCP/2，而在其研发途中诞生的就是QUIC了。

### What is QUIC

- QUIC is a generic transport protocol (much like TCP)
- QUIC 本可以替代TCP，成为新一代的基础协议；但因为现存的绝大部分互联网设备基本上不会动态更新，导致新的协议很难推广，于是才利用起已知的UDP协议，封装成了上层协议
- We can call QUIC as TCP/2 (a much advanced version of TCP)

### The Big Changes

- QUIC deeply integrates with TLS - 协议内置TLS
- QUIC supports multiple independent byte streams - 多路Stream
- QUIC uses connection IDs - 4元组变动时，无需重新建立连接
- QUIC uses frames - 数据包可以做到服务端与客户端自定义格式
- TLS Comparison
- ![image.png](../assets/image_1660204526230_0.png)
- Package Comparison
- ![image.png](../assets/image_1660102466024_0.png)

## References

- [HTTP/3 From A To Z: Core Concepts](https://www.smashingmagazine.com/2021/08/http3-core-concepts-part1/)
- [HTTP/3 Performance Features](https://www.smashingmagazine.com/2021/08/http3-performance-improvements-part2/)
- [Practical HTTP/3 Deployment Options](https://www.smashingmagazine.com/2021/09/http3-practical-deployment-options-part3/)
