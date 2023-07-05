---
title: 'Rust 中的链接 #[linkage = "xxx"]'
date: 2023-07-05T12:20:54+08:00
tags: ["linkage", "link", "unstable", "rust"]
draft: false
---

Rust 中有一个方便的注解 `#[linkage]`，这是一个不稳定的 feature，网上关于它的
信息基本找不到，在我搜索半天后，在 Github 上找到了一个 tracking issue:
[rust-lang/rust issue#29603](https://github.com/rust-lang/rust/issues/29603)

<!--more-->

## 目前 Rust 支持的链接模型[^1]

Rust 是基于 LLVM 的，所以使用的也是 LLVM 的链接模型

```rust
match name {
    "appending" => Some(llvm::AppendingLinkage),
    "available_externally" => Some(llvm::AvailableExternallyLinkage),
    "common" => Some(llvm::CommonLinkage),
    "extern_weak" => Some(llvm::ExternalWeakLinkage),
    "external" => Some(llvm::ExternalLinkage),
    "internal" => Some(llvm::InternalLinkage),
    "linkonce" => Some(llvm::LinkOnceAnyLinkage),
    "linkonce_odr" => Some(llvm::LinkOnceODRLinkage),
    "private" => Some(llvm::PrivateLinkage),
    "weak" => Some(llvm::WeakAnyLinkage),
    "weak_odr" => Some(llvm::WeakODRLinkage),
    _ => None,
}
```

~~相关的值会转换成 LLVM 的链接，而 LLVM 现在的链接器还没有稳定，用的还是 gcc 的链接器?~~

e.g.

```rust
#[linkage = "weak"]
#[no_mangle]
fn main() -> ! {
    ...
}
```

## LLVM 中不同的链接属性[^2]

All Global Variables and Functions have one of the following types of linkage:

- `private`

  Global values with “private” linkage are only directly accessible by objects
  in the current module. In particular, linking code into a module with a
  private global value may cause the private to be renamed as necessary to avoid
  collisions. Because the symbol is private to the module, all references can be
  updated. This doesn’t show up in any symbol table in the object file.

- `internal`

  Similar to private, but the value shows as a local symbol (STB_LOCAL in the
  case of ELF) in the object file. This corresponds to the notion of the
  ‘static’ keyword in C.

- `available_externally`

  Globals with “available_externally” linkage are never emitted into the object
  file corresponding to the LLVM module. From the linker’s perspective, an
  available_externally global is equivalent to an external declaration. They
  exist to allow inlining and other optimizations to take place given knowledge
  of the definition of the global, which is known to be somewhere outside the
  module. Globals with available_externally linkage are allowed to be discarded
  at will, and allow inlining and other optimizations. This linkage type is only
  allowed on definitions, not declarations.

- `linkonce`

  Globals with “linkonce” linkage are merged with other globals of the same name
  when linkage occurs. This can be used to implement some forms of inline
  functions, templates, or other code which must be generated in each
  translation unit that uses it, but where the body may be overridden with a
  more definitive definition later. Unreferenced linkonce globals are allowed to
  be discarded. Note that linkonce linkage does not actually allow the optimizer
  to inline the body of this function into callers because it doesn’t know if
  this definition of the function is the definitive definition within the
  program or whether it will be overridden by a stronger definition. To enable
  inlining and other optimizations, use “linkonce_odr” linkage.

- `weak`

  “weak” linkage has the same merging semantics as linkonce linkage, except that
  unreferenced globals with weak linkage may not be discarded. This is used for
  globals that are declared “weak” in C source code.

- `common`

  “common” linkage is most similar to “weak” linkage, but they are used for
  tentative definitions in C, such as “int X;” at global scope. Symbols with
  “common” linkage are merged in the same way as weak symbols, and they may not
  be deleted if unreferenced. common symbols may not have an explicit section,
  must have a zero initializer, and may not be marked ‘constant’. Functions and
  aliases may not have common linkage.

- `appending`

  “appending” linkage may only be applied to global variables of pointer to
  array type. When two global variables with appending linkage are linked
  together, the two global arrays are appended together. This is the LLVM,
  typesafe, equivalent of having the system linker append together “sections”
  with identical names when .o files are linked.

  Unfortunately this doesn’t correspond to any feature in .o files, so it can
  only be used for variables like llvm.global_ctors which llvm interprets
  specially.

- `extern_weak`

  The semantics of this linkage follow the ELF object file model: the symbol is
  weak until linked, if not linked, the symbol becomes null instead of being an
  undefined reference.

- `linkonce_odr`, `weak_odr`

  Some languages allow differing globals to be merged, such as two functions
  with different semantics. Other languages, such as C++, ensure that only
  equivalent globals are ever merged (the “one definition rule” — “ODR”). Such
  languages can use the linkonce_odr and weak_odr linkage types to indicate that
  the global will only be merged with equivalent globals. These linkage types
  are otherwise the same as their non-odr versions.

- `external`

  If none of the above identifiers are used, the global is externally visible,
  meaning that it participates in linkage and can be used to resolve external
  symbol references.

It is illegal for a global variable or function declaration to have any linkage type other than external or extern_weak.

## LLVM 函数调用规范

_[其他非 LLVM 调用规范](../func-call-convention)_

- “ccc” - The C calling convention
- “fastcc” - The fast calling convention
- “coldcc” - The cold calling convention
- “cc 10” - GHC convention
- “cc 11” - The HiPE calling convention
- “webkit_jscc” - WebKit’s JavaScript calling convention
- “anyregcc” - Dynamic calling convention for code patching
- “preserve_mostcc” - The PreserveMost calling convention
- “preserve_allcc” - The PreserveAll calling convention
- “cxx_fast_tlscc” - The CXX_FAST_TLS calling convention for access functions
- “tailcc” - Tail callable calling convention
- “swiftcc” - This calling convention is used for Swift language.
- “swifttailcc”
- “cfguard_checkcc” - Windows Control Flow Guard (Check mechanism)
- “cc \<n\>” - Numbered convention

[^1]: https://github.com/rust-lang/rust/issues/29603#issuecomment-154123326
[^2]: https://llvm.org/docs/LangRef.html#linkage-types
