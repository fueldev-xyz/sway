# Fuel 工具链

Fuel 工具链由几个组件组成。

## Forc (`forc`)

"Fuel 编排器" [Forc](https://github.com/FuelLabs/sway/tree/master/forc) 是我们等价于 Rust 的 [Cargo](https://doc.rust-lang.org/cargo/)。它是创建、构建、测试和部署 Sway 项目的主要入口点。

## Sway 语言服务器 (`forc-lsp`)

Sway 语言服务器 `forc-lsp` 用于向集成开发环境暴露功能。[安装说明](../lsp/installation.md)。

## Sway 格式化器 (`forc-fmt`)

`forc-fmt` 提供了一个标准的格式化器。[安装说明](./getting_started.md)。它可以通过以下方式手动运行：

```sh
forc fmt
```

[Visual Studio Code 插件](https://marketplace.visualstudio.com/items?itemName=FuelLabs.sway-vscode-plugin)在保存时会自动使用 forc-fmt 格式化 Sway 文件，但你可能需要明确将 Sway 插件设置为默认格式化程序，像这样：

```json
"[sway]": {
  "editor.defaultFormatter": "FuelLabs.sway-vscode-plugin"
}
```

## Fuel 核心 (`fuel-core`)

提供了 Fuel 协议的一种实现，[Fuel Core](https://github.com/FuelLabs/fuel-core)，它与 _Sway 工具链_ 一起组成了 _Fuel 工具链_。[Rust SDK](https://github.com/FuelLabs/fuels-rs) 在测试期间将自动启动和停止节点实例，因此除非直接使用 Forc 而不使用 SDK，否则无需手动运行节点。
