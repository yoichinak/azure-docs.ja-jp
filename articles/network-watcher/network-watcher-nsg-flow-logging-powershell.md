---
title: Azure Network Watcher を使用したネットワーク セキュリティ グループのフローのログの管理 - PowerShell | Microsoft Docs
description: このページでは、PowerShell を使用して Azure Network Watcher でネットワーク セキュリティ グループのフローのログを管理する方法を説明します。
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 2dfc3112-8294-4357-b2f8-f81840da67d3
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 0b0516eb079dc45c46ce56bd9d2522fc66d45548
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2018
ms.locfileid: "39089861"
---
# <a name="configuring-network-security-group-flow-logs-with-powershell"></a>PowerShell を使用したネットワーク セキュリティ グループのフローのログの構成

> [!div class="op_single_selector"]
> - [Azure ポータル](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

ネットワーク セキュリティ グループのフローのログは、ネットワーク セキュリティ グループを使用したイングレスおよびエグレス IP トラフィックに関する情報を表示できる Network Watcher の機能です。 これらのフローのログは json 形式で記述され、ルールごとの送信フローと受信フロー、フローが適用される NIC、フローに関する 5 組の情報 (送信元/宛先 IP、送信元/宛先ポート、プロトコル)、トラフィックが許可されているか拒否されているかが示されます。

## <a name="register-insights-provider"></a>Insights プロバイダーの登録

フローのログ記録を成功させるには、**Microsoft.Insights** プロバイダーを登録する必要があります。 **Microsoft.Insights** プロバイダーが登録されているかどうか不明な場合は、次のスクリプトを実行します。

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs"></a>ネットワーク セキュリティ グループのフローのログを有効にする

フローのログを有効にするコマンドを次の例に示します。

```powershell
$NW = Get-AzurermNetworkWatcher -ResourceGroupName NetworkWatcherRg -Name NetworkWatcher_westcentralus
$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName nsgRG -Name nsgName
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName StorageRG -Name contosostorage123
Get-AzureRmNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id
Set-AzureRmNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true
```

指定するストレージ アカウントに対しては、Microsoft サービスまたは特定の仮想ネットワークのみにネットワーク アクセスを制限するネットワーク規則が構成されていてはなりません。 ストレージ アカウントは、フロー ログを有効化した NSG と同じ Azure サブスクリプションにも、異なる Azure サブスクリプションにも設定できます。 異なるサブスクリプションを使用する場合は、それらが同じ Azure Active Directory テナントに関連付けられている必要があります。 各サブスクリプションで使用するアカウントは、[必要なアクセス許可](required-rbac-permissions.md)を持っている必要があります。

## <a name="disable-network-security-group-flow-logs"></a>ネットワーク セキュリティ グループのフローのログを無効にする

フローのログを無効にするには、次の例を使用します。

```powershell
Set-AzureRmNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $false
```

## <a name="download-a-flow-log"></a>フローのログをダウンロードする

フローのログの保存場所は作成時に定義されます。 ストレージ アカウントに保存されているこれらのフローのログにアクセスする際の便利なツールが Microsoft Azure Storage Explorer です。このツールは、http://storageexplorer.com/ からダウンロードできます。

ストレージ アカウントが指定されている場合、パケット キャプチャ ファイルは、次の場所にあるストレージ アカウントに保存されます。

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

ログの構造については、[ネットワーク セキュリティ グループのフローのログの概要](network-watcher-nsg-flow-logging-overview.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次の手順

[PowerBI を使用して、NSG フロー ログを視覚化する](network-watcher-visualize-nsg-flow-logs-power-bi.md)方法を確認する

[オープン ソース ツールを使用して、NSG フロー ログを視覚化する](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)方法を確認する
