# 评审本体提案（Review ontology proposals）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/ontologies/review-ontology-proposals/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

本体提案类似于版本控制系统中的拉取请求。提案是一种机制，用于在单独分支中所做的更改集成到 `main` 之前对其进行评审和批准。

对于全局分支，本体提案会在创建[全局分支化提案](https://www.palantir.com/docs/foundry/global-branching/core-concepts/#create-and-prepare-a-proposal)时自动创建，并包含元数据，例如评审、名称以及要合并到 `main` 的更改的描述。对于旧版本体分支，本体提案在分支创建时创建。

本页介绍如何评审本体提案，包括检查资源状态、评审任务以及查看在分支上所做的更改。

## 提案选项卡

通过侧边选项卡导航到 **Proposals**（提案）页面，你可以在其中选择查看所有本体提案。提案被分组到以下选项卡中：

- **My proposals（我的提案）：** 由你创建的提案。
- **Assigned to me（分配给我的）：** 你被指定为评审者的提案。
- **In review（评审中）：** 进行中或已批准的提案。
- **Merged proposals（已合并的提案）：** 已合并到 `Main`（主）本体的提案。
- **Closed proposals（已关闭的提案）：** 已关闭且未合并的提案。

![Ontology Manager 的“我的提案”概览页面。](https://www.palantir.com/docs/resources/foundry/ontologies/ontology-proposals-page.png)

## 提案视图

访问 **Proposal overview**（提案概览）、**Preview status**（预览状态）、**Review changes**（评审更改）和 **Changelog**（变更日志）选项卡，以获取有关单个提案的更多信息。

### 提案概览页面

在全局分支上工作时，要访问单个提案，请从分支任务栏中选择任意本体资源，然后选择 **View ontology proposal**（查看本体提案）。如果你在 `Main` 上，请导航到 **Proposals**（提案）选项卡并选择你希望查看的提案。如果你在本体分支上，请从导航顶部栏选择 **Open proposal details**（打开提案详情）以直接访问提案。

在提案中，你将看到 **Proposal overview**（提案概览）、**Preview status**（预览状态）、**Review changes**（评审更改）和 **Changelog**（变更日志）选项卡，以获取更多信息。

提案概览页面集中展示了提案的阶段、更改、需要评审的任务以及选定的评审者。

- **查看分支上的更改：** 编辑显示在概览页面的底部。编辑按作者和任务分类，其中任务对应于一个本体资源。你可以查看更改、导航到资源或从分支中移除更改。更改历史记录也可以通过 **Changelog**（变更日志）选项卡访问，其中还显示了更改的确切时间。
- **查看和添加评审者：** 指派特定同事评审你的提案。
- **查看需要关注的任务：** 此部分将显示评审阶段中所有被拒绝的任务。
- 使用 **Share**（分享）选项复制提案链接。

![特定全局分支的提案概览。](https://www.palantir.com/docs/resources/foundry/ontologies/ontology-proposal-overview.png)

### 预览状态

**Preview status**（预览状态）选项卡显示哪些对象类型已在你的分支上构建索引、正在进行中或无法构建索引。对象类型一旦完成索引构建，就可以进行预览，这意味着其数据可在你的分支上用于查看和测试。

![预览状态选项卡。](https://www.palantir.com/docs/resources/foundry/ontologies/ontology-proposal-preview-status-tab.png)

### 评审更改

**Review changes**（评审更改）选项卡显示提案中的所有任务。你可以从此处执行以下操作：

- 邀请其他评审者
- 查看已迁移到项目的资源的批准策略
- 单独批准或拒绝每个任务，或对所有符合条件的任务批量批准或拒绝
- 在任务级别发表评论，并与你的同事协作

![评审更改页面。](https://www.palantir.com/docs/resources/foundry/ontologies/ontology-proposal-review-changes.png)

### 变更日志

Changelog（变更日志）选项卡显示分支上更改的详细历史记录。可以展开任务以显示特定用户在给定时间点所做的编辑。你也可以直接导航到相关的本体资源。

![变更日志选项卡。](https://www.palantir.com/docs/resources/foundry/ontologies/ontology-proposal-changelog-tab.png)

## 提案权限

- **查看提案：** 提案的标题和描述可由所有有权访问本体的人发现。对提案中某些资源至少拥有 `Viewer`（查看者）访问权限的任何用户都可以看到与这些资源相关的更改。
- **修改本体资源：** 对资源拥有编辑权限的用户可以在分支上编辑它。对于使用[本体角色](https://www.palantir.com/docs/foundry/object-permissioning/ontology-permissions-legacy/)（而非[项目权限](https://www.palantir.com/docs/foundry/object-permissioning/ontology-permissions/)）的资源，查看者可以在分支上建议更改。
- **接受或拒绝提案中的任务：** 要批准任务，批准者默认必须是底层资源的编辑者或所有者。如果资源已迁移到项目并受到保护，则批准者必须根据项目策略拥有批准权限。
- **合并本体提案：** 本体提案通过合并全局分支化提案来合并。但是，对于旧版本体分支，只要获得了所有必需的批准，任何可以查看该分支的人都可以合并提案。

---

*原文：[Review ontology proposals](https://www.palantir.com/docs/foundry/ontologies/review-ontology-proposals/)*
