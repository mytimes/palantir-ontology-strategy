# TypeScript v2 和 Python 函数中的语言模型（Language models in TypeScript v2 and Python functions）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/language-models-python-tsv2/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

> **ℹ️ 提示：前提条件**
>
> 要使用 Palantir 提供的语言模型，[必须在你的注册实例上启用 AIP](https://www.palantir.com/docs/foundry/aip/enable-aip-features/)。你还必须拥有使用 [AIP 构建者能力](https://www.palantir.com/docs/foundry/aip/aip-features/#aip-applications-and-builder-capabilities)的权限。

Palantir 提供了一组可在函数中使用的语言模型。[详细了解 Palantir 提供的 LLM](https://www.palantir.com/docs/foundry/aip/supported-llms/)。

## 导入语言模型

要开始使用语言模型，你必须按照以下步骤将特定模型导入你的函数代码仓库：

1. 打开 **Resource imports** 面板中的 **Platform SDK** 选项卡。

![TypeScript v2 代码仓库中访问 Platform SDK 资源的选项卡。](https://www.palantir.com/docs/resources/foundry/functions/platform-sdk-tab.png)

2. 要导入新的语言模型，请在右上角选择 **Add > Models**。将打开一个窗口，你可以在其中查看可用的模型，包括 Palantir 提供的模型和已注册模型。

![TypeScript v2 代码仓库中的模型导入对话框。](https://www.palantir.com/docs/resources/foundry/functions/models-v3-import-dialog.png)

3. 选择要导入的模型，然后选择 **Confirm selection**。将打开一个配置对话框，你可以在其中为每个所选模型配置别名。选择别名旁边的笔形图标进行编辑，或选择保留默认值。

> **⚠️ 注意**
>
> 每个模型都必须有别名，且别名在代码仓库内必须唯一。

![选择要导入的模型后配置模型别名。](https://www.palantir.com/docs/resources/foundry/functions/configure-models-aliases.png)

4. 导入的模型将显示在 **Resource imports** 侧边面板的 **Platform SDK** 选项卡中。你可以选择别名旁边的笔形图标，内联编辑任何别名。

![内联配置模型别名。](https://www.palantir.com/docs/resources/foundry/functions/inline-models-aliases-edit.png)

> **ℹ️ 提示**
>
> 你还可以在 **Platform SDK** 选项卡中定义[自定义别名](https://www.palantir.com/docs/foundry/functions/custom-aliases/)。自定义别名存储配置参数、功能开关或特定环境的设置，安装者可以在 Marketplace 安装期间自定义这些内容。

## 编写使用语言模型的函数

TypeScript v2 和 Python 函数中的语言模型使用代理端点与模型交互。以下示例使用 [OpenAI 聊天补全代理端点](https://www.palantir.com/docs/foundry/api/v2/llm-apis/models/openai-chat-completions-proxy/)。你可以从文档侧边面板中选择其他提供商。

> **ℹ️ 提示**
>
> 第三方库（如下例中的 `openai`）未预装。请从左侧边栏的 **Libraries** 部分安装它们。

要在函数中使用已导入的语言模型，首先导入必要的工具：

**TypeScript v2**

```typescript
import { PlatformClient } from "@osdk/client";
import OpenAI from "openai";
import { Aliases } from "@osdk/functions";
import { getFoundryToken, getOpenAiBaseUrl, createFetch } from "@osdk/language-models";
```

**Python**

```python
from openai import OpenAI
from functions.api import function
from functions.aliases import model
from foundry_sdk.v2.language_models import (
    get_openai_base_url,
    get_foundry_token,
    get_http_client,
)
```

使用你配置的模型别名以及导入的工具直接调用模型。这种方法比 TypeScript v1 工作流更简单，并减少了硬编码资源标识符的需要。

**TypeScript v2**

```typescript
export default async function callOpenAi(client: PlatformClient, prompt: string): Promise<string> {
    const oaiClient = new OpenAI({
        apiKey: await getFoundryToken(client),
        baseURL: getOpenAiBaseUrl(client),
        fetch: createFetch(client),
    });

    const completion = await oaiClient.chat.completions.create({
        model: Aliases.model("{MY_ALIAS}").rid,
        messages: [
            { role: 'user', content: prompt },
        ],
        reasoning_effort: "minimal",
        max_completion_tokens: 200,
    });

    return completion.choices[0]?.message.content ?? "";
}
```

**Python**

```python
@function
def get_chat_completion(prompt: str) -> str:
    client = OpenAI(
        api_key=get_foundry_token(preview=True),
        base_url=get_openai_base_url(preview=True),
        http_client=get_http_client(preview=True),
    )

    completion = client.chat.completions.create(
        model=model("{MY_ALIAS}").rid,
        messages=[
            {
                "role": "user",
                "content": prompt,
            },
        ],
    )

    return str(completion.choices[0].message.content)
```

---

*原文：[Language models in TypeScript v2 and Python functions](https://www.palantir.com/docs/foundry/functions/language-models-python-tsv2/)*
