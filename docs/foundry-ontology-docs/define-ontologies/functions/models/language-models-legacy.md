# 函数中的旧版语言模型（Legacy language models within functions）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/language-models-legacy/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

> **🚨 警告：旧版**
>
> 本页记录的是函数中**旧版**语言模型。对于 TypeScript v2 和 Python，请参阅[使用代理端点和模型别名的工作流](https://www.palantir.com/docs/foundry/functions/language-models-python-tsv2/)。更新后的 [TypeScript v1 语言模型](https://www.palantir.com/docs/foundry/functions/language-models/)支持视觉和流式等能力。要使用更新后的 TypeScript v1 模型，请遵循[升级说明](https://www.palantir.com/docs/foundry/functions/language-models/#upgrade-from-legacy-language-models-within-functions)。

Palantir 提供了一组可在函数中使用的语言模型。[详细了解 Palantir 提供的 LLM](https://www.palantir.com/docs/foundry/aip/supported-llms/)。

> **ℹ️ 提示：前提条件**
>
> 要使用 Palantir 提供的语言模型，[必须先在你的注册实例上启用 AIP](https://www.palantir.com/docs/foundry/aip/enable-aip-features/)。你还必须拥有使用 [AIP 构建者能力](https://www.palantir.com/docs/foundry/aip/aip-features/#aip-applications-and-builder-capabilities)的权限。

## 导入语言模型

要开始使用语言模型，你必须按照以下步骤将特定模型导入到你编写函数的代码仓库中：

1. 导航并打开 **Model Imports**（模型导入）侧面板，查看所有现有的已导入模型。

![模型导入侧边栏。](https://www.palantir.com/docs/resources/foundry/functions/language-model-import-sidebar.png?width=400)

1. 要导入新的语言模型，在 **Resource Imports**（资源导入）面板的右上角选择 **Add**（添加），然后选择 **Models**（模型）。这将打开一个新窗口，你可以在其中查看可供你使用的 Palantir 提供的模型。

![模型导入对话框，展示了几个 Palantir 提供的 LLM。](https://www.palantir.com/docs/resources/foundry/functions/language-model-import-dialog.png?width=600)

1. 你还会看到一个选项卡，可以在其中查看之前通过 Modeling Objectives 应用或直接模型部署所创建的自定义模型。有关使用这些模型的更多信息，请参阅[基于模型的函数](https://www.palantir.com/docs/foundry/functions/functions-on-models/)文档。
2. 选择你想要导入的模型，然后选择 **Confirm selection**（确认选择），将这些模型导入到你的代码仓库中。Task runner 将执行 `localDev` 任务，生成用于与这些模型交互的代码绑定。
3. 导入语言模型后，你现在可以通过添加以下导入语句在代码仓库中使用它们，将 GPT_4o 替换为你已导入代码仓库的语言模型的名称：

```typescript
import { GPT_4o } from "@foundry/models-api/language-models"
```

## 编写使用语言模型的函数

在此阶段，我们可以编写一个使用已导入语言模型的函数。在本示例中，我们假设已按上述方式导入了 GPT_4o。

我们首先向文件中添加以下导入语句：

```typescript
import { GPT_4o } from "@foundry/models-api/language-models"
```

每个语言模型都会生成带有强类型输入和输出的可用方法。例如，GPT_4o 模型提供了 createChatCompletion 方法，允许用户传入一组消息以及用于修改模型行为的附加参数，例如温度（temperature）或最大 token 数。

在下面的说明性示例中，我们使用提供的 GPT_4o 模型对用户提供的一段文本运行简单的情感分析。该函数会将文本分类为“Good”、“Bad”或“Uncertain”。

```typescript
@Function()
public async sentimentAnalysis(userPrompt: string): Promise<string> {
    const systemPrompt = "Provide an estimation of the sentiment the text the user has provided. \
    You may respond with either Good, Bad, or Uncertain. Only choose Good or Bad if you are overwhelmingly \
    sure that the text is either good or bad. If the text is neutral, or you are unable to determine, choose Uncertain."

    const systemMessage = { role: "SYSTEM", contents: [{ text: systemPrompt }] };
    const userMessage = { role: "USER", contents: [{ text: userPrompt }] };
    const gptResponse = await GPT_4o.createChatCompletion({messages: [systemMessage, userMessage], params: { temperature: 0.7 } });
    return gptResponse.choices[0].message.content ?? "Uncertain";
}
```

之后，该函数就可以在整个平台中使用。

## 嵌入

除了生成式语言模型之外，Palantir 还提供可用于生成嵌入的模型。一个简单的示例如下：

```typescript
@Function()
public async generateEmbeddingsForText(inputs: string[]): Promise<Double[][]> {
    const response = await TextEmbeddingAda_002.createEmbeddings({ inputs });
    return response.embeddings;
}
```

这最常用于执行[语义搜索](https://www.palantir.com/docs/foundry/ontology/using-palantir-provided-models-to-create-a-semantic-search-workflow/)工作流。

## 性能注意事项

某些模型可能设有速率限制，限制在特定时间段内可传入的 token 数量。这将与适用于函数的任何标准限制一起强制执行。

---

注意：AIP 功能的可用性可能会发生变化，并且在不同客户之间可能有所不同。

---

*原文：[Legacy language models within functions](https://www.palantir.com/docs/foundry/functions/language-models-legacy/)*
