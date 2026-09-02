![本体概览头图](https://www.palantir.com/docs/resources/foundry/ontology/ontology-overview-header.png)

# 本体构建（Ontology Building）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/ontology/overview/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

Palantir 的**本体（Ontology）**是组织的运营层。本体位于已集成进 Palantir 平台的数字资产——[数据集（datasets）](https://www.palantir.com/docs/foundry/data-integration/datasets/)、[虚拟表（virtual tables）](https://www.palantir.com/docs/foundry/data-integration/virtual-tables/)与[模型（models）](https://www.palantir.com/docs/foundry/integrate-models/integrate-overview/)——之上，并将它们与现实世界中的对应物连接起来：从工厂、设备、产品等物理资产，到客户订单、金融交易等概念。在许多场景中，本体扮演着组织数字孪生的角色，同时包含支持各类用例所需的语义要素（对象、属性、链接）与动力学要素（动作、函数、动态安全）。

## 对象类型与链接类型（Object and Link Types）

定义组织的语义，是通过把现有数据源映射为本体中的**对象、属性与链接（objects, properties, links）**来实现的。本体远超数据编目或模式设计类解决方案：它能为最终用户工作流定义坚实的基础，为所有字段提供丰富的元数据，并为所有变更提供细粒度的安全与治理。

了解如何创建本体的语义要素：[对象类型（object types）](https://www.palantir.com/docs/foundry/object-link-types/object-types-overview/)与[链接类型（link types）](https://www.palantir.com/docs/foundry/object-link-types/link-types-overview/)。

## 动作类型与函数（Action Types and Functions）

组织的动力学——在遵守组织控制与治理的前提下促成变更——通过本体中的**动作类型（action types）**与**函数（functions）**来定义。动作类型让你能够从组织中的操作人员处采集数据，或编排连接到你现有系统的决策流程；函数则提供了一种编写并演进任意复杂度业务逻辑的方式。

了解如何创建本体的动力学要素：[动作类型（action types）](https://www.palantir.com/docs/foundry/action-types/overview/)与[函数（functions）](https://www.palantir.com/docs/foundry/functions/overview/)。

## 接口（Interfaces）

**接口（interface）**是一种本体类型，用于描述对象类型的结构（shape）及其能力。接口提供对象类型多态能力，允许对共享相同结构的对象类型进行一致的建模和交互。

进一步了解[接口（interfaces）](https://www.palantir.com/docs/foundry/interfaces/interface-overview/)。

## 为决策赋能（Powering Decision-Making）

投资本体的目标，是让组织能够规模化地做出更好的决策。为此，本体与 Palantir 面向用户的分析与运营工具深度集成：用户可以创建可复用的 Object Views（对象视图），在 Object Explorer 中搜索感兴趣的对象，在 Quiver 中执行复杂分析，在 Workshop 中构建高质量应用，等等。

[进一步了解如何在面向用户的应用中利用本体。](https://www.palantir.com/docs/foundry/ontology/applications/)

> **✅ 说明：Palantir Learning 门户**
>
> 了解理论之后，欢迎通过 [learn.palantir.com ↗](https://learn.palantir.com/deep-dive-creating-your-first-ontology) 上的课程，动手构建你的第一个本体。

**下一篇：**[为什么要创建本体？ →](https://www.palantir.com/docs/foundry/ontology/why-ontology/)

---

*原文：[Ontology building • Ontology • Palantir](https://www.palantir.com/docs/foundry/ontology/overview/)*
