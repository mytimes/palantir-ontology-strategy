# 为新对象生成唯一 ID（Generate unique IDs for new objects）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/edits-generate-id/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

在编写创建对象的[本体编辑函数](https://www.palantir.com/docs/foundry/functions/edits-overview/)时，你可能希望为新创建的对象生成唯一 ID。

## TypeScript v2

TypeScript v2 没有内置或平台特定的 UUID 生成器。请使用标准 UUID 库，例如来自 npm 的 [`uuid` 包 ↗](https://www.npmjs.com/package/uuid)。

### 安装包

将 `uuid` 包添加到代码仓库的依赖中：

```bash
npm install uuid
npm install --save-dev @types/uuid
```

### 在代码中使用包

```typescript
import { v4 as uuidv4 } from 'uuid';

export default function createFlightScenario(): void {
    const uniqueId = uuidv4();
    // Use uniqueId as the primary key for your new object
}
```

## TypeScript v1

> **⚠️ 注意**
>
> 以下文档专门针对 TypeScript v1 函数。如需更[强大的能力](https://www.palantir.com/docs/foundry/functions/language-feature-support/#typescript-v1-vs-typescript-v2)，包括对 Ontology SDK 的支持和可配置的资源请求，我们建议[迁移到 TypeScript v2](https://www.palantir.com/docs/foundry/functions/typescript-v2-migration/)。

在 TypeScript v1 函数中，使用 `@foundry/functions-utils` 包生成全局唯一标识符。

### 导入包

`@foundry/functions-utils` 包默认已安装，但如果 `package.json` 文件中没有该包：

- 在 `"dependencies"` 部分，添加 `"@foundry/functions-utils": "0.1.0"`

如[添加依赖的文档](https://www.palantir.com/docs/foundry/functions/add-dependencies/)中所述，请记住重启 Code Assist 以使新包可用于自动补全。

### 在代码中使用包

要生成唯一 ID，你可以使用 `@foundry/functions-utils` 包中的 `Uuid.random()` 工具函数。以下代码示例展示了如何在示例本体编辑函数中使用 `random` 函数。

```typescript
import { OntologyEditFunction, Timestamp } from "@foundry/functions-api";
import { Objects } from "@foundry/ontology-api";
import { Uuid } from "@foundry/functions-utils";

export class ExampleEditFunctions {
    @Edits(FlightScenario)
    @OntologyEditFunction()
    public createFlightScenario(): void {
        const scenario = Objects.create().flightScenarios(Uuid.random());
        scenario.scenarioName = "New scenario";
        scenario.creationTime = Timestamp.now();
    }
}
```

---

*原文：[Generate unique IDs for new objects](https://www.palantir.com/docs/foundry/functions/edits-generate-id/)*
