(汽车)(飞机)

[Chaos载具插件](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/vehicles-in-unreal-engine)

---

# 载具基础

直升机

DMC_12 轮式载具

使用 Chaos 物理系统 Chaos 载具

异步 tick 物理/在引擎物理设置里面/选择性开启

必须要骨骼网格体

轮子必须要对齐 X 和 Z 轴, 因为 Y 轴用于旋转所以可以不用分别正负, 但是 XZ 必须对齐, X 轴向前, Z 轴向上

必须保证 Y 轴旋转, Z 轴转向

骨骼轴缩放必须是 1:1:1

Root 根节点物理类型, 盒体, 凸包等都行工具生成碰撞：这里生成碰撞的工具需要骨骼上有顶点绑定，否则就只会生成骨骼大小的碰撞体

四个车轮骨骼, 工具生成球体碰撞

车轮物理禁用碰撞反应, 更改物理类型为运动学

创建载具蓝图, 导入对应的网格体和动画蓝图

添加设置所需的轮子

车轮蓝图设置牵引力, 半径, 宽度, 旋转角度, 能否被控制等等

设置外部曲线

![[Pasted image 20250426103215.png]]

设置油门, 转向, 刹车

启用模拟物理

质心设置

优先调整车轮的牵引力参数

调试命令

![[Pasted image 20250426103224.png]]
`Show Debug Vehicle`

![[Pasted image 20250426103227.png]]
`p.Vehicle.NextDebugPage`

换页

---

Chaos 调试命令

![[Pasted image 20250426103230.png]]
`p.Chaos.DebugDrawEnabled`

启用 Chaos 全局调试模式

---

重载 Chaos 载具 Pawn

添加 ASC 插件, 这是刚需, 因为在 HUD 里面需要, 没有就会崩溃
