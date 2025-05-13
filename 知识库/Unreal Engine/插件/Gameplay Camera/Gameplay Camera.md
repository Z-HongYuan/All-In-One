# Gameplay Camera

Gameplay 摄像机系统 (摄像机组件)

UE 的新组件:CameraSystem

---

模块化的摄像机系统

使用 `CameraAsset` 和 `BlueprintCameraDirectorEvaluator(CDE)` 蓝图导演控制

其中 `CDE` 需要填入 `CA` 的导演选项中, 才能正确的关联起来

![[Pasted image 20250426103845.png]]

|   |   |
|---|---|
|Gameplay 摄像机组件（Gameplay Camera Component）|使用摄像机资产的蓝图组件。|
|Gameplay 摄像机 Actor（Gameplay Camera Actor）|包含 Gameplay 摄像机组件并使用摄像机资产的蓝图 Actor。|
|摄像机资产（Camera Asset）|包含摄像机绑定、过渡和摄像机指示器相关数据的资产。|
|摄像机绑定（Camera Rigs）|通过节点网络定义摄像机的行为。|
|摄像机过渡（Camera Transitions）|定义各摄像机绑定的进入和退出的过渡。|
|摄像机共享过渡（Shared Camera Transitions）|如未指定过渡，则任何摄像机绑定都可用的进入和退出过渡。|
|摄像机指示器求值器（Camera Director Evaluator）|对摄像机指示器求值器蓝图类的引用。|
|摄像机指示器（Camera Director）|管理任何特定时间内活动的摄像机绑定。指示器可以是下列类型之一： 蓝图摄像机指示器、单个摄像机指示器、或状态树摄像机指示器。|
|蓝图摄像机指示器（Blueprint Camera Director）|包含在 Gameplay 过程中选择活动摄像机绑定自定义逻辑的蓝图类。|
|单个摄像机指示器（Single Camera Director）|激活指定的摄像机绑定。不包含选择逻辑。|
|状态树摄像机指示器（StateTree Camera Director）|使用状态树选择活动的摄像机绑定。|
|摄像机变量集合（Camera Variable Collection）|包含用于修改摄像机资产内部属性的变量集合。|
