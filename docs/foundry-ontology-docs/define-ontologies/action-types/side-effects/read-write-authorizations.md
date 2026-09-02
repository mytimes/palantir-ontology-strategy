# 读和写授权（Read and write authorizations）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/action-types/read-write-authorizations/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

> **ℹ️ 提示：Beta**
>
> 读和写授权处于 [Beta](https://www.palantir.com/docs/foundry/platform-overview/development-life-cycle/) 开发阶段，可能在你的注册实例上不可用。在活跃开发期间，功能和平台支持可能会发生变化。

读和写授权为动作类型增加安全边界。[读授权](#读授权)限制动作可以直接读取、或在被另一段逻辑调用时作为输入接收的带标记数据。[写授权](#写授权)为动作创建或修改的数据设定最低安全要求。这些授权是对用户已有权限和动作类型[提交条件](https://www.palantir.com/docs/foundry/action-types/submission-criteria/)的补充。它们不会授予访问权限，也不会取代已有权限或提交条件。

## 读授权

读授权为动作在执行期间可以读取或作为输入接收的数据提供额外的上限。读授权在 Foundry 将值加载到参数时生效，包括另一段逻辑调用动作时，以及函数支撑的动作执行读取时。如果数据超出配置的读授权，即使用户本可以访问该数据，动作也无法加载它。

应用动作的用户仍必须具有访问该数据的权限。如果没有为读授权配置任何标记，则除用户已有访问权限外，动作没有额外的读取边界。

## 写授权

写授权定义动作创建或修改的数据必须满足的最低安全要求。Foundry 会校验结果数据的安全性，如果结果低于配置的最低要求，则阻止该动作。

写授权不会向用户授予对标记的访问权限，不会自动添加任何已配置的标记，也不会使本不合法的编辑变为允许。用户必须具有所需权限、在适用时提供有效的标记值，并通过动作类型的提交条件。如果没有为写授权配置标记，此功能不会强制实施额外的最低输出安全要求。

对于由平台管理的写入（包括[动作日志](https://www.palantir.com/docs/foundry/action-types/action-log/)和[编辑历史](https://www.palantir.com/docs/foundry/object-edits/user-edit-history/)对象），不会强制实施已配置的写授权。这意味着应用于动作日志和编辑历史的安全级别可能低于写授权。

## 强制标记保证

[强制标记](https://www.palantir.com/docs/foundry/security/markings/#data-dependency)旨在沿数据依赖传播，使派生数据保留其输入的保护。对于动作，动作的逻辑设定应用于输出数据的安全级别。因此，动作产生的输出，其安全级别可以与所接收或读取的数据相同、更高或更低。读和写授权为动作的行为设定上限和下限。它们共同保证了动作输入与输出之间允许的安全差异。

将写授权配置为比读授权宽松，会允许动作写入安全级别低于其可读取数据安全边界的数据。这会有意切断强制标记传播，并可能导致数据解密（declassification）。每当读和写设置不同时，动作类型编辑器都会显示警告。

当读和写授权不同时，Foundry 会检查保存或发布动作类型的用户的权限。该用户必须具有对在读和写边界之间被切断的每个强制标记进行解密的权限。如果检查通过，动作类型可以执行已获批准的解密；Foundry 不会在运行时重复此权限检查。

> **🚨 警告**
>
> 读和写授权目前是可选的。如果未配置读授权，Foundry 在保存或发布动作类型时不会执行解密权限检查。因此，动作可以读取高于其写边界的数据并写入更宽松的数据，这可能导致数据溢出（data spill）。只要动作可能读取带标记数据，就应配置读授权。

## 按标记类别的授权规则

Foundry 根据标记的类别组合已配置的标记：

| 标记类别 | 读授权 | 写授权 |
| --- | --- | --- |
| 分类标记（Classification markings） | 遵循环境配置的层级以及合取（conjunctive）或析取（disjunctive）类别规则。 | 遵循环境配置的层级以及合取或析取类别规则。 |
| 其他强制标记 | 要求满足所有已配置的标记。 | 要求满足所有已配置的标记。 |
| 组织（Organizations） | 要求满足所有已配置的组织。 | 要求至少满足一个已配置的组织。 |

分类行为在不同环境之间可能有所不同。有关分类层级以及合取或析取类别的详细信息，请参阅[基于分类的访问控制](https://www.palantir.com/docs/foundry/security/classification-based-access-controls/#conjunctive-and-disjunctive-classification-marking-categories)。

## 配置读和写授权

在动作类型编辑器中，启用**访问约束（Access constraints）**以配置授权。启用访问约束会初始化两个授权且不含任何标记。禁用访问约束会清除两个授权。

![已启用访问约束，写入安全选项卡显示分类、强制标记和组织要求。](https://www.palantir.com/docs/resources/foundry/action-types/read-write-authorizations-write-security.png?width=800)

使用**写入安全（Write security）**和**读取安全（Read security）**选项卡分别配置每个授权。当设置不同时，编辑器会显示警告。如果某个授权包含你无法查看的值，这些值会显示为已脱敏（redacted）且无法编辑。

![读取安全选项卡，其中有一个分类选择，以及由读和写授权不同引起的解密警告。](https://www.palantir.com/docs/resources/foundry/action-types/read-write-authorizations-read-security.png?width=800)

提交动作之前，动作表单可以基于写授权显示**最低要求安全级别（Minimum required security）**。状态可以是：

- **安全检查通过（Security passed）：** 目标安全级别预计满足配置的最低要求。
- **安全检查未通过（Security not passed）：** 目标安全级别低于配置的最低要求。
- **无法确定（Can't be determined）：** Foundry 无法在提交前确定目标安全级别。
- **未设置（Not set）：** 未配置写授权标记。
- **已脱敏（Redacted）：** 用户无法查看已配置的授权。

![动作表单显示在提交前无法确定最低要求安全级别。](https://www.palantir.com/docs/resources/foundry/action-types/read-write-authorizations-minimum-security.png?width=500)

表单状态提供早期反馈。Foundry 在动作提交时执行权威校验，如果结果数据不满足写授权，则动作失败。

![当动作的数据不满足配置的分类或标记要求时显示的错误消息。](https://www.palantir.com/docs/resources/foundry/action-types/read-write-authorizations-submission-error.png?width=600)

---

*原文：[Read and write authorizations](https://www.palantir.com/docs/foundry/action-types/read-write-authorizations/)*
