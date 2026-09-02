# 动作监控（Action monitoring）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/action-types/monitoring/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

可以监控 Foundry 中的动作，以跟踪性能和可靠性。本页说明动作可用的监控能力。

## 可用的监控规则

Foundry 中的动作监控支持两种关键规则类型：

1. **动作时长 p95：**当第 95 百分位执行时间超过阈值时发出警报。
2. **时间窗口内动作失败次数：**当某个时间范围内失败次数超过阈值时发出警报。

有关详细的配置选项和参数，请参阅我们的[监控规则参考文档](https://www.palantir.com/docs/foundry/monitoring-views/rules-reference/#action-rules)。

## 设置动作监控

要为你的动作设置监控，请按照创建监控视图和规则的标准流程操作：

1. 按照[监控视图概览文档](https://www.palantir.com/docs/foundry/monitoring-views/overview/#create-a-new-monitoring-view)中的说明创建监控视图。
2. 按照[添加监控规则](https://www.palantir.com/docs/foundry/monitoring-views/overview/#add-a-monitoring-rule)一节中的说明，为某个动作或动作类型添加监控规则。
3. 配置适当的阈值和严重级别。
4. 按照[警报订阅指南](https://www.palantir.com/docs/foundry/monitoring-views/overview/#subscribe-to-alerts)设置警报通知。

![监控警报设置示例。](https://www.palantir.com/docs/resources/foundry/action-types/monitoring-alerts.png)

### 动态范围

动作监控器支持将 **Workflow Lineage**、**Workshop** 和 **OSDK 应用**作为动态范围。选择其中一种范围后，监控器会自动跟踪该范围内资源使用的所有动作，并在动作添加或移除时自行调整，无需进一步干预。

![选择范围对话框，显示动作类型监控器的动态范围选项，包括 Workshop 模块、Workflow Lineage 和 Developer Console 应用。](https://www.palantir.com/docs/resources/foundry/action-types/app-as-dynamic-scope-monitoring.png)

## 相关文档

- [监控规则参考](https://www.palantir.com/docs/foundry/monitoring-views/rules-reference/#action-rules)
- [监控视图概览](https://www.palantir.com/docs/foundry/monitoring-views/overview/)

---

*原文：[Action monitoring](https://www.palantir.com/docs/foundry/action-types/monitoring/)*
