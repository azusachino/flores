---
title: Redis
created: 2022-09-01 22:04:19
modified: 2023-03-27 10:29:10
tags: [CS, MiddleWare]
---

Redis 是一个开源、基于内存、使用 C 语言编写的 key-value 数据库，并提供了多种语言的 API。它的数据结构十分丰富，基础数据类型包括：string（字符串）、list（列表，双向链表）、hash（散列，键值对集合）、set（集合，不重复）和 sorted set（有序集合）。主要可以用于数据库、缓存、分布式锁、消息队列等...

## 单线程

首先要明确的是 Redis 单线程指的是**网络 IO**和**键值对读写**是由一个线程来完成的，但 Redis 持久化、集群数据等是由额外的线程执行的。

**IO 多路复用机制：使其在网络 IO 操作中能并发处理大量的客户端请求从而实现高吞吐率**

**Redis 是基于内存的，绝大部分请求都是内存操作，十分的迅速；**

**Redis 具有高效的底层数据结构，为优化内存，对每种类型基本都有两种底层实现方式；**

**主要执行过程是单线程，避免了不必要的上下文切换和资源竞争，不存在多线程导致的 CPU 切换和锁的问题；**

## References

- [Redis 多线程网络模型全面揭秘](https://mp.weixin.qq.com/s/pm2NsPzTO4lJQfGUC4-sJQ)
- [妈妈再也不担心我面试被 Redis 问得脸都绿了](https://mp.weixin.qq.com/s/vXBFscXqDcXS_VaIERplMQ)
- [带你走进 Redis](https://mp.weixin.qq.com/s/4bAPVdUr_XbIw9xFCtWhfw)
