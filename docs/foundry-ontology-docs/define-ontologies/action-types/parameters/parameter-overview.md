# 参数（Parameters）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/action-types/parameter-overview/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

**参数**是动作类型的输入。它们是 **Rules（规则）** 与其他 Foundry 应用（如 [Workshop](https://www.palantir.com/docs/foundry/workshop/overview/)、[Slate](https://www.palantir.com/docs/foundry/slate/overview/) 和 [Object Views](https://www.palantir.com/docs/foundry/object-views/overview/)）之间的接口。参数被视为包含外部值的变量。每个参数由一个类型定义，该类型决定它可以接受什么样的值。除类型外，参数还有各种其他潜在配置。每个参数都可以单独配置：是否在表单中暴露，或者是否可由用户更改。

参数在动作类型中传递值，并可在规则中被引用，以将值回传到对象、链接或副作用上；可在提交条件中被引用，以检查动作是否可以提交；可用于在动作更改对象属性之前访问其当前值；或在覆盖（overrides）中被引用，以更改后续参数的配置。

> **ℹ️ 提示：示例**
>
> 在允许用户修改所选工单状态的动作类型中，参数可以采用 `Ticket` 对象类型的形式。`Status` 参数被定义为字符串。提交动作时，对象类型参数将采用所选 `Ticket` 对象的值，`Status` 参数则包含新状态。然后动作类型将两个参数值传递给规则并执行，以编辑对象。

> **ℹ️ 提示：示例**
>
> 作为 Workshop 中的变量，`previous_status` 可以采用所选 `Ticket` 对象的 `Status` 属性的当前值。这可以传递给动作中的隐藏参数 `Previous Status`，而 `Status` 参数可以包含更新后的状态。提交动作时，动作类型会将 `Previous Status` 和 `Status` 两个值传递给规则并执行，以编辑对象。

---

*原文：[Parameters](https://www.palantir.com/docs/foundry/action-types/parameter-overview/)*
