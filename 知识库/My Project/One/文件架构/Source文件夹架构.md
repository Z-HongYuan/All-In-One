# Source 文件夹架构

使用 `Lyra` 的文件夹架构, 不分 `public` 和 `private` 文件夹, 直接把 `.h` 文件和 `.cpp` 文件放在一起

- `Common` (通用的 `Cpp` 文件)
	- `AbilitySystem`
		- `Ability`
		- ~~`AbilityTask`~~
		- `Attribute`
	- `Actor`
	- `Animation`
	- `Character`
		- `Player`
		- `Enemy`
	- `Gameplay`
		- `Component`
		- `Controller`
		- `GameMode`
		- `GameState`
		- `PlayerState`
	- `InputSystem`
		- `DataAsset`
	- `UI`
		- `HUD`
- `Subsystem`
- `Misc`(杂项)
	- `CheatManager`
	- `FunctionLibrary`

- `Plugins`
	- `GameFeature`
		- `TPSFeature`

使用 `Game Feature` 实现游戏模块开发, `Common` 文件夹内只存在 `One` 项目的基类
