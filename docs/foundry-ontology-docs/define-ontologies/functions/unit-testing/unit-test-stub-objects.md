# 创建桩对象（Create stub objects）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/unit-test-stub-objects/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

你可以使用 `Objects.create()` 创建和定义你的模拟对象，其用法与普通函数完全相同。随后你可以在编写单元测试时使用这些模拟对象。下面是一个示例：

```typescript
import { MyFunctions } from ".."

import { Objects, ExampleDataAirport } from "@foundry/ontology-api";

describe("example test suite", () => {
    const myFunctions = new MyFunctions();

    test("test created objects", () => {
        const JFK = Objects.create().exampleDataAirport("JFK Test");
        JFK.displayAirportName = "John F. Kennedy International";
        expect(myFunctions.getAirportName(JFK)).toEqual("John F. Kennedy International");
    });
});
```

供参考，上面的示例使用了 Jest 语法 [`expect(...).toEqual(...)` ↗](https://jestjs.io/docs/expect#toequalvalue)。

---

*原文：[Create stub objects](https://www.palantir.com/docs/foundry/functions/unit-test-stub-objects/)*
