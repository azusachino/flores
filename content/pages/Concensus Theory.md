---
title: Concensus Theory
created: 2022-10-07 05:25:13
modified: 2023-03-26 11:19:47
aliases: [共识理论]
tags: [CS]
---

## CAP

- **Consistency**: consistency means all clients see the same data at the same time no matter which node they connect to.  
- **Availability**: availability means any client which requests data gets a response even if some of the nodes are down.  
- **Partition Tolerance**: a partition indicates a communication break between two nodes. Partition tolerance means the system continues to operate despite network partitions.

![[../images/cap-theorem.png]]

## References

- [EP26: Proxy vs reverse proxy](https://blog.bytebytego.com/p/ep25-proxy-vs-reverse-proxy)
