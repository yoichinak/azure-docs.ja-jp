---
title: チュートリアル - Azure での SQL&#47;IIS&#47;.NET スタックを実行する VM の作成 | Microsoft Docs
description: このチュートリアルでは、Azure で Windows 仮想マシンに Azure SQL/IIS/.NET スタックをインストールする方法について説明します。
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: f9fea933dd664955a0bc6f47db775fbc469fd684
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38719403"
---
# <a name="tutorial-install-the-sql47iis47net-stack-in-a-windows-vm-with-azure-powershell"></a>チュートリアル: Azure PowerShell を使用して Windows VM に SQL&#47;IIS&#47;.NET スタックをインストールする

このチュートリアルでは、Azure PowerShell を使用して SQL&#47;IIS&#47;.NET スタックをインストールします。 このスタックは、1 つは IIS および .NET を、もう 1 つは SQL Server を使用して Windows Server 2016 を実行する 2 つの VM で構成されています。

> [!div class="checklist"]
> * VM の作成 
> * VM に IIS と .NET Core SDK をインストールする
> * SQL Server を実行する VM を作成する
> * SQL Server 拡張機能をインストールする

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

PowerShell をローカルにインストールして使用する場合、このチュートリアルでは AzureRM.Compute モジュール バージョン 5.7.0 以降が必要です。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM.Compute` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。

## <a name="create-a-iis-vm"></a>IIS の仮想マシンを作成する 

この例では、PowerShell Cloud Shell の [New-AzureRMVM](/powershell/module/azurerm.compute/new-azurermvm) コマンドレットを使用して、Windows Server 2016 仮想マシンを作成し、IIS と .NET Framework をインストールします。 IIS と Azure SQL VM はリソース グループと仮想ネットワークを共有するため、それらの名前となる変数を作成します。


```azurepowershell-interactive
$vmName = "IISVM"
$vNetName = "myIISSQLvNet"
$resourceGroup = "myIISSQLGroup"
New-AzureRmVm `
    -ResourceGroupName $resourceGroup `
    -Name $vmName `
    -Location "East US" `
    -VirtualNetworkName $vNetName `
    -SubnetName "myIISSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -AddressPrefix 192.168.0.0/16 `
    -PublicIpAddressName "myIISPublicIpAddress" `
    -OpenPorts 80,3389 
```

カスタム スクリプト拡張機能を使用して、IIS および .NET framework をインストールします。

```azurepowershell-interactive
Set-AzureRmVMExtension `
    -ResourceGroupName $resourceGroup `
    -ExtensionName IIS `
    -VMName $vmName `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features"}' `
    -Location EastUS
```

## <a name="create-another-subnet"></a>別のサブネットの作成

SQL VM に使用する 2 つ目のサブネットを作成します。 [Get-AzureRmVirtualNetwork]{/powershell/module/azurerm.network/get-azurermvirtualnetwork} を使用して vNet を取得します。

```azurepowershell-interactive
$vNet = Get-AzureRmVirtualNetwork `
   -Name $vNetName `
   -ResourceGroupName $resourceGroup
```

[Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig) を使用してサブネットの構成を作成します。


```azurepowershell-interactive
Add-AzureRmVirtualNetworkSubnetConfig `
   -AddressPrefix 192.168.0.0/24 `
   -Name mySQLSubnet `
   -VirtualNetwork $vNet `
   -ServiceEndpoint Microsoft.Sql
```

[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) を使用し、新しいサブネット情報で vNet を更新します。
   
```azurepowershell-interactive   
$vNet | Set-AzureRmVirtualNetwork
```

## <a name="azure-sql-vm"></a>Azure SQL 仮想マシン

SQL Server の構成済みの Azure Marketplace イメージを使用して Azure SQL VM を作成します。 まず VM を作成し、VM に SQL Server 拡張機能をインストールします。 


```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName $resourceGroup `
    -Name "mySQLVM" `
    -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
    -Location eastus `
    -VirtualNetworkName $vNetName `
    -SubnetName "mySQLSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "mySQLPublicIpAddress" `
    -OpenPorts 3389,1401 
```

[Set-AzureRmVMSqlServerExtension](/powershell/module/azurerm.compute/set-azurermvmsqlserverextension) を使用して [ SQL Server 拡張機能](/sql/virtual-machines-windows-sql-server-agent-extension.md) を Azure SQL 仮想マシンに追加します。

```azurepowershell-interactive
Set-AzureRmVMSqlServerExtension `
   -ResourceGroupName $resourceGroup  `
   -VMName mySQLVM `
   -Name "SQLExtension" `
   -Location "EastUS"
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure PowerShell を使用して Azure SQL、IIS、および .NET スタックをインストールしました。 以下の方法について学習しました。

> [!div class="checklist"]
> * VM の作成 
> * VM に IIS と .NET Core SDK をインストールする
> * SQL Server を実行する VM を作成する
> * SQL Server 拡張機能をインストールする

SSL 証明書を使用して IIS Web サーバーをセキュリティ保護する方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [SSL 証明書を使用してセキュリティ保護された IIS Web サーバー](tutorial-secure-web-server.md)

