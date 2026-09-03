# 覆盖（Overrides）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/action-types/parameters-override/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

覆盖用于在特定情况下更改参数的行为和配置。使用覆盖，参数和表单可以变得更加灵活，无需为细微差异单独配置多个动作类型。恰当使用覆盖可以引导用户完成动作提交，从而改善用户体验。

例如，假设你有一个更改支持工单对象状态的动作类型，并且希望将动作提交限制为经理和经办人（assignee）。经办人可以更改状态，而经理则必须提供理由。使用覆盖，可以让 `Justification reason`（理由说明）参数对经理变为必填且可见，而对经办人则隐藏且可选。

## 添加和编辑覆盖

你可以在参数视图的不同位置添加和编辑覆盖。添加新覆盖最简单的方式是直接在**常规（General）**区域的**值（Value）**选项卡中操作。在三个选项中的任意一个上点击**添加覆盖（Add override）**，即可通过弹窗轻松创建覆盖，弹窗会根据所选选项自动配置覆盖。**常规**区域还会显示各个选项是否已配置覆盖以及已配置的数量。要编辑现有覆盖，请选择覆盖按钮。

![覆盖弹窗](https://www.palantir.com/docs/resources/foundry/action-types/override_pop_up.png)

你也可以通过**覆盖（Overrides）**选项卡手动添加覆盖。覆盖选项卡展示为该参数配置的所有覆盖的概览。你可以从这里添加覆盖块，或向现有块添加新的条件或覆盖。

![覆盖选项卡](https://www.palantir.com/docs/resources/foundry/action-types/override_tab.png)

## 覆盖块

覆盖块是覆盖的基本构成单位。它同时定义了条件（显示在 “if” 部分）和覆盖（显示在 “then” 部分）。每个块的头部显示该逻辑的摘要。每个参数可以包含多个覆盖块，但如果多个块同时为真，只有第一个块会被执行。

![覆盖块](https://www.palantir.com/docs/resources/foundry/action-types/override_block.png)

### “If” 与条件

每个块可以包含一个或多个条件。要详细了解条件及其配置方式，请参阅[提交条件文档中关于条件的部分](https://www.palantir.com/docs/foundry/action-types/submission-criteria/#conditions)。覆盖条件与提交条件之间的唯一区别是：覆盖条件只能引用在表单层级中位于当前参数上方的参数。

### “Then” 与覆盖

**Then** 部分定义了当块的条件满足时将应用的覆盖。每个块的 **Then** 部分可以包含多个覆盖，它们将一起应用。覆盖可以更改参数的约束、可见性、必填性和默认值配置。如果某个覆盖配置的值与参数上已设置的默认值相同，该覆盖本身上会显示一条警告。

### 多个覆盖块

你可以为单个参数添加多个覆盖块。如果多个块为真，只有第一个覆盖会被执行。

---

*原文：[Overrides](https://www.palantir.com/docs/foundry/action-types/parameters-override/)*
