---
title: チュートリアル - Azure 内の Windows VM 上にアプリケーションをインストールする | Microsoft Docs
description: このチュートリアルでは、カスタム スクリプト拡張機能を使ってスクリプトを実行し、Azure 内の Windows 仮想マシンにアプリケーションを展開する方法を説明します
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
ms.date: 02/09/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 2a365321e35c84d5b09570a7eac41bec6cdeb143
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928966"
---
# <a name="tutorial---deploy-applications-to-a-windows-virtual-machine-in-azure-with-the-custom-script-extension"></a>チュートリアル - カスタム スクリプト拡張機能を使って Azure 内の Windows 仮想マシンにアプリケーションを展開する

仮想マシン (VM) を迅速かつ一貫した方法で構成するには、一般的に、何らかの形で自動化することが望ましいです。 Windows VM をカスタマイズする一般的なアプローチとして、[Windows のカスタム スクリプト拡張機能](extensions-customscript.md)を使用する方法があります。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * カスタム スクリプト拡張機能を使用して IIS をインストールする
> * カスタム スクリプト拡張機能を使用する仮想マシンを作成する
> * 拡張機能の適用後に実行中の IIS サイトを表示する

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは Azure PowerShell モジュール バージョン 5.7.0 以降が必要になります。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzureRmAccount` を実行して Azure との接続を作成することも必要です。


## <a name="custom-script-extension-overview"></a>カスタム スクリプト拡張機能の概要
カスタム スクリプト拡張機能は、Azure VM でスクリプトをダウンロードし、実行します。 この拡張機能は、デプロイ後の構成、ソフトウェアのインストール、その他の構成や管理タスクに役立ちます。 スクリプトは、Azure ストレージや GitHub からダウンロードできます。また、拡張機能の実行時に Azure Portal に提供することもできます。

カスタム スクリプト拡張機能は Azure Resource Manager テンプレートと統合されており、Azure CLI、PowerShell、Azure Portal、または Azure 仮想マシン REST API を使用して実行することもできます。

カスタム スクリプト拡張機能は、Windows VM と Linux VM の両方で使用することができます。


## <a name="create-virtual-machine"></a>仮想マシンの作成
まず、[Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) を使用して、VM の管理者のユーザー名とパスワードを設定します。

```azurepowershell-interactive
$cred = Get-Credential
```

[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) を使用して VM を作成できるようになりました。 次の例では、場所 *EastUS* に *myVM* という名前の VM を作成します。 これらが存在しない場合は、リソース グループ *myResourceGroupAutomate* と関連ネットワーク リソースが作成されます。 Web トラフィックを許可するには、このコマンドレットでポート *80* も開きます。

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Credential $cred
```

リソースと VM が作成されるまで数分かかります。


## <a name="automate-iis-install"></a>IIS のインストールを自動化する
[Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) を使用して、カスタム スクリプト拡張機能をインストールします。 この拡張機能によって `powershell Add-WindowsFeature Web-Server` が実行され、IIS Web サーバーがインストールされます。次に、VM のホスト名を表示するように *Default.htm* ページが更新されます。

```azurepowershell-interactive
Set-AzureRmVMExtension -ResourceGroupName "myResourceGroupAutomate" `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location "EastUS" `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```


## <a name="test-web-site"></a>Web サイトをテストする
ロード バランサーのパブリック IP アドレスを取得するには、[Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) を使用します。 次の例では、先ほど作成した *myPublicIPAddress* の IP アドレスを取得します。

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myPublicIPAddress" | select IpAddress
```

このパブリック IP アドレスを Web ブラウザーに入力できます。 次の例のように、Web サイトが表示され、ロード バランサーによって負荷分散されたトラフィックの宛先となった VM のホスト名が表示されます。

![実行中の IIS Web サイト](./media/tutorial-automate-vm-deployment/running-iis-website.png)


## <a name="next-steps"></a>次の手順

このチュートリアルでは、仮想マシンへの IIS のインストールを自動化しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * カスタム スクリプト拡張機能を使用して IIS をインストールする
> * カスタム スクリプト拡張機能を使用する仮想マシンを作成する
> * 拡張機能の適用後に実行中の IIS サイトを表示する

次のチュートリアルに進み、カスタムの VM イメージを作成する方法を学習してください。

> [!div class="nextstepaction"]
> [カスタム VM イメージを作成する](./tutorial-custom-images.md)
