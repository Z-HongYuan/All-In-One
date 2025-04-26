# Title
> [!总结]
> > # My First Project
> > # 我的第一个正式项目
> > 使用了许多 `UE5` 的新技术, 目的是为了学习, 理解游戏开发,以及巩固 `UE` 基础

# 版本控制管理
~~使用Github远程保存整个项目，包括但不限于Cpp文件和蓝图。~~
==~~为什么使用Git+Perforce?~~==
~~因为在UE中，蓝图文件使用二进制保存。对于Git而言不擅长处理频繁变化的二进制文件，会使整个.git仓库变得庞大。反而采用集中式的Perforce适合管理UE的蓝图等资产。~~
~~因此使用Git管理代码相关的文件，使用Perforce管理蓝图文件。~~

1.  ~~Git~~
	~~使用Git管理代码，配置等文本文件~~
	- ~~Source文件夹，包含项目所需的Cpp文件等~~
	- ~~Config文件夹，包含项目设置，游戏设置等配置文件~~
	- ~~Plugins文件夹，包含项目所需的插件等~~

2.  ~~Perforce~~
	~~使用Perforce管理蓝图等频繁修改的二进制文件。~~
	- ~~仅仅只有Content文件夹~~

 **最后选择使用 `Git` + `Github` 管理整个项目文件, 包括美术文件, 但是需要确定版本后再添加进 `VSC` 管理**

---

# AI 建议
在 **Unreal Engine** 中使用 **GAS（Gameplay Ability System）** 实现类似《原神》的 **元素反应机制**，需要结合 **Gameplay Tags**、**Gameplay Abilities** 和 **Gameplay Effects** 来管理元素状态和触发反应。以下是实现的核心步骤和逻辑：

---

1. 核心设计思路
    

《原神》的元素反应机制基于 **元素附着（Elemental Application）** 和 **元素交互（Elemental Interaction）**。例如：

- 火 + 水 → 蒸发（Vaporize）
    
- 雷 + 冰 → 超导（Superconduct）
    
- 其他元素组合触发不同效果（融化、感电、冻结等）。
    

核心逻辑：

1. 每个元素技能施加一个
    

**元素状态（如 Pyro, Hydro, Cryo 等）**。

2. 当两个元素状态共存时，触发对应的反应，并清除或替换原有状态。
    
3. 反应可能造成伤害、状态变化或增益效果。
    

---

4. 实现步骤
    

#### 2.1 定义元素标签（Gameplay Tags）

- 在
    

**Gameplay Tag Manager** 中创建元素标签：

plaintext

复制

Element.Pyro

Element.Hydro

Element.Cryo

Element.Electro

Element.Dendro

Element.Anemo

Element.Geo

- 创建反应类型标签（可选）：
    

plaintext

复制

Reaction.Vaporize

Reaction.Melt

Reaction.Superconduct

#### 2.2 创建元素效果（Gameplay Effects）

- 元素附着（Elemental Application）
    

：

每个元素技能施加一个 **持续时间（Duration）** 的 Gameplay Effect，附带对应的元素标签。

GE_Pyro，附带 Element.Pyro 标签。

：

当检测到元素组合时，触发一个新的 Gameplay Effect，例如：

Element.Pyro 和 Element.Hydro，施加高额伤害。

State.Frozen 状态，限制移动。

#### 2.3 检测元素反应

在 **Gameplay Ability** 或 **Actor Component** 中检测元素组合：

1. 当施加元素时，检查目标当前的
    

**Active Gameplay Effects**。

2. 通过
    

Gameplay Tag 匹配现有元素和反应表。

3. 触发对应的反应逻辑。
    

**示例代码（C++）**：

cpp

复制

```c++
// 在施加元素效果后检测反应
void UElementSystemComponent::CheckElementReaction(FGameplayTagContainer AppliedTags)
{
    // 获取目标当前的所有元素标签
    FGameplayTagContainer ExistingTags;
    TargetActor->GetActiveGameplayTags()->GetGameplayTagContainer(ExistingTags);

    // 检测元素组合
    if (ExistingTags.HasTag(Element.Pyro) && AppliedTags.HasTag(Element.Hydro))
    {
        TriggerReaction(Reaction.Vaporize);
    }
    else if (...)
    {
        // 其他反应逻辑
    }
}
```

  

#### 2.4 反应逻辑实现

- 触发反应
    

：根据反应类型执行逻辑：

立即伤害

：通过 Gameplay Effect 施加伤害（Modify Attribute）。

状态变化

：施加新的状态（如冻结、感电）。

元素清除

：移除原有元素标签。

**示例（蓝图）**：

1. 使用
    

Gameplay Effect 的 Modifiers 调整角色属性（如生命值）。

2. 通过
    

Gameplay Cue 播放特效和音效。

---

3. 高级功能
    

#### 3.1 元素优先级与共存

- 元素优先级
    

：某些元素会覆盖其他元素（如强火 + 弱水 → 火残留）。

- 持续时间管理
    

：通过 Gameplay Effect 的 Duration 和 Period 控制元素附着时间。

#### 3.2 网络同步

- 使用
    

Gameplay Ability 的 Net Execution Policy 确保客户端和服务器同步。

- 通过
    

Ability Task 处理客户端特效。

#### 3.3 数据驱动设计

- 使用
    

**Data Table** 或 **Curve Table** 定义反应伤害公式（如 基础伤害 × 元素精通）。

---

4. 工具与插件推荐
    

- GAS Companion
    

：简化 GAS 的配置和管理。

- GAS Shooter 示例项目
    

：参考如何实现状态交互。

- Niagara 系统
    

：用于元素反应特效。

---

5. 参考资源
    

- 官方文档
    

：

[Unreal Engine Gameplay Ability System](https://docs.unrealengine.com/5.0/en-US/gameplay-ability-system-in-unreal-engine/)

- 社区教程
    

：

[GAS 实战教程 by Tranek](https://github.com/tranek/GASDocumentation)

通过以上步骤，可以实现类似《原神》的复杂元素反应系统，关键在于合理设计 Gameplay Tag 和 Gameplay Effect 的交互逻辑。

---

---

开发一款 **TPS（第三人称射击）游戏** 需要一套完整的系统架构，涵盖核心玩法、技术实现、网络同步、内容管理等多个方面。以下是基于 **Unreal Engine** 的 TPS 游戏典型系统架构设计，分为 **核心系统**、**技术支撑系统** 和 **扩展系统** 三大部分：

---

### 一、核心系统架构

6. 角色控制系统
    

- 角色移动（Character Movement）
    
    - 实现基础移动（走、跑、蹲、匍匐）、跳跃、攀爬、掩体交互（Cover System）。
        
    - 使用 UE 的
        

**Character Movement Component**，结合 **Root Motion** 或 **Motion Warping** 实现平滑动作。

- 摄像机控制（Camera Control）
    
    - 第三人称摄像机跟随（Spring Arm + Camera Boom）。
        
    - 瞄准时切换为越肩视角（ADS：Aim Down Sight）。
        
    - 动态碰撞检测（防止穿墙）。
        
- 角色动画系统
    
    - 使用
        

**Animation Blueprints** + **Blend Spaces** 控制移动和动作混合。

**Anim Montages** 或 **State Machines** 实现。

7. 武器与战斗系统
    

- 武器管理（Weapon System）
    
    - 武器切换（主武器、副武器、近战武器）。
        
    - 武器属性（射速、弹匣容量、后坐力、散射）。
        
    - 使用
        

**Sockets** 将武器绑定到角色骨骼。

- 射击与伤害计算
    
    - 射线检测（Line Trace）或弹道模拟（Projectile）。
        
    - 伤害计算（命中部位、护甲穿透、暴击）。
        
    - 使用
        

**GAS（Gameplay Ability System）** 管理技能和状态（如换弹、武器过热）。

- 弹药与换弹系统
    
    - 弹匣管理（当前弹药、备弹量）。
        
    - 换弹动作打断与动画同步。
        

8. 敌人与 AI 系统
    

- 行为树（Behavior Tree）
    
    - 巡逻、追击、攻击、寻找掩体等逻辑。
        
    - 使用
        

**Blackboard** 存储目标位置、玩家状态等数据。

- 感知系统（Perception System）
    
    - AI 通过
        

**AIPerceptionComponent** 检测玩家（视觉、听觉）。

- 实现视野锥（Field of View）和听力范围。
    
- 敌人状态与战斗逻辑
    
- 血量管理、受击反馈（Hit Reaction）。
    
- 弱点系统（如爆头伤害加成）。
    

---

### 二、技术支撑系统

9. 1. 网络与多人游戏（Multiplayer）
    

- 网络同步（Replication）

- 角色移动、射击、状态同步（使用

Replicated 变量和 RPC）。

- 预测与插值（Prediction & Interpolation）减少延迟影响。

- 游戏模式（Game Mode）

- 匹配机制（Matchmaking）、队伍分配、胜负判定。

- 同步游戏状态（倒计时、比分）。

2. UI 与交互系统

- HUD（Heads-Up Display）

- 准星、弹药量、血量、小地图。

- 使用

UMG 设计动态 UI（如受击屏幕泛红）。

- 菜单系统

- 暂停菜单、设置界面、背包系统。

- 支持手柄/键鼠输入切换。

3. 物理与特效

- 物理交互

- 可破坏环境（Chaos Destruction）、布娃娃系统（Ragdoll）。

- 子弹碰撞反馈（火花、弹孔 Decals）。

- 视觉与音效

- 使用

Niagara 实现枪口火焰、爆炸特效。

- 空间化音效（3D Sound）与脚步声混合。

4. 存档与数据管理

- 玩家进度存档

- 保存武器解锁、成就、关卡进度（使用

SaveGame 类）。

- 加密存档防止篡改。

- 数据驱动设计

- 使用

Data Tables 配置武器属性、敌人数据。

- Curve Tables

管理伤害衰减、后坐力曲线。

---

### 三、扩展系统1. 技能与装备系统

  - 角色技能（Abilities）

  - 使用

   GAS（Gameplay Ability System） 实现翻滚、冲刺、战术技能。

  - 技能冷却（Cooldown）与资源消耗（如能量值）。

  - 装备与改装

  - 武器配件（瞄准镜、消音器）动态附加。

  - 属性加成（通过

   GameplayEffect 实现）。

  2. 关卡与任务系统

  - 关卡设计（Level Design）

  - 动态关卡加载（Level Streaming）。

  - 触发器（Trigger Volumes）控制事件（如伏击、爆炸）。

  - 任务与目标系统

  - 主线/支线任务管理（Quest Log）。

  - 目标标记与导航（使用

   Navigation Mesh）。

  3. 反作弊与安全

  - 服务器权威（Server Authority）

  - 关键逻辑（如伤害计算）在服务器端执行。

  - 防止客户端篡改（如无限弹药、穿墙）。

  - 行为检测

  - 异常数据检测（如移动速度过快、射击频率异常）。

---

### 四、推荐 UE 工具与插件

1. GAS（Gameplay Ability System）
    

：管理技能、状态和战斗逻辑。

2. ALS（Advanced Locomotion System）
    

：社区开源的高质量角色移动解决方案。

3. AIModule
    

：内置 AI 行为树和导航系统。

4. Online Subsystem
    

：集成 Steam、Epic 等平台的多人服务。

5. Niagara
    

：高性能粒子特效系统。

---

### 五、开发流程建议

1. 原型阶段
    

：优先实现核心玩法（移动、射击、基础 AI）。

2. 迭代阶段
    

：逐步加入武器系统、UI、多人联机。

3. 优化阶段
    

：性能分析（使用 **Unreal Insights**）、网络同步优化。

4. 测试阶段
    

：QA 测试、反作弊验证、用户体验调整。

通过以上架构，可以构建一个功能完整的 TPS 游戏，后续根据需求扩展更多玩法（如 PvE 副本、大逃杀模式）。

---

---

好的！以下是一份详细的 **TPS 游戏开发计划**，包含 **阶段划分**、**核心任务**、**时间预估** 和 **工具推荐**，适合列入项目管理和开发日程。基于 **Unreal Engine 5**，假设团队规模为 **3-5 人**，开发周期约 **6-12 个月**（根据功能复杂度调整）。

---

### 一、开发阶段划分

将项目分为 **5 个主要阶段**，每阶段包含关键任务和交付成果：

|   |   |   |
|---|---|---|
|阶段|时间预估|核心目标|
|1. 预生产阶段|2-4 周|明确核心玩法、技术验证、制定开发规范|
|2. 原型阶段|4-8 周|实现可玩的核心玩法原型（移动、射击、基础 AI）|
|3. 生产阶段|12-24 周|开发完整系统（武器、关卡、多人联机、UI）|
|4. 优化阶段|4-8 周|性能优化、Bug 修复、内容打磨|
|5. 发布阶段|2-4 周|测试、本地化、平台适配（Steam/Epic/主机）|

---

### 二、详细开发计划

#### 阶段 1：预生产阶段（Pre-Production）

目标：明确游戏方向，验证技术可行性，制定开发规范。

1.核心玩法设计

- 确定游戏核心循环（如 PvP 对战、PvE 副本、战役模式）。

- 设计武器类型（步枪、手枪、狙击枪）、角色技能（翻滚、战术装备）。

- 定义关键机制（掩体系统、弹道物理、伤害计算规则）。

2.技术验证

- UE5 功能验证

：

- Lumen/Nanite 性能测试（是否支持目标平台）。

- GAS（Gameplay Ability System）实现技能和状态机。

- 第三方插件测试

：

- 使用

ALS（Advanced Locomotion System） 快速搭建角色移动。

- 测试

AIModule 或 Behavior Tree 实现基础 AI。

3.美术风格与资源规划

- 确定艺术风格（写实/卡通/科幻）。

- 制定角色、武器、场景的建模/动画规范（如 Polygon 面数、纹理分辨率）。

4.文档与工具准备

- 编写

Game Design Document (GDD) 和 Technical Design Document (TDD)。

- 配置版本控制（Git + Git LFS 或 Perforce）。

- 搭建项目管理工具（Jira/Trello + Confluence）。

--------------------------------------------------------------------------------

阶段 2：原型阶段（Prototype）

目标：实现可运行的玩法原型，验证核心机制。

1.角色控制原型

- 实现基础移动（走、跑、蹲、跳跃）和摄像机控制（第三人称 + ADS）。

- 集成

ALS 或自定义 Animation Blueprint。

- 输出成果：可控制角色在场景中移动和瞄准。

2.武器与射击原型

- 创建基础武器（步枪），实现射线检测射击和弹道反馈。

- 简单伤害系统（血量、护甲、爆头机制）。

- 输出成果：角色可射击并造成伤害，显示弹药量。

3.AI 原型

- 使用

Behavior Tree 实现敌人巡逻、追击、攻击逻辑。

- 基础感知系统（检测玩家位置）。

- 输出成果：AI 能发现玩家并发动攻击。

4.快速迭代与测试

- 每周进行团队内测，收集反馈调整手感（如后坐力、移动速度）。

- 使用

UE5 的蓝图快速原型 功能，避免过早深入代码。

--------------------------------------------------------------------------------

阶段 3：生产阶段（Production）

目标：开发完整游戏系统，填充内容。

1.角色与武器系统

- 角色系统

：

- 技能系统（GAS 实现翻滚、冲刺、战术装备）。

- 角色属性（血量、护甲、移动速度）通过

Data Table 配置。

- 武器系统

：

- 武器库扩展（狙击枪、霰弹枪、近战武器）。

- 武器配件（瞄准镜、消音器）动态附加。

- 弹药管理系统（换弹动画、备弹量）。

2.关卡与任务设计

- 关卡搭建

：

- 使用

Quixel Megascans 快速创建场景。

- 设计掩体、高低差地形、动态事件（爆炸、伏击）。

- 任务系统

：

- 主线/支线任务逻辑（使用

Quest System 插件 或自定义蓝图）。

- 目标标记与导航（Navigation Mesh）。

3.多人联机系统

- 网络同步

：

- 角色移动、射击、状态同步（

Replicated 变量 + RPC）。

- 伤害计算在服务器端执行（防作弊）。

- 游戏模式

：

- 团队死斗（TDM）、占领据点（Domination）等模式。

- 匹配系统（通过

Online Subsystem 集成 Steam/Epic 服务）。

4.UI 与音效

- HUD

：准星动态扩散、受击提示、小地图（使用 UMG）。

- 菜单系统

：设置界面、背包系统、技能树。

- 音效

：空间化枪声、脚步声、角色语音（使用 MetaSounds）。

5.内容填充

- 制作 3-5 个关卡，设计敌人类型（普通士兵、精英怪、Boss）。

- 配置武器平衡（伤害、射速、后坐力曲线）。

--------------------------------------------------------------------------------

阶段 4：优化阶段（Polishing）

目标：提升性能与用户体验。

1.性能优化

- 使用

Unreal Insights 分析帧率瓶颈。

- 优化材质 LOD、粒子特效（Niagara）、动画压缩。

- 测试多平台性能（PC/主机/低配设备）。

2.Bug 修复

- 高频测试（每日构建 + 自动化测试）。

- 修复同步问题（如角色位置抖动、技能延迟）。

3.内容打磨

- 调整武器手感（后坐力反馈、换弹动画）。

- 优化 AI 行为（更智能的掩体利用、包抄策略）。

--------------------------------------------------------------------------------

阶段 5：发布阶段（Release）

目标：准备上线，覆盖多平台。

1.测试与本地化

- 封闭测试（Closed Beta）收集玩家反馈。

- 支持多语言（中文、英文、日文字幕）。

2.平台适配

- Steam/Epic 商店页面上线（截图、视频、描述）。

- 主机平台适配（PS5/Xbox Series X 性能优化）。

3.发布后维护

- 监控服务器稳定性（防 DDoS）。

1. - 定期更新内容（新武器、地图、活动）。
    

---

### 三、关键开发工具推荐

1. 1.代码管理
    

：Git + Git LFS（大文件支持）。

2.项目管理

：Jira（任务跟踪） + Confluence（文档）。

3.美术资源

：Blender（建模）、Substance Painter（贴图）。

4.UE5 插件

：

- ALS（Advanced Locomotion System）

：角色移动。

- GAS Companion

：简化 Gameplay Ability System 配置。

- AITree

：AI 行为树扩展。

---

### 四、风险管理

1.技术风险

：

- 提前验证网络同步和物理交互（如载具系统）。

- 使用 UE5 官方样例项目（如 Lyra）学习最佳实践。

2.内容风险

：

- 优先开发核心玩法，避免过早陷入美术资源制作。

3.时间风险

：

- 采用敏捷开发（2 周一个 Sprint），定期调整优先级。

---

### 五、总结

将以上计划拆解为 **具体任务卡片**，分配给团队成员，并通过每日站会同步进度。例如：

|   |   |   |   |
|---|---|---|---|
|任务|负责人|预计工时|优先级|
|实现基础武器射击逻辑|程序|20h|P0|
|设计第一关卡白模|策划/美术|40h|P1|
|配置 ALS 角色动画|动画师|30h|P0|

通过分阶段、模块化的开发，逐步构建完整的 TPS 游戏！

---

graph TD

A[原始输入] --> B[InputComponent]

B --> C{输入分类}

C -->|战斗输入| D[角色技能系统]

C -->|UI输入| E[UMG控件系统]

C -->|全局输入| F[暂停/截图等]

# 关卡切换相关

  

总的来说使用世界分区|关卡流送

  

在虚幻引擎5（UE5）中，关卡转换的实现方式多样，开发者可以根据项目需求选择不同的技术方案。以下是主要的实现方法及其应用场景：

  

---

  

### **1. 基础关卡切换（非无缝切换）**

通过直接加载新关卡实现，但会重置当前关卡的所有对象，适用于简单场景切换。

- **实现步骤**：
    
    - **触发器盒子（Trigger Volume）**：在场景中放置触发器盒子，绑定 `BeginOverlap` 事件，当玩家进入时触发关卡切换。
        
    - **蓝图节点** `**OpenLevel**`：在蓝图中使用 `UGameplayStatics::OpenLevel` 节点加载新关卡。
        
    - **数据重置**：所有Actor会被销毁，需通过 `GameInstance` 手动保存关键数据（如角色装备）。
        
- **优点**：实现简单，适合小型项目或原型开发。
    
- **缺点**：切换时有明显卡顿，无法保留数据。
    

  

---

  

### **2. 无缝切换（Seamless Travel）**

通过保留部分Actor实现平滑过渡，适合需要携带数据（如玩家状态）的场景。

- **实现步骤**：
    
    - **启用无缝漫游**：在 `GameMode` 中设置 `bUseSeamlessTravel = true`。
        
    - **保留Actor**：重写 `AGameMode::GetSeamlessTravelActorList` 函数，指定需要保留的Actor（如玩家控制器、角色数据）。
        
    - **使用** `**ServerTravel**`：通过 `UWorld::ServerTravel` 切换关卡（需在C++中暴露给蓝图）。
        
- **优点**：无卡顿，支持数据持久化。
    
- **缺点**：实现复杂，需处理Actor的跨关卡同步。
    

  

---

  

### **3. 关卡流送（Level Streaming）**

动态加载或卸载子关卡，适用于开放世界或大型场景。

- **实现步骤**：
    
    - **创建子关卡**：将场景分割为多个子关卡，添加到持久关卡中。
        
    - **流送体积（Streaming Volume）**：定义触发区域，当玩家进入时加载关联的子关卡。
        
    - **蓝图控制**：通过 `LoadStreamLevel` 和 `UnloadStreamLevel` 节点动态管理关卡资源。
        
- **优点**：优化内存占用，支持无缝探索。
    
- **缺点**：需合理规划关卡分割，调试复杂度较高。
    

  

---

  

### **4. 子关卡（Sublevels）与地理定位**

结合地理数据（如Cesium插件）实现基于位置的关卡切换，适用于地球规模或真实地理场景。

- **实现步骤**：
    
    - **创建地理标记（GeoMarker）**：使用 `CesiumGlobeAnchor` 组件绑定经纬度坐标。
        
    - **UI交互触发**：通过按钮点击事件切换关联的子关卡。
        
    - **持久关卡管理**：确保核心逻辑（如UI、玩家状态）保留在持久关卡中。
        
- **优点**：精准定位与动态加载结合，适合地图类应用。
    
- **缺点**：依赖插件，需处理地理坐标对齐。
    

  

---

  

### **5. 数据持久化与GameInstance**

跨关卡传递数据的关键方案，通过 `GameInstance` 类保存全局状态。

- **实现步骤**：
    
    - **自定义GameInstance**：继承 `UGameInstance`，定义需保存的变量（如角色属性、装备）。
        
    - **项目设置绑定**：在项目设置中指定自定义的 `GameInstance` 类。
        
    - **数据读写**：在关卡切换前后通过 `GetGameInstance()` 访问和更新数据。
        
- **优点**：简单高效，适合中小型项目。
    
- **缺点**：不适用于高频数据更新。
    

  

---

  

### **6. 混合方案与最佳实践**

- **开放世界**：优先使用 **关卡流送** 动态加载地形和场景片段。
    
- **剧情驱动游戏**：结合 **无缝切换** 和 **GameInstance** 保留玩家进度。
    
- **多人在线游戏**：通过 `ServerTravel` 和 `Seamless Travel` 同步服务器与客户端状态。
    

  

---

  

### **总结**

UE5的关卡转换实现方式多样，核心选择依据包括：

1. **项目规模**：小型项目可用基础切换，大型项目需流送或无缝切换。
    
2. **数据需求**：需保留数据时选择 `GameInstance` 或无缝切换。
    
3. **性能优化**：流送技术适合开放世界，避免内存过载。
    

  

开发者可参考官方文档和示例（如搜索结果中的蓝图代码和C++实现），结合具体需求选择最适配的方案。

