# 本体编辑（Ontology edits）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/typescript-v2-ontology-edits/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

除了编写从本体读取数据的函数外，你还可以编写创建对象以及编辑对象之间属性和链接的函数。
本页记录了函数中可供你使用的对象编辑 API。
有关编辑函数工作方式的更多详情，请参阅[概览页](https://www.palantir.com/docs/foundry/functions/edits-overview/)。

要使函数中创建的编辑真正被应用，本体编辑函数*必须配置为[函数支撑的动作](https://www.palantir.com/docs/foundry/action-types/function-actions-overview/)*。
以这种方式配置动作允许你提供额外的元数据、配置权限，并在各种运营界面中访问该动作。
正如[文档](https://www.palantir.com/docs/foundry/functions/edits-overview/#when-edits-are-applied)中所指出的，在动作之外运行编辑函数实际上不会修改任何对象数据。

> **ℹ️ 提示**
>
> 标准本体编辑使用编辑批次。TypeScript v2 还支持[暂存写入](https://www.palantir.com/docs/foundry/functions/typescript-v2-staged-writes/)，它在函数内提供写后读保证。使用暂存写入时，编辑对同一执行中后续的查询立即可见，并且嵌套的暂存写入函数调用会共享这些编辑。当函数必须读取它刚刚写入的数据时，请使用暂存写入。

> **⚠️ 注意：警告**
>
> 在编辑对象后立即搜索它们可能会返回意外的结果。详情请参阅[注意事项部分](https://www.palantir.com/docs/foundry/functions/edits-overview/#edits-and-object-search)。

## 定义编辑函数

编辑本体的函数必须使用从 `@osdk/functions` 包导出的 `Edits` 类型显式声明将要编辑的实体。以下示例声明了一个新类型，表示对 `Employee` 和 `Ticket` 对象类型以及 `Employee` 和 `Ticket` 之间的链接类型的编辑。对多个实体的编辑需要使用 `|` 运算符连接。

```typescript
import { Employee, Person, Ticket } from "@ontology/sdk";
import { Edits } from "@osdk/functions";

type OntologyEdit = Edits.Object<Employee> | Edits.Interface<Person> | Edits.Object<Ticket> | Edits.Link<Employee, "assignedTickets">;
```

然后，你必须声明该函数返回由该新类型编辑组成的数组。

```typescript
export default function createNewTicketAndAssignToEmployee(): OntologyEdit[] {
    // ...
}
```

## 构建本体编辑批次

要在 TypeScript v2 函数中执行本体编辑，首先使用从 `@osdk/functions` 导出的 `createEditBatch` 函数构建一个本体编辑批次，并将先前声明的类型作为类型参数传入：

```typescript
import { Employee, Ticket } from "@ontology/sdk";
import { Client } from "@osdk/client";
import { createEditBatch, Edits } from "@osdk/functions";

type OntologyEdit = Edits.Object<Employee> | Edits.Object<Ticket> | Edits.Link<Employee, "assignedTickets">;

export default function createNewTicketAndAssignToEmployee(client: Client): OntologyEdit[] {

    const batch = createEditBatch<OntologyEdit>(client);
    // ...
}
```

此批次用于跟踪函数中所做的所有编辑。

## 更新属性

### 对象属性

使用在创建的批次上可用的 `update` 方法来修改一个或多个对象属性：

```typescript
batch.update(employee, { lastName: newName });
```

如果你尚未将 `employee` 对象实例加载到内存中，你也可以通过引用该对象的 API 名称和主键来更新它：

```typescript
batch.update({ $apiName: "Employee", $primaryKey: 23 }, { lastName: newName });
```

在同一函数执行的后续过程中，对 `employee` 的 `lastName` 属性值的访问*不会*反映你在编辑批次上调用 `update` 时所做的更改。

有时，将一个对象类型实例的所有属性值复制到另一个实例会很有用。以下示例将 `employee2` 的属性值赋给 `employee1`：

```typescript
batch.update(employee1, employee2);
```

请注意，现有对象的主键属性值无法更新。

### 接口属性

你可以使用 `update` 方法通过本体接口修改对象的接口属性。在下面的示例中，`person` 的类型是本体接口，但底层实例是一个实现了 `Person` 接口的对象。

`update` 方法对于对象类型和接口都接受两个参数。对于接口，它接受将要修改的接口以及要修改的接口属性。

```typescript
batch.update(person, { firstName: newFirstName });
```

请注意，由底层对象的主键属性实现的接口属性无法更新。

## 更新链接

对于多对多链接，在创建的批次上提供了 `link` 和 `unlink` 方法，用于添加或移除对象之间的链接。

```typescript
// Assign an employee to an office.
batch.link(employee, "office", office);

// Unassign an office from an employee.
batch.unlink(employee, "office", office);
```

对于一对一和一对多链接，使用在创建的批次上可用的 `update` 方法来修改源对象的外键属性。下面的示例说明了一对多链接。一名员工可以有多个工单，但每个工单只能有一名员工。

```typescript
// Assign a ticket to an employee.
batch.update({ $apiName: "Ticket", $primaryKey: 13 }, { assignedEmployeeId: 52 });

// Unassign a ticket.
batch.update({ $apiName: "Ticket", $primaryKey: 13 }, { assignedEmployeeId: undefined });
```

与更新属性一样，如果你之前没有加载对象类型的具体实例，你也可以使用 API 名称和主键来引用链接的任一侧。

```typescript
// Assign a ticket to an employee.
batch.link({ $apiName: "Employee", $primaryKey: 23 }, "assignedTickets", { $apiName: "Ticket", $primaryKey: 12 });

// Unassign a ticket from an employee.
batch.unlink({ $apiName: "Employee", $primaryKey: 23 }, "assignedTickets", { $apiName: "Ticket", $primaryKey: 12 });
```

## 创建对象

### 对象

你可以使用编辑批次上的 `create` 方法创建新对象。创建新对象时，你必须为其主键指定一个值，并可以选择初始化任何其他属性。

在此示例中，我们创建一个具有给定 ID 的新 `Ticket` 对象，设置其 `dueDate` 属性，并通过修改 `assignedTickets` 链接将其分配给给定的 `Employee`。为了简化 `dueDate` 新值的计算，我们使用 `luxon` 库。

```typescript
import { Employee, Ticket } from "@ontology/sdk";
import { Client, Osdk } from "@osdk/client";
import { createEditBatch, Edits, Integer } from "@osdk/functions";
import { DateTime } from "luxon";

type OntologyEdit = Edits.Object<Employee> | Edits.Object<Ticket> | Edits.Link<Employee, "assignedTickets">;

export default function createNewTicketAndAssignToEmployee(
    client: Client,
    employee: Osdk.Instance<Employee>,
    ticketId: Integer,
): OntologyEdit[] {
    const batch = createEditBatch<OntologyEdit>(client);

    batch.create(Ticket, {
        ticketId,
        dueDate: DateTime.now().plus({ days: 7 }).toFormat('yyyy-MM-dd'),
    });

    // The new ticket does not exist in the Ontology as a concrete instance, but we can link it
    // by referencing its API name and primary key.
    batch.link(employee, "assignedTickets", { $apiName: "Ticket", $primaryKey: ticketId });

    return batch.getEdits();
}
```

### 接口

要通过接口创建新的对象实例，可以调用 `create` 方法并指定一个接口、底层对象类型以及一组接口属性。所提供的接口属性中必须有一个由底层对象类型的主键属性实现。

```typescript
editBatch.create(Person, {
    $objectType: "Employee",
    firstName: "John",
    lastName: "Doe",
});
```

## 删除对象

### 对象

你可以通过在编辑批次上调用 `delete` 方法来删除对象。

在此示例中，我们删除分配给给定员工的所有工单：

```typescript
for await (const ticket of employee.$link.assignedTickets.asyncIter()) {
    batch.delete(ticket);
}
```

也可以使用主键而不是实例来删除对象：

```typescript
batch.delete({ $apiName: "Ticket", $primaryKey: 12 });
```

### 接口

你可以通过接口调用 `delete` 方法来删除对象。

```typescript
batch.delete(person);
```

## 对结构体属性进行编辑

对象类型和接口类型的本体结构体属性都可以使用 TypeScript v2 函数进行编辑。TypeScript v2 中的[结构体类型](https://www.palantir.com/docs/foundry/functions/types-reference/#structcustom-type)使用 TypeScript 接口定义。函数中的结构体类型可用于编辑本体结构体属性，只要它们包含与结构体属性相同的字段，且字段名与本体结构体属性字段的 API 名称匹配即可。

```typescript
interface Address {
    street: string,
    city: string,
    state: string,
    country: string,
    zipcode: string,
}

export default function updateEmployeeAddress(
    client: Client,
    employee: Osdk.Instance<Employee>,
    newAddress: Address
): OntologyEdit[] {
    const batch = createEditBatch<OntologyEdit>(client);
    batch.update(employee, { address: newAddress });
    return batch.getEdits();
}
```

---

*原文：[Ontology edits](https://www.palantir.com/docs/foundry/functions/typescript-v2-ontology-edits/)*
