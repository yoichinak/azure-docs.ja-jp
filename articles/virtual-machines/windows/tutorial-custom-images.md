---
title: チュートリアル - Azure PowerShell を使用してカスタム VM イメージを作成する | Microsoft Docs
description: このチュートリアルでは、Azure PowerShell を使って Azure にカスタム仮想マシン イメージを作成する方法について説明します
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 930f5dc0369c998039b9cc0c089f60de17993be0
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37435161"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-azure-powershell"></a>チュートリアル: Azure PowerShell を使用して Azure VM のカスタム イメージを作成する

カスタム イメージは Marketplace のイメージに似ていますが、カスタム イメージは自分で作成します。 カスタム イメージは、アプリケーションのプリロード、アプリケーションの構成、その他の OS 構成などの構成のブートストラップを実行するために使用できます。 このチュートリアルでは、Azure 仮想マシンの独自のカスタム イメージを作成します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * VM を sysprep して汎用化する
> * カスタム イメージを作成する
> * カスタム イメージから VM を作成する
> * サブスクリプション内のすべてのイメージを一覧表示する
> * イメージを削除する

## <a name="before-you-begin"></a>開始する前に

以下の手順では、既存の VM を取得し、新しい VM インスタンスの作成に使用できる再利用可能なカスタム イメージに変換する方法について詳しく説明します。

このチュートリアルの例を完了するには、既存の仮想マシンが必要です。 必要に応じて、この[サンプル スクリプト](../scripts/virtual-machines-windows-powershell-sample-create-vm.md)で仮想マシンを作成できます。 このチュートリアルを実行するときは、リソース グループと VM の名前を適宜置き換えてください。

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは AzureRM モジュール バージョン 5.7.0 以降が必要になります。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。

## <a name="prepare-vm"></a>VM の準備

仮想マシンのイメージを作成するには、VM を一般化して割り当てを解除し、ソース VM を Azure で一般化済みとマークすることで、VM を準備する必要があります。

### <a name="generalize-the-windows-vm-using-sysprep"></a>Sysprep を使用して Windows VM を一般化する

特に重要な点は、Sysprep がすべての個人アカウント情報を削除して、マシンをイメージとして使用できるように準備することです。 Sysprep の詳細については、「 [Sysprep の使用方法: 紹介](http://technet.microsoft.com/library/bb457073.aspx)」を参照してください。


1. 仮想マシンへの接続
2. 管理者としてコマンド プロンプト ウィンドウを開きます。 ディレクトリを *%windir%\system32\sysprep* に変更し、*sysprep.exe* を実行します。
3. **[システム準備ツール]** ダイアログ ボックスで *[システムの OOBE (Out-of-Box Experience) に入る]* を選択し、*[一般化する]* チェック ボックスがオンになっていることを確認します。
4. **[シャットダウン オプション]** の *[シャットダウン]* を選択し、**[OK]** をクリックします。
5. Sysprep は完了時に仮想マシンをシャットダウンします。 **VM は再起動しないでください**。

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>VM の割り当てを解除して VM を汎用としてマークする

イメージを作成するには、VM の割り当てを解除し、Azure で VM を一般化済みとしてマークする必要があります。

[Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) を使用して VM の割り当てを解除します。

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Force
```

[Set-AzureRmVm](/powershell/module/azurerm.compute/set-azurermvm) を使用して、仮想マシンの状態を `-Generalized` に設定します。 
   
```azurepowershell-interactive
Set-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Generalized
```


## <a name="create-the-image"></a>イメージの作成

[New-AzureRmImageConfig](/powershell/module/azurerm.compute/new-azurermimageconfig) と [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage) を使用して、VM のイメージを作成できるようになりました。 次の例では、*myVM* という名前の VM から *myImage* という名前のイメージを作成します。

仮想マシンを取得します。 

```azurepowershell-interactive
$vm = Get-AzureRmVM -Name myVM -ResourceGroupName myResourceGroup
```

イメージの構成を作成します。

```azurepowershell-interactive
$image = New-AzureRmImageConfig -Location EastUS -SourceVirtualMachineId $vm.ID 
```

イメージを作成します。

```azurepowershell-interactive
New-AzureRmImage -Image $image -ImageName myImage -ResourceGroupName myResourceGroup
``` 

 
## <a name="create-vms-from-the-image"></a>イメージからの VM の作成

イメージが用意できたので、イメージから新しい VM を 1 つ以上作成できます。 カスタム イメージからの VM の作成は、Marketplace イメージを使用した VM の作成に似ています。 Marketplace イメージを使うときは、イメージ、イメージ プロバイダー、プラン、SKU、バージョンに関する情報を提供する必要があります。 リソース グループが同じであれば、[New-AzureRMVM]() コマンドレットに設定されている簡易パラメーターを利用し、カスタム イメージの名前を指定する必要があります。 

この例では、*myResourceGroup* で *myImage* から *myVMfromImage* という名前の VM が作成されます。


```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVMfromImage" `
    -ImageName "myImage" `
    -Location "East US" `
    -VirtualNetworkName "myImageVnet" `
    -SubnetName "myImageSubnet" `
    -SecurityGroupName "myImageNSG" `
    -PublicIpAddressName "myImagePIP" `
    -OpenPorts 3389
```

## <a name="image-management"></a>イメージの管理 

ここでは、一般的なマネージド イメージ タスクと PowerShell を使用してそれらを完了する方法の例をいくつか示します。

すべてのイメージの名前を一覧表示します。

```azurepowershell-interactive
$images = Get-AzureRMResource -ResourceType Microsoft.Compute/images 
$images.name
```

イメージを削除します。 この例では、*myOldImage* という名前のイメージを *myResourceGroup* から削除します。

```azurepowershell-interactive
Remove-AzureRmImage `
    -ImageName myOldImage `
    -ResourceGroupName myResourceGroup
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、カスタム VM イメージを作成しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * VM を sysprep して汎用化する
> * カスタム イメージを作成する
> * カスタム イメージから VM を作成する
> * サブスクリプション内のすべてのイメージを一覧表示する
> * イメージを削除する

次のチュートリアルに進み、仮想マシンの高可用性について学習してください。

> [!div class="nextstepaction"]
> [高可用性 VM の作成](tutorial-availability-sets.md)



