# 关键字

以下列表包含 Sway 语言当前或将来保留用于当前或将来使用的关键字。因此，它们不能用作标识符。标识符是函数、变量、参数、模块、常量、属性、类型或特征等的名称。

## 当前正在使用的关键字

以下是当前正在使用的关键字列表，附有其功能描述。

- `as` - 重命名 `use` 语句中的项目，例如，`use type::a as alias_name`
- [`abi`](../sway-program-types/smart_contracts.md#the-abi-declaration) - 以与特征类似的语法定义智能合约 ABI
- [`break`](../basics/control_flow.md#break-and-continue) - 立即退出循环
- [`const`](../basics/constants.md) - 定义常量项
- [`continue`](../basics/control_flow.md#break-and-continue) - 继续下一个循环迭代
- `else` - 与 `if` 条件结构一起使用的控制流构造
- [`enum`](../basics/structs_tuples_and_enums.md#enums) - 定义枚举
- `false` - 布尔假文字
- [`fn`](../basics/functions.md)- 定义函数或函数指针类型
- [`if`](../basics/control_flow.md#if-expressions) - 根据条件表达式的结果进行分支
- `impl` - 实现固有或特征功能
- `let` - 绑定变量
- [`match`](../basics/control_flow.md#match-expressions) - 对值进行模式匹配
- `mod` - 定义一个模块
- `mut` - 在引用或模式绑定中表示可变性
- `pub` - 表示 Sway 数据结构、特征或模块的公共可见性
- `ref` - 按引用绑定
- `return` - 从函数中提前返回
- `Self` - 我们正在定义或实现的类型的类型别名
- `self` - 方法主体
- [`struct`](../basics/structs_tuples_and_enums.md#structs) - 定义一个结构
- [`trait`](../advanced/traits.md#declaring-a-trait) - 定义一个特征
- `true` - 布尔真文字
- [`type`](../advanced/advanced_types.md#creating-type-synonyms-with-type-aliases) - 定义类型别名或关联类型
- `use` - 将符号引入作用域
- `where` - 为泛型类型指定特征
- [`while`](../basics/control_flow.md#while) - 根据表达式的结果进行条件循环

## 保留以供将来使用的关键字

- `abstract`
- `async`
- `await`
- `become`
- `box`
- `do`
- `dyn`
- `extern`
- `for`
- `in`
- `loop`
- `macro`
- `move`
- `override`
- `priv`
- `static`
- `super`
- `try`
- `typeof`
- `unsafe`
- `unsized`
- `virtual`
- `yield`

## 特殊关键字

### 程序关键字

与定义要编译的 Sway 程序类型相关的关键字

- [`contract`](../sway-program-types/smart_contracts.md) - 类似于具有某些数据库状态的已部署 API
- [`library`](../sway-program-types/libraries.md) - 定义新的常见行为的 Sway 代码
- [`predicate`](../sway-program-types/predicates.md) - 返回布尔值的程序，执行时代表对某些资源的所有权为 true
- [`script`](../sway-program-types/scripts.md) - 在链上可运行的字节码，执行一次以执行任务

### 属性关键字

与定义属性功能相关的关键字

- [`allow`](./attributes.md#allow) - 覆盖否则会导致错误或警告的检查
- [`doc`](./attributes.md#doc) - 指定文档
- [`inline`](./attributes.md#inline) - 建议将标记的函数的副本放置在调用者中，而不是在定义函数的地方生成调用函数的代码
- [`payable`](./attributes.md#payable) - 暗示方法是可付款的，用于编译时
- [`storage`](./attributes.md#storage) - 包含一组存储变量的声明
- [`test`](./attributes.md#test) - 将一个函数标记为要执行的测试
- [`deprecated`](./attributes.md#deprecated) - 将一个项标记为已弃用
