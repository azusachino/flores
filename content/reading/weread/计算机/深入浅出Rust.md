---
doc_type: weread-highlights-reviews
bookId: "22987515"
author: 范长春
cover: https://wfqqreader-1252317822.image.myqcloud.com/cover/515/22987515/t7_22987515.jpg
reviewCount: 0
noteCount: 2
isbn: 9787111606420
category: 计算机-编程设计
tags:
  - Book
  - Weread
created: 2022-12-05 16:39:58
modified: 2022-12-05 16:39:58
---

## 深入浅出Rust

![22987515](https://wfqqreader-1252317822.image.myqcloud.com/cover/515/22987515/t7_22987515.jpg)
- 书名: 深入浅出Rust
- 作者: 范长春
- 简介: 本书详细描述了Rust语言的基本语法，穿插讲解一部分高级使用技巧，并以更容易理解的方式解释其背后的设计思想。全书总共分五个部分。第一部分介绍Rust基本语法。第二部分介绍属于Rust独一无二的内存管理方式。这部分是本书的重点和核心所在，也是Rust语言的思想内核精髓之处。第三部分介绍Rust的抽象表达能力。第四部分介绍并发模型。在目前这个阶段，对并行编程的支持是新一代编程语言不可绕过的重要话题。第五部分介绍一些实用设施。Rust语言有许多创新，但它绝不是高高在上孤芳自赏的类型，设计者在设计过程中充分考虑了语言的工程实用性。
- 出版时间: 2018-08-01 00:00:00
- ISBN: 9787111606420
- 分类: 计算机-编程设计
- 出版社: 机械工业出版社

## 高亮划线

### 第15章 内部可变性


> [!quote] ⏱ 2022-03-07 12:14:36
> 为Cell类型把数据包裹在内部，用户无法获得指向内部状态的指针，这意味着每次方法调用都是执行的一次完整的数据移动操作。
 


### 第16章 解引用


> [!quote] ⏱ 2022-03-08 08:11:37
> Rc类型的引用计数是普通整数操作，只能用在单线程中；Arc类型的引用计数是原子操作，可以用在多线程中。
 


### 第17章 泄漏


> [!quote] ⏱ 2022-03-09 08:10:19
> use std::rc::Rc;use std::cell::RefCell;struct Node {next : Option<Rc<RefCell<Node>>>}impl Node {fn new() -> Node {Node { next : None}}}impl Drop for Node {fn drop(&mut self) {println! ("drop");}}fn alloc_objects() {let node1 = Rc::new(RefCell::new(Node::new()));let node2 = Rc::new(RefCell::new(Node::new()));let node3 = Rc::new(RefCell::new(Node::new()));node1.borrow_mut().next = Some(node2.clone());node2.borrow_mut().next = Some(node3.clone());node3.borrow_mut().next = Some(node1.clone());}fn main() {
 



## 读书笔记


## 本书评论

