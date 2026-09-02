# 通知（Notifications）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/action-types/notifications/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

可以通过 **添加新规则（Add new rule）** 下拉菜单向动作添加通知。[了解有关如何添加通知的更多信息。](https://www.palantir.com/docs/foundry/action-types/set-up-notification/)

![“Add new rule（添加新规则）”下拉菜单](https://www.palantir.com/docs/resources/foundry/action-types/side_effects_notification_tutorial_edit_action_rules.png?width=500)

配置通知需要指定[接收人](#接收人)和[内容](#内容)。以下各节将详细介绍这些选项。

## 接收人

配置通知的 **接收人（Recipients）** 选项，可以指定动作运行时接收通知的 Foundry 用户集合。通知将单独发送给每位接收人。不支持在电子邮件通知中将用户添加为抄送（CC，carbon copy）接收人。

支持以下几种指定接收人的方式：

![接收人下拉菜单](https://www.palantir.com/docs/resources/foundry/action-types/side_effects_recipients_selector.png?width=400)

- **静态（Static）：**在配置中，你可以选择一组用户或用户组，动作运行时他们将始终收到通知。
- **来自参数（From a parameter）：**如果动作有一个类型为 Foundry 用户或用户组 ID 的参数，你可以将其指定为通知的接收人。
  - 这可用于让发送者在使用该动作的应用或模块的用户界面中选择一个或多个接收人，或用于自动检测并向运行该动作的用户发送通知。
- **来自对象参数的属性（From an attribute of an object parameter）：**如果动作有一个对象参数，且该对象的某个属性包含 Foundry 用户或用户组 ID，你可以将参数的该属性指定为接收人。对于 Foundry 用户 ID 和用户组 ID 的列表同样适用。
- **来自函数（From a function）：**如果以上选项不能覆盖你的用例，你可以编写一个自定义函数，接收动作参数并返回应收到通知的用户或用户组列表。[了解有关如何编写返回用户或用户组列表的函数的更多信息。](https://www.palantir.com/docs/foundry/functions/types-reference/#users-groups-and-principals) 基于函数指定接收人的用例示例包括：
  - 组合其他接收人选项；例如，通知从对象参数属性中指定的 `assignee`（经办人），同时始终通知一组静态的额外接收人。
  - 基于其他参数或参数的属性值选择接收人；例如，每当 EMEA 地区出现新任务时通知一组用户，每当北美地区出现新任务时通知另一组用户。
  - 任何不适合结构化选项的其他自定义逻辑。

> **ℹ️ 提示**
>
> 接收人可以更改通知的投递偏好。例如，一位用户可能选择仅在 Web 浏览器中接收通知，而另一位用户可能选择同时接收平台内提示（toast）和电子邮件。如果用户在其个人偏好设置中关闭了动作通知，他们将不会收到通知。不过，他们登录 Foundry 后仍可在工作区（Workspace）中通过“Notifications（通知）”然后“See All（查看全部）”查看自己的通知。

## 内容

自定义通知内容有多种选项。内容可以通过*模板（Template）*配置，也可以通过自定义*函数*提供。选择模板内容后，你可以直接在配置对话框中配置全部内容。函数内容则要求你拥有一个已发布的函数，该函数返回相应的通知类型。

![内容类型下拉菜单](https://www.palantir.com/docs/resources/foundry/action-types/side_effects_content_selection_dropdown.png)

### 内容组成部分

1. **主题（Subject）：**通常，内容包含一个主题行。默认情况下，所有投递机制使用相同的主题。
2. **正文（Body）：**通知的正文。对于平台内通知，它显示在通知提示（toast）中；对于电子邮件，它渲染在邮件正文中。
3. **链接（Link）：**你可以指定一个链接。它将以按钮形式显示在通知正文内容的下方。按钮文字可以自定义。
  - 配置链接时有以下选项：
    - 链接到现有对象参数
    - 链接到 Workshop 应用
    - 链接到 Carbon 工作区
    - 链接到新创建的对象
4. **高级电子邮件配置（Advanced Email Configuration）：**配置通知时，你可以指定通过电子邮件投递通知时使用的自定义内容正文。此选项允许你使用 HTML 实现平台内通知不支持的更高级格式。预览将展示通知的样式（不包含任何参数引用）。只有当接收人将偏好设置为通过电子邮件接收通知时，他们才会收到此内容。

> **ℹ️ 提示**
>
> 在上述主题、正文和链接中，可以使用三层花括号（triple handlebars）引用参数和用户属性。编辑某个部分时，点击可用参数之一，将自动生成该参数或用户属性对应的正确花括号引用。

1. **来自函数（From a Function）：**选择“来自函数”时，你不需要配置上面列出的各个部分。相反，你必须提供一个函数，该函数返回一个 `Notification` 对象，其相应属性指定自定义内容的每个部分。如果满足以下任一情况，你可能需要使用函数：
  - 通知内容因接收人或动作的输入参数不同而完全不同。
  - 你希望电子邮件和平台内通知使用不同的主题行。
  - 你希望使用完整的链接 URL，包括指向 Foundry 之外的外部系统或应用的链接。
  - 你希望在渲染内容时执行环绕搜索（Search Around）、聚合，或查询参数所提供数据之外的数据。
  - 你有任何其他无法通过模板内容选项实现的自定义需求。

关于 `Notification` 返回类型的更多信息，请参见[函数文档](https://www.palantir.com/docs/foundry/functions/configure-notifications/)。

> **ℹ️ 提示**
>
> 用于生成通知内容的任何本体数据都将反映当前动作的编辑应用之前的本体状态。若要让通知接收人访问特定对象的最新状态，可以在通知中嵌入通过对象参数引用的对象的链接，或新创建对象的链接（前提是这些对象通过“创建对象”规则创建，而非通过函数创建）。

---

## 配置示例

以下是一个通知的配置示例。

![带标注的通知配置示例](https://www.palantir.com/docs/resources/foundry/action-types/side_effects_numbered_example_configuration.png?width=400)

1. **接收人**配置
2. **内容**配置
  - 从模板（直接在本体应用对话框中配置）或函数（指定一个返回完整 `Notification` 对象的函数）中选择。
3. 模板通知的**主题**行。
4. 基于动作可用参数的可用**参数**。点击某个参数即可生成引用该参数的 `{{{}}}` 语法。
5. 模板通知的**正文**内容。
6. 模板通知的**链接**配置（可选）。
7. 模板通知的**电子邮件自定义 HTML 内容**（可选）。

---

## 其他关键信息

### 接收人数量上限

- 使用“来自函数”选项渲染通知内容时，最多支持 50 位接收人。在内容配置选项下选择“来自函数”时，配置面板中会出现警告，并且每次运行动作时都会检查接收人数量。如果接收人数量超过上限，将显示红色错误提示，动作将运行失败。
- 使用“模板”选项直接在配置对话框中配置内容时，单个动作通知最多支持 500 位接收人。

![函数渲染内容的最大接收人数量警告](https://www.palantir.com/docs/resources/foundry/action-types/side_effects_function_content_max_recipients.png)

### 内容长度限制

- 主题最长为 250 个字符。
- 正文最长为 1,000 个字符。为电子邮件渲染自定义 HTML 内容时，最长为 51,200 个字符。

请注意，这些内容长度上限会在通知渲染时进行校验并截断。这意味着如果渲染的内容是动态的（例如，通知内容包含对象数据），任何超过允许最大长度的内容都将被截断，并以末尾的 `...` 标示。

### 严格脱敏

如果你的 Foundry 实例对出站电子邮件通知启用了“严格脱敏（Strict Redaction）”或“用户组脱敏（Group Redaction）”，则不会渲染自定义通知内容。用户将收到如下所示的通用消息。选择“View（查看）”会将他们引导至 Foundry，在那里可以查看完整的通知内容。[了解有关 Foundry 中电子邮件内容脱敏的更多信息。](https://www.palantir.com/docs/foundry/email/email-content-redaction/)

![严格脱敏的电子邮件默认内容](https://www.palantir.com/docs/resources/foundry/action-types/side_effects_redacted_email_content.png)

### 接收人用户账户

- 用户组将被解析为单个用户，以便在发送通知之前检查数据权限。
- Foundry 用户 ID 和用户组 ID 可以在 Account（账户）下的 Settings（设置）中找到。选择静态接收人集合时，通知配置界面提供了用户和用户组选择器。该选择器仅显示配置动作的人员拥有足够权限的用户和用户组。
- 如果通过引用对象属性来配置接收人，请确保该属性以字符串形式存储 Foundry 用户 ID 或用户组 ID。你可以使用条件格式来显示关联的用户或用户组显示名（更多详情请参见[值格式文档](https://www.palantir.com/docs/foundry/object-link-types/value-formatting/)）。
- 不支持直接向电子邮件地址发送通知。

### 指向新创建对象的链接

链接新对象时必须引用该新对象的主键，因为在通知渲染时对象 RID 尚未生成。

**示例：**你有一个创建新 `task`（任务）对象的动作，创建任务时会生成一个唯一 ID。在动作通知中，你使用 [Object Explorer 提供的参数选项](https://www.palantir.com/docs/foundry/object-explorer/generate-urls/)渲染指向新创建对象的链接。

- 使用函数生成内容时，支持两种指定 URL 链接的方式：
  - 完整链接示例：`https://<your-foundry-instance>.com/workspace/module/view/latest/<module-rid>`
  - 相对链接示例：`/module/view/latest/<module-rid>`

### 接收人所需的数据访问权限

- 用户只能接收包含其有权查看的数据的通知。
- 在有多个接收人的情况下，所有接收人都必须有权访问通知内容中渲染的对象数据。
- 配置动作时，侧边栏 **Security & Submission Criteria（安全与提交条件）** 标签页底部提供了两种处理通知失败的方法：
  - **要求所有用户都具有权限（默认）：**如果任何接收人不具备所需的访问权限，尝试应用动作时将显示错误。发生这种情况时，不会编辑任何数据，也不会发送任何通知。
  - **要求任意用户具有权限：**如果至少有一个用户可以看到该对象，动作就会成功。只有具有权限的用户才会收到通知。

### 覆盖并禁用电子邮件内容脱敏

[如果组织设置允许](https://www.palantir.com/docs/foundry/email/email-content-redaction/#disable-email-redaction-in-action-types)，你可以针对特定动作类型绕过组织级别的其他严格脱敏设置，使该动作类型发送未脱敏的内容。

要覆盖电子邮件通知的脱敏设置，请导航至 **Security & Submission（安全与提交）** 标签页，然后选择 **Notification settings（通知设置）> Disable notification redaction（禁用通知脱敏）**。

![通知设置，包含禁用通知脱敏选项。](https://www.palantir.com/docs/resources/foundry/action-types/notification_settings.png)

要了解如何为组织启用此功能，请参阅[电子邮件脱敏文档页面](https://www.palantir.com/docs/foundry/email/email-content-redaction/#disable-email-redaction-in-action-types)。

---

*原文：[Notifications](https://www.palantir.com/docs/foundry/action-types/notifications/)*
