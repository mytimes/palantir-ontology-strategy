# 将资源导入 Code Repositories（Import resources into Code Repositories）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/resource-imports-sidebar/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

> **⚠️ 注意**
>
> 以下文档专门针对 TypeScript v1 函数。若需要更[强大的能力](https://www.palantir.com/docs/foundry/functions/language-feature-support/#typescript-v1-vs-typescript-v2)，包括对 Ontology SDK 和可配置资源请求的支持，我们建议[迁移到 TypeScript v2](https://www.palantir.com/docs/foundry/functions/typescript-v2-migration/)。

Code Repositories 中的**资源导入（Resource imports）**侧边栏提供了一个集中式界面，用于在你的 TypeScript 函数仓库中管理已导入的 Foundry 资源。通过该侧边栏，你可以导入、移除各类资源并查看其详情，包括本体类型、LMS 语言模型、实时部署以及 REST API 等外部系统。

![资源导入侧边栏](https://www.palantir.com/docs/resources/foundry/functions/resource-imports-sidebar.png)

## 选择本体

导入对象类型和链接类型需要先指定一个本体。选择本体的步骤如下：

1. 选择 **Add（添加）** 打开资源选择器菜单，然后选择 **Ontology（本体）** 开始导入本体类型。如果尚未选择本体，这将自动打开本体选择器对话框。

如果你已经导入了至少一个本体类型，则会自动选中该类型所属的本体。若要更改本体，选择所选本体名称旁边的 **Edit（编辑）** 按钮以打开本体选择器对话框。

![本体选择器对话框](https://www.palantir.com/docs/resources/foundry/functions/sidebar-ontology-picker.png)

仓库中所有已导入的资源必须关联到同一个本体。请注意，在更改本体之后导入资源会覆盖来自其他本体的任何现有导入。

## 导入资源

> **⚠️ 注意**
>
> 现代版本的 TypeScript v1 模板会通过签入到仓库中的 `resources.json` 文件来维护仓库导入的当前状态。
>
> 如果你在侧边栏中遇到关于无法解析的文件的警告，请参阅[基于文件的仓库导入](#基于文件的仓库导入)部分，了解预期的文件格式以及解决该问题的排查步骤。

要使用侧边栏导入资源：

1. 使用侧边栏右上角的 **Add（添加）** 按钮并选择所需的资源类型。这将打开该资源的选择器对话框。
2. 使用搜索栏和筛选器定位你想要导入的资源。
3. 选择一个资源以显示其包含详细信息的预览面板。
4. 使用 **Select（选择）** 按钮将资源添加到你的选择中。
5. 展开购物车面板以检查你的选择，并通过选择 **Confirm selection（确认选择）** 进行确认。

确认选择后，Code Assist 将重启以重新运行必要的代码生成任务，从而应用你的更改。随后，每个导入的资源都会作为一个生成的包暴露出来，因此你需要从生成的包路径而不是相对于你自己文件的路径来导入它。例如，本体类型从 `@foundry/ontology-api` 导入；具体路径取决于资源类型。

![资源选择器对话框示例](https://www.palantir.com/docs/resources/foundry/functions/language-model-import-dialog.png)

详细了解如何导入特定类型的资源：

- [本体类型](https://www.palantir.com/docs/foundry/functions/ontology-imports/)
- [语言模型](https://www.palantir.com/docs/foundry/functions/language-models-python-tsv2/#import-a-language-model)
- [实时部署](https://www.palantir.com/docs/foundry/functions/functions-on-models/#import-a-live-deployment-in-a-repository)
- [外部源](https://www.palantir.com/docs/foundry/functions/webhooks/)

## 管理已导入的资源

资源在侧边栏中按类型分类：

- 本体（Ontology）：对象类型、接口类型和链接类型
- 模型（Models）：LMS 模型和实时部署
- 源（Sources）：REST API 等外部系统

选择侧边栏顶部相应的资源图标可按类型筛选，或使用文本输入框按名称搜索。要移除某个资源，将鼠标悬停在资源图标上并选择 **Remove（移除）** 按钮。要同时添加或移除多个资源，请使用选择器对话框。要查看更多详情，选择一个已导入的资源以打开其预览面板。

某些资源类型之间可能存在依赖关系。例如，链接类型归属于其各自的对象类型之下。如果某个已导入的资源存在依赖，资源标题旁边会显示类似“(1 个链接类型)”的消息。要查看资源的依赖，将鼠标悬停在资源图标上并选择出现的 V 形箭头。

![资源导入侧边栏筛选控件](https://www.palantir.com/docs/resources/foundry/functions/resource-imports-sidebar-filters.png)

## 导入没有 API 名称的资源

资源必须具有 API 名称，才能在 TypeScript 函数仓库的代码中被引用。如果某个资源缺少 API 名称，将显示警告。将鼠标悬停在警告标志上可了解更多信息，或通过选择 **Add API name（添加 API 名称）** 轻松配置 API 名称。或者，选择 **Learn more（了解更多）** 查看针对特定资源类型添加 API 名称的文档。

![资源导入侧边栏 API 名称警告](https://www.palantir.com/docs/resources/foundry/functions/resource-imports-sidebar-api-name-warning.png)

## 导入带有值类型依赖的资源

某些资源依赖于[值类型](https://www.palantir.com/docs/foundry/object-link-types/value-types-overview/)来定义与其交互时使用的数据类型，例如函数接口。对于这些资源，它们的值类型依赖会自动导入到仓库中，以便与该资源一起使用。

在某些情况下，导入此类资源的组合可能会导致值类型依赖冲突。当不同的资源依赖于同一个值类型但所依赖的版本不同时，就会发生这种情况。同一个值类型不可能同时导入两个版本，这会导致编译错误。该错误会伴随侧边栏中的警告，让你能够查看存在依赖冲突的资源。

![值类型冲突警告](https://www.palantir.com/docs/resources/foundry/functions/value-type-conflicts-warning.png)

## 基于文件的仓库导入

现代版本的 TypeScript v1 模板使用签入到仓库中的 `resources.json` 文件来维护仓库导入的当前状态。这为你提供了完整的 Git 语义，允许你对导入的更改进行评审、创建分支和回滚。资源导入侧边栏会通过自动向 `resources.json` 文件中插入条目来帮助你更新该文件。

如果 `resources.json` 文件处于无效状态，侧边栏中会出现警告，通知你该文件无法被处理。如果你遇到此错误，请确保你的文件包含一个具有以下数据的单一 JSON 对象：

| 字段 | 类型 |
| --- | --- |
| `objectTypes` | `{ rid: string }` 数组 |
| `linkTypes` | `{ rid: string }` 数组 |
| `sources` | `{ rid: string }` 数组 |
| `functions` | `{ rid: string, version: string }` 数组 |
| `valueTypes` | `{ rid: string, version: string }` 数组 |
| `functionInterfaces` | `{ rid: string, version: string }` 数组 |
| `_comment` | 字符串 |
| `version`[1] | 整数 |

[1] `version` 字段用于表示 `resources.json` 文件的版本和格式。目前仅支持 `version: 1`。`version: 0` 用于表示你的仓库必须从先前的仓库级导入工作流进行迁移。当提交包含 `version: 0` 的更改时，此迁移会通过应用到你仓库的补丁自动处理。

请注意，由于 `resources.json` 文件已签入到你的仓库中，你可以查看提交历史并使用该历史将文件回滚到可用状态。

## 启用资源类型

默认情况下，某些资源类型可能未在你的仓库中启用。已启用的资源类型由你的 `functions.json` 文件决定。以下是一个典型的默认 `functions.json` 文件的内容。

```json
{
  "useOntologyApiNames" : true,
  "enableModelFunctions" : false,
  "enableModelGraphFunctions" : false,
  "enableDiscoverImproperOntologyAccess": false,
  "enableQueries": false,
  "enableModelMetadata": false,
  "useDeploymentApiNames": true,
  "enableVectorProperties": true,
  "enableTimeSeriesProperties": false,
  "enableExternalSystems": false,
  "enableMediaReferenceProperties": false
}
```

在 `functions.json` 文件中未启用相应标志的情况下导入资源，可能会导致仓库中的检查失败。要使用已导入的实时部署，请将 `enableModelFunctions` 设置为 true。要使用已导入的源，请将 `enableExternalSystems` 设置为 true。

---

*原文：[Import resources into Code Repositories](https://www.palantir.com/docs/foundry/functions/resource-imports-sidebar/)*
