# 测试运行（Test run）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/action-types/test-run/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

**测试运行（test run）**允许你在最终用户能够应用动作类型之前，在 Ontology Manager 中模拟该动作类型。提交测试运行时，Foundry 会根据你提供的参数值评估该动作，并返回该动作*将会*做出的编辑，以及详细的执行明细。

> **ℹ️ 提示**
>
> 测试运行产生的编辑**不会**应用到你的本体。测试运行是一种安全的方式，可以在不更改任何数据的情况下查看动作将会做出的编辑及其执行方式。

测试运行可从动作类型编辑器中动作表单预览的 **Test run（测试运行）** 标签页访问。

![动作类型编辑器中动作表单预览的 Test run（测试运行）标签页。](https://www.palantir.com/docs/resources/foundry/action-types/action-test-run-tab.png?width=500)

## 运行测试

1. 在 Ontology Manager 中打开一个动作类型，并打开动作表单预览。
2. 确认预览设置为表单布局。测试运行在表单布局中可用，在表格布局中不可用。
3. 在动作表单预览中选择 **Test run（测试运行）** 标签页。
4. 用你想要测试的参数值填写表单。
5. 选择 **Submit（提交）** 运行测试。

运行完成后，测试运行的结果会显示在动作表单预览中。

测试运行在你当前的本体分支上评估动作类型，并以你的权限执行，应用与常规动作提交相同的对象安全规则和提交条件。如果你能够在 Ontology Manager 中查看动作类型配置，就可以运行测试。

> **ℹ️ 提示**
>
> 当动作类型有未保存的编辑时，测试运行不可用。请在运行测试之前保存更改，以便测试评估的是已保存的配置。

## 解读结果

完成的测试运行会在两个标签页中展示其结果。

### 提议的更改

**Proposed changes（提议的更改）** 标签页列出动作将对本体做出的编辑，包括创建、修改和删除的对象与链接。属性更改显示为当前值与提议值之间的对比。如果动作不会产生任何编辑，该标签页显示 **No proposed changes（无提议更改）**。

![Proposed changes（提议的更改）标签页，显示动作将会做出的对象和链接编辑。](https://www.palantir.com/docs/resources/foundry/action-types/action-test-run-proposed-changes.png?width=400)

### 详情

**Details（详情）** 标签页说明动作的评估方式，以及除直接编辑之外还会发生什么：

- **执行日志（Execution log）：**运行的逐步明细，涵盖元数据加载、依赖校验、提交条件、参数校验和编辑计算。使用此日志可以了解动作为什么成功或在哪里失败。
- **副作用（Side effects）：**动作将触发的[副作用](https://www.palantir.com/docs/foundry/action-types/side-effects-overview/)的预览，例如[通知](https://www.palantir.com/docs/foundry/action-types/notifications/)。你可以打开通知预览，查看将生成的内容和接收人。
- **引用的实体（Referred entities）：**动作在运行期间引用的对象类型、链接类型、接口类型和[函数](https://www.palantir.com/docs/foundry/action-types/function-actions-overview/)。

如果动作失败，**Details（详情）** 标签页将错误分为两类：

- **面向管理员的错误（Admin-facing errors）：**用于调试的技术细节。
- **面向最终用户的错误（End-user-facing errors）：**向触发动作的用户显示的消息。

![Details（详情）标签页，显示测试运行的引用实体和执行日志。](https://www.palantir.com/docs/resources/foundry/action-types/action-test-run-details.png?width=400)

## 测试运行期间跳过的内容

测试运行仅执行确定动作结果所需的工作。在动作应用后才会生效的副作用会被跳过。当你的动作类型包含以下任何内容时，动作表单预览会通知你它们将被跳过：

- [副作用 Webhook](https://www.palantir.com/docs/foundry/action-types/webhooks/#side-effect-webhooks) 不会被调用。
- [通知](https://www.palantir.com/docs/foundry/action-types/notifications/) 不会被发送。测试运行结果中会显示本应触发的通知的明细。
- [调度构建](https://www.palantir.com/docs/foundry/action-types/trigger-schedule-build/) 不会被触发。

## 外部调用

为了产生准确的结果，测试运行会执行动作达成其结果所需的函数和调用。这包括动作[规则](https://www.palantir.com/docs/foundry/action-types/rules/)中使用的函数、生成通知正文或接收人的函数、生成 Webhook 负载的函数、[回写 Webhook](https://www.palantir.com/docs/foundry/action-types/webhooks/#writeback-webhooks)，以及访问外部资源的函数。仅会进行确定动作结果所需的调用。

> **⚠️ 注意**
>
> 由于外部调用会被执行，它们可能会影响外部系统。当你的动作类型包含任何此类调用时，Foundry 会列出执行来源，并在测试运行继续之前提示你 **Confirm the external call（确认外部调用）**。只有在你确认后，调用才会执行。

![Confirm the external call（确认外部调用）对话框，列出测试运行之前需要确认的执行来源。](https://www.palantir.com/docs/resources/foundry/action-types/action-test-run-confirm-external-call.png?width=500)

## 限制

- 测试运行仅在表单布局中可用；在表格布局中不可用。
- 当动作类型有未保存的编辑时，测试运行不可用。请在运行测试之前保存更改。
- 动作应用后生效的副作用会被跳过，如[测试运行期间跳过的内容](#测试运行期间跳过的内容)中所述。某些函数支撑的调用仍会执行；请参见[外部调用](#外部调用)。

---

*原文：[Test run](https://www.palantir.com/docs/foundry/action-types/test-run/)*
