# 属性（Properties）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/properties-overview/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

[对象类型](https://www.palantir.com/docs/foundry/object-link-types/object-types-overview/)的**属性**是现实世界实体或事件的某项特征的模式定义。**属性值**是指对象上某个属性的值，即该现实世界实体或事件的单个实例。

例如，在 Ontology Manager 中，`Employee` 对象类型可能具有属性 `employee number`（员工编号）、`start date`（入职日期）和 `role`（角色）。虚构员工“Melissa Chang”的属性值可能是：`employee number` 为“11502”，`start date` 为“October 9, 2016”，`role` 为“software engineer”。

类似地，在 Ontology Manager 中，`Flight` 对象类型可能具有属性 `departure date`（出发日期）、`arrival date`（到达日期）和 `passenger count`（乘客数量）。对象“JFK → SFO 24-02-2021”的属性值可能是：`departure date` 为“24-02-2021”，`arrival date` 为“25-02-2021”，`passenger count` 为“150”。

支撑本体的概念与数据集结构中的概念存在对应关系。本体中属性的定义类似于数据集中列的定义，而属性值的定义类似于数据集中字段的定义。例如，`Employee` 数据集可能具有 `employee number`、`start date` 和 `role` 列。在这种情况下，员工“Melissa Chang”所在行的 `employee number` 列的单个字段值将为“11502”。

Foundry 本体并非抽象数据模型，而是将每个本体概念映射到组织的实际数据，使这一数据资产能够驱动现实世界应用。在 Ontology Manager 中为对象类型添加支撑数据源，即可在用户应用中创建和显示属性值。要为 `Employee` 类型对象上的 `employee number`、`start date` 和 `role` 属性创建属性值，组织需要为 `Employee` 对象类型添加支撑数据源，并将其员工名录和其他企业数据接入本体。

## 受支持的属性类型

| 属性基础类型 | 可作为标题键？ | 可作为主键？ | 备注 |
| --- | --- | --- | --- |
| 常用：`String`、`Integer`、`Short` | 是 | 是 |  |
| 基于时间：`Date`、`Timestamp` | 是 | 不建议 | 通常情况下，时间值不适合作为主键，因为存储格式与显示格式不同，可能导致意外的冲突/唯一性问题。在大多数情况下，我们建议改用 `String`。 |
| 数字类：`Boolean`、`Byte`、`Long` | 是 | 不建议 | `Boolean` 会将你的对象类型限制为两个对象实例。`Byte` 属性只能在动作中通过 `Integer` 参数赋值，因此在大多数情况下我们建议改用 `Integer` 属性。`Long` 在 JavaScript 中存在[表示问题 ↗](https://www.w3schools.com/js/js_numbers.asp#:~:text=JavaScript%20Numbers%20are%20Always%2064,the%20international%20IEEE%20754%20standard.)，因此并非所有前端库和代码都能很好地处理大于 1e15 的 `Long` 值。在大多数情况下，我们建议改用 `String`。 |
| 浮点类：`Float`、`Double`、`Decimal` | 是 | 否 |  |
| `Vector` | 否 | 否 |  |
| `Array` | 是 | 否 | 数组属性不能包含 null 元素。如果 `Array` 的内部类型不是有效的标题属性，则 `Array` 属性也不能用作标题属性。Object Storage v2 不支持嵌套数组。 |
| `Struct` | 否 | 否 | 结构体属性不支持嵌套，且字段不能是数组。有关受支持字段类型的详细信息，请参阅[结构体文档](https://www.palantir.com/docs/foundry/object-link-types/structs-overview/#struct-configuration)。 |
| `Media Reference`、`Time Series`、`Geotemporal Series`、`Attachment` | 否 | 否 |  |
| `Geopoint` | 是 | 否 | Geopoint 值以逗号分隔的字符串存储，格式为 `latitude,longitude`（例如 `57.64911,10.40744`）。另请参阅[在本体中使用地理空间数据](https://www.palantir.com/docs/foundry/geospatial/ontology/#points)、[创建 GeoPoint](https://www.palantir.com/docs/foundry/pb-functions-expression/constructGeoPointV1/)、[转换为本体 GeoPoint](https://www.palantir.com/docs/foundry/pb-functions-expression/createOntologyGeopointV1/)和[是否为有效的本体 GeoPoint](https://www.palantir.com/docs/foundry/pb-functions-expression/isValidOntologyGeopointV1/)。 |
| `Geoshape` | 否 | 否 |  |
| `Marking` | 否 | 否 |  |
| `Cipher` | 是 | 否 |  |

要详细了解基础类型，请参阅[基础类型](https://www.palantir.com/docs/foundry/object-link-types/base-types/)。

---

*原文：[Properties](https://www.palantir.com/docs/foundry/object-link-types/properties-overview/)*
