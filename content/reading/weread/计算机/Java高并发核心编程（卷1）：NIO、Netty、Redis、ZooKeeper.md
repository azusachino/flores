---
doc_type: weread-highlights-reviews
bookId: "37445673"
author: 尼恩编著
cover: https://weread-1258476243.file.myqcloud.com/weread/cover/93/YueWen_37445673/t7_YueWen_37445673.jpg
reviewCount: 2
noteCount: 12
isbn: 9787111677581
category: 计算机-编程设计
tags:
  - Book
  - Weread
created: 2022-12-19 22:21:15
modified: 2022-12-19 22:21:15
---

## Java高并发核心编程（卷1）：NIO、Netty、Redis、ZooKeeper

![37445673](https://weread-1258476243.file.myqcloud.com/weread/cover/93/YueWen_37445673/t7_YueWen_37445673.jpg)
- 书名: Java高并发核心编程（卷1）：NIO、Netty、Redis、ZooKeeper
- 作者: 尼恩编著
- 简介: 本书从操作系统底层的IO原理入手讲解Java高并发核心编程知识，同时提供高性能开发的实战案例，是一本Java高并发编程的基础原理和实战图书。本书共分为15章。第1～4章为高并发基础，浅显易懂地剖析高并发IO的底层原理，图文并茂地介绍Java异步回调模式，细致地讲解Reactor高性能模式。这些原理方面的基础知识非常重要，会为读者打下坚实的基础，也是日常开发Java后台应用时解决实际问题的金钥匙。第5～8章为Netty原理和实战，是本书的重中之重，主要介绍高性能通信框架Netty、Netty的重要组件、单体IM的实战设计和模块实现。第9～12章从TCP、HTTP入手，介绍客户端与服务端、服务端与服务端之间的高性能HTTP通信和WebSocket通信。第13～15章对ZooKeeper、Curator API、Redis、Jedis API的使用进行详尽的说明，以提升读者设计和开发高并发、可扩展系统的能力。本书兼具基础知识和实战案例，既可作为对Java NIO、高性能IO、高并发编程感兴趣的大专院校学生以及初/中级Java工程师的自学图书，也可作为在生产项目中需要用到Netty、Redis、ZooKeeper三大框架的架构师或项目人员的参考书。
- 出版时间: 2021-03-01 00:00:00
- ISBN: 9787111677581
- 分类: 计算机-编程设计
- 出版社: 机械工业出版社

## 高亮划线

### 2.1 IO读写的基本原理


> [!quote] ⏱ 2022-12-09 23:42:42
> 简单来说，应用程序的IO操作实际上不是物理设备级别的读写，而是缓存的复制。read和write两大系统调用都不负责数据在内核缓冲区和物理设备（如磁盘、网卡等）之间的交换。这个底层的读写交换操作是由操作系统内核（Kernel）来完成的。
 


### 2.2 四种主要的IO模型


> [!quote] ⏱ 2022-12-12 08:07:48
> 阻塞IO指的是需要内核IO操作彻底完成后才返回到用户空间执行用户程序的操作指令。
 


> [!quote] ⏱ 2022-12-12 08:09:53
> 在Linux系统中，新的系统调用为select/epoll系统调用。通过该系统调用，一个用户进程（或者线程）可以监视多个文件描述符，一旦某个描述符就绪（一般是内核缓冲区可读/可写），内核就能够将文件描述符的就绪状态返回给用户进程（或者线程），用户空间可以根据文件描述符的就绪状态进行相应的IO系统调用。
 


### 3.1 Java NIO简介


> [!quote] ⏱ 2022-12-13 07:45:38
> 面向字节流或字符流的IO操作总是以流式的方式顺序地从一个流（Stream）中读取一个或多个字节，因此，我们不能随意改变读取指针的位置。在NIO操作中则不同，NIO中引入了Channel和Buffer的概念。面向缓冲区的读取和写入只需要从通道读取数据到缓冲区中，或将数据从缓冲区写入通道中。NIO不像OIO那样是顺序操作，它可以随意读取Buffer中任意位置的数据。
 


### 3.3 详解NIO Buffer类的重要方法


> [!quote] ⏱ 2022-12-13 20:00:19
> 在读完之后是否可以立即对缓冲区进行数据写入呢？答案是不能。现在还处于读模式，我们必须调用Buffer.clear()或Buffer.compact()方法，即清空或者压缩缓冲区，将缓冲区切换成写模式，让其重新可写。
 


> [!quote] ⏱ 2022-12-13 20:00:35
> mark()和reset()两个方法是配套使用的：Buffer.mark()方法将当前position的值保存起来放在mark属性中，让mark属性记住这个临时位置；然后可以调用Buffer.reset()方法将mark的值恢复到position中
 


### 5.2 解密Netty中的Reactor模式


> [!quote] ⏱ 2022-12-15 16:19:05
> Reactor模式中IO事件的处理流程大致分为4步，具体如下：第1步：通道注册。IO事件源于通道（Channel），IO是和通道（对应于底层连接而言）强相关的。一个IO事件一定属于某个通道。如果要查询通道的事件，首先就要将通道注册到选择器。第2步：查询事件。在Reactor模式中，一个线程会负责一个反应器（或者SubReactor子反应器），不断地轮询，查询选择器中的IO事件（选择键）。第3步：事件分发。如果查询到IO事件，则分发给与IO事件有绑定关系的Handler业务处理器。第4步：完成真正的IO操作和业务处理，这一步由Handler业务处理器负责。
 


> [!quote] ⏱ 2022-12-15 19:17:53
> （1）反应器（或者SubReactor子反应器）和通道之间是一对多的关系：一个反应器可以查询很多个通道的IO事件。（2）通道和Handler处理器实例之间是多对多的关系：一个通道的IO事件可以被多个Handler实例处理；一个Handler处理器实例也能绑定到很多通道，处理多个通道的IO事件。
 


### 5.5 详解Handler


> [!quote] ⏱ 2022-12-15 20:01:17
> （1）handlerAdded()：当业务处理器被加入到流水线后，此方法将被回调。也就是在完成ch.pipeline().addLast(handler)语句之后会回调handlerAdded()。（2）channelRegistered()：当通道成功绑定一个NioEventLoop反应器后，此方法将被回调。（3）channelActive()：当通道激活成功后，此方法将被回调。通道激活成功指的是所有的业务处理器添加、注册的异步任务完成，并且与NioEventLoop反应器绑定的异步任务完成。（4）channelInactive()：当通道的底层连接已经不是ESTABLISH状态或者底层连接已经关闭时，会首先回调所有业务处理器的channelInactive()方法。（5）channelUnregistered()：通道和NioEventLoop反应器解除绑定，移除掉对这条通道的事件处理之后，回调所有业务处理器的channelUnregistered ()方法。（6）handlerRemoved()：Netty会移除掉通道上所有的业务处理器，并且回调所有业务处理器的handlerRemoved()方法。
 


### 5.6 详解Pipeline


> [!quote] ⏱ 2022-12-15 20:02:38
> ChannelHandlerContext中包含了许多方法，主要可以分为两类：第一类是获取上下文所关联的Netty组件实例，如所关联的通道、所关联的流水线、上下文内部Handler业务处理器实例等；第二类是入站和出站处理方法。
 


### 5.8 Netty的零拷贝


> [!quote] ⏱ 2022-12-16 13:54:02
> 在Netty接收和发送ByteBuffer的过程中会使用直接内存进行Socket通道读写，使用JVM的堆内存进行业务处理，会涉及直接内存、堆内存之间的数据复制。
 


### 5.9 EchoServer的实战案例


> [!quote] ⏱ 2022-12-19 17:13:00
> Netty读取底层的二进制数据，填充到msg时，msg是ByteBuf类型，然后经过流水线，传入第一个入站处理器；每一个节点处理完后，将自己的处理结果（类型不一定是ByteBuf）作为msg参数不断向后传递。
 



## 读书笔记


### 3.2 详解NIO Buffer类及其属性
 

#### 章节评论 No.1 ⏱ 2022-12-13 07:52:46

cap是底层数组的大小，limit是还能进行多少次读写操作，position是读写指针。



### 4.1 Reactor模式的重要性

#### 划线评论 No.1 ⏱ 2022-12-15 11:28:36

> [!quote]
> Reactor模式

Reactor模式就是对epoll的封装，主线程注册到selector，然后管理子线程池，去调度、派发、处理IO事件；
Reactor线程的职责：负责响应IO事件，并分发到Handlers处理器
Handlers的职责：非阻塞的执行业务处理逻辑
 



## 本书评论

