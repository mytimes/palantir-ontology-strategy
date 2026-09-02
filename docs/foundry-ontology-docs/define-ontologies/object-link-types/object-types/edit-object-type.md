# 编辑对象类型（Edit object types）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/edit-object-type/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

> **⚠️ 注意：警告**
>
> 编辑对象类型及其属性可能产生**导致应用中断、干扰用户工作流的后果**。在进行任何对象类型或属性编辑**之前**，请先阅读下方关于[潜在破坏性更改](#潜在破坏性更改)的章节。

## 潜在破坏性更改

**回写（Writeback）**是将用户对本体对象的编辑持久化到一个持久保存的 Foundry 数据集的过程。该数据集称为回写数据集。在 [Object Storage v1（Phonograph）](https://www.palantir.com/docs/foundry/object-databases/object-storage-v1/)中，你必须为对象类型，或为带连接表的多对多链接类型配置回写数据集。这是对象类型或链接类型能够接收用户编辑之前的必要条件。详细了解回写数据集与 Object Storage v2 中[物化数据集](https://www.palantir.com/docs/foundry/object-edits/materializations/#comparison-of-writeback-datasets-and-materialized-datasets)的对比。

### 无回写的对象类型

需要 Object Storage v1（Phonograph）注销并重新注册对象类型支撑数据源的更改，会在该重新索引期间使该类型的对象在用户应用中**不可用**；这些更改如下所述。

保存时，以下更改将注销并重新注册（或删除）对象类型的支撑数据源：

- 更改对象类型的支撑数据源。
- 更改对象类型的主键。
- 删除对象类型。

当你尝试保存这些更改中的任何一项时，系统会就其对用户应用的潜在影响发出警告。

![警告：重新索引将使对象不可用](https://www.palantir.com/docs/resources/foundry/object-link-types/edit-object-type-warning-reindex.png?width=500)

例如，如果某个对象类型用于 Workshop 应用中，该 Workshop 应用将在重新索引完成之前处于中断状态。你可以在其 **Datasources** 页面的 **Phonograph** 窗格中跟踪对象类型重新索引的进度。

![在 Phonograph 中跟踪重新索引](https://www.palantir.com/docs/resources/foundry/object-link-types/edit-object-type-phonograph-track-reindex.png?width=500)

[详细了解 Object Storage v1（Phonograph）。](https://www.palantir.com/docs/foundry/object-databases/object-storage-v1/)

### 有回写的对象类型

如果对象类型启用了回写，在编辑该对象类型时应格外谨慎。对对象类型所做编辑的历史记录存储在 Object Storage v1（Phonograph）中。每次构建回写数据集时，都会重新应用编辑历史记录，以获得回写数据集中已编辑对象的最终状态。当对象类型的支撑数据源从 Object Storage v1（Phonograph）注销时，Object Storage v1（Phonograph）中的编辑历史记录会被删除，未来的回写数据集构建将会失败。

除了[上一节](#无回写的对象类型)中列出的需要注销的更改之外，对于有回写的对象类型，当对其**任何****曾经**接收过编辑的属性进行模式更改时（即使该属性当前不接收编辑），也需要注销。模式更改包括对属性 ID 和基础类型的更改。

以下更改***不***需要注销，因此不存在丢失编辑历史记录的风险：

- 更改已接收编辑的属性的显示名、标题键、渲染提示、类型类和可见性，***不***需要对象类型注销。
- 删除从未接收过编辑的字段或对其进行模式更改，***不***需要对象类型注销，因此不会擦除或撤销其他正在接收编辑的字段上的编辑。

> **⚠️ 注意：警告**
>
> Object Storage v1（Phonograph）**不会**响应此类模式更改而自动注销对象类型的支撑数据源。相反，重新索引会失败，并且只有在撤销已保存的模式更改时，或者你在对象类型 **Datasource** 页面的 **Phonograph** 窗格中手动注销并重新注册对象类型的支撑数据源时，重新索引才会成功。

属性编辑器中的属性窗格会高亮显示某个字段是否曾经接收过编辑。

![属性窗格](https://www.palantir.com/docs/resources/foundry/object-link-types/edit-object-type-properties-pane.png?width=500)

此外，当你尝试保存任何有擦除编辑历史记录风险的更改时，系统会就其对编辑的潜在影响发出警告。

![关于编辑影响的警告](https://www.palantir.com/docs/resources/foundry/object-link-types/edit-object-type-warning-edit-impact.png?width=500)

现在你已经了解了编辑现有对象类型和属性的注意事项，可以安全地进行更改了。

## 编辑现有对象类型

- [前往现有对象类型](#前往现有对象类型)
- [删除对象类型](#删除对象类型)
- [更改支撑数据源](#更改支撑数据源)
- [编辑对象类型的元数据](#编辑对象类型的元数据)

### 前往现有对象类型

你随时可以通过从主页侧边栏选择对象类型页面并从列表中选择其他对象类型，来更改正在处理的对象类型。你也随时可以在应用页眉的搜索栏中搜索新的对象类型。[阅读更多关于导航的内容。](https://www.palantir.com/docs/foundry/ontology-manager/navigation/)

### 删除对象类型

你可以删除对象类型：选择对象类型视图侧边栏右上角的 ![...](https://www.palantir.com/docs/resources/foundry/object-link-types/three-dots.png)（三点）图标（见下图），然后从下拉菜单中选择 **Delete** 选项。系统将弹出一个对话框，确认你要将该对象类型及其所有关联链接类型暂存为待删除。

- 请注意，对象类型的删除仅在你保存更改后生效，并且会中断任何引用该对象类型的视图或应用。
- 状态为 `active` 的对象类型无法删除。[阅读更多关于状态的内容。](https://www.palantir.com/docs/foundry/object-link-types/metadata-statuses/)

![删除对象类型](https://www.palantir.com/docs/resources/foundry/object-link-types/edit-object-type-delete-object-type.png?width=500)

### 更改支撑数据源

你可以通过以下步骤更改支撑数据源：

1. 在对象类型的 **Properties** 页面顶部选择 **Edit property mapping**，前往属性编辑器。
2. 选择 **Datasources** 窗格顶部的 ![笔](https://www.palantir.com/docs/resources/foundry/object-link-types/pen.png) **Replace** 按钮。这将允许你浏览并选择 Foundry 中可用的数据源。

> **⚠️ 注意：警告**
>
> 更改对象类型的支撑数据源会移除旧数据源中的列与对象类型属性之间的所有关联。**只有当**你更换为与旧数据源具有**相同模式**的新数据源时，系统才会为你自动重新映射属性。否则，你需要将对象类型的属性重新映射到新数据源。

![支撑数据源](https://www.palantir.com/docs/resources/foundry/object-link-types/edit-object-type-backing-datasource.png)

### 编辑对象类型的元数据

![编辑对象类型元数据](https://www.palantir.com/docs/resources/foundry/object-link-types/edit-object-type-metadata-annotated.png)

1. **Icon（图标）：** 选择默认图标以自定义对象类型的图标和颜色；当用户在应用中查看此类型的对象时，将显示该图标和颜色。
2. **Display names and description（显示名和描述）：** 选择现有的显示名或描述以编辑文本。
3. **Status（状态）：** 选择现有状态以打开可用状态的下拉菜单。可从 `deprecated`（已弃用）、`experimental`（实验性）和 `active`（活跃）状态中选择。
  - 阅读更多关于[状态](https://www.palantir.com/docs/foundry/object-link-types/metadata-statuses/)的内容。
4. **Visibility（可见性）：** 选择现有可见性以打开可用可见性的下拉菜单。`prominent`（突出）对象类型会使应用优先向用户显示该对象类型。`hidden`（隐藏）对象类型不会出现在用户应用中。
5. **API name（API 名称）：** 选择现有的 API 名称以更改其值。
  - 请注意，你无法更改状态为 `active` 的对象类型的 API 名称。
    - 阅读更多关于[状态](https://www.palantir.com/docs/foundry/object-link-types/metadata-statuses/)的内容。
    - 阅读更多关于[有效 API 名称](https://www.palantir.com/docs/foundry/object-link-types/create-object-type/#api-name)的内容。

> **ℹ️ 提示**
>
> 对象类型的对象 ID 在初始对象类型创建流程完成后无法编辑。

## 故障排查

#### 错误：`Phonograph2:FoundryColumnNameNotFound`

如果你收到错误 `Phonograph2:FoundryColumnNameNotFound`，表示你尝试保存的对象类型的支撑数据源中有一列已被移除，导致某个属性未被映射。该属性需要被映射或删除。

#### 错误：`Phonograph2:InvalidColumnRemoval`

如果你收到错误 `Phonograph2:InvalidColumnRemoval`，表示被移除的列此前支撑着一个已接收过编辑的属性。你需要将该列重新添加到数据源，或者注销并重新注册对象类型。

请参阅上方关于[潜在破坏性更改](#潜在破坏性更改)的章节了解更多。

#### 错误：`Phonograph2:InvalidColumnFieldSchemaChange`

如果你收到错误 `Phonograph2:InvalidColumnFieldSchemaChange`，表示某个已接收过编辑的属性的 ID 或键发生了更改。你需要撤销该更改，或者注销并重新注册对象类型。

请参阅上方关于[潜在破坏性更改](#潜在破坏性更改)的章节了解更多。

#### 错误：`OntologyMetadata:IncompatibleFoundryFieldSchemaForPropertyType`

如果你收到错误 `OntologyMetadata:IncompatibleFoundryFieldSchemaForPropertyType`，表示你尝试保存的属性的基础类型与其支撑列的列类型不兼容。例如，列 X 的类型可能已更改为“string”，但它映射到基础类型为“integer”的属性 X。

#### 错误：`Phonograph2:SchemaMismatch`

如果你收到错误 `Phonograph2:SchemaMismatch`，很可能是你有意更改了支撑对象的模式，但尚未在 Ontology Manager 中更新对象的属性类型。通过编辑属性的数据类型以接受新类型来修改本体。发布更改并重新构建数据集，然后启动对象的重新索引。

#### 错误：`FieldTypeIncompatibleWithOntologyPropertyType`

如果你收到错误 `FieldTypeIncompatibleWithOntologyPropertyType` 或收到消息“Failed to Update Object Type in Phonograph”（在 Phonograph 中更新对象类型失败），表示支撑对象的数据集中的数据类型与本体预期的数据类型之间存在不匹配。你必须确保任何模式更新都同时反映在数据集和本体中。

如果你确实对本体或数据集进行了任何有意更改，请与对象及其支撑数据源的负责人沟通，以了解最近的更改。

---

*原文：[Edit object types](https://www.palantir.com/docs/foundry/object-link-types/edit-object-type/)*
