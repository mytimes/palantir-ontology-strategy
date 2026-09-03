# 函数（Functions）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/overview/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

**函数**使代码编写者能够编写可在运营场景中快速执行的逻辑，例如用于支持决策流程的仪表板和应用。该逻辑在隔离环境中的服务器端执行。

值得注意的是，函数对基于本体编写逻辑提供了一等公民级的支持。这包括支持读取各种对象类型的属性、遍历链接，以及灵活地进行本体编辑。

函数的常见用例包括：

- 在 [Workshop](https://www.palantir.com/docs/foundry/workshop/functions-use/) 中返回对象集或变量值以供使用。
- 使用 [Workshop 的函数支撑列](https://www.palantir.com/docs/foundry/workshop/widgets-object-table/#function-backed-columns)在派生表列中显示转换后的值。
- 聚合对象类型值以显示为 [Workshop 图表](https://www.palantir.com/docs/foundry/workshop/widgets-chart/#function-aggregations-function-backed-layers)。
- 通过[函数支撑的动作](https://www.palantir.com/docs/foundry/action-types/function-actions-overview/)表达对本体的复杂编辑，从而更新许多对象。
- 在后端运行逻辑，返回要在 [Slate](https://www.palantir.com/docs/foundry/slate/overview/) 前端显示的信息。
- 计算自定义指标或聚合，以在 [Quiver](https://www.palantir.com/docs/foundry/quiver/overview/) 中显示。
- 通过[外部函数](https://www.palantir.com/docs/foundry/functions/webhooks/)查询外部系统以丰富本体中的对象。
- 以增量方式流式返回结果，从而打造实时体验，尤其是在使用[语言模型](https://www.palantir.com/docs/foundry/functions/streaming-functions/)时。
- 在 [Pipeline Builder](https://www.palantir.com/docs/foundry/functions/python-functions-builder/) 中将 Python 函数用作 sidecar 容器。

函数支持的语言是 [TypeScript ↗](https://www.typescriptlang.org/docs/handbook/basic-types.html) 和 [Python ↗](https://www.python.org/)。

有关各语言的功能支持以及如何选择语言或语言版本的更多信息，请参阅[语言功能支持规范](https://www.palantir.com/docs/foundry/functions/language-feature-support/)。

要开始在 Foundry 中使用函数，我们推荐以下教程：

- [TypeScript v1 函数入门](https://www.palantir.com/docs/foundry/functions/typescript-v1-getting-started/)
- [TypeScript v2 函数入门](https://www.palantir.com/docs/foundry/functions/typescript-v2-getting-started/)
- [Python 函数入门](https://www.palantir.com/docs/foundry/functions/python-getting-started/)

> **✅ 说明：Palantir Learning 门户**
>
> 在 learn.palantir.com 上试用我们的[“速通：你的第一个本体函数”课程 ↗](https://learn.palantir.com/speedrun-your-first-ontology-function)。

---

*原文：[Functions](https://www.palantir.com/docs/foundry/functions/overview/)*
