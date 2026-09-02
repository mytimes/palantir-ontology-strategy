# 调试函数（Debug functions）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/debug/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

> **⚠️ 注意**
>
> 以下文档专门针对 TypeScript v1 函数。如需更[强大的能力](https://www.palantir.com/docs/foundry/functions/language-feature-support/#typescript-v1-vs-typescript-v2)，包括对 Ontology SDK 的支持和可配置的资源请求，我们建议[迁移到 TypeScript v2](https://www.palantir.com/docs/foundry/functions/typescript-v2-migration/)。

在编写函数时，你很可能需要检查执行状态，以修复代码正确性或性能方面的问题。以下是你可以用来实现这一点的功能。请注意，这些调试步骤也适用于[单元测试](https://www.palantir.com/docs/foundry/functions/unit-test-getting-started/)。

## Authoring 调试器（Authoring debugger）

使用 Code Repositories 中的调试器工具，在单元测试运行时检查其行为。设置断点以暂停单元测试的执行，从而检查变量并理解函数和库。

![调试器概览面板。](https://www.palantir.com/docs/resources/foundry/functions/debugger-overview.png)

## 设置断点

要使用调试器，你需要设置断点。这些断点指示调试器应暂停代码执行的具体位置，使你能够与变量交互。

选择每行代码边距中变淡的红点即可设置断点。调试器会在标记行运行*之前*挂起执行。如果需要，你可以跨多个文件设置多个断点。

![调试器断点。](https://www.palantir.com/docs/resources/foundry/functions/debugger-breakpoint.png)

## 运行调试器

### 在实时预览期间

在代码中添加断点后，选择函数面板中的 **Run and debug（运行并调试）**。

![实时预览调试器布局。](https://www.palantir.com/docs/resources/foundry/functions/live-preview-debugger-run.png)

### 在测试期间

在代码中添加断点后，选择代码编辑器中单元测试旁边的 **Run test（运行测试）**。

![测试调试器布局。](https://www.palantir.com/docs/resources/foundry/functions/test-debugger-run.png)

## 使用调试器

调试器启动后，调试器面板将打开，并在遇到的第一个断点处暂停。调试器的左侧栏允许你导航代码、移除断点以及完成或停止调试会话。

当你导航代码时，编辑器会高亮显示接下来要执行的代码行。使用以下按钮推进调试器：

![调试器控件。](https://www.palantir.com/docs/resources/foundry/functions/debugger-controls.png)

1. **Resume execution（恢复执行）：** 继续执行，直到完成或被下一个断点暂停。
2. **Step over（步过）：** 执行该代码行，而不步入内部函数。
3. **Step into（步入）：** 如果该代码行中存在内部函数，则导航进入内部函数。
4. **Step out（步出）：** 导航出内部函数并推进调试器。
5. **Stop execution（停止执行）：** 完全停止调试器。
6. **Remove breakpoints（移除断点）：** 从代码仓库中移除所有断点，并在不暂停执行的情况下运行单元测试。
7. **Settings（设置）：** 打开/关闭调试器（不清除断点）。
8. **Documentation（文档）：** 打开文档以获取更多详细信息。

## 检查变量

调试器运行时，你可以在代码执行的确切位置检查变量和数据。

### 帧（Frames）

帧表示调试器处于活动状态或其中存在断点的函数。每个帧指示函数名，后跟编写该函数的文件名和行号。

选择一个帧以检查该帧内的变量，并针对它运行控制台命令。

### 变量（Variables）

变量部分显示转换执行时存储在局部变量和全局变量中的值。

![调试器变量。](https://www.palantir.com/docs/resources/foundry/functions/debugger-variables.png)

### 控制台（Console）

控制台允许你在运行调试器时使用 JavaScript 控制台命令与数据交互。

> **ℹ️ 提示**
>
> 请注意，控制台在所选帧的上下文中运行。尝试对不同帧的局部变量执行命令将导致错误。

![调试器控制台。](https://www.palantir.com/docs/resources/foundry/functions/debugger-console.png)

## 控制台日志记录

函数支持在执行期间发出控制台日志以进行调试。为此，只需使用 `console.log` 命令发出日志。例如：

```typescript
    @Function()
    public testConsoleLogging(n: Integer): Integer {
        for (let i = 0; i < n; i++) {
            console.log(`Iteration ${i}`);
        }
        return n;
    }
```

以这种方式使用控制台日志有助于调试正确性问题。你还可以添加控制台日志来识别代码中的性能瓶颈。有关如何改进链接遍历逻辑性能的更多信息，请参阅[优化性能](https://www.palantir.com/docs/foundry/functions/optimize-performance/)指南。

### 在测试期间

当你使用 **Authoring（创作）** 中的 **Tests（测试）** 辅助工具运行函数时，控制台日志将被捕获并显示在下方：

![控制台日志记录测试。](https://www.palantir.com/docs/resources/foundry/functions/console-logging-tests.png)

### 在实时预览期间

当你使用 **Authoring（创作）** 中的 **Functions（函数）** 辅助工具运行函数时，控制台日志将被捕获并连同时间戳显示在下方：

![控制台日志记录实时预览。](https://www.palantir.com/docs/resources/foundry/functions/console-logging-live-preview.png)

---

*原文：[Debug functions](https://www.palantir.com/docs/foundry/functions/debug/)*
