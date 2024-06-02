# 泛型类型

## 基础知识

在 Sway 中，泛型类型遵循与 Rust 中相似的模式。让我们从一个泛型函数的示例语法开始：

```sway
fn noop<T>(argument: T) -> T {
argument
}
```

在这里，`noop()` 函数以与之传入相同的方式平凡地返回值。`T` 是一个 _类型参数_，它表示这个函数适用于所有类型 `T`。更正式地说，这个函数可以被类型为：

```math
noop :: ∀T. T -> T
```

泛型类型是一种引用 _一般_ 类型的方式，这意味着没有指定单个类型。我们的 `noop` 函数可以适用于语言中的任何类型，因此我们不需要指定 `noop(argument: u8) -> u8`、`noop(argument: u16) -> u16` 等。

## 代码生成

在处理泛型类型时，一个问题是：汇编如何处理这个问题？在最底层处理泛型类型时有几种方法。Sway 使用一种称为 [单态化](https://en.wikipedia.org/wiki/Monomorphization) 的技术。这意味着泛型函数将编译为每种类型都被调用的非泛型版本。通过这种方式，泛型函数纯粹是为了符号上的方便。

## Trait 约束

在深入讨论特征约束之前，重要的背景知识是 `where` 子句可用于指定泛型参数所需的特征。因此，当编写像 `HashMap` 这样的东西时，您可能想要指定泛型参数实现了 `Hash` 特征。

```sway
fn get_hashmap_key<T>(key: T) -> b256
where T: Hash
{
// 此处的代码可以调用与 Hash 特征关联的方法
}
```

当然，我们的 `noop()` 函数并不实用。通常，程序员会想要声明符合某些特征的类型的函数。例如，让我们尝试为所有数字类型实现后继函数 `successor()`。

```sway
fn successor<T>(argument: T)
where T: Add
{
argument + 1
}
```

运行 `forc build`，您将得到：

```console
.. |
9 | where T: Add
10 | {
11 | argument + 1
 | ^ 类型不匹配：期望类型 "T"，但看到类型 "u64"
12 | }
13 |
```

这是因为我们无法确定 `1`，在这种情况下默认为 `1u64`，实际上是否可以添加到 `T` 上。如果 `T` 是 `f64`？或者 `b256`？在这些情况下，将 `1u64` 添加到 `T` 是什么意思？

我们可以通过另一个特征约束解决这个问题。只有在类型 `T` 定义了某种增量器时，我们才能找到类型 `T` 的一些值的后继函数。让我们创建一个特征：

```sway
trait Incrementable {
/// 返回计算值的后继时要添加的值。
fn incrementor() -> Self;
}
```

现在，我们可以修改我们的 `successor()` 函数：

```sway
fn successor<T>(argument: T)
where T: Add,
T: Incrementable
{
argument + T::incrementor()
}
```

## 泛型结构体和枚举

与函数类似，结构体和枚举也可以是泛型的。让我们看看标准库版本的 `Option<T>`：

```sway
enum Option<T> {
Some: T,
None: (),
}
```

就像一个无约束的泛型函数一样，这个类型对于所有 (∀) 类型 `T` 都是存在的。`Result<T, E>` 是另一个例子：

```sway
enum Result<T, E> {
Ok: T,
Err: E,
}
```

泛型枚举和泛型结构体都可以是特征约束的。考虑这个结构体：

```sway
struct Foo<T>
where T: Add
{
field_one: T,
}
```

## 类型参数

与 Rust 类似，Sway 中有一个俗称为 [turbofish](https://github.com/rust-lang/rust/blob/e98309298d927307c5184f4869604bd068d26183/src/test/ui/parser/bastion-of-the-turbofish.rs) 的概念。turbofish 看起来像这样：`::<>`（看到后面带着气泡的小鱼了吗？）。turbofish 用于在泛型上下文中注释类型。假设你有以下函数：

```sway
fn foo<T, E>(t: T) -> Result<T, E> {
Ok(t)
}
```

在这个示例代码中，你不可能知道类型 `E` 是什么。你需要手动提供类型，使用 turbofish：

```sway
fn foo<T, E>(t: T) -> Result<T, E> {
Ok::<T, MyErrorType>(t)
}
```

通常还会在函数本身上看到 turbofish 的使用：

```sway
fn main() {
foo::<Bar, Baz>()
}
```
