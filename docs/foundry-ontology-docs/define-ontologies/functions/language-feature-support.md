# 各语言的功能支持（Feature support by language）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/language-feature-support/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

并非所有功能都受所有语言支持。各语言的功能支持请参阅下表。

| 各语言的函数能力 | AIP Logic | TypeScript v1 | TypeScript v2 | Python | 描述 |
| --- | --- | --- | --- | --- | --- |
| 本体对象支持 | 是 | 是 | 是 | 是 | 在函数中[访问本体对象](https://www.palantir.com/docs/foundry/functions/foo-getting-started/)的能力。 |
| 本体接口支持 | 否 | 否 | 是 | 否 | 在函数中访问和编辑本体接口的能力。 |
| 本体编辑支持 | 是 | 是 | 是 | 是 | 在函数中[编辑本体对象](https://www.palantir.com/docs/foundry/functions/edits-overview/)的能力。 |
| 媒体处理支持 | 否 | 是 | 部分 | 部分 | 与[媒体项](https://www.palantir.com/docs/foundry/functions/media/)交互的能力。TypeScript v1 支持完整的媒体操作，包括文档 OCR 和音频转写。TypeScript v2 和 Python 支持通过本体编辑上传媒体以及对媒体属性的基本操作，但不支持文档专用或音频专用的操作。 |
| 可在 Workshop 中调用 | 是 | 是 | 是 | 是 | 从 [Workshop 应用](https://www.palantir.com/docs/foundry/workshop/functions-use/)中调用函数。 |
| 可在 Pipeline Builder 中使用 | 否 | 否 | 否 | 是 | 从 [Pipeline Builder 管道](https://www.palantir.com/docs/foundry/functions/python-functions-builder/)中调用函数。 |
| 基于模型的函数支持 | 是 | 是 | 是 | 是 | [从函数](https://www.palantir.com/docs/foundry/functions/functions-on-models/)中执行实时部署的模型。TypeScript v2 和 Python 需要绑定本体的模型函数。 |
| 语言模型集成 | 否 | 否 | 是 | 是 | 使用模型别名和 Platform SDK 导入，通过[代理端点](https://www.palantir.com/docs/foundry/functions/language-models-python-tsv2/)调用语言模型。 |
| 语义搜索支持 | 是 | 是 | 是 | 是 | 使用函数为[语义搜索](https://www.palantir.com/docs/foundry/ontology/overview-semantic-search/)创建向量。 |
| Webhook 支持 | 否 | 是 | 否 | 否 | [从函数中调用 Webhook](https://www.palantir.com/docs/foundry/functions/webhooks/)的能力。 |
| 外部 API 调用支持 | 否 | 是 | 是 | 是 | [在函数内部](https://www.palantir.com/docs/foundry/functions/api-calls/)查询外部服务。 |
| 无服务器执行支持 | 是 | 是 | 是 | 是 | 无服务器函数会在被调用时按需启动。更多信息请参阅下文的[无服务器函数](#无服务器函数)。 |
| 已部署执行支持 | 否 | 否 | 是 | 是 | 已部署函数会为其分配专用资源，随时准备处理请求。 |
| 暂存写入支持 [Beta] | 否 | 否 | 是 | 否 | 在编辑函数中使用[暂存写入](https://www.palantir.com/docs/foundry/functions/typescript-v2-staged-writes/)以获得写后读保证和原子执行的能力。 |
| 从 API 网关调用函数 | 是 | 是 | 是 | 是 | 从 API 网关访问[查询函数](https://www.palantir.com/docs/foundry/functions/query-functions/)的能力。 |
| Marketplace 支持 | 是 | 是 | 是 | 是 | 在 [Marketplace](https://www.palantir.com/docs/foundry/marketplace/overview/) 中打包和交付函数的能力。 |
| 自带模型（Bring-your-own-model） | 是 | 是 | 否 | 否 | 将函数注册[为模型](https://www.palantir.com/docs/foundry/aip/bring-your-own-model/)的能力。[函数接口方法](https://www.palantir.com/docs/foundry/aip/chat-completion-function-interface-quickstart/)是仅适用于 TypeScript v1 的旧版方式。 |

## Ontology SDK 支持

Python 和 TypeScript v2 函数支持 [Ontology SDK](https://www.palantir.com/docs/foundry/ontology-sdk/overview/)（OSDK）。OSDK 允许你直接从开发环境利用本体，并提供诸如与 Developer Console 兼容和 OSDK 版本管理等[优势](https://www.palantir.com/docs/foundry/ontology-sdk/overview/#osdk-benefits)。我们建议使用 Python 或 TypeScript v2，以便在你的函数代码仓库中获得这些优势。

## TypeScript v1 与 TypeScript v2

TypeScript v1 和 TypeScript v2 都允许用户利用 TypeScript 的核心语言功能，但如上功能支持表所示，二者支持的平台功能存在差异。我们建议使用 TypeScript v2 函数构建工作流，以利用其相较于 TypeScript v1 的几项关键改进：

- **在完整 Node.js 运行时中进行无服务器执行：** TypeScript v2 函数在 Node.js 环境中运行，支持 `fs`、`child_process` 和 `crypto` 等核心模块。这让那些与文件系统交互、并行执行 CPU 密集型任务或需要其他系统级操作的 NPM 库具有更好的兼容性。
- **一等公民级的 OSDK 支持：** OSDK 现在可以在 TypeScript v2 函数中无缝使用，使得在平台内外复用代码变得容易。它还为处理大规模本体数据提供了更高效的 API。
- **可配置的资源请求：** TypeScript v2 函数允许你请求最多 8 个 vCPU 和 5GB 内存，从而对性能和可扩展性提供更强的控制。
- **暂存写入 [Beta]：** TypeScript v2 支持[暂存写入](https://www.palantir.com/docs/foundry/functions/typescript-v2-staged-writes/)，这是本体编辑函数的一种替代执行模型，可在函数内提供写后读保证，并允许嵌套调用其他暂存写入函数。

## 无服务器函数

如果你的注册实例启用了无服务器函数，新代码仓库将默认使用无服务器函数。对于大多数用例，我们建议使用无服务器函数而非已部署函数。使用无服务器函数，你可以按需提供单个函数的多个版本，使升级更安全。

使用 Python 或 TypeScript v2 函数时，也[有一些情形更适合或必须使用已部署函数](https://www.palantir.com/docs/foundry/functions/functions-deployed/#choose-between-deployed-and-serverless-execution-modes)而非无服务器函数，但这些情形并不常见。在可用的情况下，无服务器函数因以下几个原因更受青睐：

- 无服务器函数支持按需执行单个函数的不同版本，使升级更安全。使用已部署函数时，你一次只能运行单个函数版本。
- 无服务器函数仅在执行时产生成本，而已部署函数只要部署处于运行状态就会产生成本。
- 无服务器函数需要的前期设置和长期维护更少，因为基础设施是自动管理的。

---

*原文：[Feature support by language](https://www.palantir.com/docs/foundry/functions/language-feature-support/)*
