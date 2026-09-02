# 编辑结构体属性类型（Edit a struct property type）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/edit-struct-type/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

1. 在 Ontology Manager 中，打开左侧边栏中的 **Object types**（对象类型）选项卡，并选择一个现有对象类型。
2. 在对象类型详情页面，打开左侧边栏中的 **Properties**（属性）选项卡，并从 **Properties** 表格中选择相关的结构体属性类型。
3. 在 **Property editor**（属性编辑器）面板中，滚动到 **Struct fields**（结构体字段）部分，并选择你要编辑的结构体字段。所做编辑的数量将显示在 Ontology Manager 界面的右上角。

![“属性编辑器”面板中的结构体字段。](https://www.palantir.com/docs/resources/foundry/object-link-types/Edit-struct.png?width=500)

1. 在 **Edit struct field**（编辑结构体字段）对话框中进行必要的编辑，然后选择 **Confirm**（确认）。

![“编辑结构体字段”对话框。](https://www.palantir.com/docs/resources/foundry/object-link-types/confirm-struct-edit.png?width=500)

> **ℹ️ 提示**
>
> 请注意，更改结构体字段的 API 名称将导致生成新的结构体字段 RID。这将覆盖现有索引，类似于更改属性类型的属性 ID 时的行为。任何引用了更新后结构体字段的应用也需要进行更新。

---

*原文：[Edit a struct property type](https://www.palantir.com/docs/foundry/object-link-types/edit-struct-type/)*
