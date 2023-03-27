---
title: Java并发编程实战
created: 2023-01-14 09:34:23
modified: 2023-03-27 10:34:31
tags: [Column, Tech, Java]
author: 王宝令
read: 45
total: 45
---

分工、同步、互斥。

- 分工：如何高效地拆解任务并分配给线程 (`ForkJoinPool`)
- 同步：线程之间如何协作 (`CountDownLatch`)
- 互斥：保证同一时刻只允许一个线程访问共享资源 (`ReentrantLock`)

## 基本概念

- 可见性：多核 CPU 缓存所导致的问题
- 原子性：CPU 分片调度机制，导致指令执行顺序不保证【 i++ 分散在多个指令中】
- 有序性：编译器优化导致语句顺序调整

为平衡 CPU、内存、IO 设备之间地性能差异：

1. CPU 增加了三级缓存
2. 操作系统增加了进程、线程，以分时复用 CPU
3. 编译程序优化指令执行次序，使得缓存能够得到更加合理的利用

### 缓存导致的可见性问题

![[../../images/java-multi-core-value-visibility-problem.png]]

### 线程切换带来的原子性问题

完成 `count+=1` 至少需要三条 CPU 指令：

1. 首先，需要把变量 count 从内存加载到 CPU 寄存器
2. 之后，在寄存器中执行 `+1` 操作
3. 最后，将结果写入内存 (缓存机制导致可能写入的是 CPU 缓存，而不是内存)

操作系统做任务切换，可以发生在任何一条**CPU 指令**执行完，而不是高级语言里的一条语句。

![[../../images/java-thread-switch-problem.png]]

### 编译优化带来的有序性问题

```java
public class Singleton {
    // 通过添加 volatile，禁止指令重排
    private static volatile Singleton instance;

    public static Singleton getInstance() {
        if (null == instance) {
            syncrhonized(Singleton.class) {
                if (null == instance) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

new 操作可能被优化成：

1. 分配一块内存 M
2. 将 M 的地址赋值给 instance 变量
3. 最后在内存 M 上初始化 Singleton 对象

![[../../images/java-new-compilation-optimization-problem.png]]

### JMM

解决可见性、有序性最直接的办法就是**禁用缓存和编译优化**。

#### Happens-Before

**前面一个操作的结果对后续操作是可见的**。

```java
// write runs in thread-A, read runs in thread-B
class VolatileExample {
    int x = 0;
    volatile boolean v = false;
    void write() {
        x = 42;
        v = true;
    }

    void read() {
        if (v == true) {
        // what is x?
        // before jdk 1.5, might be 42, might be 0
        // after jdk 1.5, x is 42
        }
    }
}
```

1. 程序的顺序性规则：程序对某个变量的修改一定是对后续操作可见的
2. volatile 变量规则：对一个 volatile 变量的写操作，先于后续对这个 volatile 变量的读操作
3. 传递性规则：如果 A 操作先于 B 操作，且 B 先于 C，那么 A 先于 C
4. 管程中锁的规则：对一个锁的解锁先于后续对这个锁的加锁【管程是一种通用的同步原语，Java 中的 synchronized】
5. 线程 start() 规则：主线程 A 启动子线程 B 后，子线程 B 能够看到主线程在启动子线程 B 前的操作
6. 线程 join() 规则：主线程 A 等待子线程 B 完成，当子线程 B 退出后，主线程能够看到子线程的操作

### 死锁

1. 互斥 - 共享资源 X 和 Y 只能被一个线程占用
2. 占有且等待 - 线程 A 已经取得共享资源 X，在等待共享资源 Y 时不释放共享资源 X
3. 不可抢占 - 其他线程不能强行抢占线程 A 的资源
4. 循环等待 - 线程 A 等待线程 B 占有的资源，线程 B 等待线程 A 占有的资源

### 线程

![[../../images/java-thread-status.png]]

#### Runnable <-> Blocked

线程等待 synchronized 隐式锁；

synchronized 修饰的方法、代码块同一时刻只允许一个线程执行，其他线程只能等待，此时，等待的线程从 RUNNABLE 切换到 BLOCKED

#### Runnable <-> Waiting

1. `Object.wait`
2. `Thread.join`
3. `LockSupport.park`

#### Runnable <-> `Timed_Waiting`

1. `Thread.sleep(long milis)`
2. `Object.wai(long timeout)`
3. `Thread.join(long milis)`
4. `LockSupport.parkNanos(Object bloker, long deadline)`
5. `LockSupport.parkUntil(long deadline)`

#### Runnable -> Terminated

1. `Thread.stop`
2. `Thread.interrupt`

stop() 方法真的会杀死线程，不给线程喘息机会，如果线程持有 `ReentrantLock`，被 stop 的线程并不会自动调用 unlock 方法，可能会导致其他线程无法获取到该锁，进而产生系统问题。【最好不使用】

interrupt() 方法仅仅是通知线程，线程有机会执行一些后续操作，同时也可以无视这个通知。

```java
Thread th = Thread.currentThread();
while(true) {
  if(th.isInterrupted()) {
    break;
  }
  // 省略业务代码无数
  try {
    Thread.sleep(100);
  } catch (InterruptedException e){
    e.printStackTrace();
  }
}
```

**在触发 InterruptedException 异常的同时，JVM 会同时把线程的中断标志位清除，所以这个时候 th.isInterrupted()返回的是 false。**

```java
try {
  Thread.sleep(100);
} catch(InterruptedException e){
  // 重新设置中断标志位
  th.interrupt();
}
```

### 并发策略

1. 避免共享【ThreadLocal 思想】
2. 不变模式【合理使用 final】
3. 管程及其他同步工具【sychronized】

---

1. 优先使用成熟的工具类
2. 迫不得已才使用低级的同步原语
3. 避免过早优化

## 锁

**锁，应是私有的，不可变的，不可重用的**

### 用锁的最佳实践

- 永远只在更新对象的成员变量时加锁
- 永远只在访问可变的成员变量时加锁
- 永远不在调用其他对象的方法时加锁

## JUC

- Semaphore
- ReadWriteLock
- StampedLock
- CountDownLatch
- CyclicBarrier
- Blocking*
- Atomic*

![[../../images/concurrent-programming.png]]

## References

- [Java 并发编程实战 - 极客时间](http://localhost)
