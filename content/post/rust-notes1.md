---
title: "Rust 配置在 debug/release 构建中的不同表现"
date: 2023-06-13T20:47:49+08:00
tags: ["rust"]
draft: false
---

可以通过 `#[cfg(debug_assertions)]` 和 `#[cfg(not(debug_assertions))]` 来控制

Reference: https://doc.rust-lang.org/reference/conditional-compilation.html#debug_assertions

<!--more-->

```rust
fn main() {
    #[cfg(not(debug_assertions))]
    println!("release mode build");
    #[cfg(debug_assertions)]
    println!("debug mode build");
}
```

```bash
# 输出: release mode build
cargo r --release

# 输出: debug mode build
cargo r
```
