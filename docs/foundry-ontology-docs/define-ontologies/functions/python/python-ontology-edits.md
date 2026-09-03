# 本体编辑（Ontology edits）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/functions/python-ontology-edits/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

除了编写从本体读取数据的函数外，你还可以编写用于创建对象、编辑对象属性以及对象之间的链接的函数。本页记录了函数中可供你使用的对象编辑 API。有关编辑函数工作原理的更多细节，请参阅[概览页](https://www.palantir.com/docs/foundry/functions/edits-overview/)。

要使函数中创建的编辑真正被应用，本体编辑函数*必须配置为[函数支撑的动作（function-backed Action）](https://www.palantir.com/docs/foundry/action-types/function-actions-overview/)*。以这种方式配置动作后，你可以提供额外的元数据、配置权限，并在各种运营界面中访问该动作。如[文档](https://www.palantir.com/docs/foundry/functions/edits-overview/#when-edits-are-applied)中所述，在动作之外运行编辑函数不会实际修改任何对象数据。

> **⚠️ 注意**
>
> 编辑对象后立即搜索对象可能会返回意外结果。详情请参阅[注意事项一节](https://www.palantir.com/docs/foundry/functions/edits-overview/#edits-and-object-search)。

## 定义编辑函数

编辑本体的函数必须：

- 使用从 `functions.api` 导入的 `@function(edits=[MyObjectType])` 装饰器进行装饰，以指定将要编辑的对象类型。
- 具有从 `functions.api` 导入的显式 `list[OntologyEdit]` 返回类型提示。

## 构建本体编辑容器

要在 Python 函数中执行本体编辑，首先通过 [OSDK 客户端](https://www.palantir.com/docs/foundry/functions/python-functions-on-objects/)构建一个本体编辑容器。例如：

```python
ontology_edits = FoundryClient().ontology.edits()
```

该容器用于跟踪函数中所做的全部编辑。

## 更新属性

Python 函数中的本体对象默认是只读的。尝试修改其属性将引发异常。

要编辑对象，首先使用本体编辑容器获取该对象的可编辑视图，可以基于现有对象实例：

```python
editable_object = ontology_edits.objects.MyObjectType.edit(my_object)
```

或者给定对象主键：

```python
editable_object = ontology_edits.objects.MyObjectType.edit(object_primary_key)
```

获得可编辑对象后，你可以通过为对象的属性重新赋值来编辑属性值。例如：

```python
editable_employee.last_name = new_name
```

在同一次函数执行中，随后访问 `editable_employee` 的 `last_name` 属性值将得到刚刚设置的新值。但是，原始的不可编辑对象*不会*反映这些更改。

可编辑对象上的[数组属性](https://www.palantir.com/docs/foundry/functions/api-objects-links/#array-properties)是只读的。要修改数组，请创建它的副本、修改副本，然后更新属性：

```python
# Copy to a new array
array_copy = list(editable_object.my_array_property)
# Now you can modify the copied array
array_copy.append(new_item)
# Then overwrite the property value
editable_object.my_array_property = array_copy
```

请注意，现有对象的主键属性值无法更新。

## 更新链接

单链接和多链接属性提供了多种用于更新链接的方法：

```python
# Set an Employee's supervisor
editable_employee.supervisor.set(new_supervisor)

# Clear an Employee's supervisor
editable_employee.supervisor.clear()

# Add a new report to the given employee
editable_employee.reports.add(new_report)

# Remove an old report associated with the given employee
editable_employee.reports.remove(new_report)
```

与更新属性一样，在更新 `editable_employee` 的链接之后访问这些链接，将反映你所做的更新。

## 创建对象

你可以使用本体编辑容器上的 `MyObjectType.create()` 方法创建新对象。创建新对象时，必须为其主键指定一个值。

在本例中，我们使用给定 ID 创建一个新的 Ticket 对象，设置其 `due_date` 属性，并通过修改 `assigned_tickets` 链接将其分配给给定的 Employee。

```python
from datetime import datetime, timedelta

from functions.api import function, Integer, Array, OntologyEdit
from ontology_sdk import FoundryClient
from ontology_sdk.ontology.objects import Employee, Ticket

@function(edits=[Employee, Ticket])
def create_new_ticket_and_assign_to_employee(
    employee: Employee,
    ticket_id: Integer
) -> list[OntologyEdit]:
    ontology_edits = FoundryClient().ontology.edits()

    new_ticket = ontology_edits.objects.Ticket.create(ticket_id)
    new_ticket.due_date = datetime.now() + timedelta(days=7)

    editable_employee = ontology_edits.objects.Employee.edit(employee)
    editable_employee.assigned_tickets.add(new_ticket)

    return ontology_edits.get_edits()
```

除主键外，属性值也可以直接传递给 `create()` 方法。例如：

```python
new_due_date = datetime.now() + timedelta(days=7)
new_ticket = ontology_edits.objects.Ticket.create(ticket_id, due_date=new_due_date)
```

## 删除对象

你可以通过调用本体编辑容器上的 `MyObjectType.delete()` 方法来删除对象。

在本例中，我们删除分配给给定员工的所有 Ticket：

```python
for ticket in employee.tickets:
    ontology_edits.objects.Ticket.delete(ticket)
```

也可以使用主键而非实例来删除对象：

```python
ontology_edits.objects.Ticket.delete(ticket_id)
```

---

*原文：[Ontology edits](https://www.palantir.com/docs/foundry/functions/python-ontology-edits/)*
