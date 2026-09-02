# 在平台中使用动作（Use actions in the platform）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/action-types/use-actions/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

动作类型可以无缝集成到 Foundry 的各个应用中。继续阅读，了解如何从 [Object Explorer](https://www.palantir.com/docs/foundry/object-explorer/overview/) 和 [Workshop](https://www.palantir.com/docs/foundry/workshop/overview/) 配置和应用动作。

在下文中，我们使用**单个动作类型**一词指代使用对象引用参数的动作类型，使用**批量动作类型**指代使用对象引用列表参数的动作。

## 对象视图

可以使用**“动作”区块**将动作添加到 [Object View](https://www.palantir.com/docs/foundry/object-views/overview/) 中。

![Object View 的“动作”区块](https://www.palantir.com/docs/resources/foundry/action-types/integrate_actions_object_explorer_object_view_actions_section.png)

配置**“动作”区块**时，你可以：

- 将任意动作作为按钮添加到该区块中。
- 为每个按钮设置独立的标签和颜色。
- 更改默认的点击行为：默认点击会打开表单；你可以改为使用默认值立即应用动作（前提是默认值有效）。
- 指定当某个不可见参数无效时，按钮应当隐藏还是禁用（其思路是：可见参数可以在打开表单后再修正）。
- 为每个参数提供默认值；默认值可以是当前对象的某个属性值，也可以是一个“本地”值（当前用户、当前时间戳、当前对象，或手动输入的值）。
- 覆盖每个参数的可见性。

因此，如上所示，你可以利用该区块为同一个通用动作提供多个结构化版本（“延迟 10 分钟”“延迟 30 分钟”等）。

## Object Explorer

动作会自动出现在 [Object Explorer](https://www.palantir.com/docs/foundry/object-explorer/overview/) 的三个位置：

1. 探索视图（Exploration View）右上角的**“动作”下拉菜单**中。

![探索视图中的“动作”下拉菜单](https://www.palantir.com/docs/resources/foundry/action-types/integrate_actions_object_explorer_exploration_view_actions_dropdown.png)

该下拉菜单会使用当前对象集，自动填充适用的批量动作。

2. 对象视图右上角的**“对象动作”下拉菜单**中。

![对象视图中的“对象动作”下拉菜单](https://www.palantir.com/docs/resources/foundry/action-types/integrate_actions_object_explorer_object_view_object_actions_dropdown.png)

该下拉菜单会使用当前对象，自动填充适用的单个动作类型和批量动作类型。

3. 对象视图顶部的**“链接对象视图”区块**中。

![对象视图中的“链接对象视图”区块](https://www.palantir.com/docs/resources/foundry/action-types/integrate_actions_object_explorer_object_view_linked_objects_view_section.png)

该下拉菜单会使用所选对象，自动填充适用的单个动作类型和/或批量动作类型。

> **ℹ️ 提示**
>
> 在“批量”场景中（即列表视图里显示多个对象时），只会显示接受正确类型的对象列表参数的动作。

## Workshop

在 [Workshop](https://www.palantir.com/docs/foundry/workshop/overview/) 中，可以使用[**按钮组**组件](https://www.palantir.com/docs/foundry/workshop/widgets-button-group/)来配置和应用动作。

![Workshop 中的按钮组组件](https://www.palantir.com/docs/resources/foundry/action-types/integrate_actions_workshop_button_group_widget.png)

该组件具有与对象视图中[“动作”区块](#对象视图)相同的配置选项，此外还有几个值得注意的扩展：

- 共有三种可能的布局，上图中均有展示。
- 按钮还有更多显示选项，包括左侧/右侧图标、极简样式和标签样式。
- 除了动作之外，单个按钮还可以触发 Workshop 事件、打开 URL 或导出对象集。

还有一点不同：

- 默认值可以是[变量](https://www.palantir.com/docs/foundry/workshop/concepts-variables/)、当前用户或当前时间戳。

阅读更多关于 [Workshop 中的动作](https://www.palantir.com/docs/foundry/workshop/actions-overview/)的内容，或阅读[按钮组组件](https://www.palantir.com/docs/foundry/workshop/widgets-button-group/)的完整参考，了解所有可用的配置选项。

---

*原文：[Use actions in the platform](https://www.palantir.com/docs/foundry/action-types/use-actions/)*
