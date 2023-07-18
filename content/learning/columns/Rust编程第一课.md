---
title: Rust 编程第一课
created: 2023-02-08 09:34:23
modified: 2023-07-18 13:31:24
tags: [Column, Rust]
author: 陈天
read: 46
total: 46
status: done
---

## Key Points

- 所有权：一个值只能被一个变量所拥有，且同一个时刻只能有一个所有者，当所有者离开作用域，其拥有的值被丢弃，内存得到释放
- Move 语义：赋值或者传参会导致值Move，所有权被转移，一旦所有权转移，之前的变量就无法访问
- Copy 语义：如果值实现了 Copy trait，赋值及传参就会使用 Copy 语义，相应的值会被按位拷贝，产生新的值。

### 所有权

![[../../images/rust-ownership-ideas.png]]

### 多态实现

![[../../images/rust-polymorphism.png]]

### rc

![[../../images/rust-ownership-with-rc.png]]

### 生命周期

1. 所有引用类型的参数都有独立的生命周期 `'a, 'b`
2. 如果只有一个引用型输入，它的生命周期会赋给所有输出
3. 如果有多个引用类型的参数，其中一个是 self，那么它的生命周期会赋给所有输出

### Trait

- 不允许返回 Self，在 trait object 产生时，原来的类型会被抹去，没有 Self 信息。
- 不允许携带泛型参数，Rust 中带泛型的类型在编译时会做单态化，而 trait object 是运行时产物，无法兼容。

![[../../images/rust-trait-related-topics.png]]

### 数据类型

凡是需要做资源回收的数据结构，且实现了 Deref/DerefMut/Drop，都是智能指针。

#### slices

```rust
use std::iter::FromIterator;

fn main() {
    let arr = ['h', 'e', 'l', 'l', 'o'];
    let vec = vec!['h', 'e', 'l', 'l', 'o'];
    let s = String::from("hello");
    let s1 = &arr[1..3];
    let s2 = &vec[1..3];
    let s3 = &s[1..3];

    // &[char] &[char] 长度和内容
    assert_eq!(s1, s2);
    // &[char] &[str] 不能直接对比
    assert_eq!(s2, s3.chars().collect::<Vec<_>>());
    // &[char] 可以通过迭代器转换成 String
    assert_eq!(String::from_iter(s2), s3);
}
```

![[../../images/rust-string-str-char_slice.png]]

#### HashMap

A hash map implemented with quadratic probing and SIMD lookup.

#### Closure

闭包的大小跟参数、局部变量都无关，只跟捕获的变量有关。

闭包是存储在栈上，并且除了捕获的数据外，闭包本身不包含任何额外函数指针指向闭包的代码。

#### 泛型数据结构的逐步约束

```rust
pub struct BufRead<R> {
    inner: R,
    buf: Box<[u8]>,
    pos: usize,
    cap: usize,
}

// capacity(), buffer() 无任何限制
impl <R> BufReader<R> {
    pub fn capcity(&self) -> usize {}
    pub fn buffer(&self) -> &[u8] {}
}

// new - Read trait
impl <R: Read> BufReader<R> {
    pub fn new(inner: R) -> Self<R> {}
    pub fn with_capacity(capacity: usize, inner: R) -> BufReader<R> {}
}

impl <R> fmt::Debug for BufReader<R> where R: fmt::Debug {}
```

### Copy

#### is copy

原生类型：函数、不可变引用、裸指针，数组、元组 (如果其内部数据结构实现了Copy)

- bool
- char
- i8
- u64
- i64
- `usize`
- `fn()` - pointer
- `*const String` - raw pointer
- `*mut String` - raw pointer
- `&[Vec<u8>]` - immutable reference
- `&String` - immutable reference
- `[u8; 4]` array
- `(&str, &str)` tuple

#### not copy

可变引用、非固定大小的数据结构

- str - usized or dynamic sized type
- `[u8]`
- `Vec<u8>`
- String
- `&mut String` - mutable reference
- `[Vec<u8>; 4]` - array | tuple with values that not Copy is not Copy
- `(String, u32)`

## Futures

![[../../images/rust-async-await.png]]

### 自引用结构 Self-Referential Structure

一旦被移动，原来的指针就会指向旧的地址。

![[../../images/rust-self-referential-structure.png]]

Pin 的目的是，把 T 的内存位置锁住，从而避免移动后自引用类型带来的 invalid pointer 问题。

![[../../images/rust-pin-self-referential-structure.png]]

## References

- [Rust编程第一课 - 极客时间专栏](#)
