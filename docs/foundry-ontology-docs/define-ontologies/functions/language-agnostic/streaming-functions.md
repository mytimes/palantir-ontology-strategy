# 流式函数（Streaming functions）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/streaming-functions/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

Python 和 TypeScript v2 函数可以分块流式传输执行结果。当数据随时间逐步可用时，这非常有用，它让消费方应用可以在结果完成之前就开始处理。

## 编写带流式响应的函数

要流式传输响应，Python 函数必须标注 `Iterable[T]` 返回类型，并使用 `yield` 关键字在值可用时生成它们。在 TypeScript 中，你必须通过在 `function` 关键字后添加 `*` 字符来声明异步生成器函数，指定返回类型为 `AsyncIterable<T>`，并使用 `yield` 关键字在值可用时生成它们。

以下示例演示了函数如何返回一个整数流，其中每个整数之间相隔一秒：

**Python**

```python
from functions.api import function
from typing import Iterable
import time

@function
def my_lazy_number_generator(n: int) -> Iterable[int]:
    for i in range(n):
        time.sleep(1)
        yield i
```

**TypeScript v2**

```typescript
import { Integer } from "@osdk/functions";

export default async function* myLazyNumberGenerator(n: Integer): AsyncIterable<Integer> {
    for (let i = 0; i < n; i++) {
        await new Promise(resolve => setTimeout(resolve, 1_000));
        yield i;
    }
}
```

带流式响应的函数在处理可能需要时间生成完整输出的语言模型时非常有用。通过在模型每生成一个响应数据块时就用 `yield` 将其产出，你可以提供实时体验，而不必阻塞等待响应全部完成。有关在函数中调用语言模型的更多信息，请参阅 [TypeScript v2 和 Python 函数中的语言模型](https://www.palantir.com/docs/foundry/functions/language-models-python-tsv2/)。

以下示例使用 `openai` SDK 调用语言模型，并通过在请求中启用 `stream` 标志将响应流式传回：

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
from typing import Iterable

@function
def create_chat_completion(prompt: str) -> Iterable[str]:
    client = OpenAI(
        api_key=get_foundry_token(preview=True),
        base_url=get_openai_base_url(preview=True),
        http_client=get_http_client(preview=True),
    )

    stream = client.chat.completions.create(
        model=model("gpt55").rid,
        messages=[
            {
                "role": "user",
                "content": prompt,
            },
        ],
        stream=True
    )

    for event in stream:
        if event.choices:
            content = event.choices[0].delta.content
            if content:
                yield content
```

**TypeScript v2**

```typescript
import { PlatformClient } from "@osdk/client";
import OpenAI from "openai";
import { Aliases } from "@osdk/functions";
import { getFoundryToken, getOpenAiBaseUrl, createFetch } from "@osdk/language-models";

export default async function* createChatCompletion(client: PlatformClient, prompt: string): AsyncIterable<string> {
    const oaiClient = new OpenAI({
        apiKey: await getFoundryToken(client),
        baseURL: getOpenAiBaseUrl(client),
        fetch: createFetch(client),
    });

    const stream = await oaiClient.chat.completions.create({
        model: Aliases.model("gpt55").rid,
        messages: [
            { role: 'user', content: prompt },
        ],
        stream: true
    });

    for await (const event of stream) {
        const content = event.choices[0]?.delta?.content;
        if (content) {
            yield content;
        }
    }
}
```

## 通过 Ontology SDK 调用流式函数

在为流式函数打好标签并发布后，你可以在 React 应用、Workshop 中的自定义组件或另一个函数中通过 [Ontology SDK](https://www.palantir.com/docs/foundry/ontology-sdk/overview/) 执行它。

> **ℹ️ 提示：Beta**
>
> 通过 Ontology SDK 执行带流式响应的函数目前处于 [Beta](https://www.palantir.com/docs/foundry/platform-overview/development-life-cycle/) 开发阶段。功能在活跃开发期间可能会发生变化。

**Python**

```python
from foundry_sdk_runtime import AllowBetaFeatures

with AllowBetaFeatures():
    with client.ontology.queries.create_chat_completion_streaming(prompt="Where is Mount Everest?") as stream:
        for text in stream:
            # ...
```

**TypeScript**

```typescript
import { __EXPERIMENTAL__NOT_SUPPORTED_YET__executeStreamingFunction } from "@osdk/api/unstable";

const stream = client(__EXPERIMENTAL__NOT_SUPPORTED_YET__executeStreamingFunction).executeStreamingFunction(
    createChatCompletion,
    {
        prompt: "Where is Mount Everest?",
    }
);

for await (const text of stream) {
    // ...
}
```

---

*原文：[Streaming functions](https://www.palantir.com/docs/foundry/functions/streaming-functions/)*
