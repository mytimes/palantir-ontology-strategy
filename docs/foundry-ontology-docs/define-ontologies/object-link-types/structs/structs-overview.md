# 结构体（Structs）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/structs-overview/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

**结构体（struct）** 是本体属性的[基础类型](https://www.palantir.com/docs/foundry/object-link-types/base-types/)，它允许单个属性容纳多个字段而不是单个值。你在定义属性时声明这些字段，每个字段都有自己的名称和类型。你可以用这种方式为许多常见的对象属性建模。例如，`Full Name`（全名）属性可以容纳 `firstName` 和 `lastName` 字段，`Address`（地址）属性可以容纳 `street`、`city`、`postalCode` 和 `country`。

结构体属性由单个数据源列支撑，该列的类型本身即为结构体。在 Ontology Manager 中，你在[创建结构体属性](https://www.palantir.com/docs/foundry/object-link-types/create-struct-type/)时将该列选为属性的**支撑列（Backing column）**。字段值最初可以来自不同的数据源，只要你在本体中定义该属性之前将它们合并到单个结构体类型列中即可。

## 结构体配置

结构体属性具有以下约束：

- 结构体属性不能包含另一个结构体。
- 结构体字段不能是数组，但结构体属性本身可以容纳结构体数组。
- 结构体属性必须至少有一个字段。
- 结构体属性字段目前仅支持以下类型：
  - `BOOLEAN`
  - `BYTE`
  - `DATE`
  - `DECIMAL`
  - `DOUBLE`
  - `FLOAT`
  - `GEOPOINT`
  - `INTEGER`
  - `LONG`
  - `SHORT`
  - `STRING`
  - `TIMESTAMP`

## 查询语义

结构体的索引构建方式类似于 [ElasticSearch 对象字段类型 ↗](https://www.elastic.co/docs/reference/elasticsearch/mapping-reference/object)，这意味着查询会独立评估每个字段条件。当结构体属性容纳数组时，即使该数组中没有单个结构体满足所有条件，查询也可能匹配某个对象。

例如，考虑一个容纳值数组的 `Full Name` 属性。如果某个对象存储了 `[{"firstName": "Ada", "lastName": "Chen"}, {"firstName": "Blake", "lastName": "Moreau"}]`，那么查询 `"firstName": "Ada" AND "lastName": "Moreau"` 会匹配该对象。第一个值满足 `firstName` 条件，第二个值满足 `lastName` 条件，因此两个条件都得到满足，尽管没有哪个值单独同时满足两者。

## 当前支持程度

对结构体属性的支持仍在扩展中，因此在 Palantir 平台上的可用性各不相同。目前以下应用和服务支持结构体：

- **[Ontology Manager](https://www.palantir.com/docs/foundry/ontology-manager/overview/)：** 定义和编辑结构体。
- **[Actions（动作）](https://www.palantir.com/docs/foundry/action-types/overview/)：** [创建和修改结构体属性值](https://www.palantir.com/docs/foundry/action-types/actions-on-structs/)。
- **[Pipeline Builder](https://www.palantir.com/docs/foundry/pipeline-builder/overview/)：** 定义和编辑结构体。
- **[Workshop](https://www.palantir.com/docs/foundry/workshop/overview/)：** 显示结构体属性并将其作为变量使用。
- **[Marketplace](https://www.palantir.com/docs/foundry/marketplace/overview/)：** 打包和安装结构体属性。
- **[Object Explorer](https://www.palantir.com/docs/foundry/object-explorer/search-objects/)：** 按结构体属性值搜索对象。结构体字段搜索正在开发中。
- **[Ontology SDK](https://www.palantir.com/docs/foundry/ontology-sdk/overview/)：** 加载结构体属性并按结构体属性值搜索对象。并非每个 Ontology SDK 都支持结构体属性，请参阅[不支持的属性类型](https://www.palantir.com/docs/foundry/ontology-sdk/unsupported-types/#object-types-unsupported-property-types)文档。
- **[Functions（函数）](https://www.palantir.com/docs/foundry/functions/overview/)：** TypeScript v2 和 Python 函数支持结构体参数和结构体属性编辑。

对象存储 v1（Phonograph）将不支持结构体。你目前只能从数据集和受限视图创建结构体属性。

---

*原文：[Structs](https://www.palantir.com/docs/foundry/object-link-types/structs-overview/)*
