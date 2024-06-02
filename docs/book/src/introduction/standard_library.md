# 标准库

<!-- 本节应解释标准库是什么 -->
<!-- std_lib:example:start -->

与 Rust 类似，Sway 也有自己的标准库。

Sway 标准库是可移植 Sway 软件的基础，是整个 Sway 生态系统的一组最小的共享抽象。它提供了核心类型，如 `Result<T, E>` 和 `Option<T>`，库定义的语言原语操作，本地资产管理，区块链上下文操作，访问控制，存储管理，以及其他 VM 类型的支持等功能。

<!-- std_lib:example:end -->

整个 Sway 标准库是一个名为 `std` 的 Forc 项目，可以直接在[这里](https://github.com/FuelLabs/sway/tree/master/sway-lib-std)找到。如果最新的 `master` 版本不兼容，请导航到相应的标签版本。您可以在[这里](https://fuellabs.github.io/sway/master/std/)找到最新的 `std` 文档。

## 使用标准库

标准库在使用 [`forc new`](../forc/commands/forc_new.md) 创建的所有 Forc 项目中隐式可用。换句话说，不需要手动将 `std` 指定为显式依赖项。Forc 将自动使用与其版本匹配的 `std` 版本。

使用 `use` 关键字可以从标准库导入项目，就像从任何 Sway 项目导入项目一样。例如：

```sway
use std::storage::storage_vec::*;
```

这会将 `StorageVec` 类型导入当前命名空间。

## 标准库 Prelude

<!-- 本节应解释标准库 Prelude 是什么 -->
<!-- prelude:example:start -->

Sway 的标准库中包含多种内容。但是，如果您必须手动导入使用的每个项目，会显得非常冗长。但导入许多程序从未使用的内容也不好。需要找到一个平衡点。

Prelude 是 Sway 自动导入到每个 Sway 程序中的项目列表。它尽可能保持精简，重点关注几乎每个 Sway 程序都使用的内容。

当前版本的 Prelude 位于 [`std::prelude`](https://github.com/FuelLabs/sway/blob/master/sway-lib-std/src/prelude.sw)，并重新导出以下内容：

- [`std::address::Address`](https://github.com/FuelLabs/sway/blob/master/sway-lib-std/src/address.sw)，围绕 `b256` 类型的包装器，表示钱包地址。
- [`std::contract_id::ContractId`](https://github.com/FuelLabs/sway/blob/master/sway-lib-std/src/contract_id.sw)，围绕 `b256` 类型的包装器，表示合约的 ID。
- [`std::identity::Identity`](https://github.com/FuelLabs/sway/blob/master/sway-lib-std/src/identity.sw)，一个枚举，有两个可能的变体：`Address: Address` 和 `ContractId: ContractId`。
- [`std::vec::Vec`](https://github.com/FuelLabs/sway/blob/master/sway-lib-std/src/vec.sw)，一个可增长的堆分配向量。
- [`std::storage::storage_key::*`](https://github.com/FuelLabs/sway/blob/master/sway-lib-std/src/storage/storage_key.sw)，包含访问 `core::storage::StorageKey` 的 API，描述存储位置。
- [`std::storage::storage_map::*`](https://github.com/FuelLabs/sway/blob/master/sway-lib-std/src/storage/storage_map.sw)，合同存储中的键值映射。
- [`std::option::Option`](https://github.com/FuelLabs/sway/blob/master/sway-lib-std/src/option.sw)，一个枚举，表示值的存在或缺失。
- [`std::result::Result`](https://github.com/FuelLabs/sway/blob/master/sway-lib-std/src/result.sw)，一个枚举，适用于可能成功或失败的函数。
- [`std::assert::assert`](https://github.com/FuelLabs/sway/blob/master/sway-lib-std/src/assert.sw)，一个函数，如果提供的条件为 `false`，则回滚虚拟机。
- [`std::assert::assert_eq`](https://github.com/FuelLabs/sway/blob/master/sway-lib-std/src/assert.sw)，一个函数，如果条件 `v1` == `v2` 为 `false`，则回滚虚拟机并记录其两个输入 `v1` 和 `v2`。
- [`std::assert::assert_ne`](https://github.com/FuelLabs/sway/blob/master/sway-lib-std/src/assert.sw)，一个函数，如果条件 `v1` != `v2` 为 `false`，则回滚虚拟机并记录其两个输入 `v1` 和 `v2`。
- [`std::revert::require`](https://github.com/FuelLabs/sway/blob/master/sway-lib-std/src/revert.sw)，一个函数，如果提供的条件为 `false`，则回滚虚拟机并记录给定的值。
- [`std::revert::revert`](https://github.com/FuelLabs/sway/blob/master/sway-lib-std/src/revert.sw)，一个函数，回滚虚拟机。
- [`std::logging::log`](https://github.com/FuelLabs/sway/blob/master/sway-lib-std/src/logging.sw)，一个函数，记录任意堆栈类型。
- [`std::auth::msg_sender`](https://github.com/FuelLabs/sway/blob/master/sway-lib-std/src/auth.sw)，一个函数，获取发出调用的 `Identity`。
<!-- prelude:example:end -->
