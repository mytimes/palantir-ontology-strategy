# 扩展接口（Extend an interface）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/interfaces/extend-interface/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

扩展接口允许你将接口组合在一起，创建一个新的、更具体的接口。这对于构建实现多个[能力接口](https://www.palantir.com/docs/foundry/interfaces/interface-overview/)的[抽象对象接口](https://www.palantir.com/docs/foundry/interfaces/interface-overview/)特别有用。接口会继承它所扩展的接口的共享属性、链接类型约束和动作类型约束。一个接口可以扩展任意数量的其他接口。

要扩展接口，请按照以下步骤操作。

1. 在 Ontology Manager 中，选择你希望扩展的接口以打开接口概览页面。
2. 在概览页面，从左侧面板选择 **Extension**（扩展）。
3. 在接口扩展页面，选择 **Add extension**（添加扩展）。

![向接口添加扩展。](https://www.palantir.com/docs/resources/foundry/interfaces/extend-interface.png?width=800)

1. 从下拉菜单中，选择当前接口要扩展的接口。

![确认接口扩展。](https://www.palantir.com/docs/resources/foundry/interfaces/confirm-extension.png?width=500)

1. 在确认对话框中，查看将添加到接口扩展的共享属性、链接类型约束和动作类型约束，然后选择 **Confirm**（确认）。
2. 选择右上角的 **Save**（保存）以将接口扩展添加到本体。

你也可以移除扩展以将一个接口与另一个接口解耦。此操作将从接口移除所有继承的共享属性、移除所有继承的链接类型约束、移除所有继承的动作类型约束，并解除扩展接口与基础接口之间的关联。

![移除现有的接口扩展。](https://www.palantir.com/docs/resources/foundry/interfaces/remove-interface-extension.png?width=800)

---

*原文：[Extend an interface](https://www.palantir.com/docs/foundry/interfaces/extend-interface/)*
