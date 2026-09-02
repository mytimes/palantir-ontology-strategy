# 规模与属性限制（Scale and property limits）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/action-types/scale-property-limits/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

为确保被编辑的对象类型能够快速处理编辑并更新面向用户的数据、而不拖慢线上应用，平台设有若干限制。提交的动作若超出这些限制将不会成功，并会向用户显示错误消息。

## 配置限制

`Allow multiple values`（允许多个值）开关允许用户向参数传入值列表。

| 限制 | 最大值 |
| --- | --- |
| 原始类型列表参数中的元素数量 | 10,000 |
| 对象引用列表参数中的元素数量 | 1,000 |
| 列表参数用于提交条件时的元素数量 | 1,000 |

## 编辑限制

| 限制 | 最大值 |
| --- | --- |
| 单次动作提交中可编辑的**对象类型**数量 | 50 |
| 单次动作提交中可编辑的**对象**数量 | 10,000 |
| 动作提交中对**对象**的每次单独编辑 | 32KB（OSv1）、3MB（OSv2） |

## 批量调用限制

一个动作在批量调用中最多可被调用 10,000 次。当动作由函数支撑且该函数未配置为使用[批量执行](https://www.palantir.com/docs/foundry/action-types/function-actions-batched-execution/)时，此限制降为 20 次。

在强制执行[编辑限制](#编辑限制)时，批量动作调用中应用的编辑会被视为单个组，无论批量中的哪个请求导致了这些编辑。

根据调用应用的不同，可能还会有其他限制适用。

## 支持的属性类型

以下是受支持的单值属性类型和数组属性类型的规格。请注意，某些属性类型仅受对象存储 v2（OSv2）支持。

### 单值属性类型

| 属性类型 | 参数类型 | 支持情况 |
| --- | --- | --- |
| 附件 | 附件 | 是 |
| 布尔 | 布尔 | 是 |
| 字节 | 整数 | 是 |
| 密文 | 字符串 | 是 |
| 日期 | 日期 | 是 |
| Decimal | Decimal | 是 |
| Double | Double | 是 |
| Float | Double | 是 |
| 地理点 | 地理点 | 是 |
| 地理形状 | 地理形状 | 是 |
| 地理时间序列引用 | 地理时间序列引用 | 是（仅 OSv2） |
| 整数 | 整数 | 是 |
| Long | Long | 是 |
| 强制控制 | - | 不作为属性受支持，动作中也不支持 |
| 媒体引用 | 媒体引用 | 是（仅 OSv2） |
| 字符串 | 字符串 | 是 |
| Short | 整数 | 是 |
| 结构体 | 结构体 | 是（仅 OSv2） |
| 时间戳 | 时间戳 | 是 |
| 时间序列引用 | 时间序列引用 | 是（仅 OSv2） |
| 向量 | Double 列表 | 是（仅 OSv2） |

### 数组属性类型

| 数组属性类型 | 列表参数类型 | 支持情况 |
| --- | --- | --- |
| 附件 | 附件 | 是 |
| 布尔 | 布尔 | 是 |
| 字节 | 整数 | 是 |
| 密文 | 字符串 | 是 |
| 日期 | 日期 | 是 |
| Decimal | Decimal | 是 |
| Double | Double | 是 |
| Float | Double | 是 |
| 地理点 | 地理点 | 是 |
| 地理形状 | 地理形状 | 是 |
| 地理时间序列引用 | 地理时间序列引用 | 否 |
| 整数 | 整数 | 是 |
| Long | Long | 是 |
| 强制控制 | 强制控制 | 是 |
| 媒体引用 | - | 动作中不支持 |
| 字符串 | 字符串 | 是 |
| Short | 整数 | 是 |
| 结构体 | 结构体 | 是（仅 OSv2） |
| 时间戳 | 时间戳 | 是 |
| 时间序列引用 | - | 不作为属性受支持，动作中也不支持 |
| 向量 | - | 不作为属性受支持，动作中也不支持 |

## 支持的属性

目前，动作不能用于编辑对象的**主键**。修改主键等同于删除一个对象然后再添加一个新对象；你可以使用[规则](https://www.palantir.com/docs/foundry/action-types/rules/#ontology-rules)直接创建或删除对象，而不是用动作编辑主键。

## 通知接收者

使用[副作用通知](https://www.palantir.com/docs/foundry/action-types/notifications/)时，单个动作最多可通知 500 名接收者。当通知内容为“从函数”（From a function）渲染时，此限制降为 50 名接收者。有关生成通知时需要考虑的更多限制，请参阅[通知的最大接收者限制](https://www.palantir.com/docs/foundry/action-types/notifications/#maximum-recipient-limits)文档。

---

*原文：[Scale and property limits](https://www.palantir.com/docs/foundry/action-types/scale-property-limits/)*
