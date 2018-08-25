---
title: PowerShell での新しい Azure Machine Learning Web サービスの再トレーニング | Microsoft Docs
description: Azure Machine Learning で Machine Learning Management PowerShell コマンドレットを使用してプログラムによるモデルの再トレーニングをしてWeb サービスを更新し、新しくトレーニングを行ったモデルを使用する方法について説明します。
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 3953a398-6174-4d2d-8bbd-e55cf1639415
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.openlocfilehash: 069a3022cf9b6423b95e8f9f35686965d2654be7
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39631080"
---
# <a name="retrain-a-new-resource-manager-based-web-service-using-the-machine-learning-management-powershell-cmdlets"></a>Machine Learning Management PowerShell コマンドレットを使用した新しい Resource Manager ベースの Web サービスの再トレーニング
新しい Web サービスを再トレーニングする場合は、新しいトレーニング済みのモデルを参照するよう予測 Web サービス定義を更新します。

## <a name="prerequisites"></a>前提条件
「[プログラムによる Machine Learning のモデルの再トレーニング](retrain-models-programmatically.md)」に示されているようにトレーニング実験と予測実験が設定されているものとします。

> [!IMPORTANT]
> 予測実験は、Azure Resource Manager (新規) ベースの Machine Learning Web サービスとしてデプロイする必要があります。
> 新しい Web サービスをデプロイするには、Web サービスのデプロイ先となるサブスクリプションで十分なアクセス許可を持っている必要があります。 詳しくは、「[Azure Machine Learning Web サービス ポータルを使用して Web サービスを管理する](manage-new-webservice.md)」をご覧ください。

Web サービスのデプロイの詳細については、「[Azure Machine Learning Web サービスをデプロイする](publish-a-machine-learning-web-service.md)」を参照してください。

このプロセスでは、Azure Machine Learning コマンドレットがインストールされていることが必要です。 Machine Learning コマンドレットのインストール方法については、MSDN の [Azure Machine Learning コマンドレット](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/) リファレンスをご覧ください。

再トレーニングの出力から次の情報がコピーされている必要があります。

* BaseLocation
* RelativeLocation

これを実行する手順は次のとおりです。

1. Azure Resource Manager アカウントにサインインします。
2. Web サービス定義を取得します
3. Web サービス定義を JSON としてエクスポートします。
4. JSON で ilearner BLOB への参照を更新します。
5. JSON を Web サービス定義にインポートします。
6. Web サービスを新しい Web サービス定義で更新します

## <a name="sign-in-to-your-azure-resource-manager-account"></a>Azure Resource Manager アカウントへのサインイン
最初に [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) コマンドレットを使用して、PowerShell 環境から Azure アカウントにサインインする必要があります。

## <a name="get-the-web-service-definition"></a>Web サービス定義を取得します。
[Get-AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/get-azurermmlwebservice) コマンドレットを呼び出して Web サービスを取得します。 Web サービス定義は Web サービスのトレーニング済みのモデルの内部的な表現であり、直接変更できるものではありません。 トレーニング実験ではなく、予測実験の Web サービス定義を取得してください。

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

既存の Web サービスのリソース グループ名を決定するには、パラメーターを指定しないで Get-AzureRmMlWebService コマンドレットを実行しサブスクリプションの Web サービスを表示します。 Web サービスを見つけて、その Web サービス ID を確認します。 リソース グループの名前は ID の 4 番目の要素で、" *resourceGroups* " 要素の後にあります。 次の例では、リソース グループ名は Default-MachineLearning-SouthCentralUS です。

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

または、既存の Web サービスのリソース グループ名を判断するには、Microsoft Azure Machine Learning Web サービス ポータルにログインします。 Web サービスを選択します。 リソース グループ名は Web サービスの URL の 5 番目の要素で、" *resourceGroups* " 要素の直後にあります。 次の例では、リソース グループ名は Default-MachineLearning-SouthCentralUS です。

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-as-json"></a>Web サービス定義を JSON としてエクスポートします。
新しいトレーニング済みモデルを使用するようにトレーニング済みモデルの定義を変更するには、最初にこれを [Export-AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/export-azurermmlwebservice) コマンドレットを使用して、JSON フォーマット ファイルにインポートする必要があります。

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob-in-the-json"></a>JSON で ilearner BLOB への参照を更新します。
資産で、[<トレーニング済みモデル>] を見つけ、ilearner BLOB の URI で *locationInfo* の *uri* の値を更新します。 URI は BES 再トレーニング呼び出しの出力からの *BaseLocation* と *RelativeLocation* を組み合わせて作成します。 これにより、新しいトレーニング済みモデルを参照するパスが更新されます。

     "asset3": {
        "name": "Retrain Samp.le [trained model]",
        "type": "Resource",
        "locationInfo": {
          "uri": "https://mltestaccount.blob.core.windows.net/azuremlassetscontainer/baca7bca650f46218633552c0bcbba0e.ilearner"
        },
        "outputPorts": {
          "Results dataset": {
            "type": "Dataset"
          }
        }
      },

## <a name="import-the-json-into-a-web-service-definition"></a>JSON を Web サービス定義にインポートします。
[Import-AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/import-azurermmlwebservice) コマンドレットを使用して、変更された JSON ファイルを Web サービス定義を更新するために使用できる Web サービス定義に変換します。

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service-with-new-web-service-definition"></a>Web サービスを新しい Web サービス定義で更新します
最後に、[Update-AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/update-azurermmlwebservice) コマンドレットを使用して、Web サービス定義を更新します。

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'  -ServiceUpdates $wsd

## <a name="summary"></a>まとめ
Machine Learning PowerShell 管理コマンドレットを使用して、予測 Web サービスのトレーニング済みモデルを更新すると、次のようなシナリオを有効にできます。

* 新しいデータでの定期的なモデルの再トレーニング。
* 自身のデータでモデルを再トレーニングすることを目標としている顧客へのモデルの配布。

