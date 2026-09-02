# 本体编辑（Ontology edits）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/edits-overview/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

**本体编辑（Ontology edit）**是指创建、修改或删除对象的行为。函数支持返回[本体编辑](https://www.palantir.com/docs/foundry/functions/types-reference/#ontology-edit)，以供[函数支撑的动作](https://www.palantir.com/docs/foundry/action-types/function-actions-overview/)使用。

- TypeScript v1 函数使用 `@OntologyEditFunction` 装饰器编写，该装饰器提供特殊语义以简化你的代码。TypeScript v1 函数还使用 [`@Edits` 装饰器](https://www.palantir.com/docs/foundry/functions/api-ontology-edits/#the-edits-decorator)为动作提供溯源（provenance）信息，动作可利用这些信息来[强制执行权限控制](https://www.palantir.com/docs/foundry/action-types/permissions/)。你可以使用[验证本体编辑](https://www.palantir.com/docs/foundry/functions/unit-test-ontology-edits/)相关的 API，为 TypeScript v1 本体编辑函数编写单元测试。
- TypeScript v2 函数使用从 `@osdk/functions` 包导出的 [`createEditBatch`](https://www.palantir.com/docs/foundry/functions/typescript-v2-ontology-edits/#construct-an-ontology-edits-batch) 函数编写。这些函数依赖 `Edits` 类型为动作提供溯源信息。
- Python 函数通过使用从 Ontology SDK 导出的 [`FoundryClient`](https://www.palantir.com/docs/foundry/functions/python-ontology-edits/#construct-an-ontology-edits-container) 创建编辑容器来编写。这些函数依赖 [`@function`](https://www.palantir.com/docs/foundry/functions/python-ontology-edits/#define-an-edit-function) 装饰器的 `edits` 参数为动作提供溯源信息。

本文档的其余部分将介绍本体编辑函数在幕后的工作方式，以帮助你更好地理解底层基础设施。

### 编辑何时应用

关于本体编辑函数，一个常见的误解是：运行它们是否会更新本体中的对象。当你在 **Authoring（创作）** 界面的函数助手（functions helper）中运行本体编辑函数时，编辑不会应用到实际对象上。使用函数更新对象的唯一方式，是按照[函数支撑的动作](https://www.palantir.com/docs/foundry/action-types/function-actions-overview/)文档中的说明，配置一个使用该函数的动作。

这意味着你可以在函数助手中自由运行本体编辑函数，以验证各种输入下的结果，而不必担心对象本身会被更新。

![结果窗格](https://www.palantir.com/docs/resources/foundry/functions/results-pane-edits.png)

### 注意事项

#### 编辑与对象搜索

对对象和链接的更改会在你的函数执行完成*之后*才传播到对象集 API。这意味着，任何结果依赖于已编辑值的 `Objects.search()` 查询——例如基于你编辑的属性或链接进行筛选、环绕搜索（search around）或聚合——都将使用旧的对象、属性和链接，可能无法反映对本体的编辑（包括创建和删除）。你的函数需要手动处理这种情况。

这与检索你已经编辑过的特定对象（例如按主键检索）不同。在那种情况下，函数基础设施会在对象物化时应用你挂起的编辑，因此会返回编辑后的属性和链接值。示例请参见 [@Edits 装饰器](https://www.palantir.com/docs/foundry/functions/api-ontology-edits/#the-edits-decorator)。

对于下面的示例，假设有一个 ID 为 1 的 Employee。

**TypeScript v1**

```typescript
import { OntologyEditFunction, Edits } from "@foundry/functions-api";
import { Employee, Objects } from "@foundry/ontology-api";

export class CaveatEditFunctions {
    @Edits(Employee)
    @OntologyEditFunction()
    public async editAndSearch(): Promise<void> {
        const employeeOne = Objects.search().employee().filter(e => e.id.exactMatch(1)).all()[0];
        employeeOne.name = "Bob";

        const count = await Objects.search().employee().filter(e => e.name.exactMatch("Bob")).count() ?? -1;
        console.log(count);
        // Expected: 1, Actual: 0
    }
}
```

**TypeScript v2**

```typescript
import { Client } from "@osdk/client";
import { Employee } from "@ontology/sdk";
import { Edits, createEditBatch } from "@osdk/functions";

type OntologyEdit = Edits.Object<Employee>;

async function editAndSearch(client: Client): Promise<OntologyEdit[]> {
    const batch = createEditBatch<OntologyEdit>(client);

    const employeeOne = await client(Employee).fetchOne(1);
    batch.update(employeeOne, { name: "Bob" });

    const count = await client(Employee)
        .where({
            name: {
                $eq: "Bob"
            }
        })
        .aggregate({
            $select: {
                $count: "unordered"
            }
        })
        .then(response => response.$count);
    console.log(count);
    // Expected: 1, Actual: 0

    return batch.getEdits();
}

export default editAndSearch;
```

**Python**

```python
from functions.api import function, OntologyEdit
from ontology_sdk import FoundryClient
from ontology_sdk.ontology.objects import Employee

@function(edits=[Employee])
def edit_and_search() -> list[OntologyEdit]:
    client = FoundryClient()
    ontology_edits = client.ontology.edits()

    employee = client.ontology.objects.Employee.get(1)
    editable_employee = ontology_edits.objects.Employee.edit(employee)
    editable_employee.name = "Bob"

    count = client.ontology.objects.Employee.where(Employee.object_type.name == "Bob").count().compute()
    print(count)
    # Expected: 1, Actual: 0

    return ontology_edits.get_edits()
```

#### 在 Automate 中应用本体编辑

[Automate](https://www.palantir.com/docs/foundry/automate/overview/) 不会应用由用作效果（effect）的函数所返回的编辑。要通过自动化应用本体编辑，请改为配置函数支撑的动作。更多信息请参见[函数效果](https://www.palantir.com/docs/foundry/automate/effect-function/)。

#### 函数支撑的动作中的可选数组

在代码仓库中运行 `@OntologyEditFunction` 时，被省略的可选数组会被当作 `undefined` 处理；而在通过动作执行该函数时，它们会被作为空数组传入。

---

*原文：[Ontology edits](https://www.palantir.com/docs/foundry/functions/edits-overview/)*
