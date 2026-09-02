# 入门（Getting started）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/action-types/getting-started/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

在本指南中，我们将创建一个简单的动作类型，用于更改工单的优先级。

我们将配置提交条件，确保优先级为 `P0`、`P1` 或 `P2`，且工单状态为 `Open`。

## 前提条件

在本指南中，我们将使用一个 `Demo Ticket` 对象类型，它有四个属性：

- `Ticket ID`
- `Title`
- `Priority`
- `Status`

我们还有两个可用的演示对象：

| Ticket ID | Title | Status | Priority |
| --- | --- | --- | --- |
| PDS-123 | Demo Ticket One | Open | P2 |
| PDS-124 | Demo Ticket Two | Closed | P1 |

如果需要，你可以在你的本体中重新创建这些对象，但这不是必需的。

请注意，用户要能够执行动作类型配置中定义的动作，[还需要额外配置](https://www.palantir.com/docs/foundry/object-link-types/allow-editing/#set-up-the-prerequisites)。如果运行 Object Storage v2，用户必须通过开关启用编辑。如果运行 Object Storage v1（Phonograph），则必须创建回写数据集。请注意，[Object Storage v1](https://www.palantir.com/docs/foundry/object-databases/object-storage-v1/) 处于[计划弃用](https://www.palantir.com/docs/foundry/platform-overview/development-life-cycle/)的开发阶段；请[迁移到 Object Storage v2](https://www.palantir.com/docs/foundry/object-backend/osv1-osv2-migration/)。

## 创建新动作类型

我们首先创建一个用于更改工单优先级的新动作类型。在 Ontology Manager 中，选择左侧边栏的 **Action types（动作类型）**，然后在视图右上角选择 **New action type（新建动作类型）**。

![创建新动作类型](https://www.palantir.com/docs/resources/foundry/action-types/actions_wizard.png)

创建向导允许你配置动作类型最重要的特性。在 **Action type（动作类型）** 步骤中，打开 **Object（对象）** 选项卡，选择 `Demo Ticket` 对象类型，然后在 **Object actions（对象动作）** 下选择 **Modify object(s)（修改对象）**。在 **Mapping（映射）** 步骤中，通过选择 **Add property（添加属性）** 添加 `Priority` 属性。在 **Metadata（元数据）** 步骤中，为你的动作类型输入 **Action type name（动作类型名称）**。继续到最后一步并选择 **Create（创建）**。

现在你可以看到动作类型的完整详细视图。你可以进行额外调整，例如在 **Overview（概览）** 选项卡中添加 **Description（描述）**，或在 **Rules（规则）** 选项卡中添加要修改的其他属性。

## 编辑参数

选择 **Parameters（参数）** 选项卡以查看参数概览。`Ticket` 和 `Priority` 参数已根据 **Rule（规则）** 创建。

![动作表单](https://www.palantir.com/docs/resources/foundry/action-types/actions_form.png)

选择 `Priority` 参数以限制它可以接受的值。将约束从 **User input（用户输入）** 更改为 **Multiple choice（多选）**。这将允许你选择该参数可以选择哪些值。添加 `P0`、`P1` 和 `P2` 作为选项。如果你现在将动作应用于对象，就可以将工单的优先级更改为 `P0`、`P1` 或 `P2`。接下来你将添加提交条件，将你限制为只能更改未关闭工单的优先级。

![Priority 参数](https://www.palantir.com/docs/resources/foundry/action-types/actions_constraints.png)

## 添加提交条件

从边栏打开 **Security & Submission Criteria（安全与提交条件）** 选项卡中的提交条件区域。在 **Execution（执行）** 区域选择 **Condition（条件）** 以创建新条件。使用 **Parameter（参数）** 条件模板，对 `Ticket` 对象参数的 `Status` 属性设置条件。使用 `is` 运算符，你可以对工单状态与特定值 `Open` 进行精确字符串比较。

![提交条件](https://www.palantir.com/docs/resources/foundry/action-types/actions_submission_criteria.png)

添加失败消息，以便用户可以看到动作失败的原因。你的动作定义现已完成，你可以将其配置为显示在 Object Explorer 中 Object View 旁边。

## 将动作添加到 Object View

前往 **Demo Ticket One** 并编辑其 Object View。在顶部添加一个新组件，选择 **Actions（动作）** 组件。在边栏中选择 **Add Item（添加项）**。从 Ontology Manager 复制动作 RID 并粘贴到 Action RID 字段中。将标签命名为“Change Ticket Priority”（更改工单优先级）。

![将动作添加到 Object View](https://www.palantir.com/docs/resources/foundry/action-types/getting_started_add_RID.png)

默认情况下，动作表单会将每个参数显示为表单中的一个字段，包括 `Ticket` 参数。此外，动作并不知道应该为 `Ticket` 参数填入当前对象。我们将配置动作表单以隐藏工单字段（这样用户就无法更改其他工单的状态），并将其值设置为当前对象。
在 **Default value（默认值）** 下，选择 **Add Item（添加项）**。输入 `Ticket` 参数的参数 ID——在本教程中，我们将其设置为 `ticket`。将值类型更改为 **Environment variable（环境变量）** 并选择 **Current object（当前对象）**。最后，将显示选项更改为 **Hidden（隐藏）**。

![配置动作表单](https://www.palantir.com/docs/resources/foundry/action-types/getting_started_configure_action_form.png)

现在你会在预览页面上看到动作按钮：

![预览页面上的动作按钮](https://www.palantir.com/docs/resources/foundry/action-types/getting_started_preview_page.png)

你现在可以保存并发布 Object View。

> **ℹ️ 提示**
>
> 在应用动作之前，你可以在 Ontology Manager 中使用[测试运行](https://www.palantir.com/docs/foundry/action-types/test-run/)验证其提交条件、规则和生成的编辑。

## 应用动作

访问一个未关闭的工单，选择我们配置的 **Change Ticket Priority** 按钮。你应该会看到动作表单出现在视图上方。点击 **Priority** 字段将显示我们在参数上配置的那一条提交条件：

![用动作更改工单优先级](https://www.palantir.com/docs/resources/foundry/action-types/getting_started_apply_action.png)

选择一个优先级并选择 **Submit（提交）**。表单将消失，对象视图将更新为新的优先级。我们的提交条件规定，不应该能对已关闭的工单运行此动作。如果我们打开已关闭的 Demo Ticket Two，会看到以下内容：

![提交条件阻止动作在已关闭工单上运行](https://www.palantir.com/docs/resources/foundry/action-types/getting_started_testing_validation.png)

## 解决冲突的用户编辑（动作）与数据源更新

Foundry 本体中的对象实例可以由输入数据源和用户编辑/动作共同创建和修改。当单个对象实例（即具有特定主键值的一行或一个对象）同时从输入数据源和用户编辑接收数据时，必须通过冲突解决策略透明地解决这些接收到的值。

有两种解决冲突的策略：

- 策略 1：应用用户编辑（默认）
- 策略 2：应用最新值（你的注册实例上可能不可用）

[详细了解如何解决冲突的用户编辑与数据源更新。](https://www.palantir.com/docs/foundry/object-edits/how-edits-applied/#resolve-conflicting-user-edits-and-datasource-updates)

## 后续步骤

- [探索你可以构建的其他动作类型，包括链接、函数、Webhook 和场景动作。](https://www.palantir.com/docs/foundry/action-types/explore-action-types/)
- [详细了解动作权限。](https://www.palantir.com/docs/foundry/action-types/permissions/)
- [创建函数支撑的动作。](https://www.palantir.com/docs/foundry/action-types/function-actions-getting-started/)
- [在平台的其他地方使用动作。](https://www.palantir.com/docs/foundry/action-types/use-actions/)
- [解决冲突的用户编辑（动作）与数据源更新](https://www.palantir.com/docs/foundry/object-edits/how-edits-applied/#resolve-conflicting-user-edits-and-datasource-updates)

---

*原文：[Getting started](https://www.palantir.com/docs/foundry/action-types/getting-started/)*
