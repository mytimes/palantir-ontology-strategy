# 内联编辑（Inline edits）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/action-types/inline-edits/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

由动作支撑的内联编辑，其校验和提交方式与标准[动作](https://www.palantir.com/docs/foundry/action-types/getting-started/)不同。对于标准动作，需要设置多个参数才能使动作有效。然而，对于动作支撑的内联编辑，每个参数都是可选的，并默认取对象的现有值，因此用户可以一次对一个属性进行单独更改。

本文档讨论如何在使用内联编辑时避免意外结果。内联编辑在 Workshop 和 Object Explorer 中均可用。内联编辑动作的配置取决于该动作的使用位置。

## Object Explorer 内联编辑

内联编辑允许用户在 [Object Explorer 结果视图](https://www.palantir.com/docs/foundry/object-explorer/view-results/)或原生 Object View 组件（如属性卡片或指标卡片组件）中快速编辑对象的值。

### 配置

![内联编辑动作配置](https://www.palantir.com/docs/resources/foundry/action-types/inline-action-configuration.png)

要设置内联编辑动作，请在 Ontology Manager 中导航到对象类型的**属性（Properties）**选项卡，然后进入**交互（Interaction）**选项卡。选择一个属性，并在侧边栏中导航到**内联编辑（Inline edit）**。在下拉菜单中，选择一个可用的动作类型或创建一个新的动作类型。创建新动作类型将触发动作类型创建工作流。每个属性只能有一个内联编辑动作类型。

你可以将同一个动作类型用作多个属性的内联编辑，也可以为不同属性使用不同的动作类型。

#### 内联编辑的动作类型要求

并非所有动作类型都能用作内联编辑动作类型。要被接受，动作类型必须满足以下要求：

- 只能修改单个对象类型的单个对象。
- 必须启用默认值。
- 默认值必须来自内联动作定义所基于的对象引用参数。因此，动作中正在更改的属性不能映射到静态值或“Current User”（当前用户）、“Current Time”（当前时间）等特殊值。
- 可以设置可见性状态和覆盖；但是，如果内联编辑在 Object Explorer 和 Object Views 中使用，它们将被忽略。
- 不能启用[副作用 Webhook](https://www.palantir.com/docs/foundry/action-types/webhooks/#webhooks-writeback-vs-side-effect)或[副作用通知](https://www.palantir.com/docs/foundry/action-types/notifications/)。

## Workshop 内联编辑

在 Workshop 中将动作类型用作内联编辑不需要额外配置，但并非所有动作都适合单元格级编辑。有关如何配置内联编辑的信息，请参阅 [Workshop 文档](https://www.palantir.com/docs/foundry/workshop/widgets-object-table/#inline-edits-cell-level-writeback)。

### 背景

运行单个动作时，编辑会逐个（顺序地）校验和提交。内联编辑的不同之处在于它们是批量校验和提交的。因此，并非所有动作都适合内联编辑。可能因内联编辑而失败或产生意外结果的动作包括：

- 任何尝试读取另一个动作可能已写入的数据的动作，或者
- 两个尝试写入同一对象的动作。

> **ℹ️ 提示**
>
> 当内联编辑应用于[场景](https://www.palantir.com/docs/foundry/workshop/scenarios-overview/)时，提交的动作是顺序应用的（顺序不确定），而不是同时应用（内联编辑通常的情况）。因此，通常因多个动作尝试写入同一对象而失败的内联编辑动作，在应用于场景时可能会成功，但我们不建议构建依赖于此行为差异的应用。

### 有效的内联动作

动作必须提交不冲突的编辑，才能作为动作支撑的内联编辑有效发挥作用。实际上，这意味着在同一个表格编辑组件中配置的多个动作不得：

- 写入同一对象，
- 创建同一链接，或者
- 尝试保持聚合值一致。

### 无效的内联动作

**如果内联编辑尝试两次编辑同一对象，动作将返回错误。**此外，内联编辑不支持添加或删除连接表链接，并将导致面向用户的错误消息。

当用户应用内联编辑时，[提交条件](https://www.palantir.com/docs/foundry/action-types/submission-criteria/)会应用于每个编辑，但编辑将批量提交。对于每个被编辑的对象，参数级和全局提交条件都会被求值，但引用共享对象或链接对象的提交条件与内联编辑不兼容。这是因为在应用内联编辑时，累积性提交条件会将编辑后的值与该列中未编辑的值进行比较。在最终提交时，所有编辑将一次性提交，并且只有当它们全部通过相应对象的参数级和全局提交条件时才会成功。

因此，对于多个动作类型之间共享的对象或链接对象，其上的提交条件会按每次编辑求值一次，且求值发生在任何编辑进行之前。

> **⚠️ 注意**
>
> 引用共享动作类型或链接对象的提交条件与内联编辑不兼容；批量更新对象可能会违反那些在顺序应用（一次一个）时按预期工作的提交条件规则。

#### 示例：无效的内联动作

设想一个 `Delay Flight`（延误航班）动作，它可以将单个航班最多延误 20 分钟；而某机场所有航班的总延误上限为 50 分钟。

- 每次更新单元格时，两条提交条件——20 分钟的要求和 50 分钟的总量——都会被求值。
  - 由于尚未提交任何编辑，50 分钟总量会将新的延误与该列中未编辑延误的总和（内联编辑开始前的延误）进行比较。
- 第二条提交条件（机场所有延误之和小于 50 分钟）依赖于一个聚合值，并由该列中的所有对象共享。
  - 由于内联编辑是批量提交的，这第二条提交条件将无法有效限制给定机场航班延误的总时长；最终编辑的总和可能超过第二条提交条件允许的 50（分钟）。
- 此动作不适合表格编辑，因为与为每个单元格单独运行该动作相比，它会导致不一致的结果。

---

*原文：[Inline edits](https://www.palantir.com/docs/foundry/action-types/inline-edits/)*
