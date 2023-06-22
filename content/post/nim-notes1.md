---
title: "Nim Notes1"
date: 2023-05-31T09:09:42+08:00
tags: ["todo"]
draft: true
---

> Nim is a statically typed compiled systems programming language.
> It combines successful concepts from mature languages like Python, Ada and Modula.

<!--more-->

## FAQs

1. Nim 能直接编译成机器码吗?

这一点~~除了汇编其他语言基本都不能~~，首先需要知道的是，Nim 官方的编译器是先将 Nim 编译
成 C/C++/Obj-C (甚至是 JavaScript)，之后再利用不同的后端编译成可执行文件

如果说的是不生成中间的 C 代码，当然可以，比如第三方的 `nimc` 前端 [`nlvm`](https://github.com/arnetheduck/nlvm)，
生成 LLVM IR 再生成机器码，没有中间的 C 代码

但还有一种做法是使用 `clang` 代替传统 `gcc` 编译生成出来的 C 代码以获得更高的
执行效率(能选择不同的后端也是 Nim 的~~一大特点~~)

## talk is cheap, show me the code

接下来我们就来举个例子(比如一个简单的 B 站爬虫):

```nim
## 一个简单的 B 站爬虫，爬取一个简单的视频
```

Nim 中的文档注释以 `##` 开头，文档注释在后面可以用用于生成本地的文档

普通的注释则以 `#` 开头

> **注意**  
> Nim 中还有多行注释，我个人习惯使用单行的形式，如果好奇多行注释怎么写，建议参考官方文档
