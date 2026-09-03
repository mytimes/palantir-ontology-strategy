# 副作用（Side effects）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/action-types/side-effects-overview/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

动作类型旨在支持组织内全方位的决策流程。当本体作为决策流程的记录系统时，使用[规则](https://www.palantir.com/docs/foundry/action-types/rules/)定义对象修改可以让你极其灵活地表达业务流程。为了支持全方位的组织流程，动作类型还支持一些额外功能：

- 对于实时流程，你可能需要*通知*用户系统中正在发生的更改，以便他们采取相应行动。
- 当 Foundry 之外的系统是你组织的事实来源时，你可能需要与其他系统*集成*以支持现有业务流程。这种模式有时被称为“决策编排（decision orchestration）”。

动作类型中的**副作用**使你能够将数据发送到 Foundry 之外，以与现有组织流程集成。副作用主要有两种类型：

- [通知](https://www.palantir.com/docs/foundry/action-types/notifications/)允许你灵活配置应用动作时应如何通知用户，包括向平台上的用户发送电子邮件的能力。
- [Webhook](https://www.palantir.com/docs/foundry/action-types/webhooks/)允许你以高度灵活的方式连接到 Foundry 之外的系统，包括向 REST API 或 ERP 系统发送请求。这使你能够写入组织中的其他源系统，或通过与消息传递系统集成来更灵活地向用户发送通知。

你可以通过上面的链接了解有关通知和 Webhook 的更多信息，也可以通过以下指南入门：

- [设置通知](https://www.palantir.com/docs/foundry/action-types/set-up-notification/)
- [设置 Webhook](https://www.palantir.com/docs/foundry/action-types/set-up-webhook/)

---

*原文：[Side effects](https://www.palantir.com/docs/foundry/action-types/side-effects-overview/)*
