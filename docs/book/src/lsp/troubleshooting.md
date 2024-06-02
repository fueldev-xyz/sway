# 故障排除

首先，确认你正在运行最新版本：

```sh
fuelup toolchain install latest
fuelup update
forc-lsp --version
```

其次，确认你的 `$PATH` 解析到 `$HOME/.fuelup/bin` 中的 `forc-lsp` 二进制文件。

```sh
which forc-lsp
```

## 性能慢

如果你遇到性能慢的问题，可以尝试以下操作：

按照[上面的步骤](#troubleshooting)确保你正在运行最新版本。

然后，确保你只运行了最新版本的 LSP 服务器。

```sh
pkill forc-lsp
```

### 大型项目

具有十个或更多 Sway 文件的项目可能会导致 LSP 性能较慢。我们正在努力改进对大型项目的支持。

在此期间，如果速度太慢，你可以通过设置 `sway-lsp.diagnostic.disableLsp` 来完全禁用 LSP 服务器。插件仍将提供基本的语法高亮、命令面板以及 Sway 调试器，但所有其他语言功能将被禁用。

## 服务器日志

你可以启用 LSP 服务器的详细日志记录。

在 VSCode 中，这是在设置中的：

```json
"sway-lsp.trace.server": "verbose"
```

启用后，你可以在输出窗口中找到 Sway Language Server 下的日志。

对于其他编辑器，请参阅[安装](./installation.md)以获取文档链接。
