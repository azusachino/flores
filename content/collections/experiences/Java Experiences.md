---
title: Java Experiences
created: 2023-01-09 00:00:00
modified: 2023-01-09 14:05:33
tags: [Java]
---

被线程池拒绝的任务，不会触发 run 方法，也会导致 FutureTask 不会从NEW状态向后流转至其他状态。

## FutureTask

在Submit异步事件到线程池的时候，若要使用 Future.get，务必添加超时时间，否则有可能一直阻塞在 `LockSupport.park` 这个位置，导致内存泄露问题。

且，在自定义 `RejectedPolicy` 的时候，也要注意处理 `rejectedException`，保证 `FutureTask` 的状态能够向后流转，需要抛出 `RejectedExecutionException`，让 `FutureTask` 捕获，以进入到 `Rejected` 状态，从而 `unpark` 上述的 `Future.get` 导致的 `LockSupport.park` 等待。
