# 桩对象搜索与聚合（Stub object searches and aggregations）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/unit-test-object-searches/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

在编写单元测试时，你可能希望为对象集搜索或对象聚合创建预设答案（也称为“桩”，即 stub），以规定你的代码在单元测试中发起调用时得到的响应。你需要从 `"@foundry/functions-testing-lib"` 导入 `{ whenObjectSet }` 才能使用桩。

#### 测试对象集上的筛选

```typescript
import { Objects } from "@foundry/ontology-api";

const objectSet = Objects.search().objectType();

expect(myFunctions.filterObjectSet(objectSet))
        .toEqual(objectSet.filter(s => s.prop.range().gte(0)))
```

#### 使用桩测试对象属性上的聚合

你可以使用桩来定义对聚合调用的响应。

```typescript
import { whenObjectSet } from "@foundry/functions-testing-lib"

whenObjectSet(Objects.search().objectType().sum(s => s.property)).thenReturn(55);
```

这意味着每当运行 `Objects.search().objectType().sum(s => s.property))` 时，结果都将是 55。

#### 使用桩测试对象

你也可以使用桩来定义对某些对象搜索的响应。

```typescript
import { whenObjectSet } from "@foundry/functions-testing-lib";

whenObjectSet(Objects.search().objectType().orderBy().takeAsync(10)).thenReturn([employeeObj])
await expect(myFunctions.aggregateSum(objectSet)).resolves.toEqual(65);
```

这意味着每当运行这个特定的对象搜索聚合时，属性求和结果都将解析为 65。

#### 使用桩测试不同的对象集

你可以通过重载搜索构造函数来模拟多个特定的对象集搜索。你必须为每个对象提供一个 `rid` 属性。

```typescript
import { whenObjectSet } from "@foundry/functions-testing-lib";

const objA = Objects.create().objectType('a');
const objB = Objects.create().objectType('b');

objA.rid = 'ridA';
objB.rid = 'ridB';

whenObjectSet(Objects.search().ObjType([objA]).all()).thenReturn([objA]);
whenObjectSet(Objects.search().ObjType([objB, objB]).all()).thenReturn([objA, objB]);
```

#### 使用桩测试链接遍历

你可以像对任何其他对象集搜索一样对 `searchAround` 遍历进行打桩，包括当你从单个对象出发并通过搜索构造函数对其进行转换时也是如此。遍历方法名由[链接类型字段名](https://www.palantir.com/docs/foundry/functions/api-objects-links/#link-types)生成，并且你必须为传递给搜索构造函数的每个对象提供一个 `rid` 属性。

```typescript
import { whenObjectSet } from "@foundry/functions-testing-lib";

const objA = Objects.create().objectType('a');
const objB = Objects.create().objectType('b');

objA.rid = 'ridA';

whenObjectSet(Objects.search().ObjType([objA]).searchAroundLinkField().all()).thenReturn([objB]);
```

有关在桩对象上设置链接状态的示例，请参阅[验证本体编辑](https://www.palantir.com/docs/foundry/functions/unit-test-ontology-edits/#verify-link-creation-to-an-object)。

---

*原文：[Stub object searches and aggregations](https://www.palantir.com/docs/foundry/functions/unit-test-object-searches/)*
