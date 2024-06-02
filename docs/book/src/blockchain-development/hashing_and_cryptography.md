# 哈希和加密

Sway 标准库提供了一系列加密哈希函数（`sha256` 和与 EVM 兼容的 `keccak256`），以及基于 EVM 兼容的 `secp256k1` 签名恢复操作的便捷访问。

## 哈希

```sway
{{#include ../../../../examples/hashing/src/main.sw}}
```

## 签名恢复

```sway
{{#include ../../../../examples/signatures/src/main.sw}}
```

> **注意**: 通过 `std::vm::evm` 还支持 EVM 地址的恢复。
