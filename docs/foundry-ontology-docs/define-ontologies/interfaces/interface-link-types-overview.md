# 接口链接类型约束（Interface link type constraints）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/interfaces/interface-link-types-overview/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

接口链接类型约束定义了实现某个接口的所有对象类型之间共有的对象间关系。用户可以为链接指定描述，并为链接类型指定 API 名以在代码中作为参考。当对象实现一个带有接口链接类型约束的接口时，对象类型上的具体链接类型用于满足接口链接类型约束。

![接口链接类型创建。](https://www.palantir.com/docs/resources/foundry/interfaces/create-link-type-constraint-modal.png?width=500)

如上面的示例所示，为了对设施与其所服务的航空公司之间的关系建模，`Facility`（设施）接口声明了一个可选的一对多链接类型约束，位于任何实现 `Facility` 接口的对象与 `Airline`（航空公司）对象类型之间。这意味着如果实现对象类型（例如 `Airport`（机场））拥有一个指向 `Airlines` 对象类型的具体链接类型，则可以通过接口链接类型 API 名访问该链接。

## 链接类型约束

链接类型约束定义了接口链接类型的各项参数。如果链接类型是必填的，所有实现对象类型都必须有一个满足这些约束的链接。这些参数包括：

- **链接目标类型：** 接口或对象类型。
- **目标：** 特定的接口或对象类型。
- **基数：** 一对一或一对多。
- 该链接是否为对象类型实现中的必填项。

## 链接目标：接口

当你想对两个抽象对象类型之间的关系建模时，应使用 `interface` 类型的链接目标。

例如，你可以使用接口链接目标来对 `Facility`（设施）与在该设施发生的 `Alert`（警报）之间的关系建模。由于设施有多种、警报也有多种，如果链接的每一端只能使用单个对象类型，就无法对两者之间的联系建模。相反，你可以通过定义一个 `Facility` 接口、一个 `Alert` 接口，以及 `Facility` 上一个设置为链接到 `Alert` 接口的接口链接来对此关系建模。然后你可以定义一个实现 `Facility` 接口的 `Airport`（机场）对象类型，以及一个实现 `Alert` 接口的 `Flight Alert`（航班警报）对象。在此基础上，你可以定义一个从 `Airport` 到 `Flight Alert` 的具体链接类型，以满足 `Facility` 接口的链接类型约束。

## 链接目标：对象类型

当接口与目标之间的关系是具体的、且这种具体性应由链接类型约束强制保证时，应使用 `object type` 类型的链接目标。

例如，你可以定义一个链接到 `Airlines`（航空公司）对象类型的 `Facility`（设施）接口。此接口链接将建模这样一个事实：无论设施类型是什么，你都期望它有一个链接，指向它所服务的特定航空公司。

## 基数

接口链接类型还可以进一步指定为 `ONE` 或 `MANY` 基数。这两种基数分别类似于一对一和一对多建模。`ONE` 基数表示实现接口的每个对象应链接到目标类型的一个对象。`MANY` 基数表示实现接口的每个对象可以链接到目标类型的任意数量的对象。

你应根据本体的建模需求决定使用 `ONE` 还是 `MANY`。在某些情况下，将链接的基数限制为单个对象可能更合理。例如，你可能想将 `Driver's License`（驾照）与 `Person`（人员）之间的关系建模为 `SINGLE` 基数链接，因为每张驾照只能属于一个具体的个人。如果关系允许更大的灵活性，例如 `Company`（公司）与其 `Shareholders`（股东）之间，你可能希望使用 `MANY` 基数链接来表示每个公司可以有一个或多个具体股东。

---

*原文：[Interface link type constraints](https://www.palantir.com/docs/foundry/interfaces/interface-link-types-overview/)*
