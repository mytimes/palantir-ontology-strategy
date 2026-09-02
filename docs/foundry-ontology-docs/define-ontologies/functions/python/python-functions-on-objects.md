# 对象函数（Functions on objects）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/python-functions-on-objects/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

你可以使用 Python Ontology SDK 编写与本体交互的函数。

## 生成 Python Ontology SDK

要生成 Python Ontology SDK 客户端，请导航到[**Resource imports**（资源导入）侧边栏](https://www.palantir.com/docs/foundry/functions/resource-imports-sidebar/)并选择 **Add > Ontology**（添加 > 本体）。在那里，选择你所需的本体，并导入你希望在函数中操作的任何对象和链接。保存以确认选择后，将出现一个横幅，提示尚未创建相应的 OSDK。

导航到 **SDK Generation**（SDK 生成）选项卡以生成并安装 OSDK。

> **⚠️ 注意**
>
> 如果 SDK 生成返回导入错误，例如 `cannot import name 'Equipment2' from 'ontology_sdk.ontology.objects'`，请确认你已在界面底部的蓝色栏中选择了一个本体。导入对象类型并不会选择 SDK 生成所需的本体。

![创建新的 SDK。](https://www.palantir.com/docs/resources/foundry/functions/python-sdk-create-new.png)

如果尚未生成 OSDK，系统会提示你为生成的包输入名称。第一个版本生成后，包名将无法更改。

选择 **Create new version**（创建新版本）后，你可以在此视图中监控生成进度。

![正在生成 SDK 包。](https://www.palantir.com/docs/resources/foundry/functions/python-sdk-package-generating.png)

生成完成后，你需要使用 ![安装](https://www.palantir.com/docs/resources/foundry/functions/python-sdk-package-install-icon.png) 按钮安装新生成的版本。

![生成的 SDK 包已准备好安装。](https://www.palantir.com/docs/resources/foundry/functions/python-sdk-package-ready-to-install.png)

这将在任务运行器面板中触发交互式安装。该任务成功完成后（Task Runner 将显示 `BUILD SUCCESSFUL`），OSDK 的代码补全将在你的 Code Assist 会话中可用。

`meta.yml` 文件也会被更新，以包含对生成包的引用。你可以手动更新 `meta.yml` 而不使用安装辅助工具，但如果手动更新 `meta.yml`，你需要重新构建 Code Assist 会话以使更改生效。

![meta.yml 包含已安装的 SDK 包。](https://www.palantir.com/docs/resources/foundry/functions/python-sdk-meta-yml-updated.png)

每当你在侧边栏中导入更多资源时，系统都会提示你生成并安装包含这些资源的新版本 OSDK。此外，如果你修改了已导入的资源（例如，向已导入的对象类型添加新属性），你需要生成新的 OSDK 版本以使这些更改生效。

## 示例

对于一个名为 `Aircraft`、具有 `brand`（品牌）和 `capacity`（容量）属性的示例对象类型，你可以编写一个接收 `Aircraft` 对象并返回其摘要的函数，如下所示：

```python
from functions.api import function
from ontology_sdk.ontology.objects import Aircraft

@function
def aircraft_input_example(aircraft: Aircraft) -> str:
    return f"{aircraft.brand} aircraft, holds {aircraft.capacity} passengers"
```

此外，如果你想搜索满足某个容量阈值的 `Aircraft` 对象，可以编写以下代码：

```python
from functions.api import function
from ontology_sdk import FoundryClient
from ontology_sdk.ontology.objects import Aircraft
from ontology_sdk.ontology.object_sets import AircraftObjectSet

@function
def aircraft_search_example() -> AircraftObjectSet:
    client = FoundryClient()
    return client.ontology.objects.Aircraft.where(Aircraft.object_type.capacity > 100)
```

Python OSDK 还提供 beta 功能，例如与 pandas DataFrame 的互操作性：

```python
from functions.api import function
from ontology_sdk.ontology.object_sets import AircraftObjectSet

@function
def aircraft_dataframe_example(aircrafts: AircraftObjectSet) -> int:
    df = aircrafts.to_dataframe()
    return df['capacity'].sum()
```

查看 [Python Ontology SDK 文档](https://www.palantir.com/docs/foundry/ontology-sdk/python-osdk/)以了解更多信息。

---

*原文：[Functions on objects](https://www.palantir.com/docs/foundry/functions/python-functions-on-objects/)*
