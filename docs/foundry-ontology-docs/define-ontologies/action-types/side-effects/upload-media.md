# 上传媒体（Upload media）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/action-types/upload-media/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

动作支持使用动作表单或表格上传媒体文件。对于 Foundry 中的大多数用例，推荐将文件上传到媒体引用属性（media reference property）。

媒体引用属性（由[媒体集（media sets）](https://www.palantir.com/docs/foundry/data-integration/media-sets/)支撑）相比附件属性具有若干优势：

- **可扩展性（Scalability）：**支持数十亿个文件，具有高效的存储和检索能力。
- **内置转换（Built-in transformations）：**支持许多媒体转换和 LLM 功能，开箱即用、简单易用。
- **高级预览（Advanced previews）：**为受支持的格式提供内置渲染和丰富的预览功能。
- **格式支持（Format support）：**支持标准格式和专用格式（如 NITF、GeoTIFF 和 DICOM）上的定制工作流。

用户可以通过文件选择器界面上传媒体文件，文件在动作成功提交后持久化到媒体集。

> **⚠️ 注意**
>
> [格式转换](https://www.palantir.com/docs/foundry/media-sets-advanced-formats/media-overview/#additional-input-formats)仅在动作完成且媒体文件已上传到媒体集之后才会发生。

## 配置

有关配置媒体引用属性和设置媒体上传动作的详细说明，请参见[配置媒体引用属性](https://www.palantir.com/docs/foundry/object-link-types/base-types/#configure-media-reference-properties)和[上传媒体](https://www.palantir.com/docs/foundry/media-sets-advanced-formats/upload-media/)。

## 权限

通过动作上传媒体的权限由[动作提交条件](https://www.palantir.com/docs/foundry/action-types/submission-criteria/)管理。如果用户满足动作提交条件，他们无需对支撑媒体集拥有任何权限即可上传媒体。

> **ℹ️ 提示**
>
> 媒体集的编辑权限会在它第一次被添加到对象类型或第一次被动作类型引用时进行检查。将媒体集添加到本体后，访问控制就从媒体集委托给了本体。这意味着任何能够在该对象类型上管理动作的人，都可以控制谁能够向该媒体集上传媒体。

---

*原文：[Upload media](https://www.palantir.com/docs/foundry/action-types/upload-media/)*
