# 区块链类型

Sway 从根本上讲是一种区块链语言，它提供了一系列针对区块链使用场景定制的类型。

这些类型通过标准库（[`lib-std`](https://github.com/FuelLabs/sway/tree/master/sway-lib-std)）提供，既增加了类型安全性，又使开发者的意图更加清晰。

## `Address` 类型

<!-- 此部分应解释 `Address` 类型 -->
<!-- address:example:start -->

`Address` 类型是原始 `b256` 类型的类型安全封装。与 EVM 不同，地址 **永远不会** 指代已部署的智能合约（参见下文的 `ContractId` 类型）。`Address` 可能是公钥的哈希值（如果你来自 EVM，则有效地是一个[外部拥有的账户](https://ethereum.org/en/whitepaper/#ethereum-accounts)），也可能是 [断言](../sway-program-types/predicates.md) 的哈希值。地址拥有 UTXOs。

<!-- address:example:end -->

`Address` 的实现如下。

```sway
pub struct Address {
value: b256,
}
```

在 `b256` 和 `Address` 类型之间进行转换必须显式进行：

```sway
let my_number: b256 = 0x000000000000000000000000000000000000000000000000000000000000002A;
let my_address: Address = Address::from(my_number);
let forty_two: b256 = my_address.into();
```

## `ContractId` 类型

<!-- 此部分应解释 `ContractId` 类型 -->
<!-- contract_id:example:start -->

`ContractId` 类型是原始 `b256` 类型的类型安全封装。合约的 ID 是一个唯一的、确定性的标识符，类似于 EVM 中合约的地址。合约不能拥有 UTXOs，但可以拥有资产。

<!-- contract_id:example:end -->

`ContractId` 的实现如下。

```sway
pub struct ContractId {
value: b256,
}
```

在 `b256` 和 `ContractId` 类型之间进行转换必须显式进行：

```sway
let my_number: b256 = 0x000000000000000000000000000000000000000000000000000000000000002A;
let my_contract_id: ContractId = ContractId::from(my_number);
let forty_two: b256 = my_contract_id.into();
```

### 获取合约的 `ContractId`

在内部环境中获取合约的 `ContractId` 使用 `ContractId::this()` 函数：

```sway
impl MyContract for Contract {
fn foo() {
let this_contract_id: ContractId = ContractId::this();
}
}
```

## `Identity` 类型

<!-- 此部分应解释 `Identity` 类型 -->
<!-- identity:example:start -->

`Identity` 类型是一个枚举类型，允许处理 `Address` 和 `ContractId` 类型。这在接受两种类型的情况下非常有用，例如从已识别的发送方接收资金，但不关心发送方是地址还是合约。

<!-- identity:example:end -->

`Identity` 的实现如下。

```sway
{{#include ../../../../sway-lib-std/src/identity.sw:docs_identity}}
```

将类型转换为 `Identity` 必须显式进行：

```sway
{{#include ../../../../examples/identity/src/main.sw:cast_to_identity}}
```

`match` 语句可以用于返回 `Address` 或 `ContractId`，并处理它们的执行方式不同的情况。

```sway
{{#include ../../../../examples/identity/src/main.sw:identity_to_contract_id}}
```

```sway
{{#include ../../../../examples/identity/src/main.sw:different_executions}}
```

<!-- 此部分应解释 `Identity` 类型的用例 -->
<!-- use_identity:example:start -->

`Identity` 的一个常见用例是用于访问控制。`Identity` 的独特使用方式允许同时包含 `ContractId` 和 `Address` 的访问控制。

<!-- use_identity:example:end -->

```sway
{{#include ../../../../examples/identity/src/main.sw:access_control_with_identity}}
```
