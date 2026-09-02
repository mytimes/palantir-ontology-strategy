# 通过 Foundry 平台 SDK 使用平台 API（Use platform APIs with the Foundry platform SDK）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/platform-sdk/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

Foundry API 暴露了丰富的功能，你可以在函数中通过 [Foundry 平台 SDK](https://www.palantir.com/docs/foundry/api/v2/general/overview/sdks/#foundry-platform-software-development-kits) 库加以利用。你可以使用平台 SDK 构建用于管理或治理工作流的函数、与调度和构建交互、访问媒体集，等等。

> **⚠️ 注意**
>
> TypeScript v1 函数不支持原生认证（first-class authentication）。对于这些工作流，我们建议使用 Python 函数和 TypeScript v2 函数。

## 安装 SDK

要安装 Foundry 平台 SDK，请前往代码仓库中的 **Libraries**（库）侧面板，搜索 SDK 名称：Python 为 `foundry-platform-sdk`，TypeScript 为 `@osdk/foundry`。

![Libraries 搜索面板，正在搜索 Python 平台 SDK。](https://www.palantir.com/docs/resources/foundry/functions/platform-sdk-lib-panel-py.png?width=400)

![Libraries 搜索面板，正在搜索 TypeScript 平台 SDK。](https://www.palantir.com/docs/resources/foundry/functions/platform-sdk-lib-panel-ts.png?width=400)

### Python 版本兼容性

Python 版 `foundry-platform-sdk` 需要 Python 3.9 或更高版本。对 Python 3.14 的支持从 `foundry-platform-sdk` 1.93.0 版本开始。较早的 SDK 版本在 Python 3.14 下可能返回 `typing` 模块错误。要解决这些错误，请升级到 1.93.0 或更高版本。或者，使用 Python 3.11、3.12 或 3.13。

## 初始化你的客户端

你的函数需要认证才能与 Foundry API 交互。这个过程涉及实例化一个已认证的“客户端”（client），你可以通过它经 SDK 向 Foundry API 发起请求。在 TypeScript v2 代码仓库中，这需要 `@osdk/client` 库，该库应已预装。你可以通过寻找绿色图钉标记来确认：

![TypeScript 的认证库。](https://www.palantir.com/docs/resources/foundry/functions/platform-sdk-lib-panel-client-ts.png?width=400)

## 使用平台 API

函数完成认证后，你就可以开始使用 Foundry API 了。下面的示例展示了如何在 Python 和 TypeScript 中调用语言模型或查询媒体集：

**TypeScript v2**

```typescript
import {
    Client, // Use Client if your function interacts with the Ontology
    PlatformClient // Use PlatformClient if your function does not interact with the Ontology
} from "@osdk/client";
import { Functions } from "@osdk/foundry";

export default async function useLlm(
    client: PlatformClient, // This parameter gets populated by Foundry at runtime
    prompt: string
): Promise<string> {

    const promptMessage = [
        {
            role: "USER",
            content: prompt
        }
    ];
    const result = await Functions.Queries.execute(
        client,
        "com.foundry.languagemodelservice.models.gpt41.CreateChatCompletion",
        {
            parameters: {
                messages: promptMessage
            }
        },
        {
            preview: true, // Required only for unstable endpoints, see API reference
        }
    );
    return result.value["completion"] as string;
}
```

**Python**

```python
from foundry_sdk import FoundryClient

@function
def media_item_to_base64(media_item_rid: str, media_set_rid: str) -> str:
    foundry_client = FoundryClient()

    result = foundry_client.media_sets.MediaSet.read(
        media_set_rid=media_set_rid,
        media_item_rid=media_item_rid,
        preview=True  # Required only for unstable endpoints, see API reference
    )

    # Convert the binary stream to a base64 encoded string
    base64_encoded = base64.b64encode(result).decode('utf-8')

    return base64_encoded
```

### 客户端权限

TypeScript v2 客户端（由 Foundry 在运行时传入函数）和在代码中初始化的 Python 客户端具有以下权限范围：

- `api:admin-read`
- `api:functions-read`
- `api:ontologies-read`
- `api:orchestration-read`
- `api:usage:mediasets-read`
- `api:usage:ontologies-write`

每个平台 API 端点都需要特定的权限范围才能调用。有关这些权限范围的文档可在 [API 参考](https://www.palantir.com/docs/foundry/api/v2)中找到。

> **⚠️ 注意**
>
> Foundry 函数不支持使用 `api:usage:datasets-read` 权限范围直接读取数据集。读取数据集会返回权限错误。代码在 Code Workspaces 实时预览中可能成功，因为它以你的用户上下文和完整权限范围运行；但作为已部署的函数可能会失败，因为已部署的函数使用受限权限。

---

*原文：[Use platform APIs with the Foundry platform SDK](https://www.palantir.com/docs/foundry/functions/platform-sdk/)*
