# 本地开发（Local development）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/python-local-development/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

你可以对 Python 函数代码仓库进行本地开发，从而在你定制的环境中高速迭代。

## 为 Python 函数代码仓库设置本地开发

### 克隆代码仓库

1. 在代码仓库的菜单栏中，选择 **Work locally**（在本地工作）以打开对话框，并复制给定的代码仓库 URL。
![代码仓库的顶部菜单栏，右侧为 “Work locally” 选项。](https://www.palantir.com/docs/resources/foundry/functions/clone-repo.png)
![“Work locally” 对话框。](https://www.palantir.com/docs/resources/foundry/functions/work-locally-dialog.png)
2. 使用命令行，在你本地机器上选择的目录中运行 `git clone <URI>`。然后使用 `cd` 命令导航到该代码仓库。

### 限制

- 克隆所用的令牌有效期很短且权限为只读，但允许向你的代码仓库推送更改。
- 你仍需要将更改推送到 Foundry 以发布产物（artifacts），或者在你希望运行检查或构建时进行推送。

## 设置开发环境

### 前提条件

- 确保已安装 Java 21，并且环境变量 `JAVA_HOME` 指向正确的 Java 安装。可以从 [Oracle 网站 ↗](https://www.oracle.com/java/technologies/downloads/#java21) 下载 Java 21。

> **ℹ️ 提示**
>
> 根据你的操作系统设置 `JAVA_HOME` 环境变量：
>
> - Windows：在 PowerShell 中运行 `SETX JAVA_HOME -m "<java-home-dir>"`。这会修改系统环境变量，你需要重启 shell 才能使更改生效。或者，你可以运行 ` [System.Environment]::SetEnvironmentVariable("JAVA_HOME", "<java-home-dir>")`，以便在当前运行的进程中设置 `JAVA_HOME`。
> - Linux 或 macOS：运行 `export JAVA_HOME=<java-home-dir>`。

- 按照[此处](https://www.palantir.com/docs/foundry/code-repositories/repository-upgrades/#manual-branch-upgrade)概述的步骤，确保你的代码仓库已升级到最新模板版本。
- 确保未设置环境变量 `CI`、`JEMMA` 和 `CA`。
- 如果在 Apple 芯片的 Mac 上运行，确保已安装 [Rosetta 2 ↗](https://developer.apple.com/documentation/apple-silicon/about-the-rosetta-translation-environment)。你可以在终端中运行 `/usr/sbin/softwareupdate --install-rosetta --agree-to-license` 来安装 Rosetta 2。

> **⚠️ 注意：模板版本错误**
>
> 如果 `./gradlew` 命令失败并出现类似以下的错误，说明你的代码仓库使用的是过时的模板版本：
>
> ```
> A problem occurred evaluating project ':transforms-python'.
> > Failed to apply plugin class 'com.palantir.transforms.lang.python.gradle.ModelTrainingPlugin'.
>    > Could not create task ':transforms-python:createCondaPackLayer'.
>       > Task with name 'condaPackRun' not found in project ':transforms-python'.
> ```
>
> 如上所述将你的代码仓库升级到最新模板版本即可解决此错误。

## Visual Studio Code

- 确保已安装 [Visual Studio Code ↗](https://code.visualstudio.com/)。
- 从 Visual Studio Code 网站或应用中的 **Extensions**（扩展）选项卡安装 [Python 扩展 ↗](https://marketplace.visualstudio.com/items?itemName=ms-python.python)。
- 要自动生成用于配置 Visual Studio Code Python 解释器的设置文件，运行命令 `./gradlew vsCode`。

## PyCharm

- 要设置 Python 开发环境，运行命令 `./gradlew condaDevelop`。
- 确保本地已安装 [JetBrains PyCharm ↗](https://www.jetbrains.com/pycharm/)。
- 按照[此处 ↗](https://www.jetbrains.com/help/pycharm/open-projects.html)概述的步骤导入项目。
- 从状态栏上的 [Python 解释器选择器 ↗](https://www.jetbrains.com/help/pycharm/configuring-python-interpreter.html#widget) 中选择 **Add New Interpreter**（添加新解释器）。
![添加 python 解释器截图](https://www.palantir.com/docs/resources/foundry/functions/pycharm-add-python-interpreter.png)
- 在 **Add Python Interpreter**（添加 Python 解释器）对话框的左侧窗格中，选择 **Virtualenv Environment**（虚拟环境）。
![配置 python 解释器截图](https://www.palantir.com/docs/resources/foundry/functions/pycharm-configure-python-interpreter.png)
- 选择 **Existing environment**（现有环境），并将 **Interpreter**（解释器）字段设置为你的 Conda 环境中的 Python 解释器。
  - 对于 Unix，Python 解释器路径为 `<your-conda-environment-dir>/bin/python`。
  - 对于 Windows，Python 解释器路径为 `<your-conda-environment-dir>\python.exe`。

> **ℹ️ 提示**
>
> 根据测试插件是否启用，已安装的环境会包括 `./python-functions/build/conda/run-env`、`./python-functions/build/conda/test-env` 或两者。如果你计划运行测试，应选择测试环境。

- 选择 **Ok**（确定）。

---

*原文：[Local development](https://www.palantir.com/docs/foundry/functions/python-local-development/)*
