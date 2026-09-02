# 验证本体编辑（Verify Ontology edits）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/unit-test-ontology-edits/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

你可以使用 `verifyOntologyEditFunction()` API 来验证你的函数所执行的编辑。你需要从 `"@foundry/functions-testing-lib"` 导入它。这使你能够围绕下面列出的工作流创建单元测试。

#### 单数与复数验证方法

每一种编辑都可以用单数方法或复数方法来验证。单数方法验证一次编辑，接受单个值；复数方法验证同一种类的多次编辑，接受这些值组成的数组。这两种形式不可互换，用错会导致类型错误。

| 要验证的编辑 | 验证一次编辑 | 验证多次编辑 |
| --- | --- | --- |
| 对象创建 | `.createsObject({ objectType, properties })` | `.createsObjects([{ objectType, properties }])` |
| 对象属性编辑 | `.modifiesObject({ object, properties })` | `.modifiesObjects([{ object, properties }])` |
| 对象删除 | `.deletesObject(object)` | `.deletesObjects([object])` |
| 链接创建 | `.addsLink({ link, linkedObject })` | `.addsLinks([{ link, linkedObject }])` |
| 链接移除 | `.removesLink({ link, unlinkedObject })` | `.removesLinks([{ link, unlinkedObject }])` |

四个链接方法还接受一个回调，该回调会收到目前已收集到的编辑；当要链接的对象是由被测函数创建时，这很有用。在这种情况下，`.addsLink` 和 `.removesLink` 的回调返回单个链接，而 `.addsLinks` 和 `.removesLinks` 的回调返回数组。示例请参阅[验证对新建对象的编辑](#验证对新建对象的编辑)。

`.hasNoMoreEdits()` 没有复数形式，并且由于它不返回结果，它必须位于验证链的最后。

#### 验证对象创建

你可以使用 `.createsObject` 方法来验证对象创建。若要在一次调用中验证多个对象的创建，请改用 [`.createsObjects`](#验证创建了多个对象)。下面是一个示例：

```typescript
import { MyFunctions } from ".."

import { Objects , ExampleDataAirport } from "@foundry/ontology-api";
import { verifyOntologyEditFunction } from "@foundry/functions-testing-lib";

describe("example test suite", () => {
    const myFunctions = new MyFunctions();

    test("create airport", () => { 
        verifyOntologyEditFunction(() => myFunctions.createAirport("airportCode", "airportDisplayName"))
            .createsObject(
                {
                    objectType: ExampleDataAirport,
                    properties: {
                        airport: "airportCode",
                        displayAirportName: "airportDisplayName",
                    },
                });
    });
});
```

这可用于测试以下函数：

```typescript
import { Function, OntologyEditFunction, Edits } from "@foundry/functions-api";
import { Objects, ExampleDataAirport } from "@foundry/ontology-api";

export class MyFunctions {

    @Edits(ExampleDataAirport)
    @OntologyEditFunction()
    public createAirport(airport: string, displayName: string): void {
        const newAirport = Objects.create().exampleDataAirport(airport);
        newAirport.displayAirportName = displayName;
    }
}
```

#### 验证对新建对象的编辑

你可以验证涉及新创建对象的编辑。例如，你可能想要创建一个新的 `ExampleDataFlight` 对象，并验证是否创建了指向 `new-flight-delay-0` 的链接。下面是一个示例：

```typescript
import { MyFunctions } from ".."

import { Objects , ExampleDataFlight, ExampleFlightDelayEvent } from "@foundry/ontology-api";
import { verifyOntologyEditFunction } from "@foundry/functions-testing-lib";

describe("example test suite", () => {
    const myFunctions = new MyFunctions();

    test("single key with single created object", () => {
            const flight = Objects.create().exampleDataFlight("flightTest");
            verifyOntologyEditFunction(() => myFunctions.createAndLinkDelays(flight, 1))
                .createsObject({
                    objectType: ExampleFlightDelayEvent,
                    properties: {
                        eventId: "new-flight-delay-0",
                    },
                })
                .addsLink(edits => ({
                    link: flight.flightDelayEvent,
                    linkedObject: edits.createdObjects.byObjectType(ExampleFlightDelayEvent)[0],
                }))
        });
});
```

这可用于测试以下函数：

```typescript
import { Function, Integer, OntologyEditFunction, Edits } from "@foundry/functions-api";
import { Objects, ExampleDataFlight, ExampleFlightDelayEvent } from "@foundry/ontology-api";

export class MyFunctions {

    @Edits(ExampleDataFlight, ExampleFlightDelayEvent )
    @OntologyEditFunction()
    public createAndLinkDelays(flight: ExampleDataFlight, numDelay: Integer): void {
        for (let n = 0; n < numDelay; n++) {
            const delay = Objects.create().exampleFlightDelayEvent(`new-flight-delay-${n}`);
            flight.flightDelayEvent.add(delay);
        }
    }
}
```

#### 验证对象属性编辑

你可以使用 `.modifiesObject` 验证对属性的编辑。若要在一次调用中验证多个对象上的属性编辑，请向 `.modifiesObjects` 传入一个数组。下面是一个示例：

```typescript
import { MyFunctions } from ".."

import { Objects , ExampleDataFlight, ExampleFlightDelayEvent } from "@foundry/ontology-api";
import { verifyOntologyEditFunction } from "@foundry/functions-testing-lib";

describe("example test suite", () => {
    const myFunctions = new MyFunctions();

    test("modifies aircraft of the flight", () => {
        const flight = Objects.create().exampleDataFlight("NY -> LA");
        const oldAircraft = Objects.create().exampleDataAircraft("N11111");
        flight.aircraft.set(oldAircraft);
        const newAircraft = Objects.create().exampleDataAircraft("A00000");
        verifyOntologyEditFunction(() => myFunctions.assignAircraftToFlight(flight, newAircraft))
            .modifiesObject(
                { 
                    object: flight, 
                    properties: { 
                        tailNumber: "A00000" 
                    } 
                })
        });
});
```

这可用于测试以下函数：

```typescript
import { Function, OntologyEditFunction, Edits } from "@foundry/functions-api";
import { Objects, ExampleDataFlight, ExampleFlightDelayEvent } from "@foundry/ontology-api";

export class MyFunctions {

    @Edits(ExampleDataFlight)
    @OntologyEditFunction()
    public assignAircraftToFlight(flight: ExampleDataFlight, aircraft: ExampleDataAircraft): void {
        flight.aircraft.clear();
        aircraft.flight.set(flight);
        flight.tailNumber = aircraft.tailNumber;
    }
}
```

#### 验证对象没有其他编辑

你可以使用可选的 `.hasNoMoreEdits()` 来确保不存在其他编辑。这意味着只允许指定的编辑，如果检测到其他编辑，验证将失败。下面是一个示例：

```typescript
import { MyFunctions } from ".."

import { Objects , ExampleDataFlight, ExampleFlightDelayEvent } from "@foundry/ontology-api";
import { verifyOntologyEditFunction } from "@foundry/functions-testing-lib";

describe("example test suite", () => {
    const myFunctions = new MyFunctions();

    test("single key with linked object", () => {
            const flight = Objects.create().exampleDataFlight("flightAnotherTest");
            const delay = Objects.create().exampleFlightDelayEvent("new-flight-delay")
            verifyOntologyEditFunction(() => myFunctions.linkDelays(flight, delay))
                .addsLink({link: flight.flightDelayEvent, linkedObject: delay })
                .hasNoMoreEdits();
        });
});
```

使用 `.hasNoMoreEdits()` 时，你可以忽略所发生的特定种类的编辑。方法是传入一个包含以下部分或全部项的对象：

- `ignoreExtraCreatedObjects: true`
- `ignoreExtraModifiedObjects: true`
- `ignoreExtraDeletedObjects: true`
- `ignoreExtraLinkedObjects: true`
- `ignoreExtraUnlinkedObjects: true`

#### 验证指向对象的链接创建

你可以使用 `.addsLink` 验证对象上的链接创建。下面是一个示例：

```typescript
import { MyFunctions } from ".."

import { Objects , ExampleDataFlight, ExampleFlightDelayEvent } from "@foundry/ontology-api";
import { verifyOntologyEditFunction } from "@foundry/functions-testing-lib";

describe("example test suite", () => {
    const myFunctions = new MyFunctions();

    test("single key with linked object", () => {
            const flight = Objects.create().exampleDataFlight("flightAnotherTest");
            const delay = Objects.create().exampleFlightDelayEvent("new-flight-delay")
            verifyOntologyEditFunction(() => myFunctions.linkDelays(flight, delay))
                .addsLink({link: flight.flightDelayEvent, linkedObject: delay })
                .hasNoMoreEdits();
        });
});
```

此测试等价于测试沿相反方向的同一链接：

```typescript
import { MyFunctions } from ".."

import { Objects , ExampleDataFlight, ExampleFlightDelayEvent } from "@foundry/ontology-api";
import { verifyOntologyEditFunction } from "@foundry/functions-testing-lib";

describe("example test suite", () => {
    const myFunctions = new MyFunctions();

    test("single key with linked object reverse", () => {
            const flight = Objects.create().exampleDataFlight("flightAnotherTest");
            const delay = Objects.create().exampleFlightDelayEvent("new-flight-delay")
            verifyOntologyEditFunction(() => myFunctions.linkDelays(flight, delay))
                .addsLink({link: delay.flight, linkedObject: flight })
                .hasNoMoreEdits();
        });
});
```

这可用于测试以下函数：

```typescript
import { Function, OntologyEditFunction, Edits } from "@foundry/functions-api";
import { Objects, ExampleDataFlight, ExampleFlightDelayEvent } from "@foundry/ontology-api";

export class MyFunctions {

    @Edits(ExampleDataFlight, ExampleFlightDelayEvent )
    @OntologyEditFunction()
    public linkDelays(flight: ExampleDataFlight, delay: ExampleFlightDelayEvent): void {
        flight.flightDelayEvent.add(delay);
    }
}
```

#### 验证从对象移除链接

你可以使用 `.removesLink` 验证从对象移除链接。下面是一个示例：

```typescript
import { MyFunctions } from ".."

import { Objects , ExampleDataFlight, ExampleFlightDelayEvent } from "@foundry/ontology-api";
import { verifyOntologyEditFunction } from "@foundry/functions-testing-lib";

describe("example test suite", () => {
    const myFunctions = new MyFunctions();

    test("test link removal", () => {
            const flight = Objects.create().exampleDataFlight("flightAnotherTest");
            const delay = Objects.create().exampleFlightDelayEvent("new-flight-delay")
            flight.flightDelayEvent.add(delay);
            verifyOntologyEditFunction(() => myFunctions.removeAllDelays(flight))
                .removesLink({link: flight.flightDelayEvent, unlinkedObject: delay })
                .hasNoMoreEdits();
        });
});
```

这可用于测试以下函数：

```typescript
import { Function, OntologyEditFunction, Edits } from "@foundry/functions-api";
import { Objects, ExampleDataFlight, ExampleFlightDelayEvent } from "@foundry/ontology-api";

export class MyFunctions {

    @Edits(ExampleDataFlight, ExampleFlightDelayEvent)
    @OntologyEditFunction()
    public removeAllDelays(flight: ExampleDataFlight): void {
        flight.flightDelayEvent.clear();
    }
}
```

#### 验证删除对象

你可以使用 `.deletesObject` 验证删除对象。下面是一个示例：

```typescript
import { MyFunctions } from ".."

import { Objects , ExampleDataFlight } from "@foundry/ontology-api";
import { verifyOntologyEditFunction } from "@foundry/functions-testing-lib";

describe("example test suite", () => {
    const myFunctions = new MyFunctions();

    test("test object deletion", () => {
            const flight = Objects.create().exampleDataFlight("flightAnotherTest");
            verifyOntologyEditFunction(() => myFunctions.deleteFlight(flight))
                .deletesObject(flight)
                .hasNoMoreEdits();
        });
});
```

这可用于测试以下函数：

```typescript
import { Function, OntologyEditFunction, Edits } from "@foundry/functions-api";
import { Objects, ExampleDataFlight } from "@foundry/ontology-api";

export class MyFunctions {

    @Edits(ExampleDataFlight)
    @OntologyEditFunction()
    public deleteFlight(flight: ExampleDataFlight): void {
        flight.delete();
    }
}
```

#### 验证创建了多个对象

你可以使用 `.createsObjects` 方法并传入一个对象规格数组，以验证创建了多个对象。下面是一个示例：

```typescript
import { MyFunctions } from ".."

import { Objects , ExampleDataFlight, ExampleFlightDelayEvent } from "@foundry/ontology-api";
import { verifyOntologyEditFunction } from "@foundry/functions-testing-lib";

describe("example test suite", () => {
    const myFunctions = new MyFunctions();

    test("single key with many created objects", () => {
            const flight = Objects.create().exampleDataFlight("flightTest");
            verifyOntologyEditFunction(() => myFunctions.createAndLinkDelays(flight, 3))
                .createsObjects(
                    [0, 1, 2].map(i => ({
                        objectType: ExampleFlightDelayEvent,
                        properties: {
                            eventId: "new-flight-delay-" + i,
                        },
                    })),
                )
                .addsLinks(edits =>
                    edits.createdObjects.byObjectType(ExampleFlightDelayEvent).map(event => ({
                        link: flight.flightDelayEvent,
                        linkedObject: event,
                    })),
                )
                .hasNoMoreEdits();
        });
});
```

这可用于测试以下函数：

```typescript
import { Function, Integer, OntologyEditFunction, Edits } from "@foundry/functions-api";
import { Objects, ExampleDataFlight, ExampleFlightDelayEvent } from "@foundry/ontology-api";

export class MyFunctions {

    @Edits(ExampleDataFlight, ExampleFlightDelayEvent )
    @OntologyEditFunction()
    public createAndLinkDelays(flight: ExampleDataFlight, numDelay: Integer): void {
        for (let n = 0; n < numDelay; n++) {
            const delay = Objects.create().exampleFlightDelayEvent(`new-flight-delay-${n}`);
            flight.flightDelayEvent.add(delay);
        }
    }
}
```

### 异步本体编辑

你可以按如下方式验证异步本体编辑：

```typescript
import { verifyOntologyEditFunction } from "@foundry/functions-testing-lib";

test("test async edit function", async () => {
        const obj = Objects.create().objectWithAllPropertyTypes(1);
        (await verifyOntologyEditFunction(() => myFunctions.setDateAndTimestampToNow(obj))).modifiesObject({
            object: obj,
            properties: {
                timestampProperty: makeTimestamp(),
            },
        });
    });
```

### 多重验证

正如我们在上面的示例中看到的，我们可以链式调用验证。以下模式说明了这一点：

```typescript
import { verifyOntologyEditFunction } from "@foundry/functions-testing-lib";
import { Objects, ExampleDataObject } from "@foundry/ontology-api";

test("multiple action edit", () => { 
    verifyOntologyEditFunction(() => myFunctions.multistageEdits("objectId", "objectName"))
        .createsObject({...})
        .modifiesObjects([{...}])
        .addsLinks([{...}])
        .removesLinks([{...}])
        .deletesObject(...)
        .hasNoMoreEdits(); 
});
```

---

*原文：[Verify Ontology edits](https://www.palantir.com/docs/foundry/functions/unit-test-ontology-edits/)*
