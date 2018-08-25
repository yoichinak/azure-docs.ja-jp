---
title: Premium Azure Redis Cache 向けの Virtual Network のサポートの構成 | Microsoft Docs
description: Premium レベル Azure Redis Cache インスタンスの Virtual Network のサポートを作成および管理する方法
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 8b1e43a0-a70e-41e6-8994-0ac246d8bf7f
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: wesmc
ms.openlocfilehash: 250c66c3a39519a6eddc1ecb51259ec1944c88a9
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38671124"
---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-redis-cache"></a>Premium Azure Redis Cache の Virtual Network のサポートを構成する方法
Azure Redis Cache には、クラスタリング、永続性、仮想ネットワークのサポートといった Premium レベルの機能など、キャッシュのサイズと機能を柔軟に選択できるさまざまなキャッシュ サービスがあります。 VNet とは、クラウド内のプライベート ネットワークです。 VNet を使用して Azure Redis Cache インスタンスを構成する場合、パブリックにアドレスを指定することはできないため、VNet 内の仮想マシンとアプリケーションからしかアクセスできません。 この記事では、Premium Azure Redis Cache インスタンスの仮想ネットワークのサポートを構成する方法について説明します。

> [!NOTE]
> Azure Redis Cache では、クラシック VNet と Resource Manager VNet の両方がサポートされています。
> 
> 

Premium キャッシュのその他の機能の詳細については、「 [Azure Redis Cache Premium レベルの概要](cache-premium-tier-intro.md)」を参照してください。

## <a name="why-vnet"></a>VNet を選ぶ理由
[Azure Virtual Network (VNet)](https://azure.microsoft.com/services/virtual-network/) のデプロイにより、Azure Redis Cache のセキュリティと分離が強化されるとともに、サブネット、アクセス制御ポリシー、アクセスをさらに制御する他の機能も提供されます。

## <a name="virtual-network-support"></a>Virtual Network のサポート
仮想ネットワーク (VNet) のサポートは、キャッシュの作成中に **[新規 Redis Cache]** ブレードで構成します。 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Premium 価格レベルを選択すると、キャッシュと同じサブスクリプションと場所にある VNet を選択することで、Redis VNet 統合を構成できます。 新しい VNet を使用するには、まず「[Azure ポータルを使用した仮想ネットワークの作成](../virtual-network/manage-virtual-network.md#create-a-virtual-network)」または「[Azure ポータルを使用した仮想ネットワーク (従来型) の作成](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)」の手順に従って VNet を作成した後、**[新規 Redis Cache]** ブレードに戻り、Premium キャッシュを作成して構成します。

新しいキャッシュ用に VNet を構成するには、**[新規 Redis Cache]** ブレードの **[仮想ネットワーク]** をクリックし、ドロップダウン リストから目的の VNet を選択します。

![Virtual network][redis-cache-vnet]

**[サブネット]** ボックスの一覧で目的のサブネットを選択し、必要な**静的 IP アドレス**を指定します。 クラシック VNet を使用している場合、**[静的 IP アドレス]** フィールドは省略可能です。何も指定しないと、選択したサブネットから 1 つのアドレスが選択されます。

> [!IMPORTANT]
> Azure Redis Cache を Resource Manager VNet にデプロイする場合、キャッシュは、Azure Redis Cache インスタンス以外のリソースを含まない専用サブネット内に存在する必要があります。 Resource Manager VNet で他のリソースが含まれるサブネットに Azure Redis Cache をデプロイしようとすると、そのデプロイは失敗します。
> 
> 

![Virtual network][redis-cache-vnet-ip]

> [!IMPORTANT]
> Azure は、各サブネット内で一部の IP アドレスを予約し、これらのアドレスを使用することはできません。 サブネットの最初と最後の IP アドレスは、Azure サービスで使用される 3 つ以上のアドレスと共に、プロトコル準拠に予約されます。 詳細については、「 [これらのサブネット内の IP アドレスの使用に関する制限はありますか](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
> 
> Azure VNET インフラストラクチャによって使用される IP アドレスに加えて、サブネットの各 Redis インスタンスは、シャードごとに 2 つの IP アドレスおよびロード バランサー用に 1 つの追加 IP アドレスを使用します。 クラスター化されていないキャッシュは、1 つのシャードを持つと見なされます。
> 
> 

キャッシュが作成されたら、**[リソース]** メニューの **[仮想ネットワーク]** をクリックすることで、VNet の構成を表示できます。

![Virtual network][redis-cache-vnet-info]

VNet の使用時に Azure Redis Cache インスタンスに接続するには、次の例に示すように、接続文字列でキャッシュのホスト名を指定します。

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

## <a name="azure-redis-cache-vnet-faq"></a>Azure Redis Cache VNet についてよく寄せられる質問 (FAQ)
次の一覧は、Azure Redis Cache のスケーリングに関するよく寄せられる質問への回答です。

* [Azure Redis Cache と VNet の誤った構成に関してよく見られる問題を教えてください](#what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets)
* [VNET で自分のキャッシュの動作を確認するにはどうすればよいですか](#how-can-i-verify-that-my-cache-is-working-in-a-vnet)
* [VNET で自分の Redis キャッシュへの接続を試行すると、リモート証明書が無効であることを示すエラーが表示されるのはなぜですか](#when-trying-to-connect-to-my-redis-cache-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid)
* [Standard キャッシュまたは Basic キャッシュで VNet を使用できますか](#can-i-use-vnets-with-a-standard-or-basic-cache)
* [Redis Cache の作成が失敗するサブネットと成功するサブネットがあるのはなぜですか](#why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others)
* [サブネット アドレス空間の要件には何がありますか](#what-are-the-subnet-address-space-requirements)
* [VNET でキャッシュをホストしている場合、キャッシュ機能はすべて動作しますか](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets"></a>Azure Redis Cache と VNet の誤った構成に関してよく見られる問題を教えてください
Azure Redis Cache が VNet でホストされている場合は、次の表にあるポートが使用されます。 

>[!IMPORTANT]
>次の表のポートがブロックされている場合、キャッシュが正常に動作しない可能性があります。 VNet で Azure Redis Cache を使用する場合、構成の誤りに関する最も一般的な問題は、これらのポートのうち 1 つ以上がブロックされていることです。
> 
> 

- [送信ポートの要件](#outbound-port-requirements)
- [受信ポートの要件](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>送信ポートの要件

送信ポートには 7 個の要件があります。

- 必要に応じて、インターネットに対するすべての送信接続をクライアントのオンプレミス監査デバイス経由にすることができます。
- 3 つのポートは、Azure Storage と Azure DNS を提供する Azure エンドポイントにトラフィックをルーティングします。
- その他のポート範囲は、内部の Redis サブネット通信用です。 内部 Redis サブネット通信用にサブネット NSG 規則は必要ありません。

| ポート | 方向 | トランスポート プロトコル | 目的 | ローカル IP | リモート IP |
| --- | --- | --- | --- | --- | --- |
| 80、443 |送信 |TCP |Azure Storage/PKI (インターネット) に対する Redis の依存関係 | (Redis サブネット) |* |
| 53 |送信 |TCP/UDP |DNS (インターネット/VNet) に対する Redis の依存関係 | (Redis サブネット) |* |
| 8443 |送信 |TCP |Redis の内部通信 | (Redis サブネット) | (Redis サブネット) |
| 10221-10231 |送信 |TCP |Redis の内部通信 | (Redis サブネット) | (Redis サブネット) |
| 20226 |送信 |TCP |Redis の内部通信 | (Redis サブネット) |(Redis サブネット) |
| 13000-13999 |送信 |TCP |Redis の内部通信 | (Redis サブネット) |(Redis サブネット) |
| 15000-15999 |送信 |TCP |Redis の内部通信 | (Redis サブネット) |(Redis サブネット) |
| 6379-6380 |送信 |TCP |Redis の内部通信 | (Redis サブネット) |(Redis サブネット) |


#### <a name="inbound-port-requirements"></a>受信ポートの要件

受信ポートの範囲には、8 個の要件があります。 これらの範囲の受信要件は、同じ VNET でホストされている他のサービスからの受信、または Redis サブネット通信への内部の要件です。

| ポート | 方向 | トランスポート プロトコル | 目的 | ローカル IP | リモート IP |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |受信 |TCP |Redis へのクライアント通信、Azure 負荷分散 | (Redis サブネット) | (Redis サブネット)、Virtual Network、Azure Load Balancer |
| 8443 |受信 |TCP |Redis の内部通信 | (Redis サブネット) |(Redis サブネット) |
| 8500 |受信 |TCP/UDP |Azure 負荷分散 | (Redis サブネット) |Azure Load Balancer |
| 10221-10231 |受信 |TCP |Redis の内部通信 | (Redis サブネット) |(Redis サブネット)、Azure Load Balancer |
| 13000-13999 |受信 |TCP |Redis クラスターへのクライアント通信、Azure 負荷分散 | (Redis サブネット) |Virtual Network、Azure Load Balancer |
| 15000-15999 |受信 |TCP |Redis クラスターへのクライアント通信、Azure 負荷分散 | (Redis サブネット) |Virtual Network、Azure Load Balancer |
| 16001 |受信 |TCP/UDP |Azure 負荷分散 | (Redis サブネット) |Azure Load Balancer |
| 20226 |受信 |TCP |Redis の内部通信 | (Redis サブネット) |(Redis サブネット) |

#### <a name="additional-vnet-network-connectivity-requirements"></a>その他の VNET ネットワーク接続の要件

Azure Redis Cache のネットワーク接続要件には、仮想ネットワークで最初に満たされていないものがある可能性があります。 仮想ネットワーク内で使用したときに正常に動作させるには、Azure Redis Cache に次の項目すべてが必要になります。

* 世界各国の Azure Storage エンドポイントに対する発信ネットワーク接続 これには、Azure Redis Cache インスタンスと同じリージョンにあるエンドポイントと、**他の** Azure リージョンにあるストレージ エンドポイントが含まれます。 Azure Storage エンドポイントは、次の DNS ドメインで解決されます: *table.core.windows.net*、*blob.core.windows.net*、*queue.core.windows.net*、*file.core.windows.net*。 
* *ocsp.msocsp.com*、*mscrl.microsoft.com*、*crl.microsoft.com* に対する発信ネットワーク接続。 この接続は、SSL 機能をサポートするために必要です。
* 仮想ネットワークの DNS 構成は、前述したすべてのエンドポイントとドメインを解決できるようにする必要があります。 これらの DNS 要件を満たすには、仮想ネットワークの有効な DNS インフラストラクチャを構成し、保守します。
* 次の DNS ドメインで解決される次の Azure Monitoring エンドポイントに対する発信ネットワーク接続: shoebox2-black.shoebox2.metrics.nsatc.net、north-prod2.prod2.metrics.nsatc.net、azglobal-black.azglobal.metrics.nsatc.net、shoebox2-red.shoebox2.metrics.nsatc.net、east-prod2.prod2.metrics.nsatc.net、azglobal-red.azglobal.metrics.nsatc.net。

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-vnet"></a>VNET で自分のキャッシュの動作を確認するにはどうすればよいですか

>[!IMPORTANT]
>VNET にホストされている Azure Redis Cache インスタンスに接続している場合、キャッシュ クライアントは同じ VNET または VNET ピアリングが有効な VNET にある必要があります。 これには、テスト アプリケーションや診断 ping ツールが含まれます。 クライアント アプリケーションがどこでホストされているかに関係なく、クライアントのネットワーク トラフィックが Redis インスタンスに到達できるようネットワーク セキュリティ グループを構成する必要があります。
>
>

前のセクションで説明したようにポート要件を構成した後、次の手順を実行して、キャッシュが動作していることを確認できます。

- すべてのキャッシュ ノードを[再起動](cache-administration.md#reboot)します。 必要なすべてのキャッシュ依存関係 ([「受信ポートの要件」](cache-how-to-premium-vnet.md#inbound-port-requirements)と[「送信ポートの要件」](cache-how-to-premium-vnet.md#outbound-port-requirements)で説明) に到達できない場合、キャッシュは正常に再起動できません。
- キャッシュ ノードが再起動したら (Azure Portal のキャッシュの状態で報告されます)、次のテストを実行できます。
  - [tcping](https://www.elifulkerson.com/projects/tcping.php) を使って、キャッシュと同じ VNET 内にあるコンピューターからキャッシュ エンドポイントを ping します (ポート 6380 を使用)。 例: 
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    `tcping` ツールでポートが開いていることがレポートされる場合、キャッシュは VNET 内のクライアントからの接続に使用できます。

  - キャッシュに接続してキャッシュのいくつかの項目を追加および取得するテスト キャッシュ クライアント (StackExchange.Redis を使ったシンプルなコンソール アプリケーションにできます) を作成することでテストする方法もあります。 このサンプル クライアント アプリケーションをキャッシュと同じ VNET 内の VM にインストールし、実行してキャッシュへの接続性を確認します。


### <a name="when-trying-to-connect-to-my-redis-cache-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid"></a>VNET で自分の Redis キャッシュへの接続を試行すると、リモート証明書が無効であることを示すエラーが表示されるのはなぜですか

VNET で自分の Redis キャッシュへの接続を試行すると、次のような証明書の検証エラーが表示されます。

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

IP アドレスでホストに接続していることが原因になっている可能性があります。 ホスト名を使用することをお勧めします。 つまり、次を使用してください。     

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

次の接続文字列のような IP アドレスは使用しないでください。

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

DNS 名を解決できない場合、StackExchange.Redis クライアントによって指定される `sslHost` のような構成オプションが、クライアント ライブラリに含まれている場合があります。 このオプションによって、証明書の検証に使用されるホスト名をオーバーライドできます。 例: 

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>Standard キャッシュまたは Basic キャッシュで VNet を使用できますか
VNet は Premium キャッシュでのみ使用できます。

### <a name="why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others"></a>Redis Cache の作成が失敗するサブネットと成功するサブネットがあるのはなぜですか
Azure Redis Cache を Resource Manager VNet にデプロイする場合、キャッシュは、他の種類のリソースが含まれない専用サブネット内に存在する必要があります。 他のリソースが含まれる Resource Manager VNet サブネットに Azure Redis Cache をデプロイしようとすると、そのデプロイは失敗します。 新しい Redis Cache を作成する前に、サブネット内の既存のリソースを削除する必要があります。

使用可能な IP アドレスが十分にあれば、複数の種類のリソースをクラシック VNet にデプロイできます。

### <a name="what-are-the-subnet-address-space-requirements"></a>サブネット アドレス空間の要件には何がありますか
Azure は、各サブネット内で一部の IP アドレスを予約し、これらのアドレスを使用することはできません。 サブネットの最初と最後の IP アドレスは、Azure サービスで使用される 3 つ以上のアドレスと共に、プロトコル準拠に予約されます。 詳細については、「 [これらのサブネット内の IP アドレスの使用に関する制限はありますか](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Azure VNET インフラストラクチャによって使用される IP アドレスに加えて、サブネットの各 Redis インスタンスは、シャードごとに 2 つの IP アドレスおよびロード バランサー用に 1 つの追加 IP アドレスを使用します。 クラスター化されていないキャッシュは、1 つのシャードを持つと見なされます。

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>VNET でキャッシュをホストしている場合、キャッシュ機能はすべて動作しますか
キャッシュが VNET の一部である場合は、VNET のクライアントだけがキャッシュにアクセスできます。 そのため、次のキャッシュ管理機能は現時点では動作しません。

* Redis コンソール - Redis コンソールは、VNET の外部にあるローカル ブラウザーで実行されるため、キャッシュに接続できません。


## <a name="use-expressroute-with-azure-redis-cache"></a>Azure Redis Cache と ExpressRoute の使用

顧客は、 [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) 回線を自分の仮想ネットワーク インフラストラクチャに接続することで、オンプレミスのネットワークを Azure に拡張できます。 

既定では、新たに作成した ExpressRoute 回線では VNET での強制トンネリングは実行されません (既定のルートのアドバタイズ、0.0.0.0/0)。 その結果、発信インターネット接続は VNET から直接行うことができ、クライアント アプリケーションは、Azure Redis Cache を含む他の Azure エンドポイントに接続できます。

ただし、顧客の一般的な構成では、発信インターネット トラフィックを強制的にオンプレミスにフローさせる強制トンネリング (既定のルートのアドバタイズ) が使用されます。 このトラフィック フローは、送信トラフィックの場合には、Azure Redis Cache との接続を切断し、Azure Redis Cache インスタンスがその依存関係と通信できないように、オンプレミスでブロックされます。

解決策は、Azure Redis Cache を含むサブネット上で 1 つ (以上) のユーザー定義ルート (UDR) を定義することです。 UDR は、既定のルートに優先するサブネット固有のルートを定義します。

可能であれば、次の構成を使用することをお勧めします。

* ExpressRoute 構成は 0.0.0.0/0 をアドバタイズし、既定でオンプレミスのすべての発信トラフィックを強制的にトンネリングします。
* Azure Redis Cache を含むサブネットに適用される UDR は、たとえば、次ホップの種類を 'Internet' に設定することにより、パブリックなインターネットへの TCP/IP トラフィックの作業ルートを持つ 0.0.0.0/0 と定義します。

これらの手順を組み合わせた結果として、サブネット レベル UDR は ExpressRoute 強制トンネリングよりも優先されるので、Azure Redis Cache からの発信インターネット アクセスを確保できます。

ExpressRoute を使用したオンプレミス アプリケーションから Azure Redis Cache インスタンスへの接続は、パフォーマンス上の理由から一般的な使用シナリオではありません (最適なパフォーマンスを得るには、Azure Redis Cache クライアントを Azure Redis Cache と同じリージョンに配置する必要があります)。

>[!IMPORTANT] 
>ExpressRoute 構成でアドバタイズされたルートよりも優先するには、UDR に定義されているルートを詳細にする **必要があります** 。 以下の例では、0.0.0.0/0 の広域なアドレス範囲を使用しているので、より詳細なアドレス範囲を使用するルート アドバタイズで誤ってオーバーライドされる可能性があります。

>[!WARNING]  
>Azure Redis Cache は、**パブリック ピアリング パスからプライベート ピアリング パスにルートを正しくクロスアドバタイズしていない** ExpressRoute 構成ではサポートされません。 パブリック ピアリングが構成された ExpressRoute 構成は、大規模な Microsoft Azure の IP アドレス範囲について Microsoft からルート アドバタイズを受信します。 これらのアドレス範囲がプライベート ピアリング パスで誤ってクロスアドバタイズされている場合、Azure Redis Cache インスタンスのサブネットからのすべての発信ネットワーク パケットは、誤って顧客のオンプレミス ネットワーク インフラストラクチャに強制的にトンネリングされます。 このネットワーク フローでは、Azure Redis Cache が機能しません。 この問題を解決するには、パブリック ピアリング パスからプライベート ピアリング パスへのルートのクロスアドバタイズを停止します。


ユーザー定義ルートの背景情報については、この [概要](../virtual-network/virtual-networks-udr-overview.md)を参照してください。

ExpressRoute の詳細については、「[ExpressRoute の技術概要](../expressroute/expressroute-introduction.md)」を参照してください。

## <a name="next-steps"></a>次の手順
Premium キャッシュ機能をさらに使用する方法を学習します。

* [Azure Redis Cache Premium レベルの概要](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png

