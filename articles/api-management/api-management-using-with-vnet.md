---
title: Azure API Management で仮想ネットワークを使用する方法
description: Azure API Management で仮想ネットワークへの接続を設定して Web サービスにアクセスする方法について説明します。
services: api-management
documentationcenter: ''
author: vlvinogr
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: apimpm
ms.openlocfilehash: deba3ad8a283b111dc94a5361f3fa4e73d95c0b8
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187385"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Azure API Management で仮想ネットワークを使用する方法
Azure Virtual Network (VNET) を使用すると、任意の Azure リソースをインターネット以外のルーティング可能なネットワークに配置し、アクセスを制御できます。 これらのネットワークは、さまざまな VPN テクノロジを使用して、オンプレミスのネットワークに接続できます。 Azure Virtual Network の詳細については、まず [Azure Virtual Network の概要](../virtual-network/virtual-networks-overview.md)に関するページに記載されている情報をご覧ください。

Azure API Management は、仮想ネットワーク (VNET) の内部でデプロイできるため、ネットワーク内でバックエンド サービスにアクセスできます。 開発者ポータルと API ゲートウェイは、インターネットから、または仮想ネットワーク内でのみアクセスできるように構成可能です。

> [!NOTE]
> Azure API Management では、従来型および Azure Resource Manager の両方の VNet がサポートされています。
>

## <a name="prerequisites"></a>前提条件

この記事で説明されている手順を実行するには、以下が必要です。

+ 有効な Azure サブスクリプション

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ APIM インスタンス。 詳細については、[Azure API Management インスタンスの作成](get-started-create-service-instance.md)に関する記事を参照してください。
+ VNET 接続は、Premium レベルと Developer レベルでのみ利用できます。 「[アップグレードとスケーリング](upgrade-and-scale.md#upgrade-and-scale)」の指示に従って、これらのレベルのどちらかに切り替えてください。

## <a name="enable-vpn"> </a>VNET 接続の有効化

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>Azure ポータルを使用して VNET 接続を有効にする

1. [Azure ポータル](https://portal.azure.com/)で、APIM インスタンスに移動します。
2. **[仮想ネットワーク]** を選択します。
3. 仮想ネットワーク内にデプロイされる API Management インスタンスを構成します。

    ![API Management の [仮想ネットワーク] メニュー][api-management-using-vnet-menu]
4. 目的のアクセスの種類を選択します。

    * **外部**: API Management のゲートウェイと開発者ポータルには、パブリック インターネットから外部ロード バランサーを使用してアクセスできます。 ゲートウェイは、仮想ネットワーク内のリソースにアクセスできます。

    ![パブリック ピアリング][api-management-vnet-public]

    * **内部**: API Management のゲートウェイと開発者ポータルには、仮想ネットワークから内部ロード バランサーを使用してアクセスできます。 ゲートウェイは、仮想ネットワーク内のリソースにアクセスできます。

    ![プライベート ピアリング][api-management-vnet-private]`

    API Management サービスがプロビジョニングされているすべてのリージョンの一覧が表示されます。 すべてのリージョンについて、VNET とサブネットを選択します。 この一覧には、構成しているリージョンで設定された Azure サブスクリプションで使用できる従来型および Resource Manager の仮想ネットワークが含まれます。

    > [!NOTE]
    > 上の図の**サービス エンドポイント**には、ゲートウェイ/プロキシ、Azure Portal、開発者ポータル、GIT、およびダイレクト管理エンドポイントが含まれています。
    > 上の図の**管理エンドポイント**は、Azure Portal と Powershell を使用して構成を管理するためにサービスでホストされるエンドポイントです。
    > さらに、図にはさまざまなエンドポイントの IP アドレスが示されていますが、API Management サービスは、構成済みのホスト名で**のみ**で応答することに注意してください。

    > [!IMPORTANT]
    > Azure API Management インスタンスを Resource Manager VNET にデプロイする場合、サービスは Azure API Management インスタンス以外のリソースを含まない専用サブネットにある必要があります。 他のリソースが含まれる Resource Manager VNET サブネットに Azure API Management インスタンスをデプロイしようとすると、そのデプロイは失敗します。
    >

    ![VPN の選択][api-management-setup-vpn-select]

5. 画面の上部にある **[保存]** をクリックします。

> [!NOTE]
> API Management インスタンスの VIP アドレスは VNET を有効または無効にするたびに変更されます。
> また、VIP アドレスは、API Management が**外部**から**内部**に、またはその逆に移動された場合にも変更されます。
>

> [!IMPORTANT]
> VNET から API Management を削除するか、VNET にデプロイされる API Management を変更した場合、それまで使用されていた VNET が最大 2 時間ロックされる可能性があります。 この期間中は、VNET の削除も、新しいリソースのデプロイも実行できません。

## <a name="enable-vnet-powershell"> </a>PowerShell コマンドレットを使用して VNET 接続を有効にする
PowerShell コマンドレットを使用して VNET 接続を有効にすることもできます。

* **VNET 内に API Management サービスを作成する**: コマンドレット [New-AzureRmApiManagement](/powershell/module/azurerm.apimanagement/new-azurermapimanagement) を使用して、VNET 内に Azure API Management サービスを作成します。

* **VNET 内に既存の API Management サービスをデプロイする**: コマンドレット [Update-AzureRmApiManagementDeployment](/powershell/module/azurerm.apimanagement/update-azurermapimanagementdeployment) を使用して、既存の Azure API Management サービスを Virtual Network 内に移動します。

## <a name="connect-vnet"> </a>仮想ネットワーク内でホストされる Web サービスに接続する
API Management サービスが VNET に接続された後で VNET 内のバックエンド サービスにアクセスする方法は、パブリック サービスにアクセスする方法と同じです。 単に、新しい API を作成するときや既存の API を編集するときに Web サービスのローカル IP アドレスまたはホスト名 (VNET に対して DNS サーバーが構成されている場合) を **[Web サービスの URL]** ボックスに入力するだけです。

![VPN からの API の追加][api-management-setup-vpn-add-api]

## <a name="network-configuration-issues"> </a>ネットワーク構成に関する一般的な問題
API Management サービスを Virtual Network にデプロイするときに発生する可能性のある一般的な誤った構成の一覧を以下に示します。

* **カスタム DNS サーバーのセットアップ**: API Management サービスは、複数の Azure サービスに依存します。 カスタム DNS サーバーを使用して VNET で API Management をホストする場合、その DNS サーバーはこれらの Azure サービスのホスト名を解決する必要があります。 カスタム DNS のセットアップについては、 [こちらの](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) ガイダンスに従ってください。 下にあるポートの表とその他のネットワーク要件を参照してください。

> [!IMPORTANT]
> VNET でカスタム DNS サーバーを使用する予定の場合は、API Management サービスをデプロイする**前**にサーバーをセットアップします。 それ以外の場合、[ネットワーク構成の処理の適用](https://docs.microsoft.com/rest/api/apimanagement/ApiManagementService/ApplyNetworkConfigurationUpdates)を実行して DNS サーバーを変更するたびに API Management サービスを更新する必要があります。

* **API Management に必要なポート**: API Management がデプロイされるサブネットへの受信トラフィックと送信トラフィックは[ネットワーク セキュリティ グループ][Network Security Group]を使用して制御できます。 これらのポートのいずれかが利用できない場合、API Management は正しく動作しない可能性があり、アクセス不能になる場合があります。 VNET で API Management を使用した場合の不正な構成に関するその他の一般的な問題として、これらの 1 つまたは複数のポートがブロックされていることが挙げられます。

API Management サービス インスタンスが VNET でホストされている場合は、次の表のポートが使用されます。

| ソース / ターゲット ポート | 方向 | トランスポート プロトコル | ソース / ターゲット | 目的 ( * ) | 仮想ネットワークの種類 |
| --- | --- | --- | --- | --- | --- |
| * / 80, 443 |受信 |TCP |INTERNET / VIRTUAL_NETWORK|API Management へのクライアント通信|外部 |
| * / 3443 |受信 |TCP |INTERNET / VIRTUAL_NETWORK|Azure Portal と Powershell 用の管理エンドポイント |内部 |
| * / 80, 443 |送信 |TCP |VIRTUAL_NETWORK / INTERNET|**Azure Storage、Azure Service Bus、Azure Active Directory への依存関係** (該当する場合)。|外部 / 内部 |
| * / 1433 |送信 |TCP |VIRTUAL_NETWORK / SQL|**Azure SQL エンドポイントへのアクセス** |外部 / 内部 |
| * / 5672 |送信 |TCP |VIRTUAL_NETWORK / INTERNET|Event Hub へのログ ポリシーおよび監視エージェントの依存関係 |外部 / 内部 |
| * / 445 |送信 |TCP |VIRTUAL_NETWORK / INTERNET|GIT のための Azure ファイル共有への依存関係 |外部 / 内部 |
| * / 1886 |送信 |TCP |VIRTUAL_NETWORK / INTERNET|正常性の状態を Resource Health に公開するために必要 |外部 / 内部 |
| * / 25028 |送信 |TCP |VIRTUAL_NETWORK / INTERNET|電子メールを送信するために SMTP リレーに接続する |外部 / 内部 |
| * / 6381 - 6383 |受信および送信 |TCP |VIRTUAL_NETWORK / VIRTUAL_NETWORK|Role インスタンス間での Redis Cache インスタンスへのアクセス |外部 / 内部 |
| * / * | 受信 |TCP |AZURE_LOAD_BALANCER / VIRTUAL_NETWORK| Azure インフラストラクチャの Load Balancer |外部 / 内部 |

>[!IMPORTANT]
> "*目的*" が**太字**になっているポートは、API Management サービスの正常なデプロイを必要とします。 ただし、その他のポートをブロックすると、実行中のサービスの使用と監視を行う能力が低下します。

* **SSL 機能**: SSL 証明書チェーンの構築と検証を有効にするには、API Management サービスに ocsp.msocsp.com、mscrl.microsoft.com、および crl.microsoft.com に対する送信ネットワーク接続が必要です。 API Management にアップロードする任意の証明書に CA ルートへの完全なチェーンが含まれている場合、この依存関係は必要ありません。

* **DNS アクセス**: DNS サーバーとの通信には、ポート 53 での発信アクセスが必要です。 カスタム DNS サーバーが VPN ゲートウェイの相手側にある場合、DNS サーバーは API Management をホストしているサブネットから到達できる必要があります。

* **メトリックと正常性の監視**: Azure Monitoring エンドポイントへの発信ネットワーク接続、次のドメインで解決されます: 

    | Azure 環境 | エンドポイント |
    | --- | --- |
    | Azure Public | <ul><li>prod.warmpath.msftcloudes.com</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li><li>prod3-black.prod3.metrics.nsatc.net</li><li>prod3-red.prod3.metrics.nsatc.net</li></ul> |
    | Azure Government | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li></ul> |
    | Azure China | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li></ul> |

* **Express Route セットアップ**: 顧客の一般的な構成として、発信インターネット トラフィックを強制的にオンプレミスにフローさせる独自の既定のルート (0.0.0.0/0) を定義します。 このトラフィック フローでは、Azure API Management を使用した接続は必ず切断されます。これは、発信トラフィックがオンプレミスでブロックされるか、さまざまな Azure エンドポイントで有効ではなくなった、認識できないアドレス セットに NAT 処理されることが原因です。 解決策は、Azure API Management を含むサブネット上で 1 つ (以上) のユーザー定義ルート ([UDR][UDRs]) を定義することです。 UDR は、既定のルートに優先するサブネット固有のルートを定義します。
  可能であれば、次の構成を使用することをお勧めします。
 * ExpressRoute 構成は 0.0.0.0/0 をアドバタイズし、既定でオンプレミスのすべての発信トラフィックを強制的にトンネリングします。
 * Azure API Management を含むサブネットに適用される UDR では、次ホップの種類がインターネットである 0.0.0.0/0 を定義します。
 これらの手順を組み合わせた結果として、サブネット レベル UDR は ExpressRoute 強制トンネリングよりも優先されるので、Azure API Management からの発信インターネット アクセスを確保できます。

* **ネットワーク仮想アプライアンス経由のルーティング**: 既定ルート (0.0.0.0/0) の UDR を使って API Management サブネットから Azure 内で実行されているネットワーク仮想アプライアンスを介し、インターネット宛てトラフィックをルーティングする構成では、仮想ネットワーク サブネット内にデプロイされた API Management サービス インスタンスにインターネットから着信する管理トラフィックがブロックされます。 この構成はサポートされていません。

>[!WARNING]
>Azure API Management は、**パブリック ピアリング パスからプライベート ピアリング パスにルートを正しくクロスアドバタイズしていない** ExpressRoute 構成ではサポートされません。 パブリック ピアリングが構成された ExpressRoute 構成は、大規模な Microsoft Azure の IP アドレス範囲について Microsoft からルート アドバタイズを受信します。 これらのアドレス範囲がプライベート ピアリング パスで誤ってクロスアドバタイズされている場合、Azure API Management インスタンスのサブネットからのすべての発信ネットワーク パケットは、誤って顧客のオンプレミス ネットワーク インフラストラクチャに強制的にトンネリングされます。 このネットワーク フローでは、Azure API Management が機能しません。 この問題を解決するには、パブリック ピアリング パスからプライベート ピアリング パスへのルートのクロスアドバタイズを停止します。


## <a name="troubleshooting"> </a>トラブルシューティング
* **初回のセットアップ**: API Management サービスのサブネットへの初回のデプロイが成功しなかった場合は、同じサブネットに仮想マシンを先にデプロイすることをお勧めします。 次に、リモート デスクトップを仮想マシンにデプロイし、Azure サブスクリプション内の次のリソースのいずれかに接続できることを確認します。
    * Azure Storage BLOB
    * Azure SQL Database
    * Azure Storage Table

 > [!IMPORTANT]
 > 接続を確認したら、サブネットにデプロイされているすべてのリソースを必ず削除してから、サブネットに API Management をデプロイしてください。

* **増分更新**: ネットワークを変更するときは、[NetworkStatus API](https://docs.microsoft.com/rest/api/apimanagement/networkstatus) を参照して、API Management サービスが依存している重要なリソースへのアクセスが失われていないことを確認してください。 接続状態は、15 分間隔で更新されます。

* **リソース ナビゲーション リンク**: Resource Manager スタイルの VNET サブネットにデプロイすると、API Management は、リソース ナビゲーション リンクを作成することでサブネットを予約します。 サブネットに別のプロバイダーのリソースが既に含まれている場合、デプロイは**失敗**します。 同様に、API Management サービスを別のサブネットに 移動するか削除すると、そのリソース ナビゲーション リンクが削除されます。

* **Azure portal からの API のテスト**: Azure portal と API Management インスタンスからの API のテストが内部 VNet と統合されている場合は、VNet に構成されている DNS サーバーが名前解決に使用されます。 Azure portal からテストするときに 404 が表示される場合は、VNet の DNS サーバーが、API Management インスタンスのホスト名を正しく解決できることをご確認ください。 

## <a name="subnet-size"></a>サブネットのサイズ要件
Azure は、各サブネット内で一部の IP アドレスを予約し、これらのアドレスを使用することはできません。 サブネットの最初と最後の IP アドレスは、Azure サービスで使用される 3 つ以上のアドレスと共に、プロトコル準拠に予約されます。 詳細については、「 [これらのサブネット内の IP アドレスの使用に関する制限はありますか](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Azure VNET インフラストラクチャによって使用される IP アドレスに加えて、サブネットの各 API Management インスタンスは、Premium SKU のユニットごとに 2 つの IP アドレス、または Developer SKU 用に 1 つの IP アドレスを使用します。 各インスタンスによって、外部ロード バランサー用の IP アドレスが別途予約されています。 内部 VNET に展開する場合は、内部ロード バランサー用に追加の IP アドレスが必要です。

前述の計算によると、API Management で展開できるサブネットの最小サイズは /29 で、3 つの IP アドレスを提供します。

## <a name="routing"> </a> ルーティング
+ 負荷分散されたパブリック IP アドレス (VIP) は、すべてのサービス エンドポイントへのアクセスを提供するために予約されます。
+ サブネット IP 範囲 (DIP) の IP アドレスは VNET 内のリソースにアクセスするために使用され、パブリック IP アドレス (VIP) は VNET の外部のリソースにアクセスするために使用されます。
+ 負荷分散されたパブリック IP アドレスは、Azure Portal の [概要]/[要点] ブレードで確認できます。

## <a name="limitations"> </a>制限事項
* API Management インスタンスが含まれるサブネットには、その他の Azure リソースの種類を含めることはできません。
* サブネットと API Management サービスは、同じサブスクリプション内になければなりません。
* API Management インスタンスが含まれるサブネットは、サブスクリプション間で移動できません。
* 内部仮想ネットワーク モードで構成された複数リージョンの API Management デプロイでは、ルーティングを設定するユーザーが分散負荷の管理を担当します。デプロイでは、ユーザーが、ルーティングを担当するように、複数のリージョンの負荷分散の管理を担当します。
* 別のリージョン内にあるグローバルにピアリングされた VNET 内のリソースから、内部モードの API Management サービスへの接続は、プラットフォームの制限により機能しません。 詳しくは、[ある仮想ネットワーク内のリソースは、ピアリングされた仮想ネットワークの Azure 内部ロード バランサーと通信できない](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)ことに関するページをご覧ください。


## <a name="related-content"> </a>関連コンテンツ
* [VPN Gateway を使用して Virtual Network をバックエンドに接続する](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)
* [異なるデプロイ モデルの Virtual Network を PowerShell を使用して接続する](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Azure API Management で API Inspector を使用して呼び出しをトレースする方法](api-management-howto-api-inspector.md)
* [仮想ネットワークに関する FAQ](../virtual-network/virtual-networks-faq.md)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-type.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-internal.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/security-overview.md
