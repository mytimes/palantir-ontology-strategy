# 使用值类型（Use value types）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/use-value-type/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

[创建值类型](https://www.palantir.com/docs/foundry/object-link-types/create-value-type/)后，你可以在整个 Foundry 中将其用作数据类型。值类型可支持以下列出的用例。

- 将值类型分配给对象类型属性。
- 将值类型分配给共享属性。
- 使用 `logical type cast`（逻辑类型转换）表达式将值类型作为逻辑类型分配给 Pipeline Builder 管道属性，并在写入对象目标时在属性上选择该值类型。

要将值类型分配给属性，请在属性配置期间从下拉菜单中选择该值类型。

![约束更新警告](https://www.palantir.com/docs/resources/foundry/object-link-types/value-type-use.png?width=500)

> **⚠️ 注意**
>
> 如果你将值类型应用于包含未通过验证的属性值的对象属性，该对象类型将无法完成索引构建。你可以在 Ontology Manager 的对象类型健康状态中查看此类索引失败，并在其中更正数据或更新值类型以解决问题。

---

*原文：[Use value types](https://www.palantir.com/docs/foundry/object-link-types/use-value-type/)*
