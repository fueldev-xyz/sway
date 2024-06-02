# 什么是智能合约？

智能合约与脚本或断言没有区别，它是一段字节码，通过[交易](https://fuellabs.github.io/fuel-specs/master/protocol/tx_format)部署到区块链上。智能合约与脚本或断言的主要特点是它可以被调用且具有状态。换句话说，智能合约类似于部署了一些数据库状态的 API。

智能合约的接口，也称为合约，必须严格地使用 [ABI 声明](#the-abi-declaration) 定义。查看[此合约](../examples/wallet_smart_contract.md)以获取示例。

## 智能合约的语法

与任何 Sway 程序一样，程序始于声明其[程序类型](./index.md)。合约必须定义或导入一个[ABI 声明](#the-abi-declaration)并实现它。

**ABI 最佳实践**

在单独的库中定义 ABI 并将其导入到合约中被认为是一个良好的做法。这样，调用方只需直接导入 ABI，并在其脚本中使用它来调用您的合约。

让我们来看一个库中的 ABI 声明：

\```sway
{{\#include ../../../../examples/wallet_abi/src/main.sw:abi_library}}
\```

让我们专注于 ABI 声明并逐行检查它。

### The ABI Declaration

\```sway
{{\#include ../../../../examples/wallet_abi/src/main.sw:abi}}
\```

---

在第一行中，\`abi Wallet {\`，我们声明这个*应用程序二进制接口*（ABI）的名称。我们将这个 ABI 命名为 `Wallet`。要将此 ABI 导入到脚本以进行调用或导入到合约以进行实现，您将使用

\```sway
{{\#include ../../../../examples/wallet_smart_contract/src/main.sw:abi_import}}
\```

---

在第二行中，

\```sway
{{\#include ../../../../examples/wallet_abi/src/main.sw:receive_funds}}
\```

我们声明了一个名为`receive_funds`的 ABI 方法，当调用时，应该将资金存入这个钱包。请注意，我们在这里只是定义了一个接口，所以没有函数体或函数的实现。我们只需要定义接口本身。这种方式，ABI 声明类似于[特征声明](../advanced/traits.md)。这个特定的 ABI 方法不接受任何参数。

---

在第三行中，

\```sway
{{#include ../../../../examples/wallet_abi/src/main.sw:send_funds}}
\```

我们声明了另一个 ABI 方法，这次称为`send_funds`。它接受两个参数：要发送的金额和要发送资金的地址。

> **注意**: ABI 方法`receive_funds`和`send_funds`还需要注释`#[storage(read, write)]`，因为它们的实现需要读取和写入跟踪钱包余额的存储变量。有关存储注释的更多信息，请参见[Purity](../blockchain-development/purity.md#Purity)。

## 为智能合约实现 ABI

既然我们讨论了如何定义接口，让我们讨论如何使用它。我们将从为特定合约实现上述 ABI 开始。

通过`impl <ABI名称> for Contract`语法实现合约的 ABI。`for Contract`语法只能用于实现合约的 ABI；为结构体实现方法应该使用`impl Foo`语法。

\```sway
{{#include ../../../../examples/wallet_smart_contract/src/main.sw:abi_impl}}
\```

您可能再次注意到[特征](../advanced/traits.md)和 ABI 之间的相似之处。而且，作为额外的奖励，您可以定义除了 ABI 接口表面之外的方法，就像特征一样。这些预实现的 ABI 方法自动成为实现相应 ABI 的合约接口的一部分。

请注意，上述 ABI 的实现遵循[Checks, Effects, Interactions](https://docs.soliditylang.org/en/v0.6.11/security-considerations.html#re-entrancy)模式。

## `ContractId`类型

合约在 Sway 中有一个关联的`ContractId`类型。`ContractId`类型允许 Sway 程序在 Sway 语言中引用合约。有关`ContractId`的更多信息，请参见书中的[ContractId](../basics/blockchain_types.md#contractid-type)部分。

## 从脚本调用智能合约

> **注意**: 在大多数情况下，调用合约应该使用[Rust SDK](../testing/testing-with-rust.md)或[TypeScript SDK](https://docs.fuel.network/docs/fuels-ts)，因为它们为与合约交互提供了更人性化的界面。然而，有些情况下需要手动编写脚本来调用合约。

既然我们已经为合约定义了接口并实现了它，我们需要知道如何实际*调用*我们的合约。让我们看一下合约调用：

\```sway
{{#include ../../../../examples/wallet_contract_caller_script/src/main.sw}}
\```

主要的新概念是`abi cast`: `abi(AbiName, contract_address)`。这返回一个`ContractCaller`类型，可用于调用合约。ABI 的方法成为此合约调用者可用的方法：`send_funds`和`receive_funds`。然后我们直接调用合约 ABI 方法，就像它是常规方法一样。您还可以在主参数列表之前在花括号中指定以下特殊参数：

1. `gas`: 一个`u64`，表示调用合约时转发的 gas。
2. `coins`: 一个`u64`，表示此调用中转发的硬币数量。
3. `asset_id`: 一个`b256`，表示转发的硬币的*资产类型*的 ID。

每个特殊参数都是可选的，如果省略，则假定默认值：

1. `gas`的默认值是上下文 gas（即特殊寄存器 `$cgas` 中的内容）。有关上下文 gas 的更多信息，请参阅[FuelVM 规范](https://fuellabs.github.io/fuel-specs/master/vm)。
2. `coins`的默认值为 0。
3. `asset_id`的默认值是`b256::zero()`。
