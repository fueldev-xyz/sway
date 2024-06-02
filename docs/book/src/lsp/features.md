# 功能特性

## 代码操作

_源代码:_ [code_actions](https://github.com/FuelLabs/sway/tree/master/sway-lsp/src/capabilities/code_actions)

快速生成函数、结构体和 ABI 的样板代码和代码注释。

## 自动补全

_源代码:_ [completion.rs](https://github.com/FuelLabs/sway/blob/master/sway-lsp/src/capabilities/completion.rs)

针对部分编写的语句，提供函数和变量的代码建议。

## 诊断信息

_源代码:_ [diagnostic.rs](https://github.com/FuelLabs/sway/blob/master/sway-lsp/src/capabilities/diagnostic.rs)

在代码中内联显示编译器的警告和错误。

## 语法高亮

_源代码:_ [highlight.rs](https://github.com/FuelLabs/sway/blob/master/sway-lsp/src/capabilities/highlight.rs)

根据类型和上下文对代码进行高亮显示。

## 悬停提示

_源代码:_ [hover](https://github.com/FuelLabs/sway/tree/master/sway-lsp/src/capabilities/hover)

在悬停在函数和变量上时提供文档、编译器诊断和引用链接。

## 内联提示

_源代码:_ [inlay_hints.rs](https://github.com/FuelLabs/sway/blob/master/sway-lsp/src/capabilities/inlay_hints.rs)

在变量名称旁边显示变量的隐含类型。可以在设置中配置。

## 重命名

_源代码:_ [rename.rs](https://github.com/FuelLabs/sway/blob/master/sway-lsp/src/capabilities/rename.rs)

在工作区中的所有位置重命名符号。

## 运行

_源代码:_ [runnable.rs](https://github.com/FuelLabs/sway/blob/master/sway-lsp/src/capabilities/runnable.rs)

在可运行的函数或测试上方显示一个按钮。
