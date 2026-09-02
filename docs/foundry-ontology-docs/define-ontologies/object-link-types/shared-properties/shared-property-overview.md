# 共享属性（Shared properties）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/shared-property-overview/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

**共享属性（shared property）** 是可以在你的本体中多个[对象类型](https://www.palantir.com/docs/foundry/object-link-types/object-types-overview/)上使用的[属性](https://www.palantir.com/docs/foundry/object-link-types/properties-overview/)。共享属性支持跨对象类型的一致数据建模，并支持对属性元数据进行集中管理。虽然属性元数据在对象之间共享，但底层对象数据并不共享。

例如，在 Ontology Manager 中，你可能有 `Employee` 和 `Contractor` 对象类型，它们都具有 `start date` 属性。通过创建 `start date` 共享属性并将其用于这两个对象类型，你可以使用一致的属性对数据建模，并在一个地方而不是在每个对象类型上更新 `start date` 元数据。

共享属性可以[直接创建](https://www.palantir.com/docs/foundry/object-link-types/create-shared-property/)，也可以将对象类型上的现有属性转换为共享属性。添加到你的本体后，共享属性可以作为数据本体化过程的一部分在对象类型上[使用](https://www.palantir.com/docs/foundry/object-link-types/use-shared-property/)，并以类似于常规属性的方式进行[编辑](https://www.palantir.com/docs/foundry/object-link-types/edit-shared-property/)。

对象上的共享属性通过其名称旁边的地球图标标示。

![Ontology Manager 中的共享属性页面](https://www.palantir.com/docs/resources/foundry/object-link-types/shared-property-menu-option.png?width=800)

---

*原文：[Shared properties](https://www.palantir.com/docs/foundry/object-link-types/shared-property-overview/)*
