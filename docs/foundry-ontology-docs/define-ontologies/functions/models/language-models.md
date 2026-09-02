# TypeScript v1 函数中的语言模型（Language models in TypeScript v1 functions）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/language-models/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

> **⚠️ 注意**
>
> 本页介绍 TypeScript v1 函数。有关 TypeScript v2 或 Python，请参阅 [TypeScript v2 和 Python 函数中的语言模型](https://www.palantir.com/docs/foundry/functions/language-models-python-tsv2/)。要使用 Ontology SDK 和可配置的资源请求，请[迁移到 TypeScript v2](https://www.palantir.com/docs/foundry/functions/typescript-v2-migration/)。

> **ℹ️ 提示：前提条件（Prerequisites）**
>
> 要使用 Palantir 提供的语言模型，[必须首先在你的注册实例上启用 AIP](https://www.palantir.com/docs/foundry/aip/enable-aip-features/)。你还必须具有使用 [AIP 构建者能力](https://www.palantir.com/docs/foundry/aip/aip-features/#aip-applications-and-builder-capabilities)的权限。

Palantir 提供了一组可在函数内使用的语言模型。[阅读更多关于 Palantir 提供的 LLM 的信息](https://www.palantir.com/docs/foundry/aip/supported-llms/)。

## 导入语言模型

要开始使用语言模型，你必须按照以下步骤将特定模型导入你编写函数所在的代码仓库：

1. 导航并打开**模型导入（Model Imports）**侧边面板，查看所有现有的已导入模型。

![模型导入侧边栏。](https://www.palantir.com/docs/resources/foundry/functions/v3-lms-functions-import-sidebar.png?width=400)

1. 要导入新的语言模型，请在**资源导入（Resource Imports）**面板的右上角选择**添加（Add）**，然后选择**模型（Models）**。这将打开一个新窗口，你可以在其中看到对你可用的 Palantir 提供的模型。

![模型导入对话框，显示几个 Palantir 提供的 LLM。](https://www.palantir.com/docs/resources/foundry/functions/v3-lms-functions-import-dialog.png?width=600)

1. 你还会看到一个选项卡，可以在其中查看以前通过 Modeling Objectives 应用或直接模型部署创建的自定义模型。有关使用这些模型的更多信息，请参阅[基于模型的函数](https://www.palantir.com/docs/foundry/functions/functions-on-models/)文档。
2. 选择你想要导入的模型，然后选择**确认选择（Confirm selection）**将这些模型导入你的代码仓库。任务运行器（Task runner）将执行 `localDev` 任务，生成与这些模型交互的代码绑定。
3. 导入语言模型后，在侧边栏中选择模型以查看该模型提供的详细能力。你还可以复制代码片段，以帮助你导入模型并使用该模型编写函数。

![侧边栏中的模型详情。](https://www.palantir.com/docs/resources/foundry/functions/v3-lms-functions-sidebar-detail.png?width=400)

## 编写使用语言模型的函数

现在，你可以编写一个使用已导入语言模型的函数。在本示例中，假设你导入了 GPT-4.1。

首先向你的文件添加以下导入语句：

```typescript
import { Function, Uses } from "@foundry/functions-api";
import { Gpt41 } from "@foundry/languagemodelservice/models";
```

每个语言模型都会生成一批带有强类型输入和输出的可用方法。例如，GPT-4.1 模型提供 `createChatCompletion`、`createChatVisionCompletion` 和 `createChatCompletionStreamed` 作为与模型交互的不同 API。能力列表可能会在导入模型的后续版本中扩展。

Foundry 必须知道你从已发布函数中调用了哪些模型方法。静态分析会自动检测大多数调用；静态分析遗漏的调用会导致运行时错误，提示你添加 `@Uses` 装饰器，该装饰器会补充自动检测到的使用情况。在装饰器的 `queries` 键下声明模型方法，如下方及模型详情面板中的代码片段所示。

在以下说明性示例中，使用提供的 GPT-4.1 模型对用户提供的一段文本或图像运行简单的情感分析。该函数会将文本分类为 “Good”、“Bad” 或 “Uncertain”。

```typescript
const SYSTEM_PROMPT =
    "Provide an estimation of the sentiment the text the user has provided. \
You may respond with either Good, Bad, or Uncertain. Only choose Good or Bad if you are overwhelmingly \
sure that the text is either good or bad. If the text is neutral, or you are unable to determine, choose Uncertain.";

export class MyFunctions {
    @Function()
    @Uses({ queries: [Gpt41.createChatCompletion] })
    public async llmFunction_createChatCompletion(userPrompt: string): Promise<string | undefined> {
        const response = await Gpt41.createChatCompletion({
            messages: [
                {
                    role: "SYSTEM",
                    content: SYSTEM_PROMPT,
                },
                {
                    role: "USER",
                    content: userPrompt,
                },
            ],
            params: {
                temperature: 0,
            },
        });
        return response.type === "ok" ? response.value.completion : "error";
    }

    @Function()
    @Uses({ queries: [Gpt41.createChatVisionCompletion] })
    public async llmFunction_createChatVisionCompletion(
        userPrompt: string,
        pngBase64String: string,
    ): Promise<string | undefined> {
        const response = await Gpt41.createChatVisionCompletion({
            messages: [
                {
                    role: "USER",
                    content: [
                        { type: "text", text: userPrompt },
                        {
                            type: "genericMedia",
                            genericMedia: {
                                mimeType: "IMAGE_PNG",
                                // Base64 encoded PNG String
                                content: pngBase64String,
                            },
                        },
                    ],
                },
            ],
            params: {
                temperature: 0,
            },
        });
        return response.type === "ok" ? response.value.completion : "error";
    }
}
```

然后，该函数可以在整个平台中使用。

[`@Uses` 装饰器](https://www.palantir.com/docs/foundry/functions/query-functions/#call-a-query-function)也适用于导入的模型。在 `queries` 下声明这些调用，例如 `@Uses({ queries: [Gpt41.createChatCompletion] })`。

## 嵌入

除了生成式语言模型，Palantir 还提供可用于生成嵌入的模型。一个简单的示例如下：

```typescript
@Function()
@Uses({ queries: [Textembedding3large.createEmbeddings] })
public async llmFunction_embeddings(inputs: string[]): Promise<Double[][]> {
    const response = await Textembedding3large.createEmbeddings({ inputs });
    return response.type === "ok" ? response.value.embeddings : [[]];
}
```

这最常用于执行[语义搜索](https://www.palantir.com/docs/foundry/ontology/using-palantir-provided-models-to-create-a-semantic-search-workflow/)工作流。

## 从函数内的旧版语言模型升级

> **ℹ️ 提示**
>
> 如果你是从一个没有导入[旧版语言模型](https://www.palantir.com/docs/foundry/functions/language-models-legacy/)的新代码仓库开始，请跳过此步骤。

> **⚠️ 注意**
>
> 以下过程会导致代码仓库编译失败，因为代码语法将被更新。请参阅侧边栏中每个已更新模型的代码片段来更新你的代码。

函数中更新后的语言模型提供更高级的能力，例如对视觉和流式的更好支持。我们强烈建议升级你的代码仓库，以利用最新的 AIP 功能。

1. 如果你已导入现有的[旧版语言模型](https://www.palantir.com/docs/foundry/functions/language-models-legacy/)，侧边栏中会出现一个用于升级的警告图标。点击 **Select imports（选择导入）**以打开模型导入对话框。

![侧边栏中的模型导入警告。](https://www.palantir.com/docs/resources/foundry/functions/v3-lms-functions-migration-sidebar.png?width=400)

1. 在模型导入对话框中，选择**修复（Fix）**以移除任何已弃用的旧版语言模型。

![带有警告的模型导入对话框。](https://www.palantir.com/docs/resources/foundry/functions/v3-lms-functions-migration-dialog-warning.png?width=400)

1. 重新选择模型以迁移到更新后的语言模型版本。你可以在对话框中央的详情面板中查看此版本支持的额外能力。

![模型导入对话框显示已移除的模型。](https://www.palantir.com/docs/resources/foundry/functions/v3-lms-functions-migration-dialog-selected.png?width=400)

1. 现在，你可以在侧边栏中查看更新后的模型导入。选择模型会显示一个带有代码片段的详情面板，以帮助你更新代码来利用额外能力。

![侧边栏中的模型详情和代码片段。](https://www.palantir.com/docs/resources/foundry/functions/v3-lms-functions-sidebar-detail.png?width=400)

## 性能注意事项

某些模型可能应用了速率限制，限制在特定时间段内可以传递的令牌（token）数量。这将与适用于函数的任何标准限制一起强制执行。

---

注意：AIP 功能的可用性可能会发生变化，并且可能因客户而异。

---

*原文：[Language models in TypeScript v1 functions](https://www.palantir.com/docs/foundry/functions/language-models/)*
