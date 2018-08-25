---
title: Azure HDInsight で実行される Apache Spark ジョブのデバッグ
description: Azure HDInsight の Spark クラスターで実行されるジョブの追跡とデバッグには、YARN UI、Spark UI、Spark History Server を使用します。
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: jasonh
ms.openlocfilehash: 10d46da66fa6b06089e09816f400eefbe40c0c65
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622665"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Azure HDInsight で実行される Apache Spark ジョブのデバッグ

この記事では、YARN UI、Spark UI、および Spark History Server を使用して、HDInsight クラスター上で実行される Spark ジョブの追跡とデバッグを行う方法について説明します。 まず、「**Machine Learning: MLLib を使用した食品検査データの予測分析**」で使用した Spark クラスターのノートブックを使用して Spark ジョブを実行します。 他の方法 (**spark-submit** など) で送信したアプリケーションについても、以下の手順に従って追跡することができます。

## <a name="prerequisites"></a>前提条件
次のものが必要です。

* Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
* HDInsight での Apache Spark クラスター。 手順については、「 [Create Apache Spark clusters in Azure HDInsight (Azure HDInsight での Apache Spark クラスターの作成)](apache-spark-jupyter-spark-sql.md)」を参照してください。
* 「 **[Machine Learning: MLlib を使用した食品検査データの予測分析](apache-spark-machine-learning-mllib-ipython.md)**」で使用した Spark クラスターのノートブックを使用して Spark ジョブを実行します。 このノートブックの実行方法については、リンク先のページを参照してください。  

## <a name="track-an-application-in-the-yarn-ui"></a>YARN UI でのアプリケーションの追跡
1. YARN UI を起動します。 **[クラスター ダッシュボード]** をクリックし、**[YARN]** をクリックします。
   
    ![Launch YARN UI](./media/apache-spark-job-debugging/launch-yarn-ui.png)
   
   > [!TIP]
   > Ambari UI から YARN UI を起動してもかまいません。 Ambari UI を起動するには、**[クラスター ダッシュボード]** をクリックし、**[HDInsight クラスター ダッシュボード]** をクリックします。 Ambari UI から **[YARN]**、**[クイック リンク]** の順にクリックし、アクティブな Resource Manager をクリックして、**[Resource Manager UI]** をクリックします。    
   > 
   > 
2. Jupyter Notebook を使用して Spark ジョブを開始したため、アプリケーションの名前は **remotesparkmagics** (ノートブックから開始されたすべてのアプリケーションに使用される名前) になっています。 ジョブに関する詳しい情報を確認するには、アプリケーション名に対応するアプリケーション ID をクリックします。 これによりアプリケーション ビューが起動されます。
   
    ![Find Spark application ID](./media/apache-spark-job-debugging/find-application-id.png)
   
    Jupyter Notebook から起動されたアプリケーションの場合、ノートブックを終了するまでステータスは常に **[実行中]** になります。
3. アプリケーション ビューからドリルダウンして、アプリケーションやログ (stdout/stderr) に関連付けられているコンテナーを探すことができます。 次のように、 **[追跡 URL]** に対応するリンクをクリックして Spark UI を起動することもできます。 
   
    ![Download container logs](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Spark UI でのアプリケーションの追跡
以前アプリケーションを実行したときに生成された Spark ジョブは、Spark UI からドリルダウンすることができます。

1. Spark UI を起動するには、アプリケーション ビューから **[追跡 URL]** に対応するリンクをクリックします (上のスクリーン ショットを参照)。 Jupyter Notebook で実行中のアプリケーションによって開始されたすべての Spark ジョブを表示できます。
   
    ![View Spark jobs](./media/apache-spark-job-debugging/view-spark-jobs.png)
2. **[Executors]** タブをクリックすると、実行プログラムごとの処理や記憶域の情報が表示されます。 **[Thread Dump (スレッド ダンプ)]** リンクをクリックして呼び出し履歴を取得することもできます。
   
    ![View Spark executors](./media/apache-spark-job-debugging/view-spark-executors.png)
3. アプリケーションに関連付けられているステージを確認するには、 **[Stages (ステージ)]** タブをクリックします。
   
    ![View Spark stages](./media/apache-spark-job-debugging/view-spark-stages.png)
   
    1 つのステージに複数のタスクが存在する場合もあり、対応する実行の統計を表示することができます (下図参照)。
   
    ![View Spark stages](./media/apache-spark-job-debugging/view-spark-stages-details.png) 
4. ステージの詳細ページから DAG Visualization を起動できます。 以下に示したように、ページの上部にある **[DAG Visualization]** リンクを展開してください。
   
    ![View Spark stages DAG visualization](./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png)
   
    DAG (Direct Aclyic Graph) は、アプリケーション内の各種ステージを表します。 グラフ内の青色のボックスはそれぞれ、アプリケーションから呼び出された Spark 操作を表します。
5. ステージの詳細ページから、アプリケーションのタイムライン ビューを起動することもできます。 以下に示したように、ページの上部にある **[Event Timeline (イベントのタイムライン)]** リンクを展開してください。
   
    ![View Spark stages event timeline](./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png)
   
    Spark イベントがタイムラインの形式で表示されます。 タイムライン ビューには 3 種類の表示があります (ジョブ間、ジョブ内、ステージ内)。 上の画像は、特定のステージのタイムライン ビューをキャプチャしたものです。
   
   > [!TIP]
   > **[Enable zooming (ズームを有効にする)]** チェック ボックスをオンにすると、タイムライン ビューを左右にスクロールすることができます。
   > 
   > 
6. Spark UI には、Spark インスタンスに関する有益な情報を得ることのできるタブが他にもあります。
   
   * [Storage (ストレージ)] タブ: アプリケーションで RDD を作成した場合、その情報を [Storage (ストレージ)] タブで確認できます。
   * [Environment (環境)] タブ: Spark インスタンスに関する有益な情報が多数表示されます。その例を次に示します。 
     * Scala バージョン
     * クラスターに関連付けられているイベント ログ ディレクトリ
     * アプリケーションの Executor のコア数
     * その他

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>完了したジョブに関する情報を Spark History Server で探す
完了したジョブに関する情報は、Spark History Server に保存されています。

1. Spark History Server を起動するには、クラスター ブレードから **[クラスター ダッシュボード]** をクリックし、**[Spark History Server]** をクリックします。
   
    ![Launch Spark History Server](./media/apache-spark-job-debugging/launch-spark-history-server.png)
   
   > [!TIP]
   > Ambari UI から Spark History Server の UI を起動してもかまいません。 Ambari UI を起動するには、クラスター ブレードから **[クラスター ダッシュボード]** をクリックし、**[HDInsight クラスター ダッシュボード]** をクリックします。 Ambari UI から **[Spark]**、**[クイック リンク]** の順にクリックし、**[Spark History Server UI]** をクリックします。
   > 
   > 
2. 完了済みのすべてのアプリケーションが一覧表示されます。 アプリケーションをドリルダウンして、より詳しい情報を入手するには、アプリケーション ID をクリックします。
   
    ![Launch Spark History Server](./media/apache-spark-job-debugging/view-completed-applications.png)

## <a name="see-also"></a>関連項目
*  [Azure HDInsight での Apache Spark クラスターのリソースの管理](apache-spark-resource-manager.md)
*  [拡張された Spark History Server を使用して Spark ジョブをデバッグする方法に関する記事](apache-azure-spark-history-server.md)

### <a name="for-data-analysts"></a>データ アナリスト向け

* [Spark と Machine Learning: HDInsight で Spark を使用して HVAC データを基に建物の温度を分析する](apache-spark-ipython-notebook-machine-learning.md)
* [Spark with Machine Learning: Use Spark in HDInsight to predict food inspection results (Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する)](apache-spark-machine-learning-mllib-ipython.md)
* [Website log analysis using Spark in HDInsight (HDInsight での Spark を使用した Web サイト ログ分析)](apache-spark-custom-library-website-log-analysis.md)
* [HDInsight での Spark を使用した Application Insight テレメトリ データ分析](apache-spark-analyze-application-insight-logs.md)
* [分散型深層学習用に Azure HDInsight Spark で Caffe を使用する](apache-spark-deep-learning-caffe.md)

### <a name="for-spark-developers"></a>Spark 開発者向け

* [Scala を使用してスタンドアロン アプリケーションを作成する](apache-spark-create-standalone-application.md)
* [Livy を使用して Spark クラスターでジョブをリモートで実行する](apache-spark-livy-rest-interface.md)
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark Scala アプリケーションを作成し、送信する](apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark アプリケーションをリモートでデバッグする](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight の Spark クラスターで Zeppelin Notebook を使用する](apache-spark-zeppelin-notebook.md)
* [HDInsight 用の Spark クラスターの Jupyter Notebook で使用可能なカーネル](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](apache-spark-jupyter-notebook-install-locally.md)


