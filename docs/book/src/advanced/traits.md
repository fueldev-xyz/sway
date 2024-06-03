# 特性(traits)

## 声明特性

_特性_ 选择类型以实现某种类型的行为或功能，这些功能可以在类型之间共享。这允许轻松重用代码和泛型编程。如果您曾经在 Haskell 中使用过类型类、在 Rust 中使用过特性，甚至在 Java 中使用过接口，那么这些都是类似的概念。

让我们看一些代码：

```sway
trait Compare {
fn equals(self, b: Self) -> bool;
} {
fn not_equals(self, b: Self) -> bool {
!self.equals(b)
}
}
```

我们刚刚声明了一个名为 `Compare` 的特性。在特性的名称之后，有两个代码块（一个 _代码块_ 是被 `{` 大括号 `}` 包围的代码）。第一个代码块是 _接口表面_。第二个代码块是特性提供的 _方法_。如果一个类型可以提供接口表面中的方法，那么它就可以免费使用特性中的方法！上面的特性所表达的是：如果您可以确定两个值是否相等，那么您就可以免费确定它们不相等。请注意，特性方法可以访问接口表面中定义的方法。

## 实现特性

下面的示例为 `u64` 实现了一个 `Compare` 特性，以检查两个数字是否相等。让我们看看是如何实现的：

```sway
impl Compare for u64 {
fn equals(self, b: Self) -> bool {
self == b
}
}
```

上面的代码片段为类型 `u64` 声明了特性 `Compare` 中的所有方法。现在，只要特性 `Compare` 在作用域中，我们就可以访问 `u64` 的 `equals` 和 `not_equals` 方法。

## 超特性

在使用多个特性时，通常会出现一种情况，其中一个特性可能需要另一个特性的功能。这就是超特性的用武之地，因为它们允许您在实现另一个特性时要求另一个特性，即，一个特性与另一个特性的特性。

一个很好的例子是 Sway 的 `core` 库的 `Ord` 特性。`Ord` 特性需要 `Eq` 特性，因此 `Eq` 被保留为一个单独的特性，因为您可能决定实现 `Eq` 而不实现 `Ord` 特性的其他部分。

```sway

trait Eq {
fn equals(self, b: Self) -> bool;
}

trait Ord: Eq {
fn gte(self, b: Self) -> bool;
}

impl Ord for u64 {
fn gte(self, b: Self) -> bool {
// 由于 `Eq` 是 `Ord` 的超特性，`Ord` 可以访问 equals 方法
self.equals(b) || self.gt(b)
}
}
```

要求超特性时，在特性名称后面添加 `:`，然后列出您想要的特性，并用 `+` 分隔它们。

### ABI 超特性

ABIs 也可以具有超特性注释：

```sway
{{#include ../../../../examples/abi_supertraits/src/main.sw}}
```

为 `Contract` 实现 `MyAbi` 的同时，还必须实现 `ABIsupertrait` 超特性。`ABIsupertrait` 中的方法在外部不可用，即它们实际上不是合约方法，但可以在实际的合约方法中使用，如上面的示例所示。

ABI 超特性旨在使合约实现可组合，允许使用库等组合正交合约特性。

### 超 ABIs

除了超特性外，ABIs 还可以具有 _超 ABI_ 注释：

```sway
{{#include ../../../../examples/abi_superabis/src/main.sw}}
```

为 `Contract` 实现 `MyAbi` 的同时，还必须实现 `MySuperAbi` 超 ABI。`MySuperAbi` 中的方法将成为 `MyAbi` 合约接口的一部分，即将在外部可用（因此不能从其他 `MyAbi` 合约方法中调用）。

超 ABI 旨在使合约实现可组合，允许使用库等组合正交合约特性。

## 关联项

特性可以在其接口表面中声明不同类型的关联项：

- [函数](#相关函数)
- [常量](#相关常量)
- [类型](#相关类型)

### 相关函数

特性中的关联函数仅包含函数签名。这表示给定类型的特性的每个实现都必须定义所有特性函数。

```sway
trait Trait {
fn associated_fn(self, b: Self) -> bool;
}
```

### 相关常量

相关常量是与类型关联的常量。

```sway
trait Trait {
const ID: u32 = 0;
}
```

在特性定义中，相关常量的初始化表达式可以被省略，以表示给定类型的 `trait` 的每个实现必须指定一个初始化器：

```sway
trait Trait {
const ID: u32;
}
```

请查看 [相关常量](../basics/constants.md#associated-constants) 页面上的 `associated consts` 部分。

### 关联类型

Sway 中的关联类型允许您在特性中定义占位符类型，这些类型可以由该特性的具体实现自定义。这些关联类型用于指定特性方法的返回类型或在特性中定义类型关系。

```sway
trait MyTrait {
type AssociatedType;
}
```

请查看 [相关类型](./associated_types.md) 页面上的 `associated types` 部分。

## 应用案例

### 自定义类型（结构体、枚举）

通常，库和 API 具有在实现某个特定特性的类型上抽象出的接口。由接口的消费者来为他们希望在接口中使用的类型实现该特性。例如，让我们来看一个基于特性构建的特性和接口。

```sway
library;

pub enum Suit {
Hearts: (),
Diamonds: (),
Clubs: (),
Spades: (),
}

pub trait Card {
fn suit(self) -> Suit;
fn value(self) -> u8;
}

fn play_game_with_deck<T>(a: Vec<T>) where T: Card {
// 在这里插入一些创意扑克游戏
}
```

> **注意** 特性约束（即使用 `where` 关键字）尚未实现。

现在，如果您想要使用您的结构体调用函数 `play_game_with_deck`，则必须为您的结构体实现 `Card`。请注意，以下代码示例假定 `Forc.toml` 文件中已经包含了名为 _games_ 的依赖项。

```sway
script;

use games::*;

struct MyCard {
    suit: Suit,
    value: u8
}

impl Card for MyCard {
    fn suit(self) -> Suit {
        self.suit
    }
    fn value(self) -> u8 {
        self.value
    }
}

fn main() {
    let mut i = 52;
    let mut deck: Vec<MyCard> = Vec::with_capacity(50);
    while i > 0 {
        i = i - 1;
        deck.push(MyCard { suit: generate_random_suit(), value: i % 4}
    }
    play_game_with_deck(deck);
}

fn generate_random_suit() -> Suit {
  [ ... ]
}
```
