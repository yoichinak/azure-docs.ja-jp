---
title: Contoso Linux サービスデスク アプリの Azure および Azure MySQL へのリホスト | Microsoft Docs
description: Contoso が、オンプレミス Linux アプリを Azure VM と Azure MySQL に移行することによって、どのようにこのアプリをリホストするかを説明します。
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: fbb70bd20b89bb1b711630ba54fe31806292385c
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39002230"
---
# <a name="contoso-migration-rehost-an-on-premises-linux-app-to-azure-vms-and-azure-mysql"></a>Contoso の移行: オンプレミス Linux アプリを Azure VM と Azure MySQL にリホストする

この記事では、オンプレミスの 2 階層 Linux サービスデスク アプリ (osTicket) を Azure VM と Azure MySQL に移行することによって、Contoso がどのようにこのアプリをリホストするかを示します。

このドキュメントは、架空の会社 Contoso がオンプレミス リソースを Microsoft Azure クラウドに移行する方法を説明するシリーズ記事の 1 つです。 このシリーズには、背景情報とシナリオが含まれており、移行インフラストラクチャのセットアップ方法や、さまざまな種類の移行を実施する方法を具体的に説明しています。 シナリオが複雑になってきているため、徐々に記事が追加される予定です。

**記事** | **詳細** | **状態**
--- | --- | ---
[記事 1: 概要](contoso-migration-overview.md) | Contoso の移行戦略、記事シリーズ、および使用するサンプル アプリの概要を示します。 | 使用可能
[記事 2: Azure インフラストラクチャのデプロイ](contoso-migration-infrastructure.md) | Contoso が移行に備えて、オンプレミスと Azure のインフラストラクチャをどのように準備するかを説明します。 Contoso のすべての移行シナリオで、同じインフラストラクチャが使用されます。 | 使用可能
[記事 3: オンプレミス リソースの評価](contoso-migration-assessment.md)  | Contoso が、VMware 上で実行されるオンプレミスの 2 階層アプリ、SmartHotel の評価をどのように実行するかを示します。 アプリの VM については [Azure Migrate](migrate-overview.md) サービスで、アプリの SQL Server データベースについては [Azure Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) で評価を行います。 | 使用可能
[記事 4: Azure VM および SQL Managed Instance へのリホスト](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso が SmartHotel アプリを Azure に移行する方法を説明します。 SQL Managed Instance に移行するために、アプリの Web VM は [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) を使用して移行し、アプリのデータベースは [Azure Database Migration](https://docs.microsoft.com/azure/dms/dms-overview) サービスを使用して移行します。 | 使用可能
[記事 5: Azure VM へのリホスト](contoso-migration-rehost-vm.md) | Contoso が Site Recovery サービスを使用して、どのように SmartHotel アプリを Azure IaaS VM を移行するかを示します。 | 使用可能
[記事 6: Azure VM および SQL Server 可用性グループへのリホスト](contoso-migration-rehost-vm-sql-ag.md) | Contoso が SmartHotel アプリを移行する方法を示します。 Contoso は、Site Recovery を使用してアプリの VM を移行し、Database Migration サービスを使用してアプリのデータベースを SQL Server 可用性グループに移行します。 | 使用可能
[記事 7: Linux アプリの Azure VM へのリホスト](contoso-migration-rehost-linux-vm.md) | Contoso が、Azure Site Recovery を使用して、どのように osTicket Linux アプリを Azure IaaS VM 移行するかを示します。 | 使用可能
記事 8: Linux アプリの Azure VM および Azure MySQL Server へのリホスト | Contoso がどのように osTicket Linux アプリを移行するかを具体的に説明します。 VM の移行には Site Recovery を使用し、Azure MySQL Server インスタンスへの移行には MySQL Workbench を使用します。 | この記事の内容は次のとおりです。
[記事 9: Azure Web Apps と Azure SQL Database でのアプリのリファクター](contoso-migration-refactor-web-app-sql.md) | Contoso が SmartHotel アプリを Azure Web App に移行して、アプリ データベースを Azure SQL Server インスタンスに移行する方法を示します。 | 使用可能
[記事 10: Azure Web Apps と Azure MySQL での Linux アプリのリファクター](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso が Linux osTicket アプリを複数のサイトの (継続的デリバリーのために GitHub と統合された) Azure Web Apps に移行する方法を示します。 Contoso は、アプリ データベースを Azure MySQL インスタンスに移行します。 | 使用可能
[記事 11: VSTS での TFS のリファクター](contoso-migration-tfs-vsts.md) | Contoso がオンプレミスの Team Foundation Server (TFS) の展開を Azure の Visual Studio Team Services (VSTS) に移行する方法を示します。 | 使用可能
[記事 12: Azure コンテナーと Azure SQL Database でのアプリの再構築](contoso-migration-rearchitect-container-sql.md) | Contoso が SmartHotel アプリを Azure に移行して再構築する方法を示します。 Contoso は、アプリの Web 階層を Windows コンテナーとして再構築し、Azure SQL Database 内のアプリ データベースを再構築します。 | 使用可能
[記事 13: Azure でのアプリのリビルド](contoso-migration-rebuild.md) | Contoso が Azure のさまざまな機能とサービス (App Services、Azure Kubernetes、Azure Functions、Cognitive サービス、Cosmos DB など) を使用して SmartHotel アプリをリビルドする方法を示します。 | 使用可能


この記事で Contoso は、2 階層の Linux Apache MySQL PHP (LAMP) サービスデスク アプリ (osTicket) を Azure に移行します。 このオープンソース アプリを使用したい場合は、[GitHub](https://github.com/osTicket/osTicket) からダウンロードできます。



## <a name="business-drivers"></a>ビジネス ドライバー

IT リーダーシップ チームは、ビジネス パートナーと密接に連絡を取り合い、彼らが何を達成しようとしているのかを理解しました。

- **ビジネスの成長への対応**: Contoso は成長を続けています。その結果、オンプレミスのシステムとインフラストラクチャに負荷がかかっています。
- **リスクの限定**: サービスデスク アプリは、Contoso のビジネスにとって非常に重要です。 Contoso はこれを、リスクを負うことなく Azure に移行したいと考えています。
- **拡張**: Contoso は、今すぐアプリに変更を加えることは望んでいません。 安定した状態を維持したいだけなのです。


## <a name="migration-goals"></a>移行の目標

Contoso クラウド チームは、最適な移行方法を決定するために、この移行の目標を明確にしました。

- 移行後の Azure 内のアプリは、現在のオンプレミス VMWare 環境と同じパフォーマンスを発揮できる必要があります。  このアプリは、オンプレミスの場合と同様に、クラウド内にあっても非常に重要であり続けます。 
- Contoso は、このアプリへの投資は望んでいません。  これはビジネスにとって重要なものですが、現状のまま確実にクラウドに移すことだけを望んでいます。
- Windows アプリの移行を何件か完了したら、Contoso は Azure で Linux ベースのインフラストラクチャを使用する方法を学びたいと考えています。
- Contoso は、アプリケーションがクラウドに移された後、データベース管理タスクを最小限にしたいと考えています。

## <a name="proposed-architecture"></a>提案されたアーキテクチャ

このシナリオでは:

- アプリは 2 つの VM (OSTICKETWEB および OSTICKETMYSQL) にわたって階層化されます。
- VM は、VMware ESXi ホスト **contosohost1.contoso.com** (バージョン 6.5) 上に配置されます。
- VMware 環境は、VM 上で実行中の vCenter Server 6.5 (**vcenter.contoso.com**) によって管理されています。
- Contoso にはオンプレミスのデータセンター (contoso-datacenter) があり、そこにオンプレミスのドメイン コントローラー (**contosodc1**) が含まれています。
- OSTICKETWEB 上の Web 階層アプリは、Azure IaaS VM に移行されます。
- アプリのデータベースは、Azure Database for MySQL の PaaS サービスに移行されます。
- 運用のワークロードを移行しようとしているため、リソースは、運用リソース グループ **ContosoRG** 内に存在することになります。
- リソースはプライマリ リージョン (米国東部 2) にレプリケートされ、運用ネットワーク (VNET-PROD-EUS2) 内に配置されます。
    - Web VM は、フロントエンド サブネット (PROD-FE-EUS2) 内に存在することになります。
    - データベース インスタンスは、データベース サブネット (PROD-DB-EUS2) 内に存在することになります。
- アプリのデータベースは、MySQL ツールを使用して Azure MySQL に移行されます。
- Contoso データセンター内のオンプレミス VM は、移行が行われた後に使用停止にされます。


![シナリオのアーキテクチャ](./media/contoso-migration-rehost-linux-vm-mysql/architecture.png) 


## <a name="migration-process"></a>移行プロセス

Contoso は、次のようにして移行プロセスを完了します。

Web VM を移行するには:

1. 最初の手順として、Contoso は、Site Recovery をデプロイするために必要な Azure とオンプレミスのインフラストラクチャを設定します。
2. Azure とオンプレミスのコンポーネントを準備したら、これらを設定し、Web VM のレプリケーションを有効にします。
3. レプリケーションが有効になり、実行中となったら、Azure へのフェールオーバーによって VM を移行します。

データベースを移行するには:

1. Contoso は、Azure で MySQL インスタンスをプロビジョニングします。
2. MySQL ワークベンチを設定し、データベースはローカルでバックアップします。
3. その後、ローカルのバックアップからデータベースを Azure に復元します。

![移行プロセス](./media/contoso-migration-rehost-linux-vm-mysql/migration-process.png) 


### <a name="azure-services"></a>Azure サービス

**サービス** | **説明** | **コスト**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | このサービスは、Azure VM、オンプレミス VM、物理サーバーの移行とディザスター リカバリーを調整および管理します。  | Azure へのレプリケーションの間に、Azure Storage の料金が発生します。  フェールオーバーが発生すると、Azure VM が作成されて料金がかかります。 料金と価格について[詳しくはこちら](https://azure.microsoft.com/pricing/details/site-recovery/)をご覧ください。
[Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/) | データベースは、オープン ソースの MySQL Server エンジンに基づいています。 これは、アプリの開発とデプロイに向けたサービスとしての、完全管理型のエンタープライズ対応コミュニティ MySQL データベースです。 

 
## <a name="prerequisites"></a>前提条件

作業担当者 (と Contoso) がこのシナリオを実行する場合は、次のものが必要です。

**要件** | **詳細**
--- | ---
**Azure サブスクリプション** | このシリーズの最初の方の記事で、既にサブスクリプションを作成しているはずです。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)を作成してください。<br/><br/> 無料アカウントを作成する場合、サブスクリプションの管理者としてすべてのアクションを実行できます。<br/><br/> 既存のサブスクリプションを使用しており、管理者でない場合は、管理者に依頼して所有者アクセス許可または共同作成者アクセス許可を割り当ててもらう必要があります。<br/><br/> さらに詳細なアクセス許可が必要な場合は、[こちらの記事](../site-recovery/site-recovery-role-based-linked-access-control.md)をご覧ください。 
**Azure インフラストラクチャ** | Contoso は、[移行のための Azure インフラストラクチャ](contoso-migration-infrastructure.md)についての記事で説明されているように、Azure インフラストラクチャを設定します。<br/><br/> Site Recovery 用の[ネットワーク](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network)と[ストレージ](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage)の具体的な要件の詳細を確認してください。
**オンプレミスのサーバー** | オンプレミスの vCenter Server は、バージョン 5.5、6.0、または 6.5 を実行している必要があります。<br/><br/> バージョン 5.5、6.0、または 6.5 を実行している ESXi ホスト<br/><br/> ESXi ホスト上で実行している 1 つ以上の VMware VM。
**オンプレミスの VM** | Site Recovery を使用した移行がサポートされる [Linux VM の要件を確認](https://docs.microsoft.com//azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines)します。<br/><br/> サポートされる [Linux のファイル システムとストレージ システム](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#linux-file-systemsguest-storage)を確認します。<br/><br/> VM は [Azure の要件](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements)を満たす必要があります。


## <a name="scenario-steps"></a>シナリオのステップ

Azure で、この移行がどのように完了されるかを示します。

> [!div class="checklist"]
> * **ステップ 1: Site Recovery のために Azure を準備する**: レプリケートされたデータを保持するための Azure ストレージ アカウントと、Recovery Services のコンテナーを作成します。
> * **ステップ 2: Site Recovery 用にオンプレミスの VMware を準備する**: VM の検出とエージェントのインストール用のアカウントを準備し、フェールオーバー後に Azure VM に接続するための準備をします。
 * **ステップ 3: データベースをプロビジョニングする**: Azure で、Azure MySQL データベースのインスタンスをプロビジョニングします。
> * **ステップ 4: VM をレプリケートする**: Site Recovery のソース環境とターゲット環境を構成し、レプリケーション ポリシーを設定して、Azure のストレージへの VM のレプリケーションを開始します。
> * **ステップ 5: データベースを移行する**: MySQL ツールを使用して移行を設定します。
> * **ステップ 6: Site Recovery を使用して VM を移行する**: テスト フェールオーバーを実行してすべてが機能していることを確認してから、完全なフェールオーバーを実行して VM を Azure に移行します。




## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>ステップ 1: Site Recovery サービスのために Azure を準備する

Site Recovery のために、Contoso には数個の Azure コンポーネントが必要です。

- フェールオーバーされたリソースが置かれる VNet (Contoso では既にデプロイ済みの運用 VNet を使用します)。
- レプリケートされたデータを保持する Azure の新しいストレージ アカウント。 
- Azure 内の Recovery Services コンテナー。

Contoso は、[Azure インフラストラクチャのデプロイメント](contoso-migration-infrastructure.md)の間に VNet を既に作成したため、必要なのはストレージ アカウントとコンテナーを作成することだけです。


1. Contoso は、米国東部 2 リージョンの中に Azure のストレージ アカウント (**contosovmsacc20180528**) を作成します。

    - ストレージ アカウントは、Recovery Services コンテナーと同じリージョンに存在する必要があります。
    - Contoso は、標準ストレージと LRS レプリケーション付きの汎用アカウントを使用します。

    ![Site Recovery ストレージ](./media/contoso-migration-rehost-linux-vm-mysql/asr-storage.png)

3. ネットワークとストレージ アカウントを準備したので、Contoso はコンテナー (ContosoMigrationVault) を作成し、それをプライマリの米国東部 2 リージョン内の **ContosoFailoverRG** リソース グループ内に配置します。

    ![Recovery Services コンテナー](./media/contoso-migration-rehost-linux-vm-mysql/asr-vault.png)

**さらにサポートが必要な場合**

Site Recovery のために Azure を設定することについての[説明を参照します](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure)。


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>ステップ 2: Site Recovery のためにオンプレミスの VMware を準備する

Contoso では、次のようにオンプレミスの VMware インフラストラクチャを準備します。

- VM の検出を自動化するために、vCenter サーバー上にアカウントを作成します。
- レプリケートする VMware VM 上にモビリティ サービスを自動インストールできるアカウントを作成します。
- オンプレミスの VM を準備して、それらの VM が、移行後に Azure VM が作成されたらそこへ接続できるようにします。


### <a name="prepare-an-account-for-automatic-discovery"></a>自動検出用のアカウントを準備する

Site Recovery では、次のことを実行するために、VMware サーバーへのアクセスが必要です。

- VM を自動的に検出します。 少なくとも読み取り専用のアカウントが必要です。
- レプリケーション、フェールオーバー、およびフェールバックを調整します。 ディスクを作成および削除する、VM の電源をオンにするなどの操作を実行できるアカウントが必要です。

Contoso は、アカウントを以下のように設定します。

1. Contoso は、vCenter レベルでロールを作成します。
2. Contoso は次に、必要なアクセス許可をそのロールに割り当てます。


### <a name="prepare-an-account-for-mobility-service-installation"></a>モビリティ サービスのインストール用のアカウントを準備する

モビリティ サービスは、Contoso が移行しようとしてうる各 VM にインストールする必要があります。

- VM のレプリケーションが有効になっているときには、Site Recovery はこのコンポーネントの自動プッシュ インストールを行うことができます。
- 自動インストールの場合。 Site Recovery には、VM にアクセスするアクセス許可を持っているアカウントが必要です。 
- アカウントの詳細が、レプリケーションのセットアップ中の入力です。 
- このアカウントには、インストールのアクセス許可を持っていれば、ドメインまたはローカルのアカウントを指定できます。


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>フェールオーバー後に Azure VM に接続するための準備をする

Contoso は、Azure へのフェールオーバー後に、Azure VM に接続できることを望んでいます。 これを行うには、いくつかのことを実行する必要があります。 

- インターネット経由でアクセスするには、移行前に、オンプレミス Linux VM 上で SSH を有効にします。  Ubuntu の場合、これは **Sudo apt-get ssh install -y** というコマンドを使用して済ませることができます。
- フェールオーバー後、**ブート診断**を調べて VM のスクリーンショットを確認する必要があります。
- これが機能しない場合は、その VM が実行中であることを確認し、これらの[トラブルシューティングのヒント](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)を見直す必要があります。

**さらにサポートが必要な場合**

- 自動検出のためにロールを作成して割り当てることについての[説明を参照します](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery)。
- モビリティ サービスのプッシュ インストールのためにアカウントを作成することについての[説明を参照します](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation)。


## <a name="step-3-provision-azure-database-for-mysql"></a>ステップ 3: Azure Database for MySQL をプロビジョニングする

Contoso は、MySQL データベース インスタンスを、プライマリの米国東部 2 リージョン内にプロビジョニングします。

1. Azure Portal で、Azure Database for MySQL のリソースを作成します。 

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-1.png)

2. Azure データベースの名前 **contosoosticket** を追加します。 運用リソース グループ **ContosoRG** にデータベースを追加し、そのための資格情報を指定します。
3. オンプレミスの MySQL データベースはバージョン 5.7 なので、互換性のためにこのバージョンが選択されています。 既定のサイズを使用しますが、それらはデータベースの要件に適合しています。

     ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-2.png)

4. **バックアップ冗長オプション**については、Contoso は **Geo 冗長**を使用することを選択しています。 このオプションでは、停止が発生した場合、セカンダリの米国中部リージョンでデータベースを復元できます。 このオプションは、データベースをプロビジョニングするときにのみ構成できます。

     ![冗長性](./media/contoso-migration-rehost-linux-vm-mysql/db-redundancy.png)

4. **VNET-PROD-EUS2** ネットワークの **サービス エンドポイント**で、SQL サービスのサービス エンドポイント (データベース サブネット) を追加します。

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-3.png)

5. サブネットの追加後に、運用ネットワーク内のデータベース サブネットからのアクセスを許可する仮想ネットワーク ルールを作成します。

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-4.png)


## <a name="step-4-replicate-the-on-premises-vms"></a>ステップ 4: オンプレミスの VM をレプリケートする

Web VM を Azure に移行する前に、Contoso はレプリケーションを設定し、有効にします。

### <a name="set-a-protection-goal"></a>保護の目標を設定する

1. コンテナー内のコンテナー名 (ContosoVMVault) の下で、レプリケーションの目標を設定します (**[作業の開始]** > **[Site Recovery]** > **[Prepare infrastructure]**(インフラストラクチャの準備))。
2. マシンがオンプレミスに配置されていること、それらのマシンは VMware VM であること、およびレプリケート先は Azure にすることを指定します。

    ![レプリケーションの目標](./media/contoso-migration-rehost-linux-vm-mysql/replication-goal.png)

### <a name="confirm-deployment-planning"></a>展開の計画を確認する

続行するには、**[Yes, I have done it]**(はい、完了しました) を選択して、デプロイ計画が完了したことを確認します。 このシナリオで Contoso が移行するのは 1 つの VM だけなので、デプロイ計画は不要です。

### <a name="set-up-the-source-environment"></a>ソース環境をセットアップする

Contoso はソース環境を構成する必要があります。 これを行うには、OVF テンプレートを使用して、Site Recovery 構成サーバーを高可用性オンプレミス VMware VM としてデプロイします。 構成サーバーを起動して実行したら、それをコンテナーに登録します。

構成サーバーでは、いくつかのコンポーネントが実行されます。

- オンプレミスと Azure の間の通信を調整し、データのレプリケーションを管理する構成サーバー コンポーネント。
- プロセス サーバー: レプリケーション ゲートウェイとして機能します。 レプリケーション データを受信し、そのデータをキャッシュ、圧縮、暗号化によって最適化して、Azure Storage に送信します。
- また、プロセス サーバーは、レプリケートする VM へのモビリティ サービスのインストールや、オンプレミスの VMware VM の自動検出も行います。

Contoso は、これらのステップを以下のように実行します。


1. OVF テンプレートを **[Prepare Infrastructure]**(インフラストラクチャの準備) > **[ソース]** > **[構成サーバー]** からダウンロードします。
    
    ![OVF をダウンロードする](./media/contoso-migration-rehost-linux-vm-mysql/add-cs.png)

2. VMware にテンプレートをインポートして VM を作成し、その VM をデプロイします。

    ![OVF テンプレート](./media/contoso-migration-rehost-linux-vm-mysql/vcenter-wizard.png)

3. 初めて VM をオンにすると、Windows Server 2016 インストール環境が起動します。 使用許諾契約書に同意し、管理者パスワードを入力します。
4. インストールの完了後に、管理者として VM にサインインします。 初めてサインインしたときは、Azure Site Recovery 構成ツールが既定で実行されます。
5. このツールでは、構成サーバーをコンテナーに登録するために使用する名前を指定します。
6. このツールは、VM が Azure に接続できることを確認します。
7. 接続が確立されたら、Azure サブスクリプションにサインインします。 資格情報は、構成サーバーを登録するコンテナーにアクセスできる必要があります。

    ![構成サーバーの登録](./media/contoso-migration-rehost-linux-vm-mysql/config-server-register2.png)

8. ツールがいくつかの構成タスクを実行した後、再起動されます。
9. マシンに再度サインインすると、構成サーバーの管理ウィザードが自動的に起動されます。
10. ウィザードで、レプリケーション トラフィックを受信する NIC を選択します。 この設定は、構成後に変更することはできません。
11. サブスクリプション、リソース グループ、および構成サーバーを登録するコンテナーを選択します。

    ![コンテナー](./media/contoso-migration-rehost-linux-vm-mysql/cswiz1.png) 

12. ここで、MySQL Server と VMWare PowerCLI をダウンロードしてインストールします。 
13. 検証が終わったら、vCenter サーバーまたは vSphere ホストの FQDN または IP アドレスを指定します。 ポートは既定のままにして、vCenter サーバーには、わかりやすい名前を指定します。
14. 自動検出のために作成したアカウントと、モビリティ サービスを自動的にインストールするために Site Recovery が使用する資格情報を入力します。 

    ![vCenter](./media/contoso-migration-rehost-linux-vm-mysql/cswiz2.png)

14. 登録が完了したら、Azure Portal で、コンテナーの **[ソース]** ページの一覧に、構成サーバーと VMware サーバーが表示されていることを確認します。 検出には 15 分以上かかる場合があります。 
15. すべての準備ができると、Site Recovery は VMware サーバーに接続し、VM を検出します。

### <a name="set-up-the-target"></a>ターゲットをセットアップする

ここでは、ターゲットのレプリケーション設定を入力します。

1. **[インフラストラクチャの準備]**(インフラストラクチャの準備) > **[ターゲット]** で、ターゲットの設定を選択します。
2. Site Recovery は、指定されたターゲットに Azure のストレージ アカウントとネットワークが存在することを確認します。


### <a name="create-a-replication-policy"></a>レプリケーション ポリシーを作成する

ソースとターゲットが設定されたら、Contoso はレプリケーション ポリシーを作成する準備が整います。

1. **[インフラストラクチャの準備]** > **[Replication Settings]\(レプリケーションの設定\)** > **[レプリケーション ポリシー]** >  **[Create and Associate]\(作成および関連付け\)** で、ポリシー **ContosoMigrationPolicy** を作成します。
2. 以下の既定の設定を使用します。
    - **[RPO しきい値]**: 既定値は 60 分です。 この値で、復旧ポイントの作成頻度を指定します。 継続的なレプリケーションがこの制限を超えると、アラートが生成されます。
    - **[復旧ポイントのリテンション期間]**。 既定値は 24 時間です。 この値は、各復旧ポイントのリテンション期間の長さを指定します。 レプリケートされた VM は、期間内の任意の時点に復旧できます。
    - **[アプリ整合性スナップショットの頻度]**。 既定値は 1 時間です。 この値は、アプリケーション整合性スナップショットが作成される頻度を指定します。
 
        ![レプリケーション ポリシーの作成](./media/contoso-migration-rehost-linux-vm-mysql/replication-policy.png)

5. このポリシーは自動的に構成サーバーに関連付けられます。 

    ![レプリケーション ポリシーを関連付ける](./media/contoso-migration-rehost-linux-vm-mysql/replication-policy2.png)


**さらにサポートが必要な場合**

- これらすべてのステップの詳細な手順については、[オンプレミス VMware VM のディザスター リカバリーの設定に関する記事](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial)を参照してください。
- [ソース環境の設定](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source)、[構成サーバーのデプロイ](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server)、および[レプリケーション設定の構成](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication)に役立つ詳細な手順が記載されています。
- Linux 用の Azure ゲスト エージェントの詳細については、[こちら](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)を参照してください。

### <a name="enable-replication-for-the-web-vm"></a>Web VM のレプリケーションを有効にする

これで Contoso は、**OSTICKETWEB** VM のレプリケートを開始できるようになりました。

1. **[アプリケーションのレプリケート]** > **[ソース]** > **[+Replicate]\(+ レプリケート\)** で、ソースの設定を選択します。
2. vCenter サーバーと構成サーバーを含めて、仮想マシンを有効にすることを指定し、ソース設定を選択します。

    ![レプリケーションを有効にする](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication-source.png)

3. ここでターゲットの設定を指定します。 これには、フェールオーバー後に Azure VM が配置されるリソース グループとネットワークのほか、レプリケートされたデータが保存されるストレージ アカウントが含まれます。 

     ![レプリケーションを有効にする](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication2.png)

3. Contoso はレプリケーションのために **OSTICKETWEB** を選択します。 

    ![レプリケーションを有効にする](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication3.png)

4. VM のプロパティで、モビリティ サービスを VM に自動的にインストールするために使用する必要があるアカウントを選択します。

     ![モビリティ サービス](./media/contoso-migration-rehost-linux-vm-mysql/linux-mobility.png)

5. **[レプリケーションの設定]** > **[レプリケーション設定の構成]** で、正しいレプリケーション ポリシーが適用されていることを確認し、**[Enable Replication]**(レプリケーションを有効にする) を選択します。 モビリティ サービスが自動的にインストールされます。
6.  レプリケーションの進行状況は、**[ジョブ]** で追跡します。 **保護の最終処理**ジョブが実行されると、マシンはフェールオーバーできる状態になります。


**さらにサポートが必要な場合**

これらのすべてのステップの詳細な手順は、「[レプリケーションを有効にする](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication)」で確認できます。


## <a name="step-5-migrate-the-database"></a>ステップ 5: データベースを移行する

Contoso は、MySQL ツールでバックアップと復元を使用して、データベースを移行します。 MySQL Workbench をインストールし、OSTICKETMYSQL からデータベースをバックアップして、Azure Database for MySQL Server に復元します。

### <a name="install-mysql-workbench"></a>MySQL Workbench のインストール

1. [MySQL Workbench の前提条件を確認してダウンロード](https://dev.mysql.com/downloads/workbench/?utm_source=tuicool)します。
2. [インストールの指示](https://dev.mysql.com/doc/workbench/en/wb-installing.html)に従って、MySQL Workbench for Windows をインストールします。
3. MySQL workbench で、OSTICKETMYSQL への MySQL 接続を作成します。 

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench1.png)

4. データベースを **osticket** として、ローカルの自己完結型ファイルにエクスポートします。

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench2.png)

5. データベースがローカルにバックアップされたら、Azure Database for MySQL インスタンスへの接続を作成します。

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench3.png)

6. これで、自己完結型ファイルから、Azure MySQL インスタンス内にあるデータベースをインポート (復元) できます。 このインスタンスのために新しいスキーマ (osticket) が作成されます。

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench4.png)

## <a name="step-6-migrate-the-vms-with-site-recovery"></a>ステップ 6: Site Recovery を使用して VM を移行する

Contoso は、簡単なテスト フェールオーバーを実行してから VM を移行します。

### <a name="run-a-test-failover"></a>テスト フェールオーバーの実行

テスト フェールオーバーを実行すると、移行の前に、すべてが正常に機能していることを確認する助けになります。 

1. Contoso では、利用可能な最新の時点 (**[最後に処理があった時点]**) へのテスト フェールオーバーを実行します。
2. **[Shut down machine before beginning failover]\(フェールオーバー前にマシンをシャットダウンする\)** を選択します。これにより、Site Recovery は、フェールオーバーをトリガーする前にソース VM のシャットダウンを試みます。 仮にシャットダウンが失敗したとしても、フェールオーバーは続行されます。 
3. テスト フェールオーバーでは、以下が実行されます。 

    - 移行を実行するために必要なすべての条件が満たされていることを確認する前提条件チェックが実行されます。
    - Azure VM を作成できるように、フェールオーバーによってデータが処理されます。 最新の復旧ポイントを選択した場合は、データから復旧ポイントが作成されます。
    - 前の手順で処理されたデータを使用して、Azure VM が作成されます。

3. フェールオーバーが完了すると、Azure Portal にはレプリカの Azure VM が表示されます。 VM が適切なサイズであること、適切なネットワークに接続されていること、実行中であることを確認します。 
4. 確認したら、フェールオーバーをクリーンアップして、確認された事柄をすべて記録して保存します。

### <a name="migrate-the-vm"></a>VM の移行

VM を移行するには、VM を含む復旧計画を作成し、計画に従って Azure にフェールオーバーします。

1. Contoso は計画を作成し、それに **OSTICKETWEB** を追加します。

    ![復旧計画](./media/contoso-migration-rehost-linux-vm-mysql/recovery-plan.png)

2. 計画に基づいてフェールオーバーを実行します。 最新の復旧ポイントを選択し、Site Recovery では、フェールオーバーをトリガーする前にオンプレミス VM のシャットダウンを試みる必要があることを指定します。 フェールオーバーの進行状況は **[ジョブ]** ページで確認できます。

    ![フェールオーバー](./media/contoso-migration-rehost-linux-vm-mysql/failover1.png)

3. フェールオーバー中に、vCenter Server は、ESXi ホストで実行されている 2 つの VM を停止するコマンドを発行します。

    ![フェールオーバー](./media/contoso-migration-rehost-linux-vm-mysql/vcenter-failover.png)

4. フェールオーバーの後、Azure VM が Azure Portal に正常に表示されることを確認します。

    ![フェールオーバー](./media/contoso-migration-rehost-linux-vm-mysql/failover2.png)  

5. VM を確認したら、移行を完了します。 これで、VM のレプリケーションが停止し、その VM に対する Site Recovery の課金が停止します。

    ![フェールオーバー](./media/contoso-migration-rehost-linux-vm-mysql/failover3.png)

**さらにサポートが必要な場合**

- テスト フェールオーバーの実行に関する[説明を参照します](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure)。 
- 復旧計画の作成方法に関する[説明を参照します](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans)。
- Azure へのフェールオーバーに関する[説明を参照します](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover)。


### <a name="connect-the-vm-to-the-database"></a>VM をデータベースに接続する

移行プロセスの最終ステップとして、Azure Database for MySQL を指すようにアプリの接続文字列を更新します。 

1. Putty または別の SSH クライアントを使用して、OSTICKETWEB VM への SSH 接続を作成します。 この VM はプライベートなので、プライベート IP アドレスを使用して接続します。

    ![データベースへの接続](./media/contoso-migration-rehost-linux-vm-mysql/db-connect.png)  

    ![データベースへの接続](./media/contoso-migration-rehost-linux-vm-mysql/db-connect2.png)  

2. **OSTICKETWEB** VM が **OSTICKETMYSQL** データベースと通信できるように設定を更新します。 構成は、この時点で、オンプレミスの IP アドレス 172.16.0.43 を使用してハードコードされています。

    **更新より前**
    
    ![IP の更新](./media/contoso-migration-rehost-linux-vm-mysql/update-ip1.png)  

    **更新より後**
    
    ![IP の更新](./media/contoso-migration-rehost-linux-vm-mysql/update-ip2.png) 
    
    ![IP の更新](./media/contoso-migration-rehost-linux-vm-mysql/update-ip3.png) 

3. **systemctl restart apache2** と指定してサービスを再起動します。

    ![Restart](./media/contoso-migration-rehost-linux-vm-mysql/restart.png) 

4. 最後に、いずれかの Contoso ドメイン コント ローラー上で、**OSTICKETWEB** の DNS レコードを更新します。

    ![DNS の更新](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 


##  <a name="clean-up-after-migration"></a>移行後にクリーンアップする

移行が完了すると、Azure VM で osTicket のアプリ層が実行されています。

ここで、以下のことを行う必要があります。 
- VMware VM を vCenter インベントリから削除します
- ローカルのバックアップ ジョブからからオンプレミスの VM を削除します。
- 社内のドキュメントを更新して、新しい場所と IP アドレスを示します。 
- こうしたオンプレミスの VM と対話しているリソースがないか確認し、新しい構成が反映されるように、関連する設定やドキュメントをすべて更新します。
- Contoso では、依存関係のマッピングを指定して Azure Migrate サービスを使用し、移行のための **OSTICKETWEB** VM を評価しました。 ここで、この目的のためにインストールしたエージェント (Microsoft Monitoring Agent や Dependency Agent) を VM から削除する必要があります。

## <a name="review-the-deployment"></a>デプロイを再調査する

アプリが実行されるようになり、Contoso は新しいインフラストラクチャを完全に操作可能にして、セキュリティで保護する必要があります。

### <a name="security"></a>セキュリティ

Contoso のセキュリティ チームは VM とデータベースを再調査して、セキュリティの問題を特定します。

- VM がアクセスを制御するためのネットワーク セキュリティ グループ (NSG) を見直します。 NSG は、アプリケーションに対して許可されるトラフィックのみが通過できるようにするために使用されます。
- ディスクの暗号化と Azure KeyVault を使用した、VM ディスク上のデータのセキュリティ保護を検討します。
- VM とデータベース インスタンス間の通信は、SSL 用に構成されていません。 データベース トラフィックをハッキングできないようにするため、これを行う必要があります。

VM に関するセキュリティの実務の[詳細については、こちら](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control)を参照してください。

### <a name="backups"></a>バックアップ

- Contoso は、Azure Backup サービスを使用して VM 上のデータをバックアップします。 [詳細情報](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
- データベースのバックアップを構成する必要はありません。 Azure Database for MySQL は、サーバーのバックアップを自動的に作成して保存します。 彼らはデータベースのために geo 冗長性を使用することを選択したため、このデータベースは耐障害性があり、運用準備ができています。

### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

- Contoso は、リソースのデプロイ後、[Azure インフラストラクチャ](contoso-migration-infrastructure.md#set-up-tagging)のデプロイ中に下した決定に従って Azure タグを割り当てます。
- Contoso Ubuntu サーバーにライセンスの問題はありません。
- Contoso は、Microsoft の子会社である Cloudyn からライセンスが供与される Azure Cost Management を有効にします。 これは、Azure やその他のクラウド リソースの利用や管理に役立つ、マルチクラウド対応のコスト管理ソリューションです。  Azure Cost Management の詳細については、[こちら](https://docs.microsoft.com/azure/cost-management/overview)を参照してください。


## <a name="next-steps"></a>次の手順

このシナリオでは、Contoso が試した最後のリホストのシナリオを示しました。オンプレミスの Linux osTicket アプリのフロントエンド VM を Azure VM に移行し、アプリ データベースを Azure MySQL インスタンスに移行しました。

移行シリーズの次のチュートリアルのセットでは、Contoso がより複雑な移行のセットをどのように実行したかを示す予定です。これは、撤去して場所を移す単純な移行ではなく、アプリのリファクタリングを伴う移行です。
