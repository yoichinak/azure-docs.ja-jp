---
title: Azure Data Factory で Spark を使用してデータを変換する | Microsoft Docs
description: このチュートリアルでは、Azure Data Factory で Spark アクティビティを使用してデータを変換するための詳細な手順を説明します。
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: douglasl
ms.openlocfilehash: 5c6192fdf235b5ecb1148b024da7b11f844e9321
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2018
ms.locfileid: "37081085"
---
# <a name="transform-data-in-the-cloud-by-using-spark-activity-in-azure-data-factory"></a>Azure Data Factory で Spark アクティビティを使用してクラウドのデータを変換する
このチュートリアルでは、Azure PowerShell を使用して Data Factory パイプラインを作成します。このパイプラインで、Spark アクティビティとオンデマンドの HDInsight のリンクされたサービスを使用してデータを変換します。 このチュートリアルでは、以下の手順を実行します。

> [!div class="checklist"]
> * データ ファクトリを作成します。 
> * リンクされたサービスを作成してデプロイします。
> * パイプラインを作成してデプロイします。 
> * パイプラインの実行を開始します。
> * パイプラインの実行を監視します。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="prerequisites"></a>前提条件
* **Azure Storage アカウント**。 Python スクリプトと入力ファイルを作成し、Azure ストレージにアップロードします。 Spark プログラムからの出力は、このストレージ アカウントに格納されます。 オンデマンドの Spark クラスターでは、同じストレージ アカウントがプライマリ ストレージとして使用されます。  
* **Azure PowerShell**。 [Azure PowerShell のインストールと構成の方法](/powershell/azure/install-azurerm-ps)に関するページに記載されている手順に従います。


### <a name="upload-python-script-to-your-blob-storage-account"></a>Python スクリプトを BLOB ストレージ アカウントにアップロードする
1. 次の内容が含まれた、**WordCount_Spark.py** という名前の Python ファイルを作成します。 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. **&lt;storageAccountName&gt;** を Azure ストレージ アカウントの名前に置き換えます。 その後、ファイルを保存します。 
3. Azure BLOB ストレージで、**adftutorial** という名前のコンテナーを作成します (存在しない場合)。 
4. **spark** という名前のフォルダーを作成します。
5. **spark** フォルダーの下に、**script** という名前のサブフォルダーを作成します。 
6. **WordCount_Spark.py** ファイルを **script** サブフォルダーにアップロードします。 


### <a name="upload-the-input-file"></a>入力ファイルをアップロードする
1. **minecraftstory.txt** という名前のファイルを作成し、任意のテキストを入力しておきます。 このテキストの単語数が Spark プログラムによってカウントされます。 
2. `spark` フォルダーに、`inputfiles` という名前のサブフォルダーを作成します。 
3. `minecraftstory.txt` を `inputfiles` サブフォルダーにアップロードします。 

## <a name="author-linked-services"></a>リンクされたサービスを作成する
このセクションでは、次の 2 つのリンクされたサービスを作成します。 
    
- Azure ストレージ アカウントをデータ ファクトリにリンクする、Azure Storage のリンクされたサービス。 このストレージは、オンデマンドの HDInsight クラスターによって使用されます。 ここには、実行される Spark スクリプトも含まれています。 
- オンデマンドの HDInsight のリンクされたサービス。 Azure Data Factory によって自動的に HDInsight クラスターが作成され、Spark プログラムが実行されます。その後、アイドル状態が事前に構成した時間を超えたら、HDInsight クラスターは削除されます。 

### <a name="azure-storage-linked-service"></a>Azure Storage のリンクされたサービス
任意のエディターを使用して JSON ファイルを作成し、Azure Storage のリンクされたサービスから次の JSON 定義をコピーして、ファイルを **MyStorageLinkedService.json** として保存します。  

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": {
          "value": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>",
          "type": "SecureString"
        }
      }
    }
}
```
&lt;storageAccountName&gt; と &lt;storageAccountKey&gt; を Azure ストレージ アカウントの名前とキーで更新してください。 


### <a name="on-demand-hdinsight-linked-service"></a>オンデマンドの HDInsight のリンクされたサービス
任意のエディターを使用して JSON ファイルを作成し、Azure HDInsight のリンクされたサービスから次の JSON 定義をコピーして、ファイルを **MyOnDemandSparkLinkedService.json** として保存します。  

```json
{
    "name": "MyOnDemandSparkLinkedService",
    "properties": {
      "type": "HDInsightOnDemand",
      "typeProperties": {
        "clusterSize": 2,
        "clusterType": "spark",
        "timeToLive": "00:15:00",
        "hostSubscriptionId": "<subscriptionID> ",
        "servicePrincipalId": "<servicePrincipalID>",
        "servicePrincipalKey": {
          "value": "<servicePrincipalKey>",
          "type": "SecureString"
        },
        "tenant": "<tenant ID>",
        "clusterResourceGroup": "<resourceGroupofHDICluster>",
        "version": "3.6",
        "osType": "Linux",
        "clusterNamePrefix":"ADFSparkSample",
        "linkedServiceName": {
          "referenceName": "MyStorageLinkedService",
          "type": "LinkedServiceReference"
        }
      }
    }
}
```
リンクされたサービスの定義で、以下のプロパティの値を更新します。 

- **hostSubscriptionId**。 &lt;subscriptionID&gt; は、実際の Azure サブスクリプションの ID に置き換えてください。 オンデマンドの HDInsight クラスターは、このサブスクリプション内に作成されます。 
- **tenant**。 &lt;tenantID&gt; は、実際の Azure テナントの ID に置き換えてください。 
- **servicePrincipalId**、**servicePrincipalKey**。 &lt;servicePrincipalID&gt; と &lt;servicePrincipalKey&gt; は、Azure Active Directory の実際のサービス プリンシパルの ID とキーに置き換えてください。 このサービス プリンシパルは、サブスクリプションまたはクラスターが作成されるリソース グループの共同作成者ロールのメンバーである必要があります。 詳細については、[Azure Active Directory のアプリケーションとサービス プリンシパルの作成](../azure-resource-manager/resource-group-create-service-principal-portal.md)に関するページを参照してください。 
- **clusterResourceGroup**。 &lt;resourceGroupOfHDICluster&gt; は、HDInsight クラスターの作成先であるリソース グループの名前に置き換えてください。 

> [!NOTE]
> Azure HDInsight には、サポートされる各 Azure リージョンで使用できるコアの合計数に制限があります。 オンデマンドの HDInsight のリンクされるサービスの場合、HDInsight クラスターは、プライマリ ストレージとして使用される Azure ストレージと同じ場所に作成されます。 クラスターを正常に作成するための十分なコア クォータがあることを確認します。 詳細については、「[Hadoop、Spark、Kafka などの HDInsight クラスターをセットアップする](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。 


## <a name="author-a-pipeline"></a>パイプラインを作成する 
この手順では、Spark アクティビティがある新しいパイプラインを作成します。 アクティビティでは **word count** サンプルが使用されます。 まだコンテンツをダウンロードしていない場合は、この場所からダウンロードします。

任意のエディターで JSON ファイルを作成し、パイプライン定義から次の JSON 定義をコピーして、**MySparkOnDemandPipeline.json** として保存します。 

```json
{
  "name": "MySparkOnDemandPipeline",
  "properties": {
    "activities": [
      {
        "name": "MySparkActivity",
        "type": "HDInsightSpark",
        "linkedServiceName": {
            "referenceName": "MyOnDemandSparkLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "rootPath": "adftutorial/spark",
          "entryFilePath": "script/WordCount_Spark.py",
          "getDebugInfo": "Failure",
          "sparkJobLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }
    ]
  }
}
```

以下の点に注意してください。 

- rootPath は、adftutorial コンテナーの spark フォルダーを指します。 
- entryFilePath は、spark フォルダーの script サブフォルダーの WordCount_Spark.py ファイルを指します。 


## <a name="create-a-data-factory"></a>Data Factory を作成する。 
JSON ファイルで、リンクされたサービスとパイプライン定義を作成しました。 次に、PowerShell コマンドレットを使用して、データ ファクトリを作成し、リンクされたサービスとパイプライン JSON ファイルをデプロイしましょう。 以下の PowerShell コマンドを 1 つずつ実行します。 

1. 変数を 1 つずつ設定します。

    **リソース グループ名**
    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup" 
    ```

    **データ ファクトリ名。名前はグローバルに一意である必要があります。** 
    ```powershell
    $dataFactoryName = "MyDataFactory09102017"
    ```
    
    **パイプライン名**
    ```powershell
    $pipelineName = "MySparkOnDemandPipeline" # Name of the pipeline
    ```
2. **PowerShell**を起動します。 Azure PowerShell は、このクイックスタートが終わるまで開いたままにしておいてください。 Azure PowerShell を閉じて再度開いた場合は、これらのコマンドをもう一度実行する必要があります。 現在 Data Factory が利用できる Azure リージョンの一覧については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)」ページで目的のリージョンを選択し、**[分析]** を展開して **[Data Factory]** を探してください。 データ ファクトリで使用するデータ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (HDInsight など) は他のリージョンに配置できます。

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
3. リソース グループ ADFTutorialResourceGroup を作成します。 

    ```powershell
    New-AzureRmResourceGroup -Name $resourceGroupName -Location "East Us" 
    ```
4. データ ファクトリを作成します。 

    ```powershell
     $df = Set-AzureRmDataFactoryV2 -Location EastUS -Name $dataFactoryName -ResourceGroupName $resourceGroupName
    ```

    次のコマンドを実行して、出力を表示します。 

    ```powershell
    $df
    ```
5. JSON ファイルを作成したフォルダーに移動し、次のコマンドを実行して、Azure Storage のリンクされたサービスをデプロイします。 
       
    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyStorageLinkedService" -File "MyStorageLinkedService.json"
    ```
6. 次のコマンドを実行し、オンデマンドの Spark のリンクされたサービスをデプロイします。 
       
    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyOnDemandSparkLinkedService" -File "MyOnDemandSparkLinkedService.json"
    ```
7. 次のコマンドを実行し、パイプラインをデプロイします。 
       
    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name $pipelineName -File "MySparkOnDemandPipeline.json"
    ```
    
## <a name="start-and-monitor-a-pipeline-run"></a>パイプラインの実行を開始して監視する  

1. パイプラインの実行を開始します。 将来の監視のために、パイプラインの実行の ID もキャプチャされます。

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName  
    ```
2. 次のスクリプトを実行し、パイプラインの実行の状態を、完了するまで継続的にチェックします。

    ```powershell
    while ($True) {
        $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    
        if(!$result) {
            Write-Host "Waiting for pipeline to start..." -foregroundcolor "Yellow"
        }
        elseif (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
        }
        else {
            Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
        ($result | Format-List | Out-String)
        Start-Sleep -Seconds 15
    }

    Write-Host "Activity `Output` section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"

    Write-Host "Activity `Error` section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n" 
    ```  
3. サンプル実行の出力結果を次に示します。 

    ```
    Pipeline run status: In Progress
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : 
    ActivityName      : MySparkActivity
    PipelineRunId     : 94e71d08-a6fa-4191-b7d1-cf8c71cb4794
    PipelineName      : MySparkOnDemandPipeline
    Input             : {rootPath, entryFilePath, getDebugInfo, sparkJobLinkedService}
    Output            : 
    LinkedServiceName : 
    ActivityRunStart  : 9/20/2017 6:33:47 AM
    ActivityRunEnd    : 
    DurationInMs      : 
    Status            : InProgress
    Error             :
    …
    
    Pipeline ' MySparkOnDemandPipeline' run finished. Result:
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : MyDataFactory09102017
    ActivityName      : MySparkActivity
    PipelineRunId     : 94e71d08-a6fa-4191-b7d1-cf8c71cb4794
    PipelineName      : MySparkOnDemandPipeline
    Input             : {rootPath, entryFilePath, getDebugInfo, sparkJobLinkedService}
    Output            : {clusterInUse, jobId, ExecutionProgress, effectiveIntegrationRuntime}
    LinkedServiceName : 
    ActivityRunStart  : 9/20/2017 6:33:47 AM
    ActivityRunEnd    : 9/20/2017 6:46:30 AM
    DurationInMs      : 763466
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity Output section:
    "clusterInUse": "https://ADFSparkSamplexxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.azurehdinsight.net/"
    "jobId": "0"
    "ExecutionProgress": "Succeeded"
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
    Activity Error section:
    "errorCode": ""
    "message": ""
    "failureType": ""
    "target": "MySparkActivity"
    ```
4. adftutorial コンテナーの `spark` フォルダーに `outputfiles` というフォルダーが作成され、Spark プログラムの出力が含まれていることを確認します。 


## <a name="next-steps"></a>次の手順
このサンプルのパイプラインは、Azure BLOB ストレージ内のある場所から別の場所にデータをコピーするものです。 以下の方法について学習しました。 

> [!div class="checklist"]
> * データ ファクトリを作成します。 
> * リンクされたサービスを作成してデプロイします。
> * パイプラインを作成してデプロイします。 
> * パイプラインの実行を開始します。
> * パイプラインの実行を監視します。

仮想ネットワークにある Azure HDInsight クラスター上で Hive スクリプトを実行してデータを変換する方法については、次のチュートリアルに進んでください。 

> [!div class="nextstepaction"]
> [チュートリアル: Azure Virtual Network で Hive を使用してデータを変換する](tutorial-transform-data-hive-virtual-network.md)





