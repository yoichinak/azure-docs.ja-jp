---
title: Stream Analytics から Data Lake Store へのデータのストリーム | Microsoft Docs
description: Azure Stream Analytics を使用した Azure Data Lake Store へのデータのストリーム
services: data-lake-store,stream-analytics
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: edb58e0b-311f-44b0-a499-04d7e6c07a90
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: nitinme
ms.openlocfilehash: 396d514d0d75c43f20ab7b0fcdf8c7351cb3dd89
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213454"
---
# <a name="stream-data-from-azure-storage-blob-into-data-lake-store-using-azure-stream-analytics"></a>Azure Stream Analytics を使用した Azure Storage BLOB から Data Lake Store へのデータ ストリーム
この記事では、Azure Data Lake Store を Azure Stream Analytics ジョブの出力として使用する方法について説明します。 ここでは、Azure Storage BLOB (入力) からデータを読み取り、そのデータを Data Lake Store (出力) に書き込む簡単なシナリオを紹介します。

## <a name="prerequisites"></a>前提条件
このチュートリアルを読み始める前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

* **Azure Storage アカウント**。 このアカウントから BLOB コンテナーを使用して、Stream Analytics ジョブ向けのデータを入力します。 このチュートリアルでは、**storageforasa** という名前のストレージ アカウントと、そのアカウント内に **storageforasacontainer** という名前のコンテナーを持っていることを前提とします。 コンテナーを作成したら、サンプル データ ファイルをアップロードします。 
  
* **Azure Data Lake Store アカウント**。 「[Azure Portal で Azure Data Lake Store の使用を開始する](data-lake-store-get-started-portal.md)」の手順に従ってください。 **asadatalakestore** という名前の Data Lake Store アカウントを持っているとします。 

## <a name="create-a-stream-analytics-job"></a>Stream Analytics のジョブの作成
まず、入力ソースと出力先を含む Stream Analytics ジョブを作成します。 このチュートリアルでは、ソースは Azure BLOB コンテナー、出力先は Data Lake Store です。

1. [Azure Portal](https://portal.azure.com) にサインオンします。

2. 左側のウィンドウで、**[Stream Analytics ジョブ]** をクリックし、**[追加]** をクリックします。

    ![Stream Analytics ジョブの作成](./media/data-lake-store-stream-analytics/create.job.png "Stream Analytics ジョブの作成")

    > [!NOTE]
    > ストレージ アカウントと同じリージョンにジョブを作成していることを確認してください。そうでない場合、リージョン間でのデータ転送で追加料金が発生します。
    >

## <a name="create-a-blob-input-for-the-job"></a>ジョブに BLOB 入力を作成

1. Stream Analytics ジョブのページを開き、左側のウィンドウで **[入力]** タブをクリックし、**[追加]** をクリックします。

    ![ジョブへの入力の追加](./media/data-lake-store-stream-analytics/create.input.1.png "ジョブへの入力の追加")

2. **[新しい入力]** ブレードで、次の値を指定します。

    ![ジョブへの入力の追加](./media/data-lake-store-stream-analytics/create.input.2.png "ジョブへの入力の追加")

    * **[入力のエイリアス]** で、このジョブ入力の一意の名前を入力します。
    * **[ソースの種類]** で、**[データ ストリーム]** を選択します。
    * **[ソース]** で、**[BLOB ストレージ]** を選択します。
    * **[サブスクリプション]** で、**[現在のサブスクリプションの BLOB ストレージを使う]** を選択します。
    * **[ストレージ アカウント]** で、前提条件の一部として作成したストレージ アカウントを選択します。 
    * **[コンテナー]** で、選択したストレージ アカウント内に作成したコンテナーを選択します。
    * **[イベントのシリアル化の形式]** で、**[CSV]** を選択します。
    * **[区切り記号]** で、**[タブ]** を選択します。
    * **[エンコード]** で、**[UTF-8]** を選択します。

    **Create** をクリックしてください。 これで、ポータルは、この入力を追加して接続をテストします。


## <a name="create-a-data-lake-store-output-for-the-job"></a>ジョブに Data Lake Store 出力を作成

1. Stream Analytics ジョブのページを開き、**[出力]** タブをクリックし、**[追加]** をクリックします。

    ![ジョブへの出力の追加](./media/data-lake-store-stream-analytics/create.output.1.png "ジョブへの出力の追加")

2. **[新しい出力]** ブレードで、次の値を指定します。

    ![ジョブへの出力の追加](./media/data-lake-store-stream-analytics/create.output.2.png "ジョブへの出力の追加")

    * **[出力のエイリアス]** で、このジョブ出力の一意名を入力します。 クエリの出力をこの Data Lake Store に出力するためにクエリで使用されるわかりやすい名前です。
    * **[シンク]** で、**[Data Lake Store]** を選択します。
    * Data Lake Store アカウントへのアクセスを承認することを求められます。 **[承認]** をクリックします。

3. **[新しい出力]** ブレードで、引き続き次の値を指定します。

    ![ジョブへの出力の追加](./media/data-lake-store-stream-analytics/create.output.3.png "ジョブへの出力の追加")

    * **[アカウント名]** で、ジョブ出力の送信先として作成済みの Data Lake Store アカウントを選択します。
    * **[パス プレフィックスのパターン]** で、指定した Data Lake Store アカウント内にファイルを書き込むために使用するファイル パスを入力します。
    * プレフィックス パスで日付トークンを使用する場合は、**[日付形式]** でファイルを編成する日付形式を選択できます。
    * プレフィックス パスで時刻トークンを使用する場合は、**[時刻形式]** でファイルを編成する時刻形式を選択できます。
    * **[イベントのシリアル化の形式]** で、**[CSV]** を選択します。
    * **[区切り記号]** で、**[タブ]** を選択します。
    * **[エンコード]** で、**[UTF-8]** を選択します。
    
    **Create** をクリックしてください。 これで、ポータルは、この出力を追加して接続をテストします。
    
## <a name="run-the-stream-analytics-job"></a>Stream Analytics ジョブの実行

1. Stream Analytics ジョブを実行するには、**[クエリ]** タブからクエリを実行する必要があります。このチュートリアルでは、次の画面キャプチャに示すように、プレースホルダーをジョブの入力および出力エイリアスで置き換えて、サンプル クエリを実行することができます。

    ![クエリの実行](./media/data-lake-store-stream-analytics/run.query.png "クエリの実行")

2. 画面上部の **保存** をクリックした後、**概要** タブで **開始** をクリックします。 ダイアログ ボックスで **[ユーザー設定時刻]** を選択し、現在の日付と時刻を設定します。

    ![ジョブの時刻の設定](./media/data-lake-store-stream-analytics/run.query.2.png "ジョブの時刻の設定")

    **[開始]** をクリックしてジョブを開始します。 ジョブが開始されるまでに数分かかる場合があります。

3. Blob からデータを取得するジョブをトリガーするには、サンプル データ ファイルを BLOB コンテナーにコピーします。 サンプル データ ファイルは [Azure Data Lake Git リポジトリ](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt)で取得できます。 このチュートリアルでは、**vehicle1_09142014.csv** ファイルをコピーします。 [Azure Storage Explorer](http://storageexplorer.com/)などのさまざまなクライアントを使用して、BLOB コンテナーにデータをアップロードすることができます。

4. **[概要]** タブの **[監視]** で、データがどのように処理されたかを確認します。

    ![ジョブの監視](./media/data-lake-store-stream-analytics/run.query.3.png "ジョブの監視")

5. 最後に、ジョブの出力データが Data Lake Store アカウントで使用可能であることを確認できます。 

    ![出力の確認](./media/data-lake-store-stream-analytics/run.query.4.png "出力の確認")

    [データ エクスプローラー] ウィンドウで、Data Lake Store 出力設定 (`streamanalytics/job/output/{date}/{time}`) で指定したフォルダー パスに出力が書き込まれていることがわかります。  

## <a name="see-also"></a>関連項目
* [Azure ポータルを使用して、Data Lake Store を使用する HDInsight クラスターを作成する](data-lake-store-hdinsight-hadoop-use-portal.md)
