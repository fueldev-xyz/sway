# 常见集合

Sway 标准库包含一些非常有用的数据结构，称为集合。大多数其他数据类型表示一个特定的值，但集合可以包含多个值。与内置的数组和元组类型不同，它们分配在 "堆栈" 上并且大小不可增长，这些集合指向的数据存储在 "堆" 上或合约 "存储" 中，这意味着数据量不需要在编译时知道，并且可以随着程序运行而增长。每种类型的集合具有不同的功能和成本，选择适合当前情况的集合是您将逐渐培养的技能。在本章中，我们将讨论在 Sway 程序中经常使用的三种集合：

- 在堆上的向量允许您存储可变数量的值在一起。
- `StorageVec` 类似于在堆上的向量，但使用持久存储。
- `StorageMap` 允许将值与特定键关联起来。

我们将讨论如何创建和更新向量、`StorageVec` 和 `StorageMap`，以及每个集合的特点。

- [堆上的向量](./vec.md)
- [`StorageVec`](./storage_vec.md)
- [`StorageMap`](./storage_map.md)
