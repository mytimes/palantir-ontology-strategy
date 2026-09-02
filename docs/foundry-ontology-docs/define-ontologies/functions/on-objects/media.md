# 媒体（Media）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/media/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

函数使你能够在 [TypeScript v2](https://www.palantir.com/docs/foundry/functions/typescript-v2-getting-started/)、[Python](https://www.palantir.com/docs/foundry/functions/python-getting-started/) 和 [TypeScript v1](https://www.palantir.com/docs/foundry/functions/typescript-v1-getting-started/) 中访问和修改媒体。TypeScript v2 和 Python 使用 `Media` 类型来读取、上传和转换媒体，并支持通过[本体编辑](https://www.palantir.com/docs/foundry/functions/edits-overview/)和 OSDK 上传媒体。TypeScript v1 函数提供 `MediaItem` 类型，内置了处理不同种类媒体的操作，无需使用外部库。

如果你需要任何目前无法开箱即用的操作，很可能需要使用外部库或自行编写自定义代码。[了解有关向函数代码仓库添加依赖的更多信息。](https://www.palantir.com/docs/foundry/functions/add-dependencies/)

## TypeScript v2 和 Python

使用本体编辑函数上传媒体并在本体中创建对象。上传后，你可以从对象读取和下载媒体文件，以供在应用中使用。[了解有关 Foundry 中媒体集的更多信息。](https://www.palantir.com/docs/foundry/media-sets-advanced-formats/media-overview/)

你可以在 TypeScript v2 和 Python 函数中向本体上传媒体，以获得一个 `Media` 实例，并用它来构造本体编辑。`Media` 类型包装了 `MediaReference`，并暴露了用于获取内容、获取元数据以及将媒体附加到对象的[高层操作](#媒体-ontology-sdk-操作)。你可以使用 `Media` 来构造本体编辑，也可以将现有媒体作为参数传入函数。

### 用作函数输入或输出类型

函数可以接收 `Media` 作为输入，通过使用 `uploadMedia` 上传数据来创建临时媒体，或者从对象上的媒体引用属性检索 `Media`。函数也可以返回 `Media` 类型，无论它是临时上传的，还是来自对象的媒体引用属性。在函数中，你可以获取 `Media` 的字节内容、获取其元数据，或通过本体编辑将其附加到本体对象上。TypeScript v2 和 Python 都可以检索高层元数据字段（`mediaType`、`sizeBytes` 和 `path`，在 Python 中为 snake_case 形式）或特定于媒体类型的[完整元数据](#获取媒体元数据)。

[实时预览](https://www.palantir.com/docs/foundry/functions/getting-started/#test-in-live-preview)支持接收媒体参数的函数。你可以搜索你的媒体集并内联选择一个项目，而无需手动输入媒体引用。

**TypeScript v2**

```typescript
import type { Media } from "@osdk/client";

export default async function echoMedia(media: Media): Promise<Media> {
    return media;
}
```

**Python**

```python
from functions.api import function, Media
# The Media type may also be imported from foundry_sdk_runtime
# from foundry_sdk_runtime.media import Media

@function
def echo_media(media: Media) -> Media:
    return media
```

### 上传媒体

使用 Ontology SDK 的 `uploadMedia`（TypeScript v2）和 `client.ontology.media.upload_media`（Python）辅助函数在函数内上传原始字节。两者都返回一个 `Media`，随后你可以通过本体编辑用它来修改本体对象的媒体属性，或者将其从函数返回。

**TypeScript v2**

```typescript
import type { Client, Media } from "@osdk/client";
import { uploadMedia } from "@osdk/functions";

export default async function uploadMediaItem(
    client: Client,
    body: string,
    fileName: string,
): Promise<Media> {
    const blob = new Blob([body], { type: "text/plain" });
    const media: Media = await uploadMedia(
        client,
        { data: blob, fileName }
    );
    return media;
}
```

**Python**

```python
from ontology_sdk import FoundryClient
from foundry_sdk_runtime.media import Media
from functions.api import function

@function
def upload_media(body: str, media_set_filename: str) -> Media:
    client = FoundryClient()
    media: Media = client.ontology.media.upload_media(
        body=body.encode("utf8"),
        filename=media_set_filename,
    )
    return media
```

**Python (async)**

```python
from ontology_sdk import FoundryClient
from foundry_sdk_runtime.media import Media
from functions.api import function

@function
async def upload_media(body: str, media_set_filename: str) -> Media:
    client = FoundryClient()
    media_coroutine = client.ontology.media.async_upload_media(
        body=body.encode("utf8"),
        filename=media_set_filename,
    )
    # media_coroutine is awaitable.
    return await media_coroutine
```

> **ℹ️ 提示**
>
> 上传媒体是临时性的，除非将其设置到本体对象的媒体引用属性上。当本体编辑被应用时，媒体才会持久化到本体对象属性上。

### 在本体编辑函数中上传媒体

无论你是在函数内上传了媒体，还是将 `Media` 作为函数输入接收，你都可以更新现有本体对象上的媒体属性，或创建带有 `Media` 参数的新本体对象。

**TypeScript v2**

```typescript
// Ensure you are using TypeScript OSDK 2.16 or greater

import type { Client, Media } from "@osdk/client";
import { Aircraft } from "@ontology-sdk/sdk";
import type { Edits } from "@osdk/functions";
import { createEditBatch, uploadMedia } from "@osdk/functions";

async function uploadTextToNewPlane(client: Client): Promise<Edits.Object<Aircraft>[]> {
    const batch = createEditBatch<Edits.Object<Aircraft>>(client);
    const blob = new Blob(["Hello, world"], { type: "text/plain" });
    const media: Media = await uploadMedia(
        client,
        { data: blob, fileName: "/planes/aircraft.txt" }
    );
    batch.create(Aircraft, { myMediaProperty: media, /* ... */ });
    return batch.getEdits();
}

export default uploadTextToNewPlane;
```

**Python**

```python
# Ensure you are using Python OSDK 2.198 or greater

from ontology_sdk import FoundryClient
from ontology_sdk.ontology.objects import Aircraft
from functions.api import function, OntologyEdit
from foundry_sdk_runtime.media import Media

@function(edits=[Aircraft])
def upload_text_to_new_plane() -> list[OntologyEdit]:
    client = FoundryClient()
    edits = client.ontology.edits()
    media: Media = client.ontology.media.upload_media(
        body="Hello, world".encode("utf8"),
        filename="/planes/aircraft.txt",
    )
    edits.objects.Aircraft.create(
        pk = "primary_key",
        my_media_property=media,
        # ...
    )
    return edits.get_edits()
```

> **ℹ️ 提示**
>
> 在 2.20 之前的 TypeScript OSDK 生成器版本中，`uploadMedia` 返回一个 `MediaReference`。从 2.20 版本开始，`uploadMedia` 返回一个 `Media`，它包装了底层的 `MediaReference` 并暴露了诸如 `fetchContents`、`fetchMetadata` 和 `getMediaReference()` 等高层操作。你可以将 `Media` 直接传入 `createEditBatch` 操作。

### 在动作类型上传递媒体引用参数

动作的媒体引用类型参数可以直接传入函数。

下面的截图展示了一个动作向其支撑函数传递媒体参数。

![动作](https://www.palantir.com/docs/resources/foundry/functions/media-tutorial-media-action-parameter.png?width=600)

### 媒体 Ontology SDK 操作

> **ℹ️ 提示**
>
> 下面的方法适用于任何 `Media` 实例，包括从 `upload_media` 返回的实例以及作为对象类型上 `Media` 属性暴露的实例。

#### 检索媒体字节数据

你可以访问存储在 `Media` 上的原始数据。该方法的签名如下：

**TypeScript v2**

```typescript
fetchContents(): Promise<Response>;

// "Response" is a standard interface on the JavaScript Fetch API
// https://developer.mozilla.org/en-US/docs/Web/API/Response
const mediaContents: Response = await myAircraft.myMediaProperty.fetchContents();

if (mediaContents.ok) {
    const mediaMimeType = mediaContents.headers.get("Content-Type");

    // Blob is a standard JavaScript type, representing a file-like object of immutable, raw data.
    // https://developer.mozilla.org/en-US/docs/Web/API/Blob
    // https://developer.mozilla.org/en-US/docs/Web/API/Response/blob
    const mediaBlob: Blob = await mediaContents.blob();
}
```

**Python**

```python
get_media_content(self) -> BytesIO: ...

from io import BytesIO

# https://docs.python.org/3/library/io.html#io.BytesIO
raw_data: BytesIO = my_aircraft.my_media_property.get_media_content()
```

#### 获取媒体元数据

你可以检索 `Media` 的元数据：

**TypeScript v2**

```typescript
fetchMetadata(): Promise<MediaMetadata>;

// Example usage:
const mediaMetadata = await myAircraft.myMediaProperty.fetchMetadata();
const sizeBytes = mediaMetadata.sizeBytes;
const mediaType = mediaMetadata.mediaType;
```

**Python**

```python
from foundry_sdk_runtime.media import MediaMetadata

# Example usage:
media_metadata: MediaMetadata = my_aircraft.my_media_property.get_media_metadata()
path = media_metadata.path
size_bytes = media_metadata.size_bytes
media_type = media_metadata.media_type
```

两种语言还可以检索媒体类型的完整元数据。该元数据包括仅适用于该类型的字段，例如文档的页数或图像的尺寸和波段。TypeScript v2 暴露了 `fetchFullMetadata`，Python 暴露了 `get_media_full_metadata`。两者都返回一个 `MediaFullMetadata`，其 `itemMetadata`（Python 中为 `item_metadata`）是一个基于媒体类型的可辨识联合（discriminated union）。

要访问特定于类型的字段，请先收窄该联合类型。在 TypeScript v2 中，检查 `itemMetadata.type` 字符串判别字段。在 Python 中，对变体类使用 `isinstance`，或检查 `item_metadata.type`。

**TypeScript v2**

```typescript
// Ensure you are using @osdk/client 2.56.0 or greater for fetchFullMetadata.
fetchFullMetadata?(): Promise<MediaFullMetadata>;

// Other variants include "audio", "video", "spreadsheet", "model3d", "dicom",
// "email", and "untyped". See the full type definition:
// https://github.com/palantir/osdk-ts/blob/main/packages/api/src/object/Media.ts

const fullMetadata = await myAircraft.myMediaProperty.fetchFullMetadata?.();
const itemMetadata = fullMetadata?.itemMetadata;

if (itemMetadata?.type === "document") {
    const pageCount = itemMetadata.pages;
    const title = itemMetadata.title;
} else if (itemMetadata?.type === "imagery") {
    const dimensions = itemMetadata.dimensions;
    const bands = itemMetadata.bands;
}
```

**Python**

```python
get_media_full_metadata(self) -> MediaFullMetadata: ...

# Other variants include AudioMediaItemMetadata, VideoMediaItemMetadata,
# SpreadsheetMediaItemMetadata, Model3dMediaItemMetadata, DicomMediaItemMetadata,
# EmailMediaItemMetadata, and UntypedMediaItemMetadata. See the full schema:
# https://github.com/palantir/foundry-platform-python/blob/develop/docs/v2/MediaSets/models/MediaItemMetadata.md

from foundry_sdk.v2.media_sets.models import (
    DocumentMediaItemMetadata,
    ImageryMediaItemMetadata,
)
from foundry_sdk_runtime.media import MediaFullMetadata

full_metadata: MediaFullMetadata = my_aircraft.my_media_property.get_media_full_metadata()
item = full_metadata.item_metadata

if isinstance(item, DocumentMediaItemMetadata):
    page_count = item.pages
    title = item.title
elif isinstance(item, ImageryMediaItemMetadata):
    dimensions = item.dimensions
    bands = item.bands
```

在 TypeScript v2 中，`fetchFullMetadata` 是 `Media` 接口的可选成员，以便 `Media` 的外部实现仍能编译通过。Ontology SDK 返回的每个 `Media` 都实现了它，因此如上所示使用 `?.()` 调用它是安全的。

TypeScript v2 还定义了一个 `unknown` 变体。如果平台返回的媒体类型是你安装的 SDK 版本尚未建模的，`itemMetadata` 会使用该变体，并且 `itemMetadata.raw` 会保留原始负载以供检查。升级 `@osdk/client` 即可收窄该类型。`unknown` 变体与 `untyped` 变体不同，后者是用于[接受任何文件格式的媒体集](https://www.palantir.com/docs/foundry/media-sets-advanced-formats/media-overview/#multimodal-media-sets)的平台类型。

### 转换媒体

> **⚠️ 注意**
>
> 媒体转换功能目前处于 beta 阶段。该功能仍在活跃开发中，后续可能会发生变化。

你可以转换媒体项目（例如旋转、调整大小或重新编码图像，切片或渲染 PDF 页面，或运行 OCR）并等待结果。转换作业提交后，系统会轮询其状态直至完成，然后返回转换后的内容。

在 TypeScript v2 中，转换通过 `@osdk/api/unstable` 作为实验性辅助函数暴露。在 Python 中，在生成的 `FoundryClient` 上调用 `client.ontology.media.transform_and_wait`。异步变体 `async_transform_and_wait` 接受相同的参数，并可使用 await 等待其完成。

**TypeScript v2**

```typescript
// Ensure you are using @osdk/api 2.55.0 or greater for transformAndWait.
// In versions before 2.55.0, this helper was exported as
// __EXPERIMENTAL__NOT_SUPPORTED_YET__transformAndWait and took a "mediaReference"
// argument instead of the "media" argument shown below.
//
// "MediaTransformation" is a discriminated union:
// each variant (`$image`, `$video`, `$audio`, `$documentToText`, `$documentToImage`, `$documentToDocument`, `$audioToText`, etc.)
// selects a transformation kind, with its own encoding and operation fields.
// See the "MediaTransformation" type definition for a full set of variants and operations:
// https://github.com/palantir/osdk-ts/blob/main/packages/api/src/experimental/MediaTransformation.ts

import { transformAndWait, type MediaTransformation } from "@osdk/api/unstable";
import type { Client, Media } from "@osdk/client";
import { uploadMedia } from "@osdk/functions";

export default async function rotateImage(
    client: Client,
    media: Media,
): Promise<Media> {
    const transformation: MediaTransformation = {
        $image: {
            $encoding: "jpg",
            $operations: [{ $rotate: { $angle: "DEGREE_180" } }],
        },
    };

    const result: Response = await client(transformAndWait).transformAndWait({
        media,
        transformation,
        options: { pollIntervalMs: 3000, pollTimeoutMs: 30000 },
    });

    if (!result.ok) {
        // The transformation failed; inspect result.status / result.text() for details.
        throw new Error(`Transformation failed with status ${result.status}`);
    }

    // Re-upload the transformed bytes so the function returns a Media.
    return uploadMedia(client, { data: await result.blob(), fileName: "rotated.jpg" });
}
```

**Python**

```python
from foundry_sdk.v2.media_sets.models import (
    ImageTransformation,
    JpgFormat,
    RotateImageOperation,
)
from foundry_sdk_runtime.errors import (
    MediaTransformationFailedError,
    MediaTransformationTimeoutError,
)
from foundry_sdk_runtime.media import Media
from functions.api import function
from ontology_sdk import FoundryClient

@function(beta=True)
def image_transform(document: Media) -> Media:
    client = FoundryClient()
    transformation = ImageTransformation(
        encoding=JpgFormat(),
        operations=[RotateImageOperation(angle="DEGREE_180")],
    )
    try:
        transformed_bytes: bytes = client.ontology.media.transform_and_wait(
            media_reference=document.get_media_reference(),
            transformation=transformation,
            poll_interval_seconds=3.0,
            poll_timeout_seconds=30.0,
        )
    except MediaTransformationFailedError:
        # The transformation job reported FAILED status.
        raise
    except MediaTransformationTimeoutError:
        # poll_timeout_seconds elapsed before the job completed.
        raise
    # Re-upload the transformed bytes so the function returns a Media.
    return client.ontology.media.upload_media(body=transformed_bytes, filename="rotated.jpg")
```

#### 示例：对 PDF 逐页运行 OCR 并输出边界框

此工作流接收一个 PDF（上传到媒体集或附加到对象上），在每一页上运行 OCR，并请求 hOCR 输出。hOCR 是一种 HTML，其中每个检测到的词和行都带有 `bbox` 属性，因此你可以从同一个响应中提取识别出的文本及其边界框坐标。每次 `transform_and_wait` 调用返回一页的字节；迭代调用即可覆盖整个文档。

**TypeScript v2**

```typescript
import { transformAndWait, type MediaTransformation } from "@osdk/api/unstable";
import type { Client, Media } from "@osdk/client";
import type { Integer } from "@osdk/functions";

export default async function ocrPdfPages(
    client: Client,
    media: Media,
    pageCount: Integer,
): Promise<string[]> {
    const runTransformation = client(transformAndWait).transformAndWait;

    const pageResults: string[] = [];
    for (let pageNumber = 0; pageNumber < pageCount; pageNumber++) {
        const transformation: MediaTransformation = {
            $documentToText: {
                $operation: {
                    $ocrOnPage: {
                        $pageNumber: pageNumber,
                        $parameters: {
                            $outputFormat: { $hocr: {} },
                            $languages: [{ $language: "ENG" }],
                        },
                    },
                },
            },
        };

        const result = await runTransformation({
            media,
            transformation,
            options: { pollTimeoutMs: 120_000 },
        });
        if (!result.ok) {
            throw new Error(`OCR failed on page ${pageNumber}: ${result.status}`);
        }
        pageResults.push(await result.text());
    }
    return pageResults;
}
```

**Python**

```python
from foundry_sdk.v2.media_sets.models import (
    DocumentMediaItemMetadata,
    DocumentToTextTransformation,
    OcrHocrOutputFormat,
    OcrLanguageWrapper,
    OcrOnPageOperation,
    OcrParameters,
)
from foundry_sdk_runtime.media import Media
from functions.api import function
from ontology_sdk import FoundryClient

@function(beta=True)
def ocr_pdf_pages(document: Media) -> list[bytes]:
    """Run OCR on every page of a PDF and return the hOCR bytes per page.

    Each hOCR document includes `bbox` attributes on detected words, lines, and
    paragraphs; parse with any HTML parser to recover both text and bounding
    boxes in a single pass.
    """
    client = FoundryClient()
    metadata = document.get_media_full_metadata().item_metadata
    if not isinstance(metadata, DocumentMediaItemMetadata) or metadata.pages is None:
        raise ValueError("Expected a PDF document with a known page count")

    media_reference = document.get_media_reference()
    page_results: list[bytes] = []

    for page_number in range(metadata.pages):
        transformation = DocumentToTextTransformation(
            operation=OcrOnPageOperation(
                page_number=page_number,
                parameters=OcrParameters(
                    output_format=OcrHocrOutputFormat(),
                    languages=[OcrLanguageWrapper(language="ENG")],
                ),
            ),
        )
        hocr_bytes: bytes = client.ontology.media.transform_and_wait(
            media_reference=media_reference,
            transformation=transformation,
            poll_timeout_seconds=120.0,
        )
        page_results.append(hocr_bytes)

    return page_results
```

内容密集的页面可能会使 OCR 运行时间远超默认的函数超时时间。请参阅[管理已发布的函数](https://www.palantir.com/docs/foundry/functions/manage-functions/)来配置函数执行超时。

#### 示例：将 PDF 页面渲染为图像并切片页面范围

对于需要获得每页视觉渲染结果的工作流（用于下游图像标注、嵌入或显示），使用 `$documentToImage` 配合 `$renderPage` 来获取特定页面的 PNG/JPG 图像。要将 PDF 的一个子范围提取为独立的 PDF 文档，使用 `$documentToDocument` 配合 `$slicePdfRange`。下面的每个函数都会重新上传转换后的字节，以便返回一个 `Media`。每个函数都是独立的模块；已注册的函数是该模块的 `export default`。

将单个页面渲染为 PNG 图像：

**TypeScript v2**

```typescript
import { transformAndWait, type MediaTransformation } from "@osdk/api/unstable";
import type { Client, Media } from "@osdk/client";
import { uploadMedia } from "@osdk/functions";

export default async function renderFirstPageAsPng(
    client: Client,
    media: Media,
): Promise<Media> {
    const transformation: MediaTransformation = {
        $documentToImage: {
            $encoding: "png",
            $operation: { $renderPage: { $pageNumber: 0, $width: 1200 } },
        },
    };
    const result = await client(transformAndWait).transformAndWait({
        media,
        transformation,
    });
    if (!result.ok) {
        throw new Error(`Render failed: ${result.status}`);
    }
    // Re-upload the rendered page so the function returns a Media.
    return uploadMedia(client, { data: await result.blob(), fileName: "page.png" });
}
```

**Python**

```python
from foundry_sdk.v2.media_sets.models import (
    DocumentToImageTransformation,
    PngFormat,
    RenderPageOperation,
)
from foundry_sdk_runtime.media import Media
from functions.api import function
from ontology_sdk import FoundryClient

@function(beta=True)
def render_first_page_as_png(document: Media) -> Media:
    """Render page 0 of a PDF at 1200px wide as a PNG and return it as a Media."""
    client = FoundryClient()
    transformation = DocumentToImageTransformation(
        encoding=PngFormat(),
        operation=RenderPageOperation(page_number=0, width=1200),
    )
    rendered_png: bytes = client.ontology.media.transform_and_wait(
        media_reference=document.get_media_reference(),
        transformation=transformation,
    )
    # Re-upload the rendered page so the function returns a Media.
    return client.ontology.media.upload_media(body=rendered_png, filename="page.png")
```

将一个页面范围切片为新的 PDF 文档：

**TypeScript v2**

```typescript
import { transformAndWait, type MediaTransformation } from "@osdk/api/unstable";
import type { Client, Media } from "@osdk/client";
import { uploadMedia } from "@osdk/functions";

export default async function sliceFirstTenPages(
    client: Client,
    media: Media,
): Promise<Media> {
    const transformation: MediaTransformation = {
        $documentToDocument: {
            $encoding: "pdf",
            $operation: {
                $slicePdfRange: {
                    $startPageInclusive: 0,
                    $endPageExclusive: 10,
                    $strictlyEnforceEndPage: false,
                },
            },
        },
    };
    const result = await client(transformAndWait).transformAndWait({
        media,
        transformation,
    });
    if (!result.ok) {
        throw new Error(`Slice failed: ${result.status}`);
    }
    // Re-upload the sliced PDF so the function returns a Media.
    return uploadMedia(client, { data: await result.blob(), fileName: "slice.pdf" });
}
```

**Python**

```python
from foundry_sdk.v2.media_sets.models import (
    DocumentToDocumentTransformation,
    PdfFormat,
    SlicePdfRangeOperation,
)
from foundry_sdk_runtime.media import Media
from functions.api import function
from ontology_sdk import FoundryClient

@function(beta=True)
def slice_first_ten_pages(document: Media) -> Media:
    """Return a new PDF containing pages 0-9 of the input PDF as a Media."""
    client = FoundryClient()
    transformation = DocumentToDocumentTransformation(
        encoding=PdfFormat(),
        operation=SlicePdfRangeOperation(
            start_page_inclusive=0,
            end_page_exclusive=10,
            strictly_enforce_end_page=False,  # tolerate documents shorter than 10 pages
        ),
    )
    sliced_pdf: bytes = client.ontology.media.transform_and_wait(
        media_reference=document.get_media_reference(),
        transformation=transformation,
    )
    # Re-upload the sliced PDF so the function returns a Media.
    return client.ontology.media.upload_media(body=sliced_pdf, filename="slice.pdf")
```

#### 示例：用检测到的边界框标注每一页

要生成可视化调试输出（每个 PDF 页面渲染后，在其上绘制 OCR 检测到的边界框），请对每一页串联三个转换。对于每一页，将该页面渲染为图像，并对同一页面运行 OCR 以恢复词级和行级边界框。然后重新上传渲染后的图像，并使用图像标注操作对其进行标注。页数来自完整元数据，因此在 TypeScript v2 中使用 `fetchFullMetadata` 读取，在 Python 中使用 `get_media_full_metadata` 读取。每一步都将上一步生成的字节重新上传，作为下一步的输入。函数会重新上传每个标注后的页面，因此每页返回一个 `Media`。

**TypeScript v2**

```typescript
import { transformAndWait, type MediaTransformation } from "@osdk/api/unstable";
import type { Client, Media } from "@osdk/client";
import { uploadMedia } from "@osdk/functions";

export default async function annotatePdfWithOcrBoxes(
    client: Client,
    media: Media,
): Promise<Media[]> {
    const runTransformation = client(transformAndWait).transformAndWait;

    // Use the full metadata to discover the page count.
    const fullMetadata = await media.fetchFullMetadata?.();
    const itemMetadata = fullMetadata?.itemMetadata;
    if (itemMetadata?.type !== "document" || itemMetadata.pages == null) {
        throw new Error("Expected a PDF document with a known page count");
    }

    const annotatedPages: Media[] = [];
    for (let pageNumber = 0; pageNumber < itemMetadata.pages; pageNumber++) {
        const renderTransformation: MediaTransformation = {
            $documentToImage: {
                $encoding: "png",
                $operation: { $renderPage: { $pageNumber: pageNumber, $width: 1200 } },
            },
        };
        const ocrTransformation: MediaTransformation = {
            $documentToText: {
                $operation: {
                    $ocrOnPage: {
                        $pageNumber: pageNumber,
                        $parameters: {
                            $outputFormat: { $hocr: {} },
                            $languages: [{ $language: "ENG" }],
                        },
                    },
                },
            },
        };

        // 1. Render the page as a PNG and OCR the same page. Both read from the
        // same source document and are independent, so they can run concurrently.
        const [renderResult, ocrResult] = await Promise.all([
            runTransformation({ media, transformation: renderTransformation }),
            runTransformation({
                media,
                transformation: ocrTransformation,
                options: { pollTimeoutMs: 120_000 },
            }),
        ]);
        if (!renderResult.ok || !ocrResult.ok) {
            throw new Error(`Page ${pageNumber} failed to render or OCR`);
        }

        // 2. Parse hOCR for bounding boxes in image pixels.
        // The parseHocrBoundingBoxes helper is omitted here; see the note below the example.
        const boxes = parseHocrBoundingBoxes(await ocrResult.text());

        // 3. Re-upload the rendered PNG as a temporary media item.
        const renderedMedia = await uploadMedia(client, {
            data: await renderResult.blob(),
            fileName: `page-${pageNumber}.png`,
        });

        // 4. Annotate the rendered page with a media transformation.
        const annotateTransformation: MediaTransformation = {
            $image: {
                $encoding: "png",
                $operations: [{
                    $annotate: {
                        $annotations: boxes.map(({ label, boundingBox }) => ({
                            $geometry: { $boundingBox: boundingBox },
                            $label: label,
                        })),
                    },
                }],
            },
        };
        const annotatedResult = await runTransformation({
            media: renderedMedia,
            transformation: annotateTransformation,
        });
        if (!annotatedResult.ok) {
            throw new Error(`Annotation failed on page ${pageNumber}`);
        }

        // 5. Re-upload the annotated page so the function returns a Media.
        annotatedPages.push(await uploadMedia(client, {
            data: await annotatedResult.blob(),
            fileName: `page-${pageNumber}-annotated.png`,
        }));
    }

    return annotatedPages;
}
```

**Python**

```python
from foundry_sdk.v2.media_sets.models import (
    AnnotateImageOperation,
    Annotation,
    BoundingBox,
    BoundingBoxGeometry,
    DocumentMediaItemMetadata,
    DocumentToImageTransformation,
    DocumentToTextTransformation,
    ImageTransformation,
    OcrHocrOutputFormat,
    OcrLanguageWrapper,
    OcrOnPageOperation,
    OcrParameters,
    PngFormat,
    RenderPageOperation,
)
from foundry_sdk_runtime.media import Media
from functions.api import function
from ontology_sdk import FoundryClient

@function(beta=True)
def annotate_pdf_with_ocr_boxes(document: Media) -> list[Media]:
    """Render every page of a PDF, OCR each page to find text bounding boxes,
    draw them on the rendered image, and return one annotated Media per page."""
    client = FoundryClient()
    media_reference = document.get_media_reference()

    # Use the full metadata to discover the page count.
    metadata = document.get_media_full_metadata().item_metadata
    if not isinstance(metadata, DocumentMediaItemMetadata) or metadata.pages is None:
        raise ValueError("Expected a PDF document with a known page count")

    annotated_pages: list[Media] = []
    for page_number in range(metadata.pages):
        # 1. Render the page as a PNG.
        rendered_png: bytes = client.ontology.media.transform_and_wait(
            media_reference=media_reference,
            transformation=DocumentToImageTransformation(
                encoding=PngFormat(),
                operation=RenderPageOperation(page_number=page_number, width=1200),
            ),
        )

        # 2. OCR the same page in hOCR mode to get word-level bounding boxes.
        hocr_bytes: bytes = client.ontology.media.transform_and_wait(
            media_reference=media_reference,
            transformation=DocumentToTextTransformation(
                operation=OcrOnPageOperation(
                    page_number=page_number,
                    parameters=OcrParameters(
                        output_format=OcrHocrOutputFormat(),
                        languages=[OcrLanguageWrapper(language="ENG")],
                    ),
                ),
            ),
            poll_timeout_seconds=120.0,
        )

        # 3. Parse hOCR for bounding boxes in image pixels.
        # The parse_hocr_bounding_boxes helper is omitted here; see the note below the example.
        boxes: list[tuple[str, BoundingBox]] = parse_hocr_bounding_boxes(hocr_bytes)

        # 4. Re-upload the rendered PNG as a temporary media item.
        rendered_media = client.ontology.media.upload_media(
            body=rendered_png, filename=f"page-{page_number}.png"
        )

        # 5. Annotate the rendered page with a Media transformation.
        annotated_bytes: bytes = client.ontology.media.transform_and_wait(
            media_reference=rendered_media.get_media_reference(),
            transformation=ImageTransformation(
                encoding=PngFormat(),
                operations=[
                    AnnotateImageOperation(
                        annotations=[
                            Annotation(
                                geometry=BoundingBoxGeometry(bounding_box=box),
                                label=label,
                            )
                            for label, box in boxes
                        ],
                    ),
                ],
            ),
        )

        # 6. Re-upload the annotated page so the function returns a Media.
        annotated_pages.append(
            client.ontology.media.upload_media(
                body=annotated_bytes, filename=f"page-{page_number}-annotated.png"
            )
        )

    return annotated_pages
```

**Python (async)**

```python
import asyncio

from foundry_sdk.v2.media_sets.models import (
    AnnotateImageOperation,
    Annotation,
    BoundingBox,
    BoundingBoxGeometry,
    DocumentMediaItemMetadata,
    DocumentToImageTransformation,
    DocumentToTextTransformation,
    ImageTransformation,
    OcrHocrOutputFormat,
    OcrLanguageWrapper,
    OcrOnPageOperation,
    OcrParameters,
    PngFormat,
    RenderPageOperation,
)
from foundry_sdk_runtime.media import Media
from functions.api import function
from ontology_sdk import FoundryClient

@function(beta=True)
async def annotate_pdf_with_ocr_boxes(document: Media) -> list[Media]:
    """Render every page of a PDF, OCR each page, annotate it, and return one Media per page."""
    client = FoundryClient()
    media_reference = document.get_media_reference()

    metadata = document.get_media_full_metadata().item_metadata
    if not isinstance(metadata, DocumentMediaItemMetadata) or metadata.pages is None:
        raise ValueError("Expected a PDF document with a known page count")

    async def annotate_page(page_number: int) -> Media:
        # Render the page as a PNG and OCR the same page concurrently.
        # Both transformations read from the same source document and are independent,
        # so asyncio.gather lets them poll in parallel instead of one after the other.
        rendered_png, hocr_bytes = await asyncio.gather(
            client.ontology.media.async_transform_and_wait(
                media_reference=media_reference,
                transformation=DocumentToImageTransformation(
                    encoding=PngFormat(),
                    operation=RenderPageOperation(page_number=page_number, width=1200),
                ),
            ),
            client.ontology.media.async_transform_and_wait(
                media_reference=media_reference,
                transformation=DocumentToTextTransformation(
                    operation=OcrOnPageOperation(
                        page_number=page_number,
                        parameters=OcrParameters(
                            output_format=OcrHocrOutputFormat(),
                            languages=[OcrLanguageWrapper(language="ENG")],
                        ),
                    ),
                ),
                poll_timeout_seconds=120.0,
            ),
        )

        # Parse hOCR for bounding boxes (see the sync example) and re-upload the
        # rendered PNG as a temporary media item, both concurrently.
        boxes, rendered_media = await asyncio.gather(
            async_parse_hocr_bounding_boxes(hocr_bytes),
            client.ontology.media.async_upload_media(
                body=rendered_png,
                filename=f"page-{page_number}.png",
            ),
        )

        # Annotate the rendered page with a Media transformation.
        annotated_bytes: bytes = await client.ontology.media.async_transform_and_wait(
            media_reference=rendered_media.get_media_reference(),
            transformation=ImageTransformation(
                encoding=PngFormat(),
                operations=[
                    AnnotateImageOperation(
                        annotations=[
                            Annotation(
                                geometry=BoundingBoxGeometry(bounding_box=box),
                                label=label,
                            )
                            for label, box in boxes
                        ],
                    ),
                ],
            ),
        )

        # Re-upload the annotated page so the function returns a Media.
        return await client.ontology.media.async_upload_media(
            body=annotated_bytes,
            filename=f"page-{page_number}-annotated.png",
        )

    # Process every page concurrently.
    return list(await asyncio.gather(*(annotate_page(p) for p in range(metadata.pages))))
```

两个示例中都省略了 hOCR 解析辅助函数。任何 HTML 解析器都可以提取 `class="ocrx_word"` 元素及其 `title="bbox X1 Y1 X2 Y2 ..."` 属性。在 Python 中，使用诸如 `lxml` 或 `BeautifulSoup` 之类的解析器，并将每个匹配项转换为 `BoundingBox(left=X1, top=Y1, width=X2-X1, height=Y2-Y1)`。在 TypeScript v2 中，将每个匹配项转换为 `{ $left: X1, $top: Y1, $width: X2-X1, $height: Y2-Y1 }`。

## TypeScript v1

> **⚠️ 注意**
>
> Foundry 在执行 TypeScript v1 函数时实施严格的内存限制。为确保你不超过这些内存限制，你应仅处理 20MB 以下的媒体文件。

> **⚠️ 注意**
>
> TypeScript v1 不支持在函数内上传媒体。下面的示例涵盖了将现有媒体传入本体编辑以及操作对象类型的媒体属性。

### 在对象上设置现有媒体

使用本体编辑函数将现有媒体项目附加到对象：

**TypeScript v1**

```typescript
import { OntologyEditFunction, MediaItem } from "@foundry/functions-api";
import { Aircraft } from "@foundry/ontology-api";

export class MyFunctions {
    @OntologyEditFunction()
    public async setExistingMediaToObject(
        aircraft: Aircraft,
        mediaItem: MediaItem
    ): Promise<void> {
        // Ontology Edits with passed in MediaItems are supported
        aircraft.myMediaProperty = mediaItem;
    }
}
```

### 对象类型上的媒体项目参数

下面的示例展示了在对象类型的媒体引用属性上的 `isAudio` 媒体操作：

**TypeScript v1**

```typescript
MediaItem.isAudio(objectType.mediaReferenceProperty)
```

### 读取原始媒体数据

你可以通过选择对象上的媒体引用属性来访问媒体项目。该方法的签名如下：

**TypeScript v1**

```typescript
// Blob is a standard JavaScript type, representing a file-like object of immutable, raw data.
// https://developer.mozilla.org/en-US/docs/Web/API/Blob
readAsync(): Promise<Blob>;
```

### 获取媒体元数据

你可以访问媒体项目的元数据。该方法的签名如下：

**TypeScript v1**

```typescript
getMetadataAsync(): Promise<IMediaMetadata>;
```

### 类型守卫

TypeScript v1 中的类型守卫允许你访问特定于某些媒体类型的功能。以下类型守卫可用于媒体项目元数据：

- `isAudioMetadata()`
- `isDicomMetadata()`
- `isDocumentMetadata()`
- `isImageryMetadata()`
- `isSpreadsheetMetadata()`
- `isUntypedMetadata()`
- `isVideoMetadata()`

例如，你可以使用图像类型守卫来提取特定于图像的元数据字段：

**TypeScript v1**

```typescript
const metadata = await myObject.mediaReference?.getMetadataAsync();
if (isImageryMetadata(metadata)) {
    const imageWidth = metadata.dimensions?.width;
    ...
}
```

你还可以在媒体项目命名空间上使用类型守卫，这样就能访问特定类型的媒体项目上的更多方法。你可以在此处使用的类型守卫有：

- `MediaItem.isAudio()`
- `MediaItem.isDicom()`
- `MediaItem.isDocument()`
- `MediaItem.isImagery()`
- `MediaItem.isSpreadsheet()`
- `MediaItem.isVideo()`

### 特定于文档的操作

#### 文本提取

要从文档中提取文本，你可以使用光学字符识别（OCR），或提取媒体项目上嵌入的文本。

对于机器生成的 PDF，提取 PDF 中以数字方式嵌入的文本可能比使用光学字符识别（OCR）更快和/或更准确。下面是文本提取用法的示例：

**TypeScript v1**

```typescript
extractTextAsync(options: IDocumentExtractTextOptions): Promise<string[]>;
```

使用 TypeScript v1 时，可以选择将以下内容作为对象提供：

- `startPage`：从零开始索引的起始页（含，可为空）
- `endPage`：从零开始索引的结束页（不含，可为空）。

如果 `startPage` 和 `endPage` 都留空，将返回文档中所有页面的文本。

对于非机器生成的 PDF，最好使用 OCR 方法来提取文本。

**TypeScript v1**

```typescript
ocrAsync(options: IDocumentOcrOptions): Promise<string[]>;
```

可以选择将以下内容作为 TypeScript 对象提供：

- `startPage`：从零开始索引的起始页（含）。
- `endPage`：从零开始索引的结束页（不含）。
- `languages`：要识别的语言列表（可为空）。
- `scripts`：要识别的文字脚本列表（可为空）。
- `outputType`：将输出类型指定为 `text` 或 `hocr`。

请记住，你需要使用类型守卫才能访问特定于媒体类型的操作。下面是使用 `isDocument()` 类型守卫来执行 OCR 文本提取的示例：

**TypeScript v1**

```typescript
import { MediaItem } from "@foundry/functions-api";
import { ArxivPaper } from "@foundry/ontology-api";

@Function()
public async firstPageText(paper: ArxivPaper): Promise<string | undefined> {
    if (MediaItem.isDocument(paper.mediaReference)) {
        const text = (await paper.mediaReference.ocrAsync({ endPage: 1, languages: [], scripts: [], outputType: 'text' }))[0];
        return text;
    }

    return undefined;
}
```

### 特定于音频的操作

#### 转录

音频媒体项目支持使用 transcribe 方法进行转录。签名如下：

**TypeScript v1**

```typescript
transcribeAsync(options: IAudioTranscriptionOptions): Promise<string>;
```

可以选择传入以下内容来指定转录应如何运行：

- `language`：要转录的语言，使用 `TranscriptionLanguage` 枚举传入。
- `performanceMode`：以 `More Economical`（更经济）或 `More Performant`（更高性能）模式运行转录，使用 `TranscriptionPerformanceMode` 枚举传入。
- `outputFormat`：通过传入一个 `type` 为 `plainTextNoSegmentData`（纯文本）或 `pttml` 的对象来指定输出格式。`pttml` 是一种[类似 TTML 的格式 ↗](https://en.wikipedia.org/wiki/Timed_Text_Markup_Language)；当类型为 `plainTextNoSegmentData` 时，该对象还接受一个布尔型的 `addTimestamps` 参数。

提供转录选项的示例：

**TypeScript v1**

```typescript
import { Function, MediaItem, TranscriptionLanguage, TranscriptionPerformanceMode } from "@foundry/functions-api";
import { AudioFile } from "@foundry/ontology-api";

@Function()
public async transcribeAudioFile(file: AudioFile): Promise<string|undefined> {
    if (MediaItem.isAudio(file.mediaReference)) {
        return await file.mediaReference.transcribeAsync({
            language: TranscriptionLanguage.ENGLISH,
            performanceMode: TranscriptionPerformanceMode.MORE_ECONOMICAL,
            outputFormat: {type: "plainTextNoSegmentData", addTimestamps: true}
        });
    }

    return undefined;
}
```

---

*原文：[Media](https://www.palantir.com/docs/foundry/functions/media/)*
