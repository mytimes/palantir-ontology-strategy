# 元数据参考（Metadata reference）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/property-metadata/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

属性在本体中由以下元数据表示：

- **ID：** 属性的唯一标识符，主要用于在配置应用时引用该属性。例如，`start-date` 可能是入职日期属性的 ID。
- **显示名：** 在用户应用中向访问此属性值的任何人显示的名称。例如，`start date` 属性的显示名可能是 `Start date`。
- **描述：** 关于属性的说明性文字，任何人都可以在用户应用中阅读。例如，`start date` 属性的描述可能是 `The day the employee began new hire training`（员工开始新员工培训的日期）。
- **RID：** Foundry 中为每个资源自动生成的唯一标识符。属性的 RID 会在整个平台的错误消息中被引用。
- **状态：** 向用户和其他本体构建者指示属性在开发流程中所处的阶段。它可以是 `active`（活跃）、`experimental`（实验性）或 `deprecated`（已弃用）。默认情况下，`start date` 属性的状态为 `experimental`。详细了解[状态](https://www.palantir.com/docs/foundry/object-link-types/metadata-statuses/)。
- **API 名称：** 在代码中以编程方式引用属性时使用的名称。例如，`start date` 属性的 API 名称可能是 `startDate`。详细了解 [API 名称](https://www.palantir.com/docs/foundry/functions/api-objects-links/)。
- **键：** 指示该属性是对象类型的标题键还是主键。
  - **标题键**是充当此类型对象显示名的属性。例如，将 `full name`（全名）属性设置为 `Employee` 对象类型的标题键后，会使用该属性的值（例如虚构员工“Melissa Chang”和“Diego Rodriguez”）作为各自 `Employee` 对象的显示名。
  - **主键**是充当对象类型每个实例的唯一标识符的属性，意味着支撑数据源中的每一行在此属性上必须具有不同的值。例如，`employee number` 属性的值可用于将“Melissa Chang”标识为组织内的唯一员工。
- **基础类型：** 指示此属性的值类型，并决定用户应用中可用的操作集合。例如，`start date` 属性的基础类型为 `date`。用户应用将允许你使用此属性配置时间线组件。
- **值格式：** 根据属性的基础类型，可对属性应用数值格式、日期和时间格式、用户 ID 和资源 ID 格式，将其原始值转换为用户应用中更具可读性的版本。详细了解[值格式](https://www.palantir.com/docs/foundry/object-link-types/value-formatting/)。
- **条件格式：** 在属性上设置的规则，规定该属性值在面向用户的应用中如何渲染（例如着色、对齐等）。例如，你可以在 `full name` 属性上设置一条规则：如果 `start date` 属性的值距今不足 2 周，则将 `full name` 的值显示为绿色，以在用户应用中表示新员工。详细了解[条件格式](https://www.palantir.com/docs/foundry/object-link-types/conditional-formatting/)。
- **类型类：** 由用户应用解释的额外元数据。详细了解[类型类](https://www.palantir.com/docs/foundry/object-link-types/metadata-typeclasses/)。
- **渲染提示：** 向用户应用指示如何渲染该属性，可能与同一基础类型的大多数属性不同。许多渲染提示可用于影响定义该属性的对象类型的重新索引性能。例如，如果你预计不会有用户在应用中对 `start date` 属性进行搜索或排序，可以取消选择 `searchable`（可搜索）和 `sortable`（可排序）渲染提示，从而提高 `Employee` 对象类型的重新索引性能。详细了解[渲染提示](https://www.palantir.com/docs/foundry/object-link-types/metadata-render-hints/)。
- **可见性：** 向用户应用指示属性的显示醒目程度。`prominent`（突出）属性会使应用优先向用户显示该属性。`hidden`（隐藏）属性不会出现在用户应用中。默认情况下，`start date` 属性的可见性为 `normal`（普通）。

[详细了解如何在本体中创建和配置属性，以及属性元数据的验证要求。](https://www.palantir.com/docs/foundry/object-link-types/create-object-type/)

## 支持有限的属性基础类型

某些属性基础类型的支持有限。这些类型在属性基础类型选择器中以 `Limited support`（支持有限）标签标示。

- `byte`：
  - 此类型的属性不能在动作类型中使用。
- `float`：
  - 此类型的属性不能在动作类型中使用。
- `short`：
  - 此类型的属性不能在动作类型中使用。
- `vector`：
  - 向量只能通过 [KNN](https://www.palantir.com/docs/foundry/functions/api-object-sets/#k-nearest-neighbors-knn) 查询。
  - 最大向量维度为 2048。

有关动作类型中属性基础类型限制的更多信息，请参阅[关于受支持属性类型的文档](https://www.palantir.com/docs/foundry/action-types/scale-property-limits/#supported-property-types)。

---

*原文：[Metadata reference](https://www.palantir.com/docs/foundry/object-link-types/property-metadata/)*
