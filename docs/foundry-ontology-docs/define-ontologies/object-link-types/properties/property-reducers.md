# 属性归约器 [Beta]（Property reducers [Beta]）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/property-reducers/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

> **ℹ️ 提示：Beta**
>
> 属性归约器处于开发的 [Beta](https://www.palantir.com/docs/foundry/platform-overview/development-life-cycle/) 阶段，可能在你的注册实例上不可用。功能仍在活跃开发中，可能会发生变化。

**属性归约器**使你能够将[数组属性](https://www.palantir.com/docs/foundry/object-link-types/properties-overview/#supported-property-types)转换为数组中的单个值，用于显示和接口实现。归约*不会*更改底层属性类型或存储的属性数据；而是在读取属性值时提供对数组中归约值的访问。

例如，对包含多个检查日期的数组应用归约器，使你在表格或应用中查看该属性时仅显示最近的日期，同时确保完整数组仍可用于查询和其他操作。支持归约器的应用（例如 [Workshop](https://www.palantir.com/docs/foundry/workshop/overview/)）还使你能够在悬停或展开视图中查看完整数组。

归约器适用于包含数值、时间、字符串和布尔基础类型的数组属性。你可以基于使用受支持基础类型的任何结构体字段对[结构体](https://www.palantir.com/docs/foundry/object-link-types/structs-overview/)数组进行归约。查看[下方表格](#受支持和不受支持的基础类型)以详细了解属性归约器支持的基础类型。

## 何时使用属性归约器

在以下情况下使用属性归约器：

- 你的历史或时间数据存储在数组中，例如检查日期、状态更新或测量读数。
- 你希望在表格或应用中显示最新/最早、最高/最低或第一个/最后一个数组值，同时保留属性的完整历史。
- 数组属性需要非数组类型才能顺利实现[接口属性](https://www.palantir.com/docs/foundry/interfaces/implement-interface/)。

## 受支持和不受支持的基础类型

归约器基于底层基础类型处理数组属性。以下表格对哪些数组子类型支持归约器、哪些不支持进行了分类。

### 受支持的基础类型

属性归约器可用于包含以下子类型的数组：

| 类别 | 基础类型 | 归约器选项 |
| --- | --- | --- |
| **数值（Numeric）** | `Byte`、`Short`、`Integer`、`Long`、`Float`、`Double`、`Decimal` | 最高值、最低值 |
| **时间（Temporal）** | `Date`、`Timestamp` | 最近（最新）、最早（最久远） |
| **字符串（String）** | `String` | 第一个、最后一个（按字典序） |
| **布尔（Boolean）** | `Boolean` | 真值优先、假值优先 |
| **结构体（Struct）** | 基于[下文](#对结构体数组的支持)所述的任何受支持结构体字段 | 取决于结构体字段的基础类型 |

### 不受支持的基础类型

属性归约器*不*可用于包含以下子类型的数组：

- `Attachment`
- `Cipher Text`
- `Geohash`
- `Geoshape`
- `Geotemporal Series Reference`
- `Marking`
- `Media Reference`
- `Time Dependent`
- `Vector`

### 对结构体数组的支持

归约器在结构体数组上基于结构体内的特定**字段**（而非结构体本身）发挥作用。你只能基于使用上述[受支持基础类型](#受支持的基础类型)之一的结构体字段进行归约。你还可以使用不同的结构体字段配置多个归约器，以处理平局决胜场景。

## 配置属性归约器

1. 导航到 **Ontology Manager**。
2. 在左侧面板的 **Resources**（资源）下选择 **Object types**（对象类型），搜索并选择你的对象类型。
3. 从对象类型的 **Properties** 选项卡中选择要配置的数组属性。
4. 在右侧打开的属性编辑器面板中选择 **Interaction**（交互）选项卡。
5. 滚动到 **Reduce array**（归约数组）部分。

![带有 Interactions 选项卡的属性侧边栏](https://www.palantir.com/docs/resources/foundry/object-link-types/property-reducers-interactions-tab.png?width=800)

6. 选择 **Add array reducer**（添加数组归约器）。
7. 根据属性的基础类型选择你想要的[归约器选项](#受支持的基础类型)。

![添加归约器部分](https://www.palantir.com/docs/resources/foundry/object-link-types/property-reducers-add-reducer-section.png?width=600)

8. **保存**你的更改。

## 为结构体数组配置属性归约器

结构体数组为归约器提供了最大的灵活性。你可以基于结构体中使用[受支持基础类型](#受支持的基础类型)的任何字段进行归约，并配置多个归约器以处理平局决胜场景。

### 示例：客户评价历史

考虑一个 `Product`（产品）对象类型，它具有 `customerReviews`（客户评价）结构体数组属性，包含以下字段：

- `rating`（`Integer`）：一到五星的评分。
- `reviewDate`（`Date`）：客户发布评价的日期。
- `reviewerName`（`String`）：评价者的姓名。
- `verifiedPurchase`（`Boolean`）：指示购买是否经过验证。

`Product` 对象的 `customerReviews` 属性的示例数据如下：

| `rating` | `reviewDate` | `reviewerName` | `verifiedPurchase` |
| --- | --- | --- | --- |
| 5 | 2024-11-20 | Alice Chen | true |
| 3 | 2024-11-15 | Bob Smith | false |
| 4 | 2024-11-22 | Carol Lee | true |

### 配置单个归约器

> **✅ 说明**
>
> 在继续之前，请查看[属性归约器配置说明](#配置属性归约器)和[受支持基础类型](#受支持的基础类型)表格。

你可以在结构体字段上配置单个归约器，Foundry 使用它将结构体数组归约为单个结构体，并按配置了归约器的字段对要显示的值进行排序。

- **单个归约器：** 渲染最新的 `reviewDate`。
- **结果：** 显示包含 Carol Lee 在 `2024-11-22` 发布的四星评价的结构体。

用户仍然可以查询和访问完整的评价历史。

### 配置多个归约器

你可以配置多个归约器以处理平局决胜场景：

- **主归约器：** 对结构体排序并显示具有最新 `reviewDate` 的结构体。
- **后备归约器：** 渲染最高的 `rating`。
- **用例：** 如果两条评价在同一天发布，渲染评分较高的评价。

后备归约器*仅*在主归约器产生多个项时应用。主归约器始终首先求值，只有在主条件上平局的项才会由后备归约器进一步归约。对于任何额外配置的归约器，此归约行为会重复进行。

![结构体数组归约器配置](https://www.palantir.com/docs/resources/foundry/object-link-types/property-reducers-struct-configuration.png?width=600)

## 归约器在应用中的显示方式

支持归约器的应用在表格或列表等紧凑视图中显示归约值，同时通过展开视图或悬停机制提供对完整数组的访问。这种方法使用户能够快速浏览数据而无需看到冗长的数组表示，同时仍能在需要时检查完整数组。

例如，应用可能在表格单元格中仅显示 `2024-11-22`（最近日期），但当用户悬停或展开该单元格时显示完整的日期数组。

## 将属性归约器与接口一起使用

使用属性归约器确保数组属性可以实现非数组接口属性。这使具有数组数据的对象类型能够映射到期望单值的[接口属性](https://www.palantir.com/docs/foundry/interfaces/implement-interface/)。

### 示例：通过接口实现渲染最近的设备维护日期

**接口：** `Asset`

- 属性：`lastMaintenanceDate`（`Date`）

**对象类型：** `Equipment`

- 属性：`maintenanceHistory`（`Date` 数组）
  - 值：`[2024-01-15, 2024-03-22, 2024-11-01]`
- **归约器：** 最新的 `maintenanceHistory` 日期。
- **实现：** `Date` 数组属性通过配置的归约器实现单个 `Date`。用户通过接口查看该属性时将看到 `2024-11-01`。

`Equipment` 对象类型可以实现 `Asset` 接口，因为归约器使其 `maintenanceHistory` 数组属性能够表示为单个 `Date` 值。当用户通过 `Asset` 接口查看 `Equipment` 对象时，他们只看到最近的维护日期。

![通过归约器实现接口](https://www.palantir.com/docs/resources/foundry/object-link-types/property-reducers-interface-implementation.png?width=700)

> **⚠️ 注意**
>
> 当使用对象类型上的归约数组值来满足作为[接口动作](https://www.palantir.com/docs/foundry/action-types/actions-on-interfaces/)参数的接口属性时，在该类型的对象上调用动作将返回错误。但是，对于未通过归约数组值实现该接口的对象，你仍然可以使用该接口动作。

## 将属性归约器与结构体主字段组合使用

将属性归约器与[结构体主字段](https://www.palantir.com/docs/foundry/object-link-types/struct-main-fields/)组合使用时，你可以启用属性显示，并扩展这些属性可实现的接口的数量和形态。

考虑下面的 `Event` 对象类型，它包含一个 `Locations` 属性，该属性是结构体数组：

**对象类型：** `Event`

- 属性：`locations`（`Struct` 数组）
  - 结构体字段：`streetName`（`String`）、`dateCollected`（`Date`）、`numberOfGuests`（`Integer`）。
- **已配置主字段：** `streetName`
- **已配置归约器：** 按 `dateCollected` 取最近。

将 `streetName` 字段配置为主字段并将最近的 `dateCollected` 作为属性归约器后，即可支持多种接口实现选项：

| 配置 | 可以实现 |
| --- | --- |
| 两个功能均未配置 | 仅 `Struct Array` |
| 仅主字段 | `Struct Array`、`String Array` |
| 仅归约器 | `Struct Array`、`Struct` |
| **两者均配置** | `Struct Array`、`String Array`、`Struct`、`String` |

例如，同时配置结构体主字段和属性归约器后，你可以使用 `locations` 属性来满足接口中的*单个*字符串属性，例如下图中虚构的 `Event` 接口的 `Event street name`。

![通过归约器和主字段实现单个字符串](https://www.palantir.com/docs/resources/foundry/object-link-types/property-reducers-combination-single-string.png?width=700)

当你同时配置结构体主字段和属性归约器时，该转换：

- 应用配置的属性归约器，根据日期获取最近的位置。
- 提取配置的主字段并以字符串形式返回其值。

这意味着单个属性可以实现需要以下任何类型的接口：`Struct Array`、`String Array`、`Struct` 或 `String`。

## 限制和注意事项

- **无法直接查询归约值：** 你无法基于归约值进行筛选或查询。查询针对完整数组操作，而非归约表示。
- **归约或结构体主字段实现不支持接口动作：** 编辑通过属性归约器或[结构体主字段](https://www.palantir.com/docs/foundry/object-link-types/struct-main-fields/)实现的属性的[接口动作](https://www.palantir.com/docs/foundry/action-types/actions-on-interfaces/)在该类型的对象上调用时将返回错误。这是因为归约值和结构体主字段值无法转换回底层对象属性。例如，从数组中选择一个元素的归约器没有逆操作来重建完整数组；提取字段子集的结构体主字段无法填充其余字段。不针对这些属性的接口动作，或针对在没有归约器或结构体主字段的情况下实现接口的对象的接口动作，将按预期工作。

## 常见问题

### 配置后可以更改或移除归约器吗？

可以，你可以随时在 Ontology Manager 中修改或移除归约器。更改不需要重新索引，并会立即生效。

### 如果多个项在归约器条件上平局怎么办？

对于结构体数组，你可以配置后备归约器来处理平局。后备归约器仅对在主归约器上平局的项求值。对于原始类型数组或没有后备归约器的结构体，会以确定性但无序的方式返回其中一个平局值。

### 归约器可以与仅编辑属性一起使用吗？

可以，你可以为[仅编辑属性](https://www.palantir.com/docs/foundry/object-link-types/edit-only-properties/)配置归约器。归约器配置与该属性是否有支撑列无关。

### 归约器在所有应用中都有效吗？

各应用正在逐步推出对归约属性的支持。如果你在属性上配置了归约器并使用尚不支持归约器的应用，它不应破坏任何现有功能；该属性只会继续显示为数组。只有支持归约器的应用才能显示归约值。

---

*原文：[Property reducers [Beta]](https://www.palantir.com/docs/foundry/object-link-types/property-reducers/)*
