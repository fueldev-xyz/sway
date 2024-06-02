# 调用合约

智能合约可以被其他合约或脚本所调用。在 FuelVM 中，这主要通过 [`call`](https://fuellabs.github.io/fuel-specs/master/vm/instruction_set#call-call-contract) 指令来实现。

Sway 提供了一种很好的方式来管理可调用的接口，即其 ABI 系统。Fuel ABI 规范可以在[这里](https://fuellabs.github.io/fuel-specs/master/protocol/abi)找到。

## 示例

这里是一个在 Sway 中调用另一个合约的示例。脚本可以以相同的方式调用合约。

```sway
// ./contract_a.sw
contract;

abi ContractA {
    fn receive(field_1: bool, field_2: u64) -> u64;
}

impl ContractA for Contract {
    fn receive(field_1: bool, field_2: u64) -> u64 {
        assert(field_1 == true);
        assert(field_2 > 0);
        return_45()
    }
}

fn return_45() -> u64 {
  45
}
```

```sway
// ./contract_b.sw
contract;

use contract_a::ContractA;

abi ContractB {
    fn make_call();
}

const contract_id = 0x79fa8779bed2f36c3581d01c79df8da45eee09fac1fd76a5a656e16326317ef0;

impl ContractB for Contract {
    fn make_call() {
      let x = abi(ContractA, contract_id);
      let return_value = x.receive(true, 3); // will be 45
    }
}
```

> **注意**：ABI 仅适用于外部调用，因此您不能在 ABI 中定义方法并在同一合约中调用它。如果您想为合约定义一个函数，但希望它是私有的，只有您的合约才能调用它，您可以在 `impl` 外部定义它，并在合约内部调用它，类似于上面的 `return_45()` 函数。

## 高级调用

所有调用都会转发一个 gas 补贴，并可能额外转发一个原生资产与调用一起。

以下是如何指定要转发的 gas 量 (`gas`)、原生资产的资产 ID (`asset_id`) 和原生资产的数量 (`coins`) 的示例：

```sway
script;

abi MyContract {
    fn foo(field_1: bool, field_2: u64);
}

fn main() {
    let x = abi(MyContract, 0x79fa8779bed2f36c3581d01c79df8da45eee09fac1fd76a5a656e16326317ef0);
    let asset_id = 0x7777_7777_7777_7777_7777_7777_7777_7777_7777_7777_7777_7777_7777_7777_7777_7777;
    x.foo {
        gas: 5000, asset_id: asset_id, coins: 5000
    }
    (true, 3);
}
```

## 处理重入

智能合约的一个常见攻击向量是 [重入](https://docs.soliditylang.org/en/v0.8.4/security-considerations.html#re-entrancy)。与 EVM 类似，FuelVM 也允许重入。

一个 _无状态_ 的重入性防护已经包含在 [`sway-libs`](https://fuellabs.github.io/sway-libs/book/reentrancy/index.html) 库中。如果检测到重入，该防护将在运行时触发 panic (revert)。

```sway
contract;

use reentrancy::reentrancy_guard;

abi MyContract {
    fn some_method();
}

impl ContractB for Contract {
    fn some_method() {
        reentrancy_guard();
        // 做一些事情
    }
}
```

### CEI 模式违规静态分析

避免重入攻击的另一种方法是遵循所谓的 _CEI_ 模式。CEI 代表 "Checks, Effects, Interactions"，意思是合约代码应首先执行安全检查，也称为 "前提条件"，然后执行效果，即修改或读取合约存储，并仅在函数/方法的最后执行外部合约调用（交互）。

请参阅此 [博文](https://fravoll.github.io/solidity-patterns/checks_effects_interactions.html) 以了解有关在交互后修改存储时的一些漏洞的更多细节，以及此 [博文](https://chainsecurity.com/curve-lp-oracle-manipulation-post-mortem) 以获取有关交互后存储读取的更多信息。

Sway 编译器实现了对用户合约违反 CEI 模式的检查，并在这种情况下发出警告。

例如，在以下合约中，违反了 CEI 模式，因为在存储写入之前执行了外部合约调用。

```sway
{{#include ../../../../examples/cei_analysis/src/main.sw}}
```

这里，`other_contract` 的定义如下：

```sway
{{#include ../../../../examples/cei_analysis/src/other_contract.sw}}
```

CEI 模式分析器发出如下警告，指向存储修改之前的交互：

```sh
warning
  --> /path/to/contract/main.sw:28:9
   |
26 |
27 |           let caller = abi(OtherContract, external_contract_id.into());
28 |           caller.external_call { coins: bal }();
   |  _________-
29 | |
30 | |         // Storage update _after_ external call
31 | |         storage.balances.insert(sender, 0);
   | |__________________________________________- Storage write after external contract interaction in function or method "withdraw". Consider making all storage writes before calling another contract
32 |       }
33 |   }
   |
____
```

在交互之后如果存在对存储的读取，CEI 分析器将发出类似的警告。

除了交互之后的存储读取和写入之外，CEI 分析器还报告了以下类似的警告：

- 余额树更新，即余额树读取后随后的写入，这可能是由 `tr` 和 `tro` ASM 指令或使用它们的库函数在内部产生的；
- 使用 `bal` 指令读取余额树；
- 由 `__smo` 内部函数或 `smo` ASM 指令产生的输出消息的更改。

## 与 EVM 的不同之处

虽然 Fuel 合约调用范式与 EVM 的类似（使用 ABI、转发 gas 和数据），但有两个关键区别：

1. [**本机资产**](./native_assets.md)：FuelVM 调用可以转发任何本机资产，而不仅仅是基本资产。

2. **无数据序列化**：FuelVM 中的合约调用不需要对数据进行序列化以在合约之间传递数据；相反，它们只是传递数据的指针。这是因为 FuelVM 具有所有调用帧都可以从中读取的共享全局内存。

## 回退函数

当编译合约时，还会生成一个名为“合约选择”的特殊部分。该部分检查合约调用方法是否与任何可用 ABI 方法匹配。如果失败，将发生以下两种可能的情况之一：

1 - 如果未指定回退函数，则合约将回滚；
2 - 否则，将调用回退函数。

就所有目的而言，回退函数被视为合约方法，这意味着它具有其他合约方法的所有限制。作为回退函数签名，该函数不能有参数，但它们可以返回任何内容。

如果出于某种原因回退函数需要返回不同类型，可以使用内置的 `__contract_ret`。

```sway
contract;

abi MyContract {
    fn some_method();
}

impl ContractB for Contract {
    fn some_method() {
    }
}

#[fallback]
fn fallback() {
}
```

You may still access the method selector and arguments to the call in the fallback.
For instance, let's assume a function `fn foobar(bool, u64) {}` gets called on a contract that doesn't have it with arguments `true` and `42`.
It can execute the following fallback:

```sway
#[fallback]
fn fallback() {
    // the method selector is the first four bytes of sha256("foobar(bool,u64)")
    // per https://fuellabs.github.io/fuel-specs/master/protocol/abi#function-selector-encoding
    let method_selector = std::call_frames::first_param::<u64>();

    // the arguments tuple is (true, 42)
    let arguments = std::call_frames::second_param::<(bool, u64)>();
}
```
