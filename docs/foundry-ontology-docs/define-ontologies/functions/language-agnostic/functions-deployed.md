# 部署函数（Deploy functions）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/functions-deployed/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

## 前提条件

本指南要求你已经编写并发布了一个 Python 或 TypeScript v2 函数。教程请参阅 [Python 函数入门](https://www.palantir.com/docs/foundry/functions/python-getting-started/)或 [TypeScript v2 函数入门](https://www.palantir.com/docs/foundry/functions/typescript-v2-getting-started/)文档。

## 在已部署与无服务器执行模式之间选择

如果你的注册实例已启用无服务器函数，新仓库将默认使用它。对于大多数用例，我们通常推荐无服务器函数。虽然已部署函数在某些情况下可能有用，但无服务器执行模式需要的维护更少，并且可以避免长期运行部署所带来的成本。

已部署函数具备一些无服务器函数不可用的能力：

- 已部署函数的长期运行特性意味着，如果函数能够容忍重启，就可能使用本地缓存。
- 无服务器函数支持使用所提供的源对象中的客户端访问[外部数据源](https://www.palantir.com/docs/foundry/functions/api-calls/)，但不支持第三方客户端。你必须部署函数，才能使用第三方客户端发起外部 API 调用。
- 已部署函数支持 GPU 分配，通过并行处理加速计算密集型的模型训练和推理工作流，而无服务器函数不支持。
- 已部署函数可以访问完整的服务发现 API，而无服务器函数只能访问有限的服务发现 API 子集，例如 `api_gateway` 和 `telemetry`。

已部署函数也有一些不适用于无服务器执行的限制：

- 无服务器函数支持按需执行同一函数的不同版本，使升级更安全。对于已部署函数，你一次只能运行一个函数版本。
- 无服务器函数仅在执行时产生成本，而已部署函数只要部署处于运行状态就会产生成本。
- 无服务器函数需要的前期设置和长期维护更少，因为基础设施是自动管理的。

要为你的注册实例启用无服务器函数，请联系你的 Palantir 管理员。

## 架构

函数可以利用按需资源在无服务器模式下运行，也可以部署到长期运行的容器中。

> **✅ 说明**
>
> 如果你的注册实例已启用无服务器函数，我们建议使用无服务器函数而非已部署函数。虽然[在某些情况下已部署函数很有用](#在已部署与无服务器执行模式之间选择)，但无服务器执行器通常更灵活。

函数部署后，系统会创建一个长期运行的环境来处理传入的执行请求。该环境会根据请求量扩缩容，并偶尔被自动化流程重启。单个仓库中的所有函数由同一个部署托管。

函数通过 Marketplace 发布时，其执行模式和部署配置会被打包并复现。有关详情和限制，请参阅 [Marketplace 中的已部署函数](https://www.palantir.com/docs/foundry/functions/marketplace-functions/#deployed-functions)。

> **⚠️ 注意：计算成本**
>
> 已部署函数会因运行中的部署产生计算成本。无服务器函数仅在执行时产生成本。

## 部署函数

按照以下步骤配置并部署函数：

1. 打开你的函数仓库并导航到 **Branches（分支）** 选项卡，然后选择 **Tags and releases（标签与发布）**。
2. 将鼠标悬停在你要部署的函数上，然后选择 **Open in Ontology Manager（在 Ontology Manager 中打开）**。

![在 Ontology Manager 中打开所选函数。](https://www.palantir.com/docs/resources/foundry/functions/python-functions-open-ontology-manager-v2.png)

1. 从左侧的版本选择器中选择你要使用的函数版本。
2. 选择 **Configure execution（配置执行）**。

![为函数配置执行。](https://www.palantir.com/docs/resources/foundry/functions/python-functions-configure-execution.png)

1. 如果你的环境中启用了无服务器函数，你会看到在无服务器和已部署之间切换的选项。如果未选择且不存在部署，默认使用无服务器。

![无服务器模式下函数的设置。](https://www.palantir.com/docs/resources/foundry/functions/python-functions-serverless-mode-configuration.png)

1. 选择 **Deployed（已部署）** 执行模式选项。
2. 如果该函数不存在部署，选择 **Create deployment（创建部署）**。

![已部署模式下函数的设置（尚无现有部署）。](https://www.palantir.com/docs/resources/foundry/functions/python-functions-create-deployment.png)

1. 首次创建部署时，配置将采用默认值。你可以滚动到页面末尾查看完整配置。

![已部署模式下函数的设置。](https://www.palantir.com/docs/resources/foundry/functions/python-functions-deployed-mode-configuration.png)

1. 根据需要修改部署配置。你可以配置以下内容：
  - 分配给部署的计算资源，包括 CPU、GPU 和内存。
  - 基于请求负载进行自动扩缩容的下限和上限。
  - 部署启动时将为其设置的环境变量。
  - 函数在返回超时错误之前允许运行的总时长。与其他部署设置不同，超时是针对每个函数版本单独配置的。

![修改已部署模式下函数的内存分配。](https://www.palantir.com/docs/resources/foundry/functions/python-functions-modify-deployment-memory-allocation.png)

1. 选择 **Save and start deployment（保存并启动部署）** 以保存更改并启动部署。你也可以选择 **Save without starting deployment（保存但不启动部署）**，仅保存配置而不启动部署。

![为已部署模式下的函数保存并启动部署。](https://www.palantir.com/docs/resources/foundry/functions/python-functions-save-and-start-deployment.png)

1. 如果你选择了 **Save and start deployment（保存并启动部署）** 选项，则需要等待托管该函数的部署启动。这可能需要几分钟时间。
2. 要验证部署是否正在运行，请导航到包含该函数的代码仓库并运行该函数。函数应成功执行并返回预期结果。

![在已部署模式下运行函数。](https://www.palantir.com/docs/resources/foundry/functions/python-functions-run-deployed-function.png)

---

*原文：[Deploy functions](https://www.palantir.com/docs/foundry/functions/functions-deployed/)*
