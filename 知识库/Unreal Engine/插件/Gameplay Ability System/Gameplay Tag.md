GameplayTag(游戏标签)

一种用于标记, 快速查询, 便于复制的层级标签

# 在编辑器内添加 GameplayTag

在项目设置的 GameplayTag 栏目添加设置 Tag

也可在 ini 文件中自定义 Tag

# 在 Cpp 中创建 GameplayTag

1. 创建空 Cpp 类
    
2. 在空 Cpp 类里面创建所需 Tag
    
3. 注册到资产管理器内 (AssetManager)(需要自己自定义, 全局唯一单例)

---

GameplayTags 类

```c++
// Copyright © 2025 鸿源z 保留所有权利

#pragma once

#include "CoreMinimal.h"
#include "GameplayTagContainer.h"

/**
 * GenshinGameplayTags
 * 单例, 全局唯一
 * 在Cpp中的所有游戏标签都应该在这里注册
 * 包含原生GameplayTags
 */
//GenshinGameplayTags.h
struct FGenshinGameplayTags
{
public:
    static const FGenshinGameplayTags& Get() {return GameplayTags;}
    static void InitializeNativeGameplayTags();

    //添加Tag
    FGameplayTag Tag_GameplayAbility_ActivateFail_IsDead;
    
protected:
    
private:
    static FGenshinGameplayTags GameplayTags;
};
```

```c++
// Copyright © 2025 鸿源z 保留所有权利


#include "GenshinGameplayTags.h"
#include "GameplayTagsManager.h"

//GenshinGameplayTags.cpp

FGenshinGameplayTags FGenshinGameplayTags::GameplayTags;

void FGenshinGameplayTags::InitializeNativeGameplayTags()
{
    //保存一个变量, 可以在Cpp中引用的添加
    GameplayTags.Tag_GameplayAbility_ActivateFail_IsDead = UGameplayTagsManager::Get().AddNativeGameplayTag(
       FName("GameplayAbility.ActivateFail.IsDead"),
       TEXT("死亡")
       );

    //Cpp引用不了的添加
    UGameplayTagsManager::Get().AddNativeGameplayTag(FName("Camera.NeedCamera"), TEXT("需要摄像机"));
}
```

---

资产管理器 AssetManager

```c++
// Copyright © 2025 鸿源z 保留所有权利

#pragma once

#include "CoreMinimal.h"
#include "Engine/AssetManager.h"
#include "GenshinAssetManager.generated.h"

/**
 * 资产管理器
 * 单例, 全局唯一
 * 
 */
UCLASS()
class GENSHINELEMENTALWAR_API UGenshinAssetManager : public UAssetManager
{
    GENERATED_BODY()
    
public:
    static UGenshinAssetManager& Get();

protected:

    //注册资产, Tag资产
    virtual void StartInitialLoading() override;
};
```

```c++
// Copyright © 2025 鸿源z 保留所有权利


#include "GenshinAssetManager.h"

#include "GenshinGameplayTags.h"

UGenshinAssetManager& UGenshinAssetManager::Get()
{
    check(GEngine);
    
    return *Cast<UGenshinAssetManager>(GEngine->AssetManager);
}

void UGenshinAssetManager::StartInitialLoading()
{
    Super::StartInitialLoading();
    
    FGenshinGameplayTags::InitializeNativeGameplayTags();
}
```
