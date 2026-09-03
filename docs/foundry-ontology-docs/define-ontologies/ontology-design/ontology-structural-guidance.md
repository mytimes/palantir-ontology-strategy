# 本体设计：结构指南（Ontology design: Structural guidance）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/ontology/ontology-structural-guidance/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

以下各节提供关于如何在本体中组织属性、关系和访问控制的指南。

## 规范化与派生属性

**每个事实只存一次。为便利起见使用派生属性。**

反规范化数据（将链接对象的值复制到父对象上）可能有风险。当数据源变更时，每个副本都必须更新。规范化保持数据一致，而派生属性为你提供反规范化访问的便利，却无需维护负担。

并非所有计算值都一样。正确的方法取决于一个值是可以从稳定输入安全地预计算，还是需要与动态的本体变更保持同步。

### 预计算值与动态派生值

| 类型 | 特征 | 推荐工具 | 示例 |
| --- | --- | --- | --- |
| 预计算 | 从同一对象上的属性计算而来；输入很少变更，或仅因管道摄取而变更。 | 管道转换 | `fullName` = `firstName` + " " + `lastName`。输入稳定且在同一管道中更新，因此预计算是安全的，且不增加任何运行时开销。 |
| 动态派生 | 依赖于通过动作、自动化或其他本体级操作变更的链接对象或值。 | 派生属性 | `directReportCount`。员工通过动作被重新分配、入职和离职。一个统计链接的 `Employee` 对象数量的派生属性会自动保持正确。 |

> **⚠️ 注意**
>
> 当一个值依赖于通过动作做出的变更时，每一个可能影响该值的动作也必须更新该值。如果任何动作未能这样做，该值将一直保持不正确，直到差异被发现。

### 反模式

- 同一个值作为属性存储在多个对象类型上
- 属性只是别处维护的值的副本，因而会过时
- 更新单个真实世界事实需要写入多个对象
- 整数或计数属性被手动维护，而不是从链接计算得出

### 示例

一个 `Manager` 对象类型需要显示直接下属的数量：

```
✗ Avoid                                    ✓ Prefer
────────────────────────────────────────   ────────────────────────────────────────
Manager                                    Manager
  - directReportCount: 5                     - directReportCount (derived):
  (manually maintained integer;                counts linked Employee objects
   must be updated every time          →       at query time
   an employee joins or leaves)
                                           Employee
Employee                                     - manager (link to Manager)
  - managerName: "Alice"
  (copied from the linked Manager;
   breaks if the manager's name
   changes)
```

### 性能考量

派生属性在运行时求值。其性能特征随规模而变化：

| 规模 | 建议 |
| --- | --- |
| 低到中等（每次查询 <~1 万个对象） | 可自由使用派生属性。对于大多数工作流，运行时求值的性能足够。 |
| 高（每次查询 >~1 万个对象） | 派生属性可能因开销更高的查询路径而引入延迟。反规范化可能是一个适当的权衡，但它应该是一个有意识的、有文档记录的决策，而不是默认选择。 |

### 最佳实践

1. **将每个事实存储在一个地方**，即它在语义上所属的对象上。
2. **使用派生属性**在查询时从链接对象计算或聚合值。
3. **监控性能**：随着规模增长，如果派生属性在高规模下引入不可接受的延迟，可考虑有选择的反规范化。
4. **为任何反规范化编写文档**，记录理由、事实来源以及保持副本同步的更新策略。

## 结构体

**将语义相关的字段组合为结构体。**

当一个属性天然是多字段的（例如，包含街道、城市、州和邮政编码的地址），使用结构体而不是扁平化为多个独立属性。结构体保留语义分组，并支持更丰富的元数据捕获。

### 何时使用结构体

| 场景 | 示例 |
| --- | --- |
| 多字段值 | 地址（街道、城市、州、邮政编码）、坐标（地理点、海拔） |
| 带元数据的值 | 带有置信度分数、来源引用和推理过程的 AI 生成输出 |
| 带选择逻辑的多值属性 | 多个电话号码，由归约器呈现主号码 |

### 示例

在 `Facility` 对象类型上建模地址：

```
✗ Avoid                                    ✓ Prefer
────────────────────────────────────────   ────────────────────────────────────────
Facility                                   Facility
  - addressStreet                            - address (struct array)
  - addressCity                                  - street (Main field)
  - addressState                     →           - city (Main field)
  - addressPostalCode                            - state (Main field)
  - addressCountry                               - postalCode (Main field)
  - addressGeopoint                              - country (Main field)
  - addressLastOccupied                          - geopoint
  - addressDatasource                            - lastOccupied (used for reducer sorting)
  - addressLlmConfidence                         - datasource
  - addressLlmReasoning                          - llmConfidence
                                                 - llmReasoning

(Ten unrelated properties with a
 naming convention as the only link         (One semantic concept with main
 between them)                               fields and structured sub-fields)
```

### 主要优势

| 优势 | 详情 |
| --- | --- |
| 语义分组 | 地址是一个概念，而不是十个互不相关的属性。本体反映了这一点。 |
| 元数据捕获 | 结构体可以在主值之外携带来源、置信度和时间戳信息。 |
| 归约器支持 | 在多值场景中，归约器可以呈现最相关的值（例如，具有最新 `lastOccupied` 字段的地址）。 |
| 主字段行为 | 结构体可以指定一个或多个主字段，使其在接口和查询中表现得像一个简单属性，或像一个仅含字段子集的结构体。 |

在 AI 优先的工作流中，大语言模型（LLM）的输出同时具有主结果和相关元数据（推理过程、来源引用、置信度分数），结构体在这种场景下尤其有价值。将这些信息一起捕获，而不是分散到互不相关的属性中。

### 最佳实践

1. **识别多字段属性**，即字段在语义上相关且总是一起使用的属性。
2. **定义结构体**，使用清晰的字段名和类型。
3. **指定一个主字段**，使结构体在大多数情境中表现得像简单属性。
4. 对多值结构体属性**使用归约器**以呈现最相关的值。
5. 在结构体中与主值一起**捕获元数据**（来源、置信度、时间戳），尤其是对于 AI 生成的输出。

## 接口

**使用接口构建可复用、面向未来的抽象。**

接口是实现[“不要重复自己”（Do not repeat yourself）设计原则](https://www.palantir.com/docs/foundry/ontology/ontology-best-practices/#2-do-not-repeat-yourself-rule-of-three)和[开闭可扩展性](https://www.palantir.com/docs/foundry/ontology/ontology-best-practices/#3-open-for-extension-closed-for-modification)的主要工具。它们定义了多个对象类型可以实现的共享形状（属性、链接、动作），使工作流可以面向接口而非单个类型。

### 何时使用接口

| 场景 | 示例 |
| --- | --- |
| 跨类型的共同属性 | `Inspectable` 接口，带有 `lastInspectionDate` 和 `inspectionStatus`，由 `Vehicle`、`Equipment`、`Facility` 实现 |
| 共享工作流 | 面向 `SchedulableResource` 的调度工作流无需修改即可适用于场馆、会议室和车辆 |
| 分类学分组 | 由 `Aircraft`、`Vessel`、`GroundVehicle` 实现的 `MilitaryAsset` 接口，用于下钻聚合工作流 |
| 多层抽象 | `SchedulableResource` 扩展 `Trackable`，在更广泛的追踪抽象之上添加调度专用属性 |

### 示例

多个对象类型需要对检查进行追踪：

```
✗ Avoid                                    ✓ Prefer
────────────────────────────────────────   ────────────────────────────────────────
Vehicle                                    Interface: Inspectable
  - lastInspectionDate                       - lastInspectionDate
  - inspectionStatus                         - inspectionStatus
  - (duplicate action: Schedule              - (shared action: Schedule Inspection)
     Vehicle Inspection)
                                     →     Vehicle implements Inspectable
Equipment                                    - make, model, mileage, ...
  - lastInspectionDate
  - inspectionStatus                       Equipment implements Inspectable
  - (duplicate action: Schedule              - serialNumber, warrantyExpiry, ...
     Equipment Inspection)
                                           Facility implements Inspectable
Facility                                     - address, capacity, ...
  - lastInspectionDate
  - inspectionStatus                       (One interface, one shared action,
  - (duplicate action: Schedule             three implementing types)
     Facility Inspection)

(Three copies of the same properties
 and logic, maintained independently)
```

### 平台考量

即使当前平台工具尚不完全支持接口支撑的工作流，以接口进行设计也能奠定基础，随着支持范围扩大而获得回报。

| 情况 | 指南 |
| --- | --- |
| 接口在你的工作流中完全受支持 | 直接面向接口。单个工作流覆盖所有实现类型。 |
| 接口在特定情境中尚不受支持 | 现在就定义接口，并作为临时措施按类型复制工作流。这种方法的效率不低于不使用接口的工作方式，而且为支持可用后的整合确立了一条清晰的路径。 |

查阅我们的[接口文档](https://www.palantir.com/docs/foundry/interfaces/interface-overview/)了解当前支持详情。

### 最佳实践

1. **识别共同形状：** 如果多个对象类型共享属性、链接或动作，定义一个捕获该共享形状的接口。
2. **围绕能力或分类学设计接口：** 能力接口可以包括 `Inspectable`、`Schedulable` 或 `Billable`。分类学接口可以包括 `MilitaryAsset` 或 `MedicalDevice`。
3. **在工作流中面向接口：** 尽可能针对接口构建动作、函数和应用。
4. **扩展接口以实现多层抽象：** 接口可以扩展其他接口以构建分层抽象。
5. **先搭脚手架，后整合：** 即使由于当前平台支持缺口，某些工作流必须暂时按类型复制，也要定义接口。

## 链接与对象支撑的链接类型

**链接应当表示语义上有意义的关系。**

每个链接类型都应该回答一个清晰的领域问题，例如：

- 这位患者就诊于哪家机构？
- 这位员工属于哪个团队？
- 这张工单使用了哪台设备？

### 何时使用链接类型

| 链接类型 | 使用时机 | 示例 |
| --- | --- | --- |
| 直接链接 | 关系有意义，但不携带自身的元数据。 | `Employee` → `Department` |
| 对象支撑的链接 | 关系携带自身的元数据（日期、角色、状态、分配）。 | `Employee` → `VentureStaffing` → `Venture`（带有 `role`、`startDate`、`allocation`） |

并非每个链接对象都需要在每个情境中可见。一些工作流关心连接元数据，另一些只想要直接连接。对象支撑的链接让你可以根据工作流暴露任一视图。

### 示例

建模员工与 venture（业务项目）之间的关系，其中每次分配都有角色和开始日期：

```
✗ Avoid                                    ✓ Prefer
────────────────────────────────────────   ────────────────────────────────────────
Employee → Venture (direct link)           Employee → Venture Staffing → Venture
  (no way to capture role,
   start date, or allocation         →     Venture Staffing
   per assignment)                           - role
                                             - startDate
— OR —                                       - allocationPercentage
                                             - status
Employee
  - ventureRole                            Workflows can expose either:
  - ventureStartDate                         - Direct: Employee → Venture
  (ambiguous if employee has                 - Detailed: Employee → Staffing → Venture
   multiple venture assignments)
```

### 错误链接设计的影响

| 问题 | 影响 |
| --- | --- |
| 元数据丢失 | 直接链接无法捕获关系存在于何时、为何存在或以何种身份存在。 |
| 多重链接歧义 | 当一个实体参与多个关系时，源对象上像 `ventureRole` 这样的属性会变得有歧义。 |
| 无意义链接 | 仅因两个数据集共享一个外键而存在的链接会给本体增加噪声，并混淆导航。 |

### 最佳实践

1. **验证语义意义：** 避免仅因两个数据集共享一个外键而存在的链接。追问该关系在领域中是否有意义。
2. **评估关系是否携带元数据：** 如果携带（日期、角色、状态），使用对象支撑的链接类型来捕获该元数据。
3. **暴露正确的详细程度：** 根据情境，将工作流设计为使用直接关系，或通过链接对象使用详细关系。
4. **清晰命名链接：** 链接名称应从每个方向描述关系。查看[命名约定](#命名约定)一节了解更多信息。

## 命名约定

**以人类可读性和智能体可导航性为优化目标。**

一致且描述性强的命名，是你能对本体质量做出的最有影响力的投资之一。清晰的名称使人类和 AI 智能体都更容易浏览本体，而且一旦本体投入使用，名称就极难纠正。

### 命名规则

| 元素 | 约定 | 好例子 | 坏例子 |
| --- | --- | --- | --- |
| 对象类型 | 领域专家能认出的单数、具体名词 | `Patient`、`WorkOrder`、`FlightSegment` | `Data`、`Item`、`Record` |
| 属性 | 简洁、不言自明；不编码类型信息或实现细节 | `age`、`status`、`lastInspectionDate` | `dtLastInspMod`、`nVAL01`、`fieldX` |
| 链接 | 从每个方向读起来都自然 | `department`（Employee → Dept）、`employees`（Dept → Employee） | `relatedItems`、`link1` |
| 日期 | 在整个本体中一致遵循单一约定 | `createdDate`、`updatedDate`、`effectiveDate` | 混用 `createdDate` 和 `dateOfCreation` |
| 歧义术语 | 用具体含义限定 | `monetaryValue`、`quantityOnHand`、`riskScore` | `value`、`quantity`、`score` |

### 示例

```
✗ Avoid                                    ✓ Prefer
────────────────────────────────────────   ────────────────────────────────────────
Object type: Item                    →     Object type: Product

Property: dtLastInspMod              →     Property: lastInspectionDate

Property: value                      →     Property: monetaryValue
                                           Property: quantityOnHand

Link: Item → Related Item            →     Link: Product → Supplier
                                           Link: Employee → Supervisor
```

### 最佳实践

1. **在构建之前建立命名约定：** 事先就日期、状态、标识符和链接的模式达成一致。
2. **遵循本体已建立的约定：** 如果本体已经使用 `createdDate`，就不要引入 `dateOfCreation`。
3. **限定歧义属性：** 使用 `monetaryValue`、`quantityOnHand` 和 `riskScore`。不要使用 `value`、`quantity` 和 `score`。
4. **按关系命名链接：** 从 `Employee` 到 `Department` 的链接应该是 `department`（从员工视角）和 `employees`（从部门视角）。
5. **与最终用户一起审查名称：** 对构建者来说清晰的名称可能对使用者有歧义。与每天都会使用本体的人一起验证。

## 安全设计

**按语义设计安全，遵循最小权限原则。**

本体中的安全应该用领域中有意义的术语来表达，而不是用数据基础设施的术语。用户应该能够看着一个安全配置就明白什么受到保护以及为什么。

### 安全模型

结合行级和列级安全，实现细粒度的单元格级访问控制：

| 安全层 | 控制内容 | 示例 |
| --- | --- | --- |
| 行级 | 用户可以查看的对象 | VIP 患者仅限资深员工查看 |
| 列级 | 用户在可见对象上可以查看的属性 | 临床记录仅限照护团队查看 |
| 单元格级（组合） | 行限制和列限制的交集 | VIP 患者的临床记录仅资深照护团队可见 |

### 示例

控制对敏感患者数据的访问：

```
✗ Avoid                                    ✓ Prefer
────────────────────────────────────────   ────────────────────────────────────────
PublicPatient (object type)                Patient (single object type)
  - name                                     - name
  - dob                                      - dob
  - diagnosis                                - diagnosis (column-restricted:
                                                 care team only)
RestrictedPatient (object type)      →       - clinicalNotes (column-restricted:
  - name                                         care team only)
  - dob                                      - mentalHealthRecords (column-
  - diagnosis                                    restricted: psychiatry team only)
  - clinicalNotes
  - mentalHealthRecords                    Row-level security:
                                             - VIP patients: senior staff only
(Duplicated schemas; security
 achieved by splitting types.              Column-level security:
 Properties added to one type are            - clinicalNotes: care team only
 easily forgotten on the other.)             - mentalHealthRecords: psychiatry only

                                           (One type; security achieved by policy.
                                            Domain boundaries drive access rules.)
```

### 错误安全设计的影响

| 问题 | 影响 |
| --- | --- |
| 为安全而复制类型 | 模式会逐渐失去同步；添加到一个类型的属性很容易在另一个类型上被遗忘。违反[“不要重复自己”设计原则](https://www.palantir.com/docs/foundry/ontology/ontology-best-practices/#2-do-not-repeat-yourself-rule-of-three)。 |
| 默认权限过宽 | 从宽泛访问开始、事后再限制，有在锁定完成之前暴露敏感数据的风险。 |
| 用临时筛选代替策略 | 安全逻辑散落在应用代码中，而不是在本体层强制执行，既脆弱又难以审计。 |
| 边界错位 | 不遵循领域边界的安全边界更难推理，也更可能出现缺口。 |

### 最佳实践

1. **从严格限制开始，有意识地放开：** 默认最小访问，按需扩大，而不是从开放开始、事后再限制。
2. **结合使用行级和列级安全**，实现细粒度的单元格级访问控制。
3. **使安全与领域边界对齐：** 如果你的领域有天然的访问边界（区域经理看到其区域的数据；照护团队看到其患者），使用本体关系和安全策略对这些边界建模，而不是临时筛选数据。
4. **避免为安全复制对象类型：** 一个带有设计良好的安全策略的类型，胜过多个模式重复的类型。
5. **审查新的本体路径以保持访问控制一致性：** 确保添加的链接、类型或属性保留对受限数据的预期保护。

使用本页的指南确保安全边界与领域边界对齐，然后参阅我们的[安全与治理文档](https://www.palantir.com/docs/foundry/security/overview/)了解配置详情。

---

*原文：[Ontology design: Structural guidance](https://www.palantir.com/docs/foundry/ontology/ontology-structural-guidance/)*
