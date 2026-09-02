# 编辑接口定义（Edit an interface definition）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/interfaces/edit-interface-definition/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

> **⚠️ 注意：Breaking（破坏性变更）**
>
> 鉴于接口会暴露 API 名，对接口定义的任何更改都有可能破坏下游应用，并且必然会破坏现有的对象实现。当向接口添加新的必填属性、链接类型约束或动作类型约束时，使用该接口的对象类型的所有实现**必须**在对你的本体的同一次更新中完成。我们还建议同时更新你的接口定义和消费方。
> 如果你的下游应用无法与接口更改同时更新，你可以另行为接口创建一个新版本（作为[扩展](https://www.palantir.com/docs/foundry/interfaces/extend-interface/)或独立接口），并尽快迁移到新的接口定义。

## 添加新属性

在接口配置的 **Properties**（属性）选项卡中，选择 **New property**（新建属性）。这将打开一个接口属性配置侧面板。

![编辑接口属性。](https://www.palantir.com/docs/resources/foundry/interfaces/edit-interface-properties.png?width=800)

编辑属性元数据的可用选项集中在四个不同的选项卡中，可访问以下配置：

1. **显示名和描述：** 选择现有的显示名或描述以编辑文本。
2. **API 名：** 选择现有的 API 名以更改其值。
3. **属性基础类型：** 从下拉菜单中选择属性的基础类型。属性的类型约束了可对属性值执行的操作集合。
  - 例如，基础类型为 `timestamp` 的属性可以在 Object Explorer 的时间线组件中显示。
4. **主键约束：** 指明某个属性应当为主键还是不能为主键。

> **⚠️ 注意**
>
> 如果你更改了接口属性类型，你还必须更新实现此接口的所有对象类型。

1. **类型类：** 应用类型类作为可被应用解释的额外元数据。
  - 查阅[类型类文档](https://www.palantir.com/docs/foundry/object-link-types/metadata-typeclasses/)以获取可用类型类的列表。
2. **渲染提示：** 通过从清单中选择渲染提示，改进属性值的渲染方式以及编入 Object Storage v1（Phonograph）索引的方式。
  - 有关可用渲染提示的描述，请参阅[渲染提示文档](https://www.palantir.com/docs/foundry/object-link-types/metadata-render-hints/)。
3. **可见性：** 选择现有的可见性以打开可用可见性的下拉菜单。`prominent`（突出）属性会引导应用首先向用户展示该属性。`hidden`（隐藏）属性不会出现在用户应用中。

## 添加共享属性

在接口配置的 **Properties**（属性）选项卡中，选择 **Add shared properties**（添加共享属性），并选择要添加到接口的共享属性。

## 添加链接类型约束

在 **Link type constraints**（链接类型约束）选项卡中，选择 **Create new link type constraint**（创建新链接类型约束），并添加必要的[约束元数据](https://www.palantir.com/docs/foundry/interfaces/create-interface/#create-interface-link-types-optional)。

## 添加动作类型约束

在 **Action type constraints**（动作类型约束）选项卡中，选择 **Create new action type constraint**（创建新动作类型约束），并添加必要的[约束元数据](https://www.palantir.com/docs/foundry/interfaces/interface-action-type-constraints/#action-type-constraints)和[参数约束](https://www.palantir.com/docs/foundry/interfaces/interface-action-type-constraints/#parameter-constraints)。

## 移除属性

在 **Properties**（属性）选项卡中，选择你希望从接口移除的属性旁边的 **...**。或者，打开接口属性侧面板并选择右上角的垃圾桶图标。

![从接口移除属性。](https://www.palantir.com/docs/resources/foundry/interfaces/remove-property-from-interface.png?width=800)

## 移除或编辑链接类型约束

在 **Link type constraint**（链接类型约束）选项卡中，选择你希望从接口编辑或移除的链接类型约束旁边的 **...**。

![移除或编辑链接类型约束。](https://www.palantir.com/docs/resources/foundry/interfaces/remove-link-type-constraint.png?width=800)

如果编辑约束，你可以像首次[创建链接类型约束](https://www.palantir.com/docs/foundry/interfaces/create-interface/#create-interface-link-types-optional)时那样更新元数据。

## 移除或编辑动作类型约束

在 **Action type constraints**（动作类型约束）选项卡中，选择你希望从接口编辑或移除的动作类型约束旁边的 **...**。

如果编辑约束，你可以像首次[创建动作类型约束](https://www.palantir.com/docs/foundry/interfaces/interface-action-type-constraints/#action-type-constraints)时那样更新元数据和参数配置。

---

*原文：[Edit an interface definition](https://www.palantir.com/docs/foundry/interfaces/edit-interface-definition/)*
