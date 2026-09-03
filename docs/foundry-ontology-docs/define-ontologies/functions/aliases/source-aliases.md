# 源别名（Source aliases）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/source-aliases/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

源别名是指向 [Data Connection 数据源](https://www.palantir.com/docs/foundry/data-connection/set-up-source/)的具名、可移植引用。通过按别名键而非特定数据源标识符来引用数据源，你可以将函数逻辑与任何单一数据源解耦。这使你的函数能够保持跨环境的可移植性。

## 导入数据源

要导入数据源：

1. 打开 TypeScript v2 或 Python 代码仓库并转到**“资源导入”**面板。
2. 选择 **Ontology SDK** 标签页。
3. 选择**添加 > 源**。 ![“资源导入”面板中的“添加”下拉菜单，显示“源”选项。](https://www.palantir.com/docs/resources/foundry/functions/source-aliases-import.png)
4. 搜索并选择要导入的数据源，然后选择**确认选择**。

## 添加源别名

导入数据源后，你可以为其添加别名：

1. 在**“资源导入”**面板中，在**“源”**下找到该数据源。
2. 选择数据源名称下方的**添加别名**。 ![“资源导入”面板，显示已导入的数据源及添加别名的选项。](https://www.palantir.com/docs/resources/foundry/functions/source-aliases-add.png)

> **ℹ️ 提示**
>
> 别名的键在代码仓库内必须唯一。

## 编辑源别名

要编辑已有的源别名，请转到**“资源导入”**面板中的**“源”**区块。选择别名旁边的钢笔图标即可内联编辑。

![“资源导入”面板，显示源别名及其编辑选项。](https://www.palantir.com/docs/resources/foundry/functions/source-aliases-edit.png)

## 在代码中使用源别名

要在函数中使用源别名，请在声明和获取数据源的所有位置通过其键引用别名。将别名键传递给 `sources` 配置以及 `getSource`（TypeScript v2）或 `get_source`（Python）：

**TypeScript v2**

```typescript
import { getSource, getHttpsConnection, getFetch } from "@palantir/functions-sources";

export const config = {
    sources: ["demoSource"]
}

async function myExternalFunction(): Promise<string> {
    const source = await getSource("demoSource");
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


@function(sources=["demoSource"])
def my_external_function() -> str:
    source = get_source("demoSource")
    url = source.get_https_connection().url
    client = source.get_https_connection().get_client()
    response = client.get(url)
    return response.text
```

如果你需要别名所解析到的数据源的资源标识符（RID），请在 TypeScript v2 中使用 `Aliases.source` 工具，或在 Python 中使用 `source` 工具。从 `.rid` 属性读取解析后的 RID：

**TypeScript v2**

```typescript
import { Aliases } from "@osdk/functions";

const sourceRid = Aliases.source("demoSource").rid;
```

**Python**

```python
from functions.aliases import source

source_rid = source("demoSource").rid
```

有关在函数中使用数据源的更多信息，包括访问凭据和使用预配置客户端，请参阅[从函数发起 API 调用](https://www.palantir.com/docs/foundry/functions/api-calls/)。

## 在 Marketplace 中使用源别名

当你把使用源别名的函数添加到 [Marketplace 产品](https://www.palantir.com/docs/foundry/functions/marketplace-functions/)时，每个带别名的数据源都会作为可配置输入出现在**“Data Connection 数据源”**下。安装者可以在安装期间将每个输入数据源映射到其环境中的可用数据源，而无需修改函数源代码。

![Marketplace 产品，将 Data Connection 数据源显示为可配置输入。](https://www.palantir.com/docs/resources/foundry/functions/source-aliases-marketplace-product.png)

### 设置描述

为帮助安装者理解应当提供哪个数据源，你可以为数据源输入添加描述。在**“输入”**下选择该数据源以打开**“详情”**面板，然后在**“常规”**标签页中输入描述。

![数据源输入的“详情”面板，显示描述字段。](https://www.palantir.com/docs/resources/foundry/functions/source-aliases-description.png)

### 安装体验

安装期间，安装者会看到数据源描述，并可以选择适合其环境的数据源。Marketplace 会自动将所选数据源重新映射到所定义的别名，因此函数代码保持不变。

![安装视图，显示源别名及其描述，以及更改数据源或将其范围限定到某个命名空间的选项。](https://www.palantir.com/docs/resources/foundry/functions/source-aliases-install.png)

---

*原文：[Source aliases](https://www.palantir.com/docs/foundry/functions/source-aliases/)*
