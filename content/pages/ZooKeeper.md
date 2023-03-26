---
title: ZooKeeper
created: 2022-11-17 03:03:23
modified: 2022-12-05 14:05:27
tags: [CS, Consensus]
---

ZooKeeper is a centralized service for maintaining configuration information, naming, providing distributed synchronization, and providing group services.

## ZAB Protocol

ZooKeeper Atomic Broadcast

客户端在使用 Zookeeper 服务时会随机连接到集群中的一个节点，所有的读请求都会由当前节点处理，而写请求会被路由给主节点并由主节点向其他节点广播事务，与 2PC 非常相似，如果在所有的节点中超过一半都返回成功，那么当前写请求就会被提交。

当主节点崩溃时，其他的 Replica 节点会进入崩溃恢复模式并重新进行选举，Zab 协议必须确保提交已经被 Leader 提交的事务提案，同时舍弃被跳过的提案，这也就是说当前集群中最新 ZXID 最大的服务器会被选举成为 Leader 节点；但是在正式对外提供服务之前，新的 Leader 也需要先与 Follower 中的数据进行同步，确保所有节点拥有完全相同的提案列表。

ZXID 是 Zab 协议中设计的事务编号，它是一个 64 位的整数，其中最低的 32 位是一个计数器，每当客户端修改 Zookeeper 集群状态时，Leader 都会以当前 ZXID 值作为提案的编号创建一个新的事务，在这之后会将当前计数器加一；ZXID 中高的 32 位表示当前 Leader 的任期，每当发生崩溃进入恢复模式，集群的 Leader 重新选举之后都会将 epoch 加一。

## References

- [详解分布式协调服务 ZooKeeper](https://draveness.me/etcd-introduction/)
- [Zab vs. Paxos](https://cwiki.apache.org/confluence/display/ZOOKEEPER/Zab+vs.+Paxos)
