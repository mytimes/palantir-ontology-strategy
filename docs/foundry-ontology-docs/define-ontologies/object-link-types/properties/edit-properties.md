# 编辑对象类型属性（Edit object type properties）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/edit-properties/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

### 删除属性

在属性编辑器的属性窗格中，将鼠标悬停在要删除的属性上，然后选择 **Delete property**（删除属性）。

- 请注意，属性删除仅在你保存更改后生效，并且会使引用该属性的任何视图或应用失效。
- 状态为 `active` 的属性**无法**删除。
  - 详细了解[状态](https://www.palantir.com/docs/foundry/object-link-types/metadata-statuses/)。

### 更改支撑属性的列

在属性编辑器的属性窗格中，将鼠标悬停在要取消映射的属性上，然后选择 **Unlink property**（取消链接属性）。要将属性链接到新列，请将鼠标悬停在该属性上并选择 **Map to a column**（映射到列）。

![映射到列](https://www.palantir.com/docs/resources/foundry/object-link-types/edit-object-type-map-to-column.png)

### 编辑属性类型的元数据

你可以通过选择属性类型来编辑其元数据，如下图所示。

![编辑属性元数据](https://www.palantir.com/docs/resources/foundry/object-link-types/edit-object-type-property-metadata.png?width=500)

编辑属性元数据的可用选项集中在四个不同的选项卡中，可访问以下配置：

1. **显示名和描述：** 选择现有的显示名或描述以编辑文本。
2. **状态：** 选择现有状态以打开可用状态的下拉菜单。可从 `deprecated`（已弃用）、`experimental`（实验性）和 `active`（活跃）状态中选择。
  - 详细了解[状态](https://www.palantir.com/docs/foundry/object-link-types/metadata-statuses/)。
3. **API 名称：** 选择现有的 API 名称以更改其值。
  - 请注意，你**无法**更改状态为 `active` 的属性的 API 名称。
    - 详细了解[状态](https://www.palantir.com/docs/foundry/object-link-types/metadata-statuses/)。
    - 详细了解[有效的 API 名称](https://www.palantir.com/docs/foundry/object-link-types/create-object-type/#api-name)。
4. **键（Keys）：** 指示某个属性是对象类型的标题键还是主键。
  - 请注意，你**无法**更改状态为 `active` 的对象类型的主键。
    - 详细了解[键](https://www.palantir.com/docs/foundry/object-link-types/create-object-type/#configure-the-primary-key-and-title-key)和[状态](https://www.palantir.com/docs/foundry/object-link-types/metadata-statuses/)。
5. **值格式（Value formatting）：** 对属性的值应用特殊格式化程序，使其在应用中更具可读性。
  - 详细了解[值格式化程序](https://www.palantir.com/docs/foundry/object-link-types/value-formatting/)。
6. **条件格式：** 对属性应用规则，规定其在应用中的渲染方式。
  - 详细了解[条件格式](https://www.palantir.com/docs/foundry/object-link-types/conditional-formatting/)。
7. **属性基础类型：** 从下拉菜单中选择属性的基础类型。属性的类型约束了可对其值执行的操作集合。
  - 例如，基础类型为 `timestamp` 的属性可以在 Object Explorer 的时间线组件中显示。
  - 如果属性的类型与其支撑列的类型不兼容，你将收到错误。

> **⚠️ 注意**
>
> 如果你更改对象属性类型，你还必须更新与该对象上属性交互的动作所预期的类型。为此，请在 Ontology Manager 中打开该动作并编辑预期类型。

1. **类型类：** 将类型类作为可由应用解释的额外元数据应用。
  - 有关可用类型类的列表，请参阅[类型类文档](https://www.palantir.com/docs/foundry/object-link-types/metadata-typeclasses/)。
2. **渲染提示：** 从提供的清单中选择你希望应用于属性的渲染提示，以改善属性值的渲染方式以及在 Object Storage v1（Phonograph）中编入索引的方式。
  - 有关可用渲染提示的描述，请参阅[渲染提示文档](https://www.palantir.com/docs/foundry/object-link-types/metadata-render-hints/)。
3. **可见性：** 选择现有可见性以打开可用可见性的下拉菜单。`prominent`（突出）属性会使应用优先向用户显示该属性。`hidden`（隐藏）属性不会出现在用户应用中。

更改属性元数据后，对受影响的对象发起重新索引以更新本体。

### 批量编辑多个属性

你可以在属性编辑器中按住 **Cmd/Ctrl** 键同时选择多个属性。选择多个属性后，以下批量编辑操作将变为可用：

- 更改基础类型。
- 添加/移除类型类。
- 更改渲染提示。
- 更改可见性。
- 添加/移除值格式。

![编辑属性元数据](https://www.palantir.com/docs/resources/foundry/object-link-types/edit-object-type-bulk-edit-multiple-properties.png)

你还可以在属性编辑器之外批量编辑上述某些字段，方法是从对象类型视图的侧边栏选择 **Properties** 页面。只需选中要编辑的属性旁边的复选框，表格顶部就会出现一行新的批量编辑选项。

---

*原文：[Edit object type properties](https://www.palantir.com/docs/foundry/object-link-types/edit-properties/)*
