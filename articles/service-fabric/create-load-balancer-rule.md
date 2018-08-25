---
title: クラスターの Azure Load Balancer 規則の作成
description: Azure Service Fabric クラスターのポートを開くように Azure Load Balancer を構成します。
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: ryanwi
ms.openlocfilehash: e4c3bf627c4a5e01c4d9001fcbb0feed0b92209f
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008044"
---
# <a name="open-ports-for-a-service-fabric-cluster"></a>Service Fabric クラスターのポートを開く

Azure Service Fabric クラスターにデプロイされたロード バランサーは、ノード上で動作するアプリにトラフィックを送信します。 別のポートを使用するようにアプリを変更する場合は、Azure Load Balancer でそのポートを公開 (または別のポートをルーティング) する必要があります。

Service Fabric クラスターを Azure にデプロイすると、ロード バランサーが自動的に作成されます。 ロード バランサーがない場合は、[インターネットに接続するロード バランサーの構成](..\load-balancer\load-balancer-get-started-internet-portal.md)に関するページを参照してください。

## <a name="configure-service-fabric"></a>Service Fabric の構成

Service Fabric アプリケーションの **ServiceManifest.xml** 構成ファイルは、アプリケーションによる使用が想定されるエンドポイントを定義します。 エンドポイントを定義する構成ファイルが更新されたら、そのポート (または別のポート) を公開するようにロード バランサーを更新する必要があります。 Service Fabric のエンドポイントを作成する方法の詳細については、[エンドポイントの設定](service-fabric-service-manifest-resources.md)に関するページを参照してください。

## <a name="create-a-load-balancer-rule"></a>ロード バランサー規則の作成

ロード バランサー規則は、インターネットに接続するポートを開き、アプリケーションが使用する内部ノードのポートにトラフィックを転送します。 ロード バランサーがない場合は、[インターネットに接続するロード バランサーの構成](..\load-balancer\load-balancer-get-started-internet-portal.md)に関するページを参照してください。

ロード バランサー規則を作成するには、次の情報を収集する必要があります。

- ロード バランサー名。
- ロード バランサーと Service Fabric クラスターのリソース グループ。
- 外部ポート。
- 内部ポート。

## <a name="azure-cli"></a>Azure CLI
**Azure CLI** を使用してロード バランサー規則を作成するためのコマンドは 1 つのみです。 新しい規則を作成するには、ロード バランサー名とリソース グループ名の両方を把握している必要があります。

>[!NOTE]
>ロード バランサーの名前を確認する必要がある場合は、このコマンドを使用すると、すべてのロード バランサーと関連するリソース グループの一覧を簡単に取得できます。
>
>`az network lb list --query "[].{ResourceGroup: resourceGroup, Name: name}"`
>


```azurecli
az network lb rule create --backend-port 40000 --frontend-port 39999 --protocol Tcp --lb-name LB-svcfab3 -g svcfab_cli -n my-app-rule
```

Azure CLI コマンドには、いくつかのパラメーターがあります。それらのパラメーターを次の表に示します。

| パラメーター | 説明 |
| --------- | ----------- |
| `--backend-port`  | Service Fabric アプリケーションがリッスンしているポート。 |
| `--frontend-port` | ロード バランサーが外部接続用に公開するポート。 |
| `-lb-name` | 変更するロード バランサーの名前。 |
| `-g`       | ロード バランサーと Service Fabric クラスターの両方を備えたリソース グループ。 |
| `-n`       | ルールの希望の名前。 |


>[!NOTE]
>Azure CLI を使用してロード バランサーを作成する方法の詳細については、[Azure CLI を使用したロード バランサーの作成](..\load-balancer\load-balancer-get-started-internet-arm-cli.md)に関するページを参照してください。

## <a name="powershell"></a>PowerShell

PowerShell は、Azure CLI よりも少し複雑です。 次の概念的な手順に従って、ルールを作成します。

1. Azure からロード バランサーを取得します。
2. 規則を作成します。
3. 規則をロード バランサーに追加します。
4. ロード バランサーを更新します。

>[!NOTE]
>ロード バランサーの名前を確認する必要がある場合は、このコマンドを使用すると、すべてのロード バランサーと関連するリソース グループの一覧を簡単に取得できます。
>
>`Get-AzureRmLoadBalancer | Select Name, ResourceGroupName`

```powershell
# Get the load balancer
$lb = Get-AzureRmLoadBalancer -Name LB-svcfab3 -ResourceGroupName svcfab_cli

# Create the rule based on information from the load balancer.
$lbrule = New-AzureRmLoadBalancerRuleConfig -Name my-app-rule7 -Protocol Tcp -FrontendPort 39990 -BackendPort 40009 `
                                            -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
                                            -BackendAddressPool  $lb.BackendAddressPools[0] `
                                            -Probe $lb.Probes[0]

# Add the rule to the load balancer
$lb.LoadBalancingRules.Add($lbrule)

# Update the load balancer on Azure
$lb | Set-AzureRmLoadBalancer
```

`New-AzureRmLoadBalancerRuleConfig` コマンドでは、`-FrontendPort` はロード バランサーが外部接続用に公開するポートを表し、`-BackendPort` は Service Fabric アプリがリッスンするポートを表します。

>[!NOTE]
>PowerShell を使用してロード バランサーを作成する方法の詳細については、[PowerShell を使用したロード バランサーの作成](..\load-balancer\load-balancer-get-started-internet-arm-ps.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

[Service Fabric でのネットワーク](service-fabric-patterns-networking.md)の詳細について確認する。