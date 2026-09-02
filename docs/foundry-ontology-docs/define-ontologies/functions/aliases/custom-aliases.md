# 自定义别名（Custom aliases）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/custom-aliases/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

自定义别名是具名引用，用于存储字符串值，例如配置参数、功能开关或特定于环境的设置。通过使用自定义别名而不是硬编码值，你可以将函数逻辑与特定配置解耦，使函数能够跨环境移植。

> **ℹ️ 提示**
>
> 自定义别名存储用于配置的字符串值。若要在函数中按名称引用语言模型，请参阅[模型别名](https://www.palantir.com/docs/foundry/functions/model-aliases/)。

## 定义自定义别名

要定义自定义别名，请打开 TypeScript v2 或 Python 代码仓库并按以下步骤操作：

1. 打开**“资源导入”侧面板**并选择 **Platform SDK** 标签页。你会看到一个自定义别名区块。

![“资源导入”侧面板，显示 Platform SDK 标签页中的自定义别名选项。](https://www.palantir.com/docs/resources/foundry/functions/custom-aliases-sidebar.png)

2. 选择**新建别名**以打开别名创建对话框。提供一个**键（Key）**作为别名名称，并提供与之关联的**值（Value）**，然后选择**创建**。

![新建别名对话框，键设置为 myAlias，值设置为 someValue。](https://www.palantir.com/docs/resources/foundry/functions/custom-aliases-create.png)

> **ℹ️ 提示**
>
> 别名的键在代码仓库内必须唯一。

3. 自定义别名将出现在**“自定义别名”**区块中。

## 编辑自定义别名

要编辑已有的自定义别名，请转到 **Platform SDK** 标签页中的**“自定义别名”**区块。选择别名旁边的钢笔图标可内联编辑其值。你也可以点击三个点的图标来编辑别名的键，或彻底删除该别名。

![自定义别名列表，显示已创建的别名及编辑值的选项。](https://www.palantir.com/docs/resources/foundry/functions/custom-aliases-edit.png)

## 在代码中使用自定义别名

要在函数中使用自定义别名，请导入别名工具并按键引用别名：

**TypeScript v2**

```typescript
import { Aliases } from "@osdk/functions";

export default function getCustomValue(): string {
    return Aliases.custom("myAlias");
}
```

**Python**

```python
from functions.aliases import custom
from functions.api import function

@function
def get_custom_value() -> str:
    return custom("myAlias")
```

## 在 Marketplace 中使用自定义别名

当你把使用自定义别名的函数添加到 [Marketplace 产品](https://www.palantir.com/docs/foundry/functions/marketplace-functions/)时，这些别名会自动作为可配置参数出现在**“输入”**下。安装者无需修改函数源代码，即可设置适合其环境的别名值。

![Marketplace 产品，将自定义别名显示为与函数输出并列的可配置参数输入。](https://www.palantir.com/docs/resources/foundry/functions/custom-aliases-marketplace-product.png)

### 设置描述

为帮助安装者理解如何配置别名，你可以为别名参数添加描述。在**“输入”**下选择该别名以打开**“详情”**面板，然后在**“常规”**标签页中输入描述。

![自定义别名参数的“详情”面板，显示描述字段。](https://www.palantir.com/docs/resources/foundry/functions/custom-aliases-set-description.png)

### 添加预设值

你可以为别名定义预设值，供安装者在安装期间选择。在**“详情”**面板中，选择**“预设值”**标签页并选择**手动覆盖**，以定义一组允许的值。

![“预设值”标签页，显示手动覆盖配置及别名的预设值。](https://www.palantir.com/docs/resources/foundry/functions/custom-aliases-add-presets.png)

### 安装体验

安装期间，安装者会看到别名描述，可以从预设值中选择，也可以手动配置别名。安装完成后，函数会将别名解析为安装者配置的值。

![安装视图，显示自定义别名参数及其描述和预设值选项。](https://www.palantir.com/docs/resources/foundry/functions/custom-aliases-install.png)

---

*原文：[Custom aliases](https://www.palantir.com/docs/foundry/functions/custom-aliases/)*
