# 安装

Sway 语言服务器包含在 [`forc-lsp`](../forc/plugins/forc_lsp.md) 二进制文件中，作为 [Fuel 工具链](../introduction/fuel_toolchain.md) 的一部分安装。安装完成后，它可以与多种 IDEs 配合使用。必须安装它才能让任何 IDE 插件正常工作。

> **注意**：无需手动运行 `forc-lsp`（插件将自动启动它），但是 `forc` 和 `forc-lsp` 必须在你的 `$PATH` 中。要检查 `forc` 是否在你的 `$PATH` 中，请在终端中键入 `forc --help`。

## VSCode

目前，这是最受支持的编辑器。

你可以从 [市场](https://marketplace.visualstudio.com/items?itemName=FuelLabs.sway-vscode-plugin) 安装最新版本的插件。

请注意，我们仅支持最新版本的 VS Code。

## Code OSS (Linux 上的 VSCode)

1. 安装 [code-marketplace](https://aur.archlinux.org/packages/code-marketplace) 以获取 VSCode 市场中的所有扩展。
2. 安装 [Sway](https://marketplace.visualstudio.com/items?itemName=FuelLabs.sway-vscode-plugin) 扩展。

## vim / neovim

按照 [sway.vim](https://github.com/FuelLabs/sway.vim) 的文档进行安装。

## helix

[安装 helix](https://docs.helix-editor.com/install.html)，Sway LSP 将立即就能正常工作。

Sway 支持内置在 helix 中，使用 [tree-sitter-sway](https://github.com/FuelLabs/tree-sitter-sway)。

## Emacs

即将推出！欢迎 [贡献](https://github.com/FuelLabs/sway/issues/3527)。
