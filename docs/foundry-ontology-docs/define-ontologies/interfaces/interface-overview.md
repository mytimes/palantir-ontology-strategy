# 接口（Interfaces）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/interfaces/interface-overview/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

**接口（interface）**是一种本体类型，用于描述对象类型的形状及其能力。借助接口，可以对共享共同形状的对象类型进行一致的建模与交互。例如，`Facility`（设施）接口可能包含 `Facility Name`（设施名称）和 `Location`（位置）属性。`Facility` 可以由 `Airport`（机场）、`Manufacturing Plant`（制造工厂）或 `Maintenance Hangar`（维修机库）等对象类型实现，这些对象类型各自还可以包含额外的、特定于类型的属性。

![`Facility` 接口示例。](https://www.palantir.com/docs/resources/foundry/interfaces/interface-example.png?width=800)

通过使用 `Facility` 接口，工作流可以与 `Airport`、`Manufacturing Plant` 和 `Maintenance Hangar` 对象类型交互——无论是聚合在一起还是分别交互——而无需了解这些对象类型的具体细节。此外，如果引入了实现 `Facility` 接口的新对象类型，工作流将立即与新对象类型兼容，无需额外重构。

请查看[当前支持程度](https://www.palantir.com/docs/foundry/interfaces/interface-overview/#current-levels-of-support)，进一步了解可以在平台的哪些位置使用接口。

## 接口特性

接口由接口属性、[链接类型约束](https://www.palantir.com/docs/foundry/interfaces/interface-link-types-overview/)、[动作类型约束](https://www.palantir.com/docs/foundry/interfaces/interface-action-type-constraints/)以及关于该接口的[元数据](https://www.palantir.com/docs/foundry/interfaces/interface-metadata/)组成。接口属性可以在接口上本地定义（推荐），也可以使用[共享属性](https://www.palantir.com/docs/foundry/object-link-types/shared-property-overview/)定义。一个接口可以由多个对象类型实现。

与编程语言中的接口非常相似，你可以[扩展接口](https://www.palantir.com/docs/foundry/interfaces/extend-interface/)来创建一个继承原接口属性的子接口，然后向子接口添加新的、更具体的属性。随后，对象类型可以[实现该接口](https://www.palantir.com/docs/foundry/interfaces/implement-interface/)，以表明它们符合接口定义。对象类型可以实现多个接口，以便在不同的工作流中使用。接口也可以扩展多个其他接口，包括那些自身又扩展了其他接口的接口，从而产生通过多层接口继承而来的属性。

## 接口与对象类型的区别

在本体中，接口与对象类型之间既存在功能上的差异，也存在样式上的差异。

对象类型是具体的：它们具有由共享属性或本地属性定义的模式（schema），由包含属性值的数据集支撑，并且可以被实例化为对象。

相比之下，接口是抽象的：它们具有由接口属性定义的模式，不由数据集支撑，不能直接实例化，而必须实例化为某个特定的对象类型。

在样式上，接口在平台中通过图标周围的虚线边框与对象类型在视觉上区分开来。

![接口图标示例](https://www.palantir.com/docs/resources/foundry/interfaces/interface-icon-example.png?width=100)

## 接口权限

接口通过[本体角色](https://www.palantir.com/docs/foundry/object-permissioning/ontology-permissions-legacy/#ontology-roles)进行权限控制。

## 当前支持程度

随着对接口这种本体类型的支持不断扩展，其在 Palantir 平台各部分的可用性会有所不同。

以下应用和服务目前支持接口：

- **[Ontology Manager](https://www.palantir.com/docs/foundry/ontology-manager/overview/)：**定义、编辑和实现接口。
- **[Marketplace](https://www.palantir.com/docs/foundry/marketplace/overview/)：**打包和安装接口。
- **[Functions](https://www.palantir.com/docs/foundry/functions/overview/)：**TypeScript v2 函数。

以下应用和服务部分支持接口：

- **[Actions](https://www.palantir.com/docs/foundry/action-types/overview/)：**定义动作，以创建、修改、删除实现某接口的对象或为其建立链接。接口动作类型约束处于 beta 阶段，可用于定义实现同一接口的各对象类型应具备的动作能力。
- **[Object Set Service](https://www.palantir.com/docs/foundry/object-backend/overview/#object-set-service-oss)：**按接口搜索和排序对象。按接口聚合的支持正在开发中。接口链接类型的支持正在开发中。
- **[Ontology SDK](https://www.palantir.com/docs/foundry/ontology-sdk/overview/)：**将接口用作 API 层，与实现接口的对象类型交互。支持情况因语言而异；目前支持 TypeScript，对 Java 和 Python 的支持正在开发中。

接口正在活跃开发中，但以下方面尚未支持：

- **[Workshop](https://www.palantir.com/docs/foundry/workshop/overview/)**
- **[Functions](https://www.palantir.com/docs/foundry/functions/overview/)：**TypeScript v1 和 Python 函数

## 接口入门

要向你的本体添加接口，你可以[创建](https://www.palantir.com/docs/foundry/interfaces/create-interface/)新接口或[扩展](https://www.palantir.com/docs/foundry/interfaces/extend-interface/)现有接口。拥有接口后，你可以让形状合适的对象类型[实现](https://www.palantir.com/docs/foundry/interfaces/implement-interface/)该接口，也可以随着本体的演进[编辑](https://www.palantir.com/docs/foundry/interfaces/edit-interface-definition/)接口，使其更适合你的组织。

---

*原文：[Interfaces](https://www.palantir.com/docs/foundry/interfaces/interface-overview/)*
