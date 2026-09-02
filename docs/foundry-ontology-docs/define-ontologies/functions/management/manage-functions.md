# 管理已发布的函数（Manage published functions）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/manage-functions/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

所有类型的函数发布后，都可以使用 **Ontology Manager** 查看和管理。

## 搜索函数

要搜索函数，请导航到 **Ontology Manager** 并选择 **Functions** 选项卡。
你可以按函数的大多数元数据搜索函数，包括但不限于函数名称、描述、API 名称和 RID。

![在 Ontology Manager 中搜索函数](https://www.palantir.com/docs/resources/foundry/functions/functions-search.png)

## 函数概览页面

在 Ontology Manager 中，选择一个函数即可查看其输入、输出、使用历史以及[函数指标](https://www.palantir.com/docs/foundry/functions/function-metrics/)，包括成功和失败次数以及 P95 执行时长。

![在 Ontology Manager 中查看函数概览](https://www.palantir.com/docs/resources/foundry/functions/function-overview.png)

## 函数配置页面

某些类型的函数允许你配置超时或内存限制等资源。
如果你的函数支持任何配置选项，你可以在 **Configuration** 选项卡中查看和编辑它们。
如果该选项卡不存在，则说明该函数不支持任何配置选项。

> **ℹ️ 提示**
>
> 配置覆盖按函数版本逐个生效。根据你发布函数所用的应用不同，新版本可能采用默认配置，你可能需要重新应用任何配置覆盖。

例如，你可以像下图所示那样配置 TypeScript 函数的超时时间。

![在 Ontology Manager 中管理函数运行时配置](https://www.palantir.com/docs/resources/foundry/functions/function-configuration.png)

### 配置继承

发布新版本时，函数开箱即用地支持继承配置覆盖。配置根据语义化版本规范从先前的稳定版本继承。如果发布的是非稳定版本，则无论先前版本是否为稳定发布，配置都会从先前版本继承。

配置继承要求你的代码仓库包含更新后的模板配置。你可以检查隐藏的 `templateConfiguration.json` 文件，确认代码仓库当前使用的版本。

- 对于 TypeScript v1 函数代码仓库，必须满足 `parentTemplateVersion >= 3.512.0`
- 对于 Python 函数代码仓库，必须满足 `parentTemplateVersion >= 0.423.0`

## 一致性快照

函数支撑的动作在单次运行中会自动对所有读取请求使用同一个本体快照。

一致性快照具有以下优点：

- **数据一致性：** 如果没有快照，当底层数据在请求之间发生变化时，函数内顺序执行的本体查询可能返回不同版本的数据。有了快照，函数将在本体的一致性视图上运行，类似于数据库事务中的快照隔离。
- **性能提升：** 在所有本体请求之间复用同一个快照，可显著提升本体读取性能。单个函数支撑的动作及其内部的任何查询都能受益。

### 快照配置

如果你需要针对高级用例显式管理快照，可以在[函数配置页面](#函数配置页面)使用以下选项配置快照行为：

- **默认（推荐）：** 除非遇到与快照相关的错误，否则保持选中此选项。
- **禁用快照：** 当你需要在运行期间每次查询都获取最新数据时，或当你因长时间运行的工作负载而遇到快照错误时使用。
- **启用快照：** 当你需要在所有读取之间获得一致的时间点视图并希望获得更好的读取性能，且函数能够容忍数据在运行中途不更新时使用。对大多数用例不推荐使用。

> **⚠️ 注意**
>
> 默认情况下，带数据源的函数在不带快照的情况下针对实时数据运行。不建议强制使用快照，因为函数可能执行写入或调用外部系统。

## 强制执行的限制

系统设有若干限制，以防止函数在执行时消耗过多资源。

### 时间限制

函数默认的运行时间限制为 **60 秒**。可以在[函数配置页面](#函数配置页面)修改这些限制。

函数在实时预览中运行时，即使在函数配置页面进行了修改，最长也可运行 **280 秒**。

通过 [Automate](https://www.palantir.com/docs/foundry/automate/overview/) 执行的函数以异步方式运行，最长可达 **4 小时**，超出标准执行限制。

> **⚠️ 注意**
>
> TypeScript v1 函数还有额外的 **30 秒** CPU 时间限制，且不可配置。当函数超过此阈值时，原因通常是数据加载逻辑低效。有关如何避免 CPU 超时的建议，请参阅[优化性能](https://www.palantir.com/docs/foundry/functions/optimize-performance/)部分。

### 内存限制

TypeScript v1、TypeScript v2 和 Python 函数的内存限制各不相同。

#### TypeScript v1

函数执行的内存使用量限制为 **128 MB**。这一限制很少被触及；函数通常会在触及内存限制之前先遇到时间限制或对象加载限制。

#### 已部署的 Python 函数

已部署的 Python 函数默认有 **2 GB** 内存使用量。目前，已部署的 Python 函数无法在函数配置页面配置内存使用量。

#### 无服务器 Python 和 TypeScript v2 函数

无服务器（serverless）函数默认有 **1024 MiB** 内存使用量。可以在[函数配置页面](#函数配置页面)将其配置为 **512 MiB** 到 **5120 MiB** 之间。

### 多线程

对于 TypeScript v1，函数在单线程上执行，任一时刻只允许一个计算。不过，你可以并行加载对象集或链接。更多信息请参阅[优化性能](https://www.palantir.com/docs/foundry/functions/optimize-performance/)。

对于 TypeScript v2 和 Python 函数，你可以借助内置的 Node.js `worker_threads` 库和 Python `threading` 库使用多线程。

### TypeScript v1 的对象集限制

使用[对象集](https://www.palantir.com/docs/foundry/functions/api-object-sets/)时，调用 `.all()` 或 `.allAsync()` 会在以下情况下抛出错误：

- 一次性从对象集中加载超过 **100,000 个对象**。一般来说，即使加载数万个对象也会遇到时间限制或内存限制。对于遇到此限制的用例，可考虑使用[聚合](https://www.palantir.com/docs/foundry/functions/api-object-sets/#computing-aggregations)获取汇总数据，或使用[排序与限制](https://www.palantir.com/docs/foundry/functions/api-object-sets/#ordering-and-limiting)获取对象子集。
- 一次性使用超过 **3 次[环绕搜索（search arounds）](https://www.palantir.com/docs/foundry/functions/api-object-sets/#search-around)**。

某些聚合和分桶操作也有限制。详情请参阅[聚合](https://www.palantir.com/docs/foundry/functions/api-object-sets/#computing-aggregations)部分。

---

*原文：[Manage published functions](https://www.palantir.com/docs/foundry/functions/manage-functions/)*
