# 本体（Ontologies）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/ontologies/ontologies-overview/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

本体是一种产物（artifact），用于存储本体资源或实体，包括以下内容：

- [对象类型](https://www.palantir.com/docs/foundry/object-link-types/object-types-overview/)
- [链接类型](https://www.palantir.com/docs/foundry/object-link-types/link-types-overview/)
- [动作类型](https://www.palantir.com/docs/foundry/action-types/overview/)
- [接口](https://www.palantir.com/docs/foundry/interfaces/interface-overview/)
- [共享属性](https://www.palantir.com/docs/foundry/object-link-types/shared-property-overview/)
- [对象类型组](https://www.palantir.com/docs/foundry/object-link-types/type-groups/)

我们将这些资源称为**本体资源**。本体可以是私有的并分配给单个[组织](https://www.palantir.com/docs/foundry/security/orgs-and-spaces/)，也可以在多个组织之间共享。共享本体允许不同组织的用户安全地共享数据和工作流。将实体分组到本体中可确保只有指定组织的用户才能访问本体实体。

## 与空间的关系

本体与[空间](https://www.palantir.com/docs/foundry/security/orgs-and-spaces/#spaces)按 1:1 映射。创建新空间时，会同时创建一个同名的对应本体，并具有与该空间相同的组织[标记](https://www.palantir.com/docs/foundry/security/markings/)。私有空间映射到私有本体，而共享空间映射到共享本体。

## 私有本体与共享本体

你需要哪种本体，取决于有多少[组织](https://www.palantir.com/docs/foundry/security/orgs-and-spaces/#organizations)必须使用相同的对象类型、链接类型和动作类型。组织会对不同的用户群体和资源群体实施严格隔离；在大多数情况下，一家公司只有一个组织。只有应用于本体的组织的成员和访客成员，才能被授予对其所持资源的访问权限。

|  | 私有本体 | 共享本体 |
| --- | --- | --- |
| **选择的时机** | 需要这些对象的所有人都在一个组织中 | 两个或更多组织中的人员需要相同的对象 |
| **应用的组织** | 一个 | 两个或更多 |
| **可被授予访问权限的人员** | 该组织的成员和访客成员 | 任何已应用组织的成员和访客成员 |
| **随同创建** | 一个私有空间 | 一个共享空间 |

### 何时使用私有本体

当需要这些对象的所有人都属于一个组织时，选择私有本体。这涵盖了大多数工作流。

例如，Sky Industries（天空工业公司）为其自己的支持团队构建了一个 Flight Alert Inbox（航班警报收件箱）应用。该应用使用 `Flight`（航班）、`Flight Alert`（航班警报）、`Delay`（延误）和 `Aircraft`（飞机）对象类型、连接它们的链接类型，以及其用户应用的动作类型。所有这些都描述了 Sky Industries 的运营，且只有 Sky Industries 员工才能查看。Sky Industries 在其私有空间中构建该工作流，因此映射到该空间的本体将所有这些资源的访问限制在 Sky Industries 组织内部。

有多个团队共享这一个本体：使用该应用的运营使用者、构建该应用的应用开发者，以及维护其支撑数据的管道开发者。与其他团队协作并不是创建第二个本体的理由。要控制哪些团队可以访问哪些资源，请在持有这些资源的项目上向[用户组](https://www.palantir.com/docs/foundry/security/users-and-groups/#groups)授予[角色](https://www.palantir.com/docs/foundry/security/projects-and-roles/#roles)。

### 何时使用共享本体

当两个或更多组织中的人员必须使用相同的对象时，选择共享本体。

延续上面的示例，Sunrise Airline（日出航空）希望减少由维护问题导致的飞机延误，并同意将其维护数据与 Sky Industries 的航班延误数据合并。两家公司需要一套横跨双方的对象类型：来自 Sunrise Airline 数据的维护问题，链接到从 Sky Industries 数据构建的 `Aircraft` 和 `Delay` 对象。两家公司的分析师都必须能够打开这些对象并应用相同的动作类型。私有本体无法支持这一点：它仅限于单个组织，因此无法向另一家公司的用户授予对其资源的访问权限。

相反，管理员会创建一个同时应用了 Sky Industries 和 Sunrise Airline 组织的共享空间。与该空间一起创建的共享本体带有两个组织标记，因此两家公司的分析师都可以被授予对联合对象类型、链接类型和动作类型的访问权限。每家公司为其不想共享的数据和工作流保留自己的私有空间和私有本体。

> **ℹ️ 提示**
>
> 创建共享本体本身并不会使底层数据对每个已应用组织可见。从私有项目引用到共享项目的数据集会保留其来源组织的访问要求。在开发者移除继承的要求之前，另一个组织的用户仍无法查看该数据。有关详情，请参阅[移除继承的标记和组织](https://www.palantir.com/docs/foundry/building-pipelines/remove-inherited-markings/)。

有关此场景的分步演练（包括如何创建组织、空间和共享项目），请参阅[工作流：跨组织协作](https://www.palantir.com/docs/foundry/security/cross-organization-collaboration/)。有关共享本体的更多详情，请参阅[共享本体](https://www.palantir.com/docs/foundry/ontologies/shared-ontologies/)。

---

*原文：[Ontologies](https://www.palantir.com/docs/foundry/ontologies/ontologies-overview/)*
