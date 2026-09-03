# 值类型约束（Value type constraints）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/value-type-constraints/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

每个值类型可以选择定义一个约束来强制执行数据验证。你可以在 **Value Type Manager**（值类型管理器）应用中[创建新值类型](https://www.palantir.com/docs/foundry/object-link-types/create-value-type/)时配置这些约束。可用的值类型约束以及它们可以应用于哪些基础类型如下：

- **枚举（Enum，单选其一）：** 表示一组静态允许值的约束。
  - **有效基础类型：** String、Boolean、Decimal、Double、Float、Integer 或 Short。
  - 对于 String 属性，枚举值可以选择区分大小写或不区分大小写。
- **范围（Range）：** 最小值、最大值或允许值的范围。
  - **有效基础类型：** Decimal、Double、Float、Integer、Short、Date、Timestamp、String 或 Array。
  - 对于 String 属性，约束的是字符串的长度。
  - 对于 Array 属性，约束的是数组的大小。

此外，以下属性类型还有额外的特定于类型的约束可用：

- **String：**
  - **正则表达式（Regex）：** 字符串必须匹配的正则表达式模式。正则表达式验证可以选择在仅匹配属性值的子串时即通过。
  - **RID：** 字符串必须是有效的 RID。
  - **UUID：** 字符串必须是有效的 UUID。
- **Array：**
  - **唯一性（Uniqueness）：** 数组的所有元素必须唯一。
  - **嵌套（Nested）：** 值类型约束可以应用于数组的元素。例如，可以对数组中的每个字符串应用正则表达式约束。
- **Struct：**
  - **元素约束（Element constraints）：** 结构体字段标识符与值类型引用之间的映射，其中结构体字段标识符指示所引用的值类型应应用于哪个结构体组件。

---

*原文：[Value type constraints](https://www.palantir.com/docs/foundry/object-link-types/value-type-constraints/)*
