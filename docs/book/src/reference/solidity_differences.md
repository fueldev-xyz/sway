# 与 Solidity 的差异

本页面概述了 Sway 与 Solidity 以及 FuelVM 与 EVM 之间的一些关键差异。

## 底层虚拟机

Sway 目标的底层虚拟机是 FuelVM，规范见 [此处](https://github.com/FuelLabs/fuel-specs)。Solidity 目标的是以太坊虚拟机 (EVM)，规范见 [此处](https://ethereum.github.io/yellowpaper/paper.pdf)。

## 字长

FuelVM 中的字长为 64 位 (8 字节)，而不是 EVM 中的 256 位 (32 字节)。因此，所有小于或等于 `u64` 的原始整数都存储在寄存器中；`u256` 由于大于寄存器大小，以及哈希值 (类型为 `b256`) 不存储在寄存器中，而是存储在内存中。因此，它们是指向包含其数据的 32 字节内存区域的指针。

## 仅支持无符号整数

作为原始类型，仅提供无符号整数：`u8`、`u16`、`u32`、`u64` 和 `u256`。在 FuelVM 中，不支持有符号整数及其算术运算。如果需要，可以在高级库中实现有符号整数和有符号整数算术。

## 全局回滚

FuelVM 中的恐慌 (在 Solidity 和 EVM 中称为 "回滚") 是全局的，即它们无法被捕获。恐慌将完全且无条件地回滚事务的状态影响，减去使用的燃料。

## 默认安全数学运算

<!-- This section should explain safe math in Fuel vs EVM -->
<!-- safe_math:example:start -->

FuelVM 中的数学运算默认是安全的 (即任何溢出或异常都会导致恐慌)。安全检查是在 VM 实现中进行的，而不是像 [Solidity 的默认安全数学运算](https://docs.soliditylang.org/en/latest/080-breaking-changes.html#silent-changes-of-the-semantics) 那样在字节码级别进行。

<!-- safe_math:example:end -->

## 无\*代码大小限制

Sway 合约没有实际的代码大小限制。物理限制由 [`VM_MAX_RAM` VM 参数](https://fuellabs.github.io/fuel-specs/master/vm#parameters) 管理，目前编写时为 64 MiB。

## 账户类型

FuelVM 中的账户类型在原始 `b256` 哈希周围有类型安全的包装，以清楚地区分它们的各自类型。该包装器 `Address` 反映了 EOA (Externally Owned Account) 的情况，并且在 EVM 上下文中具有持有 UTXO 的能力。另一个包装器 `ContractId` 反映了 EVM 中已部署合约的情况，但不能持有 UTXO。
