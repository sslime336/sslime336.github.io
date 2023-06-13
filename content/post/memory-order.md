---
title: "C++ 中的六种 Memory Order"
date: 2023-06-13T20:53:27+08:00
tags: ["async", "memory order", "todo"]
draft: true
---

6 种 Memory Order 对应 3 种内存模型

<!--more-->

C++11 述了 6 种可以应用于原子变量的内存次序:

- momory_order_relaxed
- memory_order_consume
- memory_order_acquire
- memory_order_release
- memory_order_acq_rel
- memory_order_seq_cst

对应三种内存模型:

- sequential consistent:
    - memory_order_seq_cst
- relaxed: 
    - memory_order_seq_cst
- acquire release:
    - memory_order_consume
    - memory_order_acquire
    - memory_order_release
    - memory_order_acq_rel

Rust 也参考这里的内存模型: https://doc.rust-lang.org/core/sync/atomic/enum.Ordering.html

(不过是 C++ 20)

这里只是简单记录下理解，并尽可能举几个例子


## 参考:

https://www.zhihu.com/question/24301047/answer/83422523
