# 在对象类型上使用共享属性（Use shared properties on object types）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/use-shared-property/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

要将对象类型上的属性更新为共享属性，请完成以下步骤：

1. 在 Ontology Manager 中导航到该对象类型。
2. 在面板上选择要更新的属性，然后向下滚动到配置的 **Shared Property**（共享属性）部分。

![使用共享属性](https://www.palantir.com/docs/resources/foundry/object-link-types/convert-shared-property.png?width=500)

1. 使用下拉菜单选择要使用的现有共享属性，或通过[共享属性创建](https://www.palantir.com/docs/foundry/object-link-types/create-shared-property/)模态框将该属性转换为新的共享属性。

然后该属性将显示为共享属性。要将共享属性的使用持久化到本体，请选择右上角的 **Save**（保存）。

- 在对象上使用共享属性时，对象特有属性的属性 ID 和 API 名称将保持不变，以免破坏利用它们的现有下游工作流。
- 在与共享属性关联期间，对从共享属性继承的属性元数据的直接编辑将被禁用。你仍然可以添加、删除或编辑类型类。加载该属性时，生成的类型类集合将是该属性与其关联的共享属性的类型类的并集。
- 如果你使用的共享属性具有与所选属性不同的[渲染提示](https://www.palantir.com/docs/foundry/object-link-types/metadata-render-hints/)配置值，使用该共享属性将覆盖所选属性的配置值。请确保你的共享属性为你的用例配置了正确的渲染提示。

### 从对象分离共享属性

要将属性与共享属性分离，请在 Ontology Manager 中对象类型上使用相同的属性面板，并选择 **Detach**（分离）。

![分离共享属性](https://www.palantir.com/docs/resources/foundry/object-link-types/detach-shared-property.png?width=500)

这样做将移除该属性与共享属性之间的关联。

---

*原文：[Use shared properties on object types](https://www.palantir.com/docs/foundry/object-link-types/use-shared-property/)*
