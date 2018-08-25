---
title: Azure のセキュリティ グループの概要 | Microsoft Docs
description: ネットワークとアプリケーションのセキュリティ グループについて説明します。 セキュリティ グループは、フィルターを使って Azure リソース間のネットワーク トラフィックを絞り込む際に役立つものです。
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2018
ms.author: jdial
ms.openlocfilehash: f01ff2dfff19d066fe3ecd8924a5b54cd6cc68a5
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2018
ms.locfileid: "39264166"
---
# <a name="security-groups"></a>セキュリティ グループ
<a name="network-security-groups"></a>

ネットワーク セキュリティ グループを使用して、Azure [仮想ネットワーク](virtual-networks-overview.md)の Azure リソース間のネットワーク トラフィックをフィルター処理できます。 ネットワーク セキュリティ グループには、いくつかの種類の Azure リソースとの受信ネットワーク トラフィックまたは送信ネットワーク トラフィックを許可または拒否する[セキュリティ規則](#security-rules)が含まれています。 どの種類の Azure リソースを仮想ネットワークにデプロイし、ネットワーク セキュリティ グループを関連付けることができるかについては、「[Azure サービスの仮想ネットワーク統合](virtual-network-for-azure-services.md)」を参照してください。 各規則で、送信元と送信先、ポート、およびプロトコルを指定することができます。

この記事では、ネットワーク セキュリティ グループを効果的に使用できるように、その概念について説明します。 ネットワーク セキュリティ グループを作成したことがない場合は、簡単な[チュートリアル](tutorial-filter-network-traffic.md)で作成作業を体験することができます。 ネットワーク セキュリティ グループに精通しており、それらを管理する必要がある場合は、[ネットワーク セキュリティ グループの管理](manage-network-security-group.md)に関するページを参照してください。 通信に問題があり、ネットワーク セキュリティ グループのトラブルシューティングが必要な場合は、「[仮想マシン ネットワーク トラフィック フィルターの問題を診断する](diagnose-network-traffic-filter-problem.md)」を参照してください。 [ネットワーク セキュリティ グループのフロー ログ](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)を有効にして、ネットワーク セキュリティ グループが関連付けられているリソース間の[ネットワーク トラフィックを分析](../network-watcher/traffic-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)することができます。

## <a name="security-rules"></a>セキュリティ規則

ネットワーク セキュリティ グループには、0 個、または Azure サブスクリプションの[制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)内の任意の数の規則が含まれています。 各規則では次のプロパティを指定します。

|プロパティ  |説明  |
|---------|---------|
|Name|ネットワーク セキュリティ グループ内で一意の名前。|
|優先順位 | 100 ～ 4096 の数値。 規則は、優先順位に従って処理され、数値が小さいほど優先順位が高いために、大きい数値の前に小さい数値が処理されます。 トラフィックが規則に一致すると、処理が停止します。 この結果、優先順位低く (数値が大きい)、優先順位が高い規則と同じ属性を持つ規則は処理されません。|
|ソース/ターゲット| IP アドレス、クラスのないドメイン間ルーティング (CIDR) ブロック (例: 10.0.0.0/24)、[サービス タグ](#service-tags)、または[アプリケーション セキュリティ グループ](#application-security-groups)。 Azure リソースのアドレスを指定する場合は、そのリソースに割り当てられているプライベート IP アドレスを指定します。 ネットワーク セキュリティ グループが処理されるタイミングは、受信トラフィックの場合は Azure がパブリック IP アドレスをプライベート IP アドレスに変換した後、送信トラフィックの場合は Azure がプライベート IP アドレスをパブリック IP アドレスに変換する前です。 Azure IP アドレスの詳細については、[こちら](virtual-network-ip-addresses-overview-arm.md)を参照してください。 範囲、サービス タグ、またはアプリケーション セキュリティ グループを指定すると、作成するセキュリティ規則の数を減らせます。 規則内で複数の個別 IP アドレスと範囲 (複数のサービス タグまたはアプリケーション グループは指定できません) を指定する機能は、[拡張セキュリティ規則](#augmented-security-rules)と呼ばれています。 拡張セキュリティ規則は、Resource Manager デプロイ モデルで作成されたネットワーク セキュリティ グループでのみ作成できます。 クラシック デプロイ モデルで作成されたネットワーク セキュリティ グループで、複数の IP アドレスおよび IP アドレス範囲を指定することはできません。 Azure のデプロイ モデルの詳細については、[こちら](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)を参照してください。|
|プロトコル     | TCP、UDP、または Any。Any には TCP、UDP、ICMP が含まれます。 ICMP だけを指定することはできないので、ICMP が必要な場合は、Any を使用します。 |
|方向| 規則が受信トラフィックまたは送信トラフィックに適用されるかどうか。|
|ポートの範囲     |個別のポートまたはポートの範囲を指定できます。 たとえば、80 や 10000-10005 などと指定できます。 範囲を指定すると、作成するセキュリティ規則の数を減らすことができます。 拡張セキュリティ規則は、Resource Manager デプロイ モデルで作成されたネットワーク セキュリティ グループでのみ作成できます。 クラシック デプロイ モデルで作成されたネットワーク セキュリティ グループで、複数のポートまたはポート範囲を指定することはできません。   |
|アクションを表示します。     | 許可または拒否        |

トラフィックを許可または拒否するために、5 組の情報 (送信元、送信元ポート、送信先、送信先ポート、プロトコル) を使用して、優先度でネットワーク セキュリティ グループ セキュリティ規則が評価されます。 既存の接続に対するフロー レコードが作成されます。 そのフロー レコードの接続の状態に基づいて、通信が許可または拒否されます。 フロー レコードにより、ネットワーク セキュリティ グループはステートフルであることが可能になります。 たとえば、ポート 80 経由で任意のアドレスに送信セキュリティ規則を指定した場合、送信トラフィックへの応答に受信セキュリティ規則を指定する必要はありません。 通信が外部から開始された場合は、受信セキュリティ規則のみを指定する必要があります。 反対の場合も同じです。 ポートで受信トラフィックが許可されている場合、そのポートでのトラフィックに応答するために、送信セキュリティ規則を指定する必要はありません。
フローを有効にしたセキュリティ規則を削除したときに、既存の接続が中断されない場合があります。 接続が停止され、少なくとも数分間、どちらの方向のトラフィックも流れていないときに、トラフィック フローが中断されます。

ネットワーク セキュリティ グループ内に作成できるセキュリティ規則の数には、制限があります。 詳細については、[Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関する記事をご覧ください。

## <a name="augmented-security-rules"></a>拡張セキュリティ規則

拡張セキュリティ規則を使用すると仮想ネットワークのセキュリティ定義が簡略化され、大規模で複雑なネットワーク セキュリティ ポリシーを少ない規則で定義できます。 複数のポート、複数の明示的 IP アドレスおよび範囲を組み合わせて、単一のわかりやすいセキュリティ規則を作成することができます。 拡張規則は、規則のソース、ターゲット、ポート フィールドで使います。 セキュリティ規則の定義の保守を簡素化するには、拡張セキュリティ規則と[サービス タグ](#service-tags)または[アプリケーション セキュリティ グループ](#application-security-groups)を組み合わせます。 規則に指定できるアドレス、範囲、およびポートの数には、制限があります。 詳細については、[Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関する記事をご覧ください。

## <a name="service-tags"></a>サービス タグ

 サービス タグは IP アドレス プレフィックスのグループを表し、セキュリティ規則の作成の複雑さを最小限に抑えるのに役立ちます。 独自のサービス タグを作成したり、タグに含まれる IP アドレスを指定したりすることはできません。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。 セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 セキュリティ規則の定義で使うことができるサービス タグは次のとおりです。 サービス タグの名前は、[Azure のデプロイメント モデル](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)により若干異なります。

* **VirtualNetwork** (Resource Manager) (クラシックの場合は **VIRTUAL_NETWORK**): このタグには、仮想ネットワーク アドレス空間 (仮想ネットワークに対して定義されているすべての CIDR 範囲)、すべての接続されたオンプレミスのアドレス空間、および[ピアリング](virtual-network-peering-overview.md)された仮想ネットワークまたは[仮想ネットワーク ゲートウェイ](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に接続された仮想ネットワークが含まれます。
* **AzureLoadBalancer** (Resource Manager) (クラシックの場合は **AZURE_LOADBALANCER**): このタグは、Azure のインフラストラクチャのロード バランサーを表します。 このタグは、Azure の正常性プローブが開始される [Azure データセンター IP アドレス](https://www.microsoft.com/download/details.aspx?id=41653)に変換されます。 Azure Load Balancer を使っていない場合は、この規則をオーバーライドできます。
* **Internet** (Resource Manager) (クラシックの場合は **INTERNET**): このタグは、パブリック インターネットによってアクセスできる仮想ネットワークの外部の IP アドレス空間を表します。 [Azure に所有されているパブリック IP アドレス空間](https://www.microsoft.com/download/details.aspx?id=41653)がこのアドレス範囲に含まれます。
* **AzureTrafficManager** (Resource Manager のみ): このタグは、Azure Traffic Manager プローブ IP の IP アドレス空間を表します。 Traffic Manager プローブ IP の詳細については、[Azure Traffic Manager の FAQ](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs) に関するページを参照してください。 Azure [Public](https://www.microsoft.com/download/details.aspx?id=56519)、[US Government](https://www.microsoft.com/download/details.aspx?id=57063)、[China](https://www.microsoft.com/download/details.aspx?id=57062)、および [Germany](https://www.microsoft.com/download/details.aspx?id=57064) クラウドでこのタグに割り当てられているプレフィックスの一覧をダウンロードすることができます。
* **Storage** (Resource Manager のみ): このタグは、Azure Storage サービスの IP アドレス空間を表します。 値として *Storage* を指定した場合、ストレージへのトラフィックが許可または拒否されます。 特定の[リージョン](https://azure.microsoft.com/regions)のストレージに対するアクセスのみを許可する場合は、リージョンを指定することができます。 たとえば、米国東部リージョンの Azure Storage へのアクセスのみを許可する場合は、サービス タグとして *Storage.EastUS* と指定できます。 タグはサービスだけを表し、サービスの特定のインスタンスは表しません。 たとえば、このタグは Azure Storage サービスを表しますが、特定の Azure Storage アカウントは表しません。 このタグで表されるすべてのアドレス プレフィックスは、**Internet** タグでも表されます。 Azure [Public](https://www.microsoft.com/download/details.aspx?id=56519)、[US Government](https://www.microsoft.com/download/details.aspx?id=57063)、[China](https://www.microsoft.com/download/details.aspx?id=57062)、および [Germany](https://www.microsoft.com/download/details.aspx?id=57064) クラウドでこのタグに割り当てられているプレフィックスの一覧をダウンロードすることができます。
* **Sql** (Resource Manager のみ): このタグは、Azure SQL Database サービスおよび Azure SQL Data Warehouse サービスのアドレス プレフィックスを表します。 値として *Sql* を指定した場合、SQL へのトラフィックが許可または拒否されます。 特定の[リージョン](https://azure.microsoft.com/regions)の SQL へのアクセスのみを許可する場合は、リージョンを指定することができます。 たとえば、米国東部リージョンの Azure SQL Database へのアクセスのみを許可する場合は、サービス タグとして *Sql.EastUS* と指定できます。 タグはサービスだけを表し、サービスの特定のインスタンスは表しません。 たとえば、このタグは Azure SQL Database サービスを表しますが、特定の SQL データベースや SQL サーバーは表しません。 このタグで表されるすべてのアドレス プレフィックスは、**Internet** タグでも表されます。 Azure [Public](https://www.microsoft.com/download/details.aspx?id=56519)、[US Government](https://www.microsoft.com/download/details.aspx?id=57063)、[China](https://www.microsoft.com/download/details.aspx?id=57062)、および [Germany](https://www.microsoft.com/download/details.aspx?id=57064) クラウドでこのタグに割り当てられているプレフィックスの一覧をダウンロードすることができます。
* **AzureCosmosDB** (Resource Manager のみ): このタグは、Azure Cosmos Database サービスのアドレス プレフィックスを表します。 値として *AzureCosmosDB* を指定した場合、AzureCosmosDB へのトラフィックが許可または拒否されます。 特定の[リージョン](https://azure.microsoft.com/regions)の AzureCosmosDB へのアクセスのみを許可する場合は、AzureCosmosDB.[リージョン名] の形式でリージョンを指定できます。 Azure [Public](https://www.microsoft.com/download/details.aspx?id=56519)、[US Government](https://www.microsoft.com/download/details.aspx?id=57063)、[China](https://www.microsoft.com/download/details.aspx?id=57062)、および [Germany](https://www.microsoft.com/download/details.aspx?id=57064) クラウドでこのタグに割り当てられているプレフィックスの一覧をダウンロードすることができます。
* **AzureKeyVault** (Resource Manager のみ): このタグは、Azure KeyVault サービスのアドレス プレフィックスを表します。 値として *AzureKeyVault* を指定した場合、AzureKeyVault へのトラフィックが許可または拒否されます。 特定の[リージョン](https://azure.microsoft.com/regions)の AzureKeyVault へのアクセスのみを許可する場合は、AzureKeyVault.[リージョン名] の形式でリージョンを指定できます。 Azure [Public](https://www.microsoft.com/download/details.aspx?id=56519)、[US Government](https://www.microsoft.com/download/details.aspx?id=57063)、[China](https://www.microsoft.com/download/details.aspx?id=57062)、および [Germany](https://www.microsoft.com/download/details.aspx?id=57064) クラウドでこのタグに割り当てられているプレフィックスの一覧をダウンロードすることができます。

> [!NOTE]
> Azure サービスのサービス タグは、使用されている特定のクラウドからのアドレス プレフィックスを表します。 リージョン サービス タグは国内クラウドではサポートされておらず、グローバル形式でのみサポートされます。 たとえば、*Storage*、*Sql* などです。

> [!NOTE]
> Azure Storage や Azure SQL Database などのサービスの[仮想ネットワーク サービス エンドポイント](virtual-network-service-endpoints-overview.md)を実装する場合、Azure はサービスの仮想ネットワーク サブネットに[ルート](virtual-networks-udr-overview.md#optional-default-routes)を追加します。 ルートのアドレス プレフィックスは、対応するサービス タグと同じアドレス プレフィックスまたは CIDR 範囲です。

## <a name="default-security-rules"></a>既定セキュリティ規則

作成する各ネットワーク セキュリティ グループに、Azure によって次の既定の規則が作成されます。

### <a name="inbound"></a>受信

#### <a name="allowvnetinbound"></a>AllowVNetInBound

|優先順位|ソース|ソース ポート|変換先|ターゲット ポート|プロトコル|Access|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|All|ALLOW|

#### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|優先順位|ソース|ソース ポート|変換先|ターゲット ポート|プロトコル|Access|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|All|ALLOW|

#### <a name="denyallinbound"></a>DenyAllInbound

|優先順位|ソース|ソース ポート|変換先|ターゲット ポート|プロトコル|Access|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|All|拒否|

### <a name="outbound"></a>送信

#### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|優先順位|ソース|ソース ポート| 変換先 | ターゲット ポート | プロトコル | Access |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | All | ALLOW |

#### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|優先順位|ソース|ソース ポート| 変換先 | ターゲット ポート | プロトコル | Access |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | インターネット | 0-65535 | All | ALLOW |

#### <a name="denyalloutbound"></a>DenyAllOutBound

|優先順位|ソース|ソース ポート| 変換先 | ターゲット ポート | プロトコル | Access |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | All | 拒否 |

"**ソース**" 列と "**ターゲット**" 列の *VirtualNetwork*、*AzureLoadBalancer*、および *Internet* は、IP アドレスではなく[サービス タグ](#service-tags)です。 "プロトコル" 列で "**すべて**" は TCP、UDP、ICMP を含みます。 規則を作成するとき、TCP、UDP、またはすべてを指定できますが、ICMP だけを指定することはできません。 そのため、規則で ICMP が必要な場合は、プロトコルとして "*すべて*" を選択します。 "**ソース**" 列と "**ターゲット**" 列の *0.0.0.0/0* は、すべてのアドレスを表します。
 
既定の規則は削除できませんが、優先順位の高い規則を作成することでオーバーライドできます。

## <a name="application-security-groups"></a>アプリケーション セキュリティ グループ

アプリケーション セキュリティ グループを使用すると、ネットワーク セキュリティをアプリケーションの構造の自然な拡張として構成でき、仮想マシンをグループ化して、それらのグループに基づくネットワーク セキュリティ ポリシーを定義できます。 明示的な IP アドレスを手動でメンテナンスせずに、大きなセキュリティ ポリシーを再利用することができます。 プラットフォームが明示的な IP アドレスと複数の規則セットの複雑さを処理するので、ユーザーはビジネス ロジックに専念することができます。 アプリケーション セキュリティ グループをよりよく理解するために、次の例について考えてください。

![アプリケーション セキュリティ グループ](./media/security-groups/application-security-groups.png)

前の図では、*NIC1* と *NIC2* が *AsgWeb* アプリケーション セキュリティ グループのメンバーです。 *NIC3* は、*AsgLogic* アプリケーション セキュリティ グループのメンバーです。 *NIC4* は、*AsgDb* アプリケーション セキュリティ グループのメンバーです。 この例の各ネットワーク インターフェイスは 1 つのアプリケーション セキュリティ グループだけのメンバーですが、ネットワーク インターフェイスは複数のアプリケーション セキュリティ グループのメンバーにすることができます (最大数については、[Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)を参照してください)。 ネットワーク セキュリティ グループが関連付けられているネットワーク インターフェイスはありません。 *NSG1* は両方のサブネットに関連付けられており、次の規則を含んでいます。

### <a name="allow-http-inbound-internet"></a>Allow-HTTP-Inbound-Internet

この規則は、インターネットから Web サーバーへのトラフィックを許可するために必要です。 インターネットからの受信トラフィックは [DenyAllInbound](#denyallinbound) 既定セキュリティ規則によって拒否されるため、*AsgLogic* または *AsgDb* アプリケーション セキュリティ グループでは追加の規則は必要ありません。

|優先順位|ソース|ソース ポート| 変換先 | ターゲット ポート | プロトコル | Access |
|---|---|---|---|---|---|---|
| 100 | インターネット | * | AsgWeb | 80 | TCP | ALLOW |

### <a name="deny-database-all"></a>Deny-Database-All

[AllowVNetInBound](#allowvnetinbound) 既定セキュリティ規則では、同じ仮想ネットワーク内のリソース間のすべての通信が許可されるため、この規則はすべてのリソースからのトラフィックを拒否するために必要です。

|優先順位|ソース|ソース ポート| 変換先 | ターゲット ポート | プロトコル | Access |
|---|---|---|---|---|---|---|
| 120 | * | * | AsgDb | 1433 | All | 拒否 |

### <a name="allow-database-businesslogic"></a>Allow-Database-BusinessLogic

この規則は、*AsgLogic* アプリケーション セキュリティ グループから *AsgDb* アプリケーション セキュリティ グループへのトラフィックを許可します。 この規則の優先度は、*Deny-Database-All* 規則の優先度よりも高くなっています。 その結果、この規則は *Deny-Database-All* 規則の前に処理されるため、*AsgLogic* アプリケーション セキュリティ グループからのトラフィックは許可されますが、他のすべてのトラフィックはブロックされます。

|優先順位|ソース|ソース ポート| 変換先 | ターゲット ポート | プロトコル | Access |
|---|---|---|---|---|---|---|
| 110 | AsgLogic | * | AsgDb | 1433 | TCP | ALLOW |

アプリケーション セキュリティ グループを送信元または送信先として指定されている規則は、アプリケーション セキュリティ グループのメンバーであるネットワーク インターフェイスにのみ適用されます。 ネットワーク インターフェイスがアプリケーション セキュリティ グループのメンバーでない場合、ネットワーク セキュリティ グループがサブネットに関連付けられていても、規則はネットワーク インターフェイスに適用されません。

アプリケーション セキュリティ グループには、次の制約があります。

-   アプリケーション セキュリティ グループに関してはいくつかの制限がありますが、サブスクリプションに含めることができるアプリケーション セキュリティ グループの数にも制限があります。 詳細については、[Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関する記事をご覧ください。
- セキュリティ規則のソースおよびターゲットとして、1 つのアプリケーション セキュリティ グループを指定できます。 送信元と送信先に複数のアプリケーション セキュリティ グループを指定することはできません。
- アプリケーション セキュリティ グループに最初に割り当てられたネットワーク インターフェイスが存在する仮想ネットワークに、そのアプリケーション セキュリティ グループに割り当てられたすべてのネットワーク インターフェイスが存在する必要があります。 たとえば、*AsgWeb* という名前のアプリケーション セキュリティ グループに最初に割り当てられたネットワーク インターフェイスが *VNet1* という名前の仮想ネットワークにある場合、*AsgWeb* に以降に割り当てられるすべてのネットワーク インターフェイスが *VNet1* に存在する必要があります。 異なる仮想ネットワークからのネットワーク インターフェイスを同じアプリケーション セキュリティ グループに追加することはできません。
- セキュリティ規則のソースおよびターゲットとしてアプリケーション セキュリティ グループを指定する場合、両方のアプリケーション セキュリティ グループのネットワーク インターフェイスが、同じ仮想ネットワークに存在している必要があります。 たとえば、*VNet1* のネットワーク インターフェイスが *AsgLogic*、*VNet2* のネットワーク インターフェイスが *AsgDb* に存在する場合、規則の送信元として *AsgLogic*、送信先として *AsgDb* を割り当てることはできません。 送信元と送信先の両方のアプリケーション セキュリティ グループ内のすべてのネットワーク インターフェイスは、同じ仮想ネットワークに存在している必要があります。

> [!TIP]
> 必要なセキュリティ規則の数と、規則を変更する必要性を最小限に抑えるには、必要なアプリケーション セキュリティ グループを綿密に計画し、できる限り個々の IP アドレスまたは IP アドレスの範囲ではなく、サービス タグまたはアプリケーション セキュリティ グループを使用して規則を作成します。

## <a name="how-traffic-is-evaluated"></a>トラフィックの評価方法

1 つの Azure 仮想ネットワークに、いくつかの Azure サービスのリソースをデプロイすることができます。 完全な一覧については、「[仮想ネットワークにデプロイできるサービス](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)」を参照してください。 仮想マシンの各仮想ネットワーク [サブネット](virtual-network-manage-subnet.md#change-subnet-settings)および[ネットワーク インターフェイス](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group)に、ゼロ個または 1 個のネットワーク セキュリティ グループを関連付けることができます。 同じネットワーク セキュリティ グループを、任意の数のサブネットとネットワーク インターフェイスに関連付けることができます。

次の図は、インターネットとの間で TCP ポート 80 を経由してネットワーク トラフィックを送受信できるようにネットワーク セキュリティ グループをデプロイするさまざまなシナリオを示しています。

![NSG の処理](./media/security-groups/nsg-interaction.png)

Azure がネットワーク セキュリティ グループの受信規則と送信規則をどのように処理するかを理解するために、以下のテキストを読みながら、前の図を参照してください。

### <a name="inbound-traffic"></a>受信トラフィック

受信トラフィックの場合、Azure は、サブネットに関連付けられているネットワーク セキュリティ グループがあれば、まずその規則を処理し、次にネットワーク インターフェイスに関連付けられているネットワーク セキュリティ グループがあれば、その規則を処理します。

- **VM1**: *NSG1* のセキュリティ規則が処理されます。ネットワーク セキュリティ グループが *Subnet1* に関連付けられており、*VM1* が *Subnet1* 内にあるためです。 ポート 80 の受信を許可する規則を作成していない場合、トラフィックは [DenyAllInbound](#denyallinbound) 既定セキュリティ規則によって拒否され、*NSG2* によって評価されることはありません。これは、*NSG2* がネットワーク インターフェイスに関連付けられているためです。 *NSG1* にポート 80 を許可するセキュリティ規則がある場合、トラフィックは *NSG2* によって処理されます。 仮想マシンにポート 80 を許可するには、*NSG1* と *NSG2* の両方に、インターネットからのポート 80 を許可する規則が必要です。
- **VM2**: *NSG1* の規則が処理されます。*VM2* も *Subnet1* 内にあるためです。 *VM2* はネットワーク インターフェイスに関連付けられたネットワーク セキュリティ グループを持たないため、*NSG1* で許可されたすべてのトラフィックを受信するか、*NSG1* によって拒否されたすべてのトラフィックが拒否されます。 トラフィックは、ネットワーク セキュリティ グループがサブネットに関連付けられている場合、同じサブネット内のすべてのリソースに対して許可または拒否されます。
- **VM3**: *Subnet2* にはネットワーク セキュリティ グループが関連付けられていないため、トラフィックはサブネットに対して許可され、*NSG2* によって処理されます。*NSG2* が、*VM3* に接続されているネットワーク インターフェイスに関連付けられているためです。
- **VM4**: ネットワーク セキュリティ グループが *Subnet3* にも仮想マシンのネットワーク インターフェイスにも関連付けられていないため、*VM4* へのトラフィックが許可されます。 サブネットおよびネットワーク インターフェイスにネットワーク セキュリティ グループが関連付けられていない場合、すべてのネットワーク トラフィックがサブネットおよびネットワーク インターフェイスを介して許可されます。

### <a name="outbound-traffic"></a>送信トラフィック

送信トラフィックの場合、Azure はネットワーク インターフェイスに関連付けられているネットワーク セキュリティ グループがあれば、まずその規則を処理し、次にサブネットに関連付けられているネットワーク セキュリティ グループがあれば、その規則を処理します。

- **VM1**: *NSG2* のセキュリティ規則が処理されます。 インターネットへのポート 80 送信を拒否するセキュリティ規則を作成しない限り、トラフィックは *NSG1* と *NSG2* の両方の [AllowInternetOutbound](#allowinternetoutbound) 既定セキュリティ規則によって許可されます。 *NSG2* にポート 80 を拒否するセキュリティ規則がある場合、トラフィックは拒否され、*NSG1* によって評価されることはありません。 仮想マシンのポート 80 を拒否するには、ネットワーク セキュリティ グループのいずれかまたは両方に、インターネットへのポート 80 を拒否する規則が必要です。
- **VM2**: すべてのトラフィックがネットワーク インターフェイスを介してサブネットに送信されます。*VM2* に接続されているネットワーク インターフェイスに、ネットワーク セキュリティ グループが関連付けられていないためです。 *NSG1* の規則が処理されます。
- **VM3**: *NSG2* にポート 80 を拒否するセキュリティ規則がある場合、トラフィックは拒否されます。 *NSG2* にポート 80 を許可するセキュリティ規則がある場合、ネットワーク セキュリティ グループが *Subnet2* に関連付けられていないため、ポート 80 はインターネットへの送信を許可されます。
- **VM4**: ネットワーク セキュリティ グループが、仮想マシンに接続されているネットワーク インターフェイスにも *Subnet3* にも関連付けられていないため、*VM4* からのすべてのネットワーク トラフィックが許可されます。

ネットワーク インターフェイスの[有効なセキュリティ規則](virtual-network-network-interface.md#view-effective-security-rules)を表示すると、ネットワーク インターフェイスに適用されている規則の集計を簡単に確認できます。 Azure Network Watcher の [[IP フローの確認]](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 機能を使って、ネットワーク インターフェイスの受信/送信が許可されているかどうかを確認することもできます。 IP フローの確認を使うと、通信が許可または拒否されているかどうかと、どのネットワーク セキュリティ規則でトラフィックが許可/拒否されているかを確認できます。

> [!NOTE]
> ネットワーク セキュリティ グループは、Resource Manager デプロイ モデルのネットワーク インターフェイスではなく、サブネット、またはクラシック デプロイ モデルにデプロイされた仮想マシン クラウド サービスに関連付けられています。 Azure のデプロイメント モデルについて詳しくは、[Azure のデプロイメント モデルの概要](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事をご覧ください。

> [!TIP]
> 特別な理由がない限り、ネットワーク セキュリティ グループをサブネットまたはネットワーク インターフェイスに関連付けることをお勧めします。両方に関連付けることは、お勧めしません。 サブネットに関連付けられたネットワーク セキュリティ グループの規則が、ネットワーク インターフェイスに関連付けられたネットワーク セキュリティ グループの規則と競合する可能性があるため、予期しない通信の問題が発生し、トラブルシューティングが必要になることがあります。

## <a name="azure-platform-considerations"></a>Azure プラットフォームに関する考慮事項

- **ホスト ノードの仮想 IP:** DHCP、DNS、正常性の監視などの基本的なインフラストラクチャ サービスは、仮想化されたホストの IP アドレス 168.63.129.16 および 169.254.169.254 を通じて提供されます。 このパブリック IP アドレスは Microsoft に属し、この目的のためにすべてのリージョンで使われる唯一の仮想化 IP アドレスです。 このアドレスは、仮想マシンをホストしているサーバー マシン (ホスト ノード) の物理 IP アドレスにマッピングされます。 ホスト ノードは、DHCP リレー、DNS の再帰的リゾルバー、および Load Balancer の正常性プローブとマシンの正常性プローブのプローブ元として機能します。 この IP アドレスへの通信は攻撃ではありません。 これらの IP アドレスの受信/送信トラフィックをブロックすると、仮想マシンが正しく機能しない可能性があります。
- **ライセンス (キー管理サービス)**: 仮想マシンで実行されている Windows イメージのライセンスを取得する必要があります。 ライセンスを適用するために、そのような問い合わせを処理するキー管理サービスのホスト サーバーには要求が送信されます。 この要求は、ポート 1688 を通じて送信されます。 [default route 0.0.0.0/0](virtual-networks-udr-overview.md#default-route) 構成を使用したデプロイに関しては、このプラットフォーム ルールは無効となります。
- **負荷分散プール内の仮想マシン**: 適用されるソース ポートおよびアドレス範囲は、元のコンピューターからのもので、ロード バランサーではありません。 ターゲット ポートとアドレス範囲は、ロード バランサーのものではなく、ターゲット コンピューターのものになります。
- **Azure のサービス インスタンス**: HDInsight、Application Service Environments、および仮想マシン スケール セットなどの Azure サービスのインスタンスが仮想ネットワークのサブネットにデプロイされています。 仮想ネットワークにデプロイできるサービスの詳細な一覧については、[Azure サービスの仮想ネットワーク](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)に関するページをご覧ください。 リソースがデプロイされているサブネットにネットワーク セキュリティ グループを適用する前に、各サービスのポート要件を確認してください。 サービスに必要なポートを拒否すると、サービスは正しく機能しません。
- **アウトバウンド メールの送信**: Azure Virtual Machines からのメール送信に関して、Microsoft では、Authenticated SMTP リレー サービスの利用を推奨しています (通常は、TCP ポート 587 で接続されますが、他のポートが使用されることもあります)。 SMTP リレー サービスは、送信者評価に特化することで、サード パーティのメール プロバイダーによってメッセージが拒否される可能性を最小限に抑えます。 そのような SMTP リレー サービスとしては、Exchange Online Protection や SendGrid が代表的ですが、他にもさまざまなリレー サービスが存在します。 Azure に限らず、またサブスクリプションの種類に限らず、SMTP リレー サービスは広く利用されています。 

  2017 年 11 月 15 日より前に Azure サブスクリプションを作成した場合、SMTP リレー サービスが利用できるほか、TCP ポート 25 を使用して直接メールを送信することもできます。 2017 年 11 月 15 日以降に Azure サブスクリプションを作成した場合、ポート 25 を使用して直接メールを送信することはできません。 ポート 25 を使用したアウトバウンド通信の動作は、ご利用のサブスクリプションの種類によって次のように異なります。

     - **マイクロソフト エンタープライズ契約**: 送信ポート 25 の通信が許可されます。 仮想マシンから外部のメール プロバイダーに直接アウトバウンド メールを送信できます。Azure プラットフォームによる制限はありません。 
     - **従量課金制:** アウトバウンド ポート 25 の通信が、送信元となるすべてのリソースについてブロックされます。 外部のメール プロバイダーに仮想マシンから直接 (認証済み SMTP リレーを介さずに) メールを送信する必要がある場合は、申請によって制限を解除することができます。 申請は Microsoft の裁量にて審査および承認されます。また、申請が許可されるのは、詐欺行為防止チェックの実施後となります。 申請を行うには、*[Technical]\(技術\)*、*[Virtual Network Connectivity]\(仮想ネットワーク接続\)*、*[Cannot send e-mail (SMTP/Port 25)]\(メールを送信できない (SMTP/ポート 25)\)* を問題の種類とするサポート ケースを開いてください。 ご利用のサブスクリプションから Authenticated SMTP リレー経由ではなく直接メール プロバイダーにメールを送信することが必要である理由をサポート ケースに詳しく記してください。 ご利用のサブスクリプションが免除された場合に、ポート 25 を使用したアウトバウンド通信が許可されるのは、その免除日以降に作成された仮想マシンだけです。
     - **MSDN、Azure Pass、Azure イン オープン プラン、Education、BizSpark、無料試用版**: アウトバウンド ポート 25 の通信が、送信元となるすべてのリソースについてブロックされます。 制限解除を申請することはできません。申請は許可されません。 仮想マシンからメールを送信する必要がある場合は、SMTP リレー サービスを使用する必要があります。
     - **クラウド サービス プロバイダー**: クラウド サービス プロバイダーを介して Azure リソースを使用しているお客様は、クラウド サービス プロバイダーを相手方とするサポート ケースを作成できます。また、安全な SMTP リレーが利用できない場合は、お客様の代わりにプロバイダーがブロック解除のケースを作成するよう要求できます。

  Azure でポート 25 経由のメール送信が許可された場合に、メール プロバイダーが、ご利用の仮想マシンからのインバウンド メールを受け入れるかどうかについては、Microsoft では保証できません。 ご利用の仮想マシンからのメールが特定のプロバイダーによって拒否される場合は、そのプロバイダーに直接働きかけて、メッセージ配信の問題やスパム フィルターの問題を解決するか、Authenticated SMTP リレー サービスを使用します。

## <a name="next-steps"></a>次の手順

* [ネットワーク セキュリティ グループの作成](tutorial-filter-network-traffic.md)方法を学習します。
