# 基于模型的函数（Functions on models）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/functions-on-models/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

你可以通过在运行时调用模型的函数，在本体的上下文中部署模型。模型函数（model function）是围绕实时模型部署自动生成的包装器，可以导入到函数代码仓库中并从你的代码里调用。这使你能够围绕模型预测添加自定义业务逻辑、将模型与本体对象集成，或编排多次模型调用。

## 概览

模型函数可以从两种类型的部署发布：

- **[直接模型部署](https://www.palantir.com/docs/foundry/manage-models/create-a-model-deployment/)：**直接从模型页面发布。
- **[建模目标（Modeling Objective）实时部署](https://www.palantir.com/docs/foundry/manage-models/set-up-live/)：**从建模目标中的部署详情页面发布。

两种方法都会创建一个与你的模型具有相同输入和输出 API 的函数。有关函数行为、版本升级和配置选项的详情，请参阅[模型函数开发者指南](https://www.palantir.com/docs/foundry/model-integration/model-functions-guide/)。

TypeScript v1、TypeScript v2 和 Python 函数均完全支持模型函数。

## 在代码仓库中导入实时部署

为实时部署创建函数后——无论是[在模型本身上](https://www.palantir.com/docs/foundry/manage-models/create-a-model-deployment/#3-publish-a-function-for-the-deployment)还是在[建模目标](https://www.palantir.com/docs/foundry/manage-models/set-up-live/#publish-function)中创建——都必须将其导入到特定代码仓库中才能使用。在代码仓库左侧边栏的 **Resource Imports（资源导入）**菜单中选择 **Add（添加）** 和 **Query Functions（查询函数）**。可以通过发布时选择的函数名搜索模型。请注意，Python 和 TypeScript v2 代码仓库仅支持与本体绑定的模型函数，如下文所述。

> **ℹ️ 提示：备注**
>
> 对于 TypeScript v1 代码仓库，也可以通过选择 **Models（模型）** 来导入模型函数，这在功能上等同于在 **Query Functions（查询函数）** 下导入。
>
> 在建模目标中使用 **API Name** 卡片（而非推荐的[模型发布对话框](https://www.palantir.com/docs/foundry/manage-models/set-up-live/#publish-function)）的旧版模型函数，仍然可以在 **Modeling Objectives（建模目标）** 部分下导入，但即将停用。

## 绑定本体或空间（space）的函数

从 2026 年 2 月起，所有新的模型函数都将与本体绑定。这是在 [TypeScript v2 和 Python 函数](https://www.palantir.com/docs/foundry/functions/language-models-python-tsv2/)中使用的前提条件，因为它们只允许导入本体资源。在此日期之前，模型函数与模型的[空间（space）](https://www.palantir.com/docs/foundry/security/orgs-and-spaces/)绑定。TypeScript v1 允许导入这两种类型的模型函数，但导入和使用的语义略有不同，[详见下文](#如果函数注册到本体)。

要检查函数是否绑定到本体，请导航到你的模型：未绑定到本体的模型函数会提示可进行迁移。[详细了解如何将函数迁移为绑定本体](#将模型函数迁移为绑定本体的函数)。

> **ℹ️ 提示**
>
> Marketplace 会将导入代码仓库的模型函数识别为安装输入，从而实现结构化的依赖跟踪。对于未经导入、直接通过 Platform SDK 查询的模型函数，Marketplace 不会跟踪。

## 从 Python 或 TypeScript v2 函数中调用模型函数

为实时部署创建函数后，必须将其导入特定代码仓库中才能使用。之后便可以[像调用任何查询函数一样](https://www.palantir.com/docs/foundry/functions/query-functions/#call-a-query-function)对其进行查询。

### 示例代码：Python

```python
from functions.api import function, Double
from foundry_sdk_runtime import AllowBetaFeatures
from ontology_sdk import FoundryClient
from ontology_sdk.ontology.objects import Flight


@function(beta=True)
def predict_flight_delays(flight: Flight) -> Double:
    # Prepare the input to match the model function's API.
    model_output = FoundryClient().ontology.queries.flight_model_deployment(
        df_in=[
            {
                "lastArrivalTime": flight.last_arrival_time,
                "lastExpectedArrivalTime": flight.last_expected_arrival_time,
            },
        ]
    )
    return model_output.df_out[0].prediction
```

### 示例代码：TypeScript v2

```typescript
import { Client } from "@osdk/client";
import { Double } from "@osdk/functions";
import { flightModelDeployment, Flight } from "@ontology/sdk";

async function predictFlightDelays(client: Client, flight: Flight): Promise<Double> {
    // Prepare the input to match the model function's API.
    const modelOutput = await client(flightModelDeployment).executeFunction({
        "df_in": [
            {
                "lastArrivalTime": flight.lastArrivalTime,
                "lastExpectedArrivalTime": flight.lastExpectedArrivalTime,
            },
        ]
    });
    return modelOutput.df_out[0].prediction;
}

export default predictFlightDelays;
```

## 编写模型支撑的 TypeScript v1 函数

### 如果模型函数注册到空间

我们首先需要导入该函数：

```typescript
// If the model function is tied to a space and not to an ontology,
// copy the import snippet from the Resource imports sidebar.
```

然后，编写一个函数：接收一个航班、为模型准备数据，并解释模型执行的结果。模型被导入为一个异步函数，它遵循[模型的输入和输出规范（即 API）](https://www.palantir.com/docs/foundry/integrate-models/model-adapter-api/)。由此，TypeScript 可以在编译时确保发送到模型部署和从模型部署接收的数据结构是正确的。

请注意，如果你的模型 API 恰好声明了一个表格输入和一个表格输出，并且[**Enable row-wise processing（启用逐行处理）**选项](https://www.palantir.com/docs/foundry/model-integration/model-functions-guide/#row-wise-publishing)处于启用状态（默认启用），则关联的函数将接受单个 TypeScript 对象，其属性对应于为输入指定的各列。声明了任何额外输入或输出的模型 API 不符合逐行处理的条件。下面的 `predictFlightDelaysRowWise` 函数演示了这种模式。或者，可以考虑[在模型 API 中直接使用 Object 或 ObjectSet](https://www.palantir.com/docs/foundry/integrate-models/model-adapter-reference/#for-object-inputs)，以便在函数中将模型与对象结合使用。

下面的 `predictFlightDelays` 函数返回一个 [`FunctionsMap`](https://www.palantir.com/docs/foundry/functions/types-reference/#map)，这是 TypeScript v1 中用于返回以对象或标量值为键的映射的类型。在本示例中，它将每个 `Flight` 对象映射到其预测的延误值。

```typescript
import { Function, Double, FunctionsMap } from "@foundry/functions-api";
import { Flight } from "@foundry/ontology-api";

@Function()
public async predictFlightDelaysRowWise(flight: Flight): Promise<Double> {
    // Prepare the input to match the model function's API.
    // This model function expects a single flight.
    // If you'd like to process multiple flights at a time,
    // publish a new model function version with row-wise processing disabled.

    // Note you can also use an Object directly in the model API
    // to avoid tedious mapping between a model API and an object type's properties.
    const modelInput = {
        "lastArrivalTime": flight.lastArrivalTime,
        "lastExpectedArrivalTime": flight.lastExpectedArrivalTime,
    };
    // Call the Live deployment.
    const modelOutput = await FlightModelDeploymentRowWise(modelInput);
    return modelOutput.prediction;
}

@Function()
public async predictFlightDelays(flights: Flight): Promise<FunctionsMap<Flight, Double>> {
    let functionsMap = new FunctionsMap();
    // Prepare the input to match the model function's API,
    // for the case where row-wise processing is not enabled.

    // Note you can also use an ObjectSet directly in the model API
    // to avoid tedious mapping between a model API and an object type's properties.
    const dfIn = flights.map(flight => ({
        "lastArrivalTime": flight.lastArrivalTime,
        "lastExpectedArrivalTime": flight.lastExpectedArrivalTime,
    }));
    // Call the Live deployment.
    const modelOutput = await FlightModelDeployment(
        {"df_in": dfIn}
    );
    for (let i = 0; i < flights.length; i++) {
        functionsMap.set(flights[i], modelOutput.df_out[i].prediction);
    }
    return functionsMap;
}
```

请注意，上述示例假定使用以下模型 API：

```python
import palantir_models as pm


class ExampleModelAdapter(pm.ModelAdapter):
    ...

    @classmethod
    def api(cls):
        inputs = {
            "df_in": pm.Pandas(columns=[("lastArrivalTime", datetime.datetime), ("lastExpectedArrivalTime", datetime.datetime)])
        }
        outputs = {
            "df_out": pm.Pandas(columns=[("prediction", float)])
        }
        return inputs, outputs
    ...
```

### 如果函数注册到本体

在这种情况下，导入和查询语法都需要更新，具体如下方代码片段中的注释所示：

```typescript
import { Function, Double } from "@foundry/functions-api";
// Add the Queries import to use an ontology-bound model function
import { Queries, Flight } from "@foundry/ontology-api";

export class MyFunctions {
    @Function()
    public async predictFlightDelays(flight: Flight): Promise<Double> {
        // Call your model by API Name from Queries
        const modelOutput = await Queries.flightModelDeployment({
            "df_in": [
                {
                    "lastArrivalTime": flight.lastArrivalTime,
                    "lastExpectedArrivalTime": flight.lastExpectedArrivalTime,
                },
            ]
        });
        return modelOutput.df_out[0].prediction;
    }
}
```

### 将模型函数迁移为绑定本体的函数

当你从用户界面将绑定空间的模型函数迁移为绑定本体的函数时，使用该模型的 TypeScript v1 函数的已发布版本将继续正常工作。但是，导入语法将不再被识别，这意味着消费该模型函数的代码仓库将无法再进行预览，也无法为新版本打标签。

要在迁移后更新你的 TypeScript v1 函数：

1. 打开你的代码仓库，选择 **Resource imports（资源导入）**侧边栏。
2. 通过将 resources.json 文件更新到新版本，选择新创建的模型函数版本。
3. 更新函数代码以使用上文详述的查询函数语法，或参阅[调用查询函数](https://www.palantir.com/docs/foundry/functions/query-functions/#call-a-query-function)的专门文档了解更多详情。

> **⚠️ 注意**
>
> 通过 [Foundry Platform SDK](https://www.palantir.com/docs/foundry/dev-toolchain/overview/#platform-sdks) 和 [Functions.Query ↗](https://github.com/palantir/foundry-platform-python/blob/develop/docs/v2/Functions/Query.md) 方法直接使用模型函数的方式会在迁移后立即失效。这是因为这种消费模式通过 API 名称引用函数，而 API 名称会在所有模型函数版本之间一次性全局迁移。要修复这些消费方，请在迁移后将 API 名称更新为新值。

## 性能注意事项

模型作为函数运行时的一部分执行，因此所有标准[限制](https://www.palantir.com/docs/foundry/functions/manage-functions/#enforced-limits)均适用。如果你的函数支撑某个动作，则对产生的编辑数量还有[进一步的限制](https://www.palantir.com/docs/foundry/action-types/scale-property-limits/#edit-limits)。

对实时部署的调用会通过网络发送模型输入和输出数据，上限为 50 MB。此网络传输计入函数的[已配置时间限制](https://www.palantir.com/docs/foundry/functions/manage-functions/#time-limit)。要增加函数的超时时间，请联系 Palantir 支持部门。

---

*原文：[Functions on models](https://www.palantir.com/docs/foundry/functions/functions-on-models/)*
