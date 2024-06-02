# 原生资产

FuelVM 内置支持处理多种资产。

## EVM 和 FuelVM 资产之间的主要区别

### ERC-20 vs 原生资产

在 EVM 上，以太币是原生资产。因此，向地址或合约发送 ETH 是内置于 EVM 中的操作，这意味着它不依赖于智能合约的存在来更新余额以跟踪所有权，就像 ERC-20 代币那样。

在 FuelVM 上，*所有*资产都是原生资产，并且发送 _任何_ 原生资产的流程相同。

尽管您仍然需要一个智能合约来处理资产的铸造和销毁，但这些资产的发送和接收可以独立于资产合约进行。

然而，就像 EVM 一样，Fuel 有一个标准，描述了使用 Sway 语言的原生资产的标准 API。与 Sway 语言相对应的 ERC-20 是 [SRC-20; 原生资产标准](https://github.com/FuelLabs/sway-standards/blob/master/SRCs/src-20.md)。

> **注意** 需要注意的是，Fuel 没有代币。

### ERC-721 vs 原生资产

在 EVM 上，ERC-721 代币或 NFT 是一个包含多个彼此不可互换的代币的合约。

在 FuelVM 上，ERC-721 的等价物是每个资产的供应量为一的原生资产。这在 [SRC-20; 原生资产标准](https://github.com/FuelLabs/sway-standards/blob/master/SRCs/src-20.md#non-fungible-asset-restrictions) 中定义了非可互换资产限制。

实际上，这意味着所有 NFT 都与 Fuel 上的任何其他原生资产一样对待。在编写 Sway 代码时，不需要额外处理非可互换资产和可互换资产的情况。

### 无需代币批准

原生资产带来的一个优势是不需要代币批准；就像 EVM 上的以太币一样。由于每年因代币批准错误而被黑客攻击的金额达数百万美元，FuelVM 消除了这种攻击向量。

### 资产 vs 硬币 vs 代币

"资产" 是 Fuel 上的原生资产，具有关联的 `AssetId` 类型。资产是可以互相区分的。 "硬币" 表示资产的单个单位。同一资产的硬币是无法互相区分的。

Fuel 不像以太坊等其他生态系统一样使用代币，而是使用具有 UTXO 设计的原生资产。

## `AssetId` 类型

`AssetId` 类型表示 Fuel 上的任何原生资产。`AssetId` 用于与网络上的资产进行交互。

任何 Fuel 上的原生资产的 `AssetId` 是通过取铸造资产的起始 `ContractId` 和 `SubId` 的 SHA256 哈希摘要来计算的，即 `sha256((contract_id, sub_id))`。

### 创建新的 `AssetId`

有三种实例化新 `AssetId` 的方法：

#### 默认

当一个合约只会铸造单个资产时，建议使用 `DEFAULT_ASSET_ID` 子 ID。这被称为合约的默认资产。

要从内部合约调用中获取默认资产，请调用 `default()` 函数：

```sway
{{#include ../../../../examples/native_asset/src/main.sw:default_asset_id}}
```

#### 新建

如果一个合约铸造多个资产或者资产已经由外部合约铸造，将需要使用 `new()` 函数。`new()` 函数接受铸造代币的合约的 `ContractId`，以及一个 `SubId`。

要使用 `ContractId` 和 `SubId` 创建新的 `AssetId`，请调用 `new()` 函数：

```sway
{{#include ../../../../examples/native_asset/src/main.sw:new_asset_id}}
```

#### 来源

如果已知资产的 `b256` 值，则可以调用 `from()` 函数并传入该 `b256` 值。

```sway
{{#include ../../../../examples/native_asset/src/main.sw:from_asset_id}}
```

## `SubId` 类型

SubId 用于区分由同一合约创建的不同资产。`SubId` 是一个 `b256` 值。

在 Fuel 上创建单个新资产时，我们建议使用 `DEFAULT_SUB_ID` 或 `SubId::zero()`。

## 基础资产

在 Fuel 网络上，基础资产是以太币。这是 Fuel 网络上唯一没有 `SubId` 的资产。

可以通过调用 `AssetId` 类型的 `base()` 函数随时返回基础资产。

```sway
{{#include ../../../../examples/native_asset/src/main.sw:base_asset}}
```

## 基本原生资产功能

### 铸造原生资产

要铸造新资产，必须在合约内部调用 `std::asset::mint()` 函数。必须提供一个 `SubId` 和硬币数量。这些新铸造的硬币将由铸造它们的合约拥有。要从同一合约铸造另一个资产，请用您想要的 `SubId` 替换 `DEFAULT_SUB_ID`。

```sway
{{#include ../../../../examples/native_asset/src/main.sw:mint_asset}}
```

您还可以使用 `std::asset::mint_to()` 函数将资产铸造到特定的实体。请务必提供一个将拥有新铸造硬币的目标 `Identity`。

```sway
{{#include ../../../../examples/native_asset/src/main.sw:mint_to_asset}}
```

如果您打算允许外部用户使用您的合约铸造资产，[SRC-3; 铸造和销毁标准](https://github.com/FuelLabs/sway-standards/blob/master/SRCs/src-3.md#fn-mintrecipient-identity-vault_sub_id-subid-amount-u64) 定义了一个标准的 API 用于铸造资产。[Sway-Libs 资产库](https://fuellabs.github.io/sway-libs/book/asset/supply.html) 还提供了一个额外的库来支持将 SRC-3 标准实现到您的合约中。

### 销毁原生资产

要销毁资产，必须从铸造它们的合约内部调用 `std::asset::burn()` 函数。必须提供用于铸造硬币的 `SubId` 和数量。被销毁的硬币必须由合约拥有。当资产被销毁时，它不再存在。

```sway
{{#include ../../../../examples/native_asset/src/main.sw:burn_asset}}
```

如果您打算允许外部用户使用您的合约销毁资产，[SRC-3; 铸造和销毁标准](https://github.com/FuelLabs/sway-standards/blob/master/SRCs/src-3.md#fn-mintrecipient-identity-vault_sub_id-subid-amount-u64) 定义了一个标准的 API 用于销毁资产。[Sway-Libs 资产库](https://fuellabs.github.io/sway-libs/book/asset/supply.html) 还提供了一个额外的库来支持将 SRC-3 标准实现到您的合约中。

### 转移原生资产

要内部转移原生资产，必须调用 `std::asset::transfer()` 函数。还必须提供目标 `Identity` 或用户以及资产的 `AssetId` 和数量。

```sway
{{#include ../../../../examples/native_asset/src/main.sw:transfer_asset}}
```

### 原生资产和交易

#### 获取交易资产

要查询交易中发送的原生资产，可以调用 `std::call_frames::msg_asset_id()` 函数。

```sway
{{#include ../../../../examples/native_asset/src/main.sw:msg_asset_id}}
```

#### 获取交易数量

要查询交易中发送的硬币数量，可以调用 `std::context::msg_amount()` 函数。

```sway
{{#include ../../../../examples/native_asset/src/main.sw:msg_amount}}
```

### 原生资产和合约

#### 检查合约余额

要内部检查合约的余额，请使用相应的 `AssetId` 调用 `std::context::this_balance()` 函数。

```sway
{{#include ../../../../examples/native_asset/src/main.sw:this_balance}}
```

要检查外部合约的余额，请使用相应的 `AssetId` 调用 `std::context::balance_of()` 函数。

```sway
{{#include ../../../../examples/native_asset/src/main.sw:balance_of}}
```

> **注意** 由于 FuelVM 的 UTXO 设计，无法在 Sway 语言中返回 `Address` 的余额。这必须在链下使用 SDK 完成。

#### 在合约中接收原生资产

默认情况下，合约可能不会在合约调用中接收原生资产。要允许向合约转移资产，请在函数中添加 `#[payable]` 属性。

```sway
{{#include ../../../../examples/native_asset/src/main.sw:payable}}
```

## 原生资产标准

已开发了许多标准以实现原生资产的更多功能，并帮助跨合约功能。有关标准的信息可以在 [Sway 标准库](https://github.com/FuelLabs/sway-standards) 中找到。

我们目前针对原生资产有以下标准：

- [SRC-20; 原生资产标准](https://github.com/FuelLabs/sway-standards/blob/master/SRCs/src-20.md) 定义了使用 Sway 语言实现原生资产的标准 API。
- [SRC-3; 铸造和销毁标准](https://github.com/FuelLabs/sway-standards/blob/master/SRCs/src-3.md) 用于启用原生资产的铸造和销毁功能。
- [SRC-7; 任意资产元数据标准](https://github.com/FuelLabs/sway-standards/blob/master/SRCs/src-7.md) 用于存储原生资产的元数据。
- [SRC-6; 保险库标准](https://github.com/FuelLabs/sway-standards/blob/master/SRCs/src-6.md) 定义了使用 Sway 开发的资产保险库的标准 API。

## 原生资产库

已开发了其他库，允许您快速创建并部署符合 [Sway 标准](https://github.com/FuelLabs/sway-standards) 的 dApps。

- [资产库](https://fuellabs.github.io/sway-libs/book/asset/index.html) 提供了实现 [SRC-20; 原生资产标准](https://github.com/FuelLabs/sway-standards/blob/master/SRCs/src-20.md)、[SRC-3; 铸造和销毁标准](https://github.com/FuelLabs/sway-standards/blob/master/SRCs/src-3.md) 和 [SRC-7; 任意资产元数据标准](https://github.com/FuelLabs/sway-standards/blob/master/SRCs/src-7.md) 的功能。

## 单一原生资产示例

在这个完全详细的示例中，我们展示了一个铸造单一资产的原生资产合约。这相当于以太坊中的 ERC-20 标准使用。请注意，没有代币批准功能。

它实现了 [SRC-20; 原生资产](https://github.com/FuelLabs/sway-standards/blob/master/SRCs/src-20.md)、[SRC-3; 铸造和销毁](https://github.com/FuelLabs/sway-standards/blob/master/SRCs/src-3.md) 和 [SRC-5; 拥有权](https://github.com/FuelLabs/sway-standards/blob/master/SRCs/src-5.md) 标准。它不使用任何外部库。

```sway
// ERC20 equivalent in Sway.
contract;

use src3::SRC3;
use src5::{SRC5, State, AccessError};
use src20::SRC20;
use std::{
    asset::{
        burn,
        mint_to,
    },
    call_frames::{
        contract_id,
        msg_asset_id,
    },
    constants::DEFAULT_SUB_ID,
    context::msg_amount,
    string::String,
};

configurable {
    DECIMALS: u8 = 9u8,
    NAME: str[7] = __to_str_array("MyAsset"),
    SYMBOL: str[5] = __to_str_array("MYTKN"),
}

storage {
    total_supply: u64 = 0,
    owner: State = State::Uninitialized,
}

// Native Asset Standard
impl SRC20 for Contract {
    #[storage(read)]
    fn total_assets() -> u64 {
        1
    }

    #[storage(read)]
    fn total_supply(asset: AssetId) -> Option<u64> {
        if asset == AssetId::default() {
            Some(storage.total_supply.read())
        } else {
            None
        }
    }

    #[storage(read)]
    fn name(asset: AssetId) -> Option<String> {
        if asset == AssetId::default() {
            Some(String::from_ascii_str(from_str_array(NAME)))
        } else {
            None
        }
    }

    #[storage(read)]
    fn symbol(asset: AssetId) -> Option<String> {
        if asset == AssetId::default() {
            Some(String::from_ascii_str(from_str_array(SYMBOL)))
        } else {
            None
        }
    }

    #[storage(read)]
    fn decimals(asset: AssetId) -> Option<u8> {
        if asset == AssetId::default() {
            Some(DECIMALS)
        } else {
            None
        }
    }
}

// Ownership Standard
impl SRC5 for Contract {
    #[storage(read)]
    fn owner() -> State {
        storage.owner.read()
    }
}

// Mint and Burn Standard
impl SRC3 for Contract {
    #[storage(read, write)]
    fn mint(recipient: Identity, sub_id: SubId, amount: u64) {
        require(sub_id == DEFAULT_SUB_ID, "incorrect-sub-id");
        require_access_owner();

        storage
            .total_supply
            .write(amount + storage.total_supply.read());
        mint_to(recipient, DEFAULT_SUB_ID, amount);
    }

    #[storage(read, write)]
    fn burn(sub_id: SubId, amount: u64) {
        require(sub_id == DEFAULT_SUB_ID, "incorrect-sub-id");
        require(msg_amount() >= amount, "incorrect-amount-provided");
        require(
            msg_asset_id() == AssetId::default(),
            "incorrect-asset-provided",
        );
        require_access_owner();

        storage
            .total_supply
            .write(storage.total_supply.read() - amount);
        burn(DEFAULT_SUB_ID, amount);
    }
}

abi SingleAsset {
    #[storage(read, write)]
    fn constructor(owner_: Identity);
}

impl SingleAsset for Contract {
    #[storage(read, write)]
    fn constructor(owner_: Identity) {
        require(storage.owner.read() == State::Uninitialized, "owner-initialized");
        storage.owner.write(State::Initialized(owner_));
    }
}

#[storage(read)]
fn require_access_owner() {
    require(
        storage.owner.read() == State::Initialized(msg_sender().unwrap()),
        AccessError::NotOwner,
    );
}
```

## 多种原生资产示例

在这个完全详细的示例中，我们展示了一个铸造多种资产的原生资产合约。这相当于以太坊中的 ERC-1155 标准使用。请注意，没有代币批准功能。

它实现了 [SRC-20; 原生资产](https://github.com/FuelLabs/sway-standards/blob/master/SRCs/src-20.md)、[SRC-3; 铸造和销毁](https://github.com/FuelLabs/sway-standards/blob/master/SRCs/src-3.md) 和 [SRC-5; 拥有权](https://github.com/FuelLabs/sway-standards/blob/master/SRCs/src-5.md) 标准。它不使用任何外部库。

```sway
// ERC1155 equivalent in Sway.
contract;

use src5::{SRC5, State, AccessError};
use src20::SRC20;
use src3::SRC3;
use std::{
    asset::{
        burn,
        mint_to,
    },
    call_frames::{
        contract_id,
        msg_asset_id,
    },
    hash::{
        Hash,
    },
    context::this_balance,
    storage::storage_string::*,
    string::String
};

storage {
    total_assets: u64 = 0,
    total_supply: StorageMap<AssetId, u64> = StorageMap {},
    name: StorageMap<AssetId, StorageString> = StorageMap {},
    symbol: StorageMap<AssetId, StorageString> = StorageMap {},
    decimals: StorageMap<AssetId, u8> = StorageMap {},
    owner: State = State::Uninitialized,
}

// Native Asset Standard
impl SRC20 for Contract {
    #[storage(read)]
    fn total_assets() -> u64 {
        storage.total_assets.read()
    }

    #[storage(read)]
    fn total_supply(asset: AssetId) -> Option<u64> {
        storage.total_supply.get(asset).try_read()
    }

    #[storage(read)]
    fn name(asset: AssetId) -> Option<String> {
        storage.name.get(asset).read_slice()
    }

    #[storage(read)]
    fn symbol(asset: AssetId) -> Option<String> {
        storage.symbol.get(asset).read_slice()
    }

    #[storage(read)]
    fn decimals(asset: AssetId) -> Option<u8> {
        storage.decimals.get(asset).try_read()
    }
}

// Mint and Burn Standard
impl SRC3 for Contract {
    #[storage(read, write)]
    fn mint(recipient: Identity, sub_id: SubId, amount: u64) {
        require_access_owner();
        let asset_id = AssetId::new(contract_id(), sub_id);
        let supply = storage.total_supply.get(asset_id).try_read();
        if supply.is_none() {
            storage.total_assets.write(storage.total_assets.try_read().unwrap_or(0) + 1);
        }
        let current_supply = supply.unwrap_or(0);
        storage.total_supply.insert(asset_id, current_supply + amount);
        mint_to(recipient, sub_id, amount);
    }

    #[storage(read, write)]
    fn burn(sub_id: SubId, amount: u64) {
        require_access_owner();
        let asset_id = AssetId::new(contract_id(), sub_id);
        require(this_balance(asset_id) >= amount, "not-enough-coins");

        let supply = storage.total_supply.get(asset_id).try_read();
        let current_supply = supply.unwrap_or(0);
        storage.total_supply.insert(asset_id, current_supply - amount);
        burn(sub_id, amount);
    }
}

abi MultiAsset {
    #[storage(read, write)]
    fn constructor(owner_: Identity);

    #[storage(read, write)]
    fn set_name(asset: AssetId, name: String);

    #[storage(read, write)]
    fn set_symbol(asset: AssetId, symbol: String);

    #[storage(read, write)]
    fn set_decimals(asset: AssetId, decimals: u8);
}

impl MultiAsset for Contract {
    #[storage(read, write)]
    fn constructor(owner_: Identity) {
        require(storage.owner.read() == State::Uninitialized, "owner-initialized");
        storage.owner.write(State::Initialized(owner_));
    }

    #[storage(read, write)]
    fn set_name(asset: AssetId, name: String) {
        require_access_owner();
        storage.name.insert(asset, StorageString {});
        storage.name.get(asset).write_slice(name);
    }

    #[storage(read, write)]
    fn set_symbol(asset: AssetId, symbol: String) {
        require_access_owner();
        storage.symbol.insert(asset, StorageString {});
        storage.symbol.get(asset).write_slice(symbol);
    }

    #[storage(read, write)]
    fn set_decimals(asset: AssetId, decimals: u8) {
        require_access_owner();
        storage.decimals.insert(asset, decimals);
    }
}

#[storage(read)]
fn require_access_owner() {
    require(
        storage.owner.read() == State::Initialized(msg_sender().unwrap()),
        AccessError::NotOwner,
    );
}
```
