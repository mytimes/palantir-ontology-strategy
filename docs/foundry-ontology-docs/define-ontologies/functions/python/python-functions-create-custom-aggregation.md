# 创建自定义聚合（Create a custom aggregation）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/python-functions-create-custom-aggregation/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

函数可用于基于本体中的数据计算自定义聚合，然后可以将其呈现在 Workshop 的图表组件中。本指南将介绍如何编写自定义聚合逻辑：从本体加载聚合数据，对结果加以处理以推算未来结果，并返回修改后的结果。

在学习本节时，以下参考资料可能会有用：

- [Python 聚合类型](https://www.palantir.com/docs/foundry/functions/types-reference/#aggregation-types)参考

> **⚠️ 注意**
>
> `TwoDimensionalAggregation` 和 `ThreeDimensionalAggregation` 中的 `.from_osdk()` 仅在使用 v2 版本的 Python OSDK 时受支持。

## 加载聚合

在本示例中，假设你有一个由费用组成的本体，每个 `expense`（费用）对象都具有部门名称、费用 `date`（日期）和费用 `amount`（金额）属性。如果你想估算未来六个月各部门的月度支出，可以先加载月度支出的聚合数据：

```python
client = FoundryClient()
result: AggregateObjectsResponse = (
    client.ontology.objects.Expense
    .group_by(Expense.object_type.department_name.exact())
    .group_by(Expense.object_type.date.exact())
    .sum(Expense.object_type.amount)
    .compute()
)
```

## 操作聚合结果

接下来，你可以推算每个部门未来六个月的支出。在本示例中，你可以采用一种简单的方法：使用最后一个月的值作为未来六个月的估算值。

```python
current_buckets = ThreeDimensionalAggregation.from_osdk(result, "departmentName", "date")
modified_buckets: list[NestedBucket[str, Range[Date], Double]] = []
date_format = "%Y-%m-%d"
for bucket in current_buckets.buckets:
    # Find the bucket corresponding to the most recent month
    last_bucket: SingleBucket[Date, Double] = bucket[-1].value

    next_six_months: list[SingleBucket[Range[Date], Double]] = []
    # The `date` field has been converted to a string formatted YYYY-MM-DD.
    # Convert to type `Date` from the string. Convert back to a string when
    # creating a SingleBucket object for each month
    current_month: Date = datetime.strptime(last_bucket.key, date_format).date()

    # Loop six times
    for _ in range(6):
        # Construct the next month from the current month
        next_month = current_month + relativedelta(months=1)
        # Add a new bucket which uses the next month as the date range
        # and the most recent months amount as the value
        next_six_months.append(SingleBucket(Range(min=current_month, max=next_month), last_bucket.value))
        current_month = next_month

    # Append the modified results as a NestedBucket
    modified_buckets.append(NestedBucket(bucket.key, next_six_months))
```

## 返回聚合

现在你已经创建了未来六个月的估算值，可以将这些估算值作为 `ThreeDimensionalAggregation` 返回：

```python
return ThreeDimensionalAggregation(modified_buckets)
```

此函数的完整示例代码如下：

```python
from dateutil.relativedelta import relativedelta
from datetime import datetime

from functions.api import (
    Date,
    Double,
    NestedBucket,
    SingleBucket,
    String,
    Range,
    ThreeDimensionalAggregation,
    function,
)
from ontology_sdk import FoundryClient
from ontology_sdk.ontology.objects import Expense


@function
def estimated_department_expenses() -> ThreeDimensionalAggregation[str, Date, Double]:
    client = FoundryClient()
    result = (
        client.ontology.objects.Expense
        .group_by(Expense.object_type.department_name.exact())
        .group_by(Expense.object_type.date.exact())
        .sum(Expense.object_type.amount)
        .compute()
    )

    current_buckets = ThreeDimensionalAggregation.from_osdk(result, "departmentName", "date")
    modified_buckets: list[NestedBucket[str, Range[Date], Double]] = []
    date_format = "%Y-%m-%d"
    for bucket in current_buckets.buckets:
        # Find the bucket corresponding to the most recent month
        last_bucket: SingleBucket[Date, Double] = bucket.buckets[-1]

        next_six_months: list[SingleBucket[Range[Date], Double]] = []
        # The `date` field has been converted to a string formatted YYYY-MM-DD.
        # Convert to type `Date` from the string.
        current_month: Date = datetime.strptime(last_bucket.key, date_format).date()

        # Loop six times
        for _ in range(6):
            # Construct the next month from the current month
            next_month = current_month + relativedelta(months=1)
            # Add a new bucket which uses the next month as the date range
            # and the most recent months amount as the value
            next_six_months.append(SingleBucket(Range(min=current_month, max=next_month), last_bucket.value))
            current_month = next_month

        # Append the modified results as a NestedBucket
        modified_buckets.append(NestedBucket(bucket.key, next_six_months))

    return ThreeDimensionalAggregation(modified_buckets)
```

生成的聚合可用于 Workshop 图表中，以显示未来六个月的月度支出估算。

---

*原文：[Create a custom aggregation](https://www.palantir.com/docs/foundry/functions/python-functions-create-custom-aggregation/)*
