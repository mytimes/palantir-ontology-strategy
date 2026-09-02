# 分支化动作类型（Branching action types）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/action-types/branching-action-types/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

本体动作与全局分支化（Global Branching）集成，使你能够在分支上测试动作类型而不影响生产环境。在将更改合并到 `main` 之前，你可以在隔离的分支上下文中运行动作、验证其配置并观察编辑结果。来自分支的编辑不会被合并。

有关全局分支化概念和工作流的一般信息，请参阅[全局分支化文档](https://www.palantir.com/docs/foundry/global-branching/overview/)。

## 在分支上运行动作

你可以在分支上的 Workshop 模块中测试动作，以验证它们配置正确。当所有相关对象类型都已在分支上编入索引时，你可以运行动作类型并在分支上查看生成的编辑。

### 前提条件

要在分支上运行动作，被动作类型编辑的所有对象类型都必须在该分支上编入索引。你可以在 Ontology Manager 中通过单个对象类型页面或动作类型页面将对象类型编入索引。

在 Ontology Manager 中编辑动作类型时，如果该动作类型修改了未在分支上编入索引的对象类型，会出现警告。

![Ontology Manager 中的警告：该动作类型修改了未在分支上编入索引的对象类型。](https://www.palantir.com/docs/resources/foundry/action-types/action-indexing-banner.png)

如果在这些对象类型编入索引之前运行动作，该动作不会应用其编辑。提示弹窗会说明动作是有效的，但它所编辑的一个或多个对象类型在分支上是只读的。该通知链接到 Ontology Manager，你可以在那里将剩余对象类型编入索引，然后再次运行动作。

> **ℹ️ 提示**
>
> 在分支上运行动作旨在作为一种测试机制。分支上的动作编辑不会合并回 `main`。

### 在分支上使用函数支撑的动作

分支上的函数支撑动作根据其是否具备分支感知能力而有不同的行为。分支感知函数可以在分支上修改并从分支读取模式，而非分支感知函数只能从 `main` 读取模式。无论是否具备分支感知能力，所有函数支撑的动作都仅在分支上执行，不会将更改回写到 `main`。支持的函数类型请参阅[全局分支化文档](https://www.palantir.com/docs/foundry/global-branching/integrations/)。

## 在分支上管理副作用

> **ℹ️ 提示**
>
> 以下信息专门适用于在分支上**通过动作**应用的 Webhook、带有外部调用的函数和通知。通过其他方式应用的副作用将按照那些消费方的定义运行。

### Webhook

默认情况下，在分支上应用动作时 Webhook 不会执行。此行为是为了防止在测试环境中意外写入外部系统。

在这种情况下，你会看到提示弹窗说明此行为。

![提示弹窗：动作已应用但 Webhook 未执行。](https://www.palantir.com/docs/resources/foundry/action-types/action-webhook-toast.png)

但在某些情况下，在分支上测试 Webhook 是可取的，例如调用 READ 端点时。

要覆盖默认行为，请在 Ontology Manager 中配置动作类型的**安全与提交条件（Security and submission criteria）**选项卡，以启用分支上的 Webhook 执行。

![在分支上启用 Webhook 的设置。](https://www.palantir.com/docs/resources/foundry/action-types/action-webhook-setting.png)

> **ℹ️ 提示**
>
> 如果启用了分支上的 Webhook，Webhook 将完全按照在 `main` 上的方式运行。因此，如果 Webhook 配置为访问外部生产环境，即使动作在分支上执行，它也会继续访问该环境。

### 带有外部调用的函数

默认情况下，带有外部调用的函数支撑动作逻辑在分支上应用动作时不会执行；动作将完全失败。此行为是为了防止在测试环境中意外写入外部系统。

在这种情况下，你会看到提示弹窗指示失败，并解释该行为。

![外部函数调用阻止了该动作。](https://www.palantir.com/docs/resources/foundry/action-types/action-function-error-toast.png?width=400)

但有时在分支上测试是必要的，例如调用 READ 端点时。你可以在 Ontology Manager 中动作类型的**安全与提交条件**选项卡里，在分支上启用带有外部调用的函数，以覆盖此限制。

![在分支上启用带有外部调用的函数的设置。](https://www.palantir.com/docs/resources/foundry/action-types/action-function-setting.png)

> **ℹ️ 提示**
>
> 如果启用了分支上带有外部调用的函数，该函数将进行与在 `main` 上相同的外部调用。因此，如果函数配置为访问外部生产环境，即使动作在分支上执行，它也会继续访问该环境。

### 通知

默认情况下，在分支上应用动作时通知不会发送。此行为是为了防止在测试环境中意外通知收件人。

在这种情况下，你会看到提示弹窗说明此行为。

![提示弹窗：动作已应用但通知未发送。](https://www.palantir.com/docs/resources/foundry/action-types/action-notification-toast.png)

但在某些情况下，在分支上测试通知是可取的。

要覆盖默认行为，你可以在 Ontology Manager 中动作类型的**安全与提交条件**选项卡里启用分支上的通知。

此外，你可以指定动作在分支上运行时的通知收件人：

- **分支所有者：**将所有通知发送给分支所有者。
- **默认收件人：**向原始通知中配置的收件人发送通知。

![在分支上启用通知的设置。](https://www.palantir.com/docs/resources/foundry/action-types/action-notification-setting.png)

## 已知限制

- 分支上的动作编辑仅用于测试，不会合并回 `main`。
- 在分支上运行动作时，默认不执行 Webhook 和电子邮件通知。
- 调用外部系统的函数在分支上运行动作时默认会失败。

---

*原文：[Branching action types](https://www.palantir.com/docs/foundry/action-types/branching-action-types/)*
