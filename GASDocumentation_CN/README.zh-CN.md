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
1. 从 4.24 到 5.2，必须调用 `UAbilitySystemGlobals::Get().InitGlobalData()` 来使用 [`TargetData`](#concepts-targeting-data)。示例项目在 `UAssetManager::StartInitialLoading()` 中执行此操作。从 5.3 开始会自动调用。有关更多信息，请参阅 [`InitGlobalData()`](#concepts-asg-initglobaldata)。

这就是启用 GAS 所需要做的一切。从这里开始，将 [`ASC`](#concepts-asc) 和 [`AttributeSet`](#concepts-as) 添加到您的 `Character` 或 `PlayerState`，并开始制作 [`GameplayAbilities`](#concepts-ga) 和 [`GameplayEffects`](#concepts-ge)！

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts"></a>
## 4. GAS 概念

#### 章节

> 4.1 [Ability System Component](#concepts-asc)  
> 4.2 [Gameplay Tags](#concepts-gt)  
> 4.3 [Attributes](#concepts-a)  
> 4.4 [Attribute Set](#concepts-as)  
> 4.5 [Gameplay Effects](#concepts-ge)  
> 4.6 [Gameplay Abilities](#concepts-ga)  
> 4.7 [Ability Tasks](#concepts-at)  
> 4.8 [Gameplay Cues](#concepts-gc)  
> 4.9 [Ability System Globals](#concepts-asg)  
> 4.10 [Prediction](#concepts-p)

<a name="concepts-asc"></a>
### 4.1 Ability System Component
`AbilitySystemComponent` (`ASC`) 是 GAS 的核心。它是一个 `UActorComponent`（[`UAbilitySystemComponent`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAbilitySystemComponent/index.html)），处理与系统的所有交互。任何希望使用 [`GameplayAbilities`](#concepts-ga)、拥有 [`Attributes`](#concepts-a) 或接收 [`GameplayEffects`](#concepts-ge) 的 `Actor` 都必须附加一个 `ASC`。这些对象都存在于 `ASC` 内部，并由 `ASC` 管理和复制（除了 `Attributes`，它们由它们的 [`AttributeSet`](#concepts-as) 复制）。开发者可以但不是必须子类化此组件。

附加了 `ASC` 的 `Actor` 被称为 `ASC` 的 `OwnerActor`。`ASC` 的物理表示 `Actor` 被称为 `AvatarActor`。`OwnerActor` 和 `AvatarActor` 可以是同一个 `Actor`，如 MOBA 游戏中的简单 AI 小兵。它们也可以是不同的 `Actor`，如 MOBA 游戏中玩家控制的英雄，其中 `OwnerActor` 是 `PlayerState`，`AvatarActor` 是英雄的 `Character` 类。大多数 `Actor` 会将 `ASC` 放在自身上。如果您的 `Actor` 会重生并需要在重生之间保持 `Attributes` 或 `GameplayEffects` 的持久性（如 MOBA 中的英雄），那么 `ASC` 的理想位置是在 `PlayerState` 上。

**注意：**如果您的 `ASC` 在您的 `PlayerState` 上，那么您需要增加 `PlayerState` 的 `NetUpdateFrequency`。它在 `PlayerState` 上默认为一个很低的值，可能会导致延迟或在客户端上感知到的 `Attributes` 和 `GameplayTags` 等内容的变化滞后。确保启用 [`Adaptive Network Update Frequency`](https://docs.unrealengine.com/en-US/Gameplay/Networking/Actors/Properties/index.html#adaptivenetworkupdatefrequency)，Fortnite 使用它。

`OwnerActor` 和 `AvatarActor`（如果是不同的 `Actor`）都应该实现 `IAbilitySystemInterface`。这个接口有一个必须重写的函数，`UAbilitySystemComponent* GetAbilitySystemComponent() const`，它返回指向其 `ASC` 的指针。`ASC` 在系统内部通过查找此接口函数来相互交互。

`ASC` 在 `FActiveGameplayEffectsContainer ActiveGameplayEffects` 中保存其当前活动的 `GameplayEffects`。

`ASC` 在 `FGameplayAbilitySpecContainer ActivatableAbilities` 中保存其授予的 `Gameplay Abilities`。任何时候您计划遍历 `ActivatableAbilities.Items`，确保在循环上方添加 `ABILITYLIST_SCOPE_LOCK();` 以锁定列表防止更改（由于移除能力）。作用域内的每个 `ABILITYLIST_SCOPE_LOCK();` 都会增加 `AbilityScopeLockCount`，然后在超出作用域时减少。不要尝试在 `ABILITYLIST_SCOPE_LOCK();` 的作用域内移除能力（清除能力函数在内部检查 `AbilityScopeLockCount` 以防止在列表被锁定时移除能力）。

<a name="concepts-asc-rm"></a>
### 4.1.1 Replication Mode
`ASC` 为复制 `GameplayEffects`、`GameplayTags` 和 `GameplayCues` 定义了三种不同的复制模式 - `Full`、`Mixed` 和 `Minimal`。`Attributes` 由它们的 `AttributeSet` 复制。

| Replication Mode   | 何时使用                                | 描述                                                                                                                    |
| ------------------ | --------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| `Full`             | 单人游戏                           | 每个 `GameplayEffect` 都复制到每个客户端。                                                                          |
| `Mixed`            | 多人游戏，玩家控制的 `Actors` | `GameplayEffects` 只复制到拥有的客户端。只有 `GameplayTags` 和 `GameplayCues` 复制到所有人。 |
| `Minimal`          | 多人游戏，AI 控制的 `Actors`     | `GameplayEffects` 从不复制到任何人。只有 `GameplayTags` 和 `GameplayCues` 复制到所有人。           |

**注意：**`Mixed` 复制模式期望 `OwnerActor` 的 `Owner` 是 `Controller`。`PlayerState` 的 `Owner` 默认是 `Controller`，但 `Character` 的不是。如果使用 `Mixed` 复制模式且 `OwnerActor` 不是 `PlayerState`，那么您需要在 `OwnerActor` 上使用有效的 `Controller` 调用 `SetOwner()`。

从 4.24 开始，`PossessedBy()` 现在将 `Pawn` 的所有者设置为新的 `Controller`。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-asc-setup"></a>
### 4.1.2 设置和初始化
`ASC` 通常在 `OwnerActor` 的构造函数中构造，并显式标记为复制。**这必须在 C++ 中完成**。

```c++
AGDPlayerState::AGDPlayerState()
{
	// Create ability system component, and set it to be explicitly replicated
	AbilitySystemComponent = CreateDefaultSubobject<UGDAbilitySystemComponent>(TEXT("AbilitySystemComponent"));
	AbilitySystemComponent->SetIsReplicated(true);
	//...
}
```

`ASC` 需要在服务器和客户端上使用其 `OwnerActor` 和 `AvatarActor` 进行初始化。您希望在 `Pawn` 的 `Controller` 已设置后（拥有后）进行初始化。单人游戏只需要关心服务器路径。

对于 `ASC` 位于 `Pawn` 上的玩家控制角色，我通常在服务器上的 `Pawn` 的 `PossessedBy()` 函数中初始化，在客户端上的 `PlayerController` 的 `AcknowledgePossession()` 函数中初始化。

```c++
void APACharacterBase::PossessedBy(AController * NewController)
{
	Super::PossessedBy(NewController);

	if (AbilitySystemComponent)
	{
		AbilitySystemComponent->InitAbilityActorInfo(this, this);
	}

	// ASC MixedMode replication requires that the ASC Owner's Owner be the Controller.
	SetOwner(NewController);
}
```

```c++
void APAPlayerControllerBase::AcknowledgePossession(APawn* P)
{
	Super::AcknowledgePossession(P);

	APACharacterBase* CharacterBase = Cast<APACharacterBase>(P);
	if (CharacterBase)
	{
		CharacterBase->GetAbilitySystemComponent()->InitAbilityActorInfo(CharacterBase, CharacterBase);
	}

	//...
}
```

对于 `ASC` 位于 `PlayerState` 上的玩家控制角色，我通常在服务器上的 `Pawn` 的 `PossessedBy()` 函数中初始化，在客户端上的 `Pawn` 的 `OnRep_PlayerState()` 函数中初始化。这确保了 `PlayerState` 在客户端存在。

```c++
// Server only
void AGDHeroCharacter::PossessedBy(AController * NewController)
{
	Super::PossessedBy(NewController);

	AGDPlayerState* PS = GetPlayerState<AGDPlayerState>();
	if (PS)
	{
		// Set the ASC on the Server. Clients do this in OnRep_PlayerState()
		AbilitySystemComponent = Cast<UGDAbilitySystemComponent>(PS->GetAbilitySystemComponent());

		// AI won't have PlayerControllers so we can init again here just to be sure. No harm in initing twice for heroes that have PlayerControllers.
		PS->GetAbilitySystemComponent()->InitAbilityActorInfo(PS, this);
	}
	
	//...
}
```

```c++
// Client only
void AGDHeroCharacter::OnRep_PlayerState()
{
	Super::OnRep_PlayerState();

	AGDPlayerState* PS = GetPlayerState<AGDPlayerState>();
	if (PS)
	{
		// Set the ASC for clients. Server does this in PossessedBy.
		AbilitySystemComponent = Cast<UGDAbilitySystemComponent>(PS->GetAbilitySystemComponent());

		// Init ASC Actor Info for clients. Server will init its ASC when it possesses a new Actor.
		AbilitySystemComponent->InitAbilityActorInfo(PS, this);
	}

	// ...
}
```

如果您收到错误消息 `LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!`，那么您没有在客户端初始化您的 `ASC`。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-gt"></a>
### 4.2 Gameplay Tags
[`FGameplayTags`](https://docs.unrealengine.com/en-US/API/Runtime/GameplayTags/FGameplayTag/index.html) 是形式为 `Parent.Child.Grandchild...` 的分层名称，注册到 `GameplayTagManager`。这些标签对于分类和描述对象状态非常有用。例如，如果角色被眩晕，我们可以在眩晕持续期间给它一个 `State.Debuff.Stun` `GameplayTag`。

您会发现自己用 `GameplayTags` 替换过去用布尔值或枚举处理的东西，并对对象是否具有某些 `GameplayTags` 进行布尔逻辑判断。

当给对象添加标签时，如果它有 `ASC`，我们通常将它们添加到其 `ASC`，以便 GAS 可以与它们交互。`UAbilitySystemComponent` 实现了 `IGameplayTagAssetInterface`，提供访问其拥有的 `GameplayTags` 的函数。

多个 `GameplayTags` 可以存储在 `FGameplayTagContainer` 中。最好使用 `GameplayTagContainer` 而不是 `TArray<FGameplayTag>`，因为 `GameplayTagContainers` 添加了一些效率魔法。虽然标签是标准的 `FNames`，但如果在项目设置中启用了 `Fast Replication`，它们可以在 `FGameplayTagContainers` 中高效地打包在一起进行复制。`Fast Replication` 要求服务器和客户端具有相同的 `GameplayTags` 列表。这通常不应该是问题，所以您应该启用此选项。`GameplayTagContainers` 也可以返回 `TArray<FGameplayTag>` 用于迭代。

存储在 `FGameplayTagCountContainer` 中的 `GameplayTags` 有一个 `TagMap`，存储该 `GameplayTag` 的实例数量。`FGameplayTagCountContainer` 可能仍然包含 `GameplayTag`，但其 `TagMapCount` 为零。如果 `ASC` 仍然有 `GameplayTag`，您在调试时可能会遇到这种情况。任何 `HasTag()` 或 `HasMatchingTag()` 或类似函数都会检查 `TagMapCount`，如果 `GameplayTag` 不存在或其 `TagMapCount` 为零，则返回 false。

`GameplayTags` 必须提前在 `DefaultGameplayTags.ini` 中定义。Unreal Engine 编辑器在项目设置中提供一个界面，让开发者管理 `GameplayTags` 而无需手动编辑 `DefaultGameplayTags.ini`。`GameplayTag` 编辑器可以创建、重命名、搜索引用和删除 `GameplayTags`。

![项目设置中的 GameplayTag 编辑器](https://github.com/tranek/GASDocumentation/raw/master/Images/gameplaytageditor.png)

搜索 `GameplayTag` 引用将在编辑器中显示熟悉的 `Reference Viewer` 图表，显示引用该 `GameplayTag` 的所有资产。但是，这不会显示引用该 `GameplayTag` 的任何 C++ 类。

重命名 `GameplayTags` 会创建重定向，以便仍然引用原始 `GameplayTag` 的资产可以重定向到新的 `GameplayTag`。如果可能，我更喜欢创建一个新的 `GameplayTag`，手动将所有引用更新到新的 `GameplayTag`，然后删除旧的 `GameplayTag` 以避免创建重定向。

除了 `Fast Replication` 外，`GameplayTag` 编辑器还有一个选项来填充常见复制的 `GameplayTags` 以进一步优化它们。

如果 `GameplayTags` 是从 `GameplayEffect` 添加的，它们会被复制。`ASC` 允许您添加不被复制且必须手动管理的 `LooseGameplayTags`。示例项目为 `State.Dead` 使用 `LooseGameplayTag`，以便拥有的客户端可以立即响应其生命值降到零的情况。重生时手动将 `TagMapCount` 设置回零。只有在使用 `LooseGameplayTags` 时才手动调整 `TagMapCount`。最好使用 `UAbilitySystemComponent::AddLooseGameplayTag()` 和 `UAbilitySystemComponent::RemoveLooseGameplayTag()` 函数，而不是手动调整 `TagMapCount`。

在 C++ 中获取 `GameplayTag` 的引用：
```c++
FGameplayTag::RequestGameplayTag(FName("Your.GameplayTag.Name"))
```

对于高级 `GameplayTag` 操作，如获取父级或子级 `GameplayTags`，请查看 `GameplayTagManager` 提供的函数。要访问 `GameplayTagManager`，包含 `GameplayTagManager.h` 并使用 `UGameplayTagManager::Get().FunctionName` 调用它。`GameplayTagManager` 实际上将 `GameplayTags` 存储为关系节点（父、子等），以便比持续的字符串操作和比较更快地处理。

`GameplayTags` 和 `GameplayTagContainers` 可以有可选的 `UPROPERTY` 说明符 `Meta = (Categories = "GameplayCue")`，它在 Blueprint 中过滤标签，只显示具有 `GameplayCue` 父标签的 `GameplayTags`。当您知道 `GameplayTag` 或 `GameplayTagContainer` 变量应该只用于 `GameplayCues` 时，这很有用。

或者，有一个名为 `FGameplayCueTag` 的单独结构，它封装了一个 `FGameplayTag`，并且还会自动在 Blueprint 中过滤 `GameplayTags`，只显示具有 `GameplayCue` 父标签的标签。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-gt-change"></a>
#### 4.2.1 响应 Gameplay Tags 的变化
`ASC` 提供委托来监听添加或移除 `GameplayTag` 时的响应。

```c++
AbilitySystemComponent->RegisterGameplayTagEvent(FGameplayTag::RequestGameplayTag(FName("State.Debuff.Stun")), EGameplayTagEventType::NewOrRemoved).AddUObject(this, &AGDPlayerState::StunTagChanged);
```

回调函数有一个参数 `FGameplayTag` 和一个 `int32`，分别表示触发回调的 `GameplayTag` 和该 `GameplayTag` 的新计数。

```c++
virtual void StunTagChanged(const FGameplayTag CallbackTag, int32 NewCount);
```

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-a"></a>
### 4.3 Attributes
`Attributes` 是由 `float` 类型和可选数据表行定义的浮点值（用于创建曲线）。它们可以表示从角色生命值到角色等级再到一瓶药水的数量的任何内容。如果某个东西是游戏中的数值，您应该考虑使用 `Attribute`。`Attributes` 通常应该只由 [`GameplayEffects`](#concepts-ge) 修改，以便 `ASC` 可以[预测](#concepts-p)更改。

`Attributes` 由 [`AttributeSet`](#concepts-as) 定义和保存。`AttributeSet` 负责复制被标记为复制的 `Attributes`。有关如何定义 `Attributes` 的信息，请参阅 `AttributeSet` 部分。

**提示：**如果您不想硬编码 `Attributes` 的最大值，则有一种方法可以通过无限持续时间的 `GameplayEffect` 设置基础值来最大值变为当前值的方法。

**[⬆ 返回顶部](#table-of-contents)**

<a name="cae"></a>
## 5. 常见实现的能力和效果

<a name="cae-stun"></a>
### 5.1 眩晕
通常对于眩晕，我们希望取消 `Character` 的所有活动 `GameplayAbilities`，防止新的 `GameplayAbility` 激活，并在眩晕持续期间防止移动。示例项目的 Meteor `GameplayAbility` 对命中目标应用眩晕。

要取消目标的活动 `GameplayAbilities`，我们在添加眩晕 [`GameplayTag` 时](#concepts-gt-change)调用 `AbilitySystemComponent->CancelAbilities()`。

要防止在眩晕时激活新的 `GameplayAbilities`，在它们的 [`Activation Blocked Tags` `GameplayTagContainer`](#concepts-ga-tags) 中给 `GameplayAbilities` 添加眩晕 `GameplayTag`。

要防止在眩晕时移动，我们重写 `CharacterMovementComponent` 的 `GetMaxSpeed()` 函数，当所有者有眩晕 `GameplayTag` 时返回 0。

**[⬆ 返回顶部](#table-of-contents)**

<a name="cae-sprint"></a>
### 5.2 冲刺
示例项目提供了如何冲刺的示例 - 按住 `Left Shift` 时跑得更快。

更快的移动由 `CharacterMovementComponent` 通过向服务器发送标志来预测性地处理。有关详细信息，请参阅 `GDCharacterMovementComponent.h/cpp`。

`GA` 处理对 `Left Shift` 输入的响应，告诉 `CMC` 开始和停止冲刺，并在按下 `Left Shift` 时预测性地消耗体力。有关详细信息，请参阅 `GA_Sprint_BP`。

**[⬆ 返回顶部](#table-of-contents)**

<a name="cae-ads"></a>
### 5.3 瞄准下蹲
示例项目处理这个的方式与冲刺完全相同，但是减少移动速度而不是增加它。

有关预测性减少移动速度的详细信息，请参阅 `GDCharacterMovementComponent.h/cpp`。

有关处理输入的详细信息，请参阅 `GA_AimDownSight_BP`。瞄准下蹲没有体力消耗。

**[⬆ 返回顶部](#table-of-contents)**

<a name="cae-ls"></a>
### 5.4 吸血
我在伤害 [`ExecutionCalculation`](#concepts-ge-ec) 内部处理吸血。`GameplayEffect` 会有一个像 `Effect.CanLifesteal` 的 `GameplayTag`。`ExecutionCalculation` 检查 `GameplayEffectSpec` 是否有该 `Effect.CanLifesteal` `GameplayTag`。如果 `GameplayTag` 存在，`ExecutionCalculation` [创建一个动态 `Instant` `GameplayEffect`](#concepts-ge-dynamic)，以给予的生命值数量作为修饰符，并将其应用回 `Source` 的 `ASC`。

```c++
if (SpecAssetTags.HasTag(FGameplayTag::RequestGameplayTag(FName("Effect.Damage.CanLifesteal"))))
{
	float Lifesteal = Damage * LifestealPercent;

	UGameplayEffect* GELifesteal = NewObject<UGameplayEffect>(GetTransientPackage(), FName(TEXT("Lifesteal")));
	GELifesteal->DurationPolicy = EGameplayEffectDurationType::Instant;

	int32 Idx = GELifesteal->Modifiers.Num();
	GELifesteal->Modifiers.SetNum(Idx + 1);
	FGameplayModifierInfo& Info = GELifesteal->Modifiers[Idx];
	Info.ModifierMagnitude = FScalableFloat(Lifesteal);
	Info.ModifierOp = EGameplayModOp::Additive;
	Info.Attribute = UPAAttributeSetBase::GetHealthAttribute();

	SourceAbilitySystemComponent->ApplyGameplayEffectToSelf(GELifesteal, 1.0f, SourceAbilitySystemComponent->MakeEffectContext());
}
```

**[⬆ 返回顶部](#table-of-contents)**

<a name="cae-random"></a>
### 5.5 在客户端和服务器上生成随机数
有时您需要在 `GameplayAbility` 内部生成"随机"数字，用于子弹后坐力或散布等。客户端和服务器都希望生成相同的随机数。为此，我们必须在 `GameplayAbility` 激活时将 `random seed` 设置为相同。您需要每次激活 `GameplayAbility` 时设置 `random seed`，以防客户端错误预测激活并且其随机数序列与服务器不同步。

| 随机种子设置方法                                                          | 描述                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| ---------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 使用激活预测键                                            | `GameplayAbility` 激活预测键是一个 int16，保证在客户端和服务器的 `Activation()` 中同步和可用。您可以在客户端和服务器上将其设置为 `random seed`。这种方法的缺点是预测键总是在每次游戏开始时从零开始，并在生成键之间一致地递增值。这意味着每场比赛都会有完全相同的随机数序列。这对您的需求来说可能足够随机，也可能不够。 |
| 在激活 `GameplayAbility` 时通过事件有效载荷发送种子 | 通过事件激活您的 `GameplayAbility`，并通过复制的事件有效载荷从客户端向服务器发送随机生成的种子。这允许更多的随机性，但客户端可以轻易地破解游戏，每次只发送相同的种子值。此外，通过事件激活 `GameplayAbilities` 将阻止它们从输入绑定激活。                                                                                                                                                                     |

如果您的随机偏差很小，大多数玩家不会注意到每场游戏的序列都相同，使用激活预测键作为 `random seed` 应该适合您。如果您正在做更复杂的需要防黑客的事情，也许使用 `Server Initiated` `GameplayAbility` 会更好，服务器可以创建预测键或生成 `random seed` 通过事件有效载荷发送。

**[⬆ 返回顶部](#table-of-contents)**

<a name="debugging"></a>
## 6. 调试 GAS

<a name="debugging-sd"></a>
### 6.1 showdebug abilitysystem
类型化 `showdebug abilitysystem` 游戏内控制台命令。这将显示属于您的 `Character` 的 `ASC` 的 `GameplayTags`、`GameplayEffects`、和 `GameplayAbilities`。使用 `PageUp` 和 `PageDown` 键循环浏览 `GameplayEffects` 页面。

**[⬆ 返回顶部](#table-of-contents)**

<a name="debugging-gd"></a>
### 6.2 Gameplay Debugger
GAS 为 Gameplay Debugger 添加了功能。使用撇号（`'`）键访问 Gameplay Debugger，然后按数字键盘上的 3 选择 Abilities 类别。该类别可能需要在 Editor Preferences -> Gameplay Debugger 中启用。

这个调试器显示所选 `Character` 的 `GameplayTags`、`GameplayEffects`、和 `GameplayAbilities`。不幸的是，您无法更改调试器显示的 `Character`，它总是显示您的 `Character`。

**[⬆ 返回顶部](#table-of-contents)**

<a name="debugging-log"></a>
### 6.3 GAS 日志记录
GAS 源代码中有许多日志记录语句。默认情况下，大多数都设置为 `Verbose` 日志级别。要启用日志类别，在您的控制台中添加以下行之一到您的 `DefaultEngine.ini` 或使用控制台命令：

```
log LogAbilitySystem VeryVerbose
```

**[⬆ 返回顶部](#table-of-contents)**

<a name="acronyms"></a>
## 10. GAS 常见缩写

| 名称                                                                                                   | 缩写            |
|------------------------------------------------------------------------------------------------------- | ------------------- |
| AbilitySystemComponent                                                                                 | ASC                 |
| AbilityTask                                                                                            | AT                  |
| [Epic 的 Action RPG 示例项目](https://www.unrealengine.com/marketplace/en-US/product/action-rpg) | ARPG, ARPG Sample   |
| CharacterMovementComponent                                                                             | CMC                 |
| GameplayAbility                                                                                        | GA                  |
| GameplayAbilitySystem                                                                                  | GAS                 |
| GameplayCue                                                                                            | GC                  |
| GameplayEffect                                                                                         | GE                  |
| GameplayEffectExecutionCalculation                                                                     | ExecCalc, Execution |
| GameplayTag                                                                                            | Tag, GT             |
| ModifierMagnitudeCalculation                                                                           | ModMagCalc, MMC     |

**[⬆ 返回顶部](#table-of-contents)**

<a name="resources"></a>
## 11. 其他资源
* [官方文档](https://docs.unrealengine.com/en-US/Gameplay/GameplayAbilitySystem/index.html)
* 源代码！
   * 特别是 `GameplayPrediction.h`
* [Epic 的 Lyra 示例项目](https://unrealengine.com/marketplace/en-US/learn/lyra)
* [Epic 的 Action RPG 示例项目](https://www.unrealengine.com/marketplace/en-US/product/action-rpg)
* [Unreal Slackers Discord](https://unrealslackers.org/) 有一个专门用于 GAS 的文本频道 `#gameplay-ability-system`
   * 检查置顶消息
* [Dan 'Pan' 的资源 GitHub 存储库](https://github.com/Pantong51/GASContent)
* [SabreDartStudios 的 YouTube 视频](https://www.youtube.com/channel/UCCFUhQ6xQyjXDZ_d6X_H_-A)

<a name="resources-daveratti"></a>
### 11.1 与 Epic Games 的 Dave Ratti 的问答

<a name="resources-daveratti-community1"></a>
#### 11.1.1 社区问题 1
[Dave Ratti 对 Unreal Slackers Discord 服务器社区关于 GAS 问题的回应](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)。

**[⬆ 返回顶部](#table-of-contents)**

<a name="resources-daveratti-community2"></a>
#### 11.1.2 社区问题 2
社区成员 [iniside](https://github.com/iniside) 与 Dave Ratti 的问答。

**[⬆ 返回顶部](#table-of-contents)**

<a name="changelog"></a>
## 12. GAS 更改日志

这是从官方 Unreal Engine 升级更改日志和我遇到的未记录更改中编译的 GAS 重要更改（修复、更改和新功能）列表。如果您发现此处未列出的内容，请提出问题或拉取请求。

<a name="changelog-5.3"></a>
### 5.3
* 崩溃修复：修复了在无缝传输后尝试应用 Gameplay Cues 时的崩溃。
* 崩溃修复：修复了使用 Live Coding 时 GlobalAbilityTaskCount 导致的崩溃。
* 错误修复：子类中调用 `Super::ActivateAbility` 现在是安全的。以前，它会调用 `CommitAbility`。
* 错误修复：添加了对正确复制不同类型 FGameplayEffectContext 的支持。
* 新功能：确保在 Ability System 使用时调用 UAbilitySystemGlobals::InitGlobalData。以前如果用户没有调用它，Gameplay Ability System 无法正常工作。

https://docs.unrealengine.com/5.3/en-US/unreal-engine-5.3-release-notes/

<a name="changelog-5.2"></a>
### 5.2
* 错误修复：修复了 `UAbilitySystemBlueprintLibrary::MakeSpecHandle` 函数中的崩溃。
* 新功能：[Gameplay Targeting System](https://docs.unrealengine.com/en-US/gameplay-targeting-system-in-unreal-engine/) 是一种创建数据驱动目标请求的方法。
* 新功能：添加了对 GameplayTag 查询的自定义序列化支持。

https://docs.unrealengine.com/5.2/en-US/unreal-engine-5.2-release-notes/

<a name="changelog-5.1"></a>
### 5.1
* 错误修复：修复了复制的松散 gameplay tags 没有复制到所有者的问题。
* 新功能：添加了对 Gameplay Effects 添加阻止能力标签的支持。
* 新功能：添加了 WaitGameplayTagQuery 节点。

https://docs.unrealengine.com/5.1/en-US/unreal-engine-5.1-release-notes/

<a name="changelog-5.0"></a>
### 5.0

https://docs.unrealengine.com/5.0/en-US/unreal-engine-5.0-release-notes/

<a name="changelog-4.27"></a>
### 4.27
* 崩溃修复：修复了网络客户端在 Actor 完成执行使用具有强度随时间修饰符的恒定力根运动任务的能力时可能崩溃的根运动源问题。
* 新功能：原生 GameplayTags。引入了新的 `FNativeGameplayTag`，这些使得在模块加载和卸载时正确注册和取消注册的一次性原生标签成为可能。

https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_27/

<a name="changelog-4.26"></a>
### 4.26
* GAS 插件不再标记为测试版。
* 崩溃修复：修复了添加没有有效标签源选择的 gameplay tag 时的崩溃。
* 新功能：为 gameplay ability commit 函数添加了可选标签参数。

https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_26/

<a name="changelog-4.25.1"></a>
### 4.25.1
* 修复！UE-92787 使用 Get Float Attribute 节点且属性引脚设置为内联时保存蓝图的崩溃
* 修复！UE-92810 生成具有实例可编辑 gameplay tag 属性且已内联更改的 actor 时的崩溃

<a name="changelog-4.25"></a>
### 4.25
* 修复了 `RootMotionSource` `AbilityTasks` 的预测
* [`GAMEPLAYATTRIBUTE_REPNOTIFY()`](#concepts-as-attributes) 现在还额外接受旧的 `Attribute` 值。我们必须将其作为可选参数提供给我们的 `OnRep` 函数。
* 为 `UGameplayAbility` 添加了 [`NetSecurityPolicy`](#concepts-ga-netsecuritypolicy)。

https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_25/

<a name="changelog-4.24"></a>
### 4.24
* 修复了蓝图节点 `Attribute` 变量在编译时重置为 `None` 的问题。
* 需要调用 [`UAbilitySystemGlobals::InitGlobalData()`](#concepts-asg-initglobaldata) 来使用 [`TargetData`](#concepts-targeting-data)，否则您会收到 `ScriptStructCache` 错误，客户端将与服务器断开连接。我的建议是现在在每个项目中都调用这个，而在 4.24 之前这是可选的。

https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_24/

**[⬆ 返回顶部](#table-of-contents)**