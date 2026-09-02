# 处理 undefined 值（Handle undefined values）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/undefined-values/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

> **⚠️ 注意**
>
> 以下文档专门针对 TypeScript v1 函数。若需要更[强大的能力](https://www.palantir.com/docs/foundry/functions/language-feature-support/#typescript-v1-vs-typescript-v2)，包括对 Ontology SDK 和可配置资源请求的支持，我们建议[迁移到 TypeScript v2](https://www.palantir.com/docs/foundry/functions/typescript-v2-migration/)。

以下是处理访问属性或链接时可能返回的 `undefined` 值的两种实用模式。

### 显式检查

```typescript
@Function()
public getFullName(employee: Employee): string {
    if (!(employee.firstName && employee.lastName)) {
        throw new UserFacingError("Cannot derive full name because either first or last name is undefined.");
    }
    return employee.firstName + " " + employee.lastName;
}
```

通过检查 `firstName` 和 `lastName` 字段都已定义，TypeScript 编译器就知道带有 `return` 语句的最后一行可以正确编译。这种方法的好处是类型检查更加明确，并且在存在 `undefined` 值的情况下，你可以抛出关于出错原因的更明确的错误。

### 非空断言操作符

你可以使用 TypeScript [非空断言操作符 ↗](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-0.html#non-null-assertion-operator)（`!`）来忽略 `undefined` 的情况。

```typescript
@Function()
public getFullName(employee: Employee): string {
    return employee.firstName! + " " + employee.lastName!;
}
```

这种方法只是覆盖了 TypeScript 编译器，并断言你正在访问的字段是已定义的。虽然这样代码更简洁，但当某个字段实际为 `undefined` 时，可能会导致晦涩难懂的错误。我们建议在可能的情况下进行显式检查。

---

*原文：[Handle undefined values](https://www.palantir.com/docs/foundry/functions/undefined-values/)*
