# 添加 npm 依赖（Add npm dependencies）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/add-dependencies/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

> **⚠️ 注意**
>
> 以下文档专门针对 TypeScript v1 函数。如需更[强大的能力](https://www.palantir.com/docs/foundry/functions/language-feature-support/#typescript-v1-vs-typescript-v2)，包括对 Ontology SDK 的支持和可配置的资源请求，我们建议[迁移到 TypeScript v2](https://www.palantir.com/docs/foundry/functions/typescript-v2-migration/)。

函数代码仓库使用 [npm ↗](https://www.npmjs.com/) 管理依赖，包括用于基于 Foundry 本体生成代码以及在代码中发现函数的包。你可以使用 `npm` 将外部依赖安装到代码仓库中，使用标准包来处理数字和日期、执行统计计算，或处理 XML 等数据格式。

请注意，函数运行时仅支持纯 JavaScript 库——任何依赖 NodeJS 运行时并进行系统调用的包都不受支持。

## 启用从公共 npm 注册中心获取依赖

默认情况下，函数代码仓库不会从公共 npm 注册中心获取包。

如果你的代码仓库尚未从公共 npm 注册中心获取依赖，当你在 Code Repositories 中打开 `package.json` 文件时，会出现一个用于启用该功能的横幅。

![在 Code Repositories 中启用外部 npm。](https://www.palantir.com/docs/resources/foundry/functions/external-npm.png)

## 在 Code Repositories 中添加依赖

你可以使用 **Code Repositories** 中的 Libraries（库）侧边栏向函数代码仓库添加包。搜索所需包，并选择一个结果以查看最新版本等详细信息。结果包括来自 Foundry 和 [https://npmjs.com](https://npmjs.com) 的包。

![从 Code Repositories 侧边栏添加库。](https://www.palantir.com/docs/resources/foundry/functions/npm-installation-controls.png)

选择是将包添加到 `package.json` 文件中的 `dependencies` 还是 `devDependencies`。选择 **Add and install library（添加并安装库）** 将包添加到你的代码仓库。

![在添加库之前确认库依赖更改。](https://www.palantir.com/docs/resources/foundry/functions/npm-backing-repositories.png)

如果包的来源代码仓库尚未配置为支撑代码仓库（backing repository），将弹出对话框提示你导入额外资源。**Add and install library** 按钮会自动将包及其依赖导入函数代码仓库，并更新你的 `package.json` 和 `package-lock.json`。

正在运行的安装任务完成后，该包即可在你的代码仓库中使用。

如果你使用的 `typescript-functions` 模板版本低于 0.520.0，通过任务运行器进行安装将被禁用。在这种情况下，请提交更新后的 `package.json` 文件，确保检查通过，然后重启 Code Assist 以使新包可用。

## 手动添加依赖

你可以在 Code Repositories 中通过修改 `package.json` 文件来手动添加包。如果你需要安装特定的包版本，这会很有用。打开 `package.json`，添加你的依赖并从 [https://npmjs.com](https://npmjs.com) 选择相应的版本，然后选择 **Commit（提交）**。在确认检查通过后，重启 Code Assist 以使新包可用。

![将鼠标悬停在状态栏上并选择状态符号以重启 Code Assist。](https://www.palantir.com/docs/resources/foundry/functions/restart-code-assist.png)

以下是在代码仓库的 `package.json` 文件中手动添加 `d3-array` 包的示例：

```typescript
  "dependencies": {
    ...
    "d3-array": "^2.3.1"
  },
  "devDependencies": {
    ...
    "@types/d3-array": "^2.0.0"
  }
```

---

*原文：[Add npm dependencies](https://www.palantir.com/docs/foundry/functions/add-dependencies/)*
