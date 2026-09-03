# 函数中的 Webhook（Webhooks in functions）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/webhooks/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

> **⚠️ 注意**
>
> 以下文档专门针对 TypeScript v1 函数。若需要更[强大的能力](https://www.palantir.com/docs/foundry/functions/language-feature-support/#typescript-v1-vs-typescript-v2)，包括对 Ontology SDK 和可配置资源请求的支持，我们建议[迁移到 TypeScript v2](https://www.palantir.com/docs/foundry/functions/typescript-v2-migration/)。

> **ℹ️ 提示**
>
> Webhook 可以作为一等公民级的函数发布。这意味着你可以从 Workshop、Ontology SDK、Actions 和其他函数中调用它们。若要改为将 Webhook 作为函数发布，请参阅 [Webhook 函数](https://www.palantir.com/docs/foundry/data-connection/webhooks-reference/#webhook-functions)。

本指南将引导你设置一个能够使用 [Webhook](https://www.palantir.com/docs/foundry/data-connection/webhooks-overview/) 向外部系统发起请求的函数。

> **ℹ️ 提示：前提条件**
>
> 本指南假设你已经创建了数据连接源和 Webhook。有关更多信息，请参阅[关于如何创建数据连接源和 Webhook 的文档](https://www.palantir.com/docs/foundry/data-connection/external-functions/)。

支持由出站应用（outbound application）支撑的 Webhook。有关更多信息，请参阅出站应用配置页面中的[支持的工作流](https://www.palantir.com/docs/foundry/administration/configure-outbound-applications/#supported-workflows)部分。

## 将源导入函数仓库

在遵循本指南之前，请确保你已经创建了函数仓库，并理解如何按照[我们的教程](https://www.palantir.com/docs/foundry/functions/getting-started/)中所述编写和发布函数。

你必须首先启用要导入到 Code Repositories 的源。为此，请转到你的 REST API 源的 **Enable code imports（启用代码导入）** 菜单，并启用允许该源导入到 Code Repositories 的选项。由于无法在使用函数的所有工作流中执行导出时的标记（Marking）验证，你还必须在每个源的 **Enable exports（启用导出）** 菜单中，启用在不进行标记验证的情况下向该源导出。

![代码导入配置。](https://www.palantir.com/docs/resources/foundry/functions/source-code-import-configuration.png)

![导出配置。](https://www.palantir.com/docs/resources/foundry/functions/source-export-configuration.png)

接下来，要在函数中使用 Webhook，必须首先将 Webhook 的支撑 REST API 源导入到仓库中。选择[**资源导入**左侧面板](https://www.palantir.com/docs/foundry/functions/resource-imports-sidebar/)以查看导入到仓库中的源。选择 **Add > Sources（添加 > 源）** 以显示一个搜索对话框，你可以在其中选择要导入的源。只有具有 API 名称的源才能通过此对话框导入。

![外部函数源导入模态框，显示已选择要导入的特定源](https://www.palantir.com/docs/resources/foundry/functions/external-functions-source-import-modal.png)

> **ℹ️ 提示**
>
> 为使用 Webhook 而将源导入函数仓库的工作方式，与将源导入 Python 转换仓库和计算模块的方式不同。仅将给定源用于 Webhook 的函数仓库*不会*出现在源概览页面的仓库列表中。任何对源拥有 `Viewer`（查看者）访问权限的用户都能够在外部函数中导入和使用这些 Webhook。

## 在函数中使用 Webhook

将 REST API 源导入函数仓库后，它将在 TypeScript 环境中可用，并可通过该源的命名空间访问：

```typescript
import { Function } from "@foundry/functions-api";
import { MyDictionarySource } from "@foundry/external-systems/sources";
```

如果你收到错误 `Cannot find module '@foundry/external-systems' or its corresponding type declarations.`，请确保 `functions-typescript/functions.json` 文件中的 `enableExternalSystems` 值设置为 `true`。更新并提交更改后，系统应会安装必要的包，包括 `@foundry/external-systems`。

![外部函数导入错误消息。](https://www.palantir.com/docs/resources/foundry/functions/external-functions-import-error-message.png)

### 示例：从一个函数发起多次调用

在下面的示例中，我们将说明如何使用单个函数向字典 API 发起多次调用。

如果你的函数不进行任何本体编辑，你将创建一个 `@Query()` 函数。如果你想进行本体编辑，则需要改用 `@OntologyEditFunction` 装饰器。在我们的[文档](https://www.palantir.com/docs/foundry/functions/api-ontology-edits/)中详细了解如何通过函数进行本体编辑。

使用标准的 [TypeScript async/await 模式 ↗](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-1-7.html#asyncawait-support-in-es6-targets-node-v4)，可以从一个函数同时发起多个 Webhook 调用。使用从 `@foundry/functions-api` 导出的 `isOk` 辅助函数检查调用是否成功。

以下函数接受一个单词列表（TypeScript 字符串数组），并为每个单词发起一次调用：

```typescript
import { OntologyEditFunction, isOk } from "@foundry/functions-api";
import { MyDictionarySource } from "@foundry/external-systems/sources";

export class MyFunctions {

    @OntologyEditFunction()
    public async defineWords(words: string[]): Promise<void> {

        const results = await Promise.all(words.map(word => MyDictionarySource.webhooks.GetDefinition.call({
            wordToDefine: word
        })));

        results.forEach((result, i) => {
            if (isOk(result)) {
                const output = result.value.output;
                output.dictionary_definitions.forEach(definitions_for_word => {
                    definitions_for_word.meanings.forEach(meaning => {
                        meaning.definitions.forEach(def_for_part_of_speech => {
                            console.log(`Found a ${meaning.partOfSpeech} definition for "${words[i]}": ${def_for_part_of_speech.definition}`);
                        })
                    })
                });
            }
        });
    }
}
```

输入 `["tuba", "cool"]` 的日志输出：

```
LOG [2023-07-28T03:16:22.968Z] Found a noun definition for "tuba": A large brass musical instrument, usually in the bass range, played through a vibration of the lips upon the mouthpiece and fingering of the keys.
LOG [2023-07-28T03:16:22.968Z] Found a noun definition for "tuba": A type of Roman military trumpet, distinct from the modern tuba.
LOG [2023-07-28T03:16:22.968Z] Found a noun definition for "tuba": A large reed stop in organs.
LOG [2023-07-28T03:16:22.968Z] Found a noun definition for "tuba": A Malayan plant whose roots are a significant source of rotenone, Derris malaccensis.
LOG [2023-07-28T03:16:22.968Z] Found a noun definition for "tuba": A reddish palm wine made from coconut or nipa sap.
LOG [2023-07-28T03:16:22.968Z] Found a noun definition for "tuba": A tube or tubular organ.
LOG [2023-07-28T03:16:22.968Z] Found a noun definition for "cool": A moderate or refreshing state of cold; moderate temperature of the air between hot and cold; coolness.
LOG [2023-07-28T03:16:22.968Z] Found a noun definition for "cool": A calm temperament.
```

## 错误处理

为了帮助缓解在使用联网系统时出现的故障，函数通过 Result 对象公开从 Webhook 传播的错误，这些对象提供了关于所发生错误类型的信息：

```typescript
import { OntologyEditFunction, isOk } from "@foundry/functions-api";
import { MyDictionarySource } from "@foundry/external-systems/sources";

export class MyFunctions {

    @OntologyEditFunction()
    public async defineWords(words: string[]): Promise<void> {

        const results = await Promise.all(words.map(word => MyDictionarySource.webhooks.GetDefinition.call({
            wordToDefine: word
        })));

        results.forEach((result, i) => {
            if (isOk(result)) {
                // Extract the response
            } else {
                const errorName = result.error.name;

                if (errorName === "WebhookExecutionFailedToStart") {
                    console.log("We were unable to initiate a request to the dictionary API.");
                } else if (errorName === "ParsingResponseFailed") {
                    console.log("The external request succeeded, but the response couldn't be parsed.");
                } else {
                    console.log("Something went wrong.");
                }
            }
        });
    }
}
```

在处理错误时，编写的代码应监听特定的名称并做出相应反应。函数目前返回以下错误：

| 错误 | 描述 |
| --- | --- |
| `WebhookExecutionFailedToStart` | Webhook 启动失败。如果返回此错误，可以有把握地认为没有向外部系统发出任何请求。 |
| `WebhookExecutionTimedOut` | Webhook 执行已开始，但在配置的 Webhook 时间限制内未收到来自外部系统的响应。 |
| `RemoteRestApiReturnedError` | 外部系统返回了错误。仅针对在 REST API 源上配置的 Webhook 返回。 |
| `RemoteApiReturnedError` | 外部系统返回了错误。仅针对在非 REST API 源上配置的 Webhook 返回。 |
| `ParsingResponseFailed` | Webhook 执行成功，但无法成功解析来自外部系统的响应。例如，当来自外部系统的响应不包含预期字段时，可能会发生这种情况。由于 Webhook 调用的结果不一定会被使用，因此是否应将其标记为对最终用户的失败，取决于应用构建者。 |
| `ServerError` | Webhook 服务或连接器内部发生问题。 |
| `UnknownError` | 发生了无法直接归因于任何 Foundry 服务的错误。 |

此错误类型列表可能会发生变化；用户应组织其代码，以便在函数执行器返回带有新名称的错误时包含一个默认分支。

### 示例：在从单个函数发起多个 Webhook 调用时处理错误

以下代码描述了如何处理多个 Webhook 调用，其中一些调用在同一函数内成功而另一些失败。在我们的示例中，当字典服务器找不到给定单词的定义时，会返回 `RemoteRestApiReturnedError`。

```typescript
import { OntologyEditFunction, isOk } from "@foundry/functions-api";
import { MyDictionarySource } from "@foundry/external-systems/sources";

export class MyFunctions {

    @OntologyEditFunction()
    public async defineWords(words: string[]): Promise<void> {

        const results = await Promise.all(words.map(word => MyDictionarySource.webhooks.GetDefinition.call({
            wordToDefine: word
        })));

        results.forEach((result, i) => {
            if (isOk(result)) {
                const output = result.value.output;
                output.dictionary_definitions.forEach(definitions_for_word => {
                    definitions_for_word.meanings.forEach(meaning => {
                        meaning.definitions.forEach(def_for_part_of_speech => {
                            console.log(`Found a ${meaning.partOfSpeech} definition for "${words[i]}": ${def_for_part_of_speech.definition}`);
                        })
                    })
                });
            } else {
                if (result.error.name === "RemoteRestApiReturnedError") {
                    console.log(`ERROR: ${words[i]} could not be defined`, result.error.message);
                }
            }
        });
    }
}
```

向上述函数输入 `["asdf", "shire"]` 将返回以下结果：

```
LOG [2023-07-28T15:38:47.263Z] ERROR: asdf could not be defined Request returned an unsuccessful response code: 404 Response body: {"title":"No Definitions Found","message":"Sorry pal, we couldn't find definitions for the word you were looking for.","resolution":"You can try the search again at later time or head to the web instead."}
LOG [2023-07-28T15:38:47.264Z] Found a noun definition for "shire": Physical area administered by a sheriff.
LOG [2023-07-28T15:38:47.264Z] Found a noun definition for "shire": Former administrative area of Britain; a county.
LOG [2023-07-28T15:38:47.264Z] Found a noun definition for "shire": The general area in which a person lives or comes from, used in the context of travel within the United Kingdom.
LOG [2023-07-28T15:38:47.264Z] Found a noun definition for "shire": A rural or outer suburban local government area of Australia.
LOG [2023-07-28T15:38:47.264Z] Found a noun definition for "shire": A shire horse.
LOG [2023-07-28T15:38:47.264Z] Found a verb definition for "shire": To (re)constitute as one or more shires or counties.
```

## 限制

目前，对本体编辑函数内可以发起的请求数量没有限制，但现有的[函数资源限制](https://www.palantir.com/docs/foundry/functions/manage-functions/#enforced-limits)仍然适用。[Webhook 限制](https://www.palantir.com/docs/foundry/data-connection/webhooks-reference/#limits)也会被强制执行。

函数目前支持具有以下输入和输出类型的 Webhook：

- 附件（Attachments）
- 布尔值（Booleans）
- 整数（Integers）
- 长整数（Longs）
- 双精度浮点数（Doubles）
- 字符串（Strings）
- 可选类型（Optionals）
- 日期（Dates）
- 时间戳（Timestamps）
- 列表（Lists）
- 枚举（Enums，允许的 String 类型值列表）
- 带有或不带有预期字段的记录（Records）

当从 `@Query` 函数调用 Webhook 时，该 Webhook 必须仅执行不会更改外部系统的 `Read API`（读 API）调用。查询函数经常被重试，或在页面加载时被静默执行，因此无法提供 `@OntologyEditFunction` 所能实现的同等级别、结构化且有意为之的执行。在配置 Webhook 时，你可以使用 `Read API` 或 `Write API` 选项来指定它从查询函数中执行是否安全。

### 不支持的 Webhook 功能

- 目前不支持将 `OR` 类型用作输入或输出参数的 Webhook。不会为这些 Webhook 生成任何代码。

## 处理函数和 Webhook 中的版本变化

函数和 Webhook 都有版本，调用者可以调用函数或 Webhook 的任何版本。函数发布时，当时可用的最新 Webhook 版本将被固定（pin）到该函数。

当在 [Code Repositories](https://www.palantir.com/docs/foundry/code-repositories/overview/) 应用中打开函数仓库时，用于自动补全的生成代码绑定将始终使用最新版本的 Webhook。此 Webhook 版本显示在左侧的**资源导入**侧边面板中。

![函数仓库中的资源导入侧边面板，显示一个带有单个 Webhook 的源。](https://www.palantir.com/docs/resources/foundry/functions/external-functions-import-sidebar.png)

> **⚠️ 注意**
>
> 在发布依赖于你的 Webhook 功能的函数之前，请确保该 Webhook 是稳定的。

当对 Webhook 或函数进行更改时，请记住重新发布函数并让用户升级到新版本。先前发布的、被固定的函数版本仍然可以使用。

## 权限

下表总结了编写、发布和使用外部函数所需的权限。

| 操作 | 用户 | 所需权限 |
| --- | --- | --- |
| 将 Webhook 导入函数仓库 | 函数编辑者 | 对 Webhook 拥有 `webhooks:editor` 权限，该权限授予给默认角色 **Editor（编辑者）**。 |
| 发布调用 Webhook 的函数 | 函数编辑者 | 对源拥有 `webhooks:execute` 权限，该权限仅授予给默认角色 **Owner（所有者）** 和 **Editor（编辑者）**。 |
| 配置动作以使用调用 Webhook 的 `@OntologyEditFunction()` | 动作编辑者 | 对 Webhook 拥有 `webhooks:grant-action-validated-execution` 权限，并对函数拥有 `Viewer`（查看者）权限 |
| 从 Workshop 执行 `@Query()` Webhook | 最终用户 | 对源拥有 `webhooks:execute` 权限，该权限仅授予给默认角色 **Owner（所有者）** 和 **Editor（编辑者）**。 |
| 从动作执行 `@OntologyEditFunction()` | 最终用户 | 用户必须满足该动作的[提交条件](https://www.palantir.com/docs/foundry/action-types/submission-criteria/)。在这种情况下，不会检查对源、Webhook 或函数的任何权限。创建和管理动作的用户必须确保适当地配置了提交条件。 |

## 监控、排查与调试

使用以下平台工具来更深入地了解函数中的 Webhook 执行情况：

- [Webhook 执行历史](https://www.palantir.com/docs/foundry/data-connection/webhooks-reference/#webhook-history)：在 [Data Connection](https://www.palantir.com/docs/foundry/data-connection/overview/) 中查看单个 Webhook 时，可在 **History（历史）** 选项卡中找到。
- 函数使用历史可在 [Ontology Manager](https://www.palantir.com/docs/foundry/ontology-manager/overview/) 中查看，显示函数的执行历史，包括输入、输出以及触发该函数的用户。
- [函数指标](https://www.palantir.com/docs/foundry/functions/function-metrics/)，显示函数的近实时成功/失败计数和 P95 执行时长。
- [函数的代码编写预览](https://www.palantir.com/docs/foundry/functions/foo-getting-started/#test-in-live-preview)，提供性能分析、调试输出等功能。

## 最佳实践

在使用外部源从函数调用 Webhook 时，我们建议遵循以下最佳实践：

- 在尝试于函数中使用这些 Webhook 之前，先在 Data Connection 中使用[测试 Webhook 侧边面板](https://www.palantir.com/docs/foundry/data-connection/webhooks-setup/#test-the-webhook)对 Webhook 进行全面测试。
- 尽可能使用带有预期字段的 Webhook 输入和输出 `record` 类型参数。使用显式类型而非 JSON，意味着函数代码不太可能抛出意外的运行时错误。
- 使用从 `@foundry/functions-api` 导出的内置函数 `isOk` 和 `isErr` 来检查成功和错误状态，并通过 `name` 字段缩小错误类型的范围。
- 如果用户要在单个函数调用中同时向外部系统和本体写入数据，请记住，即使对外部系统的写入成功，对本体的写入也可能失败。请确保已制定措施来处理这种不一致，并在需要时让用户能够看到他们对两个系统所做修改的状态。

---

*原文：[Webhooks in functions](https://www.palantir.com/docs/foundry/functions/webhooks/)*
