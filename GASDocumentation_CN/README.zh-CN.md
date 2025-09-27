> **译者注：** 这是对原版 [README.md](../README.md) 的社区简体中文翻译，适用于 Unreal Engine 5.3。技术术语保持英文以确保准确性。

# GASDocumentation
这是我对 Unreal Engine 5 的 GameplayAbilitySystem 插件 (GAS) 的理解，包含一个简单的多人游戏示例项目。这不是官方文档，本项目和我本人都不隶属于 Epic Games。我不保证此信息的准确性。

本文档的目标是解释 GAS 中的主要概念和类，并基于我的经验提供一些额外的评论。GAS 在社区用户中有很多"部落知识"，我的目标是在这里分享我所有的知识。

示例项目和文档适用于 **Unreal Engine 5.3** (UE5)。虽然有针对旧版本 Unreal Engine 的文档分支，但它们不再受支持，可能包含错误或过时信息。请使用与您的引擎版本匹配的分支。

[GASShooter](https://github.com/tranek/GASShooter) 是一个姊妹示例项目，演示了 GAS 在多人 FPS/TPS 中的高级技术。

最好的文档永远是插件源代码。

<a name="table-of-contents"></a>
## Table of Contents

> 1. [Intro to the GameplayAbilitySystem Plugin](#intro)
> 1. [Sample Project](#sp)
> 1. [Setting Up a Project Using GAS](#setup)
> 1. [Concepts](#concepts)  
>    4.1 [Ability System Component](#concepts-asc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.1.1 [Replication Mode](#concepts-asc-rm)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.1.2 [Setup and Initialization](#concepts-asc-setup)  
>    4.2 [Gameplay Tags](#concepts-gt)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.2.1 [Responding to Changes in Gameplay Tags](#concepts-gt-change)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.2.2 [Loading Gameplay Tags from Plugin .ini Files](#concepts-gt-loadfromplugin)  
>    4.3 [Attributes](#concepts-a)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.1 [Attribute Definition](#concepts-a-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.2 [BaseValue vs CurrentValue](#concepts-a-value)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.3 [Meta Attributes](#concepts-a-meta)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.4 [Responding to Attribute Changes](#concepts-a-changes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.5 [Derived Attributes](#concepts-a-derived)  
>    4.4 [Attribute Set](#concepts-as)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.1 [Attribute Set Definition](#concepts-as-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2 [Attribute Set Design](#concepts-as-design)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.1 [Subcomponents with Individual Attributes](#concepts-as-design-subcomponents)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.2 [Adding and Removing AttributeSets at Runtime](#concepts-as-design-addremoveruntime)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3 [Item Attributes (Weapon Ammo)](#concepts-as-design-itemattributes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.1 [Plain Floats on the Item](#concepts-as-design-itemattributes-plainfloats)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.2 [`AttributeSet` on the Item](#concepts-as-design-itemattributes-attributeset)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.3 [`ASC` on the Item](#concepts-as-design-itemattributes-asc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.3 [Defining Attributes](#concepts-as-attributes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.4 [Initializing Attributes](#concepts-as-init)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.5 [PreAttributeChange()](#concepts-as-preattributechange)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.6 [PostGameplayEffectExecute()](#concepts-as-postgameplayeffectexecute)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.7 [OnAttributeAggregatorCreated()](#concepts-as-onattributeaggregatorcreated)  
>    4.5 [Gameplay Effects](#concepts-ge)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.1 [Gameplay Effect Definition](#concepts-ge-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.2 [Applying Gameplay Effects](#concepts-ge-applying)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.3 [Removing Gameplay Effects](#concepts-ga-removing)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4 [Gameplay Effect Modifiers](#concepts-ge-mods)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4.1 [Multiply and Divide Modifiers](#concepts-ge-mods-multiplydivide)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4.2 [Gameplay Tags on Modifiers](#concepts-ge-mods-gameplaytags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.5 [Stacking Gameplay Effects](#concepts-ge-stacking)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.6 [Granted Abilities](#concepts-ge-ga)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.7 [Gameplay Effect Tags](#concepts-ge-tags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.8 [Immunity](#concepts-ge-immunity)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.9 [Gameplay Effect Spec](#concepts-ge-spec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.9.1 [SetByCallers](#concepts-ge-spec-setbycaller)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.10 [Gameplay Effect Context](#concepts-ge-context)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.11 [Modifier Magnitude Calculation](#concepts-ge-mmc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12 [Gameplay Effect Execution Calculation](#concepts-ge-ec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1 [Sending Data to Execution Calculations](#concepts-ge-ec-senddata)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.1 [SetByCaller](#concepts-ge-ec-senddata-setbycaller)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.2 [Backing Data Attribute Calculation Modifier](#concepts-ge-ec-senddata-backingdataattribute)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.3 [Backing Data Temporary Variable Calculation Modifier](#concepts-ge-ec-senddata-backingdatatempvariable)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.4 [Gameplay Effect Context](#concepts-ge-ec-senddata-effectcontext)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.13 [Custom Application Requirement](#concepts-ge-car)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.14 [Cost Gameplay Effect](#concepts-ge-cost)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15 [Cooldown Gameplay Effect](#concepts-ge-cooldown)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.1 [Get the Cooldown Gameplay Effect's Remaining Time](#concepts-ge-cooldown-tr)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.2 [Listening for Cooldown Begin and End](#concepts-ge-cooldown-listen)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.3 [Predicting Cooldowns](#concepts-ge-cooldown-prediction)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.16 [Changing Active Gameplay Effect Duration](#concepts-ge-duration)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.17 [Creating Dynamic Gameplay Effects at Runtime](#concepts-ge-dynamic)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.18 [Gameplay Effect Containers](#concepts-ge-containers)  
>    4.6 [Gameplay Abilities](#concepts-ga)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1 [Gameplay Ability Definition](#concepts-ga-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.1 [Replication Policy](#concepts-ga-definition-reppolicy)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.2 [Server Respects Remote Ability Cancellation](#concepts-ga-definition-remotecancel)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.3 [Replicate Input Directly](#concepts-ga-definition-repinputdirectly)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.2 [Binding Input to the ASC](#concepts-ga-input)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.2.1 [Binding to Input without Activating Abilities](#concepts-ga-input-noactivate)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.3 [Granting Abilities](#concepts-ga-granting)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4 [Activating Abilities](#concepts-ga-activating)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4.1 [Passive Abilities](#concepts-ga-activating-passive)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4.2 [Activation Failed Tags](#concepts-ga-activating-failedtags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.5 [Canceling Abilities](#concepts-ga-cancelabilities)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.6 [Getting Active Abilities](#concepts-ga-definition-activeability)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.7 [Instancing Policy](#concepts-ga-instancing)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.8 [Net Execution Policy](#concepts-ga-net)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.9 [Ability Tags](#concepts-ga-tags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.10 [Gameplay Ability Spec](#concepts-ga-spec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.11 [Passing Data to Abilities](#concepts-ga-data)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.12 [Ability Cost and Cooldown](#concepts-ga-commit)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.13 [Leveling Up Abilities](#concepts-ga-leveling)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.14 [Ability Sets](#concepts-ga-sets)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.15 [Ability Batching](#concepts-ga-batching)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.16 [Net Security Policy](#concepts-ga-netsecuritypolicy)  
>    4.7 [Ability Tasks](#concepts-at)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.1 [Ability Task Definition](#concepts-at-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.2 [Custom Ability Tasks](#concepts-at-custom)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.3 [Using Ability Tasks](#concepts-at-using)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.4 [Root Motion Source Ability Tasks](#concepts-at-rms)  
>    4.8 [Gameplay Cues](#concepts-gc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.1 [Gameplay Cue Definition](#concepts-gc-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.2 [Triggering Gameplay Cues](#concepts-gc-trigger)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.3 [Local Gameplay Cues](#concepts-gc-local)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.4 [Gameplay Cue Parameters](#concepts-gc-parameters)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.5 [Gameplay Cue Manager](#concepts-gc-manager)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.6 [Prevent Gameplay Cues from Firing](#concepts-gc-prevention)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7 [Gameplay Cue Batching](#concepts-gc-batching)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7.1 [Manual RPC](#concepts-gc-batching-manualrpc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7.2 [Multiple GCs on one GE](#concepts-gc-batching-gcsonge)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.8 [Gameplay Cue Events](#concepts-gc-events)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.9 [Gameplay Cue Reliability](#concepts-gc-reliability)  
>    4.9 [Ability System Globals](#concepts-asg)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.9.1 [InitGlobalData()](#concepts-asg-initglobaldata)  
>    4.10 [Prediction](#concepts-p)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.1 [Prediction Key](#concepts-p-key)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.2 [Creating New Prediction Windows in Abilities](#concepts-p-windows)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.3 [Predictively Spawning Actors](#concepts-p-spawn)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.4 [Future of Prediction in GAS](#concepts-p-future)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.5 [Network Prediction Plugin](#concepts-p-npp)  
>    4.11 [Targeting](#concepts-targeting)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.1 [Target Data](#concepts-targeting-data)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.2 [Target Actors](#concepts-targeting-actors)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.3 [Target Data Filters](#concepts-target-data-filters)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.4 [Gameplay Ability World Reticles](#concepts-targeting-reticles)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.5 [Gameplay Effect Containers Targeting](#concepts-targeting-containers)  
> 1. [Commonly Implemented Abilities and Effects](#cae)  
>    5.1 [Stun](#cae-stun)  
>    5.2 [Sprint](#cae-sprint)  
>    5.3 [Aim Down Sights](#cae-ads)  
>    5.4 [Lifesteal](#cae-ls)  
>    5.5 [Generating a Random Number on Client and Server](#cae-random)  
>    5.6 [Critical Hits](#cae-crit)  
>    5.7 [Non-Stacking Gameplay Effects but Only the Greatest Magnitude Actually Affects the Target](#cae-nonstackingge)  
>    5.8 [Generate Target Data While Game is Paused](#cae-paused)  
>    5.9 [One Button Interaction System](#cae-onebuttoninteractionsystem)  
> 1. [Debugging GAS](#debugging)  
>    6.1 [showdebug abilitysystem](#debugging-sd)  
>    6.2 [Gameplay Debugger](#debugging-gd)  
>    6.3 [GAS Logging](#debugging-log)  
> 1. [Optimizations](#optimizations)  
>    7.1 [Ability Batching](#optimizations-abilitybatching)  
>    7.2 [Gameplay Cue Batching](#optimizations-gameplaycuebatching)  
>    7.3 [AbilitySystemComponent Replication Mode](#optimizations-ascreplicationmode)  
>    7.4 [Attribute Proxy Replication](#optimizations-attributeproxyreplication)  
>    7.5 [ASC Lazy Loading](#optimizations-asclazyloading)  
> 1. [Quality of Life Suggestions](#qol)  
>    8.1 [Gameplay Effect Containers](#qol-gameplayeffectcontainers)  
>    8.2 [Blueprint AsyncTasks to Bind to ASC Delegates](#qol-asynctasksascdelegates)  
> 1. [Troubleshooting](#troubleshooting)  
>    9.1 [LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!](#troubleshooting-notlocal)  
>    9.2 [ScriptStructCache errors](#troubleshooting-scriptstructcache)  
>    9.3 [Animation Montages are not replicating to clients](#troubleshooting-replicatinganimmontages)  
>    9.4 [Duplicating Blueprint Actors is setting AttributeSets to nullptr](#troubleshooting-duplicatingblueprintactors)  
>    9.5 [unresolved external symbol UEPushModelPrivate::MarkPropertyDirty(int,int)](#troubleshooting-unresolvedexternalsymbolmarkpropertydirty)  
>    9.6 [Enum names are now represented by path name](#troubleshooting-enumnamesarenowpathnames)  
> 1. [Common GAS Acronyms](#acronyms)
> 1. [Other Resources](#resources)  
>    11.1 [Q&A With Epic Game's Dave Ratti](#resources-daveratti)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;11.1.1 [Community Questions 1](#resources-daveratti-community1)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;11.1.2 [Community Questions 2](#resources-daveratti-community2)  
> 1. [GAS Changelog](#changelog)  
>    * [5.3](#changelog-5.3)  
>    * [5.2](#changelog-5.2)  
>    * [5.1](#changelog-5.1)  
>    * [5.0](#changelog-5.0)  
>    * [4.27](#changelog-4.27)  
>    * [4.26](#changelog-4.26)  
>    * [4.25.1](#changelog-4.25.1)  
>    * [4.25](#changelog-4.25)  
>    * [4.24](#changelog-4.24)
         
## 1. GameplayAbilitySystem 插件介绍
根据[官方文档](https://docs.unrealengine.com/en-US/Gameplay/GameplayAbilitySystem/index.html)：
>Gameplay Ability System 是一个高度灵活的框架，用于构建能力和属性，这些功能在 RPG 或 MOBA 游戏中很常见。您可以为游戏中的角色构建动作或被动能力，构建各种因这些动作而累积或消耗的状态效果，实现"冷却"计时器或资源消耗来调节这些动作的使用，改变能力的等级和每个等级的效果，激活粒子或音效等等。简单地说，这个系统可以帮助您设计、实现和高效地网络化游戏中的能力，无论是简单的跳跃还是您最喜欢的现代 RPG 或 MOBA 游戏中角色的复杂能力集。

GameplayAbilitySystem 插件由 Epic Games 开发，随 Unreal Engine 一起提供。它已在 Paragon 和 Fortnite 等 AAA 商业游戏中经受了实战考验。

该插件为单人和多人游戏提供了开箱即用的解决方案：
* 实施基于等级的角色能力或技能，可选择成本和冷却时间 ([GameplayAbilities](#concepts-ga))
* 操作属于 actor 的数值 `Attributes` ([Attributes](#concepts-a))
* 对 actor 应用状态效果 ([GameplayEffects](#concepts-ge))
* 对 actor 应用 `GameplayTags` ([GameplayTags](#concepts-gt))
* 生成视觉或音效 ([GameplayCues](#concepts-gc))
* 复制上述所有内容

在多人游戏中，GAS 为以下内容提供[客户端预测](#concepts-p)支持：
* 能力激活
* 播放动画蒙太奇
* `Attributes` 的更改
* 应用 `GameplayTags`
* 生成 `GameplayCues`
* 通过连接到 `CharacterMovementComponent` 的 `RootMotionSource` 函数进行移动。

**GAS 必须在 C++ 中设置**，但设计师可以在 Blueprint 中创建 `GameplayAbilities` 和 `GameplayEffects`。

GAS 当前存在的问题：
* `GameplayEffect` 延迟调节（无法预测能力冷却，导致高延迟玩家在低冷却能力上的开火率低于低延迟玩家）。
* 无法预测 `GameplayEffects` 的移除。但是，我们可以预测添加具有相反效果的 `GameplayEffects`，从而有效地移除它们。这并不总是合适或可行的，仍然是一个问题。
* 缺乏样板模板、多人示例和文档。希望这个项目能有所帮助！

**[⬆ 返回顶部](#table-of-contents)**

<a name="sp"></a>
## 2. 示例项目
本文档包含一个多人第三人称射击游戏示例项目，面向刚接触 GameplayAbilitySystem 插件但对 Unreal Engine 不陌生的人员。用户需要了解 C++、Blueprints、UMG、复制和 UE 中的其他中级主题。该项目提供了一个示例，展示如何设置一个基本的第三人称射击多人就绪项目，其中玩家/AI 控制的英雄在 `PlayerState` 类上使用 `AbilitySystemComponent` (`ASC`)，AI 控制的小兵在 `Character` 类上使用 `ASC`。

目标是保持项目简单，同时展示 GAS 基础知识并演示一些常见请求的能力，并提供良好的注释代码。由于其面向初学者的重点，该项目不展示高级主题，如[预测弹丸](#concepts-p-spawn)。

演示的概念：
* `PlayerState` 上的 `ASC` 与 `Character` 上的比较
* 复制的 `Attributes`
* 复制的动画蒙太奇
* `GameplayTags`
* 在 `GameplayAbilities` 内部和外部应用和移除 `GameplayEffects`
* 应用由护甲减轻的伤害来改变角色的生命值
* `GameplayEffectExecutionCalculations`
* 眩晕效果
* 死亡和重生
* 从服务器上的能力生成 actor（弹丸）
* 通过瞄准和冲刺预测性地更改本地玩家的速度
* 持续消耗体力来冲刺
* 使用法力施放能力
* 被动能力
* 堆叠 `GameplayEffects`
* 目标 actor
* 在 Blueprint 中创建的 `GameplayAbilities`
* 在 C++ 中创建的 `GameplayAbilities`
* 每个 `Actor` 实例化的 `GameplayAbilities`
* 非实例化的 `GameplayAbilities`（跳跃）
* 静态 `GameplayCues`（FireGun 弹丸撞击粒子效果）
* Actor `GameplayCues`（冲刺和眩晕粒子效果）

英雄类具有以下能力：

| 能力                       | 输入绑定            | 预测       | C++ / Blueprint | 描述                                                                                                                                                                         |
| -------------------------- | ------------------- | ---------- | --------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Jump                       | Space Bar           | Yes        | C++             | 让英雄跳跃。                                                                                                                                                         |
| Gun                        | Left Mouse Button   | No         | C++             | 从英雄的枪中发射弹丸。动画是预测的，但弹丸不是。                                                                                |
| Aim Down Sights            | Right Mouse Button  | Yes        | Blueprint       | 按住按钮时，英雄会走得更慢，相机会放大以便更精确地瞄准枪械。                                                    |
| Sprint                     | Left Shift          | Yes        | Blueprint       | 按住按钮时，英雄会跑得更快并消耗体力。                                                                                                         |
| Forward Dash               | Q                   | Yes        | Blueprint       | 英雄向前冲刺，消耗体力。                                                                                                                              |
| Passive Armor Stacks       | Passive             | No         | Blueprint       | 每 4 秒英雄获得一层护甲，最多 4 层。受到伤害会移除一层护甲。                                                    |
| Meteor                     | R                   | No         | Blueprint       | 玩家瞄准一个位置向敌人投下流星，造成伤害并眩晕他们。瞄准是预测的，但生成流星不是。                     |

无论 `GameplayAbilities` 是在 C++ 还是 Blueprint 中创建的都无关紧要。这里使用了两者的混合，作为如何在每种语言中执行它们的示例。

小兵没有预定义的 `GameplayAbilities`。红色小兵有更多的生命恢复，而蓝色小兵有更高的起始生命值。

对于 `GameplayAbility` 命名，我使用后缀 `_BP` 来表示 `GameplayAbility` 的逻辑是在 Blueprint 中创建的。没有后缀意味着逻辑是在 C++ 中创建的。

**Blueprint 资产命名前缀**

| 前缀        | 资产类型            |
| ----------- | ------------------- |
| GA_         | GameplayAbility     |
| GC_         | GameplayCue         |
| GE_         | GameplayEffect      |

**[⬆ 返回顶部](#table-of-contents)**

<a name="setup"></a>
## 3. 使用 GAS 设置项目
使用 GAS 设置项目的基本步骤：
1. 在编辑器中启用 GameplayAbilitySystem 插件
1. 编辑 `YourProjectName.Build.cs`，将 `"GameplayAbilities", "GameplayTags", "GameplayTasks"` 添加到您的 `PrivateDependencyModuleNames`
1. 刷新/重新生成您的 Visual Studio 项目文件