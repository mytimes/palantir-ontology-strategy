# 类型类（Type classes）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/metadata-typeclasses/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

类型类可以应用于属性、链接类型和动作类型。除了 `analyzer` 类型类种类会影响[索引构建](https://www.palantir.com/docs/foundry/object-indexing/overview/)行为之外，类型类定义的是附加元数据，可由与本体交互的用户应用解读。例如，某些 `hubble` 属性类型类的规格会影响属性值在 [Object Views](https://www.palantir.com/docs/foundry/object-views/overview/) 中的渲染方式。

下表列出了已知的类型类。该表的各列如下：

- **Deprecated（已弃用）**列表明某个类型类是否仍受支持。
  - **Deprecated** 列还表明该类型类现在是否应在 **Capabilities（能力）**页面中配置。
    - 在 Ontology Manager 中，对象类型现在有一个 **Capabilities** 页面，用于配置历史上定义为类型类的功能。所有受支持类型类的配置都将迁移到 **Capabilities** 页面。
- **Type（类型）**列表明该类型类是应用于属性、链接类型（以前称为 relation）还是动作类型。
- **Kind（种类）**和 **Name（名称）**列包含两个用户定义字段的字符串值，这些字段是在 Ontology Manager 中添加类型类时设置的。Foundry 产品使用这些值来标记类型类。

![添加类型类 - Kind 和 Name 字段](https://www.palantir.com/docs/resources/foundry/object-link-types/typeclasses-kind-name.png?width=300)

- **Description（描述）**列描述了当用户应用与已添加所列类型类的属性值、链接或动作交互时的预期行为。
  - [Object Explorer](https://www.palantir.com/docs/foundry/object-views/overview/) 是一个消费 `hubble` 类型类的应用。
  - [Quiver](https://www.palantir.com/docs/foundry/quiver/overview/) 是一个消费 `timeseries` 类型类的应用。

| 已弃用 | 类型 | 种类 | 名称 | 描述 |
| --- | --- | --- | --- | --- |
|  | 属性 | hubble | media_url | 当属性出现在 Object View 中时，将其渲染为媒体项。 |
| 已弃用 | 属性 | hubble | editable | 当属性出现在 Object View 中时，允许用户编辑该属性。请改用[内联编辑](https://www.palantir.com/docs/foundry/action-types/inline-edits/#object-explorer-inline-edits)。 |
|  | 属性 | hubble | icon | 表明作为属性值存储的 URL 包含某个对象的图标。 |
| 已弃用 | 链接类型 | hubble | creatable | 允许用户从 Object View 内的 Linked Objects View（关联对象视图）组件创建特定链接类型的新对象。该类型类放置在 relation（链接类型）上，允许用户创建一对多关系中“多”侧类型的对象。有关允许用户创建新链接的受支持方式，请参见[动作](https://www.palantir.com/docs/foundry/action-types/overview/)。 |
| 已弃用 | 属性 | hubble | endorsement_status: endorsed | 当添加到对象类型的主键属性时，在 Object Explorer 和 Object Views 中将对象标记为 `endorsed`（已认可）。 |
| 已弃用 | 属性 | hubble | endorsement_status: not_endorsed | 当添加到对象类型的主键属性时，在 Object Explorer 和 Object Views 中将对象标记为 `work in progress`（进行中）。 |
| 已弃用 | 属性 | hubble | thumbnail | 将作为属性值存储的 URL 用作搜索结果卡片中的缩略图。这仅在旧版本的 Object Explorer 中相关。 |
| 已弃用 | 属性 | hubble | array | 此类型类以前用于确保属性格式化为数组，但现在数组已是 Ontology Manager 中受妥善支持的属性基础类型。 |
| 已弃用 | 属性 | hubble | default_sort_descending | 在 Object Explorer 中自动按列值降序排序。这仅在旧版本的 Object Explorer 中相关。 |
| 已弃用 | 属性 | hubble | quick_filter | 在 Object Explorer 列表视图中，这些属性曾作为默认筛选器可用。这仅在旧版本的 Object Explorer 中相关。 |
|  | 链接类型 | hierarchy | parent | 表明链接类型中的链接方向代表层级结构中的父级。随后 Object Views 会在该层级结构的 Object View 顶部显示面包屑（例如，'Europe -> France -> Paris -> Rue Cler'）。 |
|  | 属性 | choropleth_map_config_id | <map_config_id> | 对于任何包含地理区域值（即国家/地区代码）、可绘制在地图上的属性类型，都可以创建分级统计图（choropleth）。所需类型类的 `kind` 为 `choropleth_map_config_id`，`name` 取决于该属性包含的区域代码类型。例如：- 对于国家，使用 `countries` - 美国各州 → `us_states` - 美国各县 → `us_counties` - 美国邮政编码 → `us_zip_codes` 有关更多区域边界选项，或有关添加此类型类的更多帮助，请联系你的 Palantir 代表。配置选项包括更改聚合类型以及所用的色阶。要使用此类型类，必须对该属性应用 `selectable` 或 `sortable` 渲染提示。 |
| 已弃用 | 属性 | oe_home_page_object_type_group | <your_object_type_group_name> | 将此类型类添加到对象类型的主键属性，可将该对象类型添加到某个组。请确保拼写正确，以避免组的重复。这些配置的对象类型组显示在 Object Explorer 的主页上。如果你配置了组，任何未添加到组的非隐藏对象类型都将放置在页面底部的“Other”（其他）组下。此功能已被对象类型组能力取代。 |
|  | 动作类型 | hubble-oe | hide-action | 在 Object Explorer 和 Object Views 中隐藏该动作类型；否则它们会在 `Actions` 按钮下拉菜单中被自动发现。 |
|  | 动作类型 | hubble-oe-object-set-rid | <object_type_RID> | 允许创建动态对象集的实验性功能。 |
|  | 动作类型 | hubble-oe-security-rid | <compass_RID> | 允许创建动态对象集的实验性功能。 |
|  | 动作类型 | actions | generate_uuid | 用 UUID 替换字符串参数。 |
|  | 动作类型 | actions | prefill_current_user | 用当前用户替换字符串参数。 |
|  | 动作类型 | actions | view_object_with_type | 在成功提示（toast）中显示已创建/修改的对象。 |
| 已弃用 | 属性 | analyzer | not_analyzed | 阻止 [Lucene ↗](https://lucene.apache.org/) 对该属性进行分词；用于包含连字符等内容的标识符属性。要配置分析器，请导航到 **Properties > Interaction（属性 > 交互）**，其中包含用于选择分析器的下拉菜单。 |
| 已弃用 | 属性 | analyzer | simple / standard / whitespace / english / french / japanese / arabic / korean / german / combined_arabic_english | 针对特定语言和用例的分析器。其中大多数使用 Lucene 内置分析器实现，少数是自定义插件。旧版 [Object Storage v1](https://www.palantir.com/docs/foundry/object-databases/object-storage-v1/) 不支持韩语和德语。要配置分析器，请导航到 **Properties > Interaction**，其中包含用于选择分析器的下拉菜单。 |
| 已弃用 | 属性 | analyzer | not_indexed | 阻止 Lucene 为该属性建立索引；用于不需要可搜索或可聚合的属性。此类型类不再用于管理属性是否应被编入索引，因为该功能现在由 `searchable` [渲染提示](https://www.palantir.com/docs/foundry/object-link-types/metadata-render-hints/)管理。 |
| 已弃用 | 属性 | multipass | user_id | 当应用于其值包含 Multipass UID 的属性时，该属性将在 Object Explorer 和 Object Views 中渲染为用户的用户名（由 multipass 和 multipass 属性组成）。此类型类不再用于确保属性格式化为用户名，Multipass UID 现在由 Ontology Manager 中的值格式化功能支持。 |
| 已弃用 | 属性 | global | <your_property_id> | 可用于将属性标记为全局属性。只要应按此属性筛选的每个对象类型上的属性具有相同的 property_id 和 global 类型类，就可以按一个共同属性筛选多个对象类型。这仅在旧版本的 Object Explorer 中相关。 |
| 已弃用 | 属性 | geo | geojson | 表明该属性包含 GeoJSON 数据（例如多边形、线等）。[Map 应用](https://www.palantir.com/docs/foundry/map/overview/)将在地图上渲染此 GeoJSON。已弃用：请改用 `geoshape` 属性类型。 |
| 已弃用 | 属性 | geo | latitude | 表明该属性包含供 [Map 应用](https://www.palantir.com/docs/foundry/map/overview/)使用的纬度。已弃用：请改用 `geopoint` 属性类型。 |
| 已弃用 | 属性 | geo | longitude | 表明该属性包含供 [Map 应用](https://www.palantir.com/docs/foundry/map/overview/)使用的经度。已弃用：请改用 `geopoint` 属性类型。 |
| 在对象类型的 **Capabilities** 页面中配置 | 属性 | geo | altitude | 表明该属性包含海拔/高程（以米为单位，相对于海平面），供 [Map 应用](https://www.palantir.com/docs/foundry/map/overview/)在 3D 模式下使用。 |
|  | 属性 | vertex | link_merge | 对于 Vertex 和 Vortex 的 Related Object Search Arounds（关联对象环绕搜索），始终将此对象视为中介——该对象将不再显示在 Related Object（关联对象）列表中，但来自它的二度链接会显示，并且它将在图上渲染为一条边。将类型类放置在对象的主键上。 |
|  | 链接类型 | vertex | link_merge_incoming | 与 `link_merge` 相同，但仅针对此特定 relation——被链接合并的对象是此 relation 的目标/to 侧。 |
|  | 链接类型 | vertex | link_merge_outgoing | 与 `link_merge` 相同，但仅针对此特定 relation——被链接合并的对象是此 relation 的源/from 侧。 |
|  | 链接类型 | vertex | component | 对于 Vertex 图表，表明与基础对象链接、将在图表中使用的对象。 |
|  | 属性 | vertex | component_subtype | 对于 Vertex，允许比对象类型更细粒度的分组。将类型类放置在对象的主键上。 |
|  | 属性 | vertex | event_intent.<intent_> | 在 Event（事件）的主键属性上设置此项，以在 Vertex 和 Vortex 中使用，其中 `intent` 表示事件/警报的颜色/严重程度（danger、warning、primary 或 success）。例如：`event_intent.danger` |
|  | 属性 | vertex | event_value | 表明表示事件数值的属性。 |
|  | 属性 | vertex | event_value_unit.<unit_> | 与 `event_value` 一起设置，其中 `unit` 是事件数值的度量单位。例如：`event_value_unit.Kilograms` |
|  | 属性 | vertex | event_property | 在 Vertex 和 Vortex 事件卡片中显示此属性。 |
|  | 属性 | vertex | min | 对于时间序列对象：当序列值低于此最小值时，Vertex 将发出警报。 |
|  | 属性 | vertex | max | 对于时间序列对象：当序列值超过此最大值时，Vertex 将发出警报。 |
|  | 属性 | vertex | threshold_measure.<measure_> | 对于 Vertex，在对象的主键属性上设置此项，以表明使用哪个度量进行阈值判定。例如：`threshold_measure.Temperature` |
|  | 属性 | vertex | threshold_high_limit | 与 `threshold_measure` 结合使用，以表明哪个属性表示阈值上限。 |
|  | 属性 | vertex | threshold_low_limit | 与 `threshold_measure` 结合使用，以表明哪个属性表示阈值下限。 |
|  | 属性 | vertex | threshold_exceed_intent.<intent_> | 与 `threshold_measure` 一起在对象的主键属性上设置，其中 `intent` 表示阈值越限的颜色/严重程度（danger、warning、primary 或 success）。例如：`threshold_exceed_intent.danger` |
|  | 属性 | vertex | key_measure.<measure_> | 此处列出的度量将显示在 Vertex 主页上。例如：`key_measure.Temperature` |
| 已弃用 | 属性 | vertex | enum_values | 对于时间序列对象：从数值到字符串值的 JSON 映射。此类型类不再受支持。 |
| 在对象类型的 **Capabilities** 页面中配置 | 属性 | timeseries | timeseries_id | 当应用于时间序列对象的主键时，此类型类指定该对象的*序列标识符*（`seriesId`）。该属性必须在所有时间序列对象中全局唯一，并且它是让你的对象能在 Quiver 中被发现所需的唯一类型类。 |
| 在对象类型的 **Capabilities** 页面中配置 | 属性 | timeseries | timeseries_measure | 指定时间序列对象的[度量](https://www.palantir.com/docs/foundry/quiver/timeseries-overview/)的属性。注意：`timeseries.timeseries_sensor_type` 类型类以前用于相同目的；它仍可继续使用，但为保持一致请使用 `timeseries.timeseries_measure`。 |
| 已弃用 | 属性 | timeseries | timeseries_sensor_type | 参见上面的 `timeseries_measure`。 |
| 在对象类型的 **Capabilities** 页面中配置 | 属性 | timeseries | timeseries_units | 指定时间序列对象的*值*单位的属性（例如，股价时间序列可能以 `dollars`（美元）作为值单位）。 |
| 在对象类型的 **Capabilities** 页面中配置 | 属性 | timeseries | timeseries_internal_interpolation | 指定时间序列对象默认*内部插值*的属性。内部插值是 Quiver 在相邻数据点之间推断序列值的方式。 |
| 在对象类型的 **Capabilities** 页面中配置 | 属性 | timeseries | timeseries_root_object_id | 指定时间序列对象的[根对象](https://www.palantir.com/docs/foundry/quiver/timeseries-overview/)的属性。每个时间序列对象只能有一个根对象。 |
| 在对象类型的 **Capabilities** 页面中配置 | 属性 | timeseries | timeseries_is_enum | 一个布尔属性，对于任何具有枚举值的时间序列必须为 `true`。注意：此要求是临时性的，将来可能更改。 |
| 在对象类型的 **Capabilities** 页面中配置 | 属性 | timeseries | timeseries_is_deprecated | 一个布尔属性，当为某个时间序列设置为 `true` 时，会将其从 Object Explorer 和 Object View 搜索结果中筛选掉。注意：这只会在 Quiver 中事后将这些时间序列从结果中过滤掉。它不会影响其他应用中的搜索结果。 |
|  | 链接类型 | timeseries | parent | 描述时间序列对象与每个父对象之间的链接，类似于 `hierarchy.parent`。 |
|  | 属性 | timeseries | timeseries_is_value_inverted | 设置为 true 时，此布尔属性会自动反转 Quiver 中时间序列的 y 轴值，使值向下递增。这对于绘制时间-深度序列非常有用，例如随时间跟踪地下钻井作业的进度。 |
|  | 属性 | timeseries | timeseries_depth_units | 放置在包含复杂序列（深度序列、完井序列和光纤序列）深度单位的属性上。常规的 `timeseries.timeseries_units` 属性用于深度序列和完井序列的值单位。 |
| 在对象类型的 **Capabilities** 页面中配置 | 属性 | timeseries | event_id | 指定事件对象的*事件标识符*（`eventId`）的属性。应在所有事件对象中全局唯一。 |
| 在对象类型的 **Capabilities** 页面中配置 | 属性 | timeseries | event_start_time | 指定事件对象开始时间的属性。此字段应为时间值（例如 `TIMESTAMP`）。 |
| 在对象类型的 **Capabilities** 页面中配置 | 属性 | timeseries | event_end_time | 指定事件对象结束时间的属性。此字段应为时间值（例如 `TIMESTAMP`）。 |
| 在对象类型的 **Capabilities** 页面中配置 | 属性 | timeseries | event_description | 指定事件对象字符串描述的属性。如果事件对象类型将用于[标注回写](https://www.palantir.com/docs/foundry/quiver/timeseries-overview/)，则此属性为必需。 |
| 在对象类型的 **Capabilities** 页面中配置 | 属性 | timeseries | event_root_object_id | 指定事件对象的[根对象](https://www.palantir.com/docs/foundry/quiver/timeseries-overview/)的属性。每个事件对象只能有一个根对象。 |
| 在对象类型的 **Capabilities** 页面中配置 | 属性 | timeseries | event_linked_series_id | 指定事件对象所关联的[序列对象](https://www.palantir.com/docs/foundry/quiver/timeseries-overview/)的属性。此字段支持字符串数组和单个字符串。 |
|  | 属性 | schedules | schedulable-start-time | 在动态调度组件中将时间戳属性标记为调度对象的开始时间。[Scheduling Calendar 组件](https://www.palantir.com/docs/foundry/dynamic-scheduling/scheduling-calendar-widget/)和甘特图组件的动态模式需要此项。保存处理程序动作中的动作参数 ID 必须与属性 ID 匹配。 |
|  | 属性 | schedules | schedulable-end-time | 在动态调度组件中将时间戳属性标记为调度对象的结束时间。[Scheduling Calendar 组件](https://www.palantir.com/docs/foundry/dynamic-scheduling/scheduling-calendar-widget/)和甘特图组件的动态模式需要此项。保存处理程序动作中的动作参数 ID 必须与属性 ID 匹配。 |
|  | 属性 | schedules | segment-by | 当应用于某个属性时，在调度组件中启用分段，允许使用条件格式根据属性值对 puck 进行颜色编码。 |
|  | 动作类型 | schedules | schedulable-start-time | 在动态调度组件中将动作参数标记为保存处理程序动作的开始时间参数。必须应用于与开始时间属性 ID 匹配的同一参数。 |
|  | 动作类型 | schedules | schedulable-end-time | 在动态调度组件中将动作参数标记为保存处理程序动作的结束时间参数。必须应用于与结束时间属性 ID 匹配的同一参数。 |

---

*原文：[Type classes](https://www.palantir.com/docs/foundry/object-link-types/metadata-typeclasses/)*
