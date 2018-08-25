---
title: Azure サブスクリプションの制限とクォータ
description: 一般的な Azure サブスクリプションとサービスの制限、クォータ、制約の一覧を示します。 制限を引き上げる方法と、最大値に関する情報も記載されています。
services: multiple
author: rothja
manager: jeffreyg
tags: billing
ms.assetid: 60d848f9-ff26-496e-a5ec-ccf92ad7d125
ms.service: billing
ms.topic: article
ms.date: 07/13/2018
ms.author: byvinyal
ms.openlocfilehash: 570eee6cc849c474cfeacf5a9ba1798f64f27183
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188079"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Azure サブスクリプションとサービスの制限、クォータ、制約
このドキュメントでは、最も一般的な Microsoft Azure の制限を一覧表示しています。これはクォータと呼ばれることもあります。 現時点では、すべての Azure サービスをカバーするものではありません。 今後、プラットフォームを広くカバーするように一覧を拡大し、更新していく予定です。

Azure の価格については、 [Azure の価格の概要](https://azure.microsoft.com/pricing/) に関するページを参照してください。 価格の概要に関するページでは、[料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用したり、サービス (たとえば、[Windows VM](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)) の価格の詳細ページに移動したりして、費用を見積もることができます。 コスト管理に役立つヒントについては、「[Azure の課金とコスト管理で予想外のコストを防ぐ](billing/billing-getting-started.md)」をご覧ください。

> [!NOTE]
> **既定の制限**を超える制限またはクォータの引き上げを希望される場合は、[オンライン カスタマー サポートに申請 (無料)](azure-resource-manager/resource-manager-quota-errors.md) してください。 次のテーブルにある**上限**の値を超える制限の引き上げはできません。 **上限**列が存在しない場合、記載されているリソースに調整可能な制限がないことを意味します。
>
> [無料試用版サブスクリプション](https://azure.microsoft.com/offers/ms-azr-0044p)は、制限およびクォータ引き上げの適用対象外です。 [無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p)をお持ちの場合は、[従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/)サブスクリプションにアップグレードしてください。 詳細については、「[Azure 無料試用版を従量課金制にアップグレード](billing/billing-upgrade-azure-subscription.md)」と「[Azure 無料アカウント FAQ](https://azure.microsoft.com/free/free-account-faq)」を参照してください。
>

## <a name="limits-and-the-azure-resource-manager"></a>制限と Azure Resource Manager
複数の Azure リソースを 1 つの Azure リソース グループに結合できるようになりました。 リソース グループを使用するとき、グローバルになった制限が Azure リソース マネージャーによりリージョン レベルでの管理対象になります。 Azure リソース グループの詳細については、「 [Azure Resource Manager の概要](azure-resource-manager/resource-group-overview.md)」を参照してください。

次の制限では、Azure リソース マネージャーを使用する際の制限の相違点を反映するために新しい表が追加されました。 たとえば、**サブスクリプションの制限**の表と、**サブスクリプションの制限 - Azure Resource Manager**の表などがあります。 制限が 2 つのシナリオに適用される場合、制限は最初の表にのみ表示されます。 特に記載のない限り、制限はすべてのリージョンに適用されます。

> [!NOTE]
> Azure リソース グループ内のリソースのクォータは、サブスクリプションごとではなく、サブスクリプションのリージョンごとにアクセスできることに注意してください。また、サービス管理クォータも同様です。 vCPU クォータを例に説明します。 vCPU 対応のクォータの増量をリクエストする場合、どのリージョンでいくつの vCPU を使用するかを決めてから、Azure リソース グループの vCPU クォータの数量と目的のリージョンをリクエストする必要があります。 したがって、西ヨーロッパで 30 の vCPU を使用してアプリケーションを実行する必要がある場合、具体的に、西ヨーロッパで 30 の vCPU をリクエストしてください。 この場合、他のリージョンの vCPU クォータは増量されません -- 西ヨーロッパでのみ 30 vCPU クォータが増量されます。
> <!-- -->結果として、1 つのリージョンのワークロードに対して Azure リソース グループのクォータがどれくらい必要かを判断し、デプロイを検討している各リージョンでその量を要求するのが便利です。 特定のリージョンの現在のクォータを判断する方法について詳しくは、 [デプロイメントの問題のトラブルシューティング](resource-manager-common-deployment-errors.md) に関するページを参照してください。
>
>

## <a name="service-specific-limits"></a>サービス固有の制限
* [Active Directory](#active-directory-limits)
* [API Management](#api-management-limits)
* [App Service](#app-service-limits)
* [Application Gateway](#application-gateway-limits)
* [Application Insights](#application-insights-limits)
* [Automation](#automation-limits)
* [Azure Cosmos DB](#azure-cosmos-db-limits)
* [Azure Database for MySQL](#azure-database-for-mysql)
* [Azure Database for PostgreSQL](#azure-database-for-postgresql)
* [Azure Event Grid](#azure-event-grid-limits)
* [Azure Maps](#azure-maps-limits)
* [Azure Redis Cache](#azure-redis-cache-limits)
* [Backup](#backup-limits)
* [Batch](#batch-limits)
* [BizTalk Services](#biztalk-services-limits)
* [CDN](#cdn-limits)
* [Cloud Services](#cloud-services-limits)
* [Container Instances](#container-instances-limits)
* [コンテナー レジストリ](#container-registry-limits)
* [Kubernetes サービス](#kubernetes-service-limits)
* [Data Factory](#data-factory-limits)
* [Data Lake Analytics](#data-lake-analytics-limits)
* [Data Lake Store](#data-lake-store-limits)
* [Database Migration Service](#database-migration-service-limits)
* [DNS](#dns-limits)
* [Event Hubs](#event-hubs-limits)
* [Azure Firewall](#azure-firewall-limits)
* [IoT Hub](#iot-hub-limits)
* [IoT Hub Device Provisioning Service](#iot-hub-device-provisioning-service-limits)
* [Key Vault](#key-vault-limits)
* [Log Analytics](#log-analytics-limits)
* [Managed Identity](#managed-identity-limits)
* [Media Services](#media-services-limits)
* [Mobile Engagement](#mobile-engagement-limits)
* [Mobile Services](#mobile-services-limits)
* [監視](#monitor-limits)
* [Multi-Factor Authentication](#multi-factor-authentication)
* [ネットワーク](#networking-limits)
* [Network Watcher](#network-watcher-limits)
* [Notification Hub Service](#notification-hub-service-limits)
* [リソース グループ](#resource-group-limits)
* [ロールベースのアクセス制御](#role-based-access-control-limits)
* [Scheduler](#scheduler-limits)
* [Search](#search-limits)
* [Service Bus](#service-bus-limits)
* [Site Recovery](#site-recovery-limits)
* [SQL Database](#sql-database-limits)
* [SQL Data Warehouse](#sql-data-warehouse-limits)
* [Storage](#storage-limits)
* [StorSimple System](#storsimple-system-limits)
* [Stream Analytics](#stream-analytics-limits)
* [サブスクリプション](#subscription-limits)
* [Traffic Manager](#traffic-manager-limits)
* [Virtual Machines](#virtual-machines-limits)
* [Virtual Machine Scale Sets](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>サブスクリプションの制限
#### <a name="subscription-limits"></a>サブスクリプションの制限
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>サブスクリプションの制限 - Azure Resource Manager
次の制限は、Azure Resource Manager と Azure リソース グループの使用時に適用されます。 Azure Resource Manager で変更されていない制限はこの一覧に含まれません。 これらの制限については、前の表を参照してください。

Resource Manager 要求の処理制限については、[Throttling Resource Manager requests (Resource Manager 要求を調整する)](resource-manager-request-limits.md)をご覧ください。

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>リソース グループの制限
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Virtual Machines の制限
#### <a name="virtual-machine-limits"></a>Virtual Machines の制限
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>Virtual Machines の制限 - Azure リソース マネージャー
次の制限は、Azure Resource Manager と Azure リソース グループの使用時に適用されます。 Azure Resource Manager で変更されていない制限はこの一覧に含まれません。 これらの制限については、前の表を参照してください。

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="virtual-machine-scale-sets-limits"></a>Virtual Machine Scale Sets の制限
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="container-instances-limits"></a>Container Instances の制限
[!INCLUDE [container-instances-limits](../includes/container-instances-limits.md)]

### <a name="container-registry-limits"></a>Container Registry の制限
次の表に、Basic、Standard、および Premium [サービス階層](./container-registry/container-registry-skus.md)の機能と制限について説明します。

[!INCLUDE [container-registry-limits](../includes/container-registry-limits.md)]

### <a name="kubernetes-service-limits"></a>Kubernetes サービスの制限
[!INCLUDE [container-service-limits](../includes/container-service-limits.md)]

### <a name="networking-limits"></a>ネットワークの制限
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="networking-limits"></a>ネットワークの制限
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="application-gateway-limits"></a>Application Gateway の制限
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="network-watcher-limits"></a>Network Watcher の制限
[!INCLUDE [network-watcher-limits](../includes/network-watcher-limits.md)]

#### <a name="traffic-manager-limits"></a>Traffic Manager の制限
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="dns-limits"></a>DNS の制限
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

#### <a name="azure-firewall-limits"></a>Azure Firewall の制限
[!INCLUDE [azure-firewall-limits](../includes/firewall-limits.md)]

### <a name="storage-limits"></a>ストレージの制限
ストレージ アカウントの上限の詳細については、「 [Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](storage/common/storage-scalability-targets.md)」を参照してください。

<!--like # storage accts -->
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

#### <a name="azure-blob-storage-limits"></a>Azure BLOB ストレージの制限
[!INCLUDE [storage-blob-scale-targets](../includes/storage-blob-scale-targets.md)]

#### <a name="azure-files-limits"></a>Azure Files の制限
Azure Files の制限の詳細については、「[Azure Files のスケーラビリティおよびパフォーマンスのターゲット](storage/files/storage-files-scale-targets.md)」を参照してください。

[!INCLUDE [storage-files-scale-targets](../includes/storage-files-scale-targets.md)]

#### <a name="azure-file-sync-limits"></a>Azure File Sync の制限
[!INCLUDE [storage-sync-files-scale-targets](../includes/storage-sync-files-scale-targets.md)]

#### <a name="azure-queue-storage-limits"></a>Azure Queue Storage の制限
[!INCLUDE [storage-queues-scale-targets](../includes/storage-queues-scale-targets.md)]

#### <a name="azure-table-storage-limits"></a>Azure Table Storage の制限
[!INCLUDE [storage-tables-scale-targets](../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
#### <a name="virtual-machine-disk-limits"></a>仮想マシン ディスクの制限
[!INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

その他の詳細については、「 [仮想マシンのサイズ](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 」を参照してください。

#### <a name="managed-virtual-machine-disks"></a>管理対象の仮想マシン ディスク

[!INCLUDE [azure-storage-limits-vm-disks-managed](../includes/azure-storage-limits-vm-disks-managed.md)]

#### <a name="unmanaged-virtual-machine-disks"></a>非管理対象の仮想マシン ディスク

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

### <a name="cloud-services-limits"></a>Cloud Services の制限
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="app-service-limits"></a>App Service の制限
次の App Service の制限には、Web Apps、Mobile Apps、API Apps、および Logic Apps の制限が含まれます。

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="scheduler-limits"></a>Scheduler の制限
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Batch の制限
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>BizTalk Services の制限
次の表に Azure Biztalk Services の制限内容を示します。

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="azure-cosmos-db-limits"></a>Azure Cosmos DB の制限
Azure Cosmos DB は世界規模のデータベースであり、アプリケーションで要求されることをすべて処理するようにスループットとストレージをスケールできます。 Azure Cosmos DB が提供するスケールについて質問がある場合は、askcosmosdb@microsoft.com にメールをお送りください。

### <a name="azure-database-for-mysql"></a>Azure Database for MySQL
Azure Database for MySQL の制限については、「[Azure Database for MySQL の制限事項](mysql/concepts-limits.md)」を参照してください。

### <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL
Azure Database for PostgreSQL の制限については、「[Azure Database for PostgreSQL の制限事項](postgresql/concepts-limits.md)」を参照してください。

### <a name="mobile-engagement-limits"></a>Mobile Engagement の制限
[!INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]

### <a name="search-limits"></a>Search の制限
価格レベルによって、容量と検索サービスの制限が決定されます。 レベルは次のとおりです。

* *Free* : 他の Azure サブスクライバーと共有する、評価および小規模の開発プロジェクトのためのマルチ テナント サービス。
* *Basic*: 小規模環境で運用ワークロードに対して専用コンピューティング リソースを提供します。高可用性のクエリ ワークロード用に最大 3 つのレプリカを備えています。
* *Standard (S1、S2、S3、S3 High Density)* : 大規模な運用ワークロード向けです。 Standard レベルは、ワークロード プロファイルに最適なリソース構成を選択できるように、複数のレベルに分かれています。

**サブスクリプションあたりの制限**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Search サービスあたりの制限**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

ドキュメント サイズ、1 秒あたりのクエリ数、キー数、要求数、応答数などのより細かなレベルでの制限の詳細については、「[Azure Search サービスの制限](search/search-limits-quotas-capacity.md)」を参照してください。

### <a name="media-services-limits"></a>Media Services の制限
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="cdn-limits"></a>CDN の制限
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Mobile Services の制限
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="monitor-limits"></a>監視の制限
[!INCLUDE [monitoring-limits](../includes/monitoring-limits.md)]

### <a name="notification-hub-service-limits"></a>Notification Hubs サービスの制限
[!INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Event Hubs の制限
[!INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Service Bus の制限
[!INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>IoT Hub の制限
[!INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="iot-hub-device-provisioning-service-limits"></a>IoT Hub Device Provisioning Service の制限
[!INCLUDE [azure-iotdps-limits](../includes/iot-dps-limits.md)]

### <a name="data-factory-limits"></a>Data Factory の制限
[!INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Data Lake Analytics の制限
[!INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="data-lake-store-limits"></a>Data Lake Store の制限
[!INCLUDE [azure-data-lake-store-limits](../includes/azure-data-lake-store-limits.md)]

### <a name="database-migration-service-limits"></a>Database Migration Service の制限
[!INCLUDE [database-migration-service-limits](../includes/database-migration-service-limits.md)]

### <a name="stream-analytics-limits"></a>Stream Analytics の制限
[!INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Active Directory の制限
[!INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]

### <a name="azure-event-grid-limits"></a>Azure Event Grid の制限
[!INCLUDE [event-grid-limits](../includes/event-grid-limits.md)]

### <a name="azure-maps-limits"></a>Azure Maps の制限
[!INCLUDE [maps-limits](../includes/maps-limits.md)]

### <a name="storsimple-system-limits"></a>StorSimple システムの制限
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

### <a name="log-analytics-limits"></a>Log Analytics の制限
[!INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### <a name="backup-limits"></a>Backup の制限
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="site-recovery-limits"></a>Site Recovery の制限
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="application-insights-limits"></a>Application Insights の制限
[!INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### <a name="api-management-limits"></a>API Management の制限
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-redis-cache-limits"></a>Azure Redis Cache の制限
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Key Vault の制限
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Automation の制限
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="managed-identity-limits"></a>Managed Identity の制限
[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

### <a name="role-based-access-control-limits"></a>ロール ベースのアクセス制御の制限
[!INCLUDE [role-based-access-control-limits](../includes/role-based-access-control-limits.md)]

### <a name="sql-database-limits"></a>SQL Database の制限
SQL データベースの制限については、「[SQL Database Resource Limits for single databases](sql-database/sql-database-vcore-resource-limits-single-databases.md)」 (単一データベースの SQL Database のリソース制限) および「[SQL Database Resource Limits for elastic pools and pooled databases](sql-database/sql-database-vcore-resource-limits-elastic-pools.md)」(エラスティック プールとプールされているデータベースの SQL Database のリソース制限) を参照してください。

### <a name="sql-data-warehouse-limits"></a>SQL Data Warehouse の制限
SQL Data Warehouse の制限については、[SQL Data Warehouse のリソース制限](sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md)に関する記事を参照してください。

## <a name="see-also"></a>関連項目
[Understanding Azure Limits and Increases (Azure の制限と増設について)](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Azure Virtual Machines および Cloud Services のサイズ](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Cloud Services のサイズ](cloud-services/cloud-services-sizes-specs.md)
