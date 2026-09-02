# 参数配置的性能注意事项（Performance considerations for parameter configuration）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/action-types/parameter-performance-considerations/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

参数之间的依赖关系（例如在[默认值](https://www.palantir.com/docs/foundry/action-types/parameters-default-value/)和[多选选项](https://www.palantir.com/docs/foundry/action-types/parameters-filter/)的定义中）可能会影响动作表单的加载时间。例如，考虑以下动作参数配置：

1. 第一个参数是 `object reference`（对象引用），其默认值为 `from single result of object set`（来自对象集的单个结果）。
2. 第二个参数是字符串，其默认值是引用第一个参数的 `object parameter property`（对象参数属性）。
3. 第三个参数也是字符串，没有默认值，但配置为使用 `get options from an object set`（从对象集获取选项）的 `multiple choice`（多选）下拉菜单。该对象集定义引用了第二个参数。

当用户加载此动作的动作表单时，需要迭代执行多个操作。

1. 首先，需要检索第一个参数的默认值。
2. 然后，需要从第一个参数值派生第二个参数的默认值。
3. 最后，需要从第二个参数值派生第三个参数的选项。

配置动作参数时，建议使依赖层次尽可能扁平。对于上述动作，在第三个参数的对象集定义中引用第一个参数而非第二个参数，将允许并行派生第二个和第三个参数所需的信息，从而缩短从打开表单到表单完全可交互之间的总延迟。

---

*原文：[Performance considerations for parameter configuration](https://www.palantir.com/docs/foundry/action-types/parameter-performance-considerations/)*
