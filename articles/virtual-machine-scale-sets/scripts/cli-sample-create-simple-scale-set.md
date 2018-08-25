---
title: Azure CLI 2.0 のサンプル - 仮想マシン スケール セットを作成する | Microsoft Docs
description: Azure CLI 2.0 のサンプル
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 8946a55eb2307957f95372aae93d7f9788b8f952
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38696657"
---
# <a name="create-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Azure CLI 2.0 を使用して仮想マシン スケール セットを作成する
このスクリプトでは、Ubuntu オペレーティング システムおよびロード バランサーなどの関連ネットワーク リソースを持つ Azure 仮想マシン スケール セットを作成します。 スクリプトを実行すると、SSH で VM インスタンスにアクセスできるようになります。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/simple-scale-set/simple-scale-set.sh "Create a simple virtual machine scale set")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ
次のコマンドを実行して、リソース グループ、スケール セット、すべての関連リソースを削除します。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明
このスクリプトでは、次のコマンドを使用して、リソース グループ、仮想マシン スケール セット、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](/cli/azure/ad/group#az_ad_group_create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az vmss create](/cli/azure/vmss#az_vmss_create) | 仮想マシン スケール セットを作成し、仮想ネットワーク、サブネット、およびネットワーク セキュリティ グループに接続します。 複数の VM インスタンスにトラフィックを分散するために、ロード バランサーも作成されます。 このコマンドでは、使用する VM イメージと管理者の資格情報も指定します。  |
| [az group delete](/cli/azure/ad/group#delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次の手順
Azure CLI 2.0 の詳細については、[Azure CLI 2.0 のドキュメント](https://docs.microsoft.com/cli/azure/overview)を参照してください。

その他の仮想マシン スケール セット用の Azure CLI 2.0 サンプル スクリプトは、[Azure 仮想マシン スケール セットのドキュメント](../cli-samples.md)にあります。