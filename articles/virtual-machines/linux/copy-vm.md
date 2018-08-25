---
title: Azure CLI を使用した Linux VM のコピー | Microsoft Docs
description: Azure CLI と Managed Disks を使用して Azure Linux VM のコピーを作成する方法について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 09/25/2017
ms.author: cynthn
ms.openlocfilehash: 8d250f1289c3757d5ea862a1c195dde6f8efb0eb
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938266"
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-and-managed-disks"></a>Azure CLI と Managed Disks を使用して Linux VM のコピーを作成する


この記事では、Azure CLI 2.0 と Azure Resource Manager デプロイ モデルを使用して、Linux を実行する Azure 仮想マシン (VM) のコピーを作成する方法について説明します。 

さらに、[VHD から VM をアップロードして作成する](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)こともできます。

## <a name="prerequisites"></a>前提条件


-   [Azure CLI 2.0](/cli/azure/install-az-cli2) をインストールする。

-   [az login](/cli/azure/reference-index#az_login) で Azure アカウントにサインインする。

-   コピーのソースとして使用する Azure VM がある。

## <a name="step-1-stop-the-source-vm"></a>手順 1: ソース VM を停止する


[az vm deallocate](/cli/azure/vm#az_vm_deallocate) を使用して、ソース VM の割り当てを解除します。
次の例では、**myResourceGroup** というリソース グループ内の **myVM** という VM の割り当てを解除します。

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="step-2-copy-the-source-vm"></a>手順 2: ソース VM をコピーする


VM をコピーするには、基となる仮想ハード ディスクのコピーを作成します。 このプロセスでは、ソース VM と同じ構成および設定が含まれる管理ディスクとして、特殊な VHD を作成します。

Azure Managed Disks の詳細については、「[Azure Managed Disks overview](../windows/managed-disks-overview.md)」 (Azure Managed Disks の概要) をご覧ください。 

1.  [az vm list](/cli/azure/vm#az_vm_list) で、各 VM とその OS ディスクの名前を一覧表示します。 次の例では、**myResourceGroup** という名前のリソース グループに含まれているすべての VM の一覧を表示します。
    
    ```azurecli
    az vm list -g myResourceGroup \
         --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' \
         --output table
    ```

    出力は次の例のようになります。

    ```azurecli
    Name    DiskName
    ------  --------
    myVM    myDisk
    ```

1.  [az disk create](/cli/azure/disk#az_disk_create) を使用して新しい管理ディスクを作成することによって、ディスクをコピーします。 次の例では、**myDisk** という名前の管理ディスクから **myCopiedDisk** という名前のディスクを作成します。

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  [az disk list](/cli/azure/disk#az_disk_list) を使用して、リソース グループ内に管理ディスクがあることを確認します。 次の例では、**myResourceGroup** という名前のリソース グループ内の管理ディスクの一覧を表示します。

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="step-3-set-up-a-virtual-network"></a>手順 3: 仮想ネットワークをセットアップする


省略可能な次の手順では、新しい仮想ネットワーク、サブネット、パブリック IP アドレス、仮想ネットワーク インターフェイス カード (NIC) を作成します。

トラブルシューティングまたは追加デプロイの目的で VM をコピーする場合は、既存の仮想ネットワークで VM を使用しないほうがよいことがあります。

コピーする VM のために仮想ネットワーク インフラストラクチャを作成する場合、次の手順に従ってください。 仮想ネットワークを作成しない場合は、「[手順 4: VM を作成する](#step-4-create-a-vm)」に進んでください。

1.  [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) を使用して、仮想ネットワークを作成します。 次の例では、**myVnet** という名前の仮想ネットワークと **mySubnet** という名前のサブネットを作成します。

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) を使用して、パブリック IP を作成します。 次の例では、DNS 名が **mypublicdns** で **myPublicIP** という名前のパブリック IP を作成します  (DNS 名は一意である必要があるため、一意の名前を入力してください)。

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  [az network nic create](/cli/azure/network/nic#az_network_nic_create) を使用して、NIC を作成します。
    次の例では、**mySubnet** サブネットに接続される NIC **myNic** を作成します。

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="step-4-create-a-vm"></a>手順 4: VM を作成する

これで、[az vm create](/cli/azure/vm#az_vm_create) を使用して VM を作成できます。

次のように、コピーした管理ディスクを OS ディスクとして使用するように指定します (--attach-os-disk)。

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>次の手順

Azure CLI を使用して新しい VM を管理する方法については、[Azure Resource Manager の Azure CLI コマンド](../azure-cli-arm-commands.md)に関する記事を参照してください。
