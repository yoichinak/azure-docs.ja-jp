---
title: 'Azure Site Recovery: よく寄せられる質問 | Microsoft Docs'
description: この記事では、Azure Site Recovery に関してよく寄せられる質問について説明します。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: 41fa742571b94505588172b94ea91f196e212a4c
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2018
ms.locfileid: "37920965"
---
# <a name="azure-site-recovery-frequently-asked-questions-faq"></a>Azure Site Recovery: よく寄せられる質問 (FAQ)
この記事には、Azure Site Recovery に関してよく寄せられる質問が含まれます。 この記事の内容について質問がある場合は、 [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)に投稿してください。

## <a name="general"></a>全般
### <a name="what-does-site-recovery-do"></a>Site Recovery は何をするものですか。
Site Recovery は、リージョン間の Azure VM のレプリケーション、オンプレミスの仮想マシンと物理サーバーの Azure へのレプリケーション、およびオンプレミスのマシンのセカンダリ データセンターへのレプリケーションを調整および自動化することで、ビジネス継続性とディザスター リカバリー (BCDR) 戦略に貢献します。 [詳細情報](site-recovery-overview.md)。

### <a name="what-can-site-recovery-protect"></a>Site Recovery が保護できるものは何ですか。
* **Azure VM**: Site Recovery は、サポート対象の Azure VM で実行されているすべてのワークロードをレプリケートできます。
* **Hyper-V 仮想マシン**: Site Recovery は、Hyper-V 仮想マシンで実行されているすべてのワークロードを保護できます。
* **物理サーバー**: Site Recovery は、Windows または Linux を実行する物理サーバーを保護できます。
* **VMware 仮想マシン**: Site Recovery は、VMware VM で実行されているすべてのワークロードを保護できます。



### <a name="can-i-replicate-azure-vms"></a>Azure VM をレプリケートできますか。
はい、サポートされている Azure VM は Azure リージョン間でレプリケートできます。 [詳細情報](site-recovery-azure-to-azure.md)。

### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>Site Recovery でレプリケーションを調整するには、Hyper-V で何が必要ですか。
Hyper-V ホスト サーバーに必要なものは、デプロイ シナリオによって異なります。 以下の Hyper-V に関する前提条件を参照してください。

* [Hyper-V VM を (VMM なしで) Azure にレプリケートする](site-recovery-hyper-v-site-to-azure.md)
* [Hyper-V VM を (VMM を使って) Azure にレプリケートする](site-recovery-vmm-to-azure.md)
* [Hyper-V VM をセカンダリ データセンターにレプリケートする](site-recovery-vmm-to-vmm.md)
* セカンダリ データセンターにレプリケートする場合は、「 [Hyper-V VM 用のサポートされているゲスト オペレーティング システム](https://technet.microsoft.com/library/mt126277.aspx)」をご覧ください。
* Azure にレプリケーションする場合、Site Recovery は [Azure でサポートされている](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)すべてのゲスト オペレーティング システムをサポートします。

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>クライアント オペレーティング システムで Hyper-V が実行されているときに VM を保護できますか。
いいえ。VM は、サポートされている Windows Server マシンで実行されている Hyper-V ホスト サーバーに配置されている必要があります。 クライアント コンピューターを保護する必要がある場合は、物理マシンとして [Azure](site-recovery-vmware-to-azure.md)、または[セカンダリ データセンター](site-recovery-vmware-to-vmware.md)にレプリケートできます。

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Site Recovery で保護できるワークロードは何ですか。
Site Recovery を使用すると、サポートされている VM または物理サーバーで実行されているほとんどのワークロードを保護できます。 また、アプリケーションに対応したレプリケーションもサポートしているため、アプリをインテリジェントな状態に復元できます。 Site Recovery は、SharePoint、Exchange、Dynamics、SQL Server、Active Directory などの Microsoft アプリケーションと統合し、Oracle、SAP、IBM、Red Hat などの主要なベンダーと緊密に連携します。 [詳細情報](site-recovery-workload.md) を参照してください。

### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Hyper-V ホストを VMM クラウドに配置する必要がありますか。
セカンダリ データセンターにレプリケートする場合は、VMM クラウドの Hyper-V ホスト サーバーに Hyper-V VM を配置する必要があります。 Azure にレプリケートする場合は、VMM クラウド内にあるかどうかに関係なく、VM をレプリケートできます。 Azure への Hyper-V のレプリケーションについて詳しくは、[こちら](tutorial-hyper-v-to-azure.md)をご覧ください。

### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>VMM サーバーが 1 つしかない場合、VMM で Site Recovery をデプロイできますか。

はい。 VMM クラウド内の Hyper-V サーバー上にある VM を Azure にレプリケートすることも、同じサーバー上の VMM クラウド間でレプリケートすることもできます。 オンプレミス間のレプリケーションには、プライマリ サイトとセカンダリ サイトの両方に VMM サーバーを配置することをお勧めします。  

### <a name="what-physical-servers-can-i-protect"></a>どの物理サーバーを保護できますか。
Windows および Linux を実行している物理サーバーを、Azure またはセカンダリ サイトにレプリケートできます。 [Azure へのレプリケーション](vmware-physical-azure-support-matrix.md#replicated-machines)、および[セカンダリ サイトへのレプリケーション](vmware-physical-secondary-support-matrix.md#replicated-vm-support)のための要件について説明します。


オンプレミスのサーバーがダウンした場合には、物理サーバーは Azure では VM として実行されることに注意してください。 オンプレミスの物理サーバーへのフェールバックは、現在サポートされていません。 物理保護されているマシンの場合は、VMware 仮想マシンにのみフェールバックできます。

### <a name="what-vmware-vms-can-i-protect"></a>どの VMware VM を保護できますか。

VMware VM を保護するには、vSphere ハイパーバイザーに加え、VMware ツールが実行されている仮想マシンが必要です。 また、VMware vCenter サーバーでハイパーバイザーを管理することをお勧めします。 [Azure へのレプリケーション](vmware-physical-azure-support-matrix.md#replicated-machines)、または[セカンダリ サイトへのレプリケーション](vmware-physical-secondary-support-matrix.md#replicated-vm-support)のための要件について説明します。


### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Site Recovery を使用してブランチ オフィスのディザスター リカバリーを管理できますか。
はい。 ブランチ オフィスでのレプリケーションとフェールオーバーを調整するために Site Recovery を使用すると、1 か所ですべてのブランチ オフィスのワークロードの統一されたオーケストレーションとビューが得られます。 ブランチ オフィスに出向くことなく、フェールオーバーを容易に実行し、本社からすべてのブランチのディザスター リカバリーを管理できます。

## <a name="pricing"></a>価格
価格に関する質問については、「[Site Recovery の価格](https://azure.microsoft.com/en-in/pricing/details/site-recovery/)」のよくあるご質問をご覧ください。

## <a name="security"></a>セキュリティ
### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Site Recovery サービスにレプリケーション データが送信されますか。
いいえ。Site Recovery は、レプリケートされたデータをインターセプトすることも、仮想マシンまたは物理サーバーでの実行内容に関するどのような情報を持つこともありません。
レプリケーション データは、オンプレミスの Hyper-V ホスト、VMware ハイパーバイザー、物理サーバーと Azure Storage またはセカンダリ サーバーとの間でやり取りされます。 Site Recovery には、これらのデータをインターセプトする能力はありません。 レプリケーションとフェールオーバーを調整するために必要なメタデータのみが、Site Recovery サービスに送信されます。  

Site Recovery は ISO 27001:2013、27018、HIPAA、DPA の認証を受けており、SOC2 および FedRAMP JAB の評価が進行中です。

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>コンプライアンスのため、オンプレミスのメタデータであっても、同じ地理的リージョン内に維持される必要があります。 Site Recovery は役立ちますか。
はい。 リージョンに Site Recovery コンテナーを作成すると、レプリケーションとフェールオーバーを有効にし、調整するために必要なすべてのメタデータが、そのリージョンの地理的境界内に維持されます。

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery はレプリケーションを暗号化しますか。
オンプレミスのサイト間で仮想マシンと物理サーバーをレプリケートする場合には、転送中の暗号化がサポートされます。 Azure に仮想マシンと物理サーバーをレプリケートする場合には、転送中の暗号化と [(Azure での) 保管データの暗号化](https://docs.microsoft.com/azure/storage/storage-service-encryption)の両方がサポートされます。

## <a name="replication"></a>レプリケーション

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>Azure にサイト間 VPN 経由でレプリケートできますか。
Azure Site Recovery は、パブリック エンドポイント経由で Azure ストレージ アカウントにデータをレプリケートします。 レプリケーションは、サイト間 VPN 経由では実行されません。 サイト間 VPN は、Azure 仮想ネットワークで作成できます。 これは Site Recovery のレプリケーションに干渉しません。

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>ExpressRoute を使用して Azure に仮想マシンをレプリケートできますか。
はい。[ExpressRoute を使用して](concepts-expressroute-with-site-recovery.md)オンプレミスの仮想マシンを Azure にレプリケートできます。 Azure Site Recovery は、パブリック エンドポイント経由で Azure ストレージ アカウントにデータをレプリケートします。 Site Recovery のレプリケーションに ExpressRoute を使うには、[パブリック ピアリング](../expressroute/expressroute-circuit-peerings.md#azure-public-peering)または [Microsoft ピアリング](../expressroute/expressroute-circuit-peerings.md#microsoft-peering)を設定する必要があります。 Microsoft ピアリングは、レプリケーション用に推奨されるルーティング ドメインです。 仮想マシンが Azure 仮想ネットワークにフェールオーバーされた後は、その Azure 仮想ネットワークでセットアップされた[プライベート ピアリング](../expressroute/expressroute-circuit-peerings.md#azure-private-peering)を使用して、それらのマシンにアクセスできます。 プライベート ピアリングを介したレプリケーションはサポートされていません。

### <a name="are-there-any-prerequisites-for-replicating-virtual-machines-to-azure"></a>仮想マシンを Azure にレプリケートするための前提条件はありますか。
Azure にレプリケートする [VMware VM](vmware-physical-azure-support-matrix.md#replicated-machines) や [Hyper-V VM](hyper-v-azure-support-matrix.md#replicated-vms) は、Azure 要件に適合している必要があります。

Azure ユーザー アカウントには、新しい仮想マシンを Azure にレプリケートできるようにするための特定の[アクセス許可](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)が必要です。

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Hyper-V 第 2 世代仮想マシンを Azure にレプリケートできますか。
はい。 Site Recovery は、フェールオーバー中に第 2 世代から第 1 世代への変換を行います。 フェールバック時に、マシンは第 2 世代に変換し直されます。 詳細については、[こちら](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)を参照してください。

### <a name="if-i-replicate-to-azure-how-do-i-pay-for-azure-vms"></a>Azure にレプリケートした場合、Azure VM の支払いはどうなりますか。
通常のレプリケーション中に、データが地理冗長 Azure Storage にレプリケートされるため、Azure IaaS 仮想マシンの料金は何も支払う必要はありません (すばらしいメリット)。 Azure へのフェールオーバーを実行すると、Site Recovery により、Azure IaaS 仮想マシンが自動的に作成されます。その後、Azure で消費したコンピューティング リソースについて、料金が請求されます。

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>SDK を使用して Site Recovery のシナリオを自動化できますか。
はい。 Rest API、PowerShell、Azure SDK のいずれかを使用して、Site Recovery ワークフローを自動化することができます。 PowerShell を使用した Site Recovery のデプロイについて、現在サポートされているシナリオは次のとおりです。

* [VMM クラウドの Hyper-V VM を Azure PowerShell Resource Manager にレプリケートする](hyper-v-vmm-powershell-resource-manager.md)
* [VMM なしの Hyper-V VM を Azure PowerShell Resource Manager にレプリケートする](hyper-v-azure-powershell-resource-manager.md)
* [PowerShell Resource Manager を使用して Azure へ VMware をレプリケートする](vmware-azure-disaster-recovery-powershell.md)

### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-do-i-need"></a>Azure にレプリケートする場合、どの種類のストレージ アカウントが必要ですか。
LRS または GRS ストレージ アカウントが必要です。 地域的障害が発生した場合やプライマリ リージョンが復旧できない場合にデータの復元性を確保できるように、GRS をお勧めします。 アカウントは、Recovery Services コンテナーと同じリージョンにある必要があります。 Azure Portal に Site Recovery をデプロイする場合、VMware VM、Hyper-V VM、および物理サーバーのレプリケーションで Premium Storage がサポートされます。

### <a name="how-often-can-i-replicate-data"></a>どのくらいの頻度でデータをレプリケートできますか。
* **Hyper-V:** Hyper-V VM は 30 秒 (Premium Storage を除く)、5 分、または 15 分ごとにレプリケートできます。 SAN レプリケーションを設定した場合、レプリケーションは同期されます。
* **VMware と物理サーバー:** レプリケーションの頻度はここでは関係ありません。 レプリケーションは継続的です。

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>既存の復旧サイトから第 3 のサイトにレプリケーションを拡張することができますか。
拡張またはチェーン レプリケーションはサポートされていません。 [フィードバック フォーラム](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication)でこの機能を要求してください。

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Azure に初めてレプリケートする際に、オフライン レプリケーションを行うことができますか。
これはサポートされていません。 [フィードバック フォーラム](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)でこの機能を要求してください。

### <a name="can-i-exclude-specific-disks-from-replication"></a>レプリケーションから特定のディスクを除外することはできますか。
これは、Azure Portal を使用して Azure に VMware VM と Hyper-V VM をレプリケートする場合にサポートされます。

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>ダイナミック ディスクを持つ仮想マシンをレプリケートできますか。
ダイナミック ディスクは Hyper-V 仮想マシンのレプリケート時にサポートされますが、 Azure への VMware VM および物理マシンのレプリケート時にもサポートされます。 オペレーティング システム ディスクはベーシック ディスクである必要があります。

### <a name="can-i-add-a-new-machine-to-an-existing-replication-group"></a>既存のレプリケーション グループに新しいマシンを追加することはできますか。
既存のレプリケーション グループへの新しいマシンの追加はサポートされています。 これを行うには、([レプリケートされたアイテム] ブレードから) レプリケーション グループを選択し、レプリケーション グループのコンテキスト メニューを右クリックまたは選択して、適切なオプションを選択します。

![レプリケーション グループへの追加](./media/site-recovery-faq/add-server-replication-group.png)

### <a name="can-i-throttle-bandwidth-allotted-for-hyper-v-replication-traffic"></a>Hyper-V レプリケーション トラフィックに割り当てられた帯域幅を調整できますか。
はい。 帯域幅調整の詳細については、デプロイに関する記事をご覧ください。

* [VMware VM と物理サーバーをレプリケートするためのキャパシティ プランニング](site-recovery-plan-capacity-vmware.md)
* [Azure に Hyper-V VM をレプリケートするためのキャパシティ プランニング](site-recovery-capacity-planning-for-hyper-v-replication.md)

## <a name="failover"></a>フェールオーバー
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-virtual-machines-after-failover"></a>Azure にフェールオーバーする場合、フェールオーバー後に Azure の仮想マシンにどうしたらアクセスできますか。
Azure VM には、セキュリティで保護されたインターネット接続、サイト間 VPN、または Azure ExpressRoute 経由でアクセスできます。 接続するにはさまざまこと準備する必要があります。 [詳細情報](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Azure にフェールオーバーする場合、Azure はどのようにデータの回復力を確認しますか。
Azure は復元するように設計されています。 Site Recovery は、必要に応じて、Azure SLA に従ってセカンダリ Azure データセンターにフェールオーバーする機能を既に備えています。 これが発生した場合には、お客様のメタデータとコンテナーは、お客様が選択したコンテナーと同じリージョン内に保持されます。  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>2 つのデータ センター間でレプリケートしている場合、プライマリ データセンターに予期しない障害が発生するとどうなりますか。
セカンダリ サイトから計画外のフェールオーバーをトリガーすることができます。 Site Recovery は、フェールオーバーを実行するためにプライマリ サイトからの接続を必要としません。

### <a name="is-failover-automatic"></a>フェールオーバーは自動で行われますか。
フェールオーバーは自動では行われません。 ポータルで 1 回クリックするだけでフェールオーバーを開始できます。または [Site Recovery PowerShell](/powershell/module/azurerm.siterecovery) を使用してフェールオーバーをトリガーすることもできます。 Site Recovery ポータルではフェールバックを簡単な操作で行えます。

自動化するには、オンプレミスの Orchestrator または Operations Manager を使用すると、仮想マシンのエラーを監視し、SDK を使用してフェールオーバーをトリガーできます。

* [こちら](site-recovery-create-recovery-plans.md) を参照してください。
* [詳細については、こちらを参照してください。](site-recovery-failover.md) をご覧ください。
* [詳細については、こちらを参照してください。](site-recovery-failback-azure-to-vmware.md) を参照してください

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-failover-back-to-a-different-host"></a>オンプレミスのホストが応答しない場合やクラッシュした場合は、別のホストにフェールバックできますか。
はい。alternate location recovery (別の場所への復旧) を使用すると、Azure から別のホストにフェールバックできます。 このオプションの詳細については、以下の VMware および Hyper-V 仮想マシンへのリンクを参照してください。

* [VMware 仮想マシン用](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [Hyper-V 仮想マシン用](hyper-v-azure-failback.md#perform-failback)

## <a name="service-providers"></a>サービス プロバイダー
### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>当社はサービス プロバイダーですが、 Site Recovery は、専用および共有のインフラストラクチャ モデルで動作しますか。
はい。Site Recovery は、専用および共有のインフラストラクチャ モデルの両方をサポートしています。

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>サービス プロバイダーの場合、テナントの ID は Site Recovery サービスと共有されますか。
いいえ。 テナント ID は匿名のままです。 テナントは Site Recovery ポータルにアクセスする必要はありません。 ポータルを操作するのは、サービス プロバイダーの管理者だけです。

### <a name="will-tenant-application-data-ever-go-to-azure"></a>テナントのアプリケーション データは Azure に送信されますか。
サービス プロバイダーが所有するサイト間でレプリケートするときに、アプリケーション データが Azure に送信されることはありません。 データは転送中に暗号化され、サービス プロバイダーのサイト間で直接レプリケートされます。

Azure にレプリケートする場合、アプリケーション データは Site Recovery サービスではなく、Azure ストレージに送信されます。 データは、転送中に暗号化され、暗号化された状態のまま Azure に残ります。

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>テナントに Azure サービスの請求書が届きますか。
いいえ。 Azure の請求関係は、サービス プロバイダーとの直接的な関係です。 サービス プロバイダーは、そのテナントに対して固有の請求書を生成する必要があります。

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Azure にレプリケートする場合、仮想マシンを Azure で常時実行する必要がありますか。
いいえ、データは、サブスクリプションの Azure ストレージ アカウントに複製されます。 テスト フェールオーバー (障害復旧訓練) または実際のフェールオーバーを実行すると、Site Recovery により、サブスクリプションで仮想マシンが自動的に作成されます。

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Azure にレプリケートする際に、テナント レベルでの隔離は保証されますか。
はい。

### <a name="what-platforms-do-you-currently-support"></a>現在、どのプラットフォームがサポートされていますか。
Azure パック、クラウド プラットフォーム システム、および System Center ベース (Hyper 2012 以降) のデプロイメントがサポートされています。 [詳細情報](https://technet.microsoft.com/library/dn850370.aspx) をご覧ください。

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>単一の Azure パックおよび単一の VMM サーバーのデプロイメントもサポートされますか。
はい。Hyper-V 仮想マシンを Azure にレプリケートできます。または、サービス プロバイダー サイト間でレプリケートできます。  サービス プロバイダー サイト間でレプリケートする場合は、Azure Runbook 統合が使用できないことに注意してください。

## <a name="next-steps"></a>次の手順
* [Azure Site Recovery の概要](site-recovery-overview.md)
* こちら [Site Recovery アーキテクチャ](site-recovery-components.md)  
