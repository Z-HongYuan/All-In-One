# 使用 AWS 和 GameLift 的专用服务器

# 使用 Sever.target.cs

> ❗
> 1. 从 `Game` 的编译目标复制一份到同等级目录下, 重命名为 `类名Server.target.cs` 一般来说有两个需要重命名
> 2. 把 `Server.target.cs` 内的类名改为 `项目名称 +Server` 的形式
> 3. 然后切换编译目标到 `DevelopServer` 或者 `DebugGameServer` 然后编译

# 构建 GameLift Server 和插件

用于构建 `GameliftServer`, 在插件中

## 获取 OpenSSL
