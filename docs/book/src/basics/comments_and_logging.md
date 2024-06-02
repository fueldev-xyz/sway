# 注释和日志

## 注释

<!-- This section should explain how to add comments in Sway -->
<!-- comments:example:start -->

在 Sway 中，注释以两个斜杠开头，并持续到行末。对于跨越多行的注释，需要在每一行包含`//`。

<!-- comments:example:end -->

```sway
// hello world
```

```sway
// let's make a couple of lines
// commented.
```

你也可以将注释放在包含代码的行末。

```sway
fn main() {
let baz = 8; // Eight is a lucky number
}
```

你还可以使用块注释

```sway
fn main() {
/_
You can write on multiple lines
like this if you want
_/
let baz = 8;
}
```

## Logging

<!-- This section should explain logging in Sway -->
<!-- logging:example:start -->

`logging`库提供了一个通用的`log`函数，可以使用`use std::logging::log`导入，并用于记录任何类型的变量。每次调用`log`都会向收据列表追加一个`receipt`。`log`可以生成两种类型的收据：`Log`和`LogData`。

<!-- logging:example:end -->

```sway
fn log_values(){
// 生成一个 Log 收据
log(42);

// 生成一个 LogData 收据
let string = "sway";
log(string);
}
```

### `Log` 收据

<!-- This section should explain when `Log` receipts are produced -->
<!-- log_rec:example:start -->

`Log`收据用于生成*非引用*类型，即`bool`、`u8`、`u16`、`u32`和`u64`。

<!-- log_rec:example:end -->

例如，使用`log(x)`记录一个整数变量`x`，其值为`42`，可能会生成以下收据：

```console
"Log": {
"id": "0000000000000000000000000000000000000000000000000000000000000000",
"is": 10352,
"pc": 10404,
"ra": 42,
"rb": 1018205,
"rc": 0,
"rd": 0
}
```

注意，当使用`log`时，`ra`将包含被记录的值。附加寄存器`rc`和`rd`在使用`log`时将为零，而`rb`可能包含一个非零值，代表`log`实例的唯一 ID。这个唯一 ID 本身并没有意义，但允许 Rust 和 TS SDK 通过在 JSON ABI 文件中查找 log ID 来知道被记录数据的类型。

### `LogData` 收据

<!-- This section should explain when `LogData` receipts are produced -->
<!-- log_data_rec:example:start -->

对于*引用*类型，包括除*非引用*类型以外的所有类型；以及大于 64 位整数的*非引用*类型，例如`u256`，会生成`LogData`。

<!-- log_data_rec:example:end -->

例如，使用`log(b)`记录一个`b256`变量`b`，其值为`0x1111111111111111111111111111111111111111111111111111111111111111`，可能会生成以下收据：

```console
"LogData": {
"data": "1111111111111111111111111111111111111111111111111111111111111111",
"digest": "02d449a31fbb267c8f352e9968a79e3e5fc95c1bbeaa502fd6454ebde5a4bedc",
"id": "0000000000000000000000000000000000000000000000000000000000000000",
"is": 10352,
"len": 32,
"pc": 10444,
"ptr": 10468,
"ra": 0,
"rb": 1018194
}
```

请注意，上面收据中的`data`将以十六进制形式包含被记录的值。与`Log`收据类似，附加寄存器会被写入：当使用`log`时，`ra`始终为零，而`rb`将包含`log`实例的唯一 ID。

> **注意**
> Rust SDK 提供了[API](https://fuellabs.github.io/fuels-rs/master/calling-contracts/logs.html#logs)，允许您检索已记录的值并根据 JSON ABI 文件中指示的类型将其显示为漂亮的形式。
