---
title: REST API を使用して Azure データ ファクトリを作成する | Microsoft Docs
description: Azure データ ファクトリを作成して、Azure Blob Storage 内のある場所から別の場所にデータをコピーします。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: rest-api
ms.topic: hero-article
ms.date: 01/22/2018
ms.author: jingwang
ms.openlocfilehash: ecdebad7217e27e4439562a6b3002cb203f803e2
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2018
ms.locfileid: "41920616"
---
# <a name="create-an-azure-data-factory-and-pipeline-by-using-the-rest-api"></a>REST API を使用して Azure データ ファクトリとパイプラインを作成する
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [現在のバージョン](quickstart-create-data-factory-rest-api.md)

Azure Data Factory は、データドリブン型のワークフローをクラウドに作成することでデータの移動と変換を制御し、自動化することができるクラウドベースのデータ統合サービスです。 Azure Data Factory を使えば、データ主導型のワークフロー (パイプライン) を作成し、スケジューリングできます。具体的には、各種データ ストアからデータを取り込む、そのデータを各種コンピューティング サービス (Azure HDInsight Hadoop、Spark、Azure Data Lake Analytics、Azure Machine Learning など) で処理/変換する、データ ストア (Azure SQL Data Warehouse など) に出力データを公開して、それを利用するビジネス インテリジェンス (BI) アプリケーションに提供するという一連の処理を行えるワークフローです。 

このクイックスタートでは、REST API を使用して Azure データ ファクトリを作成する方法について説明します。 このデータ ファクトリのパイプラインは、Azure BLOB ストレージ内のある場所から別の場所にデータをコピーするものです。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 サブスクリプションがない場合は、[無料試用版](http://azure.microsoft.com/pricing/free-trial/)アカウントを作成できます。
* **Azure Storage アカウント**。 BLOB ストレージを、**ソース**と**シンク**のデータ ストアとして使用します。 Azure ストレージ アカウントがない場合、ストレージ アカウントの作成手順については、「[ストレージ アカウントの作成](../storage/common/storage-quickstart-create-account.md)」を参照してください。
* Blob Storage に **BLOB コンテナー**を作成し、コンテナーに入力**フォルダー**を作成して、フォルダーにいくつかのファイルをアップロードします。 [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) などのツールを使用して、Azure Blob Storage への接続、BLOB コンテナーの作成、入力ファイルのアップロード、出力ファイルの検証を行うことができます。
* **Azure PowerShell**をインストールします。 [Azure PowerShell のインストールと構成の方法](/powershell/azure/install-azurerm-ps)に関するページに記載されている手順に従います。 このクイックスタートでは、PowerShell を使用して、REST API 呼び出しを実行します。
* [この手順](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application)に従って、**Azure Active Directory にアプリケーションを作成します**。 **アプリケーション ID**、**認証キー**、**テナント ID** の値をメモしておいてください。後の手順で使用します。 アプリケーションを "**共同作成者**" ロールに割り当てます。

## <a name="set-global-variables"></a>グローバル変数の設定

1. **PowerShell**を起動します。 Azure PowerShell は、このクイックスタートが終わるまで開いたままにしておいてください。 Azure PowerShell を閉じて再度開いた場合は、これらのコマンドをもう一度実行する必要があります。

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
2. プレースホルダーを独自の値に置き換えた後、次のコマンドを実行して、後の手順で使用するグローバル変数を設定します。

    ```powershell
    $tenantID = "<your tenant ID>"
    $appId = "<your application ID>"
    $authKey = "<your authentication key for the application>"
    $subsId = "<your subscription ID to create the factory>"
    $resourceGroup = "<your resource group to create the factory>"
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>"
    $apiVersion = "2017-09-01-preview"
    ```

## <a name="authenticate-with-azure-ad"></a>Azure AD による認証

Azure Active Directory (AAD) で認証するには、次のコマンドを実行します。

```powershell
$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]"https://login.microsoftonline.com/${tenantId}"
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($appId, $authKey)
$result = $AuthContext.AcquireToken("https://management.core.windows.net/", $cred)
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
} 
```

## <a name="create-a-data-factory"></a>Data Factory を作成する。

次のコマンドを実行して、データ ファクトリを作成します。

```powershell
$request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}?api-version=${apiVersion}"
$body = @"
{
    "name": "$dataFactoryName",
    "location": "East US",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

以下の点に注意してください。

* Azure Data Factory の名前はグローバルに一意にする必要があります。 次のエラーが発生した場合は、名前を変更してからもう一度実行してください。

    ```
    Data factory name "ADFv2QuickStartDataFactory" is not available.
    ```
* 現在 Data Factory が利用できる Azure リージョンの一覧については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)」ページで目的のリージョンを選択し、**[分析]** を展開して **[Data Factory]** を探してください。 データ ファクトリで使用するデータ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (HDInsight など) は他のリージョンに配置できます。

応答例を次に示します。

```json

{
    "name":  "<dataFactoryName>",
    "tags": {

            },
    "properties":  {
        "provisioningState":  "Succeeded",
        "loggingStorageAccountKey":  "**********",
        "createTime":  "2017-09-14T06:22:59.9106216Z",
        "version":  "2017-09-01-preview"
    },
    "identity":  {
        "type":  "SystemAssigned",
        "principalId":  "<service principal ID>",
        "tenantId":  "<tenant ID>"
    },
    "id":  "dataFactoryName",
    "type":  "Microsoft.DataFactory/factories",
    "location":  "East US"
} 

```

## <a name="create-linked-services"></a>リンクされたサービスを作成します

データ ストアおよびコンピューティング サービスをデータ ファクトリにリンクするには、リンクされたサービスをデータ ファクトリに作成します。 このクイックスタートでは、コピー ソースとシンク ストアの両方として、Azure Storage のリンクされたサービスを 1 つ作成するだけで済みます。このサービスは、サンプルでは "AzureStorageLinkedService" という名前です。

次のコマンドを実行して、**AzureStorageLinkedService** という名前のリンクされたサービスを作成します。

コマンドを実行する前に、&lt;accountName&gt; と &lt;accountKey&gt; を Azure ストレージ アカウントの名前とキーに置き換えます。

```powershell
$request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/linkedservices/AzureStorageLinkedService?api-version=${apiVersion}"
$body = @"
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>",
                "type": "SecureString"
            }
        }
    }
}
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

出力例を次に示します。

```json
{
    "id":  "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/linkedservices/AzureStorageLinkedService",
    "name":  "AzureStorageLinkedService",
    "properties":  {
                       "type":  "AzureStorage",
                       "typeProperties":  {
                                              "connectionString":  "@{value=**********; type=SecureString}"
                                          }
                   },
    "etag":  "0000c552-0000-0000-0000-59b1459c0000"
}
```

## <a name="create-datasets"></a>データセットを作成する

ソースからシンクにコピーするデータを表すデータセットを定義します。 この例のこの BLOB データセットは、前の手順で作成した Azure Storage のリンクされたサービスを参照します。 データセットは、データセットを使用するアクティビティで設定された値を持つパラメーターを受け取ります。 パラメーターは、データの存在/格納場所を指す "folderPath" を構築するために使用されます。

```powershell
$request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/datasets/BlobDataset?api-version=${apiVersion}"
$body = @"
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": {
                "value": "@{dataset().path}",
                "type": "Expression"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

出力例を次に示します。

```json
{
    "id":  "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/datasets/BlobDataset",
    "name":  "BlobDataset",
    "properties":  {
                       "type":  "AzureBlob",
                       "typeProperties":  {
                                              "folderPath":  "@{value=@{dataset().path}; type=Expression}"
                                          },
                       "linkedServiceName":  {
                                                 "referenceName":  "AzureStorageLinkedService",
                                                 "type":  "LinkedServiceReference"
                                             },
                       "parameters":  {
                                          "path":  "@{type=String}"
                                      }
                   },
    "etag":  "0000c752-0000-0000-0000-59b1459d0000"
}
```

## <a name="create-pipeline"></a>パイプラインの作成

この例では、このパイプラインには 1 つのアクティビティが含まれており、2 つのパラメーター (入力 BLOB パスと出力 BLOB パス) を受け取ります。 これらのパラメーターの値は、パイプラインがトリガー/実行されたときに設定されます。 コピー アクティビティは、入力と出力として、前の手順で作成された同じ BLOB データセットを参照します。 データセットが入力データセットとして使用される場合は、入力パスが指定されます。 また、データセットが出力データセットとして使用される場合は、出力パスが指定されます。 

```powershell
$request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelines/Adfv2QuickStartPipeline?api-version=${apiVersion}"
$body = @"
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "inputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.inputPath"
                        },
                    "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.outputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                }
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            }
        }
    }
}
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

出力例を次に示します。

```json
{
    "id":  "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/pipelines/Adfv2QuickStartPipeline",
    "name":  "Adfv2QuickStartPipeline",
    "properties":  {
                       "activities":  [
                                          "@{name=CopyFromBlobToBlob; type=Copy; inputs=System.Object[]; outputs=System.Object[]; typeProperties=}"
                                      ],
                       "parameters":  {
                                          "inputPath":  "@{type=String}",
                                          "outputPath":  "@{type=String}"
                                      }
                   },
    "etag":  "0000c852-0000-0000-0000-59b1459e0000"
}
```

## <a name="create-pipeline-run"></a>パイプラインの実行を作成する

この手順では、パイプラインで指定された **inputPath** と **outputPath** パラメーターの値を、ソースおよびシンクの BLOB パスの実際の値に設定し、パイプラインの実行をトリガーします。 応答本文で返されたパイプライン実行 ID は、後で API を監視する際に使用されます。

**inputPath** と **outputPath** の値を、ソースおよびシンクの BLOB パスに置き換え、ファイルを保存する前にデータをコピーします。


```powershell
$request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelines/Adfv2QuickStartPipeline/createRun?api-version=${apiVersion}"
$body = @"
{
    "inputPath": "<the path to existing blob(s) to copy data from, e.g. containername/path>",
    "outputPath": "<the blob path to copy data to, e.g. containername/path>"
}
"@
$response = Invoke-RestMethod -Method POST -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
$runId = $response.runId
```

出力例を次に示します。

```json
{
    "runId":  "2f26be35-c112-43fa-9eaa-8ba93ea57881"
}
```

## <a name="monitor-pipeline"></a>パイプラインを監視する

1. 次のスクリプトを実行し、データのコピーが完了するまで、パイプラインの実行の状態を継続的にチェックします。

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}?api-version=${apiVersion}"
    while ($True) {
        $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
        Write-Host  "Pipeline run status: " $response.Status -foregroundcolor "Yellow"

        if ($response.Status -eq "InProgress") {
            Start-Sleep -Seconds 15
        }
        else {
            $response | ConvertTo-Json
            break
        }
    }
    ```

    出力例を次に示します。

    ```json
    {
        "key":  "000000000-0000-0000-0000-00000000000",
        "timestamp":  "2017-09-07T13:12:39.5561795Z",
        "runId":  "000000000-0000-0000-0000-000000000000",
        "dataFactoryName":  "<dataFactoryName>",
        "pipelineName":  "Adfv2QuickStartPipeline",
        "parameters":  [
                        "inputPath: <inputBlobPath>",
                        "outputPath: <outputBlobPath>"
                    ],
        "parametersCount":  2,
        "parameterNames":  [
                            "inputPath",
                            "outputPath"
                        ],
        "parameterNamesCount":  2,
        "parameterValues":  [
                                "<inputBlobPath>",
                                "<outputBlobPath>"
                            ],
        "parameterValuesCount":  2,
        "runStart":  "2017-09-07T13:12:00.3710792Z",
        "runEnd":  "2017-09-07T13:12:39.5561795Z",
        "durationInMs":  39185,
        "status":  "Succeeded",
        "message":  ""
    }
    ```

2. 次のスクリプトを実行し、コピー アクティビティの実行の詳細 (たとえば、読み書きされたデータのサイズ) を取得します。

    ```PowerShell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}/activityruns?api-version=${apiVersion}&startTime="+(Get-Date).ToString('yyyy-MM-dd')+"&endTime="+(Get-Date).AddDays(1).ToString('yyyy-MM-dd')+"&pipelineName=Adfv2QuickStartPipeline"
    $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
    $response | ConvertTo-Json
    ```

    出力例を次に示します。

    ```json
    {
        "value":  [
                    {
                        "id":  "000000000-0000-0000-0000-00000000000",
                        "timestamp":  "2017-09-07T13:12:38.4780542Z",
                        "pipelineRunId":  "000000000-0000-00000-0000-0000000000000",
                        "pipelineName":  "Adfv2QuickStartPipeline",
                        "status":  "Succeeded",
                        "failureType":  "",
                        "linkedServiceName":  "",
                        "activityName":  "CopyFromBlobToBlob",
                        "activityType":  "Copy",
                        "activityStart":  "2017-09-07T13:12:02.3299261Z",
                        "activityEnd":  "2017-09-07T13:12:38.4780542Z",
                        "duration":  36148,
                        "input":  "@{source=; sink=}",
                        "output":  "@{dataRead=331452208; dataWritten=331452208; copyDuration=22; throughput=14712.9; errors=System.Object[]; effectiveIntegrationRuntime=DefaultIntegrationRuntime (West US); usedDataIntegrationUnits=2; billedDuration=22}",
                        "error":  "@{errorCode=; message=; failureType=; target=CopyFromBlobToBlob}"
                    }
                ]
    }
    ```

## <a name="verify-the-output"></a>出力を検証する

Azure Storage Explorer を使用して、パイプラインの実行を作成したときに指定したように BLOB が "inputBlobPath" から "outputBlobPath" にコピーされていることを確認します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ
このクイックスタートで作成したリソースは、2 とおりの方法でクリーンアップすることができます。 [Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)を削除した場合、そのリソース グループに含まれたすべてのリソースが対象となります。 他のリソースをそのまま維持する場合は、このチュートリアルで作成したデータ ファクトリだけを削除してください。

次のコマンドを実行して、リソース グループ全体を削除します。 
```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

次のコマンドを実行して、データ ファクトリだけを削除します。 

```powershell
Remove-AzureRmDataFactoryV2 -Name "<NameOfYourDataFactory>" -ResourceGroupName "<NameOfResourceGroup>"
```

## <a name="next-steps"></a>次の手順
このサンプルのパイプラインは、Azure BLOB ストレージ内のある場所から別の場所にデータをコピーするものです。 より多くのシナリオで Data Factory を使用する方法については、[チュートリアル](tutorial-copy-data-dot-net.md)を参照してください。 
