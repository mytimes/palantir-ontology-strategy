# 基础类型（Base types）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/object-link-types/base-types/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

属性基础类型定义了可存储在属性中的数据种类。有关所有受支持属性基础类型的完整参考，请参阅[属性概览](https://www.palantir.com/docs/foundry/object-link-types/properties-overview//#supported-property-types)。

**基础类型**用于定义对象上的属性。属性的基础类型决定了该属性在用户应用中可用的操作集合。除 `Map` 和 `Binary` 类型外，所有字段类型都是有效的基础类型。

基础类型还包括以下高级类型：

- **向量（Vector）：** 用于在对象上存储[向量](https://www.palantir.com/docs/foundry/announcements/2023-11/#configure-a-vector-property-type)以供语义搜索使用的类型。
- **`Geopoint`：** 用于定义表示地理[点](https://www.palantir.com/docs/foundry/geospatial/ontology/#points)的属性的类型。
- **`Geoshape`：** 用于定义表示地理[形状](https://www.palantir.com/docs/foundry/geospatial/ontology/#polygons-and-lines)的属性的类型。
- **附件（Attachment）：** 用于在对象上存储文件以供[对象函数](https://www.palantir.com/docs/foundry/functions/attachments/)使用的类型。
- **时间序列（Time series）：** 用于将属性定义为[时间序列](https://www.palantir.com/docs/foundry/time-series/time-series-overview/)的类型。
- **地理时间序列（Geotemporal series）：** 用于将属性定义为对[地理时间序列](https://www.palantir.com/docs/foundry/geospatial/geotemporal-series-overview/)的引用的类型。
- **媒体引用（Media reference）：** 用于定义[对媒体文件的引用](https://www.palantir.com/docs/foundry/media-sets-advanced-formats/media-overview/#media-references)的类型。
- **密文（Cipher text）：** 用于存储使用 [Cipher](https://www.palantir.com/docs/foundry/cipher/overview/) 编码的字符串值的类型。
- **结构体（Struct）：** 用于定义具有[多个字段](https://www.palantir.com/docs/foundry/object-link-types/structs-overview/)的基于模式的属性的类型。

所有基础类型都可用于数组中以表示属性的多个值，但 `Vector` 和 `Time series` 类型除外。

## 复杂属性基础类型

某些属性基础类型需要额外配置或具有特定用例。有关以下属性基础类型的信息，请参阅下文各节：

- **[媒体引用](#媒体引用)：** 引用存储在媒体集中的媒体项。
- **[结构体](#结构体)：** 具有多个字段的复杂结构化数据。

### 媒体引用

**媒体引用**属性类型允许你在对象上存储媒体，例如图像、视频、音频文件和文档。[媒体引用](https://www.palantir.com/docs/foundry/media-sets-advanced-formats/media-overview/#media-references)指向[媒体集](https://www.palantir.com/docs/foundry/data-integration/media-sets/)中的特定媒体项。媒体引用包含有关媒体文件的信息，这意味着 Foundry 可以在使用该媒体引用的任何位置显示媒体。

#### 媒体引用格式

下面是一个媒体引用示例：

```json
{
  "mimeType": "image/png",
  "reference": {
    "type": "mediaSetViewItem",
    "mediaSetViewItem": {
      "mediaSetRid": "ri.mio.main.media-set.00000000-0000-0000-0000-00000000000",
      "mediaSetViewRid": "ri.mio.main.view.00000000-0000-0000-0000-00000000000",
      "mediaItemRid": "ri.mio.main.media-item.00000000-0000-0000-0000-00000000000"
    }
  }
}
```

媒体引用包含以下内容：

- **`mimeType`：** 文件的媒体类型。
- **`reference`：** 包含媒体集 RID、视图 RID 和特定媒体项 RID 的引用。

#### 配置媒体引用属性

具有媒体引用属性的对象类型由数据集支撑。支撑数据集必须包含一个媒体引用列，该列将映射到媒体引用属性。此列类型专门用于存储媒体引用值，并确保本体对象与媒体集之间的正确集成。

![媒体引用属性的来源。](https://www.palantir.com/docs/resources/foundry/object-link-types/media-reference-source.png)

此外，媒体引用属性必须具有**媒体源（media source）**，可以在对象类型的 **Capabilities** 选项卡中配置。该媒体源应该是媒体引用所指向的媒体集。

![“Capabilities”选项卡中的媒体引用属性。](https://www.palantir.com/docs/resources/foundry/object-link-types/media-reference-media-source.png)

### 结构体

**结构体**是一种本体属性基础类型，允许用户创建具有多个字段的基于模式的属性。结构体属性是从结构体类型的数据集列创建的。要详细了解结构体，请参阅完整的[结构体](https://www.palantir.com/docs/foundry/object-link-types/structs-overview/)文档。

---

*原文：[Base types](https://www.palantir.com/docs/foundry/object-link-types/base-types/)*
