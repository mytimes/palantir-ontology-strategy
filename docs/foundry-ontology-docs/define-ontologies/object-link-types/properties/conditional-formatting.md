# 条件格式（Conditional formatting）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/conditional-formatting/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

**条件格式**支持为任意属性配置规则，并规定该属性的值在面向用户的应用中如何渲染（例如着色、对齐等）。在 Ontology Manager 中配置条件格式后，格式规则将应用于 Object Explorer、Object Views、Quiver 和 Workshop。

![示例](https://www.palantir.com/docs/resources/foundry/object-link-types/conditional-formatting-cond-form-example.png?width=600)

对于上图所示 Object Explorer 中的 `Aircraft` 示例对象类型，`type` 和 `wifi` 属性的值显示在彩色框中，这些颜色根据特定条件应用。添加这些颜色的主要好处是使信息更易于快速理解。如果分析师要在“JFK”查找所有没有 wifi 的“A320”飞机，只需扫一眼上面的结果，就能知道“Q-AAY”是他们需要的飞机。

下面来看看这些条件是如何应用的。

- 对于属性 `wifi`，如果表中每个对象的该属性值为“true”，我们就指定绿色；如果为“false”，则指定红色。

![规则示例](https://www.palantir.com/docs/resources/foundry/object-link-types/conditional-formatting-wifi-rules.png?width=250)

- 对于属性 `type`，我们根据与“A320”、“A321”和“A330”的精确匹配来指定颜色。

![类型颜色示例](https://www.palantir.com/docs/resources/foundry/object-link-types/conditional-formatting-type-rules.png?width=250)

## 添加条件格式

在属性编辑器中：

1. 选择要添加条件格式的属性。
2. 你会在属性窗格中看到条件格式；选择 **Add a rule**（添加规则）按钮。

![添加规则](https://www.palantir.com/docs/resources/foundry/object-link-types/conditional-formatting-cond-form-oma.png?width=600)

1. 单击新创建的默认规则以打开 **Edit conditional formatting rule**（编辑条件格式规则）编辑器。[继续阅读以了解规则编辑器各组件的更多信息](#使用规则编辑器编辑规则)。
2. 修改规则。

![修改规则](https://www.palantir.com/docs/resources/foundry/object-link-types/conditional-formatting-rule.png?width=600)

## 使用规则编辑器编辑规则

![规则编辑器](https://www.palantir.com/docs/resources/foundry/object-link-types/conditional-formatting-rule-editor-string.png?width=700)

| 标签 | 描述 | 用法 |
| --- | --- | --- |
| A | 在 **Standard**（标准）规则、**Always true**（始终为真）规则或 **Math**（数学）规则之间切换。 | 当其他规则都不匹配时，使用 **Always true** 作为兜底。在上面的示例中，当 `type` 的两个值都不匹配时，我们可以用灰色作为兜底情况。当你想对某些属性运行数学运算符时，使用 **Math** 规则。 |
| B | 规则将始终应用于你从中选择 **Add a rule** 的属性；但是，此下拉菜单允许你选择根据另一个属性的值来应用规则。 | 在上述情况下，假设我们希望当 `Performance factor`（性能系数）的值降到某个阈值以下时，将 `Type` 的值显示为红色。我们会在逻辑中选择 `Performance factor` 而不是 `Type`；但颜色仍会显示在 `Type` 上。 |
| C | 可用的比较类型取决于属性的类型。例如，对于字符串，可以使用 **String comparison**（字符串比较）和 **Is null**（为空）。对于数值类型，可以使用 **Numeric range**（数值范围）或 **Exact numeric match**（数值精确匹配）。 | 要在值为空时将 `type` 显示为灰色，请选择此下拉菜单并选择 **Is null** 而不是 **String comparison**。 |
| D | 比较的子类型：**String comparison** 包括 **Is exactly**（精确等于）、**Contains**（包含）、**Starts with**（开头为）等。 | 使用它为所有以“A32”**开头（Starts with）**的飞机 `type` 值着色。 |
| E | 与常量或属性引用进行比较。 | 在本例中，我们专门查找常量“A320”，但也可以添加来自同一对象类型的另一个属性的引用。 |
| F | 在 **True**（为真）或 **False**（为假）规则之间切换。 | 要将所有**非** A320 的飞机显示为蓝色，请将其切换为 **False**。 |
| 格式（Formatting） | 使用 Blueprint 颜色和意图（intents），或添加你自己的自定义颜色。你还可以切换对齐方式。 | 根据需要在十六进制、RGB 或 Blueprint 颜色之间切换；你还可以将框右对齐，以便于阅读数字。 |
| 预览（Preview） | 查看条件格式在各种场景中的显示效果。 | 预览 **Objects table**（对象表）或 **Property card**（属性卡片）。 |

## 复制规则

在属性编辑器中：

1. 选择要从中复制条件格式规则的属性。
2. 你会在属性窗格中看到条件格式；选择 **Copy rules**（复制规则）按钮以打开 **Copy rule**（复制规则）对话框。

![属性编辑器](https://www.palantir.com/docs/resources/foundry/object-link-types/conditional-formatting-copy-rule-select-annotated.png?width=600)

1. 选择要将条件格式规则复制到的属性。

> **ℹ️ 提示**
>
> 如果你要复制到的属性已有自己的条件格式规则，它们将被新规则覆盖。

![复制规则](https://www.palantir.com/docs/resources/foundry/object-link-types/conditional-formatting-copy-rule.png?width=600)

复制的规则将继续引用其原始属性。例如，如果某条规则规定 `wifi` 值为“true”时应显示为绿色，而该规则被复制到 `customer experience`（客户体验）属性，则当对象的 `wifi` 值为“true”时，`customer experience` 属性的值也会显示为绿色。要更改规则引用的属性，只需选择该规则并从规则编辑器的 **Property**（属性）下拉菜单中选择一个新属性。

## 常见问题

### 这能与现有类型类一起使用吗？

条件格式优先于现有类型类（[下一个问题](#这能与-object-views-中的可编辑属性一起使用吗)中详述了一个例外）。如果你同时配置了两者，将显示条件格式。不过，你可以在一个属性上使用条件格式，在另一个属性上使用类型类。

### 这能与 Object Views 中的可编辑属性一起使用吗？

为[内联编辑](https://www.palantir.com/docs/foundry/action-types/inline-edits/#object-explorer-inline-edits)配置的属性支持条件格式。具有旧版 `hubble:editable` 属性类型类的属性禁用条件格式。

---

*原文：[Conditional formatting](https://www.palantir.com/docs/foundry/object-link-types/conditional-formatting/)*
