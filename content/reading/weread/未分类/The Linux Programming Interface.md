---
doc_type: weread-highlights-reviews
bookId: CB_2Sa1vY1uzAE86dA6cN
author: Michael Kerrisk
cover: https://res.weread.qq.com/wrepub/CB_2Sa1vY1uzAE86dA6cN_parsecover
reviewCount: 5
noteCount: 2
isbn: null
category: null
tags:
  - Book
  - Weread
created: 2022-12-19 22:21:15
modified: 2022-12-19 22:21:15
---

## The Linux Programming Interface

![CB_2Sa1vY1uzAE86dA6cN](https://res.weread.qq.com/wrepub/CB_2Sa1vY1uzAE86dA6cN_parsecover)
- 书名: The Linux Programming Interface
- 作者: Michael Kerrisk
- 简介: 
- 出版时间: 
- ISBN: 
- 分类: 
- 出版社: No Starch Press, Inc.

## 高亮划线

### Process versus kernel views of the system


> [!quote] ⏱ 2022-12-14 11:34:46
> a running system has one kernel that knows and controls everything. The kernel facilitates the running of all processes on the system. The kernel decides which process will next obtain access to the CPU, when it will do so, and for how long. The kernel maintains data structures containing information about all running processes and updates these structures as processes are created, change state, and terminate. The kernel maintains all of the low-level data structures that enable the filenames used by programs to be translated into physical locations on the disk. The kernel also maintains data structures that map the virtual memory of each process into the physical memory of the computer and the swap area(s) on disk. All communication between processes is done via mechanisms provided by the kernel. In response to requests from processes, the kernel creates new processes and terminates existing processes. Lastly, the kernel (in particular, device drivers) performs all direct communication with input and output devices, transferring information to and from user processes as required.
 


### The Shell


> [!quote] ⏱ 2022-12-14 12:00:03
> I/O redirection, pipelines, filename generation (globbing), variables, manipulation of environment variables, command substitution, background command execution, and functions.
 



## 读书笔记


### The Core Operating System: The Kernel
 

#### 章节评论 No.1 ⏱ 2022-12-12 20:37:17

操作系统：提供一切用户可以在电脑上进行的操作；
Kernel：负责调度、管理计算机硬件所提供的各项资源。



### Kernel mode and user mode

#### 划线评论 No.1 ⏱ 2022-12-14 10:56:34

> [!quote]
> Certain operations can be performed only while the processor is operating in kernel mode. 

1. Executing the halt instruction to stop the system
2. Accessing the memory-management hardware
3. Initiating device I/O operations
 



### Process versus kernel views of the system

#### 划线评论 No.1 ⏱ 2022-12-14 11:32:29

> [!quote]
> A process operates in isolation; it can’t directly communicate with another process.A process can’t itself create a new process or even end its own existence.Finally, a process can’t communicate directly with the input and output devices attached to the computer.

进程之间相互隔离，无法直接与其他进程「交流」。
一个进程不能创建新的进程，也不能结束自己的生命周期；
一个进程也不直接与I/O设备做交互。
 



### Users

#### 划线评论 No.1 ⏱ 2022-12-14 12:04:55

> [!quote]
> Every user of the system has a unique login name (username) and a corresponding numeric user ID (UID). For each user, these are defined by a line in the system password file, /etc/passwd

GroupID, Home Directory, Login Shell --&gt; /etc/passwd
 



### Symbolic links

#### 划线评论 No.1 ⏱ 2022-12-14 17:24:29

> [!quote]
> a normal link is a filename-plus-pointer entry in a directory list, a symbolic link is a specially marked file containing the name of another file.

A symbolic link, also known as a symlink or a soft link, is a special type of file that points to another file or directory. Unlike a hard link, which directly links one file to another, a symbolic link simply stores the path to the target file or directory as text. This means that if the target file or directory is moved or deleted, the symbolic link will still point to its old location. Symbolic links can be useful for creating shortcuts to frequently-used files or for creating aliases for long or complex file paths.
 



## 本书评论

