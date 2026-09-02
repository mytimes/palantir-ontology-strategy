# 对象标识符（Object identifiers）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/object-identifiers/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

在 Foundry 中，对象的身份有几种不同的表示方式，理解这些不同的表示方式对于在函数中编写正确的代码非常重要。本节将介绍标识对象的各种方式，以及它们对你的代码有何影响。

## 标识符的类型

### 对象 RID

“RID” 指的是[资源标识符（Resource Identifier）↗](https://github.com/palantir/resource-identifier)，这是 Palantir 用于标识实体的开源规范。本体对象在创建时会被分配一个 RID，无论是通过索引支撑数据集还是作为动作的一部分。

在函数中，每个[本体对象](https://www.palantir.com/docs/foundry/functions/api-objects-links/)都有一个类型为 `string | undefined` 的 `rid` 字段。RID 可能为 undefined 的原因是，可以在函数中使用[对象创建](https://www.palantir.com/docs/foundry/functions/api-ontology-edits/#creating-objects) API 创建新对象。新创建的对象的 `rid` 值始终为 `undefined`，而现有对象的 `rid` 始终有定义。

### 主键

对象也可以通过其对象类型和主键来唯一标识。主键是由唯一的 `propertyId` 和值组成的键值对。例如，Employee 对象类型可以通过一个名为 `employeeId` 的 `string` 属性来唯一标识。

所有本体对象始终都有 `typeId` 和 `primaryKey` 字段，包括新创建的对象。这是因为在创建新对象时必须提供主键。

## 对代码的影响

### 检查相等性

在函数内部，每个本体对象都使用一个 [JavaScript 对象 ↗](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object) 来表示。一个本体对象有可能被表示为多个 JavaScript 对象。例如，如果你多次从[对象搜索](https://www.palantir.com/docs/foundry/functions/api-object-sets/)加载同一个本体对象，或者除了将对象作为参数传入之外还从对象搜索中加载该对象，就会发生这种情况：

```typescript
public myFunction(employee: Employee): void {
    const employee2 = Objects.search().employee()
        .filter(e => e.id.exactMatch(employee.id))
        .all()[0];
    console.log(employee == employee2); // false
    console.log(employee === employee2); // false
    console.log(employee.id === employee2.id); // true
}
```

尽管在上例中 `employee` 和 `employee2` 指向同一个概念上的本体对象，但使用 `==` 和 `===` 运算符比较它们会返回 `false`，因为这两个变量引用的是两个不同的 JavaScript 对象。简单地比较 `rid` 字段可能会有问题，因为新创建对象的 `rid` 为 `undefined`。

因此，比较两个本体对象是否相等的最佳方式是比较 `typeId` 和 `primaryKey`：

```typescript
function isEqual(o1: OntologyObject, o2: OntologyObject) {
    return o1.typeId === o2.typeId
        && JSON.stringify(o1.primaryKey) == JSON.stringify(o2.primaryKey);
}
```

### 对象映射

存储从对象到某个值的映射通常很有用。例如，你可能希望遍历一个对象数组并存储值，以便更高效地查找。

由于上述相等性检查问题，你不能简单地使用 JavaScript Map 来为每个对象存储值。相反，你可以使用 [FunctionsMap](https://www.palantir.com/docs/foundry/functions/types-reference/#collection-types)，它专门设计用于支持以 OntologyObject 作为键。

---

*原文：[Object identifiers](https://www.palantir.com/docs/foundry/functions/object-identifiers/)*
