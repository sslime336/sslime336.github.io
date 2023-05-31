---
title: "tokio tracing"
date: 2023-05-31T16:34:36+08:00
tags: ["trace", "rust", "tokio"]
draft: false
---

本文简单总结了 `tracing` 和 `tracing-subscriber` 的使用，详细内容请参阅官方文档:

- `tracing`: <https://docs.rs/tracing/latest/tracing/index.html>
- `tracing-subscriber`: <https://docs.rs/tracing-subscriber/latest/tracing_subscriber/>

[`tracing`](https://docs.rs/tracing/latest/tracing/) 是一个由 [`tokio`](https://github.com/tokio-rs) 开发并维护
的框架，这个框架用于对 Rust 程序进行检测，收集结构化、事件驱动的诊断信息。

*部分翻译自: <https://github.com/tokio-rs/tracing#overview>*

<!--more-->

该框架虽然是由 `tokio` 开发并维护的，但其并不需要 `tokio` 的运行时

## 使用方法

### 对于可执行文件(bin crate)

为了记录一系列事件驱动的 trace，应用程序需要实现一个与 `tracing` 兼容的信息收集器(`collector`)

`collector` 实现了一种收集 trace 信息的方式，比如直接将信息结构化输出到 `stdout`(如使用: `info!`, `debug!`, `error!`)

*`tracing` 按照 Rust 门面日志库 [`log`](https://github.com/rust-lang/log) 开发，在其依赖项里面我们可以看到这点:*

```toml
# tracing/Cargo.toml
log = { version = "0.4.17", optional = true }
```

上面说到对于具体的 trace，我们需要使用一个 `collector` 将其收集并处理，对于简单的场景，比如只需要
将 trace 当成日志打印到控制台，我们可以使用 `tracing-subscriber` 的 `fmt` 模块:

先添加依赖:

```toml
# lab-rs/Cargo.toml
[dependencies]
tracing = "0.1"
tracing-subscriber = "0.3.17"
```

```rust
use tracing::info;

fn main() {
    // 这里调用 fmt::init() 会装载一个全局的 tracing subscriber 来监听
    // 当前发生的事情(events)
    //
    // 这里的 subcriber 就是一个 `collector`，只不过这是 `tokio` 官方实现的，如果要
    // 实现自己的 `collector`，需要保证其与 `tracing` 兼容，即实现 [`tracing::Subcriber`]
    tracing_subscriber::fmt::init();

    info!("about to exit main");
}
```

`cargo r` 一下:

```bash
2023-05-31T09:14:17.211140Z  INFO lab_rs: about to exit main
```

可以看到我们的日志信息被收集并打印到了控制台

而如果删去 `tracing_subscriber::fmt::init();`，你会发现什么都没有输出

**注**: 如果想要实现自己的 `collector` 则其需要满足 `tracing` 的 [`Subscriber`](https://docs.rs/tracing/latest/tracing/trait.Subscriber.html) trait

### 对于库文件(lib crate)

我们只需要使用 [`tracing`]() 提供的一系列 marco 和方法就行，不需要使用 `subscriber`

> Libraries should only rely on the tracing crate and use the provided macros
> and types to collect whatever information might be useful to downstream
> consumers.

## Subscriber の `Layer`，`Filter` 和 `Registry`

懒得写了直接看官方文档吧 😛

[`Layer` & `Filter`](https://docs.rs/tracing-subscriber/latest/tracing_subscriber/index.html#layers-and-filters)

[`Registry`](https://docs.rs/tracing-subscriber/latest/tracing_subscriber/registry/index.html)
