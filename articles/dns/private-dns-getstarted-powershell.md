---
title: 'チュートリアル: Azure PowerShell を使用して Azure DNS プライベート ゾーンを作成する'
description: このチュートリアルでは、Azure DNS でプライベート DNS ゾーンとレコードを作成してテストします。 これは、Azure PowerShell を使用して最初のプライベート DNS ゾーンとレコードを作成して管理するためのステップバイステップ ガイドです。
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 7/24/2018
ms.author: victorh
ms.openlocfilehash: 872227e0521bd54e6bf7fdbe3626dfca34170863
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257727"
---
# <a name="tutorial-create-an-azure-dns-private-zone-using-azure-powershell"></a>チュートリアル: Azure PowerShell を使用して Azure DNS プライベート ゾーンを作成する

このチュートリアルでは、Azure PowerShell を使用して最初のプライベート DNS ゾーンとレコードを作成する手順について説明します。

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

DNS ゾーンは、特定のドメインの DNS レコードをホストするために使用されます。 Azure DNS でドメインのホストを開始するには、そのドメイン名用に DNS ゾーンを作成する必要があります。 ドメインの DNS レコードはすべて、この DNS ゾーン内に作成されます。 仮想ネットワークにプライベート DNS ゾーンを発行するには、そのゾーン内のレコードを解決することが認められた仮想ネットワークの一覧を指定します。  これらを "*解決仮想ネットワーク*" と呼びます。 VM が作成されたときや IP が変更されたとき、または VM が削除されたときに絶えず Azure DNS によってホスト名レコードが維持される仮想ネットワークを指定することもできます。  これを "*登録仮想ネットワーク*" と呼びます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * DNS プライベート ゾーンの作成
> * テスト用仮想マシンの作成
> * 追加の DNS レコードの作成
> * プライベート ゾーンのテスト

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

好みに応じて、[Azure CLI](private-dns-getstarted-cli.md) を使ってこのチュートリアルの手順を実行することもできます。

<!--- ## Get the Preview PowerShell modules
These instructions assume you have already installed and signed in to Azure PowerShell, including ensuring you have the required modules for the Private Zone feature. -->

<!---[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)] -->

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="create-the-resource-group"></a>リソース グループの作成

最初に、DNS ゾーンが含まれるリソース グループを作成します。 

```azurepowershell
New-AzureRMResourceGroup -name MyAzureResourceGroup -location "eastus"
```

## <a name="create-a-dns-private-zone"></a>DNS プライベート ゾーンの作成

`New-AzureRmDnsZone` コマンドレットを使用し、**ZoneType** パラメーターに *Private* という値を指定して DNS ゾーンを作成します。 次の例では、**MyAzureResourceGroup** というリソース グループに **contoso.local** という DNS ゾーンを作成し、その DNS ゾーンを **MyAzureVnet** という仮想ネットワークで利用できるようにします。

**ZoneType** パラメーターを省略した場合、そのゾーンはパブリック ゾーンとして作成されます。そのため、プライベート ゾーンを作成する場合はこのパラメーターが必須です。 

```azurepowershell
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name backendSubnet -AddressPrefix "10.2.0.0/24"
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName MyAzureResourceGroup `
  -Location eastus `
  -Name myAzureVNet `
  -AddressPrefix 10.2.0.0/16 `
  -Subnet $backendSubnet

New-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyAzureResourceGroup `
   -ZoneType Private `
   -RegistrationVirtualNetworkId @($vnet.Id)
```

(ホスト名の自動作成なしで) 名前解決のみを目的としてゾーンを作成する場合は、*RegistrationVirtualNetworkId* パラメーターの代わりに *ResolutionVirtualNetworkId* パラメーターを使用できます。

> [!NOTE]
> 自動的に作成されたホスト名レコードは表示されません。 ただし、後でテストを実行して、その存在を確認できます。

### <a name="list-dns-private-zones"></a>DNS プライベート ゾーンの一覧表示

`Get-AzureRmDnsZone`からゾーン名を省略することで、リソース グループ内のすべてのゾーンを列挙できます。 この操作により、ゾーン オブジェクトの配列が返されます。

```azurepowershell
Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

`Get-AzureRmDnsZone` でゾーン名とリソース グループ名の両方を省略すると、Azure サブスクリプションにすべてのゾーンを列挙できます。

```azurepowershell
Get-AzureRmDnsZone
```

## <a name="create-the-test-virtual-machines"></a>テスト用仮想マシンの作成

次に、プライベート DNS ゾーンをテストできるように 2 つの仮想マシンを作成します。

```azurepowershell
New-AzureRmVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM01" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389

New-AzureRmVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM02" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389
```

これが完了するまでに数分かかります。

## <a name="create-an-additional-dns-record"></a>追加の DNS レコードの作成

レコード セットは、`New-AzureRmDnsRecordSet` コマンドレットを使用して作成します。 次の例では、リソース グループ **MyAzureResourceGroup** の DNS ゾーン **contoso.local** に、相対名が **db** のレコードを作成します。 レコード セットの完全修飾名は、**db.contoso.local** になります。 レコードの種類は "A"、IP アドレスは "10.2.0.4"、TTL は 3,600 秒です。

```azurepowershell
New-AzureRmDnsRecordSet -Name db -RecordType A -ZoneName contoso.local `
   -ResourceGroupName MyAzureResourceGroup -Ttl 3600 `
   -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "10.2.0.4")
```

### <a name="view-dns-records"></a>DNS レコードの表示

ゾーン内の DNS レコードを一覧表示するには、次のコマンドを実行します。

```azurepowershell
Get-AzureRmDnsRecordSet -ZoneName contoso.local -ResourceGroupName MyAzureResourceGroup
```
ただし、2 台のテスト用仮想マシンに対して自動的に作成された A レコードは表示されません。

## <a name="test-the-private-zone"></a>プライベート ゾーンのテスト

これで、**contoso.local** プライベート ゾーンでの名前解決をテストできます。

### <a name="configure-vms-to-allow-inbound-icmp"></a>受信 ICMP を許可するように VM を構成する

名前解決は ping コマンドを使用してテストできます。 そこで、両方の仮想マシン上のファイアウォールを、受信 ICMP パケットを許可するように構成します。

1. myVM01 に接続し、管理者特権で Windows PowerShell ウィンドウを開きます。
2. 次のコマンドを実行します。

   ```powershell
   New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
   ```

MyVM02 についても同じ手順を繰り返します。

### <a name="ping-the-vms-by-name"></a>VM を名前で ping する

1. myVM02 の Windows PowerShell コマンド プロンプトから、自動的に登録されたホスト名を使用して myVM01 を ping します。
   ```
   ping myVM01.contoso.local
   ```
   次のような出力が表示されます。
   ```
   PS C:\> ping myvm01.contoso.local

   Pinging myvm01.contoso.local [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```
2. 次に、前に作成した **db** 名を ping します。
   ```
   ping db.contoso.local
   ```
   次のような出力が表示されます。
   ```
   PS C:\> ping db.contoso.local

   Pinging db.contoso.local [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>すべてのリソースの削除

このチュートリアルで作成したリソースが不要になったときに削除するには、**myresourcegroup** リソース グループを削除します。

```azurepowershell
Remove-AzureRMResourceGroup -Name MyAzureResourceGroup
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、プライベート DNS ゾーンをデプロイし、DNS レコードを作成し、ゾーンのテストを行いました。
次は、プライベート DNS ゾーンについてさらに詳しく学習します。

> [!div class="nextstepaction"]
> [プライベート ドメインに Azure DNS を使用する](private-dns-overview.md)




