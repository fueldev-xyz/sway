# 控制流

## `if` 表达式

Sway 支持 _if_、_else_ 和 _else if_ 表达式，可以根据条件分支代码。

例如：

```sway
fn main() {
let number = 6;

    if number % 4 == 0 {
        // do something
    } else if number % 3 == 0 {
        // do something else
    } else {
        // do something else
    }

}
```

### 在 `let` 语句中使用 `if`

与 Rust 一样，`if` 在 Sway 中也是表达式。这意味着您可以在 `let` 语句的右侧使用 `if` 表达式，将结果分配给一个变量。

```sway
let my_data = if some_bool < 10 { foo() } else { bar() };
```

请注意，`if` 表达式的所有分支必须返回相同类型的值。

### `match` 表达式

Sway 通过详尽的 `match` 表达式支持高级模式匹配。与 `if` 表达式不同，`match` 表达式在**编译时**断言所有可能的模式都已匹配。如果您没有处理所有模式，您将收到编译器错误，指示您的 `match` 表达式不完整。

`match` 表达式的基本语法如下：

```sway
let result = match expression {
pattern1 => code\*to_execute_if_expression_matches_pattern1,
pattern2 => code_to_execute_if_expression_matches_pattern2,
pattern3 | pattern4 => code_to_execute_if_expression_matches_pattern3_or_pattern4
...

- => code_to_execute_if_expression_matches_no_pattern,
  }
```

以下是如何使用 `match` 表达式的一些示例：

```sway
{{#include ../../../../examples/match_expressions/src/main.sw}}
```

## 循环

### `while`

这是一个 `while` 循环的示例：

```sway
while counter < 10 {
counter = counter + 1;
}
```

您需要 `while` 关键字，一些条件（在本例中为 `value < 10`），它将在每次迭代中进行评估，并且在大括号内（`{...}`）有一段代码，用于执行每次迭代。

### `for`

这是一个计算数字向量之和的 `for` 循环的示例：

```sway
for element in vector.iter() {
sum += element;
}
```

您需要 `for` 关键字，包含变量名的某些模式，例如本例中的 `element`，`ìn` 关键字，后跟一个迭代器，并且在大括号内（`{...}`）有一段代码，用于执行每次迭代。上面示例中的 `vector.iter()` 返回 `vector` 的迭代器。在每次迭代中，`element` 的值将被更新为迭代器中的下一个值，直到达到向量的末尾，并且 `for` 循环迭代结束。

### `break` 和 `continue`

在 `while` 或 `for` 循环的主体内可以使用 `break` 和 `continue` 关键字。`break` 语句的目的是提前跳出循环：

```sway
{{#include ../../../../examples/break_and_continue/src/main.sw:break_example}}
```

`continue` 语句的目的是在迭代中跳过循环的一部分，并直接进入下一次迭代：

```sway
{{#include ../../../../examples/break_and_continue/src/main.sw:continue_example}}
```

### 嵌套循环

如果需要，您也可以使用嵌套的 `while` 循环：

```sway
while condition_1 == true {
// do stuff...
while condition_2 == true {
// do more stuff...
}
}
```
