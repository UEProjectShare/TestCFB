<!-- Keep these links. Translations will automatically update with the README. -->
[Deutsch](https://zdoc.app/de/UEProjectShare/TestCFB) | 
[English](https://zdoc.app/en/UEProjectShare/TestCFB) | 
[Español](https://zdoc.app/es/UEProjectShare/TestCFB) | 
[français](https://zdoc.app/fr/UEProjectShare/TestCFB) | 
[日本語](https://zdoc.app/ja/UEProjectShare/TestCFB) | 
[한국어](https://zdoc.app/ko/UEProjectShare/TestCFB) | 
[Português](https://zdoc.app/pt/UEProjectShare/TestCFB) | 
[Русский](https://zdoc.app/ru/UEProjectShare/TestCFB) | 
[中文](https://zdoc.app/zh/UEProjectShare/TestCFB)

---

> 目前只编译上传了Win64版本，其他平台的支持只需要修改`Plugins/ControlFlowsBlueprint/ControlFlowsBlueprint.uplugin`中的`PlatformAllowList`和`SupportedTargetPlatforms`即可。

> 使用过程遇到任何问题可以直接在`Github`联系我，或者在`https://www.bonan.online/ControlFlowsBlueprintSupport`留言。
---

# ControlFlowsBlueprintExampleActor 测试文档

## 概述

`AControlFlowsBlueprintExampleActor` 是 ControlFlowsBlueprint 插件的示例测试 Actor，演示了控制流系统的各种功能和使用方式。

## 示例类型

通过 `AutoRunExampleType` 枚举选择要运行的示例：

| 类型 | 说明 |
|------|------|
| `SimpleSequence` | 简单顺序执行 |
| `AsyncWait` | 异步等待操作 |
| `Branch` | 分支选择 |
| `Concurrent` | 并发执行 |
| `Loop` | 循环执行 |
| `SubFlow` | 子流程嵌套 |
| `Complex` | 复杂混合示例 |

## 配置属性

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `bAutoRunBPExampleOnBeginPlay` | bool | false | BeginPlay 时自动运行蓝图示例 |
| `bAutoRunExampleOnBeginPlay` | bool | false | BeginPlay 时自动运行 C++ 示例 |
| `AutoRunExampleType` | EExampleType | SimpleSequence | 自动运行的示例类型 |
| `ExampleDelayTime` | float | 2.0 | 异步操作延迟时间（秒） |
| `ExampleLoopCount` | int32 | 3 | 循环示例的迭代次数 |

## 示例详解

### 1. SimpleSequence - 简单顺序执行

```cpp
CurrentControlFlow
    ->QueueStep(TEXT("Step 1"), Step1)
    ->QueueStep(TEXT("Step 2"), Step2)
    ->QueueStep(TEXT("Step 3"), Step3);
```

![](Images/SimpleSequence.png)

按顺序执行 Step1 → Step2 → Step3。

### 2. AsyncWait - 异步等待

```cpp
CurrentControlFlow
    ->QueueStep(TEXT("Prepare"), Prepare)
    ->QueueWait(TEXT("Async Operation"), AsyncWait)
    ->QueueStep(TEXT("Cleanup"), Cleanup);
```

![](Images/AsyncWait.png)

执行流程：
1. 执行 Prepare 步骤
2. 启动异步操作，等待 `ExampleDelayTime` 秒
3. 调用 `NodeRef->ContinueFlow()` 继续
4. 执行 Cleanup 步骤

### 3. Branch - 分支选择

```cpp
CurrentControlFlow
    ->QueueStep(TEXT("Initialize"), Init)
    ->QueueBranch(TEXT("Decision Point"), Decide)
    ->QueueStep(TEXT("Finalize"), Finalize);
```

![](Images/Branch.png)

在 `ExampleBranchSelector` 中动态添加分支：
- Branch 0: "Branch A"
- Branch 1: "Branch B"  
- Branch 2: "Branch C"

随机选择一个分支执行。

### 4. Concurrent - 并发执行

```cpp
CurrentControlFlow
    ->QueueStep(TEXT("Setup"), Setup)
    ->QueueConcurrent(TEXT("Parallel Tasks"), Parallel)
    ->QueueStep(TEXT("Merge Results"), Merge);
```

![](Images/Concurrent.png)

在 `SetupConcurrentExample` 中添加并发任务：
- Task A
- Task B
- Task C

所有任务并行执行，全部完成后继续。

### 5. Loop - 循环执行

```cpp
CurrentControlFlow
    ->QueueStep(TEXT("Loop Setup"), Setup)
    ->QueueLoop(TEXT("Repeat Process"), LoopCond)
    ->QueueStep(TEXT("Loop Cleanup"), Cleanup);
```

![](Images/Loop.png)

在 `ExampleLoopCondition` 中：
- 检查 `CurrentLoopIteration >= ExampleLoopCount` 决定是否继续
- 每次迭代执行 "Loop Work" 步骤和 0.5 秒延迟
- 返回 `RunLoop` 继续循环，`LoopFinished` 结束循环

### 6. SubFlow - 子流程

```cpp
CurrentControlFlow
    ->QueueStep(TEXT("Prepare"), Prepare)
    ->QueueSubFlow(TEXT("InitializeSubsystems"), InitSubsystems)
    ->QueueSubFlow(TEXT("LoadResources"), LoadResources)
    ->QueueStep(TEXT("Finalize"), Finalize);
```

![](Images/SubFlow.png)

子流程 InitializeSubsystems 包含：
- InitAudio → InitNetwork → InitUI → SubsystemWarmup (0.5s delay)

子流程 LoadResources 包含：
- LoadTextures → LoadMeshes → LoadSounds

### 7. Complex - 复杂混合

```cpp
CurrentControlFlow
    ->QueueStep(TEXT("Initialize System"), Init)
    ->QueueDelay(TEXT("Startup Delay"), 1.0f)
    ->QueueBranch(TEXT("System Check"), Decide)
    ->QueueConcurrent(TEXT("Parallel Loading"), Parallel)
    ->QueueStep(TEXT("Finalize System"), Finalize);
```

![](Images/Complex.png)

组合使用多种控制流类型。

## 事件回调

| 事件 | 说明 |
|------|------|
| `OnFlowComplete` | 流程完成时触发 |
| `OnStepComplete` | 步骤完成时触发 |
| `OnFlowCancelled` | 流程取消时触发 |

## 蓝图可调用函数

| 函数 | 说明 |
|------|------|
| `BP_CreateExampleFlow` | 创建示例控制流 |
| `RunSimpleSequenceExample` | 运行简单顺序示例 |
| `RunAsyncWaitExample` | 运行异步等待示例 |
| `RunBranchExample` | 运行分支示例 |
| `RunConcurrentExample` | 运行并发示例 |
| `RunLoopExample` | 运行循环示例 |
| `RunSubFlowExample` | 运行子流程示例 |
| `RunComplexExample` | 运行复杂混合示例 |

## 蓝图可实现事件

| 事件 | 参数 | 说明 |
|------|------|------|
| `OnStepExecuted` | StepName | 步骤执行时 |
| `OnAsyncStepStarted` | StepName | 异步步骤开始时 |
| `OnFlowCompleted` | FlowName | 流程完成时 |
| `OnFlowCancelled` | FlowName | 流程取消时 |

## 使用方法

1. 将 `AControlFlowsBlueprintExampleActor` 放入关卡
2. 设置 `bAutoRunExampleOnBeginPlay = true`
3. 选择 `AutoRunExampleType`
4. 运行游戏查看日志输出

或在蓝图/C++ 中手动调用 `RunXxxExample()` 函数。
