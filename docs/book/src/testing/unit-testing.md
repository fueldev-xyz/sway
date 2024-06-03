# 单元测试

<!-- 本节应解释 Sway 中的单元测试 -->
<!-- unit_test:example:start -->

Forc 提供了内置支持，用于构建和执行包的测试。

测试被写成带有 `#[test]` 属性的自由函数。

<!-- unit_test:example:end -->

例如：

\```sway #[test]
fn test_meaning_of_life() {
assert(6 \* 7 == 42);
}
\```

每个测试函数都像是[脚本](../sway-program-types/scripts.md)的入口点一样运行。测试在成功返回时“通过”，在恢复或[测试失败](#testing-failure)时“失败”。

如果项目中有未通过的测试，`forc test` 将以退出状态 `101` 退出。

## 构建和运行测试

我们可以使用以下命令构建并执行包内的所有测试：

\```console
forc test
\```

输出应类似于以下内容：

\```console
Compiled library "core".
Compiled library "std".
Compiled library "lib_single_test".
Bytecode size is 92 bytes.
Running 1 tests
test test_meaning_of_life ... ok (170.652µs)
Result: OK. 1 passed. 0 failed. Finished in 1.564996ms.
\```

访问 [`forc test`](../forc/commands/forc_test.md) 命令参考，以了解 `forc test` 的可用选项。

## 测试失败

<!-- 本节应解释 Sway 中对失败单元测试的支持 -->
<!-- unit_test_fail:example:start -->

Forc 支持为用 `#[test(should_revert)]` 声明的测试函数测试失败的情况。

<!-- unit_test_fail:example:end -->

例如：

\```sway #[test(should_revert)]
fn test_meaning_of_life() {
assert(6 \* 6 == 42);
}
\```

还可以指定预期的恢复代码，如下例所示。

\```sway #[test(should_revert = "18446744073709486084")]
fn test_meaning_of_life() {
assert(6 \* 6 == 42);
}
\```

带有 `#[test(should_revert)]` 的测试在恢复时被视为通过。

## 调用合约

单元测试可以调用合约函数，下面是此类调用的示例。

\```sway
contract;

abi MyContract {
fn test_function() -> bool;
}

impl MyContract for Contract {
fn test_function() -> bool {
true
}
}
\```

要测试 `test_function()`，可以编写如下的单元测试。

```sway
#[test]
fn test_success() {
    let caller = abi(MyContract, CONTRACT_ID);
    let result = caller.test_function {}();
    assert(result == true)
}
```

它还可以通过合约调用测试失败的情况。

\```sway #[test(should_revert)]
fn test_fail() {
let caller = abi(MyContract, CONTRACT_ID);
let result = caller.test_function {}();
assert(result == false)
}
\```

<!-- 本节应解释 Sway 单元测试中 `CONTRACT_ID` 变量的工作方式 -->
<!-- contract_id:example:start -->

> **注意：** 运行 `forc test` 时，你的合约将被构建两次：第一次是在没有单元测试的情况下构建，以确定合约的 ID，然后在带有单元测试的情况下再次构建，并将 `CONTRACT_ID` 提供给它们的命名空间。这个 `CONTRACT_ID` 可以与 `abi` 强制转换一起使用，以在单元测试中启用合约调用。

<!-- contract_id:example:end -->

如果那些合约作为合约依赖项添加到项目中，即在清单文件的 [`contract-dependencies`](../forc/manifest_reference.md#the-contract-dependencies-section) 部分中，则单元测试可以调用外部合约的方法。此类调用的示例如下：

\```sway
{{#include ../../../../examples/multi_contract_calls/caller/src/main.sw:multi_contract_calls}}
\```

上述合约的示例 `Forc.toml`：

\```toml
{{#include ../../../../examples/multi_contract_calls/caller/Forc.toml:multi_contract_call_toml}}
\```

## 并行或串行运行测试

<!-- 本节应解释单元测试不共享存储 -->
<!-- storage:example:start -->

默认情况下，项目中的所有单元测试都并行运行。请注意，这不会导致存储中的数据争用，因为每个单元测试都有其自己的存储空间，不与任何其他单元测试共享。

<!-- storage:example:end -->

默认情况下，`forc test` 将使用系统中的所有可用线程。要请求使用特定数量的线程，可以将 `--test-threads <val>` 标志提供给 `forc test`。

\```console
forc test --test-threads 1
\```

## 测试中的日志

<!-- 本节应解释 Sway 单元测试中日志解码的工作方式 -->
<!-- unit_test_log::example::start -->

Forc 具有一些功能，可以帮助解码单元测试返回的日志。你可以使用此功能将原始日志解码为可读格式。

\```sway
script;

fn main() {}

#[test]
fn test_fn() {
let a = 10;
log(a);
let b = 30;
log(b);
assert_eq(a, 10)
assert_eq(b, 30)
}
\```

上面的示例记录了两个不同的变量，`a` 和 `b`，它们的值分别为 `10` 和 `30`。在没有日志解码的情况下，使用 `forc test --logs` 运行此测试（由于测试通过，此示例需要使用 `--logs` 标志才能查看日志。默认情况下，通过的测试中的日志是静音的，可以使用 `--logs` 标志启用。）时打印的日志为：

\```console
Finished debug [unoptimized + fuel] target(s) in 5.23s
Bytecode hash: 0x1cb1edc031691c5c08b50fd0f07b02431848ab81b325b72eb3fd233c67d6b548
Running 1 test, filtered 0 tests
test test_fn ... ok (38.875µs, 232 gas)
[{"LogData":{"data":"000000000000000a","digest":"8d85f8467240628a94819b26bee26e3a9b2804334c63482deacec8d64ab4e1e7","id":"0000000000000000000000000000000000000000000000000000000000000000","is":10368,"len":8,"pc":11032,"ptr":67107840,"ra":0,"rb":0}},{"LogData":{"data":"000000000000001e","digest":"48a97e421546f8d4cae1cf88c51a459a8c10a88442eed63643dd263cef880c1c","id":"0000000000000000000000000000000000000000000000000000000000000000","is":10368,"len":8,"pc":11516,"ptr":67106816,"ra":0,"rb":1}}]
\```

这不太容易理解，可以使用 `--decode` 标志解码这些日志，执行 `forc test --logs --decode`：

\```console
Finished debug [unoptimized + fuel] target(s) in 5.23s
Bytecode hash: 0x1cb1edc031691c5c08b50fd0f07b02431848ab81b325b72eb3fd233c67d6b548
Running 1 test, filtered 0 tests
test test_fn ... ok (38.875µs, 232 gas)
Decoded log value: 10, log rb: 0
Decoded log value: 30, log rb: 1
\```

可以看出，值是可读的，更容易理解，这使得调试更容易。

**注意**：这是一个实验性功能，我们正在积极努力报告变量名称及其值。

<!-- unit_test_log::example::end -->
