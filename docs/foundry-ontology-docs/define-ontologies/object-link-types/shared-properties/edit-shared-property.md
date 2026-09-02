# 编辑共享属性（Edit shared properties）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/edit-shared-property/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

### 编辑共享属性元数据

你可以先从 Ontology Manager 的 **Shared property**（共享属性）页面选择要编辑的共享属性，来编辑共享属性的元数据。

![编辑共享属性元数据](https://www.palantir.com/docs/resources/foundry/object-link-types/edit-shared-property.png?width=500)

编辑共享属性元数据的可用选项集中在四个不同的选项卡中：**General**（常规）、**Display**（显示）、**Interaction**（交互）和 **Details**（详细信息）。这些选项卡包含以下配置：

- **Name（名称）：** 共享属性的名称。
- **Description（描述）：** 关于共享属性的说明性文字。例如，`start date` 共享属性的描述可以是 `The day the employee or contractor began working`（员工或承包商开始工作的日期）。
- **Base type（基础类型）：** 指示此属性的值类型，并决定用户应用中可用的操作集合。例如，`start date` 属性的基础类型为 `date`。用户应用将允许你使用此属性配置时间线组件。
- **Value formatting（值格式设置）：** 根据属性的基础类型，可对属性应用数字格式设置、日期和时间格式设置、用户 ID 和资源 ID 格式设置，在用户应用中将其原始值转换为更易读的形式。详细了解[值格式设置](https://www.palantir.com/docs/foundry/object-link-types/value-formatting/)。
- **Type classes（类型类）：** 由用户应用解释的附加元数据。详细了解[类型类](https://www.palantir.com/docs/foundry/object-link-types/metadata-typeclasses/)。
- **Render hints（渲染提示）：** 向用户应用指示如何渲染该属性，其方式可能与同一基础类型的大多数属性不同。许多渲染提示可用于影响定义该属性的对象类型的重新索引性能。例如，如果你不期望任何用户在用户应用中对 `start date` 属性进行搜索或排序，你可以取消选择 `searchable`（可搜索）和 `sortable`（可排序）渲染提示，从而提高 `Employee` 对象类型的重新索引性能。详细了解[渲染提示](https://www.palantir.com/docs/foundry/object-link-types/metadata-render-hints/)。
- **Visibility（可见性）：** 向用户应用指示显示该属性的显著程度。`prominent`（显著）属性会引导应用首先向用户展示该属性。`hidden`（隐藏）属性不会出现在用户应用中。默认情况下，`start date` 属性具有 `normal`（普通）可见性。

此外，你可以在 **Usage**（使用情况）选项卡中查看使用此共享属性的对象类型，并在 **Permissions**（权限）选项卡中更新共享属性的权限。

### 删除共享属性

要删除共享属性，请完成以下步骤：

1. 导航到 Ontology Manager 的 **Shared property**（共享属性）页面。
2. 选择一个或多个要删除的共享属性，然后选择 **Delete property**（删除属性）。

![删除共享属性](https://www.palantir.com/docs/resources/foundry/object-link-types/delete-shared-property-button.png?width=500)

1. 在模态框中确认删除操作。

![确认共享属性删除](https://www.palantir.com/docs/resources/foundry/object-link-types/delete-shared-property-modal.png?width=500)

1. 选择右上角的 **Save**（保存）。

> **⚠️ 注意**
>
> 删除共享属性时，所有使用此共享属性的对象类型都将回退为常规属性。

---

*原文：[Edit shared properties](https://www.palantir.com/docs/foundry/object-link-types/edit-shared-property/)*
