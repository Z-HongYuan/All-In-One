# One 调研

所有的集合

# AI 建议

## 文件夹结构

- 统一使用命名例如 `BP_` 前缀
- 官方推荐将 `Content/` 下资源分为：`Art/`、`Characters/`、`Environments/`、`Gameplay/`、`UI/` 等子文件夹

> > [!tip] C++ 方面文件夹结构
> > - `CommonnSource` 在 `Source/Common/` 模块中定义项目级的所有基类（例如角色基类、武器系统接口、通用工具库、日志与配置管理等），其他模块可在 `.Build.cs` 中声明依赖，从而实现 " 基类—子类 " 继承关系 。`CommonSource` 内的功能建议按功能域再拆分子文件夹（`AI、UI、Gameplay、Utils`），保持扁平且逻辑清晰。

```c++
GameplayFeatures/
├─ TPSFeature/
│  ├─ TPSFeature.uplugin
│  ├─ Content/
│  │  ├─ Maps/
│  │  ├─ Blueprints/
│  │  └─ DataTables/
│  └─ Source/                // 可选：若包含自定义模块
└─ FPSFeature/
    ├─ FPSFeature.uplugin
    └─ Content/
      ├─ Maps/
      ├─ Blueprints/
      └─ Materials/
```

> > [!tip] 蓝图方面文件夹结构
> > Content 下创建如 **Maps**、**Blueprints**、**Characters**、**Environments**、**UI**、**Audio**、**VFX**、**Textures**、**Materials**、**DataTables** 等文件夹

```c++
Content/
├─ Art/                        // 所有纯美术资源
├─ CommonAsset/                // 通用资产（公共 UI、通用特效、共享材质等）
└─ GameplayFeatures/           // 按 Game Feature 插件划分玩法包
    ├─ TPSFeature/              // TPS 玩法相关蓝图、关卡、数据表
    ├─ FPSFeature/              // FPS 玩法相关资源
    └─ MOBAFeature/             // MOBA 玩法相关资源

```

---

## 项目整体架构

> [!tip] 架构和模块
> - 使用模块化与插件化架构 `Module/Plugin` 系统
> - 使用 `GameFeature` 插件
> - 构建垂直的切片, 每个都能独立依靠 `MainCode` 独立运行

> **!!!!!!!可能需要提前学习专用服务器的构建**
