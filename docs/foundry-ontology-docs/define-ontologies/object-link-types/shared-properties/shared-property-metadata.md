# 元数据参考（Metadata reference）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/shared-property-metadata/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

共享属性在本体中由以下元数据表示：

- **Name（名称）：** 共享属性的名称。
- **Description（描述）：** 关于共享属性的说明性文字，任何人都可以在用户应用中阅读。例如，`start date` 共享属性的描述可以是 `The day the employee began new hire training`（员工开始新员工培训的日期）。
- **RID：** 在 Foundry 中为每个资源自动生成的唯一标识符。属性的 RID 将在整个平台的错误消息中被引用。
- **Base type（基础类型）：** 指示此属性的值类型，并决定用户应用中可用的操作集合。例如，`start date` 属性的基础类型为 `date`。用户应用将允许你使用此属性配置时间线组件。
- **Value formatting（值格式设置）：** 根据属性的基础类型，可对属性应用数字格式设置、日期和时间格式设置、用户 ID 和资源 ID 格式设置，在用户应用中将其原始值转换为更易读的形式。详细了解[值格式设置](https://www.palantir.com/docs/foundry/object-link-types/value-formatting/)。
- **Type classes（类型类）：** 由用户应用解释的附加元数据。详细了解[类型类](https://www.palantir.com/docs/foundry/object-link-types/metadata-typeclasses/)。
- **Render hints（渲染提示）：** 向用户应用指示如何渲染该属性，其方式可能与同一基础类型的大多数属性不同。许多渲染提示可用于影响定义该属性的对象类型的重新索引性能。例如，如果你不期望任何用户在用户应用中对 `start date` 属性进行搜索或排序，你可以取消选择 `searchable`（可搜索）和 `sortable`（可排序）渲染提示，从而提高 `Employee` 对象类型的重新索引性能。详细了解[渲染提示](https://www.palantir.com/docs/foundry/object-link-types/metadata-render-hints/)。
- **Visibility（可见性）：** 向用户应用指示显示该属性的显著程度。`prominent`（显著）属性会引导应用首先向用户展示该属性。`hidden`（隐藏）属性不会出现在用户应用中。默认情况下，`start date` 属性具有 `normal`（普通）可见性。
- **Usage（使用情况）：** 使用共享属性的对象类型。例如，`start date` 属性可以被本体中的 `Employee`、`Contractor` 和其他对象类型使用。

---

*原文：[Metadata reference](https://www.palantir.com/docs/foundry/object-link-types/shared-property-metadata/)*
