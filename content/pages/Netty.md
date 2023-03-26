---
title: Netty
created: 2022-12-16 19:56:43
modified: 2023-03-26 11:19:47
tags: [CS, Java]
---

A famous networking framework written in [[Java]] .

![[../images/netty-server-flowchart.png]]

## Concepts

### ChannelHandler

ChannelInboundHandlerAdapter function executes order:

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

### ReplayingDecoder

1. ByteToMessageDecoder 封装类，在使用 Bytebuf 前会对容量进行检测
2. 可以用于多阶段合并 数据包

## References

- [netty 之 ServerBootstrap的启动流程](https://blog.csdn.net/developer_/article/details/78394827)
