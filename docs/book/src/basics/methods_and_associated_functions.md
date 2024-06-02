# 方法和关联函数

<!-- 这部分应该解释 Sway 中的方法和关联函数 -->
<!-- methods_af:example:start -->

## 方法

方法与[函数](functions.md)类似，我们使用 `fn` 关键字声明它们，它们有参数并返回一个值。但是，与函数不同，_方法_ 是在结构体（或枚举）的上下文中定义的，并且要么引用该类型，要么对其进行修改。方法的第一个参数始终是 `self`，它表示调用该方法的结构体（或枚举）的实例。

## 关联函数

_关联函数_ 与 _方法_ 非常相似，因为它们也在结构体或枚举的上下文中定义，但它们实际上不使用结构体中的任何数据，因此不会将 _self_ 作为参数。关联函数可以是独立的函数，但它们包含在特定类型中是出于组织或语义上的原因。

### 构造函数

构造函数是构造新实例的关联函数，或者换句话说是实例化。它们的返回类型始终是类型本身。例如，具有私有字段的公共结构体必须提供公共构造函数，否则它们不能在声明它们的模块之外被实例化。

## 声明方法和关联函数

要为结构体或枚举声明方法和关联函数，请使用 `impl` 块。在这里，`impl` 是 implementation 的缩写。

<!-- methods_af:example:end -->

```sway
{{#include ../../../../examples/methods_and_associated_functions/src/main.sw}}
```

<!-- This section should explain how to call a method -->
<!-- call_method:example:start -->

要调用方法，只需使用点语法：`foo.iz_baz_true()`。

<!-- call_method:example:end -->

<!-- This section should explain how methods + assoc. fns can accept `ref mut` params -->
<!-- ref_mut:example:start -->

与[自由函数](functions.md)类似，方法和关联函数可能会接受 `ref mut` 参数。

<!-- ref_mut:example:end -->

例如：

```sway
{{#include ../../../../examples/ref_mut_params/src/main.sw:move_right}}
```

在调用时：

```sway
{{#include ../../../../examples/ref_mut_params/src/main.sw:call_move_right}}
```
