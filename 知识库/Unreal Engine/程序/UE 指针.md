# Unreal Engine 5 中的指针类型详解

在 UE5 中，指针的使用需结合引擎的内存管理机制（如垃圾回收 GC）和现代 C++ 智能指针，以下是主要指针类型及其用途：

---

# 一、**UObject 相关指针**

所有继承自 `UObject` 的类（如 `AActor`, `UActorComponent`）均由 GC 管理，其指针需特殊处理。

## 1. `TObjectPtr<T>`**（UE5 新增）**

- **用途**：替代传统的裸指针（如 `UMyClass*`），用于安全引用 `UObject` 对象，支持编辑器序列化和类型安全检查。
    
- **场景**：
    
    - **类成员变量**：在类定义中声明对 `UObject` 的引用（UE5 推荐替代裸指针）。
        
    - **序列化兼容性**：确保资源在编辑器中的引用路径正确保存。
        
- **优势**：
    
    - 类型安全：明确区分 `UObject` 和其他类型。
        
    - 编辑器友好：支持资源重定向（如资源路径变更后自动更新）。
        
- **示例**：

    ```c
    // 类定义中
    UPROPERTY(EditDefaultsOnly)
    TObjectPtr<UTexture> MyTexture; // 替代 UTexture* MyTexture
    
    // 初始化时
    MyTexture = LoadObject<UTexture>(...);
    ```

## 2. **裸指针（Raw Pointer）**

- **用途**：临时引用 `UObject` 对象（不持有所有权）。
    
- **场景**：函数参数、临时变量（如 `AActor* Actor`）。
    
- **注意**：需手动检查有效性（`IsValid(Actor)`）。

## 3. **`TWeakObjectPtr<T>`**

- **用途**：弱引用 `UObject`，不阻止 GC 回收。
    
- **场景**：解决循环引用、观察者模式。

## 4. **`TSoftObjectPtr<T>`**

- **用途**：软引用资源（异步加载）。
    
- **场景**：动态加载模型、纹理等资产。

---

# 二、**非 UObject 智能指针**

管理自定义 C++ 类或非 `UObject` 对象。

## 1. **`TSharedPtr<T>`**

- **用途**：共享所有权，引用计数管理。
    
- **场景**：共享数据、跨模块传递。

## 2. **`TUniquePtr<T>`**

- **用途**：独占所有权，自动释放。
    
- **场景**：局部资源管理（如文件句柄）。

## 3. **`TWeakPtr<T>`**

- **用途**：弱引用 `TSharedPtr`。
    
- **场景**：观察共享资源，避免循环依赖。

---

# 三、**其他特殊指针**

## 1. **`FSoftObjectPath`**

- **用途**：存储资源路径字符串。
    
- **场景**：序列化动态加载路径。

## 2. **`TSubclassOf<T>`**

- **用途**：安全引用 `UClass`。
    
- **场景**：动态生成指定派生类对象。

---

# 四、**应用场景对比表**

|                  |            |        |         |                            |
| ---------------- | ---------- | ------ | ------- | -------------------------- |
| 指针类型         | 适用对象   | 所有权 | GC 安全 | 典型场景                   |
| `TObjectPtr<T>`  | UObject    | 无     | 是      | 类成员变量、编辑器兼容引用 |
| `TWeakObjectPtr` | UObject    | 无     | 是      | 避免循环引用、观察者模式   |
| `TSoftObjectPtr` | 资源资产   | 无     | 是      | 异步加载纹理、模型         |
| `TSharedPtr`     | 非 UObject | 共享   | 否      | 共享配置数据、跨模块传递   |
| `TUniquePtr`     | 非 UObject | 独占   | 否      | 管理局部资源、文件句柄     |
|                  |            |        |         |                            |

---

# 五、**UE5 最佳实践**

1. **优先使用** **`TObjectPtr`**替代裸指针（尤其在类成员变量中），以增强编辑器兼容性和类型安全。
    
2. **非 UObject 必须用智能指针**（`TSharedPtr`/`TUniquePtr`），避免内存泄漏。
    
3. **异步加载资源**：使用 `TSoftObjectPtr` 或 `Async Load` 方法。
    
4. **跨模块数据**：明确所有权（`TSharedPtr` 共享，`TUniquePtr` 独占）。

---

# 六、什么 UE5 引入 `TObjectPtr`**？**

- **类型安全**：区分 `UObject` 和其他类型，减少误用。
    
- **编辑器支持**：资源重定向（如移动文件路径后自动修复引用）。
    
- **未来兼容性**：为引擎底层优化（如 GC 改进）做准备。

合理选择指针类型，可以显著提升 UE5 项目的健壮性和可维护性。

# 软指针和弱指针

深入了解虚幻引擎中不同的软指针和弱指针类型。

[本人Ari Arnbjörnsson](https://dev.epicgames.com/community/profile/L16/Epic_Ari)

- **[工作人员](https://dev.epicgames.com/community/profile/L16/Epic_Ari)**

2 月 24, 2022•上次更新:4 月 05, 2023•

社区 UE4.27UE5.0

## 总结

只是想要摘要吗？给你！这些假设你正在制作游戏代码而不是编辑器插件。

## ✔使用这些指针类型

- **TSoftObjectPtr**：用于引用可能通过其路径加载或不加载的对象。即使未加载，也可以指向其他级别的参与者。当指向资产（如网格）时，可以与异步加载函数一起使用以临时加载它们。与 " 软对象引用 " 蓝图变量类型相同。
    
- **TSoftClassPtr**：用于引用类或蓝图类型，这些类型可能会被加载，也可能不会被加载。加载后，它将为您提供一个可以创建实例的类类型。可以与异步加载函数一起使用来加载它们。与 " 软类引用 " 蓝图变量类型相同。
    
- **TWeakObjectPtr**：用于引用已经实例化的对象。如果对象被销毁或垃圾回收，将解析为 null。

## ❌不要使用这些指针类型

- **FSoftObjectPath**：由其他指针类型☝内部使用。速度很慢，因为它不缓存结果。如果在编辑器中设置，它将指向 UBlueprint 类而不是 UBlueprintGeneratedClass，这通常不是游戏代码想要的。不过，编辑器插件的制造商可能需要这个功能。
    
- **FSoftClassPath**：与 FSoftObjectPath 相同，但有一些与加载类相关的辅助函数。现在主要是遗留类型。
    
- **FSoftObjectPtr**：TSoftObjectPtr 的非模板化 BP 非公开版本。

## 弱指针 Vs 软指针

创建或设置**弱指针**以指向已使用其**GUObjectArray**索引实例化的现有**UObject**。指针不需要是**UPROPERTY**即可知道它指向的对象是否已被垃圾回收。

一个**软指针**是对象或资产的路径的字符串表示，**可能会或可能不会被加载**。它在内部存储一个额外的弱指针，以便在查询和找到对象后（在非编辑器构建中）跟踪对象。

## 软指针

ℹ_ 对于几乎所有情况，您应该使用 ****TSoftObjectPtr****或****TSoftClassPtr****，但所有相关类的解释如下。_

## FSoftObjectPath

🛑_ 不要为游戏代码创建自己的 ****FSoftObjectPath**** 变量。它们不会缓存解析的对象，因此每个查询都会再次搜索该对象。它们还指向蓝图类资产（****UBlueprint****），而不是生成的类（路径名中的****UBlueprintGeneratedClass****，"****_C****"）。****UBlueprint'****在打包构建中被剥离。使用****TSoftObjectPtr****或****TSoftClassPtr****。_

![[Pasted image 20250426000051.png]]

> 实例详细信息/类默认视图中的 FSoftObjectPath。

所有类型的**软指针**内部使用。软对象路径变量可以在蓝图中创建，但只应用于知道自己在做什么、不想指向**UBlueprintGeneratedClass**类且不需要缓存的编辑器实用程序。

如果在蓝图中使用，它将只允许选择磁盘上的资产，而不允许选择对象实例。要指向实例，请使用**TSoftObjectPtr**。

要将选择限制为资产类型，您可以使用**MetaClass** **UProperty**元数据（或使用**TSoftClassPtr**代替这种功能）。

当对象被解析时，它将使用哈希搜索它，与普通或弱指针相比，这有一些开销。扩展类通常会缓存它们，因此一旦解析，它就不会再次搜索。

## FSoftClassPath

与**FSoftObjectPath**相同，但有一些与加载类相关的辅助函数。现在主要是遗留类型。不要创建自己的，而是使用**TSoftClassPtr**。

## FSoftObjectPtr

软对象和类指针的基类。内部保留一个用于查找对象的**FSoftObjectPath**和一个用于在找到对象后缓存它的**FWeakObjectPtr**。

扩展了许多功能所在的**TPersistentObjectPtr**。

不是**USTRUCT**，所以它不能用于蓝图，只有 C++。

有两种扩展类型，**TSoftObjectPtr**或**TSoftClassPtr**，它们提供了一种模板化的、BP 可见的方式来分别指向对象实例或资产（数位长）。应该使用它们。

当您调用**Get（）**时，它将在基类上调用它，基类将首先检查内部**WeakPtr**是否先前已缓存（在非 PIE 会话中），否则它将调用**FSoftObjectPath. ResolveObject**来查找对象。

## TSoftObjectPtr

![[Pasted image 20250426000128.png]]

> 实例详细信息/类默认值视图中的 TSoftObjectPtr。

![[Pasted image 20250426000143.png]]

> 在蓝图中创建软对象指针。

通用**FSoftObjectPtr**的模板包装器。可用于蓝图。

可用于指向具有路径名的任何内容。最好用于指向可能未加载的内容，无论是磁盘上的资产还是任何级别的对象。

💡_ 因为它可以指向任何级别中可能尚未加载的对象，所以它对于跨级别通信非常强大，在我看来，它的使用不如应该的多。_

![[Pasted image 20250426000211.png]]

> Actor 位于当前未加载的另一个级别，但属性仍保留其路径值。

如果要引用已实例化的参与者，请改用**TWeakObjectPtr**。

编辑器中的默认值不能设置为蓝图类型（请改用**TSoftClassPtr**）。但它可以指向数据资产。

该值可以通过选择加载的参与者在实例详细信息窗口中设置，甚至可以在当前打开的地图中的任何参与者的类默认值中设置。

当指向的对象未加载时调用**Get（）**时，它将返回**nullptr**。加载后，它将返回具有匹配全名的参与者并将其缓存以供进一步查询。

## TSoftClassPtr

![[Pasted image 20250426000228.png]]

> 实例详细信息/类默认值视图中的 TSoftClassPtr。

![[Pasted image 20250426000239.png]]

> 在蓝图中创建软类指针。

围绕**FSoftObjectPtr**的模板包装器，其工作方式类似于**TSubclass Of**，它可以在**UProperties**中用于蓝图子类。

使用它来指向蓝图类型（UBlueprintGeneratedClass），您可以查询它们是否已加载。也可用于异步加载类（或同步加载，尽管这会引入故障）。

不适用于**数据资产**，因为它们不应该被实例化，而是使用**TSoftObjectPtr**。

## 弱指针

弱指针不像软指针那样存储路径名，它们只引用已经实例化的对象。

## 兜帽下

弱指针只存储两个东西：**int32 ObjectIndex**和**int32 ObjectSerialNumber**。

当你调用 Get 时，它将首先使用**ObjectIndex**从**GUObjectArray**获取对象（如果存在）。

*" 但是 ObjectIndex 只有 32 位，如果你不断生成和销毁参与者，它不容易用完数字吗？"* 你问？很好的问题，是的，理论上是这样的，但是由于你在任何时候最多只有几十万个对象活着，这些索引会被重用。

因此，我们有**ObjectSerialNumber**来确保它确实是索引重用时我们想要的对象。**ObjectSerialNumber**仅在弱指针第一次指向**UObject**时分配，否则为零。这是一个线程安全的增量**int32**计数器。

*你问 " 当我们用完****这些**__ 序列号时会发生什么 "*？这就是游戏致命的断言（即崩溃）。这意味着在你用完序列号之前，你将能够创建新的弱指针，指向最多 2, 147, 483, 647 个唯一的**UObject**。对于大多数用例来说，这应该绰绰有余，但要考虑到运行时间很长的进程或弱指针的奇怪使用。

在比较序列号后，它最终检查**UObject** **IsValid（）**，如果不是（在**PendingKill**或**IsGarbage**的情况下）返回**null**，否则返回对象。因此，您可以确定取消引用弱指针将始终返回有效的**UObject**或**nullptr**。

所有**委托**在使用**AddUObject**/**BindUObject**函数时通过**弱指针**保持对其**UObject**的引用，因此您可以安全地订阅委托，而不必担心在销毁期间取消订阅。这与使用**AddRaw**/**BindRaw**函数相反，后者使用非托管 C++ 指针，因此请小心对待这些指针，并在处理**UObject**时尽量不要使用它们。

## 总结

总而言之，**弱指针**是指向**UObject**的一种极好且安全的方法，在取消引用时有一些小开销。毕竟不是那么弱！因为它们处理获取和验证**UObject**的所有内部，所以它们也不必是**UPROPERTY**。

## FWeakObjectPtr

指向内存中 UObject 的弱指针。不是**USTRUCT**，因此不能在蓝图中使用，C++。

它是**TWeakObjectPtr**的基类，您应该使用它。

## TWeakObjectPtr

![[Pasted image 20250426000301.png]]

> 实例详细信息视图中的 TWeakObjectPtr。

通用**FWeakObjectPtr**的模板化版本。可以在蓝图中使用，但必须在 C++ 中声明。

许多人可能不知道这一点，但该值可以在**实例详细信息**视图中设置（不是类默认值），即使它在内部不使用对象的字符串表示。

因为它不使用字符串路径，所以它跳过了查找资产的开销，但它只能设置为已经实例化的对象。

C++ 您可以指向任何实例化的**UObject**，无论它在哪个级别，只要它已经加载。
