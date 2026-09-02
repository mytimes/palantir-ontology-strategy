# 核心概念（Core concepts）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/ontology/core-concepts/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

本页描述 Foundry 中与本体相关的主要概念。

## 本体

本体是对世界的分类。在 Foundry 中，本体是一个组织的数字孪生，将组织的数字资产（数据集和模型）整合为一个有机整体。Foundry 本体通过将数据集和模型映射到对象类型、属性、链接类型和动作类型，创建出组织世界的完整图景。

- [对象类型](#对象类型)定义组织中的一个实体或事件。
- [属性](#属性)定义对象类型的特征。
- [链接类型](#链接类型)定义两个对象类型之间的关系。
- [动作类型](#动作类型)定义如何修改对象类型。

组成本体的概念与数据集的结构有对应之处。你可以把每个对象类型类比为一个数据集；对象是对象类型的一个实例，正如一行是数据集中的一条记录。数据集中的列类比于对象的属性，因为它们为给定行提供附加信息。数据集字段中的值（就像电子表格中的单元格）类似于对象的属性值。正如数据集可以以各种方式连接（join）在一起，对象之间也可以基于属性值建立链接。下表总结了这一类比：

| 数据集 | 本体 |
| --- | --- |
| 数据集（Dataset） | 对象类型（Object type） |
| 行（Row） | 对象（Object） |
| 列（Column） | 属性（Property） |
| 字段（Field） | 属性值（Property value） |
| 连接（Join） | 链接类型（Link type） |

下图展示了这些概念如何组合在一起形成一个本体。下面的内容将继续更深入地定义本体的各个组成部分。

![航空业本体](https://www.palantir.com/docs/resources/foundry/ontology/airline-ontology.png)

## 对象类型

**对象类型**是对真实世界实体或事件的模式（schema）定义。**对象**指对象类型的单个实例；一个对象对应于单个真实世界实体或事件。**对象集**指多个对象实例的集合；也就是说，对象集代表一组真实世界实体或事件。

[进一步了解对象类型。](https://www.palantir.com/docs/foundry/object-link-types/object-types-overview/)

## 属性

对象类型的**属性**是对真实世界实体或事件某项特征的模式定义。**属性值**指某个对象上某一属性的值，即该真实世界实体或事件单个实例上的值。

[进一步了解属性。](https://www.palantir.com/docs/foundry/object-link-types/properties-overview/)

## 共享属性

**共享属性**是可以在你的本体中多个对象类型上使用的属性。共享属性支持跨对象类型的一致数据建模，以及属性元数据的集中管理。

[进一步了解共享属性。](https://www.palantir.com/docs/foundry/object-link-types/shared-property-overview/)

## 链接类型

**链接类型**是对两个对象类型之间关系的模式定义。**链接**指两个对象之间该关系的单个实例。

[进一步了解链接类型。](https://www.palantir.com/docs/foundry/object-link-types/link-types-overview/)

## 动作类型

**动作类型**是一种模式定义，描述用户可以一次性对对象、属性值和链接执行的一组变更或编辑。它还包括随动作提交而发生的副作用行为。在本体中配置好动作类型后，最终用户就可以通过应用动作来变更对象。

[进一步了解动作类型。](https://www.palantir.com/docs/foundry/action-types/overview/)

## 角色

**角色**是本体中的核心权限模型。与 Foundry 文件系统中的角色类似，本体角色授予对本体资源的访问权限。角色可以在本体级别或单个资源级别授予。

进一步了解[本体角色](https://www.palantir.com/docs/foundry/object-permissioning/ontology-permissions/)以及它们如何用于对象类型、链接类型和动作类型。

## 函数

**函数**是一段基于代码的逻辑，接收输入参数并返回输出。函数与本体原生集成：它们可以接收对象和对象集作为输入，读取对象的属性值，并可在基于本体构建的动作类型和应用中使用。

[进一步了解函数的总体信息](https://www.palantir.com/docs/foundry/functions/overview/)，或[进一步了解基于本体的函数](https://www.palantir.com/docs/foundry/functions/functions-on-objects/)。

## 接口

**接口**是一种本体类型，用于描述对象类型的结构（shape）及其能力。接口提供对象类型多态能力，允许对共享相同结构的对象类型进行一致的建模和交互。

进一步了解[接口](https://www.palantir.com/docs/foundry/interfaces/interface-overview/)。

## Object Views

**Object Views** 是与特定对象相关的所有信息和工作流的中心枢纽。这包括关于对象的关键信息、任何链接的对象和相关指标，以及与该对象相关的分析、仪表板和应用。

[进一步了解 Object Views。](https://www.palantir.com/docs/foundry/object-views/overview/)

---

*原文：[Core concepts](https://www.palantir.com/docs/foundry/ontology/core-concepts/)*
