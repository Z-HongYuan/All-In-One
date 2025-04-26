(汽车)(飞机)

[Chaos载具插件](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/vehicles-in-unreal-engine)

---

# 载具基础

直升机

DMC_12轮式载具

使用Chaos物理系统Chaos载具

异步tick物理/在引擎物理设置里面/选择性开启

必须要骨骼网格体

轮子必须要对齐X和Z轴,因为Y轴用于旋转所以可以不用分别正负,但是XZ必须对齐,X轴向前,Z轴向上

必须保证 Y轴旋转,Z轴转向

骨骼轴缩放必须是1:1:1

Root根节点物理类型,盒体,凸包等都行工具生成碰撞：这里生成碰撞的工具需要骨骼上有顶点绑定，否则就只会生成骨骼大小的碰撞体

四个车轮骨骼,工具生成球体碰撞

车轮物理禁用碰撞反应,更改物理类型为运动学

创建载具蓝图,导入对应的网格体和动画蓝图

添加设置所需的轮子

车轮蓝图设置牵引力,半径,宽度,旋转角度,能否被控制等等

设置外部曲线

![[Pasted image 20250426103215.png]]

设置油门,转向,刹车

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

Chaos调试命令

![[Pasted image 20250426103230.png]]
`p.Chaos.DebugDrawEnabled`

启用Chaos全局调试模式

---

重载Chaos载具Pawn

添加ASC插件,这是刚需,因为在HUD里面需要,没有就会崩溃