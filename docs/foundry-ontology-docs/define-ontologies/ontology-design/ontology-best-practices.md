# 本体设计：最佳实践（Ontology design: Best practices）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/ontology/ontology-best-practices/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

设计良好的本体能够统一而直观地表示你的组织，实现无缝数据集成、跨职能协作和强大分析。本节既提供一套可快速查阅的设计准则，也深入阐述支撑这些准则的核心设计原则。

## 设计准则

以下准则是本体设计的实用清单。在适用之处，每条准则都根植于某个[核心设计原则](#核心设计原则)、[反模式](https://www.palantir.com/docs/foundry/ontology/ontology-anti-patterns/)或[结构性建议](https://www.palantir.com/docs/foundry/ontology/ontology-structural-guidance/)。

1. **对现实建模，而非对系统建模：** 对象类型应当表示现实世界实体，而不是各个源系统或部门的表示。
  - **设计原则：** [领域驱动设计](#1-领域驱动设计)
2. **有意识地筛选：** 每个属性都应有明确的业务或技术价值。
  - **结构性建议：** [规范化与派生属性](https://www.palantir.com/docs/foundry/ontology/ontology-structural-guidance/#normalization-and-derived-properties)
3. **跨团队协作：** 本体设计应有来自多个部门或团队的利益相关者参与。团队各自为政是导致重复建设的主要原因。
  - **设计原则：** [不要重复自己](#2-不要重复自己三次法则)
4. **保持对象类型聚焦：** 每个对象类型应当表示一个不同的实体。
  - **设计原则：** [领域驱动设计](#1-领域驱动设计)
5. **选择合适的工具：** 人工或智能体决策用动作类型，自动化转换用管道。
  - **反模式：** [金锤子](https://www.palantir.com/docs/foundry/ontology/ontology-anti-patterns/#anti-pattern-the-golden-hammer)
6. **使用接口进行抽象：** 当实体共享共同特征时，用接口对抽象建模，而不是创建宽而稀疏的对象类型。
  - **设计原则：** [组合优于深层继承层次](#4-组合优于深层继承层次)
7. **记录你的决策：** 在 Ontology Manager 中记录对象类型、属性和链接。

## 核心设计原则

这四条原则来自政府和商业实施中的大量现场经验。它们按优先级顺序呈现。发生冲突时，优先级更高的原则优先。

| 优先级 | 原则 | 核心理念 |
| --- | --- | --- |
| 1 | [领域驱动设计](#1-领域驱动设计) | 对现实世界建模，而非对源数据建模。 |
| 2 | [不要重复自己](#2-不要重复自己三次法则) | 如果你把同样的东西构建了三次，就该重构了。 |
| 3 | [对扩展开放，对修改封闭](#3-对扩展开放对修改封闭) | 保护核心模型。让构建者能够扩展它们。 |
| 4 | [组合优于深层继承层次](#4-组合优于深层继承层次) | 优先通过接口实现多重继承。保持可插拔。 |

任何决策都应始终考虑实际因素。有关更多信息，请参阅[务实与权衡](#务实与权衡)一节。

### 1. 领域驱动设计

![1-领域设计](https://www.palantir.com/docs/resources/foundry/ontology/domain-design.png?width=1280)

**本体对现实世界建模，而非对源数据建模。**

对象应当表示语义上有意义的现实世界概念（例如 `Patient`、`WorkOrder` 或 `Vessel`），而不是数据库表、API 响应或电子表格标签页。链接应当表示真实关系（“这位患者就诊于这家机构”），而不是连接键或外键产物。

当被要求“将一个数据集本体化”时，要抵制把列 1:1 映射为属性就认为工作完成的冲动。[“厨房水槽”](https://www.palantir.com/docs/foundry/ontology/ontology-anti-patterns/#anti-pattern-the-kitchen-sink)反模式产生的本体反映的是源系统模式的怪癖，而不是有用的语义。设计良好的本体应当用起来直观；用户或 AI 智能体应当能够毫无障碍地浏览它，因为其结构与他们思考自己领域的方式一致。

#### 反模式

- 对象类型反映源系统表，而不是领域实体
- 属性未经筛选地从源列 1:1 映射
- 名称来自源系统约定（`dtLastInspMod`），而不是业务语言（`lastInspectionDate`）
- 对象模型是通过查看数据而非理解领域来设计的
- 包含多个实体的单个源行被建模为单个对象类型

#### 示例

一个包含 `order_id`、`customer_name`、`customer_email`、`product_sku` 和 `quantity` 列的 CSV 描述的是至少三个现实世界实体，而不是一个：

```
✗ Avoid                                    ✓ Prefer
────────────────────────────────────────   ────────────────────────────────────────
OrderData                                  Order
  - order_id                                 - order_id
  - customer_name                            - quantity
  - customer_email                →          - Links to → Customer, Product
  - product_sku
  - quantity                               Customer
                                             - name
(One object type mirroring the CSV)          - email

                                           Product
                                             - sku

                                           (Three object types modeling the domain)
```

#### 反模式的影响

| 问题 | 影响 |
| --- | --- |
| 模型不直观 | 用户和 AI 智能体无法自然地浏览本体，因为其结构与他们思考领域的方式不匹配。 |
| 与源的脆弱耦合 | 源系统中的模式变更会破坏本体消费者，因为本体反映的是源结构，而不是对其进行抽象。 |
| 错失关系 | 嵌入为列的实体（如订单上的 `customer_name`）无法被独立链接、搜索或推理。 |
| 复用性差 | 由某个系统模式塑造的对象类型很难被其他团队或用例采用。 |

#### 最佳实践

1. **在查看源模式之前先识别现实世界实体：** 与领域利益相关者合作，定义哪些概念重要。单个数据集常常描述多个实体。
2. **将身份与观察分开：** 如果一行表示关于某个实体的测量或事件，那么该实体与该观察很可能是不同的对象类型。
3. **为人类命名：** API 名称应当直观且自解释。优先使用 `person.children` 而非 `person.linkedChildPersonObjects`；优先使用 `equipment.lastInspectionDate` 而非 `equipment.dtLastInspMod`。
4. **先对领域建模，再映射数据：** 先理解领域，再设计对象模型，然后将源数据映射到该模型中。不要试图查看数据并复制其形状。
5. **将非语义类型标记为隐藏：** 当特定工作流需要非语义类型（即服务于技术目的、而非对现实世界领域实体建模的类型）时，将其标记为隐藏，以保持本体默认视图的整洁。它们仍然可供构建者在构建应用时使用。

### 2. 不要重复自己（三次法则）

![3-不要重复](https://www.palantir.com/docs/resources/foundry/ontology/do-not-repeat.png?width=1280)

**如果你把同样的东西构建了三次，就该重构了。**

重复的对象类型、冗余的属性和复制粘贴的工作流是一种维护负担，也是上下文管理问题——对于需要对本体进行推理的人类和 AI 智能体都是如此。目标是每个概念有唯一的规范表示，对该概念的每个操作有唯一的规范工作流。三次法则是应用这一原则的实用触发器：一次是巧合，两次是模式，三次就意味着该重构了。

#### 反模式

- 多个对象类型共享同一组属性和相似的链接
- 同样的派生属性逻辑或动作逻辑出现在多个类型中
- 不同团队为略有不同的目的创建了几乎相同的对象类型
- 存在复制粘贴的工作流，在各类型间只有细微差异

#### 示例

三个团队各自独立创建了模式重叠的客户相关对象类型：

```
✗ Avoid                                    ✓ Prefer
────────────────────────────────────────   ────────────────────────────────────────
Sales Customer                             Customer (single canonical type)
  - name                                     - name
  - email                                    - email
  - phone                                    - phone
                                             - salesStatus
Support Customer                   →         - supportTier
  - name                                     - billingAccountId
  - email
  - phone                                 — OR, if shapes are genuinely distinct —

Billing Customer                           Interface: CustomerBase
  - name                                     - name
  - email                                    - email
  - phone                                    - phone

(Three types, three sets of actions,       Implemented by: SalesLead, SupportContact,
 three maintenance burdens)                BillingAccount
```

#### 反模式的影响

| 问题 | 影响 |
| --- | --- |
| 维护负担 | 变更必须在每个副本中重复实施。遗漏的更新会导致副本之间的漂移。 |
| 上下文含糊 | 用户和 AI 智能体无法确定几个几乎相同的类型中哪个是规范的。 |
| 行为不一致 | 重复的动作或派生属性逻辑随时间分化，产生相互冲突的结果。 |
| 开发工作浪费 | 团队以略有不同的形式重复构建同样的东西，而不是在一个共享模型上协作。 |

#### 最佳实践

1. **审查重复：** 如果多个对象类型具有共同的形状（相同属性、相似链接、相似动作），评估它们是否应该成为一个带区分属性的单一类型，或实现一个共享接口。
2. **合并共享逻辑：** 如果同样的派生属性或动作逻辑出现在多个类型中，将其提取到接口或共享函数中。
3. **统一团队特有副本：** 当不同小组创建了几乎相同的对象类型时，将它们统一为单一规范表示，并配以适当的安全或筛选。
4. **应用三次法则：** 一个重复可以接受。两个是警告信号。三个就意味着该重构了。

### 3. 对扩展开放，对修改封闭

![2-开放封闭](https://www.palantir.com/docs/resources/foundry/ontology/open-closed.png?width=1280)

**保护核心模型。让构建者能够扩展它们。**

一旦对象类型、接口或工作流经过现场检验并投入生产，其核心结构就应当稳定。组织中的其他开发者和团队应当能够在其之上构建——添加实现某个接口的新对象类型，或消费现有对象的新工作流——而无需修改核心模型。

#### 反模式

- 对已建立的对象类型频繁进行破坏性变更，并级联影响依赖它的应用
- 新用例需要修改现有核心类型，而不是扩展它们
- 团队需要编辑共享接口或动作来满足团队特有需求
- 为一个团队的扩展所做的安全变更无意中影响了其他消费者

#### 示例

一个核心 `Equipment` 对象类型和 `Inspectable` 接口已在生产中。一个新团队需要跟踪某些设备的认证数据：

```
✗ Avoid                                    ✓ Prefer
────────────────────────────────────────   ────────────────────────────────────────
Modify the core Equipment type:            Extend without modifying core:

Equipment                                  Equipment (unchanged)
  - serialNumber                             - serialNumber
  - manufacturer                             - manufacturer
  - certificationAuthority   (new)   →       - Links to → Equipment Certification
  - certificationExpiry      (new)
  - certificationStatus      (new)         Equipment Certification (new linked type)
  - lastCertAudit            (new)           - certificationAuthority
                                             - certificationExpiry
(Four new properties, null for all           - certificationStatus
 non-certified equipment, existing           - lastCertificationAudit
 consumers must handle the change)
                                           New interface: Certifiable
                                             - certificationStatus
                                             - certificationExpiry

                                           (Core type untouched, new capability
                                            added via linked type and interface)
```

#### 反模式的影响

| 问题 | 影响 |
| --- | --- |
| 破坏性变更 | 对核心类型的修改可能破坏整个组织中依赖它的应用、动作和工作流。 |
| 范围蔓延 | 核心类型为每个新用例累积属性和逻辑，趋向[上帝对象](https://www.palantir.com/docs/foundry/ontology/ontology-anti-patterns/#anti-pattern-the-god-object)。 |
| 所有权纠缠 | 多个团队修改同一个核心类型，造成合并冲突和责任不清。 |
| 安全泄漏 | 在没有清晰边界的情况下扩展核心类型，可能无意中扩大数据访问。 |

#### 最佳实践

1. **识别什么是本质的：** 确定哪些属性和链接是该实体的根本。锁定它们。
2. **为扩展而设计：** 创建核心类型和接口时，预见到他人会在其上构建。为链接的扩展类型和新的接口实现留出空间。
3. **扩展而非修改：** 向现有模型添加内容时，考虑该添加是属于核心类型，还是属于扩展（新的链接对象类型、新的接口实现或新的属性命名空间）。
4. **强制安全边界：** 核心数据模型应当有定义良好的安全边界，使扩展本体不会无意中扩大访问。

### 4. 组合优于深层继承层次

![4-组合](https://www.palantir.com/docs/resources/foundry/ontology/composition.png?width=1280)

**优先通过接口实现多重继承。保持可插拔。**

Foundry 的本体支持通过接口实现多重继承，因此实体可以从多个聚焦的抽象组合行为，而不是使用单继承链。

#### 反模式

- 深层单继承链，其中子类型仅为组合父类能力而存在
- 像 `SchedulableBuilding` 或 `InspectableVehicle` 这样的“组合”类型，将两个不相关的概念合并到一个类型中
- 工作流与特定对象类型紧耦合，而它们本可以在共享接口上运行
- 向实体添加新能力需要重构继承链

#### 示例

一个 `Arena` 既需要是建筑物，又需要是可调度的资源：

```
✗ Avoid                                    ✓ Prefer
────────────────────────────────────────   ────────────────────────────────────────
Deep single-inheritance:                   Composed interfaces:

Asset                                      Interface: Building
  └── PhysicalAsset                          - address
        └── Building                         - squareFootage
              └── SchedulableBuilding
                    └── Arena              Interface: SchedulableResource
                                             - schedulingCalendar
(Every new combination of capabilities       - bookingPolicy
 requires a new intermediate type.
 A SchedulableWarehouse would need         Arena implements both:
 yet another branch.)                        Building + SchedulableResource
                                             - arenaName
                                             - seatingCapacity

                                           (Adding SchedulableWarehouse only
                                            requires implementing the same two
                                            interfaces — no new hierarchy needed.)
```

#### 反模式的影响

| 问题 | 影响 |
| --- | --- |
| 组合爆炸 | 每一种新的能力组合都需要层次结构中的一个新中间类型。 |
| 脆弱的层次结构 | 对父类型的变更会不可预测地级联到所有后代。 |
| 复用受限 | 构建在链深处某个特定类型上的工作流，无法为共享同一能力的其他类型复用。 |
| 语义扭曲 | 牵强的父类型（如 `SchedulableBuilding`）不表示现实世界概念，违反了[领域驱动设计原则](#1-领域驱动设计)。 |

#### 最佳实践

1. **围绕能力或角色设计接口：** 使用 `Inspectable`、`Schedulable`、`Billable` 或 `Depreciable` 等聚焦的接口，捕获特定的行为或属性集。
2. **使用分类学接口进行聚合：** 分类学身份接口（例如由 `Aircraft`、`Vessel`、`GroundVehicle` 实现的 `MilitaryAsset`）对于下钻调查或类似的聚合工作流特别有用。
3. **在工作流中面向接口：** 构建动作、函数和应用时，尽可能面向接口。构建在 `SchedulableResource` 接口上的工作流无需修改即可用于场馆、会议室和车辆。
4. **组合而非继承：** 当实体需要多种能力时，实现多个接口，而不是把它插入深层单继承链。

## 务实与权衡

**这些原则是指南，而非法律。**

现实世界的约束——包括截止期限、旧版系统、部分平台支持和用户技能水平——意味着理想的本体设计并不总是能立即实现。使用以下准则来应对权衡：

| 准则 | 细节 |
| --- | --- |
| 朝好的设计前进，但不要成为路障 | 如果某件事需要在紧迫的期限内可用，现在先构建合理的东西，并留出清晰的改进路径。 |
| 明确点出权衡 | 当推荐走捷径时，解释牺牲了什么以及何时可能产生影响。例如，反规范化在你当前的规模下可能工作良好，但如果增长超过 1 万个对象，你可能需要重新审视。 |
| 优先增量改进而非大爆炸式重构 | 一个正在使用并产生价值的、略不完美的本体，好过一个仍在设计中的、理论上完美的本体。 |
| 捍卫关键不变式 | 命名质量、语义清晰和安全设计日后很难修复。可以在实现细节上偷工，但不要在这些方面。 |

本体是驱动你组织的软件。要像对待生产代码库一样用心对待它，但要将业务价值置于完美之上。

---

*原文：[Ontology design: Best practices](https://www.palantir.com/docs/foundry/ontology/ontology-best-practices/)*
