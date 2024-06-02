# 函数

在 Sway 中，函数使用 `fn` 关键字声明。让我们来看一个例子：

```sway
fn equals(first_param: u64, second_param: u64) -> bool {
first_param == second_param
}
```

我们刚刚声明了一个名为 `equals` 的函数，它接受两个参数：`first_param` 和 `second_param`。这两个参数都必须是 64 位无符号整数。

此函数还返回一个 `bool` 值，即 `true` 或 `false`。如果两个给定参数相等，则此函数返回 `true`，如果它们不相等，则返回 `false`。如果我们想要使用这个函数，可以这样做：

```sway
fn main() {
    equals(5, 5); // 计算结果为 `true`    equals(5, 6); // 计算结果为`false`
}
```

## 可变参数

<!-- This section should explain how/when to use `ref mut` -->
<!-- ref_mut:example:start -->

我们可以通过在参数名之前添加 `ref mut` 来使函数参数可变。这允许在调用函数时改变传递给函数的参数。

<!-- ref_mut:example:end -->

例如：

```sway
{{#include ../../../../examples/ref_mut_params/src/main.sw:increment}}
```

此函数允许修改其参数 `num`，因为它带有 `mut` 关键字。此外，`ref` 关键字指示函数在调用函数时修改传递给它的参数，而不是修改它的本地副本。

```sway
{{#include ../../../../examples/ref_mut_params/src/main.sw:call_increment}}
```

请注意，为了上面的示例编译通过，变量 `num` 本身必须声明为可变的。

> **注意**
> 目前不允许在函数参数中单独使用 `mut` 或 `ref` 关键字。

类似地，`ref mut` 可以与更复杂的数据类型一起使用，例如：

```sway
{{#include ../../../../examples/ref_mut_params/src/main.sw:tuple_and_enum}}
```

然后我们可以像下面这样调用这些函数：

```sway
{{#include ../../../../examples/ref_mut_params/src/main.sw:call_tuple_and_enum}}
```

> **注意**
> 在 Sway 程序中，`ref` 关键字有效的唯一位置是在可变函数参数之前。
