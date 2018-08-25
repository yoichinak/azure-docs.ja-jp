---
title: Azure 仮想ネットワーク サービス エンドポイント | Microsoft Docs
description: サービス エンドポイントを使って仮想ネットワークから Azure リソースへの直接アクセスを有効にする方法を説明します。
services: virtual-network
documentationcenter: na
author: anithaa
manager: narayan
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/06/2018
ms.author: anithaa
ms.custom: ''
ms.openlocfilehash: a8ff6abd392b91ba553a92ac161421ced5a045bb
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2018
ms.locfileid: "39136470"
---
# <a name="virtual-network-service-endpoints"></a>仮想ネットワーク サービス エンドポイント

仮想ネットワーク (VNet) サービス エンドポイントは、直接接続によって仮想ネットワークのプライベート アドレス空間を拡張し、VNet の ID を Azure サービスに提供します。 エンドポイントを使用することで、重要な Azure サービス リソースへのアクセスを仮想ネットワークのみに限定することができます。 VNet から Azure サービスへのトラフィックは常に、Microsoft Azure のバックボーン ネットワーク上に残ります。

この機能は、次の Azure サービスとリージョンで提供されています。

- **[Azure Storage](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)**: 一般公開 (全 Azure リージョン)。
- **[Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)**: 一般公開 (全 Azure リージョン)。
- **[Azure Cosmos DB](../cosmos-db/vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)**: 一般公開 (全 Azure パブリック クラウド リージョン)。 
- **[Azure SQL Data Warehouse](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)**: プレビューで利用可能 (全 Azure パブリック クラウド リージョン)。
- **[Azure Service Bus](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)**: プレビューで利用可能。
- **[Azure Event Hubs](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)**: プレビューで利用可能。
- **[Azure Key Vault](https://blogs.technet.microsoft.com/kv/2018/06/25/announcing-virtual-network-service-endpoints-for-key-vault-preview/)**: プレビューで利用可能。
- **[Azure Database for PostgreSQL サーバー](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)**: プレビューで利用可能 (データベース サービスを利用できる Azure リージョン)。
- **[Azure Database for MySQL サーバー](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)**: プレビューで利用可能 (データベース サービスを利用できる Azure リージョン)。

最新情報については、[Azure 仮想ネットワークの更新情報](https://azure.microsoft.com/updates/?product=virtual-network)ページをご覧ください。

## <a name="key-benefits"></a>主な利点

サービス エンドポイントには次の利点があります。

- **Azure サービス リソースのセキュリティ向上**: サービス エンドポイントを使用すると、Azure サービス リソースへのアクセスをご利用の仮想ネットワークに限定することができます。 サービス リソースを仮想ネットワークに限定することで、リソースへのパブリック インターネット アクセスを完全に排除し、ご利用の仮想ネットワークからのトラフィックのみを許可することにより、セキュリティが向上します。
- **ご利用の仮想ネットワークから Azure サービスへの最適なトラフィック ルーティング**: 現在、お客様の構内および 仮想アプライアンスの両方、またはそのいずれかへのインターネット トラフィックが、ご利用の仮想ネットワーク内のすべてのルートにおいて強制されている場合 (強制トンネリング)、Azure サービス へのトラフィックにもそのインターネット トラフィックと同じルートが強制されます。 サービス エンドポイントを使用することで、Azure トラフィックのルーティングを最適化することができます。 

  エンドポイントは常に、ご利用の仮想ネットワークから Microsoft Azure のバックボーン ネットワーク上のサービスへのサービス トラフィックを直接受け取ります。 Azure のバックボーン ネットワーク上でトラフィックを維持することで、サービス トラフィックに影響を与えることなく、強制トンネリングを通じて、ご利用の仮想ネットワークからの送信インターネット トラフィックの監査と監視を続けることができます。 詳細については、[ユーザー定義のルートと強制トンネリング](virtual-networks-udr-overview.md)に関するページをご覧ください。
- **管理の手間がかからないシンプルな設定**: ご利用の仮想ネットワークで予約済みのパブリック IP アドレスを使用することなく、IP ファイアウォールを経由して Azure リソースにアクセスできるようになりました。 サービス エンドポイントの設定に NAT やゲートウェイ用のデバイスは必要ありません。 サービス エンドポイントは、サブネット上での単純なクリック操作で構成されます。 エンドポイントを維持するために余分なコストはかかりません。

## <a name="limitations"></a>制限事項

- この機能は、Azure Resource Manager デプロイ モデルを使ってデプロイされた仮想ネットワークでのみ使用できます。
- エンドポイントは、Azure 仮想ネットワークに構成されたサブネット上で有効になります。 エンドポイントは、お客様の構内から Azure サービスへのトラフィックに対しては使用できません。 詳細については、「[オンプレミスから Azure サービスへのアクセスの保護](#securing-azure-services-to-virtual-networks)」をご覧ください。
- Azure SQL の場合、サービス エンドポイントは、仮想ネットワークのリージョン内の Azure サービス トラフィックにのみ適用されます。 Azure Storage の場合、RA-GRS および GRS のトラフィックをサポートするには、仮想ネットワークのデプロイ先のリージョンとペアになっているリージョンを含めるようにエンドポイントも拡張します。 [Azure のペアになっているリージョン](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions)の詳細をご覧ください。

## <a name="securing-azure-services-to-virtual-networks"></a>Azure サービスへのアクセスを仮想ネットワークに限定する

- 仮想ネットワーク サービス エンドポイントは、ご利用の仮想ネットワークの ID を Azure サービスに提供します。 ご利用の仮想ネットワークでサービス エンドポイントが有効になったら、Azure サービス リソースに仮想ネットワーク ルールを追加することで、このリソースへのアクセスを仮想ネットワークに限定することができます。
- 現在、仮想ネットワークからの Azure サービス トラフィックは、パブリック IP アドレスを発信元 IP アドレスとして使用します。 サービス エンドポイントを使用すると、サービス トラフィックは、仮想ネットワークから Azure サービスにアクセスするときに、仮想ネットワークのプライベート アドレスを発信元 IP アドレスとして使用するよう切り替えます。 この切り替えにより、IP ファイアウォールで使用される予約済みのパブリック IP アドレスを使用することなく、サービスにアクセスすることができます。
- __オンプレミスから Azure サービスへのアクセスの保護__:

  既定では、仮想ネットワークからのアクセスに限定された Azure サービス リソースは、オンプレミスのネットワークからはアクセスできません。 オンプレミスからのトラフィックを許可する場合は、オンプレミスまたは ExpressRoute からのパブリック IP アドレス (通常は NAT) を許可する必要もあります。 これらの IP アドレスは、Azure サービス リソースの IP ファイアウォール構成を通じて追加できます。

  ExpressRoute: パブリック ピアリングまたは Microsoft ピアリングのためにオンプレミスから [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) を使用している場合、使用されている NAT の IP アドレスを識別する必要があります。 パブリック ピアリングの場合、既定で、Azure サービスのトラフィックが Microsoft Azure のネットワーク バックボーンに入ったときに適用される 2 つの NAT IP アドレスが各 ExpressRoute 回線に使用されます。 Microsoft ピアリングの場合、使用される NAT の IP アドレスは、ユーザーが指定するか、サービス プロバイダーが指定します。 サービス リソースへのアクセスを許可するには、リソースの IP ファイアウォール設定でこれらのパブリック IP アドレスを許可する必要があります。 パブリック ピアリングの ExpressRoute 回線の IP アドレスを確認するには、Azure Portal から [ExpressRoute のサポート チケットを開いて](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)ください。 詳細については、[ExpressRoute のパブリック ピアリングと Microsoft ピアリングの NAT](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering) に関するセクションを参照してください。

![Azure サービスへのアクセスを仮想ネットワークに限定する](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>構成

- サービス エンドポイントは仮想ネットワーク内のサブネット上で構成されます。 エンドポイントは、そのサブネット内で実行されているどの種類のコンピューティング インスタンスでも動作します。
- サブネット上のサポートされているすべての Azure サービス (Azure Storage や Azure SQL Database など) に対して複数のサービス エンドポイントを構成できます。
- Azure SQL Database の場合、仮想ネットワークは、Azure サービス リソースと同じリージョンにある必要があります。 GRS および RA-GRS Azure Storage アカウントを使用している場合、プライマリ アカウントは仮想ネットワークと同じリージョンにある必要があります。 その他のすべてのサービスでは、任意のリージョンで、仮想ネットワークに対する Azure サービス リソースをセキュリティで保護できます。 
- エンドポイントが構成されている仮想ネットワークのサブスクリプションは、Azure サービス リソースのサブスクリプションと同じでも違っていてもかまいません。 エンドポイントを設定して Azure サービスのセキュリティを保護するために必要なアクセス許可の詳細については、[プロビジョニング](#Provisioning)に関するページを参照してください。
- サポートされているサービスについては、サービス エンドポイントを使って新規または既存のリソースへのアクセスを仮想ネットワークに限定することができます。

### <a name="considerations"></a>考慮事項

- サービス エンドポイントを有効にすると、サブネット内の仮想マシンの発信元 IP アドレスは、そのサブネットからサービスへの通信時に、パブリック IPv4 アドレスからプライベート IPv4 アドレスを使用するように切り替わります。 この切り替え中に、サービスに接続中の既存の TCP 接続はすべて閉じられます。 サービスに接続するサブネットのサービス エンドポイントを有効または無効にするときは、重要なタスクが実行されていないことを確認してください。 また、この IP アドレスの切り替えの後に、ご利用のアプリケーションが自動的に Azure サービスに接続できることを確認してください。

  IP アドレスの切り替えが影響するのは、ご利用の仮想ネットワークからのサービス トラフィックのみです。 ご利用の仮想マシンに割り当てられたパブリック IPv4 アドレスとの間でやりとりされる他のトラフィックへの影響はありません。 Azure サービスについては、Azure のパブリック IP アドレスを使用する既存のファイアウォール ルールがある場合、これらのルールは仮想ネットワークのプライベート アドレスへの切り替え時に停止します。
- サービス エンドポイントを使用しても、Azure サービスの DNS エントリは変わらず、引き続き Azure サービスに割り当てられているパブリック IP アドレスに解決されます。
- ネットワーク セキュリティ グループ (NSG) でのサービス エンドポイントの使用:
  - 既定では、NSG は送信インターネット トラフィックを許可するので、VNet から Azure サービスへのトラフィックも許可します。 これは、サービス エンドポイントを使用しても変わりません。 
  - すべての送信インターネット トラフィックを拒否して特定の Azure サービスへのトラフィックだけを許可する場合は、NSG の[サービス タグ](security-overview.md#service-tags)を使います。 NSG のルールで、サポートされている Azure サービスを接続先として指定することができ、各タグの基になる IP アドレスのメンテナンスは Azure によって提供されます。 詳細については、[NSG の Azure サービス タグ](security-overview.md#service-tags)に関するページをご覧ください。 

### <a name="scenarios"></a>シナリオ

- **ピアリングされた仮想ネットワーク、接続された仮想ネットワーク、または複数の仮想ネットワーク**: Azure サービスへのアクセスを 1 つの仮想ネットワーク内または複数の仮想ネットワークにまたがる複数のサブネットに限定するには、サブネットごとに個別にサービス エンドポイントを有効にして、Azure サービス リソースへのアクセスをこれらのサブネットに限定します。
- **仮想ネットワークから Azure サービスへの送信トラフィックのフィルタリング**: 仮想ネットワークから Azure サービスへのトラフィックを検査またはフィルター処理する場合は、仮想ネットワーク内にネットワーク仮想アプライアンスをデプロイします。 その後、ネットワーク仮想アプライアンスがデプロイされているサブネットにサービス エンドポイントを適用し、このサブネットのみに Azure サービス リソースへのアクセスを限定します。 このシナリオは、ネットワーク仮想アプライアンス フィルタリングを使用して、ご利用の仮想ネットワークから Azure サービスへのアクセスを特定の Azure リソースにのみ制限する場合に役立ちます。 詳細については、[ネットワーク仮想アプライアンスを持つエグレス](/azure/architecture/reference-architectures/dmz/nva-ha#egress-with-layer-7-nvas.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関するページを参照してください。
- **Azure リソースへのアクセスを仮想ネットワークに直接デプロイされたサービスに限定する**: さまざまな Azure サービスを仮想ネットワーク内の特定のサブネットに直接デプロイできます。 管理されたサービスのサブネット上にサービス エンドポイントを設定することで、Azure サービス リソースへのアクセスを[管理されたサービス](virtual-network-for-azure-services.md)のサブネットに限定することができます。
- **Azure の仮想マシンからのディスク トラフィック**: マネージド/アンマネージド ディスクの仮想マシン ディスク トラフィック (マウント、マウント解除、diskIO を含みます) は、Azure Storage のサービス エンドポイントのルーティング変更の影響を受けません。 サービス エンドポイントと [Azure Storage ネットワーク ルール](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)を介して、ページ BLOB への REST アクセスを選択したネットワークに制限することができます。 

### <a name="logging-and-troubleshooting"></a>ロギングおよびトラブルシューティング

特定のサービスに対してサービス エンドポイントを構成したら、そのサービス エンドポイントのルートが有効であることを次の方法で検証します。 
 
- サービスの診断で任意のサービス要求の発信元 IP アドレスを検証します。 サービス エンドポイントを使った新しい要求では、ご利用の仮想ネットワークから要求元のクライアントに割り当てられた仮想ネットワークのプライベート IP アドレスとして、要求の発信元 IP アドレスが表示されます。 エンドポイントを使用しない場合、このアドレスは Azure のパブリック IP アドレスになります。
- サブネット内の任意のネットワーク インターフェイス上に有効なルートを表示する。 サービスへのルートでは次のことを確認できます。
  - 各サービスのアドレス プレフィックス範囲へのより詳細な既定のルート表示。
  - *VirtualNetworkServiceEndpoint* に存在する nextHopType
  - 強制トンネリングのルートに比べ、より直接的なサービスへの接続が実施されていること。

>[!NOTE]
> サービス エンドポイントのルートは、アドレス プレフィックスが Azure サービスのものと一致するすべての BGP または UDR のルートをオーバーライドします。 [有効なルートを使用したトラブルシューティング](diagnose-network-routing-problem.md)の詳細をご覧ください。

## <a name="provisioning"></a>プロビジョニング

サービス エンドポイントは、仮想ネットワークへの書き込みアクセス権を持つユーザーが仮想ネットワーク上で個別に構成できます。 Azure サービス リソースへのアクセスを VNet に限定するには、ユーザーが、追加されるサブネットの *Microsoft.Network/JoinServicetoaSubnet* へのアクセス許可を持っている必要があります。 このアクセス許可は、既定では組み込みのサービス管理者のロールに含まれ、カスタム ロールを作成することで変更できます。

[組み込みロール](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)と、特定のアクセス許可を[カスタム ロール](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に割り当てる方法の詳細をご覧ください。

仮想ネットワークと Azure サービス リソースのサブスクリプションは、同じでも異なっていてもかまいません。 仮想ネットワークと Azure サービス リソースのサブスクリプションが異なる場合、リソースは同じ Active Directory (AD) テナントの下に置かれている必要があります。 

## <a name="pricing-and-limits"></a>料金と制限

サービス エンドポイントの使用に追加料金はかかりません。 現時点では、Azure サービス (Azure Storage、Azure SQL Database など) の現在の価格設定モデルが適用されます。

仮想ネットワーク内のサービス エンドポイントの合計数に制限はありません。

Azure サービス リソース (Azure Storage アカウントなど) の場合、リソースへのアクセスに使用されるサブネットの数がサービスによって制限される場合があります。 詳細については、「[次のステップ](#next-steps)」の各種サービスに関するドキュメントを参照してください。

## <a name="next-steps"></a>次のステップ

- [仮想ネットワーク サービス エンドポイントを構成する](tutorial-restrict-network-access-to-resources.md)方法を学習する
- [Azure Storage アカウントを仮想ネットワークに限定する](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)方法を学習する
- [Azure SQL Database を仮想ネットワークに限定する](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)方法を学習する
- [仮想ネットワーク内の Azure サービス統合](virtual-network-for-azure-services.md)について学習する
-  クイック スタート: VNet のサブネットにサービス エンドポイントを設定し、そのサブネットの Azure Storage アカウントにセキュリティを確保する [Azure Resource Manager テンプレート](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration)

