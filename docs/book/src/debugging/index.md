# 调试

Forc 提供了用于调试实时交易和 Sway 单元测试的工具。
调试可以通过 CLI 或使用 VSCode IDE 进行。

**单元测试** 指的是使用 `#[test]` 注解的“语言内部”测试函数。在 VSCode IDE 中提供了逐行调试功能。

**实时交易** 指的是将交易发送到运行中的 Fuel 客户端节点以执行您的 Sway 代码的测试。在 `forc debug` CLI 中提供了逐指令调试功能。

- [使用 CLI 进行调试](./debugging_with_cli.md)
- [使用 IDE 进行调试](./debugging_with_ide.md)
