# 已知问题和解决方法

## 已知问题

- [#870](https://github.com/FuelLabs/sway/issues/870): 必须在调用它们定义的任何函数之前定义所有 `impl` 块。 这包括同一 `impl` 声明中的兄弟函数，即，`impl` 中的函数目前不能互相调用。

## 缺失功能

- [#1182](https://github.com/FuelLabs/sway/issues/1182) `storage` 块中的数组尚未支持。有关如何使用标准库中的 `store` 和 `get` 直接管理存储插槽的详细信息，请参阅[手动存储管理](../blockchain-development/storage.md#manual-storage-management) 部分。但是，请注意，`StorageMap<K, V>` _支持_ 对 `K` 和 `V` 的任意类型，没有任何限制。

## 一般性问题

- 尚未实现编译器优化 passes，因此字节码将比在生产环境中更昂贵且更大。 请注意，最终优化器将支持零成本抽象，避免开发人员需要转到内联汇编以生成最佳代码的需求。
