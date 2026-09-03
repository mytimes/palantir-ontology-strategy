# 本体分支化（Branching the ontology）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/ontologies/branching-ontology/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

本体与全局分支化（Global Branching）集成，支持安全、隔离地开发本体资源。本文档介绍如何在分支上操作本体，包括添加和修改资源、变基与冲突解决、评审与合并流程，以及已知限制。

有关全局分支化概念和工作流的一般信息，请参阅[全局分支化文档](https://www.palantir.com/docs/foundry/global-branching/overview/)。

## 定义

有关分支、提案和变基的一般定义，请参阅[全局分支化核心概念](https://www.palantir.com/docs/foundry/global-branching/core-concepts/)。以下术语是本体分支化特有的：

- **本体提案（Ontology proposal）：** 当你在包含本体更改的分支上[创建全局分支化提案](https://www.palantir.com/docs/foundry/global-branching/core-concepts/#create-and-prepare-a-proposal)时，会自动创建一个本体提案来跟踪本体特有的更改。本体提案包含元数据，例如评审、名称以及要合并到 `main` 的本体更改的描述。

## 添加和修改资源

要在分支上修改本体，你可以在 Ontology Manager 中创建新分支，也可以使用分支选择器访问现有分支。

要创建分支，请打开分支选择器并选择 **Create new branch**（创建新分支），在打开的对话框中为你的分支添加标题和描述。

![分支选择器。](https://www.palantir.com/docs/resources/foundry/ontologies/branch-selector.png?width=250)

如果你已经对本体进行了更改并希望将其包含在分支中，可以从保存对话框中选择 **Save to new branch**（保存到新分支），以创建一个包含这些更改的独立分支。请注意，如果你对任何[受保护的本体资源](#资源保护)进行了更改，系统将要求你保存到新分支。

![保存到本体选项。](https://www.palantir.com/docs/resources/foundry/ontologies/save-to-ontology.png?width=250)

> **ℹ️ 提示**
>
> 你只能从主本体（也称为 `main` 分支）创建分支。

在分支上工作时，界面底部的[分支任务栏](https://www.palantir.com/docs/foundry/global-branching/branch-taskbar/)会显示你当前的分支名称和其他元数据。

## 资源保护

分支保护支持的本体资源类型包括：

- 对象类型
- 动作类型
- 链接类型
- 接口类型
- 共享属性类型

以下类型不支持资源保护：

- 类型组
- 规则集（修改规则集时，将强制执行包含该规则集的对象类型的保护状态）

此外，本体资源必须先迁移为使用项目权限，然后才能受到保护。有关更多信息，请参阅[本体权限](https://www.palantir.com/docs/foundry/object-permissioning/ontology-permissions/)。

将本体资源迁移为使用项目权限后，你可以通过父项目的 **Files**（文件）选项卡查看和管理其保护状态。

![保护本体资源。](https://www.palantir.com/docs/resources/foundry/ontologies/protect-from-compass.png)

启用保护后，你必须在单独的分支上进行更改，并创建提案将其合并到主分支。

保护状态在 Ontology Manager 的 **Overview**（概览）选项卡中也可见：

![Ontology Manager 概览页面中受保护的本体资源。](https://www.palantir.com/docs/resources/foundry/ontologies/ontology-overview-tab.png)

你还可以在 **Security**（安全）选项卡下查看适用的策略：

![Ontology Manager 安全选项卡中受保护的本体资源。](https://www.palantir.com/docs/resources/foundry/ontologies/ontology-security-tab.png)

修改受保护资源时，**Save**（保存）对话框会替换为 **Create and save to branch**（创建并保存到分支），要求你将更改保存到新分支。

![Ontology Manager 中保存到新分支的选项。](https://www.palantir.com/docs/resources/foundry/ontologies/modify-protected-object.png)

## 变基与冲突解决

在你于分支上引入更改的同时，`main` 也可能接收来自其他人的新更改。变基将 `main` 的最新更改纳入你当前的分支中，以使你当前的分支保持最新。

> **ℹ️ 提示：自动变基**
>
> 如果你的全局分支不包含对本体的更改，变基会自动发生。一旦你向分支引入本体更改（包括为对象类型构建索引），你将需要手动变基，以使你的分支与 `main` 保持同步。

### 对分支进行变基

当有来自 `main` 的新更改时，侧边栏中的 **Main branch updates**（主分支更新）选项卡上会出现一个蓝色指示器，提示你查看这些更改。

![主分支更新。](https://www.palantir.com/docs/resources/foundry/ontologies/main-branch-updates.png?width=300)

导航到 **Main branch updates**（主分支更新）页面，查看自上次变基以来（如果这是你第一次手动变基，则是自分支创建以来）传入的更改。选择 **Rebase branch**（变基分支），用最新版本的 `main` 更新你的分支。

![变基分支视图。](https://www.palantir.com/docs/resources/foundry/ontologies/rebase-branch-view.png)

如果没有冲突或错误，变基将自动完成，你的分支也就更新到了最新状态。

### 解决合并冲突

如果存在冲突，你的变基将保持进行中状态，你将被重定向到 **Conflicts**（冲突）选项卡，以解决你的分支与 `main` 之间任何相互冲突的更改。如果只有错误，你将被重定向到 **Errors**（错误）选项卡。

在变基期间，来自 `main` 的更改会加载到你的分支上，而你当前分支上之前保存的任何更改都会重新加载回工作状态，你可以在 **All changes**（所有更改）选项卡中看到这些更改。

这种状态允许你查看和访问来自 `main` 和你的分支的更改。当某个本体资源在两个分支上都有更改时，默认会显示你的分支版本。

![评审变基更改。](https://www.palantir.com/docs/resources/foundry/ontologies/review-rebase-changes.png)

要解决冲突，你可以为每个资源选择 **Use Main branch changes**（使用主分支更改）或 **Keep current branch changes**（保留当前分支更改）。或者，你可以直接导航到该资源并应用**自定义更改**来解决其冲突。

在此示例中，`Palantir employee`（Palantir 员工）对象类型存在冲突，其显示名在 `main` 分支和你的分支上都被更改了。要解决此冲突，请选择保留该对象类型的哪个版本。

![评审对象类型变基更改。](https://www.palantir.com/docs/resources/foundry/ontologies/review-object-type-rebase-changes.png)

你也可以通过进行自定义更改来解决此冲突。在示例中，你可以导航到该对象类型，将其显示名从“Palantir employee”更改为“Current employee”（现任员工）。完成这一自定义更改后，冲突即告解决。

![现任员工示例。](https://www.palantir.com/docs/resources/foundry/ontologies/current-employee-example.png)

![现任员工示例冲突。](https://www.palantir.com/docs/resources/foundry/ontologies/current-employee-example-conflicts.png)

解决所有冲突后，请确保在完成变基之前处理完所有错误。

### 完成变基

解决所有错误和冲突后，你可以选择 **Finish rebase and save**（完成变基并保存），你的分支就更新到了最新状态。

![完成变基并保存选项。](https://www.palantir.com/docs/resources/foundry/ontologies/finish-rebase-and-save.png)

你可以继续在分支上工作，并定期变基，以使你的分支与 `main` 分支的最新版本保持同步。

![分支已是最新。](https://www.palantir.com/docs/resources/foundry/ontologies/branch-is-up-to-date.png)

## 合并要求

### 准备你的分支以供评审

在完成更改并准备将分支合并到 `main` 时，通过选择分支任务栏中的 **Create proposal**（创建提案）图标来创建提案。添加名称和描述以设置你的提案。

![从任务栏创建提案。](https://www.palantir.com/docs/resources/foundry/ontologies/create-proposal-taskbar.png)

![从对话框创建提案。](https://www.palantir.com/docs/resources/foundry/ontologies/create-proposal-dialog.png)

创建提案后，将运行**合并检查**，以验证全局分支上的资源是否能够合并到 `main` 分支。失败的检查可能包括你的分支与 `main` 分支之间的冲突，这将要求你对分支进行变基。

![包含合并检查的任务栏弹出框。](https://www.palantir.com/docs/resources/foundry/ontologies/branch-taskbar-review-button.png?width=450)

### 请求评审

你可以通过分支任务栏、全局分支化提案页面或本体提案页面向你的提案添加评审者。

在本体提案页面上，转到 **Review changes**（评审更改）并选择 **Invite reviewers**（邀请评审者）以向你的提案添加评审者。对于已迁移到项目的本体资源，选择 **View policies**（查看策略）以根据关联的项目策略查看哪些评审者需要评审该资源。

每个本体资源都被视为一个单独的任务。资源名称旁边的状态标签表示总体批准状态，而右侧的 **Your review**（你的评审）部分允许你提交评审。

> **ℹ️ 提示**
>
> 虽然本体实体在全局分支化中被视为独立的资源，但它们被分组在单个本地本体提案下。这意味着向一个本体资源添加评审者实际上会将该评审者添加到所有本体资源。

![本体提案评审更改。](https://www.palantir.com/docs/resources/foundry/ontologies/ontology-proposal-review-tab.png)

> **ℹ️ 提示：发表评论**
>
> 你还可以对提案中的各个任务发表评论，以提供有关所提议更改的上下文。通过选择最右侧的 Comments（评论）侧边栏来访问任务的评论部分。

### 评审提案

在 **Review changes**（评审更改）选项卡中，评审者可以批准或拒绝单个任务。没有权限的用户仍然可以评审任务（例如，表达他们对更改的意见），但这不会影响任务的批准状态。

> **⚠️ 注意：批准权限**
>
> 拥有批准权限的用户即使未被添加为评审者，也可以批准提案。使用评审者列表来跟踪谁应该评审更改，而不是用它来限制批准。

在本体提案中，评审者可以查看提案详情，并批准或拒绝对所有已修改资源或特定本体资源的更改。

![批准本体更改。](https://www.palantir.com/docs/resources/foundry/ontologies/review-protected-object.png)

满足策略要求后，获得批准的资源会从 `In Progress`（进行中）变为 `Approved`（已批准）。如果所有其他检查都已通过，你就可以合并提案。

### 合并你的分支

当你准备将更改合并到 `main` 时，你必须合并你的全局分支化提案。这将自动启动本体的合并流程。

![全局分支化页面中的合并分支选项。](https://www.palantir.com/docs/resources/foundry/ontologies/test-changes-foundry-branching-merge-branch.png)

为此，请选择分支任务栏中的合并图标，或导航到你在全局分支化中的提案页面并选择 **Merge proposal**（合并提案）。

## 已知限制

- **数据源删除：** 当某个对象类型发生冲突，且其支撑数据源在 `main` 分支上已被替换或移除时，选择保留你的分支更改将导致合并失败。在这种情况下，请选择 `main` 分支的更改。
- **条件格式删除：** 当某个属性类型发生冲突，且其条件格式规则集在 `main` 分支上已被替换或移除时，选择保留你的分支更改将导致合并失败。在这种情况下，请选择 `main` 分支的更改。
- **Pipeline Builder 对象类型：** 在 Pipeline Builder 中创建的对象类型无法在分支上的 Ontology Manager 中修改。
- **索引构建算作修改：** 为对象类型构建索引被视为一种修改。如果资源受项目策略保护，你将需要获得策略批准才能合并分支。要绕过此要求，请在合并前移除索引更改。
- **评审者适用于所有本体资源：** 向一个本体资源添加评审者会将他们添加到整个提案，因为本体更改被分组在一起。只有资源项目策略中的用户才需要批准该特定资源。

---

*原文：[Branching the ontology](https://www.palantir.com/docs/foundry/ontologies/branching-ontology/)*
