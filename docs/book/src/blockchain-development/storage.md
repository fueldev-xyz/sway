# 存储

在开发[智能合约](../sway-program-types/smart_contracts.md)时，通常需要某种形式的持久性存储。在这种情况下，持久性存储，通常在此上下文中称为 _存储_，是一个可以存储在合约内部并持久存在的值的地方。这与 _内存_ 中的常规值形成对比，后者在合约退出后消失。

从传统编程角度来看，合约存储就像将数据保存到硬盘上一样。这些数据在保存它的程序退出后仍然存在。这些数据是持久的。使用内存就像在程序中声明一个变量：它存在于程序的整个运行期间，是非持久的。

一些存储的基本用例包括为合约声明所有者地址和在钱包中保存余额。

## 通过 `storage` 关键字访问存储

在存储中声明变量需要一个 `storage` 块，其中包含所有变量、它们的类型和它们的初始值的列表。初始值可以是在编译期间可以计算为常量的任何表达式，如下所示：

```sway
{{#include ../../../../examples/basic_storage_variables/src/main.sw:basic_storage_declaration}}
```

要写入存储变量，您需要使用 `storage` 关键字，如下所示：

```sway
{{#include ../../../../examples/basic_storage_variables/src/main.sw:basic_storage_write}}
```

要读取存储变量，您还需要使用 `storage` 关键字。您可以使用 `read()` 或 `try_read()`，但我们建议使用 `try_read()` 进行额外的安全性。

```sway
{{#include ../../../../examples/basic_storage_variables/src/main.sw:basic_storage_read}}
```

## 存储结构体

要在存储中存储结构体，必须在 `storage` 块中为每个变量分配值。这可以通过分配字段或使用可以在编译期间评估为常量的公共[构造函数](../basics/methods_and_associated_functions.md#constructors)来实现。

```sway
{{#include ../../../../examples/struct_storage_variables/src/main.sw:struct_storage_declaration}}
```

您可以同时写入结构体的两个字段和整个结构体，如下所示：

```sway
{{#include ../../../../examples/struct_storage_variables/src/main.sw:struct_storage_write}}
```

同样的方法也适用于从存储中读取结构体，可以读取单个字段和整个结构体，如下所示：

```sway
{{#include ../../../../examples/struct_storage_variables/src/main.sw:struct_storage_read}}
```

## 常见的存储集合

我们支持以下常见的存储集合：

- `StorageMap<K, V>`
- `StorageVec<T>`
- `StorageBytes`
- `StorageString`

请注意，这些类型在编译期间不会被初始化。这意味着如果您尝试在设置存储之前从存储映射中访问键，例如，该调用将导致回滚。

在存储中声明这些变量需要一个 `storage` 块，如下所示：

```sway
{{#include ../../../../examples/advanced_storage_variables/src/main.sw:advanced_storage_declaration}}
```

### `StorageMaps<K, V>`

标准库中提供了通用的存储映射，称为 `StorageMap<K, V>`，必须在 `storage` 块中定义，允许您调用 `insert()` 和 `get()` 分别在特定键处插入值和获取这些值。有关 `StorageMap<K, V>` 的更多信息，请参阅[存储映射](../common-collections/storage_map.md)。

要写入存储映射，请调用 `insert()` 或 `try_insert()` 函数，如下所示：

```sway
{{#include ../../../../examples/advanced_storage_variables/src/main.sw:map_storage_write}}
```

以下示例演示了如何从存储映射中读取：

```sway
{{#include ../../../../examples/advanced_storage_variables/src/main.sw:map_storage_read}}
```

### `StorageVec<T>`

标准库中提供了通用的存储向量，称为 `StorageVec<T>`，必须在 `storage` 块中定义，允许您调用 `push()` 和 `pop()` 来分别从向量中推送和弹出值。有关 `StorageVec<T>` 的更多信息，请参阅[存储向量](../common-collections/storage_vec.md)。

以下示例演示了如何写入 `StorageVec<T>`：

```sway
{{#include ../../../../examples/advanced_storage_variables/src/main.sw:vec_storage_write}}
```

以下示例演示了如何从 `StorageVec<T>` 中读取：

```sway
{{#include ../../../../examples/advanced_storage_variables/src/main.sw:vec_storage_read}}
```

### `StorageBytes`

`StorageBytes` 是标准库中提供的一种存储 `Bytes` 的类型，必须在 `storage` 块中定义。`StorageBytes` 无法像 `StorageVec<T>` 或 `StorageMap<K, V>` 那样进行操纵，但以更有效的方式存储字节，从而减少 gas 消耗。只能读取/写入 `Bytes` 的全部内容。这意味着任何更改都需要将整个 `Bytes` 加载到堆中，进行更改，然后再次存储它。如果需要频繁更改，建议使用 `StorageVec<u8>`。

以下演示了如何写入 `StorageBytes`：

```sway
{{#include ../../../../examples/advanced_storage_variables/src/main.sw:bytes_storage_write}}
```

以下演示了如何从 `StorageBytes` 中读取：

```sway
{{#include ../../../../examples/advanced_storage_variables/src/main.sw:bytes_storage_read}}
```

### `StorageString`

标准库中提供了 `String` 的存储类型 `StorageString`，必须在 `storage` 块中定义。`StorageString` 无法像 `StorageVec<T>` 或 `StorageMap<K, V>` 那样进行操纵。只能读取/写入 `String` 的全部内容。

以下演示了如何写入 `StorageString`：

```sway
{{#include ../../../../examples/advanced_storage_variables/src/main.sw:string_storage_write}}
```

以下演示了如何从 `StorageString` 中读取：

```sway
{{#include ../../../../examples/advanced_storage_variables/src/main.sw:string_storage_read}}
```

## 高级存储

有关更高级的存储技术，请参阅[高级存储](../advanced/advanced_storage.md)页面。
