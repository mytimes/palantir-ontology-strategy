# 配置通知（Configure notifications）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/configure-notifications/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

函数可用于灵活配置平台中应发送的通知，包括通过外部方式发送到用户电子邮件地址的通知。

在函数中配置通知会用到 `Principal`（表示 `User` 或 `Group`）和 `notification` 类型。在阅读本节时，以下参考资料可能会有所帮助：

- 参考：[Principal、User 和 Group 类型](https://www.palantir.com/docs/foundry/functions/types-reference/#users-groups-and-principals)
- 参考：[通知类型](https://www.palantir.com/docs/foundry/functions/types-reference/#notification)

### 定义自定义通知

假设本体中包含一个可以分配给 `User` 的 `Issue` 对象。你可以创建一个函数，定义应发送给指定 `User` 的、包含该 `Issue` 详细信息的通知。

**TypeScript v1**

```typescript
import { EmailNotificationContent, Function, Notification, ShortNotification, User } from "@foundry/functions-api";
import { Issue } from "@foundry/ontology-api";

export class NotificationFunctions {
    @Function()
    public createIssueNotification(issue: Issue, user: User): Notification {
        // Create a short notification that will be shown within the platform
        const shortNotification = ShortNotification.builder()
            .heading("New issue")
            .content("A new issue has been assigned to you.")
            // Link to the Issue object in the platform
            .addObjectLink("Issue", issue)
            .build();

        // Define the email body. The email body may contain headless HTML, such as tables of data
        // Note that you can access properties of both the user and the issue in the content
        const emailBody = `Hello, ${user.firstName},

A new issue has been assigned to you: ${issue.description}.`;

        const emailNotificationContent = EmailNotificationContent.builder()
            .subject("New issue")
            .body(emailBody)
            .addObjectLink("Issue", issue)
            .build();

        return Notification.builder()
            .shortNotification(shortNotification)
            .emailNotificationContent(emailNotificationContent)
            .build();
    }
}
```

**TypeScript v2**

```typescript
import { NotificationLink, Notification } from "@osdk/functions";
import { Issue } from "@ontology/sdk";
import type { Osdk } from "@osdk/api";

export default function createIssueNotification(issue: Osdk.Instance<Issue>): Notification {
    // Link to the Issue object in the platform
    const links: NotificationLink[] = [
        {
            label: "Issue",
            linkTarget: {
                type: "object",
                object: issue
            }
        }
    ]

    const platformNotification = {
        heading: "New issue",
        content: "A new issue has been assigned to you.",
        links: links
    }

    // Define the email body. The email body may contain headless HTML, such as tables of data
    const emailBody = `Hello,

A new issue has been assigned to you: ${issue.description}.`;

    const emailNotification = {
        subject: "New issue",
        body: emailBody,
        links: links
    }

    return {
        platformNotification: platformNotification,
        emailNotification: emailNotification
    }
}
```

**Python**

```python
from functions.api import function, Notification, PlatformNotification, NotificationObjectLink, EmailNotification
from ontology_sdk.ontology.objects import Issue

@function()
def createIssueNotification(issue: Issue) -> Notification[Issue]:
# If configuring a notification with an object link, you must declare the object type as part of the return type

    # Link to the Issue object in the platform
    links = [
        NotificationObjectLink(label="Issue", objectTarget=issue)
    ]

    #Create a short notification that will be shown within the platform
    platform_notification = PlatformNotification(
        heading="New issue",
        content="A new issue has been assigned to you.",
        links=links
    )

    # Define the email body. The email body may contain headless HTML, such as tables of data
    emailBody = f"Hello, \n A new issue has been assigned to you: {issue.description}."

    email_notification = EmailNotification(
            subject="New issue",
            body=emailBody,
            links=links
        )

    return Notification(platform_notification, email_notification)
```

### 检索用户和用户组

除了将 `User` 传入函数外，你还可以按需检索 `User` 或 `Group`。假设 `Issue` 对象有一个包含用户 ID 的 `assignee` 字段。在下面的示例中，函数返回一个提醒用户处理该问题的通知：

**TypeScript v1**

```typescript
import { EmailNotificationContent, Function, Notification, ShortNotification, User, UserFacingError, Users } from "@foundry/functions-api";
import { Issue } from "@foundry/ontology-api";

export class NotificationFunctions {
    @Function()
    public async createIssueReminderNotification(issue: Issue): Promise<Notification> {
        if (!issue.assignee) {
            throw new UserFacingError("Cannot create notification for issue without an assignee.");
        }

        const user = await Users.getUserByIdAsync(issue.assignee);

        const emailBody = `Hello, ${user.firstName},

This is a reminder to investigate the following issue: ${issue.description}`;

        // You can also use this structure to build the entire notification inline
        return Notification.builder()
            .shortNotification(ShortNotification.builder()
                .heading("Issue reminder")
                .content("Investigate this issue.")
                .addObjectLink("Issue", issue)
                .build())
            .emailNotificationContent(EmailNotificationContent.builder()
                .subject("New issue")
                .body(emailBody)
                .addObjectLink("Issue", issue)
                .build())
            .build();
    }
}
```

**TypeScript v2**

```typescript
import { NotificationLink, Notification } from "@osdk/functions";
import { Users } from "@osdk/foundry.admin";
import { Issue } from "@ontology/sdk";
import { Client } from "@osdk/client";
import type { Osdk } from "@osdk/api";

export default async function createIssueReminderNotification(client: Client, issue: Osdk.Instance<Issue>): Promise<Notification> {
    const user = await Users.get(client, issue.assignee);

    const emailBody = `Hello, ${user.firstName},

This is a reminder to investigate the following issue: ${issue.description}`;

    // You can also use this structure to build the entire notification inline
    const links: NotificationLink[] = [
        {
            label: "Issue",
            linkTarget: {
                type: "object",
                object: issue
            }
        }
    ]

    // You can also use this structure to build the entire notification inline
    return {
        platformNotification: {
            heading: "Issue reminder",
            content: "Investigate this issue.",
            links:  links
        },
        emailNotification: {
            subject: "New issue",
            body: emailBody,
            links: links
        }
    }
}
```

**Python**

```python
from functions.api import function, Notification, PlatformNotification, NotificationObjectLink, EmailNotification
from ontology_sdk.ontology.objects import Issue
from foundry_sdk import FoundryClient
import foundry_sdk

@function()
def createIssueReminderNotification(issue: Issue) -> Notification[Issue]:
    client = FoundryClient(auth=foundry_sdk.UserTokenAuth(...), hostname="example.palantirfoundry.com")

    user = client.admin.User.get(issue.assignee)

    # Link to the Issue object in the platform
    links = [
        NotificationObjectLink(label="Issue", objectTarget=issue)
    ]

    #Create a short notification that will be shown within the platform
    platform_notification = PlatformNotification(
        heading="Issue reminder",
        content="Investigate this issue.",
        links=links
    )

    # Define the email body. The email body may contain headless HTML, such as tables of data
    # Note that you can access properties of both the user and the issue in the content
    emailBody = f"Hello, {user.firstName}, \n A new issue has been assigned to you: {issue.description}."

    email_notification = EmailNotification(
            subject="Issue reminder",
            body=emailBody,
            links=links
        )

    return Notification(platform_notification, email_notification)
```

### 返回接收者

上文介绍的 `Notification` API 允许你返回自定义通知内容。使用函数配置通知的另一种方式是返回通知的接收者列表。为此，创建一个返回一个或多个 `Principal` 对象（如 `User` 或 `Group` 对象）的函数。

在下面的示例中，函数同时返回报告该问题的用户和当前被分配处理该问题的用户：

**TypeScript v1**

```typescript
import { Function, User, UserFacingError, Users } from "@foundry/functions-api";
import { Issue } from "@foundry/ontology-api";

export class NotificationFunctions {
    /**
     * Given an Issue, returns users representing the current assignee for the Issue and the user
     * who originally reported the issue.
     */
    @Function()
    public async getIssueAssigneeAndReporter(issue: Issue): Promise<User[]> {
        if (!issue.assignee || !issue.reporter) {
            throw new UserFacingError("Cannot create notification for issue without an assignee or reporter.");
        }

        const user = await Users.getUserByIdAsync(issue.assignee);
        const issueReporter = await Users.getUserByIdAsync(issue.reporter);

        return [user, issueReporter];
    }
}
```

**TypeScript v2**

```typescript
import { UserId, Principal } from "@osdk/functions";
import { Users, Groups } from "@osdk/foundry.admin";
import { Issue } from "@ontology/sdk";
import { Client } from "@osdk/client";
import type { Osdk } from "@osdk/api";

/**
 * Given an Issue, returns users representing the current assignee for the Issue and the user
 * who originally reported the issue.
 */
async function getIssueAssigneeAndReporter(client: Client, issue: Osdk.Instance<Issue>): Promise<UserId[]> {
    const user = await Users.get(client, issue.assignee);
    const issueReporter = await Users.get(client, issue.reporter);

    return [user.id, issueReporter.id];
}

/**
 * Given an Issue, returns the user who is the current assignee of the issue and the group that issue belongs to.
 */
async function getIssueAssigneeAndGroups(client: Client, issue: Osdk.Instance<Issue>): Promise<Principal[]> {
    // To return both groups and users, return the Principal type.

    const user = await Users.get(client, issue.assignee);
    const group = await Groups.get(client, issue.group);

    return [{type: "user", id: user.id}, {type: "group", id: group.id}];
}
```

**Python**

```python
from functions.api import Array, function, Principal, UserId
from ontology_sdk.ontology.objects import Issue
from foundry_sdk import FoundryClient
import foundry_sdk

# Given an Issue, returns users representing the current assignee for the Issue and the user
# who originally reported the issue.
@function()
def getIssueAssigneeAndReporter(issue: Issue) -> Array[UserId]:
    client = FoundryClient(auth=foundry_sdk.UserTokenAuth(...), hostname="example.palantirfoundry.com")

    user = client.admin.User.get(issue.assignee)
    issueReporter = client.admin.User.get(issue.reporter)

    return [user.id, issueReporter.id]

# Given an Issue, returns the user who is the current assignee of the issue and the group that issue belongs to.
@function()
def getIssueAssigneeAndGroup(issue: Issue) -> Array[Principal]:
    # To return both groups and users, return the Principal type.
    client = FoundryClient(auth=foundry_sdk.UserTokenAuth(...), hostname="example.palantirfoundry.com")

    user = client.admin.User.get(issue.assignee)
    group = client.admin.Group.get(issue.group)

    return [Principal.user(user.id), Principal.group(group.id)]
```

---

*原文：[Configure notifications](https://www.palantir.com/docs/foundry/functions/configure-notifications/)*
