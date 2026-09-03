# 提交条件（Submission criteria）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/action-types/submission-criteria/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

**提交条件**（旧称 validations，即校验）是决定动作是否可以提交的条件。提交条件支持将业务逻辑编码到数据编辑权限中，确保本体数据质量和编辑治理。

提交条件通过组合基于上下文（如用户或参数）的条件与静态信息来创建逻辑语句。提交条件可以将对象、关系甚至用户信息纳入逻辑语句，以决定动作是否可以提交。

> **ℹ️ 提示：示例**
>
> 例如，航空公司可能想要更改某个特定航班所列的飞机。配置好的动作允许用户更改链接到 `Flight` 对象的 `Aircraft` 对象。然而，航空公司只希望选定的用户（如航班管制员）能够使用此动作，以确保只使用仍在运营中的飞机。借助提交条件，构建者可以将用户的用户组成员身份与动作提交时刻飞机的状态相结合，确保更改航班飞机的动作只有在条件满足时才能提交。

![示例：提交条件概览](https://www.palantir.com/docs/resources/foundry/action-types/submission_criteria_overview.png)

提交条件由条件和运算符组成。条件是管控参数或用户属性取值的单条语句。运算符用于组合和嵌套不同的条件。

借助不同类型的运算符，我们可以创建更复杂的语句，以反映业务流程和需求。只有当所有提交条件都满足时，动作才能提交。这与管控用户是否能编辑动作类型本身的权限是相互独立的。虽然一个对象类型可以有多个添加、修改和移除对象的动作类型，但每个动作类型都有独立的提交条件。

## 条件

条件是两个值之间的单次比较检查。每个条件根据其参数或用户输入而通过或失败。可以使用三种条件模板之一来配置条件：“当前用户”、“参数”或“执行上下文”。这些模板为条件的其余部分提供了框架。每个条件都是两个值之间的简单比较，中间使用一个运算符。

![示例：选择条件模板](https://www.palantir.com/docs/resources/foundry/action-types/submission_criteria_select_condition_template.png?width=300)

> **ℹ️ 提示：示例**
>
> 继续我们的示例，航班管制员的要求可以使用 `Current User` 模板来设置，因为它需要提交动作的用户的上下文。若要知道某架飞机是否仍在运营，需要通过 `Parameter` 模板使用 `Aircraft` 对象。

### 当前用户

`Current User` 模板基于提交动作的用户定义权限。`Current User` 输入可用于检查用户的 ID、通过用户组 ID 检查用户组成员身份，或任何其他可用的 Multipass 属性（例如用户的组织）。Foundry 将用户 ID 作为字符串求值，可以与静态定义的用户 ID 列表或存储用户 ID 的任何字符串参数进行比较。

用户组 ID 选项允许你使用动作用户所属的用户组（无论是直接成员身份还是继承的成员身份）来创建条件。这些用户组可以与静态选择的用户组或其他参数提供的用户组 ID 进行比较。

Multipass 属性被视为字符串列表，只能与其他字符串或字符串列表进行比较。用户将拥有一份其有权访问的建议的 Multipass 属性列表。使用 `Other user attribute` 字段，可以针对用户无权访问的属性配置条件。如果用户无权访问某个属性，则该条件将失败。

> **⚠️ 注意**
>
> 避免将 `NOT` 条件用于用户组、标记或组织成员身份。在这些情况下使用 `NOT` 条件属于配置错误。平台支持受限令牌（scoped token），这类令牌只携带用户权限的一个子集。这些令牌可能缺少 `NOT` 条件所检查的属性，从而导致条件通过并授予超出预期的访问权限。

### 参数

提交条件也可以使用参数部分定义的参数。参数从其他应用或用户自身传入动作类型。对参数使用条件，使构建者能够将业务逻辑嵌入动作类型，并防止用户对不符合业务要求的数据提交动作。

> **ℹ️ 提示：示例**
>
> 在我们的示例中，运营状态通过 `Aircraft` 对象给出，并且可能因飞机而异。该条件需要构建在 `Aircraft` 对象类型参数之上。

### 执行上下文

提交条件还可以基于动作提交被求值时所处的执行上下文，即动作是否是在[本体场景](https://www.palantir.com/docs/foundry/ontology/overview-ontology-scenario/)中提交的。场景执行上下文表示动作正在本体场景中被求值。它并不标识某个特定的场景。

> **ℹ️ 提示：示例**
>
> 继续该示例，只有航班管制员应该能够更改 `Aircraft` 对象。然而，在本体场景中，不是航班管制员的规划人员可能需要测试备选的飞机分配方案。
>
> 提交条件可以在以下任一情况下允许该动作：
>
> - 动作在场景中执行，且当前用户是规划用户组的成员；或者
> - 满足航班管制员要求的标准条件。
>
> 另外，可以通过合并场景动作的提交条件，允许航班管制员[合并场景](https://www.palantir.com/docs/foundry/ontology/merge-scenario/#configure-the-merge-action)。

提交条件不支持附件参数和对象集参数。这些参数类型会从选择面板中移除。

### 选择值

选择条件模板后，选择要比较的值。某些参数（如列表参数或对象参数）需要更细粒度地选择应在比较中使用的值。我们也可以选择比较列表的长度而非其内容。

![示例：选择值](https://www.palantir.com/docs/resources/foundry/action-types/submission_criteria_select_a_value_left.png?width=300)

> **ℹ️ 提示：示例**
>
> 在飞机示例中，飞机的运营状态存储在 `Aircraft` 对象的属性中。

### 运算符

运算符定义两个值之间的比较。为简化配置工作流，运算符会经过预筛选，仅显示对该参数有效的一部分运算符。当参数发生更改时，使用此参数的所有条件都需要重新配置。

![示例：选择运算符](https://www.palantir.com/docs/resources/foundry/action-types/submission_criteria_select_an_operator.png?width=300)

根据所选参数的不同，有多个运算符可用。对于单值参数，可使用以下运算符：

| 运算符 | 示例 | 数据示例 | 描述 |
| --- | --- | --- | --- |
| 为 | name *为* John Doe | “John Doe” 为 “John Doe” = TRUE | 左侧值与右侧值完全匹配。 |
| 不为 | **Current User** *不为* John Doe | “John Doe” 不为 “Maria Smith” = TRUE | 左侧值与右侧值不匹配。 |
| 匹配 | name *matches* ^[A | E | I |
| 小于 | Aircraft > Engine Count *小于* 2 | 4 小于 2 = TRUE | 左侧值小于右侧值。 |
| 大于或等于 | Aircraft > Engine Count *大于或等于* 2 | 4 大于或等于 2 = TRUE | 左侧值大于右侧值。 |

对于具有多个值的参数，可使用以下运算符。对象引用列表会被转换为值列表（对象值或所定义属性的值）：

| 运算符 | 示例 | 数据示例 | 描述 |
| --- | --- | --- | --- |
| 包含 | Aircrafts > Pilot Name *包含* “John Doe” | [ “John Doe”, “Maria Smith” ] 包含 “John Doe” = TRUE | 至少一个左侧值与右侧值完全匹配。 |
| 包含任一 | 姓名列表 *包含任一* Aircrafts > Pilot Name | [“King Louis”, “John Doe”] 包含在 [ “John Doe”, “Maria Smith” ] 中 = TRUE | 至少一个左侧值与至少一个右侧值完全匹配。 |
| 包含于 | name *包含于* [ “John Doe”, “Maria Smith” ] | “John Doe” 包含于 [ “John Doe”, “Maria Smith” ] = TRUE | 左侧值与至少一个右侧值完全匹配。 |
| 每个都为 | Aircrafts > Pilot Name *每个都为* “John Doe” | [ “John Doe”, “Maria Smith” ] 每个都为 “John Doe” = FALSE | 所有左侧值都与右侧值完全匹配。 |
| 每个都不为 | Aircrafts > Pilot Name *每个都不为* “John Doe” | [ “John Doe”, “Maria Smith” ] 每个都不为 “King Louis” = TRUE | 所有左侧值都不与右侧值完全匹配。 |

> **ℹ️ 提示：示例**
>
> 由于在我们的示例中用户是多个用户组的成员，而比较对象是单个用户组，因此我们需要选择 `includes` 运算符来检查是否有重叠。然而，运营状态需要与预期状态完全匹配，所以必须设置 `is` 运算符。

### 值

值代表比较的另一方。值可以基于现有参数、静态值，或者不设值。“无值”会检查第一个值是否为空（或 null）。与运算符一样，可用选项取决于第一个值的类型。

![示例：选择值](https://www.palantir.com/docs/resources/foundry/action-types/submission_criteria_select_a_value_right.png?width=300)

> **ℹ️ 提示：示例**
>
> 现在我们可以确定飞机示例中所需的两个条件了。对于航班管制员，需要选择正确的用户组作为静态参数。这是因为该用户组不应改变，而应在每次提交动作时保持不变，无论上下文如何。因此使用 `specific value`（特定值），并通过下拉菜单选择所需的用户组。当运营状态属性为 `Yes` 时，飞机被视为处于运营中，这同样可以使用特定值选项来设置。

## 逻辑运算符

逻辑运算符可用于组合不同的条件。逻辑运算符也可以嵌套，以创建更复杂的逻辑；它可以要求其下的所有条件都满足、任一条件满足、或所有条件都不满足才通过。

## 失败消息

失败消息支持定义当动作无法提交时应显示何种错误。根级别的每个条件和逻辑运算符都有自己的失败消息。如果较低级别的条件未满足，则显示相应根级别（父级）的失败消息。每当条件未满足时，失败消息将在整个 Foundry 中（Object Explorer、Workshop 或 Quiver）展示给最终用户。失败消息告知用户他们为何被阻止提交动作。

你可以在 Ontology Manager 中使用[测试运行](https://www.palantir.com/docs/foundry/action-types/test-run/)，验证提交条件针对给定参数值集的求值情况。

---

*原文：[Submission criteria](https://www.palantir.com/docs/foundry/action-types/submission-criteria/)*
