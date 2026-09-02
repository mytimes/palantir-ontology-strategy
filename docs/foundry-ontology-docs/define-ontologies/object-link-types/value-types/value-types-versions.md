# 值类型版本（Value type versions）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/value-types-versions/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

值类型通过版本管理来处理破坏性和非破坏性编辑。值类型版本包含两部分：元数据和约束。名称、描述和 apiName 的元数据值可以在必要时更改。基础类型元数据和定义该类型验证规则的约束是不可变的。

如果你选择更新值类型的约束，将创建该值类型的新版本。如果你的值类型没有使用者，你可以自由更改这些约束。但是，如果你对约束进行了破坏性更改且你的值类型有使用者，我们建议弃用当前值类型并改为创建一个新值类型。这种方法可以避免潜在的运行时错误和数据不一致。

![约束更新警告](https://www.palantir.com/docs/resources/foundry/object-link-types/value-type-versioning.png?width=500)

当你对值类型进行非破坏性更改时，也会创建一个新版本。新版本将自动传播到本体，确保整个本体中该值类型的所有使用都更新到最新版本。

---

*原文：[Value type versions](https://www.palantir.com/docs/foundry/object-link-types/value-types-versions/)*
