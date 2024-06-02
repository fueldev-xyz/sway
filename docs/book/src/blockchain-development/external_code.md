# 外部代码执行

`std-lib` 包含一个名为 `run_external` 的函数，允许 Sway 合约执行任意外部 Sway 代码。

此功能启用了可升级合约和代理等功能。

## 可升级合约

可升级合约旨在允许智能合约的逻辑在部署后进行更新。

考虑以下代理合约示例：

```sway
{{#include ../../../../examples/upgradeable_proxy/proxy/src/main.sw:proxy}}
```

该合约有两个函数：

- `set_target_contract` 使用外部合约的 `ContractId` 更新存储中的 `target_contract` 变量。
- `double_input` 从存储中读取 `target_contract` 并使用它运行外部代码。如果 `target_contract` 中有一个同名的函数（`double_input`），则将运行外部 `double_input` 函数中的代码。在这种情况下，该函数将返回一个 `u64`。

注意在上面的 `Proxy` 示例中，存储块具有 `namespace` 属性。使用此属性被认为是所有 Sway 代理合约的最佳实践，因为它可以防止与实现合约的存储冲突，因为实现合约可以访问两个存储上下文。

下面是一个实现合约可能的示例：

```sway
{{#include ../../../../examples/upgradeable_proxy/implementation/src/main.sw:target}}
```

该合约有一个名为 `double_input` 的函数，该函数计算输入值的两倍，更新存储中的 `value` 变量，并返回新值。

## 这与直接调用合约有何不同？

直接调用合约和使用 `run_external` 方法之间有几个主要区别。

首先，使用 `run_external` 时，不需要外部合约的 ABI。代理合约除了其 `ContractId` 外，对外部合约一无所知。

### 可升级合约存储

其次，代理合约的存储上下文保留给加载的代码。这意味着在上面的示例中，`value` 变量在 _代理_ 合约的存储中被更新。

例如，如果直接调用实现合约读取 `value` 变量，您将获得与通过代理合约读取不同的结果。代理合约加载代码并在其自己的上下文中执行。

## Fallback 函数

如果目标合约中不存在该函数名称，但存在 `fallback` 函数，则将触发 `fallback` 函数。

> 如果没有 `fallback` 函数，交易将回滚。

您可以使用 `std-lib` 中的 `call_frames` 模块访问 `fallback` 函数的参数。例如，要在下面的代理函数中访问 `_foo` 输入参数，可以在 `fallback` 函数中使用 `called_args` 方法：

```sway
{{#include ../../../../test/src/sdk-harness/test_projects/run_external_proxy/src/main.sw:does_not_exist_in_the_target}}
```

```sway
{{#include ../../../../test/src/sdk-harness/test_projects/run_external_target/src/main.sw:fallback}}
```

在这种情况下，`does_not_exist_in_the_target` 函数将返回 `_foo * 3`。

## 限制

`run_external` 函数的一些限制是：

- 它只能用于其他合约。脚本、谓词和库代码不能外部运行。
- 如果更改实现合约，必须保持先前存储变量和类型的顺序，因为这是在代理存储中存储的内容。
- 在使用 `run_external` 之前，不能在另一个调用帧中使用调用栈。只能在包含 `run_external` 的调用帧中使用调用栈。
