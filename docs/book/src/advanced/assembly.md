# 在 Sway 中使用内联汇编

虽然许多用户在编写 Sway 代码时永远不需要接触汇编语言，但它是一种强大的工具，可以实现许多高级用例（例如，优化、构建库等）。

## ASM 块

在 Sway 中，我们使用内联汇编的方式是声明一个 `asm` 块，就像这样：

```sway
asm() {...}
```

声明一个 `asm` 块类似于声明一个函数。
我们可以将要操作的寄存器名称作为参数传递进去，在块内执行操作，并返回一个值。
以下是一个示例，展示了这个过程可能是什么样子的：

```sway
pub fn add_1(num: u32) -> u32 {
asm(r1: num, r2) {
add r2 r1 one;
r2: u32
}
}
```

一个 `asm` 块只能返回一个寄存器。如果您确实需要返回多个值，可以修改一个元组。以下是一个示例，展示了如何实现 `(u64, u64)`：

```sway
{{#include ../../../../examples/asm_return_tuple_pointer/src/main.sw}}
```

请注意，这只是一个编造的示例，旨在演示语法；绝对没有必要使用汇编来添加整数！

请注意，在上面的示例中：

- 我们使用 `num` 的值初始化了寄存器 `r1`。
- 我们声明了第二个寄存器 `r2`（您可以选择任何寄存器名称）。
- 我们使用 `add` 操作码将 `one` 加到 `r1` 的值上，并将结果存储在 `r2` 中。
- `one` 是一个 "保留寄存器" 的示例，总共有 16 个。有关更多信息，请参阅下面的 "语义" 链接。
- 我们返回 `r2`，并将返回类型指定为 u32（返回类型默认为 u64）。

一个重要的注意事项是 `asm` 块中不可用 `ji` 和 `jnei` 操作码。对于希望在 `asm` 块中引入控制流的人士，建议用控制流（`if`、`else` 和 `while`）来包围较小的 `asm` 块。

## 有用的链接

要查看汇编实际应用的示例，请查看[Sway 标准库](https://github.com/FuelLabs/sway/tree/master/sway-lib-std)。

要查看 FuelVM 支持的所有指令的完整列表：[Instructions](https://fuellabs.github.io/fuel-specs/master/vm/instruction_set)。

要了解更多关于 FuelVM 语义的信息：[Semantics](https://fuellabs.github.io/fuel-specs/master/vm#semantics)。
