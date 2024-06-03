# 用 Rust 进行测试

Sway 的一个常见用途是编写作为更大 Rust 应用程序一部分的合约或脚本。为了测试 Sway 代码与 Rust 代码之间的交互，我们可以添加集成测试。

## 添加 Rust 集成测试

要向 Forc 项目添加 Rust 集成测试，我们可以使用 [the `sway-test-rs` cargo generate template](https://github.com/FuelLabs/sway/tree/master/templates/sway-test-rs)。这个模板可以让 Sway 开发者更容易地添加设置 Rust 集成测试时所需的样板代码。

让我们向[我们在介绍中创建的全新项目](../introduction/forc_project.md)添加一个 Rust 集成测试。

### 1. 进入项目

回顾一下，我们的空项目如下所示：

```console
$ cd my-fuel-project
$ tree .
├── Forc.toml
└── src
   └── main.sw
```

### 2. 安装 `cargo generate`

我们将使用 cargo generate 模板添加一个 Rust 集成测试工具。让我们确保安装了 `cargo generate` 命令！

```console
cargo install cargo-generate
```

> _**注意**: 你可以通过访问 [cargo-generate 仓库](https://github.com/cargo-generate/cargo-generate) 了解更多关于 cargo generate 的信息。_

### 3. 生成测试工具

让我们使用以下命令生成默认的测试工具：

```console
cargo generate --init fuellabs/sway templates/sway-test-rs --name my-fuel-project --force
```

`--force` 强制你的 `--name` 输入保持你所需的大小写，以便在模板中替换 `{{project-name}}` 占位符。否则，`cargo-generate` 会自动将其转换为 `kebab-case`。使用 `--force` 意味着 `my_fuel_project` 和 `my-fuel-project` 都是有效的项目名称，具体取决于你的需求。

> \_**注意**: `templates/sway-test-rs` 可以替换为 `templates/sway-script-test-rs` 或 `templates/sway-predicate-test-rs` 以分别为脚本和断言生成测试工具。

如果一切顺利，输出应如下所示：

```console
⚠️   Favorite `fuellabs/sway`not found in config, using it as a git repository: https://github.com/fuellabs/sway
🤷   Project Name : my-fuel-project
🔧   Destination: /home/user/path/to/my-fuel-project ...
🔧   Generating template ...
[1/3]   Done: Cargo.toml
[2/3]   Done: tests/harness.rs
[3/3]   Done: tests
🔧   Moving generated files into:`/home/user/path/to/my-fuel-project`...
✨ Done! New project created /home/user/path/to/my-fuel-project
```

让我们看看结果：

```console
$ tree .
├── Cargo.toml
├── Forc.toml
├── src
│   └── main.sw
└── tests
└── harness.rs
```

我们有两个新文件！

- `Cargo.toml` 是我们新测试工具的清单文件，并指定了所需的依赖项，包括 `fuels`（Fuel Rust SDK）。
- `tests/harness.rs` 包含一些让我们入门的样板测试代码，不过还没有调用任何合约方法。

### 4. 构建 forc 项目

在运行测试之前，我们需要构建我们的合约，以便生成必要的 ABI、存储和字节码工件。我们可以使用 `forc build` 来完成：

```console
$ forc build
  Creating a new `Forc.lock` file. (Cause: lock file did not exist)
Adding core
Adding std git+https://github.com/fuellabs/sway?tag=v0.24.5#e695606d8884a18664f6231681333a784e623bc9
Created new lock file at /home/user/path/to/my-fuel-project/Forc.lock
Compiled library "core".
Compiled library "std".
Compiled contract "my-fuel-project".
Bytecode size is 60 bytes.
```

此时，我们的项目应如下所示：

```console
$ tree
├── Cargo.toml
├── Forc.lock
├── Forc.toml
├── out
│   └── debug
│   ├── my-fuel-project-abi.json
│   ├── my-fuel-project.bin
│   └── my-fuel-project-storage_slots.json
├── src
│   └── main.sw
└── tests
└── harness.rs
```

我们现在有一个包含所需 JSON 文件的 `out` 目录！

> _**注意**: 未来这一步可能不再需要，因为我们计划使集成测试自动构建所需的工件，以便像 ABI JSON 这样的文件始终是最新的。_

### 5. 构建并运行测试

现在我们准备构建并运行默认的集成测试。

```console
$ cargo test
Updating crates.io index
Compiling version_check v0.9.4
Compiling proc-macro2 v1.0.46
Compiling quote v1.0.21
...
Compiling fuels v0.24.0
Compiling my-fuel-project v0.1.0 (/home/user/path/to/my-fuel-project)
Finished test [unoptimized + debuginfo] target(s) in 1m 03s
Running tests/harness.rs (target/debug/deps/integration_tests-373971ac377845f7)

running 1 test
test can_get_contract_id ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.36s
```

> _**注意**: 我们第一次运行 `cargo test` 时，cargo 会花一些时间获取并构建 Fuel 的 Rust SDK 的依赖项。这可能需要一段时间，但仅限于第一次！_

如果一切顺利，我们应该会看到类似上面的输出！

## 编写测试

现在我们已经了解了如何在项目中设置 Rust 集成测试，让我们尝试编写一些自己的测试！

首先，让我们用一个简单的计数器示例更新我们的合约代码：

```sway
{{#include ../../../../examples/counter/src/main.sw}}
```

为了从 Rust 测试工具测试我们的 `initialize_counter` 和 `increment_counter` 合约方法，我们可以更新 `tests/harness.rs` 文件如下：

<!--TODO add test here once examples are tested-->

```rust,ignore
use fuels::{prelude::*, types::ContractId};

// Load ABI from JSON
abigen!(TestContract, "out/debug/my-fuel-project-abi.json");

async fn get_contract_instance() -> (TestContract, ContractId) {
    // Launch a local network and deploy the contract
    let mut wallets = launch_custom_provider_and_get_wallets(
        WalletsConfig::new(
            Some(1),             /* Single wallet */
            Some(1),             /* Single coin (UTXO) */
            Some(1_000_000_000), /* Amount per coin */
        ),
        None,
    )
    .await;
    let wallet = wallets.pop().unwrap();

    let id = Contract::load_from(
        "./out/debug/my-fuel-project.bin",
        LoadConfiguration::default().set_storage_configuration(
            StorageConfiguration::load_from(
                "./out/debug/my-fuel-project-storage_slots.json",
            )
            .unwrap(),
        ),
    )
    .unwrap()
    .deploy(&wallet, TxParameters::default())
    .await
    .unwrap();

    let instance = TestContract::new(id.to_string(), wallet);

    (instance, id.into())
}

#[tokio::test]
async fn initialize_and_increment() {
    let (contract_instance, _id) = get_contract_instance().await;
    // Now you have an instance of your contract you can use to test each function

    let result = contract_instance
        .methods()
        .initialize_counter(42)
        .call()
        .await
        .unwrap();

    assert_eq!(42, result.value);

    // Call `increment_counter()` method in our deployed contract.
    let result = contract_instance
        .methods()
        .increment_counter(10)
        .call()
        .await
        .unwrap();

    assert_eq!(52, result.value);
}
```

让我们再次构建项目并运行测试：

```console
forc build
```

```console
$ cargo test
Compiling my-fuel-project v0.1.0 (/home/mindtree/programming/sway/my-fuel-project)
Finished test [unoptimized + debuginfo] target(s) in 11.61s
Running tests/harness.rs (target/debug/deps/integration_tests-373971ac377845f7)

running 1 test
test initialize_and_increment ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 1.25s
```

当 cargo 运行我们的测试时，我们的测试使用 SDK 启动一个本地内存中的 Fuel 网络，将我们的合约部署到其中，并通过 ABI 调用合约方法。

您可以添加任意数量的带有 `#[tokio::test]` 装饰的函数，`cargo test` 会自动测试每一个函数！
