---
title: Abstract Queued Synchronizer
created: 2022-09-28 23:50:06
modified: 2023-03-26 11:18:49
aliases: [AQS]
tags: [CS, Java]
---

[[Java]] 现代化并发容器和锁的基石，AQS是一种提供了原子式管理同步状态、阻塞和唤醒线程功能以及队列模型的简单框架。

## Main Properties

- state
- Node

## ReentrantLock

![[../images/reentrantlock-synchronized.png]]

## References

- [从ReentrantLock的实现看AQS的原理及应用](https://mp.weixin.qq.com/s/sA01gxC4EbgypCsQt5pVog)
- [一行一行源码分析清楚AbstractQueuedSynchronizer](https://www.javadoop.com/post/AbstractQueuedSynchronizer)
- [一行一行源码分析清楚 AbstractQueuedSynchronizer (二)](https://www.javadoop.com/post/AbstractQueuedSynchronizer-2)
- [一行一行源码分析清楚 AbstractQueuedSynchronizer (三)](https://www.javadoop.com/post/AbstractQueuedSynchronizer-3)
