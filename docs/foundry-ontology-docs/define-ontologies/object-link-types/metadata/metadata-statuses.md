# 状态（Statuses）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/metadata-statuses/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

本体中的每个对象类型、属性、链接类型、动作或接口都有一个表明其开发状态的**状态（status）**。本体资源的状态可以是 active（活跃）、experimental（实验性）、deprecated（已弃用）或 example（示例）；对象类型还可以被分类为 [promoted](#promoted-状态仅限对象类型)（已推广）。状态元数据可帮助编辑本体的用户了解哪些资源正被用户应用实际依赖。这些状态可在 [**Object Explorer**](https://www.palantir.com/docs/foundry/object-explorer/overview/)、[**Object Views**](https://www.palantir.com/docs/foundry/object-views/overview/) 和 [**Workshop**](https://www.palantir.com/docs/foundry/workshop/overview/) 中查看，以提供更多关于哪些对象类型旨在供用户应用使用的信息。

![Active 状态](https://www.palantir.com/docs/resources/foundry/object-link-types/statuses-active.png?width=400)

状态可以取以下五个值之一：

## 可用状态值

- **Promoted（已推广，仅限对象类型）：**表示该对象类型是经过本体所有者审核的核心、可信资源。`Promoted` 对象类型在 API 名称方面继承与 `active` 对象类型类似的保护。
- **Active（活跃）：**表示该资源正积极用于面向用户的应用，且不会在 Ontology Manager 中对其进行重大破坏性变更。
- **Experimental（实验性）：**表示该资源仍在开发中。可能会进行某些变更，导致实验性资源在面向用户的应用中不可用。
- **Deprecated（已弃用）：**表示该资源即将被删除。已弃用的资源不应在面向用户的应用中被依赖。
  - 已弃用资源的元数据还包括：
    - 弃用原因的描述；
    - 预计从系统中删除的截止日期；以及
    - 旨在替代被弃用资源的资源。
- **Example（示例）：**表示该资源是作为示例安装的。示例资源是虚构的，仅适合用于培训或早期探索性使用。示例*不*应用于生产工作流。

### `Promoted` 状态（仅限对象类型）

对象类型支持特殊的 `promoted` 状态，以表示其在本体中更高级别的信任度和官方地位。该状态由一个新的紫色对勾图标表示，可帮助用户将核心、可复用的对象类型与更针对特定用例或实验性的对象类型区分开来。

`promoted` 状态提供了超越标准 `active` 状态的显著度。具有此状态的对象类型应被视为“核心”资源，遵循高标准并由中央团队管理。`Promoted` 对象类型继承 `active` 状态的类似运营保护，例如删除限制。

`promoted` 状态的关键特征包括：

- **适用范围：**`promoted` 状态仅适用于对象类型。它不可用于属性、链接类型、动作类型或接口。
- **可见性：**将对象类型的状态设置为 `promoted` 会自动将其可见性设置为 `prominent`（显著），从而提高其在整个平台的可发现性。用户可以选择将该对象类型的所有属性移至 `active` 状态。
- **权限：**
  - 只有在本体级别具有 `Ontology Owner`（本体所有者）角色的用户才能直接应用 `promoted` 状态。
  - 其他用户必须提交提案，由本体级别的 `Ontology Owner` 评审和批准后才能应用该状态。

![已推广对象类型示例](https://www.palantir.com/docs/resources/foundry/object-link-types/promoted-example.png?width=400)

## 不允许的操作

鉴于应用依赖于本体资源，当资源状态为 `active` 时，有若干潜在破坏性操作是不允许的：

- 无法删除该资源。资源的状态必须先变为 `experimental` 或 `deprecated`，然后才能删除。
- 无法更改活跃资源的 API 名称。只有标记为 `experimental` 的资源才能更改 API 名称。

## 编辑状态

默认情况下，任何新的本体资源都会被赋予 `experimental` 状态。要更改状态：

1. 选择当前状态旁边的下拉菜单。
2. 选择新状态。

将资源更改为 `deprecated` 状态时，系统会提示你：

- 填写弃用原因的描述，
- 输入预计其从系统中删除的截止日期，以及
- （可选）选择一个旨在替代你正在弃用的资源的资源。

这些状态可在 Object Explorer、Object Views 和 Workshop 中查看，以提供更多关于哪些对象类型旨在供用户应用使用的信息。

![更改状态](https://www.palantir.com/docs/resources/foundry/object-link-types/edit-status-change-status.png?width=400)

Ontology Manager 确保对象类型与其相关属性或链接类型之间的状态一致性。例如，如果对象类型从 `active` 更改为 `experimental`，其所有属性也将被标记为 `experimental`。

下表列出了处于不同状态的对象类型之间链接类型的可用状态。一般来说：

- 如果链接类型中至少有一个对象类型被更改为 `experimental`，链接类型将自动更改为 `experimental`。
- 如果链接类型中至少有一个对象类型被更改为 `example`，链接类型将自动更改为 `example`。
- 如果链接类型中至少有一个对象类型被更改为 `deprecated`，链接类型将自动更改为 `deprecated`。

| *如果对象类型 A 是……* | 且对象类型 B 是…… |
| --- | --- |
|  | EXPERIMENTAL |
| *EXPERIMENTAL* | 只能为 experimental |
| *ACTIVE* | 只能为 experimental |
| *DEPRECATED* | 只能为 deprecated |

链接类型的外键也适用同样的要求。在更改属性时，应用会更改链接类型的状态：

- 如果外键属性被更改为 `experimental`，其链接类型将被更改为 `experimental`。
- 如果外键属性被更改为 `example`，其链接类型将被更改为 `example`。
- 如果外键属性被更改为 `deprecated`，其链接类型将被更改为 `deprecated`。

应用会更改状态以防止无效状态。如果外键属性是 `experimental` 且仍在开发中，其链接类型不应被标记为 `active` 并在生产中被依赖。相反，在将属性标记为 `active` 时，应用不会将引用该属性作为外键的链接类型更改为 `active`，因为外键属性已投入生产、而链接类型及其支撑数据源仍在开发中，这种情况是允许的。

## 批量编辑状态

### 属性

将对象类型从 `experimental` 更改为 `active` 时，可以选择同时将 `active` 状态应用于该对象类型上的所有属性：

![应用 active 状态](https://www.palantir.com/docs/resources/foundry/object-link-types/edit-status-apply-active-annotated.png?width=400)

当你将对象类型更改为 `example` 时，其所有属性也将自动变为 `example`。

对象类型各属性的状态也可以在该对象类型的 **Properties（属性）**页面中批量编辑。[阅读更多关于批量编辑属性的信息。](https://www.palantir.com/docs/foundry/object-link-types/edit-properties/#bulk-edit-multiple-properties)

### 对象类型

对象类型的状态也可以在主页对象视图页面中批量编辑：选中要编辑的对象类型的复选框，然后选择表格右上角的 **Edit status（编辑状态）**按钮。

![批量编辑对象类型](https://www.palantir.com/docs/resources/foundry/object-link-types/edit-status-bulk-edit.png?width=400)

## 故障排查

### 属性状态与链接类型状态之间的冲突

如果你收到错误 `OntologyMetadata:ConflictBetweenLinkTypeStatusAndPropertyTypeStatus`，说明链接类型上的状态与属性上的状态之间存在冲突。例如，如果外键是 `deprecated`，引用该外键的链接类型也应为 `deprecated`。

### 对象类型状态与链接类型状态之间的冲突

如果你收到错误 `OntologyMetadata:ConflictBetweenLinkTypeStatusAndObjectTypeStatus`，说明链接类型上的状态与其关联的某个对象类型的状态之间存在冲突。当根据上表出现无效的对象类型-链接类型组合时，可能会发生这种情况。例如，`experimental` 对象类型不能拥有 `active` 链接类型。

---

*原文：[Statuses](https://www.palantir.com/docs/foundry/object-link-types/metadata-statuses/)*
