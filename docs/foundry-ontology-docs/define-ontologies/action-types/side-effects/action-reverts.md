# 回滚或撤销动作（Revert or undo actions）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/action-types/action-reverts/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

[Ontology Manager](https://www.palantir.com/docs/foundry/ontology-manager/overview/) 中的动作回滚允许在动作应用后立即回滚（即撤销）该动作。你可以在任何动作成功应用后的成功消息中选择**撤销（Undo）**来回滚动作。

新动作默认可回滚。

> **ℹ️ 提示**
>
> 动作回滚仅适用于 Object Storage v2；也就是说，只有在 [OSv2](https://www.palantir.com/docs/foundry/object-backend/object-storage-v2-breaking-changes/) 中修改或创建对象类型的动作才能被回滚。如果你的对象类型当前未存储在 Object Storage v2 中，可以按照本[指南](https://www.palantir.com/docs/foundry/object-backend/osv1-osv2-migration/#migrate-from-object-storage-v1-phonograph-to-object-storage-v2)进行迁移。

## 配置可回滚的动作

目前，动作只能由应用该动作的用户回滚。

在动作的**表单（Form）**选项卡中，打开**允许在动作提交后回滚（Allow revert after action submission）**开关。此开关正确配置并保存到本体后，你的动作即可被回滚。

![表单区域中动作回滚的屏幕截图](https://www.palantir.com/docs/resources/foundry/action-types/action-reverts-form-button.png)

对于 2024 年 5 月之后创建且仅修改 OSv2 对象类型的动作，**表单**选项卡中的**允许在动作提交后回滚**开关将默认启用。
如果动作在 2024 年 5 月之前已存在且修改 OSv2 中的对象类型，动作回滚默认不会开启，但可以手动启用。

如果动作仅修改 OSv1 对象类型，你将无法回滚该动作。

## 回滚动作

> **ℹ️ 提示：回滚动作**
>
> 下方的提示弹窗（toast）是你回滚动作的唯一机会。执行删除动作时尤其要注意这一点。

回滚成功后，用户将看到与原始动作成功提示类似的提示弹窗，如下所示。

已应用的编辑：

![提示弹窗显示：“Edits successfully reverted”（编辑已成功回滚）。](https://www.palantir.com/docs/resources/foundry/action-types/action-reverts-revert-action.png)

已回滚的编辑：

![提示弹窗显示：“Edits successfully applied”（编辑已成功应用）。](https://www.palantir.com/docs/resources/foundry/action-types/action-reverts-edits-reverted.png)

## 注意事项

在某些情况下，动作回滚可能失败：

- 一旦对对象进行了任何后续编辑（即使编辑的是不同属性），对该对象的动作就无法回滚。换言之，只有当动作是对某个对象的最近一次编辑时，该动作才能被回滚。
- 如果在动作提交后动作回滚被关闭，即使之后又重新开启，该动作也无法回滚。

动作回滚只会回滚对对象实例的编辑，但不会回滚通知或 Webhook 等副作用，也不会像已应用的动作那样调用它们。

### 在没有回滚动作提示弹窗的情况下撤销删除动作

如果执行了删除动作且你希望撤销删除，但回滚动作的提示弹窗已不再可用，唯一的补救措施是：

- 迁移到新的对象类型，并使用函数复制所需的编辑；或者
- 丢弃该对象类型上的所有编辑。

---

*原文：[Revert or undo actions](https://www.palantir.com/docs/foundry/action-types/action-reverts/)*
