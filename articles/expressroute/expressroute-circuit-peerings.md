---
title: Azure ExpressRoute 回線とルーティング ドメイン | Microsoft Docs
description: このページでは、ExpressRoute 回線とルーティング ドメインの概要を示します。
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: ''
ms.assetid: 6f0c5d8e-cc60-4a04-8641-2c211bda93d9
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: ganesr,cherylmc
ms.openlocfilehash: 563ee61b56af22ada662fcfff9f47ae58f3f32ba
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969097"
---
# <a name="expressroute-circuits-and-routing-domains"></a>ExpressRoute 回線とルーティング ドメイン
 接続プロバイダーを経由してオンプレミス インフラストラクチャを Microsoft に接続するには、 *ExpressRoute 回線* を注文する必要があります。 下の図は、お客様の WAN と Microsoft の接続を論理的に表現したものです。

![](./media/expressroute-circuit-peerings/expressroute-basic.png)

## <a name="expressroute-circuits"></a>ExpressRoute 回線
*ExpressRoute 回線* は、接続プロバイダー経由のオンプレミス インフラストラクチャと Microsoft クラウド サービス間の論理接続を表します。 複数の ExpressRoute 回線を注文することができます。 回線をそれぞれ同じリージョンや異なるリージョンに配置したり、異なる接続プロバイダーを経由して社内に接続したりすることができます。 

ExpressRoute 回線は物理エンティティにはマップされません。 回線は、サービス キー (s キー) という標準 GUID によって一意に識別されます。 サービス キーは、Microsoft、接続プロバイダー、およびユーザー間で交換される情報のほんの一部です。 s キーは、セキュリティ目的のシークレットではありません。 ExpressRoute 回線と s キーは 1 対 1 で対応しています。

ExpressRoute 回線では、Azure パブリック、Azure プライベート、および Microsoft という最大 3 つの独立したピアリングを使用することができます。 各ピアリングは独立した BGP セッションのペアで、高可用性を実現するためにセッションはそれぞれ冗長的に構成されています。 ExpressRoute 回線とルーティング ドメインは 1 対 N (1 <= N <= 3) で対応しています。 ExpressRoute 回線 は、回線ごとに 1 つ、2 つ、または 3 つすべてのピアリングを使用することができます。

各接続には固定帯域幅 (50 Mbps、100 Mbps、200 Mbps、500 Mbps、1 Gbps、10 Gbps) があり、接続プロバイダーとピアリング場所にマップされます。 選択する帯域幅は、この回線のすべてのピアリング間で共有されます。 

### <a name="quotas-limits-and-limitations"></a>クォータ、制限、および制限事項
既定のクォータと制限は、すべての ExpressRoute 回線に適用されます。 クォータに関する最新情報については、「 [Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md) 」ページをご覧ください。

## <a name="expressroute-routing-domains"></a>ExpressRoute のルーティング ドメイン
ExpressRoute 回線には、複数のルーティング ドメイン (Azure パブリック、Azure プライベート、Microsoft) が関連付けられます。 各ルーティング ドメインは、高可用性を実現するために、ルーターのペアに同じ方法 (アクティブ/アクティブ構成または負荷分散構成) で構成されます。 Azure サービスは *Azure パブリック*および *Azure プライベート*として分類され、IP アドレス指定スキームを表します。

![](./media/expressroute-circuit-peerings/expressroute-peerings.png)

### <a name="azure-private-peering"></a>Azure プライベート ピアリング
Azure Compute Services、つまり、仮想ネットワーク内にデプロイされる仮想マシン (IaaS) とクラウド サービス (PaaS) には、プライベート ピアリング ドメイン経由で接続できます。 プライベート ピアリング ドメインは、お客様のコア ネットワークを Microsoft Azure に信頼できる方法で拡張したものと言えます。 コア ネットワークと Azure Virtual Network (VNET) の間に双方向接続を設定できます。 このピアリングにより、仮想マシンとクラウド サービスにプライベート IP アドレスで直接接続できます。  

プライベート ピアリング ドメインには複数の仮想ネットワークを接続できます。 上限と制限事項については、 [FAQ のページ](expressroute-faqs.md) を参照してください。 制限に関する最新情報については、「 [Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md) 」ページをご覧ください。  ルーティング構成の詳細については、 [ルーティング](expressroute-routing.md) に関するページを参照してください。

### <a name="azure-public-peering"></a>Azure パブリック ピアリング

> [!IMPORTANT]
> すべての Azure PaaS サービスは、Microsoft ピアリング経由でアクセスすることもできます。 Microsoft ピアリングを作成し、Microsoft ピアリング経由で Azure PaaS サービスに接続することをお勧めします。  
>   


Azure Storage、SQL Database、Websites などのサービスは、パブリック IP アドレスで提供されます。 パブリック ピアリング ルーティング ドメインを経由して、(クラウド サービスの VIP などの) パブリック IP アドレスでホストされているサービスにプライベート接続できます。 パブリック ピアリング ドメインをご使用の DMZ に接続すれば、インターネット経由で接続しなくても WAN からパブリック IP アドレス上のすべての Azure サービスに接続できます。 

接続は、常に WAN から Microsoft Azure サービスへ開始されます。 このルーティング ドメインを経由して Microsoft Azure サービスからお客様のネットワークに接続を開始することはできません。 パブリック ピアリングが有効になると、すべての Azure サービスに接続できます。 ルートをアドバタイズするサービスを選択することはできません。

ネットワーク内でカスタム ルート フィルターを定義して、必要なルートのみを使用することができます。 ルーティング構成の詳細については、 [ルーティング](expressroute-routing.md) に関するページを参照してください。 

パブリック ピアリング ルーティング ドメインでサポートされるサービスの詳細については、[FAQ](expressroute-faqs.md) のページを参照してください。

### <a name="microsoft-peering"></a>Microsoft ピアリング
[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

Microsoft オンライン サービス (Office 365、Dynamics 365、Azure PaaS サービスなど) への接続は Microsoft ピアリングを経由します。 Microsoft ピアリング ルーティング ドメイン経由で、ご使用の WAN と Microsoft クラウド サービスの双方向接続を実現できます。 お客様または接続プロバイダーが所有するパブリック IP アドレスのみを使用して Microsoft クラウド サービスに接続する必要があり、定義されてするすべての規則を遵守する必要があります。 詳細については、[ExpressRoute の前提条件](expressroute-prerequisites.md)に関するページを参照してください。

サポートされるサービス、料金、および構成の詳細については、 [FAQ のページ](expressroute-faqs.md) を参照してください。 Microsoft ピアリングのサポートを提供している接続プロバイダーの一覧については、 [ExpressRoute の場所](expressroute-locations.md) に関するページを参照してください。

## <a name="routing-domain-comparison"></a>ルーティング ドメインの比較
次の表は、3 つのルーティング ドメインを比較しています。

|  | **プライベート ピアリング** | **パブリック ピアリング** (新規作成では非推奨) | **Microsoft ピアリング** |
| --- | --- | --- | --- |
| **各ピアリングでサポートされるプレフィックスの最大数** |既定 4,000、ExpressRoute Premium 10,000 |200 |200 |
| **サポートされる IP アドレス範囲** |お客様の WAN 内の任意の有効な IP アドレス。 |お客様または接続プロバイダーが所有するパブリック IP アドレス。 |お客様または接続プロバイダーが所有するパブリック IP アドレス。 |
| **AS 番号の要件** |プライベートおよびパブリックの AS 番号。 いずれかを使用する場合はパブリックの AS 番号を所有している必要があります。 |プライベートおよびパブリックの AS 番号。 ただし、パブリック IP アドレスの所有権を証明する必要があります。 |プライベートおよびパブリックの AS 番号。 ただし、パブリック IP アドレスの所有権を証明する必要があります。 |
| **サポート対象 IP プロトコル**| IPv4 | IPv4 | IPv4、IPv6 |
| **ルーティング インターフェイスの IP アドレス** |RFC1918 およびパブリック IP アドレス |ルーティング レジストリに登録されているパブリック IP アドレス。 |ルーティング レジストリに登録されているパブリック IP アドレス。 |
| **MD5 ハッシュのサポート** |[はい] |はい |[はい] |



回線をそれぞれ同じリージョンや異なるリージョンに配置したり、異なる接続プロバイダーを経由して社内に接続したりすることができます。 すべてのルーティング ドメインを 1 つのルーティング ドメインに取り込みたい場合は、同じ VPN 上に配置するように選択できます。 また、上図に似た別のルーティング ドメイン上に配置することもできます。 推奨構成は、プライベート ピアリングをコア ネットワークに直接接続し、パブリック ピアリングと Microsoft ピアリングのリンクを DMZ に接続する構成です。

3 つのピアリング セッションすべてを使用する場合は、BGP セッションのペアを 3 つ (ピアリングの種類ごとに 1 つのペア) 設定する必要があります。 BGP セッションのペアによって、高可用性リンクが実現されます。 レイヤー 2 接続プロバイダーを経由して接続している場合、ルーティングの構成と管理はお客様の責任となります。 詳細については、ExpressRoute を設定する [ワークフロー](expressroute-workflows.md) を参照してください。

## <a name="expressroute-health"></a>ExpressRoute の正常性
[Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM) を使用して、ExpressRoute 回線の可用性、VNet の接続、および帯域幅の使用状況を監視できます。

NPM は、Azure プライベート ピアリングと Microsoft ピアリングの正常性を監視します。  詳細については、こちらの[ブログ](https://azure.microsoft.com/en-in/blog/monitoring-of-azure-expressroute-in-preview/)を参照してください。

## <a name="next-steps"></a>次の手順
* サービス プロバイダーを検索します。 [ExpressRoute のサービス プロバイダーと提供地域](expressroute-locations.md)に関するページをご覧ください。
* すべての前提条件を満たしていることを確認します。 「 [Azure ExpressRoute の前提条件](expressroute-prerequisites.md)」を参照してください。
* ExpressRoute 接続を構成します。
  * [ExpressRoute 回線の作成と管理](expressroute-howto-circuit-portal-resource-manager.md)
  * [ExpressRoute 回線用のルーティング (ピアリング) の構成](expressroute-howto-routing-portal-resource-manager.md)

