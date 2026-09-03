# 动作类型（Action types）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/action-types/overview/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

在本体中，用户可以通过应用动作来更改对象、属性和链接。动作是单个事务，它基于用户定义的逻辑更改一个或多个对象的属性。动作让用户能够围绕总体目标（而非具体的属性编辑）来处理和管理数据。

**动作类型（action type）**是一项定义，描述用户可以一次性对对象、属性值和链接执行的一组更改或编辑；它还包括动作提交时随之发生的副作用行为。

**示例：**

你可以创建一个 `Assign Employee`（分配员工）动作类型，定义用户如何更改给定 `Employee` 对象的 `role` 属性值。该动作类型可以要求一个参数定义，使用户能够以标准化表单输入新角色，并且可以包含如何自动在 `Employee` 对象与新 `Manager` 对象之间创建链接的规则。

该动作还可以：

- 包含通知副作用，将更改通知前任和新任经理。
- 校验只有授权员工（例如人力资源部门的员工）才能执行该动作。

设置好这些参数后，HR 员工便可以执行动作，例如将 “Melissa Chang” 切换为 “Product Manager”（产品经理）`role`。

Foundry 本体并非抽象的数据模型，而是将每个本体概念映射到组织的实际数据，使这一数据资产能够驱动真实世界的应用。随着用户的决策和洞察以本体编辑的形式记录下来，这一数据资产的丰富度和价值也不断增长。

当用户执行动作时，对对象、属性值和链接所做的任何更改都将提交到本体，并反映在所有用户应用中。同样，相同的动作逻辑和验证可以在所有面向用户的应用中使用，确保对本体的编辑保持一致。包含用户编辑的对象数据最新版本将保存在对象类型的回写数据集中。

请通过学习如何[创建动作类型](https://www.palantir.com/docs/foundry/action-types/getting-started/)和[探索其他动作类型](https://www.palantir.com/docs/foundry/action-types/explore-action-types/)开始，或了解[规则](https://www.palantir.com/docs/foundry/action-types/rules/)、[参数](https://www.palantir.com/docs/foundry/action-types/parameter-overview/)和[提交条件](https://www.palantir.com/docs/foundry/action-types/submission-criteria/)。

---

*原文：[Action types](https://www.palantir.com/docs/foundry/action-types/overview/)*
