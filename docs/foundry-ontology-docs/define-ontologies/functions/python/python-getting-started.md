# Python 函数入门（Getting started with Python functions）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/python-getting-started/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

以下文档将引导你完成在 Palantir 平台中使用 Python 函数的初始准备工作。你将学习如何创建 Python 函数代码仓库、提交并发布函数、在实时预览中测试等等。

## 创建 Python 函数代码仓库

导航到你选择的项目，通过选择 **+ New > Repository**（+ 新建 > 代码仓库）创建一个新的代码仓库。选择 **Python functions**（Python 函数）模板来初始化你的代码仓库。我们建议将所有用于 Workshop 或基于本体的应用的函数集中在一个代码仓库中，以最大限度降低成本。

![创建 Python 函数代码仓库。](https://www.palantir.com/docs/resources/foundry/functions/python-functions-create-repo.png)

代码仓库创建后，导航到 `python-functions/python/python-functions/my_function.py` 文件。

## 探索代码仓库

你的代码仓库将初始化一个 `my_function.py` 文件，其中包含一些示例函数，包括以下内容：

**Python**

```python
from functions.api import function, String

@function
def my_function() -> String:
    return "Hello World!"
```

请注意，该函数遵循以下约束：

- 函数必须使用 `functions.api` 包中的 `@function` 装饰器进行装饰，才能被识别为 Python 函数。你可以有多个 Python 文件，每个文件中有多个函数，但*只有带有此装饰器的函数*才会被注册为 Python 函数。
- 函数必须声明其所有输入的类型以及输出的类型，可以使用 functions API 包中的类型或其对应的 Python 类型。例如，上例的输出类型声明为 functions API 中的 `String`，但也可以声明为对应的 Python 类型 `str`。

> **ℹ️ 提示**
>
> 即使你使用 API 类型（例如 `String`）声明参数的类型，在运行时传给函数的仍将是对应的 Python 类型（在本例中为 `str`）。

有关 Python 函数中类型的完整概览，请参阅我们的[类型参考文档](https://www.palantir.com/docs/foundry/functions/types-reference/)。

要向你的代码仓库添加 Python 包，请使用 **Libraries**（库）侧边面板，如[发现和使用 Python 库](https://www.palantir.com/docs/foundry/transforms-python/use-python-libraries/)中所述。

## 在实时预览中测试

添加新函数后，你可以在 **Functions**（函数）助手中立即运行它。从屏幕左下角打开 **Functions** 助手，然后选择 **Live Preview**（实时预览）。选择 `add_seconds_to_datetime` 函数，输入参数值，然后选择 **Run**（运行）来运行代码。

![在函数助手中运行你的新函数。](https://www.palantir.com/docs/resources/foundry/functions/python-functions-live-preview.png)

选择右上角的 **Commit**（提交），将你的更改提交到代码仓库的 `master` 分支。

### 提交并发布函数

编写函数后（或取消注释提供的某个示例函数），按照以下步骤提交并发布它。

1. 在 **Source control**（源代码管理）选项卡中选择 **Commit** 并添加提交信息，以提交你的更改。
2. 从屏幕顶部中央选择 **Branches**（分支）选项卡，然后选择 **Tags and releases**（标签和发布）。
3. 选择 **New tag**（新建标签）并为发布提供一个版本号。

![选择合适的版本](https://www.palantir.com/docs/resources/foundry/functions/new-functions-tag.png?width=500)

4. 选择 **Tag and release**（打标签并发布），等待发布步骤完成。
5. 检查成功后，选择 **Code** 选项卡，然后打开页面底部的 **Functions** 选项卡。你将在结果中看到 `my_function`。

![打开 Functions 助手。](https://www.palantir.com/docs/resources/foundry/functions/python-functions-preview.png)

6. 选择该函数，然后选择 **Run** 以执行你刚刚发布的函数。

![在 Functions 助手中运行函数。](https://www.palantir.com/docs/resources/foundry/functions/python-functions-run.png)

## 添加另一个函数

现在，我们将向此代码仓库添加一个更复杂的函数来测试和发布。将下面的代码复制并粘贴到 `my_function` 文件的底部。

**Python**

```python
from functions.api import function, String
from datetime import datetime, timedelta

@function
def add_seconds_to_datetime(start_time: datetime, elapsed_millis: int) -> str:
    dt = start_time + timedelta(milliseconds=elapsed_millis)
    return dt.isoformat()
```

有关如何在平台中使用 Python 函数的更多示例，请查阅我们关于[在 Pipeline Builder 中使用 Python 函数](https://www.palantir.com/docs/foundry/functions/python-functions-builder/)和[在 Workshop 中使用 Python 函数](https://www.palantir.com/docs/foundry/functions/python-functions-workshop/)的文档。

---

*原文：[Getting started with Python functions](https://www.palantir.com/docs/foundry/functions/python-getting-started/)*
