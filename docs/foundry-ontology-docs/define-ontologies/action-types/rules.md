# 规则（Rules）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/action-types/rules/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

**规则**定义动作类型的逻辑，将参数转换为本体编辑或其他效果。规则主要有两类：编辑本体的规则，以及在 Foundry 中触发其他效果的规则。

要通过引导式介绍了解以下规则能让你构建什么，请参阅[探索其他动作类型](https://www.palantir.com/docs/foundry/action-types/explore-action-types/)。

## 本体规则

本体规则更改本体的特定要素。它们可以创建、修改或删除现有类型的对象和链接。要创建或删除一对多或一对一链接，需要使用对象规则，并修改对象上的外键属性。

1. **创建对象：**可用于创建预定义类型的对象。对象类型的主键是必填属性，必须填写。其他属性可以选择性添加。
2. **修改对象：**可用于修改现有对象，其主键从对象引用参数派生。不能引用作为当前动作一部分而创建的对象。
3. **创建或修改对象：**可用于基于对象引用参数修改现有对象。如果未选择对象，则会创建一个新对象，其主键为自动生成的唯一 ID，或由用户提交的主键。
4. **删除对象：**可用于删除现有对象，其主键从对象引用参数派生。不能引用作为当前动作一部分而创建的对象。
5. **创建链接：**可用于在通过对象引用参数传入的对象之间创建多对多链接。对于外键链接，必须使用**修改对象**规则显式修改外键属性。
6. **删除链接：**可用于删除通过对象引用参数传入的对象之间的多对多链接。对于外键链接，必须使用**修改对象**规则显式修改外键属性。
7. **函数规则：**可用于引用一个本体编辑函数，其输入从动作的参数派生。当存在此规则时，不得配置其他规则，因为函数代码本身就能够处理其他规则所能做的一切。详细了解[函数动作类型](https://www.palantir.com/docs/foundry/action-types/function-actions-overview/)。
8. **创建接口对象：**可用于创建实现了特定接口的任意类型的对象。详细了解[针对接口的动作](https://www.palantir.com/docs/foundry/action-types/actions-on-interfaces/)。
9. **修改接口对象：**可用于修改实现了特定接口的类型的对象。详细了解[针对接口的动作](https://www.palantir.com/docs/foundry/action-types/actions-on-interfaces/)。
10. **删除接口对象：**可用于删除实现了特定接口的类型的对象。详细了解[针对接口的动作](https://www.palantir.com/docs/foundry/action-types/actions-on-interfaces/)。
11. **在接口对象上创建链接：**可用于在实现了特定接口的类型的对象之间创建链接。详细了解[针对接口的动作](https://www.palantir.com/docs/foundry/action-types/actions-on-interfaces/)。
12. **在接口对象上删除链接：**可用于删除实现了特定接口的类型的对象之间的链接。详细了解[针对接口的动作](https://www.palantir.com/docs/foundry/action-types/actions-on-interfaces/)。

### 值与参数

在创建或修改链接和对象时，规则的运行需要额外的值。修改对象时，规则还定义哪些属性被修改。每个属性依次映射到由多个选项之一提供的值（链接上的规则只能接受对象引用参数）：

- **来自参数：**与属性类型相同的现有参数。默认情况下，添加到规则中的每个新属性都会自动创建一个同名参数，并映射为使用该参数的值。
- **对象参数属性：**现有对象引用参数的某个属性。对象参数的属性类型需要与其映射到的属性类型相匹配。
- **静态值：**仅存在于动作类型规则部分中的静态值。在 Workshop、Slate 或 Object Views 中与该动作交互时，无法更改此值。
- **当前用户/时间：**字符串和时间戳属性还可以采用上下文值，形式为动作的当前用户或提交时间。与**静态值**一样，提交动作时无法与这些值交互，也不能在动作类型的其他部分使用这些值。

### 创建对象与多对多链接

你也可以同时创建对象和多对多链接。仅创建多对多链接要求链接两侧的对象事先存在，而你可以通过一个动作类型同时创建两个实体。首先配置一个**创建对象**规则，选择具有多对多链接的对象类型。然后点击**添加属性**下方的**添加链接**按钮，选择链接类型并配置链接。

要创建一对多或一对一链接类型，只需编辑对象上的外键。

### 无效组合

动作类型可以包含本体规则的组合。定义多个规则时，动作后端会编译规则，为每个对象生成单一编辑（例如**添加对象**、**修改对象**或**删除对象**）。例如，如果一个规则的结果将某属性更新为“A”，而同一动作类型中的另一个规则将同一对象的同一属性更新为“B”，则最终编辑只会将该属性更新为“B”。规则的顺序会影响最终的对象编辑。因此，以下对象编辑组合不受支持：

- 对象不能在被添加或修改之前删除。
- 对象不能在被添加之前修改。
- 对象不能在一次表单提交中被创建两次。

## 其他规则

有两类规则会触发[副作用](https://www.palantir.com/docs/foundry/action-types/side-effects-overview/)：

- [**通知**](https://www.palantir.com/docs/foundry/action-types/notifications/)规则可用于发送有关该动作的通知。可以使用参数来自定义通知内容和收件人。最终用户可以调整其偏好设置，选择通过平台内推送通知、电子邮件或两者同时接收通知。通知在所有动作编辑生效后发送，但通知内容将基于编辑生效前的本体状态生成。
- [**Webhook**](https://www.palantir.com/docs/foundry/action-types/webhooks/)使动作在生效时能够向外部系统发起请求。动作参数可以传入 Webhook，Webhook 进而可以将参数传递给外部请求。Webhook 可以配置为在编辑生效之前或之后运行。

还有一个额外的高级规则会触发[构建](https://www.palantir.com/docs/foundry/data-integration/builds/)：

- [**调度**](https://www.palantir.com/docs/foundry/action-types/trigger-schedule-build/)规则可用于触发某个调度的构建。动作参数可以传入调度，调度进而可以将参数传递给构建中底层的[参数化转换](https://www.palantir.com/docs/foundry/building-pipelines/parameterization/)。Foundry 在构建开始后应用本体编辑。

---

*原文：[Rules](https://www.palantir.com/docs/foundry/action-types/rules/)*
