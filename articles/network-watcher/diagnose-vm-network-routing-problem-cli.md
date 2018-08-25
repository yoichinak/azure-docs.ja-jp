---
title: 仮想マシン ネットワークのルーティングの問題を診断する - Azure CLI | Microsoft Docs
description: この記事では、Azure Network Watcher の次ホップ機能を使用して、仮想マシン ネットワークのルーティングの問題を診断する方法について説明します。
services: network-watcher
documentationcenter: network-watcher
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 15fb39a74047bdeffed0076501f0129eb00de4e8
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39443325"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-cli"></a>仮想マシン ネットワークのルーティングの問題を診断する - Azure CLI

この記事では、仮想マシン (VM) をデプロイし、IP アドレスおよび URL との通信を確認します。 通信障害の原因と解決方法を特定します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0.28 以降を実行していることが要件です。 インストールされているバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)」を参照してください。 CLI のバージョンを確認した後、`az login` を実行して Azure との接続を作成します。 この記事の CLI コマンドは、Bash シェルで実行されることを想定して書式設定されています。

## <a name="create-a-vm"></a>VM の作成

VM を作成する前に、その VM を含めるリソース グループを作成する必要があります。 [az group create](/cli/azure/group#az-group-create) を使用して、リソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[az vm create](/cli/azure/vm#az-vm-create) を使用して VM を作成します。 既定のキーの場所にまだ SSH キーが存在しない場合は、コマンドを使って SSH キーを作成します。 特定のキーのセットを使用するには、`--ssh-key-value` オプションを使用します。 次の例では、*myVm* という名前の VM を作成します。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

VM の作成には数分かかります。 VM が作成されて、CLI から出力が返されるまでは、次の手順に進まないでください。

## <a name="test-network-communication"></a>ネットワーク通信をテストする

Network Watcher を使用してネットワーク通信をテストするには、最初にテスト対象の VM が含まれるリージョンにおいて Network Watcher を有効にしてから、Network Watcher の次ホップ機能を使用して通信をテストする必要があります。

### <a name="enable-network-watcher"></a>ネットワーク ウォッチャーを有効にする

米国東部リージョンで既にネットワーク ウォッチャーを有効にしている場合は、「[次ホップの使用](#use-next-hop)」に進んでください。 [az network watcher configure](/cli/azure/network/watcher#az-network-watcher-configure) コマンドを使用して、米国東部リージョンにネットワーク ウォッチャーを作成します。

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-next-hop"></a>次ホップの使用

Azure では、既定の接続先へのルートを自動的に作成します。 既定のルートをオーバーライドするカスタム ルートを作成できます。 場合によっては、カスタム ルートが通信の失敗の原因になることがあります。 VM からのルーティングをテストするには、[az network watcher show-next-hop](/cli/azure/network/watcher?view=azure-cli-latest#az-network-watcher-show-next-hop) を使用して、トラフィックが特定のアドレスに送信されるときの次のルーティング ホップを確認します。

VM から www.bing.com のいずれかの IP アドレスへの送信通信をテストします。

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 13.107.21.200 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

数秒後、**[nextHopType]** が **[インターネット]** であり、**[routeTableId]** が **[System Route]\(システム ルート\)** であることが出力からわかります。 この結果から、接続先に対して有効なルートが存在することがわかります。

VM から 172.31.0.100 への送信通信をテストします。

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 172.31.0.100 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

返される出力は、**[nextHopType]** が **[なし]** であり、**[routeTableId]** が **[System Route]\(システム ルート\)** であることを示しています。 この結果から、送信先への有効なシステム ルートはあるものの、接続先にトラフィックをルーティングする次ホップはないことがわかります。

## <a name="view-details-of-a-route"></a>ルートの詳細の表示

さらに詳細にルーティングを分析するには、[az network nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table) コマンドを使用して、ネットワーク インターフェイスの有効なルートを確認します。

```azurecli-interactive
az network nic show-effective-route-table \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

返される出力には、次のテキストが含まれます。

```azurecli
{
  "additionalProperties": {
    "disableBgpRoutePropagation": false
  },
  "addressPrefix": [
    "0.0.0.0/0"
  ],
  "name": null,
  "nextHopIpAddress": [],
  "nextHopType": "Internet",
  "source": "Default",
  "state": "Active"
},
```

「[次ホップの使用](#use-next-hop)」で、13.107.21.200 への送信通信のテストに `az network watcher show-next-hop` コマンドを使用したときは、出力の他のルートにはそのアドレスが含まれていないため、**addressPrefix** が 0.0.0.0/0** のルートがアドレスへのトラフィックのルーティングに使用されました。 既定では、他のルートのアドレス プレフィックス内で指定されていないすべてのアドレスが、インターネットにルーティングされます。

ただし、172.31.0.100 への送信通信のテストに `az network watcher show-next-hop` コマンドを使用したときの結果は、次ホップの種類がないことを示しています。 返される出力には、次のテキストも含まれます。

```azurecli
{
  "additionalProperties": {
    "disableBgpRoutePropagation": false
      },
  "addressPrefix": [
    "172.16.0.0/12"
  ],
  "name": null,
  "nextHopIpAddress": [],
  "nextHopType": "None",
  "source": "Default",
  "state": "Active"
},
```

`az network watcher nic show-effective-route-table` コマンドの出力でもわかるように、アドレス 172.31.0.100 を含む 172.16.0.0/12 プレフィックスへの既定のルートがあるにもかかわらず、**[nextHopType]** は **[なし]** になっています。 Azure では、172.16.0.0/12 への既定のルートを作成しますが、理由がない限り次ホップの種類は指定しません。 たとえば、仮想ネットワークのアドレス空間に 172.16.0.0/12 アドレス範囲を追加した場合は、ルートの **[nextHopType]** を **[仮想ネットワーク]** に変更します。 **[nextHopType]** として **[仮想ネットワーク]** にチェック マークが表示されます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

不要になったら、[az group delete](/cli/azure/group#az-group-delete) を使用して、リソース グループとそのグループに含まれているすべてのリソースを削除できます。

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>次の手順

この記事では、VM を作成し、VM からのネットワークのルーティングを診断しました。 Azure では複数の既定のルートが作成されることを学習し、2 つの異なる送信先へのルーティングをテストしました。 詳細については、[Azure でのルーティング](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)と[カスタム ルートを作成する](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route)方法に関するページを参照してください。

送信 VM 接続の場合は、Network Watcher の[接続のトラブルシューティング](network-watcher-connectivity-cli.md)機能を使用することにより、待機時間に加えて、VM とエンドポイント間で許可/拒否されているネットワーク トラフィックを確認することもできます。 Network Watcher 接続監視機能を使用すると、VM とエンドポイント (IP アドレスや URL など) の間の通信を継時的に監視することができます。 方法については、[ネットワーク接続の監視](connection-monitor.md)に関するページを参照してください。