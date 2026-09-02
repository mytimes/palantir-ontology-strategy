# 创建自定义聚合（Create a custom aggregation）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/create-custom-aggregation/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

函数可用于基于本体中的数据计算自定义聚合，随后可在 Workshop 的图表组件中展示。本指南将介绍如何编写自定义聚合逻辑：从本体加载聚合数据，对结果加以处理以推算未来结果，并返回修改后的结果。

在学习本节内容时，以下参考资料可能会有用：

- [对象集聚合](https://www.palantir.com/docs/foundry/functions/api-object-sets/#computing-aggregations)参考
- [聚合类型](https://www.palantir.com/docs/foundry/functions/types-reference/#aggregation-types)参考

## 加载聚合

在本例中，假设你有一个由费用（expenses）组成的本体，每个 `expense` 对象都具有部门名称、费用 `date`（日期）和费用 `amount`（金额）属性。如果你想估算未来六个月各部门的月度支出，可以先加载月度支出的聚合数据：

```typescript
const result = await Objects.search()
    .expenses()
    .groupBy(expense => expense.departmentName.topValues())
    .segmentBy(expense => expense.date.byMonth())
    .sum(expense => expense.amount);
```

## 操作聚合结果

接下来，你可以推算各部门未来六个月的支出。在本例中，你可以采用一种简单的方法：使用最后一个月的值作为未来六个月的估算值。

```typescript
const modifiedBuckets = result.buckets.map(bucket => {
    // Find the bucket corresponding to the most recent month
    const lastBucket = bucket.value[bucket.value.length - 1];

    let nextSixMonths: IBaseBucket<IRange<Timestamp>, Double>[] = [];
    let currentMonth = lastBucket.key.max!;
    // Loop six times
    for (let i = 0; i < 6; i++) {
        // Find the end of this range (the following month)
        const nextMonth = currentMonth.plusMonths(1);
        // Add a new bucket which uses the next month as the date range
        // and the most recent month as the value
        nextSixMonths.push({
            key: {
                min: currentMonth,
                max: nextMonth,
            },
            value: lastBucket.value,
        });
        currentMonth = nextMonth;
    }

    // Return the modified results
    return { key: bucket.key, value: nextSixMonths };
});
```

## 返回聚合

现在你已经创建了未来六个月的估算值，可以返回这些估算值了：

```typescript
return { buckets: modifiedBuckets };
```

该函数的完整示例代码如下：

```typescript
@Function()
public async estimatedDepartmentExpenses(): Promise<ThreeDimensionalAggregation<string, IRange<Timestamp>>> {
    const result = await Objects.search()
        .expenses()
        .groupBy(expense => expense.departmentName.topValues())
        .segmentBy(expense => expense.date.byMonths())
        .sum(expense => expense.amount);

    const modifiedBuckets = result.buckets.map(bucket => {
        // Find the bucket corresponding to the most recent month
        const lastBucket = bucket.value[bucket.value.length - 1];

        let nextSixMonths: IBaseBucket<IRange<Timestamp>, Double>[] = [];
        let currentMonth = lastBucket.key.max!;
        // Loop six times
        for (let i = 0; i < 6; i++) {
            // Find the end of this range (the following month)
            const nextMonth = currentMonth.plusMonths(1);
            // Add a new bucket which uses the next month as the date range
            // and the most recent month as the value
            nextSixMonths.push({
                key: {
                    min: currentMonth,
                    max: nextMonth,
                },
                value: lastBucket.value,
            });
            currentMonth = nextMonth;
        }

        // Return the modified results
        return { key: bucket.key, value: nextSixMonths };
    });

    return { buckets: modifiedBuckets };
}
```

生成的聚合可用于 Workshop 图表中，展示未来六个月的月度支出估算。

---

*原文：[Create a custom aggregation](https://www.palantir.com/docs/foundry/functions/create-custom-aggregation/)*
