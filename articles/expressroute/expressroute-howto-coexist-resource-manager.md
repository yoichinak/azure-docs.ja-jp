---
title: '共存する ExpressRoute とサイト間 VPN の接続の構成: Resource Manager: Azure | Microsoft Docs'
description: 接続この記事では、リソース マネージャーのデプロイ モデルにおいて、共存できる ExpressRoute 接続とサイト間 VPN 接続を構成する手順について説明します。
documentationcenter: na
services: expressroute
author: charwen
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: c7717b14-3da3-4a6d-b78e-a5020766bc2c
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/05/2018
ms.author: charwen,cherylmc,rambala
ms.openlocfilehash: 80d2f65f516d7f1190f276fa9f2c62206bd31e67
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2018
ms.locfileid: "39262874"
---
# <a name="configure-expressroute-and-site-to-site-coexisting-connections"></a>ExpressRoute 接続とサイト間接続の共存の構成
> [!div class="op_single_selector"]
> * [PowerShell - Resource Manager](expressroute-howto-coexist-resource-manager.md)
> * [PowerShell - クラシック](expressroute-howto-coexist-classic.md)
> 
> 

サイト間 VPN と ExpressRoute が共存する接続を構成することには、いくつかの利点があります。

* ExpressRoute のセキュリティで保護されたフェールオーバー パスとしてサイト間 VPN を構成することができます。 
* また、サイト間 VPN を使用して、ExpressRoute 経由で接続されていないサイトに接続することもできます。 

この記事では、両方のシナリオを構成する手順について説明します。 この記事は、Resource Manager デプロイ モデルに適用されます。また、ここでは PowerShell が使用されます。 

>[!NOTE]
>ExpressRoute 回線上でサイト間 VPN を作成する場合は、[こちらの記事](site-to-site-vpn-over-microsoft-peering.md)をご覧ください。
>

## <a name="limits-and-limitations"></a>制限と制限事項
* **トランジット ルーティングはサポートされていません。** サイト間 VPN 経由で接続されたローカル ネットワークと ExpressRoute 経由で接続されたローカル ネットワーク間で (Azure 経由で) ルーティングすることはできません。
* **Basic SKU ゲートウェイはサポートされていません。** [ExpressRoute ゲートウェイ](expressroute-about-virtual-network-gateways.md)と [VPN ゲートウェイ](../vpn-gateway/vpn-gateway-about-vpngateways.md)のどちらについても、Basic SKU 以外のゲートウェイを使用する必要があります。
* **サポートされているのはルート ベースの VPN ゲートウェイのみです。** [ルート ベースの VPN ゲートウェイを使用する必要があります](../vpn-gateway/vpn-gateway-about-vpngateways.md)。
* **VPN ゲートウェイでは静的ルートを構成する必要があります。** ローカル ネットワークが ExpressRoute とサイト間 VPN の両方に接続されている場合は、ローカル ネットワーク内で静的ルートを構成して、パブリック インターネットへのサイト間 VPN 接続をルーティングする必要があります。

## <a name="configuration-designs"></a>構成の設計
### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>ExpressRoute のフェールオーバー パスとしてサイト間 VPN を構成する
ExpressRoute のバックアップとしてサイト間 VPN 接続を構成することができます。 この接続は、Azure のプライベート ピアリング パスにリンクされている仮想ネットワークにのみ適用されます。 Azure Microsoft ピアリングを介してアクセスできるサービスの VPN ベースのフェールオーバー ソリューションはありません。 ExpressRoute 回線は常にプライマリ リンクです。 データは、ExpressRoute 回線で障害が発生した場合にのみ、サイト間 VPN パスを通過します。 非対称なルーティングを回避するには、ローカル ネットワーク構成でも、サイト間 VPN よりも ExpressRoute 回線を優先するようにします。 ExpressRoute を受け取るルートの優先度を高く設定すると、ExpressRoute パスを優先することができます。 

> [!NOTE]
> 両方のルートが同じである場合は、ExpressRoute 回線がサイト間 VPN よりも優先されますが、Azure では最長プレフィックスの一致を使用してパケットの宛先へのルートを選択します。
> 
> 

![共存](media/expressroute-howto-coexist-resource-manager/scenario1.jpg)

### <a name="configure-a-site-to-site-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>ExpressRoute 経由で接続されていないサイトに接続するようにサイト間 VPN を構成する
サイト間 VPN 経由で Azure に直接接続するサイトと、ExpressRoute 経由で接続するサイトがあるネットワークを構成することができます。 

![共存](media/expressroute-howto-coexist-resource-manager/scenario2.jpg)

> [!NOTE]
> 仮想ネットワークをトランジット ルーターとして構成することはできません。
> 
> 

## <a name="selecting-the-steps-to-use"></a>使用する手順の選択
2 とおりの手順があり、そのいずれかを選択できます。 接続先にする既存の仮想ネットワークがある場合と、新しい仮想ネットワークを作成する場合とでは、選択できる構成手順が異なります。

* VNet がないので作成する必要がある。
  
    仮想ネットワークがまだない場合、この手順で Resource Manager デプロイ モデルを使用して新しい仮想ネットワークを作成し、新しい ExpressRoute 接続とサイト間 VPN 接続を作成します。 仮想ネットワークを構成する際は、「[新しい仮想ネットワークおよび共存する接続を作成するには](#new)」の手順に従ってください。
* Resource Manager デプロイ モデル VNet が既にある。
  
    既存のサイト間 VPN 接続または ExpressRoute 接続を使用して、仮想ネットワークを既に配置している場合があります。 このシナリオでは、ゲートウェイのサブネット マスクが /28 以下 (/28、/29 など) の場合は、既存のゲートウェイを削除する必要があります。 「[既存の VNet で共存する接続を構成するには](#add)」では、ゲートウェイを削除し、新しい ExpressRoute 接続とサイト間 VPN 接続を作成する手順について説明しています。
  
    ゲートウェイを削除して再作成すると、クロスプレミス接続のダウンタイムが発生します。 ただし、移行するように構成されている場合でも、VM やサービスは、ゲートウェイの構成中にロード バランサーを経由して通信できます。

## <a name="new"></a>新しい仮想ネットワークおよび共存する接続を作成するには
この手順では、VNet を作成し、共存するサイト間接続と ExpressRoute 接続を作成します。

1. 最新バージョンの Azure PowerShell コマンドレットをインストールします。 コマンドレットのインストールについては、[Azure PowerShell のインストールと構成の方法](/powershell/azure/overview)に関するページを参照してください。 この構成に使用するコマンドレットは、使い慣れたコマンドレットとは少し異なる場合があります。 必ず、これらの手順で指定されているコマンドレットを使用してください。

2. アカウントにログインし、環境を設定します。

  ```powershell
  Connect-AzureRmAccount
  Select-AzureRmSubscription -SubscriptionName 'yoursubscription'
  $location = "Central US"
  $resgrp = New-AzureRmResourceGroup -Name "ErVpnCoex" -Location $location
  $VNetASN = 65515
  ```
3. ゲートウェイ サブネットを含む仮想ネットワークを作成します。 仮想ネットワークの作成の詳細については、「[Create a virtual network (仮想ネットワークの作成)](../virtual-network/manage-virtual-network.md#create-a-virtual-network)」を参照してください。 サブネットの作成の詳細については、[サブネットの作成](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet)に関するページを参照してください。
   
   > [!IMPORTANT]
   > ゲートウェイ サブネットは /27 またはこれより短いプレフィックス (/26 や /25 など) にする必要があります。
   > 
   > 
   
    新しい VNet を作成します。

  ```powershell
  $vnet = New-AzureRmVirtualNetwork -Name "CoexVnet" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AddressPrefix "10.200.0.0/16"
  ```
   
    サブネットを追加します。

  ```powershell
  Add-AzureRmVirtualNetworkSubnetConfig -Name "App" -VirtualNetwork $vnet -AddressPrefix "10.200.1.0/24"
  Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
  ```
   
    VNet 構成を保存します。

  ```powershell
  $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```
4. <a name="vpngw"></a>次に、サイト間 VPN ゲートウェイを作成します。 VPN ゲートウェイの構成の詳細については、[サイト間接続を使用した VNet の構成](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)に関するページを参照してください。 GatewaySku は *VpnGw1*、*VpnGw2*、*VpnGw3*、*Standard*、*HighPerformance* の各 VPN ゲートウェイでのみサポートされています。 ExpressRoute と VPN Gateway が共存する構成は、Basic SKU ではサポートされていません。 VpnType には、*RouteBased* を指定する必要があります。

  ```powershell
  $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  $gwIP = New-AzureRmPublicIpAddress -Name "VPNGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
  $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "VPNGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
  New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "VpnGw1"
  ```
   
    Azure VPN ゲートウェイは、BGP ルーティング プロトコルをサポートします。 次のコマンドに -Asn スイッチを追加することで、その仮想ネットワークの ASN (AS 番号) を指定できます。 パラメーターの指定がない場合、AS番号は既定で 65515 になります。

  ```powershell
  $azureVpn = New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "VpnGw1" -Asn $VNetASN
  ```
   
    Azure で VPN ゲートウェイに使用される BGP ピアリング IP と AS 番号は、$azureVpn.BgpSettings.BgpPeeringAddress と $azureVpn.BgpSettings.Asn で確認できます。 詳細については、Azure VPN ゲートウェイの [BGP の構成](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md)に関するページをご覧ください。
5. ローカル サイト VPN ゲートウェイのエンティティを作成します。 このコマンドは、オンプレミスの VPN ゲートウェイを構成しません。 代わりに、パブリック IP やオンプレミスのアドレス空間などのローカル ゲートウェイ設定を指定できるため、Azure VPN ゲートウェイがこれに接続できます。
   
    ローカル VPN デバイスでサポートされるのが静的ルーティングのみの場合、静的ルートは次のように構成できます。

  ```powershell
  $MyLocalNetworkAddress = @("10.100.0.0/16","10.101.0.0/16","10.102.0.0/16")
  $localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress *<Public IP>* -AddressPrefix $MyLocalNetworkAddress
  ```
   
    ローカル VPN デバイスで BGP がサポートされており、動的ルーティングを有効にする場合、ローカル VPN デバイスで使用される BGP ピアリング IP と AS 番号を把握しておく必要があります。

  ```powershell
  $localVPNPublicIP = "<Public IP>"
  $localBGPPeeringIP = "<Private IP for the BGP session>"
  $localBGPASN = "<ASN>"
  $localAddressPrefix = $localBGPPeeringIP + "/32"
  $localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress $localVPNPublicIP -AddressPrefix $localAddressPrefix -BgpPeeringAddress $localBGPPeeringIP -Asn $localBGPASN
  ```
6. ローカルの VPN デバイスを構成して、新しい Azure VPN ゲートウェイに接続します。 VPN デバイス構成の詳細については、「 [VPN デバイスの構成](../vpn-gateway/vpn-gateway-about-vpn-devices.md)」を参照してください。

7. Azure のサイト間 VPN ゲートウェイをローカル ゲートウェイにリンクします。

  ```powershell
  $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
  New-AzureRmVirtualNetworkGatewayConnection -Name "VPNConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $azureVpn -LocalNetworkGateway2 $localVpn -ConnectionType IPsec -SharedKey <yourkey>
  ```
 

8. 既存の ExpressRoute 回線に接続する場合は、手順 8 と 9 をスキップし、手順 10 にジャンプします。 ExpressRoute 回線を構成します。 ExpressRoute 回線の構成の詳細については、[ExpressRoute 回線の作成](expressroute-howto-circuit-arm.md)に関するページを参照してください。


9. ExpressRoute 回線上で Azure プライベート ピアリングを構成します。 ExpressRoute 回線上で Azure プライベート ピアリングを構成する方法の詳細については、[ピアリングの構成](expressroute-howto-routing-arm.md)に関するページを参照してください。

10. <a name="gw"></a>ExpressRoute ゲートウェイを作成します。 ExpressRoute ゲートウェイの構成の詳細については、「 [ExpressRoute gateway configuration (ExpressRoute ゲートウェイの構成)](expressroute-howto-add-gateway-resource-manager.md)」を参照してください。 GatewaySKU には、*Standard*、*HighPerformance*、または *UltraPerformance* を指定します。

  ```powershell
  $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  $gwIP = New-AzureRmPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
  $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
  $gw = New-AzureRmVirtualNetworkGateway -Name "ERGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard
  ```
11. ExpressRoute ゲートウェイを ExpressRoute 回線にリンクします。 この手順が完了すると、オンプレミスのネットワークと Azure 間の接続が ExpressRoute 経由で確立されます。 リンク操作の詳細については、「 [Link VNets to ExpressRoute (ExpressRoute への Vnet のリンク)](expressroute-howto-linkvnet-arm.md)」を参照してください。

  ```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
  New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute
  ```

## <a name="add"></a>既存の VNet で共存する接続を構成するには
既存の仮想ネットワークがある場合は、ゲートウェイ サブネットのサイズを確認します。 ゲートウェイ サブネットが /28 または /29 の場合、まず仮想ネットワーク ゲートウェイを削除してから、ゲートウェイ サブネットのサイズを増やす必要があります。 このセクションの手順では、その方法を説明します。

ゲートウェイ サブネットが /27 以上で、仮想ネットワークが ExpressRoute 経由で接続されている場合、以降の手順をスキップして、前のセクションの「[手順 4 - サイト間 VPN ゲートウェイの作成手順](#vpngw)」に進みます。 

> [!NOTE]
> この既存のゲートウェイを削除すると、この構成で作業している間、ローカル環境から仮想ネットワークに接続できなくなります。 
> 
> 

1. Azure PowerShell コマンドレットの最新版をインストールする必要があります。 コマンドレットのインストールの詳細については、[Azure PowerShell のインストールと構成の方法](/powershell/azure/overview)に関するページを参照してください。 この構成に使用するコマンドレットは、使い慣れたコマンドレットとは少し異なる場合があります。 必ず、これらの手順で指定されているコマンドレットを使用してください。 
2. 既存の ExpressRoute またはサイト間 VPN ゲートウェイを削除します。

  ```powershell 
  Remove-AzureRmVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>
  ```
3. ゲートウェイ サブネットを削除します。

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup> Remove-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet
  ```
4. /27 以上のゲートウェイ サブネットを追加します。
   
   > [!NOTE]
   > 仮想ネットワーク内の IP アドレスが不足していてゲートウェイ サブネットのサイズを増やせない場合は、IP アドレス空間を追加する必要があります。
   > 
   > 

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
  Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
  ```
   
    VNet 構成を保存します。

  ```powershell
  $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```
5. この時点では、VNet にゲートウェイがありません。 新しいゲートウェイを作成し、接続を完了するには、前述の「[手順 4 - サイト間 VPN ゲートウェイを作成する](#vpngw)」に進みます。

## <a name="to-add-point-to-site-configuration-to-the-vpn-gateway"></a>VPN ゲートウェイにポイント対サイト構成を追加するには
共存設定で VPN ゲートウェイにポイント対サイト構成を追加するのには、次の手順に従います。

1. VPN クライアント アドレス プールを追加します。

  ```powershell
  $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
  Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $azureVpn -VpnClientAddressPool "10.251.251.0/24"
  ```
2. VPN ゲートウェイ用に、Azure に VPN ルート証明書をアップロードします。 この例では、次の PowerShell コマンドレットを実行しているローカル コンピューターにルート証明書が保存されていることを前提としています。

  ```powershell
  $p2sCertFullName = "RootErVpnCoexP2S.cer" 
  $p2sCertMatchName = "RootErVpnCoexP2S" 
  $p2sCertToUpload=get-childitem Cert:\CurrentUser\My | Where-Object {$_.Subject -match $p2sCertMatchName} 
  if ($p2sCertToUpload.count -eq 1){write-host "cert found"} else {write-host "cert not found" exit} 
  $p2sCertData = [System.Convert]::ToBase64String($p2sCertToUpload.RawData) Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $p2sCertFullName -VirtualNetworkGatewayname $azureVpn.Name -ResourceGroupName $resgrp.ResourceGroupName -PublicCertData $p2sCertData
  ```

ポイント対サイト VPN の詳細については、 [ポイント対サイト接続の構成](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順
ExpressRoute の詳細については、「 [ExpressRoute のFAQ](expressroute-faqs.md)」をご覧ください。
