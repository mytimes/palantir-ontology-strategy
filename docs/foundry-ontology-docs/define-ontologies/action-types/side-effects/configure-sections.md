# 配置分区（Configure sections）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/action-types/configure-sections/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

动作表单可以使用**分区（sections）**进行自定义。这些分区对参数进行逻辑分组，以组织动作表单。分区还支持列、描述和条件覆盖。

![表单概览](https://www.palantir.com/docs/resources/foundry/action-types/form-overview.png?width=500)

## 向动作表单添加分区

在表单（Form）选项卡中，点击**添加分区（Add section）**。这将打开一个详细的分区配置模态框，你可以在其中添加标题、选择列布局，并选择性地编写面向用户的描述。该描述没有特殊样式，并且与参数描述不同，它始终显示在分区本身中，而不是在工具提示（tooltip）中。

你可以将参数按列组织，以更好地利用表单内的空间，或将相关参数更紧密地组合在一起。一个分区可以分为一列或两列。当你使用的参数在表单中不需要太多空间时，独立的列尤其有用。

![表单中的分区](https://www.palantir.com/docs/resources/foundry/action-types/section-config.png?width=500)

分区还可以折叠、完全隐藏，并可以使用条件覆盖，为你提供更多自定义表单行为的方式。所有这些特性也会应用于分区内的参数。组合使用这些特性可以实现更智能的表单，在适当的情况下展示必填参数。分区可以一开始隐藏，仅根据先前参数的值显示。

## 向分区添加参数

向分区添加参数有两种方式：从分区配置视图中添加，或从**表单**选项卡中添加。

在分区配置视图中，点击**添加新参数（Add new parameter）**。然后即可在分区内配置新添加的参数。或者，点击**添加现有参数（Add existing parameter）**将现有参数移入分区。

**表单**选项卡在一个概览中列出各个分区及其参数。点击参数左侧的八个点图标，将其拖入现有分区。参数和分区在表单中的显示顺序取决于它们在此**表单内容（Form Content）**区域中的顺序。

---

*原文：[Configure sections](https://www.palantir.com/docs/foundry/action-types/configure-sections/)*
