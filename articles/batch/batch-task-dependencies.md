---
title: タスクの依存関係を使って、他のタスクの完了に基づきタスクを実行する - Azure Batch | Microsoft Docs
description: MapReduce に見られるようなビッグ データのワークロードを Azure Batch で処理することを目的として、他のタスクの完了に依存するタスクを作成します。
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: b8d12db5-ca30-4c7d-993a-a05af9257210
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6a9b44ed56774466bae2f0f5d48b5e012382721b
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2018
ms.locfileid: "37865235"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>タスクの依存関係を作成して、他のタスクに依存するタスクを実行する

タスクの依存関係を定義して、タスクまたは一連のタスクが親タスクの完了後にのみ実行されるようにできます。 タスクの依存関係は、たとえば次のようなシナリオで役立ちます。

* クラウドでの MapReduce 形式のワークロード。
* 有向非巡回グラフ (DAG) としてデータ処理タスクを表すことのできるジョブ。
* 各タスクが次のタスクの開始前に完了している必要がある、レンダリング前およびレンダリング後プロセス。
* 下流のタスクが上流タスクの出力に依存する、その他すべてのジョブ。

Batch のタスク依存関係を使用すると、コンピューティング ノードで 1 つ以上の親タスクが完了した後に実行されるようにスケジューリングされたタスクを作成できます。 たとえば、並列実行される複数の独立したタスクを使って 3D ムービーの各フレームをレンダリングするジョブを作成できます。 最後のタスク ("マージ タスク") では、すべてのフレームが正常にレンダリングされた後にのみ、そのレンダリング済みのフレームをマージしてムービーを完成させます。

既定では、依存タスクは、親タスクが正常に完了した後にのみ実行されるようにスケジューリングされます。 既定の動作をオーバーライドし、親タスクが失敗したときにタスクを実行する、依存関係アクションを指定できます。 詳細については、「[依存関係アクション](#dependency-actions)」セクションをご覧ください。  

他のタスクに対して一対一または一対多の依存関係を持つタスクを作成できます。 特定の範囲に依存するタスク (指定した範囲のタスク ID に該当するタスクのグループが完了した場合に実行されるタスク) を作成することもできます。 その 3 つの基本シナリオを組み合わせれば、多対多の関係を作成することもできます。

## <a name="task-dependencies-with-batch-net"></a>Batch .NET でのタスクの依存関係
この記事では、[Batch .NET][net_msdn] ライブラリを使用したタスクの依存関係の構成方法について説明します。 まず、ジョブで[タスクの依存関係を有効にする](#enable-task-dependencies)方法を説明した後、[依存関係を伴うタスクを構成する](#create-dependent-tasks)方法を紹介します。 親が失敗した場合に依存タスクを実行する、依存関係アクションを指定する方法についても説明します。 最後に、Batch でサポートされる [依存関係のシナリオ](#dependency-scenarios) について取り上げます。

## <a name="enable-task-dependencies"></a>タスクの依存関係を有効にする
Batch アプリケーションでタスクの依存関係を使用するには、まず、タスクの依存関係を使用するジョブを構成する必要があります。 Batch .NET では、[CloudJob][net_cloudjob] の [UsesTaskDependencies][net_usestaskdependencies] プロパティを `true` に設定することによって有効にします。

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

前のコード スニペットでは、"batchClient" は、[BatchClient][net_batchclient] クラスのインスタンスです。

## <a name="create-dependent-tasks"></a>依存タスクの作成
1 つ以上の親タスクの完了に依存するタスクを作成するには、タスクが他のタスクに "依存" するよう指定します。 Batch .NET では、[TaskDependencies][net_taskdependencies] クラスのインスタンスを使って [CloudTask][net_cloudtask].[DependsOn][net_dependson] プロパティを構成します。

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

このコード スニペットでは、"Flowers" というタスク ID の依存タスクを作成しています。 "Flowers" タスクは "Rain" タスクと "Sun" タスクに依存します。 "Flowers" タスクは、"Rain" タスクと "Sun" タスクが正常に完了した後にのみコンピューティング ノードで実行されるようにスケジューリングされます。

> [!NOTE]
> 既定では、タスクは **completed** 状態になり、その**終了コード**が `0` であるときに正常に完了したと見なされます。 Batch .NET では、[CloudTask][net_cloudtask].[State][net_taskstate] プロパティの値が `Completed` で、なおかつ CloudTask の [TaskExecutionInformation][net_taskexecutioninformation].[ExitCode][net_exitcode] プロパティの値が `0` である状態が該当します。 これを変更する方法については、「[依存関係アクション](#dependency-actions)」セクションを参照してください。
> 
> 

## <a name="dependency-scenarios"></a>依存関係のシナリオ
Azure Batch で利用できる基本的なタスクの依存関係には、一対一、一対多、タスク ID 範囲という 3 つのシナリオがあります。 これらを組み合わせることで、第 4 のシナリオ (多対多) を実現することもできます。

| シナリオ&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 例 |  |
|:---:| --- | --- |
|  [一対一](#one-to-one) |*taskB* は *taskA* に依存 <p/> *taskB* は、*taskA* が正常に完了するまで実行されないようにスケジューリングされる |![Diagram: one-to-one task dependency][1] |
|  [一対多](#one-to-many) |*taskC* は *taskA* と *taskB* の両方に依存 <p/> *taskC* は、*taskA* と *taskB* の両方が正常に完了するまで実行されないようにスケジューリングされる |![Diagram: one-to-many task dependency][2] |
|  [タスク ID の範囲](#task-id-range) |*taskD* は、一連のタスク範囲に依存 <p/> *taskD* は、ID *1* ～ *10* のタスクが正常に完了するまで実行されないようにスケジューリングされる |![Diagram: Task id range dependency][3] |

> [!TIP]
> タスク C、D、E、F がそれぞれタスク A と B に依存するような**多対多**の関係を作成できます。たとえば下流の複数のタスクが上流にある複数のタスクの出力に依存するような、前処理を並列実行する状況で有効活用できます。
> 
> このセクションの例では、依存タスクは親タスクが正常に完了した後にのみ実行されます。 この動作は、依存タスクの既定の動作です。 既定の動作をオーバーライドする依存関係アクションを指定すると、親タスクが失敗した後にタスクを実行できます。 詳細については、「[依存関係アクション](#dependency-actions)」セクションをご覧ください。

### <a name="one-to-one"></a>一対一
一対一の依存関係では、タスクは 1 つの親タスクの正常な完了に依存します。 この依存関係を作成するには、[CloudTask][net_cloudtask] の [DependsOn][net_dependson] プロパティに値を設定する際、[TaskDependencies][net_taskdependencies].[OnId][net_onid] 静的メソッドに 1 つのタスク ID を渡します。

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>一対多
一対多の依存関係では、タスクは複数の親タスクの完了に依存します。 この依存関係を作成するには、[CloudTask][net_cloudtask] の [DependsOn][net_dependson] プロパティに値を設定する際、[TaskDependencies][net_taskdependencies].[OnId][net_onids] 静的メソッドにタスク ID のコレクションを渡します。

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

### <a name="task-id-range"></a>タスク ID の範囲
親タスクの範囲への依存関係では、タスクは ID が特定の範囲内にあるタスクの完了に依存します。
この依存関係を作成するには、[CloudTask][net_cloudtask] の [DependsOn][net_dependson] プロパティに値を設定する際、その範囲の最初のタスク ID と最後のタスク ID を [TaskDependencies][net_taskdependencies].[OnId][net_onidrange] 静的メソッドに渡します。

> [!IMPORTANT]
> 依存関係にタスク ID の範囲を使用した場合、その範囲によって選択されるのは、整数値を表す ID を持つタスクだけです。 そのため、範囲 `1..10` ではタスク `3` と `7` が選択されますが、`5flamingoes` は選択されません。 
> 
> 範囲の依存関係を評価するときに先行ゼロは重要ではないため、`4`、`04`、および `004` の文字列識別子を持つタスクはすべて範囲*内*になり、それらはすべてタスク `4` として扱われます。そのため、最初に完了したものが依存関係を満たします。
> 
> 範囲内のすべてのタスクが、正常に完了するか、または **Satisfy** に設定された依存関係アクションに指定されているエラーで終了することによって、依存関係を満たしている必要があります。 詳細については、「[依存関係アクション](#dependency-actions)」セクションをご覧ください。
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

## <a name="dependency-actions"></a>依存関係アクション

既定では、依存タスクまたは一連の依存タスク は、親タスクが正常に完了した後にのみ実行されます。 シナリオによっては、親タスクが失敗した後でも依存タスクを実行したい場合があります。 依存関係アクションを指定すると、既定の動作をオーバーライドできます。 依存関係アクションは、親タスクの成功または失敗に基づいて、依存タスクを実行対象にするかどうかを指定します。 

たとえば、依存タスクが、上流タスクの完了によって得られるデータを待機しているとします。 上流タスクが失敗しても、古いデータを使用して依存タスクを実行できる場合があります。 その場合は、依存関係アクションで、親タスクの失敗にかかわらずタスクを実行対象にするよう指定できます。

依存関係アクションは、親タスクの終了条件に基づきます。 .NET では、次のどの終了条件に対しても依存関係アクションを指定できます。詳細については、[ExitConditions][net_exitconditions] クラスに関する記事をご覧ください。

- 前処理エラーが発生したとき。
- ファイルのアップロード エラーが発生したとき。 **exitCodes** または **exitCodeRanges** で指定された終了コードでタスクが終了し、その後ファイルのアップロード エラーが発生した場合、その終了コードにより指定されたアクションが優先されます。
- **ExitCodes** プロパティで定義された終了コードでタスクが終了したとき。
- **ExitCodeRanges** プロパティで定義された範囲内の終了コードでタスクが終了したとき。
- 既定のケースで、**ExitCodes** および **ExitCodeRanges** で定義されていない終了コードでタスクが終了した、または前処理エラーでタスクが終了し、**PreProcessingError** プロパティが設定されていない、またはファイルのアップロード エラーでタスクが失敗し、**FileUploadError** プロパティが設定されていない場合。 

.NET で依存関係アクションを指定するには、終了条件の [ExitOptions][net_exitoptions].[DependencyAction][net_dependencyaction] プロパティを設定します。 **DependencyAction** プロパティは、次の 2 つの値のいずれかに設定できます。

- **DependencyAction** プロパティを **Satisfy** に設定すると、指定したエラーで親タスクが終了した場合に依存タスクが実行対象になります。
- **DependencyAction** プロパティを **Block** に設定すると、依存タスクは実行対象になりません。

**DependencyAction** プロパティの既定の設定は、終了コード 0 に対しては **Satisfy**、その他のすべての終了条件に対しては **Block** です。

次のコード スニペットでは、親タスクの **DependencyAction** プロパティを設定します。 親タスクが前処理エラーで終了した場合、または指定したエラー コードで終了した場合は、依存タスクがブロックされます。 親タスクがその他の 0 以外のエラーで終了した場合は、依存タスクが実行対象となります。

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

## <a name="code-sample"></a>サンプル コード
[TaskDependencies][github_taskdependencies] サンプル プロジェクトは、GitHub にある [Azure Batch コード サンプル][github_samples]の 1 つです。 この Visual Studio ソリューションでは以下を示しています。

- ジョブでタスクの依存関係を有効にする方法
- 他のタスクに依存するタスクを作成する方法
- コンピューティング ノードのプールでそれらのタスクを実行する方法

## <a name="next-steps"></a>次の手順
### <a name="application-deployment"></a>アプリケーションのデプロイ
コンピューティング ノード上でタスクを通じて実行するアプリケーションのデプロイとバージョン管理は、どちらも Batch の [アプリケーション パッケージ](batch-application-packages.md) 機能を使って簡単に実現できます。

### <a name="installing-applications-and-staging-data"></a>アプリケーションとステージング データのインストール
タスクの実行に使用するノードを準備する方法の概要については、Azure Batch フォーラムの「[Installing applications and staging data on Batch compute nodes][forum_post]」(Batch コンピューティング ノードへのアプリケーションとステージング データのインストール) をご覧ください。 この投稿記事は、Azure Batch チームのメンバーによって書かれたものです。コンピューティング ノードにアプリケーション、タスクの入力データ、その他のファイルをコピーする各種の方法がわかりやすく解説されています。

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
[net_dependencyaction]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitoptions#Microsoft_Azure_Batch_ExitOptions_DependencyAction
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_onid]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onid.aspx
[net_onids]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onids.aspx
[net_onidrange]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onidrange.aspx
[net_taskexecutioninformation]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_usestaskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.usestaskdependencies.aspx
[net_taskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskdependencies.aspx

[1]: ./media/batch-task-dependency/01_one_to_one.png "Diagram: one-to-one dependency"
[2]: ./media/batch-task-dependency/02_one_to_many.png "Diagram: one-to-many dependency"
[3]: ./media/batch-task-dependency/03_task_id_range.png "Diagram: task id range dependency"
