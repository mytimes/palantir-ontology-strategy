# 触发调度构建（Trigger schedule build）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/action-types/trigger-schedule-build/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

[调度](https://www.palantir.com/docs/foundry/data-integration/schedules/)定义了一组资源，Foundry 会在[构建](https://www.palantir.com/docs/foundry/data-integration/builds/)过程中重新计算这些资源。通过在动作类型上配置**调度规则（schedule rule）**，你可以在每次应用动作时触发该调度的构建。这使本体中的最终用户工作流能够重新计算数据集，而无需用户导航到 [Data Lineage（数据血缘）](https://www.palantir.com/docs/foundry/data-lineage/overview/) 或 [Builds 应用](https://www.palantir.com/docs/foundry/data-integration/application-reference/#builds)。

当动作类型包含调度规则时，动作的本体编辑在构建开始*之后*应用。编辑不会等待构建完成。相反，动作会触发构建、捕获调度运行 RID，并立即应用其余规则，包括本体编辑。

## 配置调度规则

向动作类型添加调度规则并选择一个调度。该调度必须处于[项目范围模式](https://www.palantir.com/docs/foundry/data-integration/schedules/#project-scope)。

![Ontology Manager 中的动作类型配置页面。正在添加一个调度规则。](https://www.palantir.com/docs/resources/foundry/action-types/advanced-schedule-action-type-rule.png)

如果所选调度是[参数化](https://www.palantir.com/docs/foundry/building-pipelines/parameterization/)的，你必须为每个调度参数提供一个值。当动作被应用时，解析后的参数值会传递给调度，并转发给构建中底层的参数化转换。

调度规则与[并行化参数化调度](https://www.palantir.com/docs/foundry/building-pipelines/parameterization/#parallelized-mode-advanced)配合使用时尤其有用。请参阅[参数化文档](https://www.palantir.com/docs/foundry/building-pipelines/parameterization/#use-action-types-for-parallelized-schedules)，了解如何在本体中将动作用于并行化调度。

## 权限

动作的[提交条件](https://www.palantir.com/docs/foundry/action-types/submission-criteria/)管理通过动作触发调度所需的权限。如果用户满足动作提交条件，他们无需对该调度拥有任何直接权限即可运行该调度。

> **ℹ️ 提示**
>
> Foundry 会在调度第一次被引用时以及每次调度规则被编辑时，检查用户是否有权运行该调度。从动作类型引用调度，会将运行调度的控制权从调度委托给动作类型。此后，任何能够在该动作类型上管理动作的人都可以控制谁能触发该调度。

## 跟踪构建进度

当调度规则被触发时，产生的调度运行由一个**调度运行 RID（schedule run RID）**标识。该 RID 会作为一个值暴露出来，可在动作类型的本体编辑规则中引用，让你可以将其写入被编辑对象的字符串属性。当你想在对象上保留由该动作触发的构建记录时，这非常有用。

要捕获调度运行 RID，请在同一动作类型上配置 **Modify object（修改对象）** 或 **Create object（创建对象）** 规则，并将目标对象的某个字符串属性映射到调度规则提供的调度运行 RID 值。

![Ontology Manager 中的动作类型配置页面。已添加一个调度规则，并且调度运行 RID 通过 Create object（创建对象）规则写入了一个字符串属性。](https://www.palantir.com/docs/resources/foundry/action-types/build-schedule-run-rid-property.png)

要将存储的 RID 渲染为实时构建状态指示器，请对该属性应用[资源 RID 格式](https://www.palantir.com/docs/foundry/object-link-types/value-formatting/#supported-value-formatting)。启用格式后，Foundry 将 RID 值显示为一个链接，带有反映构建当前状态的图标和文本：`Running`（运行中）、`Ignored`（已忽略）、`Failed`（失败）或 `Succeeded`（成功）。

---

*原文：[Trigger schedule build](https://www.palantir.com/docs/foundry/action-types/trigger-schedule-build/)*
