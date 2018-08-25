---
title: 'ExpressRoute 回線への仮想ネットワークのリンク: PowerShell: クラシック: Azure | Microsoft Docs'
description: このドキュメントでは、クラシック デプロイ モデルと PowerShell を使用して ExpressRoute 回線に仮想ネットワーク (VNet) をリンクする方法の概要について説明します。
services: expressroute
documentationcenter: na
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/27/2018
ms.author: cherylmc
ms.openlocfilehash: 10b623947b6e776c4f8f41e8424262d7f2a3e933
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2018
ms.locfileid: "39343377"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-powershell-classic"></a>PowerShell (クラシック) を使用して仮想ネットワークを ExpressRoute 回線に接続する
> [!div class="op_single_selector"]
> * [Azure ポータル](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [ビデオ - Azure Portal](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (クラシック)](expressroute-howto-linkvnet-classic.md)
>

この記事は、PowerShell を使用して Azure ExpressRoute 回線に仮想ネットワーク (Vnet) をリンクする場合に役立ちます。 単一の VNet を最大 4 つの ExpressRoute 回線にリンクできます。 この記事の手順を使用して、接続先となる各 ExpressRoute 回線への新しいリンクを作成します。 ExpressRoute 回線は、同じサブスクリプション、異なるサブスクリプション、または両方の組み合わせにすることができます。 この記事は、クラシック デプロイ モデルを使用して作成された仮想ネットワークに適用されます。

最大 10 個の仮想ネットワークを ExpressRoute 回線に接続できます。 仮想ネットワークはすべて同じ地理的リージョンにある必要があります。 ExpressRoute プレミアム アドオンを有効にした場合、ExpressRoute 回線により多くの 仮想ネットワークをリンクしたり、それ以外の地理的リージョンにある仮想ネットワークをリンクしたりできます。 プレミアム アドオンの詳細については、[FAQ](expressroute-faqs.md) を確認してください。

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Azure のデプロイ モデルについて**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>構成の前提条件

* 構成を開始する前に、[前提条件](expressroute-prerequisites.md)、[ルーティングの要件](expressroute-routing.md)、[ワークフロー](expressroute-workflows.md)を確認します。
* アクティブな ExpressRoute 回線が必要です。
   * 手順に従って、 [ExpressRoute 回線を作成](expressroute-howto-circuit-classic.md) し、接続プロバイダーに回線を有効にしてもらいます。
   * 回線用に Azure プライベート ピアリングが構成されていることを確認してください。 ルーティング手順については、 [ルーティングの構成](expressroute-howto-routing-classic.md) に関する記事を参照してください。
   * Azure プライベート ピアリングが構成されていることを確認します。また、エンド ツー エンド接続を有効にできるように、ネットワークと Microsoft の間の BGP ピアリングを起動しておく必要があります。
   * 仮想ネットワークと仮想ネットワーク ゲートウェイを作成して完全にプロビジョニングする必要があります。 指示に従い、 [ExpressRoute 用の仮想ネットワークを構成](expressroute-howto-vnet-portal-classic.md)します。

### <a name="download-the-latest-powershell-cmdlets"></a>最新の PowerShell コマンドレットをダウンロードする

最新バージョンの Azure Service Management (SM) PowerShell モジュールと ExpressRoute モジュールをインストールします。 次の例を使用する場合は、コマンドレットの新しいバージョンがリリースされると、バージョン番号 (この例では、5.1.1) が変わることに注意してください。

```powershell
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
```

Azure PowerShell に関するより詳細な情報が必要な場合は、[Azure PowerShell コマンドレットの概要](/powershell/azure/overview)に関するページで、お使いのコンピューターを構成して Azure PowerShell モジュールを使用する方法のステップ バイ ステップのガイダンスを参照してください。

### <a name="sign-in"></a>[サインイン]

Azure アカウントにサインインするには、次の例を使用します。

1. 管理者特権で PowerShell コンソールを開き、アカウントに接続します。

  ```powershell
  Connect-AzureRmAccount
  ```
2. アカウントのサブスクリプションを確認します。

  ```powershell
  Get-AzureRmSubscription
  ```
3. 複数のサブスクリプションがある場合は、使用するサブスクリプションを選択します。

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```

4. 次に、次のコマンドレットを使用して、Azure サブスクリプションをクラシック デプロイ モデルの PowerShell に追加します。

  ```powershell
  Add-AzureAccount
  ```

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>同じサブスクリプション内の仮想ネットワークを回線に接続する
次のコマンドレットを使用して、ExpressRoute 回線に仮想ネットワークをリンクすることができます。 コマンドレットを実行する前に、仮想ネットワーク ゲートウェイが作成されており、リンクの準備ができていることを確認してください。

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
Provisioned
```
    
## <a name="remove-a-virtual-network-link-to-a-circuit"></a>仮想ネットワークのリンクを回線から削除する
次のコマンドレットを使用して、仮想ネットワークのリンクを ExpressRoute 回線から削除することができます。 現在のサブスクリプションがその仮想ネットワークで選択されていることを確認します。 

```powershell
Remove-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```
 

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>別のサブスクリプション内の仮想ネットワークを回線に接続する
複数のサブスクリプションで ExpressRoute 回線を共有できます。 下図に、複数のサブスクリプションで ExpressRoute 回線を共有するしくみについて概略を示します。

大規模クラウド内のそれぞれの小規模クラウドは、組織内のさまざまな部門に属するサブスクリプションを表すために使用されています。 組織内の各部門はサービスのデプロイ用に固有のサブスクリプションを使用できますが、1 つの ExpressRoute 回線を共有することで、オンプレミス ネットワークに接続し直すことができます。 1 つの部門 (この例では IT) で ExpressRoute 回線を所有できます。 組織内の他のサブスクリプションも ExpressRoute 回線を使用できます。

> [!NOTE]
> 専用回線の接続と帯域幅の料金は、ExpressRoute 回線の所有者が負担することになります。 すべての仮想ネットワークが同じ帯域幅を共有します。
> 
> 

![サブスクリプション間接続](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>管理
*回線所有者* とは、ExpressRoute 回線が作成されたサブスクリプションの管理者または共同管理者です。 回線所有者は、他のサブスクリプションの管理者または共同管理者 ( *回線ユーザー*と呼ぶ) が所有する専用回線を使用することを承認できます。 組織の ExpressRoute 回線の使用を承認される回線ユーザーは、承認後、サブスクリプションの仮想ネットワークを ExpressRoute 回線にリンクできるようになります。

回線所有者は、承認をいつでも変更し、取り消す権限を持っています。 承認を取り消すと、アクセスが取り消されたサブスクリプションからすべてのリンクが削除されます。

### <a name="circuit-owner-operations"></a>回線所有者の操作

**承認の作成**

回線所有者は、その他のサブスクリプションの管理者が指定された回線を使用することを承認します。 次の例では、回線 (Contoso IT) の管理者は、別のサブスクリプション (Dev-Test) の管理者が最大 2 個の仮想ネットワークを回線にリンクできるようにします。 Contoso IT 管理者がこれを行うとき、Dev-Test Microsoft ID を指定します。 コマンドレットは、指定された Microsoft ID に電子メールを送信しません。 回線所有者が、承認が完了したことをその他のサブスクリプション所有者に明示的に通知する必要があります。

```powershell
New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'
```

  返された結果:

  ```powershell
  Description         : Dev-Test Links
  Limit               : 2
  LinkAuthorizationId : **********************************
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**承認の確認**

回線所有者は、次のコマンドレットを実行し、特定の回線で発行されるすべての承認を確認できます。

```powershell
Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"
```
  返された結果:

  ```powershell
  Description         : EngineeringTeam
  Limit               : 3
  LinkAuthorizationId : ####################################
  MicrosoftIds        : engadmin@contoso.com
  Used                : 1

  Description         : MarketingTeam
  Limit               : 1
  LinkAuthorizationId : @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
  MicrosoftIds        : marketingadmin@contoso.com
  Used                : 0

  Description         : Dev-Test Links
  Limit               : 2
  LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  MicrosoftIds        : salesadmin@contoso.com
  Used                : 2
  ```

**承認の更新**

回線所有者は、次のコマンドレットを使用して承認を変更できます。

```powershell
Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5
```

  返された結果:

  ```powershell
  Description         : Dev-Test Links
  Limit               : 5
  LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**承認の削除**

回線所有者は、次のコマンドレットを実行して、ユーザーに対する承認を取り消したり削除したりすることができます。

```powershell
Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"
```

### <a name="circuit-user-operations"></a>回線ユーザーの操作

**承認の確認**

回線ユーザーは、次のコマンドレットを使用して承認を確認できます。

```powershell
Get-AzureAuthorizedDedicatedCircuit
```

  返された結果:

  ```powershell
  Bandwidth                        : 200
  CircuitName                      : ContosoIT
  Location                         : Washington DC
  MaximumAllowedLinks              : 2
  ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  ServiceProviderName              : equinix
  ServiceProviderProvisioningState : Provisioned
  Status                           : Enabled
  UsedLinks                        : 0
  ```

**リンク承認の適用**

回線ユーザーは次のコマンドレットを実行し、リンク承認を利用できます。

```powershell
New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'
```

  返された結果:

  ```powershell
  State VnetName
  ----- --------
  Provisioned SalesVNET1
  ```

新たにリンクされた仮想ネットワークのサブスクリプションで、このコマンドを実行します。

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```

## <a name="next-steps"></a>次の手順

ExpressRoute の詳細については、「 [ExpressRoute のFAQ](expressroute-faqs.md)」をご覧ください。