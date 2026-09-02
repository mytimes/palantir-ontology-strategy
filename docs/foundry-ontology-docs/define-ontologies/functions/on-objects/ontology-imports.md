# 导入对象、接口和链接类型（Import object, interface, and link types）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/ontology-imports/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

你想在函数中使用的任何对象、接口或链接类型都必须导入到包含你的代码仓库的项目中。选择 **Resource Imports**（资源导入）侧边栏以查看已导入到项目中的对象类型。

![本体导入侧边栏](https://www.palantir.com/docs/resources/foundry/functions/ontology-import-side-panel.png)

> **ℹ️ 提示**
>
> 你的组织可能没有 `Airport` 和 `Flight` 对象。在按照这些步骤操作时，使用你有权访问的任何对象类型。

要导入更多对象类型，你需要在 **Resource Imports** 侧边栏中选择 **Add**（添加）按钮。如果尚未选择本体，系统会提示你选择一个本体。如果你至少导入了一种本体类型，所选本体将被自动解析。

选择本体后，将出现一个搜索模态框。你的本体取决于你的组织中可用的对象类型。首先选择一些对象类型以及连接它们的链接类型。本示例导入了 `Airport` 和 `Flight` 对象，以及它们之间的链接类型。

![本体导入示例](https://www.palantir.com/docs/resources/foundry/functions/ontology-import-example.png)

你还可以通过选择 **Add** 按钮下的 **Interfaces**（接口）来导入本体接口。

![“Add” 下拉菜单下用于导入接口的选项。](https://www.palantir.com/docs/resources/foundry/functions/interface-import-example.png)

选择 **Save**（保存）将本体类型导入项目。Code Assist 将自动重启以重新生成代码绑定，从而反映你导入的新对象类型和链接类型。

现在，你可以在代码中从 `@foundry/ontology-api` 包导入本体类型。如果你使用的是私有本体，包名将改为 `@foundry/ontology-api/<ontology-api-name>`。

Code Assist 启动后，你可以按住 `Ctrl` 并单击 @foundry/ontology-api 包名来查看所有可用的对象类型。打开的 index.ts 文件显示了你可以导入到代码中的所有有效对象类型：

![ontology-api](https://www.palantir.com/docs/resources/foundry/functions/ontology-api.png)

如果你有权访问多个本体，可以使用选择器来挑选你想使用的本体。目前不支持将多个本体导入单个项目。

---

*原文：[Import object, interface, and link types](https://www.palantir.com/docs/foundry/functions/ontology-imports/)*
