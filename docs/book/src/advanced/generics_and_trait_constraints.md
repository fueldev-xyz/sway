# 泛型和特质约束

## 泛型作为约束

在高层次上，Sway 允许您定义约束或限制，从而在编写抽象和可重用的代码以及强制执行编译时检查之间取得平衡，以确定您编写的抽象代码是否正确。

“抽象和可重用”的部分主要来自[泛型类型](./generic_types.md)，而“强制执行编译时检查”的部分主要来自特质约束。
泛型类型可以与函数、结构体和枚举一起使用（正如本书中所见），但它们也可以与特质一起使用。

## 泛型特质

将泛型类型与特质结合使用，允许您编写抽象和可重用的特质，可用于任意数量的数据类型。

例如，想象一下，您想编写一个用于在不同类型之间进行转换的特质。这类似于 Rust 的 `Into` 和 `From` 特质。在 Sway 中，您的转换特质将如下所示：

```sway
{{#include ../../../../examples/traits/src/main.sw:trait_definition}}
```

特质 `Convert` 接受一个泛型类型 `T`。`Convert` 有一个名为 `from` 的方法，该方法接受一个 `T` 类型的参数并返回一个 `Self`。这意味着当您为数据类型实现 `Convert` 时，`from` 将返回该数据类型的类型，但将您定义为 `T` 的类型作为输入。下面是一个示例：

```sway
{{#include ../../../../examples/traits/src/main.sw:trait_impl}}
```

在此示例中，您有两种不同的数据类型，`Square` 和 `Rectangle`。您知道所有正方形都是矩形，因此 `Square` 可以转换为 `Rectangle`（但反之不行），因此您可以为这些类型实现转换特质。

如果我们想调用这些方法，我们可以这样做：

```sway
{{#include ../../../../examples/traits/src/main.sw:trait_usage}}
```

## 特质约束

特质约束允许您使用泛型类型和特质来对您在程序中愿意接受的抽象代码进行约束。
这些约束采用编译时检查以确保正确性。

如果我们想要在上一节的 `Convert` 特质中使用特质约束，我们可以像这样做：

```sway
{{#include ../../../../examples/traits/src/main.sw:trait_constraint}}
```

这个函数允许您接受任何泛型数据类型 `T` 并将其转换为 `Rectangle` 类型，只要 `Convert<T>` 已为 `Rectangle` 实现即可。
调用此函数时，如果为 `Convert<T>` 没有为 `Rectangle` 实现而传入类型 `T`，则 Sway 的编译时检查将失败。
