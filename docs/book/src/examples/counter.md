# 计数器

以下是一个简单的合约示例，实现了一个计数器。`initialize_counter()` 和 `increment_counter()` ABI 方法都返回当前设置的值。

```bash
forc template --template-name counter my_counter_project
```

```sway
{{#include ../../../../examples/counter/src/main.sw}}
```
