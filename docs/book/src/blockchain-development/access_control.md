# 访问控制

在 Sway 中，智能合约需要能够限制对某些用户或合约的访问。与基于账户的区块链不同，基于 UTXO 的区块链（例如 Fuel）中的交易不一定有一个唯一的交易发送者。需要额外的逻辑来处理这种差异，并由标准库提供支持。

## `msg_sender`

为了提供类似于 EVM 中的访问控制体验，`std` 库提供了一个 `msg_sender` 函数，该函数基于调用和/或交易输入数据来标识一个唯一的调用者。

```sway
{{#include ../../../../examples/msg_sender/src/main.sw}}
```

`msg_sender` 函数的工作原理如下：

- 如果调用者是一个合约，则返回带有 `ContractId` 发送者变体的 `Ok(Sender)`。
- 如果调用者是外部的（即来自脚本），则检查交易中的所有币输入所有者。如果所有者都相同，则返回带有 `Address` 发送者变体的 `Ok(Sender)`。
- 如果调用者是外部的且币输入所有者不同，则无法确定调用者，返回 `Err(AuthError)`。

## 合约所有权

许多合约需要某种形式的所有权以进行访问控制。[SRC-5 拥有权标准](https://github.com/FuelLabs/sway-standards/blob/master/SRCs/src-5.md) 已经定义了一个可互操作的合约内部所有权接口。

为了实现这一点，使用 [Ownership 库](https://fuellabs.github.io/sway-libs/book/ownership/index.html) 来跟踪所有者。这允许使用变体 `Some(..)` 和 `None` 来设置和撤销所有权。这比直接使用 `Identity` 类型更好、更安全、更可读，其中撤销所有权必须使用某些魔术值如 `b256::zero()` 或其他方式。

以下是正确锁定函数以使只有所有者可以调用函数的示例：

```sway
{{#include ../../../../examples/ownership/src/main.sw:only_owner_example}}
```

设置所有权可以通过两种方式之一来完成：在编译时或运行时。

以下是在编译时正确设置合约所有权的示例：

```sway
{{#include ../../../../examples/ownership/src/main.sw:set_owner_example_storage}}
```

以下是在运行时正确设置合约所有权的示例：

```sway
{{#include ../../../../examples/ownership/src/main.sw:set_owner_example_function}}
```

以下是正确撤销合约所有权的示例：

```sway
{{#include ../../../../examples/ownership/src/main.sw:revoke_owner_example}}
```

以下是正确检索所有权状态的示例：

```sway
{{#include ../../../../examples/ownership/src/main.sw:get_owner_example}}
```

## 访问控制库

[Sway-Libs](../reference/sway_libs.md) 提供以下库以进一步实现访问控制。

- [Ownership 库](https://fuellabs.github.io/sway-libs/book/ownership/index.html)；用于对函数施加限制，使得只有**单个**用户可以调用它们。该库提供了 [SRC-5；所有权标准](https://github.com/FuelLabs/sway-standards/blob/master/SRCs/src-5.md) 的辅助函数。
- [Admin 库](https://fuellabs.github.io/sway-libs/book/admin/index.html)；用于对函数施加限制，使得只有少数选定的用户可以调用它们，类似于白名单。
- [Pausable 库](https://fuellabs.github.io/sway-libs/book/pausable/index.html)；允许合约实现紧急停止机制。
- [Reentrancy Guard 库](https://fuellabs.github.io/sway-libs/book/reentrancy/index.html)；用于检测和防止重入攻击。
