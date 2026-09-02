# 元数据参考（Metadata reference）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/interfaces/interface-metadata/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

接口在本体中由以下元数据表示：

- **RID：** 为 Palantir 中每个资源自动生成的唯一标识符。整个平台的错误消息都会引用接口的 RID。
- **图标：** 用作视觉标识符的图片和颜色，在用户于应用中查看此接口时显示。接口的图标周围有虚线，以在视觉上将它们与对象类型图标区分开。例如，被虚线包围的建筑物图标可用于表示 `Facility`（设施）接口。
- **显示名：** 在用户应用中向任何访问此接口的人显示的名称。例如，`Facility` 接口的显示名可能是 “Facility”。
- **描述：** 任何人都可以在用户应用中阅读的、关于接口的说明性文字。例如，`Facility` 接口的描述可能是 “An abstract object type interface for representing airline facilities”（用于表示航空公司设施的抽象对象类型接口）。
- **API 名：** 在代码中以编程方式引用接口时使用的名称。例如，`Facility` 接口的 API 名可能是 `facility`。
- **状态：** 向用户和本体构建者表明接口在开发过程中所处阶段的信号。它可以是 `active`、`experimental`、`example` 或 `deprecated`。默认情况下，`Facility` 接口将具有 `experimental` 状态。详细了解[状态](https://www.palantir.com/docs/foundry/object-link-types/metadata-statuses/)。
- **可搜索：** 一个布尔值，指定接口是否可搜索。可搜索接口使用户能够一次加载或搜索该接口的所有对象。可搜索接口限制为 50 个实现对象类型，而不可搜索接口限制为 1,000 个。默认情况下，`Facility` 接口将是可搜索的。

---

*原文：[Metadata reference](https://www.palantir.com/docs/foundry/interfaces/interface-metadata/)*
