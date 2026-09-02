# 本体编辑（Ontology edits）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/api-ontology-edits/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

> **⚠️ 注意**
>
> 以下文档专门针对 TypeScript v1 函数。有关本页的 TypeScript v2 版本，请参阅[本体编辑](https://www.palantir.com/docs/foundry/functions/typescript-v2-ontology-edits/)。如需更[强大的能力](https://www.palantir.com/docs/foundry/functions/language-feature-support/#typescript-v1-vs-typescript-v2)，包括对 Ontology SDK 的支持和可配置的资源请求，我们建议[迁移到 TypeScript v2](https://www.palantir.com/docs/foundry/functions/typescript-v2-migration/)。

除了编写基于本体返回派生值的函数外，你还可以编写编辑本体中对象属性和对象间链接的函数。本页记录了函数中可供你使用的对象编辑 API。有关编辑函数工作原理的更多细节，请参阅[概览页](https://www.palantir.com/docs/foundry/functions/edits-overview/)。

要真正在运营场景中使用，**本体编辑函数必须配置为动作**，即[函数支撑的动作](https://www.palantir.com/docs/foundry/action-types/function-actions-overview/)。以这种方式配置动作后，你可以提供额外的元数据、配置权限，并在各种运营界面中访问该动作。如[文档](https://www.palantir.com/docs/foundry/functions/edits-overview/#when-edits-are-applied)中所述，在动作之外运行编辑函数不会实际修改任何对象数据。

> **⚠️ 注意**
>
> 编辑对象后搜索对象可能会返回意外结果。详情请参阅[注意事项一节](https://www.palantir.com/docs/foundry/functions/edits-overview/#edits-and-object-search)。

## 声明编辑函数

编辑本体的函数必须：

- 使用从 `@foundry/functions-api` 导入的 `@OntologyEditFunction()` 装饰器进行装饰
- 使用从 `@foundry/functions-api` 导入的 `@Edits([object type])` 装饰器进行装饰，以指定将要编辑的对象类型
- 具有显式的 `void` 返回类型

## 更新属性

你只需通过重新赋值对象的属性值即可编辑属性。例如：

```typescript
employee.lastName = newName;
```

如果你在同一次函数执行中稍后访问 `lastName` 属性值，将返回你刚刚设置的新值。

对象上的[数组属性](https://www.palantir.com/docs/foundry/functions/api-objects-links/#array-properties)生成时使用 `ReadOnlyArray` 类型。要修改数组，请创建它的副本、修改副本，然后更新属性：

```typescript
// Copy to a new array
let arrayCopy = [...myObject.myArrayProperty];
// Now you can modify the copied array
arrayCopy.push(newItem);
// Then overwrite the property value
myObject.myArrayProperty = arrayCopy;
```

请注意，你无法更新现有对象的主键属性值。

## 更新链接

`SingleLink` 和 `MultiLink` 接口具有多种可用于更新链接的方法：

```typescript
// Set an Employee's supervisor
employee.supervisor.set(newSupervisor);

// Clear an Employee's supervisor
employee.supervisor.clear();

// Add a new report to the given employee
employee.reports.add(newReport);

// Remove an old report associated with the given employee
employee.reports.remove(oldReport);
```

与更新属性一样，在链接更新之后访问它们会反映你所做的更新。

## 创建对象

你可以使用从 `@foundry/ontology-api` 获得的 `Objects.create()` 接口创建新对象。创建新对象时，你必须为其主键指定一个值。

在本例中，我们使用给定 ID 创建一个新的 Ticket 对象，设置其 `dueDate` 属性，并将其分配给给定的 Employee（通过修改 `assignedTickets` 链接）。

```typescript
import { OntologyEditFunction, Edits } from "@foundry/functions-api";
import { Employee, Objects, Tickets } from "@foundry/ontology-api";

export class TicketActionFunctions {
    @Edits(Employee, Tickets)
    @OntologyEditFunction()
    public createNewTicketAndAssignToEmployee(employee: Employee, ticketId: Integer): void {
        const newTicket = Objects.create().ticket(ticketId);

        newTicket.dueDate = LocalDate.now().plusDays(7);

        employee.assignedTickets.add(newTicket);
    }
}
```

## 删除对象

你可以通过调用 `.delete()` 方法来删除对象。

在本例中，我们删除分配给给定员工的所有 Ticket。

```typescript
const tickets = employee.tickets.all();
tickets.forEach(ticket => ticket.delete());
```

## 编辑的捕获机制

执行本体编辑函数时，对对象的所有更新都会被函数基础设施捕获，并在函数执行结束时返回。这包括通过 `Objects.create()` API 创建的新对象、所有属性更新以及对象删除。

编辑会被智能合并，以便在动作中应用最精简的编辑集。例如，如果你创建一个新对象然后更新其属性，将返回单个包含属性更新的 **Create Object（创建对象）** 编辑。类似地，更新现有对象的多个属性将返回单个包含所有属性编辑的 **Update Object（更新对象）** 编辑。删除对象会抹去删除之前完成的任何其他属性编辑。整个函数必须成功执行，才能生成编辑列表，并将其传递给执行原子事务的动作服务。

捕获的本体编辑以列表形式从函数执行中返回，这就是本体编辑函数的返回类型必须为 `void` 或 `Promise<void>` 的原因。它们被执行时，函数真正的返回类型是本体编辑列表，因此不可能同时返回另一个值。

在单次函数执行的整个生命周期中，编辑都捕获在单个编辑存储中。这意味着可以调用那些创建、更新或删除对象的辅助函数，即使这些辅助函数并未作为本体编辑函数发布。例如：

```typescript
export class HelperEditFunctions {
    @Edits(ObjectA, ObjectB)
    @OntologyEditFunction()
    public createAndLink(): void {
        const objectA = this.createObjectA();
        const objectB = this.createObjectB();
        objectA.linkToB.set(objectB);
    }

    /**
     * Even though these helper functions are not annotated with @OntologyEditFunction(),
     * they can create new objects for use in other edit functions.
     */
    private createObjectA(): ObjectA {
        const objectA = Objects.create().objectA(this.generateRandomId());
        objectA.prop1 = "example";
        objectA.prop2 = 42;
        return objectA;
    }

    private createObjectB(): ObjectB {
        const objectB = Objects.create().objectB(this.generateRandomId());
        objectB.prop1 = "another example";
        return objectB;
    }

    /* Generate your primary keys as needed. For example,
    import { Uuid } from "@foundry/functions-utils";
    private generateRandomId(){
       return Uuid.random();
    }
    */
}
```

## 检索已编辑的值

在函数中完成编辑后，当你读取这些值时，函数基础设施将返回已编辑的值。例如，设置对象的属性然后检索它将返回新值：

```
airplane.departureTime = newDepartureTime;
console.log(airplane.departureTime); // Will log newDepartureTime
```

删除对象会将其从搜索结果中移除，并阻止访问其属性。

## @Edits 装饰器

动作可能需要溯源信息（provenance information）来强制执行其权限。为了向动作提供这些信息，你可以使用 `@Edits` 装饰器，并指定函数为其返回编辑的对象类型。

使用 `@Edits` 装饰器时请考虑以下事项：

- 编辑属性时，应声明被编辑对象的类型。
- 编辑一对一或一对多链接时，应声明具有外键属性的对象的类型。
- 编辑连接表（join table）链接时，应同时声明源对象类型和目标对象类型。

> **⚠️ 注意**
>
> 函数会对你的代码执行静态分析以自动检测引用的对象类型。但是，静态分析*可能无法*正确检测到引用。我们强烈建议你始终使用 `@Edits` 装饰器来提供有关所引用对象类型的溯源信息。

对于以下示例，对象类型 `Employee` 和 `Aircraft` 被一个函数编辑：

```typescript
import { OntologyEditFunction } from "@foundry/functions-api";
import { Employee, Aircraft, Objects } from "@foundry/ontology-api";

export class MyOntologyEditFunction {
    @Edits(Aircraft, Employee)
    @OntologyEditFunction()
    public myFunction(): void {
        const x = Objects.search().aircraft().all()[0];
        x.businessCapacity = 3;
        const y = Objects.search().employee().all()[0];
        y.department = "HR";
    }
}
```

如果你通过 `Objects.search()` API，使用编辑未更改的标识符（例如主键）检索（或物化）之前编辑过的对象，将返回已编辑的值。这是因为函数基础设施在对象被物化时会将你待处理的编辑应用到该对象上。这与基于你编辑的值进行筛选、环绕搜索或聚合不同——后者在函数执行完成之前不会反映该编辑。详情请参阅[编辑与对象搜索](https://www.palantir.com/docs/foundry/functions/edits-overview/#edits-and-object-search)。

```typescript
import { OntologyEditFunction } from "@foundry/functions-api";
import { Employee, Objects } from "@foundry/ontology-api";

export class CaveatEditFunctions {
    @Edits(Employee)
    @OntologyEditFunction()
    public async editAndSearch(): Promise<void> {
        const employeeOne = Objects.search().employee().filter(e => e.id.exactMatch(1)).all()[0];
        employeeOne.name = "Bob";

        // Retrieve the already edited object
        const employeeOneAgain = Objects.search().employee().filter(e => e.id.exactMatch(1)).all()[0];
        console.log(employeeOneAgain.name); // Prints "Bob"
    }
}
```

---

*原文：[Ontology edits](https://www.palantir.com/docs/foundry/functions/api-ontology-edits/)*
