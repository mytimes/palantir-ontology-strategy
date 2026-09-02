# 附件（Attachments）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/attachments/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

> **⚠️ 注意**
>
> TypeScript v1 函数在具有严格内存限制的环境中执行。处理文件数据时很容易超出这些内存限制；我们建议仅处理 20MB 以下的附件。Python 和 TypeScript v2 函数的默认限制为 1GB，可在 Ontology Manager 中调整。

附件是一种表现得像对象属性的文件。附件作为临时文件上传，并[通过动作附加到对象上](https://www.palantir.com/docs/foundry/action-types/upload-attachments/)。附件一旦附加到对象，就会被持久化，并且可以像其他属性一样被访问。

## 函数中的附件

附件可以作为来自动作的输入传入函数，也可以作为对象上的属性被访问。你还可以在函数中创建并返回附件。

使用 Python 时，附件通过 [API 网关](https://www.palantir.com/docs/foundry/api/ontologies-v2-resources/attachments/attachment-basics/)管理。[Python OSDK](https://www.palantir.com/docs/foundry/ontology-sdk/python-osdk/) 中提供了附件类型。

以下是各语言的附件导入语法：

**TypeScript v1**

```typescript
import { Attachment } from "@foundry/functions-api";
```

**TypeScript v2**

```typescript
import { Attachment } from "@osdk/functions";
```

**Python**

```python
# For convenience, the OSDK Attachment type is re-exported from the Python functions `functions.api` package.
from functions.api import Attachment
```

## 读取附件数据

附件上提供了一个读取方法，用于读取其原始数据。该方法的签名如下：

**TypeScript v1**

```typescript
// Blob is a standard JavaScript type, representing a file-like object of immutable, raw data.
// https://developer.mozilla.org/en-US/docs/Web/API/Blob
readAsync(): Promise<Blob>;
```

**TypeScript v2**

```typescript
// Response interface is part of the Fetch API, and is provided by `undici` in the TypeScript v2 environment.
// https://developer.mozilla.org/en-US/docs/Web/API/Response
fetchContents(): Promise<Response>;
```

**Python**

```python
# BytesIO is a standard Python type, representing a binary stream.
# https://docs.python.org/3/library/io.html#io.BytesIO
def read(self) -> BytesIO: ...
```

你可能需要使用库或编写自己的自定义代码来处理复杂的文件类型。例如，PDF 必须使用合适的库进行解析。[详细了解如何向函数代码仓库添加依赖](https://www.palantir.com/docs/foundry/functions/add-dependencies/)。

### 从附件解析 Excel 文件

要批量创建或更新本体对象，可以接受一个 Excel 文件作为附件参数并解析其行。

**TypeScript v1**

```typescript
import { Attachment, OntologyEditFunction, Edits } from "@foundry/functions-api";
import { MyObjectType } from "@foundry/ontology-api";
import * as XLSX from "xlsx";

interface ExcelRow {
    [key: string]: string | number | null;
}

@OntologyEditFunction()
@Edits(MyObjectType)
public async processExcelUpload(file: Attachment): Promise<void> {
    // Read the file as a Blob
    const blob: Blob = await file.readAsync();

    // Convert Blob to ArrayBuffer
    const arrayBuffer = await blob.arrayBuffer();

    // Parse the Excel file using SheetJS
    const workbook = XLSX.read(arrayBuffer, { type: 'array' });

    // Get the first sheet
    const firstSheetName = workbook.SheetNames[0];
    const worksheet = workbook.Sheets[firstSheetName];

    // Convert worksheet to JSON rows
    const data = XLSX.utils.sheet_to_json(worksheet, { defval: null }) as ExcelRow[];

    // Process each row to create or update objects
    for (const row of data) {
        // Create or update ontology objects based on row data
    }
}
```

**Python**

```python
from io import BytesIO
import pandas as pd

from functions.api import function, Attachment, OntologyEdit
from ontology_sdk import FoundryClient
from ontology_sdk.ontology.objects import MyObjectType


@function(edits=[MyObjectType])
def process_excel_upload(excel_file: Attachment) -> list[OntologyEdit]:
    client = FoundryClient()
    ontology_edits = client.ontology.edits()

    # Read the uploaded Excel file
    sheet_data: BytesIO = excel_file.read()
    df = pd.read_excel(sheet_data)

    # Process each row to create or update objects
    for _, row in df.iterrows():
        new_obj = ontology_edits.objects.MyObjectType.create(str(row["id"]))
        new_obj.property_a = row["column_a"]
        new_obj.property_b = row["column_b"]

    return ontology_edits.get_edits()
```

对于多文件上传，接受 `Attachment[]`（TypeScript）或 `list[Attachment]`（Python）作为参数类型。有关 `xlsx`（SheetJS）或 `pandas` 等库，请参阅[添加依赖](https://www.palantir.com/docs/foundry/functions/add-dependencies/)。

### TypeScript v1 中的文件解析

TypeScript v1 函数不提供文件系统支持。解析文件数据的相关依赖通常会依赖 `fs` 模块，而该模块在函数环境中不可用。这一限制可能导致编译和执行期间出现 `fs` 模块错误。要绕过此限制，你可以引入对内存文件系统（例如 `memfs`）的依赖，然后将该依赖以 `fs` 名称设置别名。

下面是在 `package.json` 文件中使用 NPM 依赖 `memfs` 的示例：

```json
"fs": "npm:memfs@^x.x.x"
```

## 创建附件

函数也可用于创建附件并将其附加到对象。要使函数中创建的附件被持久化，函数必须执行一次[本体编辑](https://www.palantir.com/docs/foundry/functions/api-ontology-edits/)，将附件链接到对象。

> **⚠️ 注意**
>
> 未附加到对象的附件只能由上传者查看，并且会在一段时间后自动删除。 
> TypeScript v2 函数不支持创建附件。

要创建附件，请使用附件上的上传函数。各语言的上传函数签名如下：

**TypeScript v1**

```typescript
import { Attachments, Attachment } from "@foundry/functions-api";

// On Attachments:
uploadFile(filename: string, blob: Blob): Promise<Attachment>;
```

**Python**

```python
from ontology_sdk import FoundryClient
from foundry_sdk_runtime.attachments import AttachmentMetadata

# On FoundryClient:
def upload(file_path: str, attachment_name: str) -> AttachmentMetadata: ...
# `file_path` is a local file to be uploaded.
```

下面的示例展示了上传文件并将生成的附件分配给对象的过程。

**TypeScript v1**

```typescript
import { Attachments, Attachment, OntologyEditFunction } from "@foundry/functions-api";

@OntologyEditFunction()
public async updateMaintenanceLog(aircraft: Aircraft): Promise<void> {
    const aircraftMaintenanceLogData: Blob = await aircraft.maintenanceLog.readAsync();
    const completedMaintenanceLogData: Blob = await completedMaintenanceLog.readAsync();

    // You will likely need to rely on libraries or custom code to create the `Blob` object, which is
    // passed as a parameter into the `uploadFile` method.

    // Compare the current aircraft logs and completed logs and create a new maintenance log.
    const updatedMaintenanceLogData: Blob;

    aircraft.maintenanceLog = await Attachments.uploadFile("maintenance-log.txt", updatedMaintenanceLogData);
}
```

**Python**

```python
from io import BytesIO

from functions.api import function, Attachment, OntologyEdit
from ontology_sdk import FoundryClient
from ontology_sdk.ontology.objects import Aircraft


@functions(edits=[Aircraft])
def update_maintenance_log(
    aircraft: Aircraft,
    completed_maintenance_log: Attachment
) -> list[OntologyEdit]:
    client = FoundryClient()
    ontology_edits = client.ontology.edits()

    maintenance_log_data: BytesIO = aircraft.maintenance_log.read()
    completed_maintenance_log_data: BytesIO = completed_maintenance_log.read()

    # Compare the current aircraft logs and completed logs and create a new maintenance log
    updated_maintenance_log_data: BytesIO = get_updated_maintenance_log(
        maintenance_log_data,
        completed_maintenance_log_data
    )

    editable_aircraft = ontology_edits.objects.Aircraft.edit(aircraft)

    with open("updated-maintenance-log.txt", "wb") as f:
        f.write(updated_maintenance_log_data.getbuffer())

    editable_aircraft.maintenance_log = client.ontology.attachments.upload(
        "updated-maintenance-log.txt",
        "my_attachment"
    )

    return ontology_edits.get_edits()
```

---

*原文：[Attachments](https://www.palantir.com/docs/foundry/functions/attachments/)*
