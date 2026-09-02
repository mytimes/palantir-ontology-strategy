# 对象下拉菜单安全注意事项（Object dropdown security considerations）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/action-types/dropdown-security/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

对象下拉菜单校验中的静态值筛选器会暴露给所有能够查看该动作类型的用户。使用这些筛选器有可能向无权查看被筛选对象的用户暴露属性值组合。依靠对象属性或参数来筛选对象集可以降低这一风险。这些值不会直接在界面中可见。

## 示例：数据隐私问题

举例来说，假设我们有一个带有 `Investigation Name`（调查名称）属性的 `Document`（文档）对象。在我们的动作类型中，我们在对象引用参数上添加了一个筛选器，仅显示 **Investigation Name** 为 `Area 51 Investigation` 的 **Documents（文档）**。

![对象下拉菜单安全顾虑](https://www.palantir.com/docs/resources/foundry/action-types/objectDropdownSecurityFilter.png)

在这里，我们可能会向无法查看这些文档的用户透露 `Area 51 Investigation` 是某些 `Document` 对象的一个属性值。

这仅适用于**静态值筛选器**。当按参数或另一个对象的属性筛选 `Investigation Name` 属性时，不存在对 `Area 51 Investigation` 的引用，因为：

- `Investigation Name` 参数由用户提供。不会向动作类型查看者暴露有关底层数据的任何信息。
- `Investigation Object` 参数将沿用该用户现有的对象可见性限制。

因此，这两种搜索查询都不构成数据隐私问题。

![对象下拉菜单属性筛选器](https://www.palantir.com/docs/resources/foundry/action-types/objectDropdownSecurityProperty.png)

## 技术细节

在大多数情况下，动作后端会对动作类型定义中的敏感信息进行脱敏，以避免暴露敏感属性值。例如，动作提交条件对无法编辑动作类型的用户隐藏。类似地，用户在界面中或在后端检查响应时，将无法看到动作类型定义中新的对象下拉菜单筛选器。

但是，在查看动作表单时，对象下拉菜单校验会被转换为对象集。这意味着用户可以查看包含该对象集的网络请求。在上面的示例中，用户将收到一个包含 `Investigation Name = 'Area 51 Investigation'` 筛选器的对象集 RID，这样一来，即使用户无法查看对应的任何对象，该属性值的存在也会暴露。

这意味着这些值对任何用户都**不会在界面中可见**。如果可见性比安全性更受关注，则可以忽略此警告。

---

*原文：[Object dropdown security considerations](https://www.palantir.com/docs/foundry/action-types/dropdown-security/)*
