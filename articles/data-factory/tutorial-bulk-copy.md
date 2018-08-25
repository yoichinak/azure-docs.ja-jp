---
title: Azure Data Factory を使ってデータを一括コピーする | Microsoft Docs
description: Azure Data Factory とコピー アクティビティを使い、ソース データ ストアからコピー先データ ストアにデータを一括コピーする方法について説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: jingwang
ms.openlocfilehash: ec1216d1a42791a1334b7f9e2ed37f15aaffd013
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2018
ms.locfileid: "37085642"
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Azure Data Factory を使って複数のテーブルを一括コピーする
このチュートリアルでは、**Azure SQL Database から Azure SQL Data Warehouse に多数のテーブルをコピーする方法**について説明します。 同じパターンは他のコピー シナリオでも適用できます。 たとえば、SQL Server/Oracle から Azure SQL Database/Data Warehouse/Azure BLOB にテーブルをコピーしたり、BLOB から Azure SQL Database テーブルにさまざまなパスをコピーしたりするシナリオが該当します。

このチュートリアルは大まかに次の手順で構成されます。

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * Azure SQL Database、Azure SQL Data Warehouse、Azure Storage のリンクされたサービスを作成します。
> * Azure SQL Database と Azure SQL Data Warehouse のデータセットを作成します。
> * コピーするテーブルを検索するためのパイプラインと実際のコピー操作を実行するためのパイプラインを作成します。 
> * パイプラインの実行を開始します。
> * パイプラインとアクティビティの実行を監視します。

このチュートリアルでは、Azure PowerShell を使用します。 その他のツールまたは SDK を使ってデータ ファクトリを作成する方法については、[クイックスタート](quickstart-create-data-factory-dot-net.md)を参照してください。 

## <a name="end-to-end-workflow"></a>エンド ツー エンド ワークフロー
このシナリオでは、SQL Data Warehouse にコピーする必要のあるテーブルが Azure SQL Database に多数存在します。 以下の図は、パイプラインのワークフロー ステップを論理的な発生順に並べたものです。

![ワークフロー](media/tutorial-bulk-copy/tutorial-copy-multiple-tables.png)

* 1 つ目のパイプラインでは、シンク データ ストアにコピーするテーブルの一覧が検索されます。  代わりに、シンク データ ストアにコピーするすべてのテーブルが列挙されたメタデータ テーブルを用意する方法もあります。 次に、1 つ目のパイプラインによって 2 つ目のパイプラインがトリガーされ、データベース内の各テーブルを反復処理しながらデータのコピー操作が実行されます。
* 実際のコピーは 2 つ目のパイプラインによって実行されます。 このパイプラインは、テーブルの一覧をパラメーターとして受け取ります。 その一覧の各テーブルについて、Azure SQL Database 内の特定のテーブルを SQL Data Warehouse 内の該当するテーブルにコピーします。この処理には、パフォーマンスを最大限に高めるために、[BLOB ストレージと PolyBase によるステージング コピー](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)が使用されます。 この例では、1 つ目のパイプラインからパラメーターの値としてテーブルの一覧が渡されます。 

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="prerequisites"></a>前提条件

* **Azure PowerShell**。 [Azure PowerShell のインストールと構成の方法](/powershell/azure/install-azurerm-ps)に関するページに記載されている手順に従います。
* **Azure Storage アカウント**。 この Azure ストレージ アカウントは、一括コピー操作のステージング BLOB ストレージとして使用されます。 
* **Azure SQL データベース**。 ソース データが格納されているデータベースです。 
* **Azure SQL Data Warehouse**。 SQL データベースからコピーされたデータは、このデータ ウェアハウスに格納されます。 

### <a name="prepare-sql-database-and-sql-data-warehouse"></a>SQL Database と SQL Data Warehouse の準備

**ソース Azure SQL Database の準備**:

[Azure SQL データベースの作成](../sql-database/sql-database-get-started-portal.md)に関する記事に従い、Adventure Works LT サンプル データを使って Azure SQL データベースを作成します。 このチュートリアルでは、このサンプル データベースからすべてのテーブルを SQL データ ウェアハウスにコピーします。

**シンク Azure SQL Data Warehouse の準備**:

1. Azure SQL データ ウェアハウスがない場合は、「[SQL Data Warehouse の作成](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md)」の記事に書かれている作成手順を参照してください。

2. 対応するテーブル スキーマを SQL Data Warehouse に作成します。 Azure SQL Database から Azure SQL Data Warehouse への**スキーマの移行**には、[移行ユーティリティ](https://www.microsoft.com/download/details.aspx?id=49100)を使用できます。 データの移行/コピーは、後続の手順で Azure Data Factory を使用して行います。

## <a name="azure-services-to-access-sql-server"></a>SQL サーバーにアクセスするための Azure サービス

SQL Database と SQL Data Warehouse の両方について、SQL サーバーへのアクセスを Azure サービスに許可します。 ご利用の Azure SQL サーバーで **[Azure サービスへのアクセスを許可]** 設定を**オン**にしてください。 この設定により、Data Factory サービスが Azure SQL データベースからデータを読み取ったり、Azure SQL データ ウェアハウスにデータを書き込んだりすることができます。 この設定を確認して有効にするには、次の手順を実行します。

1. 左側にある **[すべてのサービス]** をクリックし、**[SQL Server]** をクリックします。
2. サーバーを選択し、**[設定]** の **[ファイアウォール]** をクリックします。
3. **[ファイアウォールの設定]** ページの **[Azure サービスへのアクセスを許可]** で **[オン]** をクリックします。

## <a name="create-a-data-factory"></a>Data Factory を作成する。

1. **PowerShell**を起動します。 Azure PowerShell は、このチュートリアルが終わるまで開いたままにしておいてください。 Azure PowerShell を閉じて再度開いた場合は、これらのコマンドをもう一度実行する必要があります。

    次のコマンドを実行して、Azure Portal へのサインインに使用するユーザー名とパスワードを入力します。
        
    ```powershell
    Connect-AzureRmAccount
    ```
    次のコマンドを実行して、このアカウントのすべてのサブスクリプションを表示します。

    ```powershell
    Get-AzureRmSubscription
    ```
    次のコマンドを実行して、使用するサブスクリプションを選択します。 **SubscriptionId** は、実際の Azure サブスクリプションの ID に置き換えてください。

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"
    ```
2. **Set-AzureRmDataFactoryV2** コマンドレットを実行してデータ ファクトリを作成します。 各プレースホルダーを実際の値に置き換えてからコマンドを実行してください。 

    ```powershell
    $resourceGroupName = "<your resource group to create the factory>"
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>"
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
    ```

    以下の点に注意してください。

    * Azure Data Factory の名前はグローバルに一意にする必要があります。 次のエラーが発生した場合は、名前を変更してからもう一度実行してください。

        ```
        The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
        ```

    * Data Factory インスタンスを作成するには、Azure サブスクリプションの共同作成者または管理者である必要があります。
    * 現在 Data Factory が利用できる Azure リージョンの一覧については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)」ページで目的のリージョンを選択し、**[分析]** を展開して **[Data Factory]** を探してください。 データ ファクトリで使用するデータ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (HDInsight など) は他のリージョンに配置できます。

## <a name="create-linked-services"></a>リンクされたサービスを作成します

このチュートリアルでは、ソース、シンク、ステージング BLOB のそれぞれについて、データ ストアへの接続情報が含まれた、3 つのリンクされたサービスを作成します。

### <a name="create-the-source-azure-sql-database-linked-service"></a>ソース Azure SQL Database のリンクされたサービスを作成する

1. 以下の内容を記述した **AzureSqlDatabaseLinkedService.json** という名前の JSON ファイルを **C:\ADFv2TutorialBulkCopy** フォルダー内に作成します (ADFv2TutorialBulkCopy フォルダーがない場合は作成します)。

    > [!IMPORTANT]
    > &lt;servername&gt;、&lt;databasename&gt;、&lt;username&gt;@&lt;servername&gt;、&lt;password&gt; を実際の Azure SQL データベースの値に置き換えてからファイルを保存してください。

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
                }
            }
        }
    }
    ```

2. **Azure PowerShell** で **ADFv2TutorialBulkCopy** フォルダーに切り替えます。

3. **Set-AzureRmDataFactoryV2LinkedService** コマンドレットを実行して、リンクされたサービス **AzureSqlDatabaseLinkedService** を作成します。 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

    出力例を次に示します。

    ```json
    LinkedServiceName : AzureSqlDatabaseLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

### <a name="create-the-sink-azure-sql-data-warehouse-linked-service"></a>シンク Azure SQL Data Warehouse のリンクされたサービスを作成する

1. 次の内容を記述した **AzureSqlDWLinkedService.json** という名前の JSON ファイルを **C:\ADFv2TutorialBulkCopy** フォルダーに作成します。

    > [!IMPORTANT]
    > &lt;servername&gt;、&lt;databasename&gt;、&lt;username&gt;@&lt;servername&gt;、&lt;password&gt; を実際の Azure SQL データベースの値に置き換えてからファイルを保存してください。

    ```json
    {
        "name": "AzureSqlDWLinkedService",
        "properties": {
            "type": "AzureSqlDW",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
            }
        }
    }
    ```

2. リンクされたサービス **AzureSqlDWLinkedService** を作成するには、**Set-AzureRmDataFactoryV2LinkedService** コマンドレットを実行します。

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDWLinkedService" -File ".\AzureSqlDWLinkedService.json"
    ```

    出力例を次に示します。

    ```json
    LinkedServiceName : AzureSqlDWLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDWLinkedService
    ```

### <a name="create-the-staging-azure-storage-linked-service"></a>ステージング Azure Storage のリンクされたサービスを作成する

このチュートリアルでは、PolyBase でコピーのパフォーマンスを高めるために、中間ステージング領域として Azure BLOB ストレージを使用しています。

1. 次の内容を記述した **AzureStorageLinkedService.json** という名前の JSON ファイルを **C:\ADFv2TutorialBulkCopy** フォルダーに作成します。

    > [!IMPORTANT]
    > &lt;accountName&gt; と &lt;accountKey&gt; を実際の Azure ストレージ アカウントの名前とキーに置き換えてからファイルを保存してください。

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
                }
            }
        }
    }
    ```

2. リンクされたサービス **AzureStorageLinkedService** を作成するには、**Set-AzureRmDataFactoryV2LinkedService** コマンドレットを実行します。

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
    ```

    出力例を次に示します。

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

## <a name="create-datasets"></a>データセットを作成する

このチュートリアルでは、ソースとシンクのデータセットを作成して、データの格納場所を指定します。

### <a name="create-a-dataset-for-source-sql-database"></a>ソース SQL Database のデータセットを作成する

1. 次の内容を記述した **AzureSqlDatabaseDataset.json** という名前の JSON ファイルを **C:\ADFv2TutorialBulkCopy** フォルダーに作成します。 "tableName" はダミーです (後でデータを取得するためのコピー アクティビティで SQL クエリを使用することになるため)。

    ```json
    {
        "name": "AzureSqlDatabaseDataset",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": {
                "referenceName": "AzureSqlDatabaseLinkedService",
                "type": "LinkedServiceReference"
            },
            "typeProperties": {
                "tableName": "dummy"
            }
        }
    }
    ```

2. データセット **AzureSqlDatabaseDataset** を作成するには、**Set-AzureRmDataFactoryV2Dataset** コマンドレットを実行します。

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDatabaseDataset" -File ".\AzureSqlDatabaseDataset.json"
    ```

    出力例を次に示します。

    ```json
    DatasetName       : AzureSqlDatabaseDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-dataset-for-sink-sql-data-warehouse"></a>シンク SQL Data Warehouse のデータセットを作成する

1. **C:\ADFv2TutorialBulkCopy** フォルダーに、**AzureSqlDWDataset.json** という名前で以下の内容の JSON ファイルを作成します。"tableName" がパラメーターとして設定されています。後で、このデータセットを参照するコピー アクティビティによって、実際の値がデータセットに渡されます。

    ```json
    {
        "name": "AzureSqlDWDataset",
        "properties": {
            "type": "AzureSqlDWTable",
            "linkedServiceName": {
                "referenceName": "AzureSqlDWLinkedService",
                "type": "LinkedServiceReference"
            },
            "typeProperties": {
                "tableName": {
                    "value": "@{dataset().DWTableName}",
                    "type": "Expression"
                }
            },
            "parameters":{
                "DWTableName":{
                    "type":"String"
                }
            }
        }
    }
    ```

2. データセット **AzureSqlDWDataset** を作成するには、**Set-AzureRmDataFactoryV2Dataset** コマンドレットを実行します。

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDWDataset" -File ".\AzureSqlDWDataset.json"
    ```

    出力例を次に示します。

    ```json
    DatasetName       : AzureSqlDWDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDwTableDataset
    ```

## <a name="create-pipelines"></a>パイプラインを作成する

このチュートリアルでは、2 つのパイプラインを作成します。

### <a name="create-the-pipeline-iterateandcopysqltables"></a>パイプライン "IterateAndCopySQLTables" を作成する

このパイプラインは、テーブルの一覧をパラメーターとして受け取ります。 その一覧の各テーブルについて、ステージング コピーと PolyBase を使って、Azure SQL Database 内のテーブルから Azure SQL Data Warehouse にデータがコピーされます。

1. 次の内容を記述した **IterateAndCopySQLTables.json** という名前の JSON ファイルを **C:\ADFv2TutorialBulkCopy** フォルダーに作成します。

    ```json
    {
        "name": "IterateAndCopySQLTables",
        "properties": {
            "activities": [
                {
                    "name": "IterateSQLTables",
                    "type": "ForEach",
                    "typeProperties": {
                        "isSequential": "false",
                        "items": {
                            "value": "@pipeline().parameters.tableList",
                            "type": "Expression"
                        },
                        "activities": [
                            {
                                "name": "CopyData",
                                "description": "Copy data from SQL database to SQL DW",
                                "type": "Copy",
                                "inputs": [
                                    {
                                        "referenceName": "AzureSqlDatabaseDataset",
                                        "type": "DatasetReference"
                                    }
                                ],
                                "outputs": [
                                    {
                                        "referenceName": "AzureSqlDWDataset",
                                        "type": "DatasetReference",
                                        "parameters": {
                                            "DWTableName": "[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]"
                                        }
                                    }
                                ],
                                "typeProperties": {
                                    "source": {
                                        "type": "SqlSource",
                                        "sqlReaderQuery": "SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]"
                                    },
                                    "sink": {
                                        "type": "SqlDWSink",
                                        "preCopyScript": "TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]",
                                        "allowPolyBase": true
                                    },
                                    "enableStaging": true,
                                    "stagingSettings": {
                                        "linkedServiceName": {
                                            "referenceName": "AzureStorageLinkedService",
                                            "type": "LinkedServiceReference"
                                        }
                                    }
                                }
                            }
                        ]
                    }
                }
            ],
            "parameters": {
                "tableList": {
                    "type": "Object"
                }
            }
        }
    }
    ```

2. パイプライン **IterateAndCopySQLTables** を作成するには、**Set-AzureRmDataFactoryV2Pipeline** コマンドレットを実行します。

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IterateAndCopySQLTables" -File ".\IterateAndCopySQLTables.json"
    ```

    出力例を次に示します。

    ```json
    PipelineName      : IterateAndCopySQLTables
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {IterateSQLTables}
    Parameters        : {[tableList, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>パイプライン "GetTableListAndTriggerCopyData" を作成する

このパイプラインでは、次の 2 つのステップが実行されます。

* Azure SQL Database システム テーブルを検索してコピーするテーブルの一覧を取得します。
* パイプライン "IterateAndCopySQLTables" をトリガーして実際にデータのコピーを実行します。

1. **C:\ADFv2TutorialBulkCopy** フォルダーに、**GetTableListAndTriggerCopyData.json** という名前で以下の内容の JSON ファイルを作成します。

    ```json
    {
        "name":"GetTableListAndTriggerCopyData",
        "properties":{
            "activities":[
                { 
                    "name": "LookupTableList",
                    "description": "Retrieve the table list from Azure SQL dataabse",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'"
                        },
                        "dataset": {
                            "referenceName": "AzureSqlDatabaseDataset",
                            "type": "DatasetReference"
                        },
                        "firstRowOnly": false
                    }
                },
                {
                    "name": "TriggerCopy",
                    "type": "ExecutePipeline",
                    "typeProperties": {
                        "parameters": {
                            "tableList": {
                                "value": "@activity('LookupTableList').output.value",
                                "type": "Expression"
                            }
                        },
                        "pipeline": {
                            "referenceName": "IterateAndCopySQLTables",
                            "type": "PipelineReference"
                        },
                        "waitOnCompletion": true
                    },
                    "dependsOn": [
                        {
                            "activity": "LookupTableList",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        }
                    ]
                }
            ]
        }
    }
    ```

2. パイプライン **GetTableListAndTriggerCopyData** を作成するには、**Set-AzureRmDataFactoryV2Pipeline** コマンドレットを実行します。

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "GetTableListAndTriggerCopyData" -File ".\GetTableListAndTriggerCopyData.json"
    ```

    出力例を次に示します。

    ```json
    PipelineName      : GetTableListAndTriggerCopyData
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {LookupTableList, TriggerCopy}
    Parameters        :
    ```

## <a name="start-and-monitor-pipeline-run"></a>パイプラインの実行を開始して監視する

1. 主なパイプラインである "GetTableListAndTriggerCopyData" の実行を開始し、後で監視できるようパイプライン実行 ID をキャプチャします。 この ID の下で、ExecutePipeline アクティビティに指定されたパイプライン "IterateAndCopySQLTables" の実行がトリガーされます。

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'GetTableListAndTriggerCopyData'
    ```

2.  次のスクリプトを実行して、パイプライン **GetTableListAndTriggerCopyData** の状態を常時チェックし、最終的なパイプライン実行とアクティビティ実行の結果を出力します。

    ```powershell
    while ($True) {
        $run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                Write-Host "Pipeline run details:" -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 15
    }

    $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result
    ```

    サンプル実行の出力結果を次に示します。

    ```json
    Pipeline run details:
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    RunId             : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    LastUpdated       : 9/18/2017 4:08:15 PM
    Parameters        : {}
    RunStart          : 9/18/2017 4:06:44 PM
    RunEnd            : 9/18/2017 4:08:15 PM
    DurationInMs      : 90637
    Status            : Succeeded
    Message           : 

    Activity run details:
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : LookupTableList
    PipelineRunId     : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    Input             : {source, dataset, firstRowOnly}
    Output            : {count, value, effectiveIntegrationRuntime}
    LinkedServiceName : 
    ActivityRunStart  : 9/18/2017 4:06:46 PM
    ActivityRunEnd    : 9/18/2017 4:07:09 PM
    DurationInMs      : 22995
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : TriggerCopy
    PipelineRunId     : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    Input             : {pipeline, parameters, waitOnCompletion}
    Output            : {pipelineRunId}
    LinkedServiceName : 
    ActivityRunStart  : 9/18/2017 4:07:11 PM
    ActivityRunEnd    : 9/18/2017 4:08:14 PM
    DurationInMs      : 62581
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    ```

3. 次のようにして、パイプライン "**IterateAndCopySQLTables**" の実行 ID を取得し、詳細なアクティビティの実行結果をチェックすることができます。

    ```powershell
    Write-Host "Pipeline 'IterateAndCopySQLTables' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "TriggerCopy"}).Output.ToString()
    ```

    サンプル実行の出力結果を次に示します。

    ```json
    {
        "pipelineRunId": "7514d165-14bf-41fb-b5fb-789bea6c9e58"
    }
    ```

    ```powershell
    $result2 = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId <copy above run ID> -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result2
    ```

3. シンク Azure SQL Data Warehouse に接続し、Azure SQL Database から正しくデータがコピーされていることを確認します。

## <a name="next-steps"></a>次の手順
このチュートリアルでは、以下の手順を実行しました。 

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * Azure SQL Database、Azure SQL Data Warehouse、Azure Storage のリンクされたサービスを作成します。
> * Azure SQL Database と Azure SQL Data Warehouse のデータセットを作成します。
> * コピーするテーブルを検索するためのパイプラインと実際のコピー操作を実行するためのパイプラインを作成します。 
> * パイプラインの実行を開始します。
> * パイプラインとアクティビティの実行を監視します。

次のチュートリアルに進んで、ソースからコピー先にデータを増分コピーする方法について学習しましょう。
> [!div class="nextstepaction"]
>[データを増分コピーする](tutorial-incremental-copy-powershell.md)
