# Sway 贡献指南

感谢您对 Sway 的贡献兴趣！本文档概述了安装和设置 Sway 工具链进行开发的过程，以及一些贡献到 Sway 的约定。

如果在开始时遇到任何困难，您可以在我们的 [Discourse](https://forum.fuel.network/) 上提问。

## 构建和设置开发工作空间

请参阅 [introduction](../introduction/index.md) 部分中的说明，了解如何安装和设置 Sway 工具链。

## 获取存储库

1. 访问 [Sway](https://github.com/FuelLabs/sway) 存储库并 fork 项目。
2. 然后将 fork 的副本克隆到本地计算机并开始工作。

```sh
git clone https://github.com/FuelLabs/sway
cd sway
```

## 构建和测试

以下步骤将运行 Sway 测试套件并确保一切设置正确。

首先，打开一个新终端并启动 `fuel-core`：

```sh
fuel-core
```

然后打开第二个终端，`cd` 到 `sway` 存储库并运行：

```sh
cargo run --bin test
```

测试套件运行后，您应该会看到：

```console
Tests passed.
_n_ tests run (0 skipped)
```

恭喜！您现在已经设置好一切，并且准备开始做出贡献。

## 找到要处理的问题

有许多方式可以为 Sway 项目做出贡献，其中一些涉及编码知识，而另一些则不涉及。一些示例包括：

- 报告错误
- 为 Sway 书籍添加文档
- 为已经存在开放问题的新功能或错误修复添加新功能
- 发布功能请求

查看我们的 [Help Wanted](https://github.com/FuelLabs/sway/issues?q=is%3Aopen+is%3Aissue+label%3A%22help+wanted%22)、[Sway Book](https://github.com/FuelLabs/sway/issues?q=is%3Aopen+is%3Aissue+label%3A%22The+Sway+Book%22) 或 [Good First Issue](https://github.com/FuelLabs/sway/issues?q=is%3Aopen+is%3Aissue+label%3A%22good+first+issue%22) 问题，找到适合的任务。

如果您计划做一些大的事情，例如与多个组件相关或更改当前行为，请确保在开始实施之前打开问题与我们讨论。

# 贡献流程

以下是贡献者工作流程的大致概述：

- 确保您要贡献的内容已经作为一个问题进行了跟踪。
  - 我们可以在问题中讨论问题和解决方案。
- 从您希望基于的位置创建一个 Git 分支。这通常是主分支。
- 编写代码，添加测试用例，并提交您的工作。
- 运行测试，确保所有测试都通过。
- 如果 PR 包含任何破坏性更改，请在您的 PR 中添加破坏性标签。
- 将您的更改推送到存储库的分支中，并提交拉取请求。
  - 确保在提交消息中提及第 1 步创建的问题。
- 您的 PR 将被审查，并可能会提出一些更改请求。
  - 一旦您进行了更改，您的 PR 必须重新审查和批准。
  - 如果 PR 变得过时，您可以使用 GitHub 的“更新分支”按钮。
  - 如果有冲突，您可以在本地合并和解决冲突。然后将更改推送到您的 PR 分支。
    对分支的任何更改都将需要重新审查。
- 我们的 CI 系统（Github Actions）会自动测试所有已授权的拉取请求。
- 一旦获得批准，使用 Github 合并 PR。

感谢您的贡献！

### 关联问题

拉取请求应链接到同一存储库中的至少一个问题。

如果拉取请求解决了相关问题，并且您希望 GitHub 在合并到默认分支后自动关闭这些问题，您可以使用如下语法（`关键词 #问题编号`）：

```markdown
close #123
```

如果拉取请求链接了一个问题但未关闭它，您可以使用关键词 `ref`，像这样：

```markdown
ref #456
```

多个问题应该对每个问题使用完整的语法，并用逗号分隔，如：

```markdown
close #123, ref #456
```
