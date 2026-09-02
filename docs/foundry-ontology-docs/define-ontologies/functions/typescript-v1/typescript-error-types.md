# 错误类型（Error types）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/typescript-error-types/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

> **⚠️ 注意**
>
> 以下文档专门针对 TypeScript v1 函数。若需要更[强大的能力](https://www.palantir.com/docs/foundry/functions/language-feature-support/#typescript-v1-vs-typescript-v2)，包括对 Ontology SDK 和可配置资源请求的支持，我们建议[迁移到 TypeScript v2](https://www.palantir.com/docs/foundry/functions/typescript-v2-migration/)。

除了在 TypeScript 函数上声明[输出类型](https://www.palantir.com/docs/foundry/functions/types-reference/)之外，你还可以声明错误类型。这对于在查询上下文中传播和处理错误特别有用。

## 定义错误类型

你可以使用从 `@foundry/functions-api` 导出的 `FunctionsError` 类型来定义错误类型。它接受两个类型参数：一个字符串名称和一个可选类型（默认为空对象）。任何对函数[有效的输出类型](https://www.palantir.com/docs/foundry/functions/types-reference/)都可以用作错误类型，包括对象和对象集。

你可以将多个 `FunctionsError` 类型联合在一起，为你的函数定义一组可能的错误。例如，你可以为一个获取员工团队成员的函数定义以下错误类型：

```typescript
import { FunctionsError } from "@foundry/functions-api";
import { Employee } from "@foundry/ontology-api";

type GetTeammatesError =
    | FunctionsError<"EmployeeNotFoundForId", string>
    | FunctionsError<"MultipleEmployeesFoundForId", { employees: Employee[], employeeId: string }>
```

## 在函数上声明错误类型

要在 TypeScript 函数上声明错误类型，你可以使用从 `@foundry/functions-api` 导出的 `FunctionsResult` 类型。它接受两个类型参数：一个输出类型和一个错误类型。

使用上一节中的 `GetTeammatesError` 示例，你可以像这样在函数上声明错误类型：

```typescript
import { Function, FunctionsError, FunctionsResult } from "@foundry/functions-api";
import { Employee } from "@foundry/ontology-api";

type GetTeammatesError =
    | FunctionsError<"EmployeeNotFoundForId", string>
    | FunctionsError<"MultipleEmployeesFoundForId", { employees: Employee[], employeeId: string }>

@Function()
public getTeammates(employeeId: string): FunctionsResult<Employee[], GetTeammatesError> {
    ...
}
```

请注意，默认情况下，`FunctionsResult` 类型包含一些可由 TypeScript 函数运行时基础设施返回的错误：

- **`FunctionsTypeScriptExecutorService:CpuTimeoutError`：** 当函数超过 CPU 时间限制时返回。
- **`FunctionsTypeScriptExecutorService:WallTimeoutError`：** 当函数超过挂钟时间（wall time）限制时返回。
- **`FunctionsTypeScriptExecutorService:OutOfMemoryError`：** 当函数超过内存限制时返回。
- **`FunctionsTypeScriptExecutorService:RuntimeError`：** 当函数遇到其他运行时错误时返回。

## 在函数中返回输出和错误

要返回输出和错误，你可以使用从 `@foundry/functions-api` 导出的 `FunctionsResult.ok` 和 `FunctionsResult.err` 方法：

- **`FunctionsResult.ok`：** 接受单个输出值作为参数。
- **`FunctionsResult.err`：** 接受错误名称和值作为参数。

使用上一节中的 `getTeammates` 示例，你可以像这样返回输出和错误：

```typescript
import { Function, FunctionsError, FunctionsResult } from "@foundry/functions-api";
import { Employee } from "@foundry/ontology-api";

type GetTeammatesError =
    | FunctionsError<"EmployeeNotFoundForId", string>
    | FunctionsError<"MultipleEmployeesFoundForId", { employees: Employee[], employeeId: string }>

@Function()
public getTeammates(employeeId: string): FunctionsResult<Employee[], GetTeammatesError> {
    const employees = await Objects.search().employee([employeeId]).allAsync();
    if (employees.length === 0) {
        return FunctionsResult.err("EmployeeNotFoundForId", employeeId);
    }

    if (employees.length > 1) {
        return FunctionsResult.err("MultipleEmployeesFoundForId", { employees, employeeId });
    }

    const employee = employees[0];
    const teammates = await employee.teammates.allAsync();
    return FunctionsResult.ok(teammates);
}
```

## 处理来自查询的错误

当带有错误类型的函数[从另一个仓库作为查询被调用](https://www.palantir.com/docs/foundry/functions/query-functions/#call-a-query-function)时，它会返回一个 `Result` 类型的响应，该响应可能是 `ok` 或 `err` 结果。

你可以使用从 `@foundry/functions-api` 导出的 `isOk` 或 `isErr` 类型守卫（type guard）来区分这两种可能的结果。例如，调用上一节中的 `getTeammates` 示例（假设你还[将其发布为查询](https://www.palantir.com/docs/foundry/functions/query-functions/#query-decorator)），并像这样处理响应：

```typescript
import { Function, isOk } from "@foundry/function-api";
import { getTeammates } from "@foundry/ontology-api/queries";

@Function()
public async myFunction(employeeId: string): Promise<string> {
    const result = await getTeammates({ employeeId });

    if (isOk(result)) {
        const teammates = result.value;
        // Do something with "teammates" here
        ...
    }

    // You can inspect the "name" field to case on each error by name and use the "value" field to get the error value
    switch (result.error.name) {
        case "EmployeeNotFoundForId": ...
        case "MultipleEmployeesFoundForId": ...
        case "FunctionsTypescriptExecutorService:OutOfMemoryError": ...
        ...
    }
}
```

---

*原文：[Error types](https://www.palantir.com/docs/foundry/functions/typescript-error-types/)*
