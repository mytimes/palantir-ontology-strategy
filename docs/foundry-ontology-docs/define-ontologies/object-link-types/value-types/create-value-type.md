# 创建值类型（Create a value type）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/create-value-type/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

按照以下步骤创建值类型，以便在你的平台[空间](https://www.palantir.com/docs/foundry/security/orgs-and-spaces/#spaces)中使用。

1. 从平台侧边栏导航到 **Value Types Manager**（值类型管理器）应用。
2. 在左上角，使用下拉菜单选择你要在其中创建值类型的空间。
3. 在右上角选择 **Create New Value Type**（创建新值类型）。
4. 为你的值类型提供清晰的名称、描述和唯一的 API 名称。

![创建值类型元数据](https://www.palantir.com/docs/resources/foundry/object-link-types/value-type-create-metadata.png?width=500)

1. 为你的值类型选择一个[基础类型](https://www.palantir.com/docs/foundry/object-link-types/base-types/)。
2. （可选）为你的值类型定义约束。验证器可以是 `String` 类型的正则表达式、枚举、范围，或根据基础类型而定的其他验证方法。
有关基础类型支持的约束的完整列表，请参阅我们的[值类型约束](https://www.palantir.com/docs/foundry/object-link-types/value-type-constraints/)文档。

![创建值类型约束](https://www.palantir.com/docs/resources/foundry/object-link-types/value-type-create-constraint.png?width=500)

1. （可选但建议）为你的值类型提供一个示例预览值。

![创建值类型预览](https://www.palantir.com/docs/resources/foundry/object-link-types/value-type-create-preview.png?width=500)

1. 保存你的值类型。

---

*原文：[Create a value type](https://www.palantir.com/docs/foundry/object-link-types/create-value-type/)*
