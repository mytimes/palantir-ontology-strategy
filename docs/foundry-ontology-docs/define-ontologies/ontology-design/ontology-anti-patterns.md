# 本体设计：反模式（Ontology design: Anti-patterns）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/ontology/ontology-anti-patterns/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

即使是经验丰富的本体设计者，也可能落入常见的设计陷阱：这些陷阱起初看起来合理，但随着本体增长会造成严重问题。本节识别反复出现的反模式，解释它们发生的原因，并提供避免或解决它们的具体指导。

避免这些反模式将帮助你构建一个准确表示业务领域、减少维护开销并支持强大跨职能工作流的本体。

| 反模式 | 描述 | 解决方案 |
| --- | --- | --- |
| [系统竖井](#反模式系统竖井) | 为每个源系统创建独立的对象类型。 | 在管道中合并数据；创建统一的对象类型。 |
| [“厨房水槽”](#反模式厨房水槽) | 将不必要的技术列作为属性包含进来。 | 有意识地筛选属性；排除 ETL 元数据。 |
| [部门竖井](#反模式部门竖井) | 每个部门各自创建共享实体的自有版本。 | 创建共享对象类型；用属性和链接承载部门特有数据。 |
| [上帝对象](#反模式上帝对象) | 一个对象类型表示多个不同实体。 | 创建不同的对象类型；用接口表示共享特征。 |
| [金锤子](#反模式金锤子) | 过度依赖单一工具（例如动作类型、管道或函数）来解决每个问题，而不是选择合适的能力。 | 让工具匹配任务：数据处理用批处理或流式管道，人工决策用动作，事件驱动反应用自动化，复杂实时逻辑用函数。 |
| [动作蔓延](#反模式动作蔓延) | 创建许多只改单个属性的动作，而不是内聚的业务操作。 | 围绕业务操作设计动作，将相关变更捆绑成有意义的工作流。 |
| [时间机器](#反模式时间机器) | 将历史版本建模为独立的对象或对象类型。 | 每个实体使用单个对象，配合链接的历史/修订对象和时间序列属性。 |
| [错误命名](#反模式错误命名) | 对本体要素使用含糊、通用或误导性的名称。 | 使用具体、描述性的名称；限定含糊的属性；按关系为链接命名。 |

## 反模式：系统竖井

当你根据数据所属的源系统，为同一个现实世界实体创建不同的对象类型，而不是对实体本身建模时，就会出现系统竖井（System Silos）。

### 常见原因

- 不同团队拥有不同的源系统并独立构建
- 不确定如何合并来自多个来源的数据
- 希望保留系统特有字段，却不确定哪些才是必要的

### 示例

你的组织在三个系统中存有员工数据：HR 系统、门禁卡系统和项目管理工具。你没有创建单个 `Employee` 对象类型，而是创建了：

- `HR System Employee`
- `Badge System Employee`
- `Project Management Employee`

### 问题

| 问题 | 影响 |
| --- | --- |
| 现实视图碎片化 | 最终用户无法看到员工的统一视图；他们必须浏览多个对象类型才能了解全貌。 |
| 重复劳动 | 对于概念上同一个实体，动作类型、链接类型和应用必须多次构建。 |
| 数据不一致 | 同一员工在不同对象类型中可能有相互冲突的信息，且没有明确的事实来源。 |
| 维护复杂 | 业务逻辑的变更必须在所有系统特有的对象类型中重复实施。 |

### 解决方案

创建一个表示现实世界实体的单一对象类型，并使用数据管道将来自多个源系统的信息合并到统一的支撑数据集中。

```
✗ Avoid                          ✓ Prefer
─────────────────────────────    ─────────────────────────────
HR System Employee               Employee
Badge System Employee         →  (backed by merged dataset
Project Management Employee      from all three systems)
```

要实施这一点：

1. 确定跨系统唯一标识该实体的主键（例如员工 ID）。
2. 构建一个连接所有源系统数据的转换。
3. 为冲突的值定义明确的优先规则（例如，职位以 HR 系统为准）。
4. 创建一个由合并后数据集支撑的单一对象类型。

---

## 反模式：“厨房水槽”

这种反模式（也称为“除了厨房水槽什么都往里装”）发生在对象类型包含来自外部系统的不必要列时——这些列在本体语境中没有业务相关性，用技术产物把数据模型搞得杂乱不堪。

### 常见原因

- “以防万一”心态（保留以后可能有用的字段）
- 不清楚哪些字段有意义
- 未经筛选直接从源系统映射
- 担心排除列会丢失数据

### 示例

从 CRM 系统集成创建 `Customer` 对象类型时，你包含了所有可用列：

- customer_id ✓
- customer_name ✓
- email ✓
- _crm_extracted_at ✗
- _crm_received_at ✗
- _crm_batched_at ✗
- _crm_sequence ✗
- _crm_table_version ✗
- _crm_internal_record_id ✗
- last_etl_update_timestamp ✗

### 问题

| 问题 | 影响 |
| --- | --- |
| 困惑 | 最终用户在业务数据旁边看到不相关的技术字段。 |
| 性能下降 | 不必要的属性增加数据规模、计算量和索引大小，并拖慢搜索。 |
| 洞察被掩盖 | 重要的业务属性被淹没在系统元数据中。 |

### 解决方案

有意识地筛选属性。只包含具有明确业务意义且对工作流有用的列。

在决定包含哪些属性时，使用以下准则：

| 应包含 | 应排除 |
| --- | --- |
| 业务标识符（客户 ID、订单号） | 管道元数据 |
| 人类可读的属性（名称、描述） | 没有业务意义的内部系统 ID |
| 与业务流程相关的日期 | 仅与数据工程相关的时间戳 |
| 筛选或动作所需的状态字段 | 用于管道调试的审计列 |

要实施这一点：

1. 审查每一列并自问：“有人会需要查看、搜索或按此列筛选吗？”
2. 将技术元数据保留在支撑数据集中供调试使用，但不要将其暴露为属性。
3. 使用属性可见性设置，隐藏那些必须存在但很少需要的边缘属性。
4. 记录每个属性存在的原因及其使用者。

---

## 反模式：部门竖井

当不同部门为同一对象类型创建各自的版本时，就会出现部门竖井（Department Silos），导致本体碎片化，反映的是组织结构而非业务现实。

### 常见原因

- 各部门孤立工作，缺乏跨职能协调
- 每个团队都认为自己的客户视图是独特的
- 缺乏治理或统一的本体设计权威
- 团队希望对“自己的”数据拥有自主权和控制权

### 示例

多个部门需要使用客户数据，每个部门都创建了自己的对象类型：

- 销售团队创建 `Sales Customer`
- 支持团队创建 `Support Customer`
- 财务团队创建 `Billing Customer`
- 营销团队创建 `Marketing Contact`

这四个对象类型表示同一个现实世界实体：客户。

### 问题

| 问题 | 影响 |
| --- | --- |
| 没有单一事实来源 | 不同部门对同一客户持有相互冲突的信息。 |
| 跨职能工作流无法实现 | 无法轻松回答诸如“向我展示该客户在销售、支持和计费方面的所有互动”之类的问题。 |
| 重复开发 | 每个部门都构建冗余的动作、链接和应用。 |
| 治理噩梦 | 数据质量问题成倍增加；在一个对象类型中的修复不会传播到其他对象类型。 |

### 解决方案

创建服务于多个部门的共享对象类型，在需要时使用属性和链接来捕获部门特有信息。

```
✗ Avoid                          ✓ Prefer
─────────────────────────────    ─────────────────────────────
Sales Customer                   Customer
Support Customer           →       ├── salesStatus (property)
Billing Customer                   ├── supportTier (property)
Marketing Contact                  ├── billingAccountId (property)
                                   └── Links to:
                                       ├── Sales Opportunities
                                       ├── Support Tickets
                                       └── Invoices
```

要实施这一点：

1. 识别跨部门边界存在的实体。
2. 建立一个跨职能工作组来定义共享对象类型。
3. 在共享对象上用属性承载部门特有信息。
4. 使用链接类型将共享对象连接到部门特有对象（例如 `Customer` → `Support Ticket`）。
5. 如果各部门需要同一底层实体的不同“视图”，可利用对象视图或精心构建的 Workshop 和 OSDK 应用。
6. 如果某些属性只能由特定团队访问，请使用受限视图。

---

## 反模式：上帝对象

当单个对象类型被过度加载、用来表示多个不同的现实世界实体时，就会出现上帝对象（God Object）反模式，导致对象类型臃肿、混乱且难以维护。

### 常见原因

- 由表面相似性驱动的过度抽象（“它们都是资产”）
- 希望尽量减少对象类型数量
- 构建前缺乏清晰的实体定义
- 随着更多用例被添加到现有对象类型而发生范围蔓延

### 迹象

- 对象类型有许多经常为 null 的属性
- 属性的含义根据另一个属性的值（例如类型或类别）而变化
- 查看对象时，你发现自己在问“这是哪一种 `[Object]`？”
- 业务规则和验证需要基于对象“类型”的大量条件逻辑

### 示例

你创建了一个旨在表示“任何有价值之物”的 `Asset` 对象类型，最终却包含：

- 实物设备（卡车、机械）
- 软件许可证
- 房地产
- 金融工具
- 员工（作为“人力资产”）

该对象类型有 150 多个属性，其中大多数对任何给定对象都是 null，而 value、location、status 等属性的含义完全取决于该对象表示哪类“资产”。

### 问题

| 问题 | 影响 |
| --- | --- |
| 语义混乱 | 最终用户无法理解 `Asset` 到底表示什么。 |
| 数据稀疏 | 大多数对象的大多数属性为 null，使数据难以解读。 |
| 无法验证 | 无法实施业务规则，因为规则因实体类型而异。 |
| 搜索体验差 | 搜索 `Assets` 会返回一堆不相关的东西。 |
| 动作类型复杂 | 动作必须用复杂的条件逻辑处理截然不同的实体类型。 |

### 解决方案

为不同的现实世界实体创建不同的对象类型。当实体确实共享公共属性或行为时，使用接口对共享特征建模。

```
✗ Avoid                          ✓ Prefer
─────────────────────────────    ─────────────────────────────
Asset                            Equipment
  - assetType                    Vehicle
  - assetSubtype                 Software License
  - value                  →     Property (Real Estate)
  - location                     Financial Instrument
  - status
  - 145 more properties...       Interface: Depreciable Asset
                                   - purchaseDate
                                   - purchaseValue
                                   - depreciationSchedule
```

要实施这一点：

1. 列出当前由该对象类型表示的不同现实世界实体。
2. 为每个不同实体创建独立的对象类型。
3. 识别真正共享的属性和行为。
4. 使用接口对跨对象类型的共享特征建模。
5. 将现有对象迁移到合适的新对象类型。

---

## 反模式：金锤子

当你过度依赖单一工具来解决每个问题、即使其他方式更合适时，就会出现金锤子（Golden Hammer）反模式。其名称来自谚语：“[如果你只有一把锤子，那么看什么都像钉子 ↗](https://en.wikipedia.org/wiki/Law_of_the_instrument)”。

这种反模式表现为：在更适合管道的工作中过度使用动作类型；为本应是事件驱动自动化的逻辑构建管道；或为更适合在转换中预先计算的计算编写函数。

### 常见原因

- 由于对工具熟悉且其在团队中可见度高而过度依赖
- 希望让最终用户“控制”计算何时发生
- 不熟悉平台的全部能力（包括管道、自动化、函数和调度构建）
- 只在一个层面思考（本体优先、管道优先或代码优先），而不考虑完整工具集

### 示例

**过度依赖动作类型：**

你需要为一个按区域展示总销售额的仪表板计算聚合指标。你没有使用数据管道预先计算这些指标，而是创建了一个名为 `Calculate Regional Sales Totals` 的动作类型，最终用户必须手动触发。结果通过动作回写到对象。

**过度依赖管道：**

当传感器读数超过阈值时，管道会创建一个告警对象。你希望自动将该告警分配给值班工程师并发送通知。你没有使用对新对象做出反应的自动化，而是构建额外的管道逻辑来确定受派人并将分配写入支撑数据集，把运营工作流逻辑混入了数据集成。

**过度依赖函数：**

你把 `fullName` = `firstName` + `lastName` 这样简单的属性派生实现为函数支撑的列，增加了运行时开销和一个需要维护的代码仓库，而单个管道 `concat` 表达式就足够了。

### 问题

| 问题 | 影响 |
| --- | --- |
| 可扩展性限制 | 每种工具都有不同的执行限制；用错工具会早早触及天花板。 |
| 不必要的复杂性 | 在错误的层维护逻辑会增加活动部件的数量。 |
| 用户负担 | 最终用户必须执行平台本可自动处理的步骤。 |
| 性能问题 | 通过动作或函数进行实时计算比预先计算的管道结果慢；反过来，调度管道对于事件驱动反应又太慢。 |
| 调试困难 | 当逻辑位于错误的层时，故障更难诊断和解决。 |

### 解决方案

根据你的用例为工作选择合适的工具：

| 工具 | 最适合 | 不适合 |
| --- | --- | --- |
| 动作类型 | 人工决策、用户发起的对一个或少数对象的编辑、应立即应用的输入驱动变更。 | 批处理计算、计划更新、无人参与的事件驱动反应。 |
| 管道（批处理） | 批处理数据处理、聚合、清洗、丰富，以及按调度或在数据到达时预先计算派生值。 | 对单个对象变更的实时反应、需要人工输入的逻辑。 |
| 管道（流式） | 连续、低延迟的数据处理，结果必须随源数据到达而保持最新（实时仪表板、实时状态跟踪、持续丰富）。 | 批处理即可满足的低频更新、需要人工输入的逻辑、对本体级事件的反应（使用自动化）。 |
| 自动化 | 对本体变更的事件驱动反应（对象创建、属性更新、调度触发），在无用户参与的情况下编排动作或通知。 | 重型数据处理、复杂的多数据集连接、需要人工判断的逻辑。 |
| 函数 | 跨多个对象的复杂实时计算、验证逻辑、依赖本体实时状态且无法预先计算的派生值。 | 可在管道中计算的简单派生、大型数据集的批处理。 |
| 调度 | 重复的管道构建、基于时间或事件的数据刷新编排。 | 实时响应单个对象级变更。 |

应用此指导的示例：

```
✗ Avoid                                          ✓ Prefer
──────────────────────────────────────────────   ──────────────────────────────────────────────
Action: "Calculate Regional Sales"          →    Pipeline that aggregates sales data daily
                                                 into a "Regional Sales Summary" object type.

Action: "Standardize Address Format"        →    Pipeline that cleanses addresses on ingestion.

Action: "Update Inventory Status"           →    Pipeline that sets status based on quantity
(based on quantity thresholds)                   thresholds during each sync.

Action: "Assign Risk Score"                 →    Pipeline or model that calculates risk scores
(using a formula)                                and writes to the backing dataset.

Pipeline that assigns alerts to on-call     →    Automation that triggers an "Assign Alert"
engineers by writing to the backing dataset      action when a new "Alert" object is created.

Pipeline that sends a notification when     →    Automation that monitors for the condition
an object meets a condition                      and sends a notification or triggers an action.

Batch pipeline polling every minute for     →    Streaming pipeline that continuously processes
new IoT sensor readings                          sensor data as it arrives.

Function-backed column for                  →    Pipeline that computes fullName = firstName
fullName = firstName + " " + lastName         + " " + lastName in the backing dataset.

Scheduled pipeline running every minute     →    Automation that reacts to the specific object
to check for objects needing follow-up           change and triggers the follow-up immediately.
```

要实施这一点：

1. **在创建动作类型之前**，自问：“这需要人工判断或用户输入吗？”如果不需要，它很可能属于管道或自动化。
2. **在向管道添加逻辑之前**，自问：“这是数据转换，还是运营工作流？”数据清洗、聚合和丰富属于管道。分配工作、发送通知和对单个变更做出反应属于自动化。
3. **在编写函数之前**，自问：“这可以在支撑管道中预先计算吗？”如果结果只依赖源数据列且不需要实时本体遍历，就在上游计算它。
4. **在构建轮询管道之前**（每隔 N 分钟运行以检测变更），自问：“自动化可以直接对这个事件做出反应吗？”自动化以近实时方式响应本体变更，而没有调度构建的开销。如果需求是持续处理来自源系统的数据，请考虑改用流式管道。
5. **在默认选择批处理管道之前**，自问：“这些数据需要持续保持最新吗？”如果消费者依赖低延迟的新鲜度，流式管道可以避免批处理调度的折中。
6. **使用自动化**来弥合“有东西变了”和“应该发生点什么”之间的差距，而无需用户点击按钮或轮询管道。

---

## 反模式：动作蔓延

当你创建许多范围狭窄、每个只修改单个属性的动作类型，而不是设计代表有意义业务操作的内聚动作时，就会出现动作蔓延（Action Sprawl）。

### 常见原因

- 将动作视为数据库列更新，而不是业务操作
- 增量式地构建动作，而不考虑整体用户体验
- 不理解动作如何捆绑多个属性变更
- 模仿传统应用开发中的 CRUD 操作

### 迹象

- 单个对象类型有超过 10 个动作类型
- 多个动作总是按顺序执行
- 动作名称读起来像 `Set [Property]` 或 `Update [Property]`
- 最终用户抱怨完成一项任务步骤太多

### 示例

对于 `Employee` 对象类型，你没有创建有意义的业务动作，而是创建了：

- `Update Employee First Name`
- `Update Employee Last Name`
- `Update Employee Email`
- `Update Employee Phone`
- `Update Employee Department`
- `Update Employee Manager`
- ……以及另外 20 个单属性动作

### 问题

| 问题 | 影响 |
| --- | --- |
| 体验令人不知所措 | 最终用户面对一长串杂乱的动作，很难找到正确的那个。 |
| 工作流碎片化 | 简单的更新需要多次提交动作才能完成单个业务任务。 |
| 没有内聚的业务表示 | 动作不映射到现实世界流程，使本体不直观。 |
| 审计轨迹碎片化 | 变更历史散落在许多小动作中，很难理解发生了什么以及为什么。 |

### 解决方案

围绕业务操作而非数据库更新来设计动作类型。创建将相关变更捆绑成有意义工作流的动作。

```
✗ Avoid                                    ✓ Prefer
────────────────────────────────────────   ────────────────────────────────────────
Update Employee First Name                 Update Employee Contact Information
Update Employee Last Name            →       - firstName
Update Employee Email                        - lastName
Update Employee Phone                        - email
                                             - phone

Update Employee Department                 Transfer Employee to New Department
Update Employee Manager              →       - newDepartment
Update Employee Location                     - newManager
                                             - newLocation
                                             - effectiveDate

Create Employee Record                     Onboard New Employee
Set Employee Start Date              →       - All required fields for a new hire
Assign Employee Badge                        - Triggers downstream workflows
Assign Employee Equipment                    (badge assignment, equipment request)
```

要实施这一点：

1. 梳理涉及更改对象数据的真实业务流程。
2. 将相关的属性变更分组为代表这些流程的单个动作。
3. 使用动作参数在一个内聚动作中允许可选字段。
4. 以业务操作命名动作：`Transfer Employee`、`Approve Purchase Order`、`Escalate Support Ticket`。
5. 使用动作规则和验证逻辑在动作内实施业务约束。

---

## 反模式：时间机器

当你将实体的历史版本建模为独立的对象或对象类型，而不是使用时间序列数据、快照或恰当的版本管理策略时，就会出现时间机器（Time Machine）反模式。

### 常见原因

- 希望保留每次变更的完整历史
- 不理解如何在本体中对时态数据建模
- 将文件版本管理的心智模型（v1、v2、v3）应用于对象设计
- 不了解时间序列属性或链接历史模式

### 迹象

- 对象类型包含多个对象，表示不同时间点的同一个现实世界实体
- 存在 `version`、`revision` 或 `isCurrent` 等属性来区分副本
- 对象数量随变更次数（而不是实体数量）成比例增长
- 最终用户困惑于应该引用或链接哪个对象

### 示例

为了跟踪 `Contract` 的变更，你创建了：

- `Contract v1`、`Contract v2`、`Contract v3`，作为同一对象类型内的独立对象
- 或者更糟：`Contract 2023`、`Contract 2024`、`Contract 2025`，作为每一年的独立对象类型

每个“版本”都是合同的完整副本，属性值略有不同，并且指向其他对象（如 `Vendor` 或 `Department`）的链接在所有版本中重复。

### 问题

| 问题 | 影响 |
| --- | --- |
| 对象数量爆炸 | 每次变更都创建一个新对象，用冗余数据迅速撑大本体。 |
| 当前状态含糊 | 很难识别哪个版本是“当前”或权威版本。 |
| 链接含糊 | 指向合同的链接变得不明确；`Vendor` 或 `Department` 应该链接到哪个版本？ |
| 报表复杂 | 跨时间段报表需要筛选和去重逻辑，而这类逻辑容易出错。 |

### 解决方案

每个实体使用单个对象，用属性表示当前状态。将历史变更存储在独立的链接对象类型中，启用编辑历史，或利用时间序列属性。

```
✗ Avoid                                    ✓ Prefer
────────────────────────────────────────   ────────────────────────────────────────
Contract v1 (object)                       Contract (single object per contract)
Contract v2 (object)                 →       - currentValue
Contract v3 (object)                         - currentStatus
                                             - effectiveDate
— OR —                                       - Links to:
                                               └── Contract Amendments
Contract 2023 (object type)                        - amendmentDate
Contract 2024 (object type)                        - previousValue
Contract 2025 (object type)                        - newValue
                                                   - changeReason
```

要实施这一点：

1. 每个现实世界实体使用单个对象，其属性反映当前状态。
2. 创建一个独立的链接对象类型（如 `Contract Amendment` 或 `Contract History`）来捕获历史变更。
3. 对频繁变化且需要时态跟踪的值，利用时间序列属性。
4. 如有需要，使用支撑数据集或编辑历史来维护完整历史记录以供审计。

---

## 反模式：错误命名

当你对对象类型、属性和链接类型使用含糊、通用或误导性的名称、未能清楚传达其含义时，就会出现错误命名（Misnomer）反模式，导致整个本体中的困惑和误解。

### 常见原因

- 使用对你自己有意义但对他人无意义的简写名称
- 名称直接从源系统列名沿用，未经转译
- 为简洁而牺牲清晰
- 缺乏命名约定或治理标准
- 假设上下文会使含义显而易见

### 迹象

- 最终用户经常问“这个属性是什么意思？”或“这是哪一种 `[Object]`？”
- 同一名称可以合理地指代多个不同概念
- 属性名是 `value`、`type`、`status`、`date` 或 `name` 等未经限定的单个通用词
- 链接类型使用“related to”之类的通用标签，而未说明关系的性质

### 示例

你创建了以下名称含糊的本体要素：

- 对象类型：`Item`（哪种 Item？产品？行项目？库存项？）
- 属性：`value`（货币价值？数量？分数？评级？）
- 属性：`type`（什么的类型？有效值是什么？）
- 属性：`date`（创建日期？修改日期？到期日期？生效日期？）
- 链接类型：`Item` → `Related Item`（它们如何相关？父子？替代？配件？）

遇到这些名称的最终用户必须猜测其含义，或深挖文档才能理解数据实际表示什么。

### 问题

| 问题 | 影响 |
| --- | --- |
| 误解 | 最终用户没有额外上下文就无法理解本体，导致错误的分析和决策。 |
| 学习曲线陡峭 | 新团队成员必须花费大量时间了解含糊名称的实际含义。 |
| 依赖文档 | 文档从补充资料变成必需品，而且很快过时。 |
| 跨团队困惑 | 不同团队对同样的含糊名称有不同理解，导致使用不一致。 |

### 解决方案

为所有本体要素使用具体、描述性的名称。名称应当自解释，使任何人无需额外上下文就能理解含义。

```
✗ Avoid                                    ✓ Prefer
────────────────────────────────────────   ────────────────────────────────────────
Object type: Item                    →     Object type: Product
                                           Object type: Sales Order Line Item
                                           Object type: Warehouse Inventory Record

Property: value                      →     Property: monetaryValue
                                           Property: quantityOnHand
                                           Property: riskScore

Property: type                       →     Property: productCategory
                                           Property: serviceTier

Property: date                       →     Property: orderPlacedDate
                                           Property: contractEffectiveDate

Link: Item → Related Item            →     Link: Product → Purchasing Customer
                                           Link: Employee → Supervisor
                                           Link: Equipment → Manufacturing Facility
```

要实施这一点：

1. 在构建之前建立命名约定，并通过治理评审强制执行。
2. 使用具体、描述性的名称：`Product`、`Sales Order Line Item`、`Warehouse Inventory Record`。
3. 限定含糊的属性：`monetaryValue`、`quantityOnHand`、`riskScore`。
4. 为链接命名以说明关系：`Purchasing Customers`、`Manufacturing Facility`、`Supervisor`。
5. 为所有本体要素添加描述，说明其含义和有效值。
6. 与最终用户一起评审名称，确保它们直观且无歧义。

## 构建成功的本体

本指南中描述的反模式很常见，但可以避免。通过聚焦基本最佳实践（对现实而非系统建模、有意识地筛选属性、跨团队协作，以及为每项任务选择合适的工具），你可以构建一个随组织需求扩展的本体。

请记住，有效的本体设计是迭代的。从清晰的实体定义开始，尽早让利益相关者参与，并在了解什么有效时完善你的模型。当你遇到挑战时，重新审视本指南中的原则，判断是否正在出现某种反模式，并在它变得难以改变之前纠正方向。

---

*原文：[Ontology design: Anti-patterns](https://www.palantir.com/docs/foundry/ontology/ontology-anti-patterns/)*
