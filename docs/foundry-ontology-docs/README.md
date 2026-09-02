# Palantir Foundry 本体文档中文翻译

> 本目录为 [Palantir Foundry 官方文档](https://www.palantir.com/docs/foundry/ontology/overview/)中
> **Ontology building（本体构建）** 与 **Define Ontologies（定义本体）** 两个板块的非官方中文翻译，仅供学习参考。
> 原文 © 2026 Palantir Technologies Inc. 保留所有权利。术语在首次出现时多以“中文（English）”形式标注，便于与英文界面对照。

共 178 篇译文（含总览页）。

## 1. 本体构建（Ontology building）

- [本体构建（Ontology Building）](ontology-building/overview.md)
- [为什么要创建本体？（Why create an Ontology?）](ontology-building/why-ontology.md)
- [本体中的模型（Models in the Ontology）](ontology-building/models.md)
- [核心概念（Core concepts）](ontology-building/core-concepts.md)
- [本体感知的应用（Ontology-aware applications）](ontology-building/applications.md)

## 2. 定义本体（Define Ontologies）

### 2.1 本体（Ontologies）

- [本体（Ontologies）](define-ontologies/ontologies/ontologies-overview.md)
- [在本体之间迁移本体资源（Migrate ontological resource between Ontologies）](define-ontologies/ontologies/ontology-migration.md)
- [共享本体（Shared ontologies）](define-ontologies/ontologies/shared-ontologies.md)
- **本体分支化**
  - [本体分支化（Branching the ontology）](define-ontologies/ontologies/branching/branching-ontology.md)
  - [本体提案（旧版）（Ontology proposals [Legacy]）](define-ontologies/ontologies/branching/ontology-branches-legacy.md)
  - [评审本体提案（Review ontology proposals）](define-ontologies/ontologies/branching/review-ontology-proposals.md)
- **用量**
  - [计算用量：本体索引构建（Compute usage: Ontology indexing）](define-ontologies/ontologies/usage/compute-usage.md)
  - [对象集服务限制（Object Set Service limitations）](define-ontologies/ontologies/usage/oss-limitations.md)
  - [本体查询的计算用量（Compute usage with Ontology queries）](define-ontologies/ontologies/usage/query-compute-usage.md)
  - [本体用量（Ontology volume usage）](define-ontologies/ontologies/usage/volume-usage.md)

### 2.2 对象与链接类型（Object and link types）

- [类型参考（Types reference）](define-ontologies/object-link-types/type-reference.md)
- **链接类型**
  - [允许用户编辑对象和链接（Allow users to edit objects and links）](define-ontologies/object-link-types/link-types/allow-editing.md)
  - [创建链接类型（Create a link type）](define-ontologies/object-link-types/link-types/create-link-type.md)
  - [编辑链接类型（Edit link types）](define-ontologies/object-link-types/link-types/edit-link-types.md)
  - [元数据参考（Metadata reference）](define-ontologies/object-link-types/link-types/link-type-metadata.md)
  - [链接类型（Link types）](define-ontologies/object-link-types/link-types/link-types-overview.md)
- **元数据**
  - [将对象类型和链接类型添加到 Marketplace 产品（Add object and link types to a Marketplace product）](define-ontologies/object-link-types/metadata/marketplace-ontology-types.md)
  - [渲染提示（Render hints）](define-ontologies/object-link-types/metadata/metadata-render-hints.md)
  - [状态（Statuses）](define-ontologies/object-link-types/metadata/metadata-statuses.md)
  - [类型类（Type classes）](define-ontologies/object-link-types/metadata/metadata-typeclasses.md)
  - [对象类型组（Object type groups）](define-ontologies/object-link-types/metadata/type-groups.md)
- **对象类型**
  - [创建对象类型（Create an object type）](define-ontologies/object-link-types/object-types/create-object-type.md)
  - [从 Gaia 创建本体对象（Create Ontology objects from Gaia）](define-ontologies/object-link-types/object-types/create-ontology-objects-from-gaia.md)
  - [编辑对象类型（Edit object types）](define-ontologies/object-link-types/object-types/edit-object-type.md)
  - [通过类型映射启用 Gotham 集成（Enable Gotham integration through type mapping）](define-ontologies/object-link-types/object-types/enable-gotham-integration.md)
  - [元数据参考（Metadata reference）](define-ontologies/object-link-types/object-types/object-type-metadata.md)
  - [对象类型（Object types）](define-ontologies/object-link-types/object-types/object-types-overview.md)
- **属性**
  - [基础类型（Base types）](define-ontologies/object-link-types/properties/base-types.md)
  - [条件格式（Conditional formatting）](define-ontologies/object-link-types/properties/conditional-formatting.md)
  - [配置派生属性（Configure derived properties）](define-ontologies/object-link-types/properties/derived-properties-olt.md)
  - [仅编辑属性（Edit-only properties）](define-ontologies/object-link-types/properties/edit-only-properties.md)
  - [编辑对象类型属性（Edit object type properties）](define-ontologies/object-link-types/properties/edit-properties.md)
  - [强制控制属性（Mandatory control properties）](define-ontologies/object-link-types/properties/mandatory-control-properties.md)
  - [属性（Properties）](define-ontologies/object-link-types/properties/properties-overview.md)
  - [元数据参考（Metadata reference）](define-ontologies/object-link-types/properties/property-metadata.md)
  - [属性归约器 [Beta]（Property reducers [Beta]）](define-ontologies/object-link-types/properties/property-reducers.md)
  - [必填属性（Required properties）](define-ontologies/object-link-types/properties/required-properties.md)
  - [添加值格式设置（Add value formatting）](define-ontologies/object-link-types/properties/value-formatting.md)
- **共享属性**
  - [创建共享属性（Create a shared property）](define-ontologies/object-link-types/shared-properties/create-shared-property.md)
  - [编辑共享属性（Edit shared properties）](define-ontologies/object-link-types/shared-properties/edit-shared-property.md)
  - [元数据参考（Metadata reference）](define-ontologies/object-link-types/shared-properties/shared-property-metadata.md)
  - [共享属性（Shared properties）](define-ontologies/object-link-types/shared-properties/shared-property-overview.md)
  - [在对象类型上使用共享属性（Use shared properties on object types）](define-ontologies/object-link-types/shared-properties/use-shared-property.md)
- **结构体**
  - [创建结构体属性类型（Create a struct property type）](define-ontologies/object-link-types/structs/create-struct-type.md)
  - [编辑结构体属性类型（Edit a struct property type）](define-ontologies/object-link-types/structs/edit-struct-type.md)
  - [结构体属性自动映射（Automapping struct properties）](define-ontologies/object-link-types/structs/struct-automapping.md)
  - [结构体主字段 [Beta]（Struct main fields [Beta]）](define-ontologies/object-link-types/structs/struct-main-fields.md)
  - [结构体属性与共享属性类型（Struct properties and shared property types）](define-ontologies/object-link-types/structs/struct-shared-properties.md)
  - [结构体（Structs）](define-ontologies/object-link-types/structs/structs-overview.md)
- **值类型**
  - [创建值类型（Create a value type）](define-ontologies/object-link-types/value-types/create-value-type.md)
  - [使用值类型（Use value types）](define-ontologies/object-link-types/value-types/use-value-type.md)
  - [值类型约束（Value type constraints）](define-ontologies/object-link-types/value-types/value-type-constraints.md)
  - [值类型（Value types）](define-ontologies/object-link-types/value-types/value-types-overview.md)
  - [值类型权限（Value type permissions）](define-ontologies/object-link-types/value-types/value-types-permissions.md)
  - [值类型版本（Value type versions）](define-ontologies/object-link-types/value-types/value-types-versions.md)

### 2.3 动作类型（Action types）

- [探索其他动作类型（Explore other action types）](define-ontologies/action-types/explore-action-types.md)
- [入门（Getting started）](define-ontologies/action-types/getting-started.md)
- [动作类型（Action types）](define-ontologies/action-types/overview.md)
- [规则（Rules）](define-ontologies/action-types/rules.md)
- [测试运行（Test run）](define-ontologies/action-types/test-run.md)
- [在平台中使用动作（Use actions in the platform）](define-ontologies/action-types/use-actions.md)
- **函数支撑的动作**
  - [批量执行（Batched execution）](define-ontologies/action-types/function-backed/function-actions-batched-execution.md)
  - [入门（Getting started）](define-ontologies/action-types/function-backed/function-actions-getting-started.md)
  - [函数支撑的动作（Function-backed actions）](define-ontologies/action-types/function-backed/function-actions-overview.md)
- **参数**
  - [接口上的动作（Actions on interfaces）](define-ontologies/action-types/parameters/actions-on-interfaces.md)
  - [结构体上的动作（Actions on structs）](define-ontologies/action-types/parameters/actions-on-structs.md)
  - [对象下拉菜单安全注意事项（Object dropdown security considerations）](define-ontologies/action-types/parameters/dropdown-security.md)
  - [参数（Parameters）](define-ontologies/action-types/parameters/parameter-overview.md)
  - [参数配置的性能注意事项（Performance considerations for parameter configuration）](define-ontologies/action-types/parameters/parameter-performance-considerations.md)
  - [设置参数默认值（Set parameter default value）](define-ontologies/action-types/parameters/parameters-default-value.md)
  - [筛选参数下拉菜单的结果（Filter results of a parameter dropdown menu）](define-ontologies/action-types/parameters/parameters-filter.md)
  - [覆盖（Overrides）](define-ontologies/action-types/parameters/parameters-override.md)
  - [提交条件（Submission criteria）](define-ontologies/action-types/parameters/submission-criteria.md)
- **副作用**
  - [动作日志（Action log）](define-ontologies/action-types/side-effects/action-log.md)
  - [动作指标（Action metrics）](define-ontologies/action-types/side-effects/action-metrics.md)
  - [回滚或撤销动作（Revert or undo actions）](define-ontologies/action-types/side-effects/action-reverts.md)
  - [分支化动作类型（Branching action types）](define-ontologies/action-types/side-effects/branching-action-types.md)
  - [配置分区（Configure sections）](define-ontologies/action-types/side-effects/configure-sections.md)
  - [内联编辑（Inline edits）](define-ontologies/action-types/side-effects/inline-edits.md)
  - [将动作类型添加到 Marketplace 产品（Add action types to Marketplace product）](define-ontologies/action-types/side-effects/marketplace-action-types.md)
  - [动作监控（Action monitoring）](define-ontologies/action-types/side-effects/monitoring.md)
  - [通知（Notifications）](define-ontologies/action-types/side-effects/notifications.md)
  - [权限（Permissions）](define-ontologies/action-types/side-effects/permissions.md)
  - [读和写授权（Read and write authorizations）](define-ontologies/action-types/side-effects/read-write-authorizations.md)
  - [规模与属性限制（Scale and property limits）](define-ontologies/action-types/side-effects/scale-property-limits.md)
  - [设置通知（Set up a notification）](define-ontologies/action-types/side-effects/set-up-notification.md)
  - [设置 Webhook（Set up a webhook）](define-ontologies/action-types/side-effects/set-up-webhook.md)
  - [副作用（Side effects）](define-ontologies/action-types/side-effects/side-effects-overview.md)
  - [触发调度构建（Trigger schedule build）](define-ontologies/action-types/side-effects/trigger-schedule-build.md)
  - [上传附件（Upload attachments）](define-ontologies/action-types/side-effects/upload-attachments.md)
  - [上传媒体（Upload media）](define-ontologies/action-types/side-effects/upload-media.md)
  - [Webhook（Webhooks）](define-ontologies/action-types/side-effects/webhooks.md)

### 2.4 函数（Functions）

- [函数分支化（Branching functions）](define-ontologies/functions/branching-functions.md)
- [函数入门（Getting started with functions）](define-ontologies/functions/getting-started.md)
- [各语言的功能支持（Feature support by language）](define-ontologies/functions/language-feature-support.md)
- [函数（Functions）](define-ontologies/functions/overview.md)
- [类型参考（Types reference）](define-ontologies/functions/types-reference.md)
- **别名**
  - [自定义别名（Custom aliases）](define-ontologies/functions/aliases/custom-aliases.md)
  - [模型别名（Model aliases）](define-ontologies/functions/aliases/model-aliases.md)
  - [源别名（Source aliases）](define-ontologies/functions/aliases/source-aliases.md)
- **函数使用**
  - [函数指标（Function metrics）](define-ontologies/functions/consumption/function-metrics.md)
  - [优化性能（Optimize performance）](define-ontologies/functions/consumption/optimize-performance.md)
  - [在平台中使用函数（Use functions in the platform）](define-ontologies/functions/consumption/use-functions.md)
  - [函数的版本范围依赖（Version range dependencies for functions）](define-ontologies/functions/consumption/version-range-dependencies.md)
- **语言无关特性**
  - [从函数发起 API 调用（Make API calls from functions）](define-ontologies/functions/language-agnostic/api-calls.md)
  - [配置通知（Configure notifications）](define-ontologies/functions/language-agnostic/configure-notifications.md)
  - [本体编辑（Ontology edits）](define-ontologies/functions/language-agnostic/edits-overview.md)
  - [部署函数（Deploy functions）](define-ontologies/functions/language-agnostic/functions-deployed.md)
  - [函数设置（Functions settings）](define-ontologies/functions/language-agnostic/functions-settings.md)
  - [通过 Foundry 平台 SDK 使用平台 API（Use platform APIs with the Foundry platform SDK）](define-ontologies/functions/language-agnostic/platform-sdk.md)
  - [查询（Queries）](define-ontologies/functions/language-agnostic/query-functions.md)
  - [流式函数（Streaming functions）](define-ontologies/functions/language-agnostic/streaming-functions.md)
  - [面向用户的错误（User-facing errors）](define-ontologies/functions/language-agnostic/user-facing-error.md)
- **函数管理**
  - [函数版本管理（Function versioning）](define-ontologies/functions/management/functions-versioning.md)
  - [函数中的埋点与遥测（Instrumentation and telemetry in functions）](define-ontologies/functions/management/instrumentation-telemetry.md)
  - [管理已发布的函数（Manage published functions）](define-ontologies/functions/management/manage-functions.md)
  - [Marketplace 中的函数（Functions in Marketplace）](define-ontologies/functions/management/marketplace-functions.md)
  - [函数监控（Function monitoring）](define-ontologies/functions/management/monitoring.md)
  - [权限（Permissions）](define-ontologies/functions/management/permissions.md)
- **模型**
  - [函数接口（Function interfaces）](define-ontologies/functions/models/function-interfaces.md)
  - [基于模型的函数（Functions on models）](define-ontologies/functions/models/functions-on-models.md)
  - [函数中的旧版语言模型（Legacy language models within functions）](define-ontologies/functions/models/language-models-legacy.md)
  - [TypeScript v2 和 Python 函数中的语言模型（Language models in TypeScript v2 and Python functions）](define-ontologies/functions/models/language-models-python-tsv2.md)
  - [TypeScript v1 函数中的语言模型（Language models in TypeScript v1 functions）](define-ontologies/functions/models/language-models.md)
- **对象函数（FOO）**
  - [API：对象集（API: Object sets）](define-ontologies/functions/on-objects/api-object-sets.md)
  - [API：对象与链接（API: Objects and links）](define-ontologies/functions/on-objects/api-objects-links.md)
  - [附件（Attachments）](define-ontologies/functions/on-objects/attachments.md)
  - [创建自定义聚合（Create a custom aggregation）](define-ontologies/functions/on-objects/create-custom-aggregation.md)
  - [对象函数入门（Getting started with functions on objects）](define-ontologies/functions/on-objects/foo-getting-started.md)
  - [对象函数（FOO）（Functions on objects (FOO)）](define-ontologies/functions/on-objects/functions-on-objects.md)
  - [媒体（Media）](define-ontologies/functions/on-objects/media.md)
  - [对象标识符（Object identifiers）](define-ontologies/functions/on-objects/object-identifiers.md)
  - [导入对象、接口和链接类型（Import object, interface, and link types）](define-ontologies/functions/on-objects/ontology-imports.md)
- **Python**
  - [在 Pipeline Builder 中使用 Python 函数（Use a Python function in Pipeline Builder）](define-ontologies/functions/python/python-functions-builder.md)
  - [创建自定义聚合（Create a custom aggregation）](define-ontologies/functions/python/python-functions-create-custom-aggregation.md)
  - [对象函数（Functions on objects）](define-ontologies/functions/python/python-functions-on-objects.md)
  - [在 Workshop 中使用 Python 函数（Use a Python function in Workshop）](define-ontologies/functions/python/python-functions-workshop.md)
  - [Python 函数入门（Getting started with Python functions）](define-ontologies/functions/python/python-getting-started.md)
  - [本地开发（Local development）](define-ontologies/functions/python/python-local-development.md)
  - [本体编辑（Ontology edits）](define-ontologies/functions/python/python-ontology-edits.md)
- **TypeScript v1**
  - [添加 npm 依赖（Add npm dependencies）](define-ontologies/functions/typescript-v1/add-dependencies.md)
  - [本体编辑（Ontology edits）](define-ontologies/functions/typescript-v1/api-ontology-edits.md)
  - [调试函数（Debug functions）](define-ontologies/functions/typescript-v1/debug.md)
  - [装饰器（Decorators）](define-ontologies/functions/typescript-v1/decorators.md)
  - [为新对象生成唯一 ID（Generate unique IDs for new objects）](define-ontologies/functions/typescript-v1/edits-generate-id.md)
  - [将资源导入 Code Repositories（Import resources into Code Repositories）](define-ontologies/functions/typescript-v1/resource-imports-sidebar.md)
  - [错误类型（Error types）](define-ontologies/functions/typescript-v1/typescript-error-types.md)
  - [TypeScript v1 函数入门（Getting started with TypeScript v1 functions）](define-ontologies/functions/typescript-v1/typescript-v1-getting-started.md)
  - [从 TypeScript v1 迁移到 TypeScript v2（Migrate from TypeScript v1 to TypeScript v2）](define-ontologies/functions/typescript-v1/typescript-v2-migration.md)
  - [处理 undefined 值（Handle undefined values）](define-ontologies/functions/typescript-v1/undefined-values.md)
  - [函数中的 Webhook（Webhooks in functions）](define-ontologies/functions/typescript-v1/webhooks-functions.md)
- **TypeScript v2**
  - [TypeScript v2 函数入门（Getting started with TypeScript v2 functions）](define-ontologies/functions/typescript-v2/typescript-v2-getting-started.md)
  - [本体编辑（Ontology edits）](define-ontologies/functions/typescript-v2/typescript-v2-ontology-edits.md)
  - [暂存写入 [Beta]（Staged writes [Beta]）](define-ontologies/functions/typescript-v2/typescript-v2-staged-writes.md)
- **单元测试**
  - [模拟日期、时间戳和 UUID（Mock dates, timestamps, and UUIDs）](define-ontologies/functions/unit-testing/unit-test-dates-timestamps.md)
  - [调试（Debug）](define-ontologies/functions/unit-testing/unit-test-debugging.md)
  - [入门（Getting started）](define-ontologies/functions/unit-testing/unit-test-getting-started.md)
  - [桩对象搜索与聚合（Stub object searches and aggregations）](define-ontologies/functions/unit-testing/unit-test-object-searches.md)
  - [验证本体编辑（Verify Ontology edits）](define-ontologies/functions/unit-testing/unit-test-ontology-edits.md)
  - [创建桩对象（Create stub objects）](define-ontologies/functions/unit-testing/unit-test-stub-objects.md)
  - [模拟用户与用户组（Mock users and groups）](define-ontologies/functions/unit-testing/unit-test-users-groups.md)

### 2.5 接口（Interfaces）

- [创建接口（Create an interface）](define-ontologies/interfaces/create-interface.md)
- [编辑接口定义（Edit an interface definition）](define-ontologies/interfaces/edit-interface-definition.md)
- [编辑接口实现（Edit an interface implementation）](define-ontologies/interfaces/edit-interface-implementation.md)
- [扩展接口（Extend an interface）](define-ontologies/interfaces/extend-interface.md)
- [实现接口（Implement an interface）](define-ontologies/interfaces/implement-interface.md)
- [接口动作类型约束（Interface action type constraints）](define-ontologies/interfaces/interface-action-type-constraints.md)
- [接口链接类型约束（Interface link type constraints）](define-ontologies/interfaces/interface-link-types-overview.md)
- [元数据参考（Metadata reference）](define-ontologies/interfaces/interface-metadata.md)
- [接口（Interfaces）](define-ontologies/interfaces/interface-overview.md)

### 2.6 本体设计（Ontology design）

- [本体设计：反模式（Ontology design: Anti-patterns）](define-ontologies/ontology-design/ontology-anti-patterns.md)
- [本体设计：最佳实践（Ontology design: Best practices）](define-ontologies/ontology-design/ontology-best-practices.md)
- [本体设计：结构指南（Ontology design: Structural guidance）](define-ontologies/ontology-design/ontology-structural-guidance.md)

