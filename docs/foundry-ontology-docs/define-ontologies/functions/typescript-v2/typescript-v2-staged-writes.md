# 暂存写入 [Beta]（Staged writes [Beta]）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/typescript-v2-staged-writes/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

> **ℹ️ 提示：Beta**
>
> 暂存写入处于开发的 [beta（测试版）](https://www.palantir.com/docs/foundry/platform-overview/development-life-cycle/)阶段，可能在你的注册实例上不可用。在活跃开发期间，功能可能会发生变化。请联系 Palantir 支持以申请访问权限。
>
> 实时预览和已发布函数预览仅在 Code Repositories 中受支持，在本地开发或 VS Code 工作区环境中不受支持。

暂存写入为编辑本体中对象的函数提供了一种额外的执行模型。与通过常规[本体编辑函数](https://www.palantir.com/docs/foundry/functions/typescript-v2-ontology-edits/)进行的编辑不同，暂存写入函数：

- 为函数中应用的本体编辑提供写后读保证。函数内应用的所有编辑都会被暂存，并将反映在函数后续的本体查询和聚合中。
- 允许嵌套调用其他进行本体编辑的暂存写入函数。

本页介绍如何编写暂存写入函数，并记录它们的独特属性。有关编辑函数工作方式的更多详情，请参阅[概览页](https://www.palantir.com/docs/foundry/functions/edits-overview/)。

## 与常规本体编辑函数的关键区别

暂存写入函数在几个重要方面与常规编辑函数不同。

### 写后读保证

在暂存写入函数内，任何本体数据读取都会反映先前在函数中所做的所有编辑（以及在同一动作执行中调用方暂存写入函数所做的所有编辑）。此类编辑仅被暂存，对其他用户或在执行上下文之外通过函数进行的查询不可见。这使你能够在函数内使用搜索请求和聚合查询本体，并且所有暂存的编辑都会反映在查询结果中。

### 无需在函数末尾返回编辑

常规的 Python 和 TypeScript V2 本体编辑函数要求返回一批本体编辑作为函数的返回值，这些编辑才会被应用。在暂存写入函数中，本体编辑会自动暂存，并将在动作完成时（即函数执行结束时）应用到本体。这释放了函数的返回值，使其可以向调用者返回其他信息。

例如，你可以应用一个执行 TypeScript V2 暂存写入函数的动作，该函数随后进行一些编辑并进一步调用 AIP Logic 函数。AIP Logic 函数进行的查询将返回在 TypeScript V2 函数中所做的本体更改；AIP Logic 函数进行的任何额外编辑都会加入相同的暂存编辑，而无需作为 Logic 函数的一部分返回它们。一旦动作完成，所有暂存编辑都将自动应用。

### 原子执行

暂存写入函数内的所有操作（包括查询、函数调用和 AIP Logic 执行）都会将它们的编辑暂存在一起。这些暂存编辑在函数成功完成后被提交（即应用到本体）。如果函数抛出错误，本体保持不变，并且所有暂存编辑在函数被动作重试之前被丢弃。

### `WriteableClient`

暂存写入函数使用 `WriteableClient` 而不是标准的 `Client`。`WriteableClient` 提供了用于创建、更新和删除对象的直接方法，无需构建编辑批处理。

## 定义暂存写入函数

暂存写入函数必须使用从 `@osdk/functions` 包导出的 `Edits` 类型显式声明将要编辑的实体。第一个参数必须是 `WriteableClient<T>`，其中 `T` 是函数将执行的所有编辑类型的联合。返回值不再限制为编辑数组，因此你可以返回任何值。以下示例声明了一个将编辑 `Employee` 对象类型的函数：

```typescript
import { Employee } from "@ontology/sdk";
import { Edits, Integer } from "@osdk/functions";
import { WriteableClient } from "@osdk/functions/experimental";

type OntologyEdit = Edits.Object<Employee>;

export default async function assignTicket(
    client: WriteableClient<OntologyEdit>,
    employeeId: string,
    ticketId: string
): Promise<Integer> {
    // ...
}
```

## 创建对象

使用 `WriteableClient` 上的 `create` 方法创建新对象。你必须指定对象类型、为主键提供值，并提供你想要初始化的任何其他属性。

```typescript
import { Employee } from "@ontology/sdk";
import { Edits } from "@osdk/functions";
import { WriteableClient } from "@osdk/functions/experimental";

type OntologyEdit = Edits.Object<Employee>;

async function createEmployee(
    client: WriteableClient<OntologyEdit>,
    employeeId: string,
    firstName: string,
    lastName: string
): Promise<Integer> {
    await client.create(Employee, {
        employeeId: employeeId,
        firstName: firstName,
        lastName: lastName
    });

    return employeeId;
}

export default createEmployee;
```

### 使用生成的 ID 创建

当你需要生成一个 ID 然后立即使用它时：

```typescript
import { Ticket } from "@ontology/sdk";
import { Edits, Integer } from "@osdk/functions";
import { WriteableClient } from "@osdk/functions/experimental";
import { randomUUID } from "crypto";

type OntologyEdit = Edits.Object<Ticket>;

async function createTicket(
    client: WriteableClient<OntologyEdit>,
    title: string
): Promise<string> {
    const ticketId = randomUUID();

    await client.create(Ticket, {
        ticketId: ticketId,
        title: title,
        status: "open"
    });

    return ticketId;
}

export default createTicket;
```

## 更新对象

### 对象属性

使用 `WriteableClient` 上的 `update` 方法来修改对象属性：

```typescript
await client.update(employee, { lastName: newName });
```

你也可以通过引用对象的 API 名称和主键来更新对象：

```typescript
await client.update({ $apiName: "Employee", $primaryKey: 23 }, { lastName: newName });
```

暂存写入函数不支持接口编辑。

## 删除对象

你可以通过在 `WriteableClient` 上调用 `delete` 方法来删除对象：

```typescript
await client.delete(ticket);
```

也可以使用主键而不是实例来删除对象：

```typescript
await client.delete({ $apiName: "Ticket", $primaryKey: 12 });
```

## 创建或删除链接

使用 `WriteableClient` 上的 `link` 和 `unlink` 方法来添加或移除对象之间的多对多链接：

```typescript
// Assign a ticket to an employee
await client.link(employee, "assignedTickets", ticket);

// Unassign a ticket from an employee
await client.unlink(employee, "assignedTickets", ticket);
```

你也可以使用 API 名称和主键来引用链接的任一侧：

```typescript
await client.link(
    { $apiName: "Employee", $primaryKey: 23 },
    "assignedTickets",
    { $apiName: "Ticket", $primaryKey: 12 }
);
```

要编辑一对多链接，请通过创建或更新对象的编辑来修改外键属性。

## 暂存写入函数内的写后读

暂存写入函数的关键优势之一是能够在同一执行中读取刚刚写入的数据。这对于实现需要即时一致性的工作流非常有用。

```typescript
import { Employee, Ticket } from "@ontology/sdk";
import { Edits, Integer } from "@osdk/functions";
import { WriteableClient } from "@osdk/functions/experimental";
import { randomUUID } from "crypto";

type OntologyEdit = Edits.Object<Ticket> | Edits.Link<Employee, "assignedTickets">;

async function assignTicketAndCheckWorkload(
    client: WriteableClient<OntologyEdit>,
    employeeId: Integer,
    title: string
): Promise<{ ticketId: string, totalAssignedTickets: number }> {
    const ticketId = randomUUID();

    // Create the ticket
    await client.create(Ticket, {
        ticketId: ticketId,
        title: title,
        status: "open"
    });

    // Assign the ticket to the employee
    await client.link(
        { $apiName: "Employee", $primaryKey: employeeId },
        "assignedTickets",
        { $apiName: "Ticket", $primaryKey: ticketId }
    );

    // Query the employee's total workload including the newly assigned ticket.
    // This works because of the read-after-write guarantee.
    const result = await client(Ticket).aggregate((tickets) =>
        tickets
            .where(ticket => ticket.assignedTo.employeeId.exactMatch(employeeId))
            .where(ticket => ticket.status.exactMatch("open"))
            .count()
    );

    return {
        ticketId: ticketId,
        totalAssignedTickets: result
    };
}

export default assignTicketAndCheckWorkload;
```

## 从暂存写入函数调用其他函数

当你在暂存写入函数内调用另一个函数或查询时，这些操作会参与相同的暂存编辑。任何读取都会反映执行中先前暂存的编辑，被调用函数进行的任何编辑都会添加到相同的暂存编辑中。这适用于：

- 其他 TypeScript 暂存写入函数
- AIP Logic 函数
- 本体查询

如果顶层函数成功完成，则跨嵌套调用暂存的每个编辑都会一起提交。如果任何调用抛出异常，整个批处理都会回滚。

在下面的示例中，`assignTicket` 是从同一仓库发布的独立暂存写入函数。`bulkAssignTickets` 通过 OSDK 生成的 `$Queries` 导入调用它；每次调用都会将其编辑添加到相同的暂存编辑中。

```typescript
import { Employee, Ticket, $Queries } from "@ontology/sdk";
import { Edits, Integer } from "@osdk/functions";
import { WriteableClient } from "@osdk/functions/experimental";

type OntologyEdit = Edits.Object<Ticket> | Edits.Link<Employee, "assignedTickets">;

async function bulkAssignTickets(
    client: WriteableClient<OntologyEdit>,
    employeeId: Integer,
    ticketIds: string[]
): Promise<Integer> {
    let assignedCount = 0;

    for (const ticketId of ticketIds) {
        // Each call to `assignTicket` stages its edits alongside those of
        // the calling function.
        await client($Queries.assignTicket).executeFunction({
            employeeId: employeeId,
            ticketId: ticketId,
        });

        assignedCount++;
    }

    // All staged edits across the nested calls will be committed together
    // when the top-level function completes.
    return assignedCount;
}

export default bulkAssignTickets;
```

## 执行生命周期

了解暂存编辑何时被提交对于构建可靠的函数非常重要：

1. **函数执行：** 所有操作（创建、更新、删除、读取、嵌套函数调用）都被暂存到本体。它们对函数本身和所有嵌套函数可见，但在提交之前不会出现在当前执行之外。
2. **提交：** 如果函数成功完成，所有暂存编辑会在动作结束前被提交。
3. **出错时回滚：** 如果函数抛出错误，本体保持不变，所有暂存编辑都被丢弃。随后函数会被动作重试。

```typescript
import { Employee } from "@ontology/sdk";
import { Edits, Integer } from "@osdk/functions";
import { WriteableClient } from "@osdk/functions/experimental";

async function updateEmployeeWithValidation(
    client: WriteableClient<Edits.Object<Employee>>,
    employeeId: Integer,
    newSalary: number
): Promise<Integer> {
    // Validate input
    if (newSalary < 0) {
        // Staged edits will be discarded
        throw new Error("Salary cannot be negative");
    }

    // Update the employee
    await client.update(
        { $apiName: "Employee", $primaryKey: employeeId },
        { salary: newSalary }
    );

    // If we reach here, all staged edits will be committed atomically
}

export default updateEmployeeWithValidation;
```

---

*原文：[Staged writes [Beta]](https://www.palantir.com/docs/foundry/functions/typescript-v2-staged-writes/)*
