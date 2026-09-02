# 模拟日期、时间戳和 UUID（Mock dates, timestamps, and UUIDs）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/unit-test-dates-timestamps/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

你可以利用 `jest.spyOn()` 注入一个模拟来运行测试，从而指定非确定性函数的输出。

### UUID 函数

> **ℹ️ 提示**
>
> 来自 `@foundry/functions-utils` 的 `Uuid` 工具在 TypeScript v1 中可用，并在下面的示例中使用。对于 TypeScript v2，请使用标准 UUID 库，例如 [`uuid` npm 包 ↗](https://www.npmjs.com/package/uuid)，并在测试中直接模拟它。

你可以通过注入模拟来指定 `Uuid` 的输出。下面是一个示例：

```typescript
import { MyFunctions } from ".."

import { Objects, ExampleDataFlight } from "@foundry/ontology-api";
import { verifyOntologyEditFunction } from "@foundry/functions-testing-lib";
import { Uuid } from "@foundry/functions-utils";

describe("example test suite", () => {
    const myFunctions = new MyFunctions();

    test("creates new flight", () => {
        const makeUuid = () => "my-uuid";
        jest.spyOn(Uuid, "random").mockImplementation(() => makeUuid());

        verifyOntologyEditFunction(() => myFunctions.createNewFlight())
            .createsObject({
                objectType: ExampleDataFlight,
                properties: {
                    flightId: makeUuid()
                }
            })
    })
});
```

这可用于测试以下函数：

```typescript
import { Function, OntologyEditFunction, Edits } from "@foundry/functions-api";
import { Objects, ExampleDataFlight } from "@foundry/ontology-api";
import { Uuid } from "@foundry/functions-utils";

export class MyFunctions {
    @Edits(ExampleDataFlight)
    @OntologyEditFunction()
    public createNewFlight(): void {
        Objects.create().exampleDataFlight(Uuid.random());
    }
}
```

#### 高级 UUID 函数

在某些情况下，你可能希望完全控制 `Uuid` 的输出。这需要你调整正在测试的函数的代码。例如，上面的 `createNewFlight` 函数被包装在类 `MyFunctions` 中，你可以向该类添加一个构造函数，该构造函数接受一个带有默认值的供应函数（supplier）。更新后的函数（带供应函数）如下所示：

```typescript
import { Function, OntologyEditFunction, Edits } from "@foundry/functions-api";
import { Objects, ExampleDataFlight } from "@foundry/ontology-api";
import { Uuid } from "@foundry/functions-utils";

export class MyFunctions {
    constructor (private UuidSupplier: () => string = Uuid.random){} // this new constructor in the class takes a supplier

    @Edits(ExampleDataFlight)
    @OntologyEditFunction()
    public createNewFlightWithConstructor(): void {
        Objects.create().exampleDataFlight(this.UuidSupplier());
    }
}
```

这个更新后的函数可以在完全控制输出的情况下进行测试（在本例中，我们将生成的 `Uuid` 设置为 `my-other-uuid`）：

```typescript
import { MyFunctions } from ".."

import { Objects , ExampleDataFlight } from "@foundry/ontology-api";
import { verifyOntologyEditFunction } from "@foundry/functions-testing-lib";
import { Uuid } from "@foundry/functions-utils";

describe("example test suite", () => {
    const myFunctions = new MyFunctions();

    test("creates new flight with supplier", () => {
        const myNewFunctions = new MyFunctions(() => "my-other-uuid");

        verifyOntologyEditFunction(() => myNewFunctions.createNewFlightWithConstructor())
            .createsObject({
                objectType: ExampleDataFlight,
                properties: {
                    flightId: "my-other-uuid"
                }
            })

    })
});
```

### Timestamp.now() 函数

你可以通过注入模拟来指定 `Timestamp.now()` 的输出。下面是一个示例：

```typescript
import { MyFunctions } from ".."

import { Objects , ExampleDataFlight } from "@foundry/ontology-api";
import { verifyOntologyEditFunction } from "@foundry/functions-testing-lib";
import { Timestamp } from "@foundry/functions-api";

describe("example test suite", () => {
    const myFunctions = new MyFunctions();

    test("test timestamp now", () => {
        const makeTimestamp = () => Timestamp.fromISOString("2018-06-13T12:11:13+05:00");
        jest.spyOn(Timestamp, "now").mockImplementation(() => makeTimestamp());

        const flight = Objects.create().exampleDataFlight("flightAnotherTest");
        verifyOntologyEditFunction(() => myFunctions.startTakeoff(flight))
            .modifiesObject({
                object: flight,
                properties: {
                    takeoff: makeTimestamp()
                }
            })
    })
});
```

这可用于测试以下函数：

```typescript
import { Function, OntologyEditFunction, Edits, Timestamp } from "@foundry/functions-api";
import { Objects, ExampleDataFlight } from "@foundry/ontology-api";

export class MyFunctions {
    @Edits(ExampleDataFlight)
    @OntologyEditFunction()
    public startTakeoff(flight: ExampleDataFlight): void {
        flight.takeoff = Timestamp.now();
    }
}
```

### LocalDate.now() 函数

你可以通过注入模拟来指定 `LocalDate.now()` 的输出。下面是一个示例：

```typescript
import { MyFunctions } from ".."

import { Objects , ExampleDataFlight } from "@foundry/ontology-api";
import { verifyOntologyEditFunction } from "@foundry/functions-testing-lib";
import { LocalDate } from "@foundry/functions-api";

describe("example test suite", () => {
    const myFunctions = new MyFunctions();

    test("test LocalDate now", () => {
        const makeLocalDate = () => LocalDate.fromISOString("2018-06-13");
        jest.spyOn(LocalDate, "now").mockImplementation(() => makeLocalDate());

        const flight = Objects.create().exampleDataFlight("flightTest");
        verifyOntologyEditFunction(() => myFunctions.dateTakeoff(flight))
            .modifiesObject({
                object: flight,
                properties: {
                    date: makeLocalDate()
                }
            })
    })
});
```

这可用于测试以下函数：

```typescript
import { Function, OntologyEditFunction, Edits, LocalDate } from "@foundry/functions-api";
import { Objects, ExampleDataFlight } from "@foundry/ontology-api";

export class MyFunctions {
    @Edits(ExampleDataFlight)
    @OntologyEditFunction()
    public dateTakeoff(flight: ExampleDataFlight): void {
        flight.date = LocalDate.now();
    }
}
```

---

*原文：[Mock dates, timestamps, and UUIDs](https://www.palantir.com/docs/foundry/functions/unit-test-dates-timestamps/)*
