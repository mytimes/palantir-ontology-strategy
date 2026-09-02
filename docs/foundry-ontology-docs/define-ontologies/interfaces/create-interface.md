# 创建接口（Create an interface）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/interfaces/create-interface/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

按照以下步骤在 [Ontology Manager](https://www.palantir.com/docs/foundry/ontology-manager/overview/) 中创建新接口。

1. 首先，通过查看左侧面板顶部的 **Ontologies**（本体）下拉菜单，确认你正在所选的本体中工作。
2. 要创建新接口，你可以选择以下任一方式：
  - 在页面右上角，选择 **New > Interface**（新建 > 接口）。
  - 在左侧面板的 **Resources**（资源）部分下，选择 **Interfaces > + New interface**（接口 > + 新建接口）。然后，在 **Interfaces**（接口）页面，从屏幕右上角选择 **New interface**（新建接口）。
3. 向导的第一页提供了有关接口的信息。选择 **Next**（下一步）。![接口创建关于页面。](https://www.palantir.com/docs/resources/foundry/interfaces/create-interface-about.png?width=800)
4. 输入接口的显示名和 API 名。你还可以选择性地提供接口描述并选择合适的图标。![接口元数据创建](https://www.palantir.com/docs/resources/foundry/interfaces/create-interface-metadata.png?width=800)
5. 为你的接口添加属性。你可以在接口上本地定义属性（推荐），或使用[共享属性](https://www.palantir.com/docs/foundry/object-link-types/shared-property-overview/)。对于每个属性，选择它是 **required**（必填）还是 **optional**（可选）。![接口属性选择](https://www.palantir.com/docs/resources/foundry/interfaces/create-interface-choose-properties.png?width=800) 对于 **required**（必填）属性，任何实现该接口的对象类型都必须提供从本地属性到接口属性的映射。对于 **optional**（可选）属性，在实现时可以跳过映射。在构建 Marketplace 包时，可选属性非常有用，可以让你迭代接口而不会引入可能难以解决的升级阻塞问题。
6. 选择一个项目来保存此接口，然后选择 **Create**（创建）。![接口保存位置。](https://www.palantir.com/docs/resources/foundry/interfaces/create-interface-save-location.png?width=800)
7. 回到 Ontology Manager，选择右上角的 **Save**（保存）以[将更改应用到你的本体](https://www.palantir.com/docs/foundry/ontology-manager/save-changes/)。

## 创建接口链接类型（可选）

如果你希望此接口链接到另一个接口或对象类型，你可以选择性地向接口添加任意[接口链接类型](https://www.palantir.com/docs/foundry/interfaces/interface-link-types-overview/)。

![添加链接类型约束](https://www.palantir.com/docs/resources/foundry/interfaces/create-link-type-constraint.png?width=800)

1. 在左侧面板中选择 **Link type constraints**（链接类型约束）。
2. 然后，在右上角选择 **Create new link type constraint**（创建新链接类型约束）。

![创建链接类型约束](https://www.palantir.com/docs/resources/foundry/interfaces/create-link-type-constraint-modal.png?width=400)

如果你的建模用例需要某个接口链接类型，那么任何实现该接口的对象类型都必须添加一个满足该接口链接类型约束的新链接类型或现有链接类型。

## 创建接口动作类型约束（可选）

如果你希望接口定义各实现对象类型应具备的动作能力，你可以选择性地向接口添加[接口动作类型约束](https://www.palantir.com/docs/foundry/interfaces/interface-action-type-constraints/)。

![接口动作类型约束选项卡。](https://www.palantir.com/docs/resources/foundry/interfaces/iatc-config-page.png?width=800)

1. 在左侧面板中选择 **Action type constraints**（动作类型约束）。
2. 选择 **Create new**（新建）。
3. 添加约束元数据，选择是否要求实现，并配置相关参数约束。

![动作类型约束配置对话框。](https://www.palantir.com/docs/resources/foundry/interfaces/iatc-config-dialog-set.png?width=500)

如果你的建模用例需要某个接口动作类型约束，那么任何实现该接口的对象类型都必须将该约束映射到一个满足该接口动作类型约束的具体动作类型。

---

*原文：[Create an interface](https://www.palantir.com/docs/foundry/interfaces/create-interface/)*
