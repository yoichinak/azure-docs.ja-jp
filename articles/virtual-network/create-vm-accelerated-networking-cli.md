---
title: 高速ネットワークを使った Azure 仮想マシンの作成 | Microsoft Docs
description: 高速ネットワークを使った Linux 仮想マシンの作成方法について説明します。
services: virtual-network
documentationcenter: na
author: gsilva5
manager: gedegrac
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/02/2018
ms.author: gsilva
ms.custom: ''
ms.openlocfilehash: 9ea843df4cf437b97f7fe1d62636a51f8201376e
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39414574"
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking"></a>高速ネットワークを使った Linux 仮想マシンの作成

このチュートリアルでは、高速ネットワークを有効にして Linux 仮想マシン (VM) を作成する方法について説明します。 高速ネットワークを使って Windows VM を作成する場合は、「[Accelerated Networking を使った Windows VM を作成する](create-vm-accelerated-networking-powershell.md)」をご覧ください。 高速ネットワークによって、VM との間でシングル ルート I/O 仮想化 (SR-IOV) が可能になり、ネットワークのパフォーマンスが大幅に向上します。 この高パフォーマンスのパスによってデータパスからホストがバイパスされ、サポートされる VM タイプの最も要件の厳しいネットワーク ワークロードで使用した場合でも、待ち時間、ジッター、CPU 使用率が軽減されます。 次の図は、2 台の VM 間の通信を、高速ネットワークを使う場合と使わない場合とで比較したものです。

![比較](./media/create-vm-accelerated-networking/accelerated-networking.png)

高速ネットワークを使用しない場合は、VM に出入りするすべてのネットワーク トラフィックがホストと仮想スイッチをスキャンする必要があります。 仮想スイッチでは、ネットワーク セキュリティ グループ、アクセス制御リスト、分離、その他のネットワーク仮想化サービスなど、すべてのポリシーが適用されます。 仮想スイッチの詳細については、[Hyper-V ネットワーク仮想化と仮想スイッチ](https://technet.microsoft.com/library/jj945275.aspx)に関する記事を参照してください。

高速ネットワークを使用した場合、ネットワーク トラフィックは、VM のネットワーク インターフェイス (NIC) に到達した後、VM に転送されます。 仮想スイッチによって適用されるすべてのネットワーク ポリシーはオフロードされ、ハードウェアで適用されるようになりました。 ハードウェアにポリシーを適用することによって、ホストに適用されるポリシーをすべて維持したまま、ホストや仮想スイッチをバイパスして、NIC からネットワーク トラフィックを直接 VM に転送できます。

高速ネットワークのメリットが得られるのは、高速ネットワークが有効になっている VM だけです。 最適な結果を得るには、同じ Azure 仮想ネットワーク (VNet) に接続された 2 台以上の VM でこの機能を有効にしておくことをお勧めします。 VNet の境界を越えて通信するときや、オンプレミスで接続する場合、全体的な待ち時間に対してこの機能がもたらす効果は限定的です。

## <a name="benefits"></a>メリット
* **待機時間の短縮 / 1 秒あたりのパケット数 (pps)の向上:** データパスから仮想スイッチを削除することで、ホストにおけるパケットのポリシー処理に必要な時間がなくなるため、VM 内で処理できるパケット数が増加します。
* **ジッターの削減:** 仮想スイッチの処理は、適用するポリシーの量と、処理を行う CPU のワークロードによって異なります。 ハードウェアへのポリシーの適用をオフロードすると、パケットが直接 VM に配信され、ホストと VM 間の通信とソフトウェアによる干渉やコンテキスト スイッチがなくなるため、そのばらつきはなくなります。
* **CPU 使用率の削減:** ホストの仮想スイッチをバイパスすることによって、ネットワーク トラフィックを処理するための CPU の使用率を軽減できます。

## <a name="supported-operating-systems"></a>サポートされているオペレーティング システム
Azure ギャラリーでは次のディストリビューションが既定でサポートされています。 
* **Ubuntu 16.04** 
* **SLES 12 SP3** 
* **RHEL 7.4**
* **CentOS 7.4**
* **CoreOS Linux**
* **Debian "Stretch" (バックポート カーネルを含む)**
* **Oracle Linux 7.4**

## <a name="limitations-and-constraints"></a>制限と制約

### <a name="supported-vm-instances"></a>サポートされている VM インスタンス
高速ネットワークは、2 つ以上の vCPU を持つ、コンピューティングに最適化された多くの汎用のインスタンス サイズでサポートされています。  サポートされているシリーズは、D/DSv2 と F/Fs です

ハイパースレッディングをサポートするインスタンスでは、4 以上の vCPU を持つ VM インスタンスで高速ネットワークがサポートされています。 サポートされているシリーズは、D/DSv3、E/ESv3、Fsv2、Ms/Mms です。

VM インスタンスの詳細については、「[Linux 仮想マシンのサイズ](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。

### <a name="regions"></a>リージョン
すべてのパブリック Azure リージョンと Azure Government クラウドで使用できます。

### <a name="network-interface-creation"></a>ネットワーク インターフェイスの作成 
高速ネットワークは、新しい NIC でのみ有効にできます。 既存の NIC に対して有効にすることはできません。
### <a name="enabling-accelerated-networking-on-a-running-vm"></a>実行中の VM で高速ネットワークを有効にする
サポートされる VM サイズで、高速ネットワークが有効になっていない場合は、VM が停止され割り当てを解除されているときにだけ機能を有効にできます。  
### <a name="deployment-through-azure-resource-manager"></a>Azure Resource Manager によるデプロイ
仮想マシン (クラシック) は、高速ネットワークを使用したデプロイはできません。

## <a name="create-a-linux-vm-with-azure-accelerated-networking"></a>Azure 高速ネットワークが有効な Linux VM を作成する

この記事では、高速ネットワークを使用した仮想マシンを、Azure CLI を使って作成する手順について説明しますが、[高速ネットワークを使用した仮想マシンは、Azure Portal を使って作成することもできます](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 ポータルで仮想マシンを作成する際に、**[設定]** の **[高速ネットワーク]** で **[有効]** を選択します。 高速ネットワークを有効にするオプションは、[サポートされるオペレーティング システム](#supported-operating-systems)と [VM サイズ](#supported-vm-instances)を選択しない限り、ポータルには表示されません。 仮想マシンが作成されたら、「[オペレーティング システムで高速ネットワークが有効化されていることを確認する](#confirm-that-accelerated-networking-is-enabled)」の手順を実行する必要があります。

### <a name="create-a-virtual-network"></a>仮想ネットワークの作成

最新の [Azure CLI 2.0](/cli/azure/install-az-cli2) をインストールし、[az login](/cli/azure/reference-index#az_login) を使用して Azure アカウントにログインします。 次の例では、パラメーター名を独自の値を置き換えます。 たとえば、*myResourceGroup*、*myNic*、*myVM* といったパラメーター名にします。

[az group create](/cli/azure/group#az_group_create) を使用して、リソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを場所 *centralus* に作成します。

```azurecli
az group create --name myResourceGroup --location centralus
```

「[Linux accelerated networking](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview)」(Linux 高速ネットワーク) に記載されているサポート対象の Linux リージョンを選択します。

[az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) を使用して仮想ネットワークを作成します。 次の例では、1 つのサブネットで *myVnet* という名前の仮想ネットワークを作成します。

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

### <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成
[az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) で、ネットワーク セキュリティ グループを作成します。 次の例では、*myNetworkSecurityGroup* という名前のネットワーク セキュリティ グループを作成します。

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

ネットワーク セキュリティ グループには、既定の規則がいくつか含まれています。そのうちの 1 つは、インターネットからのすべての着信アクセスを無効にします。 ポートを開き、[az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) を使用して、仮想マシンに SSH アクセスできるようにします。

```azurecli
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup \
  --name Allow-SSH-Internet \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 22
```

### <a name="create-a-network-interface-with-accelerated-networking"></a>高速ネットワークを使ったネットワーク インターフェイスの作成

パブリック IP アドレスは、[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) で作成します。 インターネットから仮想マシンにアクセスする計画がない場合、パブリック IP アドレスは不要ですが、この記事の手順を完了するには必要です。

```azurecli
az network public-ip create \
    --name myPublicIp \
    --resource-group myResourceGroup
```

高速ネットワークを有効にし、[az network nic create](/cli/azure/network/nic#az_network_nic_create) を使用してネットワーク インターフェイスを作成します。 次の例では、*myVnet* 仮想ネットワークの *mySubnet* サブネットの *myNic* というネットワーク インターフェイスを作成し、*myNetworkSecurityGroup* ネットワーク セキュリティ グループをネットワーク インターフェイスに関連付けます。

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --accelerated-networking true \
    --public-ip-address myPublicIp \
    --network-security-group myNetworkSecurityGroup
```

### <a name="create-a-vm-and-attach-the-nic"></a>VM を作成して NIC を接続する
VM を作成するとき、`--nics` を使用して作成した NIC を指定します。 「[Linux accelerated networking](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview)」(Linux 高速ネットワーク) に記載されているサイズとディストリビューションを選択します。 

[az vm create](/cli/azure/vm#az_vm_create) を使用して VM を作成します。 次の例では、UbuntuLTS イメージと高速ネットワークをサポートするサイズ (*Standard_DS4_v2*) を使用して、*myVM* という VM を作成します。

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS4_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic
```

すべての VM のサイズと特性の一覧は、[Linux VM のサイズ](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関するページを参照してください。

VM が作成されると、次のサンプル出力のような出力が返されます。 **publicIpAddress** を書き留めておきます。 このアドレスは、後の手順で VM にアクセスするために使われます。

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "centralus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "192.168.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

### <a name="confirm-that-accelerated-networking-is-enabled"></a>高速ネットワークが有効になっていることを確認します。

次のコマンドを使用して、VM との SSH セッションを作成します。 作成した仮想マシンに割り当てたパブリック IP アドレスに `<your-public-ip-address>` を置き換え、VM の作成時に `--admin-username` に異なる値を使用していた場合は、*azureuser* を置き換えます。

```bash
ssh azureuser@<your-public-ip-address>
```

Bash シェルから `uname -r` を入力し、カーネル バージョンが次のいずれかのバージョン以降であることを確認します。

* **Ubuntu 16.04**: 4.11.0-1013
* **SLES SP3**: 4.4.92-6.18
* **RHEL**: 7.4.2017120423
* **CentOS**: 7.4.20171206


`lspci` コマンドを使用して、Mellanox VF デバイスが VM に公開されていることを確認します。 次の出力のような出力が返されます。

```bash
0000:00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (AGP disabled) (rev 03)
0000:00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 01)
0000:00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
0000:00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 02)
0000:00:08.0 VGA compatible controller: Microsoft Corporation Hyper-V virtual VGA
0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
```

`ethtool -S eth0 | grep vf_` コマンドを使用して、VF (仮想関数) のアクティビティを確認します。 次のサンプル出力と似た出力を受け取った場合、高速ネットワークは有効で、動作しています。

```bash
vf_rx_packets: 992956
vf_rx_bytes: 2749784180
vf_tx_packets: 2656684
vf_tx_bytes: 1099443970
vf_tx_dropped: 0
```
VM の高速ネットワークが有効になりました。

## <a name="enable-accelerated-networking-on-existing-vms"></a>既存の VM 上で高速ネットワークを有効にする
高速ネットワークを有効にしないで VM を作成した場合は、既存の VM に対してこの機能を有効にすることができです。  VM は、上記で説明した次の前提条件を満たすことによって、高速ネットワークをサポートしている必要があります。

* VM は高速ネットワークがサポートされるサイズである必要があります
* VM はサポートされる Azure ギャラリー イメージ (および Linux のカーネル バージョン) である必要があります
* 可用性セット内または VMSS 内のすべての VM は、NIC 上で高速ネットワークを有効にする前に、停止/割り当てを解除しておく必要があります

### <a name="individual-vms--vms-in-an-availability-set"></a>個別の VM および可用性セット内の VM
最初に、VM (可用性セットの場合は、セット内のすべての VM) を停止/割り当てを解除します。

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

可用性セットを使用しないで個別に作成した VM の場合、高速ネットワークを有効にするには、個別の VM を停止/割り当てを解除することだけが必要です。  可用性セットを使用して作成された VM の場合は、いずれかの NIC 上で高速ネットワークを有効にする前に、可用性セットに含まれるすべての VM を停止/割り当てを解除する必要があります。 

停止した後、VM の NIC 上で高速ネットワークを有効にします。

```azurecli
az network nic update \
    --name myNic \
    --resource-group myResourceGroup \
    --accelerated-networking true
```

VM (可用性セットの場合はセット内のすべての VM) を再起動し、高速ネットワークが有効になっていることを確認します。 

```azurecli
az vm start --resource-group myResourceGroup \
    --name myVM
```

### <a name="vmss"></a>VMSS
VMSS は若干異なりますが、同じワークフローに従います。  最初に、VM を停止します。

```azurecli
az vmss deallocate \
    --name myvmss \
    --resource-group myrg
```

VM が停止した後、ネットワーク インターフェイスにおいて高速ネットワークのプロパティを更新します。

```azurecli
az vmss update --name myvmss \
    --resource-group myrg \
    --set virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].enableAcceleratedNetworking=true
```

VMSS には、3 つの異なる設定 (自動、ローリング、手動) を使用して更新を適用する VM のアップグレードがあります。  この手順では、再起動後すぐに VMSS がポリシーを選択するように、ポリシーを自動に設定します。  変更がすぐに選択されるように自動に設定するには、次のようにします。 

```azurecli
az vmss update \
    --name myvmss \
    --resource-group myrg \
    --set upgradePolicy.mode="automatic"
```

最後に、VMSS を再起動します。

```azurecli
az vmss start \
    --name myvmss \
    --resource-group myrg
```

再起動した後、アップグレードが完了するまで待ちます。完了すると、VM 内に VF が表示されます。  (サポートされる OS と VM サイズを使用していることを確認してください。)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>高速ネットワークが有効な既存の VM のサイズを変更する

高速ネットワークが有効になっている VM のサイズは、高速ネットワークをサポートする VM にのみ変更できます。  

高速ネットワークが有効になっている VM のサイズを、高速ネットワークをサポートしていない VM インスタンスに、サイズ変更操作を使って変更することはできません。  このような VM のサイズを変更するには、次のようにします。 

* VM を停止/割り当てを解除します。可用性セット/VMSS の場合は、セット/VMSS 内のすべての VM を停止/割り当てを解除します。
* VM の NIC 上で、高速ネットワークを無効にする必要があります。可用性セット/VMSS の場合は、セット/VMSS 内のすべての VM において無効にします。
* 高速ネットワークが無効になったら、高速ネットワークをサポートしていない新しいサイズに VM/可用性セット/VMSS を移動して、再起動します。  

