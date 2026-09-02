# 接口上的动作（Actions on interfaces）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/action-types/actions-on-interfaces/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

你可以创建通用动作，将其应用于所选接口的所有对象。在动作中使用接口主要有两种方式：

- **接口动作规则：** 用于创建、修改、删除所配置接口的对象并为其建立链接。
- **接口引用参数：** 用于引用所配置接口的对象。“Modify”（修改）和 “Delete”（删除）接口动作规则需要此参数，但其他任何动作规则也可以使用它。

接口还可以定义[接口动作类型约束](https://www.palantir.com/docs/foundry/interfaces/interface-action-type-constraints/)，描述实现该接口的对象类型可通过具体动作类型满足的预期动作能力。

动作类型约束定义接口级别的动作契约；动作规则定义提交动作类型时执行的编辑。

接口动作类型约束目前是 Ontology Manager 的建模与映射功能，而不是最终用户应用或 Ontology SDK 的调用入口。

> **⚠️ 注意**
>
> 接口动作的提交条件统一适用于实现该接口的所有对象类型。在创建接口动作之前，请仔细审查哪些用户将有权限在实现该接口的所有对象类型中创建、修改或删除对象。[详细了解如何建立对接口动作的控制](#接口动作规则的限制)。

## 使用接口上的动作规则

当编辑可以应用于实现该接口的所有对象类型时，你就可以使用接口动作规则。换句话说，你只能使用接口动作规则来修改*接口共享属性*或删除对象。例如，如果 “Feature request”（功能请求）和 “Bug”（缺陷）是 “Ticket”（工单）接口的对象类型，你可以使用 “Create a ticket”（创建工单）动作类型来创建缺陷和功能请求，但不能创建任何专属于缺陷或功能请求的属性类型。

![使用接口上的动作规则](https://www.palantir.com/docs/resources/foundry/action-types/action_on_interface_rules.png)

### 创建新的接口动作类型

要设置新的接口动作类型，请在 Ontology Manager 的 **New（新建）** 菜单中选择 **Action type（动作类型）**。

1. 在 **Interfaces（接口）** 下，选择所需的接口和规则类型。

![创建新接口](https://www.palantir.com/docs/resources/foundry/action-types/action_on_interface_new_interface.png)

1. 添加你想包含在动作中的共享属性（如适用）。
2. 添加描述动作类型的元数据。请记住，此元数据应适用于实现该接口的所有对象类型。
3. 在 **Submission criteria（提交条件）** 下，选择可以执行该动作的用户（稍后可以应用更复杂的条件）。请记住，只要用户对实现该接口的对象类型拥有编辑权限，这些权限就将适用于所有这些对象类型。
4. 选择 **Create（创建）** 完成动作类型的创建。

### 接口上的 “Create”（创建）动作

由于动作类型仅与接口关联，将自动生成一个 “Object type”（对象类型）参数，用于指示应创建的对象类型。如果使用表单或表格，系统将提示用户从列表中选择一个对象类型。

![接口上的创建动作](https://www.palantir.com/docs/resources/foundry/action-types/action_on_interface_create_action.png)

请注意，**对象不能在没有主键的情况下创建**。因此，任何在规则中未分配主键的对象类型在提交时都会失败。为避免此类失败，请确保接口和 Create（创建）规则都包含一个接口属性，该属性可用作实现该接口的对象类型中的主键。

![接口上的动作缺少主键](https://www.palantir.com/docs/resources/foundry/action-types/action_on_interface_primary_key.png)

### 接口上的 “Modify”（修改）动作

接口上的 “Modify” 规则可以修改所配置接口的任何对象。将生成一个受限于所选接口的 “interface reference”（接口引用）参数。“接口引用” 参数类似于 “object reference”（对象引用）参数，区别在于 “接口引用” 参数显示实现该接口的任何类型的对象。如果使用表单或表格，用户随后可以从列表中选择一个对象。

请注意，主键值*不能*被任何动作类型修改。因此，如果动作尝试修改所选对象类型的主键属性，动作在提交时将失败。请始终确保动作规则不会修改那些可能被实现该接口的某些对象类型用作主键的属性。

在下面的示例中，“Title” 属性被错误地用作 “Bug” 对象类型的主键。“Edit ticket”（编辑工单）动作在提交时将失败，因为该动作尝试更改缺陷的主键。

![接口上的动作修改主键](https://www.palantir.com/docs/resources/foundry/action-types/action_on_interface_primary_key_modify.png)

### 接口上的 “Delete”（删除）动作

“Delete” 动作规则可以为其分配一个 “interface reference”（接口引用）参数，而不是对象引用参数。该接口引用限定于特定接口，用于指示要删除的对象。如果使用表单或表格，用户随后可以从列表中选择一个对象。

### 接口上的 “Create link”（创建链接）动作

“Create interface link”（创建接口链接）规则允许你使用在接口上定义的接口链接约束来创建链接。要配置 “创建接口链接” 规则：

1. 选择你要在其上创建链接的接口。
2. 选择在该接口上定义的接口链接约束。如果链接约束位于两个接口之间，则源参数和目标参数都将自动生成为接口引用参数。如果链接约束位于接口与对象类型之间，则源为接口引用参数，目标为对象引用参数。

如果你不想使用动作自动生成的参数，也可以选择手动配置源对象和目标对象。它们可以是：

- 引用现有对象的接口引用或对象引用参数。
- 由同一动作类型内的 “Create object”（创建对象）或 “Create object(s) of interface”（创建接口对象）规则创建的对象。

> **⚠️ 注意**
>
> 如果对象类型上针对该链接约束存在多个具体链接实现，动作将失败。此外，创建一对多链接会修改关系中“多”方的外键。如果你的动作类型还使用 “Create object”（创建对象）或 “Modify object(s)”（修改对象）规则修改该外键，请确保不存在冲突。

### 接口上的 “Delete link”（删除链接）动作

“Delete interface link”（删除接口链接）规则允许你使用在接口上定义的接口链接约束来删除链接。要配置 “删除接口链接” 规则：

1. 选择你要在其上删除链接的接口。
2. 选择在该接口上定义的接口链接约束。如果链接约束位于两个接口之间，则源参数和目标参数都将自动生成为接口引用参数。如果链接约束位于接口与对象类型之间，则源为接口引用参数，目标为对象引用参数。

你也可以选择手动配置源对象和目标对象，而不是使用自动生成的参数。它们必须是引用现有对象的参数——接口引用或对象引用参数。

> **⚠️ 注意**
>
> 如果对象类型上针对该链接约束存在多个具体链接实现，动作将尝试删除所有具体链接实现。

### 在接口上执行动作

使用接口动作规则创建的动作可以应用于其对象类型实现了该接口的对象，就像任何对象专属动作类型一样。对于给定对象，所有可应用于该对象的对象类型专属动作和基于接口的动作都会出现在动作下拉菜单中。

## 权限

接口动作规则遵循与对象动作类型相同的权限。

更多细节请参阅[动作类型权限](https://www.palantir.com/docs/foundry/action-types/permissions/)文档。

## 支持程度

随着对接口动作规则和引用参数支持的扩展，其可用性在 Palantir 平台各部分会有所不同。

### 支持的应用和服务

- **Ontology Manager：** 创建接口动作类型，以及在提交条件和覆盖（overrides）中配置接口参数。
- **Object Explorer 和 Object Views：** 渲染在接口上定义的动作。

### 接口动作规则的限制

- 提交条件统一适用于实现该接口的所有对象类型，因此你无法在单个接口动作中按对象类型配置不同权限。要限制访问，请在 [Ontology Manager](https://www.palantir.com/docs/foundry/ontology-manager/overview/) 中为特定对象类型禁用接口动作：选择其 **Interfaces（接口）** 选项卡，并在 **Interface action control（接口动作控制）** 区域建立对从接口继承的动作的控制。对接口动作应用更细粒度权限控制的能力正在活跃开发中。
- 尚不支持动作日志。
- 接口上的动作不能与函数一起使用。

---

*原文：[Actions on interfaces](https://www.palantir.com/docs/foundry/action-types/actions-on-interfaces/)*
