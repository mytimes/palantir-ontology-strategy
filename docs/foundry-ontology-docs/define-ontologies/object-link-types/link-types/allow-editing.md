# 允许用户编辑对象和链接（Allow users to edit objects and links）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/allow-editing/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

## 在 Foundry 对象应用中编辑数据

你可以允许用户在用户应用（如 Workshop 和 Object Views）中编辑属性值、添加和移除链接，以及创建和删除对象。你还可以根据用户所做的编辑配置副作用（如通知）。

配置此功能的受支持方式是在 Ontology Manager 中创建和配置动作类型。[详细了解如何设置动作类型。](https://www.palantir.com/docs/foundry/action-types/overview/)

本文档的其余部分介绍：在用户能够执行动作之前，需要在对象类型和链接类型上进行哪些配置。

## 从外部应用编辑数据

[Objects API](https://www.palantir.com/docs/foundry/api/ontology-resources/actions/apply-action/) 提供了端点，供外部客户端写入和更新对象、属性和链接，并强制执行完整权限检查。

## 设置前提条件

为了让用户能够执行动作类型配置中定义的动作，必须创建一个回写数据集（writeback dataset）。回写数据集在构建时会读取用户所做的编辑，并反映任一给定对象的最新状态。

> **ℹ️ 提示**
>
> 请注意，编辑会写入回写数据集，而不是写入支撑对象类型或链接类型的数据集。这可以确保用户在分析中既能访问原始数据，也能访问编辑后的数据。

要设置回写数据集：

1. 导航到你要启用编辑的对象类型或链接类型的 **Datasources（数据源）**页面。
2. 在页面的 **Writeback dataset（回写数据集）**部分选择 **Generate（生成）**，以创建新的回写数据集。随后会打开一个对话框，要求你选择用于放置该数据集的项目。选择一个位置。
3. 确保你希望能够编辑该对象类型或链接类型的用户对回写数据集拥有编辑权限。
4. 确保你希望能够查看对该对象类型或链接类型所做变更的用户对回写数据集拥有查看权限。
  - 查看对象和链接的能力由对象类型和链接类型的支撑数据源控制。
  - 查看对象和链接上的编辑内容的能力由回写数据集上的权限控制。
  - 如果用户只能访问前者，他们只能看到未应用编辑时的对象现状。如果用户可以访问后者，他们既能看到编辑内容，也能看到对象当前的状态。

> **ℹ️ 提示**
>
> 如果你希望对象类型中的某个属性捕获最终用户手动输入的数据（通过动作或其他回写方式），而这些数据在 Foundry 中尚不存在，你需要向该对象类型的支撑数据集添加一个空列，并将其映射为对象类型中的新属性。你还需要启用编辑；这可以通过在 Object Storage v1 中创建回写数据集，或在 Object Storage v2 中开启编辑开关来完成。

---

*原文：[Allow users to edit objects and links](https://www.palantir.com/docs/foundry/object-link-types/allow-editing/)*
