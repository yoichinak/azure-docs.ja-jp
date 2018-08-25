---
title: アプリごとのスケーリングを使って Azure App Service で高密度ホスティングを実現する | Microsoft Docs
description: Azure App Service での高密度ホスティング
author: btardif
manager: erikre
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/22/2018
ms.author: byvinyal
ms.openlocfilehash: 97e1efe34417c3bf2f23801b2112b718f55d3416
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2018
ms.locfileid: "36962408"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>アプリごとのスケーリングを使って Azure App Service で高密度ホスティングを実現する
App Service のアプリをスケーリングするときは、そのアプリを実行する [App Service プラン](azure-web-sites-web-hosting-plans-in-depth-overview.md)をスケーリングするのが既定の方法です。 同じ App Service プランでアプリを複数実行している場合には、スケールアウトしたインスタンスのそれぞれがプラン内のアプリをすべて実行することになります。

App Service プラン レベルでは、"*アプリごとのスケーリング*" を有効にすることができます。 アプリごとのスケーリングを使うと、アプリがそのホストとなる App Service プランとは無関係にスケーリングされます。 これにより、App Service プランを 10 個のインスタンスにスケーリングしながら、5 個のインスタンスだけを使用するようにアプリを設定することが可能になります。

> [!NOTE]
> アプリごとのスケーリングは、**Standard**、**Premium**、**Premium V2**、および **Isolated** の価格レベルに限り利用できます。
>

## <a name="per-app-scaling-using-powershell"></a>PowerShell を使用したアプリごとのスケーリング

プランの作成時にアプリごとのスケーリングを有効にする場合には、```New-AzureRmAppServicePlan``` コマンドレットに ```-perSiteScaling $true``` 属性を渡します。

```powershell
New-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

既存の App Service プランを更新してアプリごとのスケーリングを利用できるようにするには、次のようにします。 

- ターゲット プランを取得します。```Get-AzureRmAppServicePlan```
- プロパティをローカルに変更します。```$newASP.PerSiteScaling = $true```
- 変更を Azure に反映させます。```Set-AzureRmAppServicePlan``` 

```powershell
# Get the new App Service Plan and modify the "PerSiteScaling" property.
$newASP = Get-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan
$newASP

#Modify the local copy to use "PerSiteScaling" property.
$newASP.PerSiteScaling = $true
$newASP
    
#Post updated app service plan back to azure
Set-AzureRmAppServicePlan $newASP
```

アプリ レベルでは、App Service プランでアプリが使用できるインスタンスの数を構成します。

次の例では、App Service プランでスケール アウトされるインスタンスの数に関係なく、アプリが使用できるインスタンスは 2 個までに制限されます。

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzureRmWebApp -ResourceGroupName $ResourceGroup -Name $webapp
    
# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2
    
# Post updated app back to azure
Set-AzureRmWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers` は `$newapp.MaxNumberOfWorkers` とは異なるものです。 アプリごとのスケーリングでは、アプリのスケーリング特性の特定に `$newapp.SiteConfig.NumberOfWorkers` が使用されます。

## <a name="per-app-scaling-using-azure-resource-manager"></a>Azure Resource Manager を使用したアプリごとのスケーリング

次の Azure Resource Manager テンプレートは、以下を作成します。

- 10 個のインスタンスにスケール アウトされる App Service プラン
- 最大 5 個のインスタンスまでスケーリングされるように構成されたアプリ

App Service プランは、**PerSiteScaling** プロパティを true に設定します (`"perSiteScaling": true`)。 アプリは、使用する**ワーカーの数**を 5 に設定します (`"properties": { "numberOfWorkers": "5" }`)。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "appServicePlanName": { "type": "string" },
        "appName": { "type": "string" }
        },
    "resources": [
    {
        "comments": "App Service Plan with per site perSiteScaling = true",
        "type": "Microsoft.Web/serverFarms",
        "sku": {
            "name": "P1",
            "tier": "Premium",
            "size": "P1",
            "family": "P",
            "capacity": 10
            },
        "name": "[parameters('appServicePlanName')]",
        "apiVersion": "2015-08-01",
        "location": "West US",
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "perSiteScaling": true
        }
    },
    {
        "type": "Microsoft.Web/sites",
        "name": "[parameters('appName')]",
        "apiVersion": "2015-08-01-preview",
        "location": "West US",
        "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
        "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
        "resources": [ {
                "comments": "",
                "type": "config",
                "name": "web",
                "apiVersion": "2015-08-01",
                "location": "West US",
                "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                "properties": { "numberOfWorkers": "5" }
            } ]
        }]
}
```

## <a name="recommended-configuration-for-high-density-hosting"></a>高密度ホスティングの推奨される構成
アプリごとのスケーリングは、世界各地の Azure リージョンと [App Service Environment](environment/app-service-app-service-environment-intro.md) のどちらでも有効にできる機能です。 ただし推奨されるのは App Service Environment です。App Service Environment を使用した方が、その高度な機能と大きなプール容量を利用できます。  

アプリに対して高密度ホスティングを構成するには、次の手順に従います。

1. App Service Environment を構成し、高密度ホスティング シナリオ専用のワーカー プールを選択します。
1. 1 つの App Service プランを作成し、ワーカー プールの全使用可能容量を使用するようにスケーリングします。
1. App Service プランの `PerSiteScaling` フラグを true に設定します。
1. 新しいアプリが作成され、その App Service プランに **1** に設定された **numberOfWorkers** プロパティが割り当てられます。 この構成を使用すると、このワーカー プールで可能な最高の密度になります。
1. ワーカーの数はアプリごとに個別に構成でき、必要に応じて追加リソースを許可できます。 例: 
    - 使用率が高いアプリでは、**numberOfWorkers** を **3** に設定してそのアプリの処理能力を上げます。 
    - 使用率の低いアプリでは、**numberOfWorkers** を **1** に設定します。

## <a name="next-steps"></a>次の手順

- [Azure App Service プランの詳細な概要](azure-web-sites-web-hosting-plans-in-depth-overview.md)
- [App Service 環境の概要](environment/app-service-app-service-environment-intro.md)
