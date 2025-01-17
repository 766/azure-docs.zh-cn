---
title: 使用任务依赖关系来基于其他任务的完成情况运行任务 - Azure Batch | Microsoft 文档
description: 在 Azure Batch 中创建依赖于其他任务的完成的任务，以处理 MapReduce 样式和类似的大数据工作负荷。
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: b8d12db5-ca30-4c7d-993a-a05af9257210
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a0a258630fcb3639f20de4c72591611b7af15b90
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2019
ms.locfileid: "68322980"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>创建任务依赖关系，以运行依赖于其他任务的任务

可以定义任务依赖关系，以便仅在完成父任务后，才运行一个或一组任务。 任务依赖关系可发挥作用的部分方案包括：

* 云中的 MapReduce 样式工作负荷。
* 数据处理任务可以表示为有向无环图 (DAG) 的作业。
* 渲染前和渲染后过程，其中只有在完成每个任务后，其后续任务才能开始。
* 下游任务依赖于上游任务输出的任何其他作业。

使用批处理任务依赖关系，可以创建在完成一个或多个父任务后在计算节点上按计划执行的任务。 例如，可以创建一个作业，使用单独的并行任务渲染 3D 影片的每个帧。 最后一个任务为“合并任务”，仅在所有帧已成功呈现后，才将呈现的帧合并为完整影片。

默认情况下，依赖任务计划为仅在成功完成父任务后执行。 可以指定一个依赖关系操作来重写默认行为，并在父任务失败时运行任务。 有关详细信息，请参阅[依赖关系操作](#dependency-actions)部分。  

用户可以创建依赖于一对一或一对多关系中其他任务的任务。 甚至可以创建一个范围依赖关系，使其中一项任务依赖于特定任务 ID 范围内一组任务的完成。 可以组合这三种基本方案，以创建多对多关系。

## <a name="task-dependencies-with-batch-net"></a>Batch .NET 的任务依赖关系
本文介绍如何使用[Batch .net][net_msdn]库配置任务依赖关系。 本文首先说明如何为作业[启用任务依赖关系](#enable-task-dependencies)，然后演示如何[为任务配置依赖关系](#create-dependent-tasks)。 本文还介绍如何指定一个依赖关系操作，以便在父任务失败时运行依赖任务。 最后介绍 Batch 支持的[依赖关系方案](#dependency-scenarios)。

## <a name="enable-task-dependencies"></a>启用任务依赖关系
要在批处理应用程序中使用任务依赖关系，必须先将作业配置为使用任务依赖关系。 在 Batch .NET 中, [CloudJob][net_cloudjob] 通过将其 [UsesTaskDependencies][net_usestaskdependencies]属性设置为`true`:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

在上面的代码段中, "batchClient" 是[batchClient][net_batchclient]类的实例。

## <a name="create-dependent-tasks"></a>创建依赖任务
若要创建一个依赖于一个或多个父任务的完成的任务，可以指定该任务必须“依赖于”其他任务。 在 Batch .NET 中, 配置[CloudTask][net_cloudtask]。具有[TaskDependencies][net_taskdependencies]类的实例的[DependsOn][net_dependson]属性:

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

此代码片段创建任务 ID 为“Flowers”的依赖任务。 “Flowers”任务依赖于“Rain”和“Sun”任务。 “Flowers”任务将计划为仅在“Rain”和“Sun”任务已成功完成后才在计算节点上运行。

> [!NOTE]
> 默认情况下，当任务处于“已完成”状态并且其“退出代码”为 `0` 时，该任务视为已成功完成。 在 Batch .NET 中, 这意味着[CloudTask][net_cloudtask]。的[状态][net_taskstate]属性值`Completed`和 CloudTask 的[TaskExecutionInformation][net_taskexecutioninformation]。[ExitCode][net_exitcode]属性值为`0`。 有关如何更改此设置，请参阅[依赖项操作](#dependency-actions)部分。
> 
> 

## <a name="dependency-scenarios"></a>依赖关系方案
可以在 Azure Batch 中使用三种基本任务依赖关系方案：一对一、一对多和任务 ID 范围依赖关系。 可以组合这些方案以提供第四种方案：多对多。

| 方案&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 示例 |  |
|:---:| --- | --- |
|  [一对一](#one-to-one) |*taskB* 取决于 *taskA* <p/> *taskA* 成功完成后，*taskB* 才会按计划执行 |![关系图：一对一任务依赖关系][1] |
|  [一对多](#one-to-many) |*taskC* 同时取决于 *taskA* 和 *taskB* <p/> *taskA* 和 *taskB* 成功完成后，*taskC* 才会按计划执行 |![关系图：一对多任务依赖关系][2] |
|  [任务 ID 范围](#task-id-range) |*taskD* 取决于一系列任务 <p/> ID 为 *1* 到 *10* 的任务成功完成后，*taskD* 才会按计划执行 |![示意图：任务 ID 范围依赖项][3] |

> [!TIP]
> 可以创建**多对多**关系，例如，在此关系中任务 C、D、E 和 F 都依赖于任务 A 和 B。这很有用，例如，在下游任务依赖于多个上游任务的输出的并行化预处理方案中，即可以这样操作。
> 
> 在本部分的示例中，仅在父任务成功完成时才运行依赖任务。 此行为是依赖任务的默认行为。 可以通过指定一个依赖关系操作来重写默认行为，在父任务失败后运行依赖任务。 有关详细信息，请参阅[依赖关系操作](#dependency-actions)部分。

### <a name="one-to-one"></a>一对一
在一对一关系中，任务依赖于一个父任务的成功完成。 若要创建依赖项, 请为[TaskDependencies][net_taskdependencies]提供单个任务 ID。当你填充[CloudTask][net_cloudtask]的[DependsOn][net_dependson]属性时, [OnId][net_onid]静态方法。

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>一对多
在一对多关系中，任务依赖于多个父任务的完成。 若要创建依赖项, 请向[TaskDependencies][net_taskdependencies]提供任务 id 的集合。当你填充[CloudTask][net_cloudtask]的[DependsOn][net_dependson]属性时, [OnIds][net_onids]静态方法。

```csharp
// 'Rain' and 'Sun' don't depend on any other tasks
new CloudTask("Rain", "cmd.exe /c echo Rain"),
new CloudTask("Sun", "cmd.exe /c echo Sun"),

// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
``` 

### <a name="task-id-range"></a>任务 ID 范围
在依赖于一系列父任务的关系中，任务依赖于其 ID 位于某个范围内的任务的完成。
若要创建依赖项, 请向[TaskDependencies][net_taskdependencies]提供范围中的第一个和最后一个任务 id。当你填充[CloudTask][net_cloudtask]的[DependsOn][net_dependson]属性时, [OnIdRange][net_onidrange]静态方法。

> [!IMPORTANT]
> 将任务 ID 范围用于依赖项时，只有 ID 表示整数值的任务将由范围选定。 因此范围 `1..10` 将选择任务 `3` 和 `7`，而不是 `5flamingoes`。 
> 
> 在评估范围依赖项时，前导零不重要，因此，带字符串标识符 `4`、`04` 和 `004` 的任务都将处于范围内，它们将全部视为任务 `4`，因此，要完成的第一个任务将满足依赖项。
> 
> 范围内的每个任务必须通过成功完成或者已完成但出现了映射到设置为 **Satisfy** 的某个依赖关系操作的失败，来满足该依赖关系。 有关详细信息，请参阅[依赖关系操作](#dependency-actions)部分。
>
>

```csharp
// Tasks 1, 2, and 3 don't depend on any other tasks. Because
// we will be using them for a task range dependency, we must
// specify string representations of integers as their ids.
new CloudTask("1", "cmd.exe /c echo 1"),
new CloudTask("2", "cmd.exe /c echo 2"),
new CloudTask("3", "cmd.exe /c echo 3"),

// Task 4 depends on a range of tasks, 1 through 3
new CloudTask("4", "cmd.exe /c echo 4")
{
    // To use a range of tasks, their ids must be integer values.
    // Note that we pass integers as parameters to TaskIdRange,
    // but their ids (above) are string representations of the ids.
    DependsOn = TaskDependencies.OnIdRange(1, 3)
},
```

## <a name="dependency-actions"></a>依赖关系操作

默认情况下，只有在父任务成功完成后，才能运行某个依赖任务或任务集。 在某些情况下，建议运行依赖任务，即使父任务失败。 可以通过指定依赖关系操作来重写默认行为。 依赖关系操作根据父任务的成功或失败状态指定某个依赖任务是否符合运行的条件。 

例如，假设某个依赖任务正在等待完成上游任务后提供的数据。 如果上游任务失败，依赖任务仍可使用旧数据运行。 在这种情况下，依赖关系操作可以指定即使父任务失败，依赖任务也符合运行的条件。

依赖关系操作基于父任务的退出条件。 您可以为以下任一退出条件指定依赖关系操作;对于 .NET, 请参阅[ExitConditions][net_exitconditions]类了解详细信息:

- 发生预处理错误时。
- 发生文件上传错误时。 如果任务退出，并返回通过“exitCodes”或“exitCodeRanges”指定的退出代码，然后遇到文件上传错误，则优先执行退出代码指定的操作。
- 任务退出并返回“ExitCodes”属性定义的退出代码时。
- 任务退出并返回处于“ExitCodeRanges”属性指定的范围内的退出代码时。
- 如果任务退出，并返回非由“ExitCodes”或“ExitCodeRanges”定义的退出代码，或者如果任务退出，并返回预处理错误，而“PreProcessingError”属性未设置，或者如果任务失败，并返回文件上传错误，而“FileUploadError”属性未设置，则为默认情况。 

若要在 .NET 中指定依赖项操作, 请设置[exitoptions.][net_exitoptions]。Exit 条件的[DependencyAction][net_dependencyaction]属性。 **DependencyAction** 属性采用以下两个值之一：

- 将 **DependencyAction** 属性设置为 **Satisfy** 表示在父任务退出并返回指定的错误时，依赖任务符合运行的条件。
- 将 **DependencyAction** 属性设置为 **Block** 表示依赖任务不符合运行的条件。

对于退出代码 0，**DependencyAction** 属性的默认设置为 **Satisfy**；对于其他退出条件，其默认设置为 **Block**。

以下代码片段设置父任务的 **DependencyAction** 属性。 如果父任务退出并返回预处理错误或指定的错误代码，则依赖任务将被阻止。 如果父任务退出并返回其他任何非零错误，依赖任务将符合运行的条件。

```csharp
// Task A is the parent task.
new CloudTask("A", "cmd.exe /c echo A")
{
    // Specify exit conditions for task A and their dependency actions.
    ExitConditions = new ExitConditions
    {
        // If task A exits with a pre-processing error, block any downstream tasks (in this example, task B).
        PreProcessingError = new ExitOptions
        {
            DependencyAction = DependencyAction.Block
        },
        // If task A exits with the specified error codes, block any downstream tasks (in this example, task B).
        ExitCodes = new List<ExitCodeMapping>
        {
            new ExitCodeMapping(10, new ExitOptions() { DependencyAction = DependencyAction.Block }),
            new ExitCodeMapping(20, new ExitOptions() { DependencyAction = DependencyAction.Block })
        },
        // If task A succeeds or fails with any other error, any downstream tasks become eligible to run 
        // (in this example, task B).
        Default = new ExitOptions
        {
            DependencyAction = DependencyAction.Satisfy
        }
    }
},
// Task B depends on task A. Whether it becomes eligible to run depends on how task A exits.
new CloudTask("B", "cmd.exe /c echo B")
{
    DependsOn = TaskDependencies.OnId("A")
},
```

## <a name="code-sample"></a>代码示例
[TaskDependencies][github_taskdependencies]示例项目是 GitHub 上的[Azure Batch 代码示例][github_samples]之一。 此 Visual Studio 解决方案演示了：

- 如何在作业中启用任务依赖关系
- 如何创建依赖于其他任务的任务
- 如何在计算节点池中执行这些任务。

## <a name="next-steps"></a>后续步骤
### <a name="application-deployment"></a>应用程序部署
使用 Batch 的[应用程序包](batch-application-packages.md)功能，可以轻松地部署任务在计算节点上执行的应用程序并对其进行版本控制。

### <a name="installing-applications-and-staging-data"></a>安装应用程序和暂存数据
有关准备节点以运行任务的方法概述, 请参阅在 Azure Batch 论坛中的[Batch 计算节点上安装应用程序和暂存数据][forum_post]。 此帖子由某个 Azure Batch 团队成员编写，是一篇很好的入门教程，介绍如何使用不同的方法将应用程序、任务输入数据和其他文件复制到计算节点。

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_taskdependencies]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_dependson]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.dependson.aspx
[net_exitcode]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.exitcode.aspx
[net_exitconditions]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitconditions
[net_exitoptions]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitoptions
[net_dependencyaction]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitoptions
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_onid]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onid.aspx
[net_onids]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onids.aspx
[net_onidrange]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onidrange.aspx
[net_taskexecutioninformation]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_usestaskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.usestaskdependencies.aspx
[net_taskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskdependencies.aspx

[1]: ./media/batch-task-dependency/01_one_to_one.png "关系图：一对一依赖关系"
[2]: ./media/batch-task-dependency/02_one_to_many.png "关系图：一对多依赖关系"
[3]: ./media/batch-task-dependency/03_task_id_range.png "关系图：任务 ID 范围依赖关系"
