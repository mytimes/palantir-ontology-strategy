# 权限（Permissions）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/action-types/permissions/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

权限以下列方式应用于动作类型：

- 谁可以查看给定的动作类型？
- 谁可以编辑给定的动作类型？
- 谁可以使用给定的一组参数应用某个动作类型？

## 应用动作

应用动作类型的能力取决于其所编辑的对象类型和链接类型的配置。在所有情况下，提交动作的用户必须能够查看被编辑的对象类型和链接类型及其数据源，并通过[提交条件](https://www.palantir.com/docs/foundry/action-types/submission-criteria/)。如果对象类型仅允许通过动作进行编辑，则用户可以对其能够查看的所有对象进行编辑。对于允许通过动作之外的方式进行编辑的对象类型和链接类型，如果该对象类型或链接类型由数据集支撑，则用户还需要对回写数据集（writeback dataset）拥有编辑权限。如果对象类型或链接类型由[受限视图](https://www.palantir.com/docs/foundry/security/restricted-views/)支撑，则用户需要满足编辑策略。

> **ℹ️ 提示**
>
> 使用侧边栏中的 **Check access（检查访问权限）** 面板可以检查用户对 Workshop 模块的访问权限，包括对其依赖的动作类型及其提交条件的访问权限。更多信息请参阅[检查访问权限面板文档](https://www.palantir.com/docs/foundry/security/checking-permissions/)。

> **⚠️ 注意：仅在读取时强制执行**
>
> 行级和列级访问控制（包括[受限视图](https://www.palantir.com/docs/foundry/security/restricted-views/)、[对象安全策略](https://www.palantir.com/docs/foundry/object-permissioning/object-security-policies/)和[属性安全策略](https://www.palantir.com/docs/foundry/security/property-security-markings/)）会在用户调用动作时筛选其可读取的数据。这些控制不会延伸到动作的写入。为了在数据向下游流动时持续保护数据，请将这些控制与[标记（marking）](https://www.palantir.com/docs/foundry/security/markings/)或[基于分类的访问控制（Classification-based Access Control）](https://www.palantir.com/docs/foundry/security/classification-based-access-controls/)配合使用。完整模型请参见[访问控制传播](https://www.palantir.com/docs/foundry/security/access-control-propagation/)。

### 提交条件

动作提交条件可以细粒度地控制谁可以运行动作。简单的提交条件可以要求特定的用户 ID 或用户组 ID，并且可以与参数中的信息组合使用。更多信息请参见[提交条件文档](https://www.palantir.com/docs/foundry/action-types/submission-criteria/)。

### 读和写授权

读和写授权限制动作可以直接读取或从其他逻辑接收为输入的带标记数据，并对其创建或修改的数据强制执行最低安全要求。更多信息请参见[读/写授权文档](https://www.palantir.com/docs/foundry/action-types/read-write-authorizations/)。

### 对象编辑权限

对象编辑既可以被锁定为仅允许通过动作进行编辑，也可以重新开放为允许通过动作、Foundry Forms、直接在 Object Explorer 中编辑以及 API 调用来编辑。为了在众多工作流中实施一致的安全范式，默认情况下，新对象类型仅允许通过动作进行编辑。不建议在新的使用场景中采用其他编辑形式。

对于仅允许通过动作编辑的对象类型，提交动作的用户只需对被编辑的对象拥有 `Read`（读取）访问权限。这意味着用户有可能创建出自己无法查看的对象。

相比之下，当由数据集支撑的对象类型可以通过动作、Foundry Forms、直接在 Object Explorer 中编辑以及 API 调用进行编辑时，提交动作的用户必须对所有被编辑对象的回写数据集拥有 `Edit`（编辑）权限。拥有 `Edit` 权限的用户将能够查看回写数据集中的所有数据。

因此，不建议将对象类型设置为可通过动作、Foundry Forms、直接在 Object Explorer 中编辑以及 API 调用进行编辑，因为仅仅为了对象编辑而授予 `Edit` 权限，可能会向用户暴露超出完成本体编辑工作流所需的数据。

无论采用哪种回写设置，动作类型的配置都不会显示受影响的底层对象类型上的权限设置；配置动作类型的人员必须确保这些权限正确无误。

将对象类型的编辑权限更新为“仅允许通过动作编辑”不会移除历史上的非动作编辑，但会阻止来自 Foundry Forms、直接在 Object Explorer 中编辑以及 API 调用的进一步编辑。

![建议使用“仅允许通过动作编辑”设置。](https://www.palantir.com/docs/resources/foundry/action-types/recommended-writeback-setting.png)

[了解有关回写权限的更多信息。](https://www.palantir.com/docs/foundry/object-permissioning/configuring-rv-access-controls/)

## 副作用权限

任何能够设置动作的用户都可以配置副作用。

- Webhook 副作用默认不启用。在动作设置页面使用 Webhook 插件之前，需要先在 Data Connection 应用中配置该插件，这需要额外的权限。有关在你的 Foundry 实例上使用 Webhook 的任何问题，请联系你的 Palantir 代表。

提交条件必须照常通过；如果动作提交条件未通过，则不会触发副作用。

接收人必须有权访问通知中包含的任何对象数据。

- 如果用户无权访问通知内容中包含的所有数据，则不会向其发送通知。
- 如果有多个接收人，其中一些人缺少通知所含数据的正确权限，则只有具有足够权限的用户会收到通知。
- 如果通知因任何原因发送失败，编辑仍可能成功。

执行动作的用户必须能够查看将接收通知的用户和/或用户组。

---

*原文：[Permissions](https://www.palantir.com/docs/foundry/action-types/permissions/)*
