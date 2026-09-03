# 设置通知（Set up a notification）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/action-types/set-up-notification/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

本教程演示如何设置一个带通知的动作。

我们将使用一个动作，它会更新 `Alert`（警报）对象的 `Priority`（优先级）属性，并通知 `Assignee`（经办人，一名 Foundry 用户），该用户存储为该 Alert 对象上的一个属性。如果你想跟着操作，你需要已经完成以下设置：

- 一个具有正确属性且配置为可通过动作编辑的对象
- 一个动作，它接收你的一个对象以及一个包含新优先级的参数，并更新指定对象上的优先级属性。如果你之前学习过[动作入门教程](https://www.palantir.com/docs/foundry/action-types/getting-started/)，你应该已经完成了这一设置。

如果你刚开始管理对象，可以阅读[如何设置对象类型](https://www.palantir.com/docs/foundry/object-link-types/create-object-type/)。

## 前提条件

### 完成入门教程

本教程假设你已经完成了动作的[入门](https://www.palantir.com/docs/foundry/action-types/getting-started/)教程。

### 向你的对象类型添加经办人属性

在本教程中，你需要在 `Alert` 对象上拥有一个名为 `Case Managers`（案件经理）的属性，其中包含当前指派用户的 Foundry 用户 ID。通常，如果你使用动作来构建工作流，你可以通过应用中的用户选择器组件捕获并存储用户 ID。这些 ID 在 Foundry 中显示时，会呈现为完整的用户名。

## 添加通知

首先，导航到更新工单优先级的动作。在 **Rules（规则）** 部分选择 **Add new rule（添加新规则）**，然后选择 **Notification（通知）**。这将打开添加通知的配置对话框。

![接收人配置](https://www.palantir.com/docs/resources/foundry/action-types/side_effects_notification_tutorial_edit_action_rules.png?width=500)

## 配置接收人

在本示例中，你将把通知发送给经办人，该经办人存储为被编辑的 `Alert` 对象的一个属性。为此，请在 **Recipients（接收人）** 下拉菜单中使用“Recipient(s) from property of object parameter（来自对象参数属性的接收人）”选项。选择作为动作参数可用的 `Alert` 对象，然后在出现提示时选择 `Case managers` 属性。

你应该会在配置的 **Recipients（接收人）** 部分看到所选的对象参数和属性。请记住，接收人必须始终是 Foundry 用户 ID。如果此属性包含其他内容（例如字符串形式的电子邮件地址），则不会发送任何通知。

> **ℹ️ 提示**
>
> 为了测试，你最初可能希望为动作配置硬编码的接收人，用于验证逻辑和通知内容的配置是否符合预期。

![硬编码的接收人](https://www.palantir.com/docs/resources/foundry/action-types/side_effects_notification_tutorial_static_test_user.png)

[了解有关其他接收人配置选项的更多信息。](https://www.palantir.com/docs/foundry/action-types/notifications/#recipients)

## 配置通知内容

接下来，你将配置通知内容：自定义通知以按姓名称呼接收人，并在内容中包含 `Alert` 对象的旧优先级和新优先级。下面提供了一个通知配置示例。

首先，从内容选项中选择“Template（模板）”。这是配置内容最直接的方式，不需要编写任何代码。

对于主题行，输入你想要的消息。要添加参数引用，请输入正斜杠 `/` 并从下拉列表中选择所需的参数。如果你选择的是对象参数，系统会要求你选择要引用的属性。

对于正文，输入按姓名称呼接收人、指明做出更改的用户、并报告先前状态和更新后状态的文本。

与主题中的对象引用一样，你可以从下拉列表中选择“Recipient（接收人）”、“Current User（当前用户）”以及任何参数选项，以生成对这些用户属性的正确引用。

[了解如何生成具有更复杂需求的通知内容。](https://www.palantir.com/docs/foundry/action-types/notifications/#content)

## 配置链接

最后，你将添加一个指向 Object Explorer 中指定 `Alert` 的对象视图（Object View）的链接。选择“Object View（对象视图）”，然后从下拉菜单中选择你的工单对象参数。接着，为链接按钮添加标签，文字为 `View Ticket`（查看工单）。

现在你可以保存整个通知配置了：

![完整配置](https://www.palantir.com/docs/resources/foundry/action-types/side_effects_notification_tutorial_updated_full_finished_configuration.png?width=400)

[了解有关可以配置的其他链接类型的更多信息。](https://www.palantir.com/docs/foundry/action-types/notifications/#content)

## 发送测试通知

为了验证，请创建一个经办人为你自己的测试警报。要运行该动作，你需要按照[动作文档](https://www.palantir.com/docs/foundry/workshop/actions-overview/)中的说明，在 Object Explorer 中公开该动作，或通过 Workshop 模块中的按钮公开它。

完成测试更改后，你应该会同时收到平台内推送通知和发送到你 Foundry 用户档案中指定电子邮件账户的电子邮件通知。平台内通知和电子邮件通知的预览都显示在通知配置视图中。

如果你没有收到电子邮件，可能是因为你禁用了电子邮件和/或平台内通知。你可以在 **User Settings（用户设置）** 下的 **Notifications（通知）** 中验证这一点。

## 后续步骤

- 探索其他可选功能，例如当接收人选择通过电子邮件接收通知时使用的[自定义内容](https://www.palantir.com/docs/foundry/action-types/notifications/#content-components)。
- 使用[函数](https://www.palantir.com/docs/foundry/functions/configure-notifications/)为接收人或内容配置复杂逻辑。

---

*原文：[Set up a notification](https://www.palantir.com/docs/foundry/action-types/set-up-notification/)*
