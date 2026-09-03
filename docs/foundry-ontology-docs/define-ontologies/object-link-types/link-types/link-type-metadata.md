# 元数据参考（Metadata reference）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/link-type-metadata/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

链接类型在 Foundry 本体中由以下元数据表示：

- **ID：**链接类型的唯一标识符，主要用于在配置应用时引用此类型的链接。例如，`employee-employer` 可以是在 `Employee` 和 `Company` 对象类型之间定义的链接类型的 ID。
- **RID：**Foundry 中每个资源自动生成的唯一标识符。链接类型的 RID 会在平台各处的错误消息中被引用。
- **Status（状态）：**向用户和其他本体构建者表明链接类型在开发流程中所处阶段的信号。它可以是 `active`、`experimental` 或 `deprecated`。默认情况下，`Employee → Employer` 链接类型的状态为 `experimental`。阅读更多关于[状态](https://www.palantir.com/docs/foundry/object-link-types/metadata-statuses/)的信息。
- **Object types（对象类型）：**通过链接类型定义相关联的对象类型。例如，`Employee → Employer` 链接类型会引用 `Employee` 和 `Company` 对象类型。
- **Cardinality（基数）：**向应用表明链接类型中的每个对象类型拥有一个还是多个对象。例如，链接类型 `Employee → Employer` 中的 Employee 对象类型基数为 `many`（多），Company 对象类型基数为 `one`（一），因为多名员工链接到单一雇主。如果一名直接下属可以有多位经理，且一位经理可以有多名直接下属，那么链接类型 `Direct Report ↔ Manager` 中的 Employee 对象类型将各自具有基数 `many`。
- **Key（键）：**用于创建链接的属性或列。
  - 在一对一或一对多基数的链接类型中，一个对象类型的某个属性（外键）引用另一个对象类型的主键属性。外键与主键之间的这种引用定义了对象之间的链接。例如，在 `Employee → Employer` 链接类型中，`Employee` 对象类型可能有一个 `employer ID`（雇主 ID）属性（外键），它引用 `Company` 对象类型的 `company ID`（公司 ID）属性（主键）。
  - 在多对多基数的链接类型中，一个包含主键对的表定义了两个对象之间的链接。这类链接类型需要指定一个连接表，并映射这些键，以告知应用连接表中的哪些列引用链接类型中哪些对象类型的主键。例如，支撑 `Direct Report ↔ Manager` 链接类型的连接表可能包含成对的 `employee numbers`（员工编号），每一对代表一条 `Direct Report ↔ Manager` 链接。
- **Display name（显示名）：**在用户应用中访问此类型链接时向任何人显示的名称。链接类型的每一侧都有一个显示名。链接类型的一侧表示*指向*该对象类型的链接。例如，在 `Employee → Employer` 链接类型中，`Employee` 对象类型的显示名是 `Employee`，`Company` 对象类型的显示名是 `Employer`。
- **Plural display name（复数显示名）：**在用户应用中通过此类型的链接访问多个对象时显示的名称。例如，在 `Employee → Employer` 链接类型中，`Employee` 对象类型的复数显示名是 `Employees`，而 `Company` 对象类型没有复数显示名，因为每位员工只能对应一家公司。
- **API name（API 名称）：**在代码中以编程方式引用链接类型时使用的名称。链接类型某一侧的 API 名称可用于返回该类型的对象。例如，如果 `Employee → Employer` 链接类型中 Employee 一侧的 API 名称是 `employee`，那么调用 `Company.employee.get()` 将返回链接到这些 `Company` 对象的 `Employee` 对象。阅读更多关于 [API 名称](https://www.palantir.com/docs/foundry/functions/api-objects-links/)的信息。
- **Visibility（可见性）：**向用户应用表明应以何种显著程度展示链接类型的该侧（即*指向*该侧对象类型的链接）。链接类型的 `prominent`（显著）一侧会引导应用优先向用户展示该侧。链接类型的 `hidden`（隐藏）一侧不会出现在用户应用中。默认情况下，链接类型的 Employee 和 Company 两侧可见性均为 `normal`（普通）。
- **Type classes（类型类）：**由用户应用解读的附加元数据。阅读更多关于[类型类](https://www.palantir.com/docs/foundry/object-link-types/metadata-typeclasses/)的信息。

[详细了解如何在本体中创建和配置链接类型，以及链接类型元数据的验证要求。](https://www.palantir.com/docs/foundry/object-link-types/create-link-type/)

[详细了解属性（对象类型的特征）。](https://www.palantir.com/docs/foundry/object-link-types/properties-overview/)

---

*原文：[Metadata reference](https://www.palantir.com/docs/foundry/object-link-types/link-type-metadata/)*
