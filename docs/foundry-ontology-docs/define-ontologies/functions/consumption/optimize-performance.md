# 优化性能（Optimize performance）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/optimize-performance/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

本页介绍优化函数性能与资源使用的最佳实践。遵循这些指南有助于最大限度降低计算消耗，并确保你的函数高效运行。

> **ℹ️ 提示**
>
> 有关计算成本以及函数如何计量计费的信息，请参阅[本体查询的计算用量](https://www.palantir.com/docs/foundry/ontologies/query-compute-usage/)。

## 了解函数的计算成本

函数的成本由多个部分组成：

- **开销（Overhead）：** 无论函数执行什么操作，每次函数执行都有 4 计算秒（compute-seconds）的固定开销。
- **计算时间（Compute time）：** 函数执行所需的 vCPU 时间。
- **外部调用（External calls）：** 对平台其他部分的调用（本体查询、模型推理、LLM 调用）会产生各自的成本。

有关平台如何计算和度量计算秒的更多信息，请参阅[用量类型](https://www.palantir.com/docs/foundry/resource-management/usage-types/)。

## 使用 Performance（性能）选项卡

Performance（性能）选项卡提供了一个工具，用于分析和识别函数的性能问题。

![函数性能选项卡](https://www.palantir.com/docs/resources/foundry/functions/functions-performance-tab.png)

瀑布图将各项操作表示为沿 X 轴时间方向展开的水平条。每个操作都有标记，用于指示时间花费在何处。

- **执行函数（Execute function）** 表示执行函数代码所花费的 CPU 时间。
- **从参数加载对象（Load objects from arguments）** 和**从链接加载对象（Load objects from links）** 表示调用底层本体后端数据库服务（OSS）所花费的时间。

要提升函数性能：

- 使用 Objects API 进行聚合和链接遍历，比在函数上下文中更快（如[尽可能优先使用 Objects API](#尽可能优先使用-objects-api) 中所述）。
- 确保对本体后端服务的调用并行执行，以避免串行加载。如果你有多个 `async` / `await` 调用，请使用 `Promise.all` 并行等待所有调用。
  - 例如，一个常见模式是在列表上使用 `.map()` 创建 Promise，然后对结果列表使用 `Promise.all`。
  - **重要：** 使用 `Promise.all()` 可以提升执行速度，但不会减少资源消耗或成本。你执行的操作数量不变——它们只是并行运行而已。批量操作既更快，又更具成本效益。
- 避免不必要的嵌套循环，它们会增加执行时间。

## 选择高效的输入类型

设计函数时，你选择的输入参数类型会显著影响性能。请使用满足需求的最高效输入类型。

**最佳实践：** 尽可能使用对象集，以获得最高的效率和可扩展性。
**可接受：** 当你需要在内存中处理对象时，使用对象数组。
**反模式：** 仅当你的对象类型只包含一个实例，或者特定业务逻辑确实需要逐对象处理时，才应使用单个对象参数。

| 输入类型 | 效率 | 用例 |
| --- | --- | --- |
| 对象集 | 最佳 | 可查询的对象集合；如果你只需要聚合结果，则没有前置加载成本 |
| 对象数组 | 良好 | 当你需要迭代特定对象时 |
| 单个对象 | 效率最低 | 当业务逻辑要求一次处理一个对象时 |

### 最佳实践：对象集

作为参数传入的对象会触发本体查询以加载对象数据。即使是单个对象输入，也会触发一次将该对象加载到内存的调用。

**对象集**更可取，因为它会将加载推迟到你真正需要数据时。如果你只需要聚合结果（如计数或求和），本体后端会直接计算出结果，而无需加载单个对象。

**Python**

```python
from functions.api import function, Float
from ontology_sdk.ontology.objects import ExampleDataAircraft
from ontology_sdk.ontology.object_sets import ExampleDataAircraftObjectSet

# Less efficient: Single object triggers upfront loading

@function()
def get_aircraft_name(aircraft: ExampleDataAircraft) -> str:
    return aircraft.display_name

# Moderate: Array of objects triggers upfront loading

@function()
def get_aircrafts_names(aircraft_array: list[ExampleDataAircraft]) -> list[str]:
    return [aircraft.display_name for aircraft in aircraft_array]

# Most efficient: Object set defers loading until needed

# Here, only the aggregated value is loaded in memory of the function
@function()
def count_aircrafts(aircraft_set: ExampleDataAircraftObjectSet) -> Float:
    return aircraft_set.count().compute()
```

**TypeScript v2**

```typescript
// getAircraftName.ts - Less efficient: Single object triggers upfront loading
import { Osdk } from "@osdk/client";
import { ExampleDataAircraft } from "@ontology/sdk";

export default function getAircraftName(aircraft: Osdk.Instance<ExampleDataAircraft>): string {
    return aircraft.displayName!;
}

// getAircraftsNames.ts - Moderate: Array of objects triggers upfront loading
import { Osdk } from "@osdk/client";
import { ExampleDataAircraft } from "@ontology/sdk";

export default function getAircraftsNames(aircraftArray: Osdk.Instance<ExampleDataAircraft>[]): string[] {
    return aircraftArray.map(e => e.displayName!);
}

// countAircrafts.ts - Most efficient: Object set defers loading until needed
// Here, only the aggregated value is loaded in memory of the function
import { ObjectSet } from "@osdk/client";
import { Integer } from "@osdk/functions";
import { ExampleDataAircraft } from "@ontology/sdk";

export default async function countAircrafts(aircraftSet: ObjectSet<ExampleDataAircraft>): Promise<Integer> {
    const result = await aircraftSet.aggregate({
        $select: { $count: "unordered" }
    });
    return result.$count;
}
```

**TypeScript v1**

```typescript
import { Function, Integer } from "@foundry/functions-api";
import { ObjectSet, ExampleDataAircraft } from "@foundry/ontology-api";

export class MyFunctions {
    // Less efficient: Single object triggers upfront loading
    @Function()
    public getAircraftName(aircraft: ExampleDataAircraft): string {
        return aircraft.displayName!;
    }

    // Moderate: Array of objects triggers upfront loading
    @Function()
    public getAircraftsNames(aircraftArray: ExampleDataAircraft[]): string[] {
        return aircraftArray.map(e => e.displayName!);
    }

    // Most efficient: Object set defers loading until needed
    // Here, only the aggregated value is loaded in memory of the function
    @Function()
    public async countAircrafts(aircraftSet: ObjectSet<ExampleDataAircraft>): Promise<Integer> {
        const count = await aircraftSet.count();
        return count!;
    }
}
```

## 高效加载对象

函数中性能问题的常见来源是低效的对象加载。在循环中一次加载一个对象，会导致每次迭代都与本体发生一次往返。

### 反模式：逐个加载对象

在循环内加载对象是一种会显著影响性能的反模式。每次迭代都会向本体发起一个单独的查询：

**Python**

```python
from functions.api import function
from ontology_sdk import FoundryClient
from ontology_sdk.ontology.objects import ExampleDataAircraft

# Anti-pattern: Objects loaded one by one in a loop

@function()
def for_loop_worst(pks_list: list[str]) -> int:
    client = FoundryClient()
    seats_count = 0

    for current_pk in pks_list:
        aircrafts = client.ontology.objects.ExampleDataAircraft.where(
            ExampleDataAircraft.object_type.tail_number == current_pk
        ).page()
        aircraft = aircrafts.data[0]
        seats_count += aircraft.number_of_seats or 0

    return seats_count

# Better: Load all objects in one call, then iterate

@function()
def for_loop_better(pks_list: list[str]) -> int:
    client = FoundryClient()
    seats_count = 0

    aircrafts = client.ontology.objects.ExampleDataAircraft.where(
        ExampleDataAircraft.object_type.tail_number.in_(pks_list)
    )

    for aircraft in aircrafts:
        seats_count += aircraft.number_of_seats or 0

    return seats_count

# Best: Let the backend perform the aggregation

@function()
def for_loop_best(pks_list: list[str]) -> int:
    client = FoundryClient()

    result = client.ontology.objects.ExampleDataAircraft.where(
        ExampleDataAircraft.object_type.tail_number.in_(pks_list)
    ).sum(ExampleDataAircraft.object_type.number_of_seats).compute()

    return int(result or 0)
```

**TypeScript v2**

```typescript
// forLoopWorst.ts - Anti-pattern: Objects loaded one by one in a loop
import { Client, Osdk } from "@osdk/client";
import { ExampleDataAircraft } from "@ontology/sdk";
import { Integer } from "@osdk/functions";

export default async function forLoopWorst(client: Client, pks_list: string[]): Promise<Integer> {
    let seatsCount = 0;

    for (const currentPk of pks_list) {
        const fetchedPage = await client(ExampleDataAircraft).where({
            tailNumber: { $eq: currentPk }
        }).fetchPage();
        const aircraft = fetchedPage.data[0];
        seatsCount += aircraft?.numberOfSeats ?? 0;
    }

    return seatsCount;
}

// forLoopBetter.ts - Better: Load all objects in one call, then iterate
import { Client, Osdk } from "@osdk/client";
import { ExampleDataAircraft } from "@ontology/sdk";
import { Integer } from "@osdk/functions";

export default async function forLoopBetter(client: Client, pks_list: string[]): Promise<Integer> {
    let seatsCount = 0;

    const allObjects = client(ExampleDataAircraft).where({
        tailNumber: { $in: pks_list }
    });

    for await (const currentObject of allObjects.asyncIter()) {
        seatsCount += currentObject?.numberOfSeats ?? 0;
    }

    return seatsCount;
}

// forLoopBest.ts - Best: Let the backend perform the aggregation
import { Client } from "@osdk/client";
import { ExampleDataAircraft } from "@ontology/sdk";
import { Integer } from "@osdk/functions";

export default async function forLoopBest(client: Client, pks_list: string[]): Promise<Integer> {
    const result = await client(ExampleDataAircraft).where({
        tailNumber: { $in: pks_list }
    }).aggregate({
        $select: { "numberOfSeats:sum": "unordered" }
    });

    return result.numberOfSeats.sum!;
}
```

**TypeScript v1**

```typescript
import { Function, Integer } from "@foundry/functions-api";
import { Objects, ExampleDataAircraft } from "@foundry/ontology-api";

export class MyFunctions {
    // Anti-pattern: Objects loaded one by one in a loop
    @Function()
    public forLoopWorst(pks_list: string[]): Integer {
        let seatsCount = 0;

        for (const currentPk of pks_list) {
            const aircraft = Objects.search()
                .exampleDataAircraft()
                .filter(o => o.tailNumber.exactMatch(currentPk))
                .all()[0];
            seatsCount += aircraft.numberOfSeats ?? 0;
        }

        return seatsCount;
    }

    // Better: Bulk load objects, then iterate
    @Function()
    public forLoopBetter(pks_list: string[]): Integer {
        const allObjects = Objects.search()
            .exampleDataAircraft()
            .filter(o => o.tailNumber.exactMatch(...pks_list))
            .all();

        let seatsCount = 0;
        for (const currentObject of allObjects) {
            seatsCount += currentObject.numberOfSeats ?? 0;
        }

        return seatsCount;
    }

    // Best: Let the backend perform the aggregation
    @Function()
    public async forLoopBest(pks_list: string[]): Promise<Integer> {
        const seatsCount = await Objects.search()
            .exampleDataAircraft()
            .filter(o => o.tailNumber.exactMatch(...pks_list))
            .sum(o => o.numberOfSeats);

        return seatsCount!;
    }
}
```

### 最佳实践：后端聚合

当你需要计算计数、求和或平均值等聚合时，请使用本体后端的聚合能力，而不是加载对象后在函数中计算。

## 尽可能优先使用 Objects API

[Workshop 函数支撑的列](https://www.palantir.com/docs/foundry/workshop/widgets-object-table/#function-backed-columns)通常通过对每个对象的链接进行聚合来计算值，例如统计相关对象的数量。

> **ℹ️ 提示**
>
> 函数支撑的列不同于[派生属性](https://www.palantir.com/docs/foundry/workshop/derived-properties/)；派生属性直接在 Workshop 中配置，无需编写代码。

虽然下面的代码可以工作，但函数本身必须先取回所有链接对象，然后再执行聚合（在本例中是计算长度）：

**TypeScript v1**

```typescript
import { Function, FunctionsMap, Integer } from "@foundry/functions-api";
import { Employee } from "@foundry/ontology-api";

export class MyFunctions {
    @Function()
    public async getEmployeeProjectCount(employees: Employee[]): Promise<FunctionsMap<Employee, Integer>> {
        const promises = employees.map(employee => employee.workHistory.allAsync());
        const allEmployeeProjects = await Promise.all(promises);
        let functionsMap = new FunctionsMap();
        for (let i = 0; i < employees.length; i++) {
            functionsMap.set(employees[i], allEmployeeProjects[i].length);
        }
        return functionsMap;
    }
}
```

**TypeScript v2**

```typescript
import { ObjectSpecifier, Osdk } from "@osdk/client";
import { Employee } from "@ontology/sdk";
import { Integer } from "@osdk/functions";

export default async function getEmployeeProjectCount(
    employees: Osdk.Instance<Employee>[]
): Promise<Record<ObjectSpecifier<Employee>, Integer>> {
    const allEmployeeProjectCounts = await Promise.all(
        employees.map(async employee => {
            let count = 0;
            for await (const project of employee.$link.workHistory.asyncIter()) {
                count++;
            }
            return count;
        })
    );

    const result: Record<ObjectSpecifier<Employee>, Integer> = {};
    employees.forEach((employee, i) => {
        result[employee.$objectSpecifier] = allEmployeeProjectCounts[i];
    });

    return result;
}
```

虽然上面的代码利用了异步 API 和异步函数（参见[优化链接遍历](#优化链接遍历)），但使用 Objects API 提供的聚合方法通常更有益：

**TypeScript v1**

```typescript
import { Function, FunctionsMap, Integer } from "@foundry/functions-api";
import { Employee, Objects } from "@foundry/ontology-api";

export class MyFunctions {
    @Function()
    public async getEmployeeProjectCount(employees: Employee[]): Promise<FunctionsMap<Employee, Integer>> {
        const result: FunctionsMap<Employee, Integer> = new FunctionsMap();
        // Get all projects that have an employeeId matching from the employees parameter, then count how many projects are mapped to each employeeId
        const aggregation = await Objects.search().project()
                .filter(project => project.employeeId.exactMatch(...employees.map(employee => employee.id)))
                .groupBy(project => project.employeeId.byFixedWidths(1))
                .count();

        const map = new Map();
        aggregation.buckets.forEach(bucket => {
            // bucket.key.min represents the employeeId as each bucket size is 1.
            map.set(bucket.key.min, bucket.value);
        });
        employees.forEach(employee => {
            const value = map.get(employee.primaryKey);
            if (value === undefined) {
                return;
            }
            result.set(employee, value);
        });

        return result;
    }
}
```

**TypeScript v2**

```typescript
import { ObjectSpecifier, Osdk } from "@osdk/client";
import { Employee } from "@ontology/sdk";
import { Integer } from "@osdk/functions";

export default async function getEmployeeProjectCount(
    employees: Osdk.Instance<Employee>[]
): Promise<Record<ObjectSpecifier<Employee>, Integer>> {
    const result: Record<ObjectSpecifier<Employee>, Integer> = {};

    // Count each employee's linked projects using a backend aggregation, rather than loading the project objects themselves
    const counts = await Promise.all(
        employees.map(async employee => ({
            employee,
            count: await employee.$link.workHistory.aggregate({ $select: { $count: "unordered" } }),
        }))
    );

    counts.forEach(({ employee, count }) => {
        result[employee.$objectSpecifier] = count.$count;
    });

    return result;
}
```

这样，你无需先拉取所有链接的项目即可执行聚合。

> **ℹ️ 提示**
>
> 请注意，聚合的常规限制仍然适用。特别是，对字符串 ID 使用 `.topValues()` 只会返回前 1000 个值。目前聚合最多限制为 1 万个桶（bucket），因此你可能需要执行多次聚合才能取回所需结果。更多详情请参阅[计算聚合](https://www.palantir.com/docs/foundry/functions/api-object-sets/#computing-aggregations)。

## 优化链接遍历

函数中性能问题最常见的来源是以低效方式遍历链接。这通常发生在你编写的代码循环遍历大量对象、并在循环的每次迭代中调用 API 加载相关对象时。

**TypeScript v1**

```typescript
for (const employee of employees) {
    const pastProjects = employee.workHistory.all();
}
```

**TypeScript v2**

```typescript
for (const employee of employees) {
    const pastProjects = await employee.$link.workHistory.fetchPage();
}
```

在本例中，循环的每次迭代都会加载某个员工过去参与的项目，导致一次数据库往返。为避免这种减速，当需要一次遍历大量链接时，你可以使用异步链接遍历 API。下面是一个以异步方式加载链接的函数示例：

**TypeScript v1**

```typescript
import { Function } from "@foundry/functions-api";
import { Employee } from "@foundry/ontology-api";

export class MyFunctions {
    @Function()
    public async findEmployeeWithMostProjects(employees: Employee[]): Promise<Employee> {
        // Create a Promise to load projects for each employee
        const promises = employees.map(employee => employee.workHistory.allAsync());
        // Dispatch all the promises, which will load all links in parallel
        const allEmployeeProjects = await Promise.all(promises);
        // Iterate through the results to find the employee who has the greatest number of projects
        let result;
        let maxProjectsLength;
        for (let i = 0; i < employees.length; i++) {
            const employee = employees[i];
            const projects = allEmployeeProjects[i];

            if (!result || projects.length > maxProjectsLength) {
                result = employee;
                maxProjectsLength = projects.length;
            }
        }

        return result;
    }
}
```

**TypeScript v2**

```typescript
import { Osdk } from "@osdk/client";
import { Employee, Project } from "@ontology/sdk";

async function getProjects(employee: Osdk.Instance<Employee>): Promise<Osdk.Instance<Project>[]> {
    const projects: Osdk.Instance<Project>[] = [];
    for await (const project of employee.$link.workHistory.asyncIter()) {
        projects.push(project);
    }
    return projects;
}

export default async function findEmployeeWithMostProjects(
    employees: Osdk.Instance<Employee>[]
): Promise<Osdk.Instance<Employee>> {
    // Dispatch a request to load projects for each employee, which will load all links in parallel
    const allEmployeeProjects = await Promise.all(employees.map(employee => getProjects(employee)));

    // Iterate through the results to find the employee who has the greatest number of projects
    let result = employees[0];
    let maxProjectsLength = allEmployeeProjects[0].length;
    for (let i = 1; i < employees.length; i++) {
        if (allEmployeeProjects[i].length > maxProjectsLength) {
            result = employees[i];
            maxProjectsLength = allEmployeeProjects[i].length;
        }
    }

    return result;
}
```

TypeScript v1 示例使用 ES6 [异步函数 ↗](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)，可以方便地处理 `.getAsync()` 和 `.allAsync()` 方法返回的 `Promise` 返回值。TypeScript v2 示例则将 `Promise.all()` 与 `$link` 和 `asyncIter()` 结合使用，并行加载每个员工所链接的项目。

## 理解异步操作与资源使用

异步操作可以加快函数执行速度，但不一定能减少资源使用。理解这一区别对于成本优化非常重要。

**Python**

```python
from functions.api import function, Float
from ontology_sdk.ontology.object_sets import ExampleDataAircraftObjectSet

# Best: Bulk operation using search around

@function
def bulk_processing(aircraft_set: ExampleDataAircraftObjectSet) -> Float:
    all_maintenance_events = aircraft_set.search_around_example_data_aircraft_maintenance_event()
    return all_maintenance_events.count().compute()
```

**TypeScript v2**

```typescript
// forLoopAsync.ts - Faster execution, but still multiple Ontology calls
import { Client, ObjectSet, Osdk } from "@osdk/client";
import { ExampleDataAircraft } from "@ontology/sdk";
import { Integer } from "@osdk/functions";

async function getMaintenanceEventCount(
    client: Client,
    aircraft: Osdk.Instance<ExampleDataAircraft>
): Promise<Integer> {
    const aircraftSet = client(ExampleDataAircraft).where({
        tailNumber: { $eq: aircraft.tailNumber }
    });
    const maintenanceEvents = aircraftSet.pivotTo("exampleDataAircraftMaintenanceEvent");
    const result = await maintenanceEvents.aggregate({
        $select: { $count: "unordered" }
    });
    return result.$count ?? 0;
}

export default async function forLoopAsync(
    client: Client,
    aircraftSet: ObjectSet<ExampleDataAircraft>
): Promise<Integer> {
    const allObjects: Osdk.Instance<ExampleDataAircraft>[] = [];
    for await (const obj of aircraftSet.asyncIter()) {
        allObjects.push(obj);
    }

    const futures = allObjects.map(obj => getMaintenanceEventCount(client, obj));
    const results = await Promise.all(futures);

    return results.reduce((sum, count) => sum + count, 0);
}

// bulkProcessing.ts - Best: Single Ontology operation
import { ObjectSet } from "@osdk/client";
import { ExampleDataAircraft } from "@ontology/sdk";
import { Integer } from "@osdk/functions";

export default async function bulkProcessing(
    aircraftSet: ObjectSet<ExampleDataAircraft>
): Promise<Integer> {
    const allMaintenanceEvents = aircraftSet.pivotTo("exampleDataAircraftMaintenanceEvent");
    const result = await allMaintenanceEvents.aggregate({
        $select: { $count: "unordered" }
    });
    return result.$count ?? 0;
}
```

**TypeScript v1**

```typescript
import { Function, Integer } from "@foundry/functions-api";
import { ObjectSet, ExampleDataAircraft } from "@foundry/ontology-api";
import { Objects } from "@foundry/ontology-api";

export class MyFunctions {
    private async getMaintenanceEventCount(aircraft: ExampleDataAircraft): Promise<Integer> {
        const aircraftSet = Objects.search().exampleDataAircraft([aircraft]);
        const maintenanceEvents = aircraftSet.searchAroundExampleDataAircraftMaintenanceEvent();
        return await maintenanceEvents.count() ?? 0;
    }

    // Faster execution, but still multiple Ontology calls
    @Function()
    public async forLoopAsync(aircraftSet: ObjectSet<ExampleDataAircraft>): Promise<Integer> {
        const allObjects = aircraftSet.all();
        const futures = allObjects.map(obj => this.getMaintenanceEventCount(obj));
        const results = await Promise.all(futures);
        return results.reduce((sum, count) => sum + count, 0);
    }

    // Best: Single Ontology operation
    @Function()
    public async bulkProcessing(aircraftSet: ObjectSet<ExampleDataAircraft>): Promise<Integer> {
        const allMaintenanceEvents = aircraftSet.searchAroundExampleDataAircraftMaintenanceEvent();
        return await allMaintenanceEvents.count() ?? 0;
    }
}
```

> **⚠️ 注意：异步操作提升速度，而非降低成本**
>
> 使用 `Promise.all()` 等异步操作可以通过并行运行来提升执行速度。但必须理解的是，异步操作**不会减少资源消耗或成本**——它们只是让事情更快。
>
> 例如，将逐个查询的循环并行化比串行运行更快，但你发起的查询数量不变。**将计算下推到后端的批量操作**，比上述两种方式都更快、更节省资源。

## 编写高效的本体编辑

编写编辑对象的函数时，请应用同样的批量加载原则。一次性预先加载所有对象，而不是逐个加载。

### 编辑大量对象

编辑大量对象时，请使用分页（通过 `iterate` 或 `asyncIter` 显式或隐式地分页），将对象分成便于管理的小块来处理，而不是一次性全部加载到内存中。

**Python**

```python
from functions.api import function, OntologyEdit
from ontology_sdk.ontology.objects import ExampleDataAircraft
from ontology_sdk.ontology.object_sets import ExampleDataAircraftObjectSet
from ontology_sdk import FoundryClient

# Single object edit

@function(edits=[ExampleDataAircraft])
def edit_aircraft_name(aircraft: ExampleDataAircraft) -> list[OntologyEdit]:
    ontology_edits = FoundryClient().ontology.edits()
    editable = ontology_edits.objects.ExampleDataAircraft.edit(aircraft)
    editable.display_name = "new display name"
    return ontology_edits.get_edits()

# Bulk edit using object set with iteration

@function(edits=[ExampleDataAircraft])
def edit_all_aircrafts(aircraft_set: ExampleDataAircraftObjectSet) -> list[OntologyEdit]:
    ontology_edits = FoundryClient().ontology.edits()

    for aircraft in aircraft_set.iterate():
        editable = ontology_edits.objects.ExampleDataAircraft.edit(aircraft)
        editable.display_name = "new display name"

    return ontology_edits.get_edits()

# Alternative: Pagination

# This processes objects in chunks. The iterate() method above takes care of it behind the scenes.
@function(edits=[ExampleDataAircraft])
def edit_all_with_pagination(aircraft_set: ExampleDataAircraftObjectSet) -> list[OntologyEdit]:
    edits = FoundryClient().ontology.edits()

    next_token = None
    while True:
        page = aircraft_set.page(1000, next_token)
        for aircraft in page.data:
            editable = edits.objects.ExampleDataAircraft.edit(aircraft)
            editable.status = "reviewed"

        next_token = page.next_page_token
        if not next_token:
            break

    return edits.get_edits()
```

**TypeScript v2**

```typescript
// editAircraftName.ts - Single object edit
import { Osdk, Client } from "@osdk/client";
import { ExampleDataAircraft } from "@ontology/sdk";
import { Edits, createEditBatch } from "@osdk/functions";

type OntologyEdit = Edits.Object<ExampleDataAircraft>;

export default async function editAircraftName(
    client: Client,
    aircraft: Osdk.Instance<ExampleDataAircraft>
): Promise<OntologyEdit[]> {
    const batch = createEditBatch<OntologyEdit>(client);
    batch.update(aircraft, { displayName: "new display name" });
    return batch.getEdits();
}

// editAllAircrafts.ts - Bulk edit using object set
import { Client, ObjectSet } from "@osdk/client";
import { ExampleDataAircraft } from "@ontology/sdk";
import { Edits, createEditBatch } from "@osdk/functions";

type OntologyEdit = Edits.Object<ExampleDataAircraft>;

export default async function editAllAircrafts(
    client: Client,
    aircraftSet: ObjectSet<ExampleDataAircraft>
): Promise<OntologyEdit[]> {
    const batch = createEditBatch<OntologyEdit>(client);

    for await (const aircraft of aircraftSet.asyncIter()) {
        batch.update(aircraft, { displayName: "new display name" });
    }

    return batch.getEdits();
}
```

**TypeScript v1**

```typescript
import { Function, OntologyEditFunction, Edits } from "@foundry/functions-api";
import { ObjectSet, ExampleDataAircraft } from "@foundry/ontology-api";

export class MyFunctions {
    // Single object edit
    @Edits(ExampleDataAircraft)
    @OntologyEditFunction()
    public editAircraftName(aircraft: ExampleDataAircraft): void {
        aircraft.displayName = "new display name";
    }

    // Array edit
    @Edits(ExampleDataAircraft)
    @OntologyEditFunction()
    public editAircraftsNames(aircraftArray: ExampleDataAircraft[]): void {
        aircraftArray.forEach(aircraft => {
            aircraft.displayName = "new display name";
        });
    }

    // Object set edit - most efficient when you need to edit many objects
    @Edits(ExampleDataAircraft)
    @OntologyEditFunction()
    public editAllAircrafts(aircraftSet: ObjectSet<ExampleDataAircraft>): void {
        aircraftSet.all().forEach(aircraft => {
            aircraft.displayName = "new display name";
        });
    }
}
```

## 优化派生列生成

Workshop 支持使用对象函数（functions on objects，FOO）来计算函数支撑的列。Workshop 应用通常会用对象表中几十行内容调用这些函数。函数随后返回一个映射（map），将每个对象映射到派生列中的显示值。

### 未优化的基础实现

下面是一个未优化的实现，作为基准情形：

**TypeScript v1**

```typescript
import { Function, FunctionsMap, Double } from "@foundry/functions-api";
import { Objects, ObjectSet, objectTypeA } from "@foundry/ontology-api";

export class MyFunctions {
    /**
     * This Function takes an ObjectSet as input, and generates a derived column as output.
     * This derived column maps each object instance to the numeric value that will populate the column.
     * This implementation is a trivial for-loop that multiplies an object property by a constant value.
     * This serves as the base case that we will improve below.
     */
    @Function()
    public getDerivedColumn_noOptimization(objects: ObjectSet<objectTypeA>, scalar: Double): FunctionsMap<objectTypeA, Double> {
        // Define the result map to return
        const resultMap = new FunctionsMap<objectTypeA, Double>();

        /* There is a limit to the number of objects that can be loaded in memory.
         * See enforced limit documentation for current object set load limits.
         */
        const allObjs: objectTypeA[] = objects.all();

        // For each loaded object, perform the computation. If the result is defined, store it in the result map.
        allObjs.forEach(o => {
            const result = this.computeForThisObject(o, scalar);
            if (result) {
                resultMap.set(o, result);
            }
        });

        return resultMap;
    }

    // An example of a function that computes the required value for the provided object.
    private computeForThisObject(obj: objectTypeA, scalar: Double): Double | undefined {
        if (scalar === 0) {
            // Division by zero error
            return undefined;
        }
        // Checks if exampleProperty is defined, and divides if so. If not, it returns undefined.
        return obj.exampleProperty ? obj.exampleProperty / scalar : undefined;
    }
}
```

**TypeScript v2**

```typescript
import { ObjectSet, ObjectSpecifier, Osdk } from "@osdk/client";
import { ObjectTypeA } from "@ontology/sdk";
import { Double } from "@osdk/functions";

// An example of a function that computes the required value for the provided object.
function computeForThisObject(obj: Osdk.Instance<ObjectTypeA, never, "exampleProperty">, scalar: Double): Double | undefined {
    if (scalar === 0) {
        // Division by zero error
        return undefined;
    }
    // Checks if exampleProperty is defined, and divides if so. If not, it returns undefined.
    return obj.exampleProperty ? obj.exampleProperty / scalar : undefined;
}

/**
 * This function takes an ObjectSet as input, and generates a derived column as output.
 * This derived column maps each object instance to the numeric value that will populate the column.
 * This implementation is a trivial for-loop that multiplies an object property by a constant value.
 * This serves as the base case that we will improve below.
 */
export default async function getDerivedColumnNoOptimization(
    objects: ObjectSet<ObjectTypeA>,
    scalar: Double
): Promise<Record<ObjectSpecifier<ObjectTypeA>, Double>> {
    // Define the result map to return
    const resultMap: Record<ObjectSpecifier<ObjectTypeA>, Double> = {};

    /* There is a limit to the number of objects that can be loaded in memory.
     * See enforced limit documentation for current object set load limits.
     */
    for await (const obj of objects.asyncIter({ $select: ["exampleProperty"] })) {
        // For each loaded object, perform the computation. If the result is defined, store it in the result map.
        const result = computeForThisObject(obj, scalar);
        if (result !== undefined) {
            resultMap[obj.$objectSpecifier] = result;
        }
    }

    return resultMap;
}
```

### 并行执行优化

如果计算很复杂，可以通过异步执行来减少计算时间。这样，每个对象的计算都会并行执行：

**TypeScript v1**

```typescript
import { Function, FunctionsMap, Double } from "@foundry/functions-api";
import { Objects, ObjectSet, objectTypeA, objectTypeB } from "@foundry/ontology-api";

/**
 * This function takes a list of strings that are object primaryKeys as input, and generates a derived column as output.
 */
@Function()
public async getDerivedColumn_parallel(objects: ObjectSet<objectTypeA>, scalar: Double): Promise<FunctionsMap<objectTypeA, Double>> {
    // Define the result map
    const resultMap = new FunctionsMap<objectTypeA, Double>();

    /* There is a limit to the number of objects that can be loaded in memory.
     * See enforced limit documentation for current object set load limits.
     * This should not be a problem as Workshop can lazy-load as users are scrolling.
     */
    const allObjs: objectTypeA[] = objects.all();

    // Launch parallel computations for each object in the array
    const promises = allObjs.map(currObject => this.computeForThisObject(currObject, scalar));

    // Use Promise.all to parallelize async execution of helper function
    const allResolvedPromises = await Promise.all(promises);

    // Populate resultMap with results
    for (let i = 0; i < allObjs.length; i++) {
        resultMap.set(allObjs[i], allResolvedPromises[i]);
    }

    return resultMap;
}

// An example of a function that computes the required value for the provided object.
private async computeForThisObject(obj: objectTypeA, scalar: Double): Promise<Double | undefined> {
    if (scalar === 0) {
        // Division by zero error
        return undefined;
    }
    // Checks if exampleProperty is defined, and divides if so. If not, it returns undefined.
    return obj.exampleProperty ? obj.exampleProperty / scalar : undefined;
}
```

**TypeScript v2**

```typescript
import { ObjectSet, ObjectSpecifier, Osdk } from "@osdk/client";
import { ObjectTypeA } from "@ontology/sdk";
import { Double } from "@osdk/functions";

// An example of a function that computes the required value for the provided object.
async function computeForThisObject(obj: Osdk.Instance<ObjectTypeA, never, "exampleProperty">, scalar: Double): Promise<Double | undefined> {
    if (scalar === 0) {
        // Division by zero error
        return undefined;
    }
    // Checks if exampleProperty is defined, and divides if so. If not, it returns undefined.
    return obj.exampleProperty ? obj.exampleProperty / scalar : undefined;
}

/**
 * This function takes an ObjectSet as input, and generates a derived column as output.
 */
export default async function getDerivedColumnParallel(
    objects: ObjectSet<ObjectTypeA>,
    scalar: Double
): Promise<Record<ObjectSpecifier<ObjectTypeA>, Double>> {
    // Define the result map
    const resultMap: Record<ObjectSpecifier<ObjectTypeA>, Double> = {};

    /* There is a limit to the number of objects that can be loaded in memory.
     * See enforced limit documentation for current object set load limits.
     * This should not be a problem as Workshop can lazy-load as users are scrolling.
     */
    const allObjs: Osdk.Instance<ObjectTypeA, never, "exampleProperty">[] = [];
    for await (const obj of objects.asyncIter({ $select: ["exampleProperty"] })) {
        allObjs.push(obj);
    }

    // Launch parallel computations for each object in the array
    const promises = allObjs.map(currObject => computeForThisObject(currObject, scalar));

    // Use Promise.all to parallelize async execution of helper function
    const allResolvedPromises = await Promise.all(promises);

    // Populate resultMap with results
    allObjs.forEach((obj, i) => {
        const result = allResolvedPromises[i];
        if (result !== undefined) {
            resultMap[obj.$objectSpecifier] = result;
        }
    });

    return resultMap;
}
```

### 进阶：计算过程中的本体筛选

对于每个对象都需要查询本体的更复杂情形，请参见下面的示例。
**注意：** 同样的做法也适用于生成 `TwoDimensionalAggregation` 来填充 Workshop 中的 [Chart XY 组件](https://www.palantir.com/docs/foundry/workshop/widgets-chart/)。你可以传入类别字符串（桶）列表进行计算，而不是传入对象实例列表。下面是一个示例：

**TypeScript v1**

```typescript
/**
 * An example of a function that computes the required value for the provided object.
 * For a given object, query the Ontology (filter for other objects, search-around to another object set, etc.)
 */
@Function()
private async computeForThisObject_filterOntology(obj: objectTypeA): Promise<Double> {
    // Create an object set by filtering on some properties
    const currObjectSet = await Objects.search().objectTypeB().filter(o => o.property.exactMatch(obj.exampleProperty));
    // Note: If there is an existing link between the ObjectTypes, an alternative would be:
    // const currObjectSet = await Objects.search().objectTypeA([obj]).searchAroundObjectTypeB();
    
    // Compute the aggregation for this object set
    return await this.computeMetric_B(currObjectSet);
}

@Function()
public async computeMetric_B(objs: ObjectSet<objectTypeB>): Promise<Double> {
    // Set up calls to different parts of the equation
    const promises = [this.sumValue(objs), this.sumValueIfPresent(objs)];

    // Execute all promises
    const allResolvedPromises = await Promise.all(promises);

    // Get values from the promises
    const sum = allResolvedPromises[0];
    const sumIfPresent = allResolvedPromises[1];

    // Perform calculation
    return sum / sumIfPresent;
}

@Function()
public async sumValue(objs: ObjectSet<objectTypeB>): Promise<Double> {
    // Sum the values of the objects
    const aggregation = await objs.sum(o => o.propertyToAggregateB);
    const firstBucketValue = aggregation.primaryKeys[0].value;
    return firstBucketValue;
}

@Function()
public async sumValueIfPresent(objs: ObjectSet<objectTypeB>): Promise<Double> {
    // Sum the object values if they are not null
    const aggregation = await objs.filter(o => o.metric.hasProperty()).sum(o => o.propertyToAggregateA);
    const firstBucketValue = aggregation.primaryKeys[0].value;
    return firstBucketValue;
}
```

**TypeScript v2**

```typescript
import { Client, ObjectSet, ObjectSpecifier, Osdk } from "@osdk/client";
import { ObjectTypeA, ObjectTypeB } from "@ontology/sdk";
import { Double } from "@osdk/functions";

// Sum the values of the objects
async function sumValue(objs: ObjectSet<ObjectTypeB>): Promise<Double> {
    const aggregation = await objs.aggregate({
        $select: { "propertyToAggregateB:sum": "unordered" },
    });
    return aggregation.propertyToAggregateB.sum ?? 0;
}

// Sum the object values if they are not null
async function sumValueIfPresent(objs: ObjectSet<ObjectTypeB>): Promise<Double> {
    const aggregation = await objs
        .where({ metric: { $isNull: false } })
        .aggregate({ $select: { "propertyToAggregateA:sum": "unordered" } });
    return aggregation.propertyToAggregateA.sum ?? 0;
}

async function computeMetricB(objs: ObjectSet<ObjectTypeB>): Promise<Double> {
    // Set up calls to different parts of the equation and execute them in parallel
    const [sum, sumIfPresent] = await Promise.all([sumValue(objs), sumValueIfPresent(objs)]);

    // Perform calculation
    return sum / sumIfPresent;
}

/**
 * An example of a function that computes the required value for the provided object.
 * For a given object, query the Ontology (filter for other objects, pivot to another object set, etc.)
 */
async function computeForThisObjectFilterOntology(client: Client, obj: Osdk.Instance<ObjectTypeA, never, "exampleProperty">): Promise<Double> {
    // Create an object set by filtering on some properties
    const currObjectSet = client(ObjectTypeB).where({ property: { $eq: obj.exampleProperty } });
    // Note: If there is an existing link between the ObjectTypes, an alternative would be:
    // const currObjectSet = obj.$link.objectTypeB;

    // Compute the aggregation for this object set
    return await computeMetricB(currObjectSet);
}

/**
 * Takes an ObjectSet as input and generates a derived column, calling
 * computeForThisObjectFilterOntology for each object in parallel.
 */
export default async function getDerivedColumnFilterOntology(
    client: Client,
    objects: ObjectSet<ObjectTypeA>
): Promise<Record<ObjectSpecifier<ObjectTypeA>, Double>> {
    const resultMap: Record<ObjectSpecifier<ObjectTypeA>, Double> = {};

    const allObjs: Osdk.Instance<ObjectTypeA, never, "exampleProperty">[] = [];
    for await (const obj of objects.asyncIter({ $select: ["exampleProperty"] })) {
        allObjs.push(obj);
    }

    const results = await Promise.all(
        allObjs.map(obj => computeForThisObjectFilterOntology(client, obj))
    );
    allObjs.forEach((obj, i) => {
        resultMap[obj.$objectSpecifier] = results[i];
    });

    return resultMap;
}
```

### 转换为 TwoDimensionalAggregation

若要在 Workshop 中配合 [Chart XY 组件](https://www.palantir.com/docs/foundry/workshop/widgets-chart/)使用，你可以根据计算结果生成一个 `TwoDimensionalAggregation`：

**TypeScript v1**

```typescript
@Function()
public async getDerivedColumn_parallel_asTwoDimensional(objects: ObjectSet<objectTypeA>, scalar: Double): Promise<TwoDimensionalAggregation<string>> {
    const resultMap: FunctionsMap<objectTypeA, Double> = await this.getDerivedColumn_parallel(objects, scalar);

    // Create a TwoDimensionalAggregation from the resultMap
    const aggregation: TwoDimensionalAggregation<string> = {
        // Map the entries (object -> Double) of resultMap to (string -> Double)
        buckets: Array.from(resultMap.entries()).map(([key, value]) => ({
            key: key.pkProperty, // Use the primary key property
            value
        })),
    };

    return aggregation;
}
```

**TypeScript v2**

```typescript
import { ObjectSet, Osdk } from "@osdk/client";
import { ObjectTypeA } from "@ontology/sdk";
import { Double, TwoDimensionalAggregation } from "@osdk/functions";

export default async function getDerivedColumnParallelAsTwoDimensional(
    objects: ObjectSet<ObjectTypeA>,
    scalar: Double
): Promise<TwoDimensionalAggregation<string, Double>> {
    const allObjs: Osdk.Instance<ObjectTypeA, never, "exampleProperty">[] = [];
    for await (const obj of objects.asyncIter({ $select: ["exampleProperty"] })) {
        allObjs.push(obj);
    }

    // Reuses computeForThisObject from the parallel execution example above
    const allResolvedPromises = await Promise.all(allObjs.map(currObject => computeForThisObject(currObject, scalar)));

    // Create a TwoDimensionalAggregation, keyed by the primary key property, from the computed values
    return allObjs
        .map((obj, i) => ({ key: String(obj.$primaryKey), value: allResolvedPromises[i] }))
        .filter((bucket): bucket is { key: string; value: Double } => bucket.value !== undefined);
}
```

---

*原文：[Optimize performance](https://www.palantir.com/docs/foundry/functions/optimize-performance/)*
