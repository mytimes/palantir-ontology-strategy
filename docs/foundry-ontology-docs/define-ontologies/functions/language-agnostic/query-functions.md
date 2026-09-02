# 查询（Queries）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/query-functions/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

查询是函数的只读子集，可以选择性地通过 [API 网关](https://www.palantir.com/docs/foundry/api/general/overview/introduction/)暴露。它们不能有任何副作用，例如修改本体或更改外部系统。如果你需要通过 API 网关获得这些额外的编辑能力，应使用[动作](https://www.palantir.com/docs/foundry/api/ontology-resources/actions/apply-action/)。

## 查询装饰器

使用以下语法定义查询函数。

**TypeScript v1**

```typescript
import { Query } from "@foundry/functions-api";

@Query({ apiName: "myTypeScriptV1Function" })
```

**TypeScript v2**

```typescript
// Export a config object with an apiName parameter from the file containing the function
export const config = {
    apiName: "myTypeScriptV2Function"
};
```

**Python**

```python
from functions.api import function

@function(api_name="myPythonFunction")
```

对于 Python 和 TypeScript v1 函数，装饰器接受一个 `string` 类型的 API 名称参数，定义 API 名称时必须提供该参数。使用 TypeScript v1 时，如果未定义 `apiName` 参数，查询的行为将类似于现有的 [`@Function` 装饰器](https://www.palantir.com/docs/foundry/functions/decorators/)。请注意，相应的 Python 语法是 `api_name`。

### 示例：带 API 名称的查询

下面的示例演示了如何通过 [API 网关](https://www.palantir.com/docs/foundry/api/general/overview/introduction/)暴露查询：

**TypeScript v1**

```typescript
import { Query, Double } from "@foundry/functions-api";
import { Objects, Aircraft } from "@foundry/ontology-api";

export class PublishedQueries {
    @Query({ apiName: "getReschedulableAircraftCount" })
    public async countAircraftTakingOffAfter(minimumTimeInMinutes: Double): Promise<Double> {
        const aircraftCount = await Objects.search().aircraft()
                 .filter(aircraft => aircraft.timeUntilNextFlight.range().gt(minimumTimeInMinutes))
                 .count();

        return aircraftCount!;
    }
}
```

**TypeScript v2**

```typescript
import { Client } from "@osdk/client";
import { Double } from "@osdk/functions";
import { Aircraft } from "@ontology/sdk";

export const config = {
    apiName: "getReschedulableAircraftCount"
};

async function countAircraftTakingOffAfter(client: Client, minimumTimeInMinutes: Double): Promise<Double> {
    const { $count } = await client(Aircraft).where({
        timeUntilNextFlight: {
            $gt: minimumTimeInMinutes
        }
    }).aggregate({ $select: { $count: "unordered" } })

    return $count;
}

export default countAircraftTakingOffAfter;
```

**Python**

```python
from functions.api import Double, function
from ontology_sdk import FoundryClient
from ontology_sdk.ontology.objects import Aircraft

@function(api_name="getReschedulableAircraftCount")
def count_aircraft_taking_off_after(minimum_time_in_minutes: Double) -> Double:
    client = FoundryClient()
    aircraft_count = client.ontology.objects.Aircraft.where(
        Aircraft.object_type.time_until_next_flight > minimum_time_in_minutes
    ).count().compute()

    return aircraft_count
```

## API 名称校验

查询的 `apiName` 必须是满足以下要求的字符串：

- 采用 `lowerCamelCase` 格式。
- 长度少于 100 个字符。
- 不以数字开头。
- 在导入到该代码仓库的所有本体中唯一。
  - 如果 `apiName` 不唯一，[标记流程](https://www.palantir.com/docs/foundry/functions/getting-started/#publish-your-functions)将失败，要求你更改名称。

此外，包含带 API 名称查询的代码仓库必须从至少一个本体导入实体。

## 带 API 名称查询的版本与更新

带 API 名称的查询将始终使用已发布查询的**最新标记版本**，不遵循其他 Foundry 函数所采用的语义化版本模式。

若要解除 API 名称与查询的关联、使其在 API 网关中失效，你必须从查询装饰器中移除 API 名称，并从代码仓库发布一个新标签。

> **ℹ️ 提示**
>
> 更改装饰器中的 API 名称并发布新标签会导致消费方调用失败。仅支持查询的最新已发布版本。
>
> 为了让消费方能够方便地升级而不出现破坏性变更，你可以支持同一 API 名称的多个版本。为此，你必须在代码仓库中复制一份查询代码，并为其指定不同的 API 名称，例如 `getReschedulableAircraftCountV2`。

## 搜索和查看查询

与其他函数一样，你可以在 [Ontology Manager](https://www.palantir.com/docs/foundry/ontology-manager/overview/) 中搜索和管理你的查询。你可以按查询名称或 API 名称搜索。在[上面](#示例带-api-名称的查询)的示例中，API 名称为 `getReschedulableAircraftCount`，查询名称为 `countAircraftTakingOffAfter`。

![在 Ontology Manager 中搜索查询](https://www.palantir.com/docs/resources/foundry/functions/query-in-oma.png)

> **ℹ️ 提示**
>
> 使用 TypeScript v1 函数时，你可能需要更新代码仓库中的 `functions.json` 文件，将 `enableQueries` 属性设置为 true 以启用查询：
>
> **TypeScript v1**
>
> ```typescript
> {
>   "enableQueries": true
> }
> ```

## 调用查询函数

发布 TypeScript 或 Python 查询函数后，导航到你要在其中使用该函数的代码仓库，并使用[**资源导入**侧边栏](https://www.palantir.com/docs/foundry/functions/resource-imports-sidebar/)导入它。

你的函数将可以从消费方代码仓库中调用。例如：

**TypeScript v1**

```typescript
import { Queries } from "@foundry/ontology-api";

export class MyFunctions {
    @Function()
    public callQueryFunction(): Promise<Double> {
        return Queries.getReschedulableAircraftCount(10);
    }
}
```

**TypeScript v2**

```typescript
import { Client } from "@osdk/client";
import { Double } from "@osdk/functions";
import { getReschedulableAircraftCount } from "@ontology/sdk";

async function callQueryFunction(client: Client): Promise<Double> {
    return client(getReschedulableAircraftCount).executeFunction({ timeUntilNextFlight: 10 });
}

export default callQueryFunction;
```

**Python**

```python
from functions.api import Double, function
from ontology_sdk import FoundryClient

@function
def call_query_function() -> Double:
    return FoundryClient().ontology.queries.get_reschedulable_aircraft_count(
        time_until_next_flight=Double(10)
    )
```

对于 TypeScript v1 函数，Foundry 必须知道你从已发布函数中调用了哪些查询函数和语言模型方法。我们会自动提供静态分析，尝试检测被调用的查询。然而，这种静态分析偶尔可能会漏掉某些调用，导致运行时错误，提示你添加 `@Uses` 装饰器。该装饰器用于补充自动检测到的查询使用情况。请在同一个 `queries` 数组中列出语言模型方法。

以下示例演示了 `@Uses` 装饰器的用法：

**TypeScript v1**

```typescript
import { Uses } from "@foundry/functions-api";
import { Queries } from "@foundry/ontology-api";

export class MyFunctions {
    @Uses({ queries: [Queries.getReschedulableAircraftCount] })
    @Function()
    public callQueryFunction(): Promise<Double> {
        return Queries.getReschedulableAircraftCount(10);
    }
}
```

---

*原文：[Queries](https://www.palantir.com/docs/foundry/functions/query-functions/)*
