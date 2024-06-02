# 高级类型

## 使用类型别名创建类型同义词

Sway 提供了声明类型别名的能力，以给现有类型赋予另一个名称。我们使用 `type` 关键字来实现这一点。例如，我们可以这样创建别名 `Kilometers` 来表示 `u64`：

```sway
{{#include ../../../../examples/type_aliases/src/main.sw:type_alias}}
```

现在，别名 `Kilometers` 是 `u64` 的一个 _同义词_。请注意，`Kilometers` **不是** 一个单独的新类型。具有类型 `Kilometers` 的值将被视为与 `u64` 类型的值相同：

```sway
{{#include ../../../../examples/type_aliases/src/main.sw:addition}}
```

因为 `Kilometers` 和 `u64` 是相同的类型，我们可以对两种类型的值进行相加，并且我们可以将 `Kilometers` 值传递给接受 `u64` 参数的函数。然而，使用这种方法，我们不会得到引入名为 `Kilometers` 的 _单独_ 新类型所获得的类型检查优势。换句话说，如果我们在某个地方混淆了 `Kilometers` 和 `i32` 值，编译器将不会给我们报错。

类型同义词的主要用途是减少重复。例如，我们可能有一个冗长的数组类型，如下所示：

```sway
[MyStruct<u64, b256>; 5]
```

在函数签名和类型注解中编写这种冗长的类型以及在整个代码中的各处都是繁琐且容易出错的。想象一下，一个项目充满了类似这样的代码：

```sway
{{#include ../../../../examples/type_aliases/src/main.sw:long_type_use}}
```

通过引入一个名为 `MyArray` 的别名，使这段代码更易管理，减少了重复。我们可以将别名 `MyArray` 替换为更短的别名 `MyArray`：

```sway
{{#include ../../../../examples/type_aliases/src/main.sw:long_type_use_shorter}}
```

这段代码更容易阅读和书写！选择一个有意义的名称作为类型别名可以帮助传达您的意图。
