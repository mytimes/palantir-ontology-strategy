# 函数支撑的动作（Function-backed actions）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/action-types/function-actions-overview/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

在动作类型中，[规则](https://www.palantir.com/docs/foundry/action-types/rules/)定义了在应用动作时对象应如何变化。许多动作类型可以使用简单规则定义，让你能够创建、修改和删除对象，或创建和删除对象之间的链接。

但在某些情况下，简单规则不足以描述你想要进行的更改。例如，你可能想要：

- 修改当前链接在一起的多个对象。例如，你可能想将某个 `Incident` 对象的 `status` 字段设置为 `Closed`，同时将所有链接的 `Alert` 对象的 `status` 设置为 `Resolved`。
- 基于更复杂的逻辑修改对象属性。例如，你可能想根据读取多个对象数据的业务逻辑计算一个值，然后将该值写入对象属性。
- 创建多种不同类型的对象并在它们之间建立链接。

为支持此类用例，动作类型可以配置为调用一个[函数](https://www.palantir.com/docs/foundry/functions/overview/)，由该函数定义对象应如何修改的逻辑。这类动作类型通常称为**函数支撑的动作（function-backed actions）**。通过使用函数，你可以创建任意复杂程度的动作类型，读取任意数量的对象并按你认为合适的方式修改对象。

尽管函数支撑的动作类型非常灵活，但请注意，它们同时受[动作类型限制](https://www.palantir.com/docs/foundry/action-types/scale-property-limits/)和[函数执行限制](https://www.palantir.com/docs/foundry/functions/manage-functions/#enforced-limits)约束。

请按照[教程](https://www.palantir.com/docs/foundry/action-types/function-actions-getting-started/)开始使用函数支撑的动作。

---

*原文：[Function-backed actions](https://www.palantir.com/docs/foundry/action-types/function-actions-overview/)*
