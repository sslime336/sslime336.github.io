---
title: "Rust Memory Ordering"
date: 2023-06-13T20:53:27+08:00
tags: ["memory order", "todo"]
draft: false
---

首先要说的是，内存排序相对来说比较底层，对于不同的 CPU 硬件设计都有可能不同

Memory Ordering 针对的是多核(多线程)原子操作的内存连续性(Coherence)和一致性(Consitency)问题

- Coherence 定义了一个读操作能获得什么样的值[^1]
- Consitency 定义了何时一个写操作的值会被读操作获得。

<!--more-->

## 为什么会出现内存的一致性/连续性问题

我们知道 CPU 访问主存需要的开销非常大（上 100 个 cycles），如果 CPU 想存储一个数据到
memory 而该数据又不在 data cache 中，则这个过程是相当耗时的，大大地降低了 CPU 性能

为了进一步加快内存访问速度，CPU 设计中引入了 store buffer 这个部件，store
buffer 是位于执行单元(比如浮点执行单元 FPU, 算术逻辑单元 ALU 等)与 data cache
之间的小一块存储单元

使用 store buffer 的好处不仅在于加快存储的速度，另外还可以利用 store buffer 实现乱序执行
以及实现推断执行(speculative execution)失败后的回退 roll back，等[^2]

下面是 Rust 官方文档中的内存顺序，根据官网提示，这里并不全:

> Non-exhaustive enums could have additional variants added in future.
> Therefore, when matching against variants of non-exhaustive enums, an extra
> wildcard arm must be added to account for any future variants.

## 原子内存顺序[^3]

- Relaxed

  没有顺序约束，只是原子操作

- Release/Acquire

  这里 Release 对应写(store)操作，Acquire 对应读(load)操作

  这两这是配合着使用的

  当一个线程 store with Release 写原子类型对象, 而有另一个线程 load with Acquire 度原子类型对象时,
  那么在写及写之前的所有写原子操作都是发生在另一个线程中读该原子类型之后的所有读原子操作之前

- AcqRel

  和前者相同，可以看做一种简写，对于读(load)采用 Acquire，对于写(store)采用 Release

- SeqCst

  若某一原子类型对象在不同线程中使用 SeqCst 读写, 那么该原子操作之前的所有读写原子操作都先于该原子操作之后的读写操作

[^1]: Furion W. 在回答"如何理解 C++11 的六种 memory order？". https://www.zhihu.com/question/24301047/answer/83422523
[^2]: shawn. 内存一致性(Memory Consistency). https://zhuanlan.zhihu.com/p/422848235?utm_id=0
[^3]: mengsuenyan. Rust Ordering 语义理解. https://www.cnblogs.com/mengsuenyan/p/13043691.html
