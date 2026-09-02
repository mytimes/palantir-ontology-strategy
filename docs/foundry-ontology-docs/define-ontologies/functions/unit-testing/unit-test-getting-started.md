# 入门（Getting started）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/unit-test-getting-started/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

函数内置了对 [Jest ↗](https://jestjs.io/) 单元测试的支持。按照本指南中的步骤为你的仓库设置单元测试工具。

默认情况下，函数包含一个位于测试文件 `functions-typescript/src/__tests__/index.ts` 中的单元测试。你可以在 `__tests__` 文件夹中的任何位置创建测试文件。

## 示例

例如，我们可能想要测试 `functions-typescript/src/index.ts` 中的以下函数 `addOne`：

```typescript
import { Function, Integer } from "@foundry/functions-api";

export class MyFunctions {

    @Function()
    public addOne(n: Integer): Integer {
         return n + 1;
    }
}
```

我们可以通过编写以下测试 `test add one` 来测试函数 `addOne`：

```typescript
import { MyFunctions } from ".."

describe("example test suite", () => {
    const myFunctions = new MyFunctions();
    test("test add one", () => {
        expect(myFunctions.addOne(42)).toEqual(43);
    });
});
```

有关可供你使用的完整测试 API 的详情，请参阅 [Jest API ↗](https://jestjs.io/docs/en/api)。

## 运行测试

你可以通过点击右上角的 `Test`（测试）按钮来运行所有测试，或者通过点击每个测试行号旁边的三角形“播放”按钮来运行每个单独的测试。

![运行测试按钮](https://www.palantir.com/docs/resources/foundry/functions/button-run-tests.png)

当你点击 **Commit（提交）** 时，所有测试也将在 Checks（检查）中运行：

![运行测试](https://www.palantir.com/docs/resources/foundry/functions/run-tests.png?width=500)

## 后续步骤

接下来，了解可用于测试与本体交互的函数的一系列选项：

- [创建桩对象](https://www.palantir.com/docs/foundry/functions/unit-test-stub-objects/)
- [验证本体编辑](https://www.palantir.com/docs/foundry/functions/unit-test-ontology-edits/)
- [桩对象搜索与聚合](https://www.palantir.com/docs/foundry/functions/unit-test-object-searches/)

---

*原文：[Getting started](https://www.palantir.com/docs/foundry/functions/unit-test-getting-started/)*
