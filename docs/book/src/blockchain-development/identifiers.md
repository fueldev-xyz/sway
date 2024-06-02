# 标识符

Sway 中的地址类似于 EVM 地址。两者的主要区别在于：

1. Sway 地址长度为 32 字节（而不是 20 字节）
2. Sway 地址是使用公钥的 SHA-256 哈希计算而得，而不是 keccak-256 哈希。

另一方面，合约是通过合约 ID 而不是地址唯一标识的。合约的 ID 也是长度为 32 字节，并且是通过[此处](https://fuellabs.github.io/fuel-specs/master/protocol/id/contract)计算得到的。
