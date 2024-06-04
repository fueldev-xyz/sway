# Sway Libraries

Sway Libraries 的目的是包含用户可以导入并使用的库，这些库不是标准库的一部分。

这些库包含了对区块链开发有价值的辅助函数和其他工具。

有关如何使用 Sway-Libs 库的更多信息，请参阅 [Sway-Libs 书籍](https://fuellabs.github.io/sway-libs/book/getting_started/index.html)。

## 资产库

资产库是任何使用[原生资产](../blockchain-development/native_assets.md)在 Fuel Network 上的库。

- [资产库](https://fuellabs.github.io/sway-libs/book/asset/index.html); 提供了 [SRC-20](https://github.com/FuelLabs/sway-standards/blob/master/SRCs/src-20.md)、[SRC-3](https://github.com/FuelLabs/sway-standards/blob/master/SRCs/src-3.md) 和 [SRC-7](https://github.com/FuelLabs/sway-standards/blob/master/SRCs/src-7.md) 标准的辅助函数。

## 访问控制和安全库

访问控制和安全库是任何旨在在开发智能合约时提供额外安全性的库。

- [所有权库](https://fuellabs.github.io/sway-libs/book/ownership/index.html); 用于对函数应用限制，使只有**单个**用户可以调用它们。此库为 [SRC-5; 拥有权标准](https://github.com/FuelLabs/sway-standards/blob/master/SRCs/src-5.md) 提供了辅助函数。
- [管理员库](https://fuellabs.github.io/sway-libs/book/admin/index.html); 用于对函数应用限制，使只有少数几个用户可以调用它们，类似于白名单。
- [暂停库](https://fuellabs.github.io/sway-libs/book/pausable/index.html); 允许合约实现紧急停止机制。
- [重入防护库](https://fuellabs.github.io/sway-libs/book/reentrancy/index.html); 用于检测和防止重入攻击。

## 密码学库

密码学库是提供了超出 std-lib 范围的密码学功能的库。

- [字节码库](https://fuellabs.github.io/sway-libs/book/bytecode/index.html); 用于在链上验证和计算合约和断言的字节码根。
- [默克尔证明库](https://fuellabs.github.io/sway-libs/book/merkle/index.html); 用于验证链下计算的二进制默克尔树。

## 数学库

数学库是提供了超出 std-lib 范围的数学函数或数字类型的库。

- [固定点数库](https://fuellabs.github.io/sway-libs/book/fixed_point/index.html); 用于实现固定点数的接口。
- [有符号整数库](https://fuellabs.github.io/sway-libs/book/signed_integers/index.html); 用于实现有符号整数的接口。

## 数据结构库

数据结构库提供了复杂的数据结构，为智能合约解锁了额外的功能。

- [队列库](https://fuellabs.github.io/sway-libs/book/queue/index.html); 一个线性数据结构，提供先进先出 (FIFO) 操作。
