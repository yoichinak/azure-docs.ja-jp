---
title: Azure Automation での Runbook の出力とメッセージ
description: Azure Automation で Runbook から出力とエラー メッセージを作成および取得する方法を説明します。
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 751175e46e13d6046cd6f459e1405a876fdce39a
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2018
ms.locfileid: "42145609"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Azure Automation での Runbook の出力および メッセージ
Azure Automation のほとんどの Runbook では、ユーザーに対するエラー メッセージや別のワークフローで使用するための複合オブジェクトなど、いくつかの出力形式が含まれます。 Windows PowerShell では、スクリプトまたはワークフローから出力を送信するための [複数のストリーム](http://blogs.technet.com/heyscriptingguy/archive/2014/03/30/understanding-streams-redirection-and-write-host-in-powershell.aspx) が提供されます。 Azure Automation はこれらのストリームごとに異なる方法で対応するため、Runbook を作成する場合は、それぞれの使用法のベスト プラクティスに従ってください。

以下の表に、公開された Runbook の実行時と [Runbook のテスト](automation-testing-runbook.md)時の両方の場合の、Azure Portal での各ストリームとその動作の簡単な説明を示します。 各ストリームの詳細については、後続のセクションで説明します。

| ストリーム | 説明 | 公開先 | テスト |
|:--- |:--- |:--- |:--- |
| 出力 |他の Runbook によって使用されることを目的とするオブジェクト。 |ジョブ履歴に書き込まれます。 |[テスト出力] ウィンドウに表示されます。 |
| 警告 |ユーザー向けの警告メッセージ。 |ジョブ履歴に書き込まれます。 |[テスト出力] ウィンドウに表示されます。 |
| Error |ユーザー向けのエラー メッセージ 例外とは異なり、既定では Runbook はエラー メッセージ発行後に実行を継続します。 |ジョブ履歴に書き込まれます。 |[テスト出力] ウィンドウに表示されます。 |
| 詳細 |全般的なまたはデバッグ情報を提供するメッセージ。 |Runbook の詳細ログが有効な場合のみ、ジョブ履歴に書き込まれます。 |Runbook で $VerbosePreference が Continue に設定されている場合のみ、[テスト出力] ウィンドウに表示されます。 |
| 進捗状況 |Runbook の各アクティビティの前後に自動的に生成されるレコード。 Runbook では、独自の進行状況レコードを作成しないでください。これらのレコードは対話型ユーザー向けです。 |Runbook の進行状況ログが有効な場合のみ、ジョブ履歴に書き込まれます。 |[テスト出力] ウィンドウには表示されません。 |
| デバッグ |対話型ユーザー向けのメッセージ。 Runbook では使用しないでください。 |ジョブ履歴には書き込まれません。 |[テスト出力] ペインには書き込まれません。 |

## <a name="output-stream"></a>出力ストリーム
出力ストリームはスクリプトまたはワークフローが正しく実行された場合に作成されるオブジェクト出力のためのものです。 Azure Automation では、このストリームは、 [現在の Runbook を呼び出す親 Runbook](automation-child-runbooks.md)が使用するオブジェクトに主に使用されます。 親 Runbook から [Runbook をインラインで呼び出す](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution)と、出力ストリームのデータが親に返されます。 ユーザーに一般情報を返すときは、その Runbook が別の Runbook から呼び出されないことがわかっている場合にのみ、出力ストリームを使用してください。 ただし通常は、ベストプラクティスとして、 [詳細ストリーム](#verbose-stream) を使用してユーザーに一般情報を伝えてください。

出力ストリームへのデータの書き込みは、 [Write-Output](http://technet.microsoft.com/library/hh849921.aspx) を使用するか、Runbook の行にオブジェクトを配置することで実行できます。

```PowerShell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="output-from-a-function"></a>関数からの出力
Runbook に含まれている関数から出力ストリームに書き込むと、出力は Runbook に戻されます。 Runbook がその出力を変数に代入する場合、出力は出力ストリームに書き込まれません。 関数内から他のストリームへの書き込みは、Runbook の対応するストリームに書き込まれます。

次のサンプル Runbook で考えてみましょう。

```PowerShell
Workflow Test-Runbook
{
  Write-Verbose "Verbose outside of function" -Verbose
  Write-Output "Output outside of function"
  $functionOutput = Test-Function
  $functionOutput

  Function Test-Function
  {
    Write-Verbose "Verbose inside of function" -Verbose
    Write-Output "Output inside of function"
  }
}
```

Runbook ジョブの出力ストリームは次のようになります。

    Output inside of function
    Output outside of function

Runbook ジョブの詳細ストリームは次のようになります。

    Verbose outside of function
    Verbose inside of function

Runbook を発行したら、それを開始する前に、詳細ストリーム出力を取得するために Runbook の設定で詳細ログを有効にする必要もあります。

### <a name="declaring-output-data-type"></a>出力のデータ型の宣言
ワークフローでは、 [OutputType 属性](http://technet.microsoft.com/library/hh847785.aspx)を使用して、その出力のデータ型を指定できます。 この属性は実行時に影響はありませんが、設計時にRunbook 作成者に対して Runbook の予想される出力を通知します。 Runbook 用のツールセットは進化し続けるため、設計時に出力のデータ型を宣言することの重要性は高まり続けます。 その結果、ベスト プラクティスは、作成するすべての Runbook にこの宣言を含めることです。

出力例の種類の一覧を次に示します。

* System.String
* System.Int32
* System.Collections.Hashtable
* Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine

次のサンプル Runbook は、文字列オブジェクトを出力し、その出力の型宣言が含まれています。 Runbook が特定の型の配列を出力する場合でも、型の配列ではなく、型を指定してください。

```PowerShell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

グラフィカル Runbook またはグラフィカル PowerShell ワークフロー Runbook で出力の種類を宣言するには、**[入力と出力]** メニュー オプションを選択し、出力の種類の名前を入力します。 親 Runbook から参照するときに簡単に識別できるように、完全な .NET クラス名を使用することをお勧めします。 これにより、そのクラスのすべてのプロパティが Runbook のデータ バスに公開され、そのプロパティを条件付きロジックやログ記録に使用したり、Runbook 内の他のアクティビティの値として参照したりする際に柔軟性が非常に高まります。<br> ![Runbook Input and Output option](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

次の例には、この機能を説明するために 2 つのグラフィカル Runbook があります。 モジュール式の Runbook デザイン モデルを適用する場合は、"*認証 Runbook テンプレート*" として機能する 1 つの Runbook で、実行アカウントを使用した Azure での認証を管理します。 通常は特定のシナリオを自動化するためのコア ロジックを実行する 2 つ目の Runbook は、ここでは " *認証 Runbook テンプレート* " を実行し、結果を **[テスト]** 出力ウィンドウに表示します。 通常の状況では、この Runbook で子 Runbook からの出力を利用して、リソースに対する操作を行います。    

**AuthenticateTo-Azure** Runbook の基本的なロジックは、次のとおりです。<br> ![Authenticate Runbook Template Example](media/automation-runbook-output-and-messages/runbook-authentication-template.png)時の両方の場合の、Azure 管理ポータルでの各ストリームとその動作の簡単な説明を示します。  

出力の種類は *Microsoft.Azure.Commands.Profile.Models.PSAzureContext* で、認証プロファイルのプロパティが返されます。<br> ![Runbook Output Type Example](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png) 

この Runbook は単純ですが、ここで呼び出す構成項目が 1 つあります。 最後のアクティビティは **Write-Output** コマンドレットを実行し、**Inputobject** パラメーターの PowerShell 式を使用してプロファイル データを $_ 変数に書き込みます。これは、そのコマンドレットに必要です。  

この例の *Test-ChildOutputType* という 2 番目の Runbook には、2 つのアクティビティだけがあります。<br> ![Example Child Output Type Runbook](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png) 

最初のアクティビティは **AuthenticateTo-Azure** Runbook を呼び出し、2 番目のアクティビティは **Write-Verbose** コマンドレットを実行します。その際、**[データ ソース]** は **[アクティビティの出力]**、**[フィールド パス]** の値は **Context.Subscription.SubscriptionName** にします。この値は **AuthenticateTo-Azure** Runbook からのコンテキスト出力を指定しています。<br> ![Write-Verbose cmdlet Parameter Data Source](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)    

結果の出力は、サブスクリプションの名前です。<br> ![Test-ChildOutputType Runbook Results](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

[出力の種類] コントロールの動作に関して注意が 1 つあります。 入力と出力のプロパティ ブレードで [出力の種類] フィールドに値を入力する場合は、入力後にコントロールの外側をクリックし、入力内容をコントロールに認識させる必要があります。  

## <a name="message-streams"></a>メッセージ ストリーム
出力ストリームとは異なり、メッセージ ストリームはユーザーに情報を伝えるためのものです。 さまざまな種類の情報向けに複数のメッセージ ストリームがあり、それぞれが異なる方法で Azure Automation で処理されます。

### <a name="warning-and-error-streams"></a>警告およびエラー ストリーム
警告およびエラー ストリームは、Runbook で発生する問題をログ記録するためのものです。 それらは、Runbook が実行されたときにジョブ履歴に書き込まれ、Runbook がテストされたときに Azure Portal の [テスト出力] ウィンドウに表示されます。 既定では、Runbook は警告またはエラーの後も引き続き実行されます。 警告またはエラー時に Runbook を中断するように指定することができます。これを行うには、メッセージを作成する前に、Runbook の[ユーザー設定変数](#preference-variables)を設定します。 たとえば、Runbook を例外の場合と同様、エラーで中断するようにするには、**$ErrorActionPreference** を Stop に設定します。

警告またはエラー メッセージを作成するには、[Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) または [Write-Error](http://technet.microsoft.com/library/hh849962.aspx) コマンドレットを使用します。 アクティビティによってストリームに書き込むことができる場合もあります。

```PowerShell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="verbose-stream"></a>詳細ストリーム
詳細メッセージ ストリームは、Runbook の操作に関する一般情報用です。 Runbook では [デバッグ ストリーム](#Debug) を使用できないため、デバッグ情報には詳細メッセージを使用します。 既定では、公開された Runbook の詳細メッセージはジョブ履歴に格納されません。 詳細メッセージを格納するには、Azure Portal の Runbook の [構成] タブで、公開された Runbook を詳細レコードを記録するように構成します。 ほとんどの場合、パフォーマンス上の理由から、詳細レコードを記録しないというRunbook の既定の設定を保持する必要があります。 このオプションを有効にするのは、Runbook のトラブルシューティングやデバッグをする場合のみです。

[Runbook のテスト時](automation-testing-runbook.md)には、Runbook が詳細レコードを記録するように構成されている場合でも、詳細メッセージは表示されません。 [Runbook のテスト時](automation-testing-runbook.md)に詳細メッセージを表示するには、$VerbosePreference 変数を Continue に設定する必要があります。 この変数が設定されると、Azure Portal の [テスト出力] ウィンドウに詳細メッセージが表示されます。

詳細メッセージを作成するには、 [Write-Verbose](http://technet.microsoft.com/library/hh849951.aspx) コマンドレットを使用します。

```PowerShell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

### <a name="debug-stream"></a>デバッグ ストリーム
デバッグ ストリームは対話ユーザー向けに使用するものであるため、Runbook では使用しません。

## <a name="progress-records"></a>進捗状況レコード
Runbook を (Azure ポータルの Runbook の [構成] タブで) 進捗状況レコードが記録されるように構成すると、各アクティビティの実行前後にレコードがジョブ履歴に書き込まれます。 ほとんどの場合、パフォーマンスを最大化するために、進行状況レコードを記録しないという Runbook の既定の設定を保持する必要があります。 このオプションを有効にするのは、Runbook のトラブルシューティングやデバッグをする場合のみです。 Runbook のテスト時には、Runbook が進歩状況レコードを記録するように構成されている場合でも、進歩状況メッセージは表示されません。

[Write-Progress](http://technet.microsoft.com/library/hh849902.aspx) コマンドレットは、対話ユーザー向けに使用するものであるため、Runbook では無効です。

## <a name="preference-variables"></a>ユーザー設定変数
Windows PowerShell では [ユーザー設定変数](http://technet.microsoft.com/library/hh847796.aspx) を使用して、異なる出力ストリームに送信されるデータへの応答方法を決定します。 これらの変数を Runbook で設定することで、異なるストリームに送信されるデータへの応答方法を制御できます。

次の表は、Runbook で使用できるユーザー設定変数と、それらの有効値と既定値を示しています。 この表には、Runbook で有効な値のみが含まれています。 Windows PowerShell を Azure Automation の外部で使用する場合は、ユーザー設定変数で有効なその他の値があります。

| 可変 | 既定値 | 有効な値 |
|:--- |:--- |:--- |
| WarningPreference |Continue |Stop<br>Continue<br>SilentlyContinue |
| ErrorActionPreference |Continue |Stop<br>Continue<br>SilentlyContinue |
| VerbosePreference |SilentlyContinue |Stop<br>Continue<br>SilentlyContinue |

次の表では、Runbook で有効なユーザー設定変数値の動作を一覧表示します。

| 値 | 動作 |
|:--- |:--- |
| Continue |メッセージを記録し、Runbook の実行を続けます。 |
| SilentlyContinue |メッセージを記録せずに Runbook の実行を続けます。 これはメッセージを無視する効果があります。 |
| Stop |メッセージを記録し、Runbook を中断します。 |

## <a name="retrieving-runbook-output-and-messages"></a>Runbook の出力とメッセージの取得
### <a name="azure-portal"></a>Azure ポータル
Runbook ジョブの詳細は、Azure ポータルの Runbook の [ジョブ] タブで参照できます。 ジョブの [概要] には、ジョブと例外 (発生した場合) の一般情報だけでなく、入力パラメーターと[出力ストリーム](#output-stream)も表示されます。 詳細レコードおよび進捗状況レコードを記録するように Runbook が構成されている場合は、[履歴] に、[詳細ストリーム](#verbose-stream)と[進捗状況レコード](#progress-records)だけでなく、[出力ストリーム](#output-stream)と[警告およびエラー ストリーム](#warning-and-error-streams)からのメッセージも含まれます。

### <a name="windows-powershell"></a>Windows PowerShell
Windows Powershell では、 [Get-AzureAutomationJobOutput](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjoboutput) コマンドレットを使用して、Runbook から出力とメッセージを取得できます。 このコマンドレットにはジョブの ID が必要であり、返すストリームを指定する Stream と呼ばれるパラメーターがあります。 **[Any]** を指定して、ジョブのすべてのストリームを返すことができます。

次の例は、サンプル Runbook を開始し、完了するまで待機します。 完了すると、その出力ストリームがジョブから収集されます。

```PowerShell
$job = Start-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

$doLoop = $true
While ($doLoop) {
  $job = Get-AzureRmAutomationJob -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" -Id $job.JobId
  $status = $job.Status
  $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Output

# For more detailed job output, pipe the output of Get-AzureRmAutomationJobOutput to Get-AzureRmAutomationJobOutputRecord
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Any | Get-AzureRmAutomationJobOutputRecord
``` 

### <a name="graphical-authoring"></a>グラフィカル作成
グラフィカル Runbook では、追加のログ記録はアクティビティ レベルのトレースの形式で使用できます。 トレースには Basic および Detailed の 2 つのレベルがあります。 Basic トレースでは、Runbook での各アクティビティの開始および終了時刻と、アクティビティの試行数および開始時刻など、アクティビティの再試行に関連する情報を確認できます。 Detailed トレースでは、各アクティビティの Basic トレースと入出力のデータを取得します。 現時点では詳細ストリームを使用してトレース レコードが書き込まれるため、トレースを有効にする場合は、詳細ログを有効にする必要があります。 トレースが有効になっているグラフィカル Runbook では、Basic トレースは同じ目的を達成し、より有益であるため、進捗状況レコードを記録する必要はありません。

![グラフィカル作成のジョブ ストリーム ビュー](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

グラフィカル Runbook で詳細ログとトレースを有効にすると、より多くの情報が運用環境のジョブ ストリーム ビューで利用できることが前のスクリーンショットから確認できます。 この追加情報は Runbook での運用環境の問題のトラブルシューティングに不可欠であるため、一般的なプラクティスではなく、その目的に合わせて有効にする必要があります。 トレース レコードは、特に多数存在させることができます。 グラフィカル Runbook のトレースで、Basic または Detailed トレースを構成したかどうかに応じてアクティビティごとに 2 個から 4 個のレコードを取得できます。 トラブルシューティングのための Runbook の進捗状況を追跡するためにこの情報が必要な場合を除き、トレースのオフを保持することが必要な場合があります。

**アクティビティ レベルのトレースを有効にするには、次の手順に従います。**

1. Azure ポータルで、Automation アカウントを開きます。
2. **[プロセス オートメーション]** の **[Runbook]** を選択して、Runbook の一覧を開きます。
3. [Runbook] ページで、Runbook の一覧からグラフィカル Runbook をクリックして選択します。
4. **[設定]** の下の **[ログとトレース]** をクリックします。
5. [ログとトレース] ページで、[詳細レコードの記録] の下の **[オン]** をクリックして詳細ログを有効にし、[アクティビティ レベルのトレース] で、必要なトレースのレベルに基づいてトレース レベルを **[基本]** または **[詳細]** に変更します。<br>
   
   ![グラフィカル作成の[ログ記録とトレース] ブレード](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="microsoft-azure-log-analytics"></a>Microsoft Azure Log Analytics
Automation からは、Runbook ジョブの状態とジョブ ストリームを Log Analytics ワークスペースに送信できます。 Log Analytics では、次のことが可能です。

* Automation ジョブに関する情報を得る 
* Runbook ジョブの状態 (失敗、中断など) に基づいて電子メールまたはアラートをトリガーする 
* ジョブ ストリームをまたぐ高度なクエリを記述する 
* Automation アカウントをまたいでジョブどうしを関連付ける 
* ジョブの履歴を時系列で視覚化する    

Log Analytics との統合を構成して、ジョブ データを収集、操作、および関連付けする方法の詳細については、「[Automation から Log Analytics へのジョブの状態とジョブ ストリームの転送](automation-manage-send-joblogs-log-analytics.md)」を参照してください。

## <a name="next-steps"></a>次の手順
* Runbook の実行、Runbook ジョブの監視方法、その他の技術的な詳細については、 [Runbook ジョブの追跡](automation-runbook-execution.md)
* 子 Runbook を設計および使用する方法については、「 [Azure Automation での子 Runbook](automation-child-runbooks.md)

