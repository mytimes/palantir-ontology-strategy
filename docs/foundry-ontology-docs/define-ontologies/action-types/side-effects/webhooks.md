# Webhook（Webhooks）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/action-types/webhooks/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

[Webhook](https://www.palantir.com/docs/foundry/data-connection/webhooks-overview/) 是 Data Connection 中的一个概念，它支持向外部系统（如 Salesforce、SAP 或任何已配置的 HTTP 服务器）发送请求，通常用于修改该外部系统中的数据。

通过设置 Webhook 并将其配置为在动作中使用，当最终用户在 Foundry 中应用动作时，你可以向外部系统发送数据。这使得 Foundry 中的工作流能够直接与源系统连接，并将数据和决策回写到这些系统中。

本节详细介绍在动作中配置 Webhook 的各种选项。有关分步教程，请参阅[如何在动作中添加 Webhook](https://www.palantir.com/docs/foundry/action-types/set-up-webhook/) 的文档。

## Webhook：回写与副作用

Webhook 在动作中可以通过两种方式配置：作为**回写（writeback）**或作为**副作用（side effect）**。

![添加 Webhook](https://www.palantir.com/docs/resources/foundry/action-types/webhooks-add-webhook.png?width=400)

为方便参考，下表比较了回写 Webhook 和副作用 Webhook 的行为。

| 类型 | 应用时机 | 是否向最终用户显示失败？ | 时序 |
| --- | --- | --- | --- |
| **回写** | 对象变更之前 | 是 | 在用户看到成功或失败之前 |
| **副作用** | 对象变更之后 | 否 | 可能在用户看到成功消息之后 |

以下各节将更详细地介绍回写 Webhook 和副作用 Webhook。

### 回写 Webhook

当配置为**回写**时，Webhook 将在评估任何其他规则*之前*执行；如果 Webhook 执行失败，则不会进行任何其他更改。如果你希望确保 Foundry 不会先于外部系统做出更改，应将 Webhook 设置为回写。

这种行为在 Foundry 与外部系统之间实现了一定程度的事务性。使用回写 Webhook 可以保证：如果向外部系统发出的请求失败，则不会对 Foundry 本体应用任何更改。不过，仍有可能出现外部请求成功但本体更改失败的情况。

由于回写 Webhook 失败时动作会停止应用，因此你只能将一个 Webhook 配置为回写。如果在应用动作时该 Webhook 失败，将向最终用户显示描述该失败的错误。

当 Webhook 配置为回写时，其输出参数可在后续规则中使用。更多详情请参见下文的[输出参数](#输出参数)一节。

### 副作用 Webhook

当配置为**副作用**时，Webhook 将在评估其他规则*之后*执行。这意味着对 Foundry 对象的修改会在副作用应用之前发生。你可以在单个动作中配置多个副作用 Webhook，它们的执行没有特定顺序。在包含副作用 Webhook 的动作中，最终用户会在 Foundry 对象被修改后看到成功消息；副作用的执行可能发生在成功消息显示之后。

如果你需要从单个动作中多次调用某个 Webhook，可以通过副作用 Webhook 实现：提供一个负载列表作为输入。这将按照所提供列表中的负载数量触发相应次数的 Webhook，且处理顺序没有保证。相关示例可在下文的[输入参数](#输入参数)一节中找到。

当你希望发送尽力而为（best-effort）的通知，或向多个外部系统回写数据时，应使用副作用 Webhook。

## 输入参数

要在动作中配置 Webhook，你必须填充其所有必填输入参数。有关 Webhook 输入参数的通用参考资料，请参阅 [Data Connection 文档](https://www.palantir.com/docs/foundry/data-connection/webhooks-reference/#input-parameters)。

配置 Webhook 输入参数有两种方式：映射到动作参数，或使用函数。

映射到**动作参数**时，每个必填的 Webhook 输入必须设置为同类型的动作参数、静态值，或对象参数的某个属性。

![输入参数](https://www.palantir.com/docs/resources/foundry/action-types/webhooks-input-parameters.png?width=400)

使用[函数](https://www.palantir.com/docs/foundry/functions/overview/)时，你必须选择一个返回自定义类型的函数，该类型需包含所有必填的 Webhook 输入参数并与 Webhook 类型强匹配，否则你将收到 `OntologyMetadata:ActionWebhookInputsDoNotHaveExpectedType` 错误。当你希望使用逻辑来填充输入（尤其是该逻辑基于本体对象时），使用函数填充 Webhook 输入参数会很有用。例如，你可以检索链接的对象，并从这些对象中提取属性值来预填充 Webhook 输入。

举个例子，假设你有一个 Webhook，它接收三个 ID 分别为 `name`、`industry` 和 `country` 的输入参数：

![输入参数示例](https://www.palantir.com/docs/resources/foundry/action-types/webhooks-input-parameters-example.png?width=400)

你可以编写一个函数，返回具有相同结构的自定义接口：

```typescript
export interface MyWebhookInput {
    name: string;
    industry: string;
    country: string;
}
```

然后，在动作中配置 Webhook 输入时，你可以选择此函数，将动作参数映射到该函数所需的参数：

![将动作参数映射到函数所需的参数](https://www.palantir.com/docs/resources/foundry/action-types/webhooks-input-parameters-define-using-action.png?width=400)

下面是一个完整的函数代码示例，该函数从本体对象加载数据，并用其填充 Webhook 输入。

```typescript
import { Function, UserFacingError } from "@foundry/functions-api";
import { Company } from "@foundry/ontology-api";

export interface MyWebhookInput {
    name: string;
    industry: string;
    country: string;
}

export class MyWebhookFunctions {
    @Function()
    public returnWebhookInput(company: Company): MyWebhookInput {
        if (!company.name || !company.industry || !company.country) {
            throw new UserFacingError("Some required fields are not set.");
        }
        return {
            name: company.name,
            industry: company.industry,
            country: company.country,
        }
    }
}
```

副作用 Webhook 可以通过函数返回负载列表而被多次调用。下面是一个示例函数，它接收两个公司作为输入，并返回一个包含两个负载的列表，这些负载与 Webhook 期望的输入参数相匹配。如果在动作中使用此函数为副作用 Webhook 返回输入，将导致两次独立的 Webhook 执行。

```typescript
import { Function } from "@foundry/functions-api";
import { Company } from "@foundry/ontology-api";

export interface MyWebhookInput {
    arg1: string;
    arg2: string;
}

export class MyFunctions {
    @Function()
    public createWebhookRequest(company1: Company, company2: Company): MyWebhookInput[] {
        return [
        {
           arg1: company1.someProperty,
           arg2: company1.someOtherProperty,
        },
        {
           arg1: company2.someProperty,
           arg2: company2.someOtherProperty,
        }
        ];
    }
}
```

## 输出参数

当 Webhook 配置为[回写 Webhook](#回写-webhook)时，你可以在后续规则中使用其输出参数。当你希望将外部系统返回的数据立即写入 Foundry 对象，或在后续的[通知](https://www.palantir.com/docs/foundry/action-types/notifications/)或[副作用 Webhook](#副作用-webhook)中使用时，这非常有用。

有关 Webhook 输出参数的通用参考资料，请参阅 [Data Connection 文档](https://www.palantir.com/docs/foundry/data-connection/webhooks-reference/#output-parameters)。

要在后续逻辑规则中使用输出参数，请在为逻辑规则填充值时选择 **回写响应（Writeback response）**，然后选择你希望使用的具体输出：

![在逻辑规则中使用输出参数](https://www.palantir.com/docs/resources/foundry/action-types/webhooks-output-parameters-in-logic-rule.png?width=400)

## OAuth 2.0 认证

当 Webhook 配置在使用[出站应用](https://www.palantir.com/docs/foundry/administration/configure-outbound-applications/)进行认证的 REST API 数据源上时，Foundry 会代表用户管理 OAuth 2.0 授权流程。开发者无需处理令牌的获取或刷新。Foundry 会在每次 Webhook 调用时传递正确的访问令牌。

有关 Foundry 各工作流中 OAuth 2.0 出站应用支持的完整概览，请参阅 [OAuth 2.0 出站应用](https://www.palantir.com/docs/foundry/administration/configure-outbound-applications/)文档。

---

*原文：[Webhooks](https://www.palantir.com/docs/foundry/action-types/webhooks/)*
