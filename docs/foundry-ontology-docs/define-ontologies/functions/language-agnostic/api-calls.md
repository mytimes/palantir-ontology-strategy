# 从函数发起 API 调用（Make API calls from functions）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/api-calls/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

可以从 TypeScript v1、TypeScript v2 和 Python 函数向外部数据源发起 API 调用，但这需要额外配置。下文详细介绍该配置以及外部数据源的使用。

> **ℹ️ 提示：数据源别名（Source aliases）**
>
> 对于 TypeScript v2 和 Python 函数，我们建议通过[数据源别名](https://www.palantir.com/docs/foundry/functions/source-aliases/)引用数据源。数据源别名是一个可移植的命名引用，你可以用它代替特定数据源作为数据源标识符。当你的函数通过 [Marketplace 产品](https://www.palantir.com/docs/foundry/functions/marketplace-functions/)分发时，该别名可以按环境重新映射到不同的数据源，从而保持函数代码的可移植性。TypeScript v1 函数使用生成的数据源符号，不支持别名。

## 配置对外部 API 的访问

默认情况下，不允许函数调用外部 API。要允许从你的函数调用外部系统，你必须在 [Data Connection](https://www.palantir.com/docs/foundry/data-connection/overview/)（数据连接）中[配置一个数据源](https://www.palantir.com/docs/foundry/data-connection/set-up-source/)，以允许 Foundry 连接外部系统。

为了让函数安全地连接到数据源的外部系统，你的数据源必须配置为[启用导出](https://www.palantir.com/docs/foundry/data-connection/export-overview/#enable-exports-for-source)，并允许[将数据源导入 Code Repositories](https://www.palantir.com/docs/foundry/data-connection/external-transforms/#prerequisite-import-a-source-into-code)。这两项都可以通过在 Data Connection 中导航到该数据源并打开**连接设置（Connection settings）**部分来配置。

对于 TypeScript v1 函数，数据源的 API 名称（在**连接设置**下的**代码导入配置（Code import configuration）**选项卡中配置）就是你在代码中引用的标识符。

> **ℹ️ 提示**
>
> 请确保在数据源中完整配置证书链。
> Webhook 与函数运行时环境并不完全相同。
> 有时 Webhook 可以正常工作，而从函数发起的 API 调用可能会遇到 `UNABLE_TO_GET_ISSUER_CERT` 错误。
> 请参阅我们关于[数据源终端中的 `openssl` 命令](https://www.palantir.com/docs/foundry/data-connection/troubleshooting/#openssl)的文档来验证证书。

## 在函数中使用外部数据源

要从函数发起 API 调用，你必须首先使用[资源导入侧边栏](https://www.palantir.com/docs/foundry/functions/resource-imports-sidebar/)将数据源导入函数代码仓库。对于 TypeScript v2 和 Python 函数，我们建议随后[创建一个数据源别名](https://www.palantir.com/docs/foundry/functions/source-aliases/)，并使用其别名键作为数据源标识符。TypeScript v1 函数直接引用导入的数据源。然后，你必须声明函数使用了该数据源，如下方示例所示。

示例如下：

**TypeScript v1**

```typescript
import { ExternalSystems } from "@foundry/functions-api";
import { MySource } from "@foundry/external-systems/sources";

export class MyExternalFunctions {
    @ExternalSystems({ sources: [MySource] })
    @Function()
    public async myExternalFunction(): Promise<string> {
        const { url } = MySource.getHttpsConnection();
        const response = await MySource.fetch(url);

        return response.text();
    }
}
```

**TypeScript v2**

```typescript
import { getSource, getHttpsConnection, getFetch } from "@palantir/functions-sources";

export const config = {
    sources: ["mySourceAlias"]
}

async function MyExternalFunction(): Promise<string> {
    const source = await getSource("mySourceAlias");
    const { url } = getHttpsConnection(source);
    const fetch = await getFetch(source);

    const response = await fetch(url);

    return response.text();
}
```

**Python**

```python
from functions.api import function
from functions.sources import get_source


@function(sources=["mySourceAlias"])
def my_external_function() -> str:
    source = get_source("mySourceAlias")
    url = source.get_https_connection().url
    client = source.get_https_connection().get_client()
    response = client.get(url)
    return response.text
```

你可以在实时预览中测试函数，并在发布后用它发起外部调用。

> **⚠️ 注意**
>
> **在不覆盖 fetch 函数或 HTTP agent 的情况下，无服务器执行或实时预览尚不支持第三方客户端。** 为确保你的 API 调用在所有环境中正常工作，你必须使用相应的库方法以正确的配置发起请求。直接调用外部数据源或 Foundry 内部 URL 的 API 不保证在所有环境中都能工作。

## 访问数据源属性和凭据

你可以访问每种函数类型对应的库所提供的数据源属性。

下面的示例展示如何获取上例中数据源的基础 URL。

**TypeScript v1**

```typescript
const { url } = MySource.getHttpsConnection();
```

**TypeScript v2**

```typescript
const { url } = getHttpsConnection(source);
```

**Python**

```python
url = get_source("mySourceAlias").get_https_connection().url
```

你还可以使用以下语法访问存储在数据源上的其他密钥或凭据：

**TypeScript v1**

```typescript
const secret = MySource.getSecret("MySecret");
```

**TypeScript v2**

```typescript
const secret = source.secrets["MySecret"];
```

**Python**

```python
secret = get_source("mySourceAlias").get_secret("MySecret")
```

## 使用预配置的客户端

对于提供 REST API 的数据源，数据源对象允许你获取一个客户端。该客户端已预先配置好数据源上指定的服务器证书和客户端证书，还会包含额外的代理配置，以允许从函数运行环境出站。如果可能，你应始终使用此客户端，以保证函数能从所有环境出站访问该数据源。

**TypeScript v1**

```typescript
const fetch = MySource.fetch;
```

**TypeScript v2**

```typescript
const fetch = await getFetch(source);
```

**Python**

```python
client = source.get_https_connection().get_client()
```

或者，你可以使用自己的客户端或发起外部请求的第三方库，并使用数据源对象来[获取属性和凭据](#访问数据源属性和凭据)。

TypeScript v2 函数还提供预配置的 HTTP agent，作为额外的集成点，供接受自定义 HTTP agent 的第三方库使用。

以下示例演示如何获取此 agent 并将其与 [axios ↗](https://github.com/axios/axios) 一起使用。

**TypeScript v2**

```typescript
import { getHttpAgent, getHttpsConnection } from "@palantir/functions-sources";
import axios from 'axios';

const agent = await getHttpAgent(source);
const { url } = getHttpsConnection(source);

const response = await axios.get(url, {
    httpsAgent: agent,
});
```

> **ℹ️ 提示**
>
> 目前，除非数据源提供 HTTPS 客户端，否则无法访问非凭据的数据源属性。例如，你将无法访问 [PostgreSQL 数据源](https://www.palantir.com/docs/foundry/available-connectors/postgresql/)上的 `hostname` 或其他非密钥属性。

## 将 OAuth 2.0 与出站应用配合使用

如果你的外部 API 需要 OAuth 2.0 授权，你可以在 Control Panel（控制面板）中配置一个[出站应用](https://www.palantir.com/docs/foundry/administration/configure-outbound-applications/)，并将其用作 REST API 数据源的身份验证方法。函数运行时，数据源会将调用用户的 OAuth 访问令牌作为会话凭据暴露出来。然后，你的函数可以使用该令牌代表用户调用外部 API。

Python 和 TypeScript v2 函数支持此模式。代码示例见下文的[使用数据源的预配置客户端](#使用数据源的预配置客户端)。

### 限制

- **TypeScript v1：** TypeScript v1 函数无法直接从数据源获取 OAuth 令牌。要从 TypeScript v1 函数向 OAuth 2.0 API 进行身份验证，请在配置了出站应用的 REST API 数据源上，将调用包装在 [Webhook](https://www.palantir.com/docs/foundry/functions/webhooks/) 中。可以考虑[迁移到 TypeScript v2](https://www.palantir.com/docs/foundry/functions/typescript-v2-migration/) 以直接访问令牌。
- **部署模式（Deployed mode）：** 函数在[部署模式](https://www.palantir.com/docs/foundry/functions/functions-deployed/)下运行时，OAuth 令牌刷新不可用。如果调用用户的访问令牌在执行期间过期，函数无法自动刷新。请在[无服务器模式](https://www.palantir.com/docs/foundry/functions/functions-deployed/#choose-between-deployed-and-serverless-execution-modes)下运行函数，以使用 OAuth 支撑的出站应用。
- **在 Workshop 中直接使用函数：** 在 [Workshop](https://www.palantir.com/docs/foundry/workshop/overview/) 模块中直接使用的函数（例如[函数支撑的变量](https://www.palantir.com/docs/foundry/workshop/functions-use/#function-backed-variables-in-workshop)或填充组件内容的函数）无法触发 OAuth 2.0 交互式授权提示。如果用户尚未授权该出站应用，函数将失败而不是显示提示。要从 Workshop 使用 OAuth 支撑的函数，请将其包装在[函数支撑的动作](https://www.palantir.com/docs/foundry/action-types/function-actions-overview/)中。或者，确保用户在函数于 Workshop 中被直接调用之前，从另一个交互式界面（例如针对同一出站应用的函数支撑动作）完成授权流程。

### 使用数据源的预配置客户端

最简单的方法是使用数据源提供的 HTTP 客户端。`Authorization` 标头会自动注入。

**Python**

```python
from functions.api import function
from functions.sources import get_source

@function(sources=["myOAuthSourceAlias"])
def call_external_api() -> str:
    source = get_source("myOAuthSourceAlias")
    url = source.get_https_connection().url
    client = source.get_https_connection().get_client()

    response = client.get(url + "/api/v1/resource", timeout=10)
    return response.text
```

**TypeScript v2**

```typescript
import { getSource, getHttpsConnection, getFetch } from "@palantir/functions-sources";

export const config = {
    sources: ["myOAuthSourceAlias"]
};

export default async function callExternalApi(): Promise<string> {
    const source = await getSource("myOAuthSourceAlias");
    const { url } = getHttpsConnection(source);
    const fetch = await getFetch(source);

    const response = await fetch(url + "/api/v1/resource");

    return response.text();
}
```

### 使用原生 HTTP 客户端并手动注入令牌

如果你需要使用自己的 HTTP 客户端而非数据源提供的客户端，请从会话凭据中获取 OAuth 令牌并手动设置 `Authorization` 标头。

**Python**

```python
import requests
from functions.api import function
from functions.sources import get_source
from external_systems.sources import OauthCredentials, Refreshable, SourceCredentials

@function(sources=["myOAuthSourceAlias"])
def call_external_api() -> str:
    source = get_source("myOAuthSourceAlias")
    url = source.get_https_connection().url

    refreshable_credentials: Refreshable[SourceCredentials] = source.get_session_credentials()
    session_credentials: SourceCredentials = refreshable_credentials.get()

    if not isinstance(session_credentials, OauthCredentials):
        raise ValueError("Expected OAuth credentials")

    access_token: str = session_credentials.access_token

    response = requests.get(
        url + "/api/v1/resource",
        headers={"Authorization": f"Bearer {access_token}"},
        timeout=10,
    )
    return response.text
```

**TypeScript v2**

```typescript
import { getSource, getHttpsConnection } from "@palantir/functions-sources";

export const config = {
    sources: ["myOAuthSourceAlias"]
};

export default async function callExternalApi(): Promise<string> {
    const source = await getSource("myOAuthSourceAlias");
    const credentials = await source.sessionCredentials?.get();

    if (!credentials || credentials.type !== "oauth") {
        throw new Error("Expected OAuth credentials");
    }

    const accessToken: string = credentials.accessToken;
    const { url } = getHttpsConnection(source);

    const response = await fetch(url + "/api/v1/resource", {
        headers: { Authorization: `Bearer ${accessToken}` },
    });

    return response.text();
}
```

### 在动作中使用 OAuth 支撑的函数

一个常见模式是调用 OAuth 支撑的外部 API，并将结果传入[本体编辑](https://www.palantir.com/docs/foundry/functions/edits-overview/)。然后，你可以通过[函数支撑的动作](https://www.palantir.com/docs/foundry/action-types/function-actions-overview/)暴露该函数。当用户从 Workshop 或 AIP Studio 运行动作时，会使用他们的 OAuth 令牌发起 API 调用，并且由此产生的对象编辑会归属到他们名下。

例如，下面的函数使用 OAuth 令牌从第三方身份服务获取调用用户的个人资料，然后用该信息创建一个新的本体对象：

**Python**

```python
from functions.api import function, OntologyEdit
from functions.sources import get_source
from ontology_sdk import FoundryClient
from ontology_sdk.ontology.objects import UserProfile


@function(sources=["myOAuthSourceAlias"], edits=[UserProfile])
def link_user_profile() -> list[OntologyEdit]:
    source = get_source("myOAuthSourceAlias")
    url = source.get_https_connection().url
    client = source.get_https_connection().get_client()

    response = client.get(url + "/v1/me", timeout=10)
    response.raise_for_status()
    profile = response.json()

    ontology_edits = FoundryClient().ontology.edits()
    ontology_edits.objects.UserProfile.create(
        profile["id"],
        display_name=profile["display_name"],
    )
    return ontology_edits.get_edits()
```

**TypeScript v2**

```typescript
import { getSource, getHttpsConnection, getFetch } from "@palantir/functions-sources";
import { UserProfile } from "@ontology/sdk";
import { Client } from "@osdk/client";
import { createEditBatch, Edits } from "@osdk/functions";

type OntologyEdit = Edits.Object<UserProfile>;

export const config = {
    sources: ["myOAuthSourceAlias"],
    edits: [UserProfile],
};

export default async function linkUserProfile(client: Client): Promise<OntologyEdit[]> {
    const source = await getSource("myOAuthSourceAlias");
    const { url } = getHttpsConnection(source);
    const fetch = await getFetch(source);

    const response = await fetch(url + "/v1/me");
    if (!response.ok) {
        throw new Error(`Failed to fetch profile: ${response.status}`);
    }
    const profile = await response.json();

    const batch = createEditBatch<OntologyEdit>(client);
    batch.create(UserProfile, {
        userProfileId: profile.id,
        displayName: profile.display_name,
    });
    return batch.getEdits();
}
```

## 常见错误排查

对于 OAuth 授权错误，例如 `HTTP 401: Unauthorized`、`Credentials expired and no refresh handler provided` 或 `Resolved source credentials are not present on the Source`，请参阅 Data Connection 排查参考中的 [OAuth 和出站应用](https://www.palantir.com/docs/foundry/data-connection/troubleshooting/#oauth-and-outbound-applications)。

### HTTP 407：需要代理身份验证

函数的网络请求必须由数据源的[出站策略](https://www.palantir.com/docs/foundry/administration/configure-egress/)覆盖。如果目标主机名与允许的策略不匹配，请求可能返回 `HTTP 407: Proxy Authentication Required`。

如果出站策略看起来正确，请检查请求 URL 是如何构建的。`getHttpsConnection()` 返回的 URL 没有尾部斜杠，因此追加的路径如果省略了开头的 `/`，就会与主机名粘连：

```text
"https://example.com" + "api/v1"
→ "https://example.comapi/v1"
```

由此产生的主机名（`example.comapi`）不被任何出站策略覆盖，因此请求被拒绝。请在路径前加上 `/`（例如 `url + "/api/v1"`）。

---

*原文：[Make API calls from functions](https://www.palantir.com/docs/foundry/functions/api-calls/)*
