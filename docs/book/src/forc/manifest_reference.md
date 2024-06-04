# Manifest

`Forc.toml`（_manifest_ 文件）是每个包的必需文件，使用 [TOML] 格式编写。`Forc.toml` 包含以下字段：

- [`[project]`](#the-project-section) — 定义一个 sway 项目。

  - `name` — 项目名称。
  - `authors` — 项目作者。
  - `organization` — 项目所属组织。
  - `license`— 项目许可证。
  - `entry` — 编译器开始解析的入口点。
    - 有关选择大型库入口点的推荐方法，请参见：[Libraries](./../sway-program-types/libraries.md)
  - `implicit-std` - 控制提供的 `std` 版本（与当前 `forc` 版本）是否会被 _隐式_ 添加为依赖项。_除非你知道自己在做什么，否则保持默认。_
  - `forc-version` - 该项目正常运行所需的最低 forc 版本。

- [`[dependencies]`](#the-dependencies-section) — 定义依赖项。
- `[network]` — 定义 forc 与之交互的网络。

  - `url` — 网络的 URL。

- [`[build-profile]`](#the-build-profile-section) - 定义构建配置。

- [`[patch]`](#the-patch-section) - 定义补丁。

- [`[contract-dependencies]`](#the-contract-dependencies-section) - 定义合约依赖项。

## `[project]`

下面显示了一个示例 `Forc.toml`。在 `[project]` 下，以下字段是可选的：

- `authors`
- `organization`

对于以下字段，提供了默认值，因此可以省略它们：

- `entry` - (默认值: `main.sw`)
- `implicit-std` - (默认值: `true`)

```toml
[project]
authors = ["user"]
entry = "main.sw"
organization = "Fuel_Labs"
license = "Apache-2.0"
name = "wallet_contract"
```

## `[dependencies]`

依赖项可以提供以下字段：

- `version` - 依赖项的期望版本
- `path` - 依赖项的路径（如果是本地的）
- `git` - 托管依赖项的 git 仓库的 URL
- `branch` - 从 git 仓库获取的期望分支
- `tag` - 从 git 仓库获取的期望标签
- `rev` - 期望的 rev（即提交哈希）引用

详情请参阅[依赖项](./dependencies.md)

## `[network]`

对于以下字段，提供了默认值，因此可以省略它们：

- `URL` - (默认值: _<http://127.0.0.1:4000>_)

## `[build-profile.*]`

`[build-profile]` 表提供了一种自定义编译器设置的方法，例如调试选项。

构建配置可以提供以下字段：

- `print-ast` - 是否打印生成的 AST，默认为 false。
- `print-dca-graph` - 是否打印计算出的死代码分析 (DCA) 图（以 GraphViz DOT 格式），默认为 false。
- `print-dca-graph-url-format` - 用于生成的 DOT 文件的 URL 格式，例如 VS Code 的格式：`vscode://file/{path}:{line}:{col}`。
- `print-ir` - 是否打印生成的 Sway IR（中间表示），默认为 false。
- `print-asm` - 是否打印生成的 ASM（汇编），默认为 false。
- `terse` - 简洁模式。有限的警告和错误输出，默认为 false。
- `time_phases` - 是否输出编译过程各部分的耗时，默认为 false。
- `include_tests` - 是否在解析、类型检查和代码生成中包括测试函数。默认为 false，但 `forc test` 等调用会设置为 true。
- `json_abi_with_callpaths` - 是否生成包含 `callpaths` 而不是名称的 JSON ABI 以用于结构和枚举，默认为 false。此选项可以通过使用完整路径而不是名称来防止结构或枚举定义冲突。
- `error_on_warnings` - 是否将错误视为警告，默认为 false。

每个 manifest 文件中都有两个默认的 `[build-profile]`。它们是 `debug` 和 `release` 配置。如果要覆盖这些配置，可以在 manifest 文件中显式提供它们，如下例所示：

```toml
[project]
authors = ["user"]
entry = "main.sw"
organization = "Fuel_Labs"
license = "Apache-2.0"
name = "wallet_contract"

[build-profile.debug]
print-asm = { virtual = false, allocated = false, final = true }
print-ir = { initial = false, final = true, modified = false, passes = []}
terse = false

[build-profile.release]
print-asm = { virtual = true, allocated = false, final = true }
print-ir = { initial = true, final = false, modified = true, passes = ["dce", "sroa"]}
terse = true
```

由于 `release` 和 `debug` 在每个 manifest 文件中都隐含包含，因此可以只通过传递 `--release` 或不传递任何参数（`debug` 为默认）来使用它们。对于使用用户定义的构建配置，有一个 `--build-profile <配置名称>` 选项可用于相关命令。（示例见 [forc-build](../forc/commands/forc_build.md)）

注意，提供相应的 CLI 选项（如 `--asm`）将覆盖选定的构建配置。例如，如果同时传递 `--release` 和 `--asm all`，则会覆盖 `release` 构建配置，结果的构建配置结构如下所示：

```toml
print-ast = false
print-ir = { initial = false, final = false, modified = false, passes = []}
print-asm = { virtual = true, allocated = true, final = true }
terse = false
time-phases = false
include-tests = false
json-abi-with-callpaths = false
error-on-warnings = false
experimental-private-modules = false
```

## `[patch]`

`Forc.toml` 的 `[patch]` 部分可以用来用其他副本覆盖依赖项。下面提供的示例使用相同仓库的 `test` 分支补丁 `https://github.com/fuellabs/sway`。

```toml
[project]
authors = ["user"]
entry = "main.sw"
organization = "Fuel_Labs"
license = "Apache-2.0"
name = "wallet_contract"

[dependencies]

[patch.'https://github.com/fuellabs/sway']
std = { git = "https://github.com/fuellabs/sway", branch = "test" }
```

在上面的示例中，`std` 用来自 `std` 仓库 `test` 分支的内容进行补丁。你也可以使用定义了路径的依赖项来补丁 git 依赖项。

```toml
[patch.'https://github.com/fuellabs/sway']
std = { path = "/path/to/local_std_version" }
```

就像 `std` 或 `core` 一样，你还可以用你用 git 仓库声明的依赖项来补丁。

```toml
[project]
authors = ["user"]
entry = "main.sw"
organization = "Fuel_Labs"
license = "Apache-2.0"
name = "wallet_contract"

[dependencies]
foo = { git = "https://github.com/foo/foo", branch = "master" }

[patch.'https://github.com/foo']
foo = { git = "https://github.com/foo/foo", branch = "test" }
```

注意 `[patch]` 之后的每个键都是被补丁的源的 URL。

## `[contract-dependencies]` 部分

`[contract-dependencies]` 表可以用来为一个 Sway 合约或脚本声明合约依赖。合约依赖是指我们的合约或脚本可能会与之交互的一组合约。声明 `[contract-dependencies]` 可以让你在 Sway 源代码中更容易地引用合约，而不必在每次部署新版本时手动更新 ID。相反，我们可以像对常规库依赖一样使用 forc 来固定和更新合约依赖。

在 `[contract-dependencies]` 下声明的合约与常规 `[dependencies]` 一样被构建和固定，但我们不会导入每个合约依赖的整个公共命名空间，而是导入它们各自的合约 ID 作为 `CONTRACT_ID` 常量，通过每个合约依赖的命名空间根可用。这意味着你可以像在下面的示例中演示的那样使用合约依赖的 ID，就好像它是在合约依赖包的根中声明的 `pub const` 一样。

`[contract-dependencies]` 下的条目可以以与 `[dependencies]` 相同的方式声明。也就是说，它们可以引用另一个合约的 `path` 或 `git` 来源。请注意，`[contract-dependencies]` 下的条目必须引用合约，否则会产生错误。

示例 `Forc.toml`：

```toml
[project]
authors = ["user"]
entry = "main.sw"
organization = "Fuel_Labs"
license = "Apache-2.0"
name = "wallet_contract"

[contract-dependencies]
foo = { path = "../foo" }
```

示例用法：

```sway
script;

fn main() {
let foo_id = foo::CONTRACT_ID;
}
```

由于合约的 ID 是确定性计算的，重建相同的合约总会生成相同的合约 ID。由于具有相同合约 ID 的两个合约不能在区块链上部署，因此需要一个“salt”因子来修改合约 ID。对于在 `[contract-dependencies]` 下声明的每个合约依赖，可以指定 `salt`。下面是一个示例：

```toml
[contract-dependencies]
foo = { path = "../foo", salt = "0x1000000000000000000000000000000000000000000000000000000000000000" }
```

对于未指定 `salt` 值的合约依赖，默认会隐式应用全零的 `salt`。
