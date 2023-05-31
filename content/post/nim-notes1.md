---
title: "Nim Notes1"
date: 2023-05-31T09:09:42+08:00
tags: ["todo"]
draft: true
---

> Nim is a statically typed compiled systems programming language.
> It combines successful concepts from mature languages like Python, Ada and Modula. 

<!--more-->

## 话虽这么说...

1. 所谓的系统编程语言，就看你怎么理解了，可以是底层和硬件打交道的那种，也可以 Go 那样在云原生时代的系统基础建设。
对于前者，可以这么说，相比于直接写 C，Nim 确实方便一点，但不多。后者，除了一些区块链的应用外我没见过其他比较突出的
“杀手级”应用(不只限于云原生)
2. 确实是“编译型”，但特点是可以使用不同的后端，你可以编译成 C，或者直接走 LLVM 的路子生成 IR，前者对于历史代码的复用还是
不错的，后者编译出来程序的效率~~可能会更高？~~ 全看选择哪种编译器了(上游的 nimc 或者开源的 [nlvm](https://github.com/arnetheduck/nlvm))

## talk is cheap

Nim 的语法在一部分人眼中看起来很奇怪(~~哪个语言不是这样~~)

*而且 Nim 官方是 2 缩进*

下面用 Nim 简单**读**取下 fat32 格式镜像元数据，代码命名为 `fat32reader.nim`

```nim
## 读取 FAT32 格式镜像的元数据
## 
## 不涉及对于镜像的具体操作
## 
## (没错，两个 `#` 代表的是文档注释，Nim 的一个特点就是基础设施相对齐全也不是不能用)
## 
## 可以通过 `nim doc <filename>` 生成一个本地的文档，这是后话了
## 
## 我们用到的 FAT32 镜像是个相对标准的镜像，在磁盘上的布局大致如下:
## 
## ```text
##  +------------------+--------+------+------+-------------+
##  | Boot Sector(DBR) | FSInfo | FAT1 | FAT2 | Data Region |
##  +------------------+--------+------+------+-------------+
## ```
## 
## 具体里面有什么等会读出来再说

import std / strformat

import fat32reader / layout

proc readOut*(f: File): string =
  result = ""

```

