# TypeScript v2 函数入门（Getting started with TypeScript v2 functions）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/typescript-v2-getting-started/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

TypeScript v2 允许用户利用相比 TypeScript v1 的若干关键[改进](https://www.palantir.com/docs/foundry/functions/language-feature-support/#typescript-v1-vs-typescript-v2)，包括 Node.js 运行时和一等公民级的 OSDK 支持。请阅读以下各节以开始使用。

## 创建 TypeScript v2 函数仓库

导航到你选择的项目，通过选择 **+ New（+ 新建）> Repository（仓库）** 创建一个新的代码仓库。选择 TypeScript v2 functions 模板来初始化你的仓库。

![创建 TypeScript v2 函数代码仓库。](https://www.palantir.com/docs/resources/foundry/functions/tsv2-functions-create-repo.png)

仓库创建完成后，导航到 `typescript-functions/src/functions/helloWorld.ts` 文件。

在将本体类型导入函数之前，请通过**资源导入**侧边栏添加你的对象类型和链接类型，然后生成并安装 Ontology SDK。这些步骤请参阅[生成 Ontology SDK](https://www.palantir.com/docs/foundry/functions/typescript-v2-migration/#generate-the-ontology-sdk)。

## 编写函数

要编写新函数，请在仓库的 `typescript-functions/src/functions` 目录中创建一个新文件，并给它起一个描述性名称，例如 `helloWorld.ts`。使用 `export default` 编写你的函数，以便 Foundry 检测到它。

**TypeScript v2**

```typescript
export default function helloWorld(): string {
    return "Hello World!";
}
```

你的函数必须满足以下条件才能发布到 Foundry：

1. 在 `typescript-functions/src/functions` 目录中的 `.ts` 文件里定义你的函数。在此目录中，你还可以将相关函数分组到子目录中。
2. 文件名必须与函数名匹配。要发布一个名为 `myFunction` 的函数，它必须定义在 `typescript-functions/src/functions` 目录中名为 `myFunction.ts` 的文件里。
3. TypeScript 函数必须是该文件的默认导出。
4. 函数的输入和输出类型必须遵循支持的函数类型，详见[类型参考](https://www.palantir.com/docs/foundry/functions/types-reference/)。

函数的文件路径用于唯一标识从中发布的函数。请注意，函数文件路径的变更将导致发布一个新函数。

## 在实时预览中测试

要在实时预览中测试你的函数，打开 **Functions（函数）** 助手并选择 **Live preview（实时预览）**。选择你的函数并选择 **Run（运行）** 以执行。

![在函数助手中运行你的新函数。](https://www.palantir.com/docs/resources/foundry/functions/tsv2-functions-helper-preview-run.png?width=900)

## 提交并发布函数

选择窗口右上角的 **Commit（提交）**，将你的更改提交到仓库的 `master` 分支。要查看函数的检查，请打开页面顶部的 **Checks（检查）** 选项卡。在这里，提交之后你应该会看到一个正在运行的检查。

![选择检查以查看进度。](https://www.palantir.com/docs/resources/foundry/functions/tsv2-functions-publish.png?width=500)

提交你的工作后，你会看到 **Tag version（标记版本）** 选项。这将发布仓库中的所有函数。

![可用的标记选项。](https://www.palantir.com/docs/resources/foundry/functions/ts-functions-tags.png)

选择 **Tag version** 以从 `master` 分支标记一个发布。根据你更改的程度设置标记名称，然后选择 **Tag and release（标记并发布）**。

![为新发布选择要标记的版本类型。](https://www.palantir.com/docs/resources/foundry/functions/new-functions-tag.png?width=500)

要查看函数标记和发布过程的进度，选择 **View（查看）** 弹窗或导航到 **Tags（标记）** 选项卡。**Step 2: Release（第 2 步：发布）** 完成后，选择已发布的函数以在函数注册表中查看它们。

> **⚠️ 注意**
>
> 在权限传播期间，函数可能无法立即在 Workshop 或函数注册表中按名称搜索到。

![标记和发布检查均已通过，新函数已发布。](https://www.palantir.com/docs/resources/foundry/functions/tsv2-functions-tags-and-releases.png)

## 使用新函数

你的标记检查通过后，导航回 **Code Repositories** 中的 **Code（代码）** 选项卡，并选择 **Functions** 助手。你现在应该能够在 **Published（已发布）** 部分下看到你的函数。选择它并运行新函数：

![在函数助手中运行新函数。](https://www.palantir.com/docs/resources/foundry/functions/tsv2-functions-helper-run.png)

## 后续步骤

创建并发布一个基本函数之后，探索以下能力：

- **Ontology SDK：** 要查询或编辑本体对象，首先导入你需要的对象类型和链接类型，然后[生成并安装 Ontology SDK](https://www.palantir.com/docs/foundry/functions/typescript-v2-migration/#generate-the-ontology-sdk)。
- **本体编辑：** 了解如何在你的函数中[创建、更新和删除对象](https://www.palantir.com/docs/foundry/functions/typescript-v2-ontology-edits/)。
- **暂存写入 [Beta]：** 对于需要写后读保证或嵌套函数调用的编辑函数，请参阅[暂存写入](https://www.palantir.com/docs/foundry/functions/typescript-v2-staged-writes/)。

---

*原文：[Getting started with TypeScript v2 functions](https://www.palantir.com/docs/foundry/functions/typescript-v2-getting-started/)*
