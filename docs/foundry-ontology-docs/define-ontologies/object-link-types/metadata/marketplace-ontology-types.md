# 将对象类型和链接类型添加到 Marketplace 产品（Add object and link types to a Marketplace product）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/marketplace-ontology-types/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

使用 [Foundry DevOps](https://www.palantir.com/docs/foundry/devops/overview/) 将你的对象类型和链接类型包含到 [Marketplace 产品](https://www.palantir.com/docs/foundry/devops/core-concepts/#product)中，供其他用户安装和复用。[了解如何创建你的第一个产品。](https://www.palantir.com/docs/foundry/foundry-devops/create-products/)

## 不支持的功能

大多数[对象属性类型](https://www.palantir.com/docs/foundry/object-link-types/properties-overview/)在 Marketplace 产品中受支持，但以下类型尚不可用：

- [Cipher](https://www.palantir.com/docs/foundry/cipher/overview/)
- Geo time（地理时间）
- Vector（向量）

Marketplace 产品尚不支持以下情况：

- 带有流式数据源的对象类型
- 没有数据源的对象类型

请注意，对象本身无法与 Marketplace 一起打包。这意味着，例如，由动作产生的对象编辑无法打包到 Marketplace 产品中。不过，数据集和对象类型可以打包，以便在安装 Marketplace 产品后创建新对象。

如果你需要上述任何方面的支持，请联系你的 Palantir 代表。

## 向产品添加对象类型

要向产品添加对象类型，首先[创建一个产品](https://www.palantir.com/docs/foundry/foundry-devops/create-products/)。[添加输出](https://www.palantir.com/docs/foundry/foundry-devops/create-products/#add-outputs)，然后选择 **Add ontology entities（添加本体实体）**选项。

随后系统会提示你选择一个对象类型。选择对象类型后，你会看到一些推荐的已链接对象类型，可以酌情添加到产品中。

![添加对象类型](https://www.palantir.com/docs/resources/foundry/object-link-types/marketplace-add-object-type-dialog.png)

## 向产品添加链接类型

要向产品添加链接类型，首先[创建一个产品](https://www.palantir.com/docs/foundry/foundry-devops/create-products/)，然后选择 **Link type（链接类型）**内容类型。

随后系统会提示你选择链接类型，如下所示。

![添加链接类型](https://www.palantir.com/docs/resources/foundry/object-link-types/marketplace-add-link-type-dialog.png)

虽然你可以直接选择链接类型，但我们建议先添加对象类型，然后通过[信息面板](https://www.palantir.com/docs/foundry/foundry-devops/create-products/#add-outputs)选择相关链接，如下所示。

![通过面板添加链接类型](https://www.palantir.com/docs/resources/foundry/object-link-types/marketplace-add-link-type-panel.png)

## 向产品添加共享属性

要向产品添加共享属性类型，首先[创建一个产品](https://www.palantir.com/docs/foundry/foundry-devops/create-products/)。然后，如下所示选择 **Shared property（共享属性）**内容类型。

随后系统会提示你选择一个共享属性。

![向 Marketplace 产品添加共享属性。](https://www.palantir.com/docs/resources/foundry/object-link-types/marketplace-add-shared-property-dialog.png)

## 向产品添加接口类型

要向产品添加接口类型，首先[创建一个产品](https://www.palantir.com/docs/foundry/foundry-devops/create-products/)。然后，如下所示选择 **Interface（接口）**内容类型。

随后系统会提示你选择一个接口。

![向 Marketplace 产品添加接口类型。](https://www.palantir.com/docs/resources/foundry/object-link-types/marketplace-add-interface-dialog.png)

---

*原文：[Add object and link types to a Marketplace product](https://www.palantir.com/docs/foundry/object-link-types/marketplace-ontology-types/)*
