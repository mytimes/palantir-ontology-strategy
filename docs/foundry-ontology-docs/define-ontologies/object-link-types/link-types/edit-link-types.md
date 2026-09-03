# 编辑链接类型（Edit link types）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/edit-link-types/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

> **⚠️ 注意：警告**
>
> 编辑链接类型可能产生**破坏应用的后果**，从而干扰用户工作流。在进行任何链接类型编辑**之前**，请先阅读下面关于[潜在破坏性变更](#潜在破坏性变更)的部分。

## 潜在破坏性变更

### 无回写的链接类型

某些变更需要 Object Storage v1（Phonograph）注销并重新注册链接类型的支撑数据源，这类变更会使该类型的链接在重新索引期间在用户应用中**不可用**；这些变更如下所述。

保存以下变更时，将注销并重新注册（或删除）链接类型的支撑数据源：

- 更改多对多链接类型的支撑数据源。
- 更改链接类型的基数。
- 更改链接类型的外键。
- 删除链接类型。

当你尝试保存上述任何变更时，系统会就其对用户应用的潜在影响向你发出警告。

![警告：重新索引将使对象不可用](https://www.palantir.com/docs/resources/foundry/object-link-types/edit-link-type-warning-reindex.png?width=500)

例如，如果某个链接类型在 Workshop 应用的环绕搜索（search around）中使用，那么在重新索引完成之前，该 Workshop 应用将处于损坏状态。你可以在链接类型 **Datasources（数据源）**页面的 **Phonograph** 窗格中跟踪该链接类型重新索引的进度。

![在 Phonograph 中跟踪重新索引](https://www.palantir.com/docs/resources/foundry/object-link-types/edit-link-type-phonograph-track-reindex.png?width=500)

[详细了解 Object Storage v1（Phonograph）。](https://www.palantir.com/docs/foundry/object-databases/object-storage-v1/)

### 有回写的链接类型

如果链接类型启用了回写，在编辑该链接类型时应格外谨慎。对链接类型所做编辑的历史记录存储在 Object Storage v1（Phonograph）中。每次构建回写数据集时，都会重新应用编辑历史，以得出回写数据集中已编辑链接的最终状态。当链接类型的支撑数据源从 Object Storage v1（Phonograph）注销时，Object Storage v1（Phonograph）中的编辑历史会被删除，此后回写数据集的构建将会失败。

除了[上一节](#无回写的链接类型)中列出的需要注销的变更外，有回写的链接类型在以下情况下也需要注销：对其支撑数据源的**任何**列进行了模式变更，且该链接类型**曾经**接收过编辑（即使当前不再接收编辑）。模式变更包括对列名称和基础类型的变更。

> **⚠️ 注意：警告**
>
> Object Storage v1（Phonograph）**不会**因这类模式变更而自动注销链接类型的支撑数据源。相反，重新索引将会失败，并且只有撤销已保存的模式变更，或者你在链接类型 Datasources 页面的 Phonograph 窗格中手动注销并重新注册链接类型的支撑数据源，重新索引才会成功。

当你尝试保存任何有可能擦除编辑历史的变更时，系统会就变更对编辑的潜在影响向你发出警告。

![关于编辑影响的警告](https://www.palantir.com/docs/resources/foundry/object-link-types/edit-link-type-warning-edit-impact.png?width=500)

现在你已经了解了编辑现有链接类型的注意事项，可以安全地进行更改了。

## 编辑现有链接类型

- [导航到现有链接类型](#导航到现有链接类型)
- [删除链接类型](#删除链接类型)
- [更改支撑数据源](#更改支撑数据源)
- [编辑链接类型的元数据](#编辑链接类型的元数据)

### 导航到现有链接类型

你随时可以通过从主页侧边栏选择链接类型页面、再从列表中选择其他链接类型，来更改正在处理的链接类型。你也可以随时在应用页眉的搜索栏中搜索其他链接类型。阅读更多关于[导航](https://www.palantir.com/docs/foundry/ontology-manager/navigation/)的信息。

### 删除链接类型

你可以删除链接类型：选择链接类型视图侧边栏右上角的 ![...](https://www.palantir.com/docs/resources/foundry/object-link-types/three-dots.png)（三个点）图标（见下图），然后从下拉菜单中选择 **Delete（删除）**选项。随后会弹出一个对话框，确认你是否要暂存该链接类型以待删除。

- 请注意，链接类型的删除只有在你保存更改后才会生效，并且会破坏引用该链接类型的任何视图或应用。
- 请注意，状态为 `active` 的链接类型无法删除。阅读更多关于[状态](https://www.palantir.com/docs/foundry/object-link-types/metadata-statuses/)的信息。

![删除链接类型](https://www.palantir.com/docs/resources/foundry/object-link-types/edit-link-type-delete-link-type.png?width=500)

### 更改支撑数据源

你可以更改支撑数据源：

1. 导航到链接类型视图的 **Datasources（数据源）**页面。
2. 选择现有数据源旁边的 ![钢笔图标](https://www.palantir.com/docs/resources/foundry/object-link-types/pen.png) **Select（选择）**图标。这将允许你浏览并选择 Foundry 中可用的数据源。

> **⚠️ 注意：警告**
>
> 更改链接类型的支撑数据源将移除旧数据源中的列与定义链接类型的键之间的任何关联。只有当你更换为与旧数据源**模式相同**的新数据源时，系统才会自动为你重新映射键。否则，你需要将键重新映射到新数据源。

![选择支撑数据源](https://www.palantir.com/docs/resources/foundry/object-link-types/edit-link-type-change-backing-datasource-annotated.png)

### 编辑链接类型的元数据

![编辑链接类型元数据](https://www.palantir.com/docs/resources/foundry/object-link-types/edit-link-type-metadata-new-overview.png?width=500)

1. **Status（状态）：**选择链接类型窗格顶部的当前状态，以打开可用状态的下拉菜单。从 `deprecated`、`experimental` 和 `active` 状态中选择。
  - 阅读更多关于[状态](https://www.palantir.com/docs/foundry/object-link-types/metadata-statuses/)的信息。
2. **Key（键）：**通过下拉菜单进行选择，以更改外键，或更改多对多链接类型中的列映射。
  - 请注意，在多对多基数的链接类型中，支撑数据源中的列必须映射到对象类型的主键。如果对象类型主键属性的类型与链接类型支撑数据源中被映射列的类型不一致，将有错误阻止你保存。
  - 在任何其他基数的链接类型中，应用要求其中一个对象类型的键必须映射到该对象类型的主键，以确保基数的“一”侧是唯一的。
3. **API name（API 名称）：**选择现有的 API 名称以更改其值。
  - 请注意，你无法更改状态为 `active` 的链接类型的 API 名称。
    - 阅读更多关于[状态](https://www.palantir.com/docs/foundry/object-link-types/metadata-statuses/)的信息。
    - 阅读更多关于[有效 API 名称](https://www.palantir.com/docs/foundry/object-link-types/create-object-type/#configure-api-names)的信息。
4. **Visibility（可见性）：**从链接可见性列表中选择可见性。`prominent`（显著）链接类型会提示应用优先向用户展示此链接类型。`hidden`（隐藏）链接类型不会出现在用户应用中。
5. **Type classes（类型类）：**应用类型类作为附加元数据，可供应用解读。
  - 请参阅[可用类型类列表](https://www.palantir.com/docs/foundry/object-link-types/metadata-typeclasses/)以了解更多信息。

---

*原文：[Edit link types](https://www.palantir.com/docs/foundry/object-link-types/edit-link-types/)*
