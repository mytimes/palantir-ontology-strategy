# 对象类型组（Object type groups）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/type-groups/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

对象类型组是一种分类基元，可帮助用户更好地搜索和探索他们的本体。组使用 Ontology Manager 创建和管理，通常由本体[所有者和编辑者](https://www.palantir.com/docs/foundry/object-link-types/type-groups/#group-permissions)操作。

## 组配置

组通过组菜单创建和管理，该菜单可在 Ontology Manager 侧边栏中访问。

![选择或添加新组](https://www.palantir.com/docs/resources/foundry/object-link-types/groups-menu.png)

也可以通过在对象类型概览页面中选择 **Edit groups（编辑组）**，直接将组添加到对象类型。

![向对象类型添加组](https://www.palantir.com/docs/resources/foundry/object-link-types/group-add-to-object.png)

## 组搜索与发现

组可以在 [Ontology Manager 的 **Search（搜索）**栏和 **Search** 栏对话框](https://www.palantir.com/docs/foundry/ontology-manager/navigation/#header-search-bar)中搜索。Ontology Manager 中的对象类型表格支持按组显示和筛选。组也显示在 [Object Explorer 主页](https://www.palantir.com/docs/foundry/object-explorer/getting-started/#group-exploration-b-c-d)上。

![按组筛选](https://www.palantir.com/docs/resources/foundry/object-link-types/object-type-groups-add.png)

## 组权限

要查看对象类型组，用户必须对对象类型组所在的项目具有 **viewer**（查看者）权限。

## 旧版组迁移

自 2024 年 5 月 22 日起，本页所述的*组*基元已取代旧版组的基于标签的系统。

在大多数情况下，旧版组在此时已自动迁移为对象类型组。如果需要手动操作，本体所有者会收到 Upgrade Assistant（升级助手）的介入通知。

### 组名称可见性

此前，如果某个组内的所有对象类型对某用户都不可发现（例如，由于支撑数据集上的访问控制），该组对该用户也不可发现。如上文中关于[组权限](https://www.palantir.com/docs/foundry/object-link-types/type-groups/#group-permissions)的部分所述，现在所有组对任何能够查看本体的用户都是可发现的。这一变更使组可见性与其他[本体基元](https://www.palantir.com/docs/foundry/object-permissioning/ontology-permissions-legacy/#ontology-roles)保持一致，以提高治理的清晰度和透明度。

### 部分可见组的迁移

对一个或多个用户不可发现的旧版组不符合自动迁移条件。在这些情况下，本体所有者会收到 Upgrade Assistant 介入通知，告知需要手动操作。

2024 年 5 月 22 日，无法安全迁移的旧版组对 Workshop 和 Object Explorer 等所有应用中的运营用户隐藏。为提供向后兼容性，旧版组的名称仍作为[类型类元数据](https://www.palantir.com/docs/foundry/object-link-types/metadata-typeclasses/)存储在对象类型上。

本体所有者可以继续使用 Ontology Manager 手动迁移这些隐藏的旧版组。为此，请导航到左下角的 **Ontology Configuration（本体配置）**菜单，然后选择 **Approve all Groups for migration（批准所有组迁移）**。

---

*原文：[Object type groups](https://www.palantir.com/docs/foundry/object-link-types/type-groups/)*
