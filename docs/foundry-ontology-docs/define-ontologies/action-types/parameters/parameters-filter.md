# 筛选参数下拉菜单的结果（Filter results of a parameter dropdown menu）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/action-types/parameters-filter/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

为非对象引用的多选参数或单对象引用参数添加筛选条件，将决定参数下拉菜单中可供选择的允许值。

## 多选参数下拉菜单

在配置多选参数下拉菜单时，动作编辑者可以将允许值缩减为某个对象集的属性值。可以利用这一点，基于所链接对象的属性来显示或预填值。要实现这一点，请确保参数设置为显示多个选项，选择**从对象集获取选项（Get options from an object set）**，配置所需的对象集，然后选择包含参数下拉菜单所有允许值的属性。如果生成的对象集中只有一个链接对象，且该参数为必填参数，则参数下拉菜单将自动预填相应的属性值。生成的多选选项将派生自用户有权查看的对象集。换言之，从对象集派生多选选项时，用户看不到其无权访问的对象的属性。

![属性下拉菜单配置](https://www.palantir.com/docs/resources/foundry/action-types/property_dropdown_configuration.png)

## 对象下拉菜单

在参数配置视图中，动作编辑者可以指定筛选条件和环绕搜索（Search Around），以限制所有动作界面中下拉菜单显示的对象。配置筛选条件后，动作表单将渲染一个仅包含与筛选条件匹配的对象的下拉菜单。所选的值还会在动作执行前进行校验。

例如，一个对象下拉菜单配置为仅显示**名称（Name）**等于 `Name` 参数值的**股票系列（Stock Series）**。

![对象下拉菜单起始集](https://www.palantir.com/docs/resources/foundry/action-types/objectDropdownStartingSet.png)

下图展示了 `Name` 参数的可能取值：

![对象下拉菜单生成的表单](https://www.palantir.com/docs/resources/foundry/action-types/objectDropdownResultingForm.png)

### 下拉菜单选项中显示的属性

下拉菜单选项会显示每个对象的标题以及对象类型的[显著属性](https://www.palantir.com/docs/foundry/object-link-types/property-metadata/#metadata-reference)。与搜索词匹配的属性会优先显示，匹配文本高亮，随后显示其余显著属性。没有值的显著属性显示为 `No value`。

显示哪些属性取决于在 Ontology Manager 中为对象类型配置的可见性，而不是动作类型上的配置。用户只能看到其有权查看的对象，用户无权查看的属性值不会显示。

### 数据隐私影响

对对象参数使用新的校验机制时，所有能够查看该动作类型的人都有可能看到数据。如果参数筛选条件中包含敏感的静态值，即使用户无法查看被筛选的底层对象，也能够看到这些值。[详细了解数据隐私影响。](https://www.palantir.com/docs/foundry/action-types/dropdown-security/)

## 支持的操作

### 按属性筛选

对象下拉菜单仅显示指定属性与所提供的任一值匹配的对象。

![对象下拉菜单按属性筛选](https://www.palantir.com/docs/resources/foundry/action-types/object_dropdown_filtering_on_property.png)

该值可以由用户静态定义、从另一个参数推断，或来自 `Object Reference`（对象引用）参数的某个属性。如果提供了多个值用于比较，则结果为 **OR**（或）运算。

### 更改起始对象集

查询的**起始集（starting set）**默认设置为该对象类型的所有对象，但可以更改为任何其他类型。起始集也可以设置为 `ObjectReference` 列表参数。

![对象下拉菜单更改起始集](https://www.palantir.com/docs/resources/foundry/action-types/object_dropdown_changing_starting_set.png)

### 环绕搜索

环绕搜索会遍历当前集合中每个对象上的某个链接，从而创建一个新集合。例如，`Github Issue of Current Employee` 会取当前集合中的 `Employees`，并创建一个与这些 `Employees` 相链接的 `Github Issues` 结果集合。

![对象下拉菜单环绕搜索](https://www.palantir.com/docs/resources/foundry/action-types/object_dropdown_search_around.png)

---

*原文：[Filter results of a parameter dropdown menu](https://www.palantir.com/docs/foundry/action-types/parameters-filter/)*
