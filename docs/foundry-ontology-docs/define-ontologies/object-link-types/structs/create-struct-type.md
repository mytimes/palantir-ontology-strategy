# 创建结构体属性类型（Create a struct property type）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/create-struct-type/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

从 Ontology Manager 的 **Object types**（对象类型）页面创建和配置新的结构体属性。有关结构体属性的更多信息，请参见[概览](https://www.palantir.com/docs/foundry/object-link-types/structs-overview/)。

1. 在 Ontology Manager 中，打开左侧边栏中的 **Object types**（对象类型）选项卡，并选择一个现有对象类型。
2. 在对象类型详情页面，打开左侧边栏中的 **Properties**（属性）选项卡，并选择 **Properties** 表格右上角的 **Create property**（创建属性）按钮。

![对象类型属性表格和“属性编辑器”面板。](https://www.palantir.com/docs/resources/foundry/object-link-types/create-struct-from-ontology-manager.png?width=500)

1. 在 **Property editor**（属性编辑器）面板中，添加名称和描述，并从 **Base type**（基础类型）下拉菜单中选择 **Struct**（结构体）。

![基础类型下拉菜单中选中了“结构体”。](https://www.palantir.com/docs/resources/foundry/object-link-types/name-struct-from-ontology-manager.png?width=500)

1. 向下滚动到 **Data**（数据）部分，并从下拉菜单中选择一个 **Backing column**（支撑列）。

![在属性编辑器的数据部分选择支撑列。](https://www.palantir.com/docs/resources/foundry/object-link-types/backing-column-struct-ontology-manager.png?width=500)

1. 在 **Struct fields**（结构体字段）部分，选择 **Add field**（添加字段），然后选择 **New field**（新建字段）。

![结构体属性类型中的示例结构体字段。](https://www.palantir.com/docs/resources/foundry/object-link-types/struct-field-ontology-manager.png?width=500)

1. 为新的结构体字段命名，并可选择添加描述。
2. 最后，将数据源中的列映射到新的结构体字段。

---

*原文：[Create a struct property type](https://www.palantir.com/docs/foundry/object-link-types/create-struct-type/)*
