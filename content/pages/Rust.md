---
title: Rust-lang
created: 2022-08-28 00:00:00
modified: 2023-04-17 11:49:08
tags: [CS, ProgrammingLanguage]
---

A language empowering everyone to build reliable and efficient software.

## Strengths

- Compiled code about same performance as [[C]]/[[CPP]], and excellent memory and energy efficiency
- Can avoid 70% of all safety issues present in [[C]]/[[CPP]], and most memory issues
- Strong type system prevent data races, brings 'fearless concurrency'
- Seamless [[C]] interop, and dozens of supported platforms. based on [[LLVM]]
- Modern tooling: [[cargo]], [[clippy]], [[rustup]]

Rust 的变量默认是不可变的，如果要修改变量的值，需要显式地使用 mut 关键字。

除了 let / static / const / fn 等少数语句外，Rust 绝大多数代码都是表达式
（expression）。所以 if / while / for / loop 都会返回一个值，函数最后一个表达式就是函数的返回值，这和函数式编程语言一致。

Rust 支持面向接口编程和泛型编程。

Rust 有非常丰富的数据类型和强大的标准库。

Rust 有非常丰富的控制流程，包括模式匹配（pattern match）。

## 学习阶段

![[../images/rust-learning-progress.png]]

### `println!`

- `{}` 代表 Display，比如 println！（＂{}＂，2）
- `{:?}` 代表 Debug，比如 println！（＂{：？}＂，2）
- `{:o}` 代表八进制，比如 println！（＂{：o}＂，2）
- `{:x}` 代表十六进制小写，比如 println！（＂{：x}＂，2）
- `{:X}` 代表十六进制大写，比如 println！（＂{：X}＂，2）
- `{:p}` 代表指针，比如 println！（＂{：p}＂，2）
- `{:b}` 代表二进制，比如 println！（＂{：b}＂，2）
- `{:e}` 代表指数小写，比如 println！（＂{：e}＂，2）
- `{:E}` 代表指数大写，比如 println！（＂{：E}＂，2）。

## 内存

`Vec<T>` 结构是 3 个 word 的胖指针，包含：一个指向堆内存的指针 pointer、分配的堆内存的容量 capacity，以及数据在堆内存的长度 length

**栈上存放的数据是静态的，静态大小，静态生命周期；堆上存放的数据是动态的，动态大小，动态生命周期。**

```rust
let s = "hello world".to_string();
```

1. "hello world" 作为字符串常量，在编译时被存入可执行文件的 `.RODATA` 段(GCC)，然后再程序加载时获得一个固定的内存地址
2. 当执行 `"hello world".to_string()` 时，会在堆上分配一块新的内存，并把 `"hello world"` 逐个字节拷贝过去
3. 当把堆上的数据赋值给 `s` 时，`s` 作为分配在栈上的变量，需要知道堆上内存的地址，还需要知道它的长度以及现在有多大【堆上的数据大小不确定且可以增长】

![[../images/rust-memory-structure.png]]

### 栈

栈是程序运行的基础。每当一个函数被调用时，一块连续的内存就会在栈顶被分配出来，这块内存被称为帧（frame）。

![[../images/rust-stack-structure.png]]

**如何确定需要多大的帧？**

在编译并优化代码的时候，一个函数就是一个最小的编译单元。

在编译时，一切无法确定大小或者大小可以改变的数据，都无法安全地放在栈上，最好放在堆上。

### 堆

为了避免频繁调用 `malloc` ，会预先分配更多的空间。

堆上分配出来的每一块内存需要显式地释放，**堆上内存有更加灵活的生命周期，可以在不同的调用栈之间共享数据。**

#### GC

以 [[Java]] 为首的一系列编程语言，采用了追踪式垃圾回收的方法 (Tracing GC)，来自动管理堆内存。通过定期标记找出不再被引用的对象，然后将其清理掉以实现自动管理内存，减轻开发者负担的作用。

Objective-C 和 Swift 采用了 自动引用计数 (Automatic Reference Counting)。在编译时，它为每个函数插入 retain/release 语句来自动维护堆上对象的引用计数，当引用计数为零时， release 语句就自动释放对象

## 编程基础概念

![[../images/programming-basic-concepts.png]]

### 数据（值和类型、指针和引用）

严谨地说，类型是对值的区分，它包含了值在内存中的**长度、对齐以及值可以进行的操作**等信息。

原生类型（primitive type）是编程语言提供的最基础的数据类型。比如字符、整数、浮点数、布尔值、数组（array）、元组（tuple）、指针、引用、函数、闭包等。所有原生类型的大小都是固定的，因此它们可以被分配到栈上。

![[../images/rust-data-types.png]]

在内存中，一个值被存储到内存中的某个位置，这个位置对应一个内存地址。而指针是一个持有内存地址的值，可以通过解引用（deference）来访问它指向的内存地址，理论上可以解引用到任意数据类型。

引用（reference）和指针非常类似，不同的是，引用的解引用访问是受限的，它只能解引用到它引用数据的类型。

### 代码（函数、方法、闭包、接口和虚表）

函数是编程语言的基本要素，它是对完成某个功能的一组相关语句和表达式的封装。函数也是对代码中重复行为的抽象。

在面向对象的编程语言中，在类或者对象中定义的函数，被称为方法（method）。

而闭包是将函数，或者说代码和其环境一起存储的一种数据结构。闭包引用的上下文中的自由变量，会被捕获到闭包的结构中，成为闭包类型的一部分。

![[../images/rust-closure-sample.png]]

接口是一个软件系统开发的核心部分，它反映了系统的设计者对系统的抽象理解。作为一个抽象层，接口将使用方和实现方隔离开来，使两者不直接有依赖关系，大大提高了复用性和扩展性。

在生成这个引用的时候，我们需要构建胖指针，除了指向数据本身外，还需要指向一张涵盖了这个接口所支持方法的列表。这个列表，就是我们熟知的虚表（virtual table）。

一个 Vec 数据在运行期被抹去类型，生成一个指向 Write 接口引用的过程：

![[../images/rust-vec-trait-sample.png]]

### 运行方式（并发运行、同步、异步和 Promise/async/await）

![[../images/rust-concurrent.png]]

同步执行保证了代码的因果关系（causality），是程序正确性的保证。

在遭遇 I/O 处理时，高效 CPU 指令和低效 I/O 之间的巨大鸿沟，成为了软件的性能杀手。下图对比了 CPU、内存、I/O 设备、和网络的延迟：

![[../images/operations-latency.png]]

异步是指一个任务开始执行后，与它没有因果关系的其它任务可以正常执行，不必等待前一个任务结束。

在异步操作里，异步处理完成后的结果，一般用 Promise 来保存，它是一个对象，用来描述在未来的某个时刻才能获得的结果的值，一般存在三个状态：

1. 初始状态，Promise 还未执行
2. 等待状态（Pending），Promise 已运行，还未结束
3. 结束状态，Promise 成功 resolve 出结果，或者 error

async 定义了一个可以并发执行的任务，而 await 则触发这个任务并发执行。

## Rust 变量、函数和数据结构

![[../images/rust-variable-structure.png]]

![[../images/rust-loop.png]]

## 所有权和生命周期

1. 一个值只能被一个变量所拥有，所有者
2. 一个值同一时刻只能有一个所有者
3. 当所有者离开作用域，其拥有的值会被丢弃，内存得到释放

符合 Copy 语义的类型，在赋值或传参时，会自动按位拷贝。

```rust
#[cfg(test)]
mod tests {

    fn is_copy<T: Copy>() {}

    fn types_impl_copy_trait() {

        is_copy::<bool>();
        is_copy::<char>();

        // all iXX and uXX, usize/isize, fXX implement Copy trait
        is_copy::<i8>();
        is_copy::<u64>();
        is_copy::<f32>();
        is_copy::<usize>();
       
        // function (pointer) is Copy
        is_copy::<fn()>();

        // raw pointer is Copy
        is_copy::<*const String>();
        is_copy::<*mut String>();

        // array/tuple with values which is Copy is Copy
        is_copy::<[u8; 4]>();
        is_copy::<(&str, &str)>();
    }

    fn types_not_impl_copy_trait() {

        // unsized or dynamic sized type is not Copy
        is_copy::<str>();
        is_copy::<[u8]>();
        is_copy::<Vec<u8>>();
        is_copy::<String>();

        // mutable reference is not Copy
        is_copy::<&mut String>();

        // array / tuple with values that not Copy is not Copy
        is_copy::<[Vec<u8>; 4]>();
        is_copy::<(String, u32)>();
    }

    #[test]
    fn test() {
        types_impl_copy_trait();
        types_not_impl_copy_trait();
    }
}
```

![[../images/rust-zone-lifecycle.png]]

## Borrow

![[../images/rust-borrow.png]]
在一个作用域内，仅允许一个活跃的可变引用。

在一个作用域内，活跃的可变引用和只读引用是互斥的，不能同时存在。

`Box::leak()`，顾名思义，它创建的对象，从堆内存上泄漏出去，不受栈内存控制，是一个
自由的、生命周期可以大到和整个进程的生命周期一致的对象。

![[../images/rust-mutability.png]]

如果不用跨线程访问，可以用效率非常高的 Rc；如果要跨线程访问，那么必须用 Arc。

![[../images/rust-concurrent-pointers.png]]

## 生命周期

一般来说，堆内存的生命周期，会默认和其栈内存的生命周期绑定在一起。

如果一个值的生命周期贯穿整个进程的生命周期，那么我们就称这种生命周期为静态生命
周期。

当值拥有静态生命周期，其引用也具有静态生命周期。我们在表述这种引用的时候，可以
用 'static 来表示。比如： &'static str 代表这是一个具有静态生命周期的字符串引用。

全局变量、静态变量、字符串字面量（string literal）等，都拥有静态生命周期。我们上文中提到的堆内存，如果使用了 Box::leak 后，也具有静态生命周期。

如果一个值是在某个作用域中定义的，也就是说它被创建在栈上或者堆上，那么其生命周
期是动态的。

![[../images/rust-obj-lifecycle.png]]

函数本身携带的信息，就是编译器在编译时使用的全部信息。生命周期参数，描述的是参数和参数之间、参数和返回值之间的关系，并不改变原有的生命周期。

### 对象销毁顺序

![[../images/rust-obj-drop.png]]

## 类型系统

![[../images/cs-data-types.png]]

### Rust 原生类型

![[../images/rust-raw-data-types.png]]
![[../images/rust-data-types-conclusion.png]]

## Trait

![[../images/rust-trait-type.png]]

`dyn Trait` 实现

![[../images/rust-dyn-trait.png]]

![[../images/rust-dyn-trait-sample.png]]

- `Sized` trait 用于标记有具体大小的类型。在使用泛型参数时，编译器会自动为泛型参数加上 `Sized` 约束。
- 如果一个类型 T 实现了 Send trait，意味着 T 可以安全地从一个线程移动到另一个线程，也就是说所有权可以在线程间移动。
- 如果一个类型 T 实现了 Sync trait，则意味着 &T 可以安全地在多个线程中共享。一个类型 T 满足 Sync trait，当且仅当 &T 满足 Send trait。
  - 裸指针 `*const T / *mut T`。它们是不安全的，所以既不是 Send 也不是 Sync。
  - `UnsafeCell<T>` 不支持 Sync。也就是说，任何使用了 Cell 或者 RefCell 的数据结构不支持 Sync。
  - 引用计数 Rc 不支持 Send 也不支持 Sync。所以 Rc 无法跨线程。

![[../images/rust-operation-traits.png]]
Trait 概览

![[../images/rust-default-traits.png]]

## 数据结构

指针是一个持有内存地址的值，可以通过解引用来访问它指向的内存地址，理论上可以解引用到任意数据类型；引用是一个特殊的指针，它的解引用访问是受限的，只能解引用到它引用数据的类型，不能作其他用。

**在 Rust 中，凡是需要做资源回收的数据结构，且实现了 `Deref/DerefMut/Drop`，都是智能指针。**

### 容器类

![[../images/rust-containers.png]]

在 Rust 里，切片是描述一组属于同一类型、长度不确定的、在内存中连续存放的数据结
构，用 [T] 来表述。因为长度不确定，所以切片是个 `DST（Dynamically Sized Type）`。

slice & vec

![[../images/rust-slice-vec.png]]

`Vec<T>, &[T], Box<T>`

![[../images/rust-container-memory.png]]

## 闭包

A closure expression produces a closure value with a unique, anonymous type
that cannot be written out. A closure type is approximately equivalent to a struct
which contains the captured variables.

**闭包的大小跟参数、局部变量都无关，只跟捕获的变量有关。**

![[../images/rust-closure.png]]

## Cargo

### Cross Compilation

1. install aarch64 toolchain, say, the [linaro](https://releases.linaro.org/components/toolchain/binaries/latest-7/aarch64-linux-gnu/)
2. set aarch64 toolchain to `PATH`
3. setup the global config for cargo

```toml
# the ~/.cargo/config.toml

[source.crates-io]
replace-with = 'rsproxy'

[source.rsproxy]
registry = "https://rsproxy.cn/crates.io-index"

[registries.rsproxy]
index = "https://rsproxy.cn/crates.io-index"

[net]
git-fetch-with-cli = true

[target.aarch64-unknown-linux-gnu]
linker = "aarch64-linux-gnu-gcc"
```

```sh
1. install the target `rustup target add aarch64-unknown-linux-gnu`
2. build with target `cargo build -r --target aarch64-unknown-linux-gnu`
```

**Solve the open-sys problem**

Use the vendored version `open-sys = { version = "0.10", features = ["vendored"] }`

## References

- Rust 编程第一课 - 极客时间
- [rust official site](https://www.rust-lang.org/)
- [little rust](https://github.com/azusachino/little-rust)
- [Notes on cross-compiling Rust](https://john-millikin.com/notes-on-cross-compiling-rust)
