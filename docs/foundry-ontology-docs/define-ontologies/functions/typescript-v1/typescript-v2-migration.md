# 从 TypeScript v1 迁移到 TypeScript v2（Migrate from TypeScript v1 to TypeScript v2）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/typescript-v2-migration/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

本指南描述了在将现有 TypeScript 函数从 v1 迁移到 v2 时可能遇到的语法和结构差异。请参阅[功能支持文档](https://www.palantir.com/docs/foundry/functions/language-feature-support/#typescript-v1-vs-typescript-v2)，了解 v2 的增强功能以及每个版本所支持的内容。

## 声明函数

在 TypeScript v1 中，要将函数发布到平台，你必须使用来自 `@foundry/functions-api` 包的 `@Function()` 装饰器来注解它。此外，该函数必须是一个类的方法，且该类要从仓库的根 `index.ts` 文件导出。

```typescript
// src/index.ts

import { Function } from "@foundry/functions-api";

export class MyFunctions {

    @Function()
    public reverseStringArray(arr: string[]): string[] {
        return arr.reverse();
    }
}
```

在 TypeScript v2 中，要将函数发布到平台，你必须将其写在 `src/functions` 目录下的一个文件中，并使用 `export default` 导出。每个文件只能导出一个函数。

```typescript
// src/functions/reverseStringArray.ts

export default function reverseStringArray(
    arr: string[]
): string[] {
    return arr.reverse();
}
```

为了保持仓库井然有序，我们建议将相关的函数分组到 `src/functions` 目录下的子目录中。例如，以下文件夹结构将函数组织到 `payroll` 和 `staffing` 子目录中，使职责划分更清晰。

![TypeScript v2 函数的文件夹结构示例。](https://www.palantir.com/docs/resources/foundry/functions/typescript-v2-folder-structure.png)

有关更多信息，请参阅我们的 [TypeScript v2 函数入门](https://www.palantir.com/docs/foundry/functions/typescript-v2-getting-started/)文档。

## 使用 `@osdk/functions` 包

在 TypeScript v1 中，你必须从 `@foundry/functions-api` 包导入 `Integer` 和 `Double` 等原始类型，以便在签名中使用它们。但在 TypeScript v2 中，你必须改用 `@osdk/functions` 包。

以下示例从 `@foundry/functions-api` 包导入 `Integer` 类型，并在一个 TypeScript v1 函数的签名中使用它来计算两个整数的最大公约数：

```typescript
import { Function, Integer } from "@foundry/functions-api";

export class MyFunctions {

    @Function()
    public gcd(a: Integer, b: Integer): Integer {
        if (b === 0) {
            return a;
        }
        return gcd(b, a % b);
    }
}
```

在 TypeScript v2 中，核心 TypeScript 逻辑完全相同，但你必须使用来自 `@osdk/functions` 包的 `Integer` 类型：

```typescript
import { Integer } from "@osdk/functions";

export default function gcd(a: Integer, b: Integer): Integer {
    if (b === 0) {
        return a;
    }
    return gcd(b, a % b);
}
```

请参阅[类型参考](https://www.palantir.com/docs/foundry/functions/types-reference/)，了解如何在 TypeScript v1 和 TypeScript v2 函数中导入类型并在签名中使用它们的示例。

## 日期和时间戳

TypeScript v1 使用来自 `@foundry/functions-api` 包的 `LocalDate` 和 `Timestamp` 类型来处理时间数据。TypeScript v2 用来自 `@osdk/functions` 包的 `DateISOString` 和 `TimestampISOString` 类型取代了它们，这两个类型以 [ISO 8601 ↗](https://www.iso.org/iso-8601-date-and-time-format.html) 字符串表示日期和时间戳。

TypeScript v2 函数可以使用 NPM 生态系统中任何可用的日期和时间戳库，例如 [dayjs ↗](https://www.npmjs.com/package/dayjs)、[date-fns ↗](https://www.npmjs.com/package/date-fns) 和 [luxon ↗](https://www.npmjs.com/package/luxon)。

## 生成 Ontology SDK

TypeScript v2 函数通过 [Ontology SDK](https://www.palantir.com/docs/foundry/ontology-sdk/typescript-osdk/) 为查询和编辑本体提供了一等支持。与 TypeScript v1 一样，TypeScript v2 仓库允许你通过[**资源导入**侧边栏](https://www.palantir.com/docs/foundry/functions/resource-imports-sidebar/)导入本体实体。添加对象类型和链接类型后，系统会提示你创建 Ontology SDK 的初始版本。

![在 TypeScript 代码仓库中创建你的第一个 Ontology SDK 的提示。](https://www.palantir.com/docs/resources/foundry/functions/osdk-create-initial-version.png)

选择 **Create（创建）**，然后为 Ontology SDK 选择一个名称。第一个版本生成后，该名称无法更改。选择 **Create new version（创建新版本）** 以生成 Ontology SDK。

![生成第一个 Ontology SDK 前选择名称。](https://www.palantir.com/docs/resources/foundry/functions/osdk-name.png)

Ontology SDK 创建完成后，你会看到一个将其安装到工作区的选项。选择 **Install（安装）** 会将 Ontology SDK 作为依赖添加到 `package.json` 文件中，并使其可在 TypeScript 代码中使用。

![从 TypeScript 代码仓库安装你的 Ontology SDK 的提示。](https://www.palantir.com/docs/resources/foundry/functions/osdk-install.png)

查看侧边栏中的 **Documentation（文档）** 选项卡，获取在 TypeScript 中使用本体的全面示例。

## 查询本体

在 TypeScript v1 中，你必须从 `@foundry/ontology-api` 包导入 `Objects` 才能对本体执行搜索：

```typescript
import { Function, Integer } from "@foundry/functions-api";
import { Objects } from "@foundry/ontology-api";

export class MyFunctions {

    @Function()
    public async countAircraft(): Promise<Integer> {
        const count = await Objects.search().aircraft().count() ?? 0;

        return count;
    }
}
```

在 TypeScript v2 中，你必须通过在函数签名中将 Ontology SDK 客户端指定为第一个参数来访问它：

```typescript
import { Aircraft } from "@ontology/sdk";
import { Client } from "@osdk/client";
import { Integer } from "@osdk/functions";

export default async function countAircraft(client: Client): Promise<Integer> {
    const aircraft = await client(Aircraft).aggregate({
        $select: {
            $count: "unordered"
        }
    });

    return aircraft.$count;
}
```

## 编辑本体

要在 TypeScript v1 中编写本体编辑函数，你必须使用来自 `@foundry/functions-api` 包的 `@OntologyEditFunction()` 装饰器来注解它，并给它一个 `void` 返回类型。你还必须应用 [`@Edits` 装饰器](https://www.palantir.com/docs/foundry/functions/api-ontology-edits/#the-edits-decorator)预先声明所有被编辑的对象类型，从而在调用函数支撑的动作之前对这些对象类型强制执行权限检查。

```typescript
import { Edits, OntologyEditFunction } from "@foundry/functions-api";
import { Aircraft, Employee } from "@foundry/ontology-api";

export class MyOntologyEditFunctions {

    @Edits(Aircraft, Employee)
    @OntologyEditFunction()
    public myFunction(aircraft: Aircraft, employee: Employee): void {
        aircraft.businessCapacity = 3;
        employee.department = "HR";
    }
}
```

在 TypeScript v2 中，你必须从 `@osdk/functions` 包导入 `createEditBatch` 函数，以构建一个在整个执行期间使用的编辑存储。你必须使用 `Edits` 类型来声明允许你的函数编辑哪些实体。这会在编译时强制执行类型安全；如果你尝试编辑的对象或链接的类型不在你的 `Edits` 类型覆盖范围内，TypeScript 编译器将返回错误。

```typescript
import { createEditBatch, Edits } from "@osdk/functions";
import { Aircraft, Employee } from "@ontology/sdk";
import { Client, Osdk } from "@osdk/client";

type OntologyEdit = Edits.Object<Aircraft> | Edits.Object<Employee>;

export default function myFunction(
    client: Client, 
    aircraft: Osdk.Instance<Aircraft>, 
    employee: Osdk.Instance<Employee>
): OntologyEdit[] {

    const batch = createEditBatch<OntologyEdit>(client);

    batch.update(aircraft, { businessCapacity: 3 });
    batch.update(employee, { department: "HR" });

    return batch.getEdits();
}
```

> **ℹ️ 提示**
>
> 在 TypeScript v2 中，使用 `Edits.Interface<MyInterface>` 可以通过[本体接口](https://www.palantir.com/docs/foundry/interfaces/interface-overview/)属性创建、更新和删除对象。有关详情，请参阅[本体编辑](https://www.palantir.com/docs/foundry/functions/typescript-v2-ontology-edits/)。

在 TypeScript v1 中，编辑在函数执行期间*不会*应用到本体。正如我们的[编辑与对象搜索文档](https://www.palantir.com/docs/foundry/functions/edits-overview/#edits-and-object-search)中所述，对对象和链接的更改仅在函数执行完成后才会传播，并且仅当函数在函数支撑的动作中被调用时才会传播。

TypeScript v2 使这种行为更加明确。你的函数必须使用编辑批次来跟踪编辑，并在完成时返回它们，而不是隐式地累积编辑。

有关支持的操作的完整列表，请参阅 TypeScript v2 文档中的[本体编辑](https://www.palantir.com/docs/foundry/functions/typescript-v2-ontology-edits/)部分。

> **ℹ️ 提示**
>
> TypeScript v2 还支持[暂存写入](https://www.palantir.com/docs/foundry/functions/typescript-v2-staged-writes/)，这是一种具有写后读保证的替代执行模型。暂存写入函数使用 `WriteableClient` 而不是 `createEditBatch`，并且不需要显式返回编辑。

## 为对象生成唯一 ID

在 TypeScript v1 中，要为新创建的对象生成唯一 ID，请使用来自 `@foundry/functions-utils` 包的 `Uuid` 工具。

```typescript
import { Edits, OntologyEditFunction } from "@foundry/functions-api";
import { Uuid } from "@foundry/functions-utils";
import { FlightScenario, Objects } from "@foundry/ontology-api";

export class ExampleEditFunctions {

    @Edits(FlightScenario)
    @OntologyEditFunction()
    public createFlightScenario(): void {
        const scenario = Objects.create().flightScenarios(Uuid.random());
        scenario.scenarioName = "New scenario";
    }
}
```

TypeScript v2 在完整的 Node.js 环境中运行，因此你可以改用 `node:crypto` 核心模块：

```typescript
import { FlightScenario } from "@ontology/sdk";
import { Client } from "@osdk/client";
import { createEditBatch, Edits } from "@osdk/functions";
import { randomUUID } from "node:crypto";

type OntologyEdit = Edits.Object<FlightScenario>;

export default function createFlightScenario(client: Client): OntologyEdit[] {

    const batch = createEditBatch<OntologyEdit>(client);

    batch.create(FlightScenario, { 
        id: randomUUID(),
        scenarioName: "New scenario",
    });

    return batch.getEdits();
}
```

> **⚠️ 注意**
>
> 避免在函数体之外的模块顶层调用 `randomUUID` 或其他随机值生成器。TypeScript v2 函数使用热调用（warm invocation）：所有模块级代码在初始化期间求值一次，然后在后续调用中复用。这意味着模块级的 `randomUUID` 调用只会被求值一次，并在每次热调用中产生相同的值。请始终在函数体内生成随机值以确保唯一性。

## 将对象加载到内存中

TypeScript v1 函数提供了 `.all()` 和 `.allAsync()` API，用于将特定类型的所有对象加载到内存中进行处理。然而，随着本体中对象数量的增长，这种方法可能会导致高内存占用和较慢的性能。

```typescript
import { Edits, OntologyEditFunction } from "@foundry/functions-api";
import { Aircraft, Objects } from "@foundry/ontology-api";

export class MyFunctions {

    @Edits(Aircraft)
    @OntologyEditFunction()
    public editAircraft(): void {

        const aircraft = Objects.search().aircraft().all();

        aircraft.forEach(a => {
            a.arrived = true;
        });
    }
}
```

TypeScript v2 函数通过 Ontology SDK 支持流式对象处理，无需一次性将整个对象集保存在内存中。我们建议在可能的情况下采用这种方法。

```typescript
import { Aircraft } from "@ontology/sdk";
import { Client } from "@osdk/client";
import { createEditBatch, Edits } from "@osdk/functions";

type OntologyEdit = Edits.Object<Aircraft>;

export default async function editAircraft(client: Client): Promise<OntologyEdit[]> {

    const batch = createEditBatch<OntologyEdit>(client);
    
    for await (const a of client(Aircraft).asyncIter()) {
        batch.update(a, { arrived: true });
    }

    return batch.getEdits();
}
```

如果数据规模不是问题，以下替代方案会加载特定类型的所有对象：

```typescript
import { Aircraft } from "@ontology/sdk";
import { Client } from "@osdk/client";
import { createEditBatch, Edits } from "@osdk/functions";

type OntologyEdit = Edits.Object<Aircraft>;

export default async function editAircraft(client: Client): Promise<OntologyEdit[]> {

    const batch = createEditBatch<OntologyEdit>(client);
   
    const aircraft = await Array.fromAsync(client(Aircraft).asyncIter());

    aircraft.forEach(a => {
        batch.update(a, { arrived: true });
    });

    return batch.getEdits();
}
```

---

*原文：[Migrate from TypeScript v1 to TypeScript v2](https://www.palantir.com/docs/foundry/functions/typescript-v2-migration/)*
