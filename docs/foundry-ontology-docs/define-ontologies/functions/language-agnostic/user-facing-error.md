# 面向用户的错误（User-facing errors）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/user-facing-error/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

在平台的其他部分（例如 Workshop 或动作）中运行函数时，你可能希望抛出一条带有详细消息的错误。为此，请抛出 `UserFacingError`。例如：

**TypeScript v1**

```typescript
import { Function, UserFacingError } from "@foundry/functions-api";
import { Employee } from "@foundry/ontology-api";

export class MyFunctions {
    @Function()
    public async searchExactlyFiveEmployees(employees: Employee[]): Proimse<string> {
        if (employees.length != 5) {
            throw new UserFacingError(`Pass in exactly 5 employees. Received ${employees.length}.`);
        }

        // search employees
    }
}
```

**TypeScript v2**

```typescript
import { Osdk } from "@osdk/client";
import { Employee } from "@ontology/sdk";
import { UserFacingError } from "@osdk/functions";

export default async function searchExactlyFiveEmployees(employees: Array<Osdk.Instance<Employee>>): Promise<string> {
    if (employees.length != 5) {
        throw new UserFacingError(`Pass in exactly 5 employees. Received ${employees.length}.`);
    }

    // search employees
}
```

**Python**

```python
from functions.api import function, UserFacingError
from ontology_sdk import FoundryClient
from ontology_sdk.ontology.objects import Aircraft

@function()
def search_exactly_five_employees(
    employees: list[Aircraft]
) -> str:
    if not len(aircraft) == 5:
        raise UserFacingError(f"Pass in exactly 5 employees. Received ${len(aircraft)}.")

    # search employees
```

当在 [Workshop 应用](https://www.palantir.com/docs/foundry/workshop/functions-use/)中以[函数支撑的动作](https://www.palantir.com/docs/foundry/action-types/function-actions-overview/)运行此函数且员工数量不正确时，用户会看到以下错误：

![面向用户的错误](https://www.palantir.com/docs/resources/foundry/functions/user-facing-error.png)

当[函数支撑的导出](https://www.palantir.com/docs/foundry/workshop/widgets-button-group/#function-backed-export)中使用的函数抛出 `UserFacingError` 时，导出失败的提示弹窗（toast）会显示该错误消息。

详细的面向用户的错误消息有助于用户识别并解决问题。

---

*原文：[User-facing errors](https://www.palantir.com/docs/foundry/functions/user-facing-error/)*
