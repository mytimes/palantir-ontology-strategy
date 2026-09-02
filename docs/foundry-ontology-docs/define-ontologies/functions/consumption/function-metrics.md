# 函数指标（Function metrics）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/function-metrics/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

函数指标展示某个函数类型在过去 30 天内近实时的使用情况。你可以在 [Ontology Manager](https://www.palantir.com/docs/foundry/ontology-manager/overview/) 中[函数类型的概览](https://www.palantir.com/docs/foundry/ontology-manager/overview/#function-type-view)页面访问这些指标，也可以在 [Workflow Lineage](https://www.palantir.com/docs/foundry/workflow-lineage/overview/) 中通过选择某次执行对应的函数节点来访问。可用指标如下：

- **成功/失败指标：** 通过成功和失败计数监控函数的当前状态。这有助于快速识别问题并支持主动排障，让你能够在失败发生时立即处理。
- **P95 执行时长指标：** 跟踪每个函数类型的第 95 百分位（P95）执行时长。该指标突出显示执行时间的高位区间，帮助你检测性能瓶颈并优化工作流，使其运行稳定、高效。

你还可以访问[运行历史](https://www.palantir.com/docs/foundry/aip-observability/run-history/)，它提供某个函数在过去七天内执行情况的完整视图。在[本体与 AIP 可观测性](https://www.palantir.com/docs/foundry/aip-observability/overview/)中了解更多。

![概览区块中函数指标的屏幕截图。](https://www.palantir.com/docs/resources/foundry/functions/function-metrics.png)

所有指标使用来自 Foundry 遥测服务（Foundry Telemetry Service，FTS）的最新数据近实时更新。这确保你在监控、调试和维护函数健康状况时能够访问最新的信息。

你还可以使用 [Workshop](https://www.palantir.com/docs/foundry/workshop/overview/) 中的[可观测性图表组件](https://www.palantir.com/docs/foundry/workshop/widgets-observability-chart/)将函数指标直接嵌入运营应用中，以便与工作流的其余部分一同监控资源健康状况。

## 函数失败类型

函数指标可能显示多种类别的失败。这些类别包括：

- **运行时失败（Runtime failure）：** 执行函数时发生意外错误，通常由函数代码中的 bug 或未处理的情况导致。
- **超出资源限制（Resource limit exceeded）：** 函数影响的对象数量超过了允许的上限（默认通常为 10,000）。
- **面向用户的错误（User facing error）：** 发生了专门用于向用户展示的错误，通常会就问题所在或如何修复提供指导。
- **输入无效错误（Invalid inputs error）：** 提供给函数的一个或多个输入无效，或不满足所需条件。
- **输出无效错误（Invalid output error）：** 函数产生的输出无效，或不符合预期的格式或规则。
- **不允许加载数据错误（Data loading not allowed error）：** 函数执行尝试加载数据（包括对象、对象集、用户或用户组），但不允许这样做。
- **编辑了未声明对象类型错误（Undeclared object types edited error）：** 函数执行尝试更新、创建或删除某个对象，但该对象的对象类型未在函数规范中声明。
- **结构化错误（Structured error）：** 函数执行遇到了其规范中定义的结构化错误。
- **部署错误（Deployment error）：** 由于函数的部署出现错误，函数执行失败。
- **一致性快照错误（Consistent snapshot error）：** 由于一致性快照错误，函数未能执行。

## 设置监控告警

你还可以配置监控规则，以便在性能下降或发生失败时接收告警。函数监控支持针对 P95 函数时长以及某个时间窗口内函数失败数量的告警。有关配置说明，请参阅[监控函数](https://www.palantir.com/docs/foundry/functions/monitoring/)。

## 权限

要查看函数指标，你必须是该函数的 `viewer`。

---

*原文：[Function metrics](https://www.palantir.com/docs/foundry/functions/function-metrics/)*
