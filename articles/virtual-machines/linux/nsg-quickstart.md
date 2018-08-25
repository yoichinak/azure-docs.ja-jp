---
title: Azure CLI 2.0 を使用した Linux VM へのポートの開放 | Microsoft Docs
description: Azure Resource Manager デプロイ モデルと Azure CLI 2.0 を使用して、Linux VM へのポートを開き、エンドポイントを作成する方法について説明する
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: eef9842b-495a-46cf-99a6-74e49807e74e
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: 7125523b051441a2547560ff3af650ccd91f07a0
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928891"
---
# <a name="open-ports-and-endpoints-to-a-linux-vm-with-the-azure-cli"></a>Azure CLI 使用した Linux VM へのポートとエンドポイントの開放
サブネットまたは仮想マシン (VM) ネットワーク インターフェイスでネットワーク フィルターを作成して、Azure で VM へのポートを開くか、エンドポイントを作成します。 着信および発信の両方のトラフィックを制御するこれらのフィルターを、トラフィックを受信するリソースに接続されているネットワーク セキュリティ グループに配置します。 ポート 80 での Web トラフィックの一般的な例を使用して説明します。 この記事では、Azure CLI 2.0 を使用して VM へのポートを開く方法を説明します。 

ネットワーク セキュリティ グループとルールを作成するには、最新の [Azure CLI 2.0](/cli/azure/install-az-cli2) がインストールされ、[az login](/cli/azure/reference-index#az_login) を使用して Azure アカウントにログインしている必要があります。

次の例では、パラメーター名を独自の値を置き換えます。 たとえば、*myResourceGroup*、*myNetworkSecurityGroup*、*myVnet* といったパラメーター名にします。


## <a name="quickly-open-a-port-for-a-vm"></a>VM 用のポートを簡単に開く
開発/テスト シナリオで VM 用のポートを簡単に開く必要がある場合は、[az vm open-port](/cli/azure/vm#az_vm_open_port) コマンドを使用できます。 このコマンドは、ネットワーク セキュリティ グループを作成して規則を追加し、これを VM またはサブネットに適用します。 次の例では、*myResourceGroup* というリソース グループ内の *myVM* という VM に、ポート *80* を開きます。

```azure-cli
az vm open-port --resource-group myResourceGroup --name myVM --port 80
```

発信元 IP アドレスの範囲を定義するなど、規則をより細かく制御するには、この記事の追加の手順を続行します。


## <a name="create-a-network-security-group-and-rules"></a>ネットワーク セキュリティ グループと規則の作成
[az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) で、ネットワーク セキュリティ グループを作成します。 次の例では、*myNetworkSecurityGroup* という名前のネットワーク セキュリティ グループを *eastus* に作成します。

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

[az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) を使用して、Web サーバーへの HTTP トラフィックを許可する規則を追加します (または SSH アクセス、データベース接続など、独自のシナリオに合わせて調整します)。 次の例では、*myNetworkSecurityGroupRule* という名前の規則を作成して、ポート 80 での TCP トラフィックを許可します。

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 80
```


## <a name="apply-network-security-group-to-vm"></a>ネットワーク セキュリティ グループを VM に適用する
[az network nic update](/cli/azure/network/nic#az_network_nic_update)を使用して、ネットワーク セキュリティ グループと仮想マシンのネットワーク インターフェイス (NIC) を関連付けます。 次の例では、*myNic* という名前の既存の NIC を *myNetworkSecurityGroup* という名前のネットワーク セキュリティ グループに関連付けます。

```azurecli
az network nic update \
    --resource-group myResourceGroup \
    --name myNic \
    --network-security-group myNetworkSecurityGroup
```

また、ネットワーク セキュリティ グループは、単一の VM のネットワーク インターフェイスに関連付けるのではなく、[az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) を使用して、仮想ネットワークのサブネットに関連付けることもできます。 次の例では、*myVnet* 仮想ネットワーク内の *mySubnet* という名前の既存のサブネットを *myNetworkSecurityGroup* という名前のネットワーク セキュリティ グループに関連付けます。

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>ネットワーク セキュリティ グループの詳細
このページのクイック コマンドでは、VM にフローするトラフィックの開始と実行を行うことができます。 ネットワーク セキュリティ グループには優れた機能が多数用意されており、リソースへのアクセスをきめ細かく制御できます。 詳細については、 [ネットワーク セキュリティ グループと ACL 規則の作成](tutorial-virtual-network.md#secure-network-traffic)に関するページをご覧ください。

高可用性 Web アプリケーション用に、Azure Load Balancer の背後に VM を配置する必要があります。 ロード バランサーは、トラフィックをフィルターできるネットワーク セキュリティ グループとともに、VM のトラフィックを分散します。 詳細については、[Azure の Linux 仮想マシンを負荷分散して高可用性アプリケーションを作成する方法](tutorial-load-balancer.md)に関するページをご覧ください。

## <a name="next-steps"></a>次の手順
この例では、HTTP トラフィックを許可する単純な規則を作成します。 より精密な環境の作成については、次の記事で確認できます。

* [Azure リソース マネージャーの概要](../../azure-resource-manager/resource-group-overview.md)
* [ネットワーク セキュリティ グループ (NSG) について](../../virtual-network/security-overview.md)
