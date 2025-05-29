# Content 文件夹架构

> 总结
> 蓝图资产用于具体设置, 比如路径设置或者是一些基础参数的设置
> 明确文件夹职责
> 在插件内引用 `One` 的基类, 尽量减少 `Common` 内引用插件的内容

- `Content`
	- `Art` (美术资源)
		- `Environments`
		- `UI`
			- `Fonts`
			- `Texture`
				- `DefaultCursor`
	
	- `CommonAsset`
		- `Character`
		- `Enemy`
		- `Input`
		- `UI`

- `Plugins`
	- `GameFeatures`
		- `TPSFeatures`
