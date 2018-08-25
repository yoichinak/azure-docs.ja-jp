---
title: Powershell スクリプトによる Azure Search の管理 | Microsoft Azure | Microsoft Docs
description: PowerShell スクリプトを使用して Azure Search サービスを管理します。 Azure Search サービスの作成または更新と、Azure Search 管理者キーの管理
author: HeidiSteen
manager: cgronlun
tags: azure-resource-manager
services: search
ms.service: search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 08/15/2016
ms.author: heidist
ms.openlocfilehash: 1d0024af3bbf9edfe8c43032878a8b61e35cea9c
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39000992"
---
# <a name="manage-your-azure-search-service-with-powershell"></a>PowerShell を使用して Azure Search サービスを管理する
> [!div class="op_single_selector"]
> * [ポータル](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> 
> 

このトピックでは、Azure Search サービスの管理タスクの多くを実行する PowerShell コマンドについて説明します。 ここでは、検索サービスの作成とスケーリング、および検索サービスの API キーの管理について説明します。
以下のコマンドにより、 [Azure Search 管理 REST API](https://docs.microsoft.com/rest/api/searchmanagement)で使用できる管理オプションを並列実行します。

## <a name="prerequisites"></a>前提条件
* Azure PowerShell 1.0 以降をインストールする必要があります。 手順については、 [Azure PowerShell のインストールおよび構成に関するページ](/powershell/azure/overview)を参照してください。
* 以下に示すように、PowerShell で Azure サブスクリプションにログインする必要があります。

まず、次のコマンドで Azure にログインする必要があります。

    Connect-AzureRmAccount

Microsoft Azure のログイン ダイアログで、Azure アカウントの電子メール アドレスとパスワードを指定します。

または、 [サービス プリンシパルを使用して非対話的にログイン](../azure-resource-manager/resource-group-authenticate-service-principal.md)できます。

Azure サブスクリプションが複数ある場合は、使用する Azure サブスクリプションを設定する必要があります。 現在のサブスクリプションを一覧表示するには、次のコマンドを実行します。

    Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

サブスクリプションを指定するには、次のコマンドを実行します。 次の例では、サブスクリプション名は `ContosoSubscription`です。

    Select-AzureRmSubscription -SubscriptionName ContosoSubscription

## <a name="commands-to-help-you-get-started"></a>基本的なコマンド
    $serviceName = "your-service-name-lowercase-with-dashes"
    $sku = "free" # or "basic" or "standard" for paid services
    $location = "West US"
    # You can get a list of potential locations with
    # (Get-AzureRmResourceProvider -ListAvailable | Where-Object {$_.ProviderNamespace -eq 'Microsoft.Search'}).Locations
    $resourceGroupName = "YourResourceGroup" 
    # If you don't already have this resource group, you can create it with 
    # New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Register the ARM provider idempotently. This must be done once per subscription
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Search"

    # Create a new search service
    # This command will return once the service is fully created
    New-AzureRmResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateUri "https://gallery.azure.com/artifact/20151001/Microsoft.Search.1.0.9/DeploymentTemplates/searchServiceDefaultTemplate.json" `
        -NameFromTemplate $serviceName `
        -Sku $sku `
        -Location $location `
        -PartitionCount 1 `
        -ReplicaCount 1

    # Get information about your new service and store it in $resource
    $resource = Get-AzureRmResource `
        -ResourceType "Microsoft.Search/searchServices" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19

    # View your resource
    $resource

    # Get the primary admin API key
    $primaryKey = (Invoke-AzureRmResourceAction `
        -Action listAdminKeys `
        -ResourceId $resource.ResourceId `
        -ApiVersion 2015-08-19).PrimaryKey

    # Regenerate the secondary admin API Key
    $secondaryKey = (Invoke-AzureRmResourceAction `
        -ResourceType "Microsoft.Search/searchServices/regenerateAdminKey" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19 `
        -Action secondary).SecondaryKey

    # Create a query key for read only access to your indexes
    $queryKeyDescription = "query-key-created-from-powershell"
    $queryKey = (Invoke-AzureRmResourceAction `
        -ResourceType "Microsoft.Search/searchServices/createQueryKey" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19 `
        -Action $queryKeyDescription).Key

    # View your query key
    $queryKey

    # Delete query key
    Remove-AzureRmResource `
        -ResourceType "Microsoft.Search/searchServices/deleteQueryKey/$($queryKey)" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19

    # Scale your service up
    # Note that this will only work if you made a non "free" service
    # This command will not return until the operation is finished
    # It can take 15 minutes or more to provision the additional resources
    $resource.Properties.ReplicaCount = 2
    $resource | Set-AzureRmResource

    # Delete your service
    # Deleting your service will delete all indexes and data in the service
    $resource | Remove-AzureRmResource

## <a name="next-steps"></a>次の手順
以上でサービスの作成は終了です。引き続き、[インデックスの作成](search-what-is-an-index.md)、[インデックスのクエリ](search-query-overview.md)、最後に Azure Search を使用した独自の検索アプリケーションの作成と管理に進むことができます。

* [Azure ポータルでの Azure Search インデックスの作成](search-create-index-portal.md)
* [Azure Portal の Search エクスプローラーを使用して Azure Search インデックスを照会する](search-explorer.md)
* [インデクサーをセットアップして他のサービスからデータを読み込む](search-indexer-overview.md)
* [.NET で Azure Search を使用する方法](search-howto-dotnet-sdk.md)
* [Azure Search トラフィックを分析する](search-traffic-analytics.md)

