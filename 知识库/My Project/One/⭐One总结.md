# ⭐One 总结

## 项目立项

2025 年 5 月 20 日立项
使用 UE5 源代码引擎构建并创建项目, 选择游戏 ->空白 ->C++, 项目名称为 One
如果要在项目内使用插件内容的话, 需要在项目设置中设置可以引用的内容

# 使用的 Plugins

个人
- [x] `Async Loading Screen` 异步加载关卡
- [x] `Auto Foot step` 自动标记脚步 Tag
- [x] `Auto Size Comments` 自动注释
- [x] `Clouds Lighting` 雾气光照
- [x] `Flat Nodes` 扁平化的节点
- [x] `Project Cleaner` 项目清理
- [x] `Restart Editor` 快速重启引擎
- [x] `Switch Language` 快速切换引擎语言
- [x] `Kawaii Physics` 角色物理
- [x] `SPCR Joint Dynamics` 布料物理
- [x] `Auto Node Arranger` 自动整理节点

官方
- [x] `Gameplay Abilities` 游戏能力
- [x] `Animation Warping` 动画扭曲
- [x] `Animation Locomotion Library` 动画运动函数库
- [x] `Chaos Vehicles Plugin` 混沌车辆
- [x] `UMG Viewmodel` 视图模型
- [x] `Common UI` 通用 UI
- [x] `Gameplay State Tree` 游戏状态树
- [x] `Mover` 移动组件
- [x] `Text 3D` 3D 文本
- [x] `Water` 水

已经保存在 D 盘的资产文件夹中, **版本为支持 5.5.4 二进制版本**
已在项目的 Plugins 文件夹内创建

# 使用的引擎版本

> 根据 UE 官方分支构建的**源码引擎**, **目前是 5.5.4**
> 也许未来会升级引擎
> **原因:** 需要使用的插件不支持 5.6 预览版, 否则使用最新分支
> 但是需要源码引擎构建~~专用服务器~~

# 使用 Developers 文件夹

充分使用 `Developers` 文件夹, 将要导入的资源优先导入到开发者文件夹中
使用 `Developers` 文件夹来测试场景等

# 项目文件架构

[[Content文件夹架构]]
[[Source文件夹架构]]

# 引用文件

1. 真实天气系统 (`UltraDynamicSky`) 不能移入开发者文件
