# 渲染提示（Render hints）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/metadata-render-hints/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

Foundry 使用**渲染提示（render hints）**向平台中的 [Object Storage v1（Phonograph）](https://www.palantir.com/docs/foundry/object-databases/object-storage-v1/)和用户应用传达关于本体[属性](https://www.palantir.com/docs/foundry/object-link-types/properties-overview/)使用方式的信息。例如，字符串属性上的 `sortable`（可排序）渲染提示告知应用允许用户对该属性进行排序，如在时间线或图表中。

许多渲染提示与对象类型的重新索引性能相关。例如，你可以使用渲染提示向 [Object Storage v1（Phonograph）](https://www.palantir.com/docs/foundry/object-databases/object-storage-v1/)表明某个属性无需在应用中聚合或排序，从而使 Object Storage v1 在为这些属性建立索引时工作量更少。

你可以在属性编辑器的属性窗格中选中和取消选中渲染提示（见下图）。

![渲染提示](https://www.palantir.com/docs/resources/foundry/object-link-types/render-hints.png)

下表列出了每个可用渲染提示的**名称（Name）**和**描述（Description）**。该表还提供了渲染提示的两个技术方面的信息：“是否添加原始索引？（Adds raw index?）”和“是否需要重新索引？（Requires reindex?）”（如下所述）。

- **是否添加原始索引？**
  - 对于会添加原始索引的渲染提示，Object Storage v1（Phonograph）会在存储支撑数据集时额外创建一个索引来存储渲染提示信息。
  - 由于这个额外索引，对于应用了渲染提示的属性，将有两列计入编入 Object Storage v1（Phonograph）的总列数。
  - 这就解释了为什么取消选中这些渲染提示可以提升重新索引到 Object Storage v1（Phonograph）的性能。
- **是否需要重新索引？**
  - 某些渲染提示一旦在 Ontology Manager 中保存了选中状态，便会立即在用户应用中生效。
  - 对于其他需要重新索引的渲染提示，必须先将对象类型的支撑数据源重新索引到 Object Storage v1（Phonograph），更改才会反映在用户应用中。
  - 你可以等待下一次触发的重新索引，也可以导航到对象类型的 **Datasources（数据源）**选项卡，选择 **Phonograph** 窗格中的蓝色 **Reindex（重新索引）**按钮，手动启动重新索引。

| 名称 | 描述 | 是否添加原始索引？ | 是否需要重新索引？ |
| --- | --- | --- | --- |
| Disable formatting（禁用格式化） | - 如果属性值不应在 Object Views 中按照浏览器所在地的本地数字格式标准进行格式化，请**启用**。 |  |  |
| Identifier（标识符） | - **启用**以提升重新索引性能，并指定具有数字基础类型、无需格式化或作为数字处理的主键和外键。 - 例如，Object Views 不会将属性值格式化为数字，Object Explorer 也不会启用按这些键的范围筛选。 |  |  |
| Keywords（关键字） | - **启用**以在 Object Views 中显示属性时，将此属性突出显示在其专属分区中。 |  |  |
| Long text（长文本） | - 如果属性值包含大量文本，请**启用**。 - 例如，Object Views 将以更易读的格式显示此属性的值。 |  |  |
| Low cardinality（低基数） | - **启用**以向应用表明此属性的可能值不多。 - 例如，某些 Object View 组件仅允许对可能值不多的属性进行筛选。- Low cardinality 渲染提示**必须**与 Searchable 一起选中。 | 是 | 是 |
| Selectable（可选择） | - 在字符串属性上**启用**，以允许用户对此属性执行聚合。 - 例如，此属性将在 Object Explorer 直方图和 Object View 图表中被聚合。- 在数值和日期属性上**启用**，以允许用户对精确词项值（而不仅是分布）执行聚合。- 如果该属性不会在应用中被聚合，**禁用**可提升重新索引性能。- **启用**以使用精确匹配（Exact Match）筛选功能。- Selectable 渲染提示**必须**与 Searchable 一起选中。 | 是 | 是 |
| Sortable（可排序） | - 在字符串属性上**启用**，以允许用户对此属性排序。 - 数值和日期属性始终可排序。 - 例如，Object Views 中的时间线和图表将按此属性排序。- 如果该属性不会在应用中被排序，**禁用**可提升重新索引性能。- **不建议用于数组**，数组将基于数组中的最小值排序。- Sortable 渲染提示**必须**与 Searchable 一起选中。 | 是 | 是 |
| Searchable（可搜索） | - 如果该属性不会在应用中被搜索或排序，**禁用**可提升重新索引性能。 - 如果该属性包含大型字符串，性能提升将尤为显著。- 必须选中 Searchable，应用才会应用 Selectable、Sortable 或 Low cardinality 渲染提示。 | 是 | 是 |
| Enable leading wildcards（启用前导通配符） | - 在字符串属性上**启用**以支持前导通配符查询。- Enable leading wildcards 渲染提示**必须**与 Searchable 一起选中。 | 是 | 是 |
| Enable regex queries（启用正则查询） | - 在字符串属性上**启用**以支持正则表达式查询。- Enable regex queries 渲染提示**必须**与 Searchable 一起选中。 | 是 | 是 |

---

*原文：[Render hints](https://www.palantir.com/docs/foundry/object-link-types/metadata-render-hints/)*
