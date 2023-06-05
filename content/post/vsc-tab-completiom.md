---
title: "VSCode tab completion"
date: 2023-06-05T20:52:42+08:00
tags: ["vscode","code-completion"]
draft: false
---

今天在写汇编的时候发现我的 VSC 出现了一种恶心的 tab 补全现象

```asm
TABLE:  
	DB 3FH,06H,5BH,4FH,66H     ; 0  1   2   3   4
                                  ^
                                  |
```

当我紧贴着 `0` 敲击 tab 的时候，它没有插入制表符或是空格，而是把 `0` 补全成了
`0000H`，我知道这是我之前设置的补全选项，但是忘了在哪了，现在记录一下免得再犯傻(x)

<!--more-->

`Settings` --> 搜索 `tab` --> 找到 `Editor: Tab Completion` --> 设置为 `off`

*仔细看一下 `on` 的描述:*

> Tab complete will insert the best matching suggestion when pressing tab.

所以还是不要乱设置看起来很 nice 但不特别需要的 feature 了吧 qwq
