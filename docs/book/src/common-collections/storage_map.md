# 存储映射

另一个重要的常见集合是存储映射。

<!-- 此部分应解释 Sway 中的存储映射 -->
<!-- storage_map:example:start -->

标准库中的类型 `StorageMap<K, V>` 存储了键类型为 `K`，值类型为 `V` 的映射，使用哈希函数确定如何将这些键和值放入 _存储槽_ 中。这类似于[Rust 的 `HashMap<K, V>`](https://doc.rust-lang.org/std/collections/struct.HashMap.html)，但有一些区别。

当您想要通过键查找数据时，存储映射非常有用，而不是像使用向量那样通过索引。例如，当构建基于账本的子货币智能合约时，您可以在存储映射中跟踪每个钱包的余额，其中每个键都是钱包的 `Address`，而值是每个钱包的余额。给定一个 `Address`，您可以检索其余额。

与 `StorageVec<T>` 类似，`StorageMap<K, V>` 只能在合约中使用，因为只有合约可以访问持久存储。

`StorageMap<T>` 包含在[标准库预导入](../introduction/standard_library.md#standard-library-prelude)中，这意味着不需要手动导入它。

<!-- storage_map:example:end -->

## 创建新的存储映射

要创建一个新的空存储映射，我们必须在 `storage` 块中声明映射，如下所示：

```sway
{{#include ../../../../examples/storage_map/src/main.sw:storage_map_decl}}
```

<!-- 此部分应解释如何在 Sway 中实现存储映射 -->
<!-- use_storage_maps:example:start -->

与任何其他存储变量一样，声明 `StorageMap` 时需要两样东西：类型注释和初始化器。初始化器只是类型为 `StorageMap` 的空结构体，因为 `StorageMap<K, V>` 本身就是一个空结构体！`StorageMap<K, V>` 的所有有趣的内容都在其方法中实现。

存储映射，就像 `Vec<T>` 和 `StorageVec<T>` 一样，是使用泛型实现的，这意味着标准库提供的 `StorageMap<K, V>` 类型可以将任何类型 `K` 的键映射到任何类型 `V` 的值。在上面的示例中，我们告诉 Sway 编译器 `map` 中的 `StorageMap<K, V>` 将把类型为 `Address` 的键映射到类型为 `u64` 的值。

<!-- use_storage_maps:example:end -->

## 更新存储映射

<!-- 此部分应解释如何在 Sway 中更新存储映射 -->
<!-- update_storage_maps:example:start -->

要将键值对插入存储映射，我们可以使用 `insert` 方法。

<!-- update_storage_maps:example:end -->

例如：

```sway
{{#include ../../../../examples/storage_map/src/main.sw:storage_map_insert}}
```

请注意两个细节。首先，为了使用 `insert`，我们需要首先使用 `storage` 关键字访问存储映射。其次，因为 `insert` 需要 _写入_ 存储，所以调用 `insert` 的 ABI 函数需要添加 `#[storage(write)]` 注解。

> **注意**
> 对于尝试插入映射的任何私有函数，都需要在合约中添加存储注解。

<!-- markdownlint-disable-line MD028 -->

> **注意**
> 声明 `StorageMap<K, V>` 时无需添加 `mut` 关键字。所有存储变量默认都是可变的。

## 访问存储映射中的值

<!-- 此部分应解释如何在 Sway 中访问存储映射值 -->
<!-- access_storage_maps:example:start -->

我们可以通过将其 `key` 提供给 `get` 方法来从存储映射中获取值。

<!-- access_storage_maps:example:end -->

例如：

```sway
{{#include ../../../../examples/storage_map/src/main.sw:storage_map_get}}
```

在这里，`value1` 将具有与第一个地址关联的值，结果将为 `42`。`get` 方法返回一个 `Option<V>`；如果存储映射中没有该键的值，`get` 将返回 `None`。此程序通过调用 `unwrap_or` 处理 `Option`，如果 `map` 没有键的条目，则将 `value1` 设置为零。

## 具有多个键的存储映射

可以使用元组作为键实现具有多个键的映射。例如：

```sway
{{#include ../../../../examples/storage_map/src/main.sw:storage_map_tuple_key}}
```

## 嵌套存储映射

可以按以下方式嵌套存储映射：

```sway
{{#include ../../../../examples/storage_map/src/main.sw:storage_map_nested}}
```

然后可以按以下方式访问嵌套映射：

```sway
{{#include ../../../../examples/storage_map/src/main.sw:storage_map_nested_access}}
```
