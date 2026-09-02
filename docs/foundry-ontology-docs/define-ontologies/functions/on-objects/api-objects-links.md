# API：对象与链接（API: Objects and links）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/api-objects-links/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

每个导入到你项目中的[对象类型](https://www.palantir.com/docs/foundry/object-link-types/object-types-overview/)都会被转换为 TypeScript API，让你可以轻松访问和操作 Foundry 中可用的对象。

### 属性

每个对象类型的[属性](https://www.palantir.com/docs/foundry/object-link-types/properties-overview/)都会转换为该对象类型所生成 TypeScript 接口上的字段。生成的字段名使用本体中指定的 API 名称（API Name）。

你可以使用简单的点表示法访问每个属性的字段：

```typescript
const firstName = employee.firstName;
```

请注意，由于属性可能没有设置具体值，访问属性值时返回的类型可能是 `undefined`。除非你显式处理 `undefined` 情况，否则 TypeScript 编译器会报错。有关这一点的更多详情，请参阅[本指南](https://www.palantir.com/docs/foundry/functions/undefined-values/)。

#### 数组属性

对象类型上的数组属性会被转换为 `ReadOnlyArray` 类型。这是为了让[编辑](https://www.palantir.com/docs/foundry/functions/api-ontology-edits/)数组属性的语义清晰——修改数组属性值的唯一方式是用一个全新的数组值来更新它。

如果你想操作数组属性的值，请复制一份：

```typescript
// Copy to a new array
let arrayCopy = [...myObject.myArrayProperty];
// Now you can modify the copied array
arrayCopy.push(newItem);
```

### 链接类型

对象类型之间的[链接类型](https://www.palantir.com/docs/foundry/object-link-types/link-types-overview/)也会被转换为每个对象类型的 TypeScript 接口上的字段。要遍历链接，访问该字段，然后调用用于加载对象的方法之一。链接类型字段名使用本体中指定的 API 名称生成。

Foundry 本体支持定义一对一、一对多和多对多链接类型。访问链接的 `1` 端时，生成的字段为 `SingleLink` 类型。你可以使用 `get()` 或 `getAsync()` 方法访问链接的对象：

```typescript
const manager = employee.manager.get();
```

与属性一样，当你遍历一对一或多对一链接时，如果没有链接对象，返回值可能是 `undefined`。请按照[指南](https://www.palantir.com/docs/foundry/functions/undefined-values/)处理这些链接的 `undefined` 值。

访问链接的 `many` 端时，生成的字段为 `MultiLink` 类型。你可以使用 `all()` 或 `allAsync()` 方法访问链接对象的数组。如果没有链接对象，这些方法将返回空数组。

```typescript
const employees = employee.reports.all();
```

遍历链接的开销可能很大，因为这需要在后端加载对象之间的链接关系。有关如何更高效地执行链接遍历的详情，请参阅[本节](https://www.palantir.com/docs/foundry/functions/optimize-performance/#optimizing-link-traversals)。

调用 `.all()` 或 `.allAsync()` 返回的链接对象数组是 `ReadOnlyArray`。如果你想修改该数组，请先复制一份：

```typescript
let copiedEmployees = [...employee.reports.all()];
```

你可以将链接作为 `ObjectSet` 遍历，以避免在内存中加载链接的对象实例。在本体中创建链接后，将在此类型的对象集上生成 API，用于“环绕搜索”到其他链接对象集。

```typescript
import { ObjectSet, Employee } from "@foundry/ontology-api";

// Assume you have an object set available:
// const employee_id = "123";
// const employeeObjectSet : ObjectSet<Employee> = Objects.search().employee().filter(exactMatch(employee_id));

const linkedObjs: ObjectSet<OtherObjectType> = employeeObjectSet.searchAroundToOtherObjectType();
```

如果你在单个对象实例上操作并从该实例出发进行环绕搜索，你将得到 `MultiLink<objectType>`。你无法将此 `MultiLink` 转换为 `ObjectSet`；你必须先将对象实例转换为对象集，才能转向（pivot）其他对象集。

```typescript
// Assuming:
// const employee: Employee

// MultiLink can be loaded in memory to process further.

const linkedObjs: MultiLink<objectType> = employee.reports

// Convert a sole object instance to an object set. This statement will take longer than an `employee().filter()` statement.
const employeeObjectSet : ObjectSet<Employee> = Objects.search().employee([employee])

// From there, you can use the above "searchAroundToOtherObjectType" to process only object sets.
```

### 本体元数据

函数通过提供对象和属性列表来访问可用的本体。本体元数据信息可以通过访问每个对象类型的常量类型获得。更多详情见下文各节。

#### 对象属性元数据

对象属性还包含类型元数据，提供对每个属性类型的编程访问。你可以将此功能用于高级工作流，例如识别给定类型的所有属性，或验证给定属性名是否具有特定类型。

例如，对于一个包含员工对象**类型**的本体，你可以按如下方式访问该对象**类型**的属性类型信息：

```typescript
import { Employee } from "@foundry/ontology-api";
...
const type = Employee.properties.firstName;
```

在本例中，如果 `firstName` 是 `Employee` 对象类型上的字符串属性，那么它的类型将是 `StringPropertyBaseType`。

以下属性类型可用：

- `BooleanPropertyBaseType`
- `BytePropertyBaseType`
- `DatePropertyBaseType`
- `FloatPropertyBaseType`
- `TimestampPropertyBaseType`
- `ShortPropertyBaseType`
- `GeohashPropertyBaseType`（用于 `geopoint` 属性，此前名为 `geohash` 属性。）
- `DecimalPropertyBaseType`
- `StringPropertyBaseType`
- `LongPropertyBaseType`
- `IntegerPropertyBaseType`
- `DoublePropertyBaseType`
- `ArrayPropertyBaseType`
- `VectorPropertyBaseType`

---

*原文：[API: Objects and links](https://www.palantir.com/docs/foundry/functions/api-objects-links/)*
