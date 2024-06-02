# Sway Standards

就像许多其他智能合约语言一样，已经制定了使用标准，以实现智能合约之间的交叉兼容性。

有关使用 Sway 标准的更多信息，请参阅 [Sway-Standards 仓库](https://github.com/FuelLabs/sway-standards)。

## 标准

### 本地资产标准

- [SRC-20; 本地资产标准](https://github.com/FuelLabs/sway-standards/blob/master/SRCs/src-20.md) 定义了使用 Sway 语言实现标准 API 的 [本地资产](../blockchain-development/native_assets.md)。
- [SRC-3; 铸币和销毁](https://github.com/FuelLabs/sway-standards/blob/master/SRCs/src-3.md) 用于为本地资产启用铸币和销毁功能。
- [SRC-7; 任意资产元数据标准](https://github.com/FuelLabs/sway-standards/blob/master/SRCs/src-7.md) 用于存储 [本地资产](../blockchain-development/native_assets.md) 的元数据，通常作为 NFT。
- [SRC-9; 元数据键标准](https://github.com/FuelLabs/sway-standards/blob/master/SRCs/src-9.md) 用于与 SRC-7 标准结合使用，存储 [本地资产](../blockchain-development/native_assets.md) 的标准化元数据键。
- [SRC-6; 保险库标准](https://github.com/FuelLabs/sway-standards/blob/master/SRCs/src-6.md) 定义了在 Sway 中开发的资产保险库的标准 API 的实现。

### 断言标准

- [SRC-13; 绑定灵魂地址标准](https://github.com/FuelLabs/sway-standards/blob/master/SRCs/src-13.md) 定义了特定的 `Address` 作为绑定灵魂资产的灵魂绑定地址，使其成为不可转让的。

### 访问控制标准

- [SRC-5; 拥有权标准](https://github.com/FuelLabs/sway-standards/blob/master/SRCs/src-5.md) 用于将函数调用限制为合约中的管理员用户。

### 合约标准

- [SRC-12; 合约工厂](https://github.com/FuelLabs/sway-standards/blob/master/SRCs/src-12.md) 定义了合约工厂的标准 API 的实现。

### 桥接标准

- [SRC-8; 桥接资产](https://github.com/FuelLabs/sway-standards/blob/master/SRCs/src-8.md) 定义了桥接到 Fuel Network 的资产所需的元数据。
- [SRC-10; 本地桥接标准](https://github.com/FuelLabs/sway-standards/blob/master/SRCs/src-10.md) 定义了 Fuel Chain 和规范基链之间的本地桥接的标准 API。

### 文档标准

- [SRC-2; 内联文档](https://github.com/FuelLabs/sway-standards/blob/master/SRCs/src-2.md) 定义了如何记录您的 Sway 文件。

## 标准支持

还开发了支持 Sway 标准的库。这些可以在 [Sway-Libs](../reference/sway_libs.md) 中找到。
