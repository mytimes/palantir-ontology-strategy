# 值类型（Value types）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/value-types-overview/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

**值类型**是围绕[字段类型](https://www.palantir.com/docs/foundry/data-integration/datasets/#supported-field-types)的语义包装器，包含元数据和约束，可以增强类型安全、提高表达能力并提供附加上下文。值类型封装特定领域的数据类型，并以可在整个平台复用的方式强制执行数据验证。与定义和构建本体的对象类型、属性、链接类型或其他类型不同，值类型与平台中的[空间](https://www.palantir.com/docs/foundry/security/orgs-and-spaces/#spaces)相关联。一个空间可以容纳单个本体。值类型只能在定义它们的空间内使用。默认本体不可使用值类型。

数据集[字段类型](https://www.palantir.com/docs/foundry/data-integration/datasets/#supported-field-types)和属性[基础类型](https://www.palantir.com/docs/foundry/object-link-types/base-types/)反映了编程语言中的原始类型。这些类型与领域无关，不提供任何领域上下文。相比之下，值类型捕获数据的上下文和语义含义，并集中管理数据验证。用户直接从值类型定义和获取含义，而不是依赖列名或属性描述等周边信息。值类型还在 Builder 管道和本体中对数据强制执行其验证约束，因此数据集成者和本体管理者可以确保其数据流和模型中的语义类型正确。

例如，用户可以定义一个“email”（电子邮件）值类型，它带有正则表达式约束，以确保使用该值类型的任何属性都表示有效的电子邮件地址。然后，该值类型可以在多个对象类型和管道中复用，而不必为每个此类属性重复验证逻辑。此外，使用此值类型的每个属性都会被明确理解为包含电子邮件地址。

由于值类型旨在跨多个管道和对象类型复用，因此它们受到[权限控制](https://www.palantir.com/docs/foundry/object-link-types/value-types-permissions/)，以确保用户可以在需要的地方应用它们，并且进行了[版本管理](https://www.palantir.com/docs/foundry/object-link-types/value-types-versions/)，以处理破坏性和非破坏性编辑。

请首先学习如何[创建新值类型](https://www.palantir.com/docs/foundry/object-link-types/create-value-type/)或在属性上[使用现有值类型](https://www.palantir.com/docs/foundry/object-link-types/use-value-type/)。

---

*原文：[Value types](https://www.palantir.com/docs/foundry/object-link-types/value-types-overview/)*
