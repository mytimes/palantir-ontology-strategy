# 在 Workshop 中使用 Python 函数（Use a Python function in Workshop）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/python-functions-workshop/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

## 前提条件

本指南假设你已经编写并发布了一个 Python 函数。请查阅 [Python 函数入门](https://www.palantir.com/docs/foundry/functions/python-getting-started/)文档以获取教程。有关如何使用 Python SDK 查询本体的示例，请参阅 [Python Ontology SDK 文档](https://www.palantir.com/docs/foundry/ontology-sdk/python-osdk/)。

## 在 Workshop 中使用 Python 函数

在 Workshop 中，从模块左侧的 **Variables**（变量）选项卡搜索 Python 函数。[无服务器函数和已部署函数](https://www.palantir.com/docs/foundry/functions/functions-deployed/#choose-between-deployed-and-serverless-execution-modes)都会出现在这里。无服务器函数始终可以在任何版本运行，无需手动部署。已部署函数将显示一个图标，对函数和函数版本分别指示以下三种状态之一：

- **Running（运行中）：** 此函数和版本可以处理请求。
- **Stopped（已停止）：** 此函数和版本不可用。在函数选择器中，将鼠标悬停在信息图标上，选择 **Configure**（配置），然后选择 **Create and start deployment**（创建并启动部署）以使函数可用。
- **Upgrading（升级中）：** 此函数和版本尚不可用。

![Workshop 中的一个 Python 函数](https://www.palantir.com/docs/resources/foundry/functions/python-functions-workshop-deployment-status.png?width=650px)

### 发布新版本

同一时间只托管函数代码仓库的一个版本。为了在更改函数时尽量减少停机时间，我们建议添加一个包含更改的新函数（如 `function_v1`），并按[此处](https://www.palantir.com/docs/foundry/functions/python-getting-started/#commit-and-publish-a-function)所述打标签。在已发布函数的 tags and releases（标签与发布）中，选择 **Open in Ontology Manager**（在 Ontology Manager 中打开）。

在 Ontology Manager 中，选择你想在应用中使用的函数代码仓库版本，然后选择 **Upgrade**（升级）。

![升级已部署的函数](https://www.palantir.com/docs/resources/foundry/functions/python-functions-upgrade-deployed-function.png?width=350px)

将所有使用此代码仓库中函数的下游应用更新到你已部署的新版本。请注意，之前的部署版本将不再运行，因此在进行此更改时你的应用会有短暂的停机时间。你将同时拥有 `function_v0` 和 `function_v1`，因此虽然你需要切换到新的部署版本，但不必更改你正在使用的函数。当 `function_v0` 不再被使用时，你可以删除该函数。

### 调试错误

如果你的函数在 Workshop 中未按预期工作，首先检查问题是与函数的逻辑有关还是与响应能力有关。如果逻辑有问题，请检查支撑代码仓库中的源代码。如果函数无响应或抛出错误，请按照以下步骤操作：

1. 检查你选择的版本当前是否在函数选择器下拉菜单中处于运行状态。

![Workshop 函数版本选择器。](https://www.palantir.com/docs/resources/foundry/functions/python-functions-workshop-function-selector-running.png?width=350px)

2. 如果函数未部署或处于 `Upgrading` 状态，将鼠标悬停在函数的信息图标上并选择 **Configure**。这将带你进入 Ontology Manager，在那里你可以选择 **Start Deployment**（启动部署）以使函数重新运行。

![有关 Python 函数版本的信息。](https://www.palantir.com/docs/resources/foundry/functions/python-functions-upgrading-function-info.png?width=750px)

3. 如果你的函数处于 `Running` 状态，或者你需要有关部署行为的更多信息，请在 Ontology Manager 的左侧面板中选择 **Deployment**（部署）以查看详细日志。如果你选择 **View live**（查看实时），还可以查看 SLS 日志。

![在 Ontology Manager 中查看部署日志。](https://www.palantir.com/docs/resources/foundry/functions/python-functions-deployed-function-logs.png?width=850px)

## 创建函数支撑列

要创建函数支撑列，你必须发布一个满足以下要求的函数：

- 函数的输入参数必须包含一个对象集参数（从 `ontology_sdk.ontology.object_sets` 导入），并可以选择包含其他输入参数。该对象集参数将使表中显示的对象能够被传入函数，从而生成所需的派生列。请注意，`list[ObjectType]` 参数在这里也可以使用，但不推荐这种性能较低的选项。
- 函数的返回类型必须是 `dict[ObjectType, ColumnType]`，其中 `ColumnType` 是该列所需的[类型](https://www.palantir.com/docs/foundry/functions/types-reference/#types-reference)；或者使用 `dict[ObjectType, CustomType]` 从函数返回多个列。详细了解[自定义类型](https://www.palantir.com/docs/foundry/functions/types-reference/#structcustom-type)。

一旦满足上述条件的函数被配置并发布，你就可以在 [Object Table](https://www.palantir.com/docs/foundry/workshop/widgets-object-table/#features-of-function-backed-properties)（对象表）组件的配置中配置函数支撑属性列。

返回一列的函数示例：

```python
from functions.api import Date, Integer, String, function
from ontology_sdk import FoundryClient
from ontology_sdk.ontology.object_sets import MyObjectTypeObjectSet
from ontology_sdk.ontology.objects import MyObjectType

@function
def function_backed_column_single_col(
    selected_objects: MyObjectTypeObjectSet
) -> dict[MyObjectType, Integer]:
    final_dict = {}

    for curr_obj in selected_objects:
        final_dict[curr_obj] = 10 # The value can be defined for any arbitrary logic

    return final_dict
```

![返回一列的函数示例。](https://www.palantir.com/docs/resources/foundry/functions/python-functions-advanced-single.png)

返回多列的函数示例：

```python
from dataclasses import dataclass

@dataclass
class CustomType:
    column_name_a: int
    column_name_b: int

@function
def function_backed_column_multiple_cols(
    selected_objects: MyObjectTypeObjectSet, some_other_parameter: String
) -> dict[MyObjectType, CustomType]:
    final_dict = {}

    for curr_obj in selected_objects:
        final_dict[curr_obj] = CustomType(column_name_a=10, column_name_b=20)

    return final_dict
```

![返回多列的函数示例。](https://www.palantir.com/docs/resources/foundry/functions/python-functions-advanced-multiple.png)

---

*原文：[Use a Python function in Workshop](https://www.palantir.com/docs/foundry/functions/python-functions-workshop/)*
