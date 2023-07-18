---
title: Java核心技术面试精讲
created: 2023-01-07 17:00:00
modified: 2023-07-07 13:48:31
tags: [Column, Tech, Java]
author: 杨晓峰
read: 39
total: 39
status: done
---

## Java 基础

### 谈谈你对 Java 平台的理解

Java 是一门面向对象的语言，有两个显著特性：跨平台、GC。

Java 中的 Class Loader [Metaspace 溢出问题相关]

- Bootstrap
- Extension
- Application
- UserDefinedClassLoader【如何配置 classloader 的 unload 行为】

### Java 是解释运行吗

不完全是；Java 中存在 JIT (Just In Time Compiler)，会在运行时将部分热点代码编译成机器码执行。默认阈值为 1500 次。 directBuffer 中的 codeCache 段。

- c1 client 1500
- c2 server

`-Xint` 只解释执行；`-Xcomp` 关闭解释执行，仅编译执行，可能导致服务启动效率变低。

### Exception 与 Error 有什么区别

Error 和 Exception 都是 Throwable 的子类；在 Java 中，只有 Throwable 才能被 throw 和 catch。

1. Error 是指在正常情况下，不大可能出现的情况，绝大部分的 Error 都会导致程序（比如 JVM 自身）处于非正常的、不可恢复状态。既然是非正常情况，所以不便于也不需要捕获，常见的比如 OutOfMemoryError 之类，都是 Error 的子类。
2. Exception 又分为可检查（checked）异常和不检查（unchecked）异常
   1. 可检查异常在源代码里必须显式地进行捕获处理，这是编译期检查的一部分。
   2. 不检查异常就是所谓的运行时异常，类似 NullPointerException、ArrayIndexOutOfBoundsException 之类，通常是可以编码避免的逻辑错误，具体根据需要来判断是否需要捕获，并不会在编译期强制要求。

**NoClassDefFoundError 与 ClassNotFoundException**

ClassNotFoundException is thrown when you try to load a class at run time using Class.forName() or loadClass() methods and mentioned classes are not found in the classpath.

NoClassDefFoundError is thrown when a particular class is present at compile time, but was missing at run time.

- ClassNotFoundException is a checked exception, while NoClassDefFoundError is an unchecked exception. This means that you must catch ClassNotFoundException in your code, but you do not have to catch NoClassDefFoundError.
- ClassNotFoundException is thrown by the Java Runtime System, while NoClassDefFoundError is thrown by the application itself.
- ClassNotFoundException occurs when the JVM cannot find a class on the classpath, while NoClassDefFoundError occurs when the JVM cannot find a class that is referenced by another class.

### 谈谈 final、finally、 finalize 有什么不同

- final 可以用来修饰类、方法、变量，分别有不同的意义，final 修饰的 class 代表不可以继承扩展，final 的变量是不可以修改的，而 final 的方法也是不可以重写的（override）。
- finally 则是 Java 保证重点代码一定要被执行的一种机制。我们可以使用 try-finally 或者 try-catch-finally 来进行类似关闭 JDBC 连接、保证 unlock 锁等动作。
- finalize 是基础类 `java.lang.Object` 的一个方法，它的设计目的是保证对象在被垃圾收集前完成特定资源的回收。finalize 机制现在已经不推荐使用，并且在 JDK 9 开始被标记为 deprecated。

```java
try {
  System.exit(1);
} finally {
  // 不会执行
}
```

### 强引用、软引用、弱引用、幻象引用有什么区别

不同的引用类型，主要体现的是**对象不同的可达性 reachable 状态和对垃圾收集的影响**。

- StrongReference 强引用是最常见的普通对象引用，只要还有强引用指向一个对象，就能表明对象还“活着”，垃圾收集器不会碰这种对象。
- SoftReference 软引用是一种相对强引用弱化一些的引用，可以让对象豁免一些垃圾收集，只有当 JVM 认为内存不足时，才会去试图回收软引用指向的对象。
- WeakReference 弱引用不能使对象豁免垃圾收集，仅仅是提供一种访问在弱引用状态下对象的途径。这就可以用来构建一种没有特定约束的关系，比如，维护一种非强制性的映射关系，如果试图获取时对象还在，就使用它，否则重现实例化。
- PhantomReference 幻象引用，你不能通过它访问对象。幻象引用仅仅是提供了一种确保对象被 finalize 以后，做某些事情的机制。`get()` 总是返回 null。ReferenceQueue

![[../../images/java-reference-graph.png]]

```java
Object counter = new Object();
ReferenceQueue<?> q = new ReferenceQueue<>();
PhantomReference<Object> pr = new PhantomReference<>(count, q);
counter = null; // remove strong reference
System.gc(); // object -> finalize

try {
  Reference<Object> ref = q.remove(1000L);
  if (ref != null) {
    postWork();
  }
} catch (InterruptedException e) {
  e.printStackTrace();
}
```

### String, StringBuffer, StringBuilder 有什么区别

- String 是 Java 语言非常基础和重要的类，提供了构造和管理字符串的各种基本逻辑。**String 是典型的 Immutable 类**，被声明成为 final class，所有属性也都是 final 的。也由于它的不可变性，类似拼接、裁剪字符串等动作，都会产生新的 String 对象。
- StringBuffer 是为解决上面提到拼接产生太多中间对象的问题而提供的一个类，我们可以用 append 或者 add 方法，把字符串添加到已有序列的末尾或者指定位置。StringBuffer 本质是一个线程安全的可修改字符序列，它保证了线程安全，也随之带来了额外的性能开销
- StringBuilder 是 Java 1.5 中新增的，在能力上和 StringBuffer 没有本质区别，但是它去掉了线程安全的部分，有效减小了开销，是绝大部分情况下进行字符串拼接的首选。

`String.intern()` 是一种**显式的重排**，也可以使用 `G1GC` 中提供的 `-XX:+UseStringDeduplication` Oracle JDK 8u20

### 谈谈 Java 反射机制，动态代理是基于什么原理？

反射机制是 Java 语言提供的一种基础功能，赋予程序在运行时**自省**（introspect，官方用语）的能力。通过反射我们可以直接操作类或者对象，比如获取某个对象的类定义，获取类声明的属性和方法，调用方法或者构造对象，甚至可以运行时修改类定义。

- ASM
- cglib
- Javassist

`java.lang.reflect.*`, Class, Field, Method, Constructor. The magnificent `AccessibleObject.setAccessible(boolean flag)`

```java
public class MyDynamicProxy {
  public static  void main (String[] args) {
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

class HelloImpl implements  Hello {
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

### int vs. Integer

the boxing & unboxing

- int is one of 8 primitive types.
- Integer is the wrapped object type, also with some cache `valueOf` (-128~127).
- Boolean (TRUE/FALSE)
- Short (-128~127)
- Byte (-128~127)
- Character (`\u0000` ~ `\u007F`)

原始类型无法与 Java 泛型搭配使用，原因在于 Java 编译期会自动将类型转换为对应的特定类型【Object】；原始数组是一段连续的内存，而对象数组存储的是引用，对象的存储位置灵活。

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
- 对齐填充【字长，long 在 32 位的类似问题】

### 线程安全

线程安全是一个多线程环境下正确性的概念，保证多线程环境下**共享的**、**可修改的**状态的正确性。

**基本特性**：

- 原子性：操作不会中途被其他线程干扰，一般通过同步实现
- 可见性：一个线程修改了某个共享变量，其状态能够立即被其他线程知晓，通常被解释为将线程本地状态反映到主内存上，`volatile` 就是负责保证可见性的
- 有序性：保证线程内串行语义，避免指令重排

Happens-before 原则

1. 程序 natural 执行顺序
2. volatile 的写读顺序
3. 线程 start、join 与线程外数据的可见性

### HashMap & ConcurrentHashMap

- putVal
- resize

### IO

- BIO stands for Blocking I/O. It is the oldest and simplest I/O model. In BIO, all I/O operations are blocking, which means that the thread that calls an I/O operation will be blocked until the operation is complete. This can lead to performance problems, especially for applications that need to handle a large number of concurrent connections. (InputStream/OutputStream, Reader/Writer)
- NIO stands for Non-Blocking I/O. It was introduced in Java 1.4 to address the performance problems of BIO. In NIO, I/O operations are non-blocking, which means that the thread that calls an I/O operation will not be blocked until the operation is complete. This allows the thread to continue processing other tasks while the I/O operation is in progress. (Channel, Selector, Buffer)
- AIO stands for Asynchronous I/O. It was introduced in Java 7 to further improve the performance of I/O operations. In AIO, I/O operations are asynchronous, which means that the thread that calls an I/O operation will not be blocked at all. Instead, the I/O operation will be handled by a background thread. This allows the thread to continue processing other tasks without being interrupted by I/O operations. **AsynchronousServerSocketChannel**

```java
// BIO
public class DemoServer extends Thread {
  private ServerSocket serverSocket;
  public int getPort() {
      return  serverSocket.getLocalPort();
  }
  public void run() {
      try {
          serverSocket = new ServerSocket(0);
          while (true) {
              Socket socket = serverSocket.accept();
              RequestHandler requestHandler = new RequestHandler(socket);
              requestHandler.start();
          }
      } catch (IOException e) {
          e.printStackTrace();
      } finally {
          if (serverSocket != null) {
              try {
                  serverSocket.close();
              } catch (IOException e) {
                  e.printStackTrace();
              }
          }
      }
  }
  public static void main(String[] args) throws IOException {
      DemoServer server = new DemoServer();
      server.start();
      try (Socket client = new Socket(InetAddress.getLocalHost(), server.getPort())) {
          BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(client.getInputStream()));
          bufferedReader.lines().forEach(s -> System.out.println(s));
      }
  }
}
// 简化实现，不做读取，直接发送字符串
class RequestHandler extends Thread {
  private Socket socket;
  RequestHandler(Socket socket) {
      this.socket = socket;
  }
  @Override
  public void run() {
      try (PrintWriter out = new PrintWriter(socket.getOutputStream())) {
          out.println("Hello world!");
          out.flush();
      } catch (Exception e) {
          e.printStackTrace();
      }
  }
}

// NIO
public class NIOServer extends Thread {
    public void run() {
        try (Selector selector = Selector.open();
            ServerSocketChannel serverSocket = ServerSocketChannel.open();) {// 创建 Selector 和 Channel
            serverSocket.bind(new InetSocketAddress(InetAddress.getLocalHost(), 8888));
            serverSocket.configureBlocking(false);
            // 注册到 Selector，并说明关注点
            serverSocket.register(selector, SelectionKey.OP_ACCEPT);
            while (true) {
                selector.select();// 阻塞等待就绪的 Channel，这是关键点之一
                Set<SelectionKey> selectedKeys = selector.selectedKeys();
                Iterator<SelectionKey> iter = selectedKeys.iterator();
                while (iter.hasNext()) {
                    SelectionKey key = iter.next();
                   // 生产系统中一般会额外进行就绪状态检查
                    sayHelloWorld((ServerSocketChannel) key.channel());
                    iter.remove();
                }
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    private void sayHelloWorld(ServerSocketChannel server) throws IOException {
        try (SocketChannel client = server.accept()) {
            client.write(Charset.defaultCharset().encode("Hello world!"));
        }
    }
   // 省略了与前面类似的 main
}

// AIO
AsynchronousChannel channel = Channels.newChannel(new FileInputStream("myfile.txt"));
AsynchronousChannelGroup group = Executors.newCachedThreadPool();
channel.register(group, SelectionKey.OP_READ, new CompletionHandler<Integer, Integer>() {
    @Override
    public void completed(Integer result, Object attachment) {
        // Process the results of the read operation.
    }

    @Override
    public void failed(Throwable exc, Object attachment) {
        // Handle the failure of the read operation.
    }
});
channel.read(ByteBuffer.allocate(1024));
```

**文件拷贝**

如何避免上下文切换，利用 DMA 进行数据拷贝。

1. FileInputStream -> FileOutputStream
2. InputStream.transferTo 【减少 user space / kernel space 上下文切换，文件内容复制】
3. Files.copy

**DirectBuffer**

1. 设置直接内存大小 `-XX:MaxDirectMemorySize=512M`
2. 跟踪并诊断 DirectBuffer 内存 `-XX:NativeMemoryTracking={summary|detail}` 一定程度的性能下降

- 在应用程序中，显式地调用 System.gc() 来强制触发。
- 在大量使用 Direct Buffer 的部分框架中，框架会自己在程序中调用释放方法，Netty 就是这么做的，有兴趣可以参考其实现（PlatformDependent0）。
- 重复使用 Direct Buffer。

### 设计模式

- 创建型模式，是对对象创建过程的各种问题和解决方案的总结，包括各种工厂模式（Factory、Abstract Factory）、单例模式（Singleton）、构建器模式（Builder）、原型模式（ProtoType）。
- 结构型模式，是针对软件设计结构的总结，关注于类、对象继承、组合方式的实践经验。常见的结构型模式，包括桥接模式（Bridge）、适配器模式（Adapter）、装饰者模式（Decorator）、代理模式（Proxy）、组合模式（Composite）、外观模式（Facade）、享元模式（Flyweight）等。
- 行为型模式，是从类或对象之间交互、职责划分等角度总结的模式。比较常见的行为型模式有策略模式（Strategy）、解释器模式（Interpreter）、命令模式（Command）、观察者模式（Observer）、迭代器模式（Iterator）、模板方法模式（Template Method）、访问者模式（Visitor）。

### Thread

![[../../images/java-thread-state-circle.png]]

死锁：jstack，ThreadMXBean

排查占用 CPU 很高的 Java 线程：可以通过 linux 下 top 命令查看 cpu 使用率较高的 java 进程，进而用 top -Hp ➕pid 查看该 java 进程下 cpu 使用率较高的线程。再用 jstack 命令查看线程具体调用情况，排查问题。

1. top -Hp
2. printf "%d" pid (16)
3. jstack

`-XX:+printGCDetails`

### 类加载

加载、链接、初始化

**动态生成 Java 类**

可以利用 Java 程序生成一段源码，然后保存到文件等；直接用 ProcessBuilder 之类启动 javac 进程，并指定上面生成的文件作为输入，进行编译。最后，再利用类加载器，在运行时加载即可。

### JVM 内存区域

- Program Counter Register
- Java Virtual Machine Stack
- Heap
- Method Area [class, method], (Run-Time Constant Pool)
- Native Stack

![[../../images/jvm-memory-structure.png]]

### GC

引用计数【重复引用问题】，可达性分析【GC ROOTs (虚拟机栈和本地方法栈中正在引用的对象、静态属性的对象和常量引用)】

- SerialGC
- ParNew GC (Parallel version of Serial)
- CMS (Concurrent Mark Sweep)
- Parrallel GC
- G1 GC (Gabbage First)
- ZGC
- shenandoah GC

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

## 推荐书单

- 《Java 编程思想》
- 《Java 核心技术》
- 《Effective Java》
- 《设计模式之禅》
- 《Java 并发实战》
- 《深入理解 Java 虚拟机》
- 《Java 性能优化权威指南》
- 《Spring 实战》
- 《Netty 实战》
- 《Cloud Native Java》
- 《深入分布式缓存》

## References

- [Java 核心技术面试精讲](https://time.geekbang.org/column/intro/100006701)
- [ClassNotFoundException vs NoClassDefFoundError](https://www.baeldung.com/java-classnotfoundexception-and-noclassdeffounderror)
- [一行一行源码分析清楚 AbstractQueuedSynchronizer](https://www.javadoop.com/post/AbstractQueuedSynchronizer)
