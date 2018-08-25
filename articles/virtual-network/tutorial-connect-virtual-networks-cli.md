---
title: 仮想ネットワーク ピアリングを使用して仮想ネットワークを接続する - Azure CLI | Microsoft Docs
description: この記事では、Azure CLI を使って仮想ネットワーク ピアリングで仮想ネットワークを接続する方法を説明します。
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 29ab957e97c6aa57be6192e6ee4d86fe642ae95d
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38307858"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-cli"></a>Azure CLI を使用して仮想ネットワーク ピアリングで仮想ネットワークを接続する

仮想ネットワーク ピアリングを使用して、仮想ネットワークを相互に接続できます。 仮想ネットワークをピアリングすると、それぞれの仮想ネットワークに存在するリソースが、あたかも同じ仮想ネットワーク内に存在するかのような待ち時間と帯域幅で相互に通信できます。 この記事では、次のことについて説明します:

* 2 つの仮想ネットワークを作成する
* 仮想ネットワーク ピアリングを使用して 2 つの仮想ネットワークを接続する
* 各仮想ネットワークに仮想マシン (VM) を展開する
* VM 間の通信

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0.28 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)」を参照してください。 

## <a name="create-virtual-networks"></a>仮想ネットワークを作成する

仮想ネットワークを作成する前に、仮想ネットワークのリソース グループと、この記事で作成された他のすべてのリソースを作成する必要があります。 [az group create](/cli/azure/group#az_group_create) を使用して、リソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

[az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) を使用して仮想ネットワークを作成します。 次の例では、アドレス プレフィックスが *10.0.0.0/16* の、*myVirtualNetwork1* という名前の仮想ネットワークを作成します。

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.0.0.0/24
```

アドレス プレフィックスが *10.1.0.0/16* の、*myVirtualNetwork2* という名前の仮想ネットワークを作成します。

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --address-prefixes 10.1.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.1.0.0/24
```

## <a name="peer-virtual-networks"></a>仮想ネットワークをピアリングする

ピアリングは仮想ネットワーク ID 間で確立されるため、最初に [az network vnet show](/cli/azure/network/vnet#az_network_vnet_show) を使用して各仮想ネットワーク の ID を取得し、その ID を変数に格納する必要があります。

```azurecli-interactive
# Get the id for myVirtualNetwork1.
vNet1Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork1 \
  --query id --out tsv)

# Get the id for myVirtualNetwork2.
vNet2Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork2 \
  --query id \
  --out tsv)
```

[az network vnet peering create](/cli/azure/network/vnet/peering#az_network_vnet_peering_create) を使用して、*myVirtualNetwork1* から *myVirtualNetwork2* へのピアリングを作成します。 `--allow-vnet-access` パラメーターが指定されていない場合、ピアリングは確立されますが、通信を行うことはできません。

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --remote-vnet-id $vNet2Id \
  --allow-vnet-access
```

前のコマンドの実行後に返された出力では、**peeringState** が *Initiated* です。 このピアリングは、*myVirtualNetwork2* から *myVirtualNetwork1* へのピアリングを作成するまで *Initiated* 状態のままです。 *myVirtualNetwork2* から *myVirtualNetwork1* へのピアリングを作成します。 

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork2-myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork2 \
  --remote-vnet-id $vNet1Id \
  --allow-vnet-access
```

前のコマンドの実行後に返された出力では、**peeringState** が *Connected* です。 Azure によって、*myVirtualNetwork1-myVirtualNetwork2* ピアリングのピアリング状態も *Connected* に変更されました。 [az network vnet peering show](/cli/azure/network/vnet/peering#az_network_vnet_peering_show) を使用して、*myVirtualNetwork1-myVirtualNetwork2* ピアリングのピアリング状態が *Connected* に変更されたことを確認します。

```azurecli-interactive
az network vnet peering show \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --query peeringState
```

両方の仮想ネットワークのピアリングの **peeringState** が *Connected* になるまで、一方の仮想ネットワークのリソースともう一方の仮想ネットワークのリソースは通信できません。 

## <a name="create-virtual-machines"></a>仮想マシンを作成する

後で仮想ネットワーク間で通信できるように、各仮想ネットワーク内に VM を作成します。

### <a name="create-the-first-vm"></a>最初の VM を作成する

[az vm create](/cli/azure/vm#az_vm_create) を使用して VM を作成します。 次の例では、*myVirtualNetwork1* 仮想ネットワークで *myVm1* という名前の VM を作成します。 既定のキーの場所にまだ SSH キーが存在しない場合は、コマンドを使って SSH キーを作成します。 特定のキーのセットを使用するには、`--ssh-key-value` オプションを使用します。 `--no-wait` オプションを使用すると、VM はバックグラウンドで作成されるため、次の手順に進むことができます。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork1 \
  --subnet Subnet1 \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>2 つ目の VM を作成する

*myVirtualNetwork2* 仮想ネットワーク内に VM を作成します。

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork2 \
  --subnet Subnet1 \
  --generate-ssh-keys
```

VM の作成には数分かかります。 VM が作成されると、Azure CLI によって次の例のような情報が表示されます。 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.1.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

**publicIpAddress** を書き留めておきます。 このアドレスは、後の手順で、インターネットから VM にアクセスするときに使います。

## <a name="communicate-between-vms"></a>VM 間の通信

次のコマンドを使用して、*myVm2* VM との SSH セッションを作成します。 `<publicIpAddress>` を VM のパブリック IP アドレスに置き換えます。 前の例では、パブリック IP アドレスは *13.90.242.231* です。

```bash 
ssh <publicIpAddress>
```

*myVirtualNetwork1* 内の VM に対して ping を実行します。

```bash 
ping 10.0.0.4 -c 4
```

4 つの応答を受信します。 

*myVm2* VM への SSH セッションを閉じます。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

不要になったら、[az group delete](/cli/azure/group#az_group_delete) を使用して、リソース グループとそのグループに含まれているすべてのリソースを削除します。

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>次の手順

この記事では、仮想ネットワーク ピアリングで同じ Azure リージョン内の 2 つのネットワークを接続する方法を説明しました。 異なる[サポートされるリージョン](virtual-network-manage-peering.md#cross-region)内および[異なる Azure サブスクリプション](create-peering-different-subscriptions.md#cli)内の仮想ネットワークをピアリングすることも、ピアリングを使って[ハブとスポーク ネットワーク設計](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering)を作成することもできます。 仮想ネットワーク ピアリングについて詳しくは、[仮想ネットワーク ピアリングの概要](virtual-network-peering-overview.md)および[仮想ネットワーク ピアリングの管理](virtual-network-manage-peering.md)に関するページをご覧ください。

[ユーザーのコンピューターを VPN 経由で仮想ネットワークに接続](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)し、仮想ネットワーク、またはピアリングされた仮想ネットワークのリソースを操作できます。 仮想ネットワークの記事で説明する多くのタスクを完了するための再利用可能なスクリプトについては、[スクリプト サンプル](cli-samples.md)をご覧ください。