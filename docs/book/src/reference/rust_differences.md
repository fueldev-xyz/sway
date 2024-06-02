# 与 Rust 的不同之处

Sway 与 Rust 有很多相似之处，尤其是其语法。因为它们如此相似，当它们有所不同时，您可能会感到惊讶或措手不及。本页面旨在从高层次上概述您可能会遇到的一些语法“坑”。

## 枚举变体语法

在 Rust 中，枚举通常采用三种形式之一：_unit_ 变体，没有内部数据，_struct_ 变体，包含命名字段，以及 _tuple_ 变体，其中包含一个元组数据。如果您对这些术语不熟悉，这是它们的样子：

```rust,ignore
// note to those skimming the docs: this is Rust syntax! Not Sway! Don't copy/paste this into a Sway program.

enum Foo {
    UnitVariant,
    TupleVariant(u32, u64, bool),
    StructVariant {
        field_one: bool,
        field_two: bool
    }
}
```

在 Sway 中，枚举被简化了。枚举变体必须全部指定精确的一个类型。这个类型表示它们的内部数据。实际上，这与 Rust 提供的内容是同构的，但语法不同。您可以在下面看到具有 Sway 语法的上述枚举：

```sway
// This is equivalent Sway syntax for the above Rust enum.
enum Foo {
    UnitVariant: (),
    TupleVariant: (u32, u64, bool),
    StructVariant: MyStruct,
}

struct MyStruct {
    field_one: bool,
    field_two: bool,
}
```
