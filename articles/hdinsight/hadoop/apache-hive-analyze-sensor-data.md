---
title: Hive と Hadoop を使用したセンサー データの分析 - Azure HDInsight
description: Hive クエリ コンソールを HDInsight (Hadoop) と共に使用してセンサー データを分析してから、Microsoft Excel の Power View を使用してデータを視覚化する方法について説明します。
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
ms.topic: conceptual
ms.date: 04/14/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 412942aa41e7884c6315d921b0b272b033386d17
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39590247"
---
# <a name="analyze-sensor-data-using-the-hive-query-console-on-hadoop-in-hdinsight"></a>HDInsight の Hadoop で Hive クエリ コンソールを使用したセンサー データの分析

Hive クエリ コンソールを HDInsight (Hadoop) と共に使用してセンサー データを分析してから、Microsoft Excel の Power View を使用してデータを視覚化する方法について説明します。

> [!IMPORTANT]
> このドキュメントの手順は、Windows ベースの HDInsight クラスターに対してのみ機能します。 Windows では、バージョン 3.4 より前の HDInsight のみを使用できます。 Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Windows での HDInsight の提供終了](../hdinsight-component-versioning.md#hdinsight-windows-retirement)に関する記事を参照してください。


このサンプルでは、Hive を使用して履歴データを処理し、暖房空調システムの問題を特定します。 具体的には、以下のタスクを実行することによって、設定温度を安定的に維持できないシステムを特定します。

* コンマ区切りの値 (CSV) のファイルに格納されたデータを照会する HIVE テーブルの作成
* データを分析するための HIVE クエリの作成
* 分析したデータを取得するための、Microsoft Excel を使用した HDInsight への接続
* データを視覚化するための、Power View の使用

![A diagram of the solution architecture](./media/apache-hive-analyze-sensor-data/hvac-architecture.png)

## <a name="prerequisites"></a>前提条件

* HDInsight (Hadoop) クラスター - クラスター作成の詳細については、「[HDInsight での Hadoop クラスターの作成](../hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。
* Microsoft Excel 2013

  > [!NOTE]
  > Microsoft Excel は、 [Power View](https://support.office.com/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=en-US&rs=en-US&ad=US)を使用したデータ視覚化のために使用します。

* [Microsoft Hive ODBC ドライバー](http://www.microsoft.com/download/details.aspx?id=40886)

## <a name="to-run-the-sample"></a>サンプルを実行するには

1. Web ブラウザーで次の URL に移動します。 

         https://<clustername>.azurehdinsight.net

    `<clustername>` を、使用する HDInsight クラスターの名前に置き換えます。

    入力を要求されたら、このクラスターをプロビジョニングするときに使用した管理者ユーザー名とパスワードを使用して認証を実行します。

2. 表示された Web ページで **[概要ギャラリー]** タブをクリックし、**[Solutions with Sample Data (サンプル データを使用した解決策)]** カテゴリにある **[センサー データ分析]** サンプルを選択します。

    ![ギャラリー イメージの概要](./media/apache-hive-analyze-sensor-data/getting-started-gallery.png)

3. Web ページに記載されている手順に従って、サンプルを完了します。
