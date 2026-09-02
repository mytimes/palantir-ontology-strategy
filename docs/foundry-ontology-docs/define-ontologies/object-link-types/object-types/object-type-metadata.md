# 元数据参考（Metadata reference）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/object-type-metadata/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

对象类型在本体中由以下元数据表示：

- **ID：** 对象类型的唯一标识符，主要用于在配置应用时引用此类型的对象。例如，`employee` 可能是 `Employee` 对象类型的 ID。
- **RID：** Foundry 中每个资源的自动生成唯一标识符。对象类型的 RID 会在整个平台的错误消息中被引用。
- **图标（Icon）：** 用作对象类型视觉标识的图片和颜色，当用户在应用中查看此类型的对象时会显示。例如，人物图标可用于表示 `Employee` 对象类型。
- **显示名（Display name）：** 在用户应用中向访问此类型对象的任何人显示的名称。例如，`Employee` 对象类型的显示名可能是 `Employee`。
- **复数显示名（Plural display name）：** 在用户应用中向访问此类型多个对象的任何人显示的名称。例如，`Employee` 对象类型的复数显示名可能是 `Employees`。
- **描述（Description）：** 关于对象类型的说明性文字，任何人都可以在用户应用中阅读。例如，`Employee` 对象类型的描述可能是 `All full-time and part-time employees of Organization X`（组织 X 的所有全职和兼职员工）。
- **组（Groups）：** 组是帮助你对对象类型进行分类的标签。例如，`Employee` 对象类型可能属于 `HR` 和 `Employee 360` 组。
- **API 名称（API name）：** 在代码中以编程方式引用对象类型时使用的名称。例如，`Employee` 对象类型的 API 名称可能是 `Employee`。详细了解 [API 名称](https://www.palantir.com/docs/foundry/functions/api-objects-links/)。
- **可见性（Visibility）：** 向用户应用指示对象类型的显示醒目程度。`prominent`（突出）对象类型会使应用优先向用户显示该对象类型。`hidden`（隐藏）对象类型不会出现在用户应用中。默认情况下，`Employee` 对象类型的可见性为 `normal`（普通）。
- **状态（Status）：** 向用户和其他本体构建者指示对象类型在开发流程中所处的阶段。它可以是 `active`（活跃）、`experimental`（实验性）或 `deprecated`（已弃用）。默认情况下，`Employee` 对象类型的状态为 `experimental`。详细了解[状态](https://www.palantir.com/docs/foundry/object-link-types/metadata-statuses/)。
- **索引状态（Index status）：** 对象类型及其支撑数据源上次重建索引的状态。它可以是 `success`（成功）、`failed`（失败）或 `not started`（未开始）。详细了解[索引状态](https://www.palantir.com/docs/foundry/object-databases/object-storage-v1/)。
- **回写（Writeback）：** 指示对象类型是否已生成回写数据集，以及允许最终用户编辑此类型对象是 `enabled`（已启用）还是 `disabled`（已禁用）。详细了解[回写数据集](https://www.palantir.com/docs/foundry/object-link-types/allow-editing/)。

[详细了解如何在本体中创建和配置对象类型，以及对象类型元数据的验证要求。](https://www.palantir.com/docs/foundry/object-link-types/create-object-type/)

[详细了解属性（对象类型的特征）。](https://www.palantir.com/docs/foundry/object-link-types/properties-overview/)

---

*原文：[Metadata reference](https://www.palantir.com/docs/foundry/object-link-types/object-type-metadata/)*
