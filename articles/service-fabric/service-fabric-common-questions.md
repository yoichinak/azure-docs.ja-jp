---
title: Microsoft Azure Service Fabric に関するよく寄せられる質問 |Microsoft ドキュメント
description: Service Fabric に関してよく寄せられる質問とその回答
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: ''
ms.assetid: 5a179703-ff0c-4b8e-98cd-377253295d12
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: chackdan
ms.openlocfilehash: d864a663604794a249b08a7c7be471c3abba32af
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38971538"
---
# <a name="commonly-asked-service-fabric-questions"></a>Service Fabric に関してよく寄せられる質問

Service Fabric で実行できる内容とその使用方法に関してよく寄せられる多数の質問があります。 このドキュメントでは、これらのよく寄せられる質問とその回答を示します。

## <a name="cluster-setup-and-management"></a>クラスターのセットアップと管理

### <a name="how-do-i-rollback-my-service-fabric-cluster-certificate"></a>Service Fabric クラスターの証明書はどのようにロールバックするのですか?

アプリケーションに対するアップグレードをロールバックするには、Service Fabric クラスターのクォーラムが変更をコミットする前に正常性エラーが検出される必要があります。コミットされた変更は、ロールフォワードのみが可能です。 監視対象外の破壊的な証明書の変更が行われた場合、クラスターを回復するために、エスカレーション エンジニアによる初めから終わりまでのカスタマー サポート サービスが必要になる場合があります。  [Service Fabric アプリケーションのアップグレード](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade?branch=master)は、[Application アップグレード パラメーター](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-parameters?branch=master)に適用され、ダウンタイムが発生しないアップグレードが確約されています。  推奨されるアプリケーション アップグレードである監視モードに従えば、更新ドメインを通した自動進行は正常性チェックの合格に基づいたものとなり、既定のサービスの更新が失敗した場合は自動的にロールバックが行われます。
 
お使いのクラスターが、Resource Manager テンプレートで旧来の証明書の Thumbprint プロパティをまだ活用している場合は、最新の機密管理機能を活用するため、[クラスターで使用するのを証明書の拇印から共通名に変更する](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-change-cert-thumbprint-to-cn)ことをお勧めします。

### <a name="can-i-create-a-cluster-that-spans-multiple-azure-regions-or-my-own-datacenters"></a>複数の Azure リージョンまたは自らのデータセンターにまたがるクラスターを作成することはできますか?

はい。 

Service Fabric コア クラスタリング テクノロジを使用すると、相互にネットワーク接続されているのであれば、世界中で実行されているコンピューターを組み合わせることができます。 ただし、そのようなクラスターの構築と実行は複雑になる可能性があります。

このシナリオに関心がある場合は、[Service Fabric Github 問題一覧](https://github.com/azure/service-fabric-issues)から、あるいはサポート窓口を通じて詳しいガイダンスを入手してください。 Service Fabric チームでは、このシナリオをさらに明確にし、ガイダンスや推奨事項を追加できるよう取り組んでいます。 

以下の点を考慮してください。 

1. 現在、Azure での Service Fabric クラスター リソースは、クラスターが構築される仮想マシン スケール セットと同じように、地域に限定されています。 つまり、地域的な障害が発生したとき、Azure Resource Manager または Azure Portal を使用してクラスターを管理できなくなることがあります。 クラスターが実行し続けていて、直接やり取りできる場合にも、そのような状況になることがあります。 また、現在の Azure では、地域にまたがって使用できる単独の仮想ネットワークは提供されていません。 つまり、Azure における複数リージョン クラスターは、[VM Scale Sets の各 VM のための Public IP Addresses](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine) か [Azure VPN Gateways](../vpn-gateway/vpn-gateway-about-vpngateways.md) を必要とします。 これらのネットワーク オプションにより、コストやパフォーマンスがさまざまな影響を受けます。ある程度まではアプリケーション設計にも影響があります。このため、このような環境を立ち上げるには、事前に注意深い分析と計画が必要です。
2. 特に、異なるクラウド プロバイダーやオンプレミス リソースと Azure など、複数の環境の_タイプ_が混在する場合、これらのマシンのメンテナンス、管理、監視は複雑になります。 そのような環境で実稼働ワークロードを実行する前には、クラスターとアプリケーションの両方のアップグレード、監視、管理、診断についてよく理解する必要があります。 Azure または自身のデータセンターでこのような問題を解決した経験がある場合は、Service Fabric クラスターを構築または実行する際にも同じソリューションを適用できると考えられます。 

### <a name="do-service-fabric-nodes-automatically-receive-os-updates"></a>Service Fabric ノードでは、OS の更新は自動的に受信されますか?

現時点ではそうではありませんが、これも共通の要求であり、今後 Azure で提供する予定です。

それまでの間は、Service Fabric ノードのオペレーティング システムにパッチを適用して最新状態にするための[アプリケーションを提供](service-fabric-patch-orchestration-application.md)しています。

OS の更新に伴う課題は、それを行うには通常はコンピューターを再起動する必要があり、それによって可用性が一時的に失われることです。 Service Fabric では可用性が失われたサービスのトラフィックを他のノードに自動的にリダイレクトするため、再起動自体は問題ではありません。 ただし、OS の更新がクラスター全体で連係されていなかった場合、多数のノードが一度にダウンする可能性があります。 このような再起動の同時発生によって、サービスの可用性が完全に失われるか、少なくとも (ステートフル サービス用の) 特定のパーティションの可用性が失われる可能性があります。

今後、更新ドメイン間で連係する完全に自動化された OS 更新ポリシーをサポートして、再起動やその他の予想外の障害が発生した場合でも可用性が維持されることを保証する予定です。

### <a name="can-i-use-large-virtual-machine-scale-sets-in-my-sf-cluster"></a>SF クラスターで大規模な仮想マシン スケール セットを使用できますか? 

**簡単な回答** - いいえ。 

**詳しい回答** - 大規模な仮想マシン スケール セットでは、最大 1000 台の VM インスタンスにスケーリングできますが、これは配置グループ (PG) を使用して実行されます。 障害ドメイン (FD) とアップグレード ドメイン (UD) は、Service Fabric が FD と UD を使用してサービス レプリカ/サービス インスタンスの配置を決定する配置グループ内でのみ整合性が維持されます。 FD 数と UD 数は配置グループ内でのみ比較可能であるため、SF で使用することはできません。 たとえば、PG1 の VM1 のトロポジが FD=0 であり、PG2 の VM9 のトポロジが FD=4 の場合、VM1 と VM2 が 2 つの異なるハードウェア ラック上にあることを意味するわけではないため、SF はこの例の FD 値を使用して配置を決定することはできません。

レベル 4 の負荷分散をサポートしていないなど、現在、大規模な仮想マシン スケール セットに関する問題がほかにもあります。 詳細については、[大規模なスケール セット](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md)に関する記事をご覧ください。



### <a name="what-is-the-minimum-size-of-a-service-fabric-cluster-why-cant-it-be-smaller"></a>Service Fabric クラスターの最小サイズとは何ですか?  もっと小さくできないのはなぜですか?

運用ワークロードを実行する Service Fabric クラスターでサポートされる最小サイズは、5 つのノードです。 開発/テスト シナリオでは、3 つのノードで構成されるクラスターがサポートされます。

これらの最小要件が存在する理由はは、Service Fabric クラスターでは、一連のステートフル システム サービス (ネーム サービスやフェールオーバー マネージャーなど) が実行されるためです。 クラスターにデプロイされているサービスと現在のホスト場所を追跡するこれらのサービスは、強い一貫性に依存しています。 この強い一貫性は、これらのサービスの状態に対する特定の更新の "*クォーラム*" を取得する能力に依存しています (クォーラムは特定のサービスに対するレプリカの strict majority (N/2 +1) を表します)。

これを背景として、考えられるいくつかのクラスター構成を検討してみます。

**1 つのノード**: 単一のノードが何らかの理由で失われることはクラスター全体が失われるためを意味するため、このオプションでは高可用性を提供できません。

**2 つのノード**: 2 つのノード (N = 2) 間でデプロイされるサービスのクォーラムは 2 です (2/2 + 1 = 2)。 1 つのレプリカが失われると、クォーラムを作成できなくなります。 サービスのアップグレードを実行するには、レプリカを一時的に停止させる必要があるため、これは有用な構成ではありません。

**3 つのノード**: 3 つのノード (N = 3) でも、クォーラムを作成するためのノードの要件は 2 つのままです (3/2 + 1 = 2)。 これは、個々のノードが失われた場合でも、クォーラムを保持できることを意味します。

3 つのノードのクラスター構成は、開発/テストでサポートされます。その理由は、アップグレードや個々のノードの障害が同時に発生しない限り、アップグレードを安全に実行でき、個々のノードの障害にも対応できるためです。 運用ワークロードでは、このような同時障害から回復する必要があるため、5 つのノードが必要です。

### <a name="can-i-turn-off-my-cluster-at-nightweekends-to-save-costs"></a>コストを節約するために夜間/週末にクラスターをオフにすることはできますか?

通常はできません。 Service Fabric は、一時的なローカル ディスクに状態を格納します。これは、仮想マシンが別のホストに移動されても、データは一緒に移動されないことを意味します。 通常の運用では、新しいノードは他のノードによって最新の状態になるため、これは問題にはなりません。 ただし、すべてのノードを停止し、後で再起動した場合は、ほとんどのノードが新しいホスト上で開始され、システムが回復できない状態になる可能性が非常に高くなります。

アプリケーションをデプロイする前にテスト用のクラスターを作成する場合は、[継続的インテグレーション/継続的配置パイプライン](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)の一部としてこれらのクラスターを作成することをお勧めします。


### <a name="how-do-i-upgrade-my-operating-system-for-example-from-windows-server-2012-to-windows-server-2016"></a>オペレーティング システムはどのようにアップグレードすればいいですか? (Windows Server 2012 を 2016 にする場合など)

Microsoft はエクスペリエンスの改善に取り組んでいますが、現時点ではお客様の責任でアップグレードを行っていただく必要があります。 クラスターの仮想マシンで OS イメージをアップグレードする場合は、一度に 1 つの VM で行う必要があります。 

### <a name="can-i-encrypt-attached-data-disks-in-a-cluster-node-type-virtual-machine-scale-set"></a>クラスター ノード タイプ (仮想マシン スケール セット) で接続されたデータ ディスクを暗号化することはできますか?
はい。  詳細については、「[接続されたデータ ディスクを備えたクラスターの作成](../virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks.md#create-a-service-fabric-cluster-with-attached-data-disks)」、[ディスクの暗号化 (PowerShell)](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-ps.md) に関するセクション、および[ディスクの暗号化 (CLI)](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-cli.md) に関するセクションを参照してください。

### <a name="what-are-the-directories-and-processes-that-i-need-to-exclude-when-running-an-anti-virus-program-in-my-cluster"></a>クラスターでウイルス対策プログラムを実行するときに除外する必要があるディレクトリとプロセス

| **ウイルス対策の対象外ディレクトリ** |
| --- |
| Program Files\Microsoft Service Fabric |
| FabricDataRoot (クラスター構成による) |
| FabricLogRoot (クラスター構成による) |

| **ウイルス対策の対象外プロセス** |
| --- |
| Fabric.exe |
| FabricHost.exe |
| FabricInstallerService.exe |
| FabricSetup.exe |
| FabricDeployer.exe |
| ImageBuilder.exe |
| FabricGateway.exe |
| FabricDCA.exe |
| FabricFAS.exe |
| FabricUOS.exe |
| FabricRM.exe |
| FileStoreService.exe |
 
## <a name="application-design"></a>アプリケーションの設計

### <a name="whats-the-best-way-to-query-data-across-partitions-of-a-reliable-collection"></a>Reliable Collection のパーティション全体のデータを照会する最善の方法は何ですか?

Reliable collection は、通常は、パフォーマンスとスループットを高めるためのスケールアウトを実行できるように[パーティション分割](service-fabric-concepts-partitioning.md)されます。 これは、特定のサービスの状態が、数十から数百台のコンピューターに分散されることを意味します。 このデータ セット全体に対して操作を実行するには、いくつかのオプションがあります。

- 別のサービスのすべてのパーティションを照会して必要なデータを引き出すサービスを作成します。
- 別のサービスのすべてのパーティションからデータを受信できるサービスを作成します。
- 各サービスから外部ストアにデータを定期的にプッシュします。 この方法は、実行する照会が中核となるビジネス ロジックの一部ではない場合のみに適しています。


### <a name="whats-the-best-way-to-query-data-across-my-actors"></a>アクター全体のデータを照会する最善の方法は何ですか。

アクターは、状態とコンピューティングに依存しないように設計されているため、実行時にアクターの状態を広範に照会することは推奨されていません。 アクターの状態のフル セットを照会する必要がある場合は、次のいずれかを検討してください。

- アクター サービスをステートフルな信頼できるサービスに置き換えて、すべてのアクターからすべてのデータを収集するネットワーク要求の数がサービス内のパーティションの数と同じになるようにします。
- 状態を外部ストアに定期的にプッシュして簡単に照会できるようにアクターを設計します。 上記と同じように、この方法は、実行する照会が実行時の動作で必須でない場合のみに実行可能です。

### <a name="how-much-data-can-i-store-in-a-reliable-collection"></a>Reliable Collection には、どのくらいの量のデータを格納できますか?

Reliable Services は通常はパーティション分割されるため、格納できる量は、クラスター内のコンピューターの台数とこれらのコンピューターで使用できるメモリの量によってのみ制限されます。

たとえば、サービスに 100 個のパーティションと 3 つのレプリカがある Reliable Collection があり、平均サイズが 1 KB のオブジェクトを格納するとします。 ここで、クラスターが 10 台のコンピューターで構成され、各コンピューターのメモリが 16 GB であるとします。 単純かつ控えめに見積もるために、オペレーティング システム、システム サービス、Service Fabric ランタイム、および使用するサービスで 6 GB が消費され、各コンピューターで残りの 10 GB、つまりクラスターで 100 GB を使用できるものと想定します。

各オブジェクトは 3 回格納される必要があること (1 回はプライマリに、2 回はレプリカに) に留意すると、容量全部を使用して運用した場合は、約 3,500 万個のオブジェクトをコレクションに格納するのに十分なメモリがあります。 ただし、障害ドメインとアップグレード ドメインが同時に失われた場合の回復力を備えておくことが推奨されます。これは容量の約 1/3 に当たるため、この数値は約 2,300 万個に減少します。

この計算では、以下も想定されていることに注意してください。

- パーティション間のデータの分散はほぼ一定であるか、クラスター リソース マネージャーに負荷メトリックを報告すること。 既定では、Service Fabric は、レプリカの数に基づいて負荷を分散します。 前の例では、クラスター内の各ノードに 10 個のプライマリ レプリカと 20 個のセカンダリ レプリカが配置されます。 パーティション間で負荷が均等に分散される場合は問題はありません。 負荷が均等でない場合は、負荷をレポートして、小さなレプリカがリソース マネージャーによって 1 つにまとめられ、大きなレプリカが個々のノードでより多くのメモリを消費できるようにする必要があります。

- 問題の Reliable Service が、クラスターで格納状態にある唯一のサービスであること。 複数のサービスをクラスターにデプロイできるため、実行する必要があるリソースとその状態の管理を意識する必要があります。

- クラスター自体が拡大も縮小もしないこと。 コンピューターが追加された場合、Service Fabric は、追加された容量を活用するためにレプリカの再分散を実行します。個々のレプリカは複数のコンピューターにまたがることはできないため、この動作はコンピューターの数がサービス内のパーティションの数を上回るまで続けられます。 逆に、コンピューターを削除することでクラスターのサイズが減少した場合、レプリカはより緊密にパックされ、全体の容量が小さくなります。

### <a name="how-much-data-can-i-store-in-an-actor"></a>アクターには、どのくらいの量のデータを格納できますか?

Reliable Services と同じように、アクター サービスに格納できるデータの量は、ディスク領域の合計とクラスター内のノードで使用できるメモリによってのみ制限されます。 ただし、個々のアクターは、小さな分量の状態とそれに関連付けられたビジネス ロジックをカプセル化するために使用すると、最も効果があります。 原則として、個々のアクターには、キロバイト単位で測定される状態を格納してください。

## <a name="other-questions"></a>どの他の質問

### <a name="how-does-service-fabric-relate-to-containers"></a>Service Fabric はコンテナーとどのように関連していますか?

コンテナーは、サービスとそれに依存するものをパケージ化して、それらがすべての環境で一貫性をもって実行され、1 台のコンピューター上で隔離された方法で運用できるようにします。 Service Fabric には、[コンテナーにパッケージ化されたサービス](service-fabric-containers-overview.md)も含めて、サービスをデプロイして管理する方法が用意されています。

### <a name="are-you-planning-to-open-source-service-fabric"></a>Service Fabric をオープン ソース化する予定はありますか?

GitHub では Service Fabric のオープン ソース化された部分 ([Reliable Services フレームワーク](https://github.com/Azure/service-fabric-services-and-actors-dotnet)、[Reliable Actors フレームワーク](https://github.com/Azure/service-fabric-services-and-actors-dotnet)、[ASP.NET Core 統合ライブラリ](https://github.com/Azure/service-fabric-aspnetcore)、[Service Fabric Explorer](https://github.com/Azure/service-fabric-explorer)、[Service Fabric CLI](https://github.com/Azure/service-fabric-cli)) が提供されており、これらのプロジェクトにはコミュニティの皆さんにも参加していただいています。 

Service Fabric ラインタイムをオープン ソース化する予定であることは、[最近発表](https://blogs.msdn.microsoft.com/azureservicefabric/2018/03/14/service-fabric-is-going-open-source/)しました。 現時点では、GitHub には、Linux のビルドおよびテスト ツールを含む、[Service Fabric リポジトリ](https://github.com/Microsoft/service-fabric/)がアップされており、このリポジトリを複製し、Linux 用の Service Fabric をビルドして、基本的なテストを実行し、問題を開き、プル要求を送信することができます。 完全な CI 環境と共に、Windows ビルド環境の移行にも全力で取り組んでいます。

詳しくは、[Service Fabric ブログ](https://blogs.msdn.microsoft.com/azureservicefabric/)での発表をご覧ください。

## <a name="next-steps"></a>次の手順

- [Service Fabric の中心概念とベスト プラクティスを学習する](https://mva.microsoft.com/en-us/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965)
