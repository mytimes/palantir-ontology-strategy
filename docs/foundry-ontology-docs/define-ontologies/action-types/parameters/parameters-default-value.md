# 设置参数默认值（Set parameter default value）

> 本文为 [Palantir 官方文档](https://www.palantir.com/docs/foundry/action-types/parameters-default-value/)的非官方中文翻译，仅供学习参考。原文 © 2026 Palantir Technologies Inc. 保留所有权利。

动作类型参数的默认值用于在动作表单中预填参数。默认值在参数级别配置，在 Workshop、Object Explorer、Object Views、Quiver 和 Slate 中均受支持。可以部署默认值，以在多个使用方应用中标准化动作逻辑，从而无需在每个应用中单独添加默认值。

参数可以设置默认值，以显示固定值或所选对象的某个属性。

## 默认值与本地变量的交互

本地默认值（例如 Workshop 变量）始终优先于全局默认值。将任何 Workshop 变量传递给带有默认值的动作时，动作表单将使用 Workshop 变量的值预填。同样的模式适用于来自 Object Views 的环境变量和来自 Slate 的默认值。在动作的每个实例中提供的默认值优先。因此，任何向默认值的迁移都需要先移除本地覆盖。

## 配置默认值

选择任意参数都会打开该参数的参数配置视图。请选择参数是默认为固定值，还是使用来自对象参数属性的值。

### 静态默认值

设想一个示例动作类型，它将所选 `Aircraft`（飞机）对象的 `Type` 属性修改为 `A320`。要进行配置，请点击进入 `Type` 参数并添加静态默认值。

![配置静态默认值](https://www.palantir.com/docs/resources/foundry/action-types/default_value_static_configuration.png)

如果没有默认值，要实现类似的用户体验，需要在使用该参数的每个应用中配置输入值。更新此行为（例如改为 `A380`）将需要手动进行修改，而且可能涉及多个应用。

![静态默认值示例](https://www.palantir.com/docs/resources/foundry/action-types/default_value_static_example.png)

### 对象属性默认值

要将对象属性设置为参数的默认值，首先选择一个要配置的对象参数。考虑一个更通用的动作类型 `Change Airplane Details`（更改飞机详情）：例如，在该动作类型中，用户在进行编辑前需要知道某个属性的当前值。这可以通过将每个参数的值配置为从当前所选对象（本例中为要修改的 `Plane` 对象）预填来实现。只有在输入列表中位于该参数上方的对象引用参数才能用作默认值。

![配置属性默认值](https://www.palantir.com/docs/resources/foundry/action-types/default_value_object_configuration.png)

在 Object Explorer 中，`Change Airplane Details` 动作将使用当前值预填。在这种情况下，用户可以选择只修改一个属性而保持其余不变。无论动作在哪里提交，都会应用相同的默认逻辑。请注意，一旦动作用户更新了此默认值，`Lifetime Hours` 值就会显示为已编辑。

![对象默认值](https://www.palantir.com/docs/resources/foundry/action-types/default_value_object_example.png)

### 类型类预填

动作参数可以通过添加类型类注解来预填特殊值（例如自动生成的 UUID 或当前用户的 ID）。本体文档中提供了[可用类型类的完整列表](https://www.palantir.com/docs/foundry/object-link-types/metadata-typeclasses/)。

![配置类型类预填](https://www.palantir.com/docs/resources/foundry/action-types/default_value_type_class_configuration.png)

在大多数情况下，你应将参数可见性设置为 `hidden`（隐藏），这样用户就不会手动更改这些特殊的预填值。

---

*原文：[Set parameter default value](https://www.palantir.com/docs/foundry/action-types/parameters-default-value/)*
