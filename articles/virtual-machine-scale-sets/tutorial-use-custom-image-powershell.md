---
title: チュートリアル - Azure PowerShell を使用したスケール セットのカスタム VM イメージの使用 | Microsoft Docs
description: Azure PowerShell を使用して仮想マシン スケール セットをデプロイするためのカスタム VM イメージを作成する方法について説明します
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
ms.openlocfilehash: c4ecb7c43a9a26385d5e6cef023c7219fb1120d3
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38606165"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-azure-powershell"></a>チュートリアル: Azure PowerShell を使用した仮想マシン スケール セットのカスタム イメージの作成および使用
スケール セットを作成するときは、VM インスタンスのデプロイ時に使用するイメージを指定します。 VM インスタンスをデプロイした後のタスクの数を減らすには、カスタム VM イメージを使用できます。 このカスタム VM イメージには、すべての必要なアプリケーション インストールまたは構成が含まれます。 スケール セットで作成されたすべての VM インスタンスは、カスタム VM イメージを使用し、アプリケーション トラフィックを処理できる状態になります。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * VM の作成とカスタマイズ
> * VM のプロビジョニング解除と汎用化
> * ソース VM からのカスタム VM イメージの作成
> * カスタム VM イメージを使用するスケール セットのデプロイ

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは Azure PowerShell モジュール バージョン 6.0.0 以降が必要になります。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzureRmAccount` を実行して Azure との接続を作成することも必要です。 


## <a name="create-and-configure-a-source-vm"></a>ソース VM の作成と構成

>[!NOTE]
> このチュートリアルでは、汎用化された VM イメージを作成および使用する手順について説明します。 特殊化された VM イメージからスケール セットを作成することはできません。

最初に [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) を使用してリソース グループを作成し、次に [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) を使用して VM を作成します。 この VM は、カスタム VM イメージのソースとして使用されます。 次の例では、*myResourceGroup* という名前のリソース グループに *myCustomVM* という名前の VM を作成します。 メッセージが表示されたら、VM のログオン資格情報として使用するユーザー名とパスワードを入力します。

```azurepowershell-interactive
# Create a resource a group
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "EastUS"

# Create a Windows Server 2016 Datacenter VM
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Name "myCustomVM" `
  -ImageName "Win2016Datacenter" `
  -OpenPorts 3389
```

VM に接続するには、次のように [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) を使用してパブリック IP アドレスを一覧表示します。

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

VM とのリモート接続を作成します。 Azure Cloud Shell を使用する場合は、ローカルの PowerShell プロンプトまたはリモート デスクトップ クライアントからこの手順を実行します。 前のコマンドから返された独自の IP アドレスを入力します。 メッセージが表示されたら、最初の手順で VM を作成したときに使用した資格情報を入力します。

```powershell
mstsc /v:<IpAddress>
```

VM をカスタマイズするために、基本的な Web サーバーをインストールしてみましょう。 スケール セット内の VM インスタンスがデプロイされると、Web アプリケーションの実行に必要なパッケージがすべて揃うことになります。 VM 上でローカルの PowerShell プロンプトを開き、次のように [Install-WindowsFeature](/powershell/module/servermanager/install-windowsfeature) を使用して IIS Web サーバーをインストールします。

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

カスタム イメージとして使用する VM を準備するための最後の手順は、VM を汎用化することです。 Sysprep を使用すると、すべての個人アカウント情報と構成を削除して、将来のデプロイのために VM をクリーンな状態にリセットできます。 Sysprep の詳細については、「[How to Use Sysprep: An Introduction (Sysprep の使用方法: 紹介)](http://technet.microsoft.com/library/bb457073.aspx)」を参照してください。

VM を汎用化するには、Sysprep を実行して、すぐに使用できるように VM を設定します。 終了したら、VM をシャットダウンするよう Sysprep に指示します。

```powershell
C:\Windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown
```

Sysprep がプロセスを完了し、VM がシャットダウンされると、VM へのリモート接続は自動的に閉じられます。


## <a name="create-a-custom-vm-image-from-the-source-vm"></a>ソース VM からのカスタム VM イメージの作成
これで IIS Web サーバーがインストールされ、ソース VM がカスタマイズされました。 次に、スケール セットで使用するカスタム VM イメージを作成しましょう。

イメージを作成するには、VM の割り当てを解除する必要があります。 [Stop-AzureRmVm](/powershell/module/azurerm.compute/stop-azurermvm) を使用して VM の割り当てを解除します。 次に、[Set-AzureRmVm](/powershell/module/azurerm.compute/set-azurermvm) を使用して VM の状態を汎用化済みとして設定します。これにより、Azure プラットフォームは、カスタム イメージを使用するための VM の準備が整ったことを認識します。 イメージを作成できるのは、汎用化された VM からのみです。

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myCustomVM" -Force
Set-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myCustomVM" -Generalized
```

VM を割り当て解除して汎用化するには数分かかることがあります。

次に、[New-AzureRmImageConfig](/powershell/module/azurerm.compute/new-azurermimageconfig) と [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage) を使用して VM のイメージを作成します。 次の例では、VM から *myImage* という名前のイメージを作成します。

```azurepowershell-interactive
# Get VM object
$vm = Get-AzureRmVM -Name "myCustomVM" -ResourceGroupName "myResourceGroup"

# Create the VM image configuration based on the source VM
$image = New-AzureRmImageConfig -Location "EastUS" -SourceVirtualMachineId $vm.ID 

# Create the custom VM image
New-AzureRmImage -Image $image -ImageName "myImage" -ResourceGroupName "myResourceGroup"
```


## <a name="create-a-scale-set-from-the-custom-vm-image"></a>カスタム VM イメージからのスケール セットの作成
[New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) を使用して、スケール セットを作成します。このとき、`-ImageName` パラメーターを使用して、前の手順で作成したカスタム VM イメージを定義します。 個々の VM インスタンスにトラフィックを分散するために、ロード バランサーも作成されます。 ロード バランサーには、TCP ポート 80 上のトラフィックを分散するルールだけでなく、TCP ポート 3389 上のリモート デスクトップ トラフィックと TCP ポート 5985 上の PowerShell リモート処理を許可するルールも含まれています。 メッセージが表示されたら、スケール セット内の VM インスタンス用の自分の管理者資格情報を指定します。

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -ImageName "myImage"
```

すべてのスケール セットのリソースと VM を作成および構成するのに数分かかります。


## <a name="test-your-scale-set"></a>スケール セットのテスト
スケール セットが動作していることを確認するには、次のように [Get-AzureRmPublicIpAddress](/powershell/module/AzureRM.Network/Get-AzureRmPublicIpAddress) を使用してロード バランサーのパブリック IP アドレスを取得します。

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -ResourceGroupName "myResourceGroup" `
  -Name "myPublicIPAddress" | Select IpAddress
```

このパブリック IP アドレスを Web ブラウザーに入力します。 次の例に示すように、既定の IIS Web ページが表示されます。

![カスタム VM イメージから実行されている IIS](media/tutorial-use-custom-image-powershell/default-iis-website.png)


## <a name="clean-up-resources"></a>リソースのクリーンアップ
スケール セットと追加のリソースを削除するには、[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) を使用して、リソース グループとそのすべてのリソースを削除します。 `-Force` パラメーターは、追加のプロンプトを表示せずにリソースの削除を確定します。 `-AsJob` パラメーターは、操作の完了を待たずにプロンプトに制御を戻します。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>次の手順
このチュートリアルでは、Azure PowerShell を使用してスケール セットにカスタム VM イメージを作成して使用する方法について学習しました。

> [!div class="checklist"]
> * VM の作成とカスタマイズ
> * VM のプロビジョニング解除と汎用化
> * カスタム VM イメージの作成
> * カスタム VM イメージを使用するスケール セットのデプロイ

次のチュートリアルに進み、アプリケーションをスケール セットにデプロイする方法を学習してください。

> [!div class="nextstepaction"]
> [アプリケーションをスケール セットにデプロイする](tutorial-install-apps-powershell.md)
