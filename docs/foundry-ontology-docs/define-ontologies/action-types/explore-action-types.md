# 探索其他动作类型（Explore other action types）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/action-types/explore-action-types/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

[入门](https://www.palantir.com/docs/foundry/action-types/getting-started/)教程构建了一种动作类型：一条修改单个对象上某个属性的规则。动作类型能表达的远不止属性编辑。同一个配置界面还允许你创建和删除对象、管理对象之间的链接、在函数中运行自定义逻辑、调用外部系统、通知用户、触发构建，以及合并场景编辑。

本页逐一介绍这些能力，说明何时使用，并链接到参考文档。示例继续沿用[入门](https://www.palantir.com/docs/foundry/action-types/getting-started/)教程中的 `Demo Ticket` 对象类型，因此你可以在同一个本体中跟着操作。

## 前提条件

- 完成[入门](https://www.palantir.com/docs/foundry/action-types/getting-started/)教程。该教程涵盖了创建向导、参数和提交条件，本页其余部分假定你已了解这些内容。
- 确认你计划更改的对象类型已[配置为可编辑](https://www.palantir.com/docs/foundry/object-link-types/allow-editing/#set-up-the-prerequisites)。

## 在何处配置规则

下文的各项能力通过规则进行配置，而规则可以在两个位置添加。

创建向导用于配置新动作类型的第一条规则。在**动作类型**步骤中，打开**对象**选项卡，选择对象类型，然后在**对象动作**下选择该动作所做的更改。在**映射**步骤中选择要包含的属性。[入门](https://www.palantir.com/docs/foundry/action-types/getting-started/)教程详细演示了这一流程。

![动作类型创建向导的第一步，你可以在其中选择对象类型以及动作所做的更改。](https://www.palantir.com/docs/resources/foundry/action-types/actions_wizard.png)

对于已存在的动作类型，在 [Ontology Manager](https://www.palantir.com/docs/foundry/ontology-manager/overview/) 中打开它，选择**规则**选项卡，然后选择**添加新规则**来选择你需要的规则。本页描述的每条规则都可以在这里添加。

![动作类型“规则”选项卡上的“添加新规则”下拉菜单。](https://www.palantir.com/docs/resources/foundry/action-types/side_effects_notification_tutorial_edit_action_rules.png?width=500)

单个动作类型可以组合多条规则。[函数规则](#使用函数运行自定义逻辑)是个例外：它不能与其他本体规则组合。

## 选择规则

| 目标 | 要添加的规则 | 了解更多 |
| --- | --- | --- |
| 创建新对象 | **创建对象** | [创建和删除对象](#创建和删除对象) |
| 仅在用户未提供对象时创建对象 | **创建或修改对象** | [创建和删除对象](#创建和删除对象) |
| 删除现有对象 | **删除对象** | [创建和删除对象](#创建和删除对象) |
| 关联两个对象，或移除二者之间的关系 | **创建链接**或**删除链接** | [管理对象之间的链接](#管理对象之间的链接) |
| 表达规则无法描述的逻辑 | **运行函数** | [使用函数运行自定义逻辑](#使用函数运行自定义逻辑) |
| 向 Foundry 之外的系统发送请求 | **Webhook** | [使用 Webhook 调用外部系统](#使用-webhook-调用外部系统) |
| 在应用任何编辑之前调用外部系统 | **回写 Webhook** | [使用 Webhook 调用外部系统](#使用-webhook-调用外部系统) |
| 告知用户发生了某项更改 | **通知** | [在动作应用时通知用户](#在动作应用时通知用户) |
| 在动作应用时重新计算数据集 | **调度** | [触发构建](#触发构建) |
| 提交场景中暂存的编辑 | **应用场景** | [合并场景编辑](#合并场景编辑) |
| 更改实现某个接口的任意类型的对象 | 接口规则 | [针对接口执行操作](#针对接口执行操作) |

[规则](https://www.palantir.com/docs/foundry/action-types/rules/)参考文档详细描述了每条规则。

## 创建和删除对象

入门教程修改的是用户选择的对象。若要改为创建新工单，请添加一条**创建对象**规则并选择 `Demo Ticket` 对象类型。该对象类型的主键（本例中为 `Ticket ID`）是必填属性，必须填写。通过选择**添加属性**，添加新对象所需的其他属性，例如 `Title`、`Status` 和 `Priority`。

你添加的每个属性都会自动创建一个同名参数，并将该属性映射到该参数。你也可以将属性映射到对象引用参数的某个属性、映射到用户无法更改的静态值，或者映射到当前用户或提交时间。有关所有映射选项，请参阅[值与参数](https://www.palantir.com/docs/foundry/action-types/rules/#values-and-parameters)。

两条相关规则覆盖了其余情形：

- **创建或修改对象：** 修改在对象引用参数中提供的对象。当未提供对象时，将使用自动生成的唯一 ID 或用户提交的主键创建一个新对象。
- **删除对象：** 删除在对象引用参数中提供的对象。此规则不能引用同一动作所创建的对象。

> **ℹ️ 提示**
>
> 当一个动作类型包含多条规则时，Foundry 会将它们编译为针对每个对象的单次编辑，因此规则的顺序会影响结果，且某些组合不受支持。在同一个动作类型中组合创建、修改和删除规则之前，请参阅[无效组合](https://www.palantir.com/docs/foundry/action-types/rules/#invalid-combinations)。

## 管理对象之间的链接

动作既可以维护对象的属性，也可以维护对象之间的关系。你需要哪条规则取决于[链接类型](https://www.palantir.com/docs/foundry/object-link-types/link-types-overview/)的基数。

对于多对多链接类型，添加一条**创建链接**规则来关联通过对象引用参数提供的两个对象，或添加一条**删除链接**规则来移除它们之间的关系。

对于一对一或一对多链接类型，关系存储在对象的外键属性中。请使用**修改对象**规则来设置或清除该属性，而不是使用链接规则。

你还可以在单个动作中创建对象及其多对多链接。为某个具有多对多链接类型的对象类型配置一条**创建对象**规则，然后在**添加属性**下方选择**添加链接**，以选择链接类型并配置链接。

## 使用函数运行自定义逻辑

规则以声明式映射的方式描述编辑，这并不总是足够。当你想要修改链接到用户所选对象的每一个对象、通过读取多个对象的业务逻辑计算属性值，或者创建多种类型的对象并将它们链接到一起时，可以考虑用[本体编辑函数](https://www.palantir.com/docs/foundry/functions/edits-overview/)来支撑该动作。

添加一条单独的**运行函数**规则，然后选择已发布的函数及其要使用的版本。函数的每个输入都会创建为动作类型上的一个参数，之后你可以像约束任何其他参数一样约束它。**运行函数**规则不能与其他[本体规则](https://www.palantir.com/docs/foundry/action-types/rules/#ontology-rules)组合，因为函数代码本身就可以表达它们所能表达的一切。

完整演练请遵循[函数支撑的动作教程](https://www.palantir.com/docs/foundry/action-types/function-actions-getting-started/)，包括如何[更改动作引用的函数版本](https://www.palantir.com/docs/foundry/action-types/function-actions-getting-started/#changing-function-version)。默认情况下，动作停留在你所选的版本上。启用[自动升级](https://www.palantir.com/docs/foundry/action-types/function-actions-getting-started/#auto-upgrades)可以改为在运行时解析版本范围；在启用之前，请先参阅其中描述的权限和破坏性变更权衡。

## 使用 Webhook 调用外部系统

当 Foundry 之外的系统是某个流程环节的事实来源时，Webhook 规则会在应用动作时向该系统发送请求。动作参数可以作为输入传递给 Webhook。

发送请求的规则有两条，你添加的规则决定了请求何时运行：

- **Webhook：** 请求在对象编辑应用之后发送。从**副作用**组中添加此规则。一个动作类型可以包含多条此类规则；失败不会展示给最终用户，而此时用户可能已经看到了成功消息。
- **回写 Webhook：** 请求在任何其他规则被求值之前发送。从**外部请求**组中添加此规则。如果请求失败，则不会应用任何编辑，最终用户会看到失败。一个动作类型只能包含一条此规则，且其[输出参数](https://www.palantir.com/docs/foundry/action-types/webhooks/#output-parameters)可供后续规则使用。

在规则中选择 Webhook 之前，该 Webhook 必须已存在于 [Data Connection](https://www.palantir.com/docs/foundry/data-connection/webhooks-setup/) 中。配置步骤请遵循[设置 Webhook](https://www.palantir.com/docs/foundry/action-types/set-up-webhook/)；有关输入参数、输出参数和身份验证选项，请参阅 [Webhook](https://www.palantir.com/docs/foundry/action-types/webhooks/)。

## 在动作应用时通知用户

**通知**规则告知用户动作已应用。接收者可以是固定的一组用户，也可以从某个对象参数的属性派生；通知内容可以是引用动作参数的模板。每个接收者会根据自己的通知偏好，收到平台内推送通知、电子邮件，或两者都收到。

通知在动作的所有编辑都应用之后发送，但其内容是根据这些编辑之前的本体状态生成的。完整示例请遵循[设置通知](https://www.palantir.com/docs/foundry/action-types/set-up-notification/)；有关所有接收者和内容选项，请参阅[通知](https://www.palantir.com/docs/foundry/action-types/notifications/)。

## 触发构建

**调度**规则会在动作应用时触发某个[调度](https://www.palantir.com/docs/foundry/data-integration/schedules/)的构建。从**高级**组中添加此规则。这使得本体中的最终用户工作流可以在动作中重新计算数据集。该调度必须处于[项目范围模式](https://www.palantir.com/docs/foundry/data-integration/schedules/#project-scope)。

带有调度规则的动作类型会在构建开始后应用其本体编辑，而不是等待构建完成。请参阅[触发调度构建](https://www.palantir.com/docs/foundry/action-types/trigger-schedule-build/)，了解如何向参数化调度传递值，以及如何在对象上记录生成的调度运行 RID。

## 合并场景编辑

在[场景](https://www.palantir.com/docs/foundry/ontology/overview-ontology-scenario/)中所做的编辑仅存在于该场景的沙箱中，不会影响主本体。合并动作将所有暂存的编辑作为单个事务提交。合并动作需要一个持有场景 RID 的**场景**参数，该值通常从 Workshop 变量或 Ontology SDK 应用传入。

创建合并动作有两种途径：

- 创建新的动作类型，并在创建向导中选择**场景**选项卡，然后通过添加相关的**对象类型**和**链接类型**来定义合并的范围。
- 在已存在的动作类型上，从其**规则**选项卡添加一条**应用场景**规则。

在这两种情况下，都请使用动作类型的**安全与提交条件**选项卡来定义允许哪些用户运行合并。完整配置请参阅[合并场景](https://www.palantir.com/docs/foundry/ontology/merge-scenario/#configure-the-merge-action)。

> **ℹ️ 提示**
>
> 本体场景目前处于开发的 [Beta](https://www.palantir.com/docs/foundry/platform-overview/development-life-cycle/) 阶段，可能在你的注册实例上不可用。请联系 Palantir 支持团队申请场景的访问权限。

## 针对接口执行操作

接口为每种本体编辑都提供了专用规则：

- **创建接口对象**
- **修改接口对象**
- **删除接口对象**
- **在接口对象上创建链接**
- **在接口对象上删除链接**

每条规则面向实现该接口的任意对象类型，而非某个特定对象类型。修改和删除规则接受接口引用参数来代替对象引用参数。请参阅[针对接口的动作](https://www.palantir.com/docs/foundry/action-types/actions-on-interfaces/)。

## 在规则中映射结构体属性

[结构体属性](https://www.palantir.com/docs/foundry/object-link-types/structs-overview/)的值来自结构体参数，其嵌套字段与该属性的字段一一对应。请在规则内映射该属性，并确保每个字段都被映射。请参阅[针对结构体的动作](https://www.palantir.com/docs/foundry/action-types/actions-on-structs/)。

## 后续步骤

- [通过测试运行验证动作类型。](https://www.palantir.com/docs/foundry/action-types/test-run/)
- [查阅完整的规则参考。](https://www.palantir.com/docs/foundry/action-types/rules/)
- [用参数塑造用户可提交的内容。](https://www.palantir.com/docs/foundry/action-types/parameter-overview/)
- [用提交条件限制动作何时可以应用。](https://www.palantir.com/docs/foundry/action-types/submission-criteria/)
- [在平台的其他地方使用动作。](https://www.palantir.com/docs/foundry/action-types/use-actions/)

---

*原文：[Explore other action types](https://www.palantir.com/docs/foundry/action-types/explore-action-types/)*
