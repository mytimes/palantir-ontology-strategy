# 函数监控（Function monitoring）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/monitoring/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

可以对 Foundry 中的函数进行监控，以跟踪性能和可靠性。本页介绍函数可用的监控能力。

你还可以在 Ontology Manager 中查看实时[函数指标](https://www.palantir.com/docs/foundry/functions/function-metrics/)，包括每种函数类型的成功和失败次数以及 P95 时长。

## 可用的监控规则

Foundry 中的函数监控支持以下规则类型：

1. **函数时长 P95：** 当第 95 百分位执行时间超过阈值时发出告警。
2. **时间窗口内函数失败次数：** 当某个时间范围内失败总数超过阈值时发出告警。此规则跟踪所有失败类型。
3. **时间窗口内面向用户的函数失败次数：** 当某个时间范围内面向用户的失败次数超过阈值时发出告警。此规则仅跟踪函数代码抛出的面向用户的错误。
4. **时间窗口内非面向用户的函数失败次数：** 当某个时间范围内非面向用户的失败次数超过阈值时发出告警。此规则排除面向用户的错误，因此适用于监控基础设施和系统级故障。

有关每种规则类型的详细配置选项和参数，请参阅[监控规则参考文档](https://www.palantir.com/docs/foundry/monitoring-views/rules-reference/#function-rules)。

## 设置函数监控

要为你的函数设置监控，请按照创建监控视图和规则的标准流程操作：

1. 按照[监控视图概览文档](https://www.palantir.com/docs/foundry/monitoring-views/overview/#create-a-new-monitoring-view)中的说明创建监控视图。
2. 按照[添加监控规则](https://www.palantir.com/docs/foundry/monitoring-views/overview/#add-a-monitoring-rule)部分的说明为函数添加监控规则。
3. 配置适当的阈值和严重级别。
4. 按照[告警订阅指南](https://www.palantir.com/docs/foundry/monitoring-views/overview/#subscribe-to-alerts)设置告警通知。

![监控告警设置示例。](https://www.palantir.com/docs/resources/foundry/functions/monitoring-alerts.png)

### 动态范围

函数监控器支持将 **Workflow Lineage**、**Workshop** 和 **OSDK 应用**作为动态范围。选择其中一个范围时，监控器会自动跟踪该范围资源使用的所有函数，并随着函数的添加或移除自行调整，无需进一步干预。

![选择范围对话框，显示函数监控器的动态范围选项。](https://www.palantir.com/docs/resources/foundry/functions/functions-app-as-dynamic-scope-monitoring.png)

## 相关文档

- [监控规则参考](https://www.palantir.com/docs/foundry/monitoring-views/rules-reference/#function-rules)
- [监控视图概览](https://www.palantir.com/docs/foundry/monitoring-views/overview/)
- 用于告警的[外部系统集成](https://www.palantir.com/docs/foundry/monitoring-views/external-systems/)

---

*原文：[Function monitoring](https://www.palantir.com/docs/foundry/functions/monitoring/)*
