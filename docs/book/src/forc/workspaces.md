# 工作区

一个 _workspace_ 是一个或多个包的集合，即 _workspace members_，这些包被一起管理。

工作区的关键点是：

- 对单个包可用的常见 `forc` 命令也可用于工作区，例如 `forc build` 或 `forc deploy`。
- 所有包共享一个位于工作区根目录的通用 `Forc.lock` 文件。

工作区清单在 `Forc.toml` 文件中声明，并支持以下字段：

- [`members`](#the-members-field) - 要包含在工作区中的包。
- [`[patch]`](#the-patch-section) - 定义补丁。

可以使用 `forc new --workspace` 或 `forc init --workspace` 创建一个空的工作区。

## The `members` 字段

`members` 字段定义了哪些包是工作区的成员：

```toml
[workspace]
members = ["member1", "path/to/member2"]
```

`members` 字段接受相对于工作区根目录的相对路径条目。
位于工作区目录内但不包含在 `members` 集合中的包将被忽略。

## The `[patch]` 部分

`[patch]` 部分可用于覆盖工作区依赖关系图中的任何依赖项。用法与包级别的 `[patch]` 部分相同，详细信息可见[这里](./manifest_reference.md#the-patch-section)。

如果工作区清单文件包含补丁表，则不允许在工作区成员中声明补丁表。

示例：

```toml
[workspace]
members = ["member1", "path/to/member2"]

[patch.'https://github.com/fuellabs/sway']
std = { git = "https://github.com/fuellabs/sway", branch = "test" }
```

在上面的示例中，工作区中每次出现 `std` 作为依赖项时，都会使用 sway 仓库的 `test` 分支中的 `std` 进行替换。

## 支持工作区的一些 `forc` 命令

- `forc build` - 构建整个工作区。
- `forc deploy` - 构建并部署工作区中的所有可部署成员（即合约），并按正确顺序进行。
- `forc run` - 构建并运行工作区的所有脚本。
- `forc check` - 检查工作区的所有成员。
- `forc update` - 检查并更新在工作区成员之间共享的工作区级 `Forc.lock` 文件。
- `forc clean` - 清理工作区每个成员的所有输出工件。
- `forc fmt` - 格式化工作区的所有成员。
