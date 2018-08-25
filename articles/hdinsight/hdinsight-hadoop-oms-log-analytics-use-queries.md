---
title: Azure Log Analytics でクエリを実行して Azure HDInsight クラスターを監視する
description: Azure Log Analytics でクエリを実行し、HDInsight クラスターで実行されているジョブを監視する方法を説明します。
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: jasonh
ms.openlocfilehash: 2d2de3c2e2b291ec1f5ad170350f19e9e0582eaa
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39609313"
---
# <a name="query-azure-log-analytics-to-monitor-hdinsight-clusters"></a>Azure Log Analytics でクエリを実行して HDInsight クラスターを監視する

Azure Log Analytics を使用して Azure HDInsight クラスターを監視する基本的なシナリオを説明します。

* [HDInsight クラスターのメトリックを分析する](#analyze-hdinsight-cluster-metrics)
* [特定のログ メッセージの検索する](#search-for-specific-log-messages)
* [イベント アラートを作成する](#create-alerts-for-tracking-events)

## <a name="prerequisites"></a>前提条件

* Azure Log Analytics を使用するように HDInsight クラスターを構成し、HDInsight クラスター固有の Log Analytics 管理ソリューションをワークスペースに追加しておく必要があります。 手順については、「[Log Analytics を使用して Azure HDInsight クラスターを監視する](hdinsight-hadoop-oms-log-analytics-tutorial.md)」をご覧ください。

## <a name="analyze-hdinsight-cluster-metrics"></a>HDInsight クラスターのメトリックを分析する

HDInsight クラスターの特定のメトリックを検索する方法を説明します。

1. Azure Portal から HDInsight クラスターに関連付けられた OMS ワークスペースを開きます。
2. **[ログ検索]** タイルを選択します。
3. 検索ボックスに、Azure Log Analytics を使用するように構成したすべての HDInsight クラスターで使用できるすべてのメトリックを検索する次のクエリを入力して、**[実行]** を選択します。

        search *

    ![すべてのメトリックを検索](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "すべてのメトリックを検索")

    出力は次のようになります。

    ![すべてのメトリックの検索結果](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "すべてのメトリックの検索結果")

5. 左側のウィンドウの **[種類]** の下で、詳しく調べたいメトリックを選択して **[適用]** を選択します。 次のスクリーンショットは `metrics_resourcemanager_queue_root_default_CL` 種類を選択した場合を示しています。

    > [!NOTE]
    > 目的のメトリックが表示されていない場合は、**[[+] 増やす]** ボタンを選択します。 また、**[適用]** ボタンはリストの一番下にあるので、このボタンを表示するには下までスクロールする必要があります。

    テキスト ボックスのクエリが、下記のスクリーンショットの赤枠で示された内容に変わっていることを確認してください。

    ![特定のメトリックの検索](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "特定のメトリックの検索")

6. この特定のメトリックのデータを詳しく調べるには: たとえば、次のクエリを使用すると、クラスター名ごとに、10 分間に使用されたリソースの平均値に基づいて出力結果を調整することができます。

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)

7. 使用されたリソースの平均値に基づいて結果を調整する代わりに、次のクエリを使用すると、10 分間で最大 (および 90 パーセンタイルと 95 パーセンタイル) のリソースが使用された時刻に基づいて結果を調整できます。

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)

## <a name="search-for-specific-log-messages"></a>特定のログ メッセージを検索する

特定の時間枠中のエラー メッセージを検索する方法を説明します。 ここで説明する手順は、目的のエラー メッセージを探すためのひとつの方法にすぎません。 使用可能な任意のプロパティを使用して、目的のエラーを探すことができます。

1. Azure Portal から HDInsight クラスターに関連付けられた OMS ワークスペースを開きます。
2. **[ログ検索]** タイルを選択します。
3. Azure Log Analytics を使用するように構成したすべての HDInsight クラスターのすべてのエラー メッセージを検索する次のクエリを入力して、**[実行]** を選択します。 

         search "Error"

    出力は次のようになります。

    ![すべてのエラーの検索結果](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "すべてのエラーの検索結果")

4. 左側のウィンドウの **[種類]** カテゴリの下で、詳しく調べたいエラーの種類を選択して、**[適用]** を選択します。  選択した種類のエラーのみが表示されるように結果が調整されたことを確認してください。
5. 左側のウィンドウにあるオプションを使って、このエラーの一覧を詳しく調べることができます。 例: 

    - 特定のワーカー ノードからのエラー メッセージを表示するには:

        ![特定のエラーの検索結果](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "特定のエラーの検索結果")

    - 特定の時刻に発生したエラーを表示するには:

        ![特定のエラーの検索結果](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "特定のエラーの検索結果")

6. 特定のエラーを表示するには: **[[+] 詳細表示]** を選択すると、実際のエラー メッセージを確認できます。

    ![特定のエラーの検索結果](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "特定のエラーの検索結果")

## <a name="create-alerts-for-tracking-events"></a>イベント追跡用のアラートを作成する

アラートを作成する最初の手順は、アラートをトリガーする基準となるクエリを作成することです。 アラートの作成には任意のクエリを使用できます。

1. Azure Portal から HDInsight クラスターに関連付けられた Log Analytics ワークスペースを開きます。
2. **[ログ検索]** タイルを選択します。
3. アラートを作成する次のクエリを実行して、**[実行]** を選択します。

        metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0

    このクエリは、HDInsight クラスターで実行されている失敗したアプリケーションの一覧を提供します。

4. ページの先頭にある **[新しいアラート ルール]** を選択します。

    ![クエリを入力してアラートを作成する](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "クエリを入力してアラートを作成する")

5. **[ルールの作成]** ウィンドウで、クエリとその他の情報を入力してアラートを作成し、**[アラート ルールの作成]** を選択します。

    ![クエリを入力してアラートを作成する](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "クエリを入力してアラートを作成する")

既存のアラートを編集または削除するには、次の手順を実行します。

1. Azure Portal から Log Analytics ワークスペースを開きます。
2. 左側のメニューで、**[アラート]** を選択します。
3. 編集または削除するアラートを選択します。
4. **[保存]**、**[破棄]**、**[無効化]**、および **[削除]** の各オプションがあります。

    ![HDInsight の Log Analytics の OMS アラートの削除/編集](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

詳細については、「[Log Analytics のアラート ルールの操作](../log-analytics/log-analytics-alerts-creating.md)」を参照してください。

## <a name="see-also"></a>関連項目

* [Log Analytics の使用](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Log Analytics でのアラート ルールの作成](../log-analytics/log-analytics-alerts-creating.md)
