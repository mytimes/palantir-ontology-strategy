# 结构体主字段 [Beta]（Struct main fields [Beta]）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/struct-main-fields/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

> **ℹ️ 提示：Beta**
>
> 结构体主字段处于开发的 [Beta](https://www.palantir.com/docs/foundry/platform-overview/development-life-cycle/) 阶段，可能在你的注册实例上不可用。功能仍在活跃开发中，可能会发生变化。

**结构体主字段（Struct main fields）** 使你能够指定[结构体](https://www.palantir.com/docs/foundry/object-link-types/structs-overview/)的核心值和补充元数据。例如，`Address` 结构体可能包含以 `streetName` 和 `postalCode` 作为主值的字段，而其他字段（如 `collectionDate` 和 `collectorName`）则是描述 `Address` 获取方式的元数据。

许多结构体属性都遵循这种模式：一个或多个字段包含你最希望在应用中显示的主要数据，而其他字段则提供上下文、跟踪信息或审计详细信息。

你可以将任何[受支持的结构体字段类型](https://www.palantir.com/docs/foundry/object-link-types/structs-overview/#struct-configuration)指定为主字段。

## 何时使用结构体主字段

在以下情况下使用结构体主字段：

- 你的结构体除了表示补充元数据的字段外，还包含核心值字段。
- 你希望表格显示更简洁，同时又不失去对元数据的访问。
- 你需要仅使用结构体中的单个字段或部分字段来[实现接口](https://www.palantir.com/docs/foundry/interfaces/implement-interface/)。

## 配置结构体主字段

1. 导航到 **Ontology Manager**。
2. 在左侧面板的 **Resources**（资源）下选择 **Object types**（对象类型），搜索并选择你的对象类型。
3. 从对象类型的 **Properties**（属性）选项卡中选择要配置的结构体属性。
4. 在右侧打开的属性编辑器面板中，从 **General**（常规）选项卡的 **Struct fields**（结构体字段）部分选择要指定的字段。

![主字段配置位置](https://www.palantir.com/docs/resources/foundry/object-link-types/struct-main-fields-sidebar-location.png?width=400)

1. 在 **Edit {propertyName} struct field**（编辑 {propertyName} 结构体字段）弹出窗口中打开 **Struct main field**（结构体主字段）开关，然后再选择 **Confirm**（确认）。你可以指定多个主字段，并通过单击和拖动字段面板来重新排序它们，以使显示更清晰。

![结构体主字段开关](https://www.palantir.com/docs/resources/foundry/object-link-types/struct-main-fields-toggle.png?width=450)

1. **Save**（保存）你的更改。配置的主字段会在 **Struct fields**（结构体字段）列表中显示 **Struct main field**（结构体主字段）标签。

![结构体主字段标签](https://www.palantir.com/docs/resources/foundry/object-link-types/struct-main-fields-tags.png?width=400)

## 结构体主字段在应用中的显示方式

支持结构体主字段的应用在紧凑视图（如 [Workshop](https://www.palantir.com/docs/foundry/workshop/overview/) 中的 [Object Table](https://www.palantir.com/docs/foundry/workshop/widgets-object-table/)（对象表格）和 [Object List](https://www.palantir.com/docs/foundry/workshop/widgets-object-list/)（对象列表）组件）中仅显示主字段，同时通过展开视图或悬停提供对完整结构体的访问。这使你能够快速浏览最重要的数据而无需查看每个结构体字段，同时在需要时仍能检查完整的结构体。

应用以多种方式支持结构体主字段：

- **仅主字段：** 在表格或摘要卡片等紧凑视图中仅显示主字段。
- **主字段加悬停：** 默认显示主字段，悬停时显示元数据字段。
- **完整结构体：** 在需要完整信息的详细视图或表单中显示所有字段。

## 将结构体字段与接口一起使用

你可以将任何结构体字段映射到接口属性。实现接口时，你可以从结构体属性中选择特定字段来满足接口契约和属性要求。

例如，如果接口需要一个名为 `cityName` 的 `String` 属性，你可以使用结构体属性的 `city` 字段来满足它。接口选择器会显示所有可用的结构体字段及其类型，使你能够选择合适的字段。

![选择结构体字段以映射到接口属性](https://www.palantir.com/docs/resources/foundry/object-link-types/struct-main-fields-interface-picker.png?width=700)

将结构体字段映射到接口属性后，该接口实现会显示该结构体属性以及正在使用的特定字段。

![已映射到接口属性的结构体字段](https://www.palantir.com/docs/resources/foundry/object-link-types/struct-main-fields-interface-mapped.png?width=700)

主字段在选择器中用 **Struct main field**（结构体主字段）标签标示，便于识别。但是，无论字段是否被指定为主字段，你都可以从结构体中选择任何字段。

## 将主字段与属性归约器结合使用

将结构体主字段与[属性归约器](https://www.palantir.com/docs/foundry/object-link-types/property-reducers/)结合使用，可以在结构体数组属性的表示和接口实现方面获得更大的灵活性。单个结构体数组属性可以实现需要 `Struct Array`、主字段的数组类型、`Struct` 或主字段的基础类型的接口。

有关详细示例和实现选项，请参见[属性归约器文档](https://www.palantir.com/docs/foundry/object-link-types/property-reducers/#combine-property-reducers-with-struct-main-fields)。

## 限制和注意事项

- **查询行为：** 查询对所有结构体字段进行操作，而不仅仅是主字段。你可以基于结构体中的任何字段进行搜索和筛选。

## 常见问题

### 以后可以更改主字段的选择吗？

可以，你可以随时在 Ontology Manager 中重新配置主字段。这可能会使某些接口实现失效，你需要更新这些实现。

### 主字段会影响 Foundry 存储数据的方式吗？

不会，主字段仅影响 Foundry 显示数据和实现接口的方式。底层结构体完整保留所有字段，因此这些字段仍可查询和访问。

### 我可以将主字段与结构体数组一起使用吗？

可以。主字段*同时*适用于单个结构体属性和结构体数组属性。与[归约器](https://www.palantir.com/docs/foundry/object-link-types/property-reducers/)结合使用时，Foundry 在跨应用表示该属性方面可提供最大的灵活性。

### 我需要配置主字段才能将结构体字段与接口一起使用吗？

不需要。无论字段是否被指定为主字段，你都可以将任何结构体字段映射到接口属性。主字段只是在接口选择器中提供视觉指示，并影响应用在紧凑视图中显示结构体的方式。

---

*原文：[Struct main fields [Beta]](https://www.palantir.com/docs/foundry/object-link-types/struct-main-fields/)*
