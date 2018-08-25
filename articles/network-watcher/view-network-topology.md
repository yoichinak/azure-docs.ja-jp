---
title: Azure 仮想ネットワーク トポロジを表示する | Microsoft Docs
description: 仮想ネットワーク内のリソースと、リソース間のリレーションシップを表示する方法について説明します。
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2018
ms.author: jdial
ms.openlocfilehash: 1725a3d6a4eb82ca57078f648efa14866d2fe390
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/04/2018
ms.locfileid: "35777330"
---
# <a name="view-the-topology-of-an-azure-virtual-network"></a>Azure 仮想ネットワークのトポロジを表示する

この記事では、Microsoft Azure 仮想ネットワーク内のリソースと、リソース間のリレーションシップを表示する方法について説明します。 たとえば、仮想ネットワークにはサブネットが含まれています。 サブネットには、Azure Virtual Machines (VM) などのリソースが含まれています。 VM には、1 つまたは複数のネットワーク インターフェイスがあります。 各サブネットは、ネットワーク セキュリティ グループとそれに関連付けられたルート テーブルを持つことができます。 Azure Network Watcher のトポロジ機能を使用すると、仮想ネットワーク内のすべてのリソース、仮想ネットワーク内のリソースに関連するリソース、およびリソース間のリレーションシップを表示できます。

トポロジを表示するには、[Azure Portal](#azure-portal)、[Azure CLI](#azure-cli)、または [PowerShell](#powershell) を使用できます。

## <a name = "azure-portal"></a>トポロジを表示する - Azure Portal

1. 必要な[アクセス許可](required-rbac-permissions.md)を持つアカウントで [Azure Portal](https://portal.azure.com) にログインします。
2. ポータルの左上にある **[すべてのサービス]** を選択します。
3. **[すべてのサービス]** フィルター ボックスに、「*Network Watcher*」と入力します。 結果に **[Network Watcher]** が表示されたら、それを選択します。
4. **[トポロジ]** を選択します。 トポロジを生成するには、トポロジを生成する仮想ネットワークが存在する領域と同じ領域にネットワーク ウォッチャーが必要です。 トポロジを生成する仮想ネットワークがあるリージョンでネットワーク ウォッチャーが有効ではない場合は、すべてのリージョンでネットワーク ウォッチャーが自動的に作成されます。 ネットワーク ウォッチャーは **NetworkWatcherRG** という名前のリソース グループに作成されます。
5. トポロジを表示する仮想ネットワークのリソース グループであるサブスクリプションを選択し、仮想ネットワークを選択します。 次の図では、*MyResourceGroup* という名前のリソース グループにある、*MyVnet* という仮想ネットワークのトポロジが示されています。

    ![トポロジの表示](./media/view-network-topology/view-topology.png)

    前の図のように、この仮想ネットワークには 3 つのサブネットがあります。 1 つのサブネットには VM が配置されています。 この VM には、1 つのネットワーク インターフェイスが接続されており、パブリック IP アドレスが関連付けられています。 その他の 2 つのサブネットには、ルート テーブルが関連付けられています。 各ルート テーブルには 2 つのルートが含まれています。 1 つのサブネットには、ネットワーク セキュリティ グループが関連付けられています。 トポロジの情報は、次のリソースについてのみ表示されます。
    
    - *myVnet* 仮想ネットワークとしての同じリソース グループおよびリージョン内。 たとえば、ネットワーク セキュリティ グループが *MyVnet* 仮想ネットワーク内のサブネットに関連付けられている場合でも、*MyResourceGroup* 以外のリソース グループに存在するネットワーク セキュリティ グループは表示されません。
    - *myVnet* 仮想ネットワーク内、またはそのリソースに関連付けられている。 たとえば、*myVnet* 仮想ネットワーク内のサブネットまたはネットワーク インターフェイスに関連付けられていないネットワーク セキュリティ グループは、ネットワーク セキュリティ グループが *MyResourceGroup* リソース グループ内にあっても表示されません

  図に示すトポロジは、**ネットワーク仮想アプライアンス スクリプト サンプルを介してルート トラフィック**を展開した後に作成される仮想ネットワーク用です。これは [Azure CLI](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) または [PowerShell](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) を使用して展開できます。

6. **[ダウンロード トポロジ]** を選択して、イメージを編集可能なファイル (svg 形式) としてダウンロードします。

図に示すリソースは、仮想ネットワーク内のネットワーク コンポーネントのサブセットです。 たとえば、ネットワーク セキュリティ グループが表示されている間、その中のセキュリティ ルールは図に表示されません。 この図では区別されていませんが、各行は 2 つのリレーションシップ (*包含*または*関連*) のいずれかを示しています。 仮想ネットワーク内のリソースの完全な一覧とリソース間のリレーションシップの種類を表示するには、[PowerShell](#powershell) または [Azure CLI](#azure-cli) を使用してトポロジを生成します。

## <a name = "azure-cli"></a>トポロジを表示する - Azure CLI

次の手順でコマンドを実行できます。
- Azure Cloud Shell では、コマンドの右上にある **[テスト]** を選択します。 Azure Cloud Shell は、無料の対話型シェルで、一般的な Azure ツールがプリインストールされ、お客様のアカウントで使用するよう構成されています。
- コンピューターから CLI を実行します。 コンピューターから CLI を実行する場合は、この記事の手順で Azure CLI バージョン 2.0.31 以降が必要です。 インストールされているバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)」を参照してください。 Azure CLI をローカルで実行している場合、`az login` を実行して Azure との接続を作成することも必要です。

使用するアカウントは、必要な[アクセス許可](required-rbac-permissions.md)を持っている必要があります。

1. トポロジを作成する仮想ネットワークと同じリージョンに既にネットワーク ウォッチャーがある場合は、手順 3 に進みます。 [az group create](/cli/azure/group#az_group_create) を使用して、ネットワーク ウォッチャーを含むリソース グループを作成します。 次の例では、*米国東部*リージョンにリソース グループを作成します。

    ```azurecli-interactive
    az group create --name NetworkWatcherRG --location eastus
    ```

2. [az network watcher configure](/cli/azure/network/watcher#az-network-watcher-configure) を使用してネットワーク ウォッチャーを作成します。 次の例では、*米国東部*リージョンにネットワーク ウォッチャーを作成します。

    ```azurecli-interactive
    az network watcher configure \
      --resource-group NetworkWatcherRG \
      --location eastus \
      --enabled true
    ```

3. [az network watcher show-topology](/cli/azure/network/watcher#az-network-watcher-show-topology) を使用してトポロジを表示します。 次の例は、*MyResourceGroup* という名前のリソース グループのトポロジを示します。

    ```azurecli-interactive
    az network watcher show-topology --resource-group MyResourceGroup
    ```

    トポロジ情報は、*MyResourceGroup* リソース グループと同じリソース グループ内にあり、ネットワーク ウォッチャーと同じリージョン内にあるリソースに対してのみ返されます。 たとえば、ネットワーク セキュリティ グループが *MyVnet* 仮想ネットワーク内のサブネットに関連付けられている場合でも、*MyResourceGroup* 以外のリソース グループに存在するネットワーク セキュリティ グループは表示されません。

  返される出力で、[リレーションシップ](#relationhips)と[プロパティ](#properties)の詳細を確認します。 トポロジを表示する既存の仮想ネットワークがない場合は、[ネットワーク仮想アプライアンス経由のトラフィックのルーティング](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) スクリプト サンプルを使用して仮想ネットワークを作成できます。 トポロジの図を表示し、編集可能なファイル形式でダウンロードするには、[Portal](#azure-portal) を使用します。

## <a name = "powershell"></a>トポロジを表示する - PowerShell

次の手順でコマンドを実行できます。
- Azure Cloud Shell では、コマンドの右上にある **[テスト]** を選択します。 Azure Cloud Shell は、無料の対話型シェルで、一般的な Azure ツールがプリインストールされ、お客様のアカウントで使用するよう構成されています。
- コンピューターから PowerShell を実行します。 コンピューターから PowerShell を実行する場合、この記事の手順では、バージョン 5.7.0 以降の AzureRm モジュールが必要です。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Login-AzureRmAccount` を実行して Azure との接続を作成することも必要です。

使用するアカウントは、必要な[アクセス許可](required-rbac-permissions.md)を持っている必要があります。

1. トポロジを作成する仮想ネットワークと同じリージョンに既にネットワーク ウォッチャーがある場合は、手順 3 に進みます。 [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup) を使用して、ネットワーク ウォッチャーを含むリソース グループを作成します。 次の例では、*米国東部*リージョンにリソース グループを作成します。

    ```azurepowershell-interactive
    New-AzureRmResourceGroup -Name NetworkWatcherRG -Location EastUS
    ```

2. [New-AzureRmNetworkWatcher](/powershell/module/azurerm.network/new-azurermnetworkwatcher) を使用して、ネットワーク ウォッチャーを作成します。 次の例では、米国東部リージョンにネットワーク ウォッチャーを作成します。

    ```azurepowershell-interactive
    New-AzureRmNetworkWatcher `
      -Name NetworkWatcher_eastus `
      -ResourceGroupName NetworkWatcherRG
    ```

3. [Get-AzureRmNetworkWatcher](/powershell/module/azurerm.network/get-azurermnetworkwatcher) を使用して、Network Watcher インスタンスを取得します。 次の例では、米国東部リージョンのネットワーク ウォッチャーを取得します。

    ```azurepowershell-interactive
    $nw = Get-AzurermResource `
      | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "EastUS" }
    $networkWatcher = Get-AzureRmNetworkWatcher `
      -Name $nw.Name `
      -ResourceGroupName $nw.ResourceGroupName
    ```

4. [Get-AzureRmNetworkWatcherTopology](/powershell/module/azurerm.network/get-azurermnetworkwatchertopology) を使用してトポロジを取得します。 次の例では、*MyResourceGroup* という名前のリソース グループ内の仮想ネットワークについて、トポロジを取得します。

    ```azurepowershell-interactive
    Get-AzureRmNetworkWatcherTopology `
      -NetworkWatcher $networkWatcher `
      -TargetResourceGroupName MyResourceGroup
    ```

   トポロジ情報は、*MyResourceGroup* リソース グループと同じリソース グループ内にあり、ネットワーク ウォッチャーと同じリージョン内にあるリソースに対してのみ返されます。 たとえば、ネットワーク セキュリティ グループが *MyVnet* 仮想ネットワーク内のサブネットに関連付けられている場合でも、*MyResourceGroup* 以外のリソース グループに存在するネットワーク セキュリティ グループは表示されません。

  返される出力で、[リレーションシップ](#relationhips)と[プロパティ](#properties)の詳細を確認します。 トポロジを表示する既存の仮想ネットワークがない場合は、[ネットワーク仮想アプライアンス経由のトラフィックのルーティング](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) スクリプト サンプルを使用して仮想ネットワークを作成できます。 トポロジの図を表示し、編集可能なファイル形式でダウンロードするには、[Portal](#azure-portal) を使用します。

## <a name="relationships"></a>リレーションシップ

トポロジで返されるすべてのリソースは、別のリソースに対して次の種類のリレーションシップのいずれかを持ちます。

| リレーションシップの種類 | 例                                                                                                |
| ---               | ---                                                                                                    |
| 包含       | 仮想ネットワークにはサブネットが含まれています。 サブネットにはネットワーク インターフェイスが含まれています。                            |
| 関連付け        | ネットワーク インターフェイスは VM に関連付けられています。 パブリック IP アドレスはネットワーク インターフェイスに関連付けられています。 |

## <a name="properties"></a>Properties

トポロジで返されるすべてのリソースには次のプロパティがあります。

- **Name**: リソースの名前。
- **Id**: リソースの URI。
- **Location**: リソースが存在する Azure リージョン。
- **Associations**: 参照されたオブジェクトへの関連付けのリスト。 各関連付けには、次のプロパティがあります。
    - **AssociationType**: 子オブジェクトと親のリレーションシップを参照します。 有効な値は *Contains* または *Associated*。
    - **Name**: 参照されたリソースの名前。
    - **ResourceId**: 関連付けで参照されているリソースの URI。

## <a name="next-steps"></a>次の手順

- Network Watcher の IP フロー検証機能を使用して [VM との間で生じるネットワーク トラフィック フィルターの問題を診断](diagnose-vm-network-traffic-filtering-problem.md)する方法について学びます
- Network Watcher の次ホップ機能を使用して [VM から生じるネットワーク トラフィック のルーティングの問題を診断](diagnose-vm-network-routing-problem.md)する方法について学びます
