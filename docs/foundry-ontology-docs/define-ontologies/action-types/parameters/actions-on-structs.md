# 结构体上的动作（Actions on structs）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/action-types/actions-on-structs/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

[结构体属性](https://www.palantir.com/docs/foundry/object-link-types/structs-overview/)值可以通过动作创建和修改，所需值通过结构体参数提供。

## 结构体参数

结构体参数是基础类型为 `STRUCT` 的参数，该类型包含嵌套参数字段，这些字段有各自独立的名称和基础类型。结构体参数只能用于为结构体属性提供值。结构体参数字段支持的基础类型有 `BOOLEAN`、`DATE`、`DOUBLE`、`GEOPOINT`、`INTEGER`、`LONG`、`STRING` 和 `TIMESTAMP`。

下面是一个用于 `Create Ticket`（创建工单）动作的 `Resolution` 结构体参数。`summary`、`resolutionTime` 和 `owner` 这三个嵌套字段将工单如何解决的信息汇总到单个参数中。

![带有嵌套字段的结构体参数。](https://www.palantir.com/docs/resources/foundry/action-types/struct-parameter-nested-fields.png)

## 定义结构体属性上的动作

使用带结构体参数的动作，你可以创建和修改具有结构体属性的对象类型。结构体属性的值在映射到该属性的结构体参数中提交；结构体属性的每个字段都映射到结构体参数的特定字段。在下面的示例中，`Resolution` 结构体参数的每个字段都映射到 `Ticket` 对象类型的 `Resolution` 结构体属性中的对应字段。

![结构体属性字段映射。](https://www.palantir.com/docs/resources/foundry/action-types/struct-field-mapping.png)

结构体属性与结构体参数之间的映射必须完整，结构体属性的每个字段都要映射到结构体参数中的某个字段。结构体参数字段的基础类型*必须*与所映射的结构体属性字段的基础类型匹配。如果要对结构体属性类型进行任何破坏性变更（例如添加新字段、删除字段或更改字段的基础类型），则还必须修改相关动作类型以纳入这些变更。

### 动作表单中的结构体参数

与任何其他参数类型类似，结构体参数可以通过动作表单填充。但是，结构体参数字段在表单中是作为一个组渲染的，而不是单独渲染。

![动作表单中的结构体参数。](https://www.palantir.com/docs/resources/foundry/action-types/struct-parameter-form.png)

## 结构体参数字段的默认值

默认值是为结构体参数字段单独定义的。每个结构体参数字段都映射到指定对象类型的结构体属性的字段。必须为结构体参数中的所有字段定义默认值，并且这些默认值必须映射到同一对象类型结构体属性的字段。只有结构体属性字段才能充当结构体参数字段的默认值。结构体属性字段将用作默认值的对象类型，在 `ObjectReference` 参数中指定。

![为结构体参数字段定义默认值。](https://www.palantir.com/docs/resources/foundry/action-types/struct-parameter-default-values.png)

提交动作时会提供 `ObjectReference` 参数中指定类型的一个对象实例，该对象的结构体属性字段值将自动填入相应结构体参数字段的值。

![应用于结构体参数字段的默认值。](https://www.palantir.com/docs/resources/foundry/action-types/struct-parameter-form-default-values.png)

## 结构体参数字段的约束

与常规参数一样，可以为结构体参数字段单独配置约束。例如，可以对字符串类型的结构体参数字段定义字符串长度约束，只允许长度在 10 到 500 个字符之间的字符串值。这意味着 `Resolution` 结构体参数的 `summary` 字段长度必须至少为 10 个字符，但不超过 500 个字符。

![为结构体参数字段定义约束。](https://www.palantir.com/docs/resources/foundry/action-types/struct-parameter-field-constraint.png)

结构体参数值*仅当*所有字段都满足定义的约束时才有效。用户只有在每个字段值都满足为其定义的约束时才能提交结构体参数值。正如为 `summary` 字段定义的那样，短于 10 个字符的值将是无效的。

![应用于结构体参数字段值的约束。](https://www.palantir.com/docs/resources/foundry/action-types/invalid-struct-parameter-value.png)

## 限制

使用动作创建或修改结构体参数时，请考虑以下限制：

- 结构体属性值只能通过*结构体参数*创建或修改。不支持其他录入形式，例如静态值或对对象属性的引用。
- 结构体属性只能通过*单个*结构体参数创建或修改。动作中的结构体属性映射不能涉及多个参数。
- 结构体参数只能用于创建或修改*结构体*属性。结构体参数字段不能单独用于创建或修改非结构体属性。
- 只有对*单个对象类型结构体属性值*的引用才能充当结构体参数字段的默认值。不支持其他录入形式，例如静态值。

---

*原文：[Actions on structs](https://www.palantir.com/docs/foundry/action-types/actions-on-structs/)*
