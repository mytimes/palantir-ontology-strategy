# TypeScript v1 函数入门（Getting started with TypeScript v1 functions）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/typescript-v1-getting-started/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

> **⚠️ 注意**
>
> 以下文档专门针对 TypeScript v1 函数。本页的 TypeScript v2 版本请参阅 [TypeScript v2 函数入门](https://www.palantir.com/docs/foundry/functions/typescript-v2-getting-started/)。若需要更[强大的能力](https://www.palantir.com/docs/foundry/functions/language-feature-support/#typescript-v1-vs-typescript-v2)，包括对 Ontology SDK 和可配置资源请求的支持，我们建议[迁移到 TypeScript v2](https://www.palantir.com/docs/foundry/functions/typescript-v2-migration/)。

## 创建 TypeScript v1 函数仓库

导航到你选择的项目，通过选择 **+ New（+ 新建）> Repository（仓库）** 创建一个新的代码仓库。选择 TypeScript functions 模板来初始化你的仓库。

![创建 TypeScript v1 函数代码仓库。](https://www.palantir.com/docs/resources/foundry/functions/tsv1-functions-create-repo.png)

仓库创建完成后，导航到 `functions-typescript/src/index.ts` 文件。

## 编写函数

此仓库中的函数必须定义在 TypeScript 类中，并且该类必须从 `functions-typescript/src/index.ts` 文件导出。你可以在 `index.ts` 中预填充的示例里编写函数，也可以创建一个新文件。如果创建新文件，请确保从 `index.ts` 导出你的类。

下面是一个基本示例：

**TypeScript v1**

```typescript
import { Function, Integer } from "@foundry/functions-api";

export class ExampleFunctions {

    @Function()
    public addIntegers(a: Integer, b: Integer): Integer {
         return a + b;
    }
}
```

如果上述代码写在一个名为 `exampleFunctions.ts` 的文件中，则必须如下所示从索引文件导出：

**TypeScript v1**

```typescript
// in functions-typescript/src/index.ts

export * from "./relative/path/to/exampleFunctions";
```

## 在实时预览中测试

添加新函数后，你可以在函数助手中运行它。打开 **Functions（函数）** 助手并选择 **Live Preview（实时预览）**。选择 `range` 函数，输入参数值，然后选择 **Run（运行）** 来运行代码。

![在函数助手中运行你的新函数。](https://www.palantir.com/docs/resources/foundry/functions/tsv1-functions-helper-preview-run.png)

选择右上角的 **Commit（提交）**，将你的更改提交到仓库的 `master` 分支。

## 发布函数

提交你的工作后，你会看到 **Tag version（标记版本）** 选项。这将发布仓库中的所有函数。

![为分支标记新版本的选项。](https://www.palantir.com/docs/resources/foundry/functions/ts-functions-tags.png)

选择 **Tag version（标记版本）** 以从 `master` 分支标记一个发布。根据你更改的程度设置标记名称，然后选择 **Tag and release（标记并发布）**。

![为新发布选择要标记的版本类型。](https://www.palantir.com/docs/resources/foundry/functions/new-functions-tag.png?width=500)

要查看函数标记和发布过程的进度，选择 **View（查看）** 弹窗或导航到 **Tags（标记）** 选项卡。**Step 2: Release（第 2 步：发布）** 完成后，选择已发布的函数以在函数注册表中查看它们。

> **⚠️ 注意**
>
> 在权限传播期间，函数可能无法立即在 Workshop 或函数注册表中按名称搜索到。

![标记和发布检查均已通过，新函数已发布。](https://www.palantir.com/docs/resources/foundry/functions/tsv1-functions-tags-and-releases.png)

## 使用新函数

你的标记检查通过后，导航回 **Code Repositories** 中的 **Code（代码）** 选项卡，并选择 **Functions（函数）** 助手。你现在应该能够在 **Published（已发布）** 部分下看到你的新 `range` 函数。选择并运行该函数。

![在函数助手中运行新函数。](https://www.palantir.com/docs/resources/foundry/functions/tsv1-functions-helper-run-2.png)

### 后续步骤

在本教程中，你学习了如何使用 Code Repositories 从仓库编写、发布和测试 TypeScript v1 函数。接下来，我们建议学习如何编写[对象函数](https://www.palantir.com/docs/foundry/functions/foo-getting-started/)。

---

*原文：[Getting started with TypeScript v1 functions](https://www.palantir.com/docs/foundry/functions/typescript-v1-getting-started/)*
