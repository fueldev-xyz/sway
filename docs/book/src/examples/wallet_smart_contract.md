# 钱包智能合约

ABI 声明是与 ABI 实现分开的一个项目。代码的项目结构应该组织如下，其中 `wallet_abi` 被视为外部库：

```sh
.
├── wallet_abi
│ ├── Forc.toml
│ └── src
│ └── main.sw
└── wallet_smart_contract
├── Forc.toml
└── src
└── main.sw
```

在使用外部库时，指定依赖项的来源也很重要，应该在项目的 `Forc.toml` 文件中进行声明。在 `wallet_smart_contract` 项目内，需要声明如下：

```sh
[dependencies]
wallet_abi = { path = "../wallet_abi/" }
```

## ABI 声明

```sway
{{#include ../../../../examples/wallet_abi/src/main.sw:abi_library}}
```

## ABI 实现

```sway
{{#include ../../../../examples/wallet_smart_contract/src/main.sw:full_wallet}}
```
