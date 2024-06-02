# 使用 IDE 进行调试

`forc debug` 插件还使得在 VSCode 中对 Sway 单元测试进行逐行调试成为可能。

## 安装

1. 从 [marketplace](https://marketplace.visualstudio.com/items?itemName=FuelLabs.sway-vscode-plugin) 安装 Sway VSCode 扩展。
2. 确保已安装 `forc-debug` 二进制文件。运行 `which forc-debug` 来检查。
   可以使用 `fuelup component add forc-debug` 进行安装。
3. 创建一个 `.vscode/launch.json` 文件，内容如下：

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "sway",
      "request": "launch",
      "name": "Debug Sway",
      "program": "${file}"
    }
  ]
}
```

## 示例工程

有以下示例合约

```sway
contract;

abi CallerContract {
    fn test_false() -> bool;
}

impl CallerContract for Contract {
    fn test_false() -> bool {
        false
    }
}

abi CalleeContract {
    fn test_true() -> bool;
}

#[test]
fn test_multi_contract_calls() {
    let caller = abi(CallerContract, CONTRACT_ID);
    let callee = abi(CalleeContract, callee::CONTRACT_ID);

    let should_be_false = caller.test_false();
    let should_be_true = callee.test_true();
    assert(!should_be_false);
    assert(should_be_true);
}
```

在 VSCode 中打开的 Sway 文件中，您可以在测试中的行上设置断点，或在测试调用的函数中设置断点，然后单击“Run -> Start Debugging”开始调试单元测试。

这将构建 Sway 项目并以调试模式运行它。当断点被触发时，调试器将停止 VM 执行。

调试面板将在“Variables”选项卡下显示 VM 寄存器，以及执行暂停的当前 VM 操作码。您可以继续执行，或使用“Step Over”功能逐步进行逐条指令执行。
