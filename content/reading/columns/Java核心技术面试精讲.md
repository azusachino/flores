---
title: Java核心技术面试精讲
created: 2023-01-05 17:00:00
modified: 2023-01-12 13:59:11
tags: [Column, Tech, Java]
author: 杨晓峰
read: 16
total: 39
---

Cloud Native Java

## Java 基础

### 谈谈你对 Java 平台的理解

Java 是一门面向对象的语言，有两个显著特性：Write Once, Run Anywhere; Garbage Collection。

Java 中的 Class Loader

- Bootstrap
- Extension
- Application

加载、验证、链接、初始化

### Java 是解释运行吗

Java 中存在 JIT (Just In Time Compiler)，在运行时将部分热点代码编译成机器码执行，在这种情况下就不是纯粹的解释运行了。

### Exception 与 Error 有什么区别

1. Error，系统错误，虚拟机出错，我们处理不了，也不需要我们来处理。
2. Exception，可以捕获的异常，且作出处理。也就是要么捕获异常并作出处理，要么继续抛出异常。
3. RuntimeException，经常性出现的错误，可以 捕获，并作出处理，可以不捕获，也可以不用抛出。

### 谈谈 final、finally、 finalize 有什么不同

- final 可以用来修饰类、方法、变量，分别有不同的意义，final 修饰的 class 代表不可以继承扩展，final 的变量是不可以修改的，而 final 的方法也是不可以重写的（override）。
- finally 则是 Java 保证重点代码一定要被执行的一种机制。我们可以使用 try-finally 或者 try-catch-finally 来进行类似关闭 JDBC 连接、保证 unlock 锁等动作。
- finalize 是基础类 `java.lang.Object` 的一个方法，它的设计目的是保证对象在被垃圾收集前完成特定资源的回收。finalize 机制现在已经不推荐使用，并且在 JDK 9 开始被标记为 deprecated。

### 强引用、软引用、弱引用、幻象引用有什么区别

不同的引用类型，主要体现的是**对象不同的可达性（reachable）状态和对垃圾收集的影响**。

- 强引用（Strong Reference），就是我们最常见的普通对象引用，只要还有强引用指向一个对象，就能表明对象还“活着”，垃圾收集器不会碰这种对象。
- 软引用（SoftReference），是一种相对强引用弱化一些的引用，可以让对象豁免一些垃圾收集，只有当 JVM 认为内存不足时，才会去试图回收软引用指向的对象。
- 弱引用（WeakReference）并不能使对象豁免垃圾收集，仅仅是提供一种访问在弱引用状态下对象的途径。这就可以用来构建一种没有特定约束的关系，比如，维护一种非强制性的映射关系，如果试图获取时对象还在，就使用它，否则重现实例化。
- 幻象引用，有时候也翻译成虚引用，你不能通过它访问对象。幻象引用仅仅是提供了一种确保对象被 finalize 以后，做某些事情的机制。

### String、StringBuffer、StringBuilder 有什么区别

- String 是 Java 语言非常基础和重要的类，提供了构造和管理字符串的各种基本逻辑。它是典型的 Immutable 类，被声明成为 final class，所有属性也都是 final 的。也由于它的不可变性，类似拼接、裁剪字符串等动作，都会产生新的 String 对象。
- StringBuffer 是为解决上面提到拼接产生太多中间对象的问题而提供的一个类，我们可以用 append 或者 add 方法，把字符串添加到已有序列的末尾或者指定位置。StringBuffer 本质是一个线程安全的可修改字符序列，它保证了线程安全，也随之带来了额外的性能开销
- StringBuilder 是 Java 1.5 中新增的，在能力上和 StringBuffer 没有本质区别，但是它去掉了线程安全的部分，有效减小了开销，是绝大部分情况下进行字符串拼接的首选。

### 谈谈 Java 反射机制，动态代理是基于什么原理？

反射机制是 Java 语言提供的一种基础功能，赋予程序在运行时**自省**（introspect，官方用语）的能力。通过反射我们可以直接操作类或者对象，比如获取某个对象的类定义，获取类声明的属性和方法，调用方法或者构造对象，甚至可以运行时修改类定义。

### Java 对象

- 对象头
  - 第一部分
    - 哈希
    - 锁状态标志
    - 线程持有的锁
    - 偏向线程 id
    - GC 分代年龄
  - 第二部分
    - 类型指针
- 对象实例
- 对齐填充

### 线程安全

线程安全是一个多线程环境下正确性的概念，保证多线程环境下**共享的**、**可修改的**状态的正确性。

**基本特性**：

- 原子性：操作不会中途被其他线程干扰，一般通过同步实现
- 可见性：一个线程修改了某个共享变量，其状态能够立即被其他线程知晓，通常被解释为将线程本地状态反映到主内存上，`volatile` 就是负责保证可见性的
- 有序性：保证线程内串行语义，避免指令重排

## Java 进阶

## Java 应用开发拓展

## Java 安全基础

## Java 性能基础

## Questions

1. 谈谈你对 Java 平台的理解
2. Exception 和 Error 有什么区别
3. 谈谈 final、finally、finalize 有什么区别
4. 强引用、软引用、弱引用、虚引用有什么区别
5. String、StringBuilder、StringBuffer 有什么区别
6. 动态代理是基于什么原理
7. int 和 Integer 有什么区别
8. Vector、ArrayList、LinkedList 有什么区别
9. Hashtable、HashMap、TreeMap 有什么区别
10. 如何保证集合类的线程安全，ConcurrentHashMap 如何实现高效的线程安全
11. Java 提供了哪些 IO 方式，NIO 如何实现多路复用
12. Java 有哪几种文件拷贝方式，哪一种最高效
13. 接口与抽象类有什么区别
14. 谈谈设计模式
15. Synchronized 和 ReentrantLock 有什么区别
16. synchronized 底层是如何实现的？什么是锁的升级、降级
17. 一个线程两次调用 start 方法会出现什么情况
18. 什么情况下 Java 程序会产生死锁？如何定位、修复
19. Java 并发包提供了哪些并发工具类
20. 并发包中的 ConcurrentLinkedQueue 和 LinkedBlockingQueue 有什么区别
21. Java 并发类库提供的线程池有哪几种？有什么特点
22. AtomicInteger 底层实现原理是什么？如何在自己的代码中应用 CAS 操作
23. 请介绍类加载过程，什么是双亲委派模型
24. 如何在运行时动态生成一个 Java 类
25. 谈谈 JVM 内存区域的划分，哪些区域可能发生 OOM
26. 如何监控和诊断 JVM 堆内、堆外内存使用
27. Java 常见的垃圾回收器
28. 谈谈 GC 调优思路
29. Java 内存模型中的 happen-before 是什么
30. Java 程序运行在 docker 等容器环境有哪些新问题
31. Java 应用开发中的注入攻击？
32. 如何写出安全的 Java 代码
33. 后端服务变慢，如何进行诊断？
34. Lambda 对 Java 程序的影响
35. JVM 是如何优化 Java 代码的

## References

- [Java 核心技术面试精讲](https://time.geekbang.org/column/intro/100006701)
