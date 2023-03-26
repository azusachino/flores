---
title: Linux
created: 2022-08-24 08:00:00
modified: 2023-03-26 11:19:47
tags: [CS]
---

**Linux** is a family of open-source Unix-like operating systems based on the Linux kernel

## Registers

![[../images/linux-registers.png]]

## Debug Performance

![[../images/linux-performance-observability-tools.png]]

## 进程间通信

1. 管道（Pipe）：管道可用于具有亲缘关系进程间的通信，允许一个进程和另一个与它有共同祖先的进程之间进行通信。  
2. 命名管道（named pipe）：命名管道克服了管道没有名字的限制，因此，除具有管道所具有的功能外，它还允许无亲缘关系进程间的通信。命名管道在文件系统中有对应的文件名。命名管道通过命令mkfifo或系统调用mkfifo来创建。  
3. 信号（Signal）：信号是比较复杂的通信方式，用于通知接受进程有某种事件发生，除了用于进程间通信外，进程还可以发送信号给进程本身；linux除了支持Unix早期信号语义函数sigal外，还支持语义符合Posix.1标准的信号函数sigaction（实际上，该函数是基于BSD的，BSD为了实现可靠信号机制，又能够统一对外接口，用sigaction函数重新实现了signal函数）。Linux中可以使用kill -12 进程号，像当前进程发送信号，但前提是发送信号的进程要注册该信号。  
4. 消息（Message）队列：消息队列是消息的链接表，包括Posix消息队列system V消息队列。有足够权限的进程可以向队列中添加消息，被赋予读权限的进程则可以读走队列中的消息。消息队列克服了信号承载信息量少，管道只能承载无格式字节流以及缓冲区大小受限等缺限。  
5. 共享内存：使得多个进程可以访问同一块内存空间，是最快的可用IPC形式。是针对其他通信机制运行效率较低而设计的。往往与其它通信机制，如信号量结合使用，来达到进程间的同步及互斥。  
6. 内存映射（mapped memory）：内存映射允许任何多个进程间通信，每一个使用该机制的进程通过把一个共享的文件映射到自己的进程地址空间来实现它。
7. 信号量（semaphore）：主要作为进程间以及同一进程不同线程之间的同步手段。  
8. 套接口（Socket）：更为一般的进程间通信机制，可用于不同机器之间的进程间通信。起初是由Unix系统的BSD分支开发出来的，但现在一般可以移植到其它类Unix系统上：Linux和System V的变种都支持套接字。(containerd.sock)

## Process

### `fork`

可以将 `fork()` 认作对父进程的程序段、数据段、堆段以及栈段创建拷贝。

## Thread

`pthread` (posix)

## Network

### 优化建议

- 减少不必要的网络I/O
- 合并网络请求 (redis->multiGet)
- 调用者与被调用者的机器尽可能部署得近一些
- 内网调用不要使用外网域名
	- 外网接口慢
	- 带宽成本高
	- NAT单点瓶颈
- 调整网卡RingBuffer大小
	- 在Linux的整个网络栈中，RingBuffer充当了一个任务收发中转站的角色。对于接收过程，网卡负责往RingBuffer写入收到的数据帧，`ksoftirqd` 内核线程负责从中取走处理。**如果RingBuffer被填满了，网卡会直接丢弃后续的数据包，不做任何处理。**
- 减少内存拷贝 `mmap`, `sendfile`
- 使用 eBPF 绕开协议栈的本机 I/O
- 尽量少用 `recvfrom` 等进程阻塞的方式
- 使用成熟的网络库
- 使用 `Kernel-ByPass` 新技术
- 配置充足的端口范围
- 小心连接队列溢出
- 减少握手重试 `tcp_synack_retries`
- 如果请求频繁，启用短连接改用长连接
	- 节约握手开销
	- 规避队列满的问题
	- 端口数不一定出问题
- `TIME_WAIT` 的优化
	- 开启端口 `reuse` 和 `recycle`
- Scheduler
- Resource sharing is the key factor
- Time Accounting
	- The fundamental rule in the `Completely Fair Scheduler` is: _the task that ran less, will run next!_

### PING

![tcp-ping-differences](https://mmbiz.qpic.cn/mmbiz_png/FmVWPHrDdnkr3eLdxxIK0eujAOibyGS3aTZVia58LjFhfprOVOg8y4WclVdG4Y7tD8IGhPQia65nPey5TDWARgGNA/640?wx_fmt=png&wxfrom=5&wx_lazy=1)

-   `127.0.0.1` 是**回环地址**。`localhost`是**域名**，但默认等于 `127.0.0.1`。
-   `ping` 回环地址和 `ping` 本机地址，是一样的，走的是**lo0 "假网卡"**，都会经过网络层和数据链路层等逻辑，最后在快要出网卡前**狠狠拐了个弯**， 将数据插入到一个**链表**后就**软中断**通知 **ksoftirqd** 来进行**收数据**的逻辑，**压根就不出网络**。所以断网了也能 `ping` 通回环地址。Ï
-   如果服务器 `listen` 的是 `0.0.0.0`，那么此时用`127.0.0.1`和本机地址**都可以**访问到服务。

## Linux Service Example

`etc/systemd/system/iris.service`

```toml
[Unit]  
Description=iris service 
After=network.target  
StartLimitIntervalSec=0
StartLimitBurst=5  
StartLimitIntervalSec=10

[Service]  
Type=simple  
Restart=always  
RestartSec=1  
User=root  
ExecStart=/usr/bin/env /data/scripts/iris
  
[Install]  
WantedBy=multi-user.target
```

## 零拷贝技术

### UserSpace & KernelSpace

- `用户空间` 就是运行着用户编写的应用程序的虚拟内存空间
- `内核空间` 就是运行着操作系统代码的虚拟内存空间

在32位的操作系统中，每个进程都有 4GB 独立的虚拟内存空间，而 0 ~ 3GB 的虚拟内存空间就是用户空间 。

### MMAP (Memory Map)

#### 传统的文件读写

1. 把文件内容读取到内存中
2. 修改内存中的内容
3. 把内存中的数据写入到文件中

![[../images/normal-file-read-write.png]]

#### Sendfile

![[../images/sendfile-read-write.png]]

#### 使用MMAP读写文件

如果可以直接在用户空间读取 `页缓存`，就可以免去将 `页缓存` 数据复制到用户空间缓冲区的过程。

使用 `mmap` 系统调用可以将用户空间的虚拟内存地址与文件进行映射（绑定），对映射后的虚拟内存地址进行读写操作就如同对文件进行读写操作一样。

Linux 内核并不会主动把 `mmap` 映射的 `页缓存` 同步到磁盘，而是需要用户主动触发。同步 `mmap` 映射的内存到磁盘有 4 个时机：

-   调用 `msync` 函数主动进行数据同步（主动）。
-   调用 `munmap` 函数对文件进行解除映射关系时（主动）。
-   进程退出时（被动）。
-   系统关机时（被动）。

## Linux File Descriptor

![[../images/linux-file-descriptor.png]]

## References

- [Linux 网络性能的 15 个优化建议](https://mp.weixin.qq.com/s/GNG263EaQcUVmBff5Mzxnw)
- [A journey into the Linux scheduler](https://blog.maxgio.me/posts/linux-scheduler-journey/)
- [kernel.org](https://www.kernel.org/doc/html/v5.17/scheduler/index.html)
- [在线 Linux 源码](https://elixir.bootlin.com/linux/v5.17.9/source)
- [一文读懂零拷贝技术｜splice使用](https://mp.weixin.qq.com/s/4SFoh_Wmuvq83qVXHCLt4w)
- [Linux黑科技｜mmap实现详解](https://mp.weixin.qq.com/s/LcdmJHDj33AF_YwJZ2Yutg)
- [原来 mmap 这么简单](https://mp.weixin.qq.com/s/YjwxeLCwvJxXn7HPOF4Ayw)
- [一文读懂零拷贝技术](https://mp.weixin.qq.com/s/duJJTd-YU3yp6MolJ1cDNA)
- [EP17: Design patterns cheat sheet.](https://blog.bytebytego.com/p/ep17-design-patterns-cheat-sheet)
- [ping 可是个好东西啊！](https://mp.weixin.qq.com/s/rnA-qVCgv0G5NFEUCS3M6w)
- [硬核图解！断网了，还能ping通 127.0.0.1 吗？为什么?](https://mp.weixin.qq.com/s?__biz=Mzg5NDY2MDk4Mw==&mid=2247486417&idx=1&sn=c648ca9f2d33f77d69ae3c615c62b77e&scene=21)
