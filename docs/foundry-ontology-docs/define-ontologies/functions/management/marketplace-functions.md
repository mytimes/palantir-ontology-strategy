# Marketplace 中的函数（Functions in Marketplace）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/marketplace-functions/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

你可以使用 [Foundry DevOps](https://www.palantir.com/docs/foundry/devops/overview/) 将你的函数包含在 [Marketplace 产品](https://www.palantir.com/docs/foundry/devops/core-concepts/#product)中，供其他用户安装和复用。

## 将函数添加到产品中

要将函数添加到产品中，请先[创建产品](https://www.palantir.com/docs/foundry/foundry-devops/create-products/)。然后，如下所示添加一个函数输出。

![添加函数输出。](https://www.palantir.com/docs/resources/foundry/functions/marketplace-add-function-output.png)

系统会提示你选择一个函数和一个版本。

![搜索函数。](https://www.palantir.com/docs/resources/foundry/functions/marketplace-function-search.png)

### 包含代码仓库的源代码

打包代码仓库中编写的函数时，支撑该函数的代码仓库会作为产品中的附加输出自动包含进来。虽然你可以选择将代码仓库连同其源代码一起打包，但对于不打算以[引导模式（bootstrap mode）](https://www.palantir.com/docs/foundry/foundry-devops/create-products/#installation-mode)安装的产品，我们通常不建议包含源代码。这是因为通过 Marketplace 安装的函数不需要其支撑源代码即可成功执行。此外，代码仓库并不保证开箱即可编译或构建成功。

如果你想在安装后对函数进行更改（例如修复 bug 或增强功能），我们建议对原始函数进行这些更改，发布新版本的 Marketplace 产品，然后升级安装。如果你不是已安装产品的所有者，应联系其维护者提交 bug 报告或功能请求。

### 扩展函数执行要求

使用扩展执行能力的函数有额外的 Marketplace 安装要求。这些能力包括在函数内调用[动作](https://www.palantir.com/docs/foundry/action-types/overview/)以及获取具有更长存活时间（TTL）的认证令牌。安装前，管理员必须将目标项目添加到 [Control Panel](https://www.palantir.com/docs/foundry/administration/control-panel/) 中 **Functions settings** 下的 **Extended function execution** 允许列表中。

有关允许列表的配置说明，请参阅[函数设置](https://www.palantir.com/docs/foundry/functions/functions-settings/)。

## 版本与 API 名称解析

函数通过 Marketplace 安装时，其版本和 API 名称有两种解析方式：去重模式和稳定模式。

### 去重模式

这是函数通过 Marketplace 安装时解析版本和 API 名称的历史默认行为。

在去重模式下，函数版本按以下方式解析：

- 函数由某次安装最初创建时，其第一个版本以 `0.1.0` 发布。
- 在后续安装（如升级）时，函数会在其最新版本的基础上递增次版本号后发布。例如，如果已安装函数的最新版本为 `1.1.0`，则下一次安装将以 `1.2.0` 发布。

> **ℹ️ 提示**
>
> [函数版本](https://www.palantir.com/docs/foundry/functions/functions-versioning/)是不可变的。换言之，函数的某个版本一旦发布，就无法被修改或覆盖。

函数 API 名称的解析方式如下：

- 如果该 API 名称已被安装所在本体中的另一个函数绑定，则会通过追加递增的整数后缀来去重。例如，如果 API 名称 `myFunction` 已被占用，该函数将以 API 名称 `myFunction1` 安装。如果该 API 名称也被占用，则以 `myFunction2` 安装，依此类推。
- 一旦已安装函数拥有某个 API 名称，在后续安装（如升级）中该函数会一直保持该 API 名称。

> **ℹ️ 提示**
>
> [函数 API 名称](https://www.palantir.com/docs/foundry/functions/query-functions/#api-name-validations)在每个本体中唯一。确切地说，如果一个本体中已存在 API 名称为 `myFunction` 的函数，则同一本体中不能存在另一个具有相同 API 名称的函数。

### 稳定模式

> **ℹ️ 提示：Beta**
>
> 稳定模式处于开发的 [Beta](https://www.palantir.com/docs/foundry/platform-overview/development-life-cycle/) 阶段，可能在你的注册实例上不可用。功能在活跃开发期间可能发生变化。请联系 Palantir 支持团队在你的注册实例上启用此功能。

> **⚠️ 注意**
>
> [计算模块函数](https://www.palantir.com/docs/foundry/compute-modules/functions/)不支持稳定模式，因为计算模块函数在 Foundry 中有自己的版本管理行为。

版本和 API 名称是函数 API 不可分割的组成部分。因此，在许多情况下，通过 Marketplace 打包和安装函数时最好保留它们。当函数与以静态方式引用函数依赖的上游应用（如 [Developer Console 应用](https://www.palantir.com/docs/foundry/developer-console/marketplace-installation/)）一起安装时，这一点尤其重要。

在稳定模式下，函数版本按以下方式解析：

- 已安装的函数始终以其打包时的版本发布。
- 如果已安装函数的该版本已存在，则会创建一个全新的函数；旧函数将被隐藏，其 API 名称将被移除。

> **ℹ️ 提示**
>
> 通过创建新函数来解决版本冲突会影响上游应用。如果上游应用是同一安装的一部分，它会自动更新为引用新函数。否则，你需要手动更新它。

函数 API 名称按以下方式解析：

- 已安装的函数始终以打包时的 API 名称发布。
- 如果该 API 名称已被另一个函数占用，将发生安装错误。要解决此冲突，你必须删除现有函数或更改其 API 名称。

## 已部署的函数

函数要么以无服务器执行模式运行，要么被[部署](https://www.palantir.com/docs/foundry/functions/functions-deployed/)到长生命周期容器中。Marketplace 会将函数代码仓库的执行模式与函数本身一起打包，因此已安装函数的运行方式与其打包环境中的运行方式相同。

### Marketplace 如何打包函数执行模式

函数执行模式按代码仓库配置，对于由同一部署支撑的所有函数，Marketplace 只打包*一次*。

对于处于部署模式的代码仓库，Marketplace 会捕获环境变量、资源请求与限制、扩缩容限制以及其他关键部署配置细节。

Marketplace 不会将容器镜像作为固定值打包。相反，容器镜像在安装时根据正在安装的函数版本解析，因此已安装的部署始终运行已安装函数的镜像。

要打包代码仓库处于部署模式的函数，该代码仓库必须存在一个部署，并且该部署必须正在运行你要打包的函数版本。否则，打包会失败。要解决此问题，请启动或更新部署，使其运行你要打包的版本，或者将代码仓库切换为无服务器执行。

### Marketplace 在安装期间如何应用配置

当你安装或升级产品时，Marketplace 会将打包的执行模式和部署配置应用到目标环境。这相当于解锁安装并手动配置执行方式，使其与产品打包时所在的环境相匹配。

如果打包的模式为*部署模式*，Marketplace 会在已安装代码仓库尚不存在部署时为其创建一个部署，将打包的部署配置应用到该部署，并启动部署。

如果打包的模式为*无服务器模式*，Marketplace 会将已安装的代码仓库设置为无服务器执行。如果该代码仓库已存在部署，Marketplace 会停止安装。

无服务器函数可能并非在每个注册实例上都可用。如果产品以无服务器模式打包函数，但目标环境不支持无服务器执行，则该函数会改为以部署模式安装。发生这种情况时，Marketplace 会使用默认配置创建并启动一个部署。这保证了已安装函数可以作为已部署函数运行。

> **⚠️ 注意：计算成本**
>
> 已部署函数只要部署在运行就会产生计算成本，而无服务器函数仅在执行时产生成本。因此，因目标环境不支持无服务器执行而以部署模式安装的无服务器函数，会产生长生命周期部署的计算成本。

### Marketplace 中已部署函数的限制

Marketplace 中的已部署函数具有以下限制。

#### 同一时间只能部署函数的一个版本

一个部署运行函数的单个版本。当你升级包含已部署函数的 Marketplace 产品时，函数会以新版本重新发布，并且该新版本会自动部署，取代先前部署的版本。

此次升级不会更新被升级安装之外的产品。如果某个外部产品（即既不包含在 Marketplace 产品中、也不属于[关联产品](https://www.palantir.com/docs/foundry/marketplace/linked-products/)的产品）引用了已部署函数的特定版本，其引用会继续指向旧版本。由于同一时间只能部署函数的一个版本，任何对函数旧版本的引用都将返回错误。

为避免这种情况，你必须：

- 将已部署函数的使用者打包在同一产品或[关联产品](https://www.palantir.com/docs/foundry/marketplace/linked-products/)中，以便它们与函数一起升级。
- 尽可能使用无服务器执行。无服务器函数可以按需执行函数的不同版本，因此升级不会使对早期版本的引用失效。

#### 升级期间部署会重启

对支撑已部署函数的长生命周期容器的某些更改（例如更新其配置的数据源）无法在容器运行时应用。因此，作为 Marketplace 产品升级的一部分，部署每次升级时都可能被停止并重新启动，这意味着函数可能会经历停机。

## 静态函数输入

> **⚠️ 注意**
>
> 此功能仅在 [TypeScript v1](https://www.palantir.com/docs/foundry/functions/typescript-v1-getting-started/) 中受支持。

你可以在安装时修改函数的部分行为，方法是提供一个本地定义的函数，覆盖随 Marketplace 产品一起发布的“静态”函数输入。为此，你可以使用 `@Static` 装饰器指定某个函数可以被覆盖。

```
import { Function, Static, Double } from "@foundry/functions-api";

export class MyFunctions {

    @Function()
    public async modifyNumberByStaticFoo(
        n: Double,
        @Static() staticFunctionInput: (num: Double) => Promise<Double> = this.defaultFoo
    ): Promise<Double> {
        return await staticFunctionInput(n);
    }

    private async defaultFoo(n: number) {
        return -n;
    }

}
```

打包函数时，任何静态输入都会在安装期间显示为函数输入。安装者随后可以提供自己的函数逻辑来覆盖默认行为。

> **⚠️ 注意**
>
> 不支持在被覆盖的静态函数中调用[查询](https://www.palantir.com/docs/foundry/functions/query-functions/)或[发起 API 调用](https://www.palantir.com/docs/foundry/functions/api-calls/)。

## 自定义别名

[自定义别名](https://www.palantir.com/docs/foundry/functions/custom-aliases/)存储字符串值，例如配置参数、功能开关或特定环境的设置。当你将带有自定义别名的函数添加到 Marketplace 产品时，这些别名会自动作为可配置参数出现在 **Inputs** 下。安装者无需修改函数源代码即可设置特定环境的值。

自定义别名在 TypeScript v2 和 Python 函数中受支持。与[静态函数输入](#静态函数输入)不同，自定义别名：

- 同时适用于 TypeScript v2 和 Python
- 允许安装者配置字符串值而非函数逻辑
- 在 Marketplace 安装体验中支持描述和预设值

有关在函数中定义和使用自定义别名的详情，请参阅[自定义别名](https://www.palantir.com/docs/foundry/functions/custom-aliases/)。

## 模型别名

你可以将通过[模型别名](https://www.palantir.com/docs/foundry/functions/model-aliases/)引用语言模型的函数添加到 Marketplace 产品中。Marketplace 对这些别名施加了特定限制。

> **⚠️ 注意**
>
> 模型别名无法在安装期间重新映射。如果别名引用的模型在目标环境中不可用，函数在运行时将无法解析该别名。

与安装期间显示为可配置参数的[自定义别名](#自定义别名)不同，Marketplace 在运行时使用源代码仓库中配置的确切模型 RID 来解析模型别名。请确保别名引用的模型在产品将要安装的每个目标环境中都可用。

## 已知问题

### Marketplace 函数中的接口输入

接受[接口](https://www.palantir.com/docs/foundry/interfaces/interface-overview/)作为输入参数的函数可能会在目标环境中抛出 `MarketplaceSdkObjectMappingNotFound` 错误。Marketplace 要求 SDK 绑定包含具体对象类型，而不仅仅是接口。

如果函数声明了接口输入，而目标环境包含实现该接口的具体类型的对象，SDK 无法找到具体类型映射。函数随后会在运行时失败。

**变通方案：** 确保目标环境中使用的具体对象类型在源环境中也存在，然后将其显式包含在 Marketplace 打包的 SDK 中。这会生成所需的映射，使函数能够解析并使用该对象类型。

---

*原文：[Functions in Marketplace](https://www.palantir.com/docs/foundry/functions/marketplace-functions/)*
