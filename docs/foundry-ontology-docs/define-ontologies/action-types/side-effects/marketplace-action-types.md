# 将动作类型添加到 Marketplace 产品（Add action types to Marketplace product）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/action-types/marketplace-action-types/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

使用 [Foundry DevOps](https://www.palantir.com/docs/foundry/devops/overview/) 将你的动作类型纳入 [Marketplace 产品](https://www.palantir.com/docs/foundry/devops/core-concepts/#product)，供其他用户安装和复用。[了解如何创建你的第一个产品。](https://www.palantir.com/docs/foundry/foundry-devops/create-products/)

## 支持的特性

大多数动作类型特性都受支持，但引用了[具有不受支持特性的对象类型](https://www.palantir.com/docs/foundry/object-link-types/marketplace-ontology-types/#unsupported-features)的动作除外。在准备打包动作类型时，请确保动作类型的[**安全与提交条件**](https://www.palantir.com/docs/foundry/action-types/getting-started/#add-submission-criteria)不引用任何用户；将所有用户引用更新为引用用户组。

## 将动作类型添加到产品

要将动作类型添加到产品，首先[创建一个产品](https://www.palantir.com/docs/foundry/foundry-devops/create-products/)，然后如下所示选择**动作类型（Action type）**内容类型。

![添加动作类型。](https://www.palantir.com/docs/resources/foundry/action-types/marketplace-add-action-type.png)

然后系统会提示你选择一个动作类型。

![添加动作类型对话框。](https://www.palantir.com/docs/resources/foundry/action-types/marketplace-add-action-type-dialog.png)

虽然你可以直接选择动作类型，但我们建议先添加 [Workshop 应用](https://www.palantir.com/docs/foundry/workshop/marketplace-workshop/)等内容，然后通过依赖面板选择相关动作，如下所示。

![通过面板添加动作类型。](https://www.palantir.com/docs/resources/foundry/action-types/marketplace-add-action-type-panel.png)

---

*原文：[Add action types to Marketplace product](https://www.palantir.com/docs/foundry/action-types/marketplace-action-types/)*
