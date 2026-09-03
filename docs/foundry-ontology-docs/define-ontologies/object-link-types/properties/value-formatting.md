# 添加值格式设置（Add value formatting）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/value-formatting/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

**值格式设置（Value formatting）** 是指对属性的值应用特殊的格式化程序，将原始值转换为更易读的形式。在下图中，左侧（**Before**，格式化前）显示了没有任何格式化的 `weight` 和 `value` 列。右侧（**After**，格式化后）对 `weight` 列应用了单位（“kg”），`value` 列则以更紧凑的形式显示并带有货币符号（“$100K”）。这两个都是数字格式设置的示例。本体还支持日期和时间格式设置，以及用户 ID 格式设置、资源 RID 格式设置和产物 GID 格式设置。

![值格式设置示例](https://www.palantir.com/docs/resources/foundry/object-link-types/value-formatting-numeric-formatting-example.png?width=600)

## 支持的值格式设置

| 类型 | 描述 |
| --- | --- |
| 数字格式设置 | 添加货币/单位/前缀、各种类型的记数法（紧凑、科学）以及百分比。更多详情请参见[数字格式设置选项](#数字格式设置选项)部分。 |
| 日期和时间格式设置 | 以特定格式以及特定时区渲染时间戳和日期。 |
| Foundry ID 格式设置 | 将 Foundry ID 显示为用户的名字和姓氏或用户组名称。 |
| 资源 RID 格式设置 | 将 Foundry 资源 ID（RID）显示为图标和资源名称，并带有可点击的链接，可路由到该资源。 |
| 产物 GID 格式设置 | 将产物全局 ID（GID）显示为图标和产物名称，并带有可点击的链接，可路由到该产物。 |

## 添加值格式设置

在属性编辑器中：

1. 选择要添加值格式设置的属性。
2. 在属性面板的右侧，你会看到一种格式设置类型，具体取决于属性的基础类型（值格式设置、数字格式设置、日期和时间格式设置等）。打开格式设置开关。

![值格式设置开关](https://www.palantir.com/docs/resources/foundry/object-link-types/value-formatting-toggle.png?width=500)

1. 数字格式设置以及日期和时间格式设置还提供其他格式设置选项，如下所述：
  - [数字格式设置选项](#数字格式设置选项)
  - [日期和时间格式设置选项](#日期和时间格式设置选项)
2. 当你选择可用的格式设置选项时，你将看到预览，展示应用新格式设置后属性的值将如何渲染。

### 数字格式设置选项

![数字格式设置选项](https://www.palantir.com/docs/resources/foundry/object-link-types/value-formatting-numeric-formatting.png?width=500)

| 名称 | 描述 | 用法 |
| --- | --- | --- |
| **数字格式设置** | 开/关切换。 | 切换此项可移除/添加数字格式设置。 |
| **基础类型** | 包含各种可用的格式设置类型（货币、标准单位、百分比、前缀/后缀、固定值）以及每种类型的示例和描述。 | 如果 `Capacity in Pounds` 具有关联单位，请从此下拉菜单中选择 **Standard unit**（标准单位）。 |
| **使用分组** | 添加符合区域设置的逗号分隔符。 | 打开此项可从 123456 变为 123,456。 |
| **记数法** | 包含紧凑/科学记数法和工程记数法。 | 选择紧凑记数法以显示近似值，如 123K。 |
| **最大小数位数** | 小数点后显示的最多位数。小数位数更多的值将四舍五入到该位数。 | 设置为 `2` 可将 `3.14159` 显示为 `3.14`。 |
| **最小小数位数** | 小数点后显示的最少位数。较短的值将在末尾补零。 | 设置为 `2` 可将 `3.5` 显示为 `3.50`。 |
| **最大/最小有效数字位数** | 显示的最多和最少有效数字位数，从第一个非零数字开始计数。 | 将最大有效数字位数设置为 `3` 可将 `3.14159` 显示为 `3.14`。 |
| **最小整数位数** | 小数点前显示的最少位数，用前导零填充。 | 设置为 `2` 可将 `5` 显示为 `05`。 |
| **预览结果** | 查看和测试数字格式设置。 | 在输入框中输入任何与你期望在属性值中看到的数值相近的数字，即可预览格式设置效果。 |

### 日期和时间格式设置选项

![日期和时间格式设置选项](https://www.palantir.com/docs/resources/foundry/object-link-types/value-formatting-date-formatting.png?width=500)

| 名称 | 描述 | 示例 |
| --- | --- | --- |
| **日期** | 仅日期（无时间） | `Wed, Jul 22, 2020` |
| **日期和时间** | 日期和时间，长格式 | `Wed, July 22, 2020, 1:00:00 PM` |
| **日期和时间，短格式** | 日期和时间，短格式 | `Jul 22, 2020, 1:00 PM` |
| **ISO 时刻** | 日期和时间（ISO 8601 格式） | `2020-07-22T13:00:00.000Z` |
| **相对于现在** | 相对于当前时刻的日期 | `8 minutes ago` |
| **时间** | 仅时间（无日期） | `1:00 pm` |

> **ℹ️ 提示**
>
> 当格式设置为 **Relative to now**（相对于现在）时，应用仅对 24 小时以内的时间以相对措辞进行格式化。超过 24 小时后，将以 **Date and time, short**（日期和时间，短格式）形式渲染并带有星期几：`Wed, Jul 22, 2020, 1:00 PM`。

![相对于现在](https://www.palantir.com/docs/resources/foundry/object-link-types/value-formatting-relative-to-now.png?width=300)

#### 时区

如果你要格式化时间戳，你可以指定渲染该时间戳所用的时区：既可以是你输入的静态时区，也可以是应用用户的当前时区。输入静态时区时，你可以通过输入 UTC 偏移量或区域名称来搜索时区。

### 用户 ID 格式设置

值格式设置可应用于作为 Foundry/Multipass 用户 ID 或用户组 ID 的字符串，通过选择 **Username or group name**（用户名或用户组名称）选项，将它们转换为显示用户的名字和姓氏或用户组名称。此值格式设置选项通常用于以下情况：你创建了一个[动作](https://www.palantir.com/docs/foundry/action-types/overview/)来编辑属性，并在其中一个属性字段中存储用户 ID 或用户组 ID。在支撑数据中，此信息将存储为用户的 Foundry 用户 ID 或用户组 ID，而应用值格式设置后，可以渲染用户名或用户组名称而不是 ID。

## 常见问题

### 这能与现有的类型类一起使用吗？

在 UI 中，值格式设置优先于现有的类型类。如果两者都配置了，将显示值格式设置。不过，你可以在一个属性上使用值格式设置，在另一个属性上使用类型类。

### 这能与 Object Views 中可编辑的属性一起使用吗？

为[内联编辑](https://www.palantir.com/docs/foundry/action-types/inline-edits/#object-explorer-inline-edits)配置的属性支持值格式设置。具有旧版 `hubble:editable` 类型类的属性将禁用值格式设置。

---

*原文：[Add value formatting](https://www.palantir.com/docs/foundry/object-link-types/value-formatting/)*
