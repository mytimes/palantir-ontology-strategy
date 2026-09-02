# 权限（Permissions）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/permissions/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

在平台中编写和执行函数需要通过多种权限检查。本节概述你应当了解的不同类型权限以及可能遇到的常见问题。

## 函数编写

必须为函数代码仓库授予适当的权限，以便：

1. 访问本体，从而生成正确的代码绑定。
2. 加载对象，以便运行函数执行的实时预览。

请注意，代码仓库权限必须显式授予，且与授予你用户账户的权限不同。因此，你需要采取特定步骤，将对象类型、链接类型和支撑数据源导入包含你代码仓库的项目中。

有关这些步骤的教程，请参阅[本节](https://www.palantir.com/docs/foundry/functions/ontology-imports/)。下面我们说明导入的具体资源以及为这些资源授予的权限。

### 本体实体权限

在代码仓库中，每当运行检查或 Code Assist 启动时，函数插件都会根据代码仓库的权限加载最新本体，并为加载的每个对象和链接类型生成代码绑定。加载的对象和链接类型集合取决于以下资源类型的导入：

- 本体
- 本体分支
- [对象类型](https://www.palantir.com/docs/foundry/object-link-types/object-types-overview/)
- [链接类型](https://www.palantir.com/docs/foundry/object-link-types/link-types-overview/)

在函数代码仓库中，你可以导航到 **Settings** > **Ontology** 来导入所需的本体资源。该界面允许你选择要导入项目的对象和链接类型。

![本体设置](https://www.palantir.com/docs/resources/foundry/functions/ontology-settings-flights.png)

如果你的用户账户可以访问多个本体，你还可以选择要使用哪个本体。目前不支持将多个本体导入单个项目。

![本体选择器](https://www.palantir.com/docs/resources/foundry/functions/ontology-picker.png)

> **⚠️ 注意**
>
> 虽然上述界面出现在函数代码仓库中，但你导入的任何本体、对象类型和链接类型都是在**项目**级别添加的。这意味着在一个代码仓库中更改导入可能会影响同一项目中的其他代码仓库。如果你希望有两个依赖不同本体实体的代码仓库，应将它们分到不同的项目中。

### 对象加载权限

代码仓库中的**函数助手（functions helper）**允许用户以两种方式执行函数：执行已发布的函数，或在实时预览中执行代码。在实时预览中执行时，函数代码会在 Code Assist 中编译和执行，Code Assist 是旨在帮助代码编写者快速迭代的基础设施。

由于 Code Assist 与代码仓库绑定，它与上文所述的代码生成遵循相同的权限要求。这意味着在实时预览中运行函数时，你希望使用的每个对象类型背后的支撑数据源必须已导入项目。

在函数助手中，如果项目中导入了对象类型但未导入相应的数据源，实时预览中会显示一条警告，提示你更新导入：

![预览支撑数据源](https://www.palantir.com/docs/resources/foundry/functions/preview-backing-datasources.png)

对于大多数对象类型，**Import backing datasources** 对话框会提示你导入一个 Foundry 数据集。对于启用了[行级安全](https://www.palantir.com/docs/foundry/object-permissioning/configuring-rv-access-controls/)的对象类型，系统会提示你导入[受限视图](https://www.palantir.com/docs/foundry/security/restricted-views/)。

## 已发布函数的执行

函数发布后，即可供更广泛的用户群体使用，并可以配置为在 [Workshop](https://www.palantir.com/docs/foundry/workshop/overview/) 和 [Actions](https://www.palantir.com/docs/foundry/action-types/function-actions-overview/) 等应用中执行。对于执行已发布函数的权限，仍有一些注意事项需要牢记。

### 函数权限

用户必须对发布函数的代码仓库拥有 **Viewer** 角色，才能执行函数。通常，最好将函数代码仓库放在与依赖该仓库中函数的最终用户应用相同的项目中，无论这些应用是使用 Workshop、Slate 还是其他工具创建的。如果用户遇到错误，提示他们缺少读取函数的权限（ReadFunctionsPermissionDenied），请检查他们是否对代码仓库具有读取访问权限。[详细了解如何移动和共享资源。](https://www.palantir.com/docs/foundry/compass/move-and-share-resources/)

> **ℹ️ 提示**
>
> 侧边栏中的 **Check access** 面板可用于检查某人对 Workshop 或 Slate 应用的访问权限，包括对所依赖函数的访问权限。更多信息请参阅 [Check access 面板文档](https://www.palantir.com/docs/foundry/security/checking-permissions/)。

> **ℹ️ 提示：在 Developer Console 中注册函数**
>
> 错误代码为 `FunctionRegistry:ReadOntologyFunctionPermissionDenied` 的 `PERMISSION_DENIED` 错误通常意味着函数未在 Developer Console 中注册或不可用，而不是文件夹级别的权限问题。请在 Developer Console 中添加并注册该函数。仅凭文件夹和代码仓库权限并不能使函数可执行。

[函数支撑的动作](https://www.palantir.com/docs/foundry/action-types/function-actions-overview/)是一种特殊情况：最终用户不一定需要对函数具有读取访问权限即可应用使用该函数的动作。管理员用户在配置动作使用函数时必须对函数具有读取访问权限。之后，用户将能够根据[动作级权限](https://www.palantir.com/docs/foundry/action-types/permissions/)应用该动作，无论他们对函数是否有访问权限。

### 对象加载权限

当函数加载对象数据时（无论是作为参数还是通过[对象搜索](https://www.palantir.com/docs/foundry/functions/api-object-sets/)），运行函数的最终用户的权限决定了加载哪些对象。对于使用行级权限保护的对象类型，这意味着执行同一函数的不同用户可能得到不同结果。此行为是设计使然——用户只应看到他们有权访问的对象，该行为使单个函数能够为对各个对象具有不同访问权限的用户工作。

> **⚠️ 注意：仅在读取时强制执行**
>
> 行和列访问控制（包括[受限视图](https://www.palantir.com/docs/foundry/security/restricted-views/)、[对象安全策略](https://www.palantir.com/docs/foundry/object-permissioning/object-security-policies/)和[属性安全策略](https://www.palantir.com/docs/foundry/security/property-security-markings/)）会在函数运行时筛选用户可以读取的内容。这些控制不会延伸到函数的输出。为了在数据向下游流动时持续保护数据，请将这些控制与[标记](https://www.palantir.com/docs/foundry/security/markings/)或[基于分类的访问控制](https://www.palantir.com/docs/foundry/security/classification-based-access-controls/)配合使用。完整模型请参阅[访问控制传播](https://www.palantir.com/docs/foundry/security/access-control-propagation/)。

## 扩展函数执行

管理员通过 [Control Panel](https://www.palantir.com/docs/foundry/administration/control-panel/) 中的 **Functions settings** 控制扩展执行能力。这些能力授予提升的访问权限，例如在函数内调用[动作](https://www.palantir.com/docs/foundry/action-types/function-actions-overview/)或获取存活时间（TTL）最长可达四小时的认证令牌。

要发布、执行或安装具有扩展能力的函数，管理员必须配置相应的允许列表：

- **允许发布扩展函数的代码仓库：** 只有此允许列表中的代码仓库才能发布具有扩展能力的函数。
- **允许以扩展能力执行的函数：** 只有此允许列表中的函数才能以扩展能力执行。Foundry 会在执行时检查此允许列表。
- **允许通过 Marketplace 安装扩展函数的项目：** 只有此允许列表中的项目才能成功从 [Marketplace](https://www.palantir.com/docs/foundry/marketplace/overview/) 安装具有扩展能力的函数。

如果发布、执行或 Marketplace 安装对具有扩展能力的函数返回权限错误，请让你的管理员验证相关允许列表。

---

*原文：[Permissions](https://www.palantir.com/docs/foundry/functions/permissions/)*
