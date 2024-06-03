# 编译器内联函数

Sway 编译器支持一系列内联函数，这些函数执行各种低级操作，对于构建库非常有用。编译器内联函数应该很少使用，但它们优于 `asm` 块，因为它们是类型检查的，并且总体上更安全。以下是所有可用的编译器内联函数列表：

---

```sway
__size_of_val<T>(val: T) -> u64
```

**描述:** 返回类型 `T` 的大小（以字节为单位）。

**约束:** 无。

---

```sway
__size_of<T>() -> u64
```

**描述:** 返回类型 `T` 的大小（以字节为单位）。

**约束:** 无。

---

```sway
__size_of_str_array<T>() -> u64
```

**描述:** 返回类型 `T` 的大小（以字节为单位）。此内联函数与 `__size_of` 的区别在于“字符串数组”的情况下，它返回字符串的实际长度（以字节为单位），而不将字节大小填充到下一个字对齐。 当 `T` 不是“字符串数组”时，返回 `0`。

**约束:** 无。

---

```sway
__assert_is_str_array<T>()
```

**描述:** 如果类型 `T` 不是“字符串数组”，则抛出编译错误。

**约束:** 无。

---

```sway
__to_str_array(s: str) -> str[N]
```

**描述:** 在编译时将“字符串切片”转换为“字符串数组”。参数 “s” 必须是字符串字面量。

**约束:** 无。

---

```sway
__is_reference_type<T>() -> bool
```

**描述:** 如果 `T` 是引用类型，则返回 `true`，否则返回 `false`。

**约束:** 无。

---

```sway
__is_str_array<T>() -> bool
```

**描述:** 如果 `T` 是字符串数组，则返回 `true`，否则返回 `false`。

**约束:** 无。

---

```sway
__eq<T>(lhs: T, rhs: T) -> bool
```

**描述:** 返回 `lhs` 和 `rhs` 是否相等。

**约束:** `T` 是 `bool`，`u8`，`u16`，`u32`，`u64`，`u256`，`b256` 或 `raw_ptr`。

---

```sway
__gt<T>(lhs: T, rhs: T) -> bool
```

**描述:** 返回 `lhs` 是否大于 `rhs`。

**约束:** `T` 是 `u8`，`u16`，`u32`，`u64`，`u256`，`b256`。

---

```sway
__lt<T>(lhs: T, rhs: T) -> bool
```

**描述:** 返回 `lhs` 是否小于 `rhs`。

**约束:** `T` 是 `u8`，`u16`，`u32`，`u64`，`u256`，`b256`。

---

```sway
__gtf<T>(index: u64, tx_field_id: u64) -> T
```

**Description:** 返回适用时 ID 为 `tx_field_id` 的事务字段在索引 `index` 处的值。这是对 FuelVM 的 [`gtf` 指令](https://fuellabs.github.io/fuel-specs/master/vm/instruction_set#gtf-get-transaction-fields) 的封装。结果字段转换为 `T` 类型。

**Constraints:** 无。

---

```sway
__addr_of<T>(val: T) -> raw_ptr
```

**Description:** 返回存储 `val` 的内存地址。

**Constraints:** `T` 是引用类型。

---

```sway
__state_load_word(key: b256) -> u64
```

**Description:** 从键 `key` 处的存储中读取并返回单个字。

**Constraints:** 无。

---

```sway
__state_load_quad(key: b256, ptr: raw_ptr, slots: u64) -> bool
```

**Description:** 从键 `key` 处开始读取 `slots` 个数的槽（每个为 `b256`），并将它们存储到从地址 `ptr` 开始的内存中。返回一个布尔值，描述所有存储槽是否先前已设置。

**Constraints:** 无。

---

```sway
__state_store_word(key: b256, val: u64) -> bool
```

**Description:** 将单个字 `val` 存储到键 `key` 处的存储中。返回一个布尔值，描述存储槽是否先前已设置。

**Constraints:** 无。

---

```sway
__state_store_quad(key: b256, ptr: raw_ptr, slots: u64) -> bool
```

**Description:** 将从地址 `ptr` 开始的 `slots` 个槽（每个为 `b256`）从内存存储到从键 `key` 开始的存储中。返回一个布尔值，描述第一个存储槽是否先前已设置。

**Constraints:** 无。

---

```sway
__log<T>(val: T)
```

**Description:** 记录值 `val`。

**Constraints:** 无。

---

```sway
__add<T>(lhs: T, rhs: T) -> T
```

**Description:** 将 `lhs` 与 `rhs` 相加并返回结果。

**Constraints:** `T` 是整数类型，即 `u8`，`u16`，`u32`，`u64`，`u256`。

---

```sway
__sub<T>(lhs: T, rhs: T) -> T
```

**Description:** 将 `rhs` 从 `lhs` 中减去。

**Constraints:** `T` 是整数类型，即 `u8`，`u16`，`u32`，`u64`，`u256`。

---

```sway
__mul<T>(lhs: T, rhs: T) -> T
```

**Description:** 将 `lhs` 乘以 `rhs`。

**Constraints:** `T` 是整数类型，即 `u8`，`u16`，`u32`，`u64`，`u256`。

---

```sway
__div<T>(lhs: T, rhs: T) -> T
```

**Description:** 将 `lhs` 除以 `rhs`。

**Constraints:** `T` 是整数类型，即 `u8`，`u16`，`u32`，`u64`，`u256`。

---

```sway
__and<T>(lhs: T, rhs: T) -> T
```

**Description:** 对 `lhs` 和 `rhs` 进行按位与操作。

**Constraints:** `T` 是整数类型，即 `u8`，`u16`，`u32`，`u64`，`u256`，`b256`。

---

```sway
__or<T>(lhs: T, rhs: T) -> T
```

**Description:** 对 `lhs` 和 `rhs` 进行按位或操作。

**Constraints:** `T` 是整数类型，即 `u8`，`u16`，`u32`，`u64`，`u256`，`b256`。

---

```sway
__xor<T>(lhs: T, rhs: T) -> T
```

**Description:** 对 `lhs` 和 `rhs` 进行按位异或操作。

**Constraints:** `T` 是整数类型，即 `u8`，`u16`，`u32`，`u64`，`u256`，`b256`。

---

```sway
__mod<T>(lhs: T, rhs: T) -> T
```

**Description:** 计算 `lhs` 除以 `rhs` 的余数。

**Constraints:** `T` 是整数类型，即 `u8`，`u16`，`u32`，`u64`，`u256`。

---

```sway
__rsh<T>(lhs: T, rhs: u64) -> T
```

**Description:** 将 `lhs` 逻辑右移 `rhs` 位。

**Constraints:** `T` 是整数类型，即 `u8`，`u16`，`u32`，`u64`，`u256`，`b256`。

---

```sway
__lsh<T>(lhs: T, rhs: u64) -> T
```

**Description:** 将 `lhs` 逻辑左移 `rhs` 位。

**Constraints:** `T` 是整数类型，即 `u8`，`u16`，`u32`，`u64`，`u256`，`b256`。

---

```sway
__revert(code: u64)
```

**Description:** 使用错误代码 `code` 进行回滚。

**Constraints:** 无。

---

```sway
__ptr_add(ptr: raw_ptr, offset: u64)
```

**Description:** 将指针 `ptr` 的原始值加上 `offset`。

**Constraints:** 无。

---

```sway
__ptr_sub(ptr: raw_ptr, offset: u64)
```

**Description:** 将指针 `ptr` 的原始值减去 `offset`。

**Constraints:** 无。

---

```sway
__smo<T>(recipient: b256, data: T, coins: u64)
```

**Description:** 将任意类型 `T` 的消息 `data` 和 `coins` 数量的基础资产发送到地址 `recipient`。

**Constraints:** 无。

---

```sway
__not(op: T) -> T
```

**Description:** 对 `op` 进行按位非操作。

**Constraints:** `T` 是整数类型，即 `u8`，`u16`，`u32`，`u64`，`u256`，`b256`。

---

```sway
__jmp_mem()
```

**Description:** 跳转到 `MEM[$hp]`。

**Constraints:** 无。
