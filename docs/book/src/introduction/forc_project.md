# A Forc Project

要使用 Forc 初始化一个新项目，使用 `forc new`：

```sh
forc new my-fuel-project
```

这是 Forc 初始化的项目：

<!-- This section should show the tree for a new forc project -->
<!-- tree:example:start -->

```console
$ cd my-fuel-project
$ tree .
├── Forc.toml
└── src
    └── main.sw
```

<!-- tree:example:end -->

<!-- This section should explain the `Forc.toml` file -->
<!-- forc_toml:example:start -->

`Forc.toml` 是 _清单文件_（类似于 Cargo 的 `Cargo.toml` 或 Node 的 `package.json`），定义了项目元数据，如项目名称和依赖项。

<!-- forc_toml:example:end -->

有关依赖管理的附加信息，请参阅：[此处](../forc/dependencies.md)。

```toml
[project]
authors = ["User"]
entry = "main.sw"
license = "Apache-2.0"
name = "my-fuel-project"

[dependencies]
```

这是项目中唯一的 Sway 文件和主入口点 `src/main.sw` 的内容：

```sway
contract;

abi MyContract {
    fn test_function() -> bool;
}

impl MyContract for Contract {
    fn test_function() -> bool {
        true
    }
}
```

该项目是一个 _合约_，是四种不同项目类型之一。有关不同项目类型的其他信息，请参阅[此处](../sway-program-types/index.md)。

现在我们使用 `forc build` 编译我们的项目，并传递 `--asm final` 标志来查看生成的汇编代码：

```console
$ forc build --asm final
...
.program:
ji   i4
noop
DATA_SECTION_OFFSET[0..32]
DATA_SECTION_OFFSET[32..64]
lw   $ds $is 1
add  $$ds $$ds $is
lw   $r0 $fp i73              ; load input function selector
lw   $r1 data_0               ; load fn selector for comparison
eq   $r2 $r0 $r1              ; function selector comparison
jnzi $r2 i12                  ; jump to selected function
movi $$tmp i123               ; special code for mismatched selector
rvrt $$tmp                    ; revert if no selectors matched
ret  $one
.data:
data_0 .word 559005003

  Compiled contract "my-fuel-project".
  Bytecode size is 60 bytes.
```
