# 创建链接类型（Create a link type）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/create-link-type/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

我们建议使用下面介绍的引导式助手来创建和配置新的链接类型。不过，如果你在完成对象创建流程之前退出了助手，可以通过为新链接类型指定链接类型、键和 API 名称来手动完成该流程。

## 访问链接类型创建助手

导航到 Ontology Manager。要访问链接类型创建助手，选择以下方法之一：

- 从右上角选择 **New（新建）**，然后选择 **Link type（链接类型）**。![从 New 下拉菜单中选择 Link type 选项。](https://www.palantir.com/docs/resources/foundry/object-link-types/create-new-link-type.png?width=400)
- 在左侧边栏中，选择 **Resources（资源）**下的 **Link types（链接类型）**。然后，在 **Link types** 页面的右上角选择 **New link type（新建链接类型）**。
- 导航到你要建立链接的对象类型，然后在该对象类型 **Overview（概览）**页面的链接类型图中选择 **Create new link type（创建新链接类型）**。
![创建新链接类型](https://www.palantir.com/docs/resources/foundry/object-link-types/create-new-link-type-button.png)

## 配置新链接类型

新链接类型助手将引导你完成以下步骤：

- [选择关系类型](#选择关系类型)
- [定义链接资源](#定义链接资源)
  - [外键关系类型](#外键关系类型)
  - [连接表数据集关系类型](#连接表数据集关系类型)
  - [支撑对象关系类型](#支撑对象关系类型)
- [定义链接类型名称](#定义链接类型名称)
- [保存位置](#保存位置)
- [将更改保存到本体](#将更改保存到本体)

### 选择关系类型

1. 在 **Create a new link type（创建新链接类型）**对话框的第一步中，选择链接的关系类型。
2. 选择用于定义两个对象之间链接的关系类型：
  - **Object type foreign keys（对象类型外键）：**支持“一对一”和“多对一”基数的链接类型。此选项允许你选择表示所需对象的外键及对应主键的属性。有关使用外键定义链接资源的详细信息，请[参见下文](#外键关系类型)。
  - **Join table dataset（连接表数据集）：**用于“多对多”基数的链接类型。此选项允许你使用连接表数据集来支撑链接。有关使用数据集定义链接资源的详细信息，请[参见下文](#连接表数据集关系类型)。
  - **Backing object type（支撑对象类型）：**对象支撑的链接类型在多对一基数链接类型的基础上扩展，为将对象类型作为链接类型存储方案提供一等支持。有关定义由对象支撑的链接资源的详细信息，请[参见下文](#支撑对象关系类型)。更多信息请参阅[对象支撑的链接](#对象支撑的链接)部分。

在下面的示例中，假设存在两个通过某种基数相互关联的对象类型：`Aircraft`（飞机）对象类型和 `Flight`（航班）对象类型。基数类型包括：

- *一对一基数（one-to-one cardinality）：*表示一架 `Aircraft` 应链接到单个 `Flight`。一对一基数用于指示预期的关系，但不会强制实施一对一基数。
- *一对多基数（one-to-many cardinality）：*表示一架 `Aircraft` 可以链接到多个 `Flights`。
- *多对一基数（many-to-one cardinality）：*表示多架 `Aircraft` 可以链接到一个 `Flight`。
- *多对多基数（many-to-many cardinality）：*表示一架 `Aircraft` 可以链接到多个 `Flights`，且一个 `Flight` 可以链接到多架 `Aircraft`。

3. 选择 **Next（下一步）**进入下一步。![在创建对话框中选择链接类型的关系类型](https://www.palantir.com/docs/resources/foundry/object-link-types/create-link-relationship-type.png)

### 定义链接资源

#### 外键关系类型

**外键（foreign key）**是一个对象类型上的属性，用于存储另一个对象类型主键的值，表示两个对象类型之间的真实关系。这个概念类似于关系数据库中的外键，即一个表中的某列引用另一个表的主键列。在一对一或多对一基数的链接类型中，你需要为链接定义外键属性和主键属性。一个对象类型的**外键**属性必须引用另一个对象类型的**主键**属性。

例如，`Tail Number`（机尾编号）属性是 `Aircraft` 对象类型上的主键，用于唯一标识每架飞机。`Flight` 对象类型上的 `Flight Tail Number`（航班机尾编号）属性是外键，表示分配给该航班的飞机。当 `Aircraft` 的 `Tail Number` 与某个 `Flight` 的 `Flight Tail Number` 匹配时，将在 `Aircraft` 和 `Flight` 对象类型之间创建链接。

1. 在 **Link resources（链接资源）**步骤中，为你的链接选择对象类型。
2. 从右侧的下拉菜单中选择主键对象类型（在我们的示例中为 `Aircraft`）。
3. 从左侧的下拉菜单中选择外键对象类型（在我们的示例中为 `Flight`）。如果满足以下条件，创建对话框将检测并自动选择外键：
  - 外键与被链接对象类型的主键匹配。
  - 两个对象的属性类型匹配。
4. 选择将构成链接的属性：
  - 对于外键对象类型，选择将用作源对象类型外键的属性（对于 `Flight` 对象类型为 `Flight Tail Number`）。
  - 对象类型的主键会被自动选中，因为每个对象类型只有一个主键（对于 `Aircraft` 对象类型为 `Tail Number`）。
5. 选择 **Next（下一步）**继续。
![使用外键关系类型选择链接资源。](https://www.palantir.com/docs/resources/foundry/object-link-types/create-link-foreign-key.png)

#### 连接表数据集关系类型

在多对多基数中，选择一个数据源，其中包含第一个对象类型（在我们的示例中为 `Aircraft`）的主键与第二个对象类型（在我们的示例中为 `Flight`）之间所有链接组合。

多对多基数需要支撑数据源，它是用户能够[编辑或回写](https://www.palantir.com/docs/foundry/object-link-types/allow-editing/)链接类型的必要条件。

1. 在 **Link resources** 步骤中，为你的链接选择对象类型。
2. 从左侧的下拉菜单中选择第一个对象类型（`Flight`）。
3. 从右侧的下拉菜单中选择第二个对象类型（`Aircraft`）。
4. 选择连接表数据集。选择一个数据集，其包含的列与两个所选对象类型的主键相匹配。一列只能映射到一个主键。
  - 现在可以为新链接类型自动生成连接表。**Generate join table（生成连接表）**选项将根据你所选两个对象类型的主键创建一个具有正确模式的数据集。这意味着，如果你的数据将由用户编辑产生，或者你打算稍后再提供生产数据，就可以更快上手。
5. 选择链接类型的支撑数据源中哪些列映射到每个被链接对象类型的主键。
6. 选择 **Next（下一步）**继续。![选择链接类型关系类型](https://www.palantir.com/docs/resources/foundry/object-link-types/create-link-dataset.png)

#### 支撑对象关系类型

在创建对象支撑的链接之前，请确保已创建[前提条件](#创建对象支撑链接类型的前提条件)中的对象和链接。

1. 选择在前提条件中创建的、用于表示你所需链接类型的对象类型。左右两侧的对象表示将被链接到一起的两个实体。中间的对象充当中介，提供关于两个实体之间连接的额外元数据，并支撑该链接。
2. 如果左右两侧的对象与中间的中介对象之间存在多个链接，请使用下拉菜单选择左右对象与中介对象之间所需的链接。![使用对象支撑的链接选择链接资源。](https://www.palantir.com/docs/resources/foundry/object-link-types/oblt-link-dialog.png)

### 定义链接类型名称

1. 在 **Link type names（链接类型名称）**步骤中，为你的新链接类型提供显示名和 API 名称。
2. 为链接类型的每一侧输入显示名。一个链接类型恰好有两侧，分别对应它关联的两个对象类型，每一侧表示*指向*该对象类型的链接。在我们的示例中，`Aircraft` 对象类型一侧的显示名描述了从 `Flight` *到* `Aircraft` 的链接。你可以选择显示名 `Assigned Aircraft`（分配的飞机），因为一个 `Flight` 有一个 `Assigned Aircraft`。由于两侧是针对同一链接类型一起配置的，该链接类型可以沿任一方向遍历；不需要为反向单独定义链接类型。更多详情请参见[方向性](https://www.palantir.com/docs/foundry/object-link-types/link-types-overview/#directionality)。
3. API 名称将根据显示名自动生成，但你可以根据需要修改。
  - API 名称字段用于在代码中以编程方式引用链接类型。链接类型某一侧的 API 名称可用于返回该类型的对象。例如，如果链接类型 `Aircraft` 一侧的 API 名称是 `assignedAircraft`，那么调用 `Flight.assignedAircraft.get()` 将返回链接到这些 `Flight` 对象的 `Aircraft` 对象。
  - 链接类型 API 名称*必须*遵守以下规则：
    - 以小写字符开头，且仅包含字母数字字符。
    - 在与同一对象类型关联的所有链接类型中唯一。
    - 长度在 1 到 100 个字符之间。
    - 经过 NFKC 规范化。
    - 不能是保留关键字。
  - [详细了解 API 名称。](https://www.palantir.com/docs/foundry/functions/api-objects-links/)
4. 选择 **Next（下一步）**继续。![为链接类型命名](https://www.palantir.com/docs/resources/foundry/object-link-types/create-link-api.png)

### 保存位置

在最后一步中，选择一个项目来保存此链接类型。然后选择 **Submit（提交）**。完成这些步骤后，新链接类型将被创建，但尚未保存。

![新链接类型的保存位置步骤。](https://www.palantir.com/docs/resources/foundry/object-link-types/create-link-type-save-location-step.png?width=500)

### 将更改保存到本体

回到 Ontology Manager 后，选择右上角的 **Save（保存）**以[将更改应用到你的本体](https://www.palantir.com/docs/foundry/ontology-manager/save-changes/)。

## 对象支撑的链接

对象支撑的链接类型在多对一基数链接类型的基础上扩展，为将对象类型作为链接类型存储方案提供一等支持。对象支撑的链接类型允许在链接上包含额外元数据，并支持受限视图。

对于对象支撑的链接，除了 `Aircraft` 和 `Flight` 对象之外，再假设有一个表示 `Flight Manifest`（航班舱单）的对象类型。使用对象支撑的链接，你可以让 `Flight Manifest` 对象类型来链接 `Aircraft` 和 `Flight` 对象。与外键或数据集支撑的链接不同，这个 `Flight Manifest` 对象可以包含额外属性（如 `Pilot`（飞行员）和 `First Mate`（大副）），以提供关于链接的额外元数据。

![对象支撑的链接概览。](https://www.palantir.com/docs/resources/foundry/object-link-types/oblt_overview.png)

### 创建对象支撑链接类型的前提条件

在创建对象支撑的链接类型之前，你必须首先完成以下操作：

1. 创建链接类型两侧的对象类型。更多详情请参见[创建对象类型](https://www.palantir.com/docs/foundry/object-link-types/create-object-type/)。
2. 创建将两个对象类型链接到一起的支撑对象类型。
3. 在链接类型的每一侧与支撑对象类型之间创建多对一链接类型。更多详情请参见[配置新链接类型](#配置新链接类型)。

对于上面的 `Aircraft`、`Flight` 和 `Flight Manifest` 示例，你需要创建以下资源：

1. 创建链接类型两侧的对象类型。
  1. `Aircraft` 对象类型
  2. `Flight` 对象类型
2. 创建将两个对象类型链接到一起的支撑对象类型。
  1. `Flight Manifest` 对象类型
3. 在链接类型的每一侧与支撑对象类型之间创建多对一链接类型。
  1. `Aircraft` 对象类型与 `Flight Manifest` 对象类型之间的链接
  2. `Flight` 对象类型与 `Flight Manifest` 对象类型之间的链接

创建这些资源后，你就可以创建对象支撑的链接类型了。

### 将现有链接转换为对象支撑的链接类型

现有链接可以转换为对象支撑的链接类型。在修改现有链接之前，必须满足对象支撑链接类型的[前提条件](#创建对象支撑链接类型的前提条件)。

要修改现有链接的链接类型：

1. 在 Ontology Manager 中打开该链接。
2. 在 **Configuration（配置）**部分，更新连接方法并选择 **Object type（对象类型）**。
3. 在 **Update link type to object-backed link type（将链接类型更新为对象支撑的链接类型）**对话框中选择支撑对象类型。
4. 在 **Update link type to object-backed link type** 对话框中，从通往支撑对象的链接边中选择链接类型。
5. 选择 **Update to object-backed（更新为对象支撑）**。

### 使用对象支撑的链接类型

目前，对象支撑的链接类型可以在 Object Explorer、Vertex 和 Workshop 中查看。选择某个链接即可查看该链接的支撑对象属性。请注意，在 Vertex 中，链接标题将改为显示该链接的支撑对象标题。

## 故障排查

### 错误：`Phonograph2:DatasetAndBranchAlreadyRegistered`

如果你收到错误 `Phonograph2:DatasetAndBranchAlreadyRegistered`，说明你尝试保存的链接类型所使用的支撑数据源已经在本体中支撑另一个不同的链接类型，无法再次使用。

---

*原文：[Create a link type](https://www.palantir.com/docs/foundry/object-link-types/create-link-type/)*
