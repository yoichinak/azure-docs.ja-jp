---
title: Azure PowerShell を使用した VM へのポートの開放 | Microsoft Docs
description: Azure Resource Manager デプロイメント モデルと Azure PowerShell を使用して、Windows VM へのポートを開き、エンドポイントを作成する方法について説明します。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: cf45f7d8-451a-48ab-8419-730366d54f1e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: f79db8cdec0aa48ae300aff4c58072fb6afdc932
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37932784"
---
# <a name="how-to-open-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>PowerShell を使用して Azure の VM へのポートとエンドポイントを開放する方法
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>クイック コマンド
ネットワーク セキュリティ グループと ACL 規則を作成するには、 [最新バージョンの Azure PowerShell がインストールされている](/powershell/azureps-cmdlets-docs)必要があります。 [これらの手順は、Azure Portal を使用して実行](nsg-quickstart-portal.md)することもできます。

Azure アカウントにログインします。

```powershell
Connect-AzureRmAccount
```

以下の例では、パラメーター名を独自の値に置き換えてください。 たとえば、*myResourceGroup*、*myNetworkSecurityGroup*、*myVnet* といったパラメーター名にします。

[New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) を使用して規則を作成します。 次の例では、*myNetworkSecurityGroupRule* という名前の規則を作成して、ポート *80* での *TCP* トラフィックを許可します。

```powershell
$httprule = New-AzureRmNetworkSecurityRuleConfig `
    -Name "myNetworkSecurityGroupRule" `
    -Description "Allow HTTP" `
    -Access "Allow" `
    -Protocol "Tcp" `
    -Direction "Inbound" `
    -Priority "100" `
    -SourceAddressPrefix "Internet" `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 80
```

そして、次のように、[New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) を使用してネットワーク セキュリティ グループを作成し、先ほど作成した HTTP 規則を割り当てます。 次の例では、*myNetworkSecurityGroup* という名前のネットワーク セキュリティ グループを作成します。

```powershell
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName "myResourceGroup" `
    -Location "EastUS" `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $httprule
```

ここで、ネットワーク セキュリティ グループをサブネットに割り当ててみましょう。 次の例では、[Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork) を使用して、*myVnet* という名前の既存の仮想ネットワークを *$vnet* 変数に割り当てます。

```powershell
$vnet = Get-AzureRmVirtualNetwork `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

[Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) を使用して、ネットワーク セキュリティ グループをサブネットに関連付ます。 次の例では、*mySubnet* という名前のサブネットをネットワーク セキュリティ グループに関連付けます。

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzureRmVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

最後に、[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) を使用して仮想ディレクトリを更新し、変更を有効にします。

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>ネットワーク セキュリティ グループの詳細
このページのクイック コマンドでは、VM にフローするトラフィックの開始と実行を行うことができます。 ネットワーク セキュリティ グループには優れた機能が多数用意されており、リソースへのアクセスをきめ細かく制御できます。 詳細については、 [ネットワーク セキュリティ グループと ACL 規則の作成](tutorial-virtual-network.md#secure-network-traffic)に関するページをご覧ください。

高可用性 Web アプリケーション用に、Azure Load Balancer の背後に VM を配置する必要があります。 ロード バランサーは、トラフィックをフィルターできるネットワーク セキュリティ グループとともに、VM のトラフィックを分散します。 詳細については、[Azure の Linux 仮想マシンを負荷分散して高可用性アプリケーションを作成する方法](tutorial-load-balancer.md)に関するページをご覧ください。

## <a name="next-steps"></a>次の手順
この例では、HTTP トラフィックを許可する単純な規則を作成します。 より精密な環境の作成については、次の記事で確認できます。

* [Azure リソース マネージャーの概要](../../azure-resource-manager/resource-group-overview.md)
* [ネットワーク セキュリティ グループについて](../../virtual-network/security-overview.md)
* [ロード バランサー用の Azure Resource Manager の概要](../../load-balancer/load-balancer-arm.md)

