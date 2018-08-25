---
title: Web サイトのログ分析のための Hadoop での Hive の使用 - Azure HDInsight
description: Web サイトのログを分析するために HDInsight で Hive を使用する方法を説明します。 HDInsight テーブルへの入力にログ ファイルを使用し、HiveQL を使用してデータを照会します。
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/17/2016
ms.author: jasonh
ROBOTS: NOINDEX
ms.openlocfilehash: a40aef8d0231fcfc0ae0f399440b1fb98367dd2d
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39595717"
---
# <a name="use-hive-with-windows-based-hdinsight-to-analyze-logs-from-websites"></a>Web サイトのログを分析するための Windows ベースの HDInsight での Hive の使用
Web サイトのログを分析するために HDInsight で HiveQL を使用する方法を説明します。 Web サイトのログ分析は、類似するアクティビティに基づく対象ユーザーの区分、人口統計によるサイト訪問者の分類、参照されたコンテンツや訪問元の Web サイトの確認などのために使用できます。

> [!IMPORTANT]
> このドキュメントの手順は、Windows ベースの HDInsight クラスターに対してのみ機能します。 Windows では、バージョン 3.4 より前の HDInsight のみを使用できます。 Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Windows での HDInsight の提供終了](../hdinsight-component-versioning.md#hdinsight-windows-retirement)に関する記事を参照してください。

このサンプルでは、HDInsight クラスターを使用して Web サイトのログ ファイルを分析することにより、1 日の間に発生した外部 Web サイトからの Web サイトへのアクセス数を調べます。 また、発生した Web サイト エラーの概要を生成します。 学習内容は次のとおりです。

* Web サイトのログ ファイルが含まれている Azure Blob ストレージへの接続
* これらのログを照会するための HIVE テーブルの作成
* データを分析するための HIVE クエリの作成
* 分析したデータを取得するための、Microsoft Excel を使用した HDInsight への接続 (Open Database Connectivity (ODBC) を使用)

![HDI.Samples.Website.Log.Analysis](./media/apache-hive-analyze-website-log/hdinsight-weblogs-sample.png)

## <a name="prerequisites"></a>前提条件
* Azure HDInsight 上で Hadoop クラスターをプロビジョニングしておく必要があります。 手順については、[HDInsight クラスターのプロビジョニング](../hdinsight-hadoop-provision-linux-clusters.md)に関するページを参照してください。
* Microsoft Excel 2013 または Microsoft Excel 2010 がインストールされていること。
* Hive から Excel にデータをインポートするための [Microsoft Hive ODBC ドライバー](http://www.microsoft.com/download/details.aspx?id=40886) があること。

## <a name="to-run-the-sample"></a>サンプルを実行するには
1. [Azure ポータル](https://portal.azure.com/)のスタート画面 (ポータルにクラスターをピン留めした場合) で、サンプルを実行するクラスターのタイルをクリックします。
2. クラスターのブレードの **[クイック リンク]** で **[クラスター ダッシュボード]** をクリックし、**[クラスター ダッシュボード]** ブレードで **[HDInsight クラスター ダッシュボード]** をクリックします。 または、次に示す URL を使用してダッシュボードを直接開くこともできます。

         https://<clustername>.azurehdinsight.net

    入力を要求されたら、クラスターをプロビジョニングするときに使用した管理者名とパスワードを使用して認証します。
3. 表示された Web ページの **[概要ギャラリー]** タブをクリックし、**[サンプル データを使用したソリューション]** カテゴリにある **[Web サイトのログの分析]** サンプルをクリックします。
4. Web ページに記載されている手順に従って、サンプルを完了します。

## <a name="next-steps"></a>次の手順
「 [Hive を HDInsight と共に使用したセンサー データの分析](apache-hive-analyze-sensor-data.md)」のサンプル手順に従ってください。

[hdinsight-sensor-data-sample]: ../hdinsight-use-hive-sensor-data-analysis.md
