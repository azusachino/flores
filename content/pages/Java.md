---
title: Java
created: 2022-09-09 03:17:43
modified: 2023-04-26 09:36:40
tags: [CS, ProgrammingLanguage]
---

互联网行业中的工业级语言。

## 关键词

- Write Once, Run Anywhere
- Garbage Collection

![[../images/java-component.png]]

## 基础知识点

- Classloader: Bootstrap, Application, Extension
- 类加载过程：加载、验证、链接、初始化
- 垃圾回收的基本原理
  - 常见：SerialGC、ParallelGC、CMS、G1

### 编译执行和解释执行

编译：将源代码一次性转换为机器码的过程 `gcc -o main main.c`  
解释：将源代码逐行转换为机器码并运行的过程 (机器码并没有保存下来)

1. 编译执行：直接将所有语句都编译成了机器语言，并且保存成可执行的机器码。执行的时候，是直接进行执行机器语言，不需要再进行解释/编译。
2. 解释执行：在执行程序时，解释器将中间码（Java 的字节码通过 JVM 解释成机器码）一行行的解释成机器再码进行执行。

![[../images/different-way-of-execution.png]]

### Exception & Error

Exception 和 Error 都是继承了 Throwable 类，在 Java 中只有 Throwable 类型的实例才可以被抛出（throw）或者捕获（catch），它是异常处理机制的基本组成类型。

Error 是指在正常情况下，不大可能出现的情况，绝大部分的 Error 都会导致程序（比如 JVM 自身）处于非正常的、不可恢复状态。既然是非正常情况，所以不便于也不需要捕获，常见的比如 `OutOfMemoryError`, `StackOverFlowError` 等，都是 Error 的子类。

Exception 又分为**可检查**（checked）异常和**不检查**（unchecked）异常，可检查异常在源代码里必须显式地进行捕获处理，这是编译期检查的一部分。

不检查异常就是运行时异常，类似 `NullPointerException`、`ArrayIndexOutOfBoundsException` 之类，通常是可以编码避免的逻辑错误，具体根据需要来判断是否需要捕获，并不会在编译期强制要求。

![[../images/java-error-ex.png]]

1. 尽量不要捕获类似 Exception 这样的通用异常，而是应该捕获特定异常
2. 不要生吞异常，要做业务处理
3. 异常处理准则: Throw Early, Catch Late
   1. 如果可能产生异常，尽早暴露出来
   2. 如果异常在当前层无法处理，可以向上抛，让合适的业务逻辑处理

| `ClassNotFoundException`                                                                      | `NoClassDefFoundError`                              |
| --------------------------------------------------------------------------------------------- | --------------------------------------------------- |
| I 从 java.lang.Exception 继承，是一个 Exception 类型                                          | 从 java.lang.Error 继承，是一个 Error 类型          |
| 当动态加载 Class 的时候找不到类会抛出该异常                                                   | 当编译成功以后执行过程中 Class 找不到导致抛出该错误 |
| 一般在执行 Class.forName()、ClassLoader.loadClass()或 ClassLoader.findSystemClass()的时候抛出 | 由 JVM 的运行时系统抛出                             |

### Final & Finally & Finalize

final 可以用来修饰类、方法、变量，分别有不同的意义，final 修饰的 class 代表不可以继承扩展，final 的变量是不可以修改的，而 final 的方法也是不可以重写(override)。

finally 则是 Java 保证重点代码一定要被执行的一种机制。我们可以使用 try-finally 或者 try-catch-finally 来进行类似关闭 JDBC 连接、保证 unlock 锁等动作。

finalize 是基础类 `java.lang.Object` 的一个方法，它的设计目的是保证对象在被垃圾收集前完成特定资源的回收。finalize 机制现在已经不推荐使用，并且在 JDK 9 开始被标记为 deprecated。

```java
try {
// do something
System.exit(1);
} finally{
// 不会执行
System.out.println("Print from finally");
}

```

**final 不等同于 immutable**

```java
final List<String> strList = new ArrayList<>();
strList.add("Hello"); // ok
strList.add("world");
List<String> unmodifiableStrList = List.of("hello", "world");
unmodifiableStrList.add("again"); // exception
```

如果要实现 immutable 的类，需要做到：

- 将 class 自身声明为 final，限制拓展性。
- 将所有成员变量定义为 private 和 final，并且不要实现 setter 方法。
- 通常构造对象时，成员变量使用深度拷贝来初始化，而不是直接赋值，这是一种防御措施，因为你无法确定输入对象不被其他人修改。
- 如果确实需要实现 getter 方法，或者其他可能会返回内部状态的方法，使用 copy-on-write 原则，创建私有的 copy。

### 引用类型

引用类型不同，主要体现在 **对象不同的可达性（reachable）状态和对垃圾收集的影响**。

- 强引用
  - 最常见的普通对象引用
  - 只要还有强引用指向一个对象，就能表明对象还「活着」，垃圾收集器不会碰这种对象
- 软引用
  - 可以让对象豁免一些垃圾收集，只有当 JVM 认为内存不足时，才会去试图回收软引用指向的对象
  - JVM 会确保在抛出 `OutOfMemoryError` 之前，清理软引用指向的对象
  - 软引用通常用来实现内存敏感的缓存，如果还有空闲内存，就可以暂时保留缓存，当内存不足时清理掉，这样就保证了使用缓存的同时，不会耗尽内存
- 弱引用
  - 不能使对象豁免垃圾收集，仅仅是提供一种访问在弱引用状态下对象的途径
- 虚引用
  - 不能通过它访问对象
  - 提供了一种确保对象被 finalize 以后，做某些事情的机制

![[../images/java-object-lifecycle.png]]

### String, StringBuffer, StringBuilder

String 是 Java 语言非常基础和重要的类，提供了构造和管理字符串的各种基本逻辑。它是典型的 Immutable 类，被声明成为 final class，所有属性也都是 final 的。也由于它的不可变性，类似拼接、裁剪字符串等动作，都会产生新的 String 对象。由于字符串操作的普遍性，所以相关操作的效率往往对应用性能有明显影响。

StringBuffer 是为解决上面提到拼接产生太多中间对象的问题而提供的一个类，我们可以用 append 或者 add 方法，把字符串添加到已有序列的末尾或者指定位置。StringBuffer 本质是一个线程安全的可修改字符序列，它保证了线程安全，也随之带来了额外的性能开销，所以除非有线程安全的需要，不然还是推荐使用它的后继者，也就是 StringBuilder (线程不安全版本)。

#### 字符串缓存

`String.intern()` 显式重排机制

### 动态代理

反射机制是 Java 语言提供的一种基础功能，赋予程序在运行时**自省**（introspect，官方用语）的能力。通过反射我们可以直接操作类或者对象，比如获取某个对象的类定义，获取类声明的属性和方法，调用方法或者构造对象，甚至可以运行时修改类定义。

动态代理是一种方便运行时动态构建代理、动态处理代理方法调用的机制，很多场景都是利用类似机制做到的，比如用来包装 RPC 调用、面向切面的编程（AOP）。

实现动态代理的方式很多，比如 JDK 自身提供的动态代理，就是主要利用了上面提到的反射机制。还有其他的实现方式，比如利用传说中更高性能的字节码操作机制，类似 ASM、cglib（基于 ASM）、Javassist 等。

```java
public class MyDynamicProxy {
    public static  void main (String[] args) {
        HelloImpl hello = new HelloImpl();
        MyInvocationHandler handler = new MyInvocationHandler(hello);
        // 构造代码实例
        Hello proxyHello = (Hello) Proxy.newProxyInstance(HelloImpl.class.getClassLoader(), HelloImpl.class.getInterfaces(), handler);

        // 调用代理方法
        proxyHello.sayHello();
    }
}

interface Hello {
    void sayHello();
}

class HelloImpl implements  Hello {

    @Override
    public void sayHello() {
        System.out.println("Hello World");
    }
}

 class MyInvocationHandler implements InvocationHandler {

    private Object target;
    public MyInvocationHandler(Object target) {
        this.target = target;
    }
   
    @Override
    public Object invoke(Object proxy, Method method, Object[] args)
            throws Throwable {
        System.out.println("Invoking sayHello");
        Object result = method.invoke(target, args);
        return result;
    }
}
```

JDK Proxy 的优势：

- 最小化依赖关系，减少依赖意味着简化开发和维护，JDK 本身的支持，可能比 cglib 更加可靠。
- 平滑进行 JDK 版本升级，而字节码类库通常需要进行更新以保证在新版 Java 上能够使用。
- 代码实现简单。

基于类似 cglib 框架的优势：

- 有的时候调用目标可能不便实现额外接口，从某种角度看，限定调用者实现接口是有些侵入性的实践，类似 cglib 动态代理就没有这种限制。
- 只操作我们关心的类，而不必为其他相关类增加工作量。
- 高性能。

### Int & Integer

int 整形数字；**Java 语言虽然号称一切都是对象，但原始数据类型是例外。**

Integer 是 int 对应的包装类，它有一个 int 类型的字段存储数据，并且提供了基本操作，比如数学运算、int 和字符串之间转换等。

- 自动装箱 `Integer#valueOf`、自动拆箱 `Integer#intValue`
- `valueOf` 缓存 `-128~127`

```java
class Counter {
    private final AtomicLong counter = new AtomicLong();
    public void increase() {
        counter.incrementAndGet();
    }
}

class CompactCounter {
    private volatile long counter;
    private static final AtomicLongFieldUpdater<CompactCounter> updater = new AtomicLongFieldUpdater<>();
    public void increase() {
        updater.increase(this);
    }
}
```

### 集合

![[../images/java-containers.png]]

![[../images/java-map.png]]

### `ConcurrentHashMap`

- 总体结构上，它的内部存储变得和我在专栏上一讲介绍的 HashMap 结构非常相似，同样是大的桶（bucket）数组，然后内部也是一个个所谓的链表结构（bin），同步的粒度要更细致一些。
- 其内部仍然有 Segment 定义，但仅仅是为了保证序列化时的兼容性而已，不再有任何结构上的用处。
- 因为不再使用 Segment，初始化操作大大简化，修改为 lazy-load 形式，这样可以有效避免初始开销，解决了老版本很多人抱怨的这一点。
- 数据存储利用 volatile 来保证可见性。
- 使用 CAS 等操作，在特定场景进行无锁并发操作。
- 使用 Unsafe、LongAdder 之类底层手段，进行极端情况的优化。

### IO

BlockingIO, NewIO, AsyncIO

- 基础 API 功能与设计， InputStream/OutputStream 和 Reader/Writer 的关系和区别。
- NIO、NIO 2 的基本组成。
- 给定场景，分别用不同模型实现，分析 BIO、NIO 等模式的设计和实现原理。
- NIO 提供的高性能数据操作方式是基于什么原理，如何使用？
- 或者，从开发者的角度来看，你觉得 NIO 自身实现存在哪些问题？有什么改进的想法吗？

![[../images/java-io.png]]

### 文件拷贝

- 不同的 copy 方式，底层机制有什么区别？
  - 省略了用户态、内核态切换 (transferTo)
- 为什么零拷贝（zero-copy）可能有性能优势？
- Buffer 分类与使用。
- Direct Buffer 对垃圾收集等方面的影响与实践选择。

### 接口与抽象类

- 接口是实现约束
- 抽象类是公共方法抽取

#### SOLID

- 单一职责（Single Responsibility），类或者对象最好是只有单一职责，在程序设计中如果发现某个类承担着多种义务，可以考虑进行拆分。
- 开关原则（Open-Close, Open for extension, close for modification），设计要对扩展开放，对修改关闭。换句话说，程序设计应保证平滑的扩展性，尽量避免因为新增同类功能而修改已有实现，这样可以少产出些回归（regression）问题。
- 里氏替换（Liskov Substitution），这是面向对象的基本要素之一，进行继承关系抽象时，凡是可以用父类或者基类的地方，都可以用子类替换。
- 接口分离（Interface Segregation），我们在进行类和接口设计时，如果在一个接口里定义了太多方法，其子类很可能面临两难，就是只有部分方法对它是有意义的，这就破坏了程序的内聚性。  
  对于这种情况，可以通过拆分成功能单一的多个接口，将行为进行解耦。在未来维护中，如果某个接口设计有变，不会对使用其他接口的子类构成影响。
- 依赖反转（Dependency Inversion），实体应该依赖于抽象而不是实现。也就是说高层次模块，不应该依赖于低层次模块，而是应该基于抽象。实践这一原则是保证产品代码之间适当耦合度的法宝。

### 设计模式

[[Design Pattern]] 相关内容

### Synchronized & ReentrantLock

公平性是指在竞争场景中，当公平性为真时，会倾向于将锁赋予等待时间最久的线程。公平性是减少线程"饥饿"(个别线程长期等待锁，但始终无法获取)发生。

线程安全：

- 原子性：相关操作不会中途被其他线程干扰，一般通过同步机制实现
- 可见性：一个线程修改了某个共享变量，其状态能够立即被其他线程知晓，通常被解释为将线程本地状态反映到主内存上，`volatile` 负责保证可见性
- 有序性：保证线程内串行语义，避免指令重排

synchorized: monitorenter/monitorexit (非公平锁) Monitor 对象是同步的基本实现单元。

- Biased Lock
- 轻量级锁
- 重量级锁

### 线程

![[../images/java-thread-states.png]]

```java
public static void main(String[] args) throws InterruptedException {

ThreadMXBean mbean = ManagementFactory.getThreadMXBean();
Runnable dlCheck = new Runnable() {

    @Override
    public void run() {
        long[] threadIds = mbean.findDeadlockedThreads();
        if (threadIds != null) {
           ThreadInfo[] threadInfos = mbean.getThreadInfo(threadIds);
           System.out.println("Detected deadlock threads:");
            for (ThreadInfo threadInfo : threadInfos) { 
             System.out.println(threadInfo.getThreadName());
            }
         }
      }
   };

      ScheduledExecutorService scheduler =Executors.newScheduledThreadPool(1);
      // 稍等 5 秒，然后每 10 秒进行一次死锁扫描
      scheduler.scheduleAtFixedRate(dlCheck, 5L, 10L, TimeUnit.SECONDS);
      // 死锁样例代码…
}
```

### Concurrent 工具包

- 同步结构：CountDownLatch，CyclicBarrier，Semaphore，Phaser
- 线程安全容器：ConcurrentHashMap，ConcurrentSkipListMap，CopyOnWriteArrayList
- 并发队列：ArrayBlockingQueue，SynchorousQueue，PriorityBlockingQueue
- Executor

Concurrent 类型基于 lock-free 模式，在常见的多线程访问场景中可以提供较高的吞吐量。

LinkedBlockingQueue 内部基于锁模式

#### 弱一致性

- 利用遍历器迭代时，如果容器发生修改，迭代器仍然可以继续进行遍历
- size 等操作的准确性是有限的，未必是 100%准确
- 读取的性能具有不确定性

### Executor

![[../images/java-executor.png]]

### 类加载机制

- 加载
  - 将字节码数据从不同的数据源读取到 JVM 中
  - 映射为 JVM 认可的数据结构（Class 对象）
- 链接
  - 验证
  - 准备
  - 解析
- 初始化
  - 静态字段赋值
  - 执行类定义中的静态初始化块内的逻辑

![[../images/java-classloader.png]]

当类加载器（Class-Loader）试图加载某个类型的时候，除非父加载器找不到相应类型，否则尽量将这个任务代理给当前加载器的父加载器去做。使用委派模型的目的是避免重复加载 Java 类型。

三个特征：

- 双亲委派模型(Parent Delegation Model)。但不是所有类加载都遵守这个模型，有的时候，启动类加载器所加载的类型，是可能要加载用户代码的，比如 JDK 内部的 ServiceProvider/[ServiceLoader](https://docs.oracle.com/javase/9/docs/api/java/util/ServiceLoader.html)机制，用户可以在标准 API 框架上，提供自己的实现，JDK 也需要提供些默认的参考实现。 例如，Java 中 JNDI、JDBC、文件系统、Cipher 等很多方面，都是利用的这种机制，这种情况就不会用双亲委派模型去加载，而是利用所谓的上下文加载器。
- 可见性，子类加载器可以访问父加载器加载的类型，但是反过来是不允许的，不然，因为缺少必要的隔离，我们就没有办法利用类加载器去实现容器的逻辑。
- 单一性，由于父加载器的类型对于子加载器是可见的，所以父加载器中加载过的类型，就不会在子加载器中重复加载。但是注意，类加载器"邻居"间，同一类型仍然可以被加载多次，因为互相并不可见。

### 内存划分

![[../images/jmm-structure.png]]

![[../images/java-memory.png]]

### Happens-Before

Happen-before 关系，是 Java 内存模型中保证多线程操作可见性的机制，也是对早期语言规范中含糊的可见性概念的一个精确定义。

- 线程内执行的每个操作，都保证 happen-before 后面的操作，这就保证了基本的程序顺序规则，这是开发者在书写程序时的基本约定。
- 对于 volatile 变量，对它的写操作，保证 happen-before 在随后对该变量的读取操作。
- 对于一个锁的解锁操作，保证 happen-before 加锁操作。
- 对象构建完成，保证 happen-before 于 finalizer 的开始动作。
- 甚至是类似线程内部操作的完成，保证 happen-before 其他 Thread.join() 的线程等。

### 安全

![[../images/java-security.png]]

### JAVA NIO

Three Components:

- Buffer (memory) read, write, flip, clear
- Channel (socket) open, bind, register, close
- Selector (epoll) select, selectionKeys, SelectionKey.OP_XXX

## JVM

### 生成 dump 文件

dump: `jmap -dump:format=b,file=dump.hprof <pid>`

### 调优标志

- 布尔 `-XX:+FlagName 表示开启, -XX:-FlagName 表示关闭`
- 附带参数 `-XX:FlagName=Something` -> `-XX:NewRatio=N`

### 限制堆大小

在容器环境下，Java 只能获取服务器的配置，无法感知容器内存限制。

```sh
-XX:+UseContainerSupport 
-XX:InitialRAMPercentage=70.0 
-XX:MaxRAMPercentage=70.0 
-XX:+PrintGCDetails 
-XX:+PrintGCDateStamps 
-Xloggc:/data/gc-${POD_IP}-$(date '+%s').log 
-XX:+HeapDumpOnOutOfMemoryError 
-XX:HeapDumpPath=/data/dump-${POD_IP}-$(date '+%s').hprof
```

| 参数                                                        | 说明                                                                                                                                                |
| :---------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------- |
| -XX:+UseContainerSupport                                    | 使用容器内存。允许 JVM 从主机读取 cgroup 限制，例如可用的 CPU 和 RAM，并进行相应的配置。当容器超过内存限制时，会抛出 OOM 异常，而不是强制关闭容器。 |
| -XX:InitialRAMPercentage                                    | 设置 JVM 使用容器内存的初始百分比。建议与`-XX:MaxRAMPercentage`保持一致，推荐设置为 70.0。                                                          |
| -XX:MaxRAMPercentage                                        | 设置 JVM 使用容器内存的最大百分比。由于存在系统组件开销，建议最大不超过 75.0，推荐设置为 70.0。                                                     |
| -XX:+PrintGCDetails                                         | 输出 GC 详细信息。                                                                                                                                  |
| -XX:+PrintGCDateStamps                                      | 输出 GC 时间戳。日期形式，例如 2019-12-24T21:53:59.234+0800。                                                                                       |
| -Xloggc:/data/gc-\${POD_IP}-\$(date '+%s').log              | GC 日志文件路径。需保证 Log 文件所在容器路径已存在，建议您将该容器路径挂载到 NAS 目录，以便自动创建目录以及实现日志的持久化存储。                   |
| -XX:+HeapDumpOnOutOfMemoryError                             | JVM 发生 OOM 时，自动生成 DUMP 文件。                                                                                                               |
| -XX:HeapDumpPath=/data/dump-\${POD_IP}-\$(date '+%s').hprof | DUMP 文件路径。需保证 DUMP 文件所在容器路径已存在，建议您将该容器路径挂载到 NAS 目录，以便自动创建目录以及实现日志的持久化存储。                    |

#### 额外说明

- 使用`-XX:+UseContainerSupport`参数需 JDK 8u191+、JDK 10 及以上版本。
- JDK 11 版本下日志相关的参数`-XX:+PrintGCDetails`、`-XX:+PrintGCDateStamps`、`-Xloggc:$LOG_PATH/gc.log`参数已废弃，请使用参数`-Xlog:gc:$LOG_PATH/gc.log`代替。
- 如果您没有将/home/admin/nas 容器路径挂载到 NAS 目录，则必须保证该目录在应用启动前已存在，否则将不会产生日志文件。

### GC

**Stop the World Event** - All minor garbage collections are "Stop the World" events. This means that all application threads are stopped until the operation completes. Minor garbage collections are always Stop the World events.

![[../images/jvm-gc-logic.png]]

GC Params

![[../images/java-gc-params.png]]

### Unsafe

```java
Field f = Unsafe.class.getDeclaredField("theUnsafe");
f.setAccessible(true);
Unsafe unsafe = (Unsafe) f.get(null);
```

### JVM Monitor Tools

- jcmd 打印进程中的基本类、线程和 JVM 信息
  - `jcmd pid command optional_arguments`
- jconsole 提供 JVM 活动的图形化视图，包括线程的使用、类的使用和 GC 情况
- jmap JVM (堆)内存的 snapshot
- jinfo 查看 JVM 系统属性 (System.properties)
- jstack java 进程栈信息的 snapshot
- jstat 提供 GC 和类加载的信息
- jvisualvm 监控 JVM 的 GUI 工具

## DOC REF

### Synchronization Order

- 对于监视器 m 的解锁与所有后续操作对于 m 的加锁同步
- 对 volatile 变量 v 的写入，与所有其他线程后续对 v 的读同步
- 启动线程的操作与线程中的第一个操作同步。
- 对于每个属性写入默认值（0， false，null）与每个线程对其进行的操作同步。
- 线程 T1 的最后操作与线程 T2 发现线程 T1 已经结束同步。线程 T2 可以通过 T1.isAlive() 或 T1.join() 方法来判断 T1 是否已经终结。
- 如果线程 T1 中断了 T2，那么线程 T1 的中断操作与其他所有线程发现 T2 被中断了同步（通过抛出 InterruptedException 异常，或者调用 Thread.interrupted 或 Thread.isInterrupted ）

## Bugs

### 无法 parse `yyyyMMddHHmmssSSS`

现象：执行以下代码抛出异常`java.time.format.DateTimeParseException: Text '20130812214600025' could not be parsed at index 0`

```java
String x = "20130812214600025";
DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyyMMddHHmmssSSS");
LocalDateTime t1 = LocalDateTime.parse(x, dtf);
```

解决办法：

`DateTimeFormatter dtf = new DateTimeFormatterBuilder().appendPattern("yyyyMMddHHmmss").appendValue(ChronoField.MILLI_OF_SECOND, 3).toFormatter()`

## MISC

### Java9

Java9 模块化后，不允许应用程序查看来自 JDK 的所有类，会影响部分反射的运行，需要通过以下命令解决

```sh
--add-opens=java.base/java.lang=ALL-UNNAMED --add-opens=java.base/java.util=ALL-UNNAMED --add-opens=java.base/java.lang.reflect=ALL-UNNAMED
```

### Import

`import package.sub.*` is type-import-on-demand-declaration, which never causes other declaration to be shadowed.

### ThreadPoolExecutor & ForkJoinPool

ThreadPoolExecutor is a general-purpose thread pool implementation that is suitable for executing task that are independent of each other. It is designed to manage a fixed number of threads and a queue of tasks, and it can be configured with various parameters to control the behavior of the pool. `ThreadPoolExecutor` is a good choice for applications that need to execute a large number of short-lived tasks, such as web servers or batch processing systems.

ForkJoinPool, is designed specifically for executing recursive divide-and-conquer algorithms, where each task can be split into smaller subtasks that can be executed independently. ForkJoinPool is optimized for work-stealing, which means that idle threads can steal tasks from other threads' queue, which can improve performance in certain scenarios. ForkJoinPool is a good choice for applications that need to execute recursive algorithms, such as sorting or searching large dataset.

## References

- [容器环境 JVM 内存配置最佳实践](https://mp.weixin.qq.com/s/aEDg4LzWUuT7exm1R6NJtQ)
- [DateTimeFormatter won't parse dates with custom format "yyyyMMddHHmmssSSS"](https://bugs.java.com/bugdatabase/view_bug.do?bug_id=JDK-8031085)
- [Synchronization Order](https://docs.oracle.com/javase/specs/jls/se8/html/jls-17.html#jls-17.4.4)
- [深入分析 java 8 编程语言规范：Threads and Locks](https://javadoop.com/post/Threads-And-Locks-md)
- [GC Basics](https://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/index.html)
- [一文看懂 jdk8 中的 ConcurrentHashMap](https://juejin.cn/post/6896387191828643847)
