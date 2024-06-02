# 存储向量

我们将要介绍的第二种集合类型是 `StorageVec<T>`。与堆上的向量（即 `Vec<T>`）类似，存储向量允许您将多个值存储在单个数据结构中，其中每个值被分配一个索引，并且只能存储相同类型的值。然而，与 `Vec<T>` 不同，`StorageVec` 的元素存储在 _持久存储_ 中，并且连续的元素不一定存储在具有连续键的存储槽中。

要使用 `StorageVec<T>`，必须首先导入 `StorageVec`，如下所示：

```sway
{{#include ../../../../examples/storage_vec/src/main.sw:storage_vec_import}}
```

`Vec<T>` 和 `StorageVec<T>` 之间的另一个主要区别是 `StorageVec<T>` 只能在合约中使用，因为只有合约允许访问持久存储。

## 创建新的 `StorageVec`

要创建一个新的空 `StorageVec`，我们必须在 `storage` 块中声明向量，如下所示：

```sway
{{#include ../../../../examples/storage_vec/src/main.sw:storage_vec_decl}}
```

与任何其他存储变量一样，声明 `StorageVec` 时需要两样东西：类型注释和初始化器。初始化器只是类型为 `StorageVec` 的空结构体，因为 `StorageVec<T>` 本身就是一个空结构体！`StorageVec<T>` 的所有有趣的内容都在其方法中实现。

存储向量，就像 `Vec<T>` 一样，是使用泛型实现的，这意味着标准库提供的 `StorageVec<T>` 类型可以容纳任何类型。当我们创建一个用于保存特定类型的 `StorageVec` 时，我们可以在尖括号内指定类型。在上面的示例中，我们告诉 Sway 编译器 `v` 中的 `StorageVec<T>` 将保存 `u64` 类型的元素。

## 更新 `StorageVec`

要向 `StorageVec` 添加元素，我们可以使用 `push` 方法，如下所示：

```sway
{{#include ../../../../examples/storage_vec/src/main.sw:storage_vec_push}}
```

请注意两个细节。首先，为了使用 `push`，我们需要首先使用 `storage` 关键字访问向量。其次，因为 `push` 需要访问存储，所以调用 `push` 的 ABI 函数需要添加 `storage` 注解。虽然 `#[storage(write)]` 看起来应该足够了，但是还需要 `read` 注解，因为每次调用 `push` 都需要 _读取_（然后更新）`StorageVec` 的长度，该长度也存储在持久存储中。

> **注意**
> 对于尝试向向量中添加元素的任何私有函数，都需要在合约中添加存储注解。

<!-- markdownlint-disable-line MD028 -->

> **注意**
> 声明 `StorageVec<T>` 时无需添加 `mut` 关键字。所有存储变量默认都是可变的。

## 读取存储向量的元素

要读取向量中特定索引处存储的值，您可以使用 `get` 方法，如下所示：

```sway
{{#include ../../../../examples/storage_vec/src/main.sw:storage_vec_get}}
```

请注意三个细节。首先，我们使用索引值 `2` 来获取第三个元素，因为向量是通过数字索引的，从零开始。其次，我们使用带有索引的 `get` 方法来获取第三个元素，该索引作为参数传递给 `get`，这会给我们一个 `Option<StorageKey<T>>`。第三，调用 `get` 的 ABI 函数只需要注解 `#[storage(read)]`，因为 `get` 不会写入存储。

当 `get` 方法传递一个超出向量范围的索引时，它会返回 `None` 而不会出现紧急情况。这在正常情况下偶尔会发生访问超出向量范围的元素时特别有用。您的代码将具有逻辑来处理 `Some(element)` 或 `None`。例如，索引可能作为合约方法参数传递。如果传递的参数过大，方法 `get` 将返回一个 `None` 值，合约方法随后可以决定在发生这种情况时回滚，或者返回一个有意义的错误，告诉用户当前向量中有多少项，并为其提供另一次传递有效值的机会。

## 遍历向量中的值

要依次访问向量中的每个元素，我们将使用 `while` 循环和 `len` 方法遍历所有有效的索引，如下所示：

```sway
{{#include ../../../../examples/storage_vec/src/main.sw:storage_vec_iterate}}
```

再次强调，这与遍历 `Vec<T>` 元素的方法非常相似，其中我们使用 `len` 方法返回向量的长度。我们还调用 `unwrap` 方法来提取 `get` 返回的 `Option`，然后调用 `read()` 实际读取存储的值。我们知道 `unwrap` 不会失败（即不会导致回滚），因为传递给 `get` 的每个索引 `i` 都已知小于向量的长度。

## 使用枚举存储多种类型

存储向量，就像 `Vec<T>` 一样，只能存储相同类型的值。与我们在 [使用枚举存储多种类型](./vec.md#using-an-enum-to-store-multiple-types) 部分为 `Vec<T>` 所做的类似，我们可以定义一个枚举，其变体将保存不同的值类型，并且所有枚举变体都将被视为相同的类型：即枚举类型。示例如下：

```sway
{{#include ../../../../examples/storage_vec/src/main.sw:storage_vec_multiple_types_enum}}
```

然后，我们可以在 `storage` 块中声明一个 `StorageVec` 来保存该枚举，因此，最终会保存不同的类型：

```sway
{{#include ../../../../examples/storage_vec/src/main.sw:storage_vec_multiple_types_decl}}
```

现在，我们可以将不同的枚举变体推送到 `StorageVec`，如下所示：

```sway
{{#include ../../../../examples/storage_vec/src/main.sw:storage_vec_multiple_types_fn}}
```

现在我们已经讨论了一些使用存储向量的常见方法，请务必查看标准库中定义的 `StorageVec<T>` 的所有许多有用方法的 API 文档。目前，这些可以在 [source code for `StorageVec<T>`](https://github.com/FuelLabs/sway/blob/master/sway-lib-std/src/storage.sw) 中找到。例如，除了 `push` 之外，`pop` 方法会移除并返回最后一个元素，`remove` 方法会移除并返回向量中某个选择的索引处的元素，`insert` 方法会在向量中的某个选择的索引处插入元素等。

## 嵌套存储向量

可以像这样嵌套存储向量：

```sway
{{#include ../../../../examples/storage_vec/src/main.sw:storage_vec_nested}}
```

然后可以这样访问嵌套向量：

```sway
{{#include ../../../../examples/storage_vec/src/main.sw:access_nested_vec}}
```
