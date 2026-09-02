# 计算用量：本体索引构建（Compute usage: Ontology indexing）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/ontologies/compute-usage/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

Foundry 的本体将对象存储在本体索引（Ontology index）中，这是一种为快速访问而优化的存储格式。Foundry 数据集中的数据可以是任意大小或格式，这意味着需要进行数据转换，才能将数据集数据准备好并存入本体索引。这个过程称为*本体索引构建（Ontology indexing）*，可应用于任意大小的数据集和对象。本体索引构建的处理成本以计算秒（compute-seconds）来度量。本文档说明本体索引构建如何消耗计算量，以及如何管理计算用量。

## 度量本体索引构建的计算用量

本体索引构建使用并行化的 Spark 后端来读取任意大规模的数据，并将其转换为本体格式。运行一个索引构建作业所消耗的计算量，取决于所使用的计算资源（driver 和 executor）数量以及索引构建作业本身的总墙钟时长。

有关 Spark 用量如何换算为计算秒的更多信息，请参阅主要的[用量类型](https://www.palantir.com/docs/foundry/resource-management/usage-types/)文档。在下文中，你可以找到[本体索引构建所用计算秒的计算示例](#索引构建计算示例)。

## 调查本体索引构建产生的用量

本体索引构建作业会显示在 Foundry 的 Builds 应用中，并关联到正在被编入索引的对象。本体索引构建作业属于 Spark 作业，因而被归类为并行化批处理计算，可以用与同一后端上其他作业（例如 Code Repositories 转换和 Contour 查询）相同的方式来度量。

索引构建作业可以根据其触发方式进行分类。

- *本体索引构建作业（Ontology indexing jobs）*：将数据集索引构建到本体后端。这部分计算用于从数据集生成已编入索引的对象。
- *本体导出作业（Ontology export jobs）*：将直接在本体中所做的编辑持久化到 Foundry 转换框架中的数据集。这类作业通常比完整索引构建作业要小，因为本体导出作业处理的通常是编辑，而编辑是总对象集的严格子集。

## 本体索引构建用量的驱动因素

本体索引构建作业必须读取所有需要编入索引的数据，并将其转换成本体后端能够快速存储、搜索和编辑的格式。

读取和索引构建数据时的计算用量由以下因素驱动：

- **每个对象的记录数**
  - 随着被编入索引的数据集中记录数的增加，对象数量也会增加。每个对象在编入索引时都需要一定数量的计算操作，因此对象数量增加会提高索引构建所消耗的计算量。
- **每个对象的属性数**
  - 每个对象的每个属性都必须由索引构建作业单独分析，然后写入对象索引。需要分析并编入索引的属性越多，消耗的计算量就越大。
- **每个属性的大小**
  - 有些属性比其他属性大得多。例如，包含大量内容的文本属性比简单的数值属性需要更多的空间和计算来分析。具有更大、更复杂属性类型的对象，编入索引时需要更多计算。

索引构建频率对本体更新消耗多少计算也有很大影响。在上游数据集上设置的调度会触发对象的自动重新索引。在考察保持对象最新所带来的用量影响时，请考虑该对象及其上游数据集上的更新调度。

## 管理本体索引构建计算

可以对本体索引构建作业进行优化以降低计算用量。第一种也是最简单的优化方法，是减小索引的输入数据规模，从而减少完成作业所需的工作量。这包括在可能的情况下做到以下几点：

- 管理输入记录的数量
- 管理每个对象的属性数量
- 管理每个对象中各属性的大小

另一种优化方法是将本体索引构建作业配置为使用变更日志（changelog）策略进行索引构建。变更日志索引构建会在执行前将作业与现有对象进行比较，从而显著减少每个索引构建作业需要创建或更新的对象数量。变更日志索引构建需要更多配置，且必须遵循更新策略，但可以带来数量级的性能和效率提升。

## 索引构建计算示例

索引构建作业采用并行化 Spark 作业的形式，可以在 Builds 应用中查看。下面是一个索引构建作业的示例。请注意，本体索引构建作业会根据作业规模自动为作业选择 driver 和 executor 的大小。

```
Driver:
    num_vcpu: 1
    GiB_RAM: 6
Executors:
    num_vcpu: 1
    GiB_RAM: 4
    num_executors: 2
Total Runtime: 10 seconds

Calculation: 

driver_compute_seconds = max(num_vcpu, GiB_RAM / 7.5) * runtime_in_seconds
                       = max(1vcpu, 6GiB / 7.5) * 10sec
                       = 1 * 10 = 10 compute-seconds

executor_compute_seconds = max(num_vcpu, GiB_RAM / 7.5) * num_executors * runtime_in_seconds
                         = max(1vcpu, 4GiB / 7.5) * 2executors * 10sec
                         = 1 * 2 * 10 = 20 compute-seconds

total_compute_seconds = driver_commpute_seconds + exeucutor_compute_seconds
                      = 10 compute-seconds + 20 compute-seconds
                      = 30 compute-seconds
```

---

*原文：[Compute usage: Ontology indexing](https://www.palantir.com/docs/foundry/ontologies/compute-usage/)*
