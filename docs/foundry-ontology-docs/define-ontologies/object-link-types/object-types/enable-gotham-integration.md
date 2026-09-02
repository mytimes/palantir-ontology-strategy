# 通过类型映射启用 Gotham 集成（Enable Gotham integration through type mapping）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/enable-gotham-integration/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

> **ℹ️ 提示**
>
> 如果你的注册实例包含地图渲染服务（Map Rendering Service，MRS），则*无需*完成类型映射流程即可启用 Gotham 集成。你可以[从 Gaia 创建新的本体对象](https://www.palantir.com/docs/foundry/object-link-types/create-ontology-objects-from-gaia/)或[将现有对象添加到](https://www.palantir.com/docs/foundry/geospatial/add-ontology-data-to-gaia/) Gotham，无需额外配置。请按照下方说明检查你的注册实例是否包含 MRS。有关 MRS 的可用性、安装或 Gotham 中提供的 MRS 附加文档的问题，请联系 Palantir 支持团队。

## 如何检查你的注册实例是否包含 MRS

1. 启动 [Quicksearch](https://www.palantir.com/docs/foundry/compass/quicksearch/) 以搜索并选择 **Gaia Home**。
2. 打开一个现有 Gaia 地图，或创建一个新的 Gaia 地图。
3. 从左侧面板选择 **Data sources（数据源）**。
4. 在 **Find...** 搜索栏中搜索 `Object search`。

如果你能够找到并选择 **Object search**，则说明你的注册实例包含 MRS。

![Gaia 左侧面板中的“数据源”选项卡显示 Object search，作为 MRS 已安装的标志。](https://www.palantir.com/docs/resources/foundry/object-link-types/gaia-mrs-installation-indicator.png)

---

## 使用类型映射创建本体的统一表示

类型映射在 Foundry 和 Gotham 之间实现本体的统一表示，你可以在 Foundry 的 [Ontology Manager](https://www.palantir.com/docs/foundry/ontology-manager/overview/) 应用中对其进行管理。你可以基于现有的 Foundry 对象类型、属性类型和共享属性类型创建新的 Gotham 类型；随着本体随时间演进，这些类型将保持同步。完成类型映射流程后，Gotham 将能够通过[对象集服务（Object Set Service）](https://www.palantir.com/docs/foundry/object-backend/overview/#object-set-service-oss)查询 Foundry 对象类型及其元数据——对象集服务是一个支持对象数据搜索、筛选、聚合和加载的 Foundry 后端服务。

> **ℹ️ 提示**
>
> 类型映射仅适用于同时使用 Foundry 和 Gotham 的注册实例，并且在使用前必须由平台管理员启用。为某个 Foundry 本体启用类型映射后，将无法禁用。每个 Gotham 安装实例中只能有一个 Foundry 本体启用类型映射。有关启用类型映射的协助，请联系 Palantir 支持团队。

### 何时对本体中的对象类型进行类型映射

如果你的注册实例不[包含 MRS](#如何检查你的注册实例是否包含-mrs)，并且满足以下任一条件，请[按照下方说明](#在-foundry-的-ontology-manager-中开启类型映射)在 Ontology Manager 中对对象类型进行类型映射：

- 你计划使用自定义符号体系，而该符号体系既不是[战术图形](https://www.palantir.com/docs/foundry/object-link-types/create-ontology-objects-from-gaia/#configure-a-tactical-graphic-on-your-gaia-map-and-tag-it-to-an-object-type)也不是 [MIL-STD 2525 符号](https://www.palantir.com/docs/defense-osdk/api/common/interfaceTypes/com-palantir-ontology-defense-types-mil2525CSymbol/)。无论是否进行类型映射，都支持 [Blueprint ↗](https://blueprintjs.com/) 符号。
- 你计划配置[搜索模板](https://www.palantir.com/docs/foundry/geospatial/add-ontology-data-to-gaia/#create-a-search-template)、[函数支撑的企业地图图层（Enterprise Map Layer，EML）](https://www.palantir.com/docs/foundry/geospatial/add-ontology-data-to-gaia/#use-a-function-backed-enterprise-map-layer)或[版本化对象集支撑的 EML](https://www.palantir.com/docs/foundry/geospatial/add-ontology-data-to-gaia/#use-a-versioned-object-set-backed-enterprise-map-layer)，以将本体中的数据添加到 Gaia。

## 在 Foundry 的 Ontology Manager 中开启类型映射

要将 Foundry 本体中的数据与 Gotham 集成，你首先需要按照以下步骤在 Foundry 的 Ontology Manager 中为目标对象类型开启类型映射：

1. 从主屏幕启动 Ontology Manager。
2. 找到并选择你要进行类型映射的对象类型。
3. 在对象类型的左侧面板中选择 **Capabilities（能力）**。
4. 向下滚动到 **Gotham Integration（Gotham 集成）** 面板，开启 `Allow objects of this type to be accessed from Gotham applications`（允许从 Gotham 应用访问此类型的对象）。

![用户可以在 Foundry 的 Ontology Manager 应用中为对象类型开启 Gotham 集成。](https://www.palantir.com/docs/resources/foundry/object-link-types/enable-gotham-mapping.png)

> **ℹ️ 提示**
>
> 经过类型映射的对象必须包含 `geopoint` 属性才能显示在 Gaia 地图上。该属性可以原生存在于对象类型的支撑数据集中，也可以使用 Pipeline Builder 的[创建本体 geopoint](https://www.palantir.com/docs/foundry/pb-functions-expression/createOntologyGeopointV1/)转换功能，从纬度/经度对或 `geopoint` 派生而来。

## 配置对象类型的父类别和 Gotham 属性类型

开启 **Gotham 集成**后，你可以按照以下步骤，基于现有的 Foundry 对象类型创建新的 Gotham 对象类型、指定该对象类型的 **Parent category（父类别）**，并配置其 **Property types（属性类型）**：

1. 在 **Gotham Integration** 的 **Object type** 部分选择 **Create a new object type**（创建新对象类型），以基于现有的 Foundry 对象类型在 Gotham 中创建新的对象类型。
2. 根据你的用例，在 `Entity`（实体，例如人员、组织或车辆）、`Event`（事件，例如航班、会议或音乐会）或 `Document`（文档，例如 PDF 文件、文本文档或报告）之间进行选择，以确定新对象类型在 Gotham 中的 **Parent category（父类别）**。
3. 使用 **Property types** 部分将 Foundry 对象类型属性映射到 Gotham——属性可以被共享或克隆到 Gotham 本体中。完成某个属性的配置后，你会在该属性名称旁看到蓝色的 `Mapped`（已映射）标签。
  - `Do not map property to Gotham`（不将属性映射到 Gotham）是默认选项——未映射的 Foundry 属性不会传播到 Gotham 本体中。
  - `Assign to shared property`（分配给共享属性）使你能够选择一个*现有*共享属性进行映射。
  - `Promote to shared property`（提升为共享属性）会创建一个*新*共享属性，供其他对象使用。
  - `Create a local clone of the property in Gotham`（在 Gotham 中创建属性的本地克隆）会在 Gotham 中创建所选属性的副本，该副本与其应用兼容。

> **ℹ️ 提示**
>
> Foundry 会自动对所有共享属性进行类型映射，使其在 Gotham 中可用。

1. 选择屏幕顶部功能区右侧的绿色 **Save**（保存）按钮并进行评审。
2. 评审对对象类型所做的更改，然后选择 **Save to ontology**（保存到本体）。

![用户可以使用 Criteria 面板集成其 Foundry 和 Gotham 本体，在此过程中创建新对象类型或接管现有对象类型，该面板使他们能够选择对象类型的父类别。](https://www.palantir.com/docs/resources/foundry/object-link-types/ontology-manager-gotham-integration-view.png)

将更改保存到本体后，向上滚动回到对象类型 **Capabilities** 页面的 **Gotham Integration** 部分。你现在会看到已为该对象类型分配 `Gotham URI`，并且能够查看 Gotham 报告的 `Installation status`（安装状态）。

Foundry 的 Ontology Manager 将显示以下安装状态之一：

- `Ready to integrate`（准备集成）：对象类型已准备好进行类型映射以启用 Gotham 集成。
- `Installation in progress`（安装进行中）：对象类型的安装流程正在进行。
- `Installation complete`（安装完成）：安装流程已完成，对象类型可在 Gotham 中使用。
- `Installation failed: {failureMessage}`（安装失败：{failureMessage}）：对象类型的安装至少失败过一次，并将在下一次安装运行时重试。`{failureMessage}` 概述了失败原因。常见的安装失败包括：
  - *实时重建索引（Live reindex）*：如果 Gotham 上正在运行实时重建索引，则本体无法更新。在此期间安装不会暂存更改，而是会失败，并在实时重建索引完成后自动重新运行。
  - *未安装必需的类型*：对象类型要成功安装，必须完成所有相关属性的安装（即映射），包括共享属性类型。
  - *其他本体更新*：如果有本体更新正在并发运行，类型映射器将无法更新本体，并会自动重新运行。

一旦对象类型的安装状态显示为 `Installation complete`，你就能够在 Gotham 的应用中搜索并使用该对象类型。

要在 Gotham 中弃用经过类型映射的对象类型，你可以在 Foundry 的 Ontology Manager 中删除该对象类型。在 Foundry 中删除后，相应的对象类型及其属性类型将无法在 Gotham 中访问，也不可供其应用使用。

> **ℹ️ 提示**
>
> Gotham 会建模 Foundry 数据集安全性和标记，这意味着通过类型映射在 Gotham 中提供的 Foundry 数据会携带相同的访问控制和密级。

---

*原文：[Enable Gotham integration through type mapping](https://www.palantir.com/docs/foundry/object-link-types/enable-gotham-integration/)*
