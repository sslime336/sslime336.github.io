---
title: "Nim 不可变参数传递的优化"
date: 2023-06-02T12:46:10+08:00
tags: ["notes", "nim"]
draft: false
---

这个问题主要涉及到函数传值，但具体是传值还是传引用对于 Nim 来说并不回答，编译器会尽量采用更高效的方式

如果选用不同的 GC 策略，那么可能又是另一回事(比如 ARC/ORC 可能根据上下文来说就是移动语义)

这里只简单的考虑~~非常普通的~~参数传递情况

<!--more-->

下面是个例子:

```nim
type
  Person = object
    name: string
    age: Natural

proc intro(person: Person) =
  echo person.repr

when isMainModule:
  var p = Person(name: "John", age: 100)
  intro(p)
```

上面我们定义了一个 `Person` 类型，我们可以通过 `sizeof` 来查看它的大小:

```nim
echo "sizeof(Person): " & $sizeof(Person) & " Bytes"
```

结果如下:

```shell
sizeof(Person): 24 Bytes
```

可以看到当前的 `Person` 相对来说还是比较小的

接下来我们看一下调用 `intro` 时，变量 `p` 是如何传递的(下面是 C 代码的表示，为了方便看，我删掉了 mangle 的内容):

**注**: 我使用的官方编译器，所以会先编译出 C 代码，我配置的 C 编译器是 `clang` (如果你使用 `nlvm` 走 LLVM-IR 路线，那么这篇文章可能不是很适合这种情况，这里我们只讨论 `nimc` 的表现情况及结果)

```c
static const NimStringV2
name = {4, (NimStrPayload*)&{ 4 | NIM_STRLIT_FLAG, "John" }};

nimZeroMem((void*)(&p), sizeof(Person));
p.name = name;
p.age = ((NI) 100);
intro(p);
```

可以看到这里变量 `p` 的传递直接进行了一次拷贝

但如果我们对 `Person` 进行下面的修改:

```nim
  type
    Person = object
      name: string
      age: Natural
----------------------------------------
+     padding {.used.}: array[512, int]
----------------------------------------
```

我们加入了一个 `padding` 字段，使现在 `Person` 的大小变成了 `sizeof(Person): 4120 Bytes`

再看一下生成的 C 代码:

```c
static const NimStringV2
name = {4, (NimStrPayload*)&{ 4 | NIM_STRLIT_FLAG, "John" }};

nimZeroMem((void*)(&p), sizeof(Person));
p.name = name;
p.age = ((NI) 100);
intro((&p));
```

可以看到这里只是传递了 `p` 的地址

所以 Nim 参数的传递还是~~比较高效~~的(doge)
