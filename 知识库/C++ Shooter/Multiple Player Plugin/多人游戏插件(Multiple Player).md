# 多人游戏概念

多人游戏本质比单人游戏更复杂

## 多人游戏|单人游戏的区别

**单人游戏**是由在一台计算机上运行的单个游戏会话组成

虽然可以在同一台计算机上使用多个设备的输入 (将游戏设置分屏) **但是最终还是不需要互联网的加入, 将信息传输到另一台机器上运行的游戏实例 (本地多人游戏)**

**多人游戏**是由网络参与的, 至少有两个及以上的游戏实例在单独的计算机上运行.

多个玩家都在提供输入以改变游戏中的东西 (例如控制 Pawn)

都需要发送更改的信息到游戏的其他实例

## 多人游戏中的网络架构模式

### P2P 模式 (Peer To Peer)

传输信息的最简单模式是通过对等连接, 将例如移动信息等数据之间在玩家计算机之间传递

**缺点:**

1. **是在玩家数量上涨的时候, 需要连接的计算机通道和传输数据为指数级上升**
    
2. 没有游戏的权威版本, 在网络传输阶段, 本地游戏实例都会延后 (每个游戏实例都不同)

### C/S 模式 (Client/Server)

在客户端/服务器模式中, 有一个计算机被指定为服务器, 其他所有计算机被指定为客户端

**每个客户端只与服务器通信**, 不会与另外玩家的实例进行通讯

Sever 一般被认为是 Authority(权威的), 意味着, Server 总是正确的版本

客户端只是把例如移动信息等发送到服务器中, 服务器检测通过后才会应用到服务器实例, 然后才会发送到其他客户端 (复制)

**在 UE 中使用权威的 C/S 模式:**

1. Listen Server(监听服务器)

	其中一台玩家计算机当做服务器, 服务器版本在玩家机器上运行, 并且有图形学渲染到屏幕

玩家机器上有优势 (例如无延迟等)

2. Dedicated Server(专用服务器)

	一台独立机器被指定为游戏的服务器, 没有玩家拥有, 没有图形学渲染, 只有逻辑, 模拟游戏的权威版本

在编辑器中使用运行旁边三个小点的下拉菜单中寻找运行时的服务器架构

网络模式中选取使用专用服务器, 监听服务器, 无服务器

客户端数量大于二的情况下会运行游戏的两个实例。

# 在 UE 编辑器中测试多人游戏

## 编辑器运行选项

## Stand alone(独立)

独立实例, 无任何网络连接。游戏实例将充当服务器和客户端。

## Listen Sever

根据玩家数量开启额外实例

编辑器将同时充当服务器和客户端。

## As Client

编辑器将充当客户端，然后在幕后开启服务器并进行链接

将在后台创建一个专用服务器, 使得编辑器的客户端可以链接

## 建立局域网

Local Area Network

通过 `OpenLevel` 节点, 打开一个关卡, 并且在选项中设置为 `Listen`, 这将会使打开的关卡作为 `Listen Sever` 进行运行.

使用控制台节点输入 `Open xxx.xxx.xxx.xxx` 的控制台命令用于加入到其他玩家的游戏实例中

## 在 C++ 中打开关卡

使用**蓝图可调用**的函数, 创建如下命名

```c++
UWorld* World = GetWorld();
if (World)
{
    World->ServerTravel("/Game/ThirdPersonCPP/Maps/Lobby?listen");
    //其中/Game/ThirdPersonCPP/Maps/Lobby为关卡的路径, 只要是在Content文件夹下的, 都可以使用Game文件夹代替
    // ?listen则是作为listen服务器打开关卡
}
```

打开关卡:

**`UGameplayStatics:OpenLevel`**使用游戏静态函数 OpenLevel

**`APlayerController:ClientTravel`** 客户端转换关卡, **可以转换到关卡, IP 地址

# 在线子系统

## 游戏如何连接

虚幻在线子系统, 管理游戏会话, 连接各地玩家

使用虚幻自己的功能用于连接到服务器

相当于项目需要连接到多个服务器的时候, 例如 Steam, Xbox, 无需为对应的服务器编写对应的代码, 只需要为虚幻引擎编写一次代码即可

使用 Steam 链接多人游戏, 并把在线子系统独立为一个插件

## Online Subsystem(在线子系统)

处理链接到特定平台的接口

需要再 `Engine.ini` 指定默认的平台服务

`OnlineSubsystem` 是 `IOnlineSubsystem` 的子类, 使用静态函数获取

## Session Interface(会话接口)

- 管理和销毁游戏会话
    
- 会话搜索
    
- 会话匹配
    
- ……

会话可以被认为是在服务器上运行的游戏实例, 并且会被广播

这样玩家就能找到会话并加入

### **游戏会话的典型生命周期:**

1. Create Session(创建会话)
    
2. Wait Player join(等待玩家加入)
    
3. Register Players(在游戏会话中注册玩家)
    
4. Start Session(开始会话)
    
5. Play
    
6. End Session(结束会话)
    
7. Unregister Players(在游戏会话中注销玩家)
    
8. Update Session or Destroy Session(更新或者销毁会话)

### 会话接口的基本函数

1. `Create Session()` 创建会话
    
2. `Find Sessions()` 寻找会话
    
3. `Join Session()` 加入会话
    
4. `Start Session()` 开始会话
    
5. `Destroy Session()` 销毁会话

## 指定一个游戏计划用于多人连接的方式

![[Pasted image 20250425223012.png]]

![[Pasted image 20250425223025.png]]

# 配置 OnlineSubsystem

## [为Steam配置信息](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/online-subsystem-steam-interface-in-unreal-engine)

1. ## 在引擎构建模块中加入

**`"OnlineSubsystem", "OnlineSubsystemSteam"`** ***`//在线子系统, 用于联网`***

以便可以在编辑器和引擎中访问相应的模块

2. ## 配置 ini 文件

[为Steam配置信息](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/online-subsystem-steam-interface-in-unreal-engine)

### 步骤

1. 打开项目的 `DefaultEngine.ini` 文件，并添加以下设置：

```ini
[/Script/Engine.GameEngine]
+NetDriverDefinitions=DefName="GameNetDriver", DriverClassName="OnlineSubsystemSteam.SteamNetDriver", DriverClassNameFallback="OnlineSubsystemUtils.IpNetDriver")
```

1. **NetDriverDefinitions** 描述了可供 UE 使用的网络驱动器，并添加了以下属性：
    
    1. **DefName** 是该网络驱动器定义的唯一名称。
        
    2. **DriverClassName** 是主网络驱动器的类名称。
        
    3. **DriverClassNameFallBack** 是退却网络驱动器的类名（如果主网络驱动器类初始化失败）。
        
2. 为了告诉 UE 使用 Online Subsystem Steam，添加以下设置：

```ini
[OnlineSubsystem]
DefaultPlatformService=Steam
```

1. 现在，你已经告诉 UE，你希望应用程序使用 SSteam 在线子系统，接下来需要添加以下设置来配置 **OnlineSubsystemSteam** 模块：

```ini
[OnlineSubsystemSteam]
bEnabled=true
SteamDevAppId=480
```

1. 最后，需要为应用程序连接在网络驱动器中指定 Steam 类：

```ini
[/Script/OnlineSubsystemSteam.SteamNetDriver]
NetConnectionClassName="OnlineSubsystemSteam.SteamNetConnection"
```

之后，接下来的步骤取决于你的游戏使用的是 **会话（Sessions）** 还是 **大厅（Lobbies）**。

### 使用会话

如果你在游戏中使用会话，请在 `OnlineSubsystemSteam` 添加以下内容：

```ini
bInitServerOnClient=true
```

如果你使用的是大厅，则无需设置该值：

你需要启用 `bInitServerOnClient` 设置，以便用户能够创建和加入会话。如果不启用该设置，Steam 的在线子系统将无法成功初始化。创建会话（Create Session）蓝图节点和相应的 C++ 函数将无法工作，导致你无法加入会话。

### 最终结果

在本简短指南结束后，你的应用程序 `DefaultEngine.ini` 文件应类似于以下设置块。如果你想查看其他项目是如何设置的并使用在线子系统，请参考我们的样本项目库。

- **DefaultEngine.ini**

```ini
[/Script/Engine.GameEngine]
+NetDriverDefinitions=(DefName="GameNetDriver", DriverClassName="OnlineSubsystemSteam.SteamNetDriver", DriverClassNameFallback="OnlineSubsystemUtils.IpNetDriver")

[OnlineSubsystem]
DefaultPlatformService=Steam

[OnlineSubsystemSteam]
bEnabled=true
SteamDevAppId=480

; If using Sessions
; bInitServerOnClient=true

[/Script/OnlineSubsystemSteam.SteamNetDriver]
NetConnectionClassName="OnlineSubsystemSteam.SteamNetConnection"
```

# 测试 (访问) 在线子系统

使用 OnlineSubsystem 的办法

**`Online::GetSubsystem(GetWorld())`****这个将返回****`GetWorld()`****上下文的 OnlineSubsystem**

在 Get 后使用 `GetSessionInterface()` 函数获取 `Interface`

使用**`TWeakPtr<IOnlineSession> SessionInterface;`**保存 `IOnlineSession` 变量, 使用 `TWeakPtr`

**测试方式:**

使用 `Print` 打印当前 `OnlineSubsystem` 的 `Name`

**注意事项:**

在编辑器中无法测试当前命名, 因为无论选择那个网络模式启动, 都会连接到 UE 的 `NullOnlineSubsystem`(用于局域网链接的在线子系统)

# 创建一个会话

利用委托机制 Delegate

![[Pasted image 20250425223551.png]]

**前置要求: 了解委托的基础使用方法**

`SessionInterface` 有自己的委托, 用于获取 Steam 连接情况

我们则需要创建一个自己使用的委托添加进 `SessionInterface` 的委托列表, 使用回调函数获取连接信息

```c++
//获取会话接口并且保存
if (const IOnlineSubsystem* OnlineSubsystem = Online::GetSubsystem(GetWorld()))
{
    SessionInterface = OnlineSubsystem->GetSessionInterface();
}
//创建会话完成委托并且指定回调函数
CreateSessionCompleteDelegate = FOnCreateSessionCompleteDelegate::CreateUObject(this, &ThisClass::OnCreateSessionComplete);
```

**Tips:**

使用 TWeakPtr 需要使用 Pin 函数获取到指针, 详情请 AI

在 Pawn 中绑定的话, 使用初始化器来初始化委托绑定函数

**Tips:**

在 UE 中内置了不同类型的委托

可以使用类上存在的静态函数构造特定类型的委托

使用 `SessionInterface` 来添加委托

使用 `SessionInterface` 和 `SessionSettings` 创建会话

```c++
FOnlineSessionSettings SessionSettings;
SessionSettings.bIsLANMatch = false;
SessionSettings.NumPublicConnections = 4;
SessionSettings.bAllowJoinInProgress = true;
SessionSettings.bAllowJoinViaPresence = true;
SessionSettings.bShouldAdvertise = true;
SessionSettings.bUsesPresence = true;
SessionSettings.bUseLobbiesIfAvailable = true;  //Log报错, 这个必须跟bUsesPresence一致

const ULocalPlayer* LocalPlayer = GetWorld()->GetFirstLocalPlayerFromController();
//在编辑器内, LocalPlayer为空会导致崩溃
SessionInterface.Pin()->CreateSession(*LocalPlayer->GetPreferredUniqueNetId(), NAME_GameSession, SessionSettings);
```

```c++
FOnlineSessionSettings()    //这是会话设置的选项
    : NumPublicConnections(0)
    , NumPrivateConnections(0)
    , bShouldAdvertise(false)
    , bAllowJoinInProgress(false)
    , bIsLANMatch(false)
    , bIsDedicated(false)
    , bUsesStats(false)
    , bAllowInvites(false)
    , bUsesPresence(false)
    , bAllowJoinViaPresence(false)
    , bAllowJoinViaPresenceFriendsOnly(false)
    , bAntiCheatProtected(false)
    , bUseLobbiesIfAvailable(false)
    , bUseLobbiesVoiceChatIfAvailable(false)
    , BuildUniqueId(GetBuildUniqueId())
```

`TSharedPtr<FOnlineSessionSettings> SessionSettings =`

`MakeShareable(new FOnlineSessionSettings());`

使用共享指针指向使用构造器构造的 `FOnlineSessionSettings`

# 设置加入游戏会话

创建一个连接游戏会话函数

## 创建对应函数及委托

创建一个 Find(查找)Session 的委托和回调

*`FOnFindSessionsCompleteDelegate FindSessionsCompleteDelegate;`* *`//查找会话完成回调`*

_`// 查找会话完成的回调函数`
_ *`void OnFindSessionsComplete(bool bWasSuccessful);`*

*`//查找会话完成委托并且指定回调函数`*
*`FindSessionsCompleteDelegate=FOnFindSessionsCompleteDelegate::CreateUObject(this, &ThisClass::OnFindSessionsComplete);`*

**使用构造器来构造委托, 在 Class 的初始化函数内, 或者初始化构造器**

## 设置会话搜索设置

使用 `FOnlineSessionSearch` 设置会话搜索

```c++
//添加委托到会话接口
SessionInterface.Pin()->AddOnFindSessionsCompleteDelegate_Handle(FindSessionsCompleteDelegate);

SearchResult = MakeShareable(new FOnlineSessionSearch());   // 创建搜索结果对象
//FOnlineSessionSearch SearchResult;
SearchResult->MaxSearchResults = 10000; //最大搜索结果, 因为是480端口, 所以需要更多的搜索结果才能找到自己的会话
SearchResult->bIsLanQuery = false;

const ULocalPlayer* LocalPlayer = GetWorld()->GetFirstLocalPlayerFromController();
SessionInterface.Pin()->FindSessions(*LocalPlayer->GetPreferredUniqueNetId(), SearchResult);
```

在回调函数中, 遍历 *`SearchResult`* 数组用于获取 Username, SessionID 等信息

需要解决 `SearchResult` 的变量问题

因为 `SearchResult` 保存着通过会话接口查询有哪些会话的结果

类似于 [创建一个会话](https://jsgqfsm5h1r9.sg.larksuite.com/wiki/WjuLwHwwviYuiKkHASklfB0Igmb) 的方式, 也是通过会话接口创建或查找会话

## 创建 Level

创建一个 Level 并将它设置为监听服务器, 所以网络上的玩家就能加入这个 Level

在 `Word` 里面使用 `SeverTravel()` 函数

如果在网络上创建了一个会话, 如果想要玩家加入, 则需要使用 Session.Set 函数, 用于设置会话的 Key/Value 键值对, 以方便查找

为了使用 `SeverTravel()` 函数, 需要获取匹配会话的 Address/IP 地址

# 创建多人游戏插件

## 使用 UE 创建一个插件

在插件的.cs 构建文件和.UProject 文件中添加需要的 OnlineSubsystem 模块

```json
"Plugins": [
    {
       "Name": "OnlineSubsystem",
       "Enabled": true
    },
    {
       "Name": "OnlineSubsystemSteam",
       "Enabled": true
    }
]
```

在插件中创建游戏实例子系统

在游戏实例子系统内, 获取游戏会话接口的变量, 以供接下来使用

其中 `TWeakPtr<IOnlineSession> SessionInterface` 是 变量

```c++
if (const IOnlineSubsystem* OnlineSubsystem = Online::GetSubsystem(GetWorld()))
{
    SessionInterface = OnlineSubsystem->GetSessionInterface();
}
```

## 添加委托和回调函数

```c++
//.cpp的初始化函数中
CreateSessionCompleteDelegate = FOnCreateSessionCompleteDelegate::CreateUObject(this, &ThisClass::OnCreateSessionComplete);
FindSessionsCompleteDelegate = FOnFindSessionsCompleteDelegate::CreateUObject(this, &ThisClass::OnFindSessionsComplete);
JoinSessionCompleteDelegate = FOnJoinSessionCompleteDelegate::CreateUObject(this, &ThisClass::OnJoinSessionComplete);
DestroySessionCompleteDelegate = FOnDestroySessionCompleteDelegate::CreateUObject(this, &ThisClass::OnDestroySessionComplete);
StartSessionCompleteDelegate = FOnStartSessionCompleteDelegate::CreateUObject(this, &ThisClass::OnStartSessionComplete);

//.h的私有列表中
/*
 * 用于添加到OnlineSubsystem的委托列表中
 * 委托列表
 */
FOnCreateSessionCompleteDelegate CreateSessionCompleteDelegate; //创建会话
FDelegateHandle CreateSessionCompleteDelegateHandle;
FOnFindSessionsCompleteDelegate FindSessionsCompleteDelegate;   //查找会话
FDelegateHandle FindSessionsCompleteDelegateHandle;
FOnJoinSessionCompleteDelegate JoinSessionCompleteDelegate; //加入会话
FDelegateHandle JoinSessionCompleteDelegateHandle;
FOnDestroySessionCompleteDelegate DestroySessionCompleteDelegate;   //销毁会话
FDelegateHandle DestroySessionCompleteDelegateHandle;
FOnStartSessionCompleteDelegate StartSessionCompleteDelegate;   //启动会话
FDelegateHandle StartSessionCompleteDelegateHandle;
/*
 * 委托的回调函数
 * 仅用于OnlineSubsystem的回调函数, 不允许在其他类中使用
 */
void OnCreateSessionComplete(FName SessionName, bool bWasSuccessful);
void OnFindSessionsComplete(bool bWasSuccessful);
void OnJoinSessionComplete(FName SessionName, EOnJoinSessionCompleteResult::Type Result);
void OnDestroySessionComplete(FName SessionName, bool bWasSuccessful);
void OnStartSessionComplete(FName SessionName, bool bWasSuccessful);
```

记得在 End 的时候通过委托句柄清除委托

```c++
if (SessionInterface.IsValid())
{
    SessionInterface.Pin()->ClearOnCreateSessionCompleteDelegate_Handle(CreateSessionCompleteDelegateHandle);
    SessionInterface.Pin()->ClearOnFindSessionsCompleteDelegate_Handle(FindSessionsCompleteDelegateHandle);
    SessionInterface.Pin()->ClearOnJoinSessionCompleteDelegate_Handle(JoinSessionCompleteDelegateHandle);
    SessionInterface.Pin()->ClearOnDestroySessionCompleteDelegate_Handle(DestroySessionCompleteDelegateHandle);
    SessionInterface.Pin()->ClearOnStartSessionCompleteDelegate_Handle(StartSessionCompleteDelegateHandle);
    SessionInterface.Reset();
}
```

在具体的项目需求中, 设置对应的回调函数, 或者委托

## 为插件添加用户界面

这里需要注意, 使用的用户界面父类, 可选择 `CommonUI` , 也可选基本的 `UserWidget`

使用控件中的事件, 连接到多人游戏插件用于关卡转换
