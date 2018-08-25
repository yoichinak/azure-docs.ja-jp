---
title: Log Analytics を使用した Azure SQL データ同期の監視 | Microsoft Docs
description: Log Analytics を使用して Azure SQL データ同期を監視する方法について説明します
services: sql-database
ms.date: 04/01/2018
ms.topic: conceptual
ms.service: sql-database
author: allenwux
ms.author: xiwu
manager: craigg
ms.custom: data-sync
ms.openlocfilehash: 3bad71834d11b640bbc3577bf67c10357fad36dc
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39618312"
---
# <a name="monitor-sql-data-sync-with-log-analytics"></a>Log Analytics による SQL データ同期の監視 

SQL データ同期アクティビティ ログをチェックし、エラーおよび警告を検出するには、以前に Azure Portal で SQL データ同期を手動でチェックするか、PowerShell または REST API を使用している必要があります。 データ同期の監視エクスペリエンスを向上させるカスタム ソリューションを構成するには、この記事の手順に従ってください。 このソリューションは、シナリオに合わせてカスタマイズできます。

SQL データ同期の概要については、[Azure SQL データ同期を使用した複数のクラウドおよびオンプレミス データベース間でのデータの同期](sql-database-sync-data.md)に関する記事を参照してください。

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>すべての同期グループのためのダッシュボードの監視 

問題を見つけるために各同期グループのログを個別に調べる必要はなくなりました。 カスタム Log Analytics ビューを使用して、いずれかのサブスクリプションからすべての同期グループを 1 つの場所で監視できます。 このビューには、SQL データ同期の顧客にとって重要な情報が表示されます。

![データ同期の監視ダッシュボード](media/sql-database-sync-monitor-oms/sync-monitoring-dashboard.png)

## <a name="automated-email-notifications"></a>自動化された電子メール通知

Azure Portal で、または PowerShell や REST API を使用してログを手動でチェックする必要はなくなりました。 [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) を使用すると、エラーが発生した場合に確認する必要のあるユーザーの電子メール アドレスに直接送信されるアラートを作成できます。

![データ同期の電子メール通知](media/sql-database-sync-monitor-oms/sync-email-notifications.png)

## <a name="how-do-you-set-up-these-monitoring-features"></a>これらの監視機能を設定する方法 

次のことを実行することで、SQL データ同期のためのカスタム Log Analytics 監視ソリューションを 1 時間以内に実装できます。

3 つのコンポーネントを構成する必要があります。

-   SQL データ同期のログ データを Log Analytics に提供するための PowerShell Runbook。

-   電子メール通知のための Log Analytics アラート。

-   監視のための Log Analytics ビュー。

### <a name="samples-to-download"></a>ダウンロードするサンプル

次の 2 つのサンプルをダウンロードします。

-   [データ同期のログ PowerShell Runbook](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [データ同期の Log Analytics ビュー](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>前提条件

次のものが設定されていることを確認してください。

-   Azure Automation アカウント

-   Log Analytics ワークスペース

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>SQL データ同期のログを取得するための PowerShell Runbook 

Azure Automation でホストされた PowerShell Runbook を使用して SQL データ同期のログ データを取得し、それを Log Analytics に送信します。 サンプル スクリプトが含まれています。 前提条件として、Azure Automation アカウントを持っている必要があります。 次に Runbook を作成し、その実行をスケジュールする必要があります。 

### <a name="create-a-runbook"></a>Runbook を作成する

Runbook の作成の詳細については、「[初めての PowerShell Runbook](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell)」を参照してください。

1.  Azure Automation アカウントで、[プロセスの自動化] の下にある **[Runbook]** タブを選択します。

2.  [Runbook] ページの左上にある **[Runbook の追加]** を選択します。

3.  **[既存の Runbook のインポート]** を選択します。

4.  **[Runbook file] (Runbook ファイル)** で、指定された `DataSyncLogPowerShellRunbook` ファイルを使用します。 **[Runbook type] (Runbook の種類)** を `PowerShell` として設定します。 この Runbook に名前を付けます。

5.  **作成**を選択します。 これで Runbook が作成されました。

6.  Azure Automation アカウントで、[共有リソース] の下にある **[変数]** タブを選択します。

7.  [変数] ページで **[変数の追加]** を選択します。 Runbook の最後の実行時間を格納するための変数を作成します。 複数の Runbook がある場合は、Runbook ごとに 1 つの変数が必要です。

8.  変数名を `DataSyncLogLastUpdatedTime` として設定し、その型を [DateTime] として設定します。

9.  Runbook を選択し、ページの上部にある [編集] ボタンをクリックします。

10. アカウントや SQL データ同期の構成に必要な変更を行います。 (詳細については、サンプル スクリプトを参照してください)。

    1.  Azure の情報。

    2.  同期グループの情報。

    3.  OMS の情報。 これらの情報は、[OMS ポータル] | [設定] | [Connected Sources] (接続されたソース) で見つけます。 Log Analytics へのデータ送信の詳細については、「[Send data to Log Analytics with the HTTP Data Collector API (HTTP データ コレクター API を使用した Log Analytics へのデータの送信 (プレビュー))](../log-analytics/log-analytics-data-collector-api.md)」を参照してください。

11. [テスト] ペインで Runbook を実行します。 それが成功したことを確認します。

    エラーが発生した場合は、最新の PowerShell モジュールがインストールされていることを確認してください。 最新の PowerShell モジュールは、Automation アカウントの**モジュール ギャラリー**でインストールできます。

12. **[発行]** をクリックします。

### <a name="schedule-the-runbook"></a>Runbook のスケジュールを設定する

Runbook のスケジュールを設定するには:

1.  Runbook で、[リソース] の下にある **[スケジュール]** タブを選択します。

2.  [スケジュール] ページで **[スケジュールの追加]** を選択します。

3.  **[スケジュールを Runbook にリンクします]** を選択します。

4.  **[新しいスケジュールを作成します]** を選択します。

5.  **[繰り返し]** を [繰り返し] に設定し、必要な間隔を設定します。 ここ (このスクリプト) と OMS で同じ間隔を使用します。

6.  **作成**を選択します。

### <a name="check-the-automation"></a>オートメーションをチェックする

オートメーションが期待どおりに実行されているかどうかを監視するには、Automation アカウントの **[概要]** で、**[監視]** の下にある **[Job Statistics] (ジョブの統計)** ビューを見つけます。 見やすくするために、これをダッシュボードにピン留めします。 Runbook の実行が成功すると [完了] と表示され、実行が失敗すると [失敗] と表示されます。

## <a name="create-a-log-analytics-reader-alert-for-email-notifications"></a>電子メール通知のための Log Analytics リーダー アラートの作成

Log Analytics を使用するアラートを作成するには、次のことを実行します。 前提条件として、Log Analytics ワークスペースにリンクされた Log Analytics を使用している必要があります。

1.  OMS ポータルで、**[ログ検索]** を選択します。

2.  選択した間隔に含まれる同期グループごとのエラーおよび警告を選択するためのクエリを作成します。 例: 

    `Type=DataSyncLog\_CL LogLevel\_s!=Success| measure count() by SyncGroupName\_s interval 60minute`

3.  クエリを実行した後、**[アラート]** を示すベルを選択します。

4.  **[Generate alert based on] (アラートを生成する基準)** で、**[Metric Measurement] (メトリック測定)** を選択します。

    1.  [Aggregate Value] (集計値) を **[より大きい]** に設定します。

    2.  **[より大きい]** の後に、通知を受信するまでに経過するしきい値を入力します。 データ同期では一時的なエラーが予測されます。ノイズを減らすために、しきい値を 5 に設定します。

5.  **[アクション]** で、**[電子メール通知]** を [はい] に設定します。 目的の電子メール受信者を入力します。

6.  **[Save]** をクリックします。 これで、指定された受信者がエラー発生時に電子メール通知を受信するようになりました。

## <a name="create-an-oms-view-for-monitoring"></a>監視のための OMS ビューの作成

この手順では、指定されたすべての同期グループを視覚的に監視するための OMS ビューを作成します。 このビューには、いくつかのコンポーネントが含まれています。

-   概要タイル。これには、すべての同期グループのエラー、成功、および警告の数が表示されます。

-   すべての同期グループのタイル。これには、同期グループごとのエラーおよび警告の数が表示されます。 このタイルには、問題が発生していないグループは表示されません。

-   同期グループごとのタイル。これには、エラー、成功、および警告の数と、最新のエラー メッセージが表示されます。

OMS ビューを構成するには、次のことを実行します。

1.  OMS ホーム ページで、左側にあるプラスを選択して**ビュー デザイナー**を開きます。

2.  ビュー デザイナーの上部のバーにある **[インポート]** を選択します。 次に、"DataSyncLogOMSView" サンプル ファイルを選択します。

3.  このサンプル ビューは、2 つの同期グループを管理するためのものです。 このビューをシナリオに合わせて編集します。 **[編集]** をクリックし、次の変更を行います。

    1.  必要に応じて、ギャラリーから新しい [Donut & List] (ドーナツとリスト) オブジェクトを作成します。

    2.  各タイルで、自分の情報を使用してクエリを更新します。

        1.  各タイルで、必要に応じて TimeStamp_t 間隔を変更します。

        2.  同期グループごとのタイルで、同期グループの名前を更新します。

    3.  各タイルで、必要に応じてタイトルを更新します。

4.  **[保存]** をクリックすると、ビューの準備が整います。

## <a name="cost-of-this-solution"></a>このソリューションのコスト

ほとんどの場合、このソリューションは無料です。

**Azure Automation:** 使用法によっては、Azure Automation アカウントのコストが発生する可能性があります。 1 か月あたり、ジョブ実行時間の最初の 500 分は無料です。 ほとんどの場合、このソリューションは 1 か月あたり 500 分未満を使用すると予測されます。 料金が発生しないようにするには、2 時間以上の間隔で実行されるように Runbook のスケジュールを設定します。 詳細については、「[Automation pricing (Automation の価格)](https://azure.microsoft.com/pricing/details/automation/)」を参照してください。

**Log Analytics:** 使用法によっては、Log Analytics に関連したコストが発生する可能性があります。 Free レベルには、1 日あたり 500 MB の取り込まれたデータが含まれます。 ほとんどの場合、このソリューションは 1 日あたり 500 MB 未満を取り込むと予測されます。 使用量を減らすには、Runbook に含まれている失敗のみのフィルター処理を使用します。 1 日あたりの使用量が 500 MB を超える場合は、制限に達して分析が停止してしまうリスクを回避するために、有料レベルにアップグレードしてください。 詳細については、「[Log Analytics pricing (Log Analytics の価格)](https://azure.microsoft.com/pricing/details/log-analytics/)」を参照してください。

## <a name="code-samples"></a>コード サンプル

この記事で説明されているサンプル コードは、次の場所からダウンロードします。

-   [データ同期のログ PowerShell Runbook](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [データ同期の Log Analytics ビュー](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

## <a name="next-steps"></a>次の手順
SQL データ同期の詳細については、以下を参照してください。

-   [Sync data across multiple cloud and on-premises databases with Azure SQL Data Sync (Azure SQL データ同期を使用した複数のクラウドおよびオンプレミス データベースにまたがるデータの同期)](sql-database-sync-data.md)
-   [Azure SQL データ同期のセットアップ](sql-database-get-started-sql-data-sync.md)
-   [Azure SQL データ同期のベスト プラクティス](sql-database-best-practices-data-sync.md)
-   [Troubleshoot issues with Azure SQL Data Sync (Azure SQL データ同期に関する問題のトラブルシューティング)](sql-database-troubleshoot-data-sync.md)

-   SQL データ同期を構成する方法を示す完全な PowerShell の例
    -   [PowerShell を使用した複数の Azure SQL データベース間の同期](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [PowerShell を使用した Azure SQL Database と SQL Server オンプレミス データベース間の同期](scripts/sql-database-sync-data-between-azure-onprem.md)

SQL Database の詳細については、以下を参照してください。

-   [SQL Database の概要](sql-database-technical-overview.md)
-   [データベースのライフサイクル管理](https://msdn.microsoft.com/library/jj907294.aspx)
