# 在平台中使用函数（Use functions in the platform）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/use-functions/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

本节介绍在整个 Foundry 平台中使用函数的各种方式。此列表基本保持最新，但可能还有未收录在此的其他函数使用方式。

## Workshop

[Workshop](https://www.palantir.com/docs/foundry/workshop/overview/) 以多种方式支持与函数集成，从而可以在 Workshop 构建的模块中使用自定义逻辑。

### 变量

大多数 Workshop [变量](https://www.palantir.com/docs/foundry/workshop/concepts-variables/)都可以由函数支撑，允许应用构建者使用函数计算值，然后在整个 Workshop 中使用这些值。默认情况下，当变量所依赖的另一个变量更新时，该变量的值会重新计算。这使得值能够响应用户反馈灵活地重新计算——例如，当用户编辑某个输入组件时，依赖它的函数支撑对象集变量会自动重新计算。

> **ℹ️ 提示**
>
> 函数支撑的变量会为相同输入缓存结果，而不是重新计算。你可以配置变量的[重新计算行为](https://www.palantir.com/docs/foundry/workshop/concepts-variables/#recompute-variable-value)，或使用[设置变量值](https://www.palantir.com/docs/foundry/workshop/concepts-events/#set-variable-value)事件来显式触发重新计算。

要了解更多，请查看[关于如何使用函数支撑 Workshop 变量的教程](https://www.palantir.com/docs/foundry/workshop/functions-use/#function-backed-variables-in-workshop)。

下面是 Workshop 变量类型与其在 TypeScript 中对应类型之间的映射。每种给定类型的 Workshop 变量都可以由返回所列有效类型之一的函数支撑。[了解更多关于可用函数类型的文档。](https://www.palantir.com/docs/foundry/functions/types-reference/)

- 布尔值（Boolean）：`boolean`
- 字符串（String）：`string`
- 数值（Numeric）：`Integer`、`Long`、`Float`、`Double`
- 日期（Date）：`LocalDate`
- 时间戳（Timestamp）：`Timestamp`
- 数组（Array）：`BaseType[]` 或 `Set<BaseType>`
- 对象集（Object Set）：`ObjectSet<ObjectType>`（推荐）、`ObjectType[]` 或 `Set<ObjectType>`

### 对象表格：派生属性

Workshop 的**对象表格**组件可以配置为计算函数支撑的列，该列可以根据用户输入更新，并在最终用户滚动表格时即时重新计算。你可以查看[使用此功能的完整教程](https://www.palantir.com/docs/foundry/workshop/widgets-object-table/#function-backed-columns)。

函数支撑的列可以返回媒体引用。当函数返回带有 `mimeType` 和 `reference` 属性的 `mediaReference` 类型时，对象表格会自动将结果渲染为媒体，而不是原始数据。这支持基于自定义函数逻辑在表格列中动态显示媒体。

### 图表：派生聚合

Workshop 的**图表：XY** 组件支持使用函数支撑的聚合按需派生聚合值。如果你希望聚合数据基于用户选择，这会很有用。要在图表组件中使用函数，只需点击以配置图表图层，然后选择*函数聚合*。

![图表中的函数聚合](https://www.palantir.com/docs/resources/foundry/functions/use-functions-chart.png)

[Aggregation API 的参考](https://www.palantir.com/docs/foundry/functions/types-reference/#aggregation-types)可供查阅。对于更高级的用例，你可能需要阅读[如何计算自定义聚合](https://www.palantir.com/docs/foundry/functions/create-custom-aggregation/)的文档。

## 动作

[动作类型](https://www.palantir.com/docs/foundry/action-types/overview/)使应用能够以灵活且安全的方式修改 Foundry 本体中的对象，并发送外部通知、触发副作用。在动作中，函数提供了完全的灵活性，使代码作者能够定义应如何更新对象，或应如何配置副作用。

### 函数支撑的动作

函数支撑的动作使用[本体编辑](https://www.palantir.com/docs/foundry/functions/api-ontology-edits/) API 来定义对象更新逻辑。这使你能够在代码中表达复杂的编辑——例如，更新链接到某个起始对象的每个对象。[查看端到端使用函数支撑动作的教程。](https://www.palantir.com/docs/foundry/action-types/function-actions-getting-started/)

### 副作用：通知

动作可以配置为向指定用户发送通知。你可以使用函数来计算哪些用户应接收通知，以及通知本身的内容。这提供了灵活性，例如加载存储在对象中的接收者用户 ID，或基于对象数据渲染邮件内容。要了解更多，请查阅[关于通知的完整文档](https://www.palantir.com/docs/foundry/action-types/notifications/)和[如何使用函数配置通知的指南](https://www.palantir.com/docs/foundry/functions/configure-notifications/)。

### 副作用：Webhook

动作还可以配置为在应用时触发 Webhook。Webhook 使 Foundry 能够与其他系统集成，使用户应用的动作能够回写到 Foundry 之外的 API。你可以使用函数来计算应发送给将要执行的 Webhook 的参数，从而实现诸如基于对象数据填充 Webhook 参数之类的工作流。[查看关于 Webhook 的完整文档。](https://www.palantir.com/docs/foundry/action-types/webhooks/)

## Slate

[Slate](https://www.palantir.com/docs/foundry/slate/overview/) 在 **Platform** 标签页中原生支持查找和使用函数。编辑 Slate 文档时，打开 Platform 标签页并在左下角添加一个 **Foundry Function**。现在，你可以搜索函数、配置参数，并在 Slate 文档中使用结果。

![Slate 中的 Foundry 函数](https://www.palantir.com/docs/resources/foundry/functions/use-functions-slate.png)

请注意，由于历史原因，Slate 产品有自己的“函数”概念，即位于每个 Slate 文档中的 JavaScript 逻辑片段。这就是为什么函数产品被称为“Foundry 函数”并位于 **Platform** 标签页下。Slate 的函数能力允许在文档内快速、轻松地操作数据，但不原生支持对象。

你可以将 Slate 函数和 Foundry 函数结合使用——例如，你可以从 Foundry 函数返回数据并在 Slate 函数中操作它，或使用 Slate 函数计算应传入 Foundry 函数的参数。

## Quiver

Quiver 中的[对象集图](https://www.palantir.com/docs/foundry/quiver/objects-chart-drilldown/#code-function-categorical-plot)使用与 Workshop 的图表：XY 组件相同的底层组件。因此，你也可以在 Quiver 分析中使用函数支撑的聚合。

**代码函数对象集**和**代码函数分类图**卡片支持带对象参数的函数。通过引用另一个卡片或在卡片编辑器中选择对象来设置参数值。

你还可以从 Quiver 搜索对话框添加代码函数。**代码函数**类别支持对象集、分类图、值和时间序列输出。

## Automate

[Automate](https://www.palantir.com/docs/foundry/automate/overview/) 允许你创建函数支撑的自动化，在满足指定条件时自动执行函数。

配置函数效果时，你可以选择函数并指定其版本。对于稳定版本（1.0.0 及以上），你可以启用自动升级到兼容版本。Automate 中的函数异步执行，最长可运行 4 小时。

请注意，具有本体编辑返回类型的函数在 Automate 中用作效果时，不会应用其中的编辑。要了解更多，请参阅[函数效果文档](https://www.palantir.com/docs/foundry/automate/effect-function/)。

---

*原文：[Use functions in the platform](https://www.palantir.com/docs/foundry/functions/use-functions/)*
