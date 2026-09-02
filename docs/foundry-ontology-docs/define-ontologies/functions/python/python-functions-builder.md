# 在 Pipeline Builder 中使用 Python 函数（Use a Python function in Pipeline Builder）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/python-functions-builder/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

> **ℹ️ 提示**
>
> Pipeline Builder 同时支持 Java 和 Python 用户定义函数（UDF）。[了解有关 Java UDF 的更多信息](https://www.palantir.com/docs/foundry/transforms-java/user-defined-functions/)。

## 前提条件

本指南假设你已经编写并发布了一个 Python 函数。请查阅我们的 [Python 函数入门](https://www.palantir.com/docs/foundry/functions/python-getting-started/)文档以获取教程。

## 架构

Python 函数在 Pipeline Builder 管道中作为 sidecar 容器运行。这意味着该函数无需部署，并会根据管道的规模动态扩缩容。嵌入的函数可以像 Pipeline Builder 中的其他转换一样进行[预览](https://www.palantir.com/docs/foundry/pipeline-builder/outputs-preview-pipeline/)。

## Python 函数如何处理数据

当你在 Pipeline Builder 中将 Python 函数用作用户定义函数（UDF）时，该函数**每行运行一次**。Pipeline Builder 不会将整个表、`pandas` DataFrame 或 Spark DataFrame 传递给你的函数。相反，你将一个或多个输入列映射到函数的参数，Pipeline Builder 会为每一行调用该函数，并将该行各单元格的值作为参数传入。

函数返回的值将成为输出表中的一个新列，每行产生一个值。因此，Python UDF 返回的是标量（单值）类型，例如 `str`、`int`、`float`、`bool` 或 `datetime`，而不是 DataFrame。不存在可返回的 DataFrame 类型：Pipeline Builder 根据每行返回值组装成的列*就是*最终的表格输出。

下面的示例接收两列作为输入，每行返回一个 `str` 值，Pipeline Builder 会将其写入一个新列：

**Python**

```python
from functions.api import function

@function
def full_name(first_name: str, last_name: str) -> str:
    return f"{first_name} {last_name}"
```

当你[配置该转换](#在-pipeline-builder-管道中使用你的函数)时，将 `first_name` 和 `last_name` 参数映射到相应的输入列。转换运行时，Pipeline Builder 会对每一行执行 `full_name`，并将返回的字符串写入一个新的输出列。

有关受支持的输入和输出类型及其 Python 对应类型的完整列表，请查阅[类型参考](https://www.palantir.com/docs/foundry/functions/types-reference/)。该参考中的[本体类型](https://www.palantir.com/docs/foundry/functions/types-reference/#ontology-types)在 Pipeline Builder 中不受支持：接受或返回这些类型的函数无法被导入，也不会出现在可用函数列表中。

### 返回结构体

要从单个函数返回多个相关值，请返回[自定义类型（结构体）](https://www.palantir.com/docs/foundry/functions/types-reference/#structcustom-type)而不是标量。Pipeline Builder 会将返回值作为单个结构体列添加，自定义类型的每个属性对应一个字段。然后，你可以使用 [Get struct field](https://www.palantir.com/docs/foundry/pipeline-builder/functions-index/#get-struct-field)（获取结构体字段）转换提取各个字段。

**Python**

```python
from dataclasses import dataclass
from functions.api import function, Double

@dataclass
class Stats:
    total: Double
    average: Double

@function
def compute_stats(a: Double, b: Double) -> Stats:
    total = a + b
    return Stats(total=total, average=total / 2)
```

## 在 Pipeline Builder 管道中使用你的函数

按照以下步骤在你的管道中准备和配置 Python 函数：

1. 打开你要在其中使用 Python 函数的 Pipeline Builder 管道。

![Pipeline Builder 中的一个 Python 函数。](https://www.palantir.com/docs/resources/foundry/functions/python-functions-builder.png?width=800px)

2. 使用以下两种方法之一将你的 UDF 导入 Pipeline Builder：
  - **从图形视图：**
    1. 从管道图形的上部选择 **Reusables**（可复用项），然后选择 **User-defined functions**（用户定义函数）。![Pipeline Builder 中的 “Reusables” 按钮。](https://www.palantir.com/docs/resources/foundry/functions/python-functions-builder-reusable.png?width=500px)
    2. 选择 **Import UDF**（导入 UDF），在可用函数中搜索以找到你想使用的函数。
  3. 在函数名称旁选择 **Add**（添加）。该函数随后应显示 **Imported**（已导入）标签。![向 Pipeline Builder 添加 Python 函数。](https://www.palantir.com/docs/resources/foundry/functions/python-functions-add-builder.png?width=700px)
  4. 关闭导入对话框，并在 Pipeline Builder 图形上你想使用该函数的位置选择 **Transform**（转换）。
  5. 从转换列表中，找到左侧的 **UDFs** 选项卡以查看你导入的函数。
  - **使用转换选择器：**
    1. 在 Pipeline Builder 图形上选择 **Transform**。
    2. 输入你想导入的 UDF 的名称。![在 Pipeline Builder 中搜索未导入的 UDF。](https://www.palantir.com/docs/resources/foundry/functions/python-functions-builder-search-udfs.png?width=500px)
    3. 选择 **Search unimported UDFs**（搜索未导入的 UDF）。
  4. 将鼠标悬停在所需的 UDF 上并选择 **Import**（导入）。![在 Pipeline Builder 中导入 UDF。](https://www.palantir.com/docs/resources/foundry/functions/python-functions-builder-import-udfs.png?width=500px)
3. 填写转换定义，指定输入列和参数，然后选择 **Apply**（应用）。

![Pipeline Builder 中已配置的 Python 函数转换。](https://www.palantir.com/docs/resources/foundry/functions/python-functions-builder-transform.png?width=700px)

现在你应该能在 Pipeline Builder 图形上看到你的 Python 函数，并可以预览该函数的输出。

![Pipeline Builder 中的 Python 函数](https://www.palantir.com/docs/resources/foundry/functions/python-functions-builder-ete.png?width=900px)

## Pipeline Builder 中的外部 API 调用

要从 Pipeline Builder 对外部系统进行 API 调用，你可以发布一个[有权访问外部系统的 Python 函数](https://www.palantir.com/docs/foundry/functions/api-calls/)。这将使你能够编写与外部系统通信的逻辑，并将其用作管道的一部分。

要在 Pipeline Builder 中用作用户定义函数（UDF），函数中使用的所有数据源都必须配置为可导入到管道中。要配置此设置，请在 Data Connection（数据连接）中导航到该数据源，然后转到 **Connection settings > Code import configuration**（连接设置 > 代码导入配置）选项卡：

![允许数据源导入到管道。](https://www.palantir.com/docs/resources/foundry/functions/allow-source-to-be-imported-to-pipelines.png)

在你的数据源上启用此选项并发布 Python 函数后，就可以像任何其他 Python 函数一样在你的管道中使用它。

## Pipeline Builder 中的本体访问

> **⚠️ 注意**
>
> Pipeline Builder UDF 不支持通过 `FoundryClient()` 或 Ontology SDK（OSDK）直接访问本体。在 UDF 中实例化 `FoundryClient()` 并访问本体会返回 `EnvironmentNotConfigured` 错误。此限制有助于防止数据在跨构建时被去除安全标记或泄露。

要从 Pipeline Builder UDF 访问本体数据，请考虑以下方法：

- **使用外部数据源：** 为执行本体查询的 Python 函数创建一个外部数据源，然后将该数据源导入你的 Pipeline Builder UDF。这可以避免在 UDF 上下文中直接查询本体所带来的认证和权限问题。要配置该数据源，请在 Data Connection 中导航到它，并按[上文](#pipeline-builder-中的外部-api-调用)所述启用 **Connection settings > Code import configuration**。
- **联接本体支撑的数据集：** 如果你的用例允许，维护一个包含相关本体支撑数据的独立数据集，并将其联接到你的管道中。这可以避免本体 API 调用，并支持去重或存在性检查。

---

*原文：[Use a Python function in Pipeline Builder](https://www.palantir.com/docs/foundry/functions/python-functions-builder/)*
