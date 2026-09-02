# 结构体属性自动映射（Automapping struct properties）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/struct-automapping/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

自动映射允许用户自动映射所有列，而无需手动映射。

## 在 Ontology Manager 中自动映射结构体类型

如果对象已经创建，用户可以使用 **Automap all**（全部自动映射）功能自动映射所有列。

1. 在 Ontology Manager 中，进入 **Properties**（属性）选项卡并选择所需的属性。
2. 在 **Column mapping**（列映射）选项卡下，选择所需的列。

![“列映射”选项卡和“全部自动映射”按钮。](https://www.palantir.com/docs/resources/foundry/object-link-types/automap-struct-oma.png?width=500)

1. 选择 **Automap all**（全部自动映射）。

## 在 Pipeline Builder 中自动映射结构体类型

如果对象尚未创建，可以在首次创建对象时通过对象类型创建向导完成自动映射。

1. 在你的 Pipeline Builder 管道中，打开相关数据集并选择右上角的 **All Actions**（所有操作）下拉菜单。

![数据集详情页面中的“所有操作”下拉菜单。](https://www.palantir.com/docs/resources/foundry/object-link-types/automap-struct-pipelinebuilder.png?width=500)

1. 选择 **Create object type**（创建对象类型）以创建新对象。

![“创建新对象”对话框中的属性选项卡。](https://www.palantir.com/docs/resources/foundry/object-link-types/automap-struct-properties.png?width=500)

1. 在 **Properties**（属性）下，添加需要映射的属性。
2. 选择 **Next**（下一步）并完成剩余步骤，以创建自动映射的对象类型。

---

*原文：[Automapping struct properties](https://www.palantir.com/docs/foundry/object-link-types/struct-automapping/)*
