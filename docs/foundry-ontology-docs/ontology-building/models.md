# 本体中的模型（Models in the Ontology）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/ontology/models/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

组织希望利用人工智能（AI）和机器学习（ML）来加速并改善决策。但将 AI/ML 落地到运营的现实十分复杂，典型的投资回报很少能达到预期。

Foundry 提供了弥合这一差距所需的关键能力：可信的数据基础、根据组织目标评估和比较模型的工具，以及将模型部署到面向用户的运营工作流中的功能。本页聚焦于最后一步：将已评估的模型部署到生产环境。

## 端到端工作流

概括来说，以下是在 Foundry 中将 AI/ML 落地、借助本体进行实时推理所需的端到端步骤：

1. 在 Foundry 中[创建模型](https://www.palantir.com/docs/foundry/integrate-models/integrate-overview/)。
2. 配置[直接模型部署](https://www.palantir.com/docs/foundry/manage-models/create-a-model-deployment/)。
3. [为你的模型发布一个简单的包装函数](https://www.palantir.com/docs/foundry/model-integration/model-functions-guide/)，并[视需要从另一个函数中调用它](https://www.palantir.com/docs/foundry/functions/functions-on-models/)，围绕你的模型编排复杂逻辑。
4. 在 [Workshop](https://www.palantir.com/docs/foundry/workshop/functions-use/)、[Vertex](https://www.palantir.com/docs/foundry/vertex/overview/) 和其他面向最终用户的应用中使用该函数进行实时推理。

本体对象也可以由利用模型进行批量推理的数据集支撑——[了解如何在 Code Repositories 中使用模型](https://www.palantir.com/docs/foundry/model-integration/tutorial-train-code-repositories/)。

## 优势

正如将数据集映射到本体概念能为工作流开发和决策提供[益处](https://www.palantir.com/docs/foundry/ontology/why-ontology/)一样，将模型映射到本体也带来诸多优势：

- **可解释性（Interpretability）。** 因为所有建模结果都是以真实世界概念（对象类型的属性）定义的，最终用户无需理解机器学习即可使用建模结果。用户只需与*预测（forecast）*、*估计（estimate）*或*分类（classification）*这样的简单概念交互。
- **规模经济（Economies of scale）。** 每个建模项目不必再是为服务特定用例而单独定制的工作，各项建模成果可以随时间在彼此基础上积累。例如，为一个用例生成的预测可以立即用于后续用例，减少重复工作，并随时间更快地为最终用户提供价值。
- **大规模连通性（Connectivity at scale）。** 通过纳入 ML 模型，本体成为组织的单一事实来源，不仅在数据方面，也在*逻辑*方面。模型编码了组织对事物未来可能如何变化的预期。这样，本体成为整个企业的“数字孪生”，从而能够在整个组织范围内模拟各种变化——若是依靠一大堆各自为政的分散建模工作，这绝无可能实现。

---

*原文：[Models in the Ontology](https://www.palantir.com/docs/foundry/ontology/models/)*
