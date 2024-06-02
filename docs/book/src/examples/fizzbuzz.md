# `FizzBuzz`

这个示例不是传统的 [`FizzBuzz`](https://en.wikipedia.org/wiki/Fizz_buzz#Programming)；相反，它是智能合约版本！一个脚本可以调用这个合约的 `fizzbuzz` ABI 方法，传入一个 `u64` 值，并以 `enum` 的形式接收结果。

自定义结构体和枚举类型的格式，比如 `FizzBuzzResult`，会自动包含在 ABI JSON 中，以便链下代码可以处理返回数据的编码形式。

```sway
{{#include ../../../../examples/fizzbuzz/src/main.sw}}
```
