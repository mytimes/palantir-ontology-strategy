# 实现接口（Implement an interface）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/interfaces/implement-interface/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

接口一旦定义，任何符合接口定义的对象类型都可以实现它。这意味着对象类型必须具有满足接口必填属性的属性、满足所有必填链接类型约束的链接，以及满足接口上定义的所有必填动作类型约束的动作类型。

用一个对象类型实现接口，表示该对象类型是该接口在本体中的具体实例。此声明为该对象类型带来了额外的功能，即：

- 针对该接口的对象集服务（Object Set Service）搜索，将返回实现该接口的对象类型中相匹配的对象。
- 与实现对象类型的对象交互时，既可以在类型化为具体对象类型时使用它们的本地 API 名，也可以在类型化为接口类型时使用属性和链接的接口 API 名。

简而言之，实现接口后，应用消费方可以通过接口定义与所有实现该接口的对象交互。这使得应用代码可以将接口用作 API 层来编写，而无需应用单独支持每个实现对象类型。此外，通过将接口用作应用 API 层，新对象类型只需实现应用接口即可加入应用，而无需更改代码来显式支持新对象类型。

## 如何在 Ontology Manager 中实现接口

按照以下步骤用一个对象类型实现接口。

### 1. 选择你的接口和对象类型

首先，在 Ontology Manager 中导航到该对象类型并打开 **Interfaces**（接口）选项卡。在页面右上角选择 **+ Implement new interface**（+ 实现新接口）。

![从对象类型实现接口。](https://www.palantir.com/docs/resources/foundry/interfaces/implement-from-object-type.png?width=800)

在出现的对话框中，选择要实现的接口。

![选择要实现的接口。](https://www.palantir.com/docs/resources/foundry/interfaces/implement-select-interface.png?width=800)

或者，导航到接口概览页面并在 **Implementations**（实现）部分选择 **+ New**（+ 新建）。

![从接口概览实现接口。](https://www.palantir.com/docs/resources/foundry/interfaces/implement-from-interface-overview.png?width=800)

然后，选择要实现该接口的对象类型。

![选择要实现的接口。](https://www.palantir.com/docs/resources/foundry/interfaces/implement-select-object-type.png?width=800)

### 2. 映射本地属性

要实现接口，对象类型必须声明从现有对象属性到接口必填属性的映射。如果某个接口属性被标记为 **optional**（可选），则可以跳过映射。

![在接口与实现对象类型之间映射属性。](https://www.palantir.com/docs/resources/foundry/interfaces/implement-map-properties.png?width=800)

### 3. 映射链接类型约束

如果接口上声明了任何必填的[链接类型约束](https://www.palantir.com/docs/foundry/interfaces/interface-link-types-overview/)，你必须在对象类型上选择一个满足每个必填链接类型约束的链接类型。你也可以选择性地为任何非必填链接类型约束提供链接映射。你可以选择现有链接类型或创建新链接类型来满足每个约束。

![映射链接类型以满足链接类型约束。](https://www.palantir.com/docs/resources/foundry/interfaces/implement-link-type-constraint.png?width=800)

### 4. 映射动作类型约束

如果接口上声明了任何必填的[动作类型约束](https://www.palantir.com/docs/foundry/interfaces/interface-action-type-constraints/)，你必须在对象类型上选择一个满足每个必填动作类型约束的动作类型。你也可以选择性地为任何非必填动作类型约束提供动作映射。

创建接口实现后，从对象类型的 **Interfaces**（接口）选项卡配置参数映射。在保存对本体的更改之前，你必须将任何必填参数约束映射到具体动作类型上兼容的必填参数。

### 5. 保存更改

选择 **Save**（保存）以将更改应用到你的本体。

## 如何在 Pipeline Builder 中实现接口

按照以下步骤在 Pipeline Builder 中的[对象类型输出](https://www.palantir.com/docs/foundry/pipeline-builder/outputs-add-ontology-output/#add-an-object-type-output)上实现接口。

### 1. 打开输出类型配置

选择你希望在其上实现接口的对象类型输出，然后选择 **Edit**（编辑）选项。

![编辑对象类型输出。](https://www.palantir.com/docs/resources/foundry/interfaces/implement-interface-object-type-output-edit.png?width=800)

### 2. 选择要实现的接口

选择 **Implement interface**（实现接口）。

![选择“实现接口”。](https://www.palantir.com/docs/resources/foundry/interfaces/implement-interface-pipeline-builder-implement-button.png?width=800)

然后，选择要实现的接口并选择 **Implement and go to mapping**（实现并前往映射）。

![接口选择并前往映射。](https://www.palantir.com/docs/resources/foundry/interfaces/implement-interface-pipeline-builder-selection.png?width=800)

### 3. 映射本地属性

要实现接口，对象类型必须包含接口的共享属性，**或者**声明从现有对象属性到接口共享属性的映射。接口和对象类型上都存在的共享属性将被自动映射。对象类型上不存在的任何共享属性都需要你手动输入映射以满足接口定义。

![映射本地属性。](https://www.palantir.com/docs/resources/foundry/interfaces/implement-interface-pipeline-builder-mapping.png?width=800)

### 4. 查看已实现的接口

你可以从输出类型配置面板查看此对象类型输出已实现的接口。

![查看已实现的接口。](https://www.palantir.com/docs/resources/foundry/interfaces/implement-interface-pipeline-builder-review.png?width=800)

> **ℹ️ 提示**
>
> Pipeline Builder 目前在实现接口时不支持链接类型约束或动作类型约束映射。如果你的接口包含必填的链接类型约束或必填的动作类型约束，你必须通过 Ontology Manager 实现该接口。

---

*原文：[Implement an interface](https://www.palantir.com/docs/foundry/interfaces/implement-interface/)*
