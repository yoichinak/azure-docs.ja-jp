---
title: Azure Log Analytics でのパフォーマンス カウンターの収集と分析 | Microsoft Docs
description: Log Analytics では、Windows および Linux のエージェントのパフォーマンスを分析するためにパフォーマンス カウンターが収集されます。  この記事では、Windows および Linux の両方のエージェントでのパフォーマンス カウンターの収集の構成方法、ワークスペースに格納されたそれらの詳細、および Azure Portal でのそれらの分析方法について説明します。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 20e145e4-2ace-4cd9-b252-71fb4f94099e
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/19/2017
ms.author: magoedte
ms.component: na
ms.openlocfilehash: b23c170e557d019abf2b9aab8edcb74728bc872d
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128777"
---
# <a name="windows-and-linux-performance-data-sources-in-log-analytics"></a>Log Analytics での Windows および Linux のパフォーマンス データ ソース
Windows および Linux のパフォーマンス カウンターから、ハードウェア コンポーネント、オペレーティング システム、およびアプリケーションのパフォーマンスに関する情報が得られます。  Log Analytics は、長期的な分析とレポートのためにパフォーマンス データを集計することに加えて、ほぼリアルタイム (NRT) 分析のために頻繁な間隔でパフォーマンス カウンターを収集することができます。

![パフォーマンス カウンター](media/log-analytics-data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>パフォーマンス カウンターの構成
パフォーマンス カウンターは、[Log Analytics の [設定] の [データ] メニュー](log-analytics-data-sources.md#configuring-data-sources)から構成します。

新しい Log Analytics ワークスペースの Windows または Linux パフォーマンス カウンターを初めて構成する場合は、いくつかの一般的なカウンターをすばやく作成するためのオプションが表示されます。  それぞれのオプションの横には、チェック ボックスが表示されます。  最初に作成するカウンターがオンになっていることを確認し、 **[Add the selected performance counters (選択されたパフォーマンス カウンターを追加する)]** をクリックします。

Windows のパフォーマンス カウンターの場合、パフォーマンス カウンターごとに特定のインスタンスを選択できます。 Linux のパフォーマンス カウンターの場合、各カウンターに対して選択したインスタンスが、そのすべての子カウンターに適用されます。 次の表は、Linux と Windows の両方のパフォーマンス カウンターで利用できる共通のインスタンスを示しています。

| インスタンス名 | 説明 |
| --- | --- |
| \_Total |すべてのインスタンスの合計 |
| \* |すべてのインスタンス |
| (/&#124;/var) |/ または /var という名前のインスタンスと一致します。 |

### <a name="windows-performance-counters"></a>Windows パフォーマンス カウンター

![Windows パフォーマンス カウンターの構成](media/log-analytics-data-sources-performance-counters/configure-windows.png)

収集する新しい Windows パフォーマンス カウンターを追加するには、次の手順を実行します。

1. *<オブジェクト (インスタンス)>\<カウンター>* の形式で、テキスト ボックスにカウンターの名前を入力します。  入力を開始すると、入力内容に一致する一般的なカウンターの一覧が表示されます。  一覧からカウンターを選択するか、または独自の名前を入力することができます。  *<オブジェクト>\<カウンター>* を指定して、特定のカウンターのすべてのインスタンスを返すこともできます。  

    名前付きインスタンスから SQL Server パフォーマンス カウンターを収集するとき、すべての名前付きインスタンス カウンターの名前が *MSSQL$* から始まり、その後ろにインスタンスの名前が付きます。  たとえば、名前付き SQL インスタンス INST2 のデータベース パフォーマンス オブジェクトからログ キャッシュ ヒット率カウンターを収集するには、`MSSQL$INST2:Databases(*)\Log Cache Hit Ratio` と指定します。

2. **+** をクリックするか、または **Enter** キーを押して、一覧にカウンターを追加します。
3. カウンターを追加すると、その **[サンプルの間隔]** には既定値の 10 秒が使用されます。  収集されたパフォーマンス データのストレージ要件を削減する場合は、この値を最大 1800 秒 (30 分) まで高く変更できます。
4. カウンターの追加を完了したら、画面の上部にある **[保存]** ボタンをクリックして、構成を保存します。

### <a name="linux-performance-counters"></a>Linux パフォーマンス カウンター

![Linux パフォーマンス カウンターの構成](media/log-analytics-data-sources-performance-counters/configure-linux.png)

収集する新しい Linux パフォーマンス カウンターを追加するには、次の手順を実行します。

1. 既定では、すべての構成変更はすべてのエージェントに自動的にプッシュされます。  Linux エージェントの場合、構成ファイルが Fluentd データ コレクターに送信されます。  各 Linux エージェントでこのファイルを手動で変更する場合、*[Apply below configuration to my Linux machines (Linux コンピューターに以下の構成を適用する)]* チェック ボックスをオフにして、以下のガイダンスに従います。
2. *<オブジェクト (インスタンス)>\<カウンター>* の形式で、テキスト ボックスにカウンターの名前を入力します。  入力を開始すると、入力内容に一致する一般的なカウンターの一覧が表示されます。  一覧からカウンターを選択するか、または独自の名前を入力することができます。  
3. **+** をクリックするか、または **Enter** キーを押して、オブジェクトのその他のカウンターの一覧にカウンターを追加します。
4. オブジェクトのすべてのカウンターは、同じ **[サンプルの間隔]** を使用します。  既定値は 10 秒です。  収集されたパフォーマンス データのストレージ要件を削減したい場合は、1,800 秒 (30 分) を上限としてこの値を増やしてください。
5. カウンターの追加を完了したら、画面の上部にある **[保存]** ボタンをクリックして、構成を保存します。

#### <a name="configure-linux-performance-counters-in-configuration-file"></a>構成ファイルで Linux のパフォーマンス カウンターを構成する
Azure Portal を使用して Linux のパフォーマンス カウンターを構成する代わりに、Linux エージェントで構成ファイルを編集することもできます。  収集するパフォーマンス メトリックは、**/etc/opt/microsoft/omsagent/\<workspace id\>/conf/omsagent.conf** の構成によって制御されます。

収集するパフォーマンス メトリックの各オブジェクト (カテゴリ) は、構成ファイルの中で単一の `<source>` 要素として定義する必要があります。 次の構文形式に従って記述してください。

    <source>
      type oms_omi  
      object_name "Processor"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 30s
    </source>


この要素のパラメーターを次の表に示します。

| parameters | 説明 |
|:--|:--|
| object\_name | コレクションのオブジェクト名。 |
| instance\_regex |  収集するインスタンスを定義する*正規表現*。 すべてのインスタンスは、 `.*` という値で指定します。 \_Total インスタンスのみを対象にプロセッサ メトリックを収集するには、`_Total` を指定します。 crond または sshd のインスタンスのみを対象にプロセス メトリックを収集するには、「 `(crond\|sshd)`」と指定します。 |
| counter\_name\_regex | 収集する (オブジェクトの) カウンターを定義する*正規表現*。 オブジェクトのすべてのカウンターを収集するには、「 `.*`」と指定します。 メモリ オブジェクトを対象にスワップ領域カウンターを収集するには、たとえば `.+Swap.+` のように指定できます。 |
| interval | オブジェクトのカウンターを収集する頻度。 |


次の表は、構成ファイルで指定できるオブジェクトとカウンターを一覧表示しています。  「[Log Analytics で Linux アプリケーションのパフォーマンス カウンターを収集する](log-analytics-data-sources-linux-applications.md)」に記載されているとおり、特定のアプリケーションで使用できる追加のカウンターがあります。

| オブジェクト名 | カウンター名 |
|:--|:--|
| 論理ディスク | % Free Inodes |
| 論理ディスク | % Free Space |
| 論理ディスク | % Used Inodes |
| 論理ディスク | % Used Space |
| 論理ディスク | Disk Read Bytes/sec  |
| 論理ディスク | Disk Reads/sec  |
| 論理ディスク | Disk Transfers/sec |
| 論理ディスク | Disk Write Bytes/sec |
| 論理ディスク | Disk Writes/sec |
| 論理ディスク | Free Megabytes |
| 論理ディスク | Logical Disk Bytes/sec |
| メモリ | % Available Memory |
| メモリ | % Available Swap Space |
| メモリ | % Used Memory |
| メモリ | % Used Swap Space |
| メモリ | Available MBytes Memory |
| メモリ | Available MBytes Swap |
| メモリ | Page Reads/sec |
| メモリ | Page Writes/sec |
| メモリ | Pages/sec |
| メモリ | Used MBytes Swap Space |
| メモリ | Used Memory MBytes |
| ネットワーク | Total Bytes Transmitted |
| ネットワーク | Total Bytes Received |
| ネットワーク | Total Bytes |
| ネットワーク | Total Packets Transmitted |
| ネットワーク | Total Packets Received |
| ネットワーク | Total Rx Errors |
| ネットワーク | Total Tx Errors |
| ネットワーク | Total Collisions |
| 物理ディスク | Avg.Disk sec/Read |
| 物理ディスク | Avg.Disk sec/Transfer |
| 物理ディスク | Avg.Disk sec/Write |
| 物理ディスク | Physical Disk Bytes/sec |
| Process | Pct Privileged Time |
| Process | Pct User Time |
| Process | Used Memory kBytes |
| Process | Virtual Shared Memory |
| プロセッサ | % DPC Time |
| プロセッサ | % Idle Time |
| プロセッサ | % Interrupt Time |
| プロセッサ | % IO Wait Time |
| プロセッサ | % Nice Time |
| プロセッサ | % Privileged Time |
| プロセッサ | % Processor Time |
| プロセッサ | % User Time |
| システム | Free Physical Memory |
| システム | Free Space in Paging Files |
| システム | Free Virtual Memory |
| システム | 処理 |
| システム | Size Stored In Paging Files |
| システム | Uptime |
| システム | ユーザー |


パフォーマンス メトリックの既定の構成を次に示します。

    <source>
      type oms_omi
      object_name "Physical Disk"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 5m
    </source>

    <source>
      type oms_omi
      object_name "Logical Disk"
      instance_regex ".*
      counter_name_regex ".*"
      interval 5m
    </source>

    <source>
      type oms_omi
      object_name "Processor"
      instance_regex ".*
      counter_name_regex ".*"
      interval 30s
    </source>

    <source>
      type oms_omi
      object_name "Memory"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 30s
    </source>

## <a name="data-collection"></a>データ収集
Log Analytics は、カウンターがインストールされているすべてのエージェントについて、指定されたサンプル間隔ですべての指定されたパフォーマンス カウンターを収集します。  データは集計されず、サブスクリプションで指定した期間、すべてのログ検索ビューで生データを利用できます。

## <a name="performance-record-properties"></a>パフォーマンス レコードのプロパティ
パフォーマンス レコードには、 **Perf** の型と、次の表に示すプロパティがあります。

| プロパティ | 説明 |
|:--- |:--- |
| Computer |イベントが収集されたコンピューター。 |
| CounterName |パフォーマンス カウンターの名前 |
| CounterPath |\\\\\<コンピューター>\\オブジェクト(インスタンス)\\カウンターの形式のカウンターの完全パス。 |
| CounterValue |カウンターの数値。 |
| InstanceName |イベント インスタンスの名前。  インスタンスがない場合は空白です。 |
| ObjectName |パフォーマンス オブジェクトの名前 |
| SourceSystem |データが収集されたエージェントの種類。 <br><br>OpsManager – Windows エージェント、直接接続または SCOM <br> Linux – すべての Linux エージェント  <br> AzureStorage – Azure 診断 |
| TimeGenerated |データがサンプリングされた日付と時刻。 |

## <a name="sizing-estimates"></a>サイズ見積もり
 10 秒間隔での特定のカウンターの収集量の大まかな見積もり値は、インスタンスごとに 1 日あたり約 1 MB です。  次の式で、特定のカウンターのストレージ要件を見積もることができます。

    1 MB x (number of counters) x (number of agents) x (number of instances)

## <a name="log-searches-with-performance-records"></a>パフォーマンス レコードでのログ検索
次の表は、パフォーマンス レコードを取得するログ検索のさまざまな例をまとめたものです。

| クエリ | 説明 |
|:--- |:--- |
| Perf |すべてのパフォーマンス データ |
| Perf &#124; where Computer == "MyComputer" |特定のコンピューターからのすべてのパフォーマンス データ |
| Perf &#124; where CounterName == "Current Disk Queue Length" |特定のカウンターに関するすべてのパフォーマンス データ |
| Perf &#124; where ObjectName == "Processor" and CounterName == "% Processor Time" and InstanceName == "_Total" &#124; summarize AVGCPU = avg(Average) by Computer |コンピューター全体の平均 CPU 使用率 |
| Perf &#124; where CounterName == "% Processor Time" &#124; summarize AggregatedValue = max(Max) by Computer |コンピューター全体の最大 CPU 使用率 |
| Perf &#124; where ObjectName == "LogicalDisk" and CounterName == "Current Disk Queue Length" and Computer == "MyComputerName" &#124; summarize AggregatedValue = avg(Average) by InstanceName |特定のコンピューターのインスタンス全体における現在のディスク キューの長さの平均 |
| Perf &#124; where CounterName == "DiskTransfers/sec" &#124; summarize AggregatedValue = percentile(Average, 95) by Computer |コンピューター全体のディスク転送数/秒の 95 パーセンタイル |
| Perf &#124; where CounterName == "% Processor Time" and InstanceName == "_Total" &#124; summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1h), Computer |全コンピューターの CPU 使用率の平均値 (1 時間ごと) |
| Perf &#124; where Computer == "MyComputer" and CounterName startswith_cs "%" and InstanceName == "_Total" &#124; summarize AggregatedValue = percentile(CounterValue, 70) by bin(TimeGenerated, 1h), CounterName | 特定のコンピューターの各パーセント (%) カウンターの 70 パーセンタイル (1 時間ごと) |
| Perf &#124; where CounterName == "% Processor Time" and InstanceName == "_Total" and Computer == "MyComputer" &#124; summarize ["min(CounterValue)"] = min(CounterValue), ["avg(CounterValue)"] = avg(CounterValue), ["percentile75(CounterValue)"] = percentile(CounterValue, 75), ["max(CounterValue)"] = max(CounterValue) by bin(TimeGenerated, 1h), Computer |特定のコンピューターの CPU 使用率の平均、最小、最大、75 パーセンタイル (1 時間ごと) |
| Perf &#124; where ObjectName == "MSSQL$INST2:Databases" and InstanceName == "master" | 名前付き SQL インスタンス INST2 のマスター データベースのデータベース パフォーマンス オブジェクトのすべてのパフォーマンス データ。  




## <a name="next-steps"></a>次の手順
* MySQL および Apache HTTP Server を含む [Linux アプリケーションからパフォーマンス カウンターを収集します](log-analytics-data-sources-linux-applications.md)。
* [ログ検索](log-analytics-log-searches.md) について学習し、データ ソースとソリューションから収集されたデータを分析します。  
* 詳細な視覚化および分析を行うために、収集されたデータを [Power BI](log-analytics-powerbi.md) にエクスポートします。
