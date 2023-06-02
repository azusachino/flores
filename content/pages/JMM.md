---
title: JMM
created: 2022-09-09 11:17:43
modified: 2023-05-25 19:18:45
aliases: [Java Memory Model]
tags: [CS, Java]
---

JMM stands for [[Java]] Memory Model. It is a specification that defines the rules and guarantees for how Java programs work with memory in a multi-threaded environment. The JMM defines the relationship between threads and memory, and specifies how changes made by one thread become visible to other threads.

In a multi-threaded Java program, each thread has its own local memory where it stores its variables. However, each thread also has access to the shared main memory. The JMM defines the rules for how changes made to variables in local memory by one thread become visible to other threads in the shared main memory.

- Happens-before relationship
  - If one operation happens before another operation, the first operation's effects are guaranteed to be visible to the second operation
- Synchronization
  - Synchronization allows threads to coordinate their access to shared resources and ensures that changes made by one thread are visible to other threads
- Volatile variables
  - Volatile variables ensure that changes made to variable by one thread are immediately visible to other threads
