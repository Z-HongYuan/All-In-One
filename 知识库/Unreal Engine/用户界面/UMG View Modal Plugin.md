# [MVVC视图模式插件](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/umg-viewmodel-for-unreal-engine) 官方文档

View Modal 是一个简单的对象, 用于保存 UI 感兴趣的数据, 并定义对此数据任何更改的监听器

创建一个视图模型: 思考一个视图模型应该包含那些数据?

建议 View Modal 应该小而模块化

# 创建 View Modal

## C++ 中创建

例如一个 HP 视图模型

1. 从 MVVMViewModelBase 派生 C++ 类
    
2. 添加 UI 需要的变量

3. **标记为字段通知**

```c++
UPROPERTY(BlueprintReadWrite, FieldNotify, Setter, Getter, meta=(AllowPrivateAccess))
int32 CurrentHealth;
```

4. 当变量更改时, 手动调用相关的宏函数

![[Pasted image 20250426001943.png]]

|   |   |
|---|---|
|ViewModel 宏|说明|
|UE_MVVM_BROADCAST_FIELD_VALUE_CHANGED([事件名称])|广播事件。|
|UE_MVVM_SET_PROPERTY_VALUE([成员名称], [新值])|测试字段值是否已更改，然后设置字段的新值并广播事件。|

`SET_PROPERTY_VALUE` 宏的作用与 `BROADCAST_FIELD_VALUE` 宏基本相同，不同的是 `SET_PROPERTY_VALUE` 宏会在赋值并广播之前检查值是否已更改。这项检查在为 Viewmodel 创建 Setter 函数时很常见，将其包括在内是为了方便起见。

如果你想通知直接绑定到该值的控件， `BROADCAST_FIELD_VALUE_CHANGED` 宏能以变量本身为参数，否则也能以函数名称为参数。

5. 调用宏的最佳位置

在变量的设置器中

```c++
void SetCurrentHealth(int32 NewCurrentHealth)
{
    if (UE_MVVM_SET_PROPERTY_VALUE(CurrentHealth, NewCurrentHealth))
    {
        UE_MVVM_BROADCAST_FIELD_VALUE_CHANGED(GetHealthPercent);
    }
}
```

6. 在说明符中设置 Setter 和 Getter

这将会查找 Set 或者 Get 的同变量名的函数, 在访问或者设置的时候, 调用函数而不是之间修改变量.

或者直接在 Set 和 Get 里面设置函数名

7. **函数也能标记为字段通知**

函数必须满足的条件

- 必须具有带 `FieldNotify` 和 `BlueprintPure` 说明符的 `UFUNCTION` 宏。
    
- 不得带有输入参数。
    
- 必须是 `const` 函数。
    
- 必须仅返回单个值（没有输出参数）。

在变量更改时进行计算, 转化, 格式化非常有用, 比如计算血量百分比

---

## 蓝图中创建

例如一个 HP 视图模型

1. 从 MVVMViewModelBase 派生蓝图类
    
2. 添加 UI 需要的变量
    
3. **标记为字段通知**

![[Pasted image 20250426002048.png]]

蓝图操作类似于 C++

# 应用于 Widget

## 在 View Modal 窗口内, 添加需要的 View Modal.

## 初始化 View Modal

### 创建 View Modal 实例 (该控件会自动创建它自己的 Viewmodel 实例)

为控件的每个实例创建一个新的 View Modal 实例 (这会产生很多实例), 当每个控件实例都希望有自己的数据, 不受其他实例的影响的时候使用

你可以在 C++ 中的初始化回调后或在蓝图中的初始化回调期间为 Viewmodel 赋值。系统只会在 Viewmodel 未设置时创建新实例。

在 C++ 中创建或者在下列蓝图中创建

![[Pasted image 20250426002054.png]]

### 手动 View Modal 实例

**手动（Manual）** 创建方法需要你在应用程序代码的某个位置自行创建一个 Viewmodel 实例，然后手动将其赋值给控件。控件具有 Viewmodel 对象引用，但在你为它赋值 Viewmodel 之前，它将具有空值。你还可以在 Create Widget 节点中创建时分配 Viewmodel。

### 全局 Viewmodel 集合

**全局 Viewmodel 集合（Global Viewmodel Collection）** 是 **MVVM 游戏子系统（MVVM Game** **Subsystem****）** 中可全局访问的 Viewmodel 列表。这些 Viewmodel 非常适合用于处理可能需要在你的整个 UI 中访问的变量，例如你的游戏选项菜单的设置。如需将 Viewmodel 添加到蓝图中的全局 Viewmodel 集合，请执行以下步骤：

1. 添加对 MVVM 游戏子系统的引用。
    
2. 从 MVVM Game Subsystem 节点拖移引脚，然后命名为 **Get Viewmodel Collection** 。
    
3. 从全局 Viewmodel 集合拖出一个引脚，然后调用 **Add Viewmodel Instance** 。

然后你可以构建一个 Viewmodel 实例，并通过此节点将其添加到集合中。你可在 **游戏实例（Game Instance）** 类中方便地初始化这些实例。

![[Pasted image 20250426002100.png]]

选择 " 全局 Viewmodel 集合（Global Viewmodel Collection）" 作为初始化模式时，请通过 **全局 Viewmodel 标识符（Global Viewmodel Identifier）** 中的 Add Viewmodel Instance 节点提供 **上下文名称（Context Name）** 。此名称必须与 Viewmodel 的类名一致。例如，如果你的 Viewmodel 叫做 VM_GraphicsOptions，你需要将其同时用作上下文名称和全局 Viewmodel 标识符。

### 属性路径

**属性路径（Property Path）** 创建方法提供的替代方案可能更简洁，需要的代码支持也更少。控件并不是让其他类访问其内部来设置其 Viewmodel 引用，而是通过一系列函数调用和引用向外访问来获取 Viewmodel。编辑器中的 " 属性路径（Property Path）" 字段要求一系列句点分隔的成员名称，并且它假定调用这些函数的起始点是 `Self` 。换言之，它的起始点始终是你正在编辑的控件。

不要在你的属性路径中手动指定 `Self` ，因为 " 属性路径（Property Path）" 字段已经假定你的起始点是对 `Self` 的引用。

例如，以下字段会获取控件的所属玩家控制器，然后获取它当前控制的载具上的 Viewmodel：

```C++
        GetPlayerController.Vehicle.ViewModel
```

你还可以调用在蓝图中定义的函数，这有助于精简属性路径的逻辑并提高灵活性。例如，以下函数会从拥有该控件的角色获取角色生命值 Viewmodel：

![[Pasted image 20250426002109.png]]

然后你可以使用以下函数的名称作为属性路径：

```C++
        GetHealthViewModel
```

### 解析器

通过创建的**解析器类**, 用于**覆盖创建设图模型函数**

用自己的逻辑和实现来获取或者创建视图模型

# 监听视图模型的更改

看文档, 绑定 VM 变量
