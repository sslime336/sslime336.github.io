---
title: "Nim 一本通(bushi)"
date: 2023-06-28T11:54:18+08:00
draft: false
---

本文假定您对 [Nim](https://nim-lang.org/) 已经有一定的了解，本文并非零基础教程

<!--more-->

## 安装🔭

建议的安装姿势: 使用 [`choosenim`](https://github.com/dom96/choosenim)

### Windows🖥️

从这里 https://github.com/dom96/choosenim/releases 下载压缩包(`.zip`)，不要下载 `.exe`

### Unix🎵

[Optional] 代表可选

```sh
[CHOOSENIM_CHOOSE_VERSION=stable] curl https://nim-lang.org/choosenim/init.sh -sSf | sh
```

或者

```sh
[CHOOSENIM_CHOOSE_VERSION=stable] wget -qO - https://nim-lang.org/choosenim/init.sh | sh
```

*可以通过环境变量 `CHOOSENIM_CHOOSE_VERSION` 来指定安装的版本，默认是最新的发行版

> Note: Nim 也有类似于 Rust 的 [nightly](https://github.com/nim-lang/nightlies/releases) 构建，这个构建是基于 [`devel`](https://github.com/nim-lang/Nim) 的.
>
> *Nim 虽然已经有相当一段时间的发展，但它仍然是一个小众的社区，所以我个人并不建议使用最新的 nightly(devel) 构建，因为您可能会收获一些无法立刻解决的 bug

### 配置⚙️

### `choosenim` 🔧

`choosenim` 会默认安装到 `~/.choosenim` 中，在 Windows 上是 `C:\User\<Username>\`.

之后各个版本的工具链会安装这个目录，`choosenim` 也提供了 `--choosenimDir` 来指定其安装位置，有兴趣的可以自己试一试.


### `nimble` 📦

`nimble` 类似于 Rust 的 `cargo`，是 Nim 的官方包管理器，在通过 `choosenim` 安装的时候会一块安装好.

`nimble` 的默认安装路径是 `~/.nimble`(请自行翻译成 Windows 上的相应目录😋)

如果有需要，可以通过配置文件来修改默认的包安装路径[^1]:

1. Windows: `C:\Users\<Username>\AppData\Roaming\nimble\nimble.ini`
2. Unix: `~/.config/nimble/nimble.ini`

举个栗子🌰:

```ini
# 这是我个人的配置
httpProxy = r"127.0.0.1:7890" # 下载依赖使用的代理
nimbleDir = r"E:\.nimble"     # 默认下载/安装的包放到 E 盘
```

### `nim.cfg` 🤖

接下来我们需要配置 Nim 的编译器，这个部分之所以存在是因为 Nim 当前的稳定版(昨天还是今天来着，刚从 1.6.12 更新到 1.6.14)仍然使用的是引用计数的 GC(refc)，而非高效的 ARC/ORC

你可以在 `~/.choosenim/toolchains/` 中找到你正实际使用的 Nim 版本，之后可以修改其配置文件 `./nim-1.6.14/config/nim.cfg`:

```nim
# 同理，附上我的配置，只需找个地方加上这个即可

# -------------------------------------------------------------------------- #
#                          User defined by sslime336                         #
# -------------------------------------------------------------------------- #
cc = clang # 使用 LLVM 而非传统 GCC，因为有些包会依赖 libclang，虽然可以直接提供给
           # GCC 这个库，但使用 LLVM 相对于 GCC 会带来更好的性能

--gc:arc # 使用 ARC.
         # ORC 虽然带来了循环引用内存泄露的消除，但是也引入的大多数时候不必要的性能损耗

# 我们需要将修改后的 `nimble` 位置添加进来，不然会找不到依赖
nimblepath=r"E:\.nimble\pkgs"
nimblepath=r"E:\.nimble\pkgs2"

# 这里我们根据不同的构建方式，如 -d:release, -d:danger，在指定位置生成带有后缀标记的缓存
# *_r 表示 release 的缓存, *_rd 是 danger，*_d 是默认的 debug 构建的缓存
@if release:
  nimcache = r"E:\.cache\nimcache\${projectName}_r"
@elif danger:
  nimcache = r"E:\.cache\nimcache\${projectName}_rd"
@else
  # 需要提醒一句的是，虽然 nimc 默认是 debug 构建，但如果不指定 -d:debug，是不会走到
  # @elif debug: 中的，所以我们直接使用 @else 来 workaround
  nimcache = r"E:\.cache\nimcache\${projectName}_d"
@end

# ---------------------- End of sslime336's define -------------------------
```


行了，到这里就差不多了，现在可以:

1. touch helloworld.nim and write some code 
2. use nimble init [pkgName] to do whatever you want

## 未完待续...


[^1]: https://github.com/nim-lang/nimble#configuration