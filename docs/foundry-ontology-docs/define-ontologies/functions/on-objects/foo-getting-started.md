# 对象函数入门（Getting started with functions on objects）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/foo-getting-started/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

函数的核心特性之一是能够轻松访问已集成到 Foundry 本体中的数据。本体为你的组织提供数据的语义建模，使得访问结构化数据和跨用例复用逻辑变得容易。

> **ℹ️ 提示：前提条件**
>
> 本教程假设你已经创建并设置了 TypeScript 代码仓库。如果你尚未完成，请先完成[入门](https://www.palantir.com/docs/foundry/functions/getting-started/)教程。

### 导入本体类型

你想在函数中使用的任何对象、接口或链接类型都必须导入到包含你代码仓库的项目中。选择 **Resource Imports**（资源导入）侧边栏可以查看已导入到项目中的对象类型。

![本体导入侧边栏](https://www.palantir.com/docs/resources/foundry/functions/ontology-import-side-panel.png)

> **ℹ️ 提示**
>
> 你的组织可能没有 Airport（机场）和 Flight（航班）对象。在跟着本教程操作时，请使用你有权访问的任何对象类型。

要导入更多对象类型，你需要在 **Resource Imports** 侧边栏中选择 **Add**（添加）按钮。如果尚未选择本体，系统会提示你选择一个本体。如果你至少已导入一个本体类型，所选本体将被自动解析。

选择本体后，将出现一个搜索模态框。你的本体取决于你组织中可用的对象类型。首先选择几个对象类型以及连接它们的链接类型。本示例导入了 Airport 和 Flight 对象，以及它们之间的链接类型。

![本体导入示例](https://www.palantir.com/docs/resources/foundry/functions/ontology-import-example.png)

你也可以通过选择 **Add** 按钮下的 **Interfaces**（接口）来导入本体接口。

![“Add” 下拉菜单中导入接口的选项。](https://www.palantir.com/docs/resources/foundry/functions/interface-import-example.png)

选择 **Confirm selection**（确认选择）将本体类型导入项目。Code Assist 将自动重启以重新生成代码绑定，反映你新导入的对象和链接类型。

在 TypeScript v1 代码仓库中，你现在可以从 `@foundry/ontology-api` 包导入本体类型。如果你使用的是私有本体，包名将变为 `@foundry/ontology-api/<ontology-api-name>`。

在 TypeScript v2 代码仓库中，导入本体类型后，系统会提示你[生成并安装 Ontology SDK](https://www.palantir.com/docs/foundry/functions/typescript-v2-migration/#generate-the-ontology-sdk)。请在编写函数之前完成此步骤；只有在安装了 Ontology SDK 之后，导入的类型才能在你的代码中使用，并且你需要从 `@ontology/sdk` 包导入它们。

> **ℹ️ 提示：私有本体**
>
> 如果你使用的是私有本体，请在下面所有 TypeScript v1 示例中将 `@foundry/ontology-api` 替换为 `@foundry/ontology-api/your-private-ontology-api-name-here`。

`@foundry/ontology-api` 包适用于 TypeScript v1 函数。在 TypeScript v2 代码仓库中，你必须先生成并安装 Ontology SDK，才能从 `@ontology/sdk` 导入本体类型。这些步骤请参阅[生成 Ontology SDK](https://www.palantir.com/docs/foundry/functions/typescript-v2-migration/#generate-the-ontology-sdk)。

### 添加对象支撑的函数

接下来，使用你刚导入的对象类型编写一个函数。你的代码将取决于你可用的对象类型、属性和链接类型。切回 **Code**（代码）选项卡，尝试导入你刚添加的某个对象类型：

**TypeScript v1**

```typescript
import { Airport } from "@foundry/ontology-api";
```

**TypeScript v2**

```typescript
import { Airport } from "@ontology/sdk";
```

然后，编写一个以该对象为输入的函数。在 TypeScript v2 中，对象类型实例被包装在来自 `@osdk/client` 包的 `Osdk.Instance` 类型中，并且每个函数都是 `src/functions` 目录下以该函数命名的文件的默认导出。

**TypeScript v1**

```typescript
@Function()
public myObjectFunction(airport: Airport) {
    airport.
}
```

**TypeScript v2**

```typescript
export default function myObjectFunction(airport: Osdk.Instance<Airport>) {
    airport.
}
```

Code Assist 启动后，只需输入 `airport.` 即可看到你可用的属性和链接类型的自动补全：

![自动补全](https://www.palantir.com/docs/resources/foundry/functions/autocomplete.png)

在本例中，我们使用[模板字符串 ↗](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals#syntax)将 Airport 上的 `city` 和 `country` 字段组合成人类可读的位置：

**TypeScript v1**

```typescript
import { Function } from "@foundry/functions-api";
import { Airport } from "@foundry/ontology-api";

export class MyFunctions {

    @Function()
    public airportLocation(airport: Airport): string {
        return `${airport.city}, ${airport.country}`;
    }
}
```

**TypeScript v2**

```typescript
// src/functions/airportLocation.ts

import { Airport } from "@ontology/sdk";
import { Osdk } from "@osdk/client";

export default function airportLocation(airport: Osdk.Instance<Airport>): string {
    return `${airport.city}, ${airport.country}`;
}
```

基于你自己的本体尝试这些 API，编写一个基于你的对象类型返回值的函数。

### 在实时预览中测试

打开 **functions helper**（函数助手），切换到 **Live Preview**（实时预览），选择你上面编写的函数。要在实时预览中运行对象支撑的函数，你必须导入该对象类型的支撑数据源。选择 **Run**（运行）选项旁边的警告图标：

![助手数据源导入](https://www.palantir.com/docs/resources/foundry/functions/helper-datasource-import.png)

然后，使用该对话框导入你的对象类型的支撑数据源：

![助手数据源导入对话框](https://www.palantir.com/docs/resources/foundry/functions/helper-datasource-import-dialog.png)

导入数据源后，选择一个对象并选择 **Run** 查看结果：

![助手预览运行对象函数](https://www.palantir.com/docs/resources/foundry/functions/helper-preview-run-foo.png)

> **⚠️ 注意：实时预览权限**
>
> 实时预览中对象类型的权限由 [TypeScript 代码仓库对每个对象类型底层支撑数据源的权限](https://www.palantir.com/docs/foundry/functions/permissions/#object-loading-permissions)决定。测试[创建通知的函数](https://www.palantir.com/docs/foundry/functions/configure-notifications/#configure-notifications)时，不会强制检查接收者的权限。因此，创建通知的函数在实时预览中可能成功，但在 Foundry 其他地方由动作调用时却可能失败。
>
> 详细了解[为动作配置通知](https://www.palantir.com/docs/foundry/action-types/notifications/)。

### 发布新函数

通过提交代码并使用 **Branches**（分支）选项卡发布新标签来发布新函数。函数发布后，你可以使用 **functions helper** 测试它。

![助手运行对象函数](https://www.palantir.com/docs/resources/foundry/functions/helper-run-foo.png)

函数发布后，你就可以开始在整个平台的其他应用中使用它了。

### 后续步骤

本教程只是对象函数功能的冰山一角。要了解更多，请参阅以下资源：

- 参阅[对象 API 文档](https://www.palantir.com/docs/foundry/functions/api-objects-links/)，了解你可以用对象做什么
- 阅读[对象集文档](https://www.palantir.com/docs/foundry/functions/api-object-sets/)，了解按需搜索对象和聚合
- 了解[在平台中使用函数](https://www.palantir.com/docs/foundry/functions/use-functions/)的各种方式

---

*原文：[Getting started with functions on objects](https://www.palantir.com/docs/foundry/functions/foo-getting-started/)*
