# API：对象集（API: Object sets）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/api-object-sets/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

**对象集**（object set）表示单一类型对象的无序集合。你可以使用函数 API 筛选对象集、基于已定义的链接类型对其他对象类型执行环绕搜索（Search Around），以及计算聚合值或检索具体对象。除了将单个对象作为输入传入函数外，你还可以随时使用对象搜索 API 搜索对象集。

> **ℹ️ 提示**
>
> 筛选、排序和聚合仅对在 Ontology 应用中启用了 `Searchable` 渲染提示的属性生效。这些属性已为搜索编入索引。[了解如何启用 `Searchable` 渲染提示。](https://www.palantir.com/docs/foundry/object-link-types/metadata-render-hints/)

> **ℹ️ 提示**
>
> 作为函数输入，对象集比对象数组更高效，因为它们会将加载推迟到需要时才进行。有关高效使用对象集的最佳实践，请参阅[优化性能](https://www.palantir.com/docs/foundry/functions/optimize-performance/)。

## 对象搜索

`Objects.search()` 接口允许你对已导入项目的任意对象类型发起搜索。在下面的示例中，函数使用给定的 `airportCode` 查找所有从该机场出发的航班，然后找到这些航班的所有不同目的地并返回。

```typescript
import { Function } from "@foundry/functions-api";
import { Objects } from "@foundry/ontology-api";

export class FlightFunctions {
    @Function()
    public currentFlightDestinations(airportCode: string): Set<string> {
        const flightsFromAirport = Objects.search()
            .flights()
            .filter(flight => flight.departureAirportCode.exactMatch(airportCode))
            .all();

        const destinations = flightsFromAirport.map(flight => flight.arrivalAirportCode!);

        return new Set(destinations);
    }
}
```

对象集也可以通过对象列表、对象资源标识符（RID）列表或对象集资源标识符来创建，只需将它们作为参数传给被搜索的对象类型即可。例如：`Objects.search().flights([flight])`。

一旦你拥有了给定类型的对象集，就可以对该集合执行下文所述的各种操作。

## 筛选

对象集上的 `.filter()` 方法允许你基于对象的可搜索属性筛选对象集。该筛选方法接受一个筛选定义，筛选定义取决于你所筛选属性的类型。

- 所有属性类型都支持 `.exactMatch()` 筛选器，它会筛选出在该属性值上精确匹配的对象。这适用于对字符串进行精确匹配筛选（如上面的示例），或基于对象的主键进行筛选（例如 `.filter(object => object.primaryKey.exactMatch(PrimaryKey))`）。
  - 要检查某个属性是 `null` 还是 `undefined`，请使用 `hasProperty()` 方法。
  - 要传入多个值，请使用展开运算符 `.exactMatch(...listVariable)`。如果传入空数组，该筛选条件将被忽略。
- 字符串属性支持多种关键词筛选器。完整详情请参阅 Code Assist 中每个方法的文档。
  - `.phrase()` 将搜索查询拆分为词元（通常是单个单词），然后根据值是否按顺序包含所有给定词元且词元之间没有其他词元来筛选值。请注意，以下划线或句点分隔的字符串值会被视为一个词元。例如，搜索 “banana” 时，属性值为 “banana_pudding” 或 “banana.pudding” 的对象不会被返回。
  - `.phrasePrefix()` 与 `phrase()` 几乎相同，但最后一个词元还会匹配以该词元开头的词元。例如，用 `.phrasePrefix()` 搜索 `fresh banana` 会匹配属性值 `fresh banana_pudding`，但不会匹配属性值 `banana_pudding fresh`。用 `.phrasePrefix()` 搜索 `pudding` 不会匹配属性值 `banana_pudding`。
  - `.prefixOnLastToken()` 将搜索查询拆分为词元，然后根据值是否包含所有给定词元来筛选值，其中最后一个词元还会匹配以该词元开头的词元。例如，`big app` 会匹配 `big apples`，也会匹配 `apples from the big tree`，但不会匹配 `apples from the biggest tree`。
  - `.matchAnyToken()`、`.fuzzyMatchAnyToken()` 将搜索查询拆分为词元，然后根据值是否包含任意给定词元来筛选值。`fuzzy` 版本允许近似值匹配。
  - `.matchAllTokens()`、`.fuzzyMatchAllTokens()` 将搜索查询拆分为词元，然后根据值是否包含所有给定词元来筛选值。`fuzzy` 版本允许近似值匹配。
    - 模糊筛选器可以接受一个可选的 `Fuzziness` 参数，该参数从 `@foundry/functions-api` 导入。
    - 可用 `Fuzziness` 选项的说明可在 [ElasticSearch 文档 ↗](https://www.elastic.co/guide/en/elasticsearch/reference/current/common-options.html#fuzziness) 中找到。下文也提供了更多信息。
- 数字、日期和时间戳属性支持 `.range()` 筛选器。
  - 范围筛选器提供一组 `.lt()`、`.lte()`、`.gt()` 和 `.gte()` 方法，分别用于执行小于 / 小于等于 / 大于 / 大于等于比较。
- 布尔属性支持 `.isTrue()` 和 `.isFalse()` 筛选器。
- 地理点（geopoint）属性支持 `.withinDistanceOf()`、`.withinPolygon()` 和 `.withinBoundingBox()` 筛选器。
- 地理形状（GeoShape）属性支持 `.withinBoundingBox()`、`.intersectsBoundingBox()`、`.doesNotIntersectBoundingBox()`、`.withinPolygon()`、`.intersectsPolygon()` 和 `doesNotIntersectPolygon()` 筛选器。
- 链接筛选器可用于通过 `.isPresent()` 方法筛选拥有或不拥有某一特定类型链接对象的对象。
- 数组属性支持 `.contains()` 筛选器，它会筛选出数组属性值包含*任意*给定值的对象。

### 组合筛选条件

你可以使用从 `@foundry/functions-api` 导出的 `Filters` API 将筛选条件组合在一起。可用的方法有：

- `and()` 将对象集筛选为通过所有给定筛选条件的对象
- `or()` 将对象集筛选为通过任意给定筛选条件的对象
- `not()` 对给定筛选条件取反

在下面的示例中，我们可以使用 `and()` 按航班目的地筛选航班对象集：

```typescript
import { Filters } from "@foundry/functions-api";


Objects.search()
    .flights()
    .filter(flight => Filters.or(
        Filters.and(flight.destination.exactMatch("SFO"), flight.passengerCount.gt(100)),
        Filters.and(flight.destination.exactMatch("LAX"), flight.passengerCount.gt(300)),
    ))
```

上面的代码会筛选出要么搭载超过 100 名乘客抵达 SFO、要么搭载超过 300 名乘客抵达 LAX 的航班。

> **⚠️ 注意：警告**
>
> 对象集上的 `.filter()` 方法不使用 `&&` 或 `||` 运算符。要应用多个筛选条件，你必须使用上面列出的 `Filters` 上的某个方法（或多次调用 `.filter()` 来实现 `and` 条件）。

### 使用模糊搜索筛选字符串属性

指定可选的 `fuzziness` 参数可以对模糊匹配行为进行更精细的控制。如果你不指定 fuzziness，则会根据你搜索词元的长度自动允许一个编辑距离。要指定编辑距离，你需要从 `@foundry/functions-api` 导入 `Fuzziness`。

#### 模糊匹配任意词元

```
Objects.search().employee().filter(employee => employee.firstName.fuzzyMatchAnyToken("Michael", { fuzziness: Fuzziness.LEVENSHTEIN_TWO })).all();
```

上面的代码会返回名字与所提供搜索词相差两次编辑以内（Levenshtein 距离为 2）的所有员工。在本例中，这包括 `Michael`、`Micheal`、`Mikhael`、`Michel`、`Mikhail`、`Mihail`（但例如不包括 `Miguel`）。如果你对搜索词的准确性更有把握，可以使用更小的编辑距离（使用不同的 Levenshtein 距离）进行搜索，从而进一步细化搜索结果。

#### 模糊匹配所有词元

```
Objects.search().employee().filter(employee => employee.fullName.fuzzyMatchAllTokens("Michael Smith", { fuzziness: Fuzziness.LEVENSHTEIN_ONE })).all();
```

你也可以对多词元短语使用模糊筛选器。上面的代码会匹配全名中**同时**包含 `Michael` 和 `Smith`、且每个词元最多相差一次编辑的员工——例如 `Mikhael Smitt`（即每个词元的 Levenshtein 距离均为 1）。使用 `fuzzyMatchAllTokens` 或 `fuzzyMatchesAllTokens` 筛选器时，词元的顺序不予考虑。

#### 对字符串数组属性进行模糊匹配

基于数组的属性上的所有筛选器都可以使用其底层类型可用的方法。例如，字符串数组属性可以基于字符串属性可用的任意方法进行筛选，不过方法的命名可能略有不同。对数组属性进行筛选时，只要数组元素中有一个匹配，该对象就会被返回。

## 环绕搜索（Search Around）

> **ℹ️ 提示：环绕搜索限制**
>
> 加载到内存中的对象集（`.all()` 或 `.allAsync()`）**最多允许进行 3 次环绕搜索**。如果使用超过 3 次环绕搜索，将抛出错误。在对象存储 v2（Object Storage v2）中从对象集 A 执行环绕搜索到对象集 B 时，结果对象集 B 不能超过 1000 万个对象实例，否则将抛出错误。对于对象存储 v1（Object Storage v1），限制为 10 万个对象实例。

基于你的对象集的对象类型，系统会生成 *环绕搜索* 方法，用于遍历[链接](https://www.palantir.com/docs/foundry/object-link-types/link-types-overview/)。在下面的示例中，我们根据出发代码筛选出航班对象集，然后环绕搜索到这些航班上的乘客。结果是一个乘客对象集，可以对其进一步筛选或继续环绕搜索。

```typescript
const passengersDepartingFromAirport = Objects.search()
    .flights()
    .filter(flight => flight.departureAirportCode.exactMatch(airportCode))
    .searchAroundPassengers();
```

环绕搜索方法只会为已导入项目的链接类型生成。有关如何导入链接类型的详情，请参阅[教程](https://www.palantir.com/docs/foundry/functions/foo-getting-started/#import-ontology-types)。

请注意，出于性能原因，你在单次搜索中可以执行的环绕搜索操作数量目前限制为 3 次。如果你尝试运行环绕搜索深度超过三层的搜索，该搜索将在运行时失败。

## K 近邻（KNN）

> **ℹ️ 提示：KNN 限制**
>
> KNN 仅在已编入 [OSv2](https://www.palantir.com/docs/foundry/object-backend/overview/) 索引的对象类型上受支持。k 值限制在 0 < K <= 100 范围内。此外，搜索向量的大小必须与索引时使用的向量相同，且维度限制为 2048。如果超出上述任一限制，将抛出错误。

带有嵌入属性的对象类型可用于 KNN 搜索。这类搜索会返回嵌入属性与所提供嵌入参数最接近的 k 个对象。下面的示例返回与所提供电影剧本最相似的电影。嵌入可以在 [Pipeline Builder](https://www.palantir.com/docs/foundry/pipeline-builder/pipeline-builder-aip/#text-to-embeddings) 等转换工具中生成；也可以在函数查询时[使用 Palantir 提供的嵌入模型](language-models.md#embeddings)或[在函数中使用你自己的模型](https://www.palantir.com/docs/foundry/functions/functions-on-models/)生成。

请确保你的函数代码仓库的 `functions.json` 配置文件中 `enableVectorProperties` 条目设置为 `true`。

```typescript
import { Objects } from "@foundry/ontology-api";

const kValue: number = 2;
// Vector can be generated from FML Live or come from an existing object
const vector: Double[] = [0.7, 0.1, 0.3];
const movies: Movies[] = Objects.search()
        .movies()
        .nearestNeighbors(obj => obj.vectorProperty.near(vector, { kValue }))
        .orderByRelevance()
        .take(kValue);
```

有关完整语义搜索工作流的示例，请参阅[语义搜索工作流指南](https://www.palantir.com/docs/foundry/ontology/using-palantir-provided-models-to-create-a-semantic-search-workflow/)。

## 集合操作

同一对象类型的对象集可以通过集合操作以多种方式组合：

- `.union()` 创建一个新对象集，由存在于任意给定对象集中的对象组成。
- `.intersect()` 创建一个新对象集，由存在于所有给定对象集中的对象组成。
- `.subtract()` 移除存在于给定对象集中的所有对象。

## 检索所有对象

`.all()` 和 `.allAsync()` 方法会检索对象集中的所有对象。请注意，如果你尝试一次加载过多对象，函数将执行失败。目前，你最多可以加载 100,000 个对象。但是，加载超过 10,000 个对象也可能导致函数执行超时。[详细了解函数中的时间和空间限制。](https://www.palantir.com/docs/foundry/functions/manage-functions/#enforced-limits)

你可以使用 `.allAsync()` 方法获取一个 Promise，该 Promise 解析为对象集中的所有对象。这对于并行加载多个对象集的数据非常有用。

## 排序与限制

你可以不检索所有对象，而是通过对对象集应用排序子句、然后指定要加载的具体对象数量来加载有限数量的对象。为此，你可以使用以下方法：

- `.orderBy()` 指定一个可搜索属性作为排序依据，并允许你指定排序方向。只有类型可排序的属性（数字、日期和字符串）才能在此方法中选择。你可以多次调用 `.orderBy()` 以按多个属性排序。
- `.orderByRelevance()` 指定按对象与所提供筛选条件的匹配程度排序返回，最相关的排在最前面。查询词与给定对象上某个属性值的相关性是一项复杂的判定，需要考虑该词在属性值中出现的频率、该词在所有对象中出现的频率等因素。当仅执行 `.exactMatch()` 筛选或对非字符串属性进行筛选时，相关性排序不太适用。请注意，在单次搜索中，`.orderBy()` 和 `.orderByRelevance()` 只能使用其中一个。
- `.take()` 和 `.takeAsync()` 使你能够从集合中检索指定数量的对象。这些方法只有在你指定了排序之后才可用。

例如，下面的代码会检索入职日期最早的十名员工：

```typescript
Objects.search()
    .employees()
    .orderBy(e => e.startDate.asc())
    .take(10)
```

再举一个例子，假设有一个对象类型 `claims`，其中包含某保险公司事故索赔的文本。假设你想查找一起涉及一辆红色汽车和一只鹿的特定索赔。如果没有 `.orderByRelevance()` 这一行，前 10 条结果中可能返回包含 `red`、`car`、`collision`、`with` 或 `deer` 中任意单词的结果。有了 `.orderByRelevance()` 这一行，前 10 条结果将是包含最多搜索词的索赔，从而使最相关的索赔排在最前面。

```typescript
const results = Objects.search()
    .claims()
    .filter(doc => doc.text.matchAnyToken("red car collision with deer"))
    .orderByRelevance()
    .take(10)
```

## 计算聚合

> **ℹ️ 提示：聚合限制**
>
> 从 Objects API 返回的聚合**总共限制为 10,000 个桶**。如果超出此限制，将抛出错误。
>
> 使用 `.topValues()` 分桶时，如果数据有超过 1,000 个不同值，结果将是近似的。在这种情况下，最高频值列表可能不准确。

### 按属性对对象分组

在许多情况下，没有必要加载对象集中的所有对象。相反，你可以直接加载分桶的聚合值来进行进一步分析。

要开始计算聚合，请在对象集上调用 `.groupBy()` 方法。这允许你指定对对象集中对象类型的某个可搜索属性进行分桶。例如，下面的代码按入职日期对员工分组：

```typescript
Objects.search()
    .employees()
    .groupBy(e => e.startDate.byDays())
```

在指定按哪个属性分桶时，你需要根据属性类型提供关于如何分桶的附加信息：

- 对于 `boolean` 属性，唯一的选项是 `.topValues()`。它返回两个桶，一个对应 `true`，一个对应 `false`。
- 对于字符串属性，有两个选项：
  - `.topValues()`：适用于需要快速响应且基数较小的属性。它按该字符串属性的前 1,000 个最高频值分桶。此限制是为了确保返回的聚合不会过大。
  - `.exactValues()`：用于更精确的聚合，并可为高基数属性考虑多达 10,000 个桶。考虑的桶数量可以通过 `.exactValues({"maxBuckets": numBuckets})` 指定，其中 `numBuckets` 必须是 0 到 10,000 之间的整数。此方法的响应时间可能更长，因为需要考虑更多结果。
- 对于数值属性（例如 `Integer`、`Long`、`Float`、`Double`），两个分桶选项是：
  - `.byRanges()` 允许你指定要使用的确切范围。例如，你可以使用 `.byRanges({ min: 0, max: 50 }, { min: 50, max: 100 })` 将对象分入你在此处指定的 [0, 50] 和 [50, 100] 两个范围。范围的 `min` 是包含的，`max` 是不包含的。你可以省略 `min` 或 `max`，分别表示包含从 -∞ 到 `max` 或从 `min` 到 ∞ 的值的桶。
  - `.byFixedWidth()` 指定每个桶的宽度。例如，你可以使用 `.byFixedWidth(50)` 将对象分入宽度均为 50 的范围。
- 对于 `LocalDate` 属性，提供了多种便捷的分桶方法：
  - `.byYear()`
  - `.byQuarter()`
  - `.byMonth()`
  - `.byWeek()`
  - `.byDays()` 按天分桶。你可以传入天数作为桶宽度。
- 对于 `Timestamp` 属性，适用与 `LocalDate` 相同的分桶选项，此外还有以下新增方法：
  - `.byHours()` 按小时分桶。你可以传入小时数作为桶宽度。
  - `.byMinutes()` 按分钟分桶。你可以传入分钟数作为桶宽度。
  - `.bySeconds()` 按秒分桶。你可以传入秒数作为桶宽度。
- 对于 `Array` 属性，分桶选项由数组中元素的类型决定。具体而言，`Array<PropertyType>` 可以获得与 `PropertyType` 相同的分桶方法（例如，`Array<boolean>` 获得与 `boolean` 相同的分桶方法）。
  - 例如，如果你有一个名为 `employeeSet` 的对象集，由 Alice 和 Bob 组成，他们的 `Array<string>` 属性 `pastCountries` 分别保存 `["US", "UK"]` 和 `["US"]`，那么 `employeeSet.groupBy(e => e.pastCountries.exactValues()).count()` 将返回 `{ "US": 2, "UK": 1 }`。

在按一个属性分组后，你可以选择调用 `.segmentBy()` 方法进行进一步分桶。这允许你计算按两个可搜索属性分桶的三维聚合。例如，你可以按如下方式同时按入职日期和角色对员工分组：

```typescript
Objects.search()
    .employees()
    .groupBy(e => e.startDate.byDays())
    .segmentBy(e => e.role.topValues())
```

### 选择聚合指标

在对对象集分组后，你可以调用各种聚合方法来计算每个桶上的聚合指标。需要属性的方法只接受标记为可搜索的属性。可用的聚合方法有：

- `.count()` 仅返回每个桶中的对象数量
- `.average()` 返回给定数值、时间戳、日期属性的平均值
- `.max()` 返回给定数值、时间戳、日期属性的最大值
- `.min()` 返回给定数值、时间戳、日期属性的最小值
- `.sum()` 返回给定数值属性的值之和
- `.cardinality()` 返回给定属性的近似不同值数量

调用这些方法之一会返回 `TwoDimensionalAggregation` 或 `ThreeDimensionalAggregation`。如果你在调用最终聚合方法之前调用了 `.segmentBy()`，则返回 `ThreeDimensionalAggregation`。

[详细了解这些聚合类型的结构，包括**有效的分桶类型**。](https://www.palantir.com/docs/foundry/functions/types-reference/#aggregation-types)

请注意，结果聚合被包装在 `Promise` 中，因为计算聚合需要从远程服务加载数据。你可以使用 [async/await ↗](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function) 语法来解包 `Promise` 结果。

下面是一个加载聚合并将其作为结果返回的完整示例。

```typescript
import { Function, ThreeDimensionalAggregation } from "@foundry/functions-api";
import { Objects } from "@foundry/ontology-api";

export class AggregationFunctions {
    @Function()
    public async employeesByRoleAndOffice(): Promise<ThreeDimensionalAggregation<string, string>> {
        return Objects.search()
            .employee()
            .groupBy(e => e.title.topValues())
            .segmentBy(e => e.office.topValues())
            .count();
    }
}
```

下面是一个不使用 groupBy 语句进行聚合的完整示例：

```typescript
import { Function } from "@foundry/functions-api";
import { Objects } from "@foundry/ontology-api";

export class AggregationFunctions {
    @Function()
    public async employeesStats(): Promise<Double> {
        // Count of all employees, default to zero if count() returns undefined
        return Objects.search().employee().count() ?? 0;
    }
}
```

你也可以通过替换上面代码示例中的相应行，在不使用 groupBy 的情况下执行其他聚合，例如：

- 所有员工的数量：`Objects.search().employee().count();`（如上面的示例所示）
- 员工的平均任期：`Objects.search().employee().average(e => e.tenure);`
- 员工的最大任期：`Objects.search().employee().max(e => e.tenure);`
- 员工的最小任期：`Objects.search().employee().min(e => e.tenure);`
- 所有员工薪资之和：`Objects.search().employee().sum(e => e.salary);`
- 办公室数量：`Objects.search().employee().cardinality(e => e.office);`

有关在内存中操作聚合结果的示例，请参阅[创建自定义聚合](https://www.palantir.com/docs/foundry/functions/create-custom-aggregation/)指南。

---

*原文：[API: Object sets](https://www.palantir.com/docs/foundry/functions/api-object-sets/)*
