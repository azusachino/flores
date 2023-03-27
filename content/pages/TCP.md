---
title: TCP
created: 2022-08-30 21:23:51
modified: 2023-03-27 10:29:32
aliases: [Transmission Control Protocol]
tags: [CS, Network, Protocol]
---

TCP (Transmission Control Protocol) is a network protocol that transfers your data over the internet from your device to a web server. You use the TCP protocol whenever you [chat to your friends on Skype](https://nordvpn.com/unblock/skype/), send emails, watch online videos, or simply browse the web.

TCP is connection-based, so it establishes a connection between the receiver and sender and maintains it while transferring data. It guarantees that the data arrives completely intact. Because of its reliability, TCP is the most popular network protocol.

## Header Structure (20 字节~)

- Source Port
- Destination Port
- SEQ Num: a number used to track the order of the packets being transmitted
- ACK Num: a number used to confirm the receipt of packets
- TCP Header Length: the length of the TCP header in 32-bit words
- Flags (SYN, ACK, FIN): various 1-bit flags that indicate the packet's purpose
  - SYN (synchronize sequence numbers)
  - ACK (acknowledge receipt)
  - FIN (finish transmitting data)
- Window size: the maximum amount of data that the receiving device is willing to accept in a single packet
- Checksum: a value used to verify the integrity of the packet
- Payload

![[../images/tcp-header-structure.png]]

SYN + ACK 保证被拆分的数据包，最终在接收端可以被拼接成完整的数据。

### The sliding window

解决了来自发送方的数据背压

![[../images/tcp-sliding-window.png]]

### The routes of a packet

![[../images/route-of-a-packet.png]]

## References

- [如何使用 Wireshark 分析 TCP 吞吐瓶颈](https://mp.weixin.qq.com/s/KXPF-9f_VYRnEgIe22bxkQ)
