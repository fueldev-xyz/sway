# 常量

<!-- This section should explain what constants are in Sway -->
<!-- constants:example:start -->

常量与变量类似；但是，有一些区别：

- 常量始终在编译时评估。
- 常量可以在函数内部和全局/`impl`作用域中声明。
- 不能在常量中使用`mut`关键字。

<!-- constants:example:end -->

```sway
const ID: u32 = 0;
```

常量的初始化表达式可能相当复杂，但不能使用汇编指令、存储访问、可变变量、循环和`return`语句等。尽管如此，函数调用、基本类型和复合数据结构完全可以使用：

```sway
fn bool_to_num(b: bool) -> u64 {
if b {
1
} else {
0
}
}

fn arr_wrapper(a: u64, b: u64, c: u64) -> [u64; 3] {
[a, b, c]
}

const ARR2 = arr_wrapper(bool_to_num(1) + 42, 2, 3);
```

## 关联常量

<!-- This section should explain what associated constants are -->
<!-- assoc_constants:example:start -->

关联常量是与类型相关联的常量，可以在`impl`块或`trait`定义中声明。

在`trait`定义内部声明的关联常量可能省略它们的初始化器，以指示该 trait 的每个实现必须指定这些初始化器。

标识符是路径中使用的常量名称。类型是定义必须实现的类型。

<!-- assoc_constants:example:end -->

您可以在 trait 的接口表面直接*定义*关联`const`：

```sway
script;

trait ConstantId {
const ID: u32 = 0;
}
```

或者，您也可以在 trait 中*声明*它，并在实现 trait 的类型的接口中实现它。

```sway
script;

trait ConstantId {
const ID: u32;
}

struct Struct {}

impl ConstantId for Struct {
const ID: u32 = 1;
}

fn main() -> u32 {
Struct::ID
}
```

### `impl self` 常量

常量也可以在非 trait 的`impl`块中声明。

```sway
script;

struct Point {
x: u64,
y: u64,
}

impl Point {
const ZERO: Point = Point { x: 0, y: 0 };
}

fn main() -> u64 {
Point::ZERO.x
}
```

## 可配置常量

<!-- This section should explain what configurable constants are in Sway -->
<!-- config_constants:example:start -->

可配置常量是特殊的常量，其行为类似于常规常量，因为它们在程序执行期间无法更改，但它们可以在构建 Sway 程序之后*配置*。Rust 和 TS SDK 允许直接将新值注入这些常量的字节码中，而无需重新构建程序即可更新这些常量的值。这些对于合约工厂非常有用，并且在某种程度上类似于像 Solidity 这样的语言中的`immutable`变量。

<!-- config_constants:example:end -->

可配置常量在`configurable`块中声明，并且需要类型和初始化器，如下所示：

```sway
{{#include ../../../../examples/configurable_constants/src/main.sw:configurable_block}}
```

在 Sway 项目中最多只允许一个`configurable`块。此外，不允许在库中使用`configurable`块。

可配置常量可以像常规常量一样直接读取：

```sway
{{#include ../../../../examples/configurable_constants/src/main.sw:using_configurables}}
```
