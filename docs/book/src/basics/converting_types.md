# 类型转换

以下是在 Sway 中常见的类型转换：

- [身份转换](#identity-conversions)
- [字符串转换](#string-conversions)
- [数字转换](#number-conversions)
- [字节数组转换](#byte-array-conversions)

## 身份转换

### 转换为 `Identity`

```sway
{{#include ../../../../examples/converting_types/src/identity.sw:convert_to_identity}}
```

### 将 `Identity` 转换为 `ContractId` 或 `Address`

```sway
{{#include ../../../../examples/converting_types/src/identity.sw:convert_from_identity}}
```

### 将 `ContractId` 或 `Address` 转换为 `b256`

```sway
{{#include ../../../../examples/converting_types/src/identity.sw:convert_to_b256}}
```

### 将 `b256` 转换为 `ContractId` 或 `Address`

```sway
{{#include ../../../../examples/converting_types/src/identity.sw:convert_b256_to_address_or_contract_id}}
```

## 字符串转换

### 将 `str` 转换为 `str[]`

```sway
{{#include ../../../../examples/converting_types/src/strings.sw:strings_import}}
```

```sway
{{#include ../../../../examples/converting_types/src/strings.sw:str_to_str_array}}
```

### 将 `str[]` 转换为 `str`

```sway
{{#include ../../../../examples/converting_types/src/strings.sw:str_array_to_str}}
```

## 数字转换

### 转换为 `u256`

```sway
{{#include ../../../../examples/converting_types/src/to_u256.sw:to_u256}}
```

### 转换为 `u64`

```sway
{{#include ../../../../examples/converting_types/src/to_u64.sw:to_u64_import}}
```

```sway
{{#include ../../../../examples/converting_types/src/to_u64.sw:to_u64}}
```

### 转换为 `u32`

```sway
{{#include ../../../../examples/converting_types/src/to_u32.sw:to_u32_import}}
```

```sway
{{#include ../../../../examples/converting_types/src/to_u32.sw:to_u32}}
```

### 转换为 `u16`

```sway
{{#include ../../../../examples/converting_types/src/to_u16.sw:to_u16_import}}
```

```sway
{{#include ../../../../examples/converting_types/src/to_u16.sw:to_u16}}
```

### 转换为 `u8`

```sway
{{#include ../../../../examples/converting_types/src/to_u8.sw:to_u8_import}}
```

```sway
{{#include ../../../../examples/converting_types/src/to_u8.sw:to_u8}}
```

### 转换为 `Bytes`

```sway
{{#include ../../../../examples/converting_types/src/bytes.sw:to_bytes_import}}
```

```sway
{{#include ../../../../examples/converting_types/src/bytes.sw:to_bytes}}
```

### 从 `Bytes` 转换

```sway
{{#include ../../../../examples/converting_types/src/bytes.sw:to_bytes_import}}
```

```sway
{{#include ../../../../examples/converting_types/src/bytes.sw:from_bytes}}
```

## 字节数组转换

### 转换为字节数组

```sway
{{#include ../../../../examples/converting_types/src/byte_arrays.sw:to_byte_array_import}}
```

```sway
{{#include ../../../../examples/converting_types/src/byte_arrays.sw:to_byte_array}}
```

### 从字节数组转换

```sway
{{#include ../../../../examples/converting_types/src/byte_arrays.sw:to_byte_array_import}}
```

```sway
{{#include ../../../../examples/converting_types/src/byte_arrays.sw:from_byte_array}}
```
