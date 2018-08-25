---
title: Windows ベースの HDInsight で Tez UI を使用する - Azure
description: Windows ベースの HDInsight で Tez UI を使用して Tez ジョブをデバッグする方法について説明します。
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/17/2017
ms.author: jasonh
ROBOTS: NOINDEX
ms.openlocfilehash: f54cc60f9490b8a5ca1872a290c3895ea8b0c5e4
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39590903"
---
# <a name="use-the-tez-ui-to-debug-tez-jobs-on-windows-based-hdinsight"></a>Windows ベースの HDInsight で Tez UI を使用して Tez ジョブをデバッグする
Tez UI は、Tez を実行エンジンとして使用する Hive ジョブをデバッグするために使用できます。 Tez UI を使用すると、関連付けられた項目のグラフとしてジョブを可視化し、各項目をドリルダウンして、統計情報やログ情報を取得することができます。

> [!IMPORTANT]
> このドキュメントの手順では、Windows を使用する HDInsight クラスターが必要です。 Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Windows での HDInsight の提供終了](hdinsight-component-versioning.md#hdinsight-windows-retirement)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件
* Windows ベースの HDInsight クラスター。 新しいクラスターの作成手順については、「 [Hadoop チュートリアル: Windows 上の HDInsight で Hadoop を使用する](hdinsight-hadoop-tutorial-get-started-windows.md)」を参照してください。

  > [!IMPORTANT]
  > Tez UI は、2016 年 2 月 8 日より後に作成された Windows ベースの HDInsight クラスターでのみ利用できます。
  >
  >
* Windows ベースのリモート デスクトップ クライアント。

## <a name="understanding-tez"></a>Tez について
Tez は、Hadoop でデータを処理するための拡張可能なフレームワークで、処理速度が従来の MapReduce よりも向上します。 Tez を有効にするには、次のテキストを Hive クエリの一部に含めます。

    set hive.execution.engine=tez;

Tez は、ジョブで必要なアクションの実行順序を記述された有向非巡回グラフ (DAG) を作成します。 個々のアクションは頂点と呼ばれ、ジョブ全体の一部分を実行します。 頂点によって表される処理を実際に実行することはタスクと呼ばれ、クラスター内の複数のノードに分散される場合があります。

### <a name="understanding-the-tez-ui"></a>Tez UI について
Tez UI は、Tez を使用するプロセスに関する情報を提供する web ページです。 次のシナリオで役立つ情報が提供される場合があります。

* Map タスクと Reduce タスクの進行状況を表示して、実行時間の長いプロセスの監視する。
* 成功または失敗したプロセスの履歴データを分析して、処理の改善方法や失敗した理由を確認する。

## <a name="generate-a-dag"></a>DAG の生成
Tez UI には、Tez エンジンを使用するジョブが現在実行中か、過去に実行されていた場合にデータが表示されます。 単純な Hive クエリは、通常 Tez を使用しなくても解決できます。 フィルター処理、グループ化、並べ替え、結合などを行うより複雑なクエリは、Tez の使用が必要です。

Tez を使用する Hive クエリを実行するには、次の手順に従います。

1. Web ブラウザーで https://CLUSTERNAME.azurehdinsight.net に移動します。**CLUSTERNAME** は実際の HDInsight クラスターの名前です。
2. ページの上部にあるメニューの **[Hive エディター]** を選択します。 これにより、次のサンプルのクエリを含むページが表示されます。

        Select * from hivesampletable

    サンプルのクエリを削除し、次のクエリに置き換えます。

        set hive.execution.engine=tez;
        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;
3. **[送信]** ボタンを選択します。 ページの下部にある **[ジョブ セッション]** セクションに、クエリの状態が表示されます。 状態が **[完了]** に変わったら、**[詳細の表示]** リンクを選択して結果を表示します。 **ジョブの出力**は次のようになります。

        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica
        en-GB   Nairobi Area    Kenya

## <a name="use-the-tez-ui"></a>Tez UI の使用
> [!NOTE]
> Tez UI はクラスターのヘッド ノードのデスクトップからしか利用できないため、リモート デスクトップを使用してヘッド ノードに接続する必要があります。
>
>

1. [Azure ポータル](https://portal.azure.com)で HDInsight クラスターを選択します。 [HDInsight] ブレードの上部にある **[リモート デスクトップ]** アイコンを選択します。 リモート デスクトップのブレードがこのリンクに表示されます

    ![Remote desktop icon](./media/hdinsight-debug-tez-ui/remotedesktopicon.png)
2. [リモート デスクトップ] ブレードで、**[接続]** を選択してクラスターのヘッド ノードに接続します。 メッセージが表示されたら、クラスターのリモート デスクトップ ユーザー名とパスワードを使用して接続を認証します。

    ![Remote desktop connect icon](./media/hdinsight-debug-tez-ui/remotedesktopconnect.png)

   > [!NOTE]
   > リモート デスクトップ接続を有効にしていない場合は、ユーザー名、パスワード、有効期限を指定し、**[有効にする]** を選択してリモート デスクトップを有効にします。 リモート デスクトップが有効になったら、前の手順に従って接続します。
   >
   >
3. 接続後、リモート デスクトップで Internet Explorer を開き、ブラウザーの右上隅にある歯車アイコンを選択して、**[互換表示設定]** を選択します。
4. **[互換表示設定]** の下部で、**[イントラネット サイトを互換表示で表示する]** チェック ボックスと **[Microsoft 互換性リストの使用]** チェック ボックスをオフにし、**[閉じる]** を選択します。
5. Internet Explorer で http://headnodehost:8188/tezui/#/ を参照します。 これにより、Tez UI が表示されます

    ![Tez UI](./media/hdinsight-debug-tez-ui/tezui.png)

    Tez UI が読み込まれると、クラスターで現在実行されている DAG や実行されていた DAG の一覧が表示されます。 既定のビューには、DAG 名、ID、送信者、状態、開始時刻、終了時刻、期間、アプリケーション ID、キューが表示されます。 ページの右側にある歯車アイコンを使用すると、列をさらに追加できます。

    エントリが 1 つしかない場合、それは、前のセクションで実行したクエリのエントリです。 エントリが複数ある場合は、DAG の上にあるフィールドで検索条件を入力して検索してから、**Enter**キーを押します。
6. 最新の DAG エントリの **DAG 名**を選択します。 このリンクに、その DAG に関する情報のほか、DAG に関する情報が含まれる JSON ファイルの zip ファイルをダウンロードするオプションが表示されます。

    ![DAG Details](./media/hdinsight-debug-tez-ui/dagdetails.png)
7. **[DAG Details]** の上にはいくつかのリンクがあります。これらのリンクを使用すると、DAG に関する情報を表示できます。

   * **[DAG Counters]** をクリックすると、この DAG のカウンターの情報が表示されます。
   * **[Graphical View]** をクリックすると、この DAG がグラフィカルに表示されます。
   * **[All Vertices]** をクリックすると、この DAG 内の頂点の一覧が表示されます。
   * **[All Tasks]** をクリックすると、この DAG 内のすべての頂点のタスクの一覧が表示されます。
   * **[All TaskAttempts]** をクリックすると、この DAG のタスクの実行に関する情報が表示されます。

     > [!NOTE]
     > [Vertices]、[Tasks]、[TaskAttempts] が表示されるように列をスクロールする場合は、行ごとに**カウンター**を表示するためのリンクと**ログを表示またはダウンロードする**ためのリンクがあることに注意してください。
     >
     >

     ジョブでエラーが発生した場合、[DAG Details] には、[FAILED] という状態と、失敗したタスクに関する情報へのリンクが表示されます。 診断情報は、DAG の詳細の下に表示されます。
8. **[Graphical View]** を選択します。 これにより、DAG がグラフィカルに表示されます。 ビューの各頂点にマウス ポインターを置くと、その項目に関する情報を表示できます。

    ![[Graphical View]](./media/hdinsight-debug-tez-ui/dagdiagram.png)
9. 頂点をクリックすると、その項目の **[Vertex Details]** (頂点詳細) が読み込まれます。 **Map 1** の頂点をクリックして、この項目の詳細を表示します。 **[Confirm]** を選択してナビゲーションを確認します。

    ![[Vertex Details]](./media/hdinsight-debug-tez-ui/vertexdetails.png)
10. これで、ページの上部に頂点とタスクに関連するリンクが表示されます。

    > [!NOTE]
    > **[DAG Details]** に戻り、**[Vertex Details]** を選択して、**Map 1** の頂点を選択することで、このページに移動することもできます。
    >
    >

    * **[Vertex Counters]** をクリックすると、この頂点のカウンター情報が表示されます。
    * **[Tasks]** をクリックすると、この頂点のタスクが表示されます。
    * **[Task Attempts]** をクリックすると、この頂点のタスクの実行に関する情報が表示されます。
    * **[Sources & Sinks]** をクリックすると、この頂点のデータ ソースとシンクが表示されます。

      > [!NOTE]
      > 前のメニューと同様に、[Tasks]、[Task Attempts]、[Sources & Sinks__] が表示されるように列をスクロールすると、各項目の詳細情報へのリンクを表示できます。
      >
      >
11. **[Tasks]** を選択した後、**00_000000** という名前の項目をクリックします。 このリンクに、このタスクの **[タスク詳細]** が表示されます。 この画面から、**[Task Counters]** と **[Task Attempts]** を表示できます。

    ![タスクの詳細](./media/hdinsight-debug-tez-ui/taskdetails.png)

## <a name="next-steps"></a>次の手順
ここでは、Tez ビューの使用方法を説明しました。詳細については、[HDInsight での Hive の使用](hadoop/hdinsight-use-hive.md)に関する記事を参照してください。

Tez に関する技術的な情報の詳細については、 [Hortonworks の Tez に関するページ](http://hortonworks.com/hadoop/tez/)を参照してください。
