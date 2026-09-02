# 创建对象类型（Create an object type）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/create-object-type/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

创建和配置新对象类型的主要方式是使用[**分步骤引导助手**](#使用助手创建新对象类型)。引导助手是推荐的方法；但如果你在完成对象创建流程之前退出了助手，也可以[**手动**](#手动创建新对象类型)完成该流程，即手动指定新对象类型的元数据、支撑数据源、属性映射以及键（主键和标题键）。

创建新对象类型后，你可以[更改 API 名称](#配置-api-名称)，使其不同于分配的默认值。

本页还包含关于新对象类型创建流程的[故障排查](#故障排查)信息。

> **ℹ️ 提示**
>
> 如果你希望以编程方式创建对象类型，请使用 [SuperRepo](https://www.palantir.com/docs/foundry/superrepo/overview/)。SuperRepo 通过[本体即代码（Ontology-as-code）](https://www.palantir.com/docs/foundry/superrepo/core-concepts/#ontology-as-code)在代码中定义对象类型；在 Ontology Manager 中创建的对象类型也可以[导入](https://www.palantir.com/docs/foundry/superrepo/core-concepts/#importing)到 SuperRepo 中。

## 使用助手创建新对象类型

- [创建新对象类型](#创建新对象类型)
- [选择支撑数据源](#选择支撑数据源)
- [对象类型元数据](#对象类型元数据)
- [为对象类型创建属性](#为对象类型创建属性)
- [配置主键和标题键](#配置主键和标题键)
- [生成动作](#生成动作)
- [保存位置](#保存位置)
- [将更改保存到本体](#将更改保存到本体)

### 创建新对象类型

要创建新对象类型，请在 Ontology Manager 主页选择 **Create your first object type** 选项，或在同一页面右上角选择 **New > Create object type**。

![从 New 下拉菜单中选择对象类型。](https://www.palantir.com/docs/resources/foundry/object-link-types/create-object-type-new-object-type.png?width=300)

**Create new object type**（创建新对象类型）助手将会出现。

![新对象类型数据源步骤。](https://www.palantir.com/docs/resources/foundry/object-link-types/create-object-type-datasource-step.png?width=500)

### 选择支撑数据源

如果你在 Foundry 中已有一个包含支撑该对象类型数据的数据源，可以选择它。这会自动填充对象类型的元数据，还会将支撑数据源的每一列映射为一个属性；不过你可以在 **Properties**（属性）步骤中丢弃已添加的属性。

> **⚠️ 注意：警告**
>
> 对象类型的支撑数据源不得包含 `MapType` 或 `StructType` 列。

如果你没有包含该对象类型数据的现有数据源，可以选择不使用现有数据源继续，并选择一个位置来生成用于权限控制的数据集。如果你使用的是 Object Storage v1，则此选项不可用。由于某一类型的对象权限由其支撑数据源的位置决定，系统会提示你选择一个用于保存空数据集的位置。

![新对象类型数据源位置](https://www.palantir.com/docs/resources/foundry/object-link-types/create-object-type-choose-new-datasource-location.png?width=500)

### 对象类型元数据

在此步骤中，提供关于新对象类型的以下信息：

- **Icon（图标）：** 选择默认图标以自定义对象类型的图标和颜色；当用户在用户应用中查看此类型的对象时，将显示该图标和颜色。
- **Name（名称）：** 在用户应用中访问此类型对象时向任何人显示的名称。
- **Plural name（复数名称）：** 在用户应用中访问此类型的多个对象时向任何人显示的名称。
- **Description（描述）：** 面向在用户应用中访问此类型对象的任何人的说明文字。例如，在 Object Explorer 中搜索的用户会在搜索结果中看到该对象类型的描述。
- **Groups（组）：** 选择此对象类型是否将归属于任何组。这是一种组织本体的机制，便于你日后更轻松地筛选出想要使用的对象类型。

![新对象类型元数据步骤。](https://www.palantir.com/docs/resources/foundry/object-link-types/create-object-type-metadata-step.png?width=500)

### 为对象类型创建属性

在对话框的第三个步骤中，你可以自定义对象类型将拥有哪些属性。如果你选择了现有的 Foundry 数据源，所有列都会被自动映射，但可以在此步骤中丢弃。

每个对象类型至少需要一个属性。这是因为对象类型需要主键来唯一标识。向导允许你添加任何其他所需属性。

请注意，需要高级配置的属性类型（如媒体）无法作为引导向导的一部分生成，必须在退出向导后添加。

![新对象类型属性步骤](https://www.palantir.com/docs/resources/foundry/object-link-types/create-object-type-properties-step.png?width=500)

### 配置主键和标题键

作为 **Properties**（属性）步骤的一部分，你需要选择主键和标题键：

- **Title key（标题键）：** 充当此类型对象显示名的属性。
  - 例如，选择 `full name` 属性作为 `Employee` 对象类型的标题键，将使用该属性的值（如“Melissa Chang”、“Akriti Patel”或“Diego Rodriguez”）作为各自对应概念上的 `Employee` 对象的显示名。
- **Primary key（主键）：** 充当对象类型每个实例唯一标识符的属性。支撑数据源中的每一行在此属性上必须具有不同的值。
  - 例如，`employee ID` 属性的值将用于在组织内将“Melissa Chang”标识为唯一员工。

受支持的属性类型列表可在[对象类型属性文档](https://www.palantir.com/docs/foundry/object-link-types/properties-overview/#supported-property-types)中找到。

![新对象类型](https://www.palantir.com/docs/resources/foundry/object-link-types/create-object-type-configure-keys-helper.png?width=500)

> **⚠️ 注意**
>
> 在分配主键之前，请务必检查支撑数据源中是否存在重复项。你选择的主键对于数据源中的每条记录都必须唯一。如果你的本体使用 [Object Storage v2](https://www.palantir.com/docs/foundry/object-backend/overview/)，重复的主键会导致 [Funnel 批处理管道](https://www.palantir.com/docs/foundry/object-indexing/funnel-batch-pipelines/)错误，进而导致构建失败。如果你使用的是 Object Storage v1（Phonograph），更新会显示为成功；然而，重复的主键可能会对你的本体造成意外更改。

> **⚠️ 注意**
>
> 主键应当是确定性的。如果主键不具备确定性，并在构建时发生变化，编辑可能会丢失，链接可能会消失。编辑之所以会丢失，是因为本体编辑与对象的主键相关联。如果构建没有协调一致地更新链接 ID，对象之间的链接就可能消失。为确保主键是确定性的，你应当定义管道逻辑，使主键成为单个列或多个列的函数。避免使用编号行或随机键生成，因为这些做法会导致主键在不同构建运行之间发生变化。

### 生成动作

你可以选择生成一组标准动作来编辑此类型的对象，并指定可以运行这些动作的特定用户或用户组。

请注意，即使你已经完成对象类型的创建并退出了助手，仍然可以编辑这些动作或创建新的附加动作。

![新对象类型动作步骤](https://www.palantir.com/docs/resources/foundry/object-link-types/create-object-type-actions-step.png?width=500)

如果你使用的是 Object Storage v1，则此步骤不可用。

### 保存位置

在最后一步中，选择一个项目来保存此对象类型。然后，选择 **Create**。选择 **Create** 只会暂存你的更改，**并不会保存**它们。

![新对象类型保存位置步骤。](https://www.palantir.com/docs/resources/foundry/object-link-types/create-object-type-save-location-step.png?width=500)

### 将更改保存到本体

回到 Ontology Manager 后，选择右上角的 **Save** 以[将更改应用到你的本体](https://www.palantir.com/docs/foundry/ontology-manager/save-changes/)。

## 手动创建新对象类型

使用助手创建新对象类型时，可以在完成上文[**创建新对象类型**助手说明](#使用助手创建新对象类型)中的所有步骤之前选择 **Create**。在流程完成之前选择 **Create** 会退出助手并将你带到 **Overview**（概览）页面。

此时，对象类型尚未保存，并且在完成以下所有步骤之前无法保存。手动完成创建流程（在 **创建新对象类型** 助手之外）的步骤如下：

- [为新对象类型添加元数据](#为新对象类型添加元数据)
- [为新对象类型添加支撑数据源](#为新对象类型添加支撑数据源)
- [添加新属性](#添加新属性)
- [将单个属性映射到数据](#将单个属性映射到数据)
- [将所有未映射的列映射为新属性](#将所有未映射的列映射为新属性)
- [配置主键和标题键](#配置主键和标题键)

### 为新对象类型添加元数据

在 **Overview** 页面的元数据部分，你可以编辑对象类型的显示名、复数显示名、描述和 ID：

1. **Display name（显示名）：** 在用户应用中访问此类型对象时向任何人显示的名称。
2. **Plural display name（复数显示名）：** 在用户应用中访问此类型多个对象时向任何人显示的名称。
3. **Aliases（别名）：** 附加词语，用户搜索这些词语时也能找到该对象类型。
4. **Description（描述）：** 面向在用户应用中访问此类型对象的任何人的说明文字。例如，在 Object Explorer 中搜索的用户会在搜索结果中看到该对象类型的描述。
5. **Groups（组）：** 有助于对对象类型进行分类的一个或多个标签。
6. **ID：** 对象类型的唯一标识符，主要用于在配置用户应用时引用此类型的对象。
  - ID 可以包含小写字母、数字和短横线。
  - ID 的第一个字符**必须**是小写字母。
  - 一旦属性的 ID 被保存且该属性在用户应用中被引用，对属性 ID 的**任何**更改都会导致应用中断。
7. **Icon（图标）：** 从对象类型视图侧边栏中选择默认图标，以自定义对象类型的图标和颜色；当用户在用户应用中查看此类型的对象时，将显示该图标和颜色。
8. **Backing datasource（支撑数据源）：** 用作此类型对象属性值的数据来源。

![概览页面](https://www.palantir.com/docs/resources/foundry/object-link-types/create-object-type-new-object-overview-page-annotated.png)

---

### 为对象类型添加组

[组（Groups）](https://www.palantir.com/docs/foundry/object-link-types/type-groups/)是有助于对对象类型进行分类的标签。在对象类型元数据组件中，你可以：

- 从现有组列表中添加一个组。
- 通过输入组名来创建新组。
- 从你的对象类型中移除一个组。

![选择或添加新的对象类型组](https://www.palantir.com/docs/resources/foundry/object-link-types/object-type-groups-create.png?width=450)

组可以在 [Ontology Manager 的 **Search** 栏和 **Search** 栏对话框](https://www.palantir.com/docs/foundry/ontology-manager/navigation/#header-search-bar)中搜索。Ontology Manager 中的对象类型表格支持按组显示和筛选。组还会显示在 [Object Explorer 主页](https://www.palantir.com/docs/foundry/object-explorer/getting-started/#group-exploration-b-c-d)上。

![添加新的对象类型组](https://www.palantir.com/docs/resources/foundry/object-link-types/object-type-groups-add.png?width=300)

> **⚠️ 注意：警告**
>
> 在对象类型元数据中以组作为标签的做法，取代了先前向主键属性添加 `oe_home_page_object_type_group` 类型类的方法；先前的方法已不再可用。

---

### 为新对象类型添加支撑数据源

为了用数据填充此类型对象的属性值，你必须添加一个支撑数据源。你可以通过以下方式完成：

- 在 **Overview** 页面的 **Properties** 部分选择 **Create new**，或在对象类型视图侧边栏的 **Properties** 页面选择 **Edit property mapping** 按钮，进入属性编辑器。
- 然后，如下图所示选择 **Add a backing datasource** 按钮。这将允许你选择 Foundry 中任意可用数据源作为支撑数据源。
  - 请注意，单个数据源只能用于支撑一个对象类型。

![编辑支撑数据集](https://www.palantir.com/docs/resources/foundry/object-link-types/create-object-type-edit-backing-dataset.png)

### 添加新属性

在属性编辑器中，选择屏幕右侧 **Properties** 窗格中的 **Add**。这会向对象类型添加一个新属性。

![添加新属性](https://www.palantir.com/docs/resources/foundry/object-link-types/create-object-type-add-new-property.png)

### 将单个属性映射到数据

可以通过以下任一方式将属性映射到支撑数据源中的列：

- [将列映射为新属性](#将列映射为新属性)
- [将列映射到现有属性](#将列映射到现有属性)
- [将属性映射到列](#将属性映射到列)

#### 将列映射为新属性

在屏幕左侧的数据源窗格中（见下图），你可以看到数据源的所有列。将鼠标悬停在要映射的列上，选择 **Add as a new property** 按钮，以创建一个映射到该列的新属性。属性 ID、显示名和基础类型将根据列名推断得出。

![作为新属性添加](https://www.palantir.com/docs/resources/foundry/object-link-types/create-object-type-add-as-a-new-property.png)

#### 将列映射到现有属性

在屏幕左侧的数据源窗格中，你可以看到数据源的所有列。将鼠标悬停在未映射的列上，选择 **Add as a new property** 按钮。如果已存在属性 ID 与列名匹配的属性，该列将映射到该现有属性。

#### 将属性映射到列

在屏幕右侧的属性窗格中，将鼠标悬停在要映射到列的属性上，选择 **Map to a column**。这将打开一个下拉菜单，你可以从中选择要映射到该属性的列。

![将属性映射到列](https://www.palantir.com/docs/resources/foundry/object-link-types/create-object-type-map-property-to-column.png)

### 将所有未映射的列映射为新属性

在数据源窗格中数据源名称旁边，你会找到 **Add all unmapped columns as new properties** 按钮。选择该按钮将为数据源中所有未映射的列创建属性。属性的 ID、显示名和基础类型将根据数据源中对应的列推断得出。

- 一旦属性的 ID 被保存且该属性在用户应用中被引用，对属性 ID 的**任何**更改都会导致应用中断。

![将所有未映射的列作为新属性添加](https://www.palantir.com/docs/resources/foundry/object-link-types/create-object-type-add-all-unmapped-columns-as-new-properties.png)

### 配置主键和标题键

现在你已经创建了新对象类型、添加了支撑数据源并将其映射到新属性，但在能够保存对象类型之前，仍需配置主键和标题键。你可以前往属性编辑器中的属性元数据窗格（见下图），将某个属性设置为主键和标题键：

![配置主键和标题键](https://www.palantir.com/docs/resources/foundry/object-link-types/create-object-type-configure-keys-manual.png?width=500)

- **Primary key（主键）：** 充当对象类型每个实例唯一标识符的属性。支撑数据源中的每一行在此属性上必须具有不同的值。
  - 例如，`employee ID` 属性的值将用于在组织内将“Melissa Chang”标识为唯一的（概念上的）员工。
  - 要配置主键，请在属性编辑器的属性窗格中选择要分配为主键的属性，并勾选 **Primary key** 选项。
  - 编辑会永久绑定到其对应的主键值上。每当你更改对象类型的主键时，系统都会提示你删除所有现有编辑。
- **Title key（标题键）：** 充当此类型对象显示名的属性。
  - 例如，选择 `full name` 属性作为 `Employee` 对象类型的标题键，将使用该属性的值（如“Melissa Chang”、“Akriti Patel”或“Diego Rodriguez”）作为各自对应概念上的 `Employee` 对象的显示名。
  - 要配置标题键，请在属性编辑器的属性窗格中选择要分配为标题键的属性，并勾选 **Title key** 选项。

请注意，到目前为止，你的更改已被暂存，但**尚未保存**。要将新对象类型保存到本体，请按照[如何将更改保存到本体](https://www.palantir.com/docs/foundry/ontology-manager/save-changes/)中的说明操作。

> **⚠️ 注意**
>
> 在分配主键之前，请务必检查支撑数据源中是否存在重复项。你选择的主键对于数据源中的每条记录都必须唯一。如果你的本体使用 [Object Storage v2](https://www.palantir.com/docs/foundry/object-backend/overview/)，重复的主键会导致 [Funnel 批处理管道](https://www.palantir.com/docs/foundry/object-indexing/funnel-batch-pipelines/)错误，进而导致构建失败。如果你使用的是 Object Storage v1（也称为 Phonograph），更新会显示为成功；然而，重复的主键可能会对你的本体造成意外更改。

## 配置 API 名称

API 名称是在代码中以编程方式引用对象类型或属性时使用的名称。所有新对象类型和属性都会被自动分配根据其显示名推断得出的 API 名称。[详细了解 API 名称。](https://www.palantir.com/docs/foundry/functions/api-objects-links/)

你可以按以下方式更改自动分配的 API 名称：

- 对象类型的 API 名称可以在该对象类型的 **Overview** 页面中编辑。
- 属性的 API 名称可以在属性编辑器的属性窗格中编辑。

### 命名指南

对象类型 API 名称遵循函数式编码标准。对象类型的 API 名称必须：

- 以大写字符开头，且仅由字母数字字符组成。
- 采用 PascalCase（也称为 UpperCamelCase，即复合词中每个单词的首字母大写；例如“ThisExampleName”）。
- 在所有对象类型中唯一。
- 长度在 1 到 100 个字符之间。

![对象类型 API](https://www.palantir.com/docs/resources/foundry/object-link-types/create-object-type-api.png?width=500)

属性的 API 名称必须：

- 以小写字符开头，且仅由字母数字字符组成。
- 采用 camelCase（即复合词中第一个单词之后每个单词的首字母大写；例如“thisExampleName”）。
- 在属于同一对象类型的所有属性中唯一。
- 长度在 1 到 100 个字符之间。

![属性类型 API](https://www.palantir.com/docs/resources/foundry/object-link-types/create-object-type-property-type-api.png?width=500)

## 故障排查

### 对象类型必填字段

要保存新对象类型，以下对象类型字段不得为空：

- ID
- 显示名（Display name）
- 复数显示名（Plural display name）
- 支撑数据源（Backing datasource）
- API 名称（API name）

此外，以下属性字段不得为空：

- 属性 ID（Property ID）
- 属性显示名（Property display name）
- 支撑列（Backing column）
- 属性 API 名称（Property API name）
- 标题键（Title key）
- 主键（Primary key）

### 有效 ID 检查清单

#### 对象类型 ID

对象类型 ID：

- 可以由小写字母、数字和短横线组成。
- 应当以字母开头。
- 必须在所有对象类型中唯一。

#### 属性类型 ID

属性类型 ID：

- 可以由小写或大写字母、数字、短横线和下划线组成。
- 应当以字母开头。
- 必须在属于同一对象类型的所有属性中唯一。

#### API 名称

根据函数式编码标准，对象类型的 API 名称必须：

- 仅由字母数字字符和下划线组成。
- 在所有对象类型中唯一。
- 长度在 1 到 100 个字符之间。

属性的 API 名称必须：

- 为有效的 Unicode。
- 在属于同一对象类型的所有属性中唯一。
- 长度在 1 到 100 个字符之间。

请注意，有若干保留关键字不能用于 API 名称，它们是：`ontology`、`object`、`property`、`link`、`relation`、`rid`、`primaryKey`、`typeId` 和 `ontologyObject`。

### 错误

#### 错误：`Phonograph2:DatasetAndBranchAlreadyRegistered`

如果你收到错误 `Phonograph2:DatasetAndBranchAlreadyRegistered`，则表示你尝试保存的对象类型的支撑数据源已经在本体中支撑着另一个对象类型，无法再次使用。

---

*原文：[Create an object type](https://www.palantir.com/docs/foundry/object-link-types/create-object-type/)*
