# 批量执行（Batched execution）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/action-types/function-actions-batched-execution/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

当动作以批量方式触发时（例如在 [Workshop 内联编辑](https://www.palantir.com/docs/foundry/workshop/widgets-object-table/#inline-edits-cell-level-writeback)或 [Automate](https://www.palantir.com/docs/foundry/automate/execution-settings/) 中），支撑函数通常会在每次请求中调用一次，并依次执行，且所有编辑会在动作调用结束时以原子方式应用。

或者，为了提升性能或解决编辑冲突，你可能希望将函数配置为在单次执行中接收整批动作调用。

要启用批量执行，函数必须接收*单个输入参数*，其中包含*一个结构体列表*（也称为“映射（map）”或“字典（dictionary）”）。之后你就可以启用批量执行，并像通常向函数的顶层输入传递数据那样，将数据传入该结构体的字段。

使用批量执行时：

- 单次动作调用会触发一次函数执行，列表输入参数中只有*单个条目*。
- 批量动作调用会触发一次函数执行，列表输入参数中包含*多个条目*。

### 示例

与以下签名的函数支撑动作不同：

```typescript
@OntologyEditFunction()
  public updateDestination(flight: Flight, destination: Airport): void {
    // update flight object
}
```

函数可以改为接收一“批”请求，并在单次执行中全部处理：

```typescript
@OntologyEditFunction()
public updateDestinationBatch(batch: {flight: Flight, destination: Airport}[]): void {
    batch.forEach(({flight, destination}) => {
      // update flight object
    });
}
```

然后，在配置动作类型时，你可以为该函数启用批量执行：

![批量执行开关](https://www.palantir.com/docs/resources/foundry/action-types/function_backed_actions_batch_execution_toggle.png)

---

*原文：[Batched execution](https://www.palantir.com/docs/foundry/action-types/function-actions-batched-execution/)*
