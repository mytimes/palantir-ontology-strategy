# 配置派生属性（Configure derived properties）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/derived-properties/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

> **ℹ️ 提示：Beta**
>
> 派生属性处于开发的 [Beta](https://www.palantir.com/docs/foundry/platform-overview/development-life-cycle/) 阶段，可能在你的注册实例上不可用。该功能仍在活跃开发中，后续可能发生变化。

## 概览

派生属性是在运行时根据链接对象的值计算的属性。派生属性不直接存储数据，而是从通过链接类型连接的对象中提取信息，并可选择应用聚合，例如求平均值、计数或将值收集到列表中。

例如：

- `Department`（部门）对象类型可以有一个“平均员工薪资”派生属性，用于聚合所有链接的 `Employee` 对象的薪资值。
- `Project`（项目）对象类型可以有一个“主管工程师姓名”派生属性，用于从单个链接的 `Engineer` 对象获取姓名。
- `Order`（订单）对象类型可以有一个“产品名称”派生属性，用于将链接的 `Product` 对象的所有产品名称收集到一个列表中。

派生属性是**只读**的，不能由函数或动作编辑。这些属性使用计算中涉及的所有对象的安全上下文，确保用户只能看到他们拥有访问授权的信息。

## 配置派生属性

要在对象类型上配置派生属性，请按照以下步骤操作。

### 1. 打开属性配置面板

在对象类型的 **Properties**（属性）选项卡中，选择 **New property**（新建属性）或单击现有属性进行编辑。这将打开属性配置侧面板。

![带有派生属性选项的属性来源选项卡。](https://www.palantir.com/docs/resources/foundry/object-link-types/configure-derived-property-source-tab.png?width=400)

### 2. 导航到 Source（来源）选项卡

在属性配置侧面板中，选择 **Source** 选项卡以配置属性从何处获取值。

### 3. 选择 Linked objects（链接对象）来源类型

在 **Source type**（来源类型）部分，选择 **Linked objects** 选项。这将启用派生属性配置。

### 4. 选择链接类型

在 **Linked objects** 部分，从下拉菜单中选择一个链接类型。这决定了该属性将从哪些对象派生值。

- 下拉菜单显示当前对象类型的所有可用链接类型。
- 选择链接类型后，你可以选择添加其他链接类型以遍历多级连接（最多 3 级）。
- 使用 **Add linked object**（添加链接对象）遍历另一级链接对象。

![为派生属性选择链接类型。](https://www.palantir.com/docs/resources/foundry/object-link-types/configure-derived-property-link-selection.png?width=400)

### 5. 配置聚合（如需要）

如果你的链接链中有任何链接具有“多”基数（一个对象链接到多个对象），你必须选择一个**聚合（Aggregation）**来合并这些值：

可用的聚合：

- **Count（计数）：** 计算链接对象的数量。
- **Average（平均值）：** 计算数值的平均值。
- **Sum（求和）：** 计算数值之和。
- **Minimum（最小值）：** 选择最小值。
- **Maximum（最大值）：** 选择最大值。
- **Approximate cardinality（近似基数）：** 估算唯一值的数量。
- **Exact cardinality（精确基数）：** 计算唯一值的确切数量。
- **Collect list（收集为列表）：** 将所有值收集到一个有序列表中（保留重复项）。
- **Collect set（收集为集合）：** 将所有唯一值收集到一个无序集合中。

### 6. 选择要派生的属性

选择链接类型（以及所需的聚合）后，选择你要从链接对象类型派生哪个属性：

- 下拉菜单显示链接链中最终对象类型的所有可用属性。
- 对于 **Count** 聚合，你无需选择属性，因为对象会被自动计数。

![为派生属性选择聚合类型。](https://www.palantir.com/docs/resources/foundry/object-link-types/configure-derived-property-aggregation.png?width=400)

### 7. 配置收集限制（针对收集类聚合）

如果你选择 **Collect list** 或 **Collect set** 作为聚合，可以选择设置收集项数量的限制。默认限制为 10 项。

## 多跳派生属性

派生属性支持遍历最多 **3 级**链接对象。这使你能够从与起始对象类型间接连接的对象派生属性。

例如：

- `Department` 对象类型可以通过遍历 `Employee` 对象到其链接的 `Project` 对象来派生“项目名称”。
- 链接链为：Department → Employee → Project

![配置多跳派生属性。](https://www.palantir.com/docs/resources/foundry/object-link-types/configure-multi-hop-link.png?width=400)

配置多跳：

1. 选择你的第一个链接类型。
2. 选择 **Add linked object** 添加另一级。
3. 从新连接的对象类型中选择下一个链接类型。
4. 重复操作，总共最多 3 级。

### 已知限制

- **OSv1 支持：** 包含派生属性的查询不得包含任何使用 [OSv1](https://www.palantir.com/docs/foundry/object-backend/osv1-osv2-migration/) 编入索引的对象类型。
- **文本搜索：** 派生属性不能用于文本搜索或关键词筛选。
- **OSDK 中的结构体：** 在当前版本的 TypeScript OSDK 中，包含派生属性的查询不得包含任何[结构体](https://www.palantir.com/docs/foundry/object-link-types/structs-overview/)属性类型。你可以使用 `$select` 操作排除结构体属性。
- **内联动作：** 配置了内联动作的属性不能转换为派生属性。
- **值类型：** 具有值类型的属性不能转换为派生属性。
- **必填属性：** 派生属性不能标记为必填（不可为空）。
- **属性类型约束：** 派生属性不能具有属性类型约束。
- **显示格式：** 派生属性不能具有规则集绑定或基础格式化程序。
- **主键：** 主键属性不能是派生属性。
- **本体条件：** Default（默认）本体不支持派生属性。

---

*原文：[Configure derived properties](https://www.palantir.com/docs/foundry/object-link-types/derived-properties/)*
