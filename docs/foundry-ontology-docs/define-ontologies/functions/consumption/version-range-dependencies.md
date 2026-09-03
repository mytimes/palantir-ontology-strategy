# 函数的版本范围依赖（Version range dependencies for functions）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/version-range-dependencies-for-functions/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

Workshop、Actions 和 Automate 可以依赖某个固定版本的函数，也可以依赖一个版本范围。版本范围支持运行时自动升级，从而减少开发工作量，并让[已部署函数](https://www.palantir.com/docs/foundry/functions/functions-deployed/)无需停机即可升级。

虽然版本范围依赖是一项强大的功能，但它也带有一定的风险（例如，会带来 [Actions 特有的权限影响](#actions-中的权限与溯源)）。本文档解释版本范围解析背后的机制，帮助你更好地理解这些风险，并就是否适合在你的应用中使用版本范围依赖做出明智的决定。

> **ℹ️ 提示**
>
> 本页面假定你已了解向后兼容性和语义化版本（Semantic Versioning）等主题。如果你不熟悉这些主题，请先阅读我们关于[函数版本管理](https://www.palantir.com/docs/foundry/functions/functions-versioning/)的文档。
>
> 你还应熟悉[语义化版本规范 ↗](https://semver.org/#spec-item-11)中定义的版本优先级规则。换言之，给定两个不同的版本，你应能判断哪个版本的优先级更低。例如，`1.0.0-rc.1` < `1.0.0` < `1.0.1` < `1.1.0` < `2.0.0`。

## 版本范围

在最简单的形式下，版本范围是一组版本不等式；如果一个版本满足范围中的所有不等式，就称该版本“满足（satisfy）”该范围。例如，版本 `1.2.0` 满足范围 `>=1.0.0 <2.0.0`。

> **ℹ️ 提示**
>
> 在内部实现中，函数版本范围的语义取自 NPM——JavaScript 生态系统中流行的包管理器。严格定义请参阅 [NPM 关于版本范围的文档 ↗](https://docs.npmjs.com/cli/v6/using-npm/semver#ranges)。

Workshop 和 Actions 等应用目前只允许包含向后兼容版本（即次版本号或补丁版本升级）的版本范围。

> **ℹ️ 提示**
>
> Workshop 和 Actions 使用的这种向后兼容范围，在 NPM 中的等价物是[脱字符范围（caret range）↗](https://docs.npmjs.com/cli/v6/using-npm/semver#caret-ranges-123-025-004)。

## 版本范围解析

除已部署函数外，当你以版本范围依赖某个函数时，系统会在运行时选择一个满足该范围的具体版本。具体而言，系统最终会选择满足范围的*最高*版本（新发布的版本可能需要几分钟才会生效）。

### 已部署函数

对于已部署函数，具体版本会解析为当前部署的版本（前提是该版本满足范围）。如果已部署版本不满足范围，则会返回错误。

## 风险

尽管会引导函数开发者遵循语义化版本规范和通用最佳实践，但在非主版本（non-major）发布中意外引入破坏性变更始终是可能的。

如果你的应用拉取到破坏性变更，它可能表现为各种问题，例如运行时故障或意外行为。

发现破坏性变更后，你应立即联系该函数的开发者，让他们[发布修复版本](https://www.palantir.com/docs/foundry/functions/functions-versioning/#accidentally-releasing-a-backward-incompatible-change-as-a-patch-or-minor-version)；与此同时，你应将函数依赖固定到最后一个正常工作的版本。

> **⚠️ 注意**
>
> 除已部署函数依赖外，如果你的应用有严格的正常运行时间要求、无法容忍任何中断，你应使用固定版本依赖。

> **ℹ️ 提示**
>
> 函数发布者可以通过[将稳定版本标签限制为受保护分支](https://www.palantir.com/docs/foundry/code-repositories/branch-settings/#restrict-stable-version-tags)来降低风险。该限制要求：在发布下游应用可能自动采用的稳定版本之前，必须先经过代码评审。

### Actions 中的权限与溯源

在[函数支撑的动作（Function-backed Actions）](https://www.palantir.com/docs/foundry/action-types/function-actions-overview/)中使用函数版本范围时，存在一些关于权限和溯源（provenance）的重要注意事项，它们可能影响动作的行为。有关这些影响的更多信息，请参阅 Actions 文档中关于[自动升级](https://www.palantir.com/docs/foundry/action-types/function-actions-getting-started/#auto-upgrades)的部分。

---

*原文：[Version range dependencies for functions](https://www.palantir.com/docs/foundry/functions/version-range-dependencies-for-functions/)*
