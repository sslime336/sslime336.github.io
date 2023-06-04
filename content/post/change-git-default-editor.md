---
title: "更改 git 默认编辑器"
date: 2023-06-04T13:12:45+08:00
tags: ["git", "vi/vim", "GNU nano"]
draft: false
---

在 `git commit` 的时候，默认的编辑器是 [`GNU nano`](https://www.nano-editor.org/)，但对于重度 `vim` 用户来说，还是 `vim` 用的顺手(x)

这时候可以通过 `git config --global core.editor vi` 来修改全局默认的 commit 编辑器

<!--more-->

## `vi` 和 `vim` 的区别?

`vim` 兼容 `vi`，是后者的升级版

>   vim的这些优势主要体现在以下几个方面：
>
>   1、多级撤消
>
>   我们知道在vi里，按 u只能撤消上次命令，而在vim里可以无限制的撤消。
>
>   2、易用性
>
>   vi只能运行于unix中，而vim不仅可以运行于unix,windows ,mac等多操作平台。
>
>   3、语法加亮
>
>   vim可以用不同的颜色来加亮你的代码。
>
>   4、可视化操作
>
>   就是说vim不仅可以在终端运行，也可以运行于x window、 mac os、 windows。
>
> ......
>
>   vi和vim都是Linux中的编辑器，不同的是vim比较高级，可以视为vi的升级版本。vi使用于文本编辑，但是vim更适用于coding

摘自: https://www.cnblogs.com/KiraEXA/p/5994078.html
