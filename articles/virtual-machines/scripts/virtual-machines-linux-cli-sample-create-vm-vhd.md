---
title: Azure CLI のサンプル スクリプト - VHD を使用した VM の作成 | Microsoft Docs
description: Azure CLI のサンプル スクリプト - 仮想ハード ディスクを使用して VM を作成します。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/09/2017
ms.author: allclark
ms.custom: mvc
ms.openlocfilehash: 271390757b7e79eb29c5a3c14ca9ee1b38b53e29
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2018
ms.locfileid: "41920924"
---
# <a name="create-a-vm-with-a-virtual-hard-disk"></a>仮想ハード ディスクを使用した VM の作成

この例では、VHD を使用して仮想マシンを作成します。
ここではリソース グループ、ストレージ アカウント、コンテナーを作成し、VHD を作成したコンテナーにアップロードすることで VM を作成します。
ssh 公開キーをお使いの公開キーに置き換えることで、VM にアクセスできるようになります。

起動可能な VHD が必要です。 スクリプトは `~/sample.vhd` を探します。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-vhd/create-vm-vhd.sh "Create VM using a VHD")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```azurecli-interactive 
az group delete -n az-cli-vhd
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、仮想マシン、可用性セット、ロード バランサー、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az storage account list](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_list) | ストレージ アカウントの一覧を表示します。 |
| [az storage account check-name](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_check_name) | ストレージ アカウントの名前が有効で、存在していないことを確認します。 |
| [az storage account keys list](https://docs.microsoft.com/cli/azure/storage/account/keys#az_storage_account_keys_list) | ストレージ アカウントのキーの一覧を表示します。 |
| [az storage blob exists](https://docs.microsoft.com/cli/azure/storage/blob#az_storage_blob_exists) | BLOB が存在するかどうかを確認します。 |
| [az storage container create](https://docs.microsoft.com/cli/azure/storage/container#az_storage_container_create) | ストレージ アカウントにコンテナーを作成します。 |
| [az storage blob upload](https://docs.microsoft.com/cli/azure/storage/blob#az_storage_blob_upload) | VHD をアップロードすることで、コンテナーに BLOB を作成します。 |
| [az vm list](https://docs.microsoft.com/cli/azure/vm#az_vm_list) | `--query` チェックと併用して VM 名が使用中かどうかを確認します。 | 
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set#az_vm_availability_set_create) | 仮想マシンを作成します。 |
| [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm#az_vm_list-ip-addresses) | 作成した VM の IP アドレスを取得します。 |

## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。

その他の仮想マシン用の CLI サンプル スクリプトは、[Azure Linux VM のドキュメント](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)のページにあります。
