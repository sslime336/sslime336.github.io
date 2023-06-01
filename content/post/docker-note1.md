---
title: "在 Docker 中使用 rust-nightly toolchain 构建"
date: 2023-06-01T09:13:32+08:00
tags: ["rust", "docker", "Dockerfile", "nightly"]
draft: false
---

一般来说，Rust 的 Docker 镜像可以选择 [`rust:latest`](https://hub.docker.com/_/rust)

但是这并没有提供 rust-nightly 的环境，解决办法有两个:

<!--more-->

1. 在构建前，使用 `RUN` 执行下面的操作:

    ```shell
    RUN rustup update nightly; rustup default nightly;
    ```
    因为

    > The Rust installs in the images are managed by rustup

    但这样可能会带来一些潜在的问题，详见 [rust-lang/docker-rust/issue#5](https://github.com/rust-lang/docker-rust/issues/5#issuecomment-322657207)

2. 或者使用官方提供的另一个镜像 [`rustlang/rust:nightly`](https://github.com/rust-lang/docker-rust-nightly)

    ```shell
    docker pull rustlang/rust:nightly
    ```

    ```Dockerfile
    FROM rustlang/rust:nightly

    # 其他内容...
    ```
