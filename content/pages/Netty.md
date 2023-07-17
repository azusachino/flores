---
title: Netty
created: 2022-12-18 03:56:43
modified: 2023-07-05 09:36:08
tags: [CS, Java]
---

A famous networking framework written in [[Java]] 

每个 channel 内部都会创建一个 pipeline

![[../images/netty-workloop.png]]

## Framework

![[../images/netty-framework.png]]

## Bootstrap

![[../images/netty-server-flowchart.png]]

## Concepts

### Reactor

![[../images/hierachy-multi-thread-reactor-model.png]]

### `ChannelHandler`

`ChannelInboundHandlerAdapter` function executes order:

```sh
[main|handlerAdded]：被调用：handlerAdded()
[main|channelRegistered]：被调用：channelRegistered()
[main|channelActive]：被调用：channelActive()
[main|channelRead]：被调用：channelRead()
[main|channelReadComplete]：被调用：channelReadComplete()
[main|channelInactive]：被调用：channelInactive()
[main|channelUnregistered]：被调用: channelUnregistered()
[main|handlerRemoved]：被调用：handlerRemoved()
```

### Replaying Decoder

1. `ByteToMessageDecoder` 封装类，在使用 `Bytebuf` 前会对容量进行检测
2. 可以用于多阶段合并 数据包

## MISC

### Byte Buffer

In Netty, the ByteBuf instances are allocated from the memory pool, which is managed by the ByteBufAllocator interface. The ByteBufAllocator is responsible for allocating and deallocating the memory used by the ByteBuf instances.

By default, Netty uses a pooled allocator called PooledByteBufAllocator, which allocates memory from a pool of direct ByteBuffers. Direct ByteBuffers are allocated outside of the JVM heap, which can improve performance for I/O operations.

The PooledByteBufAllocator maintains a pool of ByteBuf instances, which are allocated and released as needed. When a ByteBuf is released, it is returned to the pool and can be reused for future allocations. This can reduce the overhead of allocating and deallocating memory, and can improve performance in high-concurrency scenarios.

Therefore, the ByteBuf instances in Netty are allocated from the memory pool managed by the ByteBufAllocator, which by default uses a pool of direct ByteBuffers.

### Prevent ByteBuffer from leak

`PooledByteBufAllocator` & `ResourceLeakDetector`

Netty uses a pooled allocator called PooledByteBufAllocator to allocate and manage ByteBuf instances. This allocator is responsible for allocating and deallocating the memory used by the ByteBuf instances, and it is designed to prevent memory leaks, especially those in direct memory.

The PooledByteBufAllocator maintains a pool of ByteBuf instances, which are allocated and released as needed. When a ByteBuf is released, it is returned to the pool and can be reused for future allocations. This can reduce the overhead of allocating and deallocating memory, and can prevent memory leaks caused by unused memory.

In addition, Netty provides a mechanism for monitoring and detecting memory leaks in direct memory. The ByteBuf class has a leak() method that can be used to detect leaks in direct memory. When a ByteBuf is leaked, it is not returned to the pool and can cause a memory leak. The leak() method can be used to detect these leaks and take appropriate action, such as logging an error message or shutting down the application.

Netty also provides a ResourceLeakDetector class that can be used to detect leaks in direct memory. This class can be configured to monitor ByteBuf instances and detect leaks based on configurable thresholds. When a leak is detected, the ResourceLeakDetector can log an error message or take other appropriate action.

In summary, Netty prevents memory leaks in direct memory by using a pooled allocator called PooledByteBufAllocator, which maintains a pool of ByteBuf instances that can be reused for future allocations. Netty also provides mechanisms for monitoring and detecting leaks in direct memory, such as the leak() method and the ResourceLeakDetector class.

## References

- [netty 之 ServerBootstrap 的启动流程](https://blog.csdn.net/developer_/article/details/78394827)
- [45 张图深度解析 Netty 架构与原理](https://cloud.tencent.com/developer/article/1754078)
- [Netty 源码解析](https://javadoop.com/post/netty-part-1)
