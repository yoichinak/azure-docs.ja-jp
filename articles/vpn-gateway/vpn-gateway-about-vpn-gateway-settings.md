---
title: クロスプレミス Azure 接続用の VPN Gateway の設定 | Microsoft Docs
description: Azure 仮想ネットワーク ゲートウェイ用の VPN Gateway の設定について説明します。
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ae665bc5-0089-45d0-a0d5-bc0ab4e79899
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2018
ms.author: cherylmc
ms.openlocfilehash: 60cdc7bbe08df7816560e9720f96edc51769c342
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38618223"
---
# <a name="about-vpn-gateway-configuration-settings"></a>VPN ゲートウェイの構成設定について

VPN ゲートウェイは、仮想ネットワークとオンプレミスの場所の間でパブリック接続を使って暗号化されたトラフィックを送信する仮想ネットワーク ゲートウェイの一種です。 VPN ゲートウェイを使用して、Azure バックボーン経由で仮想ネットワーク間にトラフィックを送信できます。

VPN Gateway の接続は複数のリソースの構成に依存し、それぞれに構成可能な設定が含まれます。 このセクションでは、Resource Manager デプロイ モデルに作成される仮想ネットワークの VPN ゲートウェイに関するリソースと設定について説明します。 各接続ソリューションの説明とトポロジ ダイアグラムについては、「[VPN Gateway について](vpn-gateway-about-vpngateways.md)」をご覧ください。

>[!NOTE]
> この記事の値は、-GatewayType 'Vpn' を使用する仮想ネットワーク ゲートウェイに適用されます。 これが、これらの特定の仮想ネットワーク ゲートウェイが VPN ゲートウェイと呼ばれる理由です。 ExpressRoute ゲートウェイの値は、VPN ゲートウェイに使用するものと同じ値ではありません。
>
>-GatewayType 'ExpressRoute' に適用される値については、「[ExpressRoute 用の仮想ネットワーク ゲートウェイについて](../expressroute/expressroute-about-virtual-network-gateways.md)」をご覧ください。
>
>

## <a name="gwtype"></a>ゲートウェイの種類

各仮想ネットワークに配置できる各種類の仮想ネットワーク ゲートウェイは 1 つに限られています。 仮想ネットワーク ゲートウェイを作成するときは、ゲートウェイの種類が構成に対して適切であることを確認する必要があります。

-GatewayType に使用できる値は次のとおりです。

* Vpn
* ExpressRoute

VPN Gateway では、 `-GatewayType` *Vpn*にする必要があります。

例:

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased
```

## <a name="gwsku"></a>ゲートウェイの SKU

[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

### <a name="configure-a-gateway-sku"></a>ゲートウェイの SKU を構成する

#### <a name="azure-portal"></a>Azure ポータル

Azure Portal を使用して Resource Manager の仮想ネットワーク ゲートウェイを作成する場合、ドロップダウンを使用してワーク ゲートウェイ SKU を選択できます。 表示されるオプションは、選択したゲートウェイの種類と VPN の種類に対応します。

#### <a name="powershell"></a>PowerShell

次の PowerShell の例では、`-GatewaySku` が VpnGw1 として指定されています。 PowerShell を使用してゲートウェイを作成する場合は、まず、IP 構成を作成してから、変数を使用して参照する必要があります。 この例では、構成変数は $gwipconfig となります。

```powershell
New-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'US East' -IpConfigurations $gwipconfig -GatewaySku VpnGw1 `
-GatewayType Vpn -VpnType RouteBased
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network vnet-gateway create --name VNet1GW --public-ip-address VNet1GWPIP --resource-group TestRG1 --vnet VNet1 --gateway-type Vpn --vpn-type RouteBased --sku VpnGw1 --no-wait
```

###  <a name="resizechange"></a>SKU のサイズ変更または変更

VPN ゲートウェイがあり、別のゲートウェイ SKU を使用する場合、使用可能なオプションは、ゲートウェイ SKU のサイズを変更するか、別の SKU に変更することです。 別のゲートウェイ SKU に変更する場合、既存のゲートウェイを完全に削除して、新しいゲートウェイを作成します。 これには作成するまで最大 45 分かかることがあります。 これと比較して、ゲートウェイ SKU のサイズを変更する場合は、ゲートウェイを削除して再構築する必要がないため、ダウンタイムはごくわずかです。 ゲートウェイ SKU を変更する代わりにサイズを変更するオプションが利用できる場合は、そのオプションを利用するようにしてください。 ただし、サイズ変更に関する以下の規則があります。

1. VpnGw1、VpnGw2、VpnGw3 SKU の間でサイズ変更できます。
2. 古いゲートウェイ SKU では、Basic、Standard、HighPerformance SKU の間でサイズ変更できます。
3. Basic/Standard/HighPerformance SKU から新しい VpnGw1/VpnGw2/VpnGw3 SKU にサイズ変更することは**できません**。 代わりに新しい SKU に[変更](#change)する必要があります。

#### <a name="resizegwsku"></a>ゲートウェイのサイズを変更する

[!INCLUDE [Resize a SKU](../../includes/vpn-gateway-gwsku-resize-include.md)]

####  <a name="change"></a>古い (レガシ) SKU から新しい SKU に変更する

[!INCLUDE [Change a SKU](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="connectiontype"></a>接続の種類

Resource Manager デプロイ モデルの各構成では、仮想ネットワーク ゲートウェイの接続の種類を指定する必要があります。 `-ConnectionType` に使用できる Resource Manager PowerShell 値は次のとおりです。

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

次の PowerShell の例では、接続の種類 *IPsec*を必要とする S2S 接続を作成しています。

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

## <a name="vpntype"></a>VPN の種類

VPN Gateway 構成に対して仮想ネットワーク ゲートウェイを作成する場合は、VPN の種類を指定する必要があります。 選択する VPN の種類は、作成する接続トポロジによって異なります。 たとえば、P2S 接続の場合、VPN の種類は RouteBased である必要があります。 VPN の種類は、使用しているハードウェアによっても異なる場合があります。 S2S 構成では、VPN デバイスが必要です。 一部の VPN デバイスでは、特定の VPN の種類のみがサポートされます。

選択した VPN の種類は、作成するソリューションのすべての接続要件を満たす必要があります。 たとえば、同じ仮想ネットワークに対して、S2S VPN ゲートウェイ接続と P2S VPN ゲートウェイ接続を作成する必要がある場合は、VPN の種類として *RouteBased* を使用します。P2S では、VPN の種類が RouteBased である必要があるためです。 VPN デバイスで RouteBased VPN 接続がサポートされていることも、確認する必要があります。 

一度作成された仮想ネットワーク ゲートウェイの VPN の種類を変更することはできません。 仮想ネットワーク ゲートウェイを削除し、新しいものを作成する必要があります。 次の 2 つの種類の VPN があります。

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

次の PowerShell の例では、 `-VpnType` を *RouteBased*に指定しています。 ゲートウェイを作成するときは、-VpnType が構成に対して適切であることを確認する必要があります。

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig `
-GatewayType Vpn -VpnType RouteBased
```

## <a name="requirements"></a>ゲートウェイの要件

[!INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## <a name="gwsub"></a>ゲートウェイ サブネット

VPN ゲートウェイを作成する前に、ゲートウェイ サブネットを作成する必要があります。 ゲートウェイ サブネットには、仮想ネットワーク ゲートウェイの VM とサービスが使用する IP アドレスが含まれます。 仮想ネットワーク ゲートウェイを作成すると、ゲートウェイ VM はゲートウェイ サブネットにデプロイされ、必要な VPN ゲートウェイ設定で構成されます。 ゲートウェイ サブネットには、追加の VM などをデプロイしないでください。 ゲートウェイ サブネットを正常に動作させるには、"GatewaySubnet" という名前を付ける必要があります。 ゲートウェイ サブネットに "GatewaySubnet" という名前を付けることで、これが仮想ネットワーク ゲートウェイの VM とサービスをデプロイするサブネットであることを Azure が認識できます。

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

ゲートウェイ サブネットを作成するときに、サブネットに含まれる IP アドレスの数を指定します。 ゲートウェイ サブネット内の IP アドレスは、ゲートウェイ VM とゲートウェイ サービスに割り当てられます。 一部の構成では、他の構成よりも多くの IP アドレスを割り当てる必要があります。 作成する構成の指示を調べて、作成するゲートウェイ サブネットがその要件を満たしていることを確認してください。 また、ゲートウェイ サブネットには、将来の構成の追加に対応できる十分な数の IP アドレスが含まれるようにしてください。 /29 のような小さいゲートウェイ サブネットを作成できますが、/28 以上 (/28、/27、/26 など) のゲートウェイ サブネットを作成することをお勧めします。 そうすれば、将来的に機能を追加する場合に、ゲートウェイを破棄し、ゲートウェイ サブネットを削除してから再作成して、より多くの IP アドレスを割り当てられるようにする必要がなくなります。

次の Resource Manager PowerShell の例では、GatewaySubnet という名前のゲートウェイ サブネットを示しています。 CIDR 表記で /27 を指定しています。これで既存のほとんどの構成で IP アドレスに十分対応できます。

```powershell
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="lng"></a>ローカル ネットワーク ゲートウェイ

VPN Gateway 構成を作成する場合、ほとんどのローカル ネットワーク ゲートウェイはオンプレミスの場所になります。 クラシック デプロイ モデルでは、ローカル ネットワーク ゲートウェイはローカル サイトとして参照されます。 

ローカル ネットワーク ゲートウェイに名前とオンプレミス VPN デバイスのパブリック IP アドレスを指定し、オンプレミスの場所に存在するアドレスのプレフィックスを指定します。 Azure は、ネットワーク トラフィックの宛先アドレスのプレフィックスを参照して、ローカル ネットワーク ゲートウェイに指定された構成を確認し、それに応じてパケットをルーティングします。 また、VPN ゲートウェイ接続を使用している VNet 間の構成に対して、ローカル ネットワーク ゲートウェイを指定します。

次の PowerShell の例では、新しいローカル ネットワーク ゲートウェイを作成します。

```powershell
New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

場合によっては、ローカル ネットワーク ゲートウェイ設定を変更する必要があります。 たとえば、アドレス範囲を追加または変更する場合や、VPN デバイスの IP アドレスが変更された場合などです。 「[PowerShell を使用したローカル ネットワーク ゲートウェイの設定の変更](vpn-gateway-modify-local-network-gateway.md)」を参照してください。

## <a name="resources"></a>REST API、PowerShell コマンドレット、および CLI

VPN Gateway 構成に対して REST API、PowerShell コマンドレット、または Azure CLI を使用する場合のテクニカル リソースおよび具体的な構文の要件については、次のページを参照してください。

| **クラシック** | **Resource Manager** |
| --- | --- |
| [PowerShell](/powershell/module/azure#networking) |[PowerShell](/powershell/module/azurerm.network#vpn) |
| [REST API](https://msdn.microsoft.com/library/jj154113) |[REST API](/rest/api/network/virtualnetworkgateways) |
| サポートされていません | [Azure CLI](/cli/azure/network/vnet-gateway)|

## <a name="next-steps"></a>次の手順

使用可能な接続構成の詳細については、「[VPN Gateway の構成設定について](vpn-gateway-about-vpngateways.md)」を参照してください。
