# 类型参考（Types reference）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/type-reference/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

定义本体时，你可以使用多种多样的类型来表示你带入 Foundry 的数据在现实世界中的定义。Foundry 中使用的类型分为*本体*类型或*数据*类型：

- **本体类型**用于将现实世界领域建模为本体。
- **数据**类型用于表示数据值。Foundry 中的数据类型受到 [RDF ↗](https://w3c.github.io/rdf-concepts/spec/#section-Datatypes)、[OWL ↗](https://www.w3.org/TR/owl-ref/#Datatype) 和 [XSD ↗](https://www.w3.org/TR/xmlschema-2/#datatype) 中类似概念的启发。

## 本体资源

以下类型可用于构建和定义你的本体。

### 对象类型

**对象类型**是对现实世界实体或事件的模式定义，由单个对象组成。例如，`JFK` 和 `LHR` 都可以是 `Airport`（机场）对象类型的对象。

[详细了解对象类型。](https://www.palantir.com/docs/foundry/object-link-types/object-types-overview/)

#### 属性

对象类型的**属性**是描述现实世界实体或事件的特征。例如，如果 `LHR` 是 `Airport` 对象类型的一个对象，那么 `name` 和 `country` 就是 `Airport` 的属性。对于 `LHR` 对象，其属性值如下：

- **name：** LHR
- **country：** United Kingdom

[详细了解属性。](https://www.palantir.com/docs/foundry/object-link-types/properties-overview/)

### 共享属性

**共享属性**是可以在本体中多个对象类型上使用的属性。共享属性支持跨对象类型的一致数据建模以及属性元数据的集中管理。

[详细了解共享属性。](https://www.palantir.com/docs/foundry/object-link-types/shared-property-overview/)

### 链接类型

**链接类型**是两个对象类型之间关系的模式定义。**链接**指两个对象之间该关系的单个实例。

[详细了解链接类型。](https://www.palantir.com/docs/foundry/object-link-types/link-types-overview/)

### 动作类型

**动作类型**是对对象、属性值和链接进行的一组更改或编辑的模式定义，用户可以一次性完成这些更改。动作类型还包括动作发生时产生的副作用行为。一旦在本体中配置了动作类型，最终用户就可以通过应用动作来更改对象。

[详细了解动作类型。](https://www.palantir.com/docs/foundry/action-types/overview/)

### 对象类型组

对象类型组是一种分类原语，可帮助用户更好地搜索和浏览他们的本体。

[详细了解对象类型组。](https://www.palantir.com/docs/foundry/object-link-types/type-groups/)

### 接口

**接口**是一种本体类型，用于描述对象类型的形状及其能力。接口提供对象类型多态性，允许对共享共同形状的对象类型进行一致的建模和交互。

详细了解[接口](https://www.palantir.com/docs/foundry/interfaces/interface-overview/)。

## 对象类型与对象的区别

为了阐明对象类型与对象之间的区别，下面给出了示例。请注意，链接类型与链接之间也存在同样的区别。

### 对象类型定义

对象类型定义（有时简称为“对象类型”）指关于本体实体（如对象类型、链接类型和动作类型）的类型级信息。例如，对象类型的元数据可能包括显示名、属性名称、属性数据类型和描述。元数据并不指对象类型属性或主键的实际数据或值；这些被视为本体数据。

### 对象实例

对象实例（有时简称为“对象”）是本体实体特定实例的实际主键和属性值。例如，`Airplane`（飞机）对象类型可以有一个对象实例，其 `Plane ID`（飞机 ID）属性的值为 `my_plane_id1`，`Maximum Occupancy`（最大载客量）属性的值为 `240`。

## 值类型

**值类型**是围绕字段类型的语义包装器，由元数据和约束组成，可以增强类型安全、提高表达能力并提供附加上下文。值类型封装特定领域的数据类型，并以可在整个平台复用的方式强制执行数据验证。常用的值类型包括电子邮件地址、URL、UUID 和枚举。

字段类型和基础类型是静态定义的，而值类型是在给定[空间](https://www.palantir.com/docs/foundry/security/orgs-and-spaces/)的上下文中定制的。因此，用户不能创建新的字段类型或基础类型，但能够动态创建**值类型**。

[详细了解值类型。](https://www.palantir.com/docs/foundry/object-link-types/value-types-overview/)

---

*原文：[Types reference](https://www.palantir.com/docs/foundry/object-link-types/type-reference/)*
