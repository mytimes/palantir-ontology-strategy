# 结构体属性与共享属性类型（Struct properties and shared property types）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/struct-shared-properties/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

结构体属性可以被本地属性类型和共享属性类型使用。在将本地属性类型转换（或提升）为共享属性类型时，需要重新映射结构体字段。由共享属性类型支撑的本地结构体属性类型将继承共享属性类型的字段，但结构体字段资源标识符（RID）除外。结构体字段元数据（显示名、描述、别名）随后将从共享属性类型继承，但结构体字段将保留其原始 RID。

## 创建结构体类型共享属性

1. 在 Ontology Manager 中，你可以通过两种方式创建结构体类型共享属性：
  - 从主页右上角选择 **New > Shared property**（新建 > 共享属性）
![New 下拉菜单中的共享属性选项。](https://www.palantir.com/docs/resources/foundry/object-link-types/new-dropdown-menu-shared-property.png?width=300)
  - 选择 **Shared properties > + New shared property**（共享属性 > + 新建共享属性）
![共享属性选项卡中的“+ 新建共享属性”选项。](https://www.palantir.com/docs/resources/foundry/object-link-types/new-shared-property-button.png?width=500)
2. 在主面板中，选择 **New shared property**（新建共享属性）按钮。这将打开一个配置向导，你可以在其中配置共享属性的元数据，包括名称、描述、别名和 API 名称。然后选择 **Next**（下一步）继续。 ![“创建共享属性”对话框。](https://www.palantir.com/docs/resources/foundry/object-link-types/create-shared-property-modal.png?width=500)
3. 配置基础类型、值类型、可见性以及是否要求该属性具有值。 ![配置步骤中的创建共享属性窗口。](https://www.palantir.com/docs/resources/foundry/object-link-types/create-shared-property-configuration.png?width=500)
4. 选择一个项目来保存此共享属性。 ![选择共享属性的保存位置。](https://www.palantir.com/docs/resources/foundry/object-link-types/create-shared-property-save-location.png?width=500)
5. 回到 Ontology Manager，选择右上角的 **Save**（保存）以[将更改应用到你的本体](https://www.palantir.com/docs/foundry/ontology-manager/save-changes/)。

## 附加结构体类型共享属性

1. 在 Ontology Manager 中，打开 **Properties**（属性）选项卡，并从 **Properties** 表格中选择所需的属性。
2. 在右侧的 **Property editor**（属性编辑器）中，向下滚动到 **Shared property**（共享属性），并在 **Assign**（分配）下选择一个共享属性。这将在两个属性之间共享属性元数据。

![分配部分中的共享属性下拉菜单。](https://www.palantir.com/docs/resources/foundry/object-link-types/spt-attachment.png?width=500)

**注意：** 在将共享属性类型分配给本地结构体属性类型之后，若要添加新的结构体字段，你必须将新的结构体字段添加到共享属性类型，并为所有由该共享属性支撑的本地结构体属性类型将这些字段映射到数据源列。

## 将结构体属性类型转换为共享属性

以下说明详细介绍了如何将结构体属性转换为由共享属性类型支撑的结构体属性。

1. 在 Ontology Manager 中，打开 **Properties**（属性）选项卡，并从 **Properties** 表格中选择所需的属性。
2. 在右侧的 **Property editor**（属性编辑器）中，向下滚动并选择 **Convert to a shared property**（转换为共享属性），这将使该结构体属性由共享属性类型支撑。

![属性编辑器中的“转换为共享属性”按钮。](https://www.palantir.com/docs/resources/foundry/object-link-types/spt-convert.png?width=500)

---

*原文：[Struct properties and shared property types](https://www.palantir.com/docs/foundry/object-link-types/struct-shared-properties/)*
