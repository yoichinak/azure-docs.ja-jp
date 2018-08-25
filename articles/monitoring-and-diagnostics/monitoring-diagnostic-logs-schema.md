---
title: Azure 診断ログでサポートされているサービスとスキーマ
description: Azure 診断ログでサポートされているサービスとイベント スキーマについて説明します。
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 7/18/2018
ms.author: johnkem
ms.component: logs
ms.openlocfilehash: a075b60c525fc3883f4464f19a8964fb64ce15a0
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627714"
---
# <a name="supported-services-schemas-and-categories-for-azure-diagnostic-logs"></a>Azure 診断ログでサポートされているサービス、スキーマ、カテゴリ

[Azure Monitor 診断ログ](monitoring-overview-of-diagnostic-logs.md)は、Azure サービスから出力されるログで、サービスやリソースの操作が記述されています。 Azure Monitor を通じて使用可能なすべての診断ログでは、共通の上位スキーマを共有します。そのため、各サービスは独自のイベントの一意のプロパティをフレキシブルに出力することができます。

(`resourceId` プロパティで使用可能な) リソースの種類と `category` を組み合わせて、スキーマを一意に識別します。 この記事では、診断ログの上位スキーマについて説明し、各サービスのスキーマへのリンクを示します。

## <a name="top-level-diagnostic-logs-schema"></a>診断ログの上位スキーマ

| Name | 必須/省略可能 | 説明 |
|---|---|---|
| time | 必須 | イベントのタイムスタンプ (UTC)。 |
| resourceId | 必須 | イベントを出力したリソースのリソース ID。 テナント サービスの場合、形式は /tenants/tenant-id/providers/provider-name です。 |
| tenantId | テナント ログで必須 | このイベントが関連付けられている Active Directory テナントのテナント ID。 このプロパティは、テナントレベルのログでのみ使用されます。リソースレベルのログには表示されません。 |
| operationName | 必須 | このイベントで表される操作の名前。 イベントが RBAC 操作を表す場合、これは RBAC 操作の名前になります (例:  Microsoft.Storage/storageAccounts/blobServices/blobs/Read)。 実際の文書化されている Resource Manager 操作でない場合でも、通常は、Resource Manager 操作の形式でモデル化されます (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`)。 |
| operationVersion | 省略可能 | operationName が API を使用して実行された場合は、操作に関連付けられている api-version (例:  http://myservice.windowsazure.net/object?api-version=2016-06-01) この操作に対応する API がない場合、バージョンは、操作に関連付けられているプロパティが今後、変更された場合、その操作のバージョンを表します。 |
| category | 必須 | イベントのログ カテゴリ。 カテゴリは細分化されており、これを使用して、特定のリソースのログを有効または無効にすることができます。 イベントのプロパティ BLOB 内に表示されるプロパティは、特定のログ カテゴリとリソースの種類内のものと同じです。 一般的なログ カテゴリは、"Audit"、"Operational"、"Execution"、"Request" です。 |
| resultType | 省略可能 | イベントの状態。 一般的な値は、Started、In Progress、Succeeded、Failed、Active、Resolved です。 |
| resultSignature | 省略可能 | イベントの副状態。 この操作が REST API 呼び出しに対応している場合、これは、対応する REST 呼び出しの HTTP 状態コードです。 |
| resultDescription | 省略可能 | この操作を説明する静的テキスト (例:  "Get storage file")。 |
| durationMs | 省略可能 | 操作時間 (ミリ秒)。 |
| callerIpAddress | 省略可能 | 操作が、一般的に利用できる IP アドレスを持つエンティティからの API 呼び出しに対応している場合は、呼び出し元 IP アドレス。 |
| correlationId | 省略可能 | 関連するイベントのセットをグループ化するために使用される GUID。 通常、2 つのイベントの operationName は同じであるものの、状態が異なる (たとえば、 "Started" と "Succeeded") 場合、同じ関連付け ID が共有されます。 これは、イベント間の他のリレーションシップを表す場合もあります。 |
| identity | 省略可能 | 操作を実行したユーザーまたはアプリケーションの ID を説明する JSON BLOB。 通常、これにはアクティブ ディレクトリからの認証および要求 / JWT トークンが含まれます。 |
| Level | 省略可能 | イベントの重大度レベル。 Informational、Warning、Error、Critical のいずれかである必要があります。 |
| location | 省略可能 | イベントを出力するリソースの領域 (例:  "East US"、"France South")。 |
| properties | 省略可能 | この特定のイベント カテゴリに関連する拡張プロパティ。 すべてのカスタム/一意のプロパティは、このスキーマの "Part B" 内に配置する必要があります。 |

## <a name="service-specific-schemas-for-resource-diagnostic-logs"></a>リソース診断ログのサービス固有のスキーマ
リソース診断ログのスキーマは、リソースとログ カテゴリによって異なります。 この一覧は、診断ログを使用できるようにするすべてのサービスと、サービスおよびカテゴリ固有のスキーマ (使用可能な場合) へのリンクを示しています。

| Service | スキーマとドキュメント |
| --- | --- |
| Azure Active Directory | [概要](../active-directory/reports-monitoring/overview-activity-logs-in-azure-monitor.md)、[監査ログ スキーマ](../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md)、および[サインイン スキーマ](../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API Management | [API Management の診断ログ](../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Application Gateway |[Application Gateway の診断ログ](../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Azure Automation のログ分析](../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure Batch 診断ログ](../batch/batch-diagnostics.md) |
| Content Delivery Network | [CDN の Azure 診断ログ](../cdn/cdn-azure-diagnostic-logs.md) |
| Cosmos DB | [Azure Cosmos DB のログ](../cosmos-db/logging.md) |
| Data Factory | [Azure Monitor を使用して、データ ファクトリを監視する](../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Azure Data Lake Analytics の診断ログへのアクセス](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Azure Data Lake Store の診断ログへのアクセス](../data-lake-store/data-lake-store-diagnostic-logs.md) |
| DB for PostgreSQL |  スキーマは使用できません。 |
| Event Hubs |[Azure Event Hubs の診断ログ](../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | スキーマは使用できません。 |
| IoT Hub | [IoT Hub 操作](../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Azure Key Vault のログ記録](../key-vault/key-vault-logging.md) |
| Load Balancer |[Azure Load Balancer のログ分析](../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Logic Apps B2B カスタム追跡スキーマ](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| ネットワーク セキュリティ グループ |[ネットワーク セキュリティ グループ (NSG) のためのログ分析](../virtual-network/virtual-network-nsg-manage-log.md) |
| DDOS Protection | [Azure DDoS Protection Standard を管理する](../virtual-network/manage-ddos-protection.md) |
| PowerBI 専用 | スキーマは使用できません。 |
| 復旧サービス | [Azure Backup のデータ モデル](../backup/backup-azure-reports-data-model.md)|
| Search |[検索トラフィックの分析の有効化と使用](../search/search-traffic-analytics.md) |
| Service Bus |[Azure Service Bus の診断ログ](../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Azure SQL Database 診断ログ](../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[ジョブの診断ログ](../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | スキーマは使用できません。 |
| 仮想ネットワーク | スキーマは使用できません。 |
| 仮想ネットワーク ゲートウェイ | スキーマは使用できません。 |

## <a name="supported-log-categories-per-resource-type"></a>リソースの種類ごとのサポートされているログ カテゴリ
|リソースの種類|Category|カテゴリの表示名|
|---|---|---|
|Microsoft.AnalysisServices/servers|Engine|Engine|
|Microsoft.AnalysisServices/servers|Service|Service|
|Microsoft.ApiManagement/service|GatewayLogs|ApiManagement Gateway に関連するログ|
|Microsoft.Automation/automationAccounts|JobLogs|ジョブ ログ|
|Microsoft.Automation/automationAccounts|JobStreams|ジョブ ストリーム|
|Microsoft.Automation/automationAccounts|DscNodeStatus|DSC ノードの状態|
|Microsoft.Batch/batchAccounts|ServiceLog|サービス ログ|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|エンドポイントのメトリック (帯域幅、エグレスなど) を取得します。|
|Microsoft.CustomerInsights/hubs|AuditEvents|AuditEvents|
|Microsoft.DataFactory/factories|ActivityRuns|パイプライン アクティビティ実行ログ|
|Microsoft.DataFactory/factories|PipelineRuns|パイプライン実行ログ|
|Microsoft.DataFactory/factories|TriggerRuns|トリガー実行ログ|
|Microsoft.DataLakeAnalytics/accounts|Audit|Audit Logs|
|Microsoft.DataLakeAnalytics/accounts|要求数|要求ログ|
|Microsoft.DataLakeStore/accounts|Audit|Audit Logs|
|Microsoft.DataLakeStore/accounts|Requests|要求ログ|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLLogs|PostgreSQL サーバー ログ|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLBackupEvents|PostgreSQL のバックアップ イベント|
|Microsoft.Devices/IotHubs|Connections|Connections|
|Microsoft.Devices/IotHubs|DeviceTelemetry|デバイス テレメトリ|
|Microsoft.Devices/IotHubs|C2DCommands|C2D コマンド|
|Microsoft.Devices/IotHubs|DeviceIdentityOperations|デバイス ID の操作|
|Microsoft.Devices/IotHubs|FileUploadOperations|ファイルのアップロード操作|
|Microsoft.Devices/IotHubs|Routes|Routes|
|Microsoft.Devices/IotHubs|D2CTwinOperations|D2CTwinOperations|
|Microsoft.Devices/IotHubs|C2DTwinOperations|C2D ツイン操作|
|Microsoft.Devices/IotHubs|TwinQueries|ツイン クエリ|
|Microsoft.Devices/IotHubs|JobsOperations|ジョブ操作|
|Microsoft.Devices/IotHubs|DirectMethods|ダイレクト メソッド|
|Microsoft.Devices/IotHubs|E2EDiagnostics|E2E 診断 (プレビュー)|
|Microsoft.Devices/provisioningServices|DeviceOperations|デバイス操作|
|Microsoft.Devices/provisioningServices|ServiceOperations|サービス操作|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft.EventHub/namespaces|ArchiveLogs|アーカイブ ログ|
|Microsoft.EventHub/namespaces|OperationalLogs|操作ログ|
|Microsoft.EventHub/namespaces|AutoScaleLogs|自動スケール ログ|
|Microsoft.KeyVault/vaults|AuditEvent|Audit Logs|
|Microsoft.Logic/workflows|WorkflowRuntime|ワークフロー ランタイムの診断イベント|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|統合アカウント追跡イベント|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|ネットワーク セキュリティ グループ イベント|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|ネットワーク セキュリティ グループの規則数|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|ロード バランサーのアラート イベント|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|ロード バランサーのプローブ正常性状態|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|DDoS 保護通知|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|VM 保護アラート|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|アプリケーション ゲートウェイのアクセス ログ|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|アプリケーション ゲートウェイのパフォーマンス ログ|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|アプリケーション ゲートウェイのファイアウォール ログ|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|ゲートウェイ診断ログ|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|トンネル診断ログ|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|ルート診断ログ|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|IKE 診断ログ|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|P2S 診断ログ|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|Traffic Manager プローブの正常性結果イベント|
|Microsoft.Network/expressRouteCircuits|GWMCountersTable|GWM カウンターのテーブル|
|Microsoft.PowerBIDedicated/capacities|Engine|Engine|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Azure Backup レポート データ|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Azure Site Recovery ジョブ|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Azure Site Recovery イベント|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Azure Site Recovery レプリケートされた項目|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Azure Site Recovery レプリケーション状態|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery 回復ポイント|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationDataUploadRate|Azure Site Recovery のレプリケーション データ アップロード速度|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryProtectedDiskDataChurn|Azure Site Recovery で保護されたディスクのデータ変更頻度|
|Microsoft.Search/searchServices|OperationLogs|操作ログ|
|Microsoft.ServiceBus/namespaces|OperationalLogs|操作ログ|
|Microsoft.Sql/servers/databases|QueryStoreRuntimeStatistics|クエリ ストアのランタイム統計|
|Microsoft.Sql/servers/databases|QueryStoreWaitStatistics|クエリ ストアの待機統計|
|Microsoft.Sql/servers/databases|Errors|Errors|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistics|データベースの待機統計|
|Microsoft.Sql/servers/databases|Timeouts|Timeouts|
|Microsoft.Sql/servers/databases|Blocks|Blocks|
|Microsoft.Sql/servers/databases|SQLInsights|SQL Insights|
|Microsoft.Sql/servers/databases|Audit|Audit Logs|
|Microsoft.Sql/servers/databases|SQLSecurityAuditEvents|SQL セキュリティ監査イベント|
|Microsoft.StreamAnalytics/streamingjobs|Execution|実行|
|Microsoft.StreamAnalytics/streamingjobs|作成|Authoring|

## <a name="next-steps"></a>次の手順

* [診断ログの詳細の確認](monitoring-overview-of-diagnostic-logs.md)
* [リソース診断ログを **Event Hubs** にストリーミングする](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Azure Monitor REST API を使用してリソース診断設定を変更する](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Log Analytics を使用した、Azure ストレージからのログの分析](../log-analytics/log-analytics-azure-storage.md)
