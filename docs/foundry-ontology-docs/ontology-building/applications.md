# 本体感知的应用（Ontology-aware applications）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/ontology/applications/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

Foundry 包含多个为在本体之上原生运行而开发的应用。这些对象感知的应用共同提供了一个强大的分析和运营平台，支持一系列用例和用户画像。

要进一步了解为什么建立本体并使用对象感知的应用很有价值，请参阅[本页](https://www.palantir.com/docs/foundry/ontology/why-ontology/)。

本页提供可用应用的参考，并说明你应该在何时使用每一个：

- [应用参考](#应用参考)
- [应用对比](#应用对比)

## 应用参考

### Object Views

[**Object Views**](https://www.palantir.com/docs/foundry/object-views/overview/) 是与特定对象相关的所有信息和工作流的中心枢纽。这包括关于对象的关键“档案数据”、任何链接的对象、关键的相关指标，以及指向与该对象相关的关键分析、仪表板和应用的链接（或对它们的嵌入）。

例如，`Airport` 对象类型的对象视图可能为每个 `Airport` 对象提供以下信息：

- 档案数据，如 `country`、`city`、`longitude`、`latitude` 等。
- 与该 `Airport` 链接的所有 `Aircraft` 对象和 `Flight` 对象的 360 度视图
- 嵌入的 `Airport Covid Response` 工作流
- 指向与该 `Airport` 相关的 `Flight delay` 事件的 `Root-Cause Analysis`（根因分析）的链接

![Object View 枢纽示例](https://www.palantir.com/docs/resources/foundry/ontology/object-apps-object-view-hub.png)

### Object Explorer

[**Object Explorer**](https://www.palantir.com/docs/foundry/object-explorer/overview/) 是一个搜索和分析工具，用于回答关于本体层中任何事物的问题。用户可以可视化地组合搜索查询——从简单筛选到环绕搜索（Search Around）——来查找感兴趣的对象。然后，他们可以使用探索视图浏览生成的对象集，或将其作为结果表格查看。此外，用户可以比较和对照对象集，并对对象集执行批量动作（例如回写）。之后，用户可以导出对象集，或在兼容的应用（如 Workshop）中打开它们。

探索视图是一组预设且可配置的可视化（如图表或地图），用户可以进一步利用它们下钻到特定的对象子集。Object Explorer 无需预先配置，面向技术程度较低的用户。

![Object Explorer](https://www.palantir.com/docs/resources/foundry/ontology/object-apps-oe.png)

### Quiver

[**Quiver**](https://www.palantir.com/docs/foundry/quiver/overview/) 通过可视化点选界面和强大的图表库，在本体层中支持高级分析工作流。Quiver 可用于支持各种分析：从简单的线性下钻分析，到包含聚合和统计函数、分支众多的复杂分析。Quiver 还支持原生时间序列分析。Quiver 分析可以模板化为只读仪表板，供更广泛的人群使用。

![Quiver](https://www.palantir.com/docs/resources/foundry/ontology/object-apps-quiver.png)

### Workshop

[**Workshop**](https://www.palantir.com/docs/foundry/workshop/overview/) 支持在本体层上原生进行点选式、无代码的应用构建。在 Workshop 中构建的应用比在其他点选工具中创建的典型仪表板更具动态性和交互性。

借助高质量的[布局（Layouts）](https://www.palantir.com/docs/foundry/workshop/concepts-layouts/)和易用而强大的[事件（Events）系统](https://www.palantir.com/docs/foundry/workshop/concepts-events/)，Workshop 应用力求达到与自定义 React 应用同等的易用性和高品质。

*Workshop 编辑器视图*

![Workshop 编辑器视图](https://www.palantir.com/docs/resources/foundry/ontology/object-apps-workshop-editor-view.png)

*最终的 Workshop 模块*

![最终的 Workshop 模块](https://www.palantir.com/docs/resources/foundry/ontology/object-apps-workshop-module.png)

### Slate

[**Slate**](https://www.palantir.com/docs/foundry/slate/overview/) 是 Foundry 的灵活应用构建器，相比 Workshop 需要更多技术配置和代码。Slate 应用与本体层交互，但也可以直接与 Foundry 数据集交互。Slate 支持基于 Web 开发范式的大量可视化定制，具有广泛的可用功能，但与 Workshop 相比，构建和维护应用也需要更多技术知识。

*Slate 编辑器视图*

![Slate 编辑器视图](https://www.palantir.com/docs/resources/foundry/ontology/object-apps-slate-editor-view.png)

*Slate 应用视图*

![Slate 应用视图](https://www.palantir.com/docs/resources/foundry/ontology/object-apps-slate-app-view.png)

### Carbon

[**Carbon**](https://www.palantir.com/docs/foundry/carbon/overview/) 支持将 Foundry 中的多个资源或应用组合起来，为运营用户创建精心策划的*工作区*。通过允许你组合仪表板等分析结果、在 Workshop 或 Slate 中构建的应用，以及 Object Views 和 Object Explorer 等开箱即用的能力，Carbon 使工作流构建者能够完成“最后一公里”的定制，为最终用户创建高度量身定制且易用的体验。

![Carbon 工作区](https://www.palantir.com/docs/resources/foundry/ontology/carbon-workspace.png)

### Map

[**Map**](https://www.palantir.com/docs/foundry/map/overview/) 应用允许你在地理空间场景中汇集并分析对象和其他数据。

![Map 应用](https://www.palantir.com/docs/resources/foundry/ontology/map-overview.png)

## 应用对比

每个对象感知的应用在几个维度上有所不同。三个特别重要的维度是：

- [主要用例](#用例)，
- [工作流风格](#工作流风格)，以及
- 应用的[配置模型](#配置模型)。

| Foundry 应用 | [主要用例](#用例) | [工作流风格](#工作流风格) | [配置模型](#配置模型) | 对象或数据集 |
| --- | --- | --- | --- | --- |
| [Object Views](#object-views) | 发现 | 工作流专用 | 即来即用 | 对象 |
| [Object Explorer](#object-explorer) | 发现与分析 | 探索式 | 即来即用 | 对象 |
| [Quiver](#quiver) | 分析与仪表板 | 探索式（分析模式）；工作流专用（仪表板模式） | 即来即用（分析模式）；可定制（仪表板模式） | 对象 |
| [Workshop](#workshop) | 应用与仪表板 | 工作流专用 | 可定制 | 对象 |
| [Slate](#slate) | 应用与仪表板（复杂） | 工作流专用 | 可定制 | 对象（推荐）和数据集 |
| [Map](#map) | 地理空间 | 探索式或工作流专用 | 即来即用 | 对象 |

### 用例

对象感知的应用支持的主要用例是**发现（Discovery）**、**分析（Analysis）**、**仪表板（Dashboards）**和**应用（Applications）**。

- **发现**使用户能够找到正确的信息或工作流。发现主要通过两个核心功能实现：精选内容枢纽和搜索。精选内容枢纽（有时称为落地页或“360 度视图”）既包括面向所有用户的综合标准视图，也包括针对特定用户组或用例的定向视图。搜索功能通过关键词的自由文本搜索，以及通过链接遍历或下钻进行的迭代式搜索，来支持发现。
- **分析**使用户能够回答广泛的问题。这些问题从简单的（*给定产品的平均客户留存率是多少？*）到非常复杂的（*三个不同的客户群在留存率和整体收入方面随时间如何比较？既要看所有产品的总体情况，也要看每个产品各自的情况。*）。分析路径是探索式的，这意味着它由最终用户自己定义，并且通常高度迭代；随着初始问题得到回答，又会产生新的问题并纳入分析路径。
- **仪表板**是一组预先配置的可视化，主要供更广泛的用户群体以只读方式使用。仪表板通常用于将有意义的*分析*转化为周期性报表或运营监控。仪表板的特点是包含大量图表和其他可视化，但不如*应用*（见下文）那样可定制或可交互。仪表板通常经过参数化，使用户可以对可视化进行筛选，查看不同的数据子集。
- **应用**是交互式的自定义运营界面，面向特定用户组解决特定问题。应用通常比仪表板更复杂，旨在使用户能够遵循特定的、轨道式（on-rails）的工作流。虽然应用可能包含执行决策所需的一些精选分析内容（如统计数据、图表、图形等），但它通常也有若干工作流元素，并且经常捕获用户输入（例如回写）。

### 工作流风格

对象感知的应用针对主要工作流风格进行了优化。

- **探索式应用**不需要由“构建者”用户预先配置，一旦数据建模进入本体，最终用户即可开箱即用。在探索式应用中，最终用户定义自己的分析路径，可以回答广泛的、非预先确定的问题。探索式应用通常包含一组搜索、可视化和转换功能来实现这一点。主要属于探索式的对象感知应用包括 Object Explorer 和 Quiver。
- **工作流专用应用**需要由“构建者”用户预先配置，最终用户才能实际使用。这是具有两个主要用户组的仪表板或应用的典型情况：(1) 配置特定仪表板或应用的构建者组，以及 (2) 应用为之构建的下游最终用户。Workshop 和 Slate 模块都需要由“构建者”用户在编辑模式下预先配置。

某些应用（如 Quiver）同时适应两种工作流风格，因为虽然它们的主要模式是探索式的，但其输出可以配置为供更广泛人群使用的工作流专用产物。虽然 Quiver 分析高度探索式，但它们可以发布为 Quiver 仪表板——即预先配置的分析视图，可供更广泛的受众访问。

### 配置模型

配置模型描述了用户界面在能被最终用户使用之前必须配置的程度。

- **即来即用（walk-up usable）**应用可以被用户立即有效地使用，几乎没有配置要求或维护负担。例如，Object Explorer 的配置要求极低甚至为零，一旦定义了本体，最终用户即可立即使用。
- **可定制（customizable）**应用需要前期投入（通常由专门的“构建者”用户承担），来实现一个为最终用户解决特定问题的界面。这也意味着更高的持续维护成本。然而，最终得到的应用通常是贴合用途的界面，精确满足特定工作流的需求。Workshop 和 Slate 就是这类定制的例子。

---

*原文：[Ontology-aware applications](https://www.palantir.com/docs/foundry/ontology/applications/)*
