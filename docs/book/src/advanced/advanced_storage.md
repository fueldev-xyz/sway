# 高级存储

## 嵌套存储集合

通过使用 `StorageKey`，您可以拥有嵌套的存储集合，例如将 `StorageString` 存储在 `StorageMap<K, V>` 中。

例如，这里我们在一个 `storage` 块中声明了一些常见的嵌套存储类型：

```sway
{{#include ../../../../examples/nested_storage_variables/src/main.sw:nested_storage_declaration}}
```

请注意，需要进行存储初始化才能实现这一点。

### 将 `StorageVec<T>` 存储在 `StorageMap<K, V>` 中

以下演示了如何将嵌套在 `StorageMap<T, V>` 中的 `StorageVec<T>` 写入：

```sway
{{#include ../../../../examples/nested_storage_variables/src/main.sw:nested_vec_storage_write}}
```

以下演示了如何从嵌套在 `StorageMap<T, V>` 中的 `StorageVec<T>` 中读取：

```sway
{{#include ../../../../examples/nested_storage_variables/src/main.sw:nested_vec_storage_read}}
```

### 将 `StorageString` 存储在 `StorageMap<K, V>` 中

以下演示了如何将嵌套在 `StorageMap<T, V>` 中的 `StorageString` 写入：

```sway
{{#include ../../../../examples/nested_storage_variables/src/main.sw:nested_string_storage_write}}
```

以下演示了如何从嵌套在 `StorageMap<T, V>` 中的 `StorageString` 中读取：

```sway
{{#include ../../../../examples/nested_storage_variables/src/main.sw:nested_string_storage_read}}
```

### 将 `StorageBytes` 存储在 `StorageVec<T>` 中

以下演示了如何将嵌套在 `StorageVec<T>` 中的 `StorageBytes` 写入：

```sway
{{#include ../../../../examples/nested_storage_variables/src/main.sw:nested_vec_storage_write}}
```

以下演示了如何从嵌套在 `StorageVec<T>` 中的 `StorageBytes` 中读取：

```sway
{{#include ../../../../examples/nested_storage_variables/src/main.sw:nested_vec_storage_read}}
```

## 存储命名空间

如果希望存储中的值位于不同的位置，例如在加载代码时避免与另一个合约的存储发生冲突，可以使用命名空间注解为插槽计算添加盐。

```sway
{{#include ../../../../examples/storage_namespace/src/main.sw:storage_namespace}}
```

## 手动存储管理

可以直接利用标准库中提供的 `std::storage::storage_api::write` 和 `std::storage::storage_api::read` 函数来利用 FuelVM 存储操作。使用这种方法，您将不得不手动分配用于存储的内部键。示例如下：

```sway
{{#include ../../../../examples/storage_example/src/main.sw}}
```

> **注意**：虽然这些函数可以用于任何数据类型，但它们主要用于数组，因为数组尚未在 `storage` 块中受到支持。但请注意，_所有_ 数据类型都可以作为 `StorageMap<K, V>` 中的键和/或值的类型使用，没有任何限制。
