---
title: "Rust 序列化框架 serde 浅析"
date: 2023-05-31T12:07:28+08:00
tags: ["serde.rs", "todo"]
draft: true
---

*持续更新喵*

首先需要说明的是，[`serde`](https://github.com/serde-rs/serde) 只是一个序列化的框架

并没有提供特别实际的序列化方法

本文将从 `serde` 和 `json` 两个库来分析 `serde` 框架的序列化过程 

<!--more-->

> The Serde ecosystem consists of data structures that know how to serialize and
> deserialize themselves along with data formats that know how to serialize and
> deserialize other things. Serde provides the layer by which these two groups
> interact with each other, allowing any supported data structure to be serialized
> and deserialized using any supported data format.


官网: <https://serde.rs/>

## 基础概念(trait)

- Serialize
- Deserialize
- Serializer
- Deserializer
