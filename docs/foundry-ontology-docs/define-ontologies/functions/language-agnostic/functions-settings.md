# 函数设置（Functions settings）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/functions-settings/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

[Control Panel](https://www.palantir.com/docs/foundry/administration/control-panel/) 中的 **Functions settings（函数设置）** 页面允许管理员管理一组设置，这些设置控制函数在特定[空间（space）](https://www.palantir.com/docs/foundry/security/orgs-and-spaces/)内的行为方式，每个设置控制某项特定能力是否启用，详见下文各节。要访问该页面，请在你要配置的空间中导航到 **Control Panel > Functions settings（函数设置）**。

## 扩展函数执行

**Extended function execution（扩展函数执行）** 模态框允许特定函数以某些扩展能力执行：

- 在函数内部调用[动作](https://www.palantir.com/docs/foundry/action-types/overview/)。
- 获取有效期（TTL）延长至最长四小时的身份验证令牌。

![Control Panel 中的扩展函数执行设置，展示了控制函数扩展能力的三个允许列表。](https://www.palantir.com/docs/resources/foundry/functions/extended-function-execution.png)

由于这些能力会授予函数更高的访问权限，扩展函数执行能力通过模态框可折叠部分中配置的一组允许列表，对这些能力在何处启用提供细粒度控制。函数必须先满足相关允许列表的要求，才能以这些扩展能力发布、执行或安装。配置以下允许列表，以控制哪些仓库、函数和项目可以使用扩展函数执行。

### 允许发布扩展函数的仓库

此允许列表中的仓库是该空间内唯一可以发布具有扩展执行能力的函数的仓库。要添加仓库，请选择 **Add（添加）** 并搜索你要允许的仓库。

### 允许以扩展能力执行的函数

此允许列表中的函数是唯一以扩展能力执行的函数。该列表在执行时检查，因此函数必须保留在允许列表中才能继续以扩展能力执行。从已列入允许列表的仓库发布的新函数会自动传播到函数允许列表。要添加函数，请选择 **Add（添加）** 并搜索你要允许的函数。

### 允许通过 Marketplace 安装扩展函数的项目

此允许列表中的项目是唯一可以成功通过 [Marketplace](https://www.palantir.com/docs/foundry/marketplace/overview/) 安装具有扩展执行能力的函数的项目。要添加项目，请选择 **Add（添加）** 并搜索你要允许的项目。

---

*原文：[Functions settings](https://www.palantir.com/docs/foundry/functions/functions-settings/)*
