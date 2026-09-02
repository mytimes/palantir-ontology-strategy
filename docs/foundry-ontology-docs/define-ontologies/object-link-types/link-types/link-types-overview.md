# 链接类型（Link types）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/link-types-overview/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

**链接类型（link type）**是两个对象类型之间关系的模式（schema）定义。**链接（link）**是指同一本体中两个对象之间该关系的单个实例。

例如，在 Ontology Manager 中，你可以在 `Employee`（员工）对象类型和 `Company`（公司）对象类型之间创建一个链接类型，定义 `Employee`（员工）与 `Employer`（雇主）之间的关系。链接是指 `Employee → Employer` 链接类型的单个实例，比如虚构员工“Melissa Chang”与其雇主“Acme, Inc.”之间的关系。

类似地，在 Ontology Manager 中，你可以在 `Flight`（航班）对象类型和 `Aircraft`（飞机）对象类型之间创建一个链接类型，定义 `Scheduled Flight`（计划航班）与 `Assigned Aircraft`（分配的飞机）之间的关系。链接是指 `Scheduled Flight → Assigned Aircraft` 链接类型的单个实例，比如“JFK → SFO 24-02-2021”与其分配的飞机“Boeing 737-123”之间的关系。

链接也可以存在于两个相同类型的对象之间。链接类型 `Direct Report ↔ Manager`（直接下属 ↔ 经理）可以定义在 `Employee` 对象类型与其自身之间。

请注意，不支持跨不同本体的对象类型之间的链接。在这种情况下，你可能更倾向于利用共享本体。

## 方向性

链接类型是双向的：它始终有两个**侧（side）**，分别对应它关联的两个对象类型。链接类型的每一侧都可以独立遍历，并有各自的显示名和 [API 名称](https://www.palantir.com/docs/foundry/functions/api-objects-links/)。例如，单个 `Flight ↔ Aircraft` 链接类型包含一个 `Flight` 侧和一个 `Aircraft` 侧。在代码中，调用 `flight.assignedAircraft.get()` 会遍历链接类型的 `Aircraft` 侧，以检索分配给某个航班的飞机；调用 `aircraft.flights.all()` 会遍历 `Flight` 侧，以检索分配给该飞机的航班。

> **ℹ️ 提示：说明**
>
> 在两个对象类型之间创建单个链接类型并不会隐式创建第二个反向链接类型。实际上，该单个链接类型已经通过其两侧支持双向遍历，因此无需分别定义一个从 `Aircraft` 到 `Flight` 的链接类型和另一个从 `Flight` 到 `Aircraft` 的链接类型来表示同一关系。

你可以在相同的两个对象类型之间定义多个不同的链接类型，但每个链接类型代表一个独立的真实关系，而不是现有链接类型的反向。例如，除了表示分配飞机的 `Flight ↔ Aircraft` 链接类型之外，你还可以定义第二个独立的 `Flight ↔ Aircraft` 链接类型，表示计划维护记录。每个链接类型在其两侧需要唯一的 [API 名称](https://www.palantir.com/docs/foundry/object-link-types/create-link-type/#define-link-type-names)，以便应用能够区分这些关系。

支撑本体的概念在数据集结构中有对应的类似概念。本体中链接类型的定义类似于两个数据集之间的连接（join），而链接的定义类似于某一行与另一个数据集中对应行各字段相连接的结果。例如，你可以将 `Employee` 数据集与 `Company` 数据集连接，以探索 `Employees` 与其 `Employers` 之间的关系。在连接后的数据集中，将“Melissa Chang”与其雇主“Acme, Inc.”连接起来的单行即代表一条链接。

Foundry 本体并非抽象的数据模型，而是将每个本体概念映射到组织的实际数据，使该数据资产能够驱动实际应用。在 Ontology Manager 中，通过向链接类型所引用的对象类型添加支撑数据源，链接会在用户应用中创建并显示。对于对象类型以多对多基数相关联的链接类型，数据源支撑链接类型本身。为了创建 `Employee → Employer` 类型的链接，组织会向 `Employee` 和 `Company` 对象类型添加支撑数据源，并将其员工名录和其他企业数据接入本体。

请从学习如何[创建新链接类型](https://www.palantir.com/docs/foundry/object-link-types/create-link-type/)开始。

---

*原文：[Link types](https://www.palantir.com/docs/foundry/object-link-types/link-types-overview/)*
