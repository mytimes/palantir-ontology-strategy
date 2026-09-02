# 函数接口（Function interfaces）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/function-interfaces/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

**函数接口（Function interfaces）**允许函数作者将其自定义逻辑与 Foundry 原生功能集成，并提供了一种在消费应用与函数之间定义契约的强大方式。

函数接口定义应用或用户应如何与函数交互，包括函数的输入、输出和错误。换句话说，函数接口描述函数的签名，但函数接口本身并不是函数，而是供函数实现的。

某些 Foundry 应用使用函数接口，在执行实现了该接口的函数时，根据已知的输入、输出和错误提供专门的行为。用户可以自行实现某些函数接口，而 Foundry 仍能继续提供这种专门行为。Foundry 中依赖某些函数接口的应用可以发现实现该接口的所有函数。

例如，AIP Logic 依赖函数接口来允许用户在 Logic 函数中使用自带的 LLM。具体而言，AIP Logic 中的 [Use LLM](https://www.palantir.com/docs/foundry/logic/blocks/#use-llm)（使用 LLM）块允许用户选择 Palantir 提供的 LLM 或*已注册的* LLM。已注册模型是用户编写的函数，它们实现了 Foundry 提供的函数接口，例如聊天补全（chat completion）函数接口。这使 AIP Logic 能够发现那些被明确定义为聊天补全实现、具有通用 LLM 典型签名、并返回 AIP Logic 可以适当处理的错误的函数。未来，用户提供的聊天补全实现将可用于平台的其他部分，例如 Pipeline Builder 和 Model Catalog（模型目录）。

[了解如何使用已注册模型功能注册 LLM。](https://www.palantir.com/docs/foundry/aip/bring-your-own-model/) 有关使用函数接口的旧版方法，请参阅[使用函数接口注册 LLM [Legacy]](https://www.palantir.com/docs/foundry/aip/chat-completion-function-interface-quickstart/)。

## Palantir 提供的函数接口

以下列表包含 Palantir 目前提供的函数接口：

- [`ChatCompletion`](#chatcompletion)

### `ChatCompletion`

**描述：**

- 根据多轮、多用户的文本对话历史生成上下文相关的文本响应的函数。
- 非常适合对话类用例。

**Foundry 集成：**

- AIP Logic 中的 *Use LLM* 面板。
- 即将支持 Pipeline Builder。

**文档：**

- [**使用函数接口注册 LLM [Legacy]**](https://www.palantir.com/docs/foundry/aip/chat-completion-function-interface-quickstart/)

## 类型自定义

为了提供更大的灵活性，在实现函数接口时，你不限于使用所提供的类型。在某些情况下，你可能希望创建自己的[自定义类型](https://www.palantir.com/docs/foundry/functions/types-reference/#structcustom-type)。只要函数与函数接口上定义的函数[兼容 ↗](https://www.typescriptlang.org/docs/handbook/type-compatibility.html#comparing-two-functions)，该函数就会被编译器接受并成功发布。如果函数接口所定义输入类型的所有字段都是可选的，则自定义该类型时双方必须至少共享一个共同的可选字段。

```typescript
...
interface CustomParams extends GenericCompletionParams {
   modelSpecificParam?: string
}
...

// valid implementation
@ChatCompletion()
public async myRegisteredModel(
    messages: FunctionsGenericChatCompletionRequestMessages,
    params: CustomParams
): Promise<FunctionsGenericChatCompletionResponse> {
  ...
}
```

## 故障排查

函数接口设计灵活，支持多种多样的实现方式。但是，在实现函数接口时你可能会遇到错误。以下是针对 TypeScript 函数的一些提示，可帮助你在自定义实现时避免这些错误。

### 错误：`Function input name does not match the required input name of the function interface at the specific input position`

每个参数的输入名称必须与函数接口在每个特定输入位置定义的输入名称匹配。正如 lint 提示所建议的，确保每个输入名称与函数接口在每个位置声明的输入名称完全相同。

![常见错误：输入名称不匹配。](https://www.palantir.com/docs/resources/foundry/functions/byom-tutorial-common-errors-input-params-not-match.png)

### 错误：`Function is missing input parameter of the function interface`

如果实现函数未包含函数接口上定义的每个必需输入，就会出现此错误。要解决该错误，请确保函数接口上声明的每个输入都包含在实现函数中。

![常见错误：未包含必需的输入。](https://www.palantir.com/docs/resources/foundry/functions/byom-tutorial-common-errors-missing-input-params.png)

### 错误：`Type {type1} is not assignable to type {type2}`

编译器可能会拒绝实现函数，认为它与接口上定义的函数不兼容。如果是这样，请通过对照函数接口上定义的类型检查每个类型的结构，确保你的实现函数与函数接口上定义的函数[兼容 ↗](https://www.typescriptlang.org/docs/handbook/type-compatibility.html#comparing-two-functions)。

![常见错误：函数与聊天补全装饰器不兼容。](https://www.palantir.com/docs/resources/foundry/functions/byom-tutorial-common-errors-function-not-compatible-with-interface.png)

---

*原文：[Function interfaces](https://www.palantir.com/docs/foundry/functions/function-interfaces/)*
