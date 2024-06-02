# 库

<!-- 这部分应该解释什么是库 -->
<!-- library:example:start -->

Sway 中的库是用于定义新的通用行为的文件。

<!-- library:example:end -->

最典型的例子是[Sway 标准库](../introduction/standard_library.md)，它对所有使用 `forc new` 创建的 Forc 项目都是隐式可用的。

## 编写库

<!-- 这部分应该解释如何定义库 -->
<!-- def_lib:example:start -->

使用 `library` 关键字在文件开头定义库，并在后面跟一个名称，以便可以导入它们。

<!-- def_lib:example:end -->

```sway
library;

// library code
```

一个很好的参考库是 [Sway Standard Library](../introduction/standard_library.md)，可以用来学习库设计。例如，标准库提供了一个 [实现](https://github.com/FuelLabs/sway/blob/master/sway-lib-std/src/option.sw) 的 `enum Option<T>`，这是一个泛型类型，表示一个值的存在（使用变体 `Some(..)`）或一个值的缺失（使用变体 `None`）。[实现 `Option<T>` 的 Sway 文件](https://github.com/FuelLabs/sway/blob/master/sway-lib-std/src/option.sw) 具有以下结构：

- `library` 关键字：

```sway
library;
```

- `use` 语句，从标准库内的另一个库中导入 `revert`：

```sway
use ::revert::revert;
```

- `enum` 定义以关键字 `pub` 开始，表示这个 `Option<T>` 可以在 `option` 库外部公开使用：

```sway
pub enum Option<T> {
    // variants
}
```

- `impl` 块为 `Option<T>` 实现了一些方法：

```sway
impl<T> Option<T> {

    fn is_some(self) -> bool {
        // body of is_some
    }

    // other methods
}
```

现在 `option` 库已经完全编写完成，因为 `Option<T>` 是使用 `pub` 关键字定义的，所以我们现在可以在任何 Sway 项目中使用 `use std::option::Option;` 导入 `Option<T>` 并访问其所有变体和方法。 话虽如此，`Option` 在 [标准库预定义](../introduction/standard_library.md#standard-library-prelude) 中是自动可用的，因此您实际上无需手动导入它。

库仅由一个 `Forc.toml` 文件和一个 `src` 目录组成，与通常包含 `tests` 目录和 `Cargo.toml` 文件的合同不同。 以下是库的 `Forc.toml` 示例：

```toml
[project]
authors = ["Fuel Labs <contact@fuel.sh>"]
entry = "lib.sw"
license = "Apache-2.0"
name = "my_library"

[dependencies]
```

这表示作者、入口文件、它可以被导入的名称以及任何依赖项。

对于大型库，建议使用 `lib.sw` 入口点重新导出所有其他子库。

<!-- 此部分应解释 `mod` 关键字 -->
<!-- mod:example:start -->

`mod` 关键字注册一个子模块，使其项目（如函数和结构）可从父库中访问。
如果在顶层使用它，它将引用 `src` 文件夹中的文件，在其他情况下将引用库中命名的文件夹。

<!-- mod:example:end -->

例如，标准库的 `lib.sw` 如下所示：

```sway
library;

mod block;
mod storage;
mod constants;
mod vm;
// .. Other deps
```

包含其他库的 `src` 文件夹，比如 `vm` 库（在 `src/vm.sw` 中）：

```sway
library;

mod evm;
// ...
```

以及它自己的子库 `evm` 位于 `src/vm/evm.sw`：

```sway
library;

// ...
```

### 使用库

有两种类型的 Sway 库，基于它们的位置和如何导入。

#### 内部库

内部库位于项目的 `src` 目录中，与 `main.sw` 文件或适当的文件夹一起，如下所示：

```bash
$ tree
.
├── Cargo.toml
├── Forc.toml
└── src
    ├── internal_lib.sw
    ├── main.sw
    └── internal_lib
        └── nested_lib.sw
```

由于 `internal_lib` 是一个内部库，因此可以按以下方式将其导入到 `main.sw` 中：

- 使用 `mod` 关键字，后跟库名称，使内部库成为依赖项
- 使用 `use` 关键字，使用 `::` 分隔库的名称和要导入的项目

```sway
mod internal_lib; // Assuming the library name in `internal_lib.sw` is `internal_lib`

use internal_lib::mint;

// `mint` from `internal_library` is now available in this file
```

### 外部库

外部库位于主 `src` 目录之外，如下所示：

```bash
$ tree
.
├── my_project
│   ├── Cargo.toml
│   ├── Forc.toml
│   └─── src
│       └── main.sw
│
└── external_lib
    ├── Cargo.toml
    ├── Forc.toml
    └─── src
        └── lib.sw
```

由于 `external_lib` 位于 `my_project` 的 `src` 目录之外，因此需要在 `my_project` 的 `Forc.toml` 文件中将其添加为依赖项。在导入之前，需要在 `dependencies` 部分中添加库的路径，如下所示：

```toml
[dependencies]
external_library = { path = "../external_library" }
```

将库依赖项添加到 `toml` 文件后，可以按以下方式从中导入项目：

- 确保要导入的项已使用 `pub` 关键字声明（如果适用，例如：`pub fn mint() {}`）
- 使用 `use` 关键字从库中选择性地导入项目

```sway
use external_library::mint;

// `mint` from `external_library` is now available in this file
```

通配符导入使用 `*` 是支持的，但通常建议在可能的情况下使用显式导入。

> **注意**: 标准库对于所有 Forc 项目都是隐式可用的，也就是说，您无需在 `Forc.toml` 中手动指定 `std` 作为显式依赖项。

## 参考 Sway 库

[`sway-libs`](https://github.com/FuelLabs/sway-libs/) 存储库是一组外部库，您可以将其导入并在 Fuel 应用程序中使用。这些库旨在成为有价值的 Dapp 开发中常见用例的实现。

一些可以尝试的 Sway 库:

- [二进制 Merkle 证明](https://github.com/FuelLabs/sway-libs/tree/master/libs/src/merkle)
- [有符号整数](https://github.com/FuelLabs/sway-libs/tree/master/libs/src/signed_integers)
- [无符号定点数](https://github.com/FuelLabs/sway-libs/tree/master/libs/src/fixed_point)
- [所有权](https://github.com/FuelLabs/sway-libs/tree/master/libs/src/ownership)

### 示例

您可以导入和使用 Sway 库，例如 [Ownership](https://github.com/FuelLabs/sway-libs/tree/master/libs/src/ownership) 库，就像任何其他外部库一样。

```sway
use ownership::Ownership;
```

一旦导入，您可以在智能合约中使用库的以下基本功能：

- 声明所有者
- 更改所有权
- 放弃所有权
- 确保只有所有者可以调用函数
