---
title: チュートリアル - Azure CLI を使用して Linux VM の作成と管理を行う | Microsoft Docs
description: このチュートリアルでは、Azure CLI 2.0 を使用して、Azure 内に Linux VM を作成して管理する方法について説明します
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/23/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: e7338da637d92520bc5f67de9d3d2bdae3204e35
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37930037"
---
# <a name="tutorial-create-and-manage-linux-vms-with-the-azure-cli-20"></a>チュートリアル - Azure CLI 2.0 を使用して Linux VM の作成と管理を行う

Azure 仮想マシンは、完全に構成可能で柔軟なコンピューティング環境を提供します。 このチュートリアルでは、VM サイズや VM イメージの選択、VM のデプロイなどの Azure 仮想マシンの展開に関する基本事項について説明します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * VM を作成して VM に接続する
> * VM イメージを選択して使用する
> * 特定の VM サイズを確認して使用する
> * VM のサイズを変更する
> * VM の状態を表示して理解する

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.30 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。

## <a name="create-resource-group"></a>リソース グループの作成

[az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) コマンドでリソース グループを作成します。 

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 仮想マシンの前にリソース グループを作成する必要があります。 この例では、*myResourceGroupVM* という名前のリソース グループが *eastus* リージョンに作成されます。 

```azurecli-interactive 
az group create --name myResourceGroupVM --location eastus
```

このチュートリアル全体で示しているように、VM の作成時または変更時にリソース グループを指定します。

## <a name="create-virtual-machine"></a>仮想マシンの作成

仮想マシンを作成するには、[az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create) コマンドを使用します。 

仮想マシンを作成するときに、オペレーティング システム イメージ、ディスクのサイズ、管理者資格情報など、いくつかの選択肢があります。 次の例では、Ubuntu Server を実行する *myVM* という名前の VM を作成します。 VM には、*azureuser* という名前のユーザー アカウントが作成されます。また、既定のキーの場所 (*~/.ssh*) に SSH キーが存在しない場合は、SSH キーが生成されます。

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupVM \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

VM の作成には数分かかることがあります。 VM が作成されると、Azure CLI で VM に関する以下の情報が出力されます。 `publicIpAddress` を記録します。このアドレスは仮想マシンへのアクセスに使用します。 

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM"
}
```

## <a name="connect-to-vm"></a>VM への接続

Azure Cloud Shell またはローカル コンピューターで、SSH を使用して VM に接続できるようになりました。 サンプルの IP アドレスは、前の手順で記録した `publicIpAddress` に置き換えてください。

```bash
ssh azureuser@52.174.34.95
```

VM にログインしたら、アプリケーションをインストールして構成できます。 作業が終了したら、通常どおり SSH セッションを閉じます。

```bash
exit
```

## <a name="understand-vm-images"></a>VM イメージについて

Azure Marketplace には、VM の作成に使用できる多くのイメージが用意されています。 前の手順では、Ubuntu のイメージを使用して仮想マシンを作成しました。 この手順では、Azure CLI を使用して Marketplace で CentOS のイメージを検索し、このイメージを使用して 2 台目の仮想マシンをデプロイします。 

[az vm image list](/cli/azure/vm/image#az_vm_image_list) コマンドを使用して、よく使用されるイメージのリストを表示します。

```azurecli-interactive 
az vm image list --output table
```

このコマンドの出力では、Azure にあるよく使用される VM イメージが返されます。

```bash
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
```

`--all` 引数を追加すると、リスト全体を確認できます。 また、`--publisher` か `–-offer` を使用してリストをフィルタリングすることも可能です。 この例では、*CentOS* に一致するプランがあるすべてのイメージを表示するように、リストをフィルター処理します。 

```azurecli-interactive 
az vm image list --offer CentOS --all --output table
```

出力の一部を次に示します。

```azurecli-interactive 
Offer             Publisher         Sku   Urn                                     Version
----------------  ----------------  ----  --------------------------------------  -----------
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201501         6.5.201501
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201503         6.5.201503
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201506         6.5.201506
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20150904       6.5.20150904
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20160309       6.5.20160309
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20170207       6.5.20170207
```

特定のイメージを使用して VM をデプロイするために、*[Urn]* 列の値をメモに記録します。この値は、イメージを[識別](cli-ps-findimage.md#terminology)するための、発行元、プラン、SKU、およびオプションのバージョン番号で構成されます。 イメージを指定するときにイメージのバージョン数を "latest" で置き換えることもできます。このようにすると、ディストリビューションの最新バージョンが選択されます。 この例では、`--image` 引数を使用して、CentOS 6.5 イメージの最新バージョンを指定します。  

```azurecli-interactive 
az vm create --resource-group myResourceGroupVM --name myVM2 --image OpenLogic:CentOS:6.5:latest --generate-ssh-keys
```

## <a name="understand-vm-sizes"></a>VM のサイズについて

仮想マシンのサイズにより、CPU、GPU、メモリなど、仮想マシンで利用できるコンピューティング リソースの量が決定されます。 仮想マシンのサイズは、予定のワーク ロードに合ったものにする必要があります。 ワークロードが増えた場合は既存の仮想マシンのサイズを変更できます。

### <a name="vm-sizes"></a>VM サイズ

次の表は、ユース ケース別にサイズを分類したものです。  

| type                     | サイズ           |    説明       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [汎用](sizes-general.md)         |Dsv3、Dv3、DSv2、Dv2、DS、D、Av2、A0 - 7| CPU とメモリのバランスがとれています。 開発/テスト環境や、小中規模のアプリケーションとデータ ソリューションに最適です。  |
| [コンピューティングの最適化](sizes-compute.md)   | Fs、F             | メモリに対する CPU の比が大きくなっています。 トラフィックが中程度のアプリケーション、ネットワーク アプライアンス、バッチ処理に適しています。        |
| [メモリの最適化](../virtual-machines-windows-sizes-memory.md)    | Esv3、Ev3、M、GS、G、DSv2、DS、Dv2、D   | コアに対するメモリの比が大きくなっています。 リレーショナル データベース、中から大規模のキャッシュ、およびインメモリ分析に適しています。                 |
| [ストレージの最適化](../virtual-machines-windows-sizes-storage.md)      | Ls                | 高いディスク スループットと IO。 ビッグ データ、SQL、および NoSQL のデータベースに最適です。                                                         |
| [GPU](sizes-gpu.md)          | NV、NC            | 負荷の高いグラフィック処理やビデオ編集に特化した VM です。       |
| [高性能](sizes-hpc.md) | H、A8 ～ 11          | オプションで高スループットのネットワーク インターフェイス (RDMA) を備えた、最も強力な CPU VM です。 


### <a name="find-available-vm-sizes"></a>使用可能な VM サイズを確認する

特定の地域で利用可能な VM サイズのリストを確認するには、[az vm list-sizes](/cli/azure/vm#az_vm_list_sizes) コマンドを使用します。 

```azurecli-interactive 
az vm list-sizes --location eastus --output table
```

出力の一部を次に示します。

```azurecli-interactive 
  MaxDataDiskCount    MemoryInMb  Name                      NumberOfCores    OsDiskSizeInMb    ResourceDiskSizeInMb
------------------  ------------  ----------------------  ---------------  ----------------  ----------------------
                 2          3584  Standard_DS1                          1           1047552                    7168
                 4          7168  Standard_DS2                          2           1047552                   14336
                 8         14336  Standard_DS3                          4           1047552                   28672
                16         28672  Standard_DS4                          8           1047552                   57344
                 4         14336  Standard_DS11                         2           1047552                   28672
                 8         28672  Standard_DS12                         4           1047552                   57344
                16         57344  Standard_DS13                         8           1047552                  114688
                32        114688  Standard_DS14                        16           1047552                  229376
                 1           768  Standard_A0                           1           1047552                   20480
                 2          1792  Standard_A1                           1           1047552                   71680
                 4          3584  Standard_A2                           2           1047552                  138240
                 8          7168  Standard_A3                           4           1047552                  291840
                 4         14336  Standard_A5                           2           1047552                  138240
                16         14336  Standard_A4                           8           1047552                  619520
                 8         28672  Standard_A6                           4           1047552                  291840
                16         57344  Standard_A7                           8           1047552                  619520
```

### <a name="create-vm-with-specific-size"></a>サイズを指定して VM を作成する

上記の VM 作成の例ではサイズを指定しなかったため、サイズは既定のものになっています。 VM のサイズは、作成時に `--size` 引数を付けて [az vm create](/cli/azure/vm#az_vm_create) を使用することで指定できます。 

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroupVM \
    --name myVM3 \
    --image UbuntuLTS \
    --size Standard_F4s \
    --generate-ssh-keys
```

### <a name="resize-a-vm"></a>VM のサイズを変更する

デプロイ後に VM のサイズを変更して、リソースの割り当てを増減できます。 VM の現在のサイズを表示するには、[az vm show](/cli/azure/vm#az_vm_show) を使用します。

```azurecli-interactive
az vm show --resource-group myResourceGroupVM --name myVM --query hardwareProfile.vmSize
```

VM のサイズを変更する前に、現在の Azure クラスターで目的のサイズを利用可能であるか確認します。 [az vm list-vm-resize-options](/cli/azure/vm#az_vm_list_vm_resize_options) コマンドでは、サイズのリストが返されます。 

```azurecli-interactive 
az vm list-vm-resize-options --resource-group myResourceGroupVM --name myVM --query [].name
```
目的のサイズが使用可能な場合は電源を入れた状態で VM のサイズを変更できます。ただし、この操作中に再起動が行われます。 [az vm resize]( /cli/azure/vm#az_vm_resize) コマンドを使用してサイズ変更を実行します。

```azurecli-interactive 
az vm resize --resource-group myResourceGroupVM --name myVM --size Standard_DS4_v2
```

目的のサイズが現在のクラスターにない場合、サイズ変更を行うには VM の割り当てを解除する必要があります。 [az vm deallocate]( /cli/azure/vm#az_vm_deallocate) コマンドを使用して、VM を停止し割り当てを解除します。 VM の電源を入れ直すと、一時ディスクのデータがすべて削除される可能性があることに注意してください。 また、静的な IP アドレスを使用している場合を除き、パブリック IP アドレスが変更されます。 

```azurecli-interactive 
az vm deallocate --resource-group myResourceGroupVM --name myVM
```

割り当ての解除後、サイズ変更を行うことができます。 

```azurecli-interactive 
az vm resize --resource-group myResourceGroupVM --name myVM --size Standard_GS1
```

サイズの変更後、VM を起動できます。

```azurecli-interactive 
az vm start --resource-group myResourceGroupVM --name myVM
```

## <a name="vm-power-states"></a>VM の電源の状態

Azure VM は、さまざまな電源状態のいずれかになります。 この状態は、ハイパーバイザーから見た VM の現在の状態を表しています。 

### <a name="power-states"></a>電源の状態

| 電源の状態 | 説明
|----|----|
| 開始中 | 仮想マシンが起動中であることを示します。 |
| 実行中 | 仮想マシンが実行中であることを示します。 |
| 停止中 | 仮想マシンが停止中であることを示します。 | 
| 停止済み | 仮想マシンが停止されていることを示します。 仮想マシンが停止済みの状態でも、コンピューティング料金は発生します。  |
| 割り当て解除中 | 仮想マシンの割り当てが解除中であることを示します。 |
| 割り当て解除済み | 仮想マシンがハイパーバイザーから削除されているものの、コントロール プレーンでは使用可能であることを示します。 仮想マシンが割り当て解除済みの状態でも、コンピューティング料金は発生します。 |
| - | 仮想マシンの電源状態が不明であることを示します。 |

### <a name="find-power-state"></a>電源の状態を確認する

特定の VM の状態を取得するには、[az vm get-instance-view](/cli/azure/vm#az_vm_get_instance_view) コマンドを使用します。 必ず仮想マシンとリソース グループの有効な名前を指定してください。 

```azurecli-interactive 
az vm get-instance-view \
    --name myVM \
    --resource-group myResourceGroupVM \
    --query instanceView.statuses[1] --output table
```

出力:

```azurecli-interactive 
ode                DisplayStatus    Level
------------------  ---------------  -------
PowerState/running  VM running       Info
```

## <a name="management-tasks"></a>管理タスク

仮想マシンのライフサイクルでは、各種の管理タスクを実行する必要がある場合があります (仮想マシンの起動、停止、削除など)。 また、何度も行う作業や複雑な作業は、スクリプトを作成して自動化したい場合もあるでしょう。 日常的な管理タスクの多くは、Azure CLI を使ってコマンド ラインやスクリプトから実行できます。 

### <a name="get-ip-address"></a>IP アドレスの取得

仮想マシンのプライベート IP アドレスとパブリック IP アドレスを取得するには、次のコマンドを使用します。  

```azurecli-interactive 
az vm list-ip-addresses --resource-group myResourceGroupVM --name myVM --output table
```

### <a name="stop-virtual-machine"></a>仮想マシンの停止

```azurecli-interactive 
az vm stop --resource-group myResourceGroupVM --name myVM
```

### <a name="start-virtual-machine"></a>仮想マシンの起動

```azurecli-interactive 
az vm start --resource-group myResourceGroupVM --name myVM
```

### <a name="delete-resource-group"></a>Delete resource group

リソース グループを削除すると、グループに含まれているリソース (VM、仮想ネットワーク、ディスクなど) もすべて削除されます。 `--no-wait` パラメーターは、操作の完了を待たずにプロンプトに制御を戻します。 `--yes` パラメーターは、追加のプロンプトを表示せずにリソースの削除を確定します。

```azurecli-interactive 
az group delete --name myResourceGroupVM --no-wait --yes
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次のような基本的な VM の作成と管理を実行する方法について説明しました。

> [!div class="checklist"]
> * VM を作成して VM に接続する
> * VM イメージを選択して使用する
> * 特定の VM サイズを確認して使用する
> * VM のサイズを変更する
> * VM の状態を表示して理解する

次のチュートリアルに進み、VM ディスクについて確認してください。  

> [!div class="nextstepaction"]
> [VM ディスクの作成と管理](./tutorial-manage-disks.md)
