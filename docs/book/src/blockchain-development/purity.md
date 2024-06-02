# 纯度

在 Sway 中，如果一个函数不访问任何[持久化存储](./storage.md)，则它是 _纯的_。相反，如果函数访问任何存储，则该函数是 _不纯的_。显然，由于存储仅在智能合约中可用，因此不纯函数不能用于谓词、脚本或库。纯函数不能调用不纯函数。

在 Sway 中，默认情况下函数是纯的，但可以通过 `storage` 函数属性选择成为不纯。`storage` 属性可以采用 `read` 和/或 `write` 参数，指示函数需要哪种类型的访问。

```sway
#[storage(read)]
fn get_amount() -> u64 {
    ...
}

#[storage(read, write)]
fn increment_amount(increment: u64) -> u64 {
    ...
}
```

> **注意**：`#[storage(write)]` 属性也允许函数从存储中读取。这是因为部分写入存储槽需要先读取存储槽。

不纯函数也可以调用其他不纯函数，但必须具有至少相同的存储权限或被调用函数的超集。例如，要调用具有写访问权限的函数，调用者必须具有写访问权限，或者同时具有读取和写入访问权限。要调用具有读取和写入访问权限的函数，调用者还必须具有这两种权限。

`storage` 属性也可以应用于[方法和关联函数](../basics/methods_and_associated_functions.md)、[特征](../advanced/traits.md)和[ABI](../sway-program-types/smart_contracts.md#the-abi-declaration)声明。

纯函数为您提供了一些保证：您不会产生过多的存储 gas 成本，编译器可以应用额外的优化，并且它们通常易于推理和审计。

[在 Solidity 中存在类似的概念](https://docs.soliditylang.org/en/v0.8.10/contracts.html#pure-functions)。请注意，在 Solidity 中，合约存储被称为 _合约状态_，而在 Sway/Fuel 生态系统中，这两个术语在很大程度上是可以互换的。
