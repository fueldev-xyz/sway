# 常用的库类型

Sway 标准库是可移植 Sway 软件的基础，是广泛的 Sway 生态系统的一组最小共享抽象。它提供了核心类型、对语言原语的库定义操作、原生资产管理、区块链上下文操作、访问控制、存储管理以及对其他 VM 类型的支持，等等。可以在[这里](https://fuellabs.github.io/sway/master/std/index.html)查看标准库文档。

## `Result<T, E>`

<!-- This section should explain what the `Result` type is -->
<!-- result:example:start -->

`Result`类型用于返回和传播错误。它是一个`enum`，有两个变体：`Ok(T)`，表示成功并包含一个值，和`Err(E)`，表示错误并包含一个错误值。在这个定义中的`T`和`E`是类型参数，允许`Result`是泛型的，并且可以与任何类型一起使用。

<!-- result:example:end -->

```sway
{{#include ../../../../sway-lib-std/src/result.sw:docs_result}}
```

<!-- This section should explain when to use the `Result` type -->
<!-- use_result:example:start -->

每当预期并且可恢复的错误时，函数都会返回`Result`。

<!-- use_result:example:end -->

考虑以下示例：

```sway
{{#include ../../../../examples/result/src/main.sw}}
```

## `Option<T>`

<!-- This section should explain the `Option` type -->
<!-- option:example:start -->

`Option`类型表示可选值：每个`Option`要么是`Some`并包含一个值，要么是`None`，不包含值。在 Sway 代码中，`Option`类型非常常见，因为它们具有多种用途：

- 初始值可以使用`None`作为初始化器。
- 作为简单错误的返回值，在错误时返回`None`。

`Option`的实现基于变体进行匹配：如果是`Ok`，它将返回内部值；如果是`None`，它会[回滚](https://github.com/FuelLabs/fuel-specs/blob/master/src/fuel-vm/instruction-set.md#rvrt-revert)。

<!-- option:example:end -->

```sway
{{#include ../../../../sway-lib-std/src/option.sw:docs_option}}
```

<!-- This section should explain when to use the `Option` type -->
<!-- use_option:example:start -->

`Option`通常与模式匹配配对，以查询值的存在并采取行动，使开发人员能够选择如何处理`None`情况。

<!-- use_option:example:end -->

下面是一个示例，使用模式匹配来处理除以 0 的无效除法，并返回一个`Option`：

```sway
{{#include ../../../../examples/option/src/main.sw}}
```
