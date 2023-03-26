---
title: JVM
created: 2022-09-02 05:42:01
modified: 2023-03-26 11:19:47
aliases: [Java Virtual Machine]
tags: [CS, Java]
---

![[../images/jvm-framework.png]]

## Java 内存结构

### Metaspace

1. Klass 结构，这个非常重要，把它理解为一个 Java 类在虚拟机内部的表示吧；
2. method metadata，包括方法的字节码、局部变量表、异常表、参数信息等；
3. 常量池；
4. 注解；
5. 方法计数器，记录方法被执行的次数，用来辅助 JIT 决策；
6. 其他

## Hotspot 虚拟机对象

## 垃圾收集策略与算法

## Hotspot 垃圾收集器

## 内存分配与回收策略

## JVM 性能调优

## 类文件结构

## 类加载

### 类加载时机

### 类加载过程

### 类加载器

## References

- [JVM 底层原理最全知识总结](https://doocs.github.io/jvm/)
- [重磅硬核 | 一文聊透对象在 JVM 中的内存布局，以及内存对齐和压缩指针的原理及应用](https://mp.weixin.qq.com/s/A7HeiQkhAXdJePzNR2nr5Q)
- [深入理解堆外内存 Metaspace](https://www.javadoop.com/post/metaspace)
