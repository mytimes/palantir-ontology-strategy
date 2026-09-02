# 类型参考（Types reference）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/types-reference/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

为了发布到注册中心，TypeScript 函数必须在所有输入参数上具有显式类型标注，并指定显式返回类型。以下是当前支持的函数注册中心类型及其对应语言类型的完整列表。

> **ℹ️ 提示**
>
> 在 Pipeline Builder 中将 Python 函数用作用户定义函数（UDF）？下面的标量类型（如 `str` 或 `int`）就是 UDF 返回的值，因为函数对每一行运行一次，其返回值会成为一个新列。你不需要返回 DataFrame。详情请参阅 [Python 函数在 Pipeline Builder 中如何处理数据](https://www.palantir.com/docs/foundry/functions/python-functions-builder/#how-python-functions-process-data)。

| 函数注册中心类型 | TypeScript v1 类型 | TypeScript v2 类型 | Python 类型 |  |
| --- | --- | --- | --- | --- |
| 附件（Attachment） | `Attachment` | `Attachment` | `Attachment` | [示例](#附件attachment) |
| 布尔（Boolean） | `boolean` | `boolean` | `bool` | [示例](#布尔boolean) |
| 二进制（Binary） | 不支持 | 不支持 | `bytes` | [示例](#二进制binary) |
| 字节（Byte） | 不支持 | 不支持 | `int*` | [示例](#字节byte) |
| 分类标记（Classification marking） | `ClassificationMarking` | `ClassificationMarking` | `ClassificationMarking` | [示例](#分类标记classification-marking) |
| 日期（Date） | `LocalDate` | `DateISOString` | `datetime.date` | [示例](#日期date) |
| Decimal | 不支持 | 不支持 | `decimal.Decimal` | [示例](#decimal) |
| Double | `Double` | `Double` | `float*` | [示例](#double) |
| Float | `Float` | `Float` | `float` | [示例](#float) |
| GeoPoint | `GeoPoint` | `Point` | `GeoPoint` | [示例](#geopoint) |
| GeoShape | `GeoShape` | `Geometry` | `GeoShape` | [示例](#geoshape) |
| 用户组（Group） | `Group` | `GroupId` | `GroupId` | [示例](#用户组group) |
| Integer | `Integer` | `Integer` | `int` | [示例](#integer) |
| 接口（Interface） | 不支持 | `Osdk.Instance<MyInterface>` | 不支持 | [示例](#接口interface) |
| 接口对象集（Interface object set） | 不支持 | `ObjectSet<MyInterface>` | 不支持 | [示例](#接口对象集interface-object-set) |
| 列表（List） | `T[]` 或 `Array<T>` | `T[]` 或 `Array<T>` | `list[T]` | [示例](#列表list) |
| Long | `Long` | `Long` | `int*` | [示例](#long) |
| 强制标记（Mandatory marking） | `MandatoryMarking` | `MandatoryMarking` | `MandatoryMarking` | [示例](#强制标记mandatory-marking) |
| 映射（Map） | `FunctionsMap<K, V>` | `Record<K, V>` | `dict[K, V]` | [示例](#映射map) |
| 媒体引用（Media reference） | `MediaItem` | `Media` | `Media` | [示例](#媒体media) |
| 通知（Notification） | `Notification` | `Notification` | `Notification` | [示例](#通知notification) |
| 对象（Object） | `MyObjectType` | `Osdk.Instance<MyObjectType>` | `MyObjectType` | [示例](#对象object) |
| 对象集（Object set） | `ObjectSet<MyObjectType>` | `ObjectSet<MyObjectType>` | `MyObjectTypeObjectSet` | [示例](#对象集object-set) |
| 本体编辑（Ontology edit） | `void` | `Edits` | `OntologyEdit` | [示例](#本体编辑ontology-edit) |
| 可选（Optional） | `T | undefined` | `T | undefined` | `typing.Optional` 或 `T | None` | [示例](#可选optional) |
| 主体（Principal） | `Principal` | `Principal` | `Principal` | [示例](#主体principal) |
| 范围（Range） | `IRange<T>` | `Range<T>` | `Range[T]` | [示例](#范围range) |
| 集合（Set） | `Set<T>` | 不支持 | `set[T]` | [示例](#集合set) |
| Short | 不支持 | 不支持 | `int*` | [示例](#short) |
| 字符串（String） | `string` | `string` | `str` | [示例](#字符串string) |
| 结构体/自定义类型（Struct/custom type） | `interface` | `interface` | `dataclasses.dataclass` | [示例](#结构体自定义类型structcustom-type) |
| 时间戳（Timestamp） | `Timestamp` | `TimestampISOString` | `datetime.datetime` | [示例](#时间戳timestamp) |
| 二维聚合（Two-dimensional aggregation） | `TwoDimensionalAggregation<K, V>` | `TwoDimensionalAggregation<K, V>` | `TwoDimensionalAggregation[K, V]` | [示例](#二维聚合two-dimensional-aggregation) |
| 三维聚合（Three-dimensional aggregation） | `ThreeDimensionalAggregation<K, S, V>` | `ThreeDimensionalAggregation<K, S, V>` | `ThreeDimensionalAggregation[K, S, V]` | [示例](#三维聚合three-dimensional-aggregation) |
| 用户（User） | `User` | `UserId` | `UserId` | [示例](#用户user) |

> **ℹ️ 提示**
>
> 虽然 `Integer` 和 `Long` 都对应 Python 的 `int` 类型，但在函数签名中直接标记为 `int` 的任何字段都将以 `Integer` 类型注册。因此，我们建议改用 API 中的 `Integer` 或 `Long` 类型来注册数值数据类型。类似的准则适用于 `Float` 和 `Double`；如果 Python 类型 `float` 直接出现在函数签名中，默认将注册为 `Float`。

## 标量类型

标量类型表示单个值，通常用于保存文本、数值或时间数据。

在 JavaScript 和 TypeScript 中，有一个 `number` 类型通常同时用于表示整数值和浮点值。为了提供进一步的类型校验和结构，对于 TypeScript v1 函数，我们仅支持从 `@foundry/functions-api` 包导出的数值别名；对于 TypeScript v2 函数，则仅支持从 `@osdk/functions` 包导出的数值别名。类似地，在 Python 函数中处理数值类型时，我们建议使用从 `functions.api` 模块导出的类型别名。

### 布尔（Boolean）

**TypeScript v1**

```typescript
import { Function, Integer } from "@foundry/functions-api";

export class MyFunctions {
    @Function()
    public isEven(num: Integer): boolean {
        return num % 2 === 0;
    }
}
```

**TypeScript v2**

```typescript
import { Integer } from "@osdk/functions";

function isEven(num: Integer): boolean {
    return num % 2 === 0;
}

export default isEven;
```

**Python**

```python
from functions.api import function, Integer

@function
def is_even(num: Integer) -> bool:
    return n % 2 == 0
```

### 字符串（String）

**TypeScript v1**

```typescript
import { Function } from "@foundry/functions-api";

export class MyFunctions {
    @Function()
    public greet(name: string): string {
        return `Hello, ${name}!`;
    }
}
```

**TypeScript v2**

```typescript
function greet(name: string): string {
    return `Hello, ${name}!`;
}

export default greet;
```

**Python**

```python
from functions.api import function

@function
def greet(name: str) -> str:
    return f"Hello, {name}!"
```

### Short

表示从 -32,768 到 32,767 的整数值。

在 Python 函数中，`Short` 类型是内置 `int` 类型的别名。

**Python**

```python
from functions.api import function, Short

@function
def increment(num: Short) -> Short:
    return num + 1
```

### Integer

表示从 -(2³¹) 到 (2³¹ - 1) 的整数值。

- 在 TypeScript v1 和 v2 函数中，`Integer` 类型都是内置 `number` 类型的别名。
- 在 Python 函数中，`Integer` 类型是内置 `int` 类型的别名。

**TypeScript v1**

```typescript
import { Function, Integer } from "@foundry/functions-api";

export class MyFunctions {
    @Function()
    public sum(a: Integer, b: Integer): Integer {
        return a + b;
    }
}
```

**TypeScript v2**

```typescript
import { Integer } from "@osdk/functions";

function sum(a: Integer, b: Integer): Integer {
    return a + b;
}

export default sum;
```

**Python**

```python
from functions.api import function, Integer

@function
def sum(a: Integer, b: Integer) -> Integer:
    return a + b
```

### Long

表示从 -(2⁵³ - 1) 到 (2⁵³ - 1) 的整数值。这些边界等同于 JavaScript 中的 `Number.MIN_SAFE_INTEGER` 和 `Number.MAX_SAFE_INTEGER`，以防止函数从浏览器上下文调用时丢失精度。

- 在 TypeScript v1 函数中，`Long` 类型是内置 `number` 类型的别名。在 TypeScript v2 函数中，`Long` 类型是内置 `string` 类型的别名。
- 在 Python 函数中，`Long` 类型是内置 `int` 类型的别名。

**TypeScript v1**

```typescript
import { Function, Long } from "@foundry/functions-api";

export class MyFunctions {
    @Function()
    public subtract(a: Long, b: Long): string {
        return (BigInt(a) - BigInt(b)).toString();
    }
}
```

**TypeScript v2**

```typescript
import { Long } from "@osdk/functions";

function subtract(a: Long, b: Long): string {
    return (BigInt(a) - BigInt(b)).toString();
}

export default subtract;
```

**Python**

```python
from functions.api import function, Long

@function
def subtract(a: Long, b: Long) -> str:
    return str(a - b)
```

### Float

表示 32 位浮点数。

- 在 TypeScript v1 和 v2 函数中，`Float` 类型都是内置 `number` 类型的别名。
- 在 Python 函数中，`Float` 类型是内置 `float` 类型的别名。

**TypeScript v1**

```typescript
import { Float, Function } from "@foundry/functions-api";

export class MyFunctions {
    @Function()
    public multiply(a: Float, b: Float): Float {
        return a * b;
    }
}
```

**TypeScript v2**

```typescript
import { Float } from "@osdk/functions";

function multiply(a: Float, b: Float): Float {
    return a * b;
}

export default multiply;
```

**Python**

```python
from functions.api import function, Float

@function
def multiply(a: Float, b: Float) -> Float:
    return a * b
```

### Double

表示 64 位浮点数。

- 在 TypeScript v1 和 v2 函数中，`Double` 类型都是内置 `number` 类型的别名。
- 在 Python 函数中，`Double` 类型是内置 `float` 类型的别名。

**TypeScript v1**

```typescript
import { Double, Function } from "@foundry/functions-api";

export class MyFunctions {
    @Function()
    public divide(a: Double, b: Double): Double {
        return a / b;
    }
}
```

**TypeScript v2**

```typescript
import { Double } from "@osdk/functions";

function divide(a: Double, b: Double): Double {
    return a / b;
}

export default divide;
```

**Python**

```python
from functions.api import function, Double

@function
def divide(a: Double, b: Double) -> Double:
    return a / b
```

### Decimal

**Python**

```python
from decimal import Decimal
from functions.api import function

@function
def return_pi() -> Decimal:
    return Decimal('3.1415926535')
```

### 日期（Date）

表示日历日期。

**TypeScript v1**

```typescript
import { Function, LocalDate } from "@foundry/functions-api";

export class MyFunctions {
    @Function()
    public returnDate(): LocalDate {
        return LocalDate.fromISOString("1999-10-17");
    }
}
```

**TypeScript v2**

```typescript
import { DateISOString } from "@osdk/functions";

function returnDate(): DateISOString {
    return "1999-10-17";
}

export default returnDate;
```

**Python**

```python
from datetime import date
from functions.api import function, Date

@function
def return_date() -> Date:
    return date.fromisoformat('1999-10-17')
```

### 时间戳（Timestamp）

表示一个时间瞬间。

**TypeScript v1**

```typescript
import { Function, Timestamp } from "@foundry/functions-api";

export class MyFunctions {
    @Function()
    public getCurrentTimestamp(): Timestamp {
        return Timestamp.now();
    }
}
```

**TypeScript v2**

```typescript
import { TimestampISOString } from "@osdk/functions";

function getCurrentTimestamp(): TimestampISOString {
    const now = new Date();
    return now.toISOString();
}

export default getCurrentTimestamp;
```

**Python**

```python
from datetime import datetime
from functions.api import function, Timestamp

@function
def get_current_timestamp() -> Timestamp:
    return datetime.now()
```

### 二进制（Binary）

在 Python 函数中，`Binary` 类型是内置 `bytes` 类型的别名。

**Python**

```python
from functions.api import function

@function
def encode_utf8(param: str) -> bytes:
    return param.encode('utf-8')
```

### 字节（Byte）

在 Python 函数中，`Byte` 类型是内置 `int` 类型的别名。

**Python**

```python
from functions.api import function, Byte

@function
def get_first_byte(param: str) -> Byte:
    if len(param) == 0:
        raise Exception("String length cannot be zero.")
    return param.encode('utf-8')[0]
```

### 强制标记（Mandatory marking）

标记是强制控制，它要求用户必须拥有特定标记才能访问数据，从而限制访问。

**TypeScript v1**

```typescript
import { OntologyEditFunction, MandatoryMarking } from "@foundry/functions-api";
import { Employee, Objects } from "@foundry/ontology-api";

export class MyFunctions {
    @Edits(Employee)
    @OntologyEditFunction()
    public async editMandatoryMarkings(markings: MandatoryMarking[]): Promise<void> {
        const employeeOne = Objects.search().employee().filter(e => e.id.exactMatch(1)).all()[0];
        employeeOne.markingsProperty = markings;
    }
}
```

**TypeScript v2**

```typescript
import { Client } from "@osdk/client";
import { Employee } from "@ontology/sdk";
import { Edits, createEditBatch, MandatoryMarking } from "@osdk/functions";

type OntologyEdit = Edits.Object<Employee>;

function editMandatoryMarkings(markings: MandatoryMarking[]): OntologyEdit[] {
    const batch = createEditBatch<OntologyEdit>(client);

    const employeeOne = await client(Employee).fetchOne(1);
    batch.update(employeeOne, { markingsProperty: markings });

    return batch.getEdits();
}

export default editMandatoryMarkings;
```

**Python**

```python
from foundry_sdk_runtime import Marking
from functions.api import function, MandatoryMarking, OntologyEdit
from ontology_sdk import FoundryClient
from ontology_sdk.ontology.objects import Employee

@function
def edit_mandatory_markings(markings: list[MandatoryMarking]) -> list[OntologyEdit]:
    ontology_edits = FoundryClient().ontology.edits()
    employee: Optional[Employee] = client.ontology.objects.Employee.get("primary_key")
    if employee is None:
        return []
    editable_employee = ontology_edits.objects.Employee.edit(employee)

    editable_employee.markings_property = [Marking(m) for m in markings]
    # Assigning type "list[MandatoryMarking]" also works, but gives an LSP warning:
    # editable_employee.markings_property = markings

    return ontology_edits.get_edits()
```

### 分类标记（Classification marking）

基于分类的访问控制（CBAC）是用于保护敏感政府信息的强制控制。它要求用户必须拥有特定分类标记才能访问信息，从而限制访问。

**TypeScript v1**

```typescript
import { OntologyEditFunction, ClassificationMarking } from "@foundry/functions-api";
import { Employee, Objects } from "@foundry/ontology-api";

export class MyFunctions {
    @Edits(Employee)
    @OntologyEditFunction()
    public async editClassificationMarkings(markings: ClassificationMarking[]): Promise<void> {
        const employeeOne = Objects.search().employee().filter(e => e.id.exactMatch(1)).all()[0];
        employeeOne.markingsProperty = markings;
    }
}
```

**TypeScript v2**

```typescript
import { Client } from "@osdk/client";
import { Employee } from "@ontology/sdk";
import { Edits, createEditBatch, ClassificationMarking } from "@osdk/functions";

type OntologyEdit = Edits.Object<Employee>;

function editClassificationMarkings(markings: ClassificationMarking[]): OntologyEdit[] {
    const batch = createEditBatch<OntologyEdit>(client);

    const employeeOne = await client(Employee).fetchOne(1);
    batch.update(employeeOne, { markingsProperty: markings });

    return batch.getEdits();
}

export default editClassificationMarkings;
```

**Python**

```python
from foundry_sdk_runtime import Marking
from functions.api import ClassificationMarking, function, OntologyEdit
from ontology_sdk import FoundryClient
from ontology_sdk.ontology.objects import Employee

@function
def edit_classification_markings(markings: list[ClassificationMarking]) -> list[OntologyEdit]:
    ontology_edits = FoundryClient().ontology.edits()
    employee: Optional[Employee] = client.ontology.objects.Employee.get("primary_key")
    if employee is None:
        return []
    editable_employee = ontology_edits.objects.Employee.edit(employee)

    editable_employee.markings_property = [Marking(m) for m in markings]
    # Assigning type "list[ClassificationMarking]" also works, but gives an LSP warning:
    # editable_employee.markings_property = markings

    return ontology_edits.get_edits()
```

## 集合类型

集合类型由其他类型参数化。例如，Array[String] 是字符串列表，Map[String, Integer] 是键为字符串、值为整数的字典。必须指定参数化类型，且该类型必须是另一个受支持的类型。Map 的键只能是标量类型或本体对象类型。

### 列表（List）

**TypeScript v1**

```typescript
import { Function, Integer } from "@foundry/functions-api";

export class MyFunctions {
    @Function()
    public filterForEvenIntegers(nums: Integer[]): Integer[] {
        return nums.filter(num => num % 2 === 0);
    }
}
```

**TypeScript v2**

```typescript
import { Integer } from "@osdk/functions";

function filterForEvenIntegers(nums: Integer[]): Integer[] {
    return nums.filter(num => num % 2 === 0);
}

export default filterForEvenIntegers;
```

**Python**

```python
from functions.api import function, Integer

@function
def filter_for_even_integers(nums: list[Integer]) -> list[Integer]:
    return [n for n in nums if n % 2 == 0]
```

### 映射（Map）

映射通常用于以标量类型为键，并访问关联的值，该值可以是任何其他函数注册中心类型。

**TypeScript v1**

```typescript
import { Function, FunctionsMap } from "@foundry/functions-api";

export class MyFunctions {
    @Function()
    public getMap(): FunctionsMap<string, string> {
        const myMap = new FunctionsMap<string, string>();

        myMap.set("Name", "Phil");
        myMap.set("Favorite Color", "Blue");

        return myMap;
    }
}
```

**TypeScript v2**

```typescript
function getMap(): Record<string, string> {
    const myMap: Record<string, string> = {};

    myMap["Name"] = "Phil";
    myMap["Favorite Color"] = "Blue";

    return myMap;
}

export default getMap;
```

**Python**

```python
from functions.api import function

@function
def get_map() -> dict[str, str]:
    my_map = {}

    my_map["Name"] = "Phil"
    my_map["Favorite Color"] = "Blue"

    return my_map
```

此外，映射支持以本体对象作为键。

**TypeScript v1**

```typescript
import { Function, FunctionsMap } from "@foundry/functions-api";
import { Airplane } from "@foundry/ontology-api";

export class MyFunctions {
    @Function()
    public getObjectMap(aircraft: Airplane[]): FunctionsMap<Airplane, Integer | undefined> {
        const myMap = new FunctionsMap<Airplane, Integer | undefined>();

        aircraft.forEach(obj => {
            myMap.set(obj, obj.capacity);
        });

        return myMap;
    }
}
```

**TypeScript v2**

```typescript
import { ObjectSpecifier, Osdk } from "@osdk/client";
import { Integer } from "@osdk/functions";
import { Airplane } from "@ontology/sdk";

function getObjectMap(aircraft: Osdk.Instance<Airplane>[]): Record<ObjectSpecifier<Airplane>, Integer | undefined> {
    const myMap: Record<ObjectSpecifier<Airplane>, Integer | undefined> = {};

    aircraft.forEach(obj => {
        myMap[obj.$objectSpecifier] = obj.capacity;
    });

    return myMap;
}

export default getObjectMap;
```

**Python**

```python
from functions.api import function, Integer
from ontology_sdk.ontology.objects import Airplane

@function
def get_object_map(aircraft: list[Airplane]) -> dict[Airplane, Integer | None]:
    my_map = {}

    for a in aircraft:
        my_map[a] = a.capacity

    return my_map
```

### 集合（Set）

**TypeScript v1**

```typescript
import { Function, Integer } from "@foundry/functions-api";

export class MyFunctions {
    @Function()
    public getSizeOfSet(mySet: Set<Integer>): Integer {
        return mySet.size;
    }
}
```

**Python**

```python
from functions.api import function, Integer

@function
def get_size_of_set(my_set: set[Integer]) -> Integer:
    return len(my_set)
```

### 可选（Optional）

- 在 TypeScript 函数中，可选参数声明为 `varName?: T` 或 `varName: T | undefined`。例如，一个具有名为 `value` 的可选整数参数的函数，可以声明为 `value?: Integer` 或 `value: Integer | undefined`。TypeScript 函数还可以通过指定 `T | undefined` 类型来声明可选返回类型。例如，一个可能返回 `Integer` 或不返回任何值的函数，其返回类型为 `Integer | undefined`。
- 在 Python 函数中，可选参数和返回值可以使用 `typing.Optional[T]` 或 `T | None` 声明。`T | None` 语法需要 Python 3.10 或更高版本。
- 在 TypeScript 和 Python 函数中，都必须指定参数化类型 `T`，且它必须是另一个受支持的类型。

**TypeScript v1**

```typescript
import { Function } from "@foundry/functions-api";

export class MyFunction {
    @Function()
    public greet(name?: string): string | undefined {
        if (name === undefined) {
            return undefined;
        }
        return `Hello, ${name}!`;
    }
}
```

**TypeScript v2**

```typescript
function greet(name?: string): string | undefined {
    if (name === undefined) {
        return undefined;
    }
    return `Hello, ${name}!`;
}

export default greet;
```

**Python**

```python
from functions.api import function

@function
def greet(name: str | None) -> str | None:
    if name is None:
        return None
    return f"Hello, {name}!"
```

函数还支持在函数签名中使用默认值。

**TypeScript v1**

```typescript
import { Double, Function } from "@foundry/functions-api";
import { Customer } from "@foundry/ontology-api";

export class MyFunctions {
    @Function()
    public computeRiskFactor(customer: Customer, weight: Double = 0.75): Double {
        // ...
    }
}
```

**TypeScript v2**

```typescript
import { Double } from "@osdk/functions";
import { Osdk } from "@osdk/client";
import { Customer } from "@ontology/sdk";

function computeRiskFactor(customer: Osdk.Instance<Customer>, weight: Double = 0.75): Double {
    // ...
}

export default computeRiskFactor;
```

**Python**

```python
from functions.api import function, Double
from ontology_sdk.ontology.objects import Customer

@function
def compute_risk_factor(customer: Customer, weight: Double = 0.75) -> Double:
    # ...
```

### 结构体/自定义类型（Struct/custom type）

自定义类型由其他受支持的类型（包括其他自定义类型）组合而成，可用于函数签名。

- 在 TypeScript 函数中，自定义类型是使用 `interface` 关键字定义的用户自定义 TypeScript 接口。
  - 可选字段通过 `?` 可选标记或与 `undefined` 的联合类型来支持。
- 在 Python 函数中，自定义类型是用户自定义的 Python 类。
  - 要成为有效的自定义类型，该类必须遵守以下要求：
    - 该类的所有字段都必须有类型标注。
    - 字段类型必须是受支持的类型；可以使用原始 API 类型或 Python 原生类型（如上表中所定义）。
    - `__init__` 方法必须只接受与字段同名、具有相同类型标注的命名参数。
  - [`dataclasses.dataclass` ↗](https://docs.python.org/3/library/dataclasses.html) 装饰器可用于自动生成符合这些要求的 `__init__` 方法。

**TypeScript v1**

```typescript
import { Function, Integer } from "@foundry/functions-api";
import { Passenger } from "@foundry/ontology-api";

interface PassengerInfo {
    name?: string;
    age?: Integer;
}

export class MyFunctions {
    @Function()
    public getPassengerInfo(passenger: Passenger): PassengerInfo {
        return {
            name: passenger.name,
            age: passenger.age,
        };
    }
}
```

**TypeScript v2**

```typescript
import { Osdk } from "@osdk/client";
import { Integer } from "@osdk/functions";
import { Passenger } from "@ontology/sdk";

interface PassengerInfo {
    name?: string;
    age?: Integer;
}

function getPassengerInfo(passenger: Osdk.Instance<Passenger>): PassengerInfo {
    return {
        name: passenger.name,
        age: passenger.age,
    };
}

export default getPassengerInfo;
```

**Python**

```python
from dataclasses import dataclass
from functions.api import function, Integer
from ontology_sdk.ontology.objects import Passenger

@dataclass
class PassengerInfo:
    name: str | None
    age: Integer | None

@function
def get_passenger_info(passenger) -> PassengerInfo:
    return PassengerInfo(
        name=passenger.name,
        age=passenger.age
    )
```

## 聚合类型

聚合类型可以从函数返回，供平台的其他部分使用，例如 [Workshop](https://www.palantir.com/docs/foundry/workshop/overview/) 中的图表。

支持两种聚合类型：

- [二维聚合](#二维聚合two-dimensional-aggregation) 将单个桶键映射到数值。例如，可用于表示诸如具有特定职位的员工数量之类的聚合。
- [三维聚合](#三维聚合three-dimensional-aggregation) 将两个桶键映射到数值。可用于表示诸如按每位员工的职位和常驻办公地点统计的员工数量之类的聚合。

聚合支持以下几种类型作为键：

- [布尔](#布尔boolean)桶表示非 `true` 即 `false` 的值。
- [字符串](#字符串string)桶可用于表示分类值。
- [范围](#范围range)桶表示桶键为值范围的聚合。这些可用于表示图表中的直方图或日期轴。
  - 数值范围，包括 [Integer](#integer) 和 [Double](#double)，表示对数值的分桶聚合。
  - 日期和时间范围，包括基于 [Date](#日期date) 和 [Timestamp](#时间戳timestamp) 的范围，表示对日期范围的分桶聚合。

### 范围（Range）

**TypeScript v1**

```typescript
import { Function, Integer, IRange } from "@foundry/functions-api";

export class MyFunctions {
    @Function()
    public getRange(min: Integer, max: Integer): IRange<Integer> {
        return {
            min,
            max,
        };
    }
}
```

**TypeScript v2**

```typescript
import { Integer, Range } from "@osdk/functions";

function getRange(min: Integer, max: Integer): Range<Integer> {
    return {
        min,
        max,
    };
}

export default getRange;
```

**Python**

```python
from functions.api import function, Integer, Range

@function
def get_range(min: Integer, max: Integer) -> Range[Integer]:
    return Range(
        min=min,
        max=max
    )
```

### 二维聚合（Two-dimensional aggregation）

**TypeScript v1**

```typescript
import { Double, Function, TwoDimensionalAggregation } from "@foundry/functions-api";

export class MyFunctions {
    @Function()
    public myTwoDimensionalAggregation(): TwoDimensionalAggregation<string, Double> {
        return {
            buckets: [
                { key: "bucket1", value: 5.0 },
                { key: "bucket2", value: 6.0 },
            ],
        };
    }
}
```

**TypeScript v2**

```typescript
import { Double, TwoDimensionalAggregation } from "@osdk/functions";

function myTwoDimensionalAggregationFunction(): TwoDimensionalAggregation<string, Double> {
    return [
        { key: "bucket1", value: 5.0 },
        { key: "bucket2", value: 6.0 },
    ];
}

export default myTwoDimensionalAggregationFunction;
```

**Python**

```python
from functions.api import (
    function,
    Double,
    TwoDimensionalAggregation,
    SingleBucket
)

@function
def my_two_dimensional_aggregation_function() -> TwoDimensionalAggregation[str, Double]:
    return TwoDimensionalAggregation(
        buckets=[
            SingleBucket(key="bucket1", value=Double(5.0)),
            SingleBucket(key="bucket2", value=Double(6.0)),
        ]
    )
```

### 三维聚合（Three-dimensional aggregation）

**TypeScript v1**

```typescript
import { Double, Function, ThreeDimensionalAggregation } from "@foundry/functions-api";

export class MyFunctions {
    @Function()
    public myThreeDimensionalAggregation(): ThreeDimensionalAggregation<string, string, Double> {
        return {
            buckets: [
                {
                    key: "group-by-1",
                    value: [
                        { key: "partition-by-1", value: 5.0 },
                        { key: "partition-by-2", value: 6.0 },
                    ],
                },
                {
                    key: "group-by-2",
                    value: [
                        { key: "partition-by-1", value: 7.0 },
                        { key: "partition-by-2", value: 8.0 },
                    ],
                },
            ]
        };
    }
}
```

**TypeScript v2**

```typescript
import { Double, ThreeDimensionalAggregation } from "@osdk/functions";

function myThreeDimensionalAggregation(): ThreeDimensionalAggregation<string, string, Double> {
    return [
        {
            key: "group-by-1",
            value: [
                { key: "partition-by-1", value: 5.0 },
                { key: "partition-by-2", value: 6.0 },
            ],
        },
        {
            key: "group-by-2",
            value: [
                { key: "partition-by-1", value: 7.0 },
                { key: "partition-by-2", value: 8.0 },
            ],
        },
    ];
}

export default myThreeDimensionalAggregation;
```

**Python**

```python
from functions.api import (
    function,
    Double,
    ThreeDimensionalAggregation,
    SingleBucket,
    NestedBucket,
)

@function
def my_three_dimensional_aggregation_function() -> (
    ThreeDimensionalAggregation[str, str, Double]
):
    return ThreeDimensionalAggregation(
        buckets=[
            NestedBucket(key="group-by-1", buckets=[
                SingleBucket(key="partition-by-1", value=Double(5.0)),
                SingleBucket(key="partition-by-2", value=Double(6.0)),
            ]),
            NestedBucket(key="group-by-2", buckets=[
                SingleBucket(key="partition-by-1", value=Double(7.0)),
                SingleBucket(key="partition-by-2", value=Double(8.0)),
            ])
        ]
    )
```

## 本体类型

> **ℹ️ 提示：本体导入（Ontology imports）**
>
> 必须将对象类型导入你的代码仓库，才能在函数签名中使用它们。[详细了解本体导入。](https://www.palantir.com/docs/foundry/functions/ontology-imports/)

### 对象（Object）

本体中的对象类型既可以用作函数签名的输入，也可以用作输出。要接受或返回单个对象类型实例，请从你的 Ontology SDK 导入该对象类型，并使用该类型标注你的函数。

**TypeScript v1**

```typescript
import { Function, Integer } from "@foundry/functions-api";
import { Airplane } from "@foundry/ontology-api";

export class MyFunctions {
    @Function()
    public getCapacity(airplane: Airplane): Integer {
        return airplane.capacity;
    }
}
```

**TypeScript v2**

```typescript
import { Osdk } from "@osdk/client";
import { Integer } from "@osdk/functions";
import { Airplane } from "@ontology/sdk";

function getCapacity(airplane: Osdk.Instance<Airplane>): Integer {
    return airplane.capacity;
}

export default getCapacity;
```

**Python**

```python
from functions.api import function, Integer
from ontology_sdk.ontology.objects import Airplane

@function
def get_capacity(airplane: Airplane) -> Integer:
    return airplane.capacity
```

在 TypeScript v2 中，支持将对象类型引用作为[结构体](#结构体自定义类型structcustom-type)参数字段。要接受带有对象类型实例的结构体或结构体列表，请使用来自 Ontology SDK 的对象类型字段创建自定义类型输入。这可以与[本体编辑](#本体编辑ontology-edit)配合使用，以支持诸如创建从其他对象类型派生的多个对象类型实例之类的工作流。

**TypeScript v2**

```typescript
import { Osdk } from "@osdk/client";
import { Integer } from "@osdk/functions";
import { Airplane, Passenger, Ticket } from "@ontology/sdk";

type TicketEdit = Edits.Object<Ticket>

interface TicketInfo {
    airplane?: Osdk.Instance<Airplane>;
    passenger?: Osdk.Instance<Passenger>;
    seat?: String;
}

function createTickets(ticketInfo: TicketInfo[]): TicketEdit[] {
    const batch = createEditBatch<TicketEdit>(client);

    ticketInfo.forEach(i => batch.create(TicketEdit, {
        flightNumber: i.airplane.flightNumber,
        passengerName: i.passenger.name,
        seat: i.seat}))

    return batch.getEdits();
}

export default createTickets;
```

### 对象集（Object set）

将对象集合传入和传出函数有两种方式：具体的对象集合（如数组），或对象集。

将对象数组传递给函数，可以在具体的对象列表上执行逻辑，但代价是需要预先将对象加载到函数执行环境中。而对象集允许你执行筛选、环绕搜索（search-around）和聚合操作，并且仅在你请求最终结果时才加载它。

> **✅ 说明**
>
> 我们建议使用对象集而非数组，因为对象集通常能获得更好的性能，并且允许向函数传入超过 10,000 个对象。

下面的示例展示了如何在不将对象加载到内存的情况下筛选对象集，从而使你能够将筛选后的对象集返回到应用的不同部分。

**TypeScript v1**

```typescript
import { Function } from "@foundry/functions-api";
import { Airplane, ObjectSet } from "@foundry/ontology-api";

export class MyFunctions {
    @Function()
    public filterAircraft(aircraft: ObjectSet<Airplane>): ObjectSet<Airplane> {
        return aircraft.filter(a => a.capacity.range().gt(200));
    }
}
```

**TypeScript v2**

```typescript
import { ObjectSet } from "@osdk/client";
import { Airplane } from "@ontology/sdk";

function filterAircraft(aircraft: ObjectSet<Airplane>): ObjectSet<Airplane> {
    return aircraft
        .where({
            capacity: {
                $gt: 200,
            }
        });
}

export default filterAircraft;
```

**Python**

```python
from functions.api import function
from ontology_sdk.ontology.objects import Airplane
from ontology_sdk.ontology.object_sets import AirplaneObjectSet

@function
def filter_aircraft(aircraft: AirplaneObjectSet) -> AirplaneObjectSet:
    return aircraft.where(Airplane.object_type.capacity > 200)
```

### 接口（Interface）

本体中的接口类型既可以用作 TypeScript v2 函数签名的输入，也可以用作输出。TypeScript v1 或 Python 不支持它们。

**TypeScript v2**

```typescript
import { Osdk } from "@osdk/client";
import { Integer } from "@osdk/functions";
import { Person } from "@ontology/sdk";

function getAge(person: Osdk.Instance<Person>): Integer {
    return person.age;
}

export default getAge;
```

### 接口对象集（Interface object set）

接口对象集在 TypeScript v2 函数签名中既可以用作输入，也可以用作输出。

**TypeScript v2**

```typescript
import { ObjectSet } from "@osdk/client";
import { Person } from "@ontology/sdk";

function filterPeople(people: ObjectSet<Person>): ObjectSet<Person> {
    return people
        .where({
            age: {
                $gt: 200,
            }
        });
}

export default filterPeople;
```

### 本体编辑（Ontology edit）

除了编写从本体读取数据的函数外，你还可以编写用于创建对象、编辑对象属性以及对象之间链接的函数。有关编辑函数工作原理的更多细节，请参阅[概览页](https://www.palantir.com/docs/foundry/functions/edits-overview/)。

要注册为编辑函数，TypeScript v1 函数要求签名中的返回类型为 `void`。而 TypeScript v2 和 Python 函数则要求显式返回本体编辑列表。

**TypeScript v1**

```typescript
import { Edits, OntologyEditFunction } from "@foundry/functions-api";
import { Employee, LaptopRequest, Objects } from "@foundry/ontology-api";

export class MyFunctions {

    @Edits(Employee, LaptopRequest)
    @OntologyEditFunction()
    public assignEmployee(newEmployee: Employee, leadEmployee: Employee): void {

        const newLaptopRequest = Objects.create().laptopRequest(Date.now().toString());
        newLaptopRequest.employeeName = newEmployee.name;

        newEmployee.lead.set(leadEmployee);
    }
}
```

**TypeScript v2**

```typescript
import { Client } from "@osdk/client";
import { createEditBatch, Edits } from "@osdk/functions";
import { Employee, LaptopRequest } from "@ontology/sdk";

type EmployeeEdit =
    | Edits.Object<Employee>
    | Edits.Object<LaptopRequest>
    | Edits.Link<Employee, "lead">;

function assignEmployee(
    client: Client,
    newEmployee: Osdk.Instance<Employee>,
    leadEmployee: Osdk.Instance<Employee>
): EmployeeEdit[] {

    const batch = createEditBatch<EmployeeEdit>(client);

    batch.create(LaptopRequest, {
        id: Date.now().toString(),
        employeeName: newEmployee.name,
    });
    batch.link(newEmployee, "lead", leadEmployee);

    return batch.getEdits();
}

export default assignEmployee;
```

**Python**

```python
from functions.api import function, OntologyEdit
from ontology_sdk import FoundryClient
from ontology_sdk.ontology.objects import Employee, LaptopRequest
from time import time

@function
def assign_employee(new_employee: Employee, lead_employee: Employee) -> list[OntologyEdit]:

    ontology_edits = FoundryClient().ontology.edits()

    new_laptop_request = ontology_edits.objects.LaptopRequest.create(str(int(time() * 1000)))
    new_laptop_request.employee_name = new_employee.name

    new_employee.lead.set(lead_employee)

    return ontology_edits.get_edits()
```

### 附件（Attachment）

**TypeScript v1**

```typescript
import { Attachment, Function } from "@foundry/functions-api";

export class MyFunctions {
    @Function()
    public loadAttachmentContents(attachment: Attachment): Promise<string> {
        return attachment.readAsync().then(blob => blob.text());
    }
}
```

**TypeScript v2**

```typescript
import { Attachment } from "@osdk/functions";

function loadAttachmentContents(attachment: Attachment): Promise<string> {
    return attachment.fetchContents().then(response => response.text());
}

export default loadAttachmentContents;
```

**Python**

```python
from functions.api import function, Attachment

@function
def load_attachment_contents(attachment: Attachment) -> str:
    return attachment.read().getvalue().decode('utf-8')
```

### 通知（Notification）

通知类型可以从函数返回，以灵活配置应在平台中发送的通知。例如，你可以编写一个函数，接收 `User` 和对象类型等参数，并返回一条配置了消息的通知。

- `Notification` 由两个字段组成：`ShortNotification` 和 `EmailNotificationContent`。
- `ShortNotification` 表示通知的摘要版本，将在 Foundry 平台内显示。它包含简短的 `heading`、`content` 以及一组 `Link`。
- `EmailNotificationContent` 表示通知的富文本版本，可通过电子邮件对外发送。它包含 `subject`、由不含 `<head>` 标签的 HTML 组成的 `body`，以及一组 `Link`。
- `Link` 具有面向用户的 `label` 和 `linkTarget`。`LinkTarget` 可以是 URL、`OntologyObject`，或 Foundry 中任何资源的 `rid`。

有关如何使用 Notifications API 的示例，请参阅[我们的指南](https://www.palantir.com/docs/foundry/functions/configure-notifications/)。

**TypeScript v1**

```typescript
import {
    EmailNotificationContent,
    Function,
    Notification,
    ShortNotification,
} from "@foundry/functions-api";

export class MyFunctions {
    @Function()
    public buildNotification(): Notification {
        return Notification.builder()
            .shortNotification(ShortNotification.builder()
                .heading("Issue reminder")
                .content("Investigate this issue.")
                .build())
            .emailNotificationContent(EmailNotificationContent.builder()
                .subject("New issue")
                .body("hello")
                .build())
            .build();
    }
}
```

**TypeScript v2**

```typescript
import {
    Notification
} from "@osdk/functions";

export default function buildNotification(): Notification {
    return {
        platformNotification: {
            heading: "Issue reminder",
            content: "Investigate this issue.",
            links: []
        },
        emailNotification: {
            subject: "New issue",
            body: "hello",
            links: []
        }
    };
}
```

**Python**

```python
from functions.api import function, Notification, PlatformNotification, EmailNotification

@function()
def buildNotification() -> Notification:
    return Notification(
        platform_notification=PlatformNotification(
            heading="Issue reminder",
            content="Investigate this issue.",
            links=[]
        ),
        email_notification=EmailNotification(
            subject="New issue",
            body="hello",
            links=[]
        ),
    )
```

## 媒体类型

### 媒体（Media）

函数可以接受和返回媒体项。在 TypeScript v1 中，使用 `MediaItem` 类型。在 TypeScript v2 和 Python 中，使用 `Media` 作为输入和输出类型。调用者可以将现有的 `MediaReference`（例如对象上的媒体属性）传入函数。下游消费者可以使用返回的值来获取内容、获取元数据，或将其附加到另一个对象上。更多信息请参阅关于[媒体](https://www.palantir.com/docs/foundry/functions/media/)的指南。

**TypeScript v1**

```typescript
import { Function, MediaItem } from "@foundry/functions-api";

export class MyFunctions {
    @Function()
    public async echoMedia(media: MediaItem): Promise<string | undefined> {
        const mimeType: string = media.mimeType;
        // Fetch type-specific metadata (page count, dimensions, duration, and so on)
        const metadata = await media.getMetadataAsync();
        // Read the binary contents as a Blob
        const contents: Blob = await media.readAsync();
        // Narrow to a specialized subtype to call type-specific methods
        if (MediaItem.isDocument(media)) {
            const text = await media.extractTextAsync({ startPage: 0, endPage: 1 });
        } else if (MediaItem.isAudio(media)) {
            const transcript = await media.transcribeAsync();
        }
        return undefined;
    }
}
```

**TypeScript v2**

```typescript
import type { Media } from "@osdk/client";

export default async function echoMedia(media: Media): Promise<Media> {
    // Get the underlying MediaReference
    const mediaReference = media.getMediaReference();
    // Fetch slim metadata: path, sizeBytes, mediaType
    const metadata = await media.fetchMetadata();
    // Fetch contents as a Response; call .blob() or .arrayBuffer() for bytes
    const response = await media.fetchContents();
    const contents = await response.blob();
    return media;
}
```

**Python**

```python
from foundry_sdk.v2.core.models import MediaReference
from functions.api import function, Media

@function
def echo_media(media: Media) -> Media:
    # Get the underlying MediaReference
    media_reference: MediaReference = media.get_media_reference()
    # Fetch slim metadata: path, size_bytes, media_type
    metadata = media.get_media_metadata()
    # Fetch type-specific metadata (page count, dimensions, duration, and more by type)
    full_metadata = media.get_media_full_metadata()
    # Fetch the binary contents as a BytesIO stream
    contents = media.get_media_content()
    return media
```

## 用户、用户组和主体

Principal 表示 Foundry 用户账户或用户组。这些类型可以传入函数，以访问与用户或用户组关联的信息，例如用户组的名称，或用户的名字、姓氏或电子邮件地址。所有 Principal 类型都从 `@foundry/functions-api` 包导出。

- `User` 始终具有用户名，并且可能具有 `firstName`、`lastName` 或 `email`。它还包括与 `Principal` 关联的所有字段。
- `Group` 具有名称。它还包括与 `Principal` 关联的所有字段。
- `Principal` 可以是 `User` 或 `Group`。你可以检查 `type` 字段来确定给定的 `Principal` 是 `User` 还是 `Group`。除了 `User` 和 `Group` 特有字段外，`Principal` 还具有 `id`、`realm` 以及 `attributes` 字典。

### 用户（User）

**TypeScript v1**

```typescript
import { Function, User } from "@foundry/functions-api";

export class MyFunctions {
    @Function()
    public getUserEmail(user: User): string {
        return user.email;
    }
}
```

**TypeScript v2**

```typescript
import { UserId } from "@osdk/functions";
import { Users } from "@osdk/foundry.admin";
import { Client } from "@osdk/client";

export default function getUserEmail(client:Client, userId: UserId): string {
    const user = Users.get(client, userId)
    return user.email;
}
```

**Python**

```python
from functions.api import function, UserId
from foundry_sdk import FoundryClient
import foundry_sdk

@function()
def getUserEmail(user_id: UserId) -> string:
    client = FoundryClient(auth=foundry_sdk.UserTokenAuth(...), hostname="example.palantirfoundry.com")
    user = client.admin.User.get(user_id)
    return user.email
```

### 用户组（Group）

**TypeScript v1**

```typescript
import { Function, Group } from "@foundry/functions-api";

export class MyFunctions {
    @Function()
    public getGroupName(group: Group): string {
        return group.name;
    }
}
```

**TypeScript v2**

```typescript
import { GroupId } from "@osdk/functions";
import { Groups } from "@osdk/foundry.admin";
import { Client } from "@osdk/client";

export default function getGroupName(client: Client, groupId: GroupId): string {
    const group = Groups.get(client, groupId)
    return group.name;
}
```

**Python**

```python
from functions.api import function, GroupId
from foundry_sdk import FoundryClient
import foundry_sdk

@function()
def getGroupName(group_id: GroupId) -> string:
    client = FoundryClient(auth=foundry_sdk.UserTokenAuth(...), hostname="example.palantirfoundry.com")
    group = client.admin.Group.get(group_id)
    return group.name
```

### 主体（Principal）

**TypeScript v1**

```typescript
import { Function, Principal } from "@foundry/functions-api";

export class MyFunctions {
    @Function()
    public getPrincipalType(principal: Principal): string {

        switch (principal.type) {
            case "user":
                return "User";
            case "group":
                return "Group";
            default:
                return "Unknown";
        }
    }
}
```

**TypeScript v2**

```typescript
import { GroupId, Principal, UserId } from "@osdk/functions";

export default async function getPrincipals(client: Client, userId: UserId, groupId: GroupId): Principal[] {
    return [{type: "user", id: userId}, {type: "group", id: groupId}];
}
```

**Python**

```python
from functions.api import Array, function, GroupId, Principal, UserId

@function()
def getPrincipals(user_id: UserId, group_id: GroupId) -> Array[Principal]:
    return [Principal.user(user_id), Principal.group(group_id)]
```

## 几何类型

几何类型在函数中表示空间数据和地理形状。支持两种几何类型：

- **GeoPoint** 表示具有经纬度坐标的单个地理点。
- **GeoShape** 表示任何有效的 GeoJSON 几何，包括点（Point）、多边形（Polygon）、线串（LineString）和其他形状。

这些类型遵循 [GeoJSON 规范 ↗](https://geojson.org/)，可用于空间操作、地图绘制和地理分析。请注意，按照 GeoJSON 规范，位置参数遵循经度、纬度的顺序。

### GeoPoint

以下示例展示了如何创建并返回一个 GeoPoint。

**TypeScript v1**

```typescript
import { Function, GeoPoint } from "@foundry/functions-api";

export class MyFunctions {
    @Function()
    public createPoint(): GeoPoint {
        return GeoPoint.fromCoordinates({
            latitude: 37.7749,
            longitude: -22.4194
        });
    }
}
```

**TypeScript v2**

```typescript
import { Point } from "@osdk/functions";

function createPoint(): Point {
    return {
        type: "Point",
        coordinates: [-22.4194, 37.7749]
    };
}

export default createPoint;
```

**Python**

```python
from functions.api import function, GeoPoint

@function
def create_point() -> GeoPoint:
    return GeoPoint(type="Point", coordinates=[-22.4194, 37.7749])
```

### GeoShape

以下示例展示了如何创建并返回一个 Polygon。

**TypeScript v1**

```typescript
import { Function, Polygon, GeoPoint } from "@foundry/functions-api";

export class MyFunctions {
    @Function()
    public createPolygon(): Polygon {
        const ring: GeoPoint[] = [
            GeoPoint.fromCoordinates({ latitude: 37.8, longitude: -22.4 }),
            GeoPoint.fromCoordinates({ latitude: 37.8, longitude: -22.5 }),
            GeoPoint.fromCoordinates({ latitude: 37.7, longitude: -22.5 }),
            GeoPoint.fromCoordinates({ latitude: 37.7, longitude: -22.4 }),
            GeoPoint.fromCoordinates({ latitude: 37.8, longitude: -22.4 })
        ];
        return Polygon.fromLinearRings([ring]);
    }
}
```

**TypeScript v2**

```typescript
import { Geometry } from "@osdk/functions";

function createPolygon(): Geometry {
    return {
        type: "Polygon",
        coordinates: [[
            [-22.4, 37.8],
            [-22.5, 37.8],
            [-22.5, 37.7],
            [-22.4, 37.7],
            [-22.4, 37.8]
        ]]
    };
}

export default createPolygon;
```

**Python**

```python
from functions.api import function, Polygon

@function
def create_polygon() -> Polygon:
    return Polygon(
        type="Polygon",
        coordinates=[[
            [-22.4, 37.8],
            [-22.5, 37.8],
            [-22.5, 37.7],
            [-22.4, 37.7],
            [-22.4, 37.8]
        ]])
```

[本体编辑函数](https://www.palantir.com/docs/foundry/functions/edits-overview/)可以通过 GeoJSON 字符串设置 `geoshape` 属性，但转换步骤因语言而异：

- **TypeScript v1：** `GeoShape.fromGeoJson()` 接受已解析的 GeoJSON 几何或几何集合，因此在传入之前请先解析字符串。
- **TypeScript v2：** `Geometry` 是普通的 GeoJSON 对象，因此无需转换函数；直接赋值解析后的值即可。
- **Python：** 每个具体几何类（如 `Polygon` 或 `LineString`）都提供接受 JSON 字符串本身的 `from_geo_json()` 方法。`GeoShape` 类型不提供此方法，因此请使用与你预期几何相匹配的类。

以下示例展示了如何通过 JSON 字符串设置 `Region` 对象的 `geoshape` 属性。

**TypeScript v1**

```typescript
import { OntologyEditFunction, Edits, GeoShape } from "@foundry/functions-api";
import { Region } from "@foundry/ontology-api";

export class MyFunctions {
    @Edits(Region)
    @OntologyEditFunction()
    public updateBoundary(region: Region, boundary: string): void {
        region.boundary = GeoShape.fromGeoJson(JSON.parse(boundary));
    }
}
```

**TypeScript v2**

```typescript
import { Region } from "@ontology/sdk";
import { Client, Osdk } from "@osdk/client";
import { createEditBatch, Edits, Geometry } from "@osdk/functions";

type RegionEdit = Edits.Object<Region>;

function updateBoundary(
    client: Client,
    region: Osdk.Instance<Region>,
    boundary: string
): RegionEdit[] {
    const batch = createEditBatch<RegionEdit>(client);

    batch.update(region, { boundary: JSON.parse(boundary) as Geometry });

    return batch.getEdits();
}

export default updateBoundary;
```

**Python**

```python
from functions.api import function, OntologyEdit, Polygon
from ontology_sdk import FoundryClient
from ontology_sdk.ontology.objects import Region

@function(edits=[Region])
def update_boundary(region: Region, boundary: str) -> list[OntologyEdit]:
    ontology_edits = FoundryClient().ontology.edits()

    editable_region = ontology_edits.objects.Region.edit(region)
    editable_region.boundary = Polygon.from_geo_json(boundary)

    return ontology_edits.get_edits()
```

---

*原文：[Types reference](https://www.palantir.com/docs/foundry/functions/types-reference/)*
