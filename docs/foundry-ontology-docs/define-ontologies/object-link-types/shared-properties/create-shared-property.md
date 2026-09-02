# 创建共享属性（Create a shared property）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/create-shared-property/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

在 Ontology Manager 应用中，从共享属性页面创建和配置新的共享属性。

要访问该页面，请按照以下步骤操作：

1. 在 Ontology Manager 中选择 **Shared properties**（共享属性）菜单选项。

![Ontology Manager 中的共享属性页面](https://www.palantir.com/docs/resources/foundry/object-link-types/shared-property-menu-option.png?width=800)

1. 进入共享属性页面后，选择右上角的 **New shared property**（新建共享属性）。

![创建共享属性按钮](https://www.palantir.com/docs/resources/foundry/object-link-types/new-shared-property-button.png?width=800)

1. 这将打开共享属性创建模态框，你可以在其中配置名称、描述、类型和其他元数据以创建共享属性。

![创建共享属性模态框](https://www.palantir.com/docs/resources/foundry/object-link-types/create-shared-property-modal.png?width=500)

共享属性可以配置常规属性元数据的一个子集：

- **Name（名称）：** 共享属性的名称。
- **Description（描述）：** 关于共享属性的说明性文字。例如，`start date` 共享属性的描述可以是 `The day the employee or contractor began working`（员工或承包商开始工作的日期）。
- **Base type（基础类型）：** 指示此属性的值类型，并决定用户应用中可用的操作集合。例如，`start date` 属性的基础类型为 `date`。用户应用将允许你使用此属性配置时间线组件。基础类型与底层列类型相关，并且必须与列类型匹配才能应用于对象类型。
- **Value formatting（值格式设置）：** 根据属性的基础类型，可对属性应用数字格式设置、日期和时间格式设置、用户 ID 和资源 ID 格式设置，在用户应用中将其原始值转换为更易读的形式。详细了解[值格式设置](https://www.palantir.com/docs/foundry/object-link-types/value-formatting/)。
- **Type classes（类型类）：** 由用户应用解释的附加元数据。详细了解[类型类](https://www.palantir.com/docs/foundry/object-link-types/metadata-typeclasses/)。
- **Render hints（渲染提示）：** 向用户应用指示如何渲染该属性，其方式可能与同一基础类型的大多数属性不同。许多渲染提示可用于影响定义该属性的对象类型的重新索引性能。例如，如果你不期望任何用户在用户应用中对 `start date` 属性进行搜索或排序，你可以取消选择 `searchable`（可搜索）和 `sortable`（可排序）渲染提示，从而提高 `Employee` 对象类型的重新索引性能。详细了解[渲染提示](https://www.palantir.com/docs/foundry/object-link-types/metadata-render-hints/)。
- **Visibility（可见性）：** 向用户应用指示显示该属性的显著程度。`prominent`（显著）属性会引导应用首先向用户展示该属性。`hidden`（隐藏）属性不会出现在用户应用中。默认情况下，`start date` 属性具有 `normal`（普通）可见性。

1. 要将共享属性持久化到本体，请在 Ontology Manager 的右上角选择 **Save**（保存）。

---

*原文：[Create a shared property](https://www.palantir.com/docs/foundry/object-link-types/create-shared-property/)*
