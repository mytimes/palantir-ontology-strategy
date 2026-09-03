# 仅编辑属性（Edit-only properties）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/edit-only-properties/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

仅编辑属性允许你定义不直接映射到对象类型支撑数据集中某一列的本体属性。
这对于你可能希望在不修改底层数据集的情况下，为对象类型存储额外信息的场景特别有用。

## 仅编辑属性摘要

使用仅编辑属性时，请记住以下几点：

- **不映射到支撑数据集中的列：** 仅编辑属性不需要映射到支撑数据集中的特定列。这使你可以在支撑列存在之前轻松创建新属性，或创建仅通过本体编辑的属性。
- **对支撑该对象类型的其中一个数据集授权：** 为确保数据一致性和安全性，仅编辑属性必须对支撑该对象类型的其中一个数据集授权。
- **仅在 Object Storage v2 中可用：** 仅编辑属性是专门为使用 Object Storage v2 的对象类型提供的功能。

### 创建仅编辑属性

1. 导航到 **Ontology Manager**。
2. 选择要添加仅编辑属性的对象类型。
3. 选择 **Create Property**（创建属性）并填写所需的详细信息，包括属性名称、类型和描述。
4. 在 **Data**（数据）部分，开启 **Edit-only property**（仅编辑属性）开关，并选择一个要授权的数据集（如果该对象类型有多个支撑数据集）。
5. **Save（保存）** 你的更改以创建仅编辑属性。

![仅编辑属性](https://www.palantir.com/docs/resources/foundry/object-link-types/edit_only_property.png?width=300)

### 将仅编辑属性映射到数据集列

如果你之后决定在支撑数据集中添加一个与当前某个仅编辑属性对应的列，你可以轻松地将该属性映射到新列。

1. 导航到 **Ontology Manager**。
2. 选择具有你要映射的仅编辑属性的对象类型。
3. 选择该仅编辑属性以打开其详细信息。
4. 在 **Data** 部分，关闭仅编辑属性开关，并从其中一个支撑数据集选择一列。
5. **保存**你的更改。

---

*原文：[Edit-only properties](https://www.palantir.com/docs/foundry/object-link-types/edit-only-properties/)*
