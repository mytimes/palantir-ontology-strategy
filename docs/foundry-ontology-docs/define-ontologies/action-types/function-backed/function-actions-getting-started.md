# 入门（Getting started）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/action-types/function-actions-getting-started/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

本教程讲解如何创建一个由[本体编辑函数（Ontology Edit function）](https://www.palantir.com/docs/foundry/functions/edits-overview/)支撑的动作类型。

## 前提条件

在本教程中，我们将使用与[动作入门教程](https://www.palantir.com/docs/foundry/action-types/getting-started/)相同的 `Demo Ticket` 对象类型和示例对象。

首先编写一个本体编辑函数，该函数会执行你的动作所需的编辑。这需要：

- 使用“对象函数（functions on objects）”TypeScript 模板建立代码仓库，
- 将相关对象类型导入你的代码仓库，以及
- 发布本体编辑函数，供动作读取。

关于这些步骤的信息可在函数文档中找到：

- **[入门](https://www.palantir.com/docs/foundry/functions/getting-started/)：** 跟随本教程创建一个基础函数代码仓库并发布函数。
- **[对象函数](https://www.palantir.com/docs/foundry/functions/functions-on-objects/)：** 跟随本教程创建一个使用对象数据的函数。
- **[本体编辑](https://www.palantir.com/docs/foundry/functions/api-ontology-edits/)：** 使用此参考文档创建本体编辑函数。

编写并发布本体编辑函数后，以下步骤会将该函数连接到动作，从而可以用该函数对对象进行编辑。就本教程而言，我们已在一个代码仓库中编写并发布了以下本体编辑函数：

![本体编辑函数](https://www.palantir.com/docs/resources/foundry/action-types/function_backed_actions_ontology_edit_function.png)

为方便起见，代码如下：

```typescript
@OntologyEditFunction()
public addPriorityToTitle(ticket: DemoTicket): void {
    let newTitle: string = "[" + ticket.ticketPriority + "]" + ticket.ticketTitle;
    ticket.ticketTitle = newTitle;
}
```

> **⚠️ 注意**
>
> 用于动作类型的函数必须使用 `@OntologyEditFunction()` 而非 `@Function()` 注解。更多细节可参阅[对象函数](https://www.palantir.com/docs/foundry/functions/api-ontology-edits/#declaring-an-edit-function)文档。

## 创建函数支撑的动作

在**规则（Rules）**部分，添加一条类型为**函数（Function）**的规则。搜索你在[前提条件](#前提条件)中发布的函数，并选择最新版本。如下所示配置输入，使其与动作参数对应。请注意，函数规则不能与[其他本体规则](https://www.palantir.com/docs/foundry/action-types/rules/#ontology-rules)组合使用。

![配置输入](https://www.palantir.com/docs/resources/foundry/action-types/function_backed_actions_configure_inputs.png)

选择函数时，函数的所有输入都会自动创建为参数并添加到**参数（Parameters）**选项卡。在这些截图所示的示例中，已创建一个类型为**对象引用（Object reference）**的 `Demo Ticket` 参数。如有需要，现在可以进一步自定义该参数。

![Demo Ticket](https://www.palantir.com/docs/resources/foundry/action-types/function_backed_actions_demo_ticket.png)

![Demo Ticket 详情](https://www.palantir.com/docs/resources/foundry/action-types/function_backed_actions_demo_ticket_details.png)

保存你的动作，并按照[与其他应用集成的指南](https://www.palantir.com/docs/foundry/action-types/use-actions/)中的说明，在整个平台上对其进行配置。

## 更改函数版本

默认情况下，如果函数逻辑发生更改，动作不会自动更新以与之匹配。你必须回到动作的**规则**部分，升级动作所引用的函数版本。例如，如果我们发布了函数的 0.1.2 版本，就需要在此处更新：

![如何更新函数版本](https://www.palantir.com/docs/resources/foundry/action-types/function_backed_actions_update_function_logic.png)

### 自动升级

你可以选择为动作引用的函数启用自动升级。启用后，动作将以[版本范围](https://www.palantir.com/docs/foundry/functions/version-range-dependencies-for-functions/)依赖该函数，并在运行时[解析版本](https://www.palantir.com/docs/foundry/functions/version-range-dependencies-for-functions/#version-range-resolution)。

要为动作启用自动升级，请转到动作的**规则**部分并选择**函数**参数。在**函数**下拉菜单中，选择你希望运行的最低函数版本，并启用**自动升级（Auto upgrade）**选项。这对应于一个版本范围依赖，包含所选最低版本的所有向后兼容版本，例如次版本（minor）或补丁版本（patch）升级。

![如何为函数支撑的动作启用自动升级](https://www.palantir.com/docs/resources/foundry/action-types/function_backed_actions_auto_upgrade.png)

> **ℹ️ 提示**
>
> 对于 `0.y.z` 形式的函数版本，自动升级处于禁用状态。这些版本保留给初始开发阶段使用，此时函数 API 和行为可能频繁变化，不应视为稳定。请参阅关于[选择发布版本](https://www.palantir.com/docs/foundry/functions/functions-versioning/#choosing-a-release-version)的文档。

#### 安全性

如果为函数支撑的动作启用了自动升级，对该动作没有[编辑权限](https://www.palantir.com/docs/foundry/object-permissioning/ontology-permissions-legacy/#permissions-for-editing-link-types)的用户可以通过修改支撑函数来改变动作的行为。这是因为对函数的编辑权限与对动作的权限并不绑定。

#### 破坏性变更

自动升级可能因有问题的函数版本中的[破坏性变更](https://www.palantir.com/docs/foundry/functions/version-range-dependencies-for-functions/#risks)而导致动作执行失败。

#### 溯源（Provenance）

动作的溯源根据所选最低函数版本的溯源设定。如果函数的较新版本返回超出此溯源范围的编辑（例如额外的对象类型），动作执行将失败。

> **ℹ️ 提示**
>
> 目前，溯源仅包含动作在运行时可能编辑的对象类型。

---

*原文：[Getting started](https://www.palantir.com/docs/foundry/action-types/function-actions-getting-started/)*
