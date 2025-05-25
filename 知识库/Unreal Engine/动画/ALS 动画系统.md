# PlayerCameraBehavior/玩家相机行为 (动画蓝图)

![[Pasted image 20250525211241.png]]
在玩家摄像机管理器内添加骨骼网格体, 通过获取骨骼网格体的动画蓝图实例内的曲线值切换摄像机的相关参数, 并且适用于状态机, 也可在动画蓝图内同步角色状态变量, 通过状态变量切换摄像机参数, 相当于同步摄像机和角色

## 事件图表

事件: 蓝图更新动画 ->Update Character Info(更新角色信息)

角色数据驱动

动画蓝图的状态机控制摄像机行为

|   |   |   |   |
|---|---|---|---|
|PlayerController/玩家控制器|玩家控制器|动画蓝图内创建的变量, 通过玩家控制器传入设置变量|References/引用|
|ControlledPawn/受控 Pawn|Pawn|
|MovementState/运动状态|XXX Movement State|通过 ControlledPawn 的蓝图接口同步数据到动画蓝图本地, 以便使用|BPI_Get_CurrentStates/BPI_ 获取 _ 当前状态|
|MovementAction/运动操作|XXX Movement Action|
|RotationMode/旋转模式|XXX Rotation Mode|
|Gait/步态|XXX Gait|
|Stance/姿势~~(站姿)~~|XXX Stance|
|ViewMode/视口模式~~(查看模式)~~|XXX ViewMode|
|RightShoulder/是否为右肩|布尔|BPI_Get_CameraParameters/BPI_ 获取 _ 摄像机参数|
|DebugView/debug 视角|布尔|

## 状态机

![[Pasted image 20250525211306.png]]
![[Pasted image 20250525211323.png]]
![[Pasted image 20250525211335.png]]
![[Pasted image 20250525211408.png]]

根据传入的数据进行混合

同时使用节点缓存姿势

状态机切换不同的骨骼曲线数据, 含义就是切换不同的摄像机位置, 旋转等数据

状态机切换规则就是根据 RotationMode 的值切换

顺序:

主状态机 (三个基础模式)->添加是否为左右肩混合 (覆盖)->添加运动操作混合 (覆盖)->添加是否为第一人称混合 (覆盖)->添加是否为布娃娃模式混合 (覆盖)->添加是否为 debug 视角混合 (覆盖)->输出最终姿势

无实现接口

# Camera_Skeleton/摄像机骨骼 (骨骼)

拥有单段骨骼的简陋骨骼网格体

## 骨骼附带曲线

|   |   |   |
|---|---|---|
|CameraOffset_X/Y/Z|摄像机偏移|X 值最大, 对于摄像注视锚点不动的情况下调整摄像机的位置, 类似于调整摄像机的支架臂长度<br><br>Z 代表摄像机整体抬高多少|
|PivotLagSpeed_X/Y/Z|枢轴点滞后速度|摄像机注视的枢轴平滑跟随到角色 (位置) 的速度|
|PivotOffset_X/Y/Z|枢轴偏移|摄像机注视的枢轴相对于角色 (位置) 的偏移量|
|RotationLagSpeed|旋转滞后速度|旋转时, 平滑跟随到确定的位置的速度|
|State_Default|状态默认值|0-1 确定是否为第一人称等状态或者<br><br>0~1 混合相对应的状态|
|State_FirstPerson|状态第一人称|
|State_Ragdoll|状态布娃娃模式|
|State_Rolling|状态翻滚|
|Weight_ADS|ADS 权重|
|Weight_FirstPerson|第一人称权重|
|State_Debug|状态 Debug|
|Override_Debug|覆盖 Debug|

# XXX_PlayerCameraManager/玩家摄像机管理器

重要重载函数 BluePrintUpdateCamera/蓝图更新摄像机

CustomCameraBehavior 计算摄像机位置, 旋转数据的函数

CalculateAxisIndependentLag 计算不同轴分别过渡的函数

Get_CameraBehaviorParam 获取动画实例内曲线值 (参数) 的函数

~~GetDebugTraceType 获取调试跟踪类型~~

![[Pasted image 20250525211433.png]]
当玩家控制器拥有新角色时，设置 " 受控制的 Pawn"。（从玩家控制器调用）

![[Pasted image 20250525211454.png]]
更新了 " 摄影机行为动画 BP" 中的引用。

|   |   |   |
|---|---|---|
|ControlledPawn|受控 Pawn||
|DebugViewOffset|调试视图偏移和旋转||
|DebugViewRotation||
|RootLocation|根位置||
|SmoothedPivotTarget|平滑的枢轴目标||
|PivotLocation|枢轴位置||
|TargetCameraLocation|目标摄像机位置||
|TargetCameraRotation|目标摄影机旋转||

![[Pasted image 20250525211538.png]]

# A1

自定义摄像机需要参数, 所以需要到角色实时位置数据, 加上动画蓝图返回的曲线值

在蓝图里面引用一个骨骼网格体并且创建基于它的动画蓝图 含义是, 把骨骼网格体作为桥梁, 骨骼网格体所包含的曲线值作为变量

因为骨骼里面的曲线, 在动画蓝图和管理器蓝图里面都能获取/修改, 就相当于一个蓝图接口传递参数, 动画蓝图又可以通过获取角色的状态, 实时修改曲线值

比如角色平常的待机状态, 这时实时通过摄像机动画蓝图获取的角色状态值实时修改状态机到对应的摄像机参数, 此时再由摄像机管理器蓝图每一帧获取并且添加到返回参数

# 玩家摄像机控制器/摄像机 Camera[摄像机动画蓝图](note://WEBec068f87b53dc28fd1498bd1358fbf19)

1. 主要是为了自定义摄像机的行为动作
2. 玩家摄像机管理为蓝图类
3. 曲线控制/曲线是关于动画蓝图的
4. 为了使用动画蓝图, 需要一个带有骨骼的骨骼网格体来创建动画蓝图

在高级运动系统里面, 作者用骨骼网格体的动画蓝图/状态机/骨骼曲线控制摄像机行为

## 在事件图表里面设置调用并和摄像机动画蓝图建立联系以同步数据

设置自定义事件 OnPossess(需输入 pawn)

操作是当玩家控制器拥有新角色时，获取设置 " 受控制的角色 (变量)"(从玩家控制器调用), 然后再通过类型转换将 " 玩家控制器 " 和 " 受控制的角色 (变量)" 同步传递到摄像机行为动画蓝图

玩家控制器 ->摄像机管理器 ->摄像机动画蓝图

## 玩家摄像机管理器 *(PlayerCameraManager)* 蓝图类 BP

- 数据/变量

- ControlledPawn/控制的角色
- SmoothedPivotTarget/平滑的轴心目标
- PivotLocation/轴心旋转
- TargetCameraLocation/目标摄像机位置
- TargetCameraRotation/目标摄像机旋转

- 局部变量

- BlueprintUpdateCamera

- TargetLocation
- TargetRotation
- TargetFOV

- CustomCameraBehavior

- PivotTarget
- FPTarget
- TPFOV
- FPFOV

- CalculateAxisIndependentLag

- CameraRotationYaw

- 为了自定义摄像行为/最重要的就是重载函数 (蓝图摄像机更新 BlueprintUpdateCamera)

基本上在玩家摄像机管理器里面的函数都是为了这个自定义摄像机行为的 函数而服务的

这个函数需要三个参数

- 摄像机位置 Location
- 摄像机旋转 Rotation
- 摄像机 FOV

接下来基本上就是为了这三个数据的计算函数

- 获取摄像机信息 _CustomCameraBehavior

从蓝图接口获取角色的基础数据

经过多次迭代计算返回最终的摄像机位置/旋转

1. (获取数据) 通过蓝图接口传递信息, 让玩家摄像机管理器里能获取到人物角色蓝图里面的数据

2. 摄像机蓝图接口 OP_Camer_BPI 需要在角色蓝图里面重写函数 (返回的数据)

3. (获取平滑的摄像机本身的旋转) 获取玩家控制器的旋转 (注意需要是拥有角色的玩家控制器) 和摄像机的旋转, 通过 "R 插值到 " 由获取摄像机行为动画蓝图里面的曲线值实现平滑的摄像机旋转到目标期望旋转 (控制器旋转)

4. 需要一个函数来获取摄像机动画蓝图里面的曲线值 GetCameraBehaviorParam 以曲线名字获取摄像机动画蓝图里面的曲线值

5. (获取能平滑的摄像机注视锚点的变换) 在第一步获取的人物的/旋转/位置, 通过曲线值提供的偏移跟随速度, 在每个 XYZ 每个轴上 (原位置/旋转)->(目标位置/旋转) 的平滑过渡.意义是, 为了平滑摄像旋转和移动, 让平滑位置 (变量), 在 XYZ 轴上平滑的跟随人物的位置, 总的来说有两个位置, 摄像机注视的位置是正在跟随角色的位置

6. 函数: 计算每个独立轴心滞后 _CalculateAxisIndependentLag 在 XYZ 轴上分解计算过渡

7. (在第三步的基础上获取摄像机注视锚点的位置) 这一步是为了应用注视锚点偏移量, 通过曲线值.得到注视目标位置/通过第三步获取的过渡平滑的轴目标计算并且获取自定义应用局部偏移后轴心位置
8. (获取第四步的轴心位置并且应用自定义摄像机偏移) 同样使用曲线值, 就相当于摄像机的弹簧臂, 获取到摄像机应该在的位置
9. 根据接口获取的初始位置跟计算多次后的摄像机位置之间检测碰撞 (通道检测) 以设置新的摄像机位置/含义是防止摄像机穿墙
10. 绘制 Debug 视角的球体
11. 由动画蓝图的曲线值判断是第一人称还是第三人称, 如果是第一人称就覆盖掉之前计算的数据, 返回第一人称的位置参数, 如果不是, 就返回计算好的数据

## 玩家摄像行为 *(PlayerCameraBehavior)* 动画蓝图

- 骨骼曲线

- CameraOffset_X/Y/Z/摄像机偏移量
- Override_Debug
- PivotLagSpeed_X/Y/Z/轴心跟随速度
- PivotOffset_X/Y/Z/轴心偏移量
- RotationLagSpeed/旋转跟随速度
- State_Default/xxx/状态
- Weight_FirstPerson/权重

- 数据/变量

- 传递过来引用的数据

- PlayerController/玩家控制器
- ControlledPawn/受控制的角色

- 角色数据

- MovementState/移动状态
- MovementAction/移动操作
- RotationMode/旋转模式
- Gait/步态
- Stance/站立姿势
- ViewMode/视图模式
- RightShoulder/是否为右肩
- DebugView/未使用/Debug 视角

- 事件图表

- 事件蓝图更新动画时

- 运行 UpdateCharacterInfo(更新角色信息)

- 函数

- UpdateCharacterInfo(更新角色信息) 蓝图接口获取角色数据

- 动画图表

- 状态机

- 主状态机

- 速度方向, 角色一直朝向运动方向
- 注视方向, 角色一直朝向摄像机方向
- 瞄准方向, 角色朝向瞄准方向

- 修改曲线

- 在每个状态机下通过修改曲线节点, 提供给玩家摄像机管理器使用/相当于提供可修改的变量以使用

- 混合姿势

- 以获取的角色数据为判断, 以此来混合姿势/或者说以角色状态数据为标准提供不同的值

# 图

![[Pasted image 20250525211630.png]]

绿色球体 (第一步) 就是通过蓝图接口传递过来的胶囊体的中间锚点

红色球体 (第三步) 就是通过跟随绿色球体产生的位置

蓝色球体 (第四步) 就是最终的摄像机注视的目标位置

# 摄像机动画蓝图 [摄像机Camera](note://WEBf9aed36a35b52b82d5d0aa8fcdf5fd7d)

重点是如何根据曲线值修改摄像机状态, 还有就是混合设置如何调解

可以当成摄像机参数的变量来看

缓存姿势的使用

根据更改的角色信息来切换提供不同的曲线值

## 更新角色引用和角色信息

![[Pasted image 20250525211721.png]]

# 动画图表

- 主状态机

- 速度方向 _VelocityDirection

![[Pasted image 20250525211732.png]]
三个基础参数 默认/奔跑/冲刺

根据 Gait 参数切换

Gait 切换后再添加修改的
![[Pasted image 20250525211758.png]]

根据 Stance 切换站立还是蹲下, 混合
![[Pasted image 20250525211807.png]]

最后修改旋转速度
![[Pasted image 20250525211816.png]]

- 注视方向 LookingDirection
- 瞄准 _Aiming

三个差不多都是一样的流程, 只是曲线值有修改

- 过渡规则

1. 有共享规则
2. 混合设置/根据自定义的曲线来混合
3. 根据旋转的模式来切换不同的状态/瞄准方向/速度方向/注视方向
