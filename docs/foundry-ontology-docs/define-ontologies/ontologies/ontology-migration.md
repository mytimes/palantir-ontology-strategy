# 在本体之间迁移本体资源（Migrate ontological resource between Ontologies）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/ontologies/ontology-migration/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

每个本体资源都会自动链接到创建它的本体。资源在创建之后，可以在不同本体之间移动。在本体之间迁移资源也会改变资源上的权限，但不会影响底层数据和输入数据源的权限。在本体之间迁移对象时，默认会保留所有编辑。

要将资源从一个本体迁移到另一个本体，请执行以下操作：

1. 通过 **Ontology Manager** 右上角的本体切换器，导航到拥有该资源的本体。
![本体选择下拉菜单截图。](https://www.palantir.com/docs/resources/foundry/ontologies/ontology-switcher.png)
2. 在同一本体中选择 **迁移资源（Migrate resources）** 以启动迁移流程。然后，使用本体选择下拉菜单在顶行选择目标本体。
![本体迁移目标选择截图。](https://www.palantir.com/docs/resources/foundry/ontologies/ontology-migration-switcher.png)
3. 选择要迁移的对象类型、链接类型、动作类型和工作流。所选待迁移资源的预览会分别显示在其当前本体（左侧）和目标本体（右侧）中。请注意，无法将对象类型从私有本体迁移到默认本体，除非该对象类型最初就是在默认本体中创建的。
![本体迁移对话框截图。](https://www.palantir.com/docs/resources/foundry/ontologies/ontology-migration.png)

> **⚠️ 注意：迁移到默认本体**
>
> 请确保将相互关联的资源一起迁移。如果所选内容中缺少相关的本体资源，迁移将会失败。

1. 完成选择后，选择 **提交（Submit）** 以迁移资源。

---

*原文：[Migrate ontological resource between Ontologies](https://www.palantir.com/docs/foundry/ontologies/ontology-migration/)*
