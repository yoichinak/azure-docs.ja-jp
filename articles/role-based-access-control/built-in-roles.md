---
title: Azure の組み込みロール | Microsoft Docs
description: Azure でのロール ベースのアクセス制御 (RBAC) の組み込みロールについて説明します。 Actions、NotActions、DataActions、NotDataActions を一覧表示します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 08/07/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: 5a373c397df09653395eea7996b19262aee75c7a
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39619051"
---
# <a name="built-in-roles-in-azure"></a>Azure の組み込みロール
[ロールベースのアクセス制御 (RBAC)](overview.md) には、ユーザー、グループ、サービス プリンシパルに割り当てることのできるいくつかの組み込みロールの定義があります。 ロールの割り当ては、Azure でリソースへのアクセスを制御する方法です。 組み込みロールが組織の特定のニーズを満たさない場合は、独自の[カスタム ロール](custom-roles.md)を作成することができます。

組み込みロールは、常に進化しています。 最新のロールの定義を取得するには、[Get AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) または [az role definition list](/cli/azure/role/definition#az-role-definition-list) を使用してください。

## <a name="built-in-role-descriptions"></a>組み込みのロールの説明
次の表に、組み込みのロールについての簡単な説明を示します。 ロール名をクリックすると、各ロールの `Actions`、`NotActions`、`DataActions`、`NotDataActions` の一覧が表示されます。


| 組み込みのロール | 説明 |
| --- | --- |
| [Owner](#owner) | リソースへのアクセスを含め、すべてを管理できます。 |
| [Contributor](#contributor) | リソースへのアクセス以外のすべてを管理できます。 |
| [Reader](#reader) | すべてを表示できますが、変更することはできません。 |
| [AcrImageSigner](#acrimagesigner) | ACR イメージ署名者 |
| [AcrQuarantineReader](#acrquarantinereader) | ACR 検査データ閲覧者 |
| [AcrQuarantineWriter](#acrquarantinewriter) | ACR 検査データ作成者 |
| [API Management Service Contributor](#api-management-service-contributor) | API Management サービスを管理できます。ただし、それらへのアクセスは含まれません。 |
| [API Management Service Operator Role](#api-management-service-operator-role) | サービスを管理できますが、API は対象外です |
| [API Management Service Reader Role](#api-management-service-reader-role) | サービスと API への読み取り専用アクセスです |
| [Application Insights Component Contributor](#application-insights-component-contributor) | Application Insights コンポーネントを管理できます |
| [Application Insights Snapshot Debugger](#application-insights-snapshot-debugger) | Application Insights Snapshot Debugger 機能を使用するアクセス許可をユーザーに付与します |
| [Automation Job Operator](#automation-job-operator) | Automation Runbook を使用してジョブを作成および管理します。 |
| [Automation Operator](#automation-operator) | Automation オペレーターはジョブを開始、停止、中断、再開することができます |
| [Automation Runbook Operator](#automation-runbook-operator) | Runbook のジョブを作成する方法については、Runbook のプロパティを参照してください。 |
| [Azure Stack Registration Owner](#azure-stack-registration-owner) | Azure Stack の登録を管理できます。 |
| [Backup Contributor](#backup-contributor) | バックアップ サービスを管理できますが、資格情報コンテナーの作成や他のユーザーに対するアクセス権の付与を行うことはできません |
| [Backup Operator](#backup-operator) | バックアップ サービスを管理できます (バックアップの削除、資格情報コンテナーの作成、他のユーザーに対するアクセス権の付与を除く) |
| [Backup Reader](#backup-reader) | バックアップ サービスを表示できますが、変更を行うことはできません |
| [Billing Reader](#billing-reader) | 課金データを読み取ることができます |
| [BizTalk Contributor](#biztalk-contributor) | BizTalk Services を管理できます。ただし、それらへのアクセスは含まれません。 |
| [CDN Endpoint Contributor](#cdn-endpoint-contributor) | CDN エンドポイントを管理できますが、アクセス権を他のユーザーに付与することはできません。 |
| [CDN Endpoint Reader](#cdn-endpoint-reader) | CDN エンドポイントを表示できますが、変更はできません。 |
| [CDN Profile Contributor](#cdn-profile-contributor) | CDN プロファイルとそのエンドポイントを管理できますが、アクセス権を他のユーザーに付与することはできません。 |
| [CDN Profile Reader](#cdn-profile-reader) | CDN プロファイルとそのエンドポイントを表示できますが、変更はできません。 |
| [Classic Network Contributor](#classic-network-contributor) | 従来のネットワークを管理できます。ただし、それらへのアクセスは含まれません。 |
| [Classic Storage Account Contributor](#classic-storage-account-contributor) | 従来のストレージ アカウントを管理できますが、アクセスすることはできません。 |
| [従来のストレージ アカウント キー オペレーターのサービス ロール](#classic-storage-account-key-operator-service-role) | 従来のストレージ アカウント キー オペレーターは、従来のストレージ アカウントでのキーの一覧表示と再生成を行うことができます |
| [Classic Virtual Machine Contributor](#classic-virtual-machine-contributor) | 従来の仮想マシンを管理できますが、アクセスすることはできません。また、接続先の仮想ネットワークやストレージ アカウントにもアクセスできません。 |
| [ClearDB MySQL DB Contributor](#cleardb-mysql-db-contributor) | ClearDB MySQL データベースを管理できます。ただし、それらへのアクセスは含まれません。 |
| [Cosmos DB アカウントの閲覧者ロール](#cosmos-db-account-reader-role) | Cosmos DB アカウントのデータを読み取ることができます。 Azure Cosmos DB アカウントの管理については、「[DocumentDB Account Contributor](#documentdb-account-contributor)」をご覧ください。 |
| [Data Box Contributor](#data-box-contributor) | Data Box サービスですべてを管理できます (他のユーザーに対するアクセス権の付与を除く)。 |
| [Data Box Operator](#data-box-operator) | Data Box サービスを管理できます (注文の作成または注文の詳細の編集、および他のユーザーに対するアクセス権の付与を除く)。 |
| [Data Factory Contributor](#data-factory-contributor) | データ ファクトリを管理できます。ただし、それらへのアクセスは含まれません。 |
| [Data Lake Analytics Developer](#data-lake-analytics-developer) | 独自のジョブを送信、監視、管理できますが、Data Lake Analytics アカウントを作成または削除することはできません。 |
| [Data Purger](#data-purger) | 分析データを削除することができます。 |
| [DevTest Labs User](#devtest-labs-user) | Azure DevTest Labs の仮想マシンに対して接続、起動、再起動、シャットダウンを行えます。 |
| [DNS Zone Contributor](#dns-zone-contributor) | Azure DNS の DNS ゾーンとレコード セットを管理できますが、それにアクセスできるユーザーを制御することはできません。 |
| [DocumentDB Account Contributor](#documentdb-account-contributor) | Azure Cosmos DB アカウントを管理できます。 Azure Cosmos DB は以前は DocumentDB と呼ばれていました。 |
| [Intelligent Systems Account Contributor](#intelligent-systems-account-contributor) | Intelligent Systems のアカウントを管理できます。ただし、それらへのアクセスは含まれません。 |
| [Key Vault Contributor](#key-vault-contributor) | キー コンテナーを管理できますが、アクセスすることはできません。 |
| [Lab Creator](#lab-creator) | Azure Lab アカウントで管理対象のラボを作成、管理、削除できます。 |
| [Log Analytics Contributor](#log-analytics-contributor) | Log Analytics 共同作成者は、すべての監視データを読み取り、監視設定を編集できます。 監視設定の編集には、VM 拡張機能の VM への追加、Azure Storage からログの収集を設定できるようにするためのストレージ アカウント キーの読み取り、Automation アカウントの作成と構成、ソリューションの追加、すべての Azure リソースでの Azure 診断の構成が含まれます。 |
| [Log Analytics Reader](#log-analytics-reader) | Log Analytics Reader は、すべての監視データの表示と検索、およびすべての Azure リソース上の Azure 診断構成の表示など、監視設定の表示を行うことができます。 |
| [Logic App Contributor](#logic-app-contributor) | ロジック アプリを管理できますが、アクセスすることはできません。 |
| [Logic App Operator](#logic-app-operator) | ロジック アプリの読み取り、有効化、無効化ができます。 |
| [Managed Application Operator Role](#managed-application-operator-role) | マネージド アプリケーション リソースに対する読み取りとアクションの実行が可能です。 |
| 
  [Managed Identity Contributor](#managed-identity-contributor) | ユーザー割り当て ID の作成、読み取り、更新、削除を行います |
| 
  [Managed Identity Operator](#managed-identity-operator) | ユーザー割り当て ID の読み取りと割り当てを行います |
| [管理グループ共同作成者](#management-group-contributor) | 管理グループ共同作成者ロール |
| [管理グループ閲覧者](#management-group-reader) | 管理グループ閲覧者ロール |
| [Monitoring Contributor](#monitoring-contributor) | すべての監視データを読み取り、監視設定を編集できます。 [「Azure Monitor での役割、アクセス許可、およびセキュリティの概要」](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles)も参照してください。 |
| [Monitoring Reader](#monitoring-reader) | すべての監視データ (メトリック、ログなど) を読み取ることができます。 [「Azure Monitor での役割、アクセス許可、およびセキュリティの概要」](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles)も参照してください。 |
| [Network Contributor](#network-contributor) | ネットワークを管理できます。ただし、それらへのアクセスは含まれません。 |
| [New Relic APM Account Contributor](#new-relic-apm-account-contributor) | New Relic Application Performance Management のアカウントとアプリケーションを管理できます。ただし、それらへのアクセスは含まれません。 |
| [Reader and Data Access](#reader-and-data-access) | すべてを表示することができますが、ストレージ アカウントや含まれるリソースの削除や作成はできません。 ストレージ アカウント キーへのアクセスを使用して、ストレージ アカウントに含まれるすべてのデータへの読み取り/書き込みアクセスも許可されます。 |
| [Redis Cache Contributor](#redis-cache-contributor) | Redis Caches を管理できます。ただし、それらへのアクセスは含まれません。 |
| [リソース ポリシーの共同作成者 (プレビュー)](#resource-policy-contributor-preview) | (プレビュー) リソース ポリシーの作成/変更、サポート チケットの作成、リソース/階層の読み取りを実行する権限により、ユーザーを EA からバックフィルしました。 |
| [Scheduler Job Collections Contributor](#scheduler-job-collections-contributor) | スケジューラ ジョブ コレクションを管理できます。ただし、それらへのアクセスは含まれません。 |
| [Search Service Contributor](#search-service-contributor) | Search サービスを管理できます。ただし、それらへのアクセスは含まれません。 |
| [Security Admin](#security-admin) | Security Center のみ: セキュリティ ポリシーの表示、セキュリティ状態の表示、セキュリティ ポリシーの編集、アラートと推奨事項の表示、アラートと推奨事項の却下を行うことができます |
| [Security Manager](#security-manager) | セキュリティ コンポーネント、セキュリティ ポリシー、Virtual Machines を管理できます |
| [Security Reader](#security-reader) | Security Center のみ: 推奨事項とアラート、セキュリティ ポリシー、セキュリティの状態を表示することはできますが、変更することはできません |
| [Site Recovery Contributor](#site-recovery-contributor) | 資格情報コンテナーの作成とロールの割り当てを除く、Site Recovery サービスを管理できます |
| [Site Recovery Operator](#site-recovery-operator) | フェールオーバーとフェールバックを実行できますが、その他の Site Recovery 管理操作は実行しません |
| [Site Recovery Reader](#site-recovery-reader) | Site Recovery の状態を表示できますが、その他の管理操作は実行できません |
| [SQL DB Contributor](#sql-db-contributor) | SQL データベースを管理できます。ただし、それらへのアクセスは含まれません。 また、セキュリティ関連のポリシーまたは親 SQL Server を管理することはできません。 |
| [SQL Security Manager](#sql-security-manager) | SQL サーバーとデータベースのセキュリティ関連のポリシーを管理できます。ただし、それらへのアクセスは管理できません。 |
| [SQL Server Contributor](#sql-server-contributor) | SQL サーバーとデータベースを管理できます。ただし、それらへのアクセスや、それらのセキュリティ関連ポリシーは管理できません。 |
| [Storage Account Contributor](#storage-account-contributor) | ストレージ アカウントを管理できますが、アクセスすることはできません。 |
| [ストレージ アカウント キー オペレーターのサービス ロール](#storage-account-key-operator-service-role) | ストレージ アカウント キー オペレーターは、ストレージ アカウントでのキーの一覧表示と再生成を行うことができます |
| [ストレージ BLOB データ共同作成者 (プレビュー)](#storage-blob-data-contributor-preview) | Azure Storage Blob コンテナーおよびデータに対する読み取りアクセス、書き込みアクセス、削除アクセスを許可します |
| [ストレージ BLOB データ閲覧者 (プレビュー)](#storage-blob-data-reader-preview) | Azure Storage Blob コンテナーおよびデータに対する読み取りアクセスを許可します |
| [ストレージ キュー データ共同作成者 (プレビュー)](#storage-queue-data-contributor-preview) | Azure Storage キューおよびキュー メッセージに対する読み取りアクセス、書き込みアクセス、削除アクセスを許可します |
| [ストレージ キュー データ閲覧者 (プレビュー)](#storage-queue-data-reader-preview) | Azure Storage キューおよびキュー メッセージに対する読み取りアクセスを許可します |
| [Support Request Contributor](#support-request-contributor) | Support request を作成して管理できます |
| [Traffic Manager Contributor](#traffic-manager-contributor) | Traffic Manager プロファイルを管理できますが、それにアクセスできるユーザーを制御することはできません。 |
| [User Access Administrator](#user-access-administrator) | Azure リソースに対するユーザー アクセスを管理します。 |
| [Virtual Machine Administrator Login](#virtual-machine-administrator-login) | ポータルで仮想マシンを表示し、管理者としてログインします |
| [Virtual Machine Contributor](#virtual-machine-contributor) | 仮想マシンを管理できますが、アクセスすることはできません。また、接続先の仮想ネットワークやストレージ アカウントにもアクセスできません。 |
| [Virtual Machine User Login](#virtual-machine-user-login) | ポータルで仮想マシンを表示し、通常のユーザーとしてログインします。 |
| [Web Plan Contributor](#web-plan-contributor) | Web サイトの Web プランを管理できます。ただし、それらへのアクセスは含まれません。 |
| [Website Contributor](#website-contributor) | Web サイト (Web プランではない) を管理できます。ただし、それらへのアクセスは含まれません。 |


## <a name="owner"></a>Owner
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | リソースへのアクセスを含め、すべてを管理できます。 |
> | **Id** | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | **アクション** |  |
> | * | あらゆる種類のリソースの作成と管理 |

## <a name="contributor"></a>Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | リソースへのアクセス以外のすべてを管理できます。 |
> | **Id** | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | **アクション** |  |
> | * | あらゆる種類のリソースの作成と管理 |
> | **NotActions** |  |
> | Microsoft.Authorization/*/Delete | ロールとロール割り当ては削除できません |
> | Microsoft.Authorization/*/Write | ロールとロール割り当ては作成できません |
> | Microsoft.Authorization/elevateAccess/Action | テナント スコープで、ユーザー アクセス管理者のアクセス権を呼び出し元に付与します。 |
> | Microsoft.Blueprint/blueprintAssignments/write |  |
> | Microsoft.Blueprint/blueprintAssignments/delete |  |

## <a name="reader"></a>Reader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | すべてを表示できますが、変更することはできません。 |
> | **Id** | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | **アクション** |  |
> | */read | 機密データを除くあらゆる種類のリソースの読み取り |

## <a name="acrimagesigner"></a>AcrImageSigner
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | ACR イメージ署名者 |
> | **Id** | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | **アクション** |  |
> | Microsoft.ContainerRegistry/registries/*/read |  |
> | Microsoft.ContainerRegistry/registries/*/write |  |

## <a name="acrquarantinereader"></a>AcrQuarantineReader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | ACR 検査データ閲覧者 |
> | **Id** | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | **アクション** |  |
> | Microsoft.ContainerRegistry/registries/*/read |  |

## <a name="acrquarantinewriter"></a>AcrQuarantineWriter
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | ACR 検査データ作成者 |
> | **Id** | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | **アクション** |  |
> | Microsoft.ContainerRegistry/registries/*/write |  |
> | Microsoft.ContainerRegistry/registries/*/read |  |

## <a name="api-management-service-contributor"></a>API Management Service Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | API Management サービスを管理できます。ただし、それらへのアクセスは含まれません。 |
> | **Id** | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | **アクション** |  |
> | Microsoft.ApiManagement/service/* | API Management サービスの作成と管理 |
> | Microsoft.Authorization/*/read | 承認の読み取り |
> | Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="api-management-service-operator-role"></a>API Management Service Operator Role
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | サービスを管理できますが、API は対象外です |
> | **Id** | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | **アクション** |  |
> | Microsoft.ApiManagement/service/*/read | API Management サービス インスタンスの読み取り |
> | Microsoft.ApiManagement/service/backup/action | ユーザーが指定したストレージ アカウント内の指定されたコンテナーに API Management サービスをバックアップします。 |
> | Microsoft.ApiManagement/service/delete | API Management サービス インスタンスを削除します。 |
> | Microsoft.ApiManagement/service/managedeployments/action | SKU/ユニット数を変更し、API Management サービスのリージョン デプロイを追加または削除します。 |
> | Microsoft.ApiManagement/service/read | API Management サービス インスタンスのメタデータの読み取り |
> | Microsoft.ApiManagement/service/restore/action | ユーザーが指定したストレージ アカウント内の指定されたコンテナーからの API Management サービスの復元 |
> | Microsoft.ApiManagement/service/updatecertificate/action | API Management サービスの SSL 証明書をアップロードします。 |
> | Microsoft.ApiManagement/service/updatehostname/action | API Management サービスのカスタム ドメイン名を設定、更新、または削除します。 |
> | Microsoft.ApiManagement/service/write | API Management サービスの新しいインスタンスの作成 |
> | Microsoft.Authorization/*/read | 承認の読み取り |
> | Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | ユーザー キーの一覧を取得します。 |

## <a name="api-management-service-reader-role"></a>API Management Service Reader Role
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | サービスと API への読み取り専用アクセスです |
> | **Id** | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | **アクション** |  |
> | Microsoft.ApiManagement/service/*/read | API Management サービス インスタンスの読み取り |
> | Microsoft.ApiManagement/service/read | API Management サービス インスタンスのメタデータの読み取り |
> | Microsoft.Authorization/*/read | 承認の読み取り |
> | Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | ユーザー キーの一覧を取得します。 |

## <a name="application-insights-component-contributor"></a>Application Insights Component Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | Application Insights コンポーネントを管理できます |
> | **Id** | ae349356-3a1b-4a5e-921d-050484c6347e |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
> | Microsoft.Insights/components/* | Insights コンポーネントの作成と管理 |
> | Microsoft.Insights/webtests/* | Web テストの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="application-insights-snapshot-debugger"></a>Application Insights Snapshot Debugger
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | Application Insights Snapshot Debugger 機能を使用するアクセス許可をユーザーに付与します |
> | **Id** | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="automation-job-operator"></a>Automation ジョブ オペレーター
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | Automation Runbook を使用してジョブを作成および管理します。 |
> | **Id** | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Automation/automationAccounts/jobs/read | Azure Automation ジョブを取得します。 |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Azure Automation ジョブを再開します。 |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Azure Automation ジョブを停止します。 |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Hybrid Runbook Worker リソースを読み取ります。 |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Azure Automation ジョブ ストリームを取得します。 |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Azure Automation ジョブを中断します。 |
> | Microsoft.Automation/automationAccounts/jobs/write | Azure Automation ジョブを作成します。 |
> | Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="automation-operator"></a>Automation Operator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | Automation オペレーターはジョブを開始、停止、中断、再開することができます |
> | **Id** | d3881f73-407a-4167-8283-e981cbba0404 |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Hybrid Runbook Worker リソースを読み取ります。 |
> | Microsoft.Automation/automationAccounts/jobs/read | Azure Automation ジョブを取得します。 |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Azure Automation ジョブを再開します。 |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Azure Automation ジョブを停止します。 |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Azure Automation ジョブ ストリームを取得します。 |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Azure Automation ジョブを中断します。 |
> | Microsoft.Automation/automationAccounts/jobs/write | Azure Automation ジョブを作成します。 |
> | Microsoft.Automation/automationAccounts/jobSchedules/read | Azure Automation ジョブ スケジュールを取得します。 |
> | Microsoft.Automation/automationAccounts/jobSchedules/write | Azure Automation ジョブ スケジュールを作成します。 |
> | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Automation アカウントにリンクされているワークスペースを取得します |
> | Microsoft.Automation/automationAccounts/read | Azure Automation アカウントを取得します。 |
> | Microsoft.Automation/automationAccounts/runbooks/read | Azure Automation Runbook を取得します。 |
> | Microsoft.Automation/automationAccounts/schedules/read | Azure Automation スケジュール資産を取得します。 |
> | Microsoft.Automation/automationAccounts/schedules/write | Azure Automation スケジュール資産を作成または更新します。 |
> | Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Automation/automationAccounts/jobs/output/read | ジョブの出力を取得します。 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="automation-runbook-operator"></a>Automation Runbook オペレーター
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | Runbook のジョブを作成する方法については、Runbook のプロパティを参照してください。 |
> | **Id** | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Automation/automationAccounts/runbooks/read | Azure Automation Runbook を取得します。 |
> | Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="azure-stack-registration-owner"></a>Azure Stack Registration Owner
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | Azure Stack の登録を管理できます。 |
> | **Id** | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | **アクション** |  |
> | Microsoft.AzureStack/registrations/products/listDetails/action | Azure Stack Marketplace の製品の拡張詳細を取得します |
> | Microsoft.AzureStack/registrations/products/read | Azure Stack Marketplace の製品のプロパティを取得します |
> | Microsoft.AzureStack/registrations/read | Azure Stack の登録のプロパティを取得します |

## <a name="backup-contributor"></a>Backup Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | バックアップ サービスを管理できますが、資格情報コンテナーの作成や他のユーザーに対するアクセス権の付与を行うことはできません |
> | **Id** | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Network/virtualNetworks/read | 仮想ネットワークの定義を取得します。 |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp は、サービスによって使用される内部操作です。 |
> | Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | バックアップ管理操作の結果の管理 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Recovery Services コンテナーのバックアップ ファブリック内でのバックアップ コンテナーの作成および管理 |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | バックアップ ジョブの作成および管理 |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | ジョブをエクスポートします。 |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | "ジョブのエクスポート" 操作の結果を返します。 |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | バックアップの管理に関連するメタ データの作成および管理 |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | バックアップ管理操作の結果の作成および管理 |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/* | バックアップ ポリシーの作成および管理 |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | バックアップできるアイテムの作成および管理 |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | バックアップ アイテムの作成および管理 |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | バックアップ アイテムを保持するコンテナーの作成および管理 |
> | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Recovery Services の保護された項目と保護されたサーバーの概要を返します。 |
> | Microsoft.RecoveryServices/Vaults/certificates/* | Recovery Services コンテナー内のバックアップに関連する証明書の作成および管理 |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | コンテナーに関連する拡張情報の作成および管理 |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Recovery Services コンテナーのアラートを取得します。 |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | "コンテナーの取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトを取得します。 |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/* | 新しく作成されたコンテナーを取得するための検出操作の管理 |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | 登録済み ID の管理 |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* |  |
> | Microsoft.RecoveryServices/Vaults/usages/* | Recovery Services コンテナーの使用状況の作成および管理 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Storage/storageAccounts/read | ストレージ アカウントの一覧を返すか、指定されたストレージ アカウントのプロパティを取得します。 |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="backup-operator"></a>Backup Operator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | バックアップ サービスを管理できます (バックアップの削除、資格情報コンテナーの作成、他のユーザーに対するアクセス権の付与を除く) |
> | **Id** | 00c29273-979b-4161-815c-10b084fb9324 |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Network/virtualNetworks/read | 仮想ネットワークの定義を取得します。 |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp は、サービスによって使用される内部操作です。 |
> | Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | 操作の状態を返します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | 保護コンテナーに対して実行された操作の結果を取得します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | 保護された項目のバックアップを実行します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | 保護された項目に対して実行された操作の結果を取得します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | 保護された項目に対して実行された操作の状態を返します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | 保護された項目のオブジェクトの詳細を返します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | 保護された項目のインスタント項目回復をプロビジョニングします。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | 保護された項目の復旧ポイントを取得します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | 保護された項目の復旧ポイントを復元します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | 保護された項目のインスタント項目回復を取り消します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | バックアップ保護項目を作成します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | すべての登録済みコンテナーを返します。 |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | バックアップ ジョブの作成および管理 |
> | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | ジョブを取り消します。 |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | ジョブ操作の結果を返します。 |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | すべてのジョブ オブジェクトを返します。 |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | ジョブをエクスポートします。 |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | "ジョブのエクスポート" 操作の結果を返します。 |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Recovery Services コンテナーのバックアップ管理メタデータを返します。 |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | バックアップ管理操作の結果の作成および管理 |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | ポリシー操作の結果を取得します。 |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationStatus/read |  |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | すべての保護ポリシーを返します。 |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | バックアップできるアイテムの作成および管理 |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | すべての保護可能な項目の一覧を返します。 |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | すべての保護された項目の一覧を返します。 |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | サブスクリプションに属するすべてのコンテナーを返します。 |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Recovery Services の保護された項目と保護されたサーバーの概要を返します。 |
> | Microsoft.RecoveryServices/Vaults/certificates/write | "リソース証明書を更新" 操作では、リソース/コンテナー資格情報証明書を更新します。 |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | "拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。 |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/write | "拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。 |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Recovery Services コンテナーのアラートを取得します。 |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | "コンテナーの取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトを取得します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | コンテナーの一覧を更新します。 |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | "操作結果を取得" 操作を使用すると、非同期で送信された操作の状態と結果を取得できます。 |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | " コンテナーを取得" 操作を使用すると、リソースの登録済みコンテナーを取得できます。 |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | "サービス コンテナーを登録" 操作を使用すると、コンテナーを Recovery Services に登録できます。 |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services コンテナーの使用状況の詳細を返します。 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Storage/storageAccounts/read | ストレージ アカウントの一覧を返すか、指定されたストレージ アカウントのプロパティを取得します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="backup-reader"></a>Backup Reader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | バックアップ サービスを表示できますが、変更を行うことはできません |
> | **Id** | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | 操作の状態を返します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | 保護コンテナーに対して実行された操作の結果を取得します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | 保護された項目に対して実行された操作の結果を取得します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | 保護された項目に対して実行された操作の状態を返します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | 保護された項目のオブジェクトの詳細を返します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | すべての登録済みコンテナーを返します。 |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | ジョブ操作の結果を返します。 |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | すべてのジョブ オブジェクトを返します。 |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | ジョブをエクスポートします。 |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Recovery Services コンテナーのバックアップ管理メタデータを返します。 |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Recovery Services コンテナーに対するバックアップ操作の結果を返します。 |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | ポリシー操作の結果を取得します。 |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | すべての保護ポリシーを返します。 |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | すべての保護された項目の一覧を返します。 |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | サブスクリプションに属するすべてのコンテナーを返します。 |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Recovery Services の保護された項目と保護されたサーバーの概要を返します。 |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | "拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。 |
> | Microsoft.RecoveryServices/Vaults/read | "コンテナーの取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトを取得します。 |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | "操作結果を取得" 操作を使用すると、非同期で送信された操作の状態と結果を取得できます。 |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | " コンテナーを取得" 操作を使用すると、リソースの登録済みコンテナーを取得できます。 |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp は、サービスによって使用される内部操作です。 |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Recovery Services コンテナーのアラートを取得します。 |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | 保護された項目の復旧ポイントを取得します。 |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | "ジョブのエクスポート" 操作の結果を返します。 |
> | Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services コンテナーの使用状況の詳細を返します。 |

## <a name="billing-reader"></a>Billing Reader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | 課金データを読み取ることができます |
> | **Id** | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Billing/*/read | 課金情報の読み取り |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.Commerce/*/read |  |
> | Microsoft.Management/managementGroups/read | 認証済みユーザーの管理グループを一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="biztalk-contributor"></a>BizTalk Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | BizTalk Services を管理できます。ただし、それらへのアクセスは含まれません。 |
> | **Id** | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.BizTalkServices/BizTalk/* | BizTalk Services の作成と管理 |
> | Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="cdn-endpoint-contributor"></a>CDN Endpoint Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | CDN エンドポイントを管理できますが、アクセス権を他のユーザーに付与することはできません。 |
> | **Id** | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/* |  |
> | Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="cdn-endpoint-reader"></a>CDN Endpoint Reader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | CDN エンドポイントを表示できますが、変更はできません。 |
> | **Id** | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/*/read |  |
> | Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="cdn-profile-contributor"></a>CDN Profile Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | CDN プロファイルとそのエンドポイントを管理できますが、アクセス権を他のユーザーに付与することはできません。 |
> | **Id** | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/* |  |
> | Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="cdn-profile-reader"></a>CDN Profile Reader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | CDN プロファイルとそのエンドポイントを表示できますが、変更はできません。 |
> | **Id** | 8f96442b-4075-438f-813d-ad51ab4019af |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/*/read |  |
> | Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="classic-network-contributor"></a>Classic Network Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | 従来のネットワークを管理できます。ただし、それらへのアクセスは含まれません。 |
> | **Id** | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | 承認の読み取り |
> | Microsoft.ClassicNetwork/* | 従来のネットワークの作成と管理 |
> | Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="classic-storage-account-contributor"></a>Classic Storage Account Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | 従来のストレージ アカウントを管理できますが、アクセスすることはできません。 |
> | **Id** | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | 承認の読み取り |
> | Microsoft.ClassicStorage/storageAccounts/* | ストレージ アカウントの作成と管理 |
> | Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="classic-storage-account-key-operator-service-role"></a>従来のストレージ アカウント キー オペレーターのサービス ロール
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | 従来のストレージ アカウント キー オペレーターは、従来のストレージ アカウントでのキーの一覧表示と再生成を行うことができます |
> | **Id** | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | **アクション** |  |
> | Microsoft.ClassicStorage/storageAccounts/listkeys/action | ストレージ アカウントのアクセス キーを一覧表示します。 |
> | Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | ストレージ アカウントの既存のアクセス キーを再生成します。 |

## <a name="classic-virtual-machine-contributor"></a>Classic Virtual Machine Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | 従来の仮想マシンを管理できますが、アクセスすることはできません。また、接続先の仮想ネットワークやストレージ アカウントにもアクセスできません。 |
> | **Id** | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | 承認の読み取り |
> | Microsoft.ClassicCompute/domainNames/* | 従来のコンピューティング ドメイン名の作成と管理 |
> | Microsoft.ClassicCompute/virtualMachines/* | 仮想マシンの作成と管理 |
> | Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
> | Microsoft.ClassicNetwork/reservedIps/link/action | 予約済み IP をリンクします。 |
> | Microsoft.ClassicNetwork/reservedIps/read | 予約済み IP を取得します。 |
> | Microsoft.ClassicNetwork/virtualNetworks/join/action | 仮想ネットワークに参加します。 |
> | Microsoft.ClassicNetwork/virtualNetworks/read | 仮想ネットワークを取得します。 |
> | Microsoft.ClassicStorage/storageAccounts/disks/read | ストレージ アカウント ディスクを返します。 |
> | Microsoft.ClassicStorage/storageAccounts/images/read | ストレージ アカウント イメージを返します。 (非推奨になりました。 'Microsoft.ClassicStorage/storageAccounts/vmImages' を使用してください。) |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | ストレージ アカウントのアクセス キーを一覧表示します。 |
> | Microsoft.ClassicStorage/storageAccounts/read | 特定のアカウントのストレージ アカウントを返します。 |
> | Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="cleardb-mysql-db-contributor"></a>ClearDB MySQL DB Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | ClearDB MySQL データベースを管理できます。ただし、それらへのアクセスは含まれません。 |
> | **Id** | 9106cda0-8a86-4e81-b686-29a22c54effe |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |
> | successbricks.cleardb/databases/* | ClearDB MySQL データベースの作成と管理 |

## <a name="cosmos-db-account-reader-role"></a>Cosmos DB アカウントの閲覧者ロール
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | Cosmos DB アカウントのデータを読み取ることができます。 Azure Cosmos DB アカウントの管理については、「[DocumentDB Account Contributor](#documentdb-account-contributor)」をご覧ください。 |
> | **Id** | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り (各ユーザーに付与されたアクセス許可を読み取ることができます) |
> | Microsoft.DocumentDB/*/read | 任意のコレクションの読み取り |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | データベース アカウントの読み取り専用キーを読み取ります。 |
> | Microsoft.Insights/MetricDefinitions/read | メトリック定義を読み取ります。 |
> | Microsoft.Insights/Metrics/read | メトリックを読み取ります。 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="data-box-contributor"></a>Data Box Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | Data Box サービスですべてを管理できます (他のユーザーに対するアクセス権の付与を除く)。 |
> | **Id** | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |
> | Microsoft.Databox/* |  |

## <a name="data-box-operator"></a>Data Box Operator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | Data Box サービスを管理できます (注文の作成または注文の詳細の編集、および他のユーザーに対するアクセス権の付与を除く)。 |
> | **Id** | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |
> | Microsoft.Databox/jobs/listsecrets/action | 注文に関連する暗号化されていないシークレットを一覧表示します。 |

## <a name="data-factory-contributor"></a>Data Factory Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | データ ファクトリを管理できます。ただし、それらへのアクセスは含まれません。 |
> | **Id** | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.DataFactory/dataFactories/* | Data Factory と Data Factory に含まれる子リソースを作成および管理します。 |
> | Microsoft.DataFactory/factories/* | Data Factory と Data Factory に含まれる子リソースを作成および管理します。 |
> | Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="data-lake-analytics-developer"></a>Data Lake Analytics Developer
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | 独自のジョブを送信、監視、管理できますが、Data Lake Analytics アカウントを作成または削除することはできません。 |
> | **Id** | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.BigAnalytics/accounts/* |  |
> | Microsoft.DataLakeAnalytics/accounts/* |  |
> | Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |
> | **NotActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | Microsoft.DataLakeAnalytics/accounts/Delete | DataLakeAnalytics アカウントを削除します。 |
> | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | 他のユーザーによって送信されたジョブを取り消すアクセス許可を付与します。 |
> | Microsoft.DataLakeAnalytics/accounts/Write | DataLakeAnalytics アカウントを作成または更新します。 |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | DataLakeAnalytics アカウントのリンクされた DataLakeStore アカウントを作成または更新します。 |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | DataLakeAnalytics アカウントから DataLakeStore アカウントのリンクを解除します。 |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | DataLakeAnalytics アカウントのリンクされたストレージ アカウントを作成または更新します。 |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | DataLakeAnalytics アカウントからストレージ アカウントをリンク解除します。 |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | ファイアウォール規則を作成または更新します。 |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | ファイアウォール規則を削除します。 |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | コンピューティング ポリシーを作成または更新します。 |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | コンピューティング ポリシーを削除します。 |

## <a name="data-purger"></a>Data Purger
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | 分析データを削除することができます。 |
> | **Id** | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | **アクション** |  |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Insights/components/purge/action | Application Insights からデータを削除します。 |
> | Microsoft.OperationalInsights/workspaces/*/read |  |
> | Microsoft.OperationalInsights/workspaces/purge/action | ワークスペースから指定されたデータを削除します。 |

## <a name="devtest-labs-user"></a>DevTest Labs User
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | Azure DevTest Labs の仮想マシンに対して接続、起動、再起動、シャットダウンを行えます。 |
> | **Id** | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Compute/availabilitySets/read | 可用性セットのプロパティを取得します。 |
> | Microsoft.Compute/virtualMachines/*/read | 仮想マシンのプロパティ (VM サイズ、実行時の状態、VM 拡張機能など) の読み取り |
> | Microsoft.Compute/virtualMachines/deallocate/action | 仮想マシンを電源オフにし、コンピューティング リソースを解放します。 |
> | Microsoft.Compute/virtualMachines/read | 仮想マシンのプロパティを取得します。 |
> | Microsoft.Compute/virtualMachines/restart/action | 仮想マシンを再起動します。 |
> | Microsoft.Compute/virtualMachines/start/action | 仮想マシンを起動します。 |
> | Microsoft.DevTestLab/*/read | ラボのプロパティの読み取り |
> | Microsoft.DevTestLab/labs/createEnvironment/action | ラボで仮想マシンを作成します。 |
> | Microsoft.DevTestLab/labs/claimAnyVm/action | ラボ内のランダムに要求可能な仮想マシンを要求します。 |
> | Microsoft.DevTestLab/labs/formulas/delete | 数式を削除します。 |
> | Microsoft.DevTestLab/labs/formulas/read | 数式を読み取ります。 |
> | Microsoft.DevTestLab/labs/formulas/write | 数式を追加または変更します。 |
> | Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | ラボ ポリシーを評価します。 |
> | Microsoft.DevTestLab/labs/virtualMachines/claim/action | 既存の仮想マシンの所有権を取得します。 |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | ロード バランサーのバックエンド アドレス プールを接続します。 |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | ロード バランサーの受信 NAT 規則を接続します。 |
> | Microsoft.Network/networkInterfaces/*/read | ネットワーク インターフェイスのプロパティ (例: そのネットワーク インターフェイスが含まれているすべてのロード バランサー) の読み取り |
> | Microsoft.Network/networkInterfaces/join/action | 仮想マシンをネットワーク インターフェイスに接続します。 |
> | Microsoft.Network/networkInterfaces/read | ネットワーク インターフェイスの定義を取得します。  |
> | Microsoft.Network/networkInterfaces/write | ネットワーク インターフェイスを作成するか、既存のネットワーク インターフェイスを更新します。  |
> | Microsoft.Network/publicIPAddresses/*/read | パブリック IP アドレスのプロパティの読み取り |
> | Microsoft.Network/publicIPAddresses/join/action | パブリック IP アドレスに接続します。 |
> | Microsoft.Network/publicIPAddresses/read | パブリック IP アドレス定義を取得します。 |
> | Microsoft.Network/virtualNetworks/subnets/join/action | 仮想ネットワークに参加します。 |
> | Microsoft.Resources/deployments/operations/read | デプロイ操作を取得または一覧表示します。 |
> | Microsoft.Resources/deployments/read | デプロイを取得または一覧表示します。 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Storage/storageAccounts/listKeys/action | 指定されたストレージ アカウントのアクセス キーを返します。 |
> | **NotActions** |  |
> | Microsoft.Compute/virtualMachines/vmSizes/read | 仮想マシンを更新する際に使用できるサイズを一覧表示します。 |

## <a name="dns-zone-contributor"></a>DNS Zone Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | Azure DNS の DNS ゾーンとレコード セットを管理できますが、それにアクセスできるユーザーを制御することはできません。 |
> | **Id** | befefa01-2a29-4197-83a8-272ff33ce314 |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
> | Microsoft.Network/dnsZones/* | DNS ゾーンとレコードの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="documentdb-account-contributor"></a>DocumentDB Account Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | Azure Cosmos DB アカウントを管理できます。 Azure Cosmos DB は以前は DocumentDB と呼ばれていました。 |
> | **Id** | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.DocumentDb/databaseAccounts/* | Azure Cosmos DB アカウントの作成と管理 |
> | Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="intelligent-systems-account-contributor"></a>Intelligent Systems Account Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | Intelligent Systems のアカウントを管理できます。ただし、それらへのアクセスは含まれません。 |
> | **Id** | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
> | Microsoft.IntelligentSystems/accounts/* | Intelligent Systems アカウントの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="key-vault-contributor"></a>Key Vault Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | キー コンテナーを管理できますが、アクセスすることはできません。 |
> | **Id** | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
> | Microsoft.KeyVault/* |  |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |
> | **NotActions** |  |
> | Microsoft.KeyVault/locations/deletedVaults/purge/action | 論理的に削除された Key Vault を消去します。 |
> | Microsoft.KeyVault/hsmPools/* |  |

## <a name="lab-creator"></a>Lab Creator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | Azure Lab アカウントで管理対象のラボを作成、管理、削除できます。 |
> | **Id** | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.LabServices/labAccounts/*/read |  |
> | Microsoft.LabServices/labAccounts/createLab/action | ラボ アカウントにラボを作成します。 |
> | Microsoft.LabServices/labAccounts/sizes/getRegionalAvailability/action | ラボ アカウントの下で構成されたサイズ カテゴリ別のリージョン別の提供状況を取得します。 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="log-analytics-contributor"></a>Log Analytics 共同作成者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | Log Analytics 共同作成者は、すべての監視データを読み取り、監視設定を編集できます。 監視設定の編集には、VM 拡張機能の VM への追加、Azure Storage からログの収集を設定できるようにするためのストレージ アカウント キーの読み取り、Automation アカウントの作成と構成、ソリューションの追加、すべての Azure リソースでの Azure 診断の構成が含まれます。 |
> | **Id** | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | **アクション** |  |
> | */read | 機密データを除くあらゆる種類のリソースの読み取り |
> | Microsoft.Automation/automationAccounts/* |  |
> | Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | ストレージ アカウントのアクセス キーを一覧表示します。 |
> | Microsoft.Compute/virtualMachines/extensions/* |  |
> | Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
> | Microsoft.Insights/diagnosticSettings/* | 分析サーバーの診断の設定の作成、更新、または読み取りを行います |
> | Microsoft.OperationalInsights/* |  |
> | Microsoft.OperationsManagement/* |  |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | 指定されたストレージ アカウントのアクセス キーを返します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="log-analytics-reader"></a>Log Analytics 閲覧者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | Log Analytics Reader は、すべての監視データの表示と検索、およびすべての Azure リソース上の Azure 診断構成の表示など、監視設定の表示を行うことができます。 |
> | **Id** | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **アクション** |  |
> | */read | 機密データを除くあらゆる種類のリソースの読み取り |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | 新しいエンジンを使用して検索します。 |
> | Microsoft.OperationalInsights/workspaces/search/action | 検索クエリを実行します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |
> | **NotActions** |  |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/read | ワークスペースの共有キーを取得します。 これらのキーを使用して、Microsoft Operational Insights エージェントをワークスペースに接続します。 |

## <a name="logic-app-contributor"></a>Logic App Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | ロジック アプリを管理できますが、アクセスすることはできません。 |
> | **Id** | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | ストレージ アカウントのアクセス キーを一覧表示します。 |
> | Microsoft.ClassicStorage/storageAccounts/read | 特定のアカウントのストレージ アカウントを返します。 |
> | Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
> | Microsoft.Insights/diagnosticSettings/* | 分析サーバーの診断の設定の作成、更新、または読み取りを行います |
> | Microsoft.Insights/logdefinitions/* | このアクセス許可は、ポータルを使用してアクティビティ ログにアクセスする必要があるユーザーに必要です。 アクティビティ ログのログのカテゴリを一覧表示します。 |
> | Microsoft.Insights/metricDefinitions/* | メトリック定義 (リソースの使用可能なメトリックの種類の一覧) を読み取ります。 |
> | Microsoft.Logic/* | Logic Apps リソースを管理します。 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/operationresults/read | サブスクリプション操作の結果を取得します。 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Storage/storageAccounts/listkeys/action | 指定されたストレージ アカウントのアクセス キーを返します。 |
> | Microsoft.Storage/storageAccounts/read | ストレージ アカウントの一覧を返すか、指定されたストレージ アカウントのプロパティを取得します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |
> | Microsoft.Web/connectionGateways/* | 接続ゲートウェイを作成および管理します。 |
> | Microsoft.Web/connections/* | 接続を作成および管理します。 |
> | Microsoft.Web/customApis/* | カスタム API を作成および管理します。 |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | App Service プランのプロパティを取得します。 |
> | Microsoft.Web/sites/functions/listSecrets/action | Web アプリの関数のシークレットを一覧表示します。 |

## <a name="logic-app-operator"></a>Logic App Operator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | ロジック アプリの読み取り、有効化、無効化ができます。 |
> | **Id** | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/*/read | Insights のアラート ルールを読み取ります |
> | Microsoft.Insights/diagnosticSettings/*/read | Logic Apps の診断設定を取得します |
> | Microsoft.Insights/metricDefinitions/*/read | Logic Apps の利用可能なメトリックを取得します。 |
> | Microsoft.Logic/*/read | Logic Apps リソースを読み取ります。 |
> | Microsoft.Logic/workflows/disable/action | ワークフローを無効にします。 |
> | Microsoft.Logic/workflows/enable/action | ワークフローを有効にします。 |
> | Microsoft.Logic/workflows/validate/action | ワークフローを検証します。 |
> | Microsoft.Resources/deployments/operations/read | デプロイ操作を取得または一覧表示します。 |
> | Microsoft.Resources/subscriptions/operationresults/read | サブスクリプション操作の結果を取得します。 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |
> | Microsoft.Web/connectionGateways/*/read | 接続ゲートウェイを読み取ります。 |
> | Microsoft.Web/connections/*/read | 接続を読み取ります。 |
> | Microsoft.Web/customApis/*/read | カスタム API を読み取ります。 |
> | Microsoft.Web/serverFarms/read | App Service プランのプロパティを取得します。 |

## <a name="managed-application-operator-role"></a>Managed Application Operator Role
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | マネージド アプリケーション リソースに対する読み取りとアクションの実行が可能です。 |
> | **Id** | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | **アクション** |  |
> | Microsoft.Solutions/applications/read | アプリケーションの一覧を取得します。 |

## <a name="managed-identity-contributor"></a>Managed Identity Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | ユーザー割り当て ID の作成、読み取り、更新、削除を行います |
> | **Id** | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | **アクション** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/write |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/delete |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="managed-identity-operator"></a>Managed Identity Operator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | ユーザー割り当て ID の読み取りと割り当てを行います |
> | **Id** | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **アクション** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="management-group-contributor"></a>管理グループ共同作成者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | 管理グループ共同作成者ロール |
> | **Id** | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | **アクション** |  |
> | Microsoft.Management/managementGroups/delete | 管理グループを削除します。 |
> | Microsoft.Management/managementGroups/read | 認証済みユーザーの管理グループを一覧表示します。 |
> | Microsoft.Management/managementGroups/subscriptions/delete | 管理グループからサブスクリプションの関連付けを解除します。 |
> | Microsoft.Management/managementGroups/subscriptions/write | 既存のサブスクリプションと管理グループを関連付けます。 |
> | Microsoft.Management/managementGroups/write | 管理グループを作成または更新します。 |

## <a name="management-group-reader"></a>管理グループ閲覧者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | 管理グループ閲覧者ロール |
> | **Id** | ac63b705-f282-497d-ac71-919bf39d939d |
> | **アクション** |  |
> | Microsoft.Management/managementGroups/read | 認証済みユーザーの管理グループを一覧表示します。 |

## <a name="monitoring-contributor"></a>Monitoring Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | すべての監視データを読み取り、監視設定を編集できます。 [「Azure Monitor での役割、アクセス許可、およびセキュリティの概要」](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles)も参照してください。 |
> | **Id** | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | **アクション** |  |
> | */read | 機密データを除くあらゆる種類のリソースの読み取り |
> | Microsoft.AlertsManagement/alerts/* |  |
> | Microsoft.AlertsManagement/alertsSummary/* |  |
> | Microsoft.Insights/actiongroups/* |  |
> | Microsoft.Insights/AlertRules/* | アラート ルールの読み取り/書き込み/削除を実行します。 |
> | Microsoft.Insights/components/* | Application Insights コンポーネントの読み取り/書き込み/削除を実行します。 |
> | Microsoft.Insights/DiagnosticSettings/* | 診断設定の読み取り/書き込み/削除を実行します。 |
> | Microsoft.Insights/eventtypes/* | サブスクリプションのアクティビティ ログのイベント (管理イベント) を一覧表示します。 このアクセス許可は、アクティビティ ログへのプログラムによるアクセスとポータル アクセスの両方に適用されます。 |
> | Microsoft.Insights/LogDefinitions/* | このアクセス許可は、ポータルを使用してアクティビティ ログにアクセスする必要があるユーザーに必要です。 アクティビティ ログのログのカテゴリを一覧表示します。 |
> | Microsoft.Insights/metricalerts/* |  |
> | Microsoft.Insights/MetricDefinitions/* | メトリック定義 (リソースの使用可能なメトリックの種類の一覧) を読み取ります。 |
> | Microsoft.Insights/Metrics/* | リソースのメトリックを読み取ります。 |
> | Microsoft.Insights/Register/Action | Microsoft Insights プロバイダーを登録します。 |
> | Microsoft.Insights/scheduledqueryrules/* |  |
> | Microsoft.Insights/webtests/* | Application Insights の Web テストの読み取り/書き込み/削除を行います。 |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Log Analytics ソリューション パックの読み取り/書き込み/削除を行います。 |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* | Log Analytics によって保存された検索の読み取り/書き込み/削除を行います。 |
> | Microsoft.OperationalInsights/workspaces/search/action | 検索クエリを実行します。 |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/action | ワークスペースの共有キーを取得します。 これらのキーを使用して、Microsoft Operational Insights エージェントをワークスペースに接続します。 |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Log Analytics ストレージ インサイト構成の読み取り/書き込み/削除を行います。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |
> | Microsoft.WorkloadMonitor/workloads/* |  |
> | Microsoft.WorkloadMonitor/workloadInsights/* |  |

## <a name="monitoring-reader"></a>Monitoring Reader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | すべての監視データ (メトリック、ログなど) を読み取ることができます。 [「Azure Monitor での役割、アクセス許可、およびセキュリティの概要」](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles)も参照してください。 |
> | **Id** | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | **アクション** |  |
> | */read | 機密データを除くあらゆる種類のリソースの読み取り |
> | Microsoft.OperationalInsights/workspaces/search/action | 検索クエリを実行します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="network-contributor"></a>Network Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | ネットワークを管理できます。ただし、それらへのアクセスは含まれません。 |
> | **Id** | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
> | Microsoft.Network/* | ネットワークの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="new-relic-apm-account-contributor"></a>New Relic APM Account Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | New Relic Application Performance Management のアカウントとアプリケーションを管理できます。ただし、それらへのアクセスは含まれません。 |
> | **Id** | 5d28c62d-5b37-4476-8438-e587778df237 |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |
> | NewRelic.APM/accounts/* |  |

## <a name="reader-and-data-access"></a>Reader and Data Access
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | すべてを表示することができますが、ストレージ アカウントや含まれるリソースの削除や作成はできません。 ストレージ アカウント キーへのアクセスを使用して、ストレージ アカウントに含まれるすべてのデータへの読み取り/書き込みアクセスも許可されます。 |
> | **Id** | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | **アクション** |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | 指定されたストレージ アカウントのアクセス キーを返します。 |
> | Microsoft.Storage/storageAccounts/read | ストレージ アカウントの一覧を返すか、指定されたストレージ アカウントのプロパティを取得します。 |

## <a name="redis-cache-contributor"></a>Redis Cache Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | Redis Caches を管理できます。ただし、それらへのアクセスは含まれません。 |
> | **Id** | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Cache/redis/* | Redis キャッシュの作成と管理 |
> | Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="resource-policy-contributor-preview"></a>リソース ポリシーの共同作成者 (プレビュー)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | (プレビュー) リソース ポリシーの作成/変更、サポート チケットの作成、リソース/階層の読み取りを実行する権限により、ユーザーを EA からバックフィルしました。 |
> | **Id** | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | **アクション** |  |
> | */read | 機密データを除くあらゆる種類のリソースの読み取り |
> | Microsoft.Authorization/policyassignments/* | ポリシーの割り当ての作成と管理 |
> | Microsoft.Authorization/policydefinitions/* | ポリシー定義の作成と管理 |
> | Microsoft.Authorization/policysetdefinitions/* | ポリシー セットの作成と管理 |
> | Microsoft.PolicyInsights/* |  |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="scheduler-job-collections-contributor"></a>Scheduler Job Collections Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | スケジューラ ジョブ コレクションを管理できます。ただし、それらへのアクセスは含まれません。 |
> | **Id** | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Scheduler/jobcollections/* | ジョブ コレクションの作成と管理 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="search-service-contributor"></a>Search Service Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | Search サービスを管理できます。ただし、それらへのアクセスは含まれません。 |
> | **Id** | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Search/searchServices/* | 検索サービスの作成と管理 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="security-admin"></a>セキュリティ管理者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | Security Center のみ: セキュリティ ポリシーの表示、セキュリティ状態の表示、セキュリティ ポリシーの編集、アラートと推奨事項の表示、アラートと推奨事項の却下を行うことができます |
> | **Id** | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Authorization/policyAssignments/* | ポリシーの割り当ての作成と管理 |
> | Microsoft.Authorization/policyDefinitions/* | ポリシー定義の作成と管理 |
> | Microsoft.Authorization/policySetDefinitions/* | ポリシー セットの作成と管理 |
> | Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
> | Microsoft.Management/managementGroups/read | 認証済みユーザーの管理グループを一覧表示します。 |
> | Microsoft.operationalInsights/workspaces/*/read | Log Analytics のデータの表示 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Security/*/read | セキュリティ コンポーネントとポリシーの読み取り |
> | Microsoft.Security/locations/alerts/activate/action | セキュリティ アラートをアクティブ化します。 |
> | Microsoft.Security/locations/alerts/dismiss/action | セキュリティ アラートを無視します |
> | Microsoft.Security/locations/tasks/activate/action | セキュリティ推奨事項をアクティブにします。 |
> | Microsoft.Security/locations/tasks/dismiss/action | セキュリティ推奨事項を無視します。 |
> | Microsoft.Security/policies/write | セキュリティ ポリシーを更新します。 |
> | Microsoft.Security/securityContacts/write | セキュリティ連絡先を更新します。 |
> | Microsoft.Security/securityContacts/delete | セキュリティ連絡先を削除します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="security-manager"></a>Security Manager
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | セキュリティ コンポーネント、セキュリティ ポリシー、Virtual Machines を管理できます |
> | **Id** | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.ClassicCompute/*/read | 従来の仮想マシンの構成情報の読み取り |
> | Microsoft.ClassicCompute/virtualMachines/*/write | 従来の仮想マシンの構成の書き込み |
> | Microsoft.ClassicNetwork/*/read | 従来のネットワークに関する構成情報の読み取り |
> | Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Security/* | セキュリティ コンポーネントおよびポリシーの作成と管理 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="security-reader"></a>セキュリティ リーダー
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | Security Center のみ: 推奨事項とアラート、セキュリティ ポリシー、セキュリティの状態を表示することはできますが、変更することはできません |
> | **Id** | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
> | Microsoft.operationalInsights/workspaces/*/read | Log Analytics のデータの表示 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Security/*/read | セキュリティ コンポーネントとポリシーの読み取り |
> | Microsoft.Support/* | サポート チケットの作成と管理 |
> | Microsoft.Management/managementGroups/read | 認証済みユーザーの管理グループを一覧表示します。 |

## <a name="site-recovery-contributor"></a>Site Recovery Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | 資格情報コンテナーの作成とロールの割り当てを除く、Site Recovery サービスを管理できます |
> | **Id** | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
> | Microsoft.Network/virtualNetworks/read | 仮想ネットワークの定義を取得します。 |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp は、サービスによって使用される内部操作です。 |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp は、サービスによって使用される内部操作です。 |
> | Microsoft.RecoveryServices/Vaults/certificates/write | "リソース証明書を更新" 操作では、リソース/コンテナー資格情報証明書を更新します。 |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | コンテナーに関連する拡張情報の作成および管理 |
> | Microsoft.RecoveryServices/Vaults/read | "コンテナーの取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトを取得します。 |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | 登録済み ID の管理 |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | レプリケーションの警告設定の作成または更新 |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | イベントを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/* | レプリケーション ファブリックの作成と管理 |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | レプリケーション ジョブの作成と管理 |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/* | レプリケーション ポリシーの作成と管理 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | 復旧計画の作成と管理 |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* | Recovery Services コンテナーのストレージ構成の作成と管理 |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read | Recovery Services コンテナーのトークン情報を返します。 |
> | Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services コンテナーの使用状況の詳細を返します。 |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | "コンテナー トークン" 操作を使用すると、コンテナー レベルのバックエンド操作のコンテナー トークンを取得できます。 |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Recovery Services コンテナーのアラートの読み取り |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Storage/storageAccounts/read | ストレージ アカウントの一覧を返すか、指定されたストレージ アカウントのプロパティを取得します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="site-recovery-operator"></a>Site Recovery Operator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | フェールオーバーとフェールバックを実行できますが、その他の Site Recovery 管理操作は実行しません |
> | **Id** | 494ae006-db33-4328-bf46-533a6560a3ca |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
> | Microsoft.Network/virtualNetworks/read | 仮想ネットワークの定義を取得します。 |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp は、サービスによって使用される内部操作です。 |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp は、サービスによって使用される内部操作です。 |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | "拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。 |
> | Microsoft.RecoveryServices/Vaults/read | "コンテナーの取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトを取得します。 |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | "操作結果を取得" 操作を使用すると、非同期で送信された操作の状態と結果を取得できます。 |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | " コンテナーを取得" 操作を使用すると、リソースの登録済みコンテナーを取得できます。 |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | アラート設定を読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | イベントを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | ファブリックの一貫性を確認します。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | ファブリックを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | ゲートウェイを再関連付けします。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | ファブリックの証明書を更新します。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | ネットワークを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | ネットワーク マッピングを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | 保護コンテナーを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | 保護可能な項目を読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | 復旧ポイントを適用します。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | フェールオーバーのコミット。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | 計画されたフェールオーバー。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | 保護された項目を読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | レプリケーションの復旧ポイントを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | レプリケーションを修復します。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | 保護された項目を再保護します。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | テスト フェールオーバー |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | テスト フェールオーバーのクリーンアップ。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | フェールオーバー |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | モビリティ サービスを更新します。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | 保護コンテナー マッピングを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Recovery Services プロバイダーを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | プロバイダーを更新します。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | ストレージの分類を読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | ストレージ分類マッピングを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | vCenter を読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | レプリケーション ジョブの作成と管理 |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | ポリシーを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | フェールオーバーのコミットの復旧計画。 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | 計画されたフェールオーバーの復旧計画。 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | 復旧計画を読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | 再保護の復旧計画。 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | テスト フェールオーバーの復旧計画。 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | テスト フェールオーバーのクリーンアップの復旧計画。 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | フェールオーバーの復旧計画。 |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Recovery Services コンテナーのアラートの読み取り |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read | Recovery Services コンテナーのトークン情報を返します。 |
> | Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services コンテナーの使用状況の詳細を返します。 |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | "コンテナー トークン" 操作を使用すると、コンテナー レベルのバックエンド操作のコンテナー トークンを取得できます。 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Storage/storageAccounts/read | ストレージ アカウントの一覧を返すか、指定されたストレージ アカウントのプロパティを取得します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="site-recovery-reader"></a>Site Recovery Reader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | Site Recovery の状態を表示できますが、その他の管理操作は実行できません |
> | **Id** | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp は、サービスによって使用される内部操作です。 |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | "拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。 |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Recovery Services コンテナーのアラートを取得します。 |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/read | "コンテナーの取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトを取得します。 |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | "操作結果を取得" 操作を使用すると、非同期で送信された操作の状態と結果を取得できます。 |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | " コンテナーを取得" 操作を使用すると、リソースの登録済みコンテナーを取得できます。 |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | アラート設定を読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | イベントを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | ファブリックを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | ネットワークを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | ネットワーク マッピングを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | 保護コンテナーを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | 保護可能な項目を読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | 保護された項目を読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | レプリケーションの復旧ポイントを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | 保護コンテナー マッピングを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Recovery Services プロバイダーを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | ストレージの分類を読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | ストレージ分類マッピングを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | vCenter を読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationJobs/read | ジョブを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | ポリシーを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | 復旧計画を読み取ります |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read | Recovery Services コンテナーのトークン情報を返します。 |
> | Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services コンテナーの使用状況の詳細を返します。 |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | "コンテナー トークン" 操作を使用すると、コンテナー レベルのバックエンド操作のコンテナー トークンを取得できます。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="sql-db-contributor"></a>SQL DB Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | SQL データベースを管理できます。ただし、それらへのアクセスは含まれません。 また、セキュリティ関連のポリシーまたは親 SQL Server を管理することはできません。 |
> | **Id** | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/databases/* | SQL データベースの作成と管理 |
> | Microsoft.Sql/servers/read | サーバーの一覧を返すか、指定されたサーバーのプロパティを取得します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |
> | **NotActions** |  |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | 監査ポリシーを編集することはできません |
> | Microsoft.Sql/servers/databases/auditingSettings/* | 監査設定を編集することはできません |
> | Microsoft.Sql/servers/databases/auditRecords/read | データベースの BLOB 監査レコードを取得します。 |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | 接続ポリシーを編集することはできません |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | データ マスク ポリシーを編集することはできません |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | セキュリティの警告のポリシーを編集することはできません |
> | Microsoft.Sql/servers/databases/securityMetrics/* | セキュリティ基準を編集することはできません |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |

## <a name="sql-security-manager"></a>SQL Security Manager
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | SQL サーバーとデータベースのセキュリティ関連のポリシーを管理できます。ただし、それらへのアクセスは管理できません。 |
> | **Id** | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | Microsoft 承認の読み取り |
> | Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | ストレージ アカウントや SQL Database などのリソースをサブネットに結合します。 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Sql/servers/auditingPolicies/* | SQL サーバー監査ポリシーの作成と管理 |
> | Microsoft.Sql/servers/auditingSettings/* | SQL サーバー監査設定の作成と管理 |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | SQL サーバー データベース監査ポリシーの作成と管理 |
> | Microsoft.Sql/servers/databases/auditingSettings/* | SQL サーバー データベース監査設定の作成と管理 |
> | Microsoft.Sql/servers/databases/auditRecords/read | 監査レコードの読み取り |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | SQL サーバー データベース接続ポリシーの作成と管理 |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | SQL サーバー データベース データ マスク ポリシーの作成と管理 |
> | Microsoft.Sql/servers/databases/read | データベースの一覧を返すか、指定されたデータベースのプロパティを取得します。 |
> | Microsoft.Sql/servers/databases/schemas/read | データベースのスキーマの一覧を取得します。 |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/read | テーブルの列の一覧を取得します。 |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/read | データベースのテーブルの一覧を取得します。 |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | SQL サーバー データベース セキュリティの警告のポリシーの作成と管理 |
> | Microsoft.Sql/servers/databases/securityMetrics/* | SQL サーバー データベース セキュリティ基準の作成と管理 |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/firewallRules/* |  |
> | Microsoft.Sql/servers/read | サーバーの一覧を返すか、指定されたサーバーのプロパティを取得します。 |
> | Microsoft.Sql/servers/securityAlertPolicies/* | SQL サーバー セキュリティの警告のポリシーの作成と管理 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="sql-server-contributor"></a>SQL Server Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | SQL サーバーとデータベースを管理できます。ただし、それらへのアクセスや、それらのセキュリティ関連ポリシーは管理できません。 |
> | **Id** | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/* | SQL サーバーの作成と管理 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |
> | **NotActions** |  |
> | Microsoft.Sql/servers/auditingPolicies/* | SQL サーバー監査ポリシーは編集できません |
> | Microsoft.Sql/servers/auditingSettings/* | SQL サーバー監査設定は編集できません |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | SQL サーバー データベース監査ポリシーは編集できません |
> | Microsoft.Sql/servers/databases/auditingSettings/* | SQL サーバー データベース監査設定は編集できません |
> | Microsoft.Sql/servers/databases/auditRecords/read | 監査レコードを読み取ることはできません |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | SQL サーバー データベース接続ポリシーは編集できません |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | SQL サーバー データベース データ マスク ポリシーは編集できません |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | SQL サーバー データベース セキュリティの警告のポリシーは編集できません |
> | Microsoft.Sql/servers/databases/securityMetrics/* | SQL サーバー データベース セキュリティ基準は編集できません |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/securityAlertPolicies/* | SQL サーバー セキュリティの警告のポリシーは編集できません |

## <a name="storage-account-contributor"></a>Storage Account Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | ストレージ アカウントを管理できますが、アクセスすることはできません。 |
> | **Id** | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | あらゆる承認の読み取り |
> | Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
> | Microsoft.Insights/diagnosticSettings/* | 診断設定の管理 |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | ストレージ アカウントや SQL Database などのリソースをサブネットに結合します。 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Storage/storageAccounts/* | ストレージ アカウントの作成と管理 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="storage-account-key-operator-service-role"></a>ストレージ アカウント キー オペレーターのサービス ロール
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | ストレージ アカウント キー オペレーターは、ストレージ アカウントでのキーの一覧表示と再生成を行うことができます |
> | **Id** | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | **アクション** |  |
> | Microsoft.Storage/storageAccounts/listkeys/action | 指定されたストレージ アカウントのアクセス キーを返します。 |
> | Microsoft.Storage/storageAccounts/regeneratekey/action | 指定されたストレージ アカウントのアクセス キーを再生成します。 |

## <a name="storage-blob-data-contributor-preview"></a>ストレージ BLOB データ共同作成者 (プレビュー)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | Azure Storage Blob コンテナーおよびデータに対する読み取りアクセス、書き込みアクセス、削除アクセスを許可します |
> | **Id** | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | **アクション** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | コンテナーを削除した結果を返します。 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | コンテナーまたはコンテナーの一覧を返します。 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | BLOB コンテナーの設定またはリースの結果を返します。 |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | BLOB を削除した結果を返します |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | BLOB または BLOB の一覧を返します |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | BLOB の書き込みの結果を返します |

## <a name="storage-blob-data-reader-preview"></a>ストレージ BLOB データ閲覧者 (プレビュー)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | Azure Storage Blob コンテナーおよびデータに対する読み取りアクセスを許可します |
> | **Id** | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | **アクション** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | コンテナーまたはコンテナーの一覧を返します。 |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | BLOB または BLOB の一覧を返します |

## <a name="storage-queue-data-contributor-preview"></a>ストレージ キュー データ共同作成者 (プレビュー)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | Azure Storage キューおよびキュー メッセージに対する読み取りアクセス、書き込みアクセス、削除アクセスを許可します |
> | **Id** | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | **アクション** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/delete | キューを削除した結果を返します。 |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | キューまたはキューの一覧を返します。 |
> | Microsoft.Storage/storageAccounts/queueServices/queues/write | キューの書き込みの結果を返します。 |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | メッセージを削除した結果を返します |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | メッセージを返します |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | メッセージの書き込みの結果を返します |

## <a name="storage-queue-data-reader-preview"></a>ストレージ キュー データ閲覧者 (プレビュー)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | Azure Storage キューおよびキュー メッセージに対する読み取りアクセスを許可します |
> | **Id** | 19e7f393-937e-4f77-808e-94535e297925 |
> | **アクション** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | キューまたはキューの一覧を返します。 |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | メッセージを返します。 |

## <a name="support-request-contributor"></a>Support Request Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | Support request を作成して管理できます |
> | **Id** | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | 承認の読み取り |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="traffic-manager-contributor"></a>Traffic Manager Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | Traffic Manager プロファイルを管理できますが、それにアクセスできるユーザーを制御することはできません。 |
> | **Id** | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
> | Microsoft.Network/trafficManagerProfiles/* |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="user-access-administrator"></a>User Access Administrator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | Azure リソースに対するユーザー アクセスを管理します。 |
> | **Id** | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **アクション** |  |
> | */read | 機密データを除くあらゆる種類のリソースの読み取り |
> | Microsoft.Authorization/* | 承認の管理 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="virtual-machine-administrator-login"></a>Virtual Machine Administrator Login
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | ポータルで仮想マシンを表示し、管理者としてログインします |
> | **Id** | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | **アクション** |  |
> | Microsoft.Network/publicIPAddresses/read | パブリック IP アドレス定義を取得します。 |
> | Microsoft.Network/virtualNetworks/read | 仮想ネットワークの定義を取得します。 |
> | Microsoft.Network/loadBalancers/read | ロード バランサー定義を取得します。 |
> | Microsoft.Network/networkInterfaces/read | ネットワーク インターフェイスの定義を取得します。  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | 仮想マシンに通常のユーザーとしてログインします。 |
> | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Windows 管理者または Linux のルート ユーザーの権限で仮想マシンにログインします。 |

## <a name="virtual-machine-contributor"></a>Virtual Machine Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | 仮想マシンを管理できますが、アクセスすることはできません。また、接続先の仮想ネットワークやストレージ アカウントにもアクセスできません。 |
> | **Id** | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | 承認の読み取り |
> | Microsoft.Compute/availabilitySets/* | コンピューティング可用性セットの作成と管理 |
> | Microsoft.Compute/locations/* | コンピューティングの場所の作成と管理 |
> | Microsoft.Compute/virtualMachines/* | 仮想マシンの作成と管理 |
> | Microsoft.Compute/virtualMachineScaleSets/* | 仮想マシン スケールセットの作成と管理 |
> | Microsoft.DevTestLab/schedules/* |  |
> | Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
> | Microsoft.Network/applicationGateways/backendAddressPools/join/action | アプリケーション ゲートウェイのバックエンド アドレス プールを接続します。 |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | ロード バランサーのバックエンド アドレス プールを接続します。 |
> | Microsoft.Network/loadBalancers/inboundNatPools/join/action | ロード バランサーの受信 NAT プールを接続します。 |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | ロード バランサーの受信 NAT 規則を接続します。 |
> | Microsoft.Network/loadBalancers/probes/join/action | ロード バランサーのプローブの使用を許可します。 たとえば、このアクセス許可では、VM スケール セットの healthProbe プロパティでプローブを参照できます。 |
> | Microsoft.Network/loadBalancers/read | ロード バランサー定義を取得します。 |
> | Microsoft.Network/locations/* | ネットワークの場所の作成と管理 |
> | Microsoft.Network/networkInterfaces/* | ネットワーク インターフェイスの作成と管理 |
> | Microsoft.Network/networkSecurityGroups/join/action | ネットワーク セキュリティ グループに参加します。 |
> | Microsoft.Network/networkSecurityGroups/read | ネットワーク セキュリティ グループの定義を取得します。 |
> | Microsoft.Network/publicIPAddresses/join/action | パブリック IP アドレスに接続します。 |
> | Microsoft.Network/publicIPAddresses/read | パブリック IP アドレス定義を取得します。 |
> | Microsoft.Network/virtualNetworks/read | 仮想ネットワークの定義を取得します。 |
> | Microsoft.Network/virtualNetworks/subnets/join/action | 仮想ネットワークに参加します。 |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | 保護された項目のオブジェクトの詳細を返します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | バックアップ保護項目を作成します。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | バックアップの保護インテントを作成します |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | すべての保護ポリシーを返します。 |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/write | 保護ポリシーを作成します。 |
> | Microsoft.RecoveryServices/Vaults/read | "コンテナーの取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトを取得します。 |
> | Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services コンテナーの使用状況の詳細を返します。 |
> | Microsoft.RecoveryServices/Vaults/write | "コンテナーの作成" 操作では、"コンテナー" 型の Azure リソースを作成します。 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Storage/storageAccounts/listKeys/action | 指定されたストレージ アカウントのアクセス キーを返します。 |
> | Microsoft.Storage/storageAccounts/read | ストレージ アカウントの一覧を返すか、指定されたストレージ アカウントのプロパティを取得します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |

## <a name="virtual-machine-user-login"></a>Virtual Machine User Login
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | ポータルで仮想マシンを表示し、通常のユーザーとしてログインします。 |
> | **Id** | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **アクション** |  |
> | Microsoft.Network/publicIPAddresses/read | パブリック IP アドレス定義を取得します。 |
> | Microsoft.Network/virtualNetworks/read | 仮想ネットワークの定義を取得します。 |
> | Microsoft.Network/loadBalancers/read | ロード バランサー定義を取得します。 |
> | Microsoft.Network/networkInterfaces/read | ネットワーク インターフェイスの定義を取得します。  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | 仮想マシンに通常のユーザーとしてログインします。 |

## <a name="web-plan-contributor"></a>Web Plan Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | Web サイトの Web プランを管理できます。ただし、それらへのアクセスは含まれません。 |
> | **Id** | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | 承認の読み取り |
> | Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |
> | Microsoft.Web/serverFarms/* | サーバー ファームの作成と管理 |

## <a name="website-contributor"></a>Website Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **説明** | Web サイト (Web プランではない) を管理できます。ただし、それらへのアクセスは含まれません。 |
> | **Id** | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **アクション** |  |
> | Microsoft.Authorization/*/read | 承認の読み取り |
> | Microsoft.Insights/alertRules/* | Insights アラート ルールの作成と管理 |
> | Microsoft.Insights/components/* | Insights コンポーネントの作成と管理 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Support/* | サポート チケットの作成と管理 |
> | Microsoft.Web/certificates/* | Web サイト証明書の作成と管理 |
> | Microsoft.Web/listSitesAssignedToHostName/read | ホスト名に割り当てられたサイトの名前を取得します。 |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | App Service プランのプロパティを取得します。 |
> | Microsoft.Web/sites/* | Web サイトの作成と管理 (サイト作成では、関連付けられた App Service プランに対する書き込みアクセス許可も必要です) |

## <a name="next-steps"></a>次の手順

- [カスタム ロール](custom-roles.md)
- [Azure Portal を使用してロール割り当てを管理する](role-assignments-portal.md)
- [Azure Security Center におけるアクセス許可](../security-center/security-center-permissions.md)
