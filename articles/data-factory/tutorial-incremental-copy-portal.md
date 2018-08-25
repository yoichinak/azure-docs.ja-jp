---
title: Azure Data Factory を使ってテーブルを増分コピーする | Microsoft Docs
description: このチュートリアルでは、Azure SQL データベースから Azure BLOB ストレージにデータを増分コピーする Azure Data Factory パイプラインを作成します。
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/11/2018
ms.author: yexu
ms.openlocfilehash: f1803dd051b380743b56f4f026ee5c5fb684ce69
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2018
ms.locfileid: "41918835"
---
# <a name="incrementally-load-data-from-an-azure-sql-database-to-azure-blob-storage"></a>Azure SQL データベースから Azure BLOB ストレージにデータを増分読み込みする
このチュートリアルでは、Azure SQL データベース内のテーブルから Azure BLOB ストレージに差分データを読み込むパイプラインを使用して Azure Data Factory を作成します。 

このチュートリアルでは、以下の手順を実行します。

> [!div class="checklist"]
> * 基準値を格納するためのデータ ストアを準備します。
> * データ ファクトリを作成します。
> * リンクされたサービスを作成します。 
> * ソース データセット、シンク データセット、および基準値データセットを作成します。
> * パイプラインを作成します。
> * パイプラインを実行します。
> * パイプラインの実行を監視します。 
> * 結果の確認
> * ソースにデータを追加します。
> * パイプラインを再実行します。
> * 2 回目のパイプラインの実行を監視します
> * 2 回目の実行の結果を確認します


## <a name="overview"></a>概要
ソリューションの概略図を次に示します。 

![データの増分読み込み](media\tutorial-Incremental-copy-portal\incrementally-load.png)

このソリューションを作成するための重要な手順を次に示します。 

1. **基準値列を選択する**。
    ソース データ ストアのいずれか 1 つの列を選択します。実行ごとに新しいレコードまたは更新されたレコードを切り分ける目的で使用されます。 通常、行が作成または更新されたときに常にデータが増える列を選択します (last_modify_time、ID など)。 この列の最大値が基準値として使用されます。

2. **基準値を格納するためのデータ ストアを準備する**。 このチュートリアルでは、SQL データベースに基準値を格納します。
    
3. **次のワークフローを含んだパイプラインを作成する**。 
    
    このソリューションのパイプラインには、次のアクティビティがあります。
  
    * 2 つのルックアップ アクティビティを作成する。 1 つ目のルックアップ アクティビティは、前回の基準値を取得するために使用します。 2 つ目のルックアップ アクティビティは、新しい基準値を取得するために使用します。 これらの基準値は、コピー アクティビティに渡されます。 
    * コピー アクティビティを作成する。これは基準値列の値がかつての基準値より大きく、かつ新しい基準値よりも小さい行をソース データ ストアからコピーするアクティビティです。 その差分データが、ソース データ ストアから新しいファイルとして BLOB ストレージにコピーされます。 
    * ストアド プロシージャ― アクティビティを作成する。これはパイプラインの次回実行に備えて基準値を更新するためのアクティビティです。 


Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="prerequisites"></a>前提条件
* **Azure SQL データベース**。 ソース データ ストアとして使うデータベースです。 SQL データベースがない場合の作成手順については、「[Azure SQL データベースを作成する](../sql-database/sql-database-get-started-portal.md)」を参照してください。
* **Azure Storage**。 シンク データ ストアとして使用する BLOB ストレージです。 Azure ストレージ アカウントがない場合の作成手順については、「[ストレージ アカウントの作成](../storage/common/storage-quickstart-create-account.md)」を参照してください。 adftutorial という名前のコンテナーを作成します。 

### <a name="create-a-data-source-table-in-your-sql-database"></a>SQL データベースにデータ ソース テーブルを作成する
1. SQL Server Management Studio を開きます。 **サーバー エクスプローラー**で目的のデータベースを右クリックし、**[新しいクエリ]** を選択します。

2. SQL データベースに対して次の SQL コマンドを実行し、`data_source_table` という名前のテーブルをデータ ソース ストアとして作成します。 
    
    ```sql
    create table data_source_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );

    INSERT INTO data_source_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'aaaa','9/1/2017 12:56:00 AM'),
    (2, 'bbbb','9/2/2017 5:23:00 AM'),
    (3, 'cccc','9/3/2017 2:36:00 AM'),
    (4, 'dddd','9/4/2017 3:21:00 AM'),
    (5, 'eeee','9/5/2017 8:06:00 AM');
    ```
    このチュートリアルでは、LastModifytime を基準値列として使用します。 データ ソース ストアに格納されているデータを次の表に示します。

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    ```

### <a name="create-another-table-in-your-sql-database-to-store-the-high-watermark-value"></a>高基準値の格納用としてもう 1 つテーブルを SQL データベースに作成する
1. SQL データベースに対して次の SQL コマンドを実行し、基準値の格納先として `watermarktable` という名前のテーブルを作成します。  
    
    ```sql
    create table watermarktable
    (
    
    TableName varchar(255),
    WatermarkValue datetime,
    );
    ```
2. ソース データ ストアのテーブル名と組み合わせて高基準値の既定値を設定します。 このチュートリアルでは、テーブル名は data_source_table です。

    ```sql
    INSERT INTO watermarktable
    VALUES ('data_source_table','1/1/2010 12:00:00 AM')    
    ```
3. `watermarktable` テーブル内のデータを確認します。
    
    ```sql
    Select * from watermarktable
    ```
    出力: 

    ```
    TableName  | WatermarkValue
    ----------  | --------------
    data_source_table | 2010-01-01 00:00:00.000
    ```

### <a name="create-a-stored-procedure-in-your-sql-database"></a>SQL データベースにストアド プロシージャを作成する 

次のコマンドを実行して、SQL データベースにストアド プロシージャを作成します。

```sql
CREATE PROCEDURE sp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN
    
    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName
    
END
```

## <a name="create-a-data-factory"></a>Data Factory を作成する。

1. Web ブラウザー (**Microsoft Edge** または **Google Chrome**) を起動します。 現在、Data Factory の UI がサポートされる Web ブラウザーは Microsoft Edge と Google Chrome だけです。
1. 左側のメニューで **[新規]** をクリックし、**[データ + 分析]**、**[Data Factory]** の順にクリックします。 
   
   ![New->DataFactory](./media/tutorial-incremental-copy-portal/new-azure-data-factory-menu.png)
2. **[新しいデータ ファクトリ]** ページで、**[名前]** に「**ADFIncCopyTutorialDF**」と入力します。 
      
     ![[新しいデータ ファクトリ] ページ](./media/tutorial-incremental-copy-portal/new-azure-data-factory.png)
 
   Azure データ ファクトリの名前は **グローバルに一意**にする必要があります。 赤い感嘆符と次のエラーが表示される場合は、データ ファクトリの名前を変更して (yournameADFIncCopyTutorialDF など)、作成し直してください。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事を参照してください。
  
       `Data factory name "ADFIncCopyTutorialDF" is not available`
3. データ ファクトリを作成する Azure **サブスクリプション**を選択します。 
4. **[リソース グループ]** について、次の手順のいずれかを行います。
     
      - **[Use existing (既存のものを使用)]** を選択し、ドロップダウン リストから既存のリソース グループを選択します。 
      - **[新規作成]** を選択し、リソース グループの名前を入力します。   
         
        リソース グループの詳細については、 [リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/resource-group-overview.md)に関するページを参照してください。  
4. **バージョン**として **[V2]** を選択します。
5. データ ファクトリの **場所** を選択します。 サポートされている場所のみがドロップダウン リストに表示されます。 データ ファクトリで使用するデータ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (HDInsight など) は他のリージョンに配置できます。
6. **[ダッシュボードにピン留めする]** をオンにします。     
7. **Create** をクリックしてください。      
8. ダッシュボードに、**[Deploying data factory]\(データ ファクトリをデプロイしています\)** というステータスを示したタイルが表示されます。 

    ![[Deploying data factory]\(データ ファクトリをデプロイしています\) タイル](media/tutorial-incremental-copy-portal/deploying-data-factory.png)
9. 作成が完了すると、図に示されているような **[Data Factory]** ページが表示されます。
   
   ![データ ファクトリのホーム ページ](./media/tutorial-incremental-copy-portal/data-factory-home-page.png)
10. **[Author & Monitor]\(作成と監視\)** タイルをクリックして、別のタブで Azure Data Factory ユーザー インターフェイス (UI) を起動します。

## <a name="create-a-pipeline"></a>パイプラインを作成する。
このチュートリアルでは、2 つのルックアップ アクティビティ、1 つのコピー アクティビティ、そして 1 つのストアド プロシージャ アクティビティを 1 つに連結したパイプラインを作成します。 

1. Data Factory UI の**開始**ページで **[Create pipeline]\(パイプラインの作成\)** タイルをクリックします。 

   ![Data Factory UI の開始ページ](./media/tutorial-incremental-copy-portal/get-started-page.png)    
3. パイプラインの **[プロパティ]** ウィンドウの **[全般]** ページで、名前として「**IncrementalCopyPipeline**」と入力します。 

   ![パイプライン名](./media/tutorial-incremental-copy-portal/pipeline-name.png)
4. 古い基準値を取得するための最初の検索アクティビティを追加します。 **[アクティビティ]** ツールボックスで **[General]\(一般\)** を展開し、パイプライン デザイナー画面に **[検索]** アクティビティをドラッグ アンド ドロップします。 アクティビティの名前を **LookupOldWaterMarkActivity** に変更します。

   ![最初の検索アクティビティ - 名前](./media/tutorial-incremental-copy-portal/first-lookup-name.png)
5. **[設定]** タブに切り替えて、**[Source Dataset]\(ソース データセット\)** の **[+ 新規]** をクリックします。 この手順では、**watermarktable** 内のデータを表すデータセットを作成します。 このテーブルには、前のコピー操作で使用されていた古い基準が含まれています。 

   ![[新しいデータセット] メニュー - 古い基準](./media/tutorial-incremental-copy-portal/new-dataset-old-watermark.png)
6. **[新しいデータセット]** ウィンドウで **[Azure SQL Database]** を選択し、**[完了]** をクリックします。 データセット用の新しいタブが開かれます。 

   ![Azure SQL Database の選択](./media/tutorial-incremental-copy-portal/select-azure-sql-database-old-watermark.png)
7. データセットのプロパティ ウィンドウで、**[名前]** に「**WatermarkDataset**」と入力します。

   ![基準データセット - 名前](./media/tutorial-incremental-copy-portal/watermark-dataset-name.png)
8. **[接続]** タブに切り替えて、**[+ 新規]** をクリックし、Azure SQL データベースへの接続を作成します (リンクされたサービスを作成します)。 

   ![新しいリンクされたサービスのボタン](./media/tutorial-incremental-copy-portal/watermark-dataset-new-connection-button.png)
9. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで、次の手順を行います。

    1. **[名前]** に「**AzureSqlDatabaseLinkedService**」と入力します。 
    2. **[サーバー名]** で、使用する Azure SQL Server を選択します。
    3. Azure SQL サーバーにアクセスするための**ユーザーの名前**を入力します。 
    4. ユーザーの**パスワード**を入力します。 
    5. Azure SQL データベースへの接続をテストするために、**[テスト接続]** をクリックします。
    6. **[Save]** をクリックします。
    7. **[接続]** タブで、**[リンクされたサービス]** で **[AzureSqlDatabaseLinkedService]** が選択されていることを確認します。
       
        ![[New linked service]\(新しいリンクされたサービス\) ウィンドウ](./media/tutorial-incremental-copy-portal/azure-sql-linked-service-settings.png)
10. **[テーブル]** で **[dbo].[watermarktable]** を選択します。 テーブル内のデータをプレビューする場合は、**[データのプレビュー]** をクリックします。

    ![基準データセット - 接続文字列](./media/tutorial-incremental-copy-portal/watermark-dataset-connection-settings.png)
11. 上部のパイプライン タブをクリックするか、左側のツリー ビューでパイプラインの名前をクリックして、パイプライン エディターに切り替えます。 **[検索]** アクティビティのプロパティ ウィンドウで、**[Source Dataset]\(ソース データセット\)** フィールドで **[WatermarkDataset]** が選択されていることを確認します。 

    ![パイプライン - 古い基準データセット](./media/tutorial-incremental-copy-portal/pipeline-old-watermark-dataset-selected.png)
12. **[アクティビティ]** ツールボックスで **[General]\(一般\)** を展開し、パイプライン デザイナー画面にもう一つの **[検索]** アクティビティをドラッグ アンド ドロップし、プロパティ ウィンドウの **[General]\(一般\)** タブで名前を「**LookupNewWaterMarkActivity**」に設定します。 この検索アクティビティは、ターゲットにコピーされるソース データを持つテーブルから新しい基準値を取得します。 

    ![2 つ目の検索アクティビティ - 名前](./media/tutorial-incremental-copy-portal/second-lookup-activity-name.png)
13. 2 つ目の **[検索]** アクティビティのプロパティ ウィンドウで **[設定]** タブに切り替え、**[新規]** をクリックします。 新しい基準値 (LastModifyTime の最大値) が含まれているソース テーブルを指すデータセットを作成します。 

    ![2 つ目の検索アクティビティ - 新しいデータセット](./media/tutorial-incremental-copy-portal/second-lookup-activity-settings-new-button.png)
14. **[新しいデータセット]** ウィンドウで **[Azure SQL Database]** を選択し、**[完了]** をクリックします。 このデータセット用の新しいタブが開かれます。 ツリー ビューにもデータセットが表示されます。 
15. プロパティ ウィンドウの **[全般]** タブで、**[名前]** に「**SourceDataset**」と入力します。 

    ![ソース データセット - 名前](./media/tutorial-incremental-copy-portal/source-dataset-name.png)
16. **[接続]** タブに切り替えて、次の手順を実行します。 

    1. **[リンクされたサービス]** で **[AzureSqlDatabaseLinkedService]** を選択します。
    2. [テーブル] で **[dbo].[data_source_table]** を選択します。 チュートリアルの後の方で、このデータセットに対してクエリを指定します。 クエリは、この手順で指定するテーブルで優先されます。 

        ![2 つ目の検索アクティビティ - 新しいデータセット](./media/tutorial-incremental-copy-portal/source-dataset-connection.png)
17. 上部のパイプライン タブをクリックするか、左側のツリー ビューでパイプラインの名前をクリックして、パイプライン エディターに切り替えます。 **[検索]** アクティビティのプロパティ ウィンドウで、**[Source Dataset]\(ソース データセット\)** フィールドで **[SourceDataset]** が選択されていることを確認します。 
18. **[クエリの使用]** フィールドで **[クエリ]** を選択し、次のクエリを入力します。単に **data_source_table** で **LastModifytime** の最大値を選択しています。 このクエリがない場合は、データセットの定義でテーブル名 (data_source_table) を指定したため、データセットはテーブルのすべての行を取得します。

    ```sql
    select MAX(LastModifytime) as NewWatermarkvalue from data_source_table
    ```

    ![2 つ目の検索アクティビティ - クエリ](./media/tutorial-incremental-copy-portal/query-for-new-watermark.png)
19. **[アクティビティ]** ツールボックスで **[DataFlow]** を展開し、[アクティビティ] ツールボックスから **[コピー]** アクティビティをドラッグ アンド ドロップして、名前を「**IncrementalCopyActivity**」に設定します。 

    ![コピー アクティビティ - 名前](./media/tutorial-incremental-copy-portal/copy-activity-name.png)
20. 検索アクティビティにアタッチされている**緑のボタン**をコピー アクティビティにドラッグして、**両方の検索アクティビティをコピー アクティビティに接続**します。 コピー アクティビティの境界線の色が青に変わったら、マウス ボタンを離します。 

    ![検索アクティビティのコピー アクティビティへの接続](./media/tutorial-incremental-copy-portal/connection-lookups-to-copy.png)
21. **[コピー アクティビティ]** を選択し、**[プロパティ]** ウィンドウにアクティビティのプロパティが表示されることを確認します。 

    ![コピー アクティビティのプロパティ](./media/tutorial-incremental-copy-portal/back-to-copy-activity-properties.png)
22. **[プロパティ]** ウィンドウで **[ソース]** タブに切り替え、以下の手順を実行します。

    1. **[Source Dataset]\(ソース データセット\)** フィールドで **[SourceDataset]** を選択します。 
    2. **[クエリの使用]** フィールドで **[クエリ]** を選択します。 
    3. **[クエリ]** フィールドに次の SQL クエリを入力します。 

        ```sql
        select * from data_source_table where LastModifytime > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and LastModifytime <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'
        ```
    
        ![コピー アクティビティ - ソース](./media/tutorial-incremental-copy-portal/copy-activity-source.png)
23. **[シンク]** タブに切り替えて、**[Sink Dataset]\(シンク データセット\)** フィールドの **[+ 新規]** をクリックします。 

    ![[新しいデータセット] ボタン](./media/tutorial-incremental-copy-portal/new-sink-dataset-button.png)
24. このチュートリアルでは、シンク データ ストアの種類は Azure Blob Storage です。 そのため、**[新しいデータセット]** ウィンドウで **[Azure BLOB ストレージ]** を選択し、**[完了]** をクリックします。 

    ![[Azure Blob Storage] の選択](./media/tutorial-incremental-copy-portal/select-azure-blob-storage.png)
25. データセットのプロパティ ウィンドウの **[全般]** タブで、**[名前]** に「**SinkDataset**」と入力します。 

    ![シンク データセット - 名前](./media/tutorial-incremental-copy-portal/sink-dataset-name.png)
26. **[接続]** タブに切り替えて、**[+ 新規]** をクリックします。 この手順では、**Azure Blob Storage** への接続 (リンクされたサービス) を作成します。

    ![シンク データセット - 新しい接続](./media/tutorial-incremental-copy-portal/sink-dataset-new-connection.png)
26. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで、次の手順を行います。 

    1. **[名前]** に「**AzureStorageLinkedService**」と入力します。 
    2. **[ストレージ アカウント名]** で、使用する Azure ストレージ アカウントを選択します。
    3. **[Save]** をクリックします。 

        ![Azure Storage のリンクされたサービス - 設定](./media/tutorial-incremental-copy-portal/azure-storage-linked-service-settings.png)
27. **[接続]** タブで、次の手順を実行します。

    1. **[リンクされたサービス]** で **AzureStorageLinkedService** が選択されていることを確認します。 
    2. **[ファイル パス]** フィールドの**フォルダー**部分に、「**adftutorial/incrementalcopy**」と入力します。 **adftutorial** は BLOB コンテナー名であり、**incrementalcopy** はフォルダー名です。 このスニペットは、BLOB ストレージに adftutorial という名前の BLOB コンテナーがあることを前提としています。 このコンテナーが存在しない場合は作成するか、既存のコンテナーの名前を設定してください。 Azure Data Factory は、出力フォルダー **incrementalcopy** が存在しない場合は、自動的に作成します。 **[ファイル パス]** の **[参照]** ボタンを使用して、BLOB コンテナー内のフォルダーに移動することもできます。 .RunId, '.txt')`.
    3. **[ファイル パス]** フィールドの**ファイル名**部分に、「`@CONCAT('Incremental-', pipeline().RunId, '.txt')`」と入力します。 ファイル名は、式を使用して動的に生成されます。 各パイプラインの実行には、一意の ID があります。 コピー アクティビティは、実行 ID を使用して、ファイル名を生成します。 

        ![シンク データセット - 接続文字列](./media/tutorial-incremental-copy-portal/sink-dataset-connection-settings.png)
28. 上部のパイプライン タブをクリックするか、左側のツリー ビューでパイプラインの名前をクリックして、**パイプライン** エディターに切り替えます。 
29. **[アクティビティ]** ツールボックスで **[General]\(一般\)** を展開し、**[アクティビティ]** ツールボックスからパイプライン デザイナー画面に **[ストアド プロシージャ]** アクティビティをドラッグ アンド ドロップします。 **[コピー]** アクティビティの緑 (成功) の出力を **[ストアド プロシージャ]** アクティビティに**接続**します。 
    
    ![コピー アクティビティ - ソース](./media/tutorial-incremental-copy-portal/connect-copy-to-stored-procedure-activity.png)
24. パイプライン デザイナーで **[ストアド プロシージャ アクティビティ]** を選択し、その名前を **StoredProceduretoWriteWatermarkActivity** に変更します。 

    ![ストアド プロシージャ アクティビティ - 名前](./media/tutorial-incremental-copy-portal/stored-procedure-activity-name.png)
25. **[SQL Account]\(SQL アカウント\)** タブに切り替えて、**[リンクされたサービス]** で *[AzureSqlDatabaseLinkedService]* \* を選択します。 

    ![ストアド プロシージャ アクティビティ - SQL アカウント](./media/tutorial-incremental-copy-portal/sp-activity-sql-account-settings.png)
26. **[ストアド プロシージャ]** タブに切り替えて、次の手順を実行します。 

    1. **[ストアド プロシージャ名]** に **[sp_write_watermark]** を選択します。 
    2. ストアド プロシージャのパラメーターの値を指定するには、**[Import parameter]\(インポート パラメーター\)** をクリックし、各パラメーターに次の値を入力します。 

        | Name | type | 値 | 
        | ---- | ---- | ----- | 
        | LastModifiedtime | Datetime | @{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue} |
        | TableName | String | @{activity('LookupOldWaterMarkActivity').output.firstRow.TableName} |

    ![ストアド プロシージャ アクティビティ - ストアド プロシージャの設定](./media/tutorial-incremental-copy-portal/sproc-activity-stored-procedure-settings.png)
27. パイプライン設定を検証するには、ツール バーの **[検証]** をクリックします。 検証エラーがないことを確認します。 [>>] をクリックして、**[Pipeline Validation Report]\(パイプライン検証レポート\)** ウィンドウを閉じます。   

    ![パイプラインの検証](./media/tutorial-incremental-copy-portal/validate-pipeline.png)
28. **[すべて公開]** ボタンを選択して、エンティティ (リンクされたサービス、データセット、およびパイプライン) を Azure Data Factory サービスに発行します。 発行が成功したというメッセージが表示されるまで待機します。 

    ![[発行] ボタン](./media/tutorial-incremental-copy-portal/publish-button.png)

## <a name="trigger-a-pipeline-run"></a>パイプラインの実行をトリガーする
1. ツール バーの **[トリガー]** をクリックし、**[Trigger Now]\(今すぐトリガー\)** をクリックします。 

    ![[Trigger Now]\(今すぐトリガー\) ボタン](./media/tutorial-incremental-copy-portal/trigger-now.png)
2. **[Pipeline Run]\(パイプラインの実行\)** ウィンドウで **[完了]** を選択します。 

## <a name="monitor-the-pipeline-run"></a>パイプラインの実行を監視します

1. 左側で **[監視]** タブに切り替えます。 手動トリガーによってトリガーされたパイプラインの実行の状態を確認できます。 一覧を更新するには、**[最新の情報に更新]** ボタンをクリックします。 
    
    ![パイプライン実行](./media/tutorial-incremental-copy-portal/pipeline-runs.png)
2. このパイプラインの実行に関連付けられているアクティビティの実行を表示するには、**[アクション]** 列にある最初のリンク (**[View Activity Runs]\(アクティビティの実行の表示\)**) をクリックします。 上部の **[パイプライン]** をクリックすると、前のビューに戻ることができます。 一覧を更新するには、**[最新の情報に更新]** ボタンをクリックします。

    ![アクティビティの実行](./media/tutorial-incremental-copy-portal/activity-runs.png)

## <a name="review-the-results"></a>結果の確認
1. [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) などのツールを使用して、Azure Storage アカウントに接続します。 **adftutorial** コンテナーの **incrementalcopy** フォルダーに出力ファイルが作成されていることを確認します。

    ![最初の出力ファイル](./media/tutorial-incremental-copy-portal/first-output-file.png)
2. 出力ファイルを開き、すべてのデータが **data_source_table** から BLOB ファイルにコピーされていることを確認します。 

    ```
    1,aaaa,2017-09-01 00:56:00.0000000
    2,bbbb,2017-09-02 05:23:00.0000000
    3,cccc,2017-09-03 02:36:00.0000000
    4,dddd,2017-09-04 03:21:00.0000000
    5,eeee,2017-09-05 08:06:00.0000000
    ```
3. `watermarktable` の最新の値をチェックします。 基準値が更新されたことを確認できます。

    ```sql
    Select * from watermarktable
    ```
    
    出力内容は次のとおりです。
 
    | TableName | WatermarkValue |
    | --------- | -------------- |
    | data_source_table | 2017-09-05    8:06:00.000 | 

## <a name="add-more-data-to-source"></a>ソースにデータを追加する

SQL データベース (データ ソース ストア) に新しいデータを挿入します。

```sql
INSERT INTO data_source_table
VALUES (6, 'newdata','9/6/2017 2:23:00 AM')

INSERT INTO data_source_table
VALUES (7, 'newdata','9/7/2017 9:01:00 AM')
``` 

SQL データベース内の更新されたデータは次のとおりです。

```
PersonID | Name | LastModifytime
-------- | ---- | --------------
1 | aaaa | 2017-09-01 00:56:00.000
2 | bbbb | 2017-09-02 05:23:00.000
3 | cccc | 2017-09-03 02:36:00.000
4 | dddd | 2017-09-04 03:21:00.000
5 | eeee | 2017-09-05 08:06:00.000
6 | newdata | 2017-09-06 02:23:00.000
7 | newdata | 2017-09-07 09:01:00.000
```


## <a name="trigger-another-pipeline-run"></a>もう一つのパイプラインの実行をトリガーする
1. **[編集]** タブに切り替えます。デザイナーでパイプラインが開かれていない場合は、ツリー ビューでパイプラインをクリックします。 

    ![[Trigger Now]\(今すぐトリガー\) ボタン](./media/tutorial-incremental-copy-portal/edit-tab.png)
2. ツール バーの **[トリガー]** をクリックし、**[Trigger Now]\(今すぐトリガー\)** をクリックします。 

    ![[Trigger Now]\(今すぐトリガー\) ボタン](./media/tutorial-incremental-copy-portal/trigger-now.png)

## <a name="monitor-the-second-pipeline-run"></a>2 回目のパイプラインの実行を監視します

1. 左側で **[監視]** タブに切り替えます。 手動トリガーによってトリガーされたパイプラインの実行の状態を確認できます。 一覧を更新するには、**[最新の情報に更新]** ボタンをクリックします。 
    
    ![パイプライン実行](./media/tutorial-incremental-copy-portal/pipeline-runs-2.png)
2. このパイプラインの実行に関連付けられているアクティビティの実行を表示するには、**[アクション]** 列にある最初のリンク (**[View Activity Runs]\(アクティビティの実行の表示\)**) をクリックします。 上部の **[パイプライン]** をクリックすると、前のビューに戻ることができます。 一覧を更新するには、**[最新の情報に更新]** ボタンをクリックします。

    ![アクティビティの実行](./media/tutorial-incremental-copy-portal/activity-runs-2.png)

## <a name="verify-the-second-output"></a>2 つ目の出力を検証する

1. BLOB ストレージで、別のファイルが作成されたことを確認します。 このチュートリアルでは、`Incremental-<GUID>.txt` が新しいファイルの名前になります。 このファイルを開くと、2 行のレコードが確認できます。

    ```
    6,newdata,2017-09-06 02:23:00.0000000
    7,newdata,2017-09-07 09:01:00.0000000    
    ```
2. `watermarktable` の最新の値をチェックします。 基準値が再び更新されたことを確認できます。

    ```sql
    Select * from watermarktable
    ```
    サンプル出力: 
    
    | TableName | WatermarkValue |
    | --------- | --------------- |
    | data_source_table | 2017-09-07 09:01:00.000 |


     
## <a name="next-steps"></a>次の手順
このチュートリアルでは、以下の手順を実行しました。 

> [!div class="checklist"]
> * 基準値を格納するためのデータ ストアを準備します。
> * データ ファクトリを作成します。
> * リンクされたサービスを作成します。 
> * ソース データセット、シンク データセット、および基準値データセットを作成します。
> * パイプラインを作成します。
> * パイプラインを実行します。
> * パイプラインの実行を監視します。 
> * 結果の確認
> * ソースにデータを追加します。
> * パイプラインを再実行します。
> * 2 回目のパイプラインの実行を監視します
> * 2 回目の実行の結果を確認します

このチュートリアルでは、パイプラインで SQL データベース内の単一のテーブルから BLOB ストレージにデータをコピーしました。 次のチュートリアルに進んで、オンプレミスの SQL Server データベースにある複数のテーブルから SQL データベースにデータをコピーする方法について学習しましょう。 

> [!div class="nextstepaction"]
>[SQL Server にある複数のテーブルから Azure SQL Database にデータを増分読み込みする](tutorial-incremental-copy-multiple-tables-portal.md)



