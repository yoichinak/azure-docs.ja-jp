---
title: チュートリアル - Azure 仮想マシン スケール セットの作成および管理 | Microsoft Docs
description: Azure CLI 2.0 を使用して仮想マシン スケール セットを作成するための方法と一般的な管理タスク (インスタンスの起動と停止、スケール セット容量の変更の方法など) について説明します。
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: a076603519cdce5a16881e0f0703d8187001e058
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38452551"
---
# <a name="tutorial-create-and-manage-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>チュートリアル: Azure CLI 2.0 を使用した仮想マシン スケール セットの作成および管理
仮想マシン スケール セットを使用すると、同一の自動スケールの仮想マシンのセットをデプロイおよび管理できます。 仮想マシン スケール セットのライフサイクルを通して、1 つ以上の管理タスクを実行することが必要になる場合があります。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * 仮想マシン スケール セットを作成して接続する
> * VM イメージを選択して使用する
> * 特定の VM インスタンス サイズを確認して使用する
> * スケール セットを手動でスケーリングする
> * スケール セットの一般的な管理タスクを実行する

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.29 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 


## <a name="create-a-resource-group"></a>リソース グループの作成
Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 仮想マシン スケール セットの前にリソース グループを作成する必要があります。 [az group create](/cli/azure/group#az_group_create) コマンドでリソース グループを作成します。 この例では、*myResourceGroup* という名前のリソース グループが *eastus* リージョンに作成されます。 

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

このチュートリアルでスケール セットを作成または変更するときにこのリソース グループ名を指定します。


## <a name="create-a-scale-set"></a>スケール セットを作成する
仮想マシン スケール セットを作成するには、[az vmss create](/cli/azure/vmss#az_vmss_create) コマンドを使用します。 次の例では、*myScaleSet* という名前のスケール セットを作成し、存在しない場合は SSH キーを生成します。

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

すべてのスケール セット リソースと VM インスタンスを作成して構成するには数分かかります。 個々の VM インスタンスにトラフィックを分散するために、ロード バランサーも作成されます。


## <a name="view-the-vm-instances-in-a-scale-set"></a>スケール セット内の VM インスタンスを表示する
スケール セット内の VM インスタンスの一覧を表示するには、次のように [az vmss list-instances](/cli/azure/vmss#az_vmss_list_instances) を使用します。

```azurecli-interactive
az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```

次の出力例には、スケール セット内の 2 つの VM インスタンスが示されています。

```bash
  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup    VmId
------------  --------------------  ----------  ------------  -------------------  ---------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUP  c059be0c-37a2-497a-b111-41272641533c
           3  True                  eastus      myScaleSet_3  Succeeded            MYRESOURCEGROUP  ec19e7a7-a4cd-4b24-9670-438f4876c1f9
```


出力の最初の列には、*InstanceId* が表示されます。 特定の VM インスタンスに関する追加情報を表示するには、`--instance-id` パラメーターを [az vmss get-instance-view](/cli/azure/vmss#az_vmss_get_instance_view) に追加します。 次の例では、VM インスタンス *1* に関する情報を表示しています。

```azurecli-interactive
az vmss get-instance-view \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --instance-id 1
```


## <a name="list-connection-information"></a>接続情報を一覧表示する
トラフィックを個々の VM インスタンスにルーティングするロード バランサーに、パブリック IP アドレスが割り当てられます。 既定では、リモート接続トラフィックを特定のポート上の各 VM に転送する Azure ロード バランサーにネットワーク アドレス変換 (NAT) 規則が追加されます。 スケール セット内の VM インスタンスに接続するには、割り当てられたパブリック IP アドレスとポート番号へのリモート接続を作成します。

スケール セット内の VM インスタンスに接続するためのアドレスとポートを一覧表示するには、[az vmss list-instance-connection-info](/cli/azure/vmss#az_vmss_list_instance_connection_info) を使用します。

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```

次の出力例には、インスタンス名、ロード バランサーのパブリック IP アドレス、および NAT 規則によってトラフィックが転送される先のポート番号が示されています。

```bash
{
  "instance 1": "13.92.224.66:50001",
  "instance 3": "13.92.224.66:50003"
}
```

最初の VM インスタンスに SSH 接続します。 `-p` パラメーターを使用して、パブリック IP アドレスとポート番号を、前のコマンドで示されたとおりに指定します。

```azurecli-interactive
ssh azureuser@13.92.224.66 -p 50001
```

VM インスタンスにログインすると、必要に応じて手動で構成を変更することができます。 ここでは、通常どおり SSH セッションを閉じます。

```bash
exit
```


## <a name="understand-vm-instance-images"></a>VM インスタンス イメージについて
チュートリアルの開始時にスケール セットを作成したとき、VM インスタンスとして `--image` に *UbuntuLTS* を指定しました。 Azure Marketplace には、VM インスタンスの作成に使用できる多くのイメージが用意されています。 [az vm image list](/cli/azure/vm/image#az_vm_image_list) コマンドを使用して、よく使用されるイメージのリストを表示します。

```azurecli-interactive
az vm image list --output table
```

次の出力例には、Azure で最も一般的な VM イメージが示されています。 *UrnAlias* を使用すると、スケール セットを作成するときにこれらの一般的なイメージの 1 つを指定できます。

```bash
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
```

完全なリストを表示するには、`--all` 引数を追加します。 また、`--publisher` か `–-offer` を使用してリストをフィルタリングすることも可能です。 次の例では、*CentOS* に一致するプランがあるすべてのイメージを表示するように、リストをフィルター処理します。

```azurecli-interactive
az vm image list --offer CentOS --all --output table
```

次の縮約された出力は、使用可能な CentOS 7.3 イメージの一部を示しています。

```azurecli-interactive 
Offer    Publisher   Sku   Urn                                 Version
-------  ----------  ----  ----------------------------------  -------------
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20161221   7.3.20161221
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170421   7.3.20170421
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170517   7.3.20170517
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170612   7.3.20170612
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170707   7.3.20170707
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170925   7.3.20170925
```

特定のイメージを使用するスケール セットをデプロイするには、*Urn* 列の値を使用します。 イメージを指定するときにイメージのバージョン番号を *latest* で置き換えることもできます。このようにすると、ディストリビューションの最新バージョンが選択されます。 次の例では、`--image` 引数を使用して、CentOS 7.3 イメージの最新バージョンを指定しています。

すべてのスケール セット リソースと VM インスタンスを作成して構成するには数分かかるため、次のスケール セットをデプロイする必要はありません。

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSetCentOS \
  --image OpenLogic:CentOS:7.3:latest \
  --admin-user azureuser \
  --generate-ssh-keys
```


## <a name="understand-vm-instance-sizes"></a>VM インスタンス サイズについて
VM インスタンス サイズ (または *SKU*) により、CPU、GPU、メモリなど、VM インスタンスで利用できるコンピューティング リソースの量が決定されます。 スケール セット内の VM インスタンスのサイズは、予定のワーク ロードに合ったものにする必要があります。

### <a name="vm-instance-sizes"></a>VM インスタンス サイズ
次の表は、ユース ケース別に一般的な VM サイズを分類したものです。

| type                     | 一般的なサイズ           |    [説明]       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [汎用](../virtual-machines/linux/sizes-general.md)         |Dsv3、Dv3、DSv2、Dv2、DS、D、Av2、A0 - 7| CPU とメモリのバランスがとれています。 開発/テスト環境や、小中規模のアプリケーションとデータ ソリューションに最適です。  |
| [コンピューティングの最適化](../virtual-machines/linux/sizes-compute.md)   | Fs、F             | メモリに対する CPU の比が大きくなっています。 トラフィックが中程度のアプリケーション、ネットワーク アプライアンス、バッチ処理に適しています。        |
| [メモリの最適化](../virtual-machines/linux/sizes-memory.md)    | Esv3、Ev3、M、GS、G、DSv2、DS、Dv2、D   | コアに対するメモリの比が大きくなっています。 リレーショナル データベース、中から大規模のキャッシュ、およびインメモリ分析に適しています。                 |
| [ストレージの最適化](../virtual-machines/linux/sizes-storage.md)      | Ls                | 高いディスク スループットと IO。 ビッグ データ、SQL、および NoSQL のデータベースに最適です。                                                         |
| [GPU](../virtual-machines/linux/sizes-gpu.md)          | NV、NC            | 負荷の高いグラフィック処理やビデオ編集に特化した VM です。       |
| [高性能](../virtual-machines/linux/sizes-hpc.md) | H、A8 ～ 11          | オプションで高スループットのネットワーク インターフェイス (RDMA) を備えた、最も強力な CPU VM です。 

### <a name="find-available-vm-instance-sizes"></a>利用可能な VM インスタンス サイズを確認する
特定のリージョンで利用可能な VM インスタンス サイズのリストを確認するには、[az vm list-sizes](/cli/azure/vm#az_vm_list_sizes) コマンドを使用します。

```azurecli-interactive
az vm list-sizes --location eastus --output table
```

出力は次の縮約された例のようになります。ここには、各 VM サイズに割り当てられたリソースが示されています。

```azurecli-interactive
  MaxDataDiskCount    MemoryInMb  Name                      NumberOfCores    OsDiskSizeInMb    ResourceDiskSizeInMb
------------------  ------------  ----------------------  ---------------  ----------------  ----------------------
                 4          3584  Standard_DS1_v2                       1           1047552                    7168
                 8          7168  Standard_DS2_v2                       2           1047552                   14336
[...]
                 1           768  Standard_A0                           1           1047552                   20480
                 2          1792  Standard_A1                           1           1047552                   71680
[...]
                 4          2048  Standard_F1                           1           1047552                   16384
                 8          4096  Standard_F2                           2           1047552                   32768
[...]
                24         57344  Standard_NV6                          6           1047552                   38912
                48        114688  Standard_NV12                        12           1047552                  696320
```

### <a name="create-a-scale-set-with-a-specific-vm-instance-size"></a>特定の VM インスタンス サイズのスケール セットを作成する
チュートリアルの開始時にスケール セットを作成したとき、VM インスタンスに対して既定の VM SKU である *Standard_D1_v2* が提供されました。 [az vm list-sizes](/cli/azure/vm#az_vm_list_sizes) の出力に基づいて、異なる VM インスタンス サイズを指定できます。 次の例では、`--vm-sku` パラメーターに VM インスタンス サイズ *Standard_F1* を指定して、スケール セットを作成します。 すべてのスケール セット リソースと VM インスタンスを作成して構成するには数分かかるため、次のスケール セットをデプロイする必要はありません。

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSetF1Sku \
  --image UbuntuLTS \
  --vm-sku Standard_F1 \
  --admin-user azureuser \
  --generate-ssh-keys
```


## <a name="change-the-capacity-of-a-scale-set"></a>スケール セットの容量を変更する
チュートリアルの開始時にスケール セットを作成したとき、既定で 2 つの VM インスタンスがデプロイされました。 [az vmss create](/cli/azure/vmss#az_vmss_create) と共に `--instance-count` パラメーターを指定して、スケール セットに作成されるインスタンスの数を変更することができます。 既存のスケール セット内の VM インスタンスの数を増減するには、手動でその容量を変更します。 スケール セットにより、必要な数の VM インスタンスが作成または削除され、その後、トラフィックを分散するようにロード バランサーが構成されます。

スケール セット内の VM インスタンスの数を手動で増減させるには、[az vmss scale](/cli/azure/vmss#az_vmss_scale) を使用します。 次の例では、スケール セット内の VM インスタンスの数を *3* に設定します。

```azurecli-interactive
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 3
```

スケール セットの容量を更新するには数分かかります。 スケール セットに存在するインスタンスの数を確認するには、[az vmss show](/cli/azure/vmss#az_vmss_show) と *sku.capacity* に対するクエリを使います。

```azurecli-interactive
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```


## <a name="common-management-tasks"></a>一般的な管理タスク
これで、スケール セットを作成し、接続情報を一覧表示し、VM インスタンスに接続することができます。 VM インスタンスに異なる OS イメージを使用する方法、別の VM サイズを選択する方法、インスタンス数を手動でスケーリングする方法についても学習しました。 日常の管理の一環として、スケール セット内の VM インスタンスの停止、起動、または再起動が必要になる場合があります。

### <a name="stop-and-deallocate-vm-instances-in-a-scale-set"></a>スケール セット内の VM インスタンスを停止および割り当て解除する
スケール セット内の 1 つ以上の VM インスタンスを停止するには、[az vmss stop](/cli/azure/vmss#az_vmss_stop) を使用します。 `--instance-ids` パラメーターには、停止する VM インスタンスを 1 つ以上指定できます。 インスタンス ID を指定しないと、スケール セット内のすべての VM インスタンスが停止されます。 次の例では、インスタンス *1* を停止します。

```azurecli-interactive
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```

停止した VM インスタンスは割り当てられたままであり、引き続きコンピューティングの料金が発生します。 VM インスタンスの割り当てを解除してストレージの料金のみが課金されるようにするには、[az vmss deallocate](/cli/azure/vmss#az_vmss_deallocate) を使用します。 次の例では、インスタンス *1* を停止および割り当て解除します。

```azurecli-interactive
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```

### <a name="start-vm-instances-in-a-scale-set"></a>スケール セット内の VM インスタンスを起動する
スケール セット内の 1 つ以上の VM インスタンスを起動するには、[az vmss start](/cli/azure/vmss#az_vmss_start) を使用します。 `--instance-ids` パラメーターには、起動する VM インスタンスを 1 つ以上指定できます。 インスタンス ID を指定しない場合は、スケール セット内のすべての VM インスタンスが起動されます。 次の例では、インスタンス *1* を起動します。

```azurecli-interactive
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```

### <a name="restart-vm-instances-in-a-scale-set"></a>スケール セット内の VM インスタンスを再起動する
スケール セット内の 1 つ以上の VM インスタンスを再起動するには、[az vmss restart](/cli/azure/vmss#az_vm_restart) を使用します。 `--instance-ids` パラメーターには、再起動する VM インスタンスを 1 つ以上指定できます。 インスタンス ID を指定しない場合は、スケール セット内のすべての VM インスタンスが再起動されます。 次の例では、インスタンス *1* を再起動します。

```azurecli-interactive
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```


## <a name="clean-up-resources"></a>リソースのクリーンアップ
リソース グループを削除すると、グループに含まれているリソース (VM インスタンス、仮想ネットワーク、ディスクなど) もすべて削除されます。 `--no-wait` パラメーターは、操作の完了を待たずにプロンプトに制御を戻します。 `--yes` パラメーターは、追加のプロンプトを表示せずにリソースの削除を確定します。

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>次の手順
このチュートリアルでは、Azure CLI 2.0 を使用していくつかの基本的なスケール セットの作成および管理タスクを実行する方法について学習しました。

> [!div class="checklist"]
> * 仮想マシン スケール セットを作成して接続する
> * VM イメージを選択して使用する
> * 特定の VM サイズを確認して使用する
> * スケール セットを手動でスケーリングする
> * スケール セットの一般的な管理タスクを実行する

次のチュートリアルに進み、スケール セット ディスクについて確認してください。

> [!div class="nextstepaction"]
> [スケール セットでデータ ディスクを使用する](tutorial-use-disks-cli.md)
