---
title: 汎用化したオンプレミスの VHD から管理対象の Azure VM を作成する | Microsoft Docs
description: 汎用化した VHD を Azure にアップロードし、それを使用して Resource Manager デプロイ モデルで新しい VM を作成します。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/26/2018
ms.author: cynthn
ms.openlocfilehash: 8fd88a0e3c5b387ce3ea586f6f23b3643a03e58d
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39618169"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>汎用化した VHD をアップロードして Azure で新しい VM を作成する

このトピックでは、汎用化した VM の VHD を PowerShell を使用して Azure にアップロードし、VHD からイメージを作成し、イメージから新しい VM を作成する方法を説明します。 オンプレミスの仮想化ツールや別のクラウドからエクスポートされた VHD をアップロードできます。 新しい VM に [Managed Disks](managed-disks-overview.md) を使用すると VM 管理が簡略化されます。また、VM を可用性セット内に配置したときの可用性が向上します。 

サンプル スクリプトを使用する場合は、[VHD を Azure にアップロードして新しい VM を作成するサンプル スクリプト](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md)を参照してください。

## <a name="before-you-begin"></a>開始する前に

- VHD を Azure にアップロードする前に、「[Windows VHD の Azure へのアップロードの準備](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」に従う必要があります
- 
  [Managed Disks](managed-disks-overview.md) への移行を開始する前に、「[Plan for the migration to Managed Disks (Managed Disks への移行の計画)](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks)」をご確認ください。
- この記事では、AzureRM モジュール バージョン 5.6 以降が必要です。 バージョンを確認するには、` Get-Module -ListAvailable AzureRM.Compute` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。


## <a name="generalize-the-source-vm-using-sysprep"></a>Sysprep を使用してソース VM を一般化する

特に重要な点は、Sysprep がすべての個人アカウント情報を削除して、マシンをイメージとして使用できるように準備することです。 Sysprep の詳細については、[Sysprep の概要](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)に関するページを参照してください。

コンピューター上で実行されるサーバー ロールが Sysprep でサポートされていることを確認します。 詳しくは、「 [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> 初めて VHD を Azure にアップロードする前に Sysprep を実行する場合、Sysprep の実行前に [VM の準備](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)ができていることを確認してください。 
> 
> 

1. Windows 仮想マシンへのサインイン
2. 管理者としてコマンド プロンプト ウィンドウを開きます。 ディレクトリを **%windir%\system32\sysprep** に変更し、`sysprep.exe` を実行します。
3. **[システム準備ツール]** ダイアログ ボックスで **[システムの OOBE (Out-of-Box Experience) に入る]** を選択し、**[一般化する]** チェック ボックスがオンになっていることを確認します。
4. **[シャットダウン オプション]** の **[シャットダウン]** を選択します。
5. Click **OK**.
   
    ![Sysprep の開始](./media/upload-generalized-managed/sysprepgeneral.png)
6. Sysprep は完了時に仮想マシンをシャットダウンします。 VM は再起動しないでください。


## <a name="get-the-storage-account"></a>ストレージ アカウントを取得する

アップロードした VM イメージを格納するには、Azure にストレージ アカウントが必要です。 既存のストレージ アカウントを選択することも、新しいストレージ アカウントを作成することもできます。 

VHD を使用して VM のマネージド ディスクを作成する場合は、ストレージ アカウントの場所が VM を作成する場所と同じである必要があります。

使用できるストレージ アカウントを表示するには、次のように入力します。

```azurepowershell
Get-AzureRmStorageAccount | Format-Table
```

## <a name="upload-the-vhd-to-your-storage-account"></a>ストレージ アカウントに VHD をアップロードする

[Add-AzureRmVhd](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvhd) コマンドレットを使用して、ストレージ アカウント内のコンテナーに VHD をアップロードします。 この例は、*myVHD.vhd* ファイルを *"C:\Users\Public\Documents\Virtual hard disks\"* から *myResourceGroup* リソース グループの *mystorageaccount* というストレージ アカウントにアップロードします。 ファイルは *mycontainer* というコンテナーに配置され、新しいファイル名は *myUploadedVHD.vhd* になります。

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


成功した場合、次のような応答を取得します。

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

このコマンドは、ネットワーク接続や VHD ファイルのサイズによっては、完了に時間がかかることがあります。

### <a name="other-options-for-uploading-a-vhd"></a>VHD をアップロードするためのその他のオプション
 
次のいずれかの方法を使用して、ストレージ アカウントに VHD をアップロードすることもできます。

- [AzCopy](http://aka.ms/downloadazcopy)
- [Azure Storage Copy Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
- [Azure ストレージ エクスプローラーでの BLOB のアップロード](https://azurestorageexplorer.codeplex.com/)
- [Storage Import/Export Service REST API リファレンス](https://msdn.microsoft.com/library/dn529096.aspx)
-   推定アップロード時間が 7 日より長い場合は、Import/Export サービスを使うことをお勧めします。 [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) を使うと、データ サイズと転送単位から時間を推定できます。 
    Import/Export は、Standard Storage アカウントへのコピーに使うことができます。 Standard Storage アカウントから Premium Storage アカウントにコピーするには、AzCopy などのツールを使用する必要があります。

> [!IMPORTANT]
> VHD を Azure にアップロードする際に AzCopy を使用する場合は、アップロード スクリプトを実行する前に、[/BlobType:page](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#blobtypeblock--page--append) を設定してください。 宛先が BLOB で、このオプションが指定されていない場合、既定では AzCopy によってブロック BLOB が作成されます。
> 
> 



## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>アップロードした VHD から管理イメージを作成する 

一般化済みの OS VHD を使って管理イメージを作成します。 値は実際の値に変更してください。


最初に、いくつかのパラメーターを設定します。

```powershell
$location = "East US" 
$imageName = "myImage"
```

一般化した OS VHD を使ってイメージを作成します。

```powershell
$imageConfig = New-AzureRmImageConfig `
   -Location $location
$imageConfig = Set-AzureRmImageOsDisk `
   -Image $imageConfig `
   -OsType Windows `
   -OsState Generalized `
   -BlobUri $urlOfUploadedImageVhd `
   -DiskSizeGB 20
New-AzureRmImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```


## <a name="create-the-vm"></a>VM の作成

イメージが用意できたので、イメージから新しい VM を 1 つ以上作成できます。 この例では、*myResourceGroup* で *myImage* から *myVM* という名前の VM が作成されます。


```powershell
New-AzureRmVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -ImageName $imageName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>次の手順

新しい仮想マシンにサインインします。 詳しくは、「[Windows が実行されている Azure 仮想マシンに接続してログオンする方法](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」をご覧ください。 

