# 函数中的埋点与遥测（Instrumentation and telemetry in functions）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/instrumentation-telemetry/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

你可以从函数中发出某些类型的遥测数据，以便对生产工作流进行监控和调试。

要了解如何查看函数发出的遥测数据，请参阅我们的[本体与 AIP 可观测性文档](https://www.palantir.com/docs/foundry/aip-observability/overview/)。

## 支持的遥测类型

下表概览了每种函数语言支持的遥测类型。请注意，对于所有函数类型，都会自动创建一个覆盖函数总执行时长的 span（跨度），以及一条请求日志。

| 语言 | 日志 | Span | 指标 |
| --- | --- | --- | --- |
| TypeScript v1 | 支持 | 仅产品定义的 span[1] | 仅产品定义的指标[2] |
| TypeScript v2 | 支持 | 支持[3] | 仅产品定义的指标[2] |
| Python | 支持 | 支持[3] | 仅产品定义的指标[2] |

[1] TypeScript v1 函数中产品定义的 span 包括对象加载、查询执行等操作。

[2] Foundry 会记录所有类型函数的总执行时长。

[3] TypeScript v2 和 Python 函数会自动为所有出站网络请求埋点，但也可以添加自定义 span。

### 日志

你可以从函数中发出自定义日志，并在事后查看。以下示例演示了如何从 TypeScript v1、TypeScript v2 和 Python 函数中发出日志。

在 TypeScript v2 函数中，Foundry 会设置 OpenTelemetry SDK 的全局 logger provider，你可以从中获取 logger。如果你想使用第三方库进行日志记录，必须将它们配置为通过从全局 logger provider 获取的 logger 发出日志。

**TypeScript v1**

```typescript
export class MyFunctions {
    @Function()
    public myFunction(name: string): string {
        console.log(`This is a custom log line, ${name}.`);
        return `Hello, ${name}!`;
    }
}
```

**TypeScript v2**

```typescript
import { logs } from "@opentelemetry/api-logs";

const logger = logs.getLogger("my-function");

export default function myFunction(name: string): string {
    logger.emit({
        body: "This is a custom log line.",
        attributes: {
            name
        },
    });

    // You can also use the global console object
    console.log(`This is a custom log line, ${name}.`);

    return `Hello, ${name}!`;
}
```

**Python**

```python
import logging

from functions.api import function

logger = logging.getLogger(__name__)

@function
def my_function(name: str) -> str:
    logger.info("This is a custom log line.")
    return f"Hello, {name}!"
```

#### 日志记录最佳实践

请遵循以下日志记录实践：

- **选择合适的日志级别：**正常操作使用 `INFO`，可恢复的问题使用 `WARN`，失败使用 `ERROR`，详细诊断使用 `DEBUG`。
- **包含相关上下文：**添加标识符、计数和操作细节，以帮助理解执行流程。
- **避免记录敏感数据：**不要记录凭据、可能包含敏感信息的完整 API 响应，或其他安全敏感内容。

有关编写有效日志、查看服务日志以及按日志级别筛选的详细指导，请参阅[服务日志与调试](https://www.palantir.com/docs/foundry/aip-observability/service-logs-and-debugging/)。

### Span

你还可以在 TypeScript v2 和 Python 函数中创建自定义 span，以跟踪特定操作的耗时。以下示例演示了如何创建自定义 span。

在 TypeScript v2 和 Python 函数中，Foundry 会设置 OpenTelemetry SDK 的全局 tracer provider，你可以从中获取 tracer。如果你想使用第三方库进行链路追踪，必须将它们配置为通过从全局 tracer provider 获取的 tracer 发出追踪数据。

**TypeScript v2**

```typescript
import { trace } from "@opentelemetry/api";
import { Integer } from "@osdk/functions";

const tracer = trace.getTracer("my-function");

export default function sqrt(n: Integer): Integer {
    const sqrt = tracer.startActiveSpan("my-custom-span", (span) => {
        try {
            return Math.sqrt(n);
        } finally {
            span.end();
        }
    });

    return sqrt;
}
```

**Python**

```python
import math

from functions.api import function

from opentelemetry import trace

tracer = trace.get_tracer(__name__)

@function
def sqrt(n: int) -> int:
    with tracer.start_as_current_span("my-custom-span"):
        return math.sqrt(n)
```

---

*原文：[Instrumentation and telemetry in functions](https://www.palantir.com/docs/foundry/functions/instrumentation-telemetry/)*
