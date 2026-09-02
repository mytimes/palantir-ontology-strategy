# 模型别名（Model aliases）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/model-aliases/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

模型别名是指向语言模型的具名引用，提供了一种在代码中引用语言模型的便捷方式。

有关在函数中使用语言模型的完整演练，请参阅 [TypeScript v2 和 Python 函数中的语言模型](https://www.palantir.com/docs/foundry/functions/language-models-python-tsv2/)。

## 定义模型别名

要定义模型别名，请打开 TypeScript v2 或 Python 代码仓库并按以下步骤操作：

1. 在**“资源导入”**面板中打开 **Platform SDK** 标签页。

![TypeScript v2 代码仓库中用于访问 Platform SDK 资源的标签页。](https://www.palantir.com/docs/resources/foundry/functions/platform-sdk-tab.png)

2. 要导入新的语言模型，请在右上角选择**添加 > 模型**。随后会打开一个窗口，你可以在其中查看可用模型，包括 Palantir 提供的模型和已注册的模型。

![TypeScript v2 代码仓库中的模型导入对话框。](https://www.palantir.com/docs/resources/foundry/functions/models-v3-import-dialog.png)

3. 选择要导入的模型，然后选择**确认选择**。随后会打开一个配置对话框，你可以在其中为每个所选模型配置别名。选择别名附近的钢笔图标进行编辑，或选择保留默认值。

> **ℹ️ 提示**
>
> 别名的键在代码仓库内必须唯一。

![选择要导入的模型后配置模型别名。](https://www.palantir.com/docs/resources/foundry/functions/configure-models-aliases.png)

4. 导入的模型将出现在**“资源导入”**侧面板的 **Platform SDK** 标签页中。你可以选择别名旁边的钢笔图标，内联编辑任意别名。

![内联配置模型别名。](https://www.palantir.com/docs/resources/foundry/functions/inline-models-aliases-edit.png)

## 在代码中使用模型别名

要在函数中使用模型别名，请导入别名工具并按名称引用别名。该别名会解析为一个模型 RID，你可以将其传递给模型客户端：

**TypeScript v2**

```typescript
import { Aliases } from "@osdk/functions";

const modelRid = Aliases.model("gpt5Nano").rid;
```

**Python**

```python
from functions.aliases import model

model_rid = model("gpt5Nano").rid
```

有关使用别名调用语言模型的完整示例，请参阅[编写使用语言模型的函数](https://www.palantir.com/docs/foundry/functions/language-models-python-tsv2/#write-a-function-that-uses-a-language-model)。

> **⚠️ 注意**
>
> 模型别名在添加到 [Marketplace 产品](https://www.palantir.com/docs/foundry/functions/marketplace-functions/)的函数中可以使用，但无法在安装期间重新映射。如果别名引用的模型在目标环境中不可用，函数将在运行时无法解析该别名。

## 相关内容

你还可以为配置参数、功能开关或特定于环境的设置等字符串值定义[自定义别名](https://www.palantir.com/docs/foundry/functions/custom-aliases/)。自定义别名出现在 **Platform SDK** 标签页中，可以使函数跨环境移植。

---

*原文：[Model aliases](https://www.palantir.com/docs/foundry/functions/model-aliases/)*
