# 函数入门（Getting started with functions）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/getting-started/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

> **ℹ️ 提示**
>
> 你可以在 [VS Code 工作区](https://www.palantir.com/docs/foundry/vs-code/overview/)中以相同的界面和工具编写函数代码仓库。VS Code 工作区支持实时预览、资源导入、SDK 生成和发布管理。

在 Foundry 中使用函数有三种语言选项：TypeScript v1、TypeScript v2 和 Python。有关每种语言所支持功能的更多信息，请参阅[语言功能支持](https://www.palantir.com/docs/foundry/functions/language-feature-support/)规范。

尽管每种语言支持的功能集不同，但你都能使用相同的基础平台功能，包括运行、测试和发布函数。本页面概述这些功能，帮助你了解如何使用函数代码仓库，而不受限于你使用的语言。

有关特定语言入门的更详细说明，请参阅以下教程：

- [TypeScript v1 函数入门](https://www.palantir.com/docs/foundry/functions/typescript-v1-getting-started/)
- [TypeScript v2 函数入门](https://www.palantir.com/docs/foundry/functions/typescript-v2-getting-started/)
- [Python 函数入门](https://www.palantir.com/docs/foundry/functions/python-getting-started/)

请阅读以下各节，了解有关函数代码仓库创建和使用的一般信息。

## 创建函数代码仓库

创建函数代码仓库时，你可以选择最适合自己需求的语言。你可以在所选项目中直接初始化函数代码仓库：选择 **+ New > Repository（新建 > 代码仓库）**；也可以在 Code Repositories 应用中选择右上角的 **+ New repository（新建代码仓库）**。仓库初始化完成后，你就可以添加并运行函数。

![创建函数代码仓库。](https://www.palantir.com/docs/resources/foundry/functions/functions-create-repo.png)

有关如何为特定语言创建函数代码仓库的详细说明，请参阅以下教程章节：

- [创建 TypeScript v1 函数代码仓库](https://www.palantir.com/docs/foundry/functions/typescript-v1-getting-started/#create-a-typescript-v1-functions-repository)
- [创建 TypeScript v2 函数代码仓库](https://www.palantir.com/docs/foundry/functions/typescript-v2-getting-started/#create-a-typescript-v2-functions-repository)
- [创建 Python 函数代码仓库](https://www.palantir.com/docs/foundry/functions/python-getting-started/#create-a-python-functions-repository)

## 开发环境选项

你可以在 Code Repositories 或 VS Code 工作区中开发函数代码仓库：

- **Code Repositories：** 下文各节所述的基于网页的 IDE。
- **VS Code 工作区：** 使用实时预览、导入资源、生成 SDK，以及管理标签和发布。要在 VS Code 工作区中打开仓库，请在页眉中选择 **Edit in VS Code（在 VS Code 中编辑）**。

[进一步了解 VS Code 工作区。](https://www.palantir.com/docs/foundry/vs-code/overview/)

## 在实时预览中测试

函数实时预览允许你在将函数提交到仓库之前对其进行测试。将函数添加到仓库后，你可以在实时预览中运行它。操作方法：打开底部工具栏的 **Functions（函数）**，选择 **Live Preview（实时预览）**。选择一个函数，填入输入值，然后选择 **Run（运行）** 来运行该函数。

![在函数实时预览中运行你的新函数。](https://www.palantir.com/docs/resources/foundry/functions/tsv2-functions-helper-preview-run.png)

> **⚠️ 注意**
>
> 实时预览的运行时环境与已发布函数不同。差异包括 CPU 资源、可用内存，以及函数在超时之前可以运行多长时间。
> [管理已发布函数的运行时环境。](https://www.palantir.com/docs/foundry/functions/manage-functions/)

选择右上角的 **Commit（提交）**，将更改提交到仓库的 `master` 分支。

## 发布你的函数

提交工作成果后，你会看到 **Tag version（标记版本）** 选项。这会将仓库中的所有函数发布到函数注册表，[使其可在整个平台中使用](https://www.palantir.com/docs/foundry/functions/use-functions/)。

![“Tag version（标记版本）”选项。](https://www.palantir.com/docs/resources/foundry/functions/ts-functions-tags.png)

选择 **Tag version（标记版本）**，从 `master` 分支标记一个发布。根据更改的程度设置标签名称，然后选择 **Tag and release（标记并发布）**。

![选择要为新发布标记的版本类型。](https://www.palantir.com/docs/resources/foundry/functions/new-functions-tag.png?width=500)

要查看函数标记和发布的进度，请选择 **View（查看）** 弹窗，或导航到 **Tags（标签）** 选项卡。**Step 2: Release（第 2 步：发布）** 完成后，选择已发布的函数即可在函数注册表中查看它们。

> **⚠️ 注意**
>
> 在权限传播期间，函数可能无法立即在 Workshop 或函数注册表中按名称搜索到。

![标签检查和发布检查均已通过，新函数已发布。](https://www.palantir.com/docs/resources/foundry/functions/tsv1-functions-tags-and-releases.png)

## 运行你的函数

标签的检查通过后，返回 Code Repositories 中的 **Code（代码）** 选项卡，从底部工具栏选择 **Functions（函数）**。你应该能在 **Published（已发布）** 部分看到新函数。选择它，尝试运行新函数：

![在函数助手中运行新函数。](https://www.palantir.com/docs/resources/foundry/functions/tsv1-functions-helper-run.png)

[进一步了解如何在整个平台中利用函数。](https://www.palantir.com/docs/foundry/functions/use-functions/)

---

*原文：[Getting started with functions](https://www.palantir.com/docs/foundry/functions/getting-started/)*
