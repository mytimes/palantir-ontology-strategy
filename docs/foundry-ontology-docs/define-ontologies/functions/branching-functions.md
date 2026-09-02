# 函数分支化（Branching functions）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/branching-functions/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

你可以在全局分支上开发、发布和使用函数。目前 TypeScript v1 函数和 AIP Logic 函数支持此功能。

> **⚠️ 注意**
>
> 你不能在分支上修改 TypeScript v2 或 Python 函数。要在合并到 `main` 之前测试这类函数，请在分支上引用特定的函数版本。函数代码只能使用 `main` 上已存在的模式（schema）。

## 开发函数

> **ℹ️ 提示**
>
> 要对 TypeScript v1 使用全局分支化，请将代码仓库模板版本升级到 `functions-typescript` 子模板的 `0.903.0` 或更高版本。有关说明，请查阅[代码仓库升级文档](https://www.palantir.com/docs/foundry/code-repositories/repository-upgrades/#manual-branch-upgrade)。

你可以开发依赖于全局分支上资源变更的函数，例如新建或修改的本体实体。

![在分支上开发新函数。](https://www.palantir.com/docs/resources/foundry/functions/branch-function.png)

准备就绪后，发布函数时需指定**版本目标**——即全局分支合并时发布到 `main` 的稳定版本。在分支上开发期间，函数以不稳定的预览版本发布。

![在分支上发布函数。](https://www.palantir.com/docs/resources/foundry/functions/branch-function-publish.png)

在分支上成功发布函数后，你就可以在 Workshop、AIP Logic 和函数支撑的动作中使用分支上的新函数版本。该函数版本带有 `Branched pre-release` 标签。请注意，在你的分支上发布的函数无法从其他分支（包括 `main`）访问。

![在 Workshop 中使用分支函数。](https://www.palantir.com/docs/resources/foundry/functions/branch-function-backed-variable.png)

![在动作中使用分支函数。](https://www.palantir.com/docs/resources/foundry/functions/branch-function-backed-action.png)

> **ℹ️ 提示**
>
> 目前无法在 TypeScript v1 代码仓库中依赖查询函数的分支版本。

随着你在分支上继续开发，可以继续向同一个版本目标发布函数版本。只要版本目标保持不变，任何使用你的函数的资源都会自动拉取最新发布的版本。这让你可以快速迭代，无需在每次发布后手动更新函数引用。

如果分支上的版本目标发生变化，你必须将该函数在分支上使用旧版本的所有依赖方更新为使用新的版本目标。这也会显示为一项[检查](https://www.palantir.com/docs/foundry/global-branching/core-concepts/#checks)。

## 冲突解决与函数变基

在分支上开发时，你不会自动收到发布到 `main` 的较新函数版本。这可以避免 `main` 上的开发干扰你的分支工作。如果 `main` 上有较新的版本可用，你会在函数版本选择器和 Ontology Manager 中看到通知。随后你可以对函数版本执行变基，以拉取 `main` 上所有较新的版本。

![函数变基对话框。](https://www.palantir.com/docs/resources/foundry/functions/rebasing-functions.png)

如果在你开发期间，你的版本目标被发布到了 `main`，你必须在合并前选择一个新的版本目标。选择后，将所有依赖方更新为使用新的版本目标。

## 合并

合并全局分支时，修改过的函数将以稳定的版本目标发布到 `main`。使用你分支上所发布版本的资源，将自动开始使用合并过程中发布到 `main` 的新稳定版本。

> **⚠️ 注意**
>
> Foundry 只会将你的版本目标合并到 `main`，不会合并分支上发布的其他版本。分支合并后，这些版本将不会存在于 `main` 上。

---

*原文：[Branching functions](https://www.palantir.com/docs/foundry/functions/branching-functions/)*
