---
title: Azure Batch のメトリック、アラート、および診断ログ | Microsoft Docs
description: プールやタスクなど Azure Batch アカウント リソースの診断ログ イベントを記録して分析します。
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 04/05/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 54034b9a851fc6f06f97be9cfd5f261465bad455
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248264"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Batch の診断の評価と監視用のメトリック、アラート、およびログ

この記事では、[Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) の機能を使用して、Batch アカウントを監視する方法を説明します。 Azure Monitor は、Batch アカウント内のリソースの[メトリック](../monitoring-and-diagnostics/monitoring-overview-metrics.md)と[診断ログ](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)を収集します。 このデータを収集し、さまざまな方法で使用して、Batch アカウントの監視と問題の診断を行います。 [メトリック アラート](../monitoring-and-diagnostics/monitoring-overview-alerts.md#alerts-on-azure-monitor-data)を構成して、メトリックが指定した値に達したときに通知を受信するように構成することもできます。 

## <a name="batch-metrics"></a>Batch メトリック

メトリックは、Azure リソースによって生成され、Azure Monitor サービスによって使用される Azure テレメトリ データです (パフォーマンス カウンターとも呼ばれます)。 Batch アカウントのメトリックスの例には、プール作成イベント、優先順位の低いノードの数、タスク完了イベントなどがあります。 

[サポートされる Batch メトリックの一覧](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftbatchbatchaccounts)を参照してください。

メトリックは、

* 各 Batch アカウントで追加構成なしで既定で有効になります。
* 1 分ごとに生成されます。
* 自動的に保存されることはありませんが、30 日間のローリング履歴があります。 [診断ログ](#work-with-diagnostic-logs)の一部として、アクティビティ メトリックを保存できます。

### <a name="view-metrics"></a>メトリックを表示する

Azure Portal で Batch アカウントのメトリックを表示します。 アカウントの **[概要]** ページには、既定でキー ノード、コア、およびタスク メトリックが表示されます。 

すべての Batch アカウントのメトリックを表示するには: 

1. ポータルで、**[すべてのサービス]** > **[Batch アカウント]** をクリックし、Batch アカウントの名前をクリックします。
2. **[監視]** で **[メトリック]** をクリックします。
3. 1 つまたは複数のメトリックを選択します。 必要に応じて、**[サブスクリプション]**、**[リソース グループ]**、**[リソースの種類]**、および **[リソース]** のドロップダウンを使用して、追加のリソース メトリックを選択します。

    ![Batch メトリック](media/batch-diagnostics/metrics-portal.png)

メトリックをプログラムで取得するには、Azure Monitor API を使用します。 例については、「[Retrieve Azure Monitor metrics with .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/)」(.NET を使用した Azure Monitor メトリックの取得) を参照してください。

## <a name="batch-metric-alerts"></a>Batch メトリック アラート

必要に応じて、指定したメトリックの値が割り当てられたしきい値に通過したときにトリガーされる、リアルタイムに近い "*メトリック アラート*" を構成します。 選択した[通知](../monitoring-and-diagnostics/insights-alerts-portal.md)がアラートによって生成されるのは、アラートが "アクティブ化済み" になったとき (しきい値を通過してアラートの条件を満たすようになったとき) と、"解決済み" になったとき (しきい値を再び通過して条件を満たさなくなったとき) です。 

たとえば、優先順位の低いコアの数が特定のレベルに落ちた場合に生成されるメトリック アラートを構成して、プールの構成を調整できるようにします。

ポータルでメトリック アラートを構成するには:

1. **[すべてのサービス]** > **[Batch アカウント]** の順にクリックし、Batch アカウントの名前をクリックします。
2. **[監視]** で、**[アラート ルール]** > **[メトリック アラートの追加]** をクリックします。
3. メトリック、アラート条件 (メトリックが期間中に特定の値を超えた場合など)、および 1 つ以上の通知を選択します。

リアルタイムに近い通知は、[REST API]() を使用して構成することもできます。 詳細については、「[Azure Portal で使用できる Azure サービスの新しいメトリック アラート](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md)」を参照してください。
## <a name="batch-diagnostics"></a>Batch 診断

診断ログには、Azure リソースによって生成された、各リソースの操作を記述する情報が含まれます。 Batch では、次のログを収集できます。

* プールやタスクなどの個々の Batch リソースの存続期間中に Azure Batch サービスによって生成された**サービス ログ** イベント。 

* アカウント レベルの**メトリック** ログ。 

診断ログの収集を有効にする設定は、既定では有効になりません。 監視する Batch アカウントごとに診断ログを明示的に有効にする必要があります。

### <a name="log-destinations"></a>ログの保存先

一般的なシナリオは、ログの保存先として Azure Storage アカウントを選択することです。 Azure Storage にログを格納するには、ログの収集を有効にする前にアカウントを作成します。 ストレージ アカウントを Batch アカウントに関連付けた場合は、ログの保存先としてそのアカウントを選択できます。 

診断ログのその他の保存先:

* Batch 診断ログ イベントを [Azure Event Hub](../event-hubs/event-hubs-what-is-event-hubs.md) にストリーミングします。 Event Hubs は、毎秒数百万のイベントを取り込み、任意のリアルタイム分析プロバイダーを使用して変換および格納できます。 

* 診断ログを [Log Analytics](../log-analytics/log-analytics-overview.md) に送信して、Operations Management Suite (OMS) ポータルで分析したり、Power BI または Excel で分析するためにエクスポートしたりできます。

> [!NOTE]
> Azure サービスで診断ログ データの格納または処理を行うには、追加料金が発生することがあります。 
>

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Batch 診断ログの収集を有効にする

1. ポータルで、**[すべてのサービス]** > **[Batch アカウント]** をクリックし、Batch アカウントの名前をクリックします。
2. **[監視]** で、**[診断ログ]** > **[診断の有効化]** をクリックします。
3. **[診断設定]** に、設定の名前を入力し、ログの保存先 (既存の Storage アカウント、Event Hub、または Log Analytics) を選択します。 **[ServiceLog]** と **[AllMetrics]** のいずれかまたは両方を選択します。

    ストレージ アカウントを選択するときに、必要に応じて保持ポリシーを設定します。 リテンション期間の日数を指定しない場合は、ストレージ アカウントが有効である間、データは保持されます。

4. **[Save]** をクリックします。

    ![Batch 診断](media/batch-diagnostics/diagnostics-portal.png)

ログの収集を有効にするためのオプションとして、他に次のオプションがあります。ポータルで Azure Monitor を使用して診断設定を構成する。[Resource Manager テンプレート](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md)を使用する。または Azure PowerShell または Azure CLI を使用する。 「[Azure リソースからのログ データの収集と使用](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-diagnostic-logs)」を参照してください。


### <a name="access-diagnostics-logs-in-storage"></a>ストレージ内の診断ログにアクセスする

Batch 診断ログをストレージ アカウント内にアーカイブする場合は、関連するイベントが発生するとすぐに、ストレージ アカウント内にストレージ コンテナーが作成されます。 次の名前付けパターンに従って、BLOB が作成されます。

```
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
例:

```
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```
各 PT1H.json BLOB ファイルには、BLOB の URL で指定された時間 (例: h = 12) 内に発生した JSON 形式のイベントが含まれます。 現在の時間内にイベントが発生すると、PT1H.json ファイルにイベントが追加されます。 分の値 (m = 00) は常に 00 です。診断ログ イベントが個々の BLOB に 1 時間ごとに分類されるためです。 (時刻はすべて UTC 形式です)。


ストレージ アカウント内の診断ログのスキーマの詳細については、[Azure 診断ログのアーカイブ](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account)に関する記事を参照してください。

ストレージ アカウント内のログにプログラムでアクセスするには、Storage API を使用します。 

### <a name="service-log-events"></a>サービス ログ イベント
Azure Batch サービス ログが収集される場合、そのログには、プールやタスクなどの個々の Batch リソースの存続期間中に Azure Batch サービスによって生成されたイベントが含まれます。 Batch によって生成された各イベントが、JSON 形式で記録されます。 たとえば、次に示すのはサンプルの**プール作成イベント**の本文です。

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "5",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicatedComputeNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

現在、Batch サービスは、次のサービス ログ イベントを出力します。 この記事の最終更新の後でイベントが追加される場合があるため、この一覧は包括的でない可能性があります。

| **サービス ログ イベント** |
| --- |
| [プール作成](batch-pool-create-event.md) |
| [プール削除の開始](batch-pool-delete-start-event.md) |
| [プール削除の完了](batch-pool-delete-complete-event.md) |
| [プールのサイズ変更の開始](batch-pool-resize-start-event.md) |
| [プールのサイズ変更の完了](batch-pool-resize-complete-event.md) |
| [タスク開始](batch-task-start-event.md) |
| [タスク完了](batch-task-complete-event.md) |
| [タスク失敗](batch-task-fail-event.md) |



## <a name="next-steps"></a>次の手順

* Batch ソリューションの構築に使用できる [Batch API とツール](batch-apis-tools.md)について学習します。
* [Batch ソリューション](monitoring-overview.md)の詳細を確認します。
