# 模拟用户与用户组（Mock users and groups）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/unit-test-users-groups/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

### 用户模拟

你可以使用 `createUser` 创建用户的部分模拟，其中除 `id` 和 `username` 之外的所有属性都是可选的。你需要从 `"@foundry/functions-testing-lib"` 导入 `{ createUser }`。

```typescript
import { MyFunctions } from ".."

import { verifyOntologyEditFunction, createGroup, createUser } from "@foundry/functions-testing-lib";

describe("example test suite", () => {
    const myFunctions = new MyFunctions();
    test("test users and groups", async () => {
        const group = createGroup({
            id: "groupId",
        });
        const user = createUser({
            id: "userId",
            username: "username",
        });
        await expect(myFunctions.searchUsers("userId", "groupId")).resolves.toEqual([user, group]);
    });
});
```

这可用于测试以下函数：

```typescript
import { Function, OntologyEditFunction, Users, Group, Principal } from "@foundry/functions-api";

export class MyFunctions {
    @Function()
    public async searchUsers(userId: string, groupId: string): Promise<Principal[]> {
        const existingPrincipals = await Promise.all([
            Users.getUserByIdAsync(userId),
            Users.getGroupByIdAsync(groupId),
        ]);
        return existingPrincipals.filter(r => !!r).map(r => r!);
    }
}
```

### 用户组模拟

你也可以使用 `createGroup` 创建用户组的部分模拟，其中除 `id` 之外的所有属性都是可选的。你需要从 `"@foundry/functions-testing-lib"` 导入 `{ createGroup }`。

```typescript
import { MyFunctions } from ".."

import { verifyOntologyEditFunction, createGroup } from "@foundry/functions-testing-lib";

describe("example test suite", () => {
    const myFunctions = new MyFunctions();
    test("test groups", async () => {
        const group = createGroup({
            id: "groupId",
        });
        await expect(myFunctions.searchGroups("groupId")).resolves.toEqual([group]);
    });
});
```

这可用于测试以下函数：

```typescript
import { Function, OntologyEditFunction, Users, Group, Principal } from "@foundry/functions-api";

export class MyFunctions {
    @Function()
    public async searchGroups(groupId: string): Promise<Principal[]> {
        const existingPrincipals = await Promise.all([
            Users.getGroupByIdAsync(groupId),
        ]);
        return existingPrincipals.filter(r => !!r).map(r => r!);
    }
}
```

---

*原文：[Mock users and groups](https://www.palantir.com/docs/foundry/functions/unit-test-users-groups/)*
