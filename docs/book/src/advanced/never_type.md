# Never 类型

Never 类型 `!` 表示永远不会解析为任何值的计算类型。

## 附加信息

`break`、`continue` 和 `return` 表达式也具有类型 `!`。例如，我们可以编写：

```sway
let x: ! = {
return 123
};
```

虽然这里的 `let` 是无意义的，但它说明了 `!` 的含义。由于 `x` 永远不会被赋值（因为 `return` 从整个函数返回），因此可以给 `x` 赋予类型 Never 类型。我们也可以用 `revert()` 或永不结束的 `loop` 替换 `return 123`，这段代码仍然有效。

Never 类型的更实际的用法如下：

```sway
let num: u32 = match get_a_number() {
Some(num) => num,
None => break,
};
```

匹配分支必须产生 [`u32`] 类型的值，但由于 `break` 永远不会产生任何值，我们知道它永远不会产生不是 [`u32`] 类型的值。这说明了 Never 类型 的另一种行为 - 类型为 Never 类型 的表达式将强制转换为任何其他类型。

注意， Never 类型 可以强制转换为任何其他类型，另一个例子是：

```sway
let x: u32 = {
return 123
};
```

无论 `x` 的类型如何，类型为 Never 类型 的返回块将始终强制转换为 `x` 类型。

## 示例

```sway
fn foo() {
let num: u64 = match Option::None::<u64> {
Some(num) => num,
None => return,
};
}
```
