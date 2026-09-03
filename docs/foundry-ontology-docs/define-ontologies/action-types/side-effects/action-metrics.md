# 动作指标（Action metrics）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/action-types/action-metrics/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

动作指标显示动作类型在过去 30 天内近实时的使用情况。你可以从 [Ontology Manager](https://www.palantir.com/docs/foundry/ontology-manager/overview/) 中动作类型的概览页面访问这些指标，或者在 [Workflow Lineage](https://www.palantir.com/docs/foundry/workflow-lineage/overview/) 中选择某次执行的动作节点来访问。可用指标如下：

- **成功/失败指标：**通过成功和失败计数监控动作的当前状态。这有助于快速识别问题并支持主动排查，使你能够在失败发生时立即处理。
- **P95 持续时间指标：**跟踪每个动作类型执行时长的第 95 百分位（P95）。该指标突显执行时间的较高区间，帮助你发现性能瓶颈并优化工作流，以实现稳定、高效的运行。

你还可以访问[运行历史](https://www.palantir.com/docs/foundry/aip-observability/run-history/)，它提供某个动作在过去七天内执行情况的完整视图。详细了解[本体与 AIP 可观测性能力](https://www.palantir.com/docs/foundry/aip-observability/overview/)。

![概览区域中动作指标的屏幕截图。](https://www.palantir.com/docs/resources/foundry/action-types/action-metrics-failures.png)

所有指标使用来自 Foundry 遥测服务（Foundry Telemetry Service，FTS）的最新数据近实时更新。这确保你能够获取最新信息，用于监控、调试和维护动作的健康状况。

你还可以使用 [Workshop](https://www.palantir.com/docs/foundry/workshop/overview/) 中的[可观测性图表组件](https://www.palantir.com/docs/foundry/workshop/widgets-observability-chart/)将动作指标直接嵌入运营应用中，以便在监控工作流其他部分的同时监控资源健康状况。

## 动作失败类型

显示动作指标不需要动作日志。与动作日志不同，动作指标会跟踪失败。

动作指标可能显示多种类别的失败。这些类别包括：

- **无效参数失败：**提交动作时使用了在该动作上下文中无效的一个或多个参数。
- **规模限制失败：**动作影响的对象数超过了允许的上限（默认通常为 10,000 个）。
- **认证失败：**用户未通过该动作的安全提交条件。
- **副作用失败：**动作因 Webhook 或配置错误的副作用而失败。
- **函数失败：**动作因底层函数失败而失败。此失败模式仅可能出现在函数支撑的动作中。
- **面向用户的函数失败：**支撑动作的函数抛出了旨在向用户显示的错误。此失败模式仅可能出现在函数支撑的动作中。
- **冲突失败：**动作因冲突（如并发修改）而失败。
- **未分类失败：**动作失败不属于上述任何类别。

## 权限

要查看动作指标，你必须是该动作的 `viewer`（查看者）。

---

*原文：[Action metrics](https://www.palantir.com/docs/foundry/action-types/action-metrics/)*
