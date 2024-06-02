# 内置类型

Sway 中的每个值都属于某种类型。尽管从根本上说，所有的值都只是底层虚拟机中的一和零，但 Sway 需要知道这些一和零实际上代表着什么。这通过 _类型_ 来实现。

<!-- This section should explain how Sway types are inferred -->
<!-- sway_types:example:start -->

Sway 是一种静态类型语言。在编译时，必须知道每个值的类型。这并不意味着您需要指定每个单独的类型：通常，编译器可以合理地推断出类型。

<!-- sway_types:example:end -->

## 原始类型

<!-- This section should list the primitive types in Sway -->
<!-- prim_types:example:start -->

Sway 有以下原始类型：

1. `u8`（8 位无符号整数）
2. `u16`（16 位无符号整数）
3. `u32`（32 位无符号整数）
4. `u64`（64 位无符号整数）
5. `u256`（256 位无符号整数）
6. `str[]`（固定长度字符串）
7. `str`（字符串切片）
8. `bool`（布尔值 `true` 或 `false`）
9. `b256`（256 位（32 字节）哈希）

Sway 中的所有其他类型都是由这些原始类型或对这些原始类型的引用构建的。您可能会注意到没有带符号的整数——这是有意设计的。在 Sway 所占据的区块链领域中，浮点数值和负数的效用较小，因此它们的实现留给了特定用例的库。

<!-- prim_types:example:end -->

## 数值类型

所有的无符号整数类型都是数值类型。

数值可以使用二进制语法、十六进制语法、十进制语法和下划线进行声明。让我们来看一下以下有效的数值原始类型：

```sway
0xffffff // 十六进制
0b10101010 // 二进制
10 // 十进制
100_000 // 以下划线分隔的十进制
0x1111_0000 // 以下划线分隔的二进制
0xfff_aaa // 以下划线分隔的十六进制
```

<!-- This section should explain the default numeric type in Sway -->
<!-- default_num:example:start -->

默认数值类型为 `u64`。FuelVM 的字长为 64 位，使用较小的数值类型节省空间的情况很少。

如果 64 位或 256 位算术操作产生溢出或下溢，则计算会自动由 FuelVM 回滚。

使用 64 位和 256 位操作（包括 `b256`）时不会发生这种情况，它们使用了专门的操作，并且尽可能高效。

<!-- default_num:example:end -->

## 布尔类型

<!-- This section should explain the `bool` type -->
<!-- bool:example:start -->

布尔类型（`bool`）有两种可能的值：`true` 或 `false`。布尔值通常用于条件逻辑或验证，例如在 `if` 表达式中。布尔值可以通过一元否定运算符 `!` 进行否定或翻转。

<!-- bool:example:end -->

例如：

```sway
fn returns_false() -> bool {
let boolean_value: bool = true;
!boolean_value
}
```

## 字符串切片

<!-- This section should explain the string type in Sway -->
<!-- str:example:start -->

在 Sway 中，字符串字面量存储为可变长度字符串切片。这意味着它们存储为指向实际字符串数据及其长度的指针。

<!-- str:example:end -->

```sway
let my_string: str = "fuel";
```

字符串切片，因为它们包含指针，所以使用受限。它们不能用作常量、存储字段或可配置常量，也不能用作主函数参数或返回值。

对于这些情况，必须使用字符串数组，如下所述。

## 字符串数组

<!-- 这一部分应该解释在 Sway 中字符串的类型 -->
<!-- str:example:start -->

在 Sway 中，静态长度的字符串是一种原始类型。这意味着当你声明一个字符串数组时，它的大小是其类型的一部分。这对于编译器来说是必要的，以便知道为该数据的存储分配多少内存。字符串的大小用方括号表示。

<!-- str:example:end -->

让我们来看一个例子：

```sway
let my_string: str[4] = \_\_to_str_array("fuel");
```

因为字符串字面量 `"fuel"` 有四个字母，所以类型是 `str[4]`，表示静态长度为 4 个字符。在 Sway 中，默认使用 UTF-8 编码。

与上面类似，字符串字面量被标记为字符串切片。这就是为什么需要 `__to_str_array` 在编译时将其转换为字符串数组

转换过程可以在运行时用 `from_str_array` 和 `try_as_str_array` 来完成。后者可能会失败，因为指定的字符串数组必须足够大以容纳字符串切片的内容。

```sway
let a: str = "abcd";
let b: str[4] = a.try_as_str_array().unwrap();
let c: str = from_str_array(b);
```

## 复合类型

复合类型是将多个值组合成一个类型的类型。在 Sway 中，我们有数组和元组。

## 元组类型

<!-- This section should explain what a tuple is -->
<!-- tuple:example:start -->

元组是一种通用的静态长度类型聚合。简单地说，元组是一个类型，由零个或多个类型的聚合组成。构成元组的内部类型和元组的度量确定了元组的类型。

<!-- tuple:example:end -->

让我们看一些例子。

```sway
let x: (u64, u64) = (0, 0);
```

## 元组类型

<!-- 此部分应解释元组是什么 -->
<!-- tuple:example:start -->

元组是一种通用的静态长度类型聚合。更简单地说，元组是一种类型，由零个或多个类型的聚合组成。构成元组的内部类型和元组的度量确定了元组的类型。

<!-- tuple:example:end -->

让我们看一些例子。

```sway
let x: (u64, u64) = (0, 0);
```

这是一个元组，由括号括起来的、逗号分隔的值表示。请注意，类型注解 `(u64, u64)` 的语法与实例化该类型的表达式 `(0, 0)` 类似。

```sway
let x: (u64, bool) = (42, true);
assert(x.1);
```

在这个例子中，我们创建了一个新的元组类型 `(u64, bool)`，它由 `u64` 和 `bool` 组成。

<!-- 此部分应解释如何访问元组中的值 -->
<!-- tuple_val:example:start -->

要访问元组中的值，我们使用 _元组索引_：`x.1` 表示元组的第一个（从零开始索引，所以是 `bool`）值。同样，`x.0` 将是元组的第零个 `u64` 值。也可以通过解构访问元组值。

<!-- tuple_val:example:end -->

```sway
struct Foo {}
let x: (u64, Foo, bool) = (42, Foo {}, true);
let (number, foo, boolean) = x;
```

要创建一元组，我们需要添加一个尾随逗号：

```sway
let x: u64 = (42); // x 是类型 u64
let y: (u64) = (42); // y 是类型 u64
let z: (u64,) = (42,); // z 是类型 (u64)，即一元组
let w: (u64) = (42,); // 类型错误
```

## 数组

<!-- 此部分应解释数组是什么 -->
<!-- array:example:start -->

数组类似于元组，但数组的值必须都是相同类型的。数组可以容纳包括非基本类型在内的任意类型。

<!-- array:example:end -->

数组以逗号分隔的列表写在方括号内：

```sway
let x = [1, 2, 3, 4, 5];
```

<!-- 此部分应深入解释数组 -->
<!-- array_details:example:start -->

由于数组的大小已知，它们在堆栈上分配内存。数组的大小始终是静态的，即不能更改。一个由五个元素组成的数组不能变成六个元素组成的数组。

与元组不同，数组可以进行迭代。数组的类型写作数组包含的类型，后跟元素数量，用分号分隔，放在方括号内，例如 `[u64; 5]`。要访问数组中的元素，请使用 _数组索引语法_，即方括号。

<!-- array_details:example:end -->

如果底层数组声明为可变的，则也可以修改数组元素：

```sway
let mut x = [1, 2, 3, 4, 5];
x[0] = 0;
```

```sway
{{#include ../../../../examples/arrays/src/main.sw}}
```
