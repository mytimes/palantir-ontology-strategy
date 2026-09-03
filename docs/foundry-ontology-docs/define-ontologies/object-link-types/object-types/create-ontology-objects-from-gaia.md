# 从 Gaia 创建本体对象（Create Ontology objects from Gaia）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/create-ontology-objects-from-gaia/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

与你可以[将本体中的数据添加到 Gaia 地图](https://www.palantir.com/docs/foundry/geospatial/add-ontology-data-to-gaia/)类似，你也可以通过将对象标记到实现了 Gaia Geoshape Creatable、Gaia Geopoint Creatable 或 MILSTD 2525C Symbol [接口](https://www.palantir.com/docs/foundry/interfaces/interface-overview/)的对象类型中，从你在地图上绘制的形状、放置的点或配置的战术图形创建本体对象。

> **⚠️ 注意**
>
> MILSTD 2525C Symbol 接口通过 Palantir 的 [Defense OSDK](https://www.palantir.com/docs/defense-osdk/api) 提供，它取代了 Gaia Milsym Creatable 接口；后者处于开发的[计划弃用](https://www.palantir.com/docs/foundry/platform-overview/development-life-cycle/#planned-deprecation)阶段，将于 2026 年 7 月前弃用。如有关于 Defense OSDK 接口可用性或你所在注册实例上 Gaia Milsym Creatable 接口弃用的问题，请联系 Palantir 支持。
>
> 如果你所在的注册实例上*两个*接口都可用，请实现 MILSTD 2525C Symbol 接口，而不是 Gaia Milsym Creatable 接口。

![Gaia 地图左侧面板中显示 Create shape（创建形状）窗口，用户可将在地图上绘制的形状标记为其本体中某个对象类型内的对象。](https://www.palantir.com/docs/resources/foundry/object-link-types/create-shape.png)

> **⚠️ 注意**
>
> 要从 Gaia 地图创建本体对象，你的注册实例必须同时使用 Foundry 和 Gotham。

以下各节概述了将 Gaia 形状、点和战术图形与本体集成的端到端流程：

- [安装所有必要的 Marketplace 产品](#安装前提-marketplace-产品)。
- [在由受限视图支撑的受支持对象类型上实现 Gaia Geoshape Creatable 接口](#创建实现-gaia-geoshape-creatable-接口的对象类型)。
- [在由受限视图支撑的受支持对象类型上实现 Gaia Geopoint Creatable 接口](#创建实现-gaia-geopoint-creatable-接口的对象类型)。
- [在由受限视图支撑的受支持对象类型上实现 MILSTD 2525C Symbol 接口](#创建实现-milstd-2525c-symbol-接口的对象类型)。
- [注册你的本体并在 Gaia 中搜索你的对象类型](#注册你的本体及其类型)。
- [了解创建对象的入口点](#在-gaia-地图上创建对象的入口点)。
- [在你的地图上绘制新形状并将其标记到你的对象类型中](#在-gaia-地图上绘制新形状并将其标记到对象类型)。
- [在你的地图上放置新点并将其标记到你的对象类型中](#在-gaia-地图上放置新点并将其标记到对象类型)。
- [在你的 Gaia 地图上配置战术图形并将其标记到你的对象类型中](#在-gaia-地图上配置战术图形并将其标记到对象类型)。

## 安装前提 Marketplace 产品

[Marketplace](https://www.palantir.com/docs/foundry/marketplace/overview/) 是 Foundry 的商店，提供已发布的数据产品或供用户安装的平台资源集合。你可以通过主屏幕上的 **Search...** 栏或 **Applications** 门户访问 Marketplace。

启动 Marketplace 后，安装以下产品：

- [来自 Core Ontology Store 的 Core Property Types](#来自-core-ontology-store-的-core-property-types)，其中包含核心共享属性类型，例如对象的密级（classification）和地理形状（geoshape），可用于你本体中的多个对象类型。
- [来自 Gaia App Store 的 Gaia Geoshape Creatable 接口](#来自-gaia-app-store-的-gaia-geoshape-creatable-接口)，它描述地理空间对象类型的形状，以便对相同形状的其他对象类型进行一致的建模和交互。
- [来自 Gaia App Store 的 Gaia Geopoint Creatable 接口](#来自-gaia-app-store-的-gaia-geopoint-creatable-接口)，它描述单点对象类型的位置。

### 来自 Core Ontology Store 的 Core Property Types

> **ℹ️ 提示**
>
> 如果 Marketplace 中没有包含 Core Property Types 产品的 Core Ontology Store，请联系 Palantir 支持，将 Core Ontology 作为 [Foundry 产品](https://www.palantir.com/docs/foundry/marketplace/foundry-products/)安装到你的 Foundry 注册实例上。

### 来自 Gaia App Store 的 Gaia Geoshape Creatable 接口

在 Foundry 中，[接口](https://www.palantir.com/docs/foundry/interfaces/interface-overview/)存在于你的本体中，用于描述对象类型的形状及其能力，从而在具有共同形状的对象类型之间实现一致的建模和交互。你可以在多个对象类型上实现同一个接口，并且接口可以扩展任意数量的其他接口。Gaia Geoshape Creatable 接口一旦在对象类型上实现，就能让你在本体中从 Gaia 地图上绘制的形状创建对象。

> **⚠️ 注意**
>
> Gaia Geoshape Creatable 接口取代了已弃用的 Gaia Geocreatable 接口。如果你无法在 Marketplace 中访问 Gaia Geoshape Creatable 接口，请联系 Palantir 支持。

要从 Marketplace 安装 Gaia Geoshape Creatable 接口：

1. 返回 Marketplace，在 **Search stores...** 中搜索 `Gaia App Store`。
2. 选择 `Gaia Geoshape Creatable` 产品。
3. 选择屏幕右侧的蓝色 **Install** 按钮以启动草稿安装。你可以选择为安装添加描述性后缀。
4. 指定安装将保存到的 **Namespace**（命名空间）。Marketplace 将在所选 **Namespace** 内自动创建一个新的 **Project**（项目）。
5. 在 **Permissions**（权限）下配置产品的密级和访问控制。你在 **Classification based access control (CBAC)**（基于密级的访问控制）下选择的密级定义了本次安装的*最高*密级。
6. 选择 **Next** 以启动安装窗口的 **Inputs**（输入）页面。
7. 在左侧面板 **Inputs** 下选择 **Shared properties**（共享属性）选项卡，将你本体中的 `Geoshape` 共享属性映射到你的新接口。
8. 选择 **Next** 以启动安装窗口的 **Content**（内容）页面。

![显示 Gaia Geoshape Creatable 接口的 Shared properties 页面，用户在此配置要包含在接口中的共享属性。](https://www.palantir.com/docs/resources/foundry/object-link-types/map-geoshape-creatable-inputs-as-spts.png)

1. 你可以选择开启 **Prefix Ontology entities**（为本体实体添加前缀）并输入有效的前缀。请注意，你的前缀不得包含某些特殊字符，例如圆括号或方括号。
2. 在屏幕右侧选择要启用的 **Ontology schema migrations**（本体模式迁移）。你可以在现有的[对象编辑与物化文档](https://www.palantir.com/docs/foundry/object-edits/schema-migrations/)中查阅更多模式管理信息。
3. 在选择 **Next** 之前，根据你的用例需要更新 **New versions**（新版本）中的自动配置。Marketplace 会预配置某些产品以自动升级。
4. 检查接口的配置并选择 **Install**。

### 来自 Gaia App Store 的 Gaia Geopoint Creatable 接口

要从 Marketplace 的 Gaia App Store 安装 Gaia Geopoint Creatable 接口，请从商店菜单中选择 `Gaia Geopoint Creatable` 产品。Marketplace 的接口安装工作流在不同接口之间是通用的，因此你可以按照上文 [Gaia Geoshape Creatable 接口安装说明](#来自-gaia-app-store-的-gaia-geoshape-creatable-接口)中列出的相同步骤操作，但有以下区别：

- 在 **Inputs** 窗口的 **Shared properties** 部分，将你本体中的 `Geopoint` 共享属性映射到 Gaia Geopoint Creatable 接口。

## 创建实现 Gaia Geoshape Creatable 接口的对象类型

在你本体中由[数据集](https://www.palantir.com/docs/foundry/data-integration/datasets/)或[受限视图](https://www.palantir.com/docs/foundry/security/restricted-views/)支撑的对象类型实现 Gaia Geoshape Creatable 接口后，Gaia 即可发现它们。在以下各节中，你将：

- [创建支撑对象类型的数据集或受限视图](#创建支撑对象类型的数据集或受限视图)。
- [创建对象类型并确保其与 Gotham 集成](#创建对象类型并确保其与-gotham-集成)。
- [配置动作类型以支持在 Gaia 中创建对象](#创建并配置动作类型以支持在-gaia-中创建对象)。
- [实现 Gaia Geoshape Creatable 接口](#实现-gaia-geoshape-creatable-接口)。

### 创建支撑对象类型的数据集或受限视图

> **ℹ️ 提示**
>
> 如果你计划根据用户的密级来保护对象，或通过应用[标记（markings）](https://www.palantir.com/docs/foundry/security/markings/)来控制文件访问，则应当创建受限视图。如果你的用例不需要基于密级的访问或标记所提供的额外对象安全性，那么应当创建数据集来支撑你的对象类型。

要创建可与 Gaia 集成的对象类型，你首先需要创建一个数据集或受限视图，其中至少包含以下列：

- `Geoshape`，将其设置为 `string`，以便 Gaia 用你所绘制对象的形状自动填充。
- `Object ID`，将其设置为 `string`，以便 Foundry 为你作为对象创建的每个 Gaia 形状自动填充唯一 ID。这将用作你的对象类型的主键。
- `Classification`，将其设置为 `array`，以记录对象的密级。*仅当*受限视图支撑你的对象类型时，才需要 `Classification` 列。

> **✅ 说明**
>
> 你可以根据具体用例在数据集或受限视图中配置额外的列，例如设置为 `string` 的 `Name`、`Category` 或 `Notes` 列，以记录用户输入的关于该对象的描述性信息。

在你的项目中选择 **New** 以上传现有文件（例如 `.csv`），或使用 [Fusion](https://www.palantir.com/docs/foundry/fusion/overview/) 创建独立数据集或支撑受限视图的数据集。如果你使用数据集来支撑对象类型，可以跳过下方的受限视图创建说明，直接前往[创建你的对象类型](#创建对象类型并确保其与-gotham-集成)。

![用户可从其项目中选择 New 按钮，将数据作为 .csv 上传，或创建新的 Fusion 电子表格来存储支撑受限视图的数据。](https://www.palantir.com/docs/resources/foundry/object-link-types/create-new-dataset-upload-fusion.png)

> **✅ 说明**
>
> 你可以在 Foundry 的[安全](https://www.palantir.com/docs/foundry/security/restricted-views/#create-restricted-views)和[对象权限](https://www.palantir.com/docs/foundry/object-permissioning/configuring-rv-access-controls/)文档中查阅更多受限视图创建说明。

你的受限视图应当包含一个[细粒度策略](https://www.palantir.com/docs/foundry/security/restricted-views/#compose-a-granular-policy)，根据用户的[密级访问权限](https://www.palantir.com/docs/foundry/security/classification-based-access-controls/)限制其对视图中数据的访问。编写细粒度策略应是 **Create '{restricted view}'** 窗口中的第一步。

![显示一个受限视图策略示例。](https://www.palantir.com/docs/resources/foundry/object-link-types/create-restricted-view.png)

> **⚠️ 注意**
>
> 如果对象包含 CBAC 或强制标记属性以限制其访问，那么 Foundry 会使用该对象从 Gaia 地图继承的 CBAC 或强制标记来创建对象，而*不会*应用地图安全与共享设置中定义的用户组限制。

你可以在下方参考一个 JSON 策略示例。

```json
{
  "condition": {
    "and": {
      "conditions": [
        {
          "markings": {
            "value": {
              "field": {
                "fieldName": "classification"
              },
              "type": "field"
            },
            "filters": [
              {
                "markingTypes": {
                  "markingTypes": [
                    "CBAC"
                  ]
                },
                "type": "markingTypes"
              }
            ]
          },
          "type": "markings"
        }
      ]
    },
    "type": "and"
  }
}
```

### 创建对象类型并确保其与 Gotham 集成

配置好受限视图后，启动 [Ontology Manager](https://www.palantir.com/docs/foundry/ontology-manager/overview/) 并按照以下步骤创建你的对象类型：

1. 从屏幕右上角选择 **New** > **Object type**。
2. 选择 **Use existing datasource**，然后选择 **Select datasource** 以找到并 **Select** 你的受限视图，再选择 **Next**。
3. 为你的对象类型命名，并可选择输入 **Description**。
4. 将 `Object ID` 设置为 **Primary Key**（主键），将 `Name` 设置为 **Title**（标题）。
5. 确保 `Classification` 的 **Property**（属性）是字符串数组，且 `Geoshape` 的 **Property** 是 geoshape（地理形状）类型。仅当受限视图支撑你的对象类型时，才需要验证前者。

![显示 Ontology Manager 的 Create a new object type 窗口，用户可在其中设置对象类型的主键和标题以及配置属性。](https://www.palantir.com/docs/resources/foundry/object-link-types/object-type-classification-array.png)

1. 选择 **Create**，因为你将在对象类型创建*之后*生成并配置动作类型。

草稿对象类型在 Ontology Manager 中可见后，接下来你要将 `Classification` 和 `Geoshape` 属性选作你本体中的共享属性类型。选择 **Overview** 下方的 **Properties** 面板，并按照以下步骤完成共享属性类型选择流程：

> **✅ 说明**
>
> 如果你使用不*包含* `Classification` 属性的数据集来支撑对象类型，则应针对你的 `Geoshape` 属性完成下方的步骤 1 和步骤 4。

1. 从属性列表中选择 `Classification`，以在屏幕右侧启动 **Property editor**（属性编辑器）窗口。
2. 更新 **Base type**（基础类型）下拉菜单，使其分别包含 `Mandatory control`（强制控制）和 `CBAC Marking`（CBAC 标记）。
3. 配置该属性的 **Max Classification**（最高密级）。

![显示 Property editor 窗口，用户在其中将属性映射为共享属性类型。](https://www.palantir.com/docs/resources/foundry/object-link-types/map-classification-spt.png?width=400)

> **⚠️ 注意**
>
> 如果你无法为 `Classification` 选择 **Mandatory control** 作为基础类型，请联系 Palantir 支持，因为 **Mandatory control** 标记并非在所有 Foundry 注册实例上都普遍可用。

1. 滚动到窗口底部的 **Shared property**（共享属性）部分，使用下拉菜单将 `Classification` 指定为共享属性。

![通过 Property editor 窗口的 Shared property 部分指定共享属性。](https://www.palantir.com/docs/resources/foundry/object-link-types/assign-classification-spt.png)

对你的 `Geoshape` 属性重复上述步骤 1 和步骤 4，因为你*不需要*配置其强制控制标记或密级。

继续之前，选择屏幕顶部的绿色 **Save** 按钮，将增量更改发布到你的本体。

如果你的 Foundry 注册实例包含 Map Rendering Service（MRS，地图渲染服务），那么你本体中具有地理空间属性类型的对象类型会自动与 Gotham 集成，你可以继续[创建动作类型以支持在 Gaia 中创建对象](#创建并配置动作类型以支持在-gaia-中创建对象)。按照 [Gotham 集成文档](https://www.palantir.com/docs/foundry/object-link-types/enable-gotham-integration/#how-to-check-if-your-enrollment-contains-mrs)中的步骤检查你的注册实例是否包含 MRS。如果你的注册实例*不*包含 MRS，则[按照说明](https://www.palantir.com/docs/foundry/object-link-types/enable-gotham-integration/#toggle-on-type-mapping-in-foundrys-ontology-manager)将你本体中的数据与 Gotham 集成。

> **ℹ️ 提示**
>
> 如有关于 MRS 安装或功能的问题，请联系 Palantir 支持。

### 创建并配置动作类型以支持在 Gaia 中创建对象

创建对象类型并确保其与 Gotham 集成后，返回 Ontology Manager 中的 **Overview** 窗口。在此创建一个[动作类型](https://www.palantir.com/docs/foundry/action-types/overview/)，使用户能够从形状创建和编辑对象，并从 Gaia 配置其属性（如 `Name` 和 `Category`）。

> **⚠️ 注意**
>
> 你的 Foundry 注册实例必须包含 MRS 才能在 Gaia 中编辑本体对象，因为此能力不扩展到通过类型映射添加到 Gaia 的对象。如有关于 MRS 安装或功能的问题，请联系 Palantir 支持。

按照以下步骤配置你的动作类型：

1. 从对象类型 **Overview** 窗口的 **Action types** 部分选择 **New**，以启动 **Create a new action type** 弹出窗口。

> **✅ 说明**
>
> 如果你无法选择 **Action types** 中的 **New** 按钮，可以在 **Datasources** 窗口中为你的对象类型开启 **Allow edits**（允许编辑）。

1. 在 **Object actions**（对象动作）下选择 **Modify or create object**（修改或创建对象），然后选择 **Next**。
2. 从 **Or create a new object with**（或使用以下方式创建新对象）下拉菜单中选择 **Auto-generated primary key**（自动生成的主键）。
3. 选择 **Add property** 将你现有的所有属性添加到动作类型，然后选择 **Next** 以配置动作类型的元数据。

![显示 Create a new action type 窗口，用户在其中映射用作动作输入的动作参数。](https://www.palantir.com/docs/resources/foundry/object-link-types/configure-create-or-modify-action-type-properties.png)

> **ℹ️ 提示**
>
> 仅当受限视图支撑你的对象类型时，才需要映射 `Classification` 属性。

1. 为你的动作类型命名，并可选择输入描述和更新其默认图标。
2. 选择可以执行该动作的 **Organization**（组织）、**Group**（用户组）或 **User**（用户），然后选择 **Create**。

选择屏幕顶部的绿色 **Save** 按钮，在继续之前将增量更改发布到你的本体。

接下来，你将按照以下步骤配置动作类型的 **Rules**（规则）和 **Parameters**（参数）：

1. 从屏幕左侧选择 **Rules**。
2. 选择 **Configure parameter**（配置参数）旁边的箭头图标，验证 `Geoshape` 属性的配置。
  - 从屏幕右侧的 **Type**（类型）下拉菜单中，确保 `Geoshape` 属性的类型为 `Geoshape` 或 `String`。
  - 确保在 **General**（常规）面板中选中 **Disabled**（已禁用）选项，这样用户就无法手动配置 `Geoshape` 的位置。

![显示 Ontology Manager 的 Create object 窗口，用户在 Rules 面板中映射属性以创建规则。](https://www.palantir.com/docs/resources/foundry/object-link-types/configure-create-object-type.png)

1. 从屏幕左侧的 **Form content**（表单内容）面板中选择你的 `Classification` 属性，并验证其 **Type** 为 `Mandatory control`。仅当受限视图支撑你的对象类型时，才需要配置 `Classification` 属性。
2. 选择 **Back to Form**（返回表单），通过选择 **Object Id** 面板最右侧的 **X** 图标，将 `Object Id` 从 **Form content** 中移除。Foundry 会为从 Gaia 创建的每个对象自动生成唯一 ID。
3. 选择屏幕顶部的绿色 **Save** 按钮，将动作类型的配置保存到本体。

### 实现 Gaia Geoshape Creatable 接口

在 Ontology Manager 中创建和配置对象类型的最后一步，是实现你[先前从 Marketplace 安装的](#来自-gaia-app-store-的-gaia-geoshape-creatable-接口) Gaia Geoshape Creatable 接口。返回对象类型的 **Overview** 页面，在将更改保存到本体之前，按照以下步骤实现该接口：

1. 前往屏幕左侧 **Object Views** 下方的 **Interfaces** 窗口。
2. 选择 **Implement new interface**（实现新接口），搜索 `Gaia Geoshape Creatable`，然后选择 **Next**。
3. 选择 **Choose an option**（选择一个选项）> **Replace existing**（替换现有），将你本体的 `Geoshape` 共享属性类型映射到实现该接口的对象类型。
4. 选择 **Confirm** 关闭 **Implement an interface** 窗口，并选择 **Save** 将新配置的接口保存到你的本体。

![Ontology Manager 的 Interfaces 窗口显示在对象类型上实现后的 Gaia Geoshape Creatable 接口。](https://www.palantir.com/docs/resources/foundry/object-link-types/implemented-gaia-geoshape-creatable-interface.png)

## 创建实现 Gaia Geopoint Creatable 接口的对象类型

要创建实现 Gaia Geopoint Creatable 接口的对象类型，你可以按照上文 [Gaia Geoshape Creatable 接口的对象类型创建说明](#创建实现-gaia-geoshape-creatable-接口的对象类型)中列出的相同步骤操作，但各节有以下区别：

- [创建支撑对象类型的受限视图](#创建支撑对象类型的数据集或受限视图)。
  - 创建 `Geopoint` 列而不是 `Geoshape` 列。
- [创建对象类型并确保其与 Gotham 集成](#创建对象类型并确保其与-gotham-集成)。
  - 在对象类型创建窗口中，确保 `Geopoint` 的 **Property** 是 geopoint（地理点）类型。
- [配置动作类型以支持在 Gaia 中创建对象](#创建并配置动作类型以支持在-gaia-中创建对象)。
  - 在动作类型的 **Parameters** 窗口中，确保你的 `Geopoint` 属性的 **Type** 为 `Geopoint`。
- [实现 Gaia Geoshape Creatable 接口](#实现-gaia-geoshape-creatable-接口)。
  - 搜索 `Gaia Geopoint Creatable` 而不是 `Gaia Geoshape Creatable` 接口。
  - 映射你本体的共享属性类型时，为 `Geopoint` 而不是 `Geoshape` 属性选择 **Replace existing**。

## 创建实现 MILSTD 2525C Symbol 接口的对象类型

> **⚠️ 注意**
>
> 与 Gaia Geoshape Creatable 和 Gaia Geopoint Creatable 接口不同，你**必须**使用受限视图来支撑实现 MILSTD 2525C Symbol 接口的对象类型。

要创建实现 MILSTD 2525C Symbol 接口的对象类型，你可以按照上文 [Gaia Geoshape Creatable 接口的对象类型创建说明](#创建实现-gaia-geoshape-creatable-接口的对象类型)中列出的相同步骤操作，但各节有以下区别：

- [创建支撑对象类型的受限视图](#创建支撑对象类型的数据集或受限视图)。
  - 你*只*需要创建 `Object ID`、`Classification` 和 `Title` 列。
- [创建对象类型并确保其与 Gotham 集成](#创建对象类型并确保其与-gotham-集成)。
  - 在对象类型创建窗口中，将 `Object ID` 设置为 **Primary key**（主键），将 `Title` 设置为 **Title**（标题）。

[配置动作类型以支持创建对象](#创建并配置动作类型以支持在-gaia-中创建对象)时，在选择 **Next** 之前，请确保在 **Create a new action type** 窗口中*至少*映射了 `Classification`、`Symbol Anchor Points` 和 `SIDC` 属性。

![显示实现 MILSTD 2525C Symbol 接口的对象类型的 Create a new action type 窗口，用户在其中映射用作动作输入的动作参数。](https://www.palantir.com/docs/resources/foundry/object-link-types/configure-milsym-action-type-properties.png)

选择屏幕顶部的绿色 **Save** 按钮，在继续之前将增量更改发布到你的本体。

接下来，你将按照以下步骤配置动作类型的 **Rules** 和 **Parameters**：

1. 从屏幕左侧选择 **Rules**。
2. 选择 `Classification` 属性右侧的箭头图标以 **Configure parameter**。

![动作类型创建窗口中的 Rules 面板显示映射为动作类型输入的属性。](https://www.palantir.com/docs/resources/foundry/object-link-types/configure-milsym-action-type.png)

1. 如果你的对象类型上的 `Classification` 属性由共享属性类型满足，那么你应当在 **General** 部分开启 **Disabled**，以确保创建的对象自动从你的 Gaia 地图继承其密级标记。如果你想在创建时手动设置密级标记，则不要开启 **Disabled**。
2. 在 **Set a parameter max classification**（设置参数最高密级）下选择 **Add**，以确保动作类型的最高密级与对象类型的最高密级相匹配。

![显示动作表单的 Classification 参数，用户可验证其类型、禁用其编辑并配置其最高密级。](https://www.palantir.com/docs/resources/foundry/object-link-types/configure-milsym-classification-parameter.png)

1. 选择屏幕顶部的绿色 **Save** 按钮，将动作类型发布到你的本体。

> **⚠️ 注意**
>
> 由于[目前不支持在动作中使用结构体属性](https://www.palantir.com/docs/foundry/object-link-types/structs-overview/#current-levels-of-support)，你将无法把 `Speed Modifier (Z)` 或 `Altitude/Depth Modifier (X)` 属性配置为动作类型参数。

- [实现 Gaia Geoshape Creatable 接口](#实现-gaia-geoshape-creatable-接口)。
  - 搜索 `MILSTD 2525C Symbol` 而不是 `Gaia Geoshape Creatable` 接口。
  - 映射你本体的共享属性类型时，为你的 `Classification` 属性选择 **Replace existing**，为其他属性选择 **Create edit-only property**（创建仅编辑属性）。

## 注册你的本体及其类型

现在，你已经有了实现 Gaia Geoshape Creatable、Gaia Geopoint Creatable 和 MILSTD 2525C Symbol 接口的对象类型，并且附带了相应的动作类型，使用户能够配置他们从 Gaia 地图上绘制的形状、放置的点和配置的战术图形创建的对象。接下来，你将根据你的注册实例，在 Gaia 的管理应用或 [Control Panel](https://www.palantir.com/docs/foundry/administration/control-panel/) 扩展中注册你的本体、对象类型和动作类型。

> **ℹ️ 提示**
>
> 要访问 Gaia 的管理应用，你必须是平台管理员。要访问 Gaia 的 Control Panel 扩展，你必须被授予 **Organization administrator**（组织管理员）角色。
> 如果你无法访问管理应用或 Control Panel 扩展，请联系 Palantir 支持。

要注册你的本体、对象类型和动作类型，请启动 Gaia 的管理应用或 Control Panel 扩展，并按照以下步骤操作：

1. 找到 **Ontology Config**（管理应用）/ **Ontology**（Control Panel 扩展）面板，并验证你本体的配置。如果你本体尚未配置，选择面板左侧的开关，将 **Ontology Config** / **Ontology** 设置为 `overridden`（管理应用）/ `Override`（Control Panel 扩展）。
2. 在 **Ontology RID** 和 **API Name** 文本框中输入你本体的 RID 和 API 名称。

> **✅ 说明**
>
> 要查找并复制你本体的 RID 和 API 名称，请前往 Ontology Manager，从其左侧面板底部选择 **Ontology configuration**，以启动 **Ontology metadata** 窗口。

![Gaia 管理应用和 Control Panel 扩展的 Ontology Config 面板。](https://www.palantir.com/docs/resources/foundry/object-link-types/gaia-admin-app-ontology-config.png)

1. 找到 **Foundry Object Creation Config** 面板，选择 **Show**（管理应用）/ **Override**（Control Panel 扩展）。
2. 在你注册实例现有对象和动作类型列表的底部选择 **Add**。
3. 将所有三个对象类型及其配套动作类型的 RID 分别复制并粘贴到三个独立的 **Object type rid** 和 **Action type rid** 文本框中。
4. 在右上角功能区中选择 **Preview and save**（管理应用）/ 在右下角选择 **Save for {Organization}**（Control Panel 扩展）。

![Gaia 管理应用和 Control Panel 扩展的 Foundry Object Creation Config 面板。](https://www.palantir.com/docs/resources/foundry/object-link-types/gaia-admin-app-foundry-object-creation-config.png)

你可以从 Ontology Manager 的 **Overview** 窗口获取对象类型的 RID。选择剪贴板图标以复制 RID。此外，你可以通过在 **Overview** 窗口的 **Action types** 部分选择 **Create {object type name}** 来获取动作类型的 RID。动作类型的 RID 也可以通过剪贴板图标复制。

![你可以从 Ontology Manager 的 Overview 窗口复制对象类型和动作类型的 RID。](https://www.palantir.com/docs/resources/foundry/object-link-types/combined-object-action-rid-copy.png)

接下来，你将启动 Gotham 的 Gaia 应用，使用其 **Add to map**（添加到地图）菜单，从你绘制的形状、放置的点和配置的战术图形创建对象。

## 在 Gaia 地图上创建对象的入口点

Gaia 提供两个不同的入口点，可用于在地图上创建对象；它们决定了 **Object type** 下拉菜单中渲染的可用对象类型列表。

- **先选择对象类型：** 当你从工具栏下拉菜单中选择对象类型时，Gaia 会列出实现了受支持接口的每个对象类型，包括 Gaia Geoshape Creatable、Gaia Geopoint Creatable、MILSTD 2525C Symbol 和 Gaia Milsym Creatable 接口。选择对象类型后，Gaia 会将可用的绘制工具筛选为*仅*与该对象类型所实现接口兼容的工具。例如，选择实现 Gaia Geoshape Creatable 接口的对象类型只会显示形状绘制工具。当你随后选择某个绘制工具时，Gaia 会打开左侧面板，并且 **Object type** 下拉菜单中已预填你所选的对象类型。

> **✅ 说明**
>
> 一个对象类型可以实现多个接口。例如，如果一个对象类型同时实现了 Gaia Geopoint Creatable 和 Gaia Geoshape Creatable 接口，那么从工具栏选择它时会同时显示点符号和所有形状绘制工具，同时筛除符号 **Search** 菜单。

- **先选择绘制工具：** 当你在选择对象类型之前先选择绘制工具时，Gaia 会打开左侧面板且不预选任何对象类型。在这种情况下，**Object type** 下拉菜单仅列出实现了与你所选工具匹配的接口的对象类型。例如，选择点符号只会显示实现 Gaia Geopoint Creatable 接口的对象类型。

### 在 Gaia 地图上绘制新形状并将其标记到对象类型

> **⚠️ 注意：关于国际日期变更线的当前限制**
>
> 虽然你可以在 Gaia 地图上创建跨越 180 度经线（即对向子午线，antimeridian）的地图标注，但你无法绘制以此方式跨越的、标记为 Foundry 对象的形状。如有关于标注创建、渲染或 Gotham 中提供的 Gaia 附加文档的问题，请联系 Palantir 支持。

开始之前，请查看[创建对象的入口点](#在-gaia-地图上创建对象的入口点)，了解你选择的对象类型和绘制工具如何决定哪些对象类型对你可用。

打开 Gaia 地图后，从地图左上方区域的菜单中选择对象图标，以从 **Draw annotation**（绘制标注）模式切换到 **Create object**（创建对象）模式。

![显示 Gaia 的工具栏。](https://www.palantir.com/docs/resources/foundry/object-link-types/select-object-map-tool-bar.png)

接下来，从同一菜单的右侧选择要绘制的形状。完成形状绘制后，**Create shape** 窗口将出现在 Gaia 的左侧面板中。按照以下步骤配置你的形状，并将其作为对象保存到你的本体：

1. 从 **Object type** 下拉菜单中搜索并选择你的对象类型。如果你是在地图画布上从工具栏的下拉菜单中选择的对象类型，Gaia 会自动用该值填充 **Create shape** 的下拉菜单。
2. 在选择 **Finish** 之前，为你的对象填写动作表单中的必填字段，例如 `Category` 和 `Name`。

![在 Gaia 地图上绘制了一个多边形，用户可将其地理标记到某个对象类型。](https://www.palantir.com/docs/resources/foundry/object-link-types/draw-polygon.png)

将形状保存为对象后，它将渲染为 Gaia 左侧面板中 **Layers**（图层）之一。你可以选择对象名称以在屏幕右侧启动 **Selection**（选择）面板，在其中查看其属性并自定义其外观。

![形状绘制后，用户可在 Gaia 地图上查看该形状的对象数据。](https://www.palantir.com/docs/resources/foundry/object-link-types/gaia-map-drawn-object-view.png)

你也可以在 Foundry 的 [Object Explorer](https://www.palantir.com/docs/foundry/object-explorer/overview/) 中查看你的对象。

![用户可以在 Foundry 的 Object Explorer 中查看他们绘制的对象。](https://www.palantir.com/docs/resources/foundry/object-link-types/view-drawn-object-in-foundry-oe.png)

#### 编辑现有形状对象

要编辑现有形状对象，请将光标悬停在左侧面板中的对象上，选择 **Edit selection**（编辑选择）以启动 **Edit shape** 窗口；你也可以双击地图上的对象来访问该窗口。

![用户可在 Gaia 地图上选择铅笔图标以在左侧面板启动 Edit shape 窗口，从而编辑对象。](https://www.palantir.com/docs/resources/foundry/object-link-types/edit-object.png)

选择并拖动任意顶点以调整形状，或选择形状边界内的某个点以将形状拖动到地图上的其他位置。完成更改后，在 **Edit shape** 窗口底部选择 **Finish**。

### 在 Gaia 地图上放置新点并将其标记到对象类型

开始之前，请查看[创建对象的入口点](#在-gaia-地图上创建对象的入口点)，了解你选择的对象类型和绘制工具如何决定哪些对象类型对你可用。

打开 Gaia 地图后，选择向下箭头以渲染符号 **Search** 菜单，你可以在其中选择一个可用符号放置在地图上的任意位置。如果你选择了某个可用的 **Tactical Graphics**（战术图形）符号，那么你只能在实现 [MILSTD 2525C Symbol 接口](#创建实现-milstd-2525c-symbol-接口的对象类型)的对象类型中创建对象。Gaia 会根据你地图的坐标系偏好自动填充 **Coordinates**（坐标）输入框。

![显示 Gaia 工具栏中的符号选择。](https://www.palantir.com/docs/resources/foundry/object-link-types/create-geopoint-from-tool-bar.png)

> **✅ 说明**
>
> 从 Gaia 顶部功能区选择 **File** > **Preferences** > **Coordinate system** 以更新默认坐标系。

**Create symbol** 窗口将出现在 Gaia 的左侧面板中，你可以在其中搜索并选择实现 Gaia Geopoint Creatable 接口的对象类型。接下来，在为对象输入 **Title** 之前，可以选择调整地理点的 **Bearing (mag)**（磁方位角）。选择 **Finish** 将放置的符号作为对象保存到你的本体。

将地理点保存为对象后，你可以与新的地图图层交互，并[以与地图上绘制的形状相同的方式](#在-gaia-地图上绘制新形状并将其标记到对象类型)在 Foundry 的 Object Explorer 中查看该对象。

#### 编辑现有地理点对象

你可以[以与编辑地图上绘制的形状相同的方式](#编辑现有形状对象)编辑现有地理点对象。

### 在 Gaia 地图上配置战术图形并将其标记到对象类型

> **⚠️ 注意**
>
> 你的 Foundry 注册实例必须包含 MRS 才能将战术图形标记到对象类型。如有关于 MRS 安装或功能的问题，请联系 Palantir 支持。

开始之前，请查看[创建对象的入口点](#在-gaia-地图上创建对象的入口点)，了解你选择的对象类型和绘制工具如何决定哪些对象类型对你可用。

[与添加新地理点以标记为对象类似](#在-gaia-地图上放置新点并将其标记到对象类型)，选择向下箭头以渲染符号 **Search** 菜单，你可以在其中选择一个可用的战术图形放置在地图上的任意位置。在 **Search symbols...** 输入框中输入你所需战术图形的名称，例如 `Brigade Support Area`（旅支援地域）。

![Gaia 的符号搜索窗口使用户能够搜索要添加到地图的符号或战术图形。](https://www.palantir.com/docs/resources/foundry/object-link-types/search-for-tactical-graphic.png)

**Create symbol** 窗口将出现在 Gaia 的左侧面板中，你可以参照上文的[形状创建说明](#在-gaia-地图上绘制新形状并将其标记到对象类型)，将战术图形添加到地图并将其标记到对象类型。

![Gaia 地图显示在地图画布上绘制并保存的战术图形。](https://www.palantir.com/docs/resources/foundry/object-link-types/draw-tactical-graphic.png)

#### 编辑现有战术图形对象

你可以按照上文的[形状对象编辑说明](#编辑现有形状对象)编辑现有战术图形对象。

### 将标注提升为对象

在 Gaia 中，*标注（annotation）*是仅存在于你当前地图的形状、点或符号。你可以将地图上的标注提升为本体中的对象，以便在其他用户创建的其他地图上使用——这些用户需有权访问相应的对象类型。

要将现有标注标记为对象，请双击地图上的标注，或将光标悬停在左侧面板 **Layers** 选项卡中标注的铅笔图标上，以启动形状、点或符号编辑窗口。接下来，选择 **Promote to object**（提升为对象），从 **Object type** 下拉菜单中选择你的对象类型，并在形状、点或符号编辑窗口中填写动作表单。

![Gaia 地图显示用户通过左侧面板将标注提升为对象。](https://www.palantir.com/docs/resources/foundry/object-link-types/promote-annotation.png)

要详细了解可用于将数据*从*你的本体*添加到* Gotham 的各种方法，请查阅现有的[地理空间数据集成文档](https://www.palantir.com/docs/foundry/geospatial/add-ontology-data-to-gaia/)。

---

*原文：[Create Ontology objects from Gaia](https://www.palantir.com/docs/foundry/object-link-types/create-ontology-objects-from-gaia/)*
