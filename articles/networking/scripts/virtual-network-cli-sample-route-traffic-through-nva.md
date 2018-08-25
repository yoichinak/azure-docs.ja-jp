---
title: Azure CLI スクリプト サンプル - ネットワーク仮想アプライアンス経由のトラフィックのルーティング | Microsoft Docs
description: Azure CLI スクリプト サンプル - ファイアウォール ネットワーク仮想アプライアンス経由のトラフィックのルーティング
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: jdial
ms.openlocfilehash: 62077f45d96e96a7fef35cf025740849d2b99445
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2018
ms.locfileid: "42141746"
---
# <a name="route-traffic-through-a-network-virtual-appliance"></a>ネットワーク仮想アプライアンス経由のトラフィックのルーティング

このスクリプト サンプルは、フロントエンド サブネットとバックエンド サブネットを含む仮想ネットワークを作成します。 2 つのサブネット間でトラフィックをルーティングするために、IP 転送を有効にした VM も作成します。 スクリプトの実行後には、ファイアウォール アプリケーションなどのネットワーク ソフトウェアを VM にデプロイできます。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>サンプル スクリプト


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.sh "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、仮想ネットワーク、およびネットワーク セキュリティ グループを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Azure 仮想ネットワークとフロントエンド サブネットを作成します。 |
| [az network subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | バックエンドおよび DMZ サブネットを作成します。 |
| [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) | インターネットから VM にアクセスするためのパブリック IP アドレスを作成します。 |
| [az network nic create](/cli/azure/network/nic#az_network_nic_create) | 仮想ネットワーク インターフェイスを作成し、IP 転送を有効にします。 |
| [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) | ネットワーク セキュリティ グループ (NSG) を作成します。 |
| [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) | VM に対する着信 HTTP ポートと着信 HTTPS ポートを許可する NSG ルールを作成します。 |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)| NSG とルート テーブルをサブネットに関連付けます。 |
| [az network route-table create](/cli/azure/network/route-table#az-network-route-table-create)| すべてのルートのルート テーブルを作成します。 |
| [az network route-table route create](/cli/azure/network/route-table/route#az-network-route-table-route-create)| サブネットや VM 経由のインターネットでトラフィックをルーティングするルートを作成します。 |
| [az vm create](/cli/azure/vm#az_vm_create) | 仮想マシンを作成し、NIC をアタッチします。 このコマンドでは、使用する仮想マシン イメージと管理者の資格情報も指定します。 |
| [az group delete](/cli/azure/group#az_group_delete) | リソース グループとそれに含まれるすべてのリソースを削除します。 |

## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他のネットワーク CLI のサンプル スクリプトは、[Azure のネットワーク概要のドキュメント](../cli-samples.md)に関するページで確認できます。