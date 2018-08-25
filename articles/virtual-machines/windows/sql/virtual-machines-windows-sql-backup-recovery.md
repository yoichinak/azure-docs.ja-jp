---
title: Azure VM における SQL Server のバックアップと復元 | Microsoft Docs
description: Azure Virtual Machines で実行されている SQL Server データベースのバックアップと復元に関する考慮事項について説明します。
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-resource-management
ms.assetid: 95a89072-0edf-49b5-88ed-584891c0e066
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/04/2018
ms.author: mikeray
ms.openlocfilehash: d46c55f809d24529ea5deeb4d84de44dae876a4b
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38968988"
---
# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Azure Virtual Machines おける SQL Server のバックアップと復元

この記事では、Microsoft Azure Virtual Machines 上で実行されている SQL Server で使用可能なバックアップと復元のオプションに関するガイダンスを紹介します。 Azure Storage では、データの損失や物理データの破損に対する保護を保証するためにすべての Azure VM ディスクの 3 つのコピーが維持されます。 したがって、オンプレミスとは異なり、ハードウェアの障害に気を配る必要がありません。 それでも、SQL Server データベースをバックアップして、アプリケーションやユーザー エラー (間違ったデータの挿入や削除など) から保護する必要はあります。 このような状況では、特定の時点に復元できることが重要です。

この記事の最初の部分では、使用可能なバックアップと復元のオプションの概要を示します。 その後のセクションでは、各戦略について詳しく説明します。

## <a name="backup-and-restore-options"></a>バックアップと復元のオプション

次の表に、Azure VM 上で実行されている SQL Server のさまざまなバックアップと復元のオプションに関する情報を示します。

| 戦略 | SQL のバージョン | 説明 |
|---|---|---|---|
| [自動化されたバックアップ](#automated) | 2014<br/> 2016<br/> 2017 | 自動バックアップを使用すると、SQL Server VM 上にあるすべてのデータベースの定期的なバックアップをスケジュールできます。 バックアップは Azure Storage に最大 30 日間保存されます。 SQL Server 2016 以降では、自動バックアップ v2 により、手動でのスケジュール設定の構成や完全バックアップとログ バックアップの頻度などの追加オプションが利用できます。 |
| [SQL VM の Azure Backup](#azbackup) | 2012<br/> 2014<br/> 2016<br/> 2017 | Azure Backup は、Azure VM 上で実行されている SQL Server 向けのエンタープライズ クラスのバックアップ機能を提供します。 このサービスを使用すると、複数のサーバーと数千のデータベースのバックアップを一元的に管理できます。 データベースは、ポータルで特定の時点に復元することができます。 これにより、数年間バックアップを維持できるカスタマイズ可能な保持ポリシーが提供されます。 現在、この機能はパブリック プレビュー段階にあります。 |
| [手動バックアップ](#manual) | すべて | Azure VM 上で実行されている SQL Server を手動でバックアップおよび復元する方法は、SQL Server のバージョンによってさまざまです。 このシナリオでは、あなたがデータベースのバックアップ方法に加え、そうしたバックアップの保存場所と管理を担当しているとします。 |

以下のセクションでは、各オプションについて詳しく説明します。 この記事の最後のセクションでは、機能マトリックスの形式でまとめています。

## <a id="autoamted"></a> 自動バックアップ

自動バックアップは、Microsoft Azure VM 上で実行されている SQL Server Standard エディションおよび Enterprise エディションの自動バックアップ サービスを提供します。 このサービスは、Azure portal で SQL Server Windows 仮想マシン イメージに自動的にインストールされる [SQL Server IaaS Agent 拡張機能](virtual-machines-windows-sql-server-agent-extension.md)によって提供されます。

すべてのデータベースは、構成した Azure Storage アカウントにバックアップされます。 バックアップを暗号化して最大 30 日間保持できます。

SQL Server 2016 以降の VM では、自動バックアップ v2 により、多くのカスタマイズ オプションが提供されます。 これらの機能強化点は次のとおりです。

- システム データベースのバックアップ
- 手動バックアップのスケジュールと時間枠
- 完全バックアップとログ ファイル バックアップの頻度

データベースを復元するには、ストレージ アカウント内にある必要なバックアップ ファイルを探し、SQL Server Management Studio (SSMS) または Transact-SQL コマンドを使用して SQL VM の復元を実行する必要があります。

SQL VM の自動バックアップを構成する方法の詳細については、次のいずれかの記事をご覧ください。

- **SQL Server 2016/2017**: [Azure Virtual Machines の自動バックアップ v2](virtual-machines-windows-sql-automated-backup-v2.md)
- **SQL Server 2014**: [SQL Server 2014 Virtual Machines の自動バックアップ](virtual-machines-windows-sql-automated-backup.md)

## <a id="azbackup"></a> SQL VM の Azure Backup (パブリック プレビュー)

[Azure Backup](/azure/backup/) は、Azure VM 上で実行されている SQL Server 向けのエンタープライズ クラスのバックアップ機能を提供します。 すべてのバックアップは、Recovery Services コンテナーに格納され、管理されます。 このソリューションにはいくつかの利点があります (特に企業向け)。

- **ゼロインフラストラクチャでのバックアップ**: バックアップ サーバーまたは保存場所を管理する必要がありません。
- **スケール**: 多くの SQL VM と数千のデータベースを保護します。
- **従量課金制**: この機能は、Azure Backup によって提供される個別のサービスですが、すべての Azure サービスと同様に、料金は使用した分だけになります。
- **一元管理と監視**: Azure Backup がサポートする他のワークロードを含め、すべてのバックアップを Azure の単一のダッシュボードで一元管理できます。
- **ポリシーに基づくバックアップと保持**: 定期的なバックアップ用の標準バックアップ ポリシーを作成できます。 何年もバックアップを維持するためのバックアップ ポリシーを作成することもできます。
- **SQL Always On のサポート**: SQL Server Always On 構成を検出して保護し、バックアップ可用性グループのバックアップ設定を使用します。
- **15 分の復旧ポイント目標 (RPO)**: SQL トランザクション ログのバックアップを最大 15 分ごとに構成できます。
- **ポイントインタイム リストア**: ポータルを使用して、データベースを特定の時点に復元できます。複数の完全バックアップ、差分バックアップ、およびログ バックアップを手動で復元する必要はありません。
- **エラーが発生した場合の統合電子メール アラート**: エラーが発生した場合の統合電子メール通知を構成できます。
- **ロールベースのアクセス制御**: ポータルを介してバックアップと復元の操作を管理できるユーザーを決定できます。

機能の簡単な概要とデモについては、次のビデオをご覧ください。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2dNbw]

SQL VM のこの Azure Backup ソリューションは現在パブリック プレビューの段階です。 詳細については、「[Azure への SQL Server データベースのバックアップ](../../../backup/backup-azure-sql-database.md)」をご覧ください。

## <a id="manual"></a> 手動バックアップ

SQL VM のバックアップと復元の操作を手動で管理する場合は、使用している SQL Server のバージョンに応じていくつかのオプションがあります。 バックアップと復元の概要については、SQL Server のバージョンに応じて、次のいずれかの記事をご覧ください。

- [SQL Server 2016 以降のバックアップと復元](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)
- [SQL Server 2014 のバックアップと復元](https://msdn.microsoft.com/library/ms187048%28v=sql.120%29.aspx)
- [SQL Server 2012 のバックアップと復元](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx)
- [SQL Server SQL Server 2008 R2 のバックアップと復元](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx)
- [SQL Server 2008 のバックアップと復元](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx)

以下のセクションでは、いくつかの手動バックアップと復元のオプションについて詳しく説明します。

### <a name="backup-to-attached-disks"></a>接続されているディスクへのバックアップ

Azure VM 上で実行されている SQL Server では、バックアップ ファイルの保存先として VM に接続されているディスクを使用するネイティブなバックアップおよび復元手法を使用できます。 ただし、[仮想マシンのサイズ](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に基づいて、Azure 仮想マシンに接続できるディスク数には制限があります。 また、ディスク管理のオーバーヘッドも考慮する必要があります。

SQL Server Management Studio (SSMS) または Transact-SQL を使用してデータベースの完全バックアップを手動で作成する方法の例については、「[データベースの完全バックアップの作成](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server)」をご覧ください。

### <a name="backup-to-url"></a>Backup to URL

SQL Server 2012 SP1 CU2 以降では、Microsoft Azure Blob ストレージに直接バックアップおよび復元できます。これは、Backup to URL とも呼ばれています。 SQL Server 2016 では、この機能の次の拡張機能も導入されました。

| 2016 の拡張機能 | 詳細 |
| --- | --- |
| **ストライピング** |Microsoft Azure Blob ストレージにバックアップする場合、SQL Server 2016 では、複数の BLOB へのバックアップをサポートしているので、最大 12.8 TB の大規模なデータベースをバックアップできます。 |
| **スナップショット バックアップ** |Azure のスナップショットを使用することで、SQL Server ファイル スナップショット バックアップでは、Azure Blob ストレージ サービスを使用して保存されたデータベース ファイルをほぼ瞬時にバックアップし、迅速に復元できます。 この機能により、バックアップと復元のポリシーを簡素化することができます。 ファイル スナップショット バックアップでは、ポイントインタイム リストアもサポートしています。 詳細については、「 [Snapshot Backups for Database Files in Azure (Azure でのデータベース ファイルのスナップショット バックアップ)](https://docs.microsoft.com/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure)」をご覧ください。 |

詳細については、SQL Server のバージョンに応じて、次のいずれかの記事をご覧ください。

- **SQL Server 2016/2017**: [SQL Server Backup to URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
- **SQL Server 2014**: [SQL Server 2014 Backup to URL](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx)
- **SQL Server 2012**: [SQL Server 2012 Backup to URL](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)

### <a name="managed-backup"></a>マネージド バックアップ

SQL Server 2014 以降では、マネージド バックアップにより、Azure Storage へのバックアップの作成が自動化されます。 マネージド バックアップは、この記事の前のセクションで説明した Backup to URL 機能をバックグラウンドで利用します。 マネージド バックアップも、SQL Server VM の自動バックアップ サービスをサポートする基本機能です。

SQL Server 2016 以降では、マネージド バックアップに、スケジュール設定、システム データベースのバックアップ、完全バックアップとログ バックアップの頻度に関するオプションが追加されています。

詳細については、SQL Server のバージョンに応じて、次のいずれかの記事をご覧ください。

- [SQL Server 2016 以降の Microsoft Azure へのマネージド バックアップ](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)
- [SQL Server 2014 の Microsoft Azure へのマネージド バックアップ](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx)

## <a name="decision-matrix"></a>デシジョン マトリックス

次の表には、Azure の SQL Server 仮想マシンのバックアップと復元の各オプションの機能がまとめられています。

|| **自動化されたバックアップ** | **SQL の Azure Backup** | **手動バックアップ** |
|---|---|---|---|
| 追加の Azure サービスが必要 |   | ![[はい]](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Azure portal でのバックアップ ポリシーの構成 | ![[はい]](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![[はい]](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Azure portal でのデータベースの復元 |   | ![[はい]](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| 1 つのダッシュボードでの複数のサーバーの管理 |   | ![[はい]](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| ポイントインタイム リストア | ![はい](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![はい](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![[はい]](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| 15 分の復旧ポイント目標 (RPO) | ![[はい]](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![はい](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![[はい]](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| 短期間のバックアップ保持ポリシー (日) | ![[はい]](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![[はい]](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| 長期間のバックアップ保持ポリシー (月、年) |   | ![[はい]](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| SQL Server Always On の組み込みサポート |   | ![[はい]](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Azure Storage アカウントへのバックアップ | ![[はい]](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(自動) | ![[はい]](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(自動) | ![[はい]](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(ユーザーが管理) |
| ストレージとバックアップ ファイルの管理 | | ![[はい]](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |  |
| VM に接続されているディスクへのバックアップ |   |   | ![[はい]](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| 一元化されたカスタマイズ可能なバックアップ レポート |   | ![[はい]](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| エラーが発生した場合の統合電子メール アラート |   | ![[はい]](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| OMS に基づいた監視のカスタマイズ |   | ![[はい]](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| SSMS または Transact-SQL スクリプトを使用したバックアップ ジョブの監視 | ![[はい]](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![はい](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![[はい]](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| SSMS または Transact-SQL スクリプトを使用したデータベースの復元 | ![[はい]](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   | ![[はい]](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |

## <a name="next-steps"></a>次の手順

Azure VM に SQL Server をデプロイすることを計画している場合、プロビジョニングのガイダンスについては、「[Azure portal で Windows SQL Server 仮想マシンをプロビジョニングする方法](virtual-machines-windows-portal-sql-server-provision.md)」をご覧ください。

バックアップと復元を使用してデータを移行できますが、Azure VM の SQL Server へのより簡単なデータ移行パスが存在する可能性があります。 移行オプションと推奨事項の詳細については、「[Azure VM の SQL Server へのデータベースの移行](virtual-machines-windows-migrate-sql.md)」をご覧ください。