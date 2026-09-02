# 对象函数（FOO）（Functions on objects (FOO)）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/functions-on-objects/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

Foundry 中的函数原生支持访问和修改本体中对象与链接的数据。在本体中定义对象和链接类型后，你可以将这些类型导入函数代码仓库，系统会自动生成代码绑定。这些代码绑定包括对以下功能的支持：

- 将对象和对象集类型作为[参数](https://www.palantir.com/docs/foundry/functions/types-reference/#ontology-types)传入函数
- 使用[对象集 API](https://www.palantir.com/docs/foundry/functions/api-object-sets/) 按需搜索对象集
- 使用 [OntologyEditFunctions](https://www.palantir.com/docs/foundry/functions/edits-overview/) 修改对象

由于这种对本体的原生支持，Foundry 中的函数远远超出了常用的函数即服务（FaaS）平台，它为数据存储、检索和修改提供原生支持——且这一切都在 Foundry 对数据安全、数据血缘和透明度的保障之下。

[了解如何开始使用对象函数。](https://www.palantir.com/docs/foundry/functions/foo-getting-started/)

> **ℹ️ 提示**
>
> “对象函数”（functions on objects，有时简称 “FOO”）一词被宽泛地用于指代读取对象数据的函数——无论是通过参数还是使用对象搜索——但在 Foundry 中，“对象函数”并不是一个有别于其他函数的正式概念。

---

*原文：[Functions on objects (FOO)](https://www.palantir.com/docs/foundry/functions/functions-on-objects/)*
