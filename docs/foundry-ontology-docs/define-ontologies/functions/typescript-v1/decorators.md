# 装饰器（Decorators）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/decorators/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

> **⚠️ 注意**
>
> 以下文档专门针对 TypeScript v1 函数。如需更[强大的能力](https://www.palantir.com/docs/foundry/functions/language-feature-support/#typescript-v1-vs-typescript-v2)，包括对 Ontology SDK 的支持和可配置的资源请求，我们建议[迁移到 TypeScript v2](https://www.palantir.com/docs/foundry/functions/typescript-v2-migration/)。

[TypeScript ↗](https://www.typescriptlang.org/docs/handbook/basic-types.html) 函数声明为 [TypeScript 类 ↗](https://www.typescriptlang.org/docs/handbook/classes.html) 的方法。函数要被发现和发布，有以下几项要求：

- 方法必须是 `public` 的
- 方法所属的类必须从 `functions-typescript/src/index.ts` 文件导出
- 方法必须使用从 `@foundry/functions-api` 包导入的以下装饰器之一进行装饰：
  - `@Function()` 用于通用函数。
  - [`@OntologyEditFunction()`](https://www.palantir.com/docs/foundry/functions/api-ontology-edits/) 用于支撑动作的函数。
    - 使用 [`@OntologyEditFunction()`](https://www.palantir.com/docs/foundry/functions/api-ontology-edits/) 方法时，可以选择使用 `@Edits([object type])` 装饰器指定对象溯源信息。
    - 如果缺少 `@Edits([object type])` 装饰器，将通过代码静态分析尽最大努力推断对象溯源信息。
  - [`@Query({ apiName: "userDefinedAPIName"})`](https://www.palantir.com/docs/foundry/functions/query-functions/) 用于你希望通过 [Foundry API](https://www.palantir.com/docs/foundry/api/general/overview/introduction/) 执行的只读查询。请注意，此装饰器不应与 `@Function` 装饰器叠加使用；它应单独使用。

以下是以这种方式正确导出的函数示例：

```typescript
import { Function, OntologyEditFunction, Query, Integer, Edits } from "@foundry/functions-api";
import { Employee, Objects, ObjectSet } from "@foundry/ontology-api";

export class MyUsefulFunctions {
    @Function()
    public incrementNumber(x: Integer): Integer {
        return x + 1;
    }

    @Edits(Employee)
    @OntologyEditFunction()
    public updateName(employee: Employee, newName: string): void {
        employee.firstName = newName;
    }

    @Query({ apiName: "getEmployeesByName" })
    public async getEmployeesByName(name: string): Promise<ObjectSet<Employee>> {
        return Objects.search().employee().filter(employee => employee.firstName.exactMatch(name));
    }
}
```

任何私有的、或未使用相关装饰器装饰的方法都不会发布到函数注册中心。这允许用户创建辅助函数和工具函数以供复用或组织代码。

> **ℹ️ 提示：重新发布（Republishing）**
>
> 请注意，TypeScript 代码仓库中的每个函数都由其类名和方法名唯一标识——如果你更改类名或方法名，该函数将以新的标识符发布。

---

*原文：[Decorators](https://www.palantir.com/docs/foundry/functions/decorators/)*
