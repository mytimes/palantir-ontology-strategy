# 对象类型（Object types）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/object-types-overview/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

**对象类型**是现实世界实体或事件的模式定义。
**对象或对象实例**是指对象类型的单个实例；一个对象对应于单个现实世界实体或事件。
**对象集**是指多个对象实例的集合；也就是说，对象集代表一组现实世界实体或事件。

例如，在 Ontology Manager 中，你可以创建一个 `Employee` 对象类型，用于定义“所有员工”或该类型所有对象的特征。对象是指 `Employee` 对象类型的单个实例，例如虚构员工“Melissa Chang”、“Akriti Patel”或“Diego Rodriguez”。像“所有终身教职员工”这样的一组对象就代表一个对象集。

类似地，在 Ontology Manager 中，你可以创建一个 `Flight` 对象类型，用于定义“所有航班”或该类型所有对象的特征。对象是指 `Flight` 对象类型的单个实例，例如“JFK → SFO 2021-02-24”或“TLV → LHR 2020-04-16”。像“所有已抵达航班”这样的一组对象就代表一个对象集。

支撑本体的概念与数据集结构中的概念存在对应关系。本体中对象类型的定义类似于数据集的定义，而对象的定义类似于数据集中一行的定义。对象集的定义类似于数据集中经过筛选的一组行。例如，`Employee` 数据集可能定义“所有员工行”的模式。在这种情况下，单行对应单个员工，如“Melissa Chang”、“Akriti Patel”或“Diego Rodriguez”。如果你根据终身教职状态筛选数据集，就会得到一组代表“所有终身教职员工”的行。

Foundry 本体并非抽象数据模型，而是将每个本体概念映射到组织的实际数据，使这一数据资产能够驱动现实世界应用。在 Ontology Manager 中为对象类型添加支撑数据源，即可在用户应用中创建和显示对象。要创建 `Employee` 类型的对象，组织需要为 `Employee` 对象类型添加支撑数据源，并将其员工名录和其他企业数据连接到本体中。

请从学习如何[创建对象类型](https://www.palantir.com/docs/foundry/object-link-types/create-object-type/)开始。

---

*原文：[Object types](https://www.palantir.com/docs/foundry/object-link-types/object-types-overview/)*
