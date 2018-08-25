---
title: Azure Resource Manager のリソース プロバイダー操作 | Microsoft Docs
description: Microsoft Azure Resource Manager の各リソース プロバイダーで使用できる操作の一覧です
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/07/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: a4e062b1bc56eada2fa2c27797151e265271022e
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621159"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Azure Resource Manager のリソース プロバイダー操作

この記事は、Azure Resource Manager の各リソース プロバイダーで使用できる操作の一覧です。 これらの操作を[カスタム ロール](custom-roles.md)で使用することで、Azure のリソースに詳細な[ロールベースのアクセス制御 (RBAC)](overview.md) を提供できます。 操作文字列は、`{Company}.{ProviderName}/{resourceType}/{action}` という形式です。

リソース プロバイダー操作は常に進化しています。 最新の操作を取得するには、[Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) または [az provider operation list](/cli/azure/provider/operation#az-provider-operation-list) を使用してください。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.AAD/domainServices/delete | ドメイン サービスを削除する |
> | Action | Microsoft.AAD/domainServices/read | ドメイン サービスを読み取る |
> | Action | Microsoft.AAD/domainServices/write | ドメイン サービスを書き込む |
> | Action | Microsoft.AAD/locations/operationresults/read |  |
> | Action | Microsoft.AAD/Operations/read |  |
> | Action | Microsoft.AAD/register/action | ドメイン サービスを登録する |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | microsoft.aadiam/diagnosticsettings/delete | 診断設定を削除します。 |
> | Action | microsoft.aadiam/diagnosticsettings/read | 診断設定を読み取ります。 |
> | Action | microsoft.aadiam/diagnosticsettings/write | 診断設定を書き込みます。 |
> | Action | microsoft.aadiam/diagnosticsettingscategories/read | 診断設定のカテゴリを読み取ります。 |
> | Action | microsoft.aadiam/tenants/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Action | microsoft.aadiam/tenants/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Action | microsoft.aadiam/tenants/providers/Microsoft.Insights/logDefinitions/read | テナントの利用可能なログを取得します。 |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.Addons/operations/read | サポートされる RP 操作を取得します。 |
> | Action | Microsoft.Addons/register/action | 指定したサブスクリプションを Microsoft.Addons に登録します。 |
> | Action | Microsoft.Addons/supportProviders/listsupportplaninfo/action | 指定したサブスクリプションの現在のサポート プラン情報の一覧を取得します。 |
> | Action | Microsoft.Addons/supportProviders/supportPlanTypes/delete | 指定された Canonical サポート プランを削除します。 |
> | Action | Microsoft.Addons/supportProviders/supportPlanTypes/read | 指定された Canonical サポート プランの状態を取得します。 |
> | Action | Microsoft.Addons/supportProviders/supportPlanTypes/write | 指定した Canonical サポート プランの種類を追加します。 |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.ADHybridHealthService/addsservices/action | テナントの新しいフォレストを作成します。 |
> | Action | Microsoft.ADHybridHealthService/addsservices/addomainservicemembers/read | 指定されたサービス名のすべてのサーバーを取得します。 |
> | Action | Microsoft.ADHybridHealthService/addsservices/alerts/read | alertid、アラートの発生日、アラートの最終検出日、アラートの説明、最終更新日、アラート レベル、アラートの状態、アラートのトラブルシューティングのリンクなど、フォレストのアラートに関する詳細を取得します。 |
> | Action | Microsoft.ADHybridHealthService/addsservices/configuration/read | フォレストのサービス構成を取得します。 たとえば、フォレスト名、機能レベル、ドメイン名前付けマスター FSMO ロール、スキーマ マスター FSMO ロールなどです。 |
> | Action | Microsoft.ADHybridHealthService/addsservices/delete | サービス、そのサーバー、および正常性データを削除します。 |
> | Action | Microsoft.ADHybridHealthService/addsservices/dimensions/read | フォレストのドメインとサイトの詳細を取得します。 たとえば、正常性の状態、アクティブなアラート、解決済みのアラート、ドメインの機能レベルなどのプロパティ、フォレスト、インフラストラクチャ マスター、PDC、RID マスターなどです。  |
> | Action | Microsoft.ADHybridHealthService/addsservices/features/userpreference/read | フォレストのユーザーの基本設定を取得します。<br>たとえば、ldapsuccessfulbinds、ntlmauthentications、kerberosauthentications、addsinsightsagentprivatebytes、ldapsearches などの MetricCounterName です。<br>UI グラフなどの設定。 |
> | Action | Microsoft.ADHybridHealthService/addsservices/forestsummary/read | フォレスト名、このフォレスト以下にあるドメイン数、サイト数、サイトの詳細など、特定のフォレストの概要を取得します。 |
> | Action | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | 特定のサービスでサポートされているメトリックの一覧を取得します。<br>たとえば、ADFS サービスのエクストラネット アカウント ロックアウト、失敗した要求の総数、未処理のトークン要求 (プロキシ)、トークン要求/秒など。<br>ADDomainService の NTLM 認証/秒、LDAP 成功バインド数/秒、LDAP バインド時間、LDAP アクティブ スレッド数、Kerberos 認証数/秒、ADQ スレッド総数など。<br>実行プロファイルの待機時間、確立された TCP 接続、Insights エージェントのプライベート バイト、統計情報の Azure AD for ADSync サービスへのエクスポート。 |
> | Action | Microsoft.ADHybridHealthService/addsservices/metrics/groups/read | サービスが指定されている場合、この API はメトリック情報を取得します。<br>たとえば、この API を使用して、ADFederation サービスのエクストラネット アカウント ロックアウト、失敗した要求の総数、未処理のトークン要求 (プロキシ)、トークン要求/秒などの情報を取得できます。<br>ADDomainService の NTLM 認証/秒、LDAP 成功バインド数/秒、LDAP バインド時間、LDAP アクティブ スレッド数、Kerberos 認証数/秒、ADQ スレッド総数など。<br>実行プロファイルの待機時間、確立された TCP 接続、Insights エージェントのプライベート バイト、統計情報の Azure AD for Sync サービスへのエクスポート。 |
> | Action | Microsoft.ADHybridHealthService/addsservices/premiumcheck/read | この API は、Premium テナントのすべてのオンボード ADDomainServices の一覧を取得します。 |
> | Action | Microsoft.ADHybridHealthService/addsservices/read | 指定されたサービス名のサービスの詳細を取得します。 |
> | Action | Microsoft.ADHybridHealthService/addsservices/replicationdetails/read | 指定されたサービス名のすべてのサーバーについてレプリケーションの詳細を取得します。 |
> | Action | Microsoft.ADHybridHealthService/addsservices/replicationstatus/read | ドメイン コントローラー数と、レプリケーション エラー数 (ある場合) を取得します。 |
> | Action | Microsoft.ADHybridHealthService/addsservices/replicationsummary/read | 特定のフォレストについて、詳細なドメイン コントローラーの一覧とレプリケーションの詳細を取得します。 |
> | Action | Microsoft.ADHybridHealthService/addsservices/servicemembers/action | サーバー インスタンスを追加します。 |
> | Action | Microsoft.ADHybridHealthService/addsservices/servicemembers/credentials/read | ADDomainService のサーバー登録時にこの API が呼び出され、新しいサーバーをオンボードするための資格情報が取得されます。 |
> | Action | Microsoft.ADHybridHealthService/addsservices/servicemembers/delete | 特定のサービスとテナントのサーバーを削除します。 |
> | Action | Microsoft.ADHybridHealthService/addsservices/write | テナントの ADDomainService インスタンスを作成または更新します。 |
> | Action | Microsoft.ADHybridHealthService/configuration/action | テナント構成を更新します。 |
> | Action | Microsoft.ADHybridHealthService/configuration/read | テナント構成を読み取ります。 |
> | Action | Microsoft.ADHybridHealthService/configuration/write | テナント構成を作成します。 |
> | Action | Microsoft.ADHybridHealthService/logs/contents/read | BLOB に格納されているエージェントのインストールおよび登録ログの内容を取得します。 |
> | Action | Microsoft.ADHybridHealthService/logs/read | テナントのエージェントのインストールおよび登録ログを取得します。 |
> | Action | Microsoft.ADHybridHealthService/operations/read | システムがサポートする操作の一覧を取得します。 |
> | Action | Microsoft.ADHybridHealthService/register/action | ADHybrid Health Service リソース プロバイダーを登録し、ADHybrid Health Service リソースの作成を有効にします。 |
> | Action | Microsoft.ADHybridHealthService/reports/availabledeployments/read | 顧客のインシデントをサポートするために DevOps から使用される、使用可能なリージョンの一覧を取得します。 |
> | Action | Microsoft.ADHybridHealthService/reports/badpassword/read | Active Directory フェデレーション サービス内のすべてのユーザーに関する無効なパスワード試行の一覧を取得します。 |
> | Action | Microsoft.ADHybridHealthService/reports/badpassworduseridipfrequency/read | 指定されたテナントの 1 日の IPAddress あたりのユーザー名/パスワードの失敗した試行の頻度について、新しくエンキューされたレポート ジョブの状態と結果を含む BLOB SAS URI を取得します。 |
> | Action | Microsoft.ADHybridHealthService/reports/consentedtodevopstenants/read | DevOps が承認したテナントの一覧を取得します。 通常、カスタマー サポートに使用されます。 |
> | Action | Microsoft.ADHybridHealthService/reports/isdevops/read | DevOps が承認したテナントかどうかを示す値を取得します。 |
> | Action | Microsoft.ADHybridHealthService/reports/selectdevopstenant/read | 選択されている DevOps テナントの userid (objectid) を更新します。 |
> | Action | Microsoft.ADHybridHealthService/reports/selecteddeployment/read | 指定されたテナントの選択されている展開を取得します。 |
> | Action | Microsoft.ADHybridHealthService/reports/tenantassigneddeployment/read | テナント ID が指定されている場合、テナントの保存場所を取得します。 |
> | Action | Microsoft.ADHybridHealthService/reports/updateselecteddeployment/read | データのアクセス元の地域を取得します。 |
> | Action | Microsoft.ADHybridHealthService/services/action | テナントのサービス インスタンスを更新します。 |
> | Action | Microsoft.ADHybridHealthService/services/alerts/read | サービスのアラートを読み取ります。 |
> | Action | Microsoft.ADHybridHealthService/services/alerts/read | サービスのアラートを読み取ります。 |
> | Action | Microsoft.ADHybridHealthService/services/checkservicefeatureavailibility/read | 機能名が指定されている場合、その機能を使用するために必要なすべての要件をサービスが満たしているかどうかを検証します。 |
> | Action | Microsoft.ADHybridHealthService/services/delete | テナントのサービス インスタンスを削除します。 |
> | Action | Microsoft.ADHybridHealthService/services/exporterrors/read | 指定された同期サービスのエクスポート エラーを取得します。 |
> | Action | Microsoft.ADHybridHealthService/services/exportstatus/read | 指定されたサービスのエクスポート状態を取得します。 |
> | Action | Microsoft.ADHybridHealthService/services/feedbacktype/feedback/read | 指定されたサービスとサーバーに対するアラートのフィードバックを取得します。 |
> | Action | Microsoft.ADHybridHealthService/services/metricmetadata/read | 特定のサービスでサポートされているメトリックの一覧を取得します。<br>たとえば、ADFS サービスのエクストラネット アカウント ロックアウト、失敗した要求の総数、未処理のトークン要求 (プロキシ)、トークン要求/秒など。<br>ADDomainService の NTLM 認証/秒、LDAP 成功バインド数/秒、LDAP バインド時間、LDAP アクティブ スレッド数、Kerberos 認証数/秒、ADQ スレッド総数など。<br>実行プロファイルの待機時間、確立された TCP 接続、Insights エージェントのプライベート バイト、統計情報の Azure AD for ADSync サービスへのエクスポート。 |
> | Action | Microsoft.ADHybridHealthService/services/metrics/groups/average/read | サービスが指定されている場合、この API はそのサービスのメトリックの平均を取得します。<br>たとえば、この API を使用して、ADFederation サービスのエクストラネット アカウント ロックアウト、失敗した要求の総数、未処理のトークン要求 (プロキシ)、トークン要求/秒などの情報を取得できます。<br>ADDomainService の NTLM 認証/秒、LDAP 成功バインド数/秒、LDAP バインド時間、LDAP アクティブ スレッド数、Kerberos 認証数/秒、ADQ スレッド総数など。<br>実行プロファイルの待機時間、確立された TCP 接続、Insights エージェントのプライベート バイト、統計情報の Azure AD for Sync サービスへのエクスポート。 |
> | Action | Microsoft.ADHybridHealthService/services/metrics/groups/read | サービスが指定されている場合、この API はメトリック情報を取得します。<br>たとえば、この API を使用して、ADFederation サービスのエクストラネット アカウント ロックアウト、失敗した要求の総数、未処理のトークン要求 (プロキシ)、トークン要求/秒などの情報を取得できます。<br>ADDomainService の NTLM 認証/秒、LDAP 成功バインド数/秒、LDAP バインド時間、LDAP アクティブ スレッド数、Kerberos 認証数/秒、ADQ スレッド総数など。<br>実行プロファイルの待機時間、確立された TCP 接続、Insights エージェントのプライベート バイト、統計情報の Azure AD for Sync サービスへのエクスポート。 |
> | Action | Microsoft.ADHybridHealthService/services/metrics/groups/sum/read | サービスが指定されている場合、この API はそのサービスの集計ビューを取得します。<br>たとえば、この API を使用して、ADFederation サービスのエクストラネット アカウント ロックアウト、失敗した要求の総数、未処理のトークン要求 (プロキシ)、トークン要求/秒などの情報を取得できます。<br>ADDomainService の NTLM 認証/秒、LDAP 成功バインド数/秒、LDAP バインド時間、LDAP アクティブ スレッド数、Kerberos 認証数/秒、ADQ スレッド総数など。<br>実行プロファイルの待機時間、確立された TCP 接続、Insights エージェントのプライベート バイト、統計情報の Azure AD for Sync サービスへのエクスポート。 |
> | Action | Microsoft.ADHybridHealthService/services/monitoringconfiguration/write | サービスの監視構成を追加または更新します。 |
> | Action | Microsoft.ADHybridHealthService/services/monitoringconfigurations/read | 指定されたサービスの監視構成を取得します。 |
> | Action | Microsoft.ADHybridHealthService/services/monitoringconfigurations/write | サービスの監視構成を追加または更新します。 |
> | Action | Microsoft.ADHybridHealthService/services/premiumcheck/read | この API は、Premium テナントのすべてのオンボード サービスの一覧を取得します。 |
> | Action | Microsoft.ADHybridHealthService/services/read | テナントのサービス インスタンスを読み取ります。 |
> | Action | Microsoft.ADHybridHealthService/services/reports/details/read | 過去 7 日間にパスワードの誤入力エラーが発生した上位 50 人のユーザーのレポートを取得します。 |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/action | サービスにサーバー インスタンスを作成します。 |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/alerts/read | サーバーのアラートを読み取ります。 |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | サーバー登録時にこの API が呼び出され、新しいサーバーをオンボードするための資格情報が取得されます。 |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/datafreshness/read | サーバーが指定されている場合、この API は、サーバーによってアップロードされているデータ型と各アップロードの最終時刻の一覧を取得します。 |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/delete | サービスのサーバー インスタンスを削除します。 |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/exportstatus/read | 指定された同期サービスの同期エクスポート エラーの詳細を取得します。 |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/metrics/groups/read | サービスが指定されている場合、この API はメトリック情報を取得します。<br>たとえば、この API を使用して、ADFederation サービスのエクストラネット アカウント ロックアウト、失敗した要求の総数、未処理のトークン要求 (プロキシ)、トークン要求/秒などの情報を取得できます。<br>ADDomainService の NTLM 認証/秒、LDAP 成功バインド数/秒、LDAP バインド時間、LDAP アクティブ スレッド数、Kerberos 認証数/秒、ADQ スレッド総数など。<br>実行プロファイルの待機時間、確立された TCP 接続、Insights エージェントのプライベート バイト、統計情報の Azure AD for Sync サービスへのエクスポート。 |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/read | サービスのサーバー インスタンスを読み取ります。 |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/serviceconfiguration/read | 指定されたテナントのサービス構成を取得します。 |
> | Action | Microsoft.ADHybridHealthService/services/tenantwhitelisting/read | 指定されたテナントの機能のホワイトリスト登録状態を取得します。 |
> | Action | Microsoft.ADHybridHealthService/services/write | テナントにサービス インスタンスを作成します。 |
> | Action | Microsoft.ADHybridHealthService/unregister/action | ADHybrid Health Service リソース プロバイダーへのサブスクリプションの登録を解除します。 |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.Advisor/configurations/read | 構成の取得 |
> | Action | Microsoft.Advisor/configurations/write | 構成を作成/更新します。 |
> | Action | Microsoft.Advisor/generateRecommendations/action | 推奨事項を生成します。 |
> | Action | Microsoft.Advisor/generateRecommendations/read | 推奨事項の生成の状態を取得します。 |
> | Action | Microsoft.Advisor/operations/read | Microsoft Advisor の操作を取得します。 |
> | Action | Microsoft.Advisor/recommendations/available/action | 新しい推奨事項は Microsoft Advisor で使用できます。 |
> | Action | Microsoft.Advisor/recommendations/read | 推奨事項を読み取ります。 |
> | Action | Microsoft.Advisor/recommendations/suppressions/delete | 抑制を削除します。 |
> | Action | Microsoft.Advisor/recommendations/suppressions/read | 抑制を取得します。 |
> | Action | Microsoft.Advisor/recommendations/suppressions/write | 抑制を作成または更新します。 |
> | Action | Microsoft.Advisor/register/action | Microsoft Advisor にサブスクリプションを登録します。 |
> | Action | Microsoft.Advisor/suppressions/delete | 抑制を削除します。 |
> | Action | Microsoft.Advisor/suppressions/read | 抑制を取得します。 |
> | Action | Microsoft.Advisor/suppressions/write | 抑制を作成または更新します。 |
> | Action | Microsoft.Advisor/unregister/action | Microsoft Advisor のサブスクリプションを登録解除します。 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.AlertsManagement/alerts/changestate/action | アラートの状態を変更します。 |
> | Action | Microsoft.AlertsManagement/alerts/read | 入力フィルターのすべてのアラートを取得します。 |
> | Action | Microsoft.AlertsManagement/alertsSummary/read | アラートの概要を取得します。 |
> | Action | Microsoft.AlertsManagement/Operations/read | 提供された操作を読み取ります。 |
> | Action | Microsoft.AlertsManagement/smartGroups/changestate/action | スマート グループの状態を変更します。 |
> | Action | Microsoft.AlertsManagement/smartGroups/read | 入力フィルターのすべてのスマート グループを取得します。 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.AnalysisServices/locations/checkNameAvailability/action | 指定された分析サーバー名が有効であり、使用されていないことを確認します。 |
> | Action | Microsoft.AnalysisServices/locations/operationresults/read | 指定された操作の結果の情報を取得します。 |
> | Action | Microsoft.AnalysisServices/locations/operationstatuses/read | 指定された操作の状態の情報を取得します。 |
> | Action | Microsoft.AnalysisServices/operations/read | 操作の情報を取得します。 |
> | Action | Microsoft.AnalysisServices/register/action | Analysis Services リソース プロバイダーを登録します。 |
> | Action | Microsoft.AnalysisServices/servers/delete | 分析サーバーを削除します。 |
> | Action | Microsoft.AnalysisServices/servers/listGatewayStatus/action | サーバーに関連付けられているゲートウェイの状態を一覧表示します。 |
> | Action | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/diagnosticSettings/read | 分析サーバーの診断の設定を取得します。 |
> | Action | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/diagnosticSettings/write | 分析サーバーの診断の設定を作成または更新します。 |
> | Action | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/logDefinitions/read | サーバーの利用可能なログを取得します。 |
> | Action | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/metricDefinitions/read | 分析サーバーの利用可能なメトリックを取得します。 |
> | Action | Microsoft.AnalysisServices/servers/read | 指定された分析サーバーの情報を取得します。 |
> | Action | Microsoft.AnalysisServices/servers/resume/action | 分析サーバーを再開します。 |
> | Action | Microsoft.AnalysisServices/servers/skus/read | サーバーの利用可能な SKU 情報を取得します。 |
> | Action | Microsoft.AnalysisServices/servers/suspend/action | 分析サーバーを中断します。 |
> | Action | Microsoft.AnalysisServices/servers/write | 指定された分析サーバーを作成または更新します。 |
> | Action | Microsoft.AnalysisServices/skus/read | SKU の情報を取得します。 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.ApiManagement/checkNameAvailability/read | 指定されたサービス名を使用できるかどうかを確認します。 |
> | Action | Microsoft.ApiManagement/operations/read | Microsoft.ApiManagement リソースで使用可能なすべての API 操作を読み取ります。 |
> | Action | Microsoft.ApiManagement/register/action | Microsoft.ApiManagement リソース プロバイダーにサブスクリプションを登録します。 |
> | Action | Microsoft.ApiManagement/reports/read | 期間、地理的地域、開発者、製品、API、操作、サブスクリプション、要求ごとに集計されたレポートを取得します。 |
> | Action | Microsoft.ApiManagement/service/apis/delete | 既存の API を削除します。 |
> | Action | Microsoft.ApiManagement/service/apis/diagnostics/delete | 既存の診断を削除します。 |
> | Action | Microsoft.ApiManagement/service/apis/diagnostics/read | 診断の一覧または診断の詳細を取得します。 |
> | Action | Microsoft.ApiManagement/service/apis/diagnostics/write | 新しい診断を追加するか、既存の診断の詳細を更新します。 |
> | Action | Microsoft.ApiManagement/service/apis/issues/attachments/delete | 既存の添付ファイルを削除します。 |
> | Action | Microsoft.ApiManagement/service/apis/issues/attachments/read | 問題の添付ファイルを取得するか、API Management の添付ファイルの詳細を取得します。 |
> | Action | Microsoft.ApiManagement/service/apis/issues/attachments/write | API の問題の添付ファイルを追加します。 |
> | Action | Microsoft.ApiManagement/service/apis/issues/comments/delete | 既存のコメントを削除します。 |
> | Action | Microsoft.ApiManagement/service/apis/issues/comments/read | 問題コメントを取得するか、API Management の問題コメントの詳細を取得します。 |
> | Action | Microsoft.ApiManagement/service/apis/issues/comments/write | API の問題コメントを追加します。 |
> | Action | Microsoft.ApiManagement/service/apis/issues/delete | 既存の問題を削除します。 |
> | Action | Microsoft.ApiManagement/service/apis/issues/read | API に関連する問題を取得するか、API Management の問題の詳細を取得します。 |
> | Action | Microsoft.ApiManagement/service/apis/issues/write | API の問題を追加または更新します。 |
> | Action | Microsoft.ApiManagement/service/apis/operations/delete | 既存の API 操作を削除します。 |
> | Action | Microsoft.ApiManagement/service/apis/operations/policies/delete | API 操作ポリシーからポリシー構成を削除します。 |
> | Action | Microsoft.ApiManagement/service/apis/operations/policies/read | API 操作のポリシーを取得するか、API 操作のポリシー構成の詳細を取得します。 |
> | Action | Microsoft.ApiManagement/service/apis/operations/policies/write | API 操作のポリシー構成の詳細を設定します。 |
> | Action | Microsoft.ApiManagement/service/apis/operations/policy/delete | 操作からポリシーの構成を削除します。 |
> | Action | Microsoft.ApiManagement/service/apis/operations/policy/read | 操作のポリシーの構成の詳細を取得します。 |
> | Action | Microsoft.ApiManagement/service/apis/operations/policy/write | 操作のポリシーの構成の詳細を設定します。 |
> | Action | Microsoft.ApiManagement/service/apis/operations/read | 既存の API 操作の一覧を取得するか、API 操作の詳細を取得します。 |
> | Action | Microsoft.ApiManagement/service/apis/operations/tags/delete | 既存のタグと既存の操作の関連付けを削除します。 |
> | Action | Microsoft.ApiManagement/service/apis/operations/tags/read | 操作に関連付けられているタグを取得するか、タグの詳細を取得します。 |
> | Action | Microsoft.ApiManagement/service/apis/operations/tags/write | 既存のタグと既存の操作を関連付けます。 |
> | Action | Microsoft.ApiManagement/service/apis/operations/write | 新しい API 操作を作成するか、既存の API 操作を更新します。 |
> | Action | Microsoft.ApiManagement/service/apis/operationsByTags/read | 操作/タグの関連付けの一覧を取得します。 |
> | Action | Microsoft.ApiManagement/service/apis/policies/delete | API ポリシーからポリシーの構成を削除します。 |
> | Action | Microsoft.ApiManagement/service/apis/policies/read | API のポリシーを取得するか、API のポリシー構成の詳細を取得します。 |
> | Action | Microsoft.ApiManagement/service/apis/policies/write | API のポリシーの構成の詳細を設定します。 |
> | Action | Microsoft.ApiManagement/service/apis/policy/delete | API からポリシーの構成を削除します。 |
> | Action | Microsoft.ApiManagement/service/apis/policy/read | API のポリシーの構成の詳細を取得します。 |
> | Action | Microsoft.ApiManagement/service/apis/policy/write | API のポリシーの構成の詳細を設定します。 |
> | Action | Microsoft.ApiManagement/service/apis/products/read | API が含まれるすべての製品を取得します。 |
> | Action | Microsoft.ApiManagement/service/apis/read | すべての登録済み API の一覧を取得するか、API の詳細を取得します。 |
> | Action | Microsoft.ApiManagement/service/apis/releases/delete | API のすべてのリリースを削除するか、API の 1 つのリリースを削除します。 |
> | Action | Microsoft.ApiManagement/service/apis/releases/read | API のリリースを取得するか、API のリリースの詳細を取得します。 |
> | Action | Microsoft.ApiManagement/service/apis/releases/write | 新しい API のリリースを作成するか、既存の API のリリースを更新します。 |
> | Action | Microsoft.ApiManagement/service/apis/revisions/delete | API のすべてのリビジョンを削除します。 |
> | Action | Microsoft.ApiManagement/service/apis/revisions/read | API に属するリビジョンを取得します。 |
> | Action | Microsoft.ApiManagement/service/apis/schemas/delete | 既存のスキーマを削除します。 |
> | Action | Microsoft.ApiManagement/service/apis/schemas/document/read | スキーマを記述しているドキュメントを取得します。 |
> | Action | Microsoft.ApiManagement/service/apis/schemas/document/write | スキーマを記述しているドキュメントを更新します。 |
> | Action | Microsoft.ApiManagement/service/apis/schemas/read | 指定された API のすべてのスキーマを取得するか、API によって使用されているスキーマを取得します。 |
> | Action | Microsoft.ApiManagement/service/apis/schemas/write | API によって使用されるスキーマを設定します。 |
> | Action | Microsoft.ApiManagement/service/apis/tagDescriptions/delete | API からタグの記述を削除します。 |
> | Action | Microsoft.ApiManagement/service/apis/tagDescriptions/read | API のスコープ内の複数または 1 つのタグの記述を取得します。 |
> | Action | Microsoft.ApiManagement/service/apis/tagDescriptions/write | API のスコープ内のタグの記述を作成または変更します。 |
> | Action | Microsoft.ApiManagement/service/apis/tags/delete | 既存の API/タグの関連付けを削除します。 |
> | Action | Microsoft.ApiManagement/service/apis/tags/read | API のすべての API/タグの関連付けを取得するか、API/タグの関連付けの詳細を取得します。 |
> | Action | Microsoft.ApiManagement/service/apis/tags/write | 新しい API/タグの関連付けを追加します。 |
> | Action | Microsoft.ApiManagement/service/apis/write | 新しい API を作成するか、既存の API の詳細を更新します。 |
> | Action | Microsoft.ApiManagement/service/apisByTags/read | API/タグの関連付けの一覧を取得します。 |
> | Action | Microsoft.ApiManagement/service/api-version-sets/delete | 既存の VersionSet を削除します。 |
> | Action | Microsoft.ApiManagement/service/api-version-sets/read | バージョン グループ エンティティの一覧を取得するか、VersionSet の詳細を取得します。 |
> | Action | Microsoft.ApiManagement/service/api-version-sets/versions/read | バージョン エンティティの一覧を取得します。 |
> | Action | Microsoft.ApiManagement/service/api-version-sets/write | 新しい VersionSet を作成するか、既存の VersionSet の詳細を更新します。 |
> | Action | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | 更新済みのネットワーク設定を選択するために、仮想ネットワークで実行されている Microsoft.ApiManagement リソースを更新します。 |
> | Action | Microsoft.ApiManagement/service/authorizationServers/delete | 既存の承認サーバーを削除します。 |
> | Action | Microsoft.ApiManagement/service/authorizationServers/read | 承認サーバーの一覧を取得するか、承認サーバーの詳細を取得します。 |
> | Action | Microsoft.ApiManagement/service/authorizationServers/write | 新しい承認サーバーを作成するか、既存の承認サーバーの詳細を更新します。 |
> | Action | Microsoft.ApiManagement/service/backends/delete | 既存のバックエンドを削除します。 |
> | Action | Microsoft.ApiManagement/service/backends/read | バックエンドの一覧を取得するか、バックエンドの詳細を取得します。 |
> | Action | Microsoft.ApiManagement/service/backends/reconnect/action | 再接続要求を作成します。 |
> | Action | Microsoft.ApiManagement/service/backends/write | 新しいバックエンドを追加するか、既存のバックエンドの詳細を更新します。 |
> | Action | Microsoft.ApiManagement/service/backup/action | ユーザーが指定したストレージ アカウント内の指定されたコンテナーに API Management サービスをバックアップします。 |
> | Action | Microsoft.ApiManagement/service/certificates/delete | 既存の証明書を削除します。 |
> | Action | Microsoft.ApiManagement/service/certificates/read | 証明書の一覧を取得するか、証明書の詳細を取得します。 |
> | Action | Microsoft.ApiManagement/service/certificates/write | 新しい証明書を追加します。 |
> | Action | Microsoft.ApiManagement/service/delete | API Management サービス インスタンスを削除します。 |
> | Action | Microsoft.ApiManagement/service/diagnostics/delete | 既存の診断を削除します。 |
> | Action | Microsoft.ApiManagement/service/diagnostics/read | 診断の一覧または診断の詳細を取得します。 |
> | Action | Microsoft.ApiManagement/service/diagnostics/write | 新しい診断を追加するか、既存の診断の詳細を更新します。 |
> | Action | Microsoft.ApiManagement/service/getssotoken/action | API Management サービスの従来のポータルに管理者としてログインする際に使用できる SSO トークンを取得します。 |
> | Action | Microsoft.ApiManagement/service/groups/delete | 既存のグループを削除します。 |
> | Action | Microsoft.ApiManagement/service/groups/read | グループの一覧を取得するか、グループの詳細を取得します。 |
> | Action | Microsoft.ApiManagement/service/groups/users/delete | 既存のグループから既存のユーザーを削除します。 |
> | Action | Microsoft.ApiManagement/service/groups/users/read | グループのユーザーの一覧を取得します。 |
> | Action | Microsoft.ApiManagement/service/groups/users/write | 既存のユーザーを既存のグループに追加します。 |
> | Action | Microsoft.ApiManagement/service/groups/write | 新しいグループを作成するか、既存のグループの詳細を更新します。 |
> | Action | Microsoft.ApiManagement/service/identityProviders/delete | 既存の ID プロバイダーを削除します。 |
> | Action | Microsoft.ApiManagement/service/identityProviders/read | ID プロバイダーの一覧を取得するか、ID プロバイダーの詳細を取得します。 |
> | Action | Microsoft.ApiManagement/service/identityProviders/write | 新しい ID プロバイダーを作成するか、既存の ID プロバイダーの詳細を更新します。 |
> | Action | Microsoft.ApiManagement/service/locations/networkstatus/read | その場所でサービスが依存しているリソースのネットワーク アクセスの状態を取得します。 |
> | Action | Microsoft.ApiManagement/service/loggers/delete | 既存のロガーを削除します。 |
> | Action | Microsoft.ApiManagement/service/loggers/read | ロガーの一覧を取得するか、ロガーの詳細を取得します。 |
> | Action | Microsoft.ApiManagement/service/loggers/write | 新しいロガーを追加するか、既存のロガーの詳細を更新します。 |
> | Action | Microsoft.ApiManagement/service/managedeployments/action | SKU/ユニット数を変更し、API Management サービスのリージョン デプロイを追加または削除します。 |
> | Action | Microsoft.ApiManagement/service/networkstatus/read | サービスが依存しているリソースのネットワーク アクセスの状態を取得します。 |
> | Action | Microsoft.ApiManagement/service/notifications/action | 指定されたユーザーに通知を送信します。 |
> | Action | Microsoft.ApiManagement/service/notifications/read | すべての API Management 発行者通知を取得するか、API Management 発行者通知の詳細を取得します。 |
> | Action | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | 通知に関連付けられている既存のメールを削除します。 |
> | Action | Microsoft.ApiManagement/service/notifications/recipientEmails/read | API Management 発行者通知に関連付けられているメール受信者を取得します。 |
> | Action | Microsoft.ApiManagement/service/notifications/recipientEmails/write | 通知の新しいメール受信者を作成します。 |
> | Action | Microsoft.ApiManagement/service/notifications/recipientUsers/delete | 通知受信者に関連付けられているユーザーを削除します。 |
> | Action | Microsoft.ApiManagement/service/notifications/recipientUsers/read | 通知に関連付けられている受信者ユーザーを取得します。 |
> | Action | Microsoft.ApiManagement/service/notifications/recipientUsers/write | 通知の受信者にユーザーを追加します。 |
> | Action | Microsoft.ApiManagement/service/notifications/write | API Management 発行者通知を作成または更新します。 |
> | Action | Microsoft.ApiManagement/service/openidConnectProviders/delete | 既存の OpenID Connect プロバイダーを削除します。 |
> | Action | Microsoft.ApiManagement/service/openidConnectProviders/read | OpenID Connect プロバイダーの一覧を取得するか、OpenID Connect プロバイダーの詳細を取得します。 |
> | Action | Microsoft.ApiManagement/service/openidConnectProviders/write | 新しい OpenID Connect プロバイダーを作成するか、既存の OpenID Connect プロバイダーの詳細を更新します。 |
> | Action | Microsoft.ApiManagement/service/operationresults/read | 実行時間の長い操作の現在の状態を取得します。 |
> | Action | Microsoft.ApiManagement/service/policies/delete | テナント ポリシーからポリシーの構成を削除します。 |
> | Action | Microsoft.ApiManagement/service/policies/read | テナントのポリシーを取得するか、テナントのポリシー構成の詳細を取得します。 |
> | Action | Microsoft.ApiManagement/service/policies/write | テナントのポリシー構成の詳細を設定します。 |
> | Action | Microsoft.ApiManagement/service/policySnippets/read | すべてのポリシーのスニペットを取得します。 |
> | Action | Microsoft.ApiManagement/service/portalsettings/read | ポータルのサインアップ、サインイン、または委任の設定を取得します。 |
> | Action | Microsoft.ApiManagement/service/portalsettings/write | サインアップ、サインイン、または委任の設定を更新します。 |
> | Action | Microsoft.ApiManagement/service/products/apis/delete | 既存の製品から既存の API を削除します。 |
> | Action | Microsoft.ApiManagement/service/products/apis/read | 既存の製品に追加された API の一覧を取得します。 |
> | Action | Microsoft.ApiManagement/service/products/apis/write | 既存の API を既存の製品に追加します。 |
> | Action | Microsoft.ApiManagement/service/products/delete | 既存の製品を削除します。 |
> | Action | Microsoft.ApiManagement/service/products/groups/delete | 既存の製品への既存の開発者グループの関連付けを削除します。 |
> | Action | Microsoft.ApiManagement/service/products/groups/read | 製品に関連付けられている開発者グループの一覧を取得します。 |
> | Action | Microsoft.ApiManagement/service/products/groups/write | 既存の開発者グループを既存の製品に関連付けます。 |
> | Action | Microsoft.ApiManagement/service/products/policies/delete | 製品ポリシーからポリシーの構成を削除します。 |
> | Action | Microsoft.ApiManagement/service/products/policies/read | 製品のポリシーまたはポリシー構成の詳細を取得します。 |
> | Action | Microsoft.ApiManagement/service/products/policies/write | 製品のポリシー構成の詳細を設定します。 |
> | Action | Microsoft.ApiManagement/service/products/policy/delete | 既存の製品からポリシーの構成を削除します。 |
> | Action | Microsoft.ApiManagement/service/products/policy/read | 既存の製品のポリシーの構成を取得します。 |
> | Action | Microsoft.ApiManagement/service/products/policy/write | 既存の製品のポリシーの構成を設定します。 |
> | Action | Microsoft.ApiManagement/service/products/read | 製品の一覧を取得するか、製品の詳細を取得します。 |
> | Action | Microsoft.ApiManagement/service/products/subscriptions/read | 製品サブスクリプションの一覧を取得します。 |
> | Action | Microsoft.ApiManagement/service/products/tags/delete | 既存のタグと既存の製品の関連付けを削除します。 |
> | Action | Microsoft.ApiManagement/service/products/tags/read | 製品に関連付けられているタグまたはタグの詳細を取得します。 |
> | Action | Microsoft.ApiManagement/service/products/tags/write | 既存のタグと既存の製品を関連付けます。 |
> | Action | Microsoft.ApiManagement/service/products/write | 新しい製品を作成するか、既存の製品の詳細を更新します。 |
> | Action | Microsoft.ApiManagement/service/productsByTags/read | 製品/タグの関連付けの一覧を取得します。 |
> | Action | Microsoft.ApiManagement/service/properties/delete | 既存のプロパティを削除します。 |
> | Action | Microsoft.ApiManagement/service/properties/read | すべてのプロパティの一覧を取得するか、指定されたプロパティの詳細を取得します。 |
> | Action | Microsoft.ApiManagement/service/properties/write | 新しいプロパティを作成するか、指定されたプロパティの値を更新します。 |
> | Action | Microsoft.ApiManagement/service/providers/Microsoft.Insights/diagnosticSettings/read | API Management サービスの診断設定を取得します。 |
> | Action | Microsoft.ApiManagement/service/providers/Microsoft.Insights/diagnosticSettings/write | API Management サービスの診断設定を作成または更新します。 |
> | Action | Microsoft.ApiManagement/service/providers/Microsoft.Insights/logDefinitions/read | API Management サービスで使用可能なログを取得します。 |
> | Action | Microsoft.ApiManagement/service/providers/Microsoft.Insights/metricDefinitions/read | API Management サービスで使用可能なメトリックを取得します。 |
> | Action | Microsoft.ApiManagement/service/quotas/periods/read | 期間のクォータ カウンターの値を取得します。 |
> | Action | Microsoft.ApiManagement/service/quotas/periods/write | クォータ カウンターの現在の値を設定します。 |
> | Action | Microsoft.ApiManagement/service/quotas/read | クォータの値を取得します。 |
> | Action | Microsoft.ApiManagement/service/quotas/write | クォータ カウンターの現在の値を設定します。 |
> | Action | Microsoft.ApiManagement/service/read | API Management サービス インスタンスのメタデータの読み取り |
> | Action | Microsoft.ApiManagement/service/reports/read | 期間、地理的地域、または開発者ごとに集計されたレポートを取得します。<br>または、製品ごとに集計されたレポートを取得します。<br>または、API、操作、サブスクリプションごとに集計されたレポートを取得します。<br>または、要求のレポート データを取得します。 |
> | Action | Microsoft.ApiManagement/service/restore/action | ユーザーが指定したストレージ アカウント内の指定されたコンテナーからの API Management サービスの復元 |
> | Action | Microsoft.ApiManagement/service/subscriptions/delete | サブスクリプションを削除します。 この操作を使用してサブスクリプションを削除できます。 |
> | Action | Microsoft.ApiManagement/service/subscriptions/read | 製品サブスクリプションの一覧を取得するか、製品サブスクリプションの詳細を取得します。 |
> | Action | Microsoft.ApiManagement/service/subscriptions/regeneratePrimaryKey/action | サブスクリプションのプライマリ キーを再生成します。 |
> | Action | Microsoft.ApiManagement/service/subscriptions/regenerateSecondaryKey/action | サブスクリプションのセカンダリ キーを再生成します。 |
> | Action | Microsoft.ApiManagement/service/subscriptions/write | 既存のユーザーを既存の製品にサブスクライブするか、既存のサブスクリプションの詳細を更新します。 この操作を使用してサブスクリプションを更新できます。 |
> | Action | Microsoft.ApiManagement/service/tagResources/read | タグおよび関連付けられているリソースの一覧を取得します。 |
> | Action | Microsoft.ApiManagement/service/tags/delete | 既存のタグを削除します。 |
> | Action | Microsoft.ApiManagement/service/tags/read | タグの一覧またはタグの詳細を取得します。 |
> | Action | Microsoft.ApiManagement/service/tags/write | 新しいタグを追加するか、既存のタグの詳細を更新します。 |
> | Action | Microsoft.ApiManagement/service/templates/delete | 既定の API Management メール テンプレートをリセットします。 |
> | Action | Microsoft.ApiManagement/service/templates/read | すべてのメール テンプレートまたは API Management メール テンプレートの詳細を取得します。 |
> | Action | Microsoft.ApiManagement/service/templates/write | API Management メール テンプレートを作成または更新します。 |
> | Action | Microsoft.ApiManagement/service/tenant/delete | テナントのポリシーの構成を削除します。 |
> | Action | Microsoft.ApiManagement/service/tenant/deploy/action | 指定された Git ブランチの変更をデータベース内の構成に適用するデプロイ タスクを実行します。 |
> | Action | Microsoft.ApiManagement/service/tenant/operationResults/read | 操作の結果の一覧を取得するか、特定の操作の結果を取得します。 |
> | Action | Microsoft.ApiManagement/service/tenant/read | テナントのポリシー構成またはテナントのアクセス情報の詳細を取得します。 |
> | Action | Microsoft.ApiManagement/service/tenant/regeneratePrimaryKey/action | プライマリ アクセス キーを再生成します。 |
> | Action | Microsoft.ApiManagement/service/tenant/regenerateSecondaryKey/action | セカンダリ アクセス キーを再生成します。 |
> | Action | Microsoft.ApiManagement/service/tenant/save/action | レポジトリ内の指定されたブランチの構成のスナップショットを使用してコミットを作成します。 |
> | Action | Microsoft.ApiManagement/service/tenant/syncState/read | 前回の Git 同期の状態を取得します。 |
> | Action | Microsoft.ApiManagement/service/tenant/validate/action | 指定された Git ブランチの変更を検証します。 |
> | Action | Microsoft.ApiManagement/service/tenant/write | テナントのポリシー構成を設定するか、テナントのアクセス情報の詳細を更新します。 |
> | Action | Microsoft.ApiManagement/service/updatecertificate/action | API Management サービスの SSL 証明書をアップロードします。 |
> | Action | Microsoft.ApiManagement/service/updatehostname/action | API Management サービスのカスタム ドメイン名を設定、更新、または削除します。 |
> | Action | Microsoft.ApiManagement/service/users/action | 新しいユーザーを登録します。 |
> | Action | Microsoft.ApiManagement/service/users/applications/attachments/delete | 添付ファイルを削除します。 |
> | Action | Microsoft.ApiManagement/service/users/applications/attachments/read | アプリケーションの 1 つまたは複数の添付ファイルを取得します。 |
> | Action | Microsoft.ApiManagement/service/users/applications/attachments/write | アプリケーションに添付ファイルを追加します。 |
> | Action | Microsoft.ApiManagement/service/users/applications/delete | 既存のアプリケーションを削除します。 |
> | Action | Microsoft.ApiManagement/service/users/applications/read | すべてのユーザー アプリケーションの一覧または API Management アプリケーションの詳細を取得します。 |
> | Action | Microsoft.ApiManagement/service/users/applications/write | アプリケーションを API Management に登録するか、アプリケーションの詳細を更新します。 |
> | Action | Microsoft.ApiManagement/service/users/delete | ユーザー アカウントを削除します。 |
> | Action | Microsoft.ApiManagement/service/users/generateSsoUrl/action | SSO URL を生成します。 この URL を使用して、管理ポータルにアクセスできます。 |
> | Action | Microsoft.ApiManagement/service/users/groups/read | ユーザー グループの一覧を取得します。 |
> | Action | Microsoft.ApiManagement/service/users/keys/read | ユーザー キーの一覧を取得します。 |
> | Action | Microsoft.ApiManagement/service/users/read | 登録済みユーザーの一覧を取得するか、ユーザーのアカウントの詳細を取得します。 |
> | Action | Microsoft.ApiManagement/service/users/subscriptions/read | ユーザー サブスクリプションの一覧を取得します。 |
> | Action | Microsoft.ApiManagement/service/users/token/action | ユーザーのトークン アクセス トークンを取得します。 |
> | Action | Microsoft.ApiManagement/service/users/write | 新しいユーザーを登録するか、既存のユーザーのアカウントの詳細を更新します。 |
> | Action | Microsoft.ApiManagement/service/write | API Management サービスの新しいインスタンスの作成 |
> | Action | Microsoft.ApiManagement/unregister/action | Microsoft.ApiManagement リソース プロバイダーへのサブスクリプションの登録を解除します。 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.Authorization/checkAccess/action | 呼び出し元が特定のアクションの実行を承認されているかどうかを確認します。 |
> | Action | Microsoft.Authorization/classicAdministrators/delete | サブスクリプションから管理者を削除します。 |
> | Action | Microsoft.Authorization/classicAdministrators/read | サブスクリプションの管理者を読み取ります。 |
> | Action | Microsoft.Authorization/classicAdministrators/write | サブスクリプションの管理者を追加または変更しします。 |
> | Action | Microsoft.Authorization/elevateAccess/action | テナント スコープで、ユーザー アクセス管理者のアクセス権を呼び出し元に付与します。 |
> | Action | Microsoft.Authorization/locks/delete | 指定されたスコープのロックを削除します。 |
> | Action | Microsoft.Authorization/locks/read | 指定されたスコープのロックを取得します。 |
> | Action | Microsoft.Authorization/locks/write | 指定されたスコープのロックを追加します。 |
> | Action | Microsoft.Authorization/operations/read | 操作の一覧を取得します。 |
> | Action | Microsoft.Authorization/permissions/read | 指定されたスコープで呼び出し元が持つすべてのアクセス許可を一覧表示します。 |
> | Action | Microsoft.Authorization/policyAssignments/delete | 指定されたスコープのポリシー割り当てを削除します。 |
> | Action | Microsoft.Authorization/policyAssignments/read | ポリシー割り当てに関する情報を取得します。 |
> | Action | Microsoft.Authorization/policyAssignments/write | 指定されたスコープのポリシー割り当てを作成します。 |
> | Action | Microsoft.Authorization/policyDefinitions/delete | ポリシー定義を削除します。 |
> | Action | Microsoft.Authorization/policyDefinitions/read | ポリシー定義に関する情報を取得します。 |
> | Action | Microsoft.Authorization/policyDefinitions/write | カスタムのポリシー定義を作成します。 |
> | Action | Microsoft.Authorization/policySetDefinitions/delete | ポリシー セットの定義を削除します。 |
> | Action | Microsoft.Authorization/policySetDefinitions/read | ポリシー セットの定義に関する情報を取得します。 |
> | Action | Microsoft.Authorization/policySetDefinitions/write | カスタム ポリシー セットの定義を作成します。 |
> | Action | Microsoft.Authorization/providerOperations/read | ロール定義で使用できる、すべてのリソース プロバイダーのための操作を取得します。 |
> | Action | Microsoft.Authorization/roleAssignments/delete | 指定したスコープにおけるロールの割り当てを削除します。 |
> | Action | Microsoft.Authorization/roleAssignments/read | ロールの割り当てに関する情報を取得します。 |
> | Action | Microsoft.Authorization/roleAssignments/write | 指定されたスコープのロールの割り当てを作成します。 |
> | Action | Microsoft.Authorization/roleDefinitions/delete | 指定されたカスタムのロール定義を削除します。 |
> | Action | Microsoft.Authorization/roleDefinitions/read | ロール定義に関する情報を取得します。 |
> | Action | Microsoft.Authorization/roleDefinitions/write | 指定されたアクセス許可と割り当て可能なスコープで、カスタムのロール定義を作成または更新します。 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.Automation/automationAccounts/agentRegistrationInformation/read | Azure Automation DSC の登録情報を読み取ります。 |
> | Action | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | Azure Automation DSC のキーを再生成する要求を書き込みます。 |
> | Action | Microsoft.Automation/automationAccounts/certificates/delete | Azure Automation 証明書資産を削除します。 |
> | Action | Microsoft.Automation/automationAccounts/certificates/getCount/action | 証明書数を読み取ります |
> | Action | Microsoft.Automation/automationAccounts/certificates/read | Azure Automation 証明書資産を取得します。 |
> | Action | Microsoft.Automation/automationAccounts/certificates/write | Azure Automation 証明書資産を作成または更新します。 |
> | Action | Microsoft.Automation/automationAccounts/compilationjobs/read | Azure Automation DSC のコンパイルを読み取ります。 |
> | Action | Microsoft.Automation/automationAccounts/compilationjobs/read | Azure Automation DSC のコンパイルを読み取ります。 |
> | Action | Microsoft.Automation/automationAccounts/compilationjobs/write | Azure Automation DSC のコンパイルを書き込みます。 |
> | Action | Microsoft.Automation/automationAccounts/compilationjobs/write | Azure Automation DSC のコンパイルを書き込みます。 |
> | Action | Microsoft.Automation/automationAccounts/configurations/content/read | 構成メディアの内容を読み取ります |
> | Action | Microsoft.Automation/automationAccounts/configurations/delete | Azure Automation DSC のコンテンツを削除します。 |
> | Action | Microsoft.Automation/automationAccounts/configurations/getCount/action | Azure Automation DSC のコンテンツの数を読み取ります。 |
> | Action | Microsoft.Automation/automationAccounts/configurations/read | Azure Automation DSC のコンテンツを取得します。 |
> | Action | Microsoft.Automation/automationAccounts/configurations/write | Azure Automation DSC のコンテンツを書き込みます。 |
> | Action | Microsoft.Automation/automationAccounts/connections/delete | Azure Automation 接続資産を削除します。 |
> | Action | Microsoft.Automation/automationAccounts/connections/getCount/action | 接続数を読み取ります |
> | Action | Microsoft.Automation/automationAccounts/connections/read | Azure Automation 接続資産を取得します。 |
> | Action | Microsoft.Automation/automationAccounts/connections/write | Azure Automation 接続資産を作成または更新します。 |
> | Action | Microsoft.Automation/automationAccounts/connectionTypes/delete | Azure Automation 接続の種類の資産を削除します。 |
> | Action | Microsoft.Automation/automationAccounts/connectionTypes/read | Azure Automation 接続の種類の資産を取得します。 |
> | Action | Microsoft.Automation/automationAccounts/connectionTypes/write | Azure Automation 接続の種類の資産を作成します。 |
> | Action | Microsoft.Automation/automationAccounts/credentials/delete | Azure Automation 資格情報資産を削除します。 |
> | Action | Microsoft.Automation/automationAccounts/credentials/getCount/action | 資格情報数を読み取ります |
> | Action | Microsoft.Automation/automationAccounts/credentials/read | Azure Automation 資格情報資産を取得します。 |
> | Action | Microsoft.Automation/automationAccounts/credentials/write | Azure Automation 資格情報資産を作成または更新します。 |
> | Action | Microsoft.Automation/automationAccounts/delete | Azure Automation アカウントを削除します。 |
> | Action | Microsoft.Automation/automationAccounts/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Action | Microsoft.Automation/automationAccounts/diagnosticSettings/write | リソースの診断設定を設定します。 |
> | Action | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | Hybrid Runbook Worker リソースを削除します。 |
> | Action | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Hybrid Runbook Worker リソースを読み取ります。 |
> | Action | Microsoft.Automation/automationAccounts/jobs/output/read | ジョブの出力を取得します。 |
> | Action | Microsoft.Automation/automationAccounts/jobs/read | Azure Automation ジョブを取得します。 |
> | Action | Microsoft.Automation/automationAccounts/jobs/resume/action | Azure Automation ジョブを再開します。 |
> | Action | Microsoft.Automation/automationAccounts/jobs/runbookContent/action | ジョブ実行時に Azure Automation Runbook のコンテンツを取得します。 |
> | Action | Microsoft.Automation/automationAccounts/jobs/stop/action | Azure Automation ジョブを停止します。 |
> | Action | Microsoft.Automation/automationAccounts/jobs/streams/read | Azure Automation ジョブ ストリームを取得します。 |
> | Action | Microsoft.Automation/automationAccounts/jobs/streams/read | Azure Automation ジョブ ストリームを取得します。 |
> | Action | Microsoft.Automation/automationAccounts/jobs/suspend/action | Azure Automation ジョブを中断します。 |
> | Action | Microsoft.Automation/automationAccounts/jobs/write | Azure Automation ジョブを作成します。 |
> | Action | Microsoft.Automation/automationAccounts/jobSchedules/delete | Azure Automation ジョブ スケジュールを削除します。 |
> | Action | Microsoft.Automation/automationAccounts/jobSchedules/read | Azure Automation ジョブ スケジュールを取得します。 |
> | Action | Microsoft.Automation/automationAccounts/jobSchedules/write | Azure Automation ジョブ スケジュールを作成します。 |
> | Action | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Automation アカウントにリンクされているワークスペースを取得します |
> | Action | Microsoft.Automation/automationAccounts/listKeys/action | Automation アカウントのキーを読み取ります |
> | Action | Microsoft.Automation/automationAccounts/logDefinitions/read | Automation アカウントで使用可能なログを取得します。 |
> | Action | Microsoft.Automation/automationAccounts/modules/activities/read | Azure Automation のアクティビティを取得します。 |
> | Action | Microsoft.Automation/automationAccounts/modules/delete | Azure Automation モジュールを削除します。 |
> | Action | Microsoft.Automation/automationAccounts/modules/getCount/action | Automation アカウント内のモジュール数を取得します。 |
> | Action | Microsoft.Automation/automationAccounts/modules/read | Azure Automation モジュールを取得します。 |
> | Action | Microsoft.Automation/automationAccounts/modules/write | Azure Automation モジュールを作成または更新します。 |
> | Action | Microsoft.Automation/automationAccounts/nodeConfigurations/delete | Azure Automation DSC のノード構成を削除します。 |
> | Action | Microsoft.Automation/automationAccounts/nodeConfigurations/rawContent/action | Azure Automation DSC のノード構成の内容を読み取ります。 |
> | Action | Microsoft.Automation/automationAccounts/nodeConfigurations/read | Azure Automation DSC のノード構成を読み取ります。 |
> | Action | Microsoft.Automation/automationAccounts/nodeConfigurations/write | Azure Automation DSC のノード構成を書き込みます。 |
> | Action | Microsoft.Automation/automationAccounts/nodecounts/read | 指定された種類のノード数の概要を読み取ります |
> | Action | Microsoft.Automation/automationAccounts/nodes/delete | Azure Automation DSC のノードを削除します。 |
> | Action | Microsoft.Automation/automationAccounts/nodes/read | Azure Automation DSC のノードを読み取ります。 |
> | Action | Microsoft.Automation/automationAccounts/nodes/reports/content/read | Azure Automation DSC のレポートの内容を読み取ります |
> | Action | Microsoft.Automation/automationAccounts/nodes/reports/read | Azure Automation DSC のレポートを読み取ります。 |
> | Action | Microsoft.Automation/automationAccounts/nodes/write | Azure Automation DSC ノードを作成または更新します。 |
> | Action | Microsoft.Automation/automationAccounts/objectDataTypes/fields/read | Azure Automation の TypeFields を取得します。 |
> | Action | Microsoft.Automation/automationAccounts/providers/Microsoft.Insights/metricDefinitions/read | Automation のメトリックの定義を取得します。 |
> | Action | Microsoft.Automation/automationAccounts/read | Azure Automation アカウントを取得します。 |
> | Action | Microsoft.Automation/automationAccounts/runbooks/content/read | Azure Automation Runbook のコンテンツを取得します。 |
> | Action | Microsoft.Automation/automationAccounts/runbooks/delete | Azure Automation Runbook を削除します。 |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/content/write | Azure Automation Runbook の下書きのコンテンツを作成します。 |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/operationResults/read | Azure Automation Runbook の下書きの操作結果を取得します。 |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/read | Azure Automation Runbook の下書きを取得します。 |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/read | Azure Automation Runbook の下書きのテスト ジョブを取得します。 |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/resume/action | Azure Automation Runbook の下書きのテスト ジョブを再開します。 |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/stop/action | Azure Automation Runbook の下書きのテスト ジョブを停止します。 |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/suspend/action | Azure Automation Runbook の下書きのテスト ジョブを中断します。 |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | Azure Automation Runbook の下書きのテスト ジョブを作成します。 |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/undoEdit/action | Azure Automation Runbook の下書きの編集を元に戻します。 |
> | Action | Microsoft.Automation/automationAccounts/runbooks/getCount/action | Azure Automation Runbook 数を取得します。 |
> | Action | Microsoft.Automation/automationAccounts/runbooks/publish/action | Azure Automation Runbook の下書きを発行します。 |
> | Action | Microsoft.Automation/automationAccounts/runbooks/read | Azure Automation Runbook を取得します。 |
> | Action | Microsoft.Automation/automationAccounts/runbooks/write | Azure Automation Runbook を作成または更新します。 |
> | Action | Microsoft.Automation/automationAccounts/schedules/delete | Azure Automation スケジュール資産を削除します。 |
> | Action | Microsoft.Automation/automationAccounts/schedules/getCount/action | Azure Automation スケジュール数を取得します。 |
> | Action | Microsoft.Automation/automationAccounts/schedules/read | Azure Automation スケジュール資産を取得します。 |
> | Action | Microsoft.Automation/automationAccounts/schedules/write | Azure Automation スケジュール資産を作成または更新します。 |
> | Action | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Azure Automation のソフトウェア更新構成を削除します |
> | Action | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Azure Automation のソフトウェア更新構成を取得します |
> | Action | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Azure Automation のソフトウェア更新構成を作成または更新します |
> | Action | Microsoft.Automation/automationAccounts/statistics/read | Azure Automation の統計情報を取得します。 |
> | Action | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | Azure Automation の更新プログラムの展開マシンを取得します。 |
> | Action | Microsoft.Automation/automationAccounts/updateManagementPatchJob/read | Azure Automation の更新プログラムの管理パッチ ジョブを取得します。 |
> | Action | Microsoft.Automation/automationAccounts/usages/read | Azure Automation の使用状況を取得します。 |
> | Action | Microsoft.Automation/automationAccounts/variables/delete | Azure Automation 変数資産を削除します。 |
> | Action | Microsoft.Automation/automationAccounts/variables/read | Azure Automation 変数資産を読み取ります。 |
> | Action | Microsoft.Automation/automationAccounts/variables/write | Azure Automation 変数資産を作成または更新します。 |
> | Action | Microsoft.Automation/automationAccounts/watchers/delete | Azure Automation Watcher ジョブを削除します。 |
> | Action | Microsoft.Automation/automationAccounts/watchers/read | Azure Automation Watcher ジョブを取得します。 |
> | Action | Microsoft.Automation/automationAccounts/watchers/start/action | Azure Automation Watcher ジョブを開始します。 |
> | Action | Microsoft.Automation/automationAccounts/watchers/stop/action | Azure Automation Watcher ジョブを停止します。 |
> | Action | Microsoft.Automation/automationAccounts/watchers/streams/read | Azure Automation Watcher のジョブ ストリームを取得します。 |
> | Action | Microsoft.Automation/automationAccounts/watchers/watcherActions/delete | Azure Automation Watcher のジョブ アクションを削除します。 |
> | Action | Microsoft.Automation/automationAccounts/watchers/watcherActions/read | Azure Automation Watcher のジョブ アクションを取得します。 |
> | Action | Microsoft.Automation/automationAccounts/watchers/watcherActions/write | Azure Automation Watcher のジョブ アクションを作成します。 |
> | Action | Microsoft.Automation/automationAccounts/watchers/write | Azure Automation Watcher ジョブを作成します。 |
> | Action | Microsoft.Automation/automationAccounts/webhooks/action | Azure Automation Webhook の URI を生成します。 |
> | Action | Microsoft.Automation/automationAccounts/webhooks/delete | Azure Automation Webhook を削除します。  |
> | Action | Microsoft.Automation/automationAccounts/webhooks/read | Azure Automation Webhook を読み取ります。 |
> | Action | Microsoft.Automation/automationAccounts/webhooks/write | Azure Automation Webhook を作成または更新します。 |
> | Action | Microsoft.Automation/automationAccounts/write | Azure Automation アカウントを作成または更新します。 |
> | Action | Microsoft.Automation/operations/read | Azure Automation リソースの使用可能な操作を取得します。 |
> | Action | Microsoft.Automation/register/action | Azure Automation にサブスクリプションを登録します。 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.AzureActiveDirectory/b2cDirectories/delete | B2C ディレクトリ リソースを削除します。 |
> | Action | Microsoft.AzureActiveDirectory/b2cDirectories/read | B2C ディレクトリ リソースを表示します。 |
> | Action | Microsoft.AzureActiveDirectory/b2cDirectories/write | B2C ディレクトリ リソースを作成または更新します。 |
> | Action | Microsoft.AzureActiveDirectory/operations/read | Microsoft.AzureActiveDirectory リソース プロバイダーで使用可能なすべての API 操作を読み取ります。 |
> | Action | Microsoft.AzureActiveDirectory/register/action | Microsoft.AzureActiveDirectory リソース プロバイダーにサブスクリプションを登録します。 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.AzureStack/Operations/read | リソース プロバイダーの操作のプロパティを取得します。 |
> | Action | Microsoft.AzureStack/register/action | Microsoft.AzureStack リソース プロバイダーにサブスクリプションを登録します。 |
> | Action | Microsoft.AzureStack/registrations/customerSubscriptions/delete | Azure Stack の顧客サブスクリプションを削除します。 |
> | Action | Microsoft.AzureStack/registrations/customerSubscriptions/read | Azure Stack の顧客サブスクリプションのプロパティを取得します。 |
> | Action | Microsoft.AzureStack/registrations/customerSubscriptions/write | Azure Stack の顧客サブスクリプションを作成または更新します。 |
> | Action | Microsoft.AzureStack/registrations/delete | Azure Stack の登録を削除します。 |
> | Action | Microsoft.AzureStack/registrations/getActivationKey/action | Azure Stack の最新のライセンス認証キーを取得します。 |
> | Action | Microsoft.AzureStack/registrations/products/listDetails/action | Azure Stack Marketplace の製品の拡張詳細を取得します |
> | Action | Microsoft.AzureStack/registrations/products/read | Azure Stack Marketplace の製品のプロパティを取得します |
> | Action | Microsoft.AzureStack/registrations/read | Azure Stack の登録のプロパティを取得します |
> | Action | Microsoft.AzureStack/registrations/write | Azure Stack の登録を作成または更新します。 |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.Batch/batchAccounts/applications/delete | アプリケーションを削除します。 |
> | Action | Microsoft.Batch/batchAccounts/applications/read | アプリケーションを一覧表示するか、アプリケーションのプロパティを取得します。 |
> | Action | Microsoft.Batch/batchAccounts/applications/versions/activate/action | アプリケーション パッケージをアクティブにします。 |
> | Action | Microsoft.Batch/batchAccounts/applications/versions/delete | アプリケーション パッケージを削除します。 |
> | Action | Microsoft.Batch/batchAccounts/applications/versions/read | アプリケーション パッケージのプロパティを取得します。 |
> | Action | Microsoft.Batch/batchAccounts/applications/versions/write | 新しいアプリケーション パッケージを作成するか、既存のアプリケーション パッケージを更新します。 |
> | Action | Microsoft.Batch/batchAccounts/applications/write | 新しいアプリケーションを作成するか、既存のアプリケーションを更新します。 |
> | Action | Microsoft.Batch/batchAccounts/certificateOperationResults/read | Batch アカウントでの実行時間の長い証明書操作の結果を取得します。 |
> | Action | Microsoft.Batch/batchAccounts/certificates/cancelDelete/action | Batch アカウントでの失敗した証明書削除を取り消します |
> | Action | Microsoft.Batch/batchAccounts/certificates/delete | Batch アカウントから証明書を削除します。 |
> | Action | Microsoft.Batch/batchAccounts/certificates/read | Batch アカウントの証明書を一覧表示するか、証明書のプロパティを取得します。 |
> | Action | Microsoft.Batch/batchAccounts/certificates/write | Batch アカウントで新しい証明書を作成するか、既存の証明書を更新します。 |
> | Action | Microsoft.Batch/batchAccounts/delete | Batch アカウントを削除します。 |
> | Action | Microsoft.Batch/batchAccounts/listkeys/action | Batch アカウントのアクセス キーを一覧表示します。 |
> | Action | Microsoft.Batch/batchAccounts/operationResults/read | Batch アカウントの実行時間の長い操作の結果を取得します。 |
> | Action | Microsoft.Batch/batchAccounts/poolOperationResults/read | Batch アカウントでの実行時間の長いプール操作の結果を取得します。 |
> | Action | Microsoft.Batch/batchAccounts/pools/delete | Batch アカウントからプールを削除します。 |
> | Action | Microsoft.Batch/batchAccounts/pools/disableAutoscale/action | Batch アカウント プールの自動スケーリングを無効にします。 |
> | Action | Microsoft.Batch/batchAccounts/pools/read | Batch アカウントのプールを一覧表示するか、プールのプロパティを取得します。 |
> | Action | Microsoft.Batch/batchAccounts/pools/stopResize/action | Batch アカウント プールで実行中のサイズ変更操作を停止します。 |
> | Action | Microsoft.Batch/batchAccounts/pools/upgradeOs/action | Batch アカウント プールのオペレーティング システムをアップグレードします。 |
> | Action | Microsoft.Batch/batchAccounts/pools/write | Batch アカウントに新しいプールを作成するか、既存のプールを更新します。 |
> | Action | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Action | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Action | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/logDefinitions/read | Batch サービスで使用可能なログを取得します。 |
> | Action | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/metricDefinitions/read | Batch サービスで使用可能なメトリックを取得します。 |
> | Action | Microsoft.Batch/batchAccounts/read | Batch アカウントを一覧表示するか、Batch アカウントのプロパティを取得します。 |
> | Action | Microsoft.Batch/batchAccounts/regeneratekeys/action | Batch アカウントのアクセス キーを再生成します。 |
> | Action | Microsoft.Batch/batchAccounts/syncAutoStorageKeys/action | Batch アカウント用に構成された自動ストレージ アカウントのアクセス キーを同期します。 |
> | Action | Microsoft.Batch/batchAccounts/write | 新しい Batch アカウントを作成するか、既存の Batch アカウントを更新します。 |
> | Action | Microsoft.Batch/locations/checkNameAvailability/action | アカウント名が有効であり、使用されていないことを確認します。 |
> | Action | Microsoft.Batch/locations/quotas/read | 指定された Azure リージョンの指定されたサブスクリプションの Batch クォータを取得します。 |
> | Action | Microsoft.Batch/operations/read | Microsoft.Batch リソース プロバイダーで使用できる操作を一覧表示します。 |
> | Action | Microsoft.Batch/register/action | Batch リソース プロバイダーにサブスクリプションを登録し、Batch アカウントを作成できるようにします。 |
> | Action | Microsoft.Batch/unregister/action | Batch リソース プロバイダーのサブスクリプションを登録解除し、Batch アカウントを作成できないようにします。 |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.BatchAI/clusters/read | Batch AI クラスターを一覧表示するか、Batch AI クラスターのプロパティを取得します。 |
> | Action | Microsoft.BatchAI/fileservers/read | Batch AI ファイル サーバーを一覧表示するか、Batch AI ファイル サーバーのプロパティを取得します。 |
> | Action | Microsoft.BatchAI/locations/operationresults/read | 指定された Azure リージョンにおける Batch AI 非同期操作の結果を取得します。 |
> | Action | Microsoft.BatchAI/locations/operationstatuses/read | 指定された Azure リージョンにおける Batch AI 非同期操作の状態を取得します。 |
> | Action | Microsoft.BatchAI/locations/usages/read | 指定された Azure リージョンの指定されたサブスクリプションの Batch AI の使用状況を取得します。 |
> | Action | Microsoft.BatchAI/register/action | Batch AI リソース プロバイダーにサブスクリプションを登録し、Batch AI リソースを作成できるようにします。 |
> | Action | Microsoft.BatchAI/unregister/action | Batch AI リソースの作成を妨げている Batch AI リソース プロバイダーのサブスクリプションの登録を解除します。 |
> | Action | Microsoft.BatchAI/workspaces/clusters/delete | Batch AI クラスターを削除します。 |
> | Action | Microsoft.BatchAI/workspaces/clusters/read | Batch AI クラスターを一覧表示するか、Batch AI クラスターのプロパティを取得します。 |
> | Action | Microsoft.BatchAI/workspaces/clusters/remoteLoginInformation/action | Batch AI クラスターのリモート ログイン情報を一覧表示します。 |
> | Action | Microsoft.BatchAI/workspaces/clusters/write | 新しい Batch AI クラスターを作成するか、既存の Batch AI クラスターを更新します。 |
> | Action | Microsoft.BatchAI/workspaces/delete | Batch AI ワークスペースを削除します。 |
> | Action | Microsoft.BatchAI/workspaces/experiments/delete | Batch AI 実験を削除します。 |
> | Action | Microsoft.BatchAI/workspaces/experiments/jobs/delete | Batch AI ジョブを削除します。 |
> | Action | Microsoft.BatchAI/workspaces/experiments/jobs/listoutputfiles/action | Batch AI ジョブの出力ファイルを一覧表示します。 |
> | Action | Microsoft.BatchAI/workspaces/experiments/jobs/read | Batch AI ジョブを一覧表示するか、Batch AI ジョブのプロパティを取得します。 |
> | Action | Microsoft.BatchAI/workspaces/experiments/jobs/remoteLoginInformation/action | Batch AI ジョブのリモート ログイン情報を一覧表示します。 |
> | Action | Microsoft.BatchAI/workspaces/experiments/jobs/terminate/action | Batch AI ジョブを終了します。 |
> | Action | Microsoft.BatchAI/workspaces/experiments/jobs/write | 新しい Batch AI ジョブを作成するか、既存の Batch AI ジョブを更新します。 |
> | Action | Microsoft.BatchAI/workspaces/experiments/read | Batch AI 実験を一覧表示するか、Batch AI 実験のプロパティを取得します。 |
> | Action | Microsoft.BatchAI/workspaces/experiments/write | 新しい Batch AI 実験を作成するか、既存の Batch AI 実験を更新します。 |
> | Action | Microsoft.BatchAI/workspaces/fileservers/delete | Batch AI ファイル サーバーを削除します。 |
> | Action | Microsoft.BatchAI/workspaces/fileservers/read | Batch AI ファイル サーバーを一覧表示するか、Batch AI ファイル サーバーのプロパティを取得します。 |
> | Action | Microsoft.BatchAI/workspaces/fileservers/write | 新しい Batch AI ファイル サーバーを作成するか、既存の Batch AI ファイル サーバーを更新します。 |
> | Action | Microsoft.BatchAI/workspaces/read | Batch AI ワークスペースを一覧表示するか、Batch AI ワークスペースのプロパティを取得します。 |
> | Action | Microsoft.BatchAI/workspaces/write | 新しい Batch AI ワークスペースを作成するか、既存の Batch AI ワークスペースを更新します。 |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.Billing/billingPeriods/read | 使用可能な請求期間を一覧表示します。 |
> | Action | Microsoft.Billing/invoices/read | 使用可能な請求書を一覧表示します。 |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.BingMaps/mapApis/Delete | 削除操作。 |
> | Action | Microsoft.BingMaps/mapApis/listSecrets/action | シークレットを一覧表示します。 |
> | Action | Microsoft.BingMaps/mapApis/listSingleSignOnToken/action | リソースのシングル サインオン認証トークンを読み取ります。 |
> | Action | Microsoft.BingMaps/mapApis/Read | 読み取り操作。 |
> | Action | Microsoft.BingMaps/mapApis/regenerateKey/action | キーを再生成します。 |
> | Action | Microsoft.BingMaps/mapApis/Write | 書き込み操作。 |
> | Action | Microsoft.BingMaps/Operations/read | 操作の説明。 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.BotService/botServices/channels/delete | ボット サービスを削除する |
> | Action | Microsoft.BotService/botServices/channels/read | ボット サービスを読み取る |
> | Action | Microsoft.BotService/botServices/channels/write | ボット サービスを書き込む |
> | Action | Microsoft.BotService/botServices/connections/delete | ボット サービスを削除する |
> | Action | Microsoft.BotService/botServices/connections/read | ボット サービスを読み取る |
> | Action | Microsoft.BotService/botServices/connections/write | ボット サービスを書き込む |
> | Action | Microsoft.BotService/botServices/delete | ボット サービスを削除する |
> | Action | Microsoft.BotService/botServices/read | ボット サービスを読み取る |
> | Action | Microsoft.BotService/botServices/write | ボット サービスを書き込む |
> | Action | Microsoft.BotService/locations/operationresults/read | 非同期操作の状態を読み取る |
> | Action | Microsoft.BotService/Operations/read | すべてのリソースの種類に対して操作を読み取る |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.Cache/checknameavailability/action | 新しい Redis Cache で名前を使用できるかどうかを確認します。 |
> | Action | Microsoft.Cache/locations/operationresults/read | 前に "Location" ヘッダーがクライアントに返された、実行時間の長い操作の結果を取得します。 |
> | Action | Microsoft.Cache/operations/read | "Microsoft.Cache" プロバイダーがサポートする操作を一覧表示します。 |
> | Action | Microsoft.Cache/redis/delete | Redis Cache 全体を削除します。 |
> | Action | Microsoft.Cache/redis/export/action | 指定された形式のプレフィックス付きストレージ BLOB に Redis データをエクスポートします。 |
> | Action | Microsoft.Cache/redis/firewallRules/delete | Redis Cache の IP ファイアウォール規則を削除します。 |
> | Action | Microsoft.Cache/redis/firewallRules/read | Redis Cache の IP ファイアウォール規則を取得します。 |
> | Action | Microsoft.Cache/redis/firewallRules/write | Redis Cache の IP ファイアウォール規則を編集します。 |
> | Action | Microsoft.Cache/redis/forceReboot/action | キャッシュ インスタンスを強制的に再起動します。データが失われる可能性があります。 |
> | Action | Microsoft.Cache/redis/import/action | 指定された形式のデータを複数の BLOB から Redis にインポートします。 |
> | Action | Microsoft.Cache/redis/linkedservers/delete | Redis Cache からリンク サーバーを削除します。 |
> | Action | Microsoft.Cache/redis/linkedservers/read | Redis Cache に関連付けられているリンク サーバーを取得します。 |
> | Action | Microsoft.Cache/redis/linkedservers/write | Redis Cache にリンク サーバーを追加します。 |
> | Action | Microsoft.Cache/redis/listKeys/action | 管理ポータルで Redis Cache のアクセス キーの値を表示します。 |
> | Action | Microsoft.Cache/redis/listUpgradeNotifications/read | キャッシュのテナントに対する最新のアップグレード通知を一覧表示します。 |
> | Action | Microsoft.Cache/redis/metricDefinitions/read | Redis Cache の利用可能なメトリックを取得します。 |
> | Action | Microsoft.Cache/redis/patchSchedules/delete | Redis Cache のパッチ スケジュールを削除します。 |
> | Action | Microsoft.Cache/redis/patchSchedules/read | Redis Cache のパッチ スケジュールを取得します。 |
> | Action | Microsoft.Cache/redis/patchSchedules/write | Redis Cache のパッチ スケジュールを変更します。 |
> | Action | Microsoft.Cache/redis/read | 管理ポータルで Redis Cache の設定と構成を表示します。 |
> | Action | Microsoft.Cache/redis/regenerateKey/action | 管理ポータルで Redis Cache のアクセス キーの値を変更します。 |
> | Action | Microsoft.Cache/redis/start/action | キャッシュ インスタンスを開始します。 |
> | Action | Microsoft.Cache/redis/stop/action | キャッシュ インスタンスを停止します。 |
> | Action | Microsoft.Cache/redis/write | 管理ポータルで Redis Cache の設定と構成を変更します。 |
> | Action | Microsoft.Cache/register/action | "Microsoft.Cache" リソース プロバイダーをサブスクリプションに登録します。 |
> | Action | Microsoft.Cache/unregister/action | サブスクリプションへの "Microsoft.Cache" リソース プロバイダーの登録を解除します。 |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.Capacity/register/action | キャパシティ リソース プロバイダーに登録し、キャパシティ リソースを作成できるようにします。 |
> | Action | Microsoft.Capacity/reservationorders/action | 予約を更新します。 |
> | Action | Microsoft.Capacity/reservationorders/delete | 予約を削除します。 |
> | Action | Microsoft.Capacity/reservationorders/read | すべての予約を読み取ります。 |
> | Action | Microsoft.Capacity/reservationorders/reservations/action | 予約を更新します。 |
> | Action | Microsoft.Capacity/reservationorders/reservations/delete | 予約を削除します。 |
> | Action | Microsoft.Capacity/reservationorders/reservations/read | すべての予約を読み取ります。 |
> | Action | Microsoft.Capacity/reservationorders/reservations/revisions/read | すべての予約を読み取ります。 |
> | Action | Microsoft.Capacity/reservationorders/reservations/write | 予約を作成します。 |
> | Action | Microsoft.Capacity/reservationorders/write | 予約を作成します。 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.Cdn/CheckNameAvailability/action |  |
> | Action | Microsoft.Cdn/CheckResourceUsage/action |  |
> | Action | Microsoft.Cdn/edgenodes/delete |  |
> | Action | Microsoft.Cdn/edgenodes/read |  |
> | Action | Microsoft.Cdn/edgenodes/write |  |
> | Action | Microsoft.Cdn/operationresults/delete |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/CheckResourceUsage/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/delete |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/CheckResourceUsage/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/delete |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/read |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/write |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/delete |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/Load/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/delete |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/read |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/write |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/Purge/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/read |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/Start/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/Stop/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/ValidateCustomDomain/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/write |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/GenerateSsoUri/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/GetSupportedOptimizationTypes/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/read |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/write |  |
> | Action | Microsoft.Cdn/operationresults/read |  |
> | Action | Microsoft.Cdn/operationresults/write |  |
> | Action | Microsoft.Cdn/operations/read |  |
> | Action | Microsoft.Cdn/profiles/CheckResourceUsage/action |  |
> | Action | Microsoft.Cdn/profiles/delete |  |
> | Action | Microsoft.Cdn/profiles/endpoints/CheckResourceUsage/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/customdomains/delete |  |
> | Action | Microsoft.Cdn/profiles/endpoints/customdomains/DisableCustomHttps/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/customdomains/EnableCustomHttps/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/customdomains/read |  |
> | Action | Microsoft.Cdn/profiles/endpoints/customdomains/write |  |
> | Action | Microsoft.Cdn/profiles/endpoints/delete |  |
> | Action | Microsoft.Cdn/profiles/endpoints/Load/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/origins/delete |  |
> | Action | Microsoft.Cdn/profiles/endpoints/origins/read |  |
> | Action | Microsoft.Cdn/profiles/endpoints/origins/write |  |
> | Action | Microsoft.Cdn/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Action | Microsoft.Cdn/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Action | Microsoft.Cdn/profiles/endpoints/providers/Microsoft.Insights/logDefinitions/read | Microsoft.Cdn で使用可能なログを取得します。 |
> | Action | Microsoft.Cdn/profiles/endpoints/Purge/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/read |  |
> | Action | Microsoft.Cdn/profiles/endpoints/Start/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/Stop/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/ValidateCustomDomain/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/write |  |
> | Action | Microsoft.Cdn/profiles/GenerateSsoUri/action |  |
> | Action | Microsoft.Cdn/profiles/GetSupportedOptimizationTypes/action |  |
> | Action | Microsoft.Cdn/profiles/read |  |
> | Action | Microsoft.Cdn/profiles/write |  |
> | Action | Microsoft.Cdn/register/action | CDN リソース プロバイダーにサブスクリプションを登録し、CDN プロファイルを作成できるようにします。 |
> | Action | Microsoft.Cdn/ValidateProbe/action |  |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.CertificateRegistration/certificateOrders/certificates/Delete | 既存の証明書を削除します。 |
> | Action | Microsoft.CertificateRegistration/certificateOrders/certificates/Read | 証明書の一覧を取得します。 |
> | Action | Microsoft.CertificateRegistration/certificateOrders/certificates/Write | 新しい証明書を追加するか、既存の証明書を更新します。 |
> | Action | Microsoft.CertificateRegistration/certificateOrders/Delete | 既存の AppServiceCertificate を削除します。 |
> | Action | Microsoft.CertificateRegistration/certificateOrders/Read | CertificateOrders の一覧を取得します。 |
> | Action | Microsoft.CertificateRegistration/certificateOrders/reissue/Action | 既存の certificateorder を再発行します。 |
> | Action | Microsoft.CertificateRegistration/certificateOrders/renew/Action | 既存の certificateorder を更新します。 |
> | Action | Microsoft.CertificateRegistration/certificateOrders/resendEmail/Action | 証明書の電子メールを再送信します。 |
> | Action | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | 要求の電子メールを別の電子メール アドレスに再送信します。 |
> | Action | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | 発行済みの App Service 証明書のサイト シールを取得します。 |
> | Action | Microsoft.CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | 証明書のアクションの一覧を取得します。 |
> | Action | Microsoft.CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | 証明書の電子メールの履歴を取得します。 |
> | Action | Microsoft.CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | ドメインの所有権を検証する |
> | Action | Microsoft.CertificateRegistration/certificateOrders/Write | 新しい certificateOrder を追加するか、既存の certificateOrder を更新します。 |
> | Action | Microsoft.CertificateRegistration/operations/Read | アプリ サービス証明書登録のすべての操作を一覧表示します。 |
> | Action | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | サービス アプリ プリンシパルのサービス プリンシパルをプロビジョニングします。 |
> | Action | Microsoft.CertificateRegistration/register/action | Microsoft Certificates リソース プロバイダーをサブスクリプションに登録します。 |
> | Action | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | 証明書購入オブジェクトを送信せずに検証します。 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.ClassicCompute/capabilities/read | 機能を表示します。 |
> | Action | Microsoft.ClassicCompute/checkDomainNameAvailability/action | 指定されたドメイン名を使用できるかどうかを確認します。 |
> | Action | Microsoft.ClassicCompute/checkDomainNameAvailability/read | 特定のドメイン名の使用状況を取得します。 |
> | Action | Microsoft.ClassicCompute/domainNames/active/write | アクティブなドメイン名を設定します。 |
> | Action | Microsoft.ClassicCompute/domainNames/availabilitySets/read | リソースの可用性セットを表示します。 |
> | Action | Microsoft.ClassicCompute/domainNames/capabilities/read | ドメイン名機能を表示します。 |
> | Action | Microsoft.ClassicCompute/domainNames/delete | リソースのドメイン名を削除します。 |
> | Action | Microsoft.ClassicCompute/domainNames/deploymentslots/read | デプロイ スロットを表示します。 |
> | Action | Microsoft.ClassicCompute/domainNames/deploymentslots/state/read | デプロイ スロットの状態を取得します。 |
> | Action | Microsoft.ClassicCompute/domainNames/deploymentslots/state/write | デプロイ スロットの状態を追加します。 |
> | Action | Microsoft.ClassicCompute/domainNames/deploymentslots/write | デプロイを作成または更新します。 |
> | Action | Microsoft.ClassicCompute/domainNames/extensions/delete | ドメイン名拡張子を削除します。 |
> | Action | Microsoft.ClassicCompute/domainNames/extensions/operationStatuses/read | ドメイン名拡張子の操作の状態を読み取ります。 |
> | Action | Microsoft.ClassicCompute/domainNames/extensions/read | ドメイン名拡張子を返します。 |
> | Action | Microsoft.ClassicCompute/domainNames/extensions/write | ドメイン名拡張子を追加します。 |
> | Action | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/delete | 新しい内部負荷分散を削除します。 |
> | Action | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | ドメイン名の内部ロード バランサーの操作の状態を読み取ります。 |
> | Action | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/read | 内部ロード バランサーを取得します。 |
> | Action | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/write | 新しい内部負荷分散を作成します。 |
> | Action | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | ドメイン名の負荷分散エンドポイント セットの操作の状態を読み取ります。 |
> | Action | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/read | 負荷分散エンドポイント セットを取得します。 |
> | Action | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/write | 負荷分散エンドポイント セットを追加します。 |
> | Action | Microsoft.ClassicCompute/domainNames/operationstatuses/read | ドメイン名の操作状態を取得します。 |
> | Action | Microsoft.ClassicCompute/domainNames/operationStatuses/read | ドメイン名拡張子の操作の状態を読み取ります。 |
> | Action | Microsoft.ClassicCompute/domainNames/read | リソースのドメイン名を返します。 |
> | Action | Microsoft.ClassicCompute/domainNames/serviceCertificates/delete | 使用するサービス証明書を削除します。 |
> | Action | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | ドメイン名のサービス証明書の操作の状態を読み取ります。 |
> | Action | Microsoft.ClassicCompute/domainNames/serviceCertificates/read | 使用するサービス証明書を返します。 |
> | Action | Microsoft.ClassicCompute/domainNames/serviceCertificates/write | 使用するサービス証明書を追加または変更します。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/abortMigration/action | デプロイ スロットの移行を中止します。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/commitMigration/action | デプロイ スロットの移行をコミットします。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/delete | 指定されたデプロイ スロットを削除します。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/operationStatuses/read | ドメイン名スロットの操作の状態を読み取ります。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/prepareMigration/action | デプロイ スロットの移行を準備します。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/read | デプロイ スロットを表示します。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/delete | デプロイ スロット ロールの拡張子参照を削除します。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/operationStatuses/read | ドメイン名スロット ロールの拡張子参照の操作の状態を読み取ります。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/read | デプロイ スロット ロールの拡張子参照を返します。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/write | デプロイ スロット ロールの拡張子参照を追加または変更します。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/metricdefinitions/read | ドメイン名のロール メトリックの定義を取得します。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/metrics/read | ドメイン名のロール メトリックを取得します。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/operationstatuses/read | ドメイン名のスロット ロールの操作状態を取得します。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read | 診断設定を取得します。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write | 診断設定を追加または変更します。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read | メトリック定義を取得します。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/read | デプロイ スロットのロールを取得します。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/downloadremotedesktopconnectionfile/action | ドメイン名のスロット ロールのロール インスタンスのリモート デスクトップ接続ファイルをダウンロードします。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | ドメイン名のスロット ロールのロール インスタンスの操作状態を取得します。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/read | ロール インスタンスを取得します。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/rebuild/action | ロール インスタンスを再構築します。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/reimage/action | ロール インスタンスを再イメージ化します。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/restart/action | ロール インスタンスを再起動します。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/skus/read | デプロイ スロットのロールの SKU を取得します。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/write | デプロイ スロットのロールを追加します。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/start/action | デプロイ スロットを開始します。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/state/start/write | デプロイ スロットを停止状態に変更します。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/state/stop/write | デプロイ スロットを開始状態に変更します。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/stop/action | デプロイ スロットを中断します。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/upgradeDomain/write | ドメインのウォーク アップグレードを行います。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/validateMigration/action | デプロイ スロットの移行を検証します。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/write | デプロイを作成または更新します。 |
> | Action | Microsoft.ClassicCompute/domainNames/swap/action | ステージング スロットを運用スロットに切り替えます。 |
> | Action | Microsoft.ClassicCompute/domainNames/write | リソースのドメイン名を追加または変更します。 |
> | Action | Microsoft.ClassicCompute/moveSubscriptionResources/action | すべてのクラシック リソースを別のサブスクリプションに移動します。 |
> | Action | Microsoft.ClassicCompute/operatingSystemFamilies/read | Microsoft Azure で利用できるゲスト オペレーティング システムのファミリと、それぞれのファミリで利用できるオペレーティング システムのバージョンを一覧表示します。 |
> | Action | Microsoft.ClassicCompute/operatingSystems/read | Microsoft Azure で現在利用できるゲスト オペレーティング システムのバージョンを一覧表示します。 |
> | Action | Microsoft.ClassicCompute/operations/read | 操作の一覧を取得します。 |
> | Action | Microsoft.ClassicCompute/operationStatuses/read | リソースの操作の状態を読み取ります。 |
> | Action | Microsoft.ClassicCompute/quotas/read | サブスクリプションのクォータを取得します。 |
> | Action | Microsoft.ClassicCompute/register/action | 従来のコンピューティングに登録します。 |
> | Action | Microsoft.ClassicCompute/resourceTypes/skus/read | サポートされているリソースの種類の SKU の一覧を取得します。 |
> | Action | Microsoft.ClassicCompute/validateSubscriptionMoveAvailability/action | 従来の移動操作でサブスクリプションが利用可能かどうかを検証します。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | 仮想マシンに関連付けられたネットワーク セキュリティ グループを削除します。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | 仮想マシンに関連付けられたネットワーク セキュリティ グループの操作の状態を読み取ります。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | 仮想マシンに関連付けられたネットワーク セキュリティ グループを取得します。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | 仮想マシンに関連付けられたネットワーク セキュリティ グループを追加します。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/asyncOperations/read | 可能な非同期操作を取得します。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/attachDisk/action | 仮想マシンにデータ ディスクを接続します。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/capture/action | 仮想マシンをキャプチャします。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/delete | 仮想マシンを削除します。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/detachDisk/action | 仮想マシンからデータ ディスクを切断します。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/diagnosticsettings/read | 仮想マシンの診断設定を取得します。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/disks/read | データ ディスクの一覧を取得します。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/action | 仮想マシンの RDP ファイルをダウンロードします。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | 仮想マシン拡張機能の操作の状態を読み取ります。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/extensions/read | 仮想マシン拡張機能を取得します。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/extensions/write | 仮想マシン拡張機能を追加します。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/metricdefinitions/read | 仮想マシンのメトリック定義を取得します。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/metrics/read | メトリックを取得します。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | ネットワーク インターフェイスに関連付けられたネットワーク セキュリティ グループを削除します。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | 仮想マシンに関連付けられたネットワーク セキュリティ グループの操作の状態を読み取ります。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | ネットワーク インターフェイスに関連付けられたネットワーク セキュリティ グループを取得します。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | ネットワーク インターフェイスに関連付けられたネットワーク セキュリティ グループを追加します。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/operationStatuses/read | 仮想マシンの操作の状態を読み取ります。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/performMaintenance/action | 仮想マシンに対してメンテナンスを実行します。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read | 診断設定を取得します。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write | 診断設定を追加または変更します。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | メトリック定義を取得します。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/read | 仮想マシンの一覧を取得します。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/redeploy/action | 仮想マシンを再デプロイします。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/restart/action | 仮想マシンを再起動します。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/shutdown/action | 仮想マシンをシャットダウンします。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/start/action | 仮想マシンを開始します。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/stop/action | 仮想マシンを停止します。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/write | 仮想マシンを追加または変更します。 |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.ClassicNetwork/expressroutecrossconnections/operationstatuses/read | ExpressRoute のクロス接続操作の状態を取得します。 |
> | Action | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/delete | ExpressRoute 交差接続のピアリングを削除します。 |
> | Action | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/operationstatuses/read | ExpressRoute 交差接続のピアリング操作の状態を取得します。 |
> | Action | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/read | ExpressRoute 交差接続のピアリングを取得します。 |
> | Action | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/write | ExpressRoute 交差接続のピアリングを追加します。 |
> | Action | Microsoft.ClassicNetwork/expressroutecrossconnections/read | ExpressRoute 交差接続を取得します。 |
> | Action | Microsoft.ClassicNetwork/expressroutecrossconnections/write | ExpressRoute 交差接続を追加します。 |
> | Action | Microsoft.ClassicNetwork/gatewaySupportedDevices/read | サポート対象デバイスの一覧を取得します。 |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/delete | ネットワーク セキュリティ グループを削除します。 |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | ネットワーク セキュリティ グループの操作の状態を読み取ります。 |
> | Action | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | ネットワーク セキュリティ グループの診断設定を取得します。 |
> | Action | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | ネットワーク セキュリティ グループの診断設定を作成または更新します。この操作は、Insghts リソース プロバイダーによって補完されます。 |
> | Action | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | ネットワーク セキュリティ グループのイベントを取得します。 |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/read | ネットワーク セキュリティ グループを取得します。 |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | セキュリティ規則を削除します。 |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | ネットワーク セキュリティ グループのセキュリティ規則の操作の状態を読み取ります。 |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | セキュリティ規則を取得します。 |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | セキュリティ規則を追加または更新します。 |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/write | 新しいネットワーク セキュリティ グループを追加します。 |
> | Action | Microsoft.ClassicNetwork/operations/read | 従来のネットワーク操作を取得します。 |
> | Action | Microsoft.ClassicNetwork/quotas/read | サブスクリプションのクォータを取得します。 |
> | Action | Microsoft.ClassicNetwork/register/action | 従来のネットワークに登録します。 |
> | Action | Microsoft.ClassicNetwork/reservedIps/delete | 予約済み IP を削除します。 |
> | Action | Microsoft.ClassicNetwork/reservedIps/join/action | 予約済み IP を結合します。 |
> | Action | Microsoft.ClassicNetwork/reservedIps/link/action | 予約済み IP をリンクします。 |
> | Action | Microsoft.ClassicNetwork/reservedIps/operationStatuses/read | 予約済み IP の操作の状態を読み取ります。 |
> | Action | Microsoft.ClassicNetwork/reservedIps/read | 予約済み IP を取得します。 |
> | Action | Microsoft.ClassicNetwork/reservedIps/write | 新しい予約済み IP を追加します。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/abortMigration/action | 仮想ネットワークの移行を中止する |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/capabilities/read | 機能を表示します。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/checkIPAddressAvailability/action | 指定された IP アドレスを仮想ネットワーク内で使用できるかどうかを確認します。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/commitMigration/action | 仮想ネットワークの移行をコミットする |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/delete | 仮想ネットワークを削除します。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/delete | クライアント証明書の失効を取り消します。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/read | 失効したクライアント証明書を読み取ります。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/write | クライアント証明書を失効させます。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/delete | 仮想ネットワーク ゲートウェイ クライアント証明書を削除します。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/download/action | 拇印によって証明書をダウンロードします。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/action | 仮想ネットワーク ゲートウェイ証明書パッケージを一覧表示します。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/read | クライアント ルート証明書を検索します。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/write | 新しいクライアント ルート証明書をアップロードします。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/connect/action | サイト間ゲートウェイ接続を行います。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/disconnect/action | サイト間ゲートウェイ接続を切断します。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/read | 接続の一覧を取得します。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/test/action | サイト間ゲートウェイ接続をテストします。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/delete | 仮想ネットワーク ゲートウェイを削除します。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDeviceConfigurationScript/action | デバイス構成スクリプトをダウンロードします。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDiagnostics/action | ゲートウェイ診断をダウンロードします。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/listCircuitServiceKey/action | 回線サービス キーを取得します。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/listPackage/action | 仮想ネットワーク ゲートウェイ パッケージを一覧表示します。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/operationStatuses/read | 仮想ネットワーク ゲートウェイの操作の状態を読み取ります。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/packages/read | 仮想ネットワーク ゲートウェイ パッケージを取得します。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/read | 仮想ネットワーク ゲートウェイを取得します。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/startDiagnostics/action | 仮想ネットワーク ゲートウェイの診断を開始します。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/stopDiagnostics/action | 仮想ネットワーク ゲートウェイの診断を停止します。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/write | 仮想ネットワーク ゲートウェイを追加します。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/join/action | 仮想ネットワークに参加します。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | 仮想ネットワークの操作の状態を読み取ります。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/peer/action | 仮想ネットワークを別の仮想ネットワークとピアリングします。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/prepareMigration/action | 仮想ネットワークの移行を準備する |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/read | 仮想ネットワークを取得します。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | リモート仮想ネットワーク ピアリング プロキシを削除します。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | リモート仮想ネットワーク ピアリング プロキシを取得します。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | リモート仮想ネットワーク ピアリング プロキシを追加または更新します。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | サブネットに関連付けられたネットワーク セキュリティ グループを削除します。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | 仮想ネットワーク サブネットに関連付けられたネットワーク セキュリティ グループの操作の状態を読み取ります。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | サブネットに関連付けられたネットワーク セキュリティ グループを取得します。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | サブネットに関連付けられたネットワーク セキュリティ グループを追加します。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/validateMigration/action | 仮想ネットワークの移行を検証する |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/virtualNetworkPeerings/read | 仮想ネットワーク ピアリングを取得します。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/write | 新しい仮想ネットワークを追加します。 |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.ClassicStorage/capabilities/read | 機能を表示します。 |
> | Action | Microsoft.ClassicStorage/checkStorageAccountAvailability/action | ストレージ アカウントを使用できるかどうかを確認します。 |
> | Action | Microsoft.ClassicStorage/checkStorageAccountAvailability/read | ストレージ アカウントの使用状況を取得します。 |
> | Action | Microsoft.ClassicStorage/disks/read | ストレージ アカウント ディスクを返します。 |
> | Action | Microsoft.ClassicStorage/images/operationstatuses/read | イメージの操作状態を取得します。 |
> | Action | Microsoft.ClassicStorage/images/read | イメージを返します。 |
> | Action | Microsoft.ClassicStorage/operations/read | 従来のストレージ操作を取得します |
> | Action | Microsoft.ClassicStorage/osImages/read | オペレーティング システム イメージを返します。 |
> | Action | Microsoft.ClassicStorage/osPlatformImages/read | オペレーティング システム プラットフォーム イメージを取得します。 |
> | Action | Microsoft.ClassicStorage/publicImages/read | 仮想マシンのパブリック イメージを取得します。 |
> | Action | Microsoft.ClassicStorage/quotas/read | サブスクリプションのクォータを取得します。 |
> | Action | Microsoft.ClassicStorage/register/action | 従来のストレージに登録します。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/abortMigration/action | ストレージ アカウントの移行を中止します。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/commitMigration/action | ストレージ アカウントの移行をコミットします。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/delete | ストレージ アカウントを削除します。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/disks/delete | 指定されたストレージ アカウント ディスクを削除します。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | リソースの操作の状態を読み取ります。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/disks/read | ストレージ アカウント ディスクを返します。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/disks/write | ストレージ アカウント ディスクを追加します。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/images/delete | 指定されたストレージ アカウント イメージを削除します。 (非推奨になりました。 'Microsoft.ClassicStorage/storageAccounts/vmImages' を使用してください。) |
> | Action | Microsoft.ClassicStorage/storageAccounts/images/operationstatuses/read | ストレージ アカウント イメージの操作状態を返します。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/images/read | ストレージ アカウント イメージを返します。 (非推奨になりました。 'Microsoft.ClassicStorage/storageAccounts/vmImages' を使用してください。) |
> | Action | Microsoft.ClassicStorage/storageAccounts/listKeys/action | ストレージ アカウントのアクセス キーを一覧表示します。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | リソースの操作の状態を読み取ります。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/osImages/delete | 指定されたストレージ アカウント オペレーティング システム イメージを削除します。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/osImages/read | ストレージ アカウント オペレーティング システム イメージを返します。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/osImages/write | 指定されたストレージ アカウント オペレーティング システム イメージを追加します。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/prepareMigration/action | ストレージ アカウントの移行を準備します。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/read | 特定のアカウントのストレージ アカウントを返します。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/regenerateKey/action | ストレージ アカウントの既存のアクセス キーを再生成します。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/read | 診断設定を取得します。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/write | 診断設定を追加または変更します。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/services/metricDefinitions/read | メトリック定義を取得します。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/services/metrics/read | メトリックを取得します。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/services/read | 利用可能なサービスを取得します。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/validateMigration/action | ストレージ アカウントの移行を検証します。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/vmImages/delete | 指定された仮想マシン イメージを削除します。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/vmImages/operationstatuses/read | 指定された仮想マシン イメージの操作状態を取得します。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/vmImages/read | 仮想マシン イメージを返します。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/vmImages/write | 指定された仮想マシン イメージを追加します。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/write | 新しいストレージ アカウントを追加します。 |
> | Action | Microsoft.ClassicStorage/vmImages/read | 仮想マシン イメージを一覧表示します。 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.CognitiveServices/accounts/delete | API アカウントを削除します。 |
> | Action | Microsoft.CognitiveServices/accounts/listKeys/action | キーを一覧表示します。 |
> | Action | Microsoft.CognitiveServices/accounts/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Action | Microsoft.CognitiveServices/accounts/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Action | Microsoft.CognitiveServices/accounts/providers/Microsoft.Insights/logDefinitions/read | Cognitive Services アカウントで閲覧できるログを取得します |
> | Action | Microsoft.CognitiveServices/accounts/providers/Microsoft.Insights/metricDefinitions/read | Cognitive Services で使用可能なメトリックを取得します。 |
> | Action | Microsoft.CognitiveServices/accounts/read | API アカウントを読み取ります。 |
> | Action | Microsoft.CognitiveServices/accounts/regenerateKey/action | キーを再生成します。 |
> | Action | Microsoft.CognitiveServices/accounts/skus/read | 既存のリソースの使用可能な SKU を読み取ります。 |
> | Action | Microsoft.CognitiveServices/accounts/usages/read | 既存のリソースのクォータ使用率を取得します。 |
> | Action | Microsoft.CognitiveServices/accounts/write | API アカウントを書き込みます。 |
> | Action | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | サブスクリプションで使用可能な SKU を読み取ります。 |
> | Action | Microsoft.CognitiveServices/Operations/read | 使用可能なすべての操作を一覧表示します。 |
> | Action | Microsoft.CognitiveServices/register/action | Cognitive Services にサブスクリプションを登録します。 |
> | Action | Microsoft.CognitiveServices/skus/read | Cognitive Services で使用可能な SKU を読み取ります。 |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.Commerce/RateCard/read | 特定のサブスクリプションのオファー データ、リソース/測定メタデータ、料金を返します。 |
> | Action | Microsoft.Commerce/UsageAggregates/read | サブスクリプションによる Microsoft Azure の利用状況を取得します。 結果には、特定の時間の範囲における利用状況データ、サブスクリプション、およびリソースに関連する情報の集計が含まれます。 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.Compute/availabilitySets/delete | 可用性セットを削除します。 |
> | Action | Microsoft.Compute/availabilitySets/read | 可用性セットのプロパティを取得します。 |
> | Action | Microsoft.Compute/availabilitySets/vmSizes/read | 可用性セットの仮想マシンを作成または更新する際に使用できるサイズを一覧表示します。 |
> | Action | Microsoft.Compute/availabilitySets/write | 新しい可用性セットを作成するか、既存の可用性セットを更新します。 |
> | Action | Microsoft.Compute/disks/beginGetAccess/action | BLOB へのアクセス用にディスクの SAS URI を取得します。 |
> | Action | Microsoft.Compute/disks/delete | ディスクを削除します。 |
> | Action | Microsoft.Compute/disks/endGetAccess/action | ディスクの SAS URI を取り消します。 |
> | Action | Microsoft.Compute/disks/read | ディスクのプロパティを取得します。 |
> | Action | Microsoft.Compute/disks/write | 新しいディスクを作成するか、既存のディスクを更新します。 |
> | Action | Microsoft.Compute/galleries/delete | ギャラリーを削除します |
> | Action | Microsoft.Compute/galleries/images/delete | ギャラリー イメージを削除します |
> | Action | Microsoft.Compute/galleries/images/read | ギャラリー イメージのプロパティを取得します |
> | Action | Microsoft.Compute/galleries/images/versions/delete | ギャラリー イメージ バージョンを削除します |
> | Action | Microsoft.Compute/galleries/images/versions/read | ギャラリー イメージ バージョンのプロパティを取得します |
> | Action | Microsoft.Compute/galleries/images/versions/write | 新しいギャラリー イメージ バージョンを作成するか、既存のギャラリー イメージ バージョンを更新します |
> | Action | Microsoft.Compute/galleries/images/write | 新しいギャラリー イメージを作成するか、既存のギャラリー イメージを更新します |
> | Action | Microsoft.Compute/galleries/read | ギャラリーのプロパティを取得します |
> | Action | Microsoft.Compute/galleries/write | 新しいギャラリーを作成するか、既存のギャラリーを更新します |
> | Action | Microsoft.Compute/images/delete | イメージを削除します。 |
> | Action | Microsoft.Compute/images/read | イメージのプロパティを取得します。 |
> | Action | Microsoft.Compute/images/write | 新しいイメージを作成するか、既存のイメージを更新します。 |
> | Action | Microsoft.Compute/locations/capsOperations/read | 非同期のキャップ操作の状態を取得します。 |
> | Action | Microsoft.Compute/locations/diskOperations/read | 非同期のディスク操作の状態を取得します。 |
> | Action | Microsoft.Compute/locations/operations/read | 非同期操作の状態を取得します。 |
> | Action | Microsoft.Compute/locations/publishers/artifacttypes/offers/read | プラットフォーム イメージ プランのプロパティを取得します。 |
> | Action | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/read | プラットフォーム イメージ SKU のプロパティを取得します。 |
> | Action | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/versions/read | プラットフォーム イメージ バージョンのプロパティを取得します。 |
> | Action | Microsoft.Compute/locations/publishers/artifacttypes/types/read | VMExtension 型のプロパティを取得します。 |
> | Action | Microsoft.Compute/locations/publishers/artifacttypes/types/versions/read | VMExtension バージョンのプロパティを取得します。 |
> | Action | Microsoft.Compute/locations/publishers/read | パブリッシャーのプロパティを取得します。 |
> | Action | Microsoft.Compute/locations/runCommands/read | 場所で使用可能な実行コマンドを一覧表示します。 |
> | Action | Microsoft.Compute/locations/usages/read | その場所のサブスクリプションのコンピューティング リソースについて、サービスの上限と現在の使用容量を取得します。 |
> | Action | Microsoft.Compute/locations/vmSizes/read | その場所で使用できる仮想マシンのサイズを一覧表示します。 |
> | Action | Microsoft.Compute/operations/read | Microsoft.Compute リソース プロバイダーで使用できる操作を一覧表示します。 |
> | Action | Microsoft.Compute/register/action | Microsoft.Compute リソース プロバイダーにサブスクリプションを登録します。 |
> | Action | Microsoft.Compute/restorePointCollections/delete | 復元ポイント コレクションと、コレクションに含まれている復元ポイントを削除します。 |
> | Action | Microsoft.Compute/restorePointCollections/read | 復元ポイント コレクションのプロパティを取得します。 |
> | Action | Microsoft.Compute/restorePointCollections/restorePoints/delete | 復元ポイントを削除します。 |
> | Action | Microsoft.Compute/restorePointCollections/restorePoints/read | 復元ポイントのプロパティを取得します。 |
> | Action | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | 復元ポイントのプロパティを BLOB SAS URI と共に取得します。 |
> | Action | Microsoft.Compute/restorePointCollections/restorePoints/write | 新しい復元ポイントを作成します。 |
> | Action | Microsoft.Compute/restorePointCollections/write | 新しい復元ポイント コレクションを作成するか、既存の復元ポイント コレクションを更新します。 |
> | Action | Microsoft.Compute/sharedVMImages/delete | SharedVMImage を削除します。 |
> | Action | Microsoft.Compute/sharedVMImages/read | SharedVMImage のプロパティを取得します。 |
> | Action | Microsoft.Compute/sharedVMImages/versions/delete | SharedVMImageVersion を削除します。 |
> | Action | Microsoft.Compute/sharedVMImages/versions/read | SharedVMImageVersion のプロパティを取得します。 |
> | Action | Microsoft.Compute/sharedVMImages/versions/replicate/action | ターゲット リージョンに SharedVMImageVersion をレプリケートします。 |
> | Action | Microsoft.Compute/sharedVMImages/versions/write | 新しい SharedVMImageVersion を作成するか、既存のものを更新します。 |
> | Action | Microsoft.Compute/sharedVMImages/write | 新しい SharedVMImage を作成するか、既存のものを更新します。 |
> | Action | Microsoft.Compute/skus/read | サブスクリプションで使用可能な Microsoft.Compute SKU の一覧を取得します。 |
> | Action | Microsoft.Compute/snapshots/beginGetAccess/action | BLOB アクセス用のスナップショットの SAS URI を取得します。 |
> | Action | Microsoft.Compute/snapshots/delete | スナップショットを削除します。 |
> | Action | Microsoft.Compute/snapshots/endGetAccess/action | スナップショットの SAS URI を取り消します。 |
> | Action | Microsoft.Compute/snapshots/read | スナップショットのプロパティを取得します。 |
> | Action | Microsoft.Compute/snapshots/write | 新しいスナップショットを作成するか、既存のスナップショットを更新します。 |
> | Action | Microsoft.Compute/virtualMachines/capture/action | 仮想ハード ディスクをコピーして仮想マシンをキャプチャし、テンプレートを生成します。このテンプレートを使用して、類似する仮想マシンを作成できます。 |
> | Action | Microsoft.Compute/virtualMachines/convertToManagedDisks/action | 仮想マシンの BLOB ベースのディスクをマネージド ディスクに変換します。 |
> | Action | Microsoft.Compute/virtualMachines/deallocate/action | 仮想マシンを電源オフにし、コンピューティング リソースを解放します。 |
> | Action | Microsoft.Compute/virtualMachines/delete | 仮想マシンを削除します。 |
> | Action | Microsoft.Compute/virtualMachines/extensions/delete | 仮想マシン拡張機能を削除します。 |
> | Action | Microsoft.Compute/virtualMachines/extensions/read | 仮想マシン拡張機能のプロパティを取得します。 |
> | Action | Microsoft.Compute/virtualMachines/extensions/write | 新しい仮想マシン拡張機能を作成するか、既存の仮想マシン拡張機能を更新します。 |
> | Action | Microsoft.Compute/virtualMachines/generalize/action | 仮想マシンの状態を [一般化] に設定し、仮想マシンのキャプチャを準備します。 |
> | Action | Microsoft.Compute/virtualMachines/instanceView/read | 仮想マシンとそのリソースの詳細なランタイムの状態を取得します。 |
> | DataAction | Microsoft.Compute/virtualMachines/login/action | 仮想マシンに通常のユーザーとしてログインします。 |
> | DataAction | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Windows 管理者または Linux のルート ユーザーの権限で仮想マシンにログインします。 |
> | Action | Microsoft.Compute/virtualMachines/performMaintenance/action | VM でメンテナンス操作を実行します。 |
> | Action | Microsoft.Compute/virtualMachines/powerOff/action | 仮想マシンを電源オフにします。 仮想マシンの料金は引き続き課金されます。 |
> | Action | Microsoft.Compute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | 仮想マシンのメトリック定義を読み取ります。 |
> | Action | Microsoft.Compute/virtualMachines/read | 仮想マシンのプロパティを取得します。 |
> | Action | Microsoft.Compute/virtualMachines/redeploy/action | 仮想マシンを再デプロイします。 |
> | Action | Microsoft.Compute/virtualMachines/reimage/action | 差分ディスクを使用する仮想マシンを再イメージ化します。 |
> | Action | Microsoft.Compute/virtualMachines/restart/action | 仮想マシンを再起動します。 |
> | Action | Microsoft.Compute/virtualMachines/runCommand/action | 仮想マシンで定義済みのスクリプトを実行します。 |
> | Action | Microsoft.Compute/virtualMachines/start/action | 仮想マシンを起動します。 |
> | Action | Microsoft.Compute/virtualMachines/vmSizes/read | 仮想マシンを更新する際に使用できるサイズを一覧表示します。 |
> | Action | Microsoft.Compute/virtualMachines/write | 新しい仮想マシンを作成するか、既存の仮想マシンを更新します。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/deallocate/action | 仮想マシン スケール セットのインスタンスのコンピューティング リソースを電源オフにして解放します。  |
> | Action | Microsoft.Compute/virtualMachineScaleSets/delete | 仮想マシン スケール セットを削除します。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/delete/action | 仮想マシン スケール セットのインスタンスを削除します。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/extensions/delete | 仮想マシン スケール セット拡張機能を削除します。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/extensions/read | 仮想マシン スケール セット拡張機能のプロパティを取得します。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/extensions/write | 新しい仮想マシン スケール セット拡張機能を作成するか、既存のものを更新します。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action | サービス ファブリック仮想マシン スケール セットのプラットフォーム更新ドメインを手動で移動して、スタックしている保留中の更新を終了します。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/instanceView/read | 仮想マシン スケール セットのインスタンス ビューを取得します。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/manualUpgrade/action | インスタンスを仮想マシン スケール セットの最新モデルに手動で更新します。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | 仮想マシン スケール セットのすべてのネットワーク インターフェイスのプロパティを取得します。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | 仮想マシン スケール セットの OS アップグレードの履歴を取得します。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/performMaintenance/action | 仮想マシン スケール セットのインスタンスで計画的なメンテナンスを実行します。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/powerOff/action | 仮想マシン スケール セットのインスタンスを電源オフにします。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/providers/Microsoft.Insights/metricDefinitions/read | 仮想マシン スケール セットのメトリック定義を読み取ります。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | 仮想マシン スケール セットのすべてのパブリック IP アドレスのプロパティを取得します。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/read | 仮想マシン スケール セットのプロパティを取得します。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/redeploy/action | 仮想マシン スケール セットのインスタンスを再デプロイします。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/reimage/action | 仮想マシン スケール セットのインスタンスを再イメージ化します。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/reimageAll/action | 仮想マシン スケール セットのインスタンスのすべてのディスク (OS ディスクとデータ ディスク) を再イメージ化します  |
> | Action | Microsoft.Compute/virtualMachineScaleSets/restart/action | 仮想マシン スケール セットのインスタンスを再起動します。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/cancel/action | 仮想マシン スケール セットのローリング アップグレードを取り消します。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | 仮想マシン スケール セットの最新のローリング アップグレードの状態を取得します。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/scale/action | 既存の仮想マシン スケール セットを、指定されたインスタンス数にスケールイン/スケールアウトできるかどうかを確認します。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/skus/read | 既存の仮想マシン スケール セットの有効な SKU を一覧表示します。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/start/action | 仮想マシン スケール セットのインスタンスを起動します。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/deallocate/action | VM スケール セット内の仮想マシンのコンピューティング リソースを電源オフにして解放します。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/delete | VM スケール セット内の特定の仮想マシンを削除します。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/instanceView/read | VM スケール セット内の仮想マシンのインスタンス ビューを取得します。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | 仮想マシン スケール セットを使って作成されたパブリック IP アドレスのプロパティを取得します。 仮想マシン スケール セットは、ipconfiguration (プライベート IP) あたり最大で 1 つのパブリック IP を作成できます。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | 仮想マシン スケール セットを使って作成されたネットワーク インターフェイスの 1 つまたはすべての IP 構成のプロパティを取得します。 IP 構成は、プライベート IP を表します。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | 仮想マシン スケール セットを使って作成された仮想マシンの 1 つまたはすべてのネットワーク インターフェイスのプロパティを取得します。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/performMaintenance/action | 仮想マシン スケール セットの仮想マシン インスタンスで計画的なメンテナンスを実行します。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/powerOff/action | VM スケール セット内の仮想マシン インスタンスを電源オフにします。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | スケール セットの仮想マシンのメトリック定義を読み取ります。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read | VM スケール セット内の仮想マシンのプロパティを取得します。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/redeploy/action | 仮想マシン スケール セット内の仮想マシン インスタンスを再デプロイします。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimage/action | 仮想マシン スケール セット内の仮想マシン インスタンスを再イメージ化します。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimageAll/action | 仮想マシン スケール セット内の仮想マシン インスタンスのすべてのディスク (OS ディスクとデータ ディスク) を再イメージ化します。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/restart/action | VM スケール セット内の仮想マシン インスタンスを再起動します。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/runCommand/action | 仮想マシン スケール セット内の仮想マシン インスタンスで、定義済みのスクリプトを実行します。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/start/action | VM スケール セット内の仮想マシン インスタンスを起動します。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/write | VM スケール セット内の仮想マシンのプロパティを更新します。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/write | 新しい仮想マシン スケール セットを作成するか、既存のものを更新します。 |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.Consumption/balances/read | 管理グループの請求期間の使用状況の概要を一覧表示します。 |
> | Action | Microsoft.Consumption/budgets/read | サブスクリプションまたは管理グループごとに予算を一覧表示します。 |
> | Action | Microsoft.Consumption/budgets/write | サブスクリプションまたは管理グループで予算を作成、更新、削除します。 |
> | Action | Microsoft.Consumption/marketplaces/read | EA および WebDirect サブスクリプションのスコープに対する Marketplace リソース使用状況の詳細を一覧表示します。 |
> | Action | Microsoft.Consumption/operations/read | Microsoft.Consumption リソース プロバイダーによってサポートされるすべての操作を一覧表示します。 |
> | Action | Microsoft.Consumption/pricesheets/read | サブスクリプションまたは管理グループの価格シート データを一覧表示します。 |
> | Action | Microsoft.Consumption/reservationDetails/read | 予約注文または管理グループごとに予約インスタンスの使用状況の詳細を一覧表示します。 詳細データは、インスタンス別の日レベル別です。 |
> | Action | Microsoft.Consumption/reservationRecommendations/read | サブスクリプションの予約インスタンスに関する 1 つのレコメンデーションまたは共有のレコメンデーションの一覧を取得します。 |
> | Action | Microsoft.Consumption/reservationSummaries/read | 予約注文または管理グループごとに予約インスタンスの使用状況の概要を一覧表示します。 概要データは、月レベル別または日レベル別です。 |
> | Action | Microsoft.Consumption/reservationTransactions/read | 管理グループ別に予約インスタンスのトランザクション履歴を一覧表示します。 |
> | Action | Microsoft.Consumption/tenants/register/action | Microsoft.Consumption のスコープのアクションをテナントごとに登録します。 |
> | Action | Microsoft.Consumption/terms/read | サブスクリプションまたは管理グループの条件を一覧表示します。 |
> | Action | Microsoft.Consumption/usageDetails/read | EA および WebDirect サブスクリプションのスコープに対する使用状況の詳細を一覧表示します。 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.ContainerInstance/containerGroups/containers/logs/read | 特定のコンテナーのログを取得します。 |
> | Action | Microsoft.ContainerInstance/containerGroups/delete | 特定のコンテナー グループを削除します。 |
> | Action | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read | コンテナー グループの診断設定を取得します。 |
> | Action | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write | コンテナー グループの診断設定を作成または更新します。 |
> | Action | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/metricDefinitions/read | コンテナー グループで使用可能なメトリックを取得します。 |
> | Action | Microsoft.ContainerInstance/containerGroups/read | すべてのコンテナー グループを取得します。 |
> | Action | Microsoft.ContainerInstance/containerGroups/restart/action | 特定のコンテナー グループを再起動します。 |
> | Action | Microsoft.ContainerInstance/containerGroups/stop/action | 特定のコンテナー グループを停止します。 コンピューティング リソースの割り当てが解除され、課金が停止されます。 |
> | Action | Microsoft.ContainerInstance/containerGroups/write | 特定のコンテナー グループを作成または更新します。 |
> | Action | Microsoft.ContainerInstance/register/action | コンテナー インスタンス リソース プロバイダーのサブスクリプションを登録し、コンテナー グループの作成を有効にします。 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.ContainerRegistry/checkNameAvailability/read | コンテナー レジストリ名が使用可能かどうかを確認します。 |
> | Action | Microsoft.ContainerRegistry/locations/deleteVirtualNetworkOrSubnets/action | 仮想ネットワークまたはサブネットを削除していることを Microsoft.ContainerRegistry に通知します。 |
> | Action | Microsoft.ContainerRegistry/locations/operationResults/read | 非同期操作の結果を取得します。 |
> | Action | Microsoft.ContainerRegistry/operations/read | 使用可能なすべての Azure Container Registry REST API 操作を一覧表示します。 |
> | Action | Microsoft.ContainerRegistry/register/action | コンテナー レジストリ リソース プロバイダーにサブスクリプションを登録し、コンテナー レジストリを作成できるようにします。 |
> | Action | Microsoft.ContainerRegistry/registries/delete | コンテナー レジストリを削除します。 |
> | Action | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | コンテナー レジストリからイベント グリッド フィルターを削除します。 |
> | Action | Microsoft.ContainerRegistry/registries/eventGridFilters/read | 指定されたイベント グリッド フィルターのプロパティを取得するか、指定されたコンテナー レジストリのすべてのイベント グリッド フィルターを一覧表示します。 |
> | Action | Microsoft.ContainerRegistry/registries/eventGridFilters/write | 指定されたパラメーターでコンテナー レジストリのイベント グリッド フィルターを作成または更新します。 |
> | Action | Microsoft.ContainerRegistry/registries/importImage/action | 指定されたパラメーターを使用して、コンテナー レジストリにイメージをインポートします。 |
> | Action | Microsoft.ContainerRegistry/registries/listCredentials/action | 指定されたコンテナー レジストリのログイン資格情報を一覧表示します。 |
> | Action | Microsoft.ContainerRegistry/registries/listPolicies/read | 指定されたコンテナー レジストリのポリシーを一覧表示します。 |
> | Action | Microsoft.ContainerRegistry/registries/listUsages/read | 指定されたコンテナー レジストリのクォータ使用状況を一覧表示します。 |
> | Action | Microsoft.ContainerRegistry/registries/operationStatuses/read | レジストリの非同期操作の状態を取得します。 |
> | Action | Microsoft.ContainerRegistry/registries/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Action | Microsoft.ContainerRegistry/registries/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Action | Microsoft.ContainerRegistry/registries/providers/Microsoft.Insights/metricDefinitions/read | Microsoft ContainerRegistry の使用可能なメトリックを取得します。 |
> | Action | Microsoft.ContainerRegistry/registries/read | 指定されたコンテナー レジストリのプロパティを取得するか、指定されたリソース グループまたはサブスクリプションのすべてのコンテナー レジストリを一覧表示します。 |
> | Action | Microsoft.ContainerRegistry/registries/regenerateCredential/action | 指定されたコンテナー レジストリのログイン資格情報の 1 つを再生成します。 |
> | Action | Microsoft.ContainerRegistry/registries/replications/delete | コンテナー レジストリからレプリケーションを削除します。 |
> | Action | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | レプリケーションの非同期操作の状態を取得します。 |
> | Action | Microsoft.ContainerRegistry/registries/replications/read | 指定されたレプリケーションのプロパティを取得するか、指定されたコンテナー レジストリのすべてのレプリケーションを一覧表示します。 |
> | Action | Microsoft.ContainerRegistry/registries/replications/write | 指定されたパラメーターでコンテナー レジストリのレプリケーションを作成または更新します。 |
> | Action | Microsoft.ContainerRegistry/registries/updatePolicies/write | 指定されたコンテナー レジストリのポリシーを更新します。 |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/delete | コンテナー レジストリから webhook を削除します。 |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/getCallbackConfig/action | webhook のサービス URI とカスタム ヘッダーの構成を取得します。 |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/listEvents/action | 指定された webhook の最近のイベントを一覧表示します。 |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | webhook の非同期操作の状態を取得します。 |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/ping/action | webhook に送信される ping イベントをトリガーします。 |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/read | 指定された webhook のプロパティを取得するか、指定されたコンテナー レジストリのすべての webhook を一覧表示します。 |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/write | 指定されたパラメーターでコンテナー レジストリの webhook を作成または更新します。 |
> | Action | Microsoft.ContainerRegistry/registries/write | 指定されたパラメーターでコンテナー レジストリを作成または更新します。 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.ContainerService/containerServices/delete | コンテナー サービスを削除します。 |
> | Action | Microsoft.ContainerService/containerServices/read | コンテナー サービスを取得します。 |
> | Action | Microsoft.ContainerService/containerServices/write | 新しいコンテナー サービスを作成するか、既存のものを更新します。 |
> | Action | Microsoft.ContainerService/locations/operationresults/read | 非同期操作結果の状態を取得します。 |
> | Action | Microsoft.ContainerService/locations/operations/read | 非同期操作の状態を取得します。 |
> | Action | Microsoft.ContainerService/locations/orchestrators/read | サポート対象のオーケストレーターを一覧表示します。 |
> | Action | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | 資格情報の一覧の取得を使用し、ロール名を指定してマネージド クラスターのアクセス プロファイルを取得します。 |
> | Action | Microsoft.ContainerService/managedClusters/accessProfiles/read | ロール名を指定してマネージド クラスターのアクセス プロファイルを取得します。 |
> | Action | Microsoft.ContainerService/managedClusters/delete | マネージド クラスターを削除します。 |
> | Action | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | 管理対象クラスターの clusterAdmin 資格情報を一覧表示します。 |
> | Action | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | 管理対象クラスターの clusterUser 資格情報を一覧表示します。 |
> | Action | Microsoft.ContainerService/managedClusters/providers/Microsoft.Insights/diagnosticSettings/read | マネージド クラスターのリソースの診断設定を取得します。 |
> | Action | Microsoft.ContainerService/managedClusters/providers/Microsoft.Insights/diagnosticSettings/write | マネージド クラスターのリソースの診断設定を作成または更新します。 |
> | Action | Microsoft.ContainerService/managedClusters/providers/Microsoft.Insights/logDefinitions/read | マネージド クラスターの使用可能なログを取得します。 |
> | Action | Microsoft.ContainerService/managedClusters/providers/Microsoft.Insights/metricDefinitions/read | マネージド クラスターの使用可能なメトリックを取得します。 |
> | Action | Microsoft.ContainerService/managedClusters/read | マネージド クラスターを取得します。 |
> | Action | Microsoft.ContainerService/managedClusters/upgradeprofiles/read | クラスターのアップグレード プロファイルを取得します。 |
> | Action | Microsoft.ContainerService/managedClusters/write | 新しいマネージド クラスターを作成するか、既存のものを更新します。 |
> | Action | Microsoft.ContainerService/operations/read | Microsoft.ContainerService リソース プロバイダーで使用できる操作を一覧表示します。 |
> | Action | Microsoft.ContainerService/register/action | Microsoft.ContainerService リソース プロバイダーにサブスクリプションを登録します。 |
> | Action | Microsoft.ContainerService/unregister/action | Microsoft.ContainerService リソース プロバイダーからサブスクリプションを登録解除します。 |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.ContentModerator/applications/delete | 削除操作。 |
> | Action | Microsoft.ContentModerator/applications/listSecrets/action | シークレットを一覧表示します。 |
> | Action | Microsoft.ContentModerator/applications/listSingleSignOnToken/action | シングル サインオン トークンを読み取ります。 |
> | Action | Microsoft.ContentModerator/applications/read | 読み取り操作。 |
> | Action | Microsoft.ContentModerator/applications/write | 書き込み操作。 |
> | Action | Microsoft.ContentModerator/applications/write | 書き込み操作。 |
> | Action | Microsoft.ContentModerator/listCommunicationPreference/action | 通信設定を一覧表示します。 |
> | Action | Microsoft.ContentModerator/operations/read | 操作を読み取ります。 |
> | Action | Microsoft.ContentModerator/updateCommunicationPreference/action | 通信設定を更新します。 |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.CustomerInsights/hubs/adobemetadata/action | Azure Customer Insights の Adobe メタデータを作成または更新します。 |
> | Action | Microsoft.CustomerInsights/hubs/adobemetadata/read | Azure Customer Insights の Adobe メタデータを読み取ります。 |
> | Action | Microsoft.CustomerInsights/hubs/authorizationPolicies/delete | Azure Customer Insights の Shared Access Signature ポリシーを削除します。 |
> | Action | Microsoft.CustomerInsights/hubs/authorizationPolicies/read | Azure Customer Insights の Shared Access Signature ポリシーを読み取ります。 |
> | Action | Microsoft.CustomerInsights/hubs/authorizationPolicies/regeneratePrimaryKey/action | Azure Customer Insights の Shared Access Signature ポリシーのプライマリ キーを再生成します。 |
> | Action | Microsoft.CustomerInsights/hubs/authorizationPolicies/regenerateSecondaryKey/action | Azure Customer Insights の Shared Access Signature ポリシーのセカンダリ キーを再生成します。 |
> | Action | Microsoft.CustomerInsights/hubs/authorizationPolicies/write | Azure Customer Insights の Shared Access Signature ポリシーを作成または更新します。 |
> | Action | Microsoft.CustomerInsights/hubs/connectors/activate/action | Azure Customer Insights コネクタをアクティブにします。 |
> | Action | Microsoft.CustomerInsights/hubs/connectors/activate/action | Azure Customer Insights コネクタをアクティブにします。 |
> | Action | Microsoft.CustomerInsights/hubs/connectors/delete | Azure Customer Insights コネクタを削除します。 |
> | Action | Microsoft.CustomerInsights/hubs/connectors/getruntimestatus/action | Azure Customer Insights コネクタのランタイムの状態を取得します。 |
> | Action | Microsoft.CustomerInsights/hubs/connectors/mappings/activate/action | Azure Customer Insights コネクタ マッピングをアクティブにします。 |
> | Action | Microsoft.CustomerInsights/hubs/connectors/mappings/delete | Azure Customer Insights コネクタ マッピングを削除します。 |
> | Action | Microsoft.CustomerInsights/hubs/connectors/mappings/operations/read | Azure Customer Insights コネクタ マッピングの操作結果を読み取ります。 |
> | Action | Microsoft.CustomerInsights/hubs/connectors/mappings/read | Azure Customer Insights コネクタ マッピングを読み取ります。 |
> | Action | Microsoft.CustomerInsights/hubs/connectors/mappings/write | Azure Customer Insights コネクタ マッピングを作成または更新します。 |
> | Action | Microsoft.CustomerInsights/hubs/connectors/operations/read | Azure Customer Insights コネクタの操作結果を読み取ります。 |
> | Action | Microsoft.CustomerInsights/hubs/connectors/read | Azure Customer Insights コネクタを読み取ります。 |
> | Action | Microsoft.CustomerInsights/hubs/connectors/saveauthinfo/action | Azure Customer Insights コネクタの認証情報を作成または更新します。 |
> | Action | Microsoft.CustomerInsights/hubs/connectors/update/action | Azure Customer Insights コネクタを更新します。 |
> | Action | Microsoft.CustomerInsights/hubs/connectors/write | Azure Customer Insights コネクタを作成または更新します。 |
> | Action | Microsoft.CustomerInsights/hubs/crmmetadata/action | Azure Customer Insights の Crm メタデータを作成または更新します。 |
> | Action | Microsoft.CustomerInsights/hubs/crmmetadata/read | Azure Customer Insights の Crm メタデータを読み取ります。 |
> | Action | Microsoft.CustomerInsights/hubs/delete | Azure Customer Insights Hub を削除します。 |
> | Action | Microsoft.CustomerInsights/hubs/gdpr/delete | Azure Customer Insights Gdpr を削除します。 |
> | Action | Microsoft.CustomerInsights/hubs/gdpr/read | Azure Customer Insights Gdpr を読み取ります。 |
> | Action | Microsoft.CustomerInsights/hubs/gdpr/write | Azure Customer Insights Gdpr を作成または更新します。 |
> | Action | Microsoft.CustomerInsights/hubs/getbillingcredits/read | Azure Customer Insights Hub の請求先クレジットを取得します。 |
> | Action | Microsoft.CustomerInsights/hubs/getbillinghistory/read | Azure Customer Insights Hub の請求履歴を取得します。 |
> | Action | Microsoft.CustomerInsights/hubs/images/delete | Azure Customer Insights イメージを削除します。 |
> | Action | Microsoft.CustomerInsights/hubs/images/read | Azure Customer Insights イメージを読み取ります。 |
> | Action | Microsoft.CustomerInsights/hubs/images/write | Azure Customer Insights イメージを作成または更新します。 |
> | Action | Microsoft.CustomerInsights/hubs/interactions/delete | Azure Customer Insights の対話を削除します。 |
> | Action | Microsoft.CustomerInsights/hubs/interactions/operations/read | Azure Customer Insights の対話の操作結果を読み取ります。 |
> | Action | Microsoft.CustomerInsights/hubs/interactions/read | Azure Customer Insights の対話を読み取ります。 |
> | Action | Microsoft.CustomerInsights/hubs/interactions/suggestrelationshiplinks/action | Azure Customer Insights の対話に対するリレーションシップ リンクを提案します。 |
> | Action | Microsoft.CustomerInsights/hubs/interactions/write | Azure Customer Insights の対話を作成または更新します。 |
> | Action | Microsoft.CustomerInsights/hubs/kpi/delete | Azure Customer Insights の主要業績評価指標を削除します。 |
> | Action | Microsoft.CustomerInsights/hubs/kpi/operations/read | Azure Customer Insights の主要業績評価指標の操作結果を読み取ります。 |
> | Action | Microsoft.CustomerInsights/hubs/kpi/read | Azure Customer Insights の主要業績評価指標を読み取ります。 |
> | Action | Microsoft.CustomerInsights/hubs/kpi/reprocess/action | Azure Customer Insights の主要業績評価指標を再処理します。 |
> | Action | Microsoft.CustomerInsights/hubs/kpi/write | Azure Customer Insights の主要業績評価指標を作成または更新します。 |
> | Action | Microsoft.CustomerInsights/hubs/links/delete | Azure Customer Insights のリンクを削除します。 |
> | Action | Microsoft.CustomerInsights/hubs/links/operations/read | Azure Customer Insights のリンクの操作結果を読み取ります。 |
> | Action | Microsoft.CustomerInsights/hubs/links/read | Azure Customer Insights のリンクを読み取ります。 |
> | Action | Microsoft.CustomerInsights/hubs/links/write | Azure Customer Insights のリンクを作成または更新します。 |
> | Action | Microsoft.CustomerInsights/hubs/msemetadata/action | Azure Customer Insights の Mse メタデータを作成または更新します。 |
> | Action | Microsoft.CustomerInsights/hubs/msemetadata/read | Azure Customer Insights の Mse メタデータを読み取ります。 |
> | Action | Microsoft.CustomerInsights/hubs/operationresults/read | Azure Customer Insights Hub の操作結果を取得します。 |
> | Action | Microsoft.CustomerInsights/hubs/predictions/delete | Azure Customer Insights の予測を削除します。 |
> | Action | Microsoft.CustomerInsights/hubs/predictions/operations/read | Azure Customer Insights の予測の操作結果を読み取ります。 |
> | Action | Microsoft.CustomerInsights/hubs/predictions/read | Azure Customer Insights の予測を読み取ります。 |
> | Action | Microsoft.CustomerInsights/hubs/predictions/write | Azure Customer Insights の予測を作成または更新します。 |
> | Action | Microsoft.CustomerInsights/hubs/predictivematchpolicies/delete | Azure Customer Insights の予測一致ポリシーを削除します。 |
> | Action | Microsoft.CustomerInsights/hubs/predictivematchpolicies/operations/read | Azure Customer Insights の予測一致ポリシーの操作結果を読み取ります。 |
> | Action | Microsoft.CustomerInsights/hubs/predictivematchpolicies/read | Azure Customer Insights の予測一致ポリシーを読み取ります。 |
> | Action | Microsoft.CustomerInsights/hubs/predictivematchpolicies/write | Azure Customer Insights の予測一致ポリシーを作成または更新します。 |
> | Action | Microsoft.CustomerInsights/hubs/profiles/delete | Azure Customer Insights プロファイルを削除します。 |
> | Action | Microsoft.CustomerInsights/hubs/profiles/operations/read | Azure Customer Insights プロファイルの操作結果を読み取ります。 |
> | Action | Microsoft.CustomerInsights/hubs/profiles/read | Azure Customer Insights プロファイルを読み取ります。 |
> | Action | Microsoft.CustomerInsights/hubs/profiles/write | Azure Customer Insights プロファイルを書き込みます。 |
> | Action | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Action | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Action | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/logDefinitions/read | リソースの利用可能なログを取得します。 |
> | Action | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/metricDefinitions/read | リソースの利用可能なメトリックを取得します。 |
> | Action | Microsoft.CustomerInsights/hubs/read | Azure Customer Insights Hub を読み取ります。 |
> | Action | Microsoft.CustomerInsights/hubs/relationshiplinks/delete | Azure Customer Insights のリレーションシップ リンクを削除します。 |
> | Action | Microsoft.CustomerInsights/hubs/relationshiplinks/operations/read | Azure Customer Insights のリレーションシップ リンクの操作結果を読み取ります。 |
> | Action | Microsoft.CustomerInsights/hubs/relationshiplinks/read | Azure Customer Insights のリレーションシップ リンクを読み取ります。 |
> | Action | Microsoft.CustomerInsights/hubs/relationshiplinks/write | Azure Customer Insights のリレーションシップ リンクを作成または更新します。 |
> | Action | Microsoft.CustomerInsights/hubs/relationships/delete | Azure Customer Insights のリレーションシップを削除します。 |
> | Action | Microsoft.CustomerInsights/hubs/relationships/operations/read | Azure Customer Insights のリレーションシップの操作結果を読み取ります。 |
> | Action | Microsoft.CustomerInsights/hubs/relationships/read | Azure Customer Insights のリレーションシップを読み取ります。 |
> | Action | Microsoft.CustomerInsights/hubs/relationships/write | Azure Customer Insights のリレーションシップを作成または更新します。 |
> | Action | Microsoft.CustomerInsights/hubs/roleAssignments/delete | Azure Customer Insights の RBAC の割り当てを削除します。 |
> | Action | Microsoft.CustomerInsights/hubs/roleAssignments/operations/read | Azure Customer Insights の RBAC 割り当ての操作結果を読み取ります。 |
> | Action | Microsoft.CustomerInsights/hubs/roleAssignments/read | Azure Customer Insights の RBAC の割り当てを読み取ります。 |
> | Action | Microsoft.CustomerInsights/hubs/roleAssignments/write | Azure Customer Insights の RBAC の割り当てを作成または更新します。 |
> | Action | Microsoft.CustomerInsights/hubs/roles/read | Azure Customer Insights の RBAC ロールを読み取ります。 |
> | Action | Microsoft.CustomerInsights/hubs/salesforcemetadata/action | Azure Customer Insights の SalesForce メタデータを作成または更新します。 |
> | Action | Microsoft.CustomerInsights/hubs/salesforcemetadata/read | Azure Customer Insights の SalesForce メタデータを読み取ります。 |
> | Action | Microsoft.CustomerInsights/hubs/segments/delete | Azure Customer Insights のセグメントを削除します。 |
> | Action | Microsoft.CustomerInsights/hubs/segments/dynamic/action | Azure Customer Insights の動的セグメントを管理します。 |
> | Action | Microsoft.CustomerInsights/hubs/segments/read | Azure Customer Insights のセグメントを読み取ります。 |
> | Action | Microsoft.CustomerInsights/hubs/segments/static/action | Azure Customer Insights の静的セグメントを管理します。 |
> | Action | Microsoft.CustomerInsights/hubs/segments/write | Azure Customer Insights のセグメントを作成または更新します。 |
> | Action | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/delete | Azure Customer Insights の SqlConnectionStrings を削除します。 |
> | Action | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/read | Azure Customer Insights の SqlConnectionStrings を読み取ります。 |
> | Action | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/write | Azure Customer Insights の SqlConnectionStrings を作成または更新します。 |
> | Action | Microsoft.CustomerInsights/hubs/suggesttypeschema/action | サンプル データから提案タイプ スキーマを生成します。 |
> | Action | Microsoft.CustomerInsights/hubs/tenantmanagement/read | Azure Customer Insights Hub の設定を管理します。 |
> | Action | Microsoft.CustomerInsights/hubs/views/delete | Azure Customer Insights のアプリ ビューを削除します。 |
> | Action | Microsoft.CustomerInsights/hubs/views/read | Azure Customer Insights のアプリ ビューを読み取ります。 |
> | Action | Microsoft.CustomerInsights/hubs/views/write | Azure Customer Insights のアプリ ビューを作成または更新します。 |
> | Action | Microsoft.CustomerInsights/hubs/widgettypes/read | Azure Customer Insights のアプリ ウィジェット タイプを読み取ります。 |
> | Action | Microsoft.CustomerInsights/hubs/write | Azure Customer Insights Hub を作成または更新します。 |
> | Action | Microsoft.CustomerInsights/operations/read | Azure Customer Insights の API メタデータを読み取ります。 |
> | Action | Microsoft.CustomerInsights/register/action | Customer Insights リソース プロバイダーにサブスクリプションを登録し、Customer Insights リソースを作成できるようにします。 |
> | Action | Microsoft.CustomerInsights/unregister/action | Customer Insights リソース プロバイダーにサブスクリプションの登録を解除します。 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.DataBox/jobs/bookShipmentPickUp/action | 返品の集配を予約できます。 |
> | Action | Microsoft.DataBox/jobs/cancel/action | 処理中の注文を取り消します。 |
> | Action | Microsoft.DataBox/jobs/copyLogsUri/action | 宛先アカウントごとにコピー ログ用 URL を取得します。 |
> | Action | Microsoft.DataBox/jobs/delete | 注文を削除します。 |
> | Action | Microsoft.DataBox/jobs/downloadShippingLabel/action | 返品用の出荷ラベルを取得します。 |
> | Action | Microsoft.DataBox/jobs/listSecrets/action | 注文に関連する暗号化されていないシークレットを一覧表示します。 |
> | Action | Microsoft.DataBox/jobs/read | 注文を一覧表示または取得します。 |
> | Action | Microsoft.DataBox/jobs/reportIssue/action | 注文の問題を報告します。 |
> | Action | Microsoft.DataBox/jobs/write | 注文を作成または更新します。 |
> | Action | Microsoft.DataBox/locations/availableSkus/action | このメソッドは、使用可能な SKU の一覧を返します。 |
> | Action | Microsoft.DataBox/locations/validateAddress/action | 配送先住所を検証し、存在する場合には別の住所を指定します。 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.Databricks/workspaces/delete | ワークスペースを削除します。 |
> | Action | Microsoft.Databricks/workspaces/read | ワークスペースの一覧を取得します。 |
> | Action | Microsoft.Databricks/workspaces/write | ワークスペースを作成します。 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.DataCatalog/catalogs/delete | カタログを削除します。 |
> | Action | Microsoft.DataCatalog/catalogs/read | サブスクリプションまたはリソース グループでカタログのプロパティを取得します。 |
> | Action | Microsoft.DataCatalog/catalogs/write | カタログを作成するか、カタログのタグとプロパティを更新します。 |
> | Action | Microsoft.DataCatalog/checkNameAvailability/action | テナントのカタログ名を使用できるかどうかを確認します。 |
> | Action | Microsoft.DataCatalog/operations/read | Microsoft.DataCatalog リソース プロバイダーで使用できる操作を一覧表示します。 |
> | Action | Microsoft.DataCatalog/register/action | Microsoft.DataCatalog リソース プロバイダーにサブスクリプションを登録します。 |
> | Action | Microsoft.DataCatalog/unregister/action | Microsoft.DataCatalog リソース プロバイダーにサブスクリプションを登録解除します。 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.DataFactory/datafactories/activitywindows/read | 指定されたパラメーターを使用して、データ ファクトリのアクティビティ ウィンドウを読み取ります。 |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/activities/activitywindows/read | 指定されたパラメーターを使用して、パイプライン アクティビティのアクティビティ ウィンドウを読み取ります。 |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/activitywindows/read | 指定されたパラメーターを使用して、パイプラインのアクティビティ ウィンドウを読み取ります。 |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/delete | パイプラインを削除します。 |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/pause/action | パイプラインを一時停止します。 |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/read | パイプラインを読み取ります |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/resume/action | パイプラインを再開します。 |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/update/action | パイプラインを更新します。 |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/write | パイプラインを作成または更新します。 |
> | Action | Microsoft.DataFactory/datafactories/datasets/activitywindows/read | 指定されたパラメーターを使用して、データセットのアクティビティ ウィンドウを読み取ります。 |
> | Action | Microsoft.DataFactory/datafactories/datasets/delete | データセットを削除します。 |
> | Action | Microsoft.DataFactory/datafactories/datasets/read | データセットを読み取ります。 |
> | Action | Microsoft.DataFactory/datafactories/datasets/sliceruns/read | 指定された開始時刻の指定されたデータ セットについて、データ スライスの実行を読み取ります。 |
> | Action | Microsoft.DataFactory/datafactories/datasets/slices/read | 指定された期間内のデータ スライスを取得します。 |
> | Action | Microsoft.DataFactory/datafactories/datasets/slices/write | データ スライスの状態を更新します。 |
> | Action | Microsoft.DataFactory/datafactories/datasets/write | データセットを作成または更新します。 |
> | Action | Microsoft.DataFactory/datafactories/delete | データ ファクトリを削除します。 |
> | Action | Microsoft.DataFactory/datafactories/gateways/connectioninfo/action | ゲートウェイの接続情報を読み取ります。 |
> | Action | Microsoft.DataFactory/datafactories/gateways/delete | ゲートウェイを削除します。 |
> | Action | Microsoft.DataFactory/datafactories/gateways/listauthkeys/action | ゲートウェイの認証キーの一覧を取得します。 |
> | Action | Microsoft.DataFactory/datafactories/gateways/read | ゲートウェイを読み取ります。 |
> | Action | Microsoft.DataFactory/datafactories/gateways/regenerateauthkey/action | ゲートウェイの認証キーを再生成します。 |
> | Action | Microsoft.DataFactory/datafactories/gateways/write | ゲートウェイを作成または更新します。 |
> | Action | Microsoft.DataFactory/datafactories/linkedServices/delete | リンクされたサービスを削除します。 |
> | Action | Microsoft.DataFactory/datafactories/linkedServices/read | リンクされたサービスを読み取ります。 |
> | Action | Microsoft.DataFactory/datafactories/linkedServices/write | リンクされたサービスを作成または更新します。 |
> | Action | Microsoft.DataFactory/datafactories/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Action | Microsoft.DataFactory/datafactories/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Action | Microsoft.DataFactory/datafactories/providers/Microsoft.Insights/metricDefinitions/read | datafactories で使用可能なメトリックを取得します。 |
> | Action | Microsoft.DataFactory/datafactories/read | データ ファクトリを読み取ります。 |
> | Action | Microsoft.DataFactory/datafactories/runs/loginfo/read | ログを含む BLOB コンテナーの SAS URI を読み取ります。 |
> | Action | Microsoft.DataFactory/datafactories/tables/delete | データセットを削除します。 |
> | Action | Microsoft.DataFactory/datafactories/tables/read | データセットを読み取ります。 |
> | Action | Microsoft.DataFactory/datafactories/tables/write | データセットを作成または更新します。 |
> | Action | Microsoft.DataFactory/datafactories/write | データ ファクトリを作成または更新します。 |
> | Action | Microsoft.DataFactory/factories/cancelpipelinerun/action | 実行 ID に指定されたパイプラインの実行を取り消します。 |
> | Action | Microsoft.DataFactory/factories/datasets/delete | データセットを削除します。 |
> | Action | Microsoft.DataFactory/factories/datasets/read | データセットを読み取ります。 |
> | Action | Microsoft.DataFactory/factories/datasets/write | データセットを作成または更新します。 |
> | Action | Microsoft.DataFactory/factories/delete | データ ファクトリを削除します。 |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/delete | 統合ランタイムを削除します。 |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/getconnectioninfo/read | 統合ランタイムの接続情報を読み取ります。 |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/getstatus/read | 統合ランタイムの状態を読み取ります。 |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/read | 統合ランタイムの認証キーの一覧を取得します。 |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/monitoringdata/read | 統合ランタイムの監視データを取得します。 |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/nodes/delete | 指定された統合ランタイムのノードを削除します。 |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | 統合ランタイムの指定されたノードの IP アドレスを返します。 |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/nodes/write | 自己ホスト型統合ランタイム ノードを更新します。 |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/read | 統合ランタイムを読み取ります。 |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | 指定された統合ランタイムの認証キーを再生成します。 |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/start/action | 統合ランタイムを開始します。 |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/stop/action | 統合ランタイムを停止します。 |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/synccredentials/action | 指定された統合ランタイムの資格情報を同期します。 |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/upgrade/action | 指定された統合ランタイムをアップグレードします。 |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/write | 統合ランタイムを作成または更新します。 |
> | Action | Microsoft.DataFactory/factories/linkedServices/delete | リンクされたサービスを削除します。 |
> | Action | Microsoft.DataFactory/factories/linkedServices/read | リンクされたサービスを読み取ります。 |
> | Action | Microsoft.DataFactory/factories/linkedServices/write | リンクされたサービスを作成または更新します。 |
> | Action | Microsoft.DataFactory/factories/pipelineruns/activityruns/read | 指定されたパイプライン実行 ID のアクティビティの実行を読み取ります。 |
> | Action | Microsoft.DataFactory/factories/pipelineruns/cancel/action | 実行 ID に指定されたパイプラインの実行を取り消します。 |
> | Action | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/action | 指定されたパイプライン実行 ID のアクティビティの実行を照会します。 |
> | Action | Microsoft.DataFactory/factories/pipelineruns/read | パイプライン実行を読み取ります。 |
> | Action | Microsoft.DataFactory/factories/pipelines/createrun/action | パイプラインの実行を作成します。 |
> | Action | Microsoft.DataFactory/factories/pipelines/delete | パイプラインを削除します。 |
> | Action | Microsoft.DataFactory/factories/pipelines/pipelineruns/read | パイプライン実行を読み取ります。 |
> | Action | Microsoft.DataFactory/factories/pipelines/read | パイプラインを読み取ります。 |
> | Action | Microsoft.DataFactory/factories/pipelines/write | パイプラインを作成または更新します。 |
> | Action | Microsoft.DataFactory/factories/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Action | Microsoft.DataFactory/factories/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Action | Microsoft.DataFactory/factories/providers/Microsoft.Insights/logDefinitions/read | ファクトリで使用可能なログを取得します。 |
> | Action | Microsoft.DataFactory/factories/providers/Microsoft.Insights/metricDefinitions/read | ファクトリで使用可能なメトリックを取得します。 |
> | Action | Microsoft.DataFactory/factories/querypipelineruns/action | パイプライン実行を照会します。 |
> | Action | Microsoft.DataFactory/factories/querytriggerruns/action | トリガーの実行を照会します。 |
> | Action | Microsoft.DataFactory/factories/read | データ ファクトリを読み取ります。 |
> | Action | Microsoft.DataFactory/factories/triggerruns/read | トリガーの実行を読み取ります。 |
> | Action | Microsoft.DataFactory/factories/triggers/delete | トリガーを削除します。 |
> | Action | Microsoft.DataFactory/factories/triggers/read | トリガーを読み取ります。 |
> | Action | Microsoft.DataFactory/factories/triggers/start/action | トリガーを開始します。 |
> | Action | Microsoft.DataFactory/factories/triggers/stop/action | トリガーを停止します。 |
> | Action | Microsoft.DataFactory/factories/triggers/triggerruns/read | トリガーの実行を読み取ります。 |
> | Action | Microsoft.DataFactory/factories/triggers/write | トリガーを作成または更新します。 |
> | Action | Microsoft.DataFactory/factories/write | データ ファクトリを作成または更新します。 |
> | Action | Microsoft.DataFactory/locations/configureFactoryRepo/action | ファクトリのリポジトリを構成します。 |
> | Action | Microsoft.DataFactory/register/action | データ ファクトリ リソース プロバイダーのサブスクリプションを登録します。 |
> | Action | Microsoft.DataFactory/unregister/action | データ ファクトリ リソース プロバイダーへのサブスクリプションの登録を解除します。 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.DataLakeAnalytics/accounts/computePolicies/delete | コンピューティング ポリシーを削除します。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | コンピューティング ポリシーに関する情報を取得します。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/computePolicies/write | コンピューティング ポリシーを作成または更新します。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | DataLakeAnalytics アカウントから DataLakeStore アカウントのリンクを解除します。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | DataLakeAnalytics アカウントのリンクされた DataLakeStore アカウントに関する情報を取得します。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/write | DataLakeAnalytics アカウントのリンクされた DataLakeStore アカウントを作成または更新します。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/delete | DataLakeAnalytics アカウントを削除します。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/firewallRules/delete | ファイアウォール規則を削除します。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | ファイアウォール規則に関する情報を取得します。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/firewallRules/write | ファイアウォール規則を作成または更新します。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/operationResults/read | DataLakeAnalytics アカウントの操作の結果を取得します。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/diagnosticSettings/read | DataLakeAnalytics アカウントの診断設定を取得します。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/diagnosticSettings/write | DataLakeAnalytics アカウントの診断設定を作成または更新します。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/logDefinitions/read | DataLakeAnalytics アカウントで使用可能なログを取得します。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/metricDefinitions/read | DataLakeAnalytics アカウントで使用可能なメトリックを取得します。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/read | 既存の DataLakeAnalytics アカウントに関する情報を取得します。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | DataLakeAnalytics アカウントのリンクされたストレージ アカウントのストレージ コンテナーの SAS トークンを一覧表示します。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/read | DataLakeAnalytics アカウントのリンクされたストレージ アカウントのコンテナーを取得します。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/storageAccounts/delete | DataLakeAnalytics アカウントからストレージ アカウントをリンク解除します。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | DataLakeAnalytics アカウントのリンクされたストレージ アカウントに関する情報を取得します。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/storageAccounts/write | DataLakeAnalytics アカウントのリンクされたストレージ アカウントを作成または更新します。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | 他のユーザーによって送信されたジョブを取り消すアクセス許可を付与します。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/write | DataLakeAnalytics アカウントを作成または更新します。 |
> | Action | Microsoft.DataLakeAnalytics/locations/capability/read | DataLakeAnalytics の使用に関するサブスクリプションの機能の情報を取得します。 |
> | Action | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | DataLakeAnalytics アカウントの名前を使用できるかどうかを確認します。 |
> | Action | Microsoft.DataLakeAnalytics/locations/operationResults/read | DataLakeAnalytics アカウントの操作の結果を取得します。 |
> | Action | Microsoft.DataLakeAnalytics/operations/read | DataLakeAnalytics で使用可能な操作を取得します。 |
> | Action | Microsoft.DataLakeAnalytics/register/action | DataLakeAnalytics にサブスクリプションを登録します。 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.DataLakeStore/accounts/delete | DataLakeStore アカウントを削除します。 |
> | Action | Microsoft.DataLakeStore/accounts/enableKeyVault/action | DataLakeStore アカウントの KeyVault を有効にします。 |
> | Action | Microsoft.DataLakeStore/accounts/eventGridFilters/delete | EventGrid フィルターを削除します。 |
> | Action | Microsoft.DataLakeStore/accounts/eventGridFilters/read | EventGrid フィルターを取得します。 |
> | Action | Microsoft.DataLakeStore/accounts/eventGridFilters/write | EventGrid のフィルターを作成または更新します。 |
> | Action | Microsoft.DataLakeStore/accounts/firewallRules/delete | ファイアウォール規則を削除します。 |
> | Action | Microsoft.DataLakeStore/accounts/firewallRules/read | ファイアウォール規則に関する情報を取得します。 |
> | Action | Microsoft.DataLakeStore/accounts/firewallRules/write | ファイアウォール規則を作成または更新します。 |
> | Action | Microsoft.DataLakeStore/accounts/operationResults/read | DataLakeStore アカウントの操作の結果を取得します。 |
> | Action | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/diagnosticSettings/read | DataLakeStore アカウントの診断設定を取得します。 |
> | Action | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/diagnosticSettings/write | DataLakeStore アカウントの診断設定を作成または更新します。 |
> | Action | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/logDefinitions/read | DataLakeStore アカウントで使用可能なログを取得します。 |
> | Action | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/metricDefinitions/read | DataLakeStore アカウントで使用可能なメトリックを取得します。 |
> | Action | Microsoft.DataLakeStore/accounts/read | 既存の DataLakeStore アカウントに関する情報を取得します。 |
> | Action | Microsoft.DataLakeStore/accounts/Superuser/action | Microsoft.Authorization/roleAssignments/write で許可されているときに、Data Lake Store でのスーパーユーザーを許可します。 |
> | Action | Microsoft.DataLakeStore/accounts/trustedIdProviders/delete | 信頼できる ID プロバイダーを削除します。 |
> | Action | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | 信頼できる ID プロバイダーに関する情報を取得します。 |
> | Action | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | 信頼できる ID プロバイダーを作成または更新します。 |
> | Action | Microsoft.DataLakeStore/accounts/write | DataLakeStore アカウントを作成または更新します。 |
> | Action | Microsoft.DataLakeStore/locations/capability/read | DataLakeStore の使用に関するサブスクリプションの機能の情報を取得します。 |
> | Action | Microsoft.DataLakeStore/locations/checkNameAvailability/action | DataLakeStore アカウントの名前を使用できるかどうかを確認します。 |
> | Action | Microsoft.DataLakeStore/locations/operationResults/read | DataLakeStore アカウントの操作の結果を取得します。 |
> | Action | Microsoft.DataLakeStore/operations/read | DataLakeStore で使用可能な操作を取得します。 |
> | Action | Microsoft.DataLakeStore/register/action | DataLakeStore にサブスクリプションを登録します。 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.DataMigration/locations/operationResults/read | 202 (Accepted) 応答に関連した長時間実行操作の状態を取得します。 |
> | Action | Microsoft.DataMigration/locations/operationStatuses/read | 202 (Accepted) 応答に関連した長時間実行操作の状態を取得します。 |
> | Action | Microsoft.DataMigration/register/action | サブスクリプションを Azure Database Migration Service プロバイダーに登録します。 |
> | Action | Microsoft.DataMigration/services/checkStatus/action | サービスが配置され実行されているかどうかを確認します。 |
> | Action | Microsoft.DataMigration/services/delete | リソースおよびそのすべての子を削除します。 |
> | Action | Microsoft.DataMigration/services/projects/accessArtifacts/action | プロジェクト成果物の GET または PUT に使用できる URL を生成します。 |
> | Action | Microsoft.DataMigration/services/projects/delete | リソースおよびそのすべての子を削除します。 |
> | Action | Microsoft.DataMigration/services/projects/files/delete | リソースおよびそのすべての子を削除します。 |
> | Action | Microsoft.DataMigration/services/projects/files/read | リソースに関する情報を読み取ります。 |
> | Action | Microsoft.DataMigration/services/projects/files/read/action | ファイルの内容の読み取りに使用可能な URL を取得します。 |
> | Action | Microsoft.DataMigration/services/projects/files/readWrite/action | ファイルの内容の読み取りまたは書き込みに使用可能な URL を取得します。 |
> | Action | Microsoft.DataMigration/services/projects/files/write | リソースとそのプロパティを作成または更新します。 |
> | Action | Microsoft.DataMigration/services/projects/read | リソースに関する情報を読み取ります。 |
> | Action | Microsoft.DataMigration/services/projects/tasks/cancel/action | タスクが現在実行されている場合、そのタスクを取り消します。 |
> | Action | Microsoft.DataMigration/services/projects/tasks/delete | リソースおよびそのすべての子を削除します。 |
> | Action | Microsoft.DataMigration/services/projects/tasks/read | リソースに関する情報を読み取ります。 |
> | Action | Microsoft.DataMigration/services/projects/tasks/write | Azure Database Migration Service タスクのタスクを実行します。 |
> | Action | Microsoft.DataMigration/services/projects/write | Azure Database Migration Service タスクのタスクを実行します。 |
> | Action | Microsoft.DataMigration/services/read | リソースに関する情報を読み取ります。 |
> | Action | Microsoft.DataMigration/services/slots/delete | リソースおよびそのすべての子を削除します。 |
> | Action | Microsoft.DataMigration/services/slots/read | リソースに関する情報を読み取ります。 |
> | Action | Microsoft.DataMigration/services/slots/write | リソースとそのプロパティを作成または更新します。 |
> | Action | Microsoft.DataMigration/services/start/action | DMS サービスを開始して、移行をもう一度処理することができるようにします。 |
> | Action | Microsoft.DataMigration/services/stop/action | DMS サービスを停止してコストを最小限に抑えます。 |
> | Action | Microsoft.DataMigration/services/write | リソースとそのプロパティを作成または更新します。 |
> | Action | Microsoft.DataMigration/skus/read | DMS リソースでサポートされている SKU の一覧を取得します。 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.DBforMySQL/locations/performanceTiers/read | 使用可能なパフォーマンス レベルの一覧を返します。 |
> | Action | Microsoft.DBforMySQL/performanceTiers/read | 使用可能なパフォーマンス レベルの一覧を返します。 |
> | Action | Microsoft.DBforMySQL/servers/configurations/read | サーバーの構成の一覧を返すか、指定された構成のプロパティを取得します。 |
> | Action | Microsoft.DBforMySQL/servers/configurations/write | 指定された構成の値を更新します。 |
> | Action | Microsoft.DBforMySQL/servers/delete | 既存のサーバーを削除します。 |
> | Action | Microsoft.DBforMySQL/servers/firewallRules/delete | 既存のファイアウォール規則を削除します。 |
> | Action | Microsoft.DBforMySQL/servers/firewallRules/read | サーバーのファイアウォール規則の一覧を返すか、指定されたファイアウォール規則のプロパティを取得します。 |
> | Action | Microsoft.DBforMySQL/servers/firewallRules/write | 指定されたパラメーターでファイアウォール規則を作成するか、既存の規則を更新します。 |
> | Action | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Action | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Action | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/metricDefinitions/read | データベースの利用可能なメトリックの種類を返します。 |
> | Action | Microsoft.DBforMySQL/servers/read | サーバーの一覧を返すか、指定されたサーバーのプロパティを取得します。 |
> | Action | Microsoft.DBforMySQL/servers/recoverableServers/read | 復旧可能な MySQL サーバーの情報を返します |
> | Action | Microsoft.DBforMySQL/servers/updateConfigurations/action | 指定されたサーバーの構成を更新します。 |
> | Action | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | 既存の仮想ネットワーク ルールを削除します。 |
> | Action | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | 仮想ネットワーク ルールの一覧を返すか、指定された仮想ネットワーク ルールのプロパティを取得します。 |
> | Action | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | 指定されたパラメーターで仮想ネットワーク ルールを作成するか、指定された仮想ネットワーク ルールのプロパティまたはタグを更新します。 |
> | Action | Microsoft.DBforMySQL/servers/write | 指定されたパラメーターでサーバーを作成するか、指定されたサーバーのプロパティまたはタグを更新します。 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | 使用可能なパフォーマンス レベルの一覧を返します。 |
> | Action | Microsoft.DBforPostgreSQL/performanceTiers/read | 使用可能なパフォーマンス レベルの一覧を返します。 |
> | Action | Microsoft.DBforPostgreSQL/servers/configurations/read | サーバーの構成の一覧を返すか、指定された構成のプロパティを取得します。 |
> | Action | Microsoft.DBforPostgreSQL/servers/configurations/write | 指定された構成の値を更新します。 |
> | Action | Microsoft.DBforPostgreSQL/servers/delete | 既存のサーバーを削除します。 |
> | Action | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | 既存のファイアウォール規則を削除します。 |
> | Action | Microsoft.DBforPostgreSQL/servers/firewallRules/read | サーバーのファイアウォール規則の一覧を返すか、指定されたファイアウォール規則のプロパティを取得します。 |
> | Action | Microsoft.DBforPostgreSQL/servers/firewallRules/write | 指定されたパラメーターでファイアウォール規則を作成するか、既存の規則を更新します。 |
> | Action | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Action | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Action | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | Postgres サーバーの使用可能なログを取得します。 |
> | Action | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | データベースの利用可能なメトリックの種類を返します。 |
> | Action | Microsoft.DBforPostgreSQL/servers/queryTexts/action | クエリのテキストを返します。 |
> | Action | Microsoft.DBforPostgreSQL/servers/read | サーバーの一覧を返すか、指定されたサーバーのプロパティを取得します。 |
> | Action | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | 復旧可能な PostgreSQL サーバーの情報を返します |
> | Action | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | 指定されたサーバーで構成されているサーバーの脅威検出ポリシーの詳細を取得します。 |
> | Action | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | 指定されたサーバーの脅威検出ポリシーを変更します。 |
> | Action | Microsoft.DBforPostgreSQL/servers/topQueryStatistics/read | 上位のクエリのクエリ統計の一覧を返します。 |
> | Action | Microsoft.DBforPostgreSQL/servers/updateConfigurations/action | 指定されたサーバーの構成を更新します。 |
> | Action | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | 既存の仮想ネットワーク ルールを削除します。 |
> | Action | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | 仮想ネットワーク ルールの一覧を返すか、指定された仮想ネットワーク ルールのプロパティを取得します。 |
> | Action | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | 指定されたパラメーターで仮想ネットワーク ルールを作成するか、指定された仮想ネットワーク ルールのプロパティまたはタグを更新します。 |
> | Action | Microsoft.DBforPostgreSQL/servers/write | 指定されたパラメーターでサーバーを作成するか、指定されたサーバーのプロパティまたはタグを更新します。 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.Devices/checkNameAvailability/Action | IotHub 名を使用できるかどうかを確認します。 |
> | Action | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | プロビジョニング サービス名を使用できるかどうかを確認します。 |
> | Action | Microsoft.Devices/ElasticPools/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Action | Microsoft.Devices/ElasticPools/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Delete | 証明書を削除します。 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/certificates/generateVerificationCode/Action | 確認コードを生成します。 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Read | 証明書を取得します。 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/certificates/verify/Action | 証明書リソースを確認します。 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Write | 証明書を作成または更新します。 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/Delete | IotHub テナントのリソースを削除します。 |
> | Action | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Action | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | EventHub コンシューマー グループを削除します。 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | EventHub コンシューマー グループを取得します。 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | EventHub コンシューマー グループを作成します。 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/exportDevices/Action | デバイスをエクスポートします。 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/getStats/Read | IotHub テナントの統計リソースを取得します |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/importDevices/Action | デバイスをインポートします。 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | IotHub テナントのキーを取得します。 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/jobs/Read | 特定の IotHub で送信されたジョブの詳細を取得します。 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | IotHub テナントのキーを取得します。 |
> | Action | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | IotHub サービスで使用可能なログ定義を取得します。 |
> | Action | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | IotHub サービスの利用可能なメトリックを取得します。 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | クォータ メトリックを取得します。 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/Read | IotHub テナントのリソースを取得します。 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testall/Action | 既存のすべてのルートに対してメッセージをテストします。 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | 指定されたテスト ルートに対してメッセージをテストします。 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | IotHub のすべてのルーティング エンドポイントの正常性を取得します。 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/Write | IotHub テナントのリソースを作成または更新します。 |
> | Action | Microsoft.Devices/ElasticPools/metricDefinitions/read | IotHub サービスの利用可能なメトリックを取得します。 |
> | Action | Microsoft.Devices/iotHubs/certificates/Delete | 証明書を削除します。 |
> | Action | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | 確認コードを生成します。 |
> | Action | Microsoft.Devices/iotHubs/certificates/Read | 証明書を取得します。 |
> | Action | Microsoft.Devices/iotHubs/certificates/verify/Action | 証明書リソースを確認します。 |
> | Action | Microsoft.Devices/iotHubs/certificates/Write | 証明書を作成または更新します。 |
> | Action | Microsoft.Devices/iotHubs/Delete | IotHub リソースを削除します。 |
> | Action | Microsoft.Devices/IotHubs/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Action | Microsoft.Devices/IotHubs/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Action | Microsoft.Devices/iotHubs/eventGridFilters/Delete | Event Grid フィルターを削除します。 |
> | Action | Microsoft.Devices/iotHubs/eventGridFilters/Read | Event Grid フィルターを取得します。 |
> | Action | Microsoft.Devices/iotHubs/eventGridFilters/Write | 新しい Event Grid フィルターを作成するか、既存の Event Grid フィルターを更新します。 |
> | Action | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | EventHub コンシューマー グループを削除します。 |
> | Action | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | EventHub コンシューマー グループを取得します。 |
> | Action | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | EventHub コンシューマー グループを作成します。 |
> | Action | Microsoft.Devices/iotHubs/exportDevices/Action | デバイスをエクスポートします。 |
> | Action | Microsoft.Devices/iotHubs/importDevices/Action | デバイスをインポートします。 |
> | Action | Microsoft.Devices/iotHubs/iotHubKeys/listkeys/Action | 特定の名前の IotHub キーを取得します。 |
> | Action | Microsoft.Devices/iotHubs/iotHubStats/Read | IotHub の統計を取得します。 |
> | Action | Microsoft.Devices/iotHubs/jobs/Read | 特定の IotHub で送信されたジョブの詳細を取得します。 |
> | Action | Microsoft.Devices/iotHubs/listkeys/Action | すべての IotHub キーを取得します。 |
> | Action | Microsoft.Devices/IotHubs/logDefinitions/read | IotHub サービスで使用可能なログ定義を取得します。 |
> | Action | Microsoft.Devices/IotHubs/metricDefinitions/read | IotHub サービスの利用可能なメトリックを取得します。 |
> | Action | Microsoft.Devices/iotHubs/operationresults/Read | 操作の結果を取得します (古い API)。 |
> | Action | Microsoft.Devices/iotHubs/quotaMetrics/Read | クォータ メトリックを取得します。 |
> | Action | Microsoft.Devices/iotHubs/Read | IotHub リソースを取得します。 |
> | Action | Microsoft.Devices/iotHubs/routing/$testall/Action | 既存のすべてのルートに対してメッセージをテストします。 |
> | Action | Microsoft.Devices/iotHubs/routing/$testnew/Action | 指定されたテスト ルートに対してメッセージをテストします。 |
> | Action | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | IotHub のすべてのルーティング エンドポイントの正常性を取得します。 |
> | Action | Microsoft.Devices/iotHubs/skus/Read | 有効な IotHub SKU を取得します。 |
> | Action | Microsoft.Devices/iotHubs/Write | IotHub リソースを作成または更新します。 |
> | Action | Microsoft.Devices/locations/operationresults/Read | 場所に基づく操作の結果を取得します。 |
> | Action | Microsoft.Devices/operationresults/Read | 操作の結果を取得します。 |
> | Action | Microsoft.Devices/operations/Read | ResourceProvider のすべての操作を取得します。 |
> | Action | Microsoft.Devices/provisioningServices/certificates/Delete | 証明書を削除します。 |
> | Action | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | 確認コードを生成します。 |
> | Action | Microsoft.Devices/provisioningServices/certificates/Read | 証明書を取得します。 |
> | Action | Microsoft.Devices/provisioningServices/certificates/verify/Action | 証明書リソースを確認します。 |
> | Action | Microsoft.Devices/provisioningServices/certificates/Write | 証明書を作成または更新します。 |
> | Action | Microsoft.Devices/provisioningServices/Delete | IotDps リソースを削除します。 |
> | Action | Microsoft.Devices/provisioningServices/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Action | Microsoft.Devices/provisioningServices/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Action | Microsoft.Devices/provisioningServices/keys/listkeys/Action | キー名の IotDps キーを取得します。 |
> | Action | Microsoft.Devices/provisioningServices/listkeys/Action | すべての IotDps キーを取得します。 |
> | Action | Microsoft.Devices/provisioningServices/logDefinitions/read | プロビジョニング サービスで使用可能なログ定義を取得します。 |
> | Action | Microsoft.Devices/provisioningServices/metricDefinitions/read | プロビジョニング サービスで使用可能なメトリックを取得します。 |
> | Action | Microsoft.Devices/provisioningServices/operationresults/Read | DPS 操作の結果を取得します。 |
> | Action | Microsoft.Devices/provisioningServices/Read | IotDps リソースを取得します。 |
> | Action | Microsoft.Devices/provisioningServices/skus/Read | 有効な IotDps SKU を取得します。 |
> | Action | Microsoft.Devices/provisioningServices/Write | IotDps リソースを作成します。 |
> | Action | Microsoft.Devices/register/action | IotHub リソース プロバイダーにサブスクリプションを登録し、IotHub リソースを作成できるようにします。 |
> | Action | Microsoft.Devices/register/action | IotHub リソース プロバイダーにサブスクリプションを登録し、IotHub リソースを作成できるようにします。 |
> | Action | Microsoft.Devices/usages/Read | このプロバイダーのサブスクリプションの使用状況の詳細を取得します。 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.DevTestLab/labCenters/delete | ラボ センターを削除します。 |
> | Action | Microsoft.DevTestLab/labCenters/read | ラボ センターを読み取ります。 |
> | Action | Microsoft.DevTestLab/labCenters/write | ラボ センターを追加または変更します。 |
> | Action | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | Azure Resource Manager テンプレートを読み取ります。 |
> | Action | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | 特定のアーティファクトの ARM テンプレートを生成し、必要なファイルをストレージ アカウントにアップロードして、生成されたアーティファクトを検証します。 |
> | Action | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | アーティファクトを読み取ります。 |
> | Action | Microsoft.DevTestLab/labs/artifactSources/delete | アーティファクト ソースを削除します。 |
> | Action | Microsoft.DevTestLab/labs/artifactSources/read | アーティファクト ソースを読み取ります。 |
> | Action | Microsoft.DevTestLab/labs/artifactSources/write | アーティファクト ソースを追加または変更します。 |
> | Action | Microsoft.DevTestLab/labs/ClaimAnyVm/action | ラボ内のランダムに要求可能な仮想マシンを要求します。 |
> | Action | Microsoft.DevTestLab/labs/costs/read | コストを読み取ります。 |
> | Action | Microsoft.DevTestLab/labs/costs/write | コストを追加または変更します。 |
> | Action | Microsoft.DevTestLab/labs/CreateEnvironment/action | ラボで仮想マシンを作成します。 |
> | Action | Microsoft.DevTestLab/labs/customImages/delete | カスタム イメージを削除します。 |
> | Action | Microsoft.DevTestLab/labs/customImages/read | カスタム イメージを読み取ります。 |
> | Action | Microsoft.DevTestLab/labs/customImages/write | カスタム イメージを追加または変更します。 |
> | Action | Microsoft.DevTestLab/labs/delete | ラボを削除します。 |
> | Action | Microsoft.DevTestLab/labs/ExportResourceUsage/action | ラボのリソース使用量をストレージ アカウントにエクスポートします。 |
> | Action | Microsoft.DevTestLab/labs/formulas/delete | 数式を削除します。 |
> | Action | Microsoft.DevTestLab/labs/formulas/read | 数式を読み取ります。 |
> | Action | Microsoft.DevTestLab/labs/formulas/write | 数式を追加または変更します。 |
> | Action | Microsoft.DevTestLab/labs/galleryImages/read | ギャラリー イメージを読み取ります。 |
> | Action | Microsoft.DevTestLab/labs/GenerateUploadUri/action | カスタム ディスク イメージをラボにアップロードするための URI を生成します。 |
> | Action | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | 別のラボに仮想マシンをインポートします。 |
> | Action | Microsoft.DevTestLab/labs/ListVhds/action | カスタム イメージの作成に使用できるディスク イメージを一覧表示します。 |
> | Action | Microsoft.DevTestLab/labs/notificationChannels/delete | notificationchannels を削除します。 |
> | Action | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | 指定されたチャンネルに通知を送信します。 |
> | Action | Microsoft.DevTestLab/labs/notificationChannels/read | notificationchannels を読み取ります。 |
> | Action | Microsoft.DevTestLab/labs/notificationChannels/write | notificationchannels を追加または変更します。 |
> | Action | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | ラボ ポリシーを評価します。 |
> | Action | Microsoft.DevTestLab/labs/policySets/policies/delete | ポリシーを削除します。 |
> | Action | Microsoft.DevTestLab/labs/policySets/policies/read | ポリシーを読み取ります。 |
> | Action | Microsoft.DevTestLab/labs/policySets/policies/write | ポリシーを追加または変更します。 |
> | Action | Microsoft.DevTestLab/labs/read | ラボを読み取ります。 |
> | Action | Microsoft.DevTestLab/labs/schedules/delete | スケジュールを削除します。 |
> | Action | Microsoft.DevTestLab/labs/schedules/Execute/action | スケジュールを実行します。 |
> | Action | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | 適用可能なすべてのスケジュールを一覧表示します。 |
> | Action | Microsoft.DevTestLab/labs/schedules/read | スケジュールを読み取ります。 |
> | Action | Microsoft.DevTestLab/labs/schedules/write | スケジュールを追加または変更します。 |
> | Action | Microsoft.DevTestLab/labs/serviceRunners/delete | サービス ランナーを削除します。 |
> | Action | Microsoft.DevTestLab/labs/serviceRunners/read | サービス ランナーを読み取ります。 |
> | Action | Microsoft.DevTestLab/labs/serviceRunners/write | サービス ランナーを追加または変更します。 |
> | Action | Microsoft.DevTestLab/labs/users/delete | ユーザー プロファイルを削除します。 |
> | Action | Microsoft.DevTestLab/labs/users/disks/Attach/action | 仮想マシンにディスクを接続し、ディスクのリースを作成します。 |
> | Action | Microsoft.DevTestLab/labs/users/disks/delete | ディスクを削除します。 |
> | Action | Microsoft.DevTestLab/labs/users/disks/Detach/action | 仮想マシンに接続されているディスクを切断し、ディスクのリースを解除します。 |
> | Action | Microsoft.DevTestLab/labs/users/disks/read | ディスクを読み取ります。 |
> | Action | Microsoft.DevTestLab/labs/users/disks/write | ディスクを追加または変更します。 |
> | Action | Microsoft.DevTestLab/labs/users/environments/delete | 環境を削除します。 |
> | Action | Microsoft.DevTestLab/labs/users/environments/read | 環境を読み取ります。 |
> | Action | Microsoft.DevTestLab/labs/users/environments/write | 環境を追加または変更します。 |
> | Action | Microsoft.DevTestLab/labs/users/read | ユーザー プロファイルを読み取ります。 |
> | Action | Microsoft.DevTestLab/labs/users/secrets/delete | シークレットを削除します。 |
> | Action | Microsoft.DevTestLab/labs/users/secrets/read | シークレットを読み取ります。 |
> | Action | Microsoft.DevTestLab/labs/users/secrets/write | シークレットを追加または変更します。 |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | サービス ファブリックを削除します。 |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | 適用可能なすべてのスケジュールを一覧表示します。 |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/read | サービス ファブリックを読み取ります。 |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | スケジュールを削除します。 |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | スケジュールを実行します。 |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | スケジュールを読み取ります。 |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | スケジュールを追加または変更します。 |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | サービス ファブリックを開始します。 |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | サービス ファブリックを停止します。 |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/write | サービス ファブリックを追加または変更します。 |
> | Action | Microsoft.DevTestLab/labs/users/write | ユーザー プロファイルを追加または変更します。 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | 新規または既存のデータ ディスクを仮想マシンに接続します。 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | 仮想マシンにアーティファクトを適用します。 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | 既存の仮想マシンの所有権を取得します。 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/delete | 仮想マシンを削除します。 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | 指定されたディスクを仮想マシンから切断します。 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/GetRdpFileContents/action | 仮想マシンの RDP ファイルの内容を表す文字列を取得します。 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | 適用可能なすべてのスケジュールを一覧表示します。 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/read | 仮想マシンを読み取ります。 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | 仮想マシンを再配置します。 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/Resize/action | 仮想マシンのサイズを変更します。 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/Restart/action | 仮想マシンを再起動します。 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | スケジュールを削除します。 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | スケジュールを実行します。 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | スケジュールを読み取ります。 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | スケジュールを追加または変更します。 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/Start/action | 仮想マシンを起動します。 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | 仮想マシンの停止 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | 仮想マシン データ ディスクの所有権を自分に譲渡します。 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | 既存の仮想マシンの所有権を放棄します。 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/write | 仮想マシンを追加または変更します。 |
> | Action | Microsoft.DevTestLab/labs/virtualNetworks/delete | 仮想ネットワークを削除します。 |
> | Action | Microsoft.DevTestLab/labs/virtualNetworks/read | 仮想ネットワークを読み取ります。 |
> | Action | Microsoft.DevTestLab/labs/virtualNetworks/write | 仮想ネットワークを追加または変更します。 |
> | Action | Microsoft.DevTestLab/labs/vmPools/delete | 仮想マシン プールを削除します。 |
> | Action | Microsoft.DevTestLab/labs/vmPools/read | 仮想マシン プールを読み取ります。 |
> | Action | Microsoft.DevTestLab/labs/vmPools/write | 仮想マシン プールを追加または変更します。 |
> | Action | Microsoft.DevTestLab/labs/write | ラボを追加または変更します。 |
> | Action | Microsoft.DevTestLab/locations/operations/read | 操作を読み取ります。 |
> | Action | Microsoft.DevTestLab/register/action | サブスクリプションを登録します。 |
> | Action | Microsoft.DevTestLab/schedules/delete | スケジュールを削除します。 |
> | Action | Microsoft.DevTestLab/schedules/Execute/action | スケジュールを実行します。 |
> | Action | Microsoft.DevTestLab/schedules/read | スケジュールを読み取ります。 |
> | Action | Microsoft.DevTestLab/schedules/Retarget/action | スケジュールのターゲット リソース ID を更新します。 |
> | Action | Microsoft.DevTestLab/schedules/write | スケジュールを追加または変更します。 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.DocumentDB/databaseAccountNames/read | 名前を使用できるかどうかを確認します。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | データベース アカウントのリソース グループを変更します。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | コレクションのメトリック定義を読み取ります。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | コレクションのメトリックを読み取ります。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | データベース アカウントのパーティション キー レベルのメトリックを読み取ります。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | データベース アカウントのパーティション レベルのメトリックを読み取ります。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | コレクション内のデータベース アカウント パーティションを読み取ります。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/usages/read | データベース アカウントのパーティション レベルの使用状況を読み取ります。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/usages/read | コレクションの使用状況を読み取ります。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | データベースのメトリック定義を読み取ります。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/metrics/read | データベースのメトリックを読み取ります。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/usages/read | データベースの使用状況を読み取ります。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/delete | データベース アカウントを削除します。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/failoverPriorityChange/action | データベース アカウントのリージョンのフェールオーバーの優先度を変更します。 これは手動フェールオーバー操作を実行する際に使用します。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | データベース アカウントの接続文字列を取得します。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/listKeys/action | データベース アカウントのキーを一覧表示します。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | データベース アカウントのメトリック定義を読み取ります。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/metrics/read | データベース アカウントのメトリックを読み取ります。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/offlineRegion/action | データベース アカウントのリージョンをオフラインにします。  |
> | Action | Microsoft.DocumentDB/databaseAccounts/onlineRegion/action | データベース アカウントのリージョンをオンラインにします。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/percentile/metrics/read | 待機時間メトリックを読み取ります。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/percentile/read | レプリケーションの待機時間のパーセンタイルを読み取ります。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read | 特定のソースおよびターゲット リージョンの待機時間メトリックを読み取ります。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/percentile/targetRegion/metrics/read | 特定のターゲット リージョンの待機時間メトリックを読み取ります。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/logDefinitions/read | データベース アカウントで使用可能なログ カテゴリを取得します。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/metricDefinitions/read | データベース アカウントで使用可能なメトリックを取得します。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/read | データベース アカウントを読み取ります。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | データベース アカウントの読み取り専用キーを読み取ります。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/readonlykeys/read | データベース アカウントの読み取り専用キーを読み取ります。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/regenerateKey/action | データベース アカウントのキーをローテーションします。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/metrics/read | リージョンのコレクション メトリックを読み取ります。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | リージョンのデータベース アカウントのパーティション キー レベルのメトリックを読み取ります。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/metrics/read | リージョンのデータベース アカウントのパーティション レベルのメトリックを読み取ります。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/read | コレクション内のリージョンのデータベース アカウント パーティションを読み取ります。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/region/metrics/read | リージョンとデータベース アカウントのメトリックを読み取ります。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/usages/read | データベース アカウントの使用状況を読み取ります。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/write | データベース アカウントを更新します。 |
> | Action | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/action | 仮想ネットワークまたはサブネットが削除されていることを Microsoft.DocumentDB に通知します。 |
> | Action | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | deleteVirtualNetworkOrSubnets 非同期操作の状態を読み取ります。 |
> | Action | Microsoft.DocumentDB/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Action | Microsoft.DocumentDB/operations/read | Microsoft DocumentDB で使用可能な操作を読み取ります。  |
> | Action | Microsoft.DocumentDB/register/action |  Microsoft DocumentDB リソース プロバイダーにサブスクリプションを登録します。 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.DomainRegistration/checkDomainAvailability/Action | ドメインを購入できるかどうかを確認します。 |
> | Action | Microsoft.DomainRegistration/domains/Delete | 既存のドメインを削除します。 |
> | Action | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Delete | 所有権の識別子を削除します。 |
> | Action | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | 所有権の識別子を一覧表示します。 |
> | Action | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | 所有権の識別子を取得します。 |
> | Action | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Write | 識別子を作成または更新します。 |
> | Action | Microsoft.DomainRegistration/domains/operationresults/Read | ドメイン操作を取得します。 |
> | Action | Microsoft.DomainRegistration/domains/Read | ドメインの一覧を取得します。 |
> | Action | Microsoft.DomainRegistration/domains/Read | ドメインを取得します。 |
> | Action | Microsoft.DomainRegistration/domains/renew/Action | 既存のドメインを更新します。 |
> | Action | Microsoft.DomainRegistration/domains/Write | 新しいドメインを追加するか、既存のドメインを更新します。 |
> | Action | Microsoft.DomainRegistration/generateSsoRequest/Action | ドメイン コントロール センターへのサインイン要求を生成します。 |
> | Action | Microsoft.DomainRegistration/listDomainRecommendations/Action | キーワードに基づいて、ドメインの推奨事項の一覧を取得します。 |
> | Action | Microsoft.DomainRegistration/operations/Read | アプリ サービス ドメイン登録のすべての操作を一覧表示します。 |
> | Action | Microsoft.DomainRegistration/register/action | Microsoft Domains リソース プロバイダーをサブスクリプションに登録します。 |
> | Action | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | 契約のアクションを一覧表示します。 |
> | Action | Microsoft.DomainRegistration/topLevelDomains/Read | 最上位ドメインを取得します。 |
> | Action | Microsoft.DomainRegistration/topLevelDomains/Read | 最上位ドメインを取得します。 |
> | Action | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | ドメイン購入オブジェクトを送信せずに検証します。 |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.DynamicsLcs/lcsprojects/clouddeployments/read | ユーザーに属する Microsoft Dynamics Lifecycle Services プロジェクトの Microsoft Dynamics AX 2012 R3 Evaluation デプロイを表示します。 |
> | Action | Microsoft.DynamicsLcs/lcsprojects/clouddeployments/write | ユーザーに属する Microsoft Dynamics Lifecycle Services プロジェクトの Microsoft Dynamics AX 2012 R3 Evaluation デプロイを作成します。 デプロイは Azure 管理ポータルから管理できます。 |
> | Action | Microsoft.DynamicsLcs/lcsprojects/connectors/read | Microsoft Dynamics Lifecycle Services プロジェクトに属するコネクタを読み取ります。 |
> | Action | Microsoft.DynamicsLcs/lcsprojects/connectors/write | Microsoft Dynamics Lifecycle Services プロジェクトに属するコネクタを作成および更新します。 |
> | Action | Microsoft.DynamicsLcs/lcsprojects/delete | ユーザーに属する Microsoft Dynamics Lifecycle Services プロジェクトを削除します。 |
> | Action | Microsoft.DynamicsLcs/lcsprojects/read | ユーザーに属する Microsoft Dynamics Lifecycle Services プロジェクトを表示します。 |
> | Action | Microsoft.DynamicsLcs/lcsprojects/write | ユーザーに属する Microsoft Dynamics Lifecycle Services プロジェクトを作成および更新します。 更新できるのは、名前プロパティと説明プロパティだけです。 プロジェクトに関連付けられているサブスクリプションと場所は、プロジェクトの作成後に更新することはできません。 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.EventGrid/eventSubscriptions/delete | eventSubscription を削除します。 |
> | Action | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | イベント サブスクリプションの完全な URL を取得します。 |
> | Action | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read | イベント サブスクリプションの診断設定を取得します。 |
> | Action | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write | イベント サブスクリプションの診断設定を作成または更新します。 |
> | Action | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read | eventSubscription で使用可能なメトリックを取得します。 |
> | Action | Microsoft.EventGrid/eventSubscriptions/read | eventSubscription を読み取ります。 |
> | Action | Microsoft.EventGrid/eventSubscriptions/write | eventSubscription を作成または更新します。 |
> | Action | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read | トピックの診断設定を取得します。 |
> | Action | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write | トピックの診断設定を作成または更新します。 |
> | Action | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | トピックで使用可能なメトリックを取得します。 |
> | Action | Microsoft.EventGrid/register/action | EventGrid リソース プロバイダーに eventSubscription を登録し、Event Grid サブスクリプションを作成できるようにします。 |
> | Action | Microsoft.EventGrid/topics/delete | トピックを削除する |
> | Action | Microsoft.EventGrid/topics/listKeys/action | トピックのキーを一覧表示します |
> | Action | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/read | トピックの診断設定を取得します。 |
> | Action | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/write | トピックの診断設定を作成または更新します。 |
> | Action | Microsoft.EventGrid/topics/providers/Microsoft.Insights/metricDefinitions/read | トピックで使用可能なメトリックを取得します。 |
> | Action | Microsoft.EventGrid/topics/read | トピックを読み取ります。 |
> | Action | Microsoft.EventGrid/topics/regenerateKey/action | トピックのキーを再生成します。 |
> | Action | Microsoft.EventGrid/topics/write | トピックを作成または更新します。 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.EventHub/checkNameAvailability/action | 特定のサブスクリプションで名前空間を使用できるかどうかを確認します。 |
> | Action | Microsoft.EventHub/checkNamespaceAvailability/action | 特定のサブスクリプションで名前空間を使用できるかどうかを確認します。 この API は非推奨です。代わりに CheckNameAvailabiltiy を使用してください。 |
> | Action | Microsoft.EventHub/clusters/providers/Microsoft.Insights/metricDefinitions/read | クラスターのメトリック リソースの説明一覧を取得します。 |
> | Action | Microsoft.EventHub/clusters/read | クラスター リソースの説明を取得します。 |
> | Action | Microsoft.EventHub/clusters/write | クラスター リソースの説明を取得します。 |
> | Action | Microsoft.EventHub/namespaces/authorizationRules/action | 名前空間の承認規則を更新します。 この API は使用されていません。 名前空間の承認規則を更新するには、代わりに PUT 呼び出しを使用してください。 この操作は、API バージョン 2017-04-01 ではサポートされていません。 |
> | Action | Microsoft.EventHub/namespaces/authorizationRules/delete | 名前空間の承認規則を削除します。 名前空間の既定の承認規則は削除できません。  |
> | Action | Microsoft.EventHub/namespaces/authorizationRules/listkeys/action | 名前空間への接続文字列を取得します。 |
> | Action | Microsoft.EventHub/namespaces/authorizationRules/read | 名前空間の承認規則の説明の一覧を取得します。 |
> | Action | Microsoft.EventHub/namespaces/authorizationRules/regenerateKeys/action | リソースのプライマリ キーまたはセカンダリ キーを再生成します。 |
> | Action | Microsoft.EventHub/namespaces/authorizationRules/write | 名前空間レベルの承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権、プライマリ キー、セカンダリ キーを更新できます。 |
> | Action | Microsoft.EventHub/namespaces/Delete | 名前空間リソースを削除します。 |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | ディザスター リカバリーのプライマリ名前空間の承認規則キーを取得します。 |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | ディザスター リカバリーのプライマリ名前空間の承認規則を取得します。 |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/breakPairing/action | ディザスター リカバリーを無効にし、プライマリ名前空間からセカンダリ名前空間への変更の複製を停止します。 |
> | Action | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | 指定されたサブスクリプションで名前空間別名を使用できるかどうかを確認します。 |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | 名前空間に関連付けられているディザスター リカバリー構成を削除します。 この操作は、プライマリ名前空間からのみ呼び出すことができます。 |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/failover/action | geo DR フェールオーバーを呼び出し、セカンダリ名前空間を指すように名前空間の別名を再構成します。 |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | 名前空間に関連付けられているディザスター リカバリーの構成を取得します。 |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | 名前空間に関連付けられているディザスター リカバリーの構成を作成または更新します。 |
> | Action | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/action | EventHub を更新する操作です。 この操作は、API バージョン 2017-04-01 ではサポートされていません。 承認規則。 承認規則を更新するには、PUT 呼び出しを使用してください。 |
> | Action | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/delete | EventHub の承認規則を削除する操作。 |
> | Action | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/listkeys/action | EventHub への接続文字列を取得します。 |
> | Action | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/read |  EventHub の承認規則の一覧を取得します。 |
> | Action | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/regenerateKeys/action | リソースのプライマリ キーまたはセカンダリ キーを再生成します。 |
> | Action | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/write | EventHub の承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権を更新できます。 |
> | Action | Microsoft.EventHub/namespaces/eventHubs/consumergroups/Delete | ConsumerGroup リソースを削除する操作。 |
> | Action | Microsoft.EventHub/namespaces/eventHubs/consumergroups/read | ConsumerGroup リソースの説明の一覧を取得します。 |
> | Action | Microsoft.EventHub/namespaces/eventHubs/consumergroups/write | ConsumerGroup のプロパティを作成または更新します。 |
> | Action | Microsoft.EventHub/namespaces/eventhubs/Delete | EventHub リソースを削除する操作。 |
> | Action | Microsoft.EventHub/namespaces/eventhubs/read | EventHub リソースの説明の一覧を取得します。 |
> | Action | Microsoft.EventHub/namespaces/eventhubs/write | EventHub のプロパティを作成または更新します。 |
> | Action | Microsoft.EventHub/namespaces/messagingPlan/read | 名前空間のメッセージング プランを取得します。<br>この API は非推奨です。<br>より新しい API バージョンでは、MessagingPlan リソースにより公開されるプロパティは (親) 名前空間リソースに移動されています。<br>この操作は、API バージョン 2017-04-01 ではサポートされていません。 |
> | Action | Microsoft.EventHub/namespaces/messagingPlan/write | 名前空間のメッセージング プランを更新します。<br>この API は非推奨です。<br>より新しい API バージョンでは、MessagingPlan リソースにより公開されるプロパティは (親) 名前空間リソースに移動されています。<br>この操作は、API バージョン 2017-04-01 ではサポートされていません。 |
> | Action | Microsoft.EventHub/namespaces/operationresults/read | 名前空間の操作の状態を取得します。 |
> | Action | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | 名前空間の診断設定リソースの説明の一覧を取得します。 |
> | Action | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | 名前空間の診断設定リソースの説明の一覧を取得します。 |
> | Action | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/logDefinitions/read | 名前空間のログ リソースの説明の一覧を取得します。 |
> | Action | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/metricDefinitions/read | 名前空間のメトリック リソースの説明の一覧を取得します。 |
> | Action | Microsoft.EventHub/namespaces/read | 名前空間リソースの説明の一覧を取得します。 |
> | Action | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | ACS 名前空間を削除します。 |
> | Action | Microsoft.EventHub/namespaces/write | 名前空間リソースを作成し、そのプロパティを更新します。 名前空間のタグとキャパシティは、更新できるプロパティです。 |
> | Action | Microsoft.EventHub/operations/read | 操作を取得します。 |
> | Action | Microsoft.EventHub/register/action | EventHub リソース プロバイダーにサブスクリプションを登録し、EventHub リソースを作成できるようにします。 |
> | Action | Microsoft.EventHub/sku/read | SKU リソースの説明の一覧を取得します。 |
> | Action | Microsoft.EventHub/sku/regions/read | SkuRegions リソースの説明の一覧を取得します。 |
> | Action | Microsoft.EventHub/unregister/action | EventHub リソース プロバイダーに登録します。 |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.Features/features/read | サブスクリプションの機能を取得します。 |
> | Action | Microsoft.Features/operations/read | 操作の一覧を取得します。 |
> | Action | Microsoft.Features/providers/features/read | 指定されたリソース プロバイダーのサブスクリプションの機能を取得します。 |
> | Action | Microsoft.Features/providers/features/register/action | 指定されたリソース プロバイダーのサブスクリプションの機能を登録します。 |
> | Action | Microsoft.Features/providers/features/unregister/action | 指定されたリソース プロバイダーのサブスクリプションの機能を登録解除します。 |
> | Action | Microsoft.Features/register/action | サブスクリプションの機能を登録します。 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.HDInsight/clusters/changerdpsetting/action | HDInsight クラスターの RDP 設定を変更します。 |
> | Action | Microsoft.HDInsight/clusters/configurations/action | HDInsight クラスターの構成を更新します。 |
> | Action | Microsoft.HDInsight/clusters/configurations/read | HDInsight クラスターの構成を取得します。 |
> | Action | Microsoft.HDInsight/clusters/delete | HDInsight クラスターを削除します。 |
> | Action | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/read | リソース HDInsight クラスターの診断設定を取得します。 |
> | Action | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/write | リソース HDInsight クラスターの診断設定を作成または更新します。 |
> | Action | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/metricDefinitions/read | HDInsight クラスターで使用可能なメトリックを取得します。 |
> | Action | Microsoft.HDInsight/clusters/read | HDInsight クラスターの詳細を取得します。 |
> | Action | Microsoft.HDInsight/clusters/roles/resize/action | HDInsight クラスターのサイズを変更します。 |
> | Action | Microsoft.HDInsight/clusters/write | HDInsight クラスターを作成または更新します。 |
> | Action | Microsoft.HDInsight/locations/capabilities/read | サブスクリプション機能を取得します。 |
> | Action | Microsoft.HDInsight/locations/checkNameAvailability/read | 名前を使用できるかどうかを確認します。 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.ImportExport/jobs/delete | 既存のジョブを削除します。 |
> | Action | Microsoft.ImportExport/jobs/listBitLockerKeys/action | 指定されたジョブの BitLocker キーを取得します。 |
> | Action | Microsoft.ImportExport/jobs/read | 指定されたジョブのプロパティを取得するか、ジョブの一覧を返します。 |
> | Action | Microsoft.ImportExport/jobs/write | 指定されたパラメーターを使用してジョブを作成するか、指定されたジョブのプロパティまたはタグを更新します。 |
> | Action | Microsoft.ImportExport/locations/read | 指定された場所のプロパティを取得するか、場所の一覧を返します。 |
> | Action | Microsoft.ImportExport/register/action | Import/Export リソース プロバイダーにサブスクリプションを登録し、インポート/エクスポート ジョブを作成できるようにします。 |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.Insights/ActionGroups/Delete | アクション グループを削除します |
> | Action | Microsoft.Insights/ActionGroups/Read | アクション グループを読み取ります |
> | Action | Microsoft.Insights/ActionGroups/Write | アクション グループを作成または更新します |
> | Action | Microsoft.Insights/ActivityLogAlerts/Activated/Action | アクティビティ ログ アラートがアクティブ化されました |
> | Action | Microsoft.Insights/ActivityLogAlerts/Delete | アクティビティ ログ アラートを削除します |
> | Action | Microsoft.Insights/ActivityLogAlerts/Read | アクティビティ ログ アラートを読み取ります |
> | Action | Microsoft.Insights/ActivityLogAlerts/Write | アクティビティ ログ アラートを作成または更新します |
> | Action | Microsoft.Insights/AlertRules/Activated/Action | クラシック メトリック アラートがアクティブ化されました |
> | Action | Microsoft.Insights/AlertRules/Delete | クラシック メトリック アラートを削除します |
> | Action | Microsoft.Insights/AlertRules/Incidents/Read | クラシック メトリック アラートのインシデントを読み取ります |
> | Action | Microsoft.Insights/AlertRules/Read | クラシック メトリック アラートを読み取ります |
> | Action | Microsoft.Insights/AlertRules/Resolved/Action | クラシック メトリック アラートが解決されました |
> | Action | Microsoft.Insights/AlertRules/Throttled/Action | クラシック メトリック アラート ルールが調整されました |
> | Action | Microsoft.Insights/AlertRules/Write | クラシック メトリック アラートを作成または更新します |
> | Action | Microsoft.Insights/AutoscaleSettings/Delete | 自動スケール設定を削除します |
> | Action | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | メトリック定義を読み取ります。 |
> | Action | Microsoft.Insights/AutoscaleSettings/Read | 自動スケール設定を読み取ります |
> | Action | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | 自動スケールのスケールダウンが開始されました |
> | Action | Microsoft.Insights/AutoscaleSettings/ScaledownResult/Action | 自動スケールのスケールダウンが完了しました |
> | Action | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | 自動スケールのスケールアップが開始されました |
> | Action | Microsoft.Insights/AutoscaleSettings/ScaleupResult/Action | 自動スケールのスケールアップが完了しました |
> | Action | Microsoft.Insights/AutoscaleSettings/Write | 自動スケール設定を作成または更新します |
> | Action | Microsoft.Insights/Components/AnalyticsItems/Delete | Application Insights の分析項目を削除します。 |
> | Action | Microsoft.Insights/Components/AnalyticsItems/Read | Application Insights の分析項目を読み取ります。 |
> | Action | Microsoft.Insights/Components/AnalyticsItems/Write | Application Insights の分析項目を書き込みます。 |
> | Action | Microsoft.Insights/Components/AnalyticsTables/Action | Application Insights の分析テーブル アクションです。 |
> | Action | Microsoft.Insights/Components/AnalyticsTables/Delete | Application Insights の分析テーブル スキーマを削除します。 |
> | Action | Microsoft.Insights/Components/AnalyticsTables/Read | Application Insights の分析テーブル スキーマを読み取ります。 |
> | Action | Microsoft.Insights/Components/AnalyticsTables/Write | Application Insights の分析テーブル スキーマを書き込みます。 |
> | Action | Microsoft.Insights/Components/Annotations/Delete | Application Insights の注釈を削除します。 |
> | Action | Microsoft.Insights/Components/Annotations/Read | Application Insights の注釈を読み取ります。 |
> | Action | Microsoft.Insights/Components/Annotations/Write | Application Insights の注釈を書き込みます。 |
> | Action | Microsoft.Insights/Components/Api/Read | Application Insights コンポーネントのデータ API を読み取ります。 |
> | Action | Microsoft.Insights/Components/ApiKeys/Action | Application Insights の API キーを生成します。 |
> | Action | Microsoft.Insights/Components/ApiKeys/Delete | Application Insights の API キーを削除します。 |
> | Action | Microsoft.Insights/Components/ApiKeys/Read | Application Insights の API キーを読み取ります。 |
> | Action | Microsoft.Insights/Components/BillingPlanForComponent/Read | Application Insights コンポーネントの料金プランを読み取ります。 |
> | Action | Microsoft.Insights/Components/CurrentBillingFeatures/Read | Application Insights コンポーネントの現在の料金機能を読み取ります。 |
> | Action | Microsoft.Insights/Components/CurrentBillingFeatures/Write | Application Insights コンポーネントの現在の料金機能を書き込みます。 |
> | Action | Microsoft.Insights/Components/DefaultWorkItemConfig/Read | Application Insights の既定の ALM 統合構成を読み取ります。 |
> | Action | Microsoft.Insights/Components/Delete | Application Insights コンポーネントの構成を削除します。 |
> | Action | Microsoft.Insights/Components/Events/Read | OData クエリ形式を使用して、Application Insights からログを取得します。 |
> | Action | Microsoft.Insights/Components/ExportConfiguration/Action | Application Insights のエクスポート設定アクションです。 |
> | Action | Microsoft.Insights/Components/ExportConfiguration/Delete | Application Insights のエクスポート設定を削除します。 |
> | Action | Microsoft.Insights/Components/ExportConfiguration/Read | Application Insights のエクスポート設定を読み取ります。 |
> | Action | Microsoft.Insights/Components/ExportConfiguration/Write | Application Insights のエクスポート設定を書き込みます。 |
> | Action | Microsoft.Insights/Components/ExtendQueries/Read | Application Insights コンポーネントの拡張クエリの結果を読み取ります。 |
> | Action | Microsoft.Insights/Components/Favorites/Delete | Application Insights のお気に入りを削除します。 |
> | Action | Microsoft.Insights/Components/Favorites/Read | Application Insights のお気に入りを読み取ります。 |
> | Action | Microsoft.Insights/Components/Favorites/Write | Application Insights のお気に入りを書き込みます。 |
> | Action | Microsoft.Insights/Components/FeatureCapabilities/Read | Application Insights コンポーネントの機能を読み取ります。 |
> | Action | Microsoft.Insights/Components/GetAvailableBillingFeatures/Read | Application Insights コンポーネントで使用可能な請求機能を読み取ります。 |
> | Action | Microsoft.Insights/Components/GetToken/Read | Application Insights コンポーネントのトークンを読み取ります。 |
> | Action | Microsoft.Insights/Components/MetricDefinitions/Read | Application Insights コンポーネントのメトリック定義を読み取ります。 |
> | Action | Microsoft.Insights/Components/Metrics/Read | Application Insights コンポーネントのメトリックを読み取ります。 |
> | Action | Microsoft.Insights/Components/Move/Action | Application Insights コンポーネントを別のリソース グループまたはサブスクリプションに移動します。 |
> | Action | Microsoft.Insights/Components/MyAnalyticsItems/Delete | Application Insights の個人分析項目を削除します。 |
> | Action | Microsoft.Insights/Components/MyAnalyticsItems/Read | Application Insights の個人分析項目を読み取ります。 |
> | Action | Microsoft.Insights/Components/MyAnalyticsItems/Write | Application Insights の個人分析項目を書き込みます。 |
> | Action | Microsoft.Insights/Components/MyFavorites/Read | Application Insights の個人のお気に入りを読み取ります。 |
> | Action | Microsoft.Insights/Components/Operations/Read | Application Insights での実行時間の長い操作の状態を取得します。 |
> | Action | Microsoft.Insights/Components/PricingPlans/Read | Application Insights コンポーネントの価格プランを読み取ります。 |
> | Action | Microsoft.Insights/Components/PricingPlans/Write | Application Insights コンポーネントの価格プランを書き込みます。 |
> | Action | Microsoft.Insights/Components/ProactiveDetectionConfigs/Read | Application Insights のプロアクティブな検出構成を読み取ります。 |
> | Action | Microsoft.Insights/Components/ProactiveDetectionConfigs/Write | Application Insights のプロアクティブな検出構成を書き込みます。 |
> | Action | Microsoft.Insights/Components/providers/Microsoft.Insights/MetricDefinitions/Read | メトリック定義を読み取ります。 |
> | Action | Microsoft.Insights/Components/Purge/Action | Application Insights からデータを削除します。 |
> | Action | Microsoft.Insights/Components/Query/Read | Application Insights ログに対してクエリを実行します。 |
> | Action | Microsoft.Insights/Components/QuotaStatus/Read | Application Insights コンポーネントのクォータの状態を読み取ります。 |
> | Action | Microsoft.Insights/Components/Read | Application Insights コンポーネントの構成を読み取ります。 |
> | Action | Microsoft.Insights/Components/SyntheticMonitorLocations/Read | Application Insights の Web サイトの場所を読み取ります。 |
> | Action | Microsoft.Insights/Components/Webtests/Read | Web テストの構成を読み取ります。 |
> | Action | Microsoft.Insights/Components/WorkItemConfigs/Delete | Application Insights の ALM 統合構成を削除します。 |
> | Action | Microsoft.Insights/Components/WorkItemConfigs/Read | Application Insights の ALM 統合構成を読み取ります。 |
> | Action | Microsoft.Insights/Components/WorkItemConfigs/Write | Application Insights の ALM 統合構成を書き込みます。 |
> | Action | Microsoft.Insights/Components/Write | Application Insights コンポーネントの構成を書き込みます。 |
> | Action | Microsoft.Insights/DiagnosticSettings/Delete | リソースの診断設定を削除します |
> | Action | Microsoft.Insights/DiagnosticSettings/Read | リソースの診断設定を読み取ります |
> | Action | Microsoft.Insights/DiagnosticSettings/Write | リソースの診断設定を作成または更新します |
> | Action | Microsoft.Insights/EventCategories/Read | 利用可能なアクティビティ ログのイベント カテゴリを読み取ります |
> | Action | Microsoft.Insights/eventtypes/digestevents/Read | イベントの種類の管理のダイジェストを読み取ります。 |
> | Action | Microsoft.Insights/eventtypes/values/Read | アクティビティ ログのイベントを読み取ります |
> | Action | Microsoft.Insights/ExtendedDiagnosticSettings/Delete | ネットワーク フロー ログの診断設定を削除します |
> | Action | Microsoft.Insights/ExtendedDiagnosticSettings/Read | ネットワーク フロー ログの診断設定を読み取ります |
> | Action | Microsoft.Insights/ExtendedDiagnosticSettings/Write | ネットワーク フロー ログの診断設定を作成または更新します |
> | Action | Microsoft.Insights/ListMigrationDate/Action | サブスクリプション移行の日付を取得します。 |
> | Action | Microsoft.Insights/ListMigrationDate/Read | サブスクリプション移行の日付を取得します。 |
> | Action | Microsoft.Insights/LogDefinitions/Read | ログ定義を読み取ります。 |
> | Action | Microsoft.Insights/LogProfiles/Delete | アクティビティ ログのログ プロファイルを削除します |
> | Action | Microsoft.Insights/LogProfiles/Read | アクティビティ ログのログ プロファイルを読み取ります |
> | Action | Microsoft.Insights/LogProfiles/Write | アクティビティ ログのログ プロファイルを作成または更新します |
> | Action | Microsoft.Insights/MetricAlerts/Delete | メトリック アラートを削除します |
> | Action | Microsoft.Insights/MetricAlerts/Read | メトリック アラートを読み取ります |
> | Action | Microsoft.Insights/MetricAlerts/Status/Read | メトリック アラートの状態を読み取ります |
> | Action | Microsoft.Insights/MetricAlerts/Write | メトリック アラートを作成または更新します |
> | Action | Microsoft.Insights/MetricDefinitions/Microsoft.Insights/Read | メトリック定義を読み取ります。 |
> | Action | Microsoft.Insights/MetricDefinitions/providers/Microsoft.Insights/Read | メトリック定義を読み取ります。 |
> | Action | Microsoft.Insights/MetricDefinitions/Read | メトリック定義を読み取ります。 |
> | Action | Microsoft.Insights/Metrics/providers/Metrics/Read | メトリックを読み取ります。 |
> | Action | Microsoft.Insights/Metrics/Read | メトリックを読み取ります。 |
> | DataAction | Microsoft.Insights/Metrics/Write | メトリックを書き込みます。 |
> | Action | Microsoft.Insights/MigrateToNewpricingModel/Action | 新しい価格モデルにサブスクリプションを移行します。 |
> | Action | Microsoft.Insights/Operations/Read | 操作を読み取ります。 |
> | Action | Microsoft.Insights/Register/Action | Microsoft Insights プロバイダーを登録します。 |
> | Action | Microsoft.Insights/RollbackToLegacyPricingModel/Action | 従来の価格モデルにサブスクリプションをロールバックします。 |
> | Action | Microsoft.Insights/ScheduledQueryRules/Delete | スケジュール済みのクエリ ルールを削除します。 |
> | Action | Microsoft.Insights/ScheduledQueryRules/Read | スケジュール済みのクエリ ルールを読み取ります。 |
> | Action | Microsoft.Insights/ScheduledQueryRules/Write | スケジュール済みのクエリ ルールを書き込みます。 |
> | Action | Microsoft.Insights/Tenants/Register/Action | Microsoft Insights プロバイダーを初期化します |
> | Action | Microsoft.Insights/Unregister/Action | Microsoft Insights プロバイダーを登録します。 |
> | Action | Microsoft.Insights/Webtests/Delete | Web テストの構成を削除します。 |
> | Action | Microsoft.Insights/Webtests/GetToken/Read | Web テストのトークンを読み取ります。 |
> | Action | Microsoft.Insights/Webtests/MetricDefinitions/Read | Web テストのメトリック定義を読み取ります。 |
> | Action | Microsoft.Insights/Webtests/Metrics/Read | Web テストのメトリックを読み取ります。 |
> | Action | Microsoft.Insights/Webtests/Read | Web テストの構成を読み取ります。 |
> | Action | Microsoft.Insights/Webtests/Write | Web テストの構成を書き込みます。 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.KeyVault/checkNameAvailability/read | Key Vault 名が有効であり、使用されていないことを確認します。 |
> | Action | Microsoft.KeyVault/deletedVaults/read | 論理的に削除された Key Vault のプロパティを表示します。 |
> | Action | Microsoft.KeyVault/hsmPools/delete | HSM プールを削除します。 |
> | Action | Microsoft.KeyVault/hsmPools/joinVault/action | HSM プールにキー コンテナーを結合します。 |
> | Action | Microsoft.KeyVault/hsmPools/read | HSM プールのプロパティを表示します。 |
> | Action | Microsoft.KeyVault/hsmPools/write | 新しい HSM プールを作成するか、既存の HSM プールのプロパティを更新します。 |
> | Action | Microsoft.KeyVault/locations/deletedVaults/purge/action | 論理的に削除された Key Vault を消去します。 |
> | Action | Microsoft.KeyVault/locations/deletedVaults/read | 論理的に削除された Key Vault のプロパティを表示します。 |
> | Action | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | 仮想ネットワークまたはサブネットが削除されていることを Microsoft.KeyVault に通知します。 |
> | Action | Microsoft.KeyVault/locations/operationResults/read | 長時間実行された操作の結果を確認します。 |
> | Action | Microsoft.KeyVault/operations/read | Microsoft.KeyVault リソース プロバイダーで使用できる操作を一覧表示します。 |
> | Action | Microsoft.KeyVault/register/action | サブスクリプションを登録します。 |
> | Action | Microsoft.KeyVault/unregister/action | サブスクリプションの登録を解除します。 |
> | Action | Microsoft.KeyVault/vaults/accessPolicies/write | 既存のアクセス ポリシーをマージまたは置換して更新するか、資格情報コンテナーに新しいアクセス ポリシーを追加します。 |
> | Action | Microsoft.KeyVault/vaults/delete | Key Vault を削除します。 |
> | Action | Microsoft.KeyVault/vaults/deploy/action | Azure リソースのデプロイ時に、Key Vault 内のシークレットにアクセスできるようにします。 |
> | Action | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/diagnosticSettings/Read | リソースの診断設定を取得します。 |
> | Action | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/diagnosticSettings/Write | リソースの診断設定を作成または更新します。 |
> | Action | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/logDefinitions/read | キー コンテナーで使用可能なログを取得します。 |
> | Action | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/metricDefinitions/read | キー コンテナーで使用可能なメトリックを取得します。 |
> | Action | Microsoft.KeyVault/vaults/read | Key Vault のプロパティを表示します。 |
> | Action | Microsoft.KeyVault/vaults/secrets/read | シークレットの値ではなく、プロパティを表示します。 |
> | Action | Microsoft.KeyVault/vaults/secrets/write | 新しいシークレットを作成するか、既存のシークレットの値を更新します。 |
> | Action | Microsoft.KeyVault/vaults/write | 新しい Key Vault を作成するか、既存の Key Vault のプロパティを更新します。 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.Kusto/Clusters/Databases/delete | データベース リソースを削除します。 |
> | Action | Microsoft.Kusto/Clusters/Databases/EventHubConnections/delete | イベント ハブの接続リソースを削除します。 |
> | Action | Microsoft.Kusto/Clusters/Databases/EventHubConnections/read | イベント ハブの接続リソースを読み取ります。 |
> | Action | Microsoft.Kusto/Clusters/Databases/EventHubConnections/write | イベント ハブの接続リソースを書き込みます。 |
> | Action | Microsoft.Kusto/Clusters/Databases/read | データベース リソースを読み取ります。 |
> | Action | Microsoft.Kusto/Clusters/Databases/write | データベース リソースを書き込みます。 |
> | Action | Microsoft.Kusto/Clusters/delete | クラスター リソースを削除します。 |
> | Action | Microsoft.Kusto/Clusters/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Action | Microsoft.Kusto/Clusters/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Action | Microsoft.Kusto/Clusters/providers/Microsoft.Insights/metricDefinitions/read | リソースのメトリック定義を取得します |
> | Action | Microsoft.Kusto/Clusters/read | クラスター リソースを読み取ります。 |
> | Action | Microsoft.Kusto/Clusters/write | クラスター リソースを書き込みます。 |
> | Action | Microsoft.Kusto/Locations/CheckNameAvailability/write | 名前を使用できるかどうかの確認のリソースを読み取ります |
> | Action | Microsoft.Kusto/locations/operationresults/read | 操作のリソースを読み取ります |
> | Action | Microsoft.Kusto/Operations/read | 操作のリソースを読み取ります |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.LabServices/labAccounts/CreateLab/action | ラボ アカウントにラボを作成します。 |
> | Action | Microsoft.LabServices/labAccounts/delete | ラボ アカウントを削除します。 |
> | Action | Microsoft.LabServices/labAccounts/galleryImages/delete | ギャラリー イメージを削除します。 |
> | Action | Microsoft.LabServices/labAccounts/galleryImages/read | ギャラリー イメージを読み取ります。 |
> | Action | Microsoft.LabServices/labAccounts/galleryImages/write | ギャラリー イメージを追加または変更します。 |
> | Action | Microsoft.LabServices/labAccounts/GetRegionalAvailability/action | ラボ アカウントの下で構成されたサイズ カテゴリ別のリージョン別の提供状況を取得します。 |
> | Action | Microsoft.LabServices/labAccounts/labs/delete | ラボを削除します。 |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/ClaimAny/action | 環境設定でユーザーのランダム環境を要求します。 |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | 環境の設定を削除します。 |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Claim/action | 環境を要求し、それをユーザーに割り当てます。 |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | 環境を削除します。 |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | 環境を読み取ります。 |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | 環境内のすべてのリソースを開始して環境を開始します。 |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | 環境内のすべてのリソースを停止して環境を停止します。 |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/write | 環境を追加または変更します。 |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | ラボ/環境設定の現在の状態に基づいて環境設定に必要なリソースをプロビジョニング/プロビジョニング解除します。 |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | 環境の設定を読み取ります。 |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | テンプレート内のすべてのリソースを開始してテンプレートを開始します。 |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | テンプレート内のすべてのリソースを開始してテンプレートを開始します。 |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | 環境の設定を追加または変更します。 |
> | Action | Microsoft.LabServices/labAccounts/labs/read | ラボを読み取ります。 |
> | Action | Microsoft.LabServices/labAccounts/labs/Register/action | マネージド ラボに登録します。 |
> | Action | Microsoft.LabServices/labAccounts/labs/users/delete | ユーザーを削除します。 |
> | Action | Microsoft.LabServices/labAccounts/labs/users/read | ユーザーを読み取ります。 |
> | Action | Microsoft.LabServices/labAccounts/labs/users/write | ユーザーを追加または変更します。 |
> | Action | Microsoft.LabServices/labAccounts/labs/write | ラボを追加または変更します。 |
> | Action | Microsoft.LabServices/labAccounts/read | ラボ アカウントを読み取ります。 |
> | Action | Microsoft.LabServices/labAccounts/sizes/GetRegionalAvailability/action | ラボ アカウントの下で構成されたサイズ カテゴリ別のリージョン別の提供状況を取得します。 |
> | Action | Microsoft.LabServices/labAccounts/sizes/read | サイズを読み取ります。 |
> | Action | Microsoft.LabServices/labAccounts/sizes/ResetOverrides/action | このラボ アカウントのサイズ構成を既定値に戻します |
> | Action | Microsoft.LabServices/labAccounts/write | ラボ アカウントを追加または変更します。 |
> | Action | Microsoft.LabServices/locations/operations/read | 操作を読み取ります。 |
> | Action | Microsoft.LabServices/register/action | サブスクリプションを登録します。 |
> | Action | Microsoft.LabServices/users/GetEnvironment/action | 仮想マシンの詳細を取得します。 |
> | Action | Microsoft.LabServices/users/GetOperationBatchStatus/action | バッチの操作状態を取得します |
> | Action | Microsoft.LabServices/users/GetOperationStatus/action | 長時間実行操作の状態を取得します。 |
> | Action | Microsoft.LabServices/users/ListEnvironments/action | ユーザーの環境の一覧を取得します。 |
> | Action | Microsoft.LabServices/users/ListLabs/action | ユーザーのラボの一覧を取得します。 |
> | Action | Microsoft.LabServices/users/Register/action | マネージド ラボにユーザーを登録します。 |
> | Action | Microsoft.LabServices/users/StartEnvironment/action | 環境内のすべてのリソースを開始して環境を開始します。 |
> | Action | Microsoft.LabServices/users/StopEnvironment/action | 環境内のすべてのリソースを停止して環境を停止します。 |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.LocationBasedServices/accounts/delete | (非推奨: /providers/Microsoft.Maps を使用してください) Location Based Services アカウントを削除します。 |
> | Action | Microsoft.LocationBasedServices/accounts/listKeys/action | (非推奨: /providers/Microsoft.Maps を使用してください) Location Based Services アカウント キーの一覧を取得します。 |
> | Action | Microsoft.LocationBasedServices/accounts/providers/Microsoft.Insights/diagnosticSettings/read | (非推奨: /providers/Microsoft.Maps を使用してください) リソースの診断設定を取得します。 |
> | Action | Microsoft.LocationBasedServices/accounts/providers/Microsoft.Insights/diagnosticSettings/write | (非推奨: /providers/Microsoft.Maps を使用してください) リソースの診断設定を作成または更新します。 |
> | Action | Microsoft.LocationBasedServices/accounts/providers/Microsoft.Insights/metricDefinitions/read | (非推奨: /providers/Microsoft.Maps を使用してください) Location Based Services アカウントに使用できるメトリックを取得します。 |
> | Action | Microsoft.LocationBasedServices/accounts/read | (非推奨: /providers/Microsoft.Maps を使用してください) Location Based Services アカウントを取得します。 |
> | Action | Microsoft.LocationBasedServices/accounts/regenerateKey/action | (非推奨: /providers/Microsoft.Maps を使用してください) Location Based Services アカウントの新しい主キーまたは 2 次キーを生成します。 |
> | Action | Microsoft.LocationBasedServices/accounts/write | (非推奨: /providers/Microsoft.Maps を使用してください) Location Based Services アカウントを作成または更新します。 |
> | Action | Microsoft.LocationBasedServices/register/action | (非推奨: /providers/Microsoft.Maps を使用してください) プロバイダーを登録します。 |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.LocationServices/accounts/delete | (非推奨: /providers/Microsoft.Maps を使用してください) Location Services アカウントを削除します。 |
> | Action | Microsoft.LocationServices/accounts/listKeys/action | (非推奨: /providers/Microsoft.Maps を使用してください) Location Based Services アカウント キーの一覧を取得します。 |
> | Action | Microsoft.LocationServices/accounts/providers/Microsoft.Insights/diagnosticSettings/read | (非推奨: /providers/Microsoft.Maps を使用してください) リソースの診断設定を取得します。 |
> | Action | Microsoft.LocationServices/accounts/providers/Microsoft.Insights/diagnosticSettings/write | (非推奨: /providers/Microsoft.Maps を使用してください) リソースの診断設定を作成または更新します。 |
> | Action | Microsoft.LocationServices/accounts/providers/Microsoft.Insights/metricDefinitions/read | (非推奨: /providers/Microsoft.Maps を使用してください) Location Based Services アカウントに使用できるメトリックを取得します。 |
> | Action | Microsoft.LocationServices/accounts/read | (非推奨: /providers/Microsoft.Maps を使用してください) Location Services アカウントを取得します。 |
> | Action | Microsoft.LocationServices/accounts/regenerateKey/action | (非推奨: /providers/Microsoft.Maps を使用してください) Location Based Services アカウントの新しい主キーまたは 2 次キーを生成します。 |
> | Action | Microsoft.LocationServices/accounts/write | (非推奨: /providers/Microsoft.Maps を使用してください) Location Services アカウントを作成または更新します。 |
> | Action | Microsoft.LocationServices/register/action | (非推奨: /providers/Microsoft.Maps を使用してください) プロバイダーを登録します。 |

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | DataAction | Microsoft.LogAnalytics/logs/ADAssessmentRecommendation/read | ADAssessmentRecommendation テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/ADReplicationResult/read | ADReplicationResult テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/ADSecurityAssessmentRecommendation/read | ADSecurityAssessmentRecommendation テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/Alert/read | Alert テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/AlertHistory/read | AlertHistory テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/ApplicationInsights/read | ApplicationInsights テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/AzureActivity/read | AzureActivity テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/AzureMetrics/read | AzureMetrics テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/BoundPort/read | BoundPort テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/CommonSecurityLog/read | CommonSecurityLog テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/ComputerGroup/read | ComputerGroup テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/ConfigurationChange/read | ConfigurationChange テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/ConfigurationData/read | ConfigurationData テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/ContainerImageInventory/read | ContainerImageInventory テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/ContainerInventory/read | ContainerInventory テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/ContainerLog/read | ContainerLog テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/ContainerServiceLog/read | ContainerServiceLog テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/CustomLogs/read | カスタム ログからデータを読み取っています。 |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceAppCrash/read | DeviceAppCrash テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceAppLaunch/read | DeviceAppLaunch テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceCalendar/read | DeviceCalendar テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceCleanup/read | DeviceCleanup テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceConnectSession/read | DeviceConnectSession テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceEtw/read | DeviceEtw テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceHardwareHealth/read | DeviceHardwareHealth テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceHealth/read | DeviceHealth テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceHeartbeat/read | DeviceHeartbeat テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceSkypeHeartbeat/read | DeviceSkypeHeartbeat テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceSkypeSignIn/read | DeviceSkypeSignIn テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceSleepState/read | DeviceSleepState テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/DHAppFailure/read | DHAppFailure テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/DHAppReliability/read | DHAppReliability テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/DHDriverReliability/read | DHDriverReliability テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/DHLogonFailures/read | DHLogonFailures テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/DHLogonMetrics/read | DHLogonMetrics テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/DHOSCrashData/read | DHOSCrashData テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/DHOSReliability/read | DHOSReliability テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/DHWipAppLearning/read | DHWipAppLearning テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/DnsEvents/read | DnsEvents テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/DnsInventory/read | DnsInventory テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/ETWEvent/read | ETWEvent テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/Event/read | Event テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/ExchangeAssessmentRecommendation/read | ExchangeAssessmentRecommendation テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/ExchangeOnlineAssessmentRecommendation/read | ExchangeOnlineAssessmentRecommendation テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/Heartbeat/read | Heartbeat テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/IISAssessmentRecommendation/read | IISAssessmentRecommendation テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/InboundConnection/read | InboundConnection テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/KubeNodeInventory/read | KubeNodeInventory テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/KubePodInventory/read | KubePodInventory テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/LinuxAuditLog/read | LinuxAuditLog テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplication/read | MAApplication テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationHealth/read | MAApplicationHealth テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationHealthAlternativeVersions/read | MAApplicationHealthAlternativeVersions テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationHealthIssues/read | MAApplicationHealthIssues テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationInstance/read | MAApplicationInstance テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationInstanceReadiness/read | MAApplicationInstanceReadiness テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationReadiness/read | MAApplicationReadiness テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MADeploymentPlan/read | MADeploymentPlan テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MADevice/read | MADevice テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MADevicePnPHealth/read | MADevicePnPHealth テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MADevicePnPHealthAlternativeVersions/read | MADevicePnPHealthAlternativeVersions テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MADevicePnPHealthIssues/read | MADevicePnPHealthIssues テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MADeviceReadiness/read | MADeviceReadiness テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MADriverInstanceReadiness/read | MADriverInstanceReadiness テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MADriverReadiness/read | MADriverReadiness テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddin/read | MAOfficeAddin テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinHealth/read | MAOfficeAddinHealth テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinHealthIssues/read | MAOfficeAddinHealthIssues テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinInstance/read | MAOfficeAddinInstance テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinInstanceReadiness/read | MAOfficeAddinInstanceReadiness テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinReadiness/read | MAOfficeAddinReadiness テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeApp/read | MAOfficeApp テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAppHealth/read | MAOfficeAppHealth テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAppInstance/read | MAOfficeAppInstance テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAppReadiness/read | MAOfficeAppReadiness テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeBuildInfo/read | MAOfficeBuildInfo テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeCurrencyAssessment/read | MAOfficeCurrencyAssessment テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeCurrencyAssessmentDailyCounts/read | MAOfficeCurrencyAssessmentDailyCounts テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeDeploymentStatus/read | MAOfficeDeploymentStatus テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroHealth/read | MAOfficeMacroHealth テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroHealthIssues/read | MAOfficeMacroHealthIssues テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroIssueInstanceReadiness/read | MAOfficeMacroIssueInstanceReadiness テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroIssueReadiness/read | MAOfficeMacroIssueReadiness テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroSummary/read | MAOfficeMacroSummary テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeSuite/read | MAOfficeSuite テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeSuiteInstance/read | MAOfficeSuiteInstance テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MAProposedPilotDevices/read | MAProposedPilotDevices テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MAWindowsBuildInfo/read | MAWindowsBuildInfo テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MAWindowsCurrencyAssessment/read | MAWindowsCurrencyAssessment テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MAWindowsCurrencyAssessmentDailyCounts/read | MAWindowsCurrencyAssessmentDailyCounts テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MAWindowsDeploymentStatus/read | MAWindowsDeploymentStatus テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/MAWindowsSysReqInstanceReadiness/read | MAWindowsSysReqInstanceReadiness テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/NetworkMonitoring/read | NetworkMonitoring テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/OfficeActivity/read | OfficeActivity テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/Operation/read | Operation テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/OutboundConnection/read | OutboundConnection テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/Perf/read | Perf テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/ProtectionStatus/read | ProtectionStatus テーブルからデータを読み取ります。 |
> | Action | Microsoft.LogAnalytics/logs/read | すべてのログからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/ReservedAzureCommonFields/read | ReservedAzureCommonFields テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/ReservedCommonFields/read | ReservedCommonFields テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/SCCMAssessmentRecommendation/read | SCCMAssessmentRecommendation テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/SCOMAssessmentRecommendation/read | SCOMAssessmentRecommendation テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityAlert/read | SecurityAlert テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityBaseline/read | SecurityBaseline テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityBaselineSummary/read | SecurityBaselineSummary テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityDetection/read | SecurityDetection テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityEvent/read | SecurityEvent テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/ServiceFabricOperationalEvent/read | ServiceFabricOperationalEvent テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/ServiceFabricReliableActorEvent/read | ServiceFabricReliableActorEvent テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/ServiceFabricReliableServiceEvent/read | ServiceFabricReliableServiceEvent テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/SfBAssessmentRecommendation/read | SfBAssessmentRecommendation テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/SfBOnlineAssessmentRecommendation/read | SfBOnlineAssessmentRecommendation テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/SharePointOnlineAssessmentRecommendation/read | SharePointOnlineAssessmentRecommendation テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/SPAssessmentRecommendation/read | SPAssessmentRecommendation テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/SQLAssessmentRecommendation/read | SQLAssessmentRecommendation テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/SQLQueryPerformance/read | SQLQueryPerformance テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/Syslog/read | Syslog テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/SysmonEvent/read | SysmonEvent テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/UAApp/read | UAApp テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/UAComputer/read | UAComputer テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/UAComputerRank/read | UAComputerRank テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/UADriver/read | UADriver テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/UADriverProblemCodes/read | UADriverProblemCodes テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/UAFeedback/read | UAFeedback テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/UAHardwareSecurity/read | UAHardwareSecurity テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/UAIESiteDiscovery/read | UAIESiteDiscovery テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/UAOfficeAddIn/read | UAOfficeAddIn テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/UAProposedActionPlan/read | UAProposedActionPlan テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/UASysReqIssue/read | UASysReqIssue テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/UAUpgradedComputer/read | UAUpgradedComputer テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/Update/read | Update テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/UpdateRunProgress/read | UpdateRunProgress テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/UpdateSummary/read | UpdateSummary テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/Usage/read | Usage テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/VMBoundPort/read | VMBoundPort テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/VMConnection/read | VMConnection テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/W3CIISLog/read | W3CIISLog テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/WaaSDeploymentStatus/read | WaaSDeploymentStatus テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/WaaSInsiderStatus/read | WaaSInsiderStatus テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/WaaSUpdateStatus/read | WaaSUpdateStatus テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/WDAVStatus/read | WDAVStatus テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/WDAVThreat/read | WDAVThreat テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/WindowsClientAssessmentRecommendation/read | WindowsClientAssessmentRecommendation テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/WindowsFirewall/read | WindowsFirewall テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/WindowsServerAssessmentRecommendation/read | WindowsServerAssessmentRecommendation テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/WireData/read | WireData テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/WUDOAggregatedStatus/read | WUDOAggregatedStatus テーブルからデータを読み取ります。 |
> | DataAction | Microsoft.LogAnalytics/logs/WUDOStatus/read | WUDOStatus テーブルからデータを読み取ります。 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.Logic/integrationAccounts/agreements/delete | 統合アカウントの契約を削除します。 |
> | Action | Microsoft.Logic/integrationAccounts/agreements/listContentCallbackUrl/action | 統合アカウントの契約内容のコールバック URL を取得します。 |
> | Action | Microsoft.Logic/integrationAccounts/agreements/read | 統合アカウントの契約を読み取ります。 |
> | Action | Microsoft.Logic/integrationAccounts/agreements/write | 統合アカウントの契約を作成または更新します。 |
> | Action | Microsoft.Logic/integrationAccounts/assemblies/delete | 統合アカウントのアセンブリを削除します。 |
> | Action | Microsoft.Logic/integrationAccounts/assemblies/listContentCallbackUrl/action | 統合アカウントのアセンブリ内容のコールバック URL を取得します。 |
> | Action | Microsoft.Logic/integrationAccounts/assemblies/read | 統合アカウントのアセンブリを読み取ります。 |
> | Action | Microsoft.Logic/integrationAccounts/assemblies/write | 統合アカウントのアセンブリを作成または更新します。 |
> | Action | Microsoft.Logic/integrationAccounts/batchConfigurations/delete | 統合アカウントのバッチ構成を削除します。 |
> | Action | Microsoft.Logic/integrationAccounts/batchConfigurations/read | 統合アカウントのバッチ構成を読み取ります。 |
> | Action | Microsoft.Logic/integrationAccounts/batchConfigurations/write | 統合アカウントのバッチ構成を作成または更新します。 |
> | Action | Microsoft.Logic/integrationAccounts/certificates/delete | 統合アカウントの証明書を削除します。 |
> | Action | Microsoft.Logic/integrationAccounts/certificates/read | 統合アカウントの証明書を読み取ります。 |
> | Action | Microsoft.Logic/integrationAccounts/certificates/write | 統合アカウントの証明書を作成または更新します。 |
> | Action | Microsoft.Logic/integrationAccounts/delete | 統合アカウントを削除します。 |
> | Action | Microsoft.Logic/integrationAccounts/join/action | 統合アカウントを結合します。 |
> | Action | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | 統合アカウントのコールバック URL を取得します。 |
> | Action | Microsoft.Logic/integrationAccounts/listKeyVaultKeys/action | キー コンテナーのキーを取得します。 |
> | Action | Microsoft.Logic/integrationAccounts/logTrackingEvents/action | 統合アカウントでの追跡イベントを記録します。 |
> | Action | Microsoft.Logic/integrationAccounts/maps/delete | 統合アカウントのマップを削除します。 |
> | Action | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | 統合アカウントのマップ内容のコールバック URL を取得します。 |
> | Action | Microsoft.Logic/integrationAccounts/maps/read | 統合アカウントのマップを読み取ります。 |
> | Action | Microsoft.Logic/integrationAccounts/maps/write | 統合アカウントのマップを作成または更新します。 |
> | Action | Microsoft.Logic/integrationAccounts/partners/delete | 統合アカウントのパートナーを削除します。 |
> | Action | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | 統合アカウントのパートナー内容のコールバック URL を取得します。 |
> | Action | Microsoft.Logic/integrationAccounts/partners/read | 統合アカウントのパートナーを読み取ります。 |
> | Action | Microsoft.Logic/integrationAccounts/partners/write | 統合アカウントのパートナーを作成または更新します。 |
> | Action | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | 統合アカウントのログ定義を読み取ります。 |
> | Action | Microsoft.Logic/integrationAccounts/read | 統合アカウントを読み取ります。 |
> | Action | Microsoft.Logic/integrationAccounts/regenerateAccessKey/action | アクセス キーのシークレットを再生成します。 |
> | Action | Microsoft.Logic/integrationAccounts/schemas/delete | 統合アカウントのスキーマを削除します。 |
> | Action | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | 統合アカウントのスキーマ内容のコールバック URL を取得します。 |
> | Action | Microsoft.Logic/integrationAccounts/schemas/read | 統合アカウントのスキーマを読み取ります。 |
> | Action | Microsoft.Logic/integrationAccounts/schemas/write | 統合アカウントのスキーマを作成または更新します。 |
> | Action | Microsoft.Logic/integrationAccounts/sessions/delete | 統合アカウントのセッションを削除します。 |
> | Action | Microsoft.Logic/integrationAccounts/sessions/read | 統合アカウントのバッチ構成を読み取ります。 |
> | Action | Microsoft.Logic/integrationAccounts/sessions/write | 統合アカウントのセッションを作成または更新します。 |
> | Action | Microsoft.Logic/integrationAccounts/write | 統合アカウントを作成または更新します。 |
> | Action | Microsoft.Logic/integrationServiceEnvironments/delete | 統合サービス環境を削除します。 |
> | Action | Microsoft.Logic/integrationServiceEnvironments/join/action | 統合サービス環境に参加します。 |
> | Action | Microsoft.Logic/integrationServiceEnvironments/read | 統合サービス環境を読み取ります。 |
> | Action | Microsoft.Logic/integrationServiceEnvironments/write | 統合サービス環境を作成または更新します。 |
> | Action | Microsoft.Logic/locations/workflows/validate/action | ワークフローを検証します。 |
> | Action | Microsoft.Logic/operations/read | 操作を取得します。 |
> | Action | Microsoft.Logic/register/action | 指定されたサブスクリプションを Microsoft.Logic リソース プロバイダーに登録します。 |
> | Action | Microsoft.Logic/workflows/accessKeys/delete | アクセス キーを削除します。 |
> | Action | Microsoft.Logic/workflows/accessKeys/list/action | アクセス キーのシークレットを一覧表示します。 |
> | Action | Microsoft.Logic/workflows/accessKeys/read | アクセス キーを読み取ります。 |
> | Action | Microsoft.Logic/workflows/accessKeys/regenerate/action | アクセス キーのシークレットを再生成します。 |
> | Action | Microsoft.Logic/workflows/accessKeys/write | アクセス キーを作成または更新します。 |
> | Action | Microsoft.Logic/workflows/delete | ワークフローを削除します。 |
> | Action | Microsoft.Logic/workflows/disable/action | ワークフローを無効にします。 |
> | Action | Microsoft.Logic/workflows/enable/action | ワークフローを有効にします。 |
> | Action | Microsoft.Logic/workflows/listCallbackUrl/action | ワークフローのコールバック URL を取得します。 |
> | Action | Microsoft.Logic/workflows/listSwagger/action | ワークフローの Swagger 定義を取得します。 |
> | Action | Microsoft.Logic/workflows/move/action | ワークフローを既存のサブスクリプション ID、リソース グループ、または名前から、別のサブスクリプション ID、リソース グループ、または名前に移動します。 |
> | Action | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/read | ワークフローの診断設定を読み取ります。 |
> | Action | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/write | ワークフローの診断設定を作成または更新します。 |
> | Action | Microsoft.Logic/workflows/providers/Microsoft.Insights/logDefinitions/read | ワークフローのログ定義を読み取ります。 |
> | Action | Microsoft.Logic/workflows/providers/Microsoft.Insights/metricDefinitions/read | ワークフローのメトリック定義を読み取ります。 |
> | Action | Microsoft.Logic/workflows/read | ワークフローを読み取ります。 |
> | Action | Microsoft.Logic/workflows/regenerateAccessKey/action | アクセス キーのシークレットを再生成します。 |
> | Action | Microsoft.Logic/workflows/run/action | ワークフローの実行を開始します。 |
> | Action | Microsoft.Logic/workflows/runs/actions/listExpressionTraces/action | ワークフロー実行アクションの式のトレースを取得します。 |
> | Action | Microsoft.Logic/workflows/runs/actions/read | ワークフロー実行アクションを読み取ります。 |
> | Action | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | ワークフロー実行アクションの繰り返し式のトレースを取得します。 |
> | Action | Microsoft.Logic/workflows/runs/actions/repetitions/read | ワークフロー実行アクションの繰り返しを読み取ります。 |
> | Action | Microsoft.Logic/workflows/runs/actions/repetitions/requestHistories/read | ワークフロー実行の繰り返しアクション要求の履歴を読み取ります。 |
> | Action | Microsoft.Logic/workflows/runs/actions/requestHistories/read | ワークフロー実行アクション要求の履歴を読み取ります。 |
> | Action | Microsoft.Logic/workflows/runs/actions/scoperepetitions/read | ワークフロー実行アクションのスコープの繰り返しを読み取ります。 |
> | Action | Microsoft.Logic/workflows/runs/cancel/action | ワークフローの実行を取り消します。 |
> | Action | Microsoft.Logic/workflows/runs/operations/read | ワークフロー実行操作の状態を読み取ります。 |
> | Action | Microsoft.Logic/workflows/runs/read | ワークフロー実行を読み取ります。 |
> | Action | Microsoft.Logic/workflows/suspend/action | ワークフローを一時停止します。 |
> | Action | Microsoft.Logic/workflows/triggers/histories/read | トリガーの履歴を読み取ります。 |
> | Action | Microsoft.Logic/workflows/triggers/histories/resubmit/action | ワークフロー トリガーを再送信します。 |
> | Action | Microsoft.Logic/workflows/triggers/listCallbackUrl/action | トリガーのコールバック URL を取得します。 |
> | Action | Microsoft.Logic/workflows/triggers/read | トリガーを読み取ります。 |
> | Action | Microsoft.Logic/workflows/triggers/reset/action | トリガーをリセットします。 |
> | Action | Microsoft.Logic/workflows/triggers/run/action | トリガーを実行します。 |
> | Action | Microsoft.Logic/workflows/triggers/setState/action | トリガーの状態を設定します。 |
> | Action | Microsoft.Logic/workflows/validate/action | ワークフローを検証します。 |
> | Action | Microsoft.Logic/workflows/versions/read | ワークフローのバージョンを読み取ります。 |
> | Action | Microsoft.Logic/workflows/versions/triggers/listCallbackUrl/action | トリガーのコールバック URL を取得します。 |
> | Action | Microsoft.Logic/workflows/write | ワークフローを作成または更新します。 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/move/action | Machine Learning コミットメント プランの関連付けを移動します。 |
> | Action | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/read | Machine Learning コミットメント プランの関連付けを読み取ります。 |
> | Action | Microsoft.MachineLearning/commitmentPlans/delete | Machine Learning コミットメント プランを削除します。 |
> | Action | Microsoft.MachineLearning/commitmentPlans/join/action | Machine Learning コミットメント プランに加入します。 |
> | Action | Microsoft.MachineLearning/commitmentPlans/read | Machine Learning コミットメント プランを読み取ります。 |
> | Action | Microsoft.MachineLearning/commitmentPlans/write | Machine Learning コミットメント プランを作成または更新します。 |
> | Action | Microsoft.MachineLearning/locations/operationresults/read | Machine Learning 操作の結果を取得します。 |
> | Action | Microsoft.MachineLearning/locations/operationsstatus/read | 実行中の Machine Learning 操作の状態を取得します。 |
> | Action | Microsoft.MachineLearning/operations/read | Machine Learning 操作を取得します。 |
> | Action | Microsoft.MachineLearning/register/action | Machine Learning Web サービス リソース プロバイダーにサブスクリプションを登録し、Web サービスを作成できるようにします。 |
> | Action | Microsoft.MachineLearning/skus/read | Machine Learning コミットメント プラン SKU を取得します。 |
> | Action | Microsoft.MachineLearning/webServices/action | サポートされているリージョンのリージョン固有の Web サービス プロパティを作成します。 |
> | Action | Microsoft.MachineLearning/webServices/delete | Machine Learning Web サービスを削除します。 |
> | Action | Microsoft.MachineLearning/webServices/listkeys/read | Machine Learning Web サービスに対するキーを取得します。 |
> | Action | Microsoft.MachineLearning/webServices/read | Machine Learning Web サービスを読み取ります。 |
> | Action | Microsoft.MachineLearning/webServices/write | Machine Learning Web サービスを作成または更新します。 |
> | Action | Microsoft.MachineLearning/Workspaces/delete | Machine Learning ワークスペースを削除します。 |
> | Action | Microsoft.MachineLearning/Workspaces/listworkspacekeys/action | Machine Learning ワークスペースのキーを一覧表示します。 |
> | Action | Microsoft.MachineLearning/Workspaces/read | Machine Learning ワークスペースを読み取ります。 |
> | Action | Microsoft.MachineLearning/Workspaces/resyncstoragekeys/action | Machine Learning ワークスペース用に構成されたストレージ アカウントのキーを再同期します。 |
> | Action | Microsoft.MachineLearning/Workspaces/write | Machine Learning ワークスペースを作成または更新します。 |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.MachineLearningCompute/operationalizationClusters/checkUpdate/action | 運用化クラスターのシステム サービスの更新プログラムが使用可能かどうか確認します。 |
> | Action | Microsoft.MachineLearningCompute/operationalizationClusters/delete | ホスティング アカウントを削除します。 |
> | Action | Microsoft.MachineLearningCompute/operationalizationClusters/listKeys/action | 運用化クラスターに関連付けられているキーを一覧表示します。 |
> | Action | Microsoft.MachineLearningCompute/operationalizationClusters/read | ホスティング アカウントを読み取ります。 |
> | Action | Microsoft.MachineLearningCompute/operationalizationClusters/updateSystem/action | 運用化クラスターのシステム サービスを更新します。 |
> | Action | Microsoft.MachineLearningCompute/operationalizationClusters/write | ホスティング アカウントを作成または更新します。 |
> | Action | Microsoft.MachineLearningCompute/register/action | リソース プロバイダーにサブスクリプション ID を登録し、機械学習コンピューティング リソースを作成できるようにします。 |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.MachineLearningModelManagement/accounts/delete | ホスティング アカウントを削除します。 |
> | Action | Microsoft.MachineLearningModelManagement/accounts/read | ホスティング アカウントを読み取ります。 |
> | Action | Microsoft.MachineLearningModelManagement/accounts/write | ホスティング アカウントを作成または更新します。 |
> | Action | Microsoft.MachineLearningModelManagement/register/action | リソース プロバイダーにサブスクリプション ID を登録し、ホスティング アカウントを作成できるようにします。 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.MachineLearningServices/register/action | Machine Learning Services リソース プロバイダーのサブスクリプションを登録します。 |
> | Action | Microsoft.MachineLearningServices/workspaces/computes/delete | Machine Learning Services ワークスペースのコンピューティング リソースを削除します。 |
> | Action | Microsoft.MachineLearningServices/workspaces/computes/listKeys/action | Machine Learning Services ワークスペースのコンピューティング リソースについて、シークレットの一覧を取得します。 |
> | Action | Microsoft.MachineLearningServices/workspaces/computes/read | Machine Learning Services ワークスペースのコンピューティング リソースを取得します。 |
> | Action | Microsoft.MachineLearningServices/workspaces/computes/write | Machine Learning Services ワークスペースのコンピューティング リソースを作成または更新します。 |
> | Action | Microsoft.MachineLearningServices/workspaces/delete | Machine Learning Services ワークスペースを削除します。 |
> | Action | Microsoft.MachineLearningServices/workspaces/listKeys/action | Machine Learning Services ワークスペースのシークレットの一覧を取得します。 |
> | Action | Microsoft.MachineLearningServices/workspaces/read | Machine Learning Services ワークスペースを取得します。 |
> | Action | Microsoft.MachineLearningServices/workspaces/write | Machine Learning Services ワークスペースを作成または更新します。 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | 既存のユーザー割り当て ID をリソースに割り当てるための RBAC アクションです。 |
> | Action | Microsoft.ManagedIdentity/userAssignedIdentities/delete | 既存のユーザー割り当て ID を削除します。 |
> | Action | Microsoft.ManagedIdentity/userAssignedIdentities/read | 既存のユーザー割り当て ID を取得します。 |
> | Action | Microsoft.ManagedIdentity/userAssignedIdentities/write | 新しいユーザー割り当て ID を作成するか、既存のユーザー割り当て ID に関連付けられているタグを更新します。 |

## <a name="microsoftmanagedlab"></a>Microsoft.ManagedLab

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.ManagedLab/labAccounts/CreateLab/action | ラボ アカウントにラボを作成します。 |
> | Action | Microsoft.ManagedLab/labAccounts/delete | ラボ アカウントを削除します。 |
> | Action | Microsoft.ManagedLab/labAccounts/labs/delete | ラボを削除します。 |
> | Action | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/delete | 環境の設定を削除します。 |
> | Action | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/delete | 環境を削除します。 |
> | Action | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/read | 環境を読み取ります。 |
> | Action | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/write | 環境を追加または変更します。 |
> | Action | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/read | 環境の設定を読み取ります。 |
> | Action | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/write | 環境の設定を追加または変更します。 |
> | Action | Microsoft.ManagedLab/labAccounts/labs/labVms/delete | ラボ仮想マシンを削除します。 |
> | Action | Microsoft.ManagedLab/labAccounts/labs/labVms/read | ラボ仮想マシンを読み取ります。 |
> | Action | Microsoft.ManagedLab/labAccounts/labs/labVms/write | ラボ仮想マシンを追加または変更します。 |
> | Action | Microsoft.ManagedLab/labAccounts/labs/read | ラボを読み取ります。 |
> | Action | Microsoft.ManagedLab/labAccounts/labs/write | ラボを追加または変更します。 |
> | Action | Microsoft.ManagedLab/labAccounts/read | ラボ アカウントを読み取ります。 |
> | Action | Microsoft.ManagedLab/labAccounts/write | ラボ アカウントを追加または変更します。 |
> | Action | Microsoft.ManagedLab/locations/operations/read | 操作を読み取ります。 |
> | Action | Microsoft.ManagedLab/register/action | サブスクリプションを登録します。 |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.Management/checkNameAvailability/action | 指定された管理グループ名が有効で一意かどうかを確認します。 |
> | Action | Microsoft.Management/getEntities/action | 認証済みユーザーのすべてのエンティティ (管理グループ、サブスクリプションなど) を一覧表示します。 |
> | Action | Microsoft.Management/managementGroups/delete | 管理グループを削除します。 |
> | Action | Microsoft.Management/managementGroups/read | 認証済みユーザーの管理グループを一覧表示します。 |
> | Action | Microsoft.Management/managementGroups/subscriptions/delete | 管理グループからサブスクリプションの関連付けを解除します。 |
> | Action | Microsoft.Management/managementGroups/subscriptions/write | 既存のサブスクリプションと管理グループを関連付けます。 |
> | Action | Microsoft.Management/managementGroups/write | 管理グループを作成または更新します。 |
> | Action | Microsoft.Management/register/action | 指定したサブスクリプションを Microsoft.Management に登録します。 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.Maps/accounts/delete | Maps アカウントを削除します。 |
> | Action | Microsoft.Maps/accounts/listKeys/action | Maps アカウント キーの一覧を取得します。 |
> | Action | Microsoft.Maps/accounts/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Action | Microsoft.Maps/accounts/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Action | Microsoft.Maps/accounts/providers/Microsoft.Insights/metricDefinitions/read | Maps アカウントの使用可能なメトリックを取得します。 |
> | Action | Microsoft.Maps/accounts/read | Maps アカウントを取得します。 |
> | Action | Microsoft.Maps/accounts/regenerateKey/action | Maps アカウントの新しい主キーまたは 2 次キーを生成します。 |
> | Action | Microsoft.Maps/accounts/write | Maps アカウントを作成または更新します。 |
> | Action | Microsoft.Maps/register/action | プロバイダーに登録します。 |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | 契約を返します。 |
> | Action | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | 署名済みのライセンス契約を受け入れます。 |
> | Action | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | イメージをエンド ユーザーの ACR にインポートします。 |
> | Action | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | 構成を返します。 |
> | Action | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | 構成を保存します。 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.MarketplaceApps/ClassicDevServices/delete | クラシック開発サービス リソースに対して DELETE 操作を行います。 |
> | Action | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | クラシック開発サービス リソースの管理キーを取得します。 |
> | Action | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | クラシック開発サービスのシングル サインオン URL を取得します。 |
> | Action | Microsoft.MarketplaceApps/ClassicDevServices/read | クラシック開発サービスに対して GET 操作を行います。 |
> | Action | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | クラシック開発サービス リソースの管理キーを生成します。 |
> | Action | Microsoft.MarketplaceApps/Operations/read | すべてのリソースの種類の操作を読み取ります。 |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.MarketplaceOrdering/agreements/offers/plans/cancel/action | 指定された Marketplace の項目の契約を取り消します。 |
> | Action | Microsoft.MarketplaceOrdering/agreements/offers/plans/read | 指定された Marketplace の項目の契約を返します。 |
> | Action | Microsoft.MarketplaceOrdering/agreements/offers/plans/sign/action | 指定された Marketplace の項目の契約に署名します。 |
> | Action | Microsoft.MarketplaceOrdering/agreements/read | 指定されたサブスクリプションのすべての契約を返します。 |
> | Action | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/read | 指定された Marketplace 仮想マシン項目の契約を取得します。 |
> | Action | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | 指定された Marketplace 仮想マシン項目の契約を署名またはキャンセルします。 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.Media/locations/checkNameAvailability/action | Media Services のアカウント名が使用可能かどうかを確認します。 |
> | Action | Microsoft.Media/mediaservices/assets/delete | 資産を削除します。 |
> | Action | Microsoft.Media/mediaservices/assets/getEncryptionKey/action | 資産暗号化キーを取得します。 |
> | Action | Microsoft.Media/mediaservices/assets/listContainerSas/action | 資産コンテナーの SAS URL の一覧を取得します。 |
> | Action | Microsoft.Media/mediaservices/assets/read | アセンブリを読み取ります |
> | Action | Microsoft.Media/mediaservices/assets/write | 資産を作成または更新します。 |
> | Action | Microsoft.Media/mediaservices/contentKeyPolicies/delete | コンテンツ キー ポリシーを削除します。 |
> | Action | Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | ポリシーのプロパティとシークレットを取得します。 |
> | Action | Microsoft.Media/mediaservices/contentKeyPolicies/read | コンテンツ キー ポリシーを読み取ります。 |
> | Action | Microsoft.Media/mediaservices/contentKeyPolicies/write | コンテンツ キー ポリシーを作成または更新します。 |
> | Action | Microsoft.Media/mediaservices/delete | Media Services アカウントを削除します。 |
> | Action | Microsoft.Media/mediaservices/liveEventOperations/read | ライブ イベント操作を読み取ります。 |
> | Action | Microsoft.Media/mediaservices/liveEvents/delete | ライブ イベントを削除します。 |
> | Action | Microsoft.Media/mediaservices/liveEvents/liveOutputs/delete | ライブ出力を削除します。 |
> | Action | Microsoft.Media/mediaservices/liveEvents/liveOutputs/read | ライブ出力を読み取ります |
> | Action | Microsoft.Media/mediaservices/liveEvents/liveOutputs/write | ライブ出力を作成または更新します。 |
> | Action | Microsoft.Media/mediaservices/liveEvents/read | ライブ イベントを読み取ります |
> | Action | Microsoft.Media/mediaservices/liveEvents/reset/action | ライブ イベント操作をリセットします。 |
> | Action | Microsoft.Media/mediaservices/liveEvents/start/action | ライブ イベント操作を開始します。 |
> | Action | Microsoft.Media/mediaservices/liveEvents/stop/action | ライブ イベント操作を停止します。 |
> | Action | Microsoft.Media/mediaservices/liveEvents/write | ライブ イベントを作成または更新します。 |
> | Action | Microsoft.Media/mediaservices/liveOutputOperations/read | ライブ出力操作を読み取ります。 |
> | Action | Microsoft.Media/mediaservices/read | Media Services アカウントを読み取ります。 |
> | Action | Microsoft.Media/mediaservices/streamingEndpointOperations/read | ストリーミング エンドポイント操作を読み取ります。 |
> | Action | Microsoft.Media/mediaservices/streamingEndpoints/delete | ストリーミング エンドポイントを削除します。 |
> | Action | Microsoft.Media/mediaservices/streamingEndpoints/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Action | Microsoft.Media/mediaservices/streamingEndpoints/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Action | Microsoft.Media/mediaservices/streamingEndpoints/providers/Microsoft.Insights/metricDefinitions/read | Media Services ストリーミング エンドポイントのメトリック定義の一覧を取得します。 |
> | Action | Microsoft.Media/mediaservices/streamingEndpoints/read | ストリーミング エンドポイントを読み取ります。 |
> | Action | Microsoft.Media/mediaservices/streamingEndpoints/scale/action | ストリーミング エンドポイント操作をスケーリングします。 |
> | Action | Microsoft.Media/mediaservices/streamingEndpoints/start/action | ストリーミング エンドポイント操作を開始します。 |
> | Action | Microsoft.Media/mediaservices/streamingEndpoints/stop/action | ストリーミング エンドポイント操作を停止します。 |
> | Action | Microsoft.Media/mediaservices/streamingEndpoints/write | ストリーミング エンドポイントを作成または更新します。 |
> | Action | Microsoft.Media/mediaservices/streamingLocators/delete | ストリーミング ロケーターを削除します。 |
> | Action | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | コンテンツ キーの一覧を取得します。 |
> | Action | Microsoft.Media/mediaservices/streamingLocators/listPaths/action | パスの一覧を取得します。 |
> | Action | Microsoft.Media/mediaservices/streamingLocators/read | ストリーミング ロケーターを読み取ります。 |
> | Action | Microsoft.Media/mediaservices/streamingLocators/write | ストリーミング ロケーターを作成または更新します。 |
> | Action | Microsoft.Media/mediaservices/streamingPolicies/delete | ストリーミング ポリシーを削除します。 |
> | Action | Microsoft.Media/mediaservices/streamingPolicies/read | ストリーミング ポリシーを読み取ります。 |
> | Action | Microsoft.Media/mediaservices/streamingPolicies/write | ストリーミング ポリシーを作成または更新します。 |
> | Action | Microsoft.Media/mediaservices/syncStorageKeys/action | アタッチされた Azure Storage アカウントのストレージ キーを同期します。 |
> | Action | Microsoft.Media/mediaservices/transforms/delete | 変換を削除します。 |
> | Action | Microsoft.Media/mediaservices/transforms/jobs/cancelJob/action | ジョブを取り消します。 |
> | Action | Microsoft.Media/mediaservices/transforms/jobs/delete | ジョブを削除します。 |
> | Action | Microsoft.Media/mediaservices/transforms/jobs/read | ジョブを読み取ります |
> | Action | Microsoft.Media/mediaservices/transforms/jobs/write | ジョブを作成または更新します。 |
> | Action | Microsoft.Media/mediaservices/transforms/read | 変換を読み取ります |
> | Action | Microsoft.Media/mediaservices/transforms/write | 変換を作成または更新します。 |
> | Action | Microsoft.Media/mediaservices/write | Media Services アカウントを作成または更新します。 |
> | Action | Microsoft.Media/operations/read | 使用可能な操作を取得します。 |
> | Action | Microsoft.Media/register/action | Media Services リソース プロバイダーにサブスクリプションを登録し、Media Services アカウントを作成できるようにします。 |
> | Action | Microsoft.Media/unregister/action | Media Services リソース プロバイダーへのサブスクリプションの登録を解除します。 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.Migrate/locations/assessmentOptions/read | 指定された場所で利用できるアセスメント オプションを取得します |
> | Action | Microsoft.Migrate/locations/checknameavailability/action | 指定された場所において、指定されたサブスクリプションでリソース名を使用できるかどうかを確認します |
> | Action | Microsoft.Migrate/Operations/read | Microsoft.Migrate リソース プロバイダーで使用できる操作を一覧表示します |
> | Action | Microsoft.Migrate/projects/assessments/read | プロジェクトでの評価を一覧表示します |
> | Action | Microsoft.Migrate/projects/delete | プロジェクトを削除します |
> | Action | Microsoft.Migrate/projects/groups/assessments/assessedmachines/read | 評価済みのマシンのプロパティを取得します |
> | Action | Microsoft.Migrate/projects/groups/assessments/delete | アセスメントを削除します |
> | Action | Microsoft.Migrate/projects/groups/assessments/downloadurl/action | 評価レポートの URL をダウンロードします |
> | Action | Microsoft.Migrate/projects/groups/assessments/read | 評価のプロパティを取得します |
> | Action | Microsoft.Migrate/projects/groups/assessments/write | 新しいアセスメントを作成するか、既存のアセスメントを更新します |
> | Action | Microsoft.Migrate/projects/groups/delete | グループを削除します |
> | Action | Microsoft.Migrate/projects/groups/read | グループのプロパティを取得します |
> | Action | Microsoft.Migrate/projects/groups/write | 新しいグループを作成するか、既存のグループを更新します |
> | Action | Microsoft.Migrate/projects/keys/action | プロジェクトの共有キーを取得します |
> | Action | Microsoft.Migrate/projects/machines/read | マシンのプロパティを取得します |
> | Action | Microsoft.Migrate/projects/read | プロジェクトのプロパティを取得します |
> | Action | Microsoft.Migrate/projects/write | 新しいプロジェクトを作成するか、既存のプロジェクトを更新します |
> | Action | Microsoft.Migrate/register/action | Microsoft.Migrate リソース プロバイダーにサブスクリプションを登録します |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.NetApp/locations/operationresults/read | 操作結果リソースを読み取ります。 |
> | Action | Microsoft.NetApp/locations/read | 利用可否の確認リソースを読み取ります。 |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/delete | プール リソースを削除します。 |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/providers/Microsoft.Insights/diagnosticSettings/read | プール リソースを削除します。 |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/providers/Microsoft.Insights/diagnosticSettings/write | プール リソースを削除します。 |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/providers/Microsoft.Insights/metricDefinitions/read | プール リソースを削除します。 |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/read | プール リソースを読み取ります。 |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/delete | ボリューム リソースを削除します。 |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/MountTargets/delete | マウント ターゲット リソースを削除します。 |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/MountTargets/read | マウント ターゲット リソースを読み取ります。 |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/MountTargets/write | マウント ターゲット リソースを書き込みます。 |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/providers/Microsoft.Insights/diagnosticSettings/read | ボリューム リソースを削除します。 |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/providers/Microsoft.Insights/diagnosticSettings/write | ボリューム リソースを削除します。 |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/providers/Microsoft.Insights/metricDefinitions/read | ボリューム リソースを削除します。 |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/read | ボリューム リソースを読み取ります。 |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots/delete | スナップショット リソースを削除します。 |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots/read | スナップショット リソースを読み取ります。 |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots/write | スナップショット リソースを書き込みます。 |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/write | ボリューム リソースを書き込みます。 |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/write | プール リソースを書き込みます。 |
> | Action | Microsoft.NetApp/netAppAccounts/delete | アカウント リソースを削除します。 |
> | Action | Microsoft.NetApp/netAppAccounts/read | アカウント リソースを読み取ります。 |
> | Action | Microsoft.NetApp/netAppAccounts/write | アカウント リソースを書き込みます。 |
> | Action | Microsoft.NetApp/Operations/read | 操作リソースを読み取ります。 |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | Application Gateway の SSL の定義済みポリシーです。 |
> | Action | Microsoft.Network/applicationGatewayAvailableSslOptions/read | Application Gateway で使用可能な SSL オプションです。 |
> | Action | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | アプリケーション ゲートウェイの利用可能な Waf ルール セットを取得します。 |
> | Action | Microsoft.Network/applicationGateways/backendAddressPools/join/action | アプリケーション ゲートウェイのバックエンド アドレス プールを接続します。 |
> | Action | Microsoft.Network/applicationGateways/backendhealth/action | アプリケーション ゲートウェイのバックエンドの正常性を取得します。 |
> | Action | Microsoft.Network/applicationGateways/delete | アプリケーション ゲートウェイを削除します。 |
> | Action | Microsoft.Network/applicationGateways/effectiveNetworkSecurityGroups/action | Application Gateway で構成されているルート テーブルを取得します。 |
> | Action | Microsoft.Network/applicationGateways/effectiveRouteTable/action | Application Gateway で構成されているルート テーブルを取得します。 |
> | Action | Microsoft.Network/applicationGateways/providers/Microsoft.Insights/logDefinitions/read | Application Gateway のイベントを取得します。 |
> | Action | Microsoft.Network/applicationGateways/providers/Microsoft.Insights/metricDefinitions/read | Application Gateway で使用可能なメトリックを取得します。 |
> | Action | Microsoft.Network/applicationGateways/read | アプリケーション ゲートウェイを取得します。 |
> | Action | Microsoft.Network/applicationGateways/setSecurityCenterConfiguration/action | Application Gateway の Security Center の構成を設定します。 |
> | Action | Microsoft.Network/applicationGateways/start/action | アプリケーション ゲートウェイを起動します。 |
> | Action | Microsoft.Network/applicationGateways/stop/action | アプリケーション ゲートウェイを停止します。 |
> | Action | Microsoft.Network/applicationGateways/write | アプリケーション ゲートウェイを作成するか、アプリケーション ゲートウェイを更新します。 |
> | Action | Microsoft.Network/applicationSecurityGroups/delete | アプリケーション セキュリティ グループを削除します。 |
> | Action | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | IP 構成をアプリケーション セキュリティ グループに結合します。 |
> | Action | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | セキュリティ ルールをアプリケーション セキュリティ グループに結合します。 |
> | Action | Microsoft.Network/applicationSecurityGroups/read | アプリケーション セキュリティ グループ ID を取得します。 |
> | Action | Microsoft.Network/applicationSecurityGroups/write | アプリケーション セキュリティ グループを作成するか、既存のアプリケーション セキュリティ グループを更新します。 |
> | Action | Microsoft.Network/azurefirewalls/delete | Azure Firewall を削除します |
> | Action | Microsoft.Network/azurefirewalls/providers/Microsoft.Insights/logDefinitions/read | Azure Firewall のイベントを取得します。 |
> | Action | Microsoft.Network/azurefirewalls/providers/Microsoft.Insights/metricDefinitions/read | Azure Firewall の利用可能なメトリックを取得します |
> | Action | Microsoft.Network/azurefirewalls/read | Azure Firewall を取得します |
> | Action | Microsoft.Network/azurefirewalls/write | Azure Firewall を作成または更新します |
> | Action | Microsoft.Network/bgpServiceCommunities/read | BGP サービス コミュニティを取得します。 |
> | Action | Microsoft.Network/checkTrafficManagerNameAvailability/action | Traffic Manager の相対 DNS 名を使用できるかどうかを確認します。 |
> | Action | Microsoft.Network/connections/delete | VirtualNetworkGatewayConnection を削除します。 |
> | Action | Microsoft.Network/connections/providers/Microsoft.Insights/diagnosticSettings/read | 接続の診断設定を取得します |
> | Action | Microsoft.Network/connections/providers/Microsoft.Insights/diagnosticSettings/write | 接続の診断設定を作成または更新します |
> | Action | Microsoft.Network/connections/providers/Microsoft.Insights/metricDefinitions/read | 接続のメトリック定義を取得します |
> | Action | Microsoft.Network/connections/read | VirtualNetworkGatewayConnection を取得します。 |
> | Action | Microsoft.Network/connections/sharedkey/action | VirtualNetworkGatewayConnection の SharedKey を取得します。 |
> | Action | Microsoft.Network/connections/sharedKey/read | VirtualNetworkGatewayConnection の SharedKey を取得します。 |
> | Action | Microsoft.Network/connections/sharedKey/write | VirtualNetworkGatewayConnection の SharedKey を作成するか、既存の SharedKey を更新します。 |
> | Action | Microsoft.Network/connections/vpndeviceconfigurationscript/action | VirtualNetworkGatewayConnection の VPN デバイス構成を取得します。 |
> | Action | Microsoft.Network/connections/write | VirtualNetworkGatewayConnection を作成するか、既存の VirtualNetworkGatewayConnection を更新します。 |
> | Action | Microsoft.Network/ddosProtectionPlans/ddosProtectionPlanProxies/delete | DDoS Protection プラン プロキシを削除します。 |
> | Action | Microsoft.Network/ddosProtectionPlans/ddosProtectionPlanProxies/read | DDoS Protection プラン プロキシの定義を取得します。 |
> | Action | Microsoft.Network/ddosProtectionPlans/ddosProtectionPlanProxies/write | DDoS Protection プラン プロキシを作成するか、既存の DDoS Protection プラン プロキシを更新します。 |
> | Action | Microsoft.Network/ddosProtectionPlans/delete | DDoS Protection プランを削除します。 |
> | Action | Microsoft.Network/ddosProtectionPlans/join/action | DDoS Protection プランを結合します。 |
> | Action | Microsoft.Network/ddosProtectionPlans/read | DDoS Protection プランを取得します。 |
> | Action | Microsoft.Network/ddosProtectionPlans/write | DDoS Protection プランを作成するか、DDoS Protection プランを更新します。  |
> | Action | Microsoft.Network/dnsoperationresults/read | DNS 操作の結果を取得します。 |
> | Action | Microsoft.Network/dnsoperationstatuses/read | DNS 操作の状態を取得します。  |
> | Action | Microsoft.Network/dnszones/A/delete | 指定された名前の "A" タイプのレコード セットを DNS ゾーンから削除します。 |
> | Action | Microsoft.Network/dnszones/A/read | "A" タイプのレコード セットを JSON 形式で取得します。 レコード セットには、レコードの一覧と、TTL、タグ、および ETag が含まれます。 |
> | Action | Microsoft.Network/dnszones/A/write | DNS ゾーン内の "A" タイプのレコード セットを作成または更新します。 レコード セットの現在のレコードが指定されたレコードに置き換えられます。 |
> | Action | Microsoft.Network/dnszones/AAAA/delete | 指定された名前の "AAAA" タイプのレコード セットを DNS ゾーンから削除します。 |
> | Action | Microsoft.Network/dnszones/AAAA/read | "AAAA" タイプのレコード セットを JSON 形式で取得します。 レコード セットには、レコードの一覧と、TTL、タグ、および ETag が含まれます。 |
> | Action | Microsoft.Network/dnszones/AAAA/write | DNS ゾーン内の "AAAA" タイプのレコード セットを作成または更新します。 レコード セットの現在のレコードが指定されたレコードに置き換えられます。 |
> | Action | Microsoft.Network/dnszones/all/read | 各タイプの DNS レコード セットを取得します。 |
> | Action | Microsoft.Network/dnszones/CAA/delete | 指定された名前の "CAA" タイプのレコード セットを DNS ゾーンから削除します。 |
> | Action | Microsoft.Network/dnszones/CAA/read | "CAA" タイプのレコード セットを JSON 形式で取得します。 レコード セットには、TTL、タグ、ETag が含まれます。 |
> | Action | Microsoft.Network/dnszones/CAA/write | DNS ゾーン内の "CAA" タイプのレコード セットを作成または更新します。 レコード セットの現在のレコードが指定されたレコードに置き換えられます。 |
> | Action | Microsoft.Network/dnszones/CNAME/delete | 指定された名前の "CNAME" タイプのレコード セットを DNS ゾーンから削除します。 |
> | Action | Microsoft.Network/dnszones/CNAME/read | "CNAME" タイプのレコード セットを JSON 形式で取得します。 レコード セットには、TTL、タグ、ETag が含まれます。 |
> | Action | Microsoft.Network/dnszones/CNAME/write | DNS ゾーン内の "CNAME" タイプのレコード セットを作成または更新します。 レコード セットの現在のレコードが指定されたレコードに置き換えられます。 |
> | Action | Microsoft.Network/dnszones/delete | JSON 形式の DNS ゾーンを削除します。 ゾーンのプロパティには、タグ、ETag、numberOfRecordSets、maxNumberOfRecordSets があります。 |
> | Action | Microsoft.Network/dnszones/MX/delete | 指定された名前の "MX" タイプのレコード セットを DNS ゾーンから削除します。 |
> | Action | Microsoft.Network/dnszones/MX/read | "MX" タイプのレコード セットを JSON 形式で取得します。 レコード セットには、レコードの一覧と、TTL、タグ、および ETag が含まれます。 |
> | Action | Microsoft.Network/dnszones/MX/write | DNS ゾーン内の "MX" タイプのレコード セットを作成または更新します。 レコード セットの現在のレコードが指定されたレコードに置き換えられます。 |
> | Action | Microsoft.Network/dnszones/NS/delete | NS タイプの DNS レコード セットを削除します。 |
> | Action | Microsoft.Network/dnszones/NS/read | NS タイプの DNS レコード セットを取得します。 |
> | Action | Microsoft.Network/dnszones/NS/write | NS タイプの DNS レコード セットを作成または更新します。 |
> | Action | Microsoft.Network/dnszones/providers/Microsoft.Insights/diagnosticSettings/read | DNS ゾーンの診断設定を取得します。 |
> | Action | Microsoft.Network/dnszones/providers/Microsoft.Insights/diagnosticSettings/write | DNS ゾーンの診断設定を作成または更新します。 |
> | Action | Microsoft.Network/dnszones/providers/Microsoft.Insights/metricDefinitions/read | DNS ゾーンのメトリック定義を取得します。 |
> | Action | Microsoft.Network/dnszones/PTR/delete | 指定された名前の "PTR" タイプのレコード セットを DNS ゾーンから削除します。 |
> | Action | Microsoft.Network/dnszones/PTR/read | "PTR" タイプのレコード セットを JSON 形式で取得します。 レコード セットには、レコードの一覧と、TTL、タグ、および ETag が含まれます。 |
> | Action | Microsoft.Network/dnszones/PTR/write | DNS ゾーン内の "PTR" タイプのレコード セットを作成または更新します。 レコード セットの現在のレコードが指定されたレコードに置き換えられます。 |
> | Action | Microsoft.Network/dnszones/read | DNS ゾーンを JSON 形式で取得します。 ゾーンのプロパティには、タグ、ETag、numberOfRecordSets、maxNumberOfRecordSets があります。 このコマンドでは、ゾーン内のレコード セットは取得されません。 |
> | Action | Microsoft.Network/dnszones/recordsets/read | 各タイプの DNS レコード セットを取得します。 |
> | Action | Microsoft.Network/dnszones/SOA/read | SOA タイプの DNS レコード セットを取得します。 |
> | Action | Microsoft.Network/dnszones/SOA/write | SOA タイプの DNS レコード セットを作成または更新します。 |
> | Action | Microsoft.Network/dnszones/SRV/delete | 指定された名前の "SRV" タイプのレコード セットを DNS ゾーンから削除します。 |
> | Action | Microsoft.Network/dnszones/SRV/read | "SRV" タイプのレコード セットを JSON 形式で取得します。 レコード セットには、レコードの一覧と、TTL、タグ、および ETag が含まれます。 |
> | Action | Microsoft.Network/dnszones/SRV/write | SRV タイプのレコード セットを作成または更新します。 |
> | Action | Microsoft.Network/dnszones/TXT/delete | 指定された名前の "TXT" タイプのレコード セットを DNS ゾーンから削除します。 |
> | Action | Microsoft.Network/dnszones/TXT/read | "TXT" タイプのレコード セットを JSON 形式で取得します。 レコード セットには、レコードの一覧と、TTL、タグ、および ETag が含まれます。 |
> | Action | Microsoft.Network/dnszones/TXT/write | DNS ゾーン内の "TXT" タイプのレコード セットを作成または更新します。 レコード セットの現在のレコードが指定されたレコードに置き換えられます。 |
> | Action | Microsoft.Network/dnszones/write | リソース グループ内の DNS ゾーンを作成または更新します。  DNS ゾーン リソースのタグを更新する際に使用します。 このコマンドを使用して、ゾーン内のレコード セットを作成または更新することはできません。 |
> | Action | Microsoft.Network/expressRouteCircuits/authorizations/delete | ExpressRouteCircuit の承認を削除します。 |
> | Action | Microsoft.Network/expressRouteCircuits/authorizations/read | ExpressRouteCircuit の承認を取得します。 |
> | Action | Microsoft.Network/expressRouteCircuits/authorizations/write | ExpressRouteCircuit の承認を作成するか、既存の承認を更新します。 |
> | Action | Microsoft.Network/expressRouteCircuits/delete | ExpressRouteCircuit を削除します。 |
> | Action | Microsoft.Network/expressRouteCircuits/join/action | Express Route 回線を結合します。 |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/arpTables/action | ExpressRouteCircuit のピアリングの ArpTable を取得します。 |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | ExpressRouteCircuit の接続を削除します。 |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/connections/read | ExpressRouteCircuit の接続を取得します。 |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/connections/write | ExpressRouteCircuit の接続リソースを作成するか、既存の接続リソースを更新します。 |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/delete | ExpressRouteCircuit のピアリングを削除します。 |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/providers/Microsoft.Insights/diagnosticSettings/read | ExpressRoute 回線ピアリングの診断設定を取得します |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/providers/Microsoft.Insights/diagnosticSettings/write | ExpressRoute 回線ピアリングの診断設定を作成または更新します |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/providers/Microsoft.Insights/metricDefinitions/read | ExpressRoute 回線ピアリングのメトリック定義を取得します |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/read | ExpressRouteCircuit のピアリングを取得します。 |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/routeTables/action | ExpressRouteCircuit のピアリングの RouteTable を取得します。 |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/routeTablesSummary/action | ExpressRouteCircuit のピアリングの RouteTable の概要を取得します。 |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/stats/read | ExpressRouteCircuit のピアリングの統計を取得します。 |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/write | ExpressRouteCircuit のピアリングを作成するか、既存のピアリングを更新します。 |
> | Action | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/read | ExpressRoute 回線の診断設定を取得します。 |
> | Action | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/write | ExpressRoute 回線の診断設定を作成または更新します。 |
> | Action | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/logDefinitions/read | ExpressRoute 回線のイベントを取得します。 |
> | Action | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/metricDefinitions/read | ExpressRoute 回線のメトリック定義を取得します。 |
> | Action | Microsoft.Network/expressRouteCircuits/read | ExpressRouteCircuit を取得します。 |
> | Action | Microsoft.Network/expressRouteCircuits/stats/read | ExpressRouteCircuit の統計を取得します。 |
> | Action | Microsoft.Network/expressRouteCircuits/write | ExpressRouteCircuit を作成するか、既存の ExpressRouteCircuit を更新します。 |
> | Action | Microsoft.Network/expressRouteCrossConnections/delete | ExpressRoute のクロス接続を削除します。 |
> | Action | Microsoft.Network/expressRouteCrossConnections/join/action | ExpressRoute のクロス接続を結合します。 |
> | Action | Microsoft.Network/expressRouteCrossConnections/peerings/arpTables/action | ExpressRoute のクロス接続のピアリング ARP テーブルを取得します。 |
> | Action | Microsoft.Network/expressRouteCrossConnections/peerings/delete | ExpressRoute のクロス接続のピアリングを削除します。 |
> | Action | Microsoft.Network/expressRouteCrossConnections/peerings/read | ExpressRoute のクロス接続のピアリングを取得します。 |
> | Action | Microsoft.Network/expressRouteCrossConnections/peerings/routeTables/action | ExpressRoute のクロス接続のピアリング ルート テーブルを取得します。 |
> | Action | Microsoft.Network/expressRouteCrossConnections/peerings/routeTableSummary/action | ExpressRoute のクロス接続のピアリング ルート テーブル概要を取得します。 |
> | Action | Microsoft.Network/expressRouteCrossConnections/peerings/write | ExpressRoute のクロス接続ピアリングを作成するか、ExpressRoute の既存のクロス接続ピアリングを更新します。 |
> | Action | Microsoft.Network/expressRouteCrossConnections/read | ExpressRoute のクロス接続を取得します。 |
> | Action | Microsoft.Network/expressRouteCrossConnections/write | ExpressRoute のクロス接続を作成または更新します。 |
> | Action | Microsoft.Network/expressRouteGateways/delete | Express Route ゲートウェイを削除します。 |
> | Action | Microsoft.Network/expressRouteGateways/expressRouteConnections/delete | Express Route 接続を削除します。 |
> | Action | Microsoft.Network/expressRouteGateways/expressRouteConnections/read | Express Route 接続を取得します。 |
> | Action | Microsoft.Network/expressRouteGateways/expressRouteConnections/write | Express Route 接続を作成するか、Express Route の既存の接続を更新します。 |
> | Action | Microsoft.Network/expressRouteGateways/join/action | Express Route ゲートウェイを結合します。 |
> | Action | Microsoft.Network/expressRouteGateways/read | Express Route ゲートウェイを取得します。 |
> | Action | Microsoft.Network/expressRouteGateways/write | Express Route ゲートウェイを作成または更新します。 |
> | Action | Microsoft.Network/expressRoutePorts/delete | ExpressRoutePorts を削除します |
> | Action | Microsoft.Network/expressRoutePorts/join/action | ExpressRoutePorts を結合します |
> | Action | Microsoft.Network/expressRoutePorts/links/read | ExpressRouteLink を取得します |
> | Action | Microsoft.Network/expressRoutePorts/read | ExpressRoutePorts を取得します |
> | Action | Microsoft.Network/expressRoutePorts/write | ExpressRoutePorts を作成または更新します |
> | Action | Microsoft.Network/expressRoutePortsLocations/read | ExpressRoute ポートの場所を取得します |
> | Action | Microsoft.Network/expressRouteServiceProviders/read | Express Route サービス プロバイダーを取得します。 |
> | Action | Microsoft.Network/frontdoors/read | Frontdoor を取得します |
> | Action | Microsoft.Network/getDnsResourceReference/action | DNS エイリアス リソースの依存関係要求 |
> | Action | Microsoft.Network/internalNotify/action | DNS エイリアス リソース通知 |
> | Action | Microsoft.Network/loadBalancers/backendAddressPools/join/action | ロード バランサーのバックエンド アドレス プールを接続します。 |
> | Action | Microsoft.Network/loadBalancers/backendAddressPools/read | ロード バランサーのバックエンド アドレス プールの定義を取得します。 |
> | Action | Microsoft.Network/loadBalancers/delete | ロード バランサーを削除します。 |
> | Action | Microsoft.Network/loadBalancers/frontendIPConfigurations/read | ロード バランサーのフロントエンド IP 構成定義を取得します。 |
> | Action | Microsoft.Network/loadBalancers/inboundNatPools/join/action | ロード バランサーの受信 NAT プールを接続します。 |
> | Action | Microsoft.Network/loadBalancers/inboundNatPools/read | ロード バランサーの受信 NAT プールの定義を取得します。 |
> | Action | Microsoft.Network/loadBalancers/inboundNatRules/delete | ロード バランサーの受信 NAT 規則を削除します。 |
> | Action | Microsoft.Network/loadBalancers/inboundNatRules/join/action | ロード バランサーの受信 NAT 規則を接続します。 |
> | Action | Microsoft.Network/loadBalancers/inboundNatRules/read | ロード バランサーの受信 NAT 規則の定義を取得します。 |
> | Action | Microsoft.Network/loadBalancers/inboundNatRules/write | ロード バランサーの受信 NAT 規則を作成するか、既存の受信 NAT 規則を更新します。 |
> | Action | Microsoft.Network/loadBalancers/loadBalancingRules/read | ロード バランサーの負荷分散規則の定義を取得します。 |
> | Action | Microsoft.Network/loadBalancers/networkInterfaces/read | ロード バランサーの下のすべてのネットワーク インターフェイスへの参照を取得します。 |
> | Action | Microsoft.Network/loadBalancers/outboundRules/read | ロード バランサーの送信規則の定義を取得します |
> | Action | Microsoft.Network/loadBalancers/probes/join/action | ロード バランサーのプローブの使用を許可します。 たとえば、このアクセス許可では、VM スケール セットの healthProbe プロパティでプローブを参照できます。 |
> | Action | Microsoft.Network/loadBalancers/probes/read | ロード バランサー プローブを取得します。 |
> | Action | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/read | ロード バランサーの診断設定を取得します。 |
> | Action | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/write | ロード バランサーの診断設定を作成または更新します。 |
> | Action | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/logDefinitions/read | ロード バランサーのイベントを取得します。 |
> | Action | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/metricDefinitions/read | ロード バランサーで使用可能なメトリックを取得します。 |
> | Action | Microsoft.Network/loadBalancers/read | ロード バランサー定義を取得します。 |
> | Action | Microsoft.Network/loadBalancers/virtualMachines/read | ロード バランサーの下のすべての仮想マシンへの参照を取得します。 |
> | Action | Microsoft.Network/loadBalancers/write | ロード バランサーを作成するか、既存のロード バランサーを更新します。 |
> | Action | Microsoft.Network/localnetworkgateways/delete | LocalNetworkGateway を削除します。 |
> | Action | Microsoft.Network/localnetworkgateways/read | LocalNetworkGateway を取得します。 |
> | Action | Microsoft.Network/localnetworkgateways/write | LocalNetworkGateway を作成するか、既存の LocalNetworkGateway を更新します。 |
> | Action | Microsoft.Network/locations/availableDelegations/read | 使用可能な委任を取得します。 |
> | Action | Microsoft.Network/locations/bareMetalTenants/action | ベア メタル テナントを割り当てまたは検証します。 |
> | Action | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | 高速ネットワークのサポートを確認します。 |
> | Action | Microsoft.Network/locations/checkDnsNameAvailability/read | 指定された場所で DNS ラベルを使用できるかどうかを確認します。 |
> | Action | Microsoft.Network/locations/effectiveResourceOwnership/action | 有効なリソース所有権を取得します。 |
> | Action | Microsoft.Network/locations/operationResults/read | 非同期の POST 操作または DELETE 操作の結果を取得します。 |
> | Action | Microsoft.Network/locations/operations/read | 非同期操作の状態を表す操作リソースを取得します。 |
> | Action | Microsoft.Network/locations/setResourceOwnership/action | リソース所有権を設定します。 |
> | Action | Microsoft.Network/locations/supportedVirtualMachineSizes/read | サポートされる仮想マシン サイズを取得します。 |
> | Action | Microsoft.Network/locations/usages/read | リソースの使用状況メトリックを取得します。 |
> | Action | Microsoft.Network/locations/validateResourceOwnership/action | リソース所有権を検証します。 |
> | Action | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | 使用可能な仮想ネットワーク エンドポイント サービスの一覧を取得します。 |
> | Action | Microsoft.Network/networkIntentPolicies/delete | ネットワーク インテント ポリシーを削除します |
> | Action | Microsoft.Network/networkIntentPolicies/read | ネットワーク インテント ポリシーの説明を取得します |
> | Action | Microsoft.Network/networkIntentPolicies/write | ネットワーク インテント ポリシーを作成するか、既存のネットワーク インテント ポリシーを更新します |
> | Action | Microsoft.Network/networkInterfaces/delete | ネットワーク インターフェイスを削除します。 |
> | Action | Microsoft.Network/networkInterfaces/diagnosticIdentity/read | リソースの診断 ID を取得します。 |
> | Action | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | VM のネットワーク インターフェイスで構成されているネットワーク セキュリティ グループを取得します。 |
> | Action | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | VM のネットワーク インターフェイスで構成されているルート テーブルを取得します。 |
> | Action | Microsoft.Network/networkInterfaces/ipconfigurations/read | ネットワーク インターフェイスの IP 構成定義を取得します。  |
> | Action | Microsoft.Network/networkInterfaces/join/action | 仮想マシンをネットワーク インターフェイスに接続します。 |
> | Action | Microsoft.Network/networkInterfaces/joinViaPrivateIp/action | サービスの関連付けを介してリソースをネットワーク インターフェイスに結合します。 |
> | Action | Microsoft.Network/networkInterfaces/loadBalancers/read | ネットワーク インターフェイスが含まれているすべてのロード バランサーを取得します。 |
> | Action | Microsoft.Network/networkInterfaces/providers/Microsoft.Insights/metricDefinitions/read | ネットワーク インターフェイスで使用可能なメトリックを取得します。 |
> | Action | Microsoft.Network/networkInterfaces/read | ネットワーク インターフェイスの定義を取得します。  |
> | Action | Microsoft.Network/networkInterfaces/serviceAssociations/delete | サービスの関連付けを削除します。 |
> | Action | Microsoft.Network/networkInterfaces/serviceAssociations/read | サービスの関連付け定義を取得します。 |
> | Action | Microsoft.Network/networkInterfaces/serviceAssociations/validate/action | サービスの関連付けを検証します。 |
> | Action | Microsoft.Network/networkInterfaces/serviceAssociations/write | 新しいサービスの関連付けを作成するか、既存のサービスの関連付けを変更します。 |
> | Action | Microsoft.Network/networkInterfaces/write | ネットワーク インターフェイスを作成するか、既存のネットワーク インターフェイスを更新します。  |
> | Action | Microsoft.Network/networkProfiles/delete | ネットワーク プロファイルを削除します |
> | Action | Microsoft.Network/networkProfiles/read | ネットワーク プロファイルを取得します |
> | Action | Microsoft.Network/networkProfiles/removeContainers/action | コンテナーを削除します |
> | Action | Microsoft.Network/networkProfiles/setContainers/action | コンテナーを設定します |
> | Action | Microsoft.Network/networkProfiles/setNetworkInterfaces/action | コンテナーのネットワーク インターフェイスを設定します |
> | Action | Microsoft.Network/networkProfiles/write | ネットワーク プロファイルを作成または更新します |
> | Action | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | 既定のセキュリティ規則の定義を取得します。 |
> | Action | Microsoft.Network/networkSecurityGroups/delete | ネットワーク セキュリティ グループを削除します。 |
> | Action | Microsoft.Network/networkSecurityGroups/join/action | ネットワーク セキュリティ グループに参加します。 |
> | Action | Microsoft.Network/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | ネットワーク セキュリティ グループの診断設定を取得します。 |
> | Action | Microsoft.Network/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | ネットワーク セキュリティ グループの診断設定を作成または更新します。この操作は、Insghts リソース プロバイダーによって補完されます。 |
> | Action | Microsoft.Network/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | ネットワーク セキュリティ グループのイベントを取得します。 |
> | Action | Microsoft.Network/networkSecurityGroups/read | ネットワーク セキュリティ グループの定義を取得します。 |
> | Action | Microsoft.Network/networkSecurityGroups/securityRules/delete | セキュリティ規則を削除します。 |
> | Action | Microsoft.Network/networkSecurityGroups/securityRules/read | セキュリティ規則の定義を取得します。 |
> | Action | Microsoft.Network/networkSecurityGroups/securityRules/write | セキュリティ規則を作成するか、既存のセキュリティ規則を更新します。 |
> | Action | Microsoft.Network/networkSecurityGroups/write | ネットワーク セキュリティ グループを作成するか、既存のネットワーク セキュリティ グループを更新します。 |
> | Action | Microsoft.Network/networkWatchers/availableProvidersList/action | 指定された Azure リージョンで利用可能なすべてのインターネット サービス プロバイダーを返します。 |
> | Action | Microsoft.Network/networkWatchers/azureReachabilityReport/action | 指定された場所から Azure リージョンまでのインターネット サービス プロバイダーの相対待機時間スコアを返します。 |
> | Action | Microsoft.Network/networkWatchers/configureFlowLog/action | ターゲット リソースのフロー ログを構成します。 |
> | Action | Microsoft.Network/networkWatchers/connectionMonitors/delete | 接続モニターを削除します。 |
> | Action | Microsoft.Network/networkWatchers/connectionMonitors/providers/Microsoft.Insights/diagnosticSettings/read | 接続モニターの診断設定を取得します。 |
> | Action | Microsoft.Network/networkWatchers/connectionMonitors/providers/Microsoft.Insights/diagnosticSettings/write | 接続モニターの診断設定を作成または更新します。 |
> | Action | Microsoft.Network/networkWatchers/connectionMonitors/providers/Microsoft.Insights/metricDefinitions/read | 接続モニターで使用可能なメトリックを取得します。 |
> | Action | Microsoft.Network/networkWatchers/connectionMonitors/query/action | 指定されたエンドポイント間の接続の監視をクエリします。 |
> | Action | Microsoft.Network/networkWatchers/connectionMonitors/read | 接続モニターの詳細を取得します。 |
> | Action | Microsoft.Network/networkWatchers/connectionMonitors/start/action | 指定されたエンドポイント間の接続の監視を開始します。 |
> | Action | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | 指定されたエンドポイント間の監視接続を停止/一時停止します。 |
> | Action | Microsoft.Network/networkWatchers/connectionMonitors/write | 接続モニターを作成します。 |
> | Action | Microsoft.Network/networkWatchers/connectivityCheck/action | 仮想マシンから、別の VM や任意のリモート サーバーなどの指定されたエンドポイントまでの、直接 TCP 接続が確立されたかどうかを確認します。 |
> | Action | Microsoft.Network/networkWatchers/delete | Network Watcher を削除します。 |
> | Action | Microsoft.Network/networkWatchers/ipFlowVerify/action | 特定の宛先との間で送受信されるパケットを許可するか拒否するかを返します。 |
> | Action | Microsoft.Network/networkWatchers/lenses/delete | レンズを削除します。 |
> | Action | Microsoft.Network/networkWatchers/lenses/query/action | 指定されたエンドポイントでのネットワーク トラフィックの監視をクエリします。 |
> | Action | Microsoft.Network/networkWatchers/lenses/read | レンズの詳細を取得します。 |
> | Action | Microsoft.Network/networkWatchers/lenses/start/action | 指定されたエンドポイントでネットワーク トラフィックの監視を開始します。 |
> | Action | Microsoft.Network/networkWatchers/lenses/stop/action | 指定されたエンドポイントでのネットワーク トラフィックの監視を停止/一時停止します。 |
> | Action | Microsoft.Network/networkWatchers/lenses/write | レンズを作成します。 |
> | Action | Microsoft.Network/networkWatchers/networkConfigurationDiagnostic/action | ネットワーク構成の診断。 |
> | Action | Microsoft.Network/networkWatchers/nextHop/action | 指定されたターゲットと宛先 IP アドレスについて、次ホップの種類と次ホップの IP アドレスを返します。 |
> | Action | Microsoft.Network/networkWatchers/packetCaptures/delete | パケット キャプチャを削除します。 |
> | Action | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | パケット キャプチャ リソースのプロパティと状態に関する情報を取得します。 |
> | Action | Microsoft.Network/networkWatchers/packetCaptures/read | パケット キャプチャ定義を取得します。 |
> | Action | Microsoft.Network/networkWatchers/packetCaptures/stop/action | 実行中のパケット キャプチャ セッションを停止します。 |
> | Action | Microsoft.Network/networkWatchers/packetCaptures/write | パケット キャプチャを作成します。 |
> | Action | Microsoft.Network/networkWatchers/pingMeshes/delete | PingMesh を削除します。 |
> | Action | Microsoft.Network/networkWatchers/pingMeshes/read | PingMesh の詳細を取得します。 |
> | Action | Microsoft.Network/networkWatchers/pingMeshes/start/action | 指定された VM 間の PingMesh を開始します。 |
> | Action | Microsoft.Network/networkWatchers/pingMeshes/stop/action | 指定された VM 間の PingMesh を停止します。 |
> | Action | Microsoft.Network/networkWatchers/pingMeshes/write | PingMesh を作成します。 |
> | Action | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | リソースのフロー ログの状態を取得します。 |
> | Action | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | 実行済みまたは現在実行中のトラブルシューティング操作のトラブルシューティングの結果を取得します。 |
> | Action | Microsoft.Network/networkWatchers/read | Network Watcher の定義を取得します。 |
> | Action | Microsoft.Network/networkWatchers/securityGroupView/action | VM に適用されている構成済みの有効なネットワーク セキュリティ グループ規則を表示します。 |
> | Action | Microsoft.Network/networkWatchers/topology/action | リソース グループ内のリソースとその関係のネットワーク レベル ビューを取得します。 |
> | Action | Microsoft.Network/networkWatchers/troubleshoot/action | Azure のネットワーク リソースでトラブルシューティングを開始します。 |
> | Action | Microsoft.Network/networkWatchers/write | Network Watcher を作成するか、既存の Network Watcher を更新します。 |
> | Action | Microsoft.Network/operations/read | 使用可能な操作を取得します。 |
> | Action | Microsoft.Network/publicIPAddresses/delete | パブリック IP アドレスを削除します。 |
> | Action | Microsoft.Network/publicIPAddresses/dnsAliases/delete | パブリック IP アドレスの DNS エイリアス リソースを削除します。 |
> | Action | Microsoft.Network/publicIPAddresses/dnsAliases/read | パブリック IP アドレスの DNS エイリアス リソースを取得します。 |
> | Action | Microsoft.Network/publicIPAddresses/dnsAliases/write | パブリック IP アドレスの DNS エイリアス リソースを作成します。 |
> | Action | Microsoft.Network/publicIPAddresses/join/action | パブリック IP アドレスに接続します。 |
> | Action | Microsoft.Network/publicIPAddresses/loadBalancerPools/delete | パブリック IP アドレスのロード バランサー バックエンド プールを削除します。 |
> | Action | Microsoft.Network/publicIPAddresses/loadBalancerPools/join/action | パブリック IP アドレスのロード バランサー バックエンド プールを結合します。 |
> | Action | Microsoft.Network/publicIPAddresses/loadBalancerPools/read | パブリック IP アドレスのロード バランサー バックエンド プール定義を取得します。 |
> | Action | Microsoft.Network/publicIPAddresses/loadBalancerPools/write | パブリック IP アドレスのロード バランサー バックエンド プールを作成するか、既存のパブリック IP アドレスのロード バランサー バックエンド プールを更新します。 |
> | Action | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/read | パブリック IP アドレスの診断設定を取得します。 |
> | Action | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/write | パブリック IP アドレスの診断設定を作成または更新します。 |
> | Action | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/logDefinitions/read | パブリック IP アドレスのログ定義を取得します。 |
> | Action | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/metricDefinitions/read | パブリック IP アドレスのメトリック定義を取得します。 |
> | Action | Microsoft.Network/publicIPAddresses/read | パブリック IP アドレス定義を取得します。 |
> | Action | Microsoft.Network/publicIPAddresses/write | パブリック IP アドレスを作成するか、既存のパブリック IP アドレスを更新します。  |
> | Action | Microsoft.Network/publicIPPrefixes/delete | パブリック IP プレフィックスを削除します |
> | Action | Microsoft.Network/publicIPPrefixes/join/action | PublicIPPrefix を結合します |
> | Action | Microsoft.Network/publicIPPrefixes/read | パブリック IP プレフィックス定義を取得します |
> | Action | Microsoft.Network/publicIPPrefixes/write | パブリック IP プレフィックスを作成するか、既存のパブリック IP プレフィックスを更新します |
> | Action | Microsoft.Network/register/action | サブスクリプションを登録します。 |
> | Action | Microsoft.Network/routeFilters/delete | ルート フィルター定義を削除します。 |
> | Action | Microsoft.Network/routeFilters/join/action | ルート フィルターを結合します。 |
> | Action | Microsoft.Network/routeFilters/read | ルート フィルター定義を取得します。 |
> | Action | Microsoft.Network/routeFilters/routeFilterRules/delete | ルート フィルター規則の定義を削除します。 |
> | Action | Microsoft.Network/routeFilters/routeFilterRules/read | ルート フィルター規則の定義を取得します。 |
> | Action | Microsoft.Network/routeFilters/routeFilterRules/write | ルート フィルター規則を作成するか、既存のルート フィルター規則を更新します。 |
> | Action | Microsoft.Network/routeFilters/write | ルート フィルターを作成するか、既存のルート フィルターを更新します。 |
> | Action | Microsoft.Network/routeTables/delete | ルート テーブルの定義を削除します。 |
> | Action | Microsoft.Network/routeTables/join/action | ルート テーブルを結合します。 |
> | Action | Microsoft.Network/routeTables/read | ルート テーブルの定義を取得します。 |
> | Action | Microsoft.Network/routeTables/routes/delete | ルート定義を削除します。 |
> | Action | Microsoft.Network/routeTables/routes/read | ルート定義を取得します。 |
> | Action | Microsoft.Network/routeTables/routes/write | ルートを作成するか、既存のルートを更新します。 |
> | Action | Microsoft.Network/routeTables/write | ルート テーブルを作成するか、既存のルート テーブルを更新します。 |
> | Action | Microsoft.Network/securegateways/applicationRuleCollections/delete | 安全なゲートウェイのアプリケーション ルール コレクションを削除します。 |
> | Action | Microsoft.Network/securegateways/applicationRuleCollections/read | 指定された安全なゲートウェイのアプリケーション ルール コレクションを取得します。 |
> | Action | Microsoft.Network/securegateways/applicationRuleCollections/write | 安全なゲートウェイのアプリケーション ルール コレクションを作成または更新します。 |
> | Action | Microsoft.Network/securegateways/delete | 安全なゲートウェイを削除します。 |
> | Action | Microsoft.Network/securegateways/networkRuleCollections/delete | 安全なゲートウェイのネットワーク ルール コレクションを削除します。 |
> | Action | Microsoft.Network/securegateways/networkRuleCollections/read | 指定された安全なゲートウェイのネットワーク ルール コレクションを取得します。 |
> | Action | Microsoft.Network/securegateways/networkRuleCollections/write | 安全なゲートウェイのネットワーク ルール コレクションを作成または更新します。 |
> | Action | Microsoft.Network/securegateways/providers/Microsoft.Insights/logDefinitions/read | Azure Firewall のイベントを取得します。 |
> | Action | Microsoft.Network/securegateways/read | 安全なゲートウェイを取得します。 |
> | Action | Microsoft.Network/securegateways/write | 安全なゲートウェイを作成または更新します。 |
> | Action | Microsoft.Network/serviceEndpointPolicies/delete | サービス エンドポイント ポリシーを削除します。 |
> | Action | Microsoft.Network/serviceEndpointPolicies/join/action | サービス エンドポイント ポリシーを結合します。 |
> | Action | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | サブネットをサービス エンドポイント ポリシーに結合します。 |
> | Action | Microsoft.Network/serviceEndpointPolicies/read | サービス エンドポイント ポリシーの説明を取得します。 |
> | Action | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | サービス エンドポイント ポリシーの定義を削除します。 |
> | Action | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | サービス エンドポイント ポリシーの定義の説明を取得します。 |
> | Action | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | サービス エンドポイント ポリシーの定義を作成するか、既存のサービス エンドポイント ポリシーの定義を更新します。 |
> | Action | Microsoft.Network/serviceEndpointPolicies/write | サービス エンドポイント ポリシーを作成するか、既存のサービス エンドポイント ポリシーを更新します。 |
> | Action | Microsoft.Network/trafficManagerGeographicHierarchies/read | 地理的トラフィック ルーティング方法で使用できるリージョンを含む、Traffic Manager の地理的階層を取得します。 |
> | Action | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | 既存の Traffic Manager プロファイルから Azure エンドポイントを削除します。 Traffic Manager は、削除された Azure エンドポイントへのトラフィックのルーティングを停止します。 |
> | Action | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | Traffic Manager プロファイルに属する Azure エンドポイントを取得します (その Azure エンドポイントのすべてのプロパティなど)。 |
> | Action | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | 既存の Traffic Manager プロファイルに新しい Azure エンドポイントを追加するか、その Traffic Manager プロファイルの既存の Azure エンドポイントのプロパティを更新します。 |
> | Action | Microsoft.Network/trafficManagerProfiles/delete | Traffic Manager プロファイルを削除します。 Traffic Manager プロファイルに関連付けられているすべての設定が失われ、プロファイルを使用してトラフィックをルーティングすることはできなくなります。 |
> | Action | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | 既存の Traffic Manager プロファイルから外部エンドポイントを削除します。 Traffic Manager は、削除された外部エンドポイントへのトラフィックのルーティングを停止します。 |
> | Action | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | Traffic Manager プロファイルに属する外部エンドポイントを取得します (その外部エンドポイントのすべてのプロパティなど)。 |
> | Action | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | 既存の Traffic Manager プロファイルに新しい外部エンドポイントを追加するか、その Traffic Manager プロファイルの既存の外部エンドポイントのプロパティを更新します。 |
> | Action | Microsoft.Network/trafficManagerProfiles/heatMaps/read | 場所およびソース IP 別にクエリ数と待機時間のデータを含む指定された Traffic Manager プロファイルに対する Traffic Manager ヒート マップを取得します。 |
> | Action | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | 既存の Traffic Manager プロファイルから入れ子になったエンドポイントを削除します。 Traffic Manager は、削除された入れ子になったエンドポイントへのトラフィックのルーティングを停止します。 |
> | Action | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | Traffic Manager プロファイルに属する入れ子になったエンドポイントを取得します (その入れ子になったエンドポイントのすべてのプロパティなど)。 |
> | Action | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | 既存の Traffic Manager プロファイルに新しい入れ子になったエンドポイントを追加するか、その Traffic Manager プロファイルの既存の入れ子になったエンドポイントのプロパティを更新します。 |
> | Action | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/read | Traffic Manager の診断設定を取得します。 |
> | Action | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/write | Traffic Manager の診断設定を作成または更新します。この操作は、Insghts リソース プロバイダーによって補完されます。 |
> | Action | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/logDefinitions/read | Traffic Manager のイベントを取得します。 |
> | Action | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/metricDefinitions/read | Traffic Manager で使用可能なメトリックを取得します。 |
> | Action | Microsoft.Network/trafficManagerProfiles/read | Traffic Manager プロファイル構成を取得します。 これには、DNS 設定、トラフィック ルーティング設定、エンドポイント監視設定、この Traffic Manager プロファイルによってルーティングされるエンドポイントの一覧が含まれます。 |
> | Action | Microsoft.Network/trafficManagerProfiles/write | Traffic Manager プロファイルを作成するか、既存の Traffic Manager プロファイルの構成を変更します。<br>これには、プロファイルの有効化または無効化と、DNS 設定、トラフィック ルーティング設定、またはエンドポイント監視設定の変更が含まれます。<br>Traffic Manager プロファイルによってルーティングされるエンドポイントを追加、削除、有効化、または無効化できます。 |
> | Action | Microsoft.Network/trafficManagerUserMetricsKeys/delete | リアルタイムのユーザー メトリック収集に使用されるサブスクリプション レベルのキーを削除します。 |
> | Action | Microsoft.Network/trafficManagerUserMetricsKeys/read | リアルタイムのユーザー メトリック収集に使用されるサブスクリプション レベルのキーを取得します。 |
> | Action | Microsoft.Network/trafficManagerUserMetricsKeys/write | リアルタイムのユーザー メトリック収集に使用されるサブスクリプション レベルのキーを新しく作成します。 |
> | Action | Microsoft.Network/unregister/action | サブスクリプションの登録を解除します。 |
> | Action | Microsoft.Network/virtualHubs/delete | 仮想ハブを削除します。 |
> | Action | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | HubVirtualNetworkConnection を削除します。 |
> | Action | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | HubVirtualNetworkConnection を取得します。 |
> | Action | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | HubVirtualNetworkConnection を作成または更新します。 |
> | Action | Microsoft.Network/virtualHubs/read | 仮想ハブを取得します。 |
> | Action | Microsoft.Network/virtualHubs/write | 仮想ハブを作成または更新します。 |
> | Action | microsoft.network/virtualnetworkgateways/connections/read | VirtualNetworkGatewayConnection を取得します。 |
> | Action | Microsoft.Network/virtualNetworkGateways/delete | virtualNetworkGateway を削除します。 |
> | Action | microsoft.network/virtualnetworkgateways/generatevpnclientpackage/action | virtualNetworkGateway の VpnClient パッケージを生成します。 |
> | Action | microsoft.network/virtualnetworkgateways/generatevpnprofile/action | VirtualNetworkGateway の VpnProfile パッケージを生成します。 |
> | Action | microsoft.network/virtualnetworkgateways/getadvertisedroutes/action | virtualNetworkGateway でアドバタイズされたルートを取得します。 |
> | Action | microsoft.network/virtualnetworkgateways/getbgppeerstatus/action | virtualNetworkGateway の BGP ピアの状態を取得します。 |
> | Action | microsoft.network/virtualnetworkgateways/getlearnedroutes/action | virtualnetworkgateway で学習されたルートを取得します。 |
> | Action | microsoft.network/virtualnetworkgateways/getvpnclientconnectionhealth/action | VirtualNetworkGateway の VPN クライアント別接続状態を取得します |
> | Action | microsoft.network/virtualnetworkgateways/getvpnclientipsecparameters/action | VirtualNetworkGateway P2S クライアントの Vpnclient Ipsec パラメーターを取得します。 |
> | Action | microsoft.network/virtualnetworkgateways/getvpnprofilepackageurl/action | 事前生成済みの VPN クライアント プロファイル パッケージの URL を取得します。 |
> | Action | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/read | Virtual Network ゲートウェイの診断設定を取得します。 |
> | Action | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/write | Virtual Network ゲートウェイの診断設定を作成または更新します。この操作は、Insghts リソース プロバイダーによって補完されます。 |
> | Action | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/logDefinitions/read | Virtual Network ゲートウェイのイベントを取得します。 |
> | Action | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/metricDefinitions/read | Virtual Network ゲートウェイで使用可能なメトリックを取得します。 |
> | Action | Microsoft.Network/virtualNetworkGateways/read | VirtualNetworkGateway を取得します。 |
> | Action | microsoft.network/virtualnetworkgateways/reset/action | virtualNetworkGateway をリセットします。 |
> | Action | microsoft.network/virtualnetworkgateways/setvpnclientipsecparameters/action | VirtualNetworkGateway P2S クライアントの Vpnclient Ipsec パラメーターを設定します。 |
> | Action | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | サポートされている VPN デバイスを一覧表示します。 |
> | Action | Microsoft.Network/virtualNetworkGateways/write | VirtualNetworkGateway を作成または更新します。 |
> | Action | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | 指定された仮想ネットワークで IP アドレスを使用できるかどうかを確認します。 |
> | Action | Microsoft.Network/virtualNetworks/customViews/get/action | 仮想ネットワークのカスタム ビューの内容を取得します。 |
> | Action | Microsoft.Network/virtualNetworks/customViews/read | 仮想ネットワークのカスタム ビューの定義を取得します。 |
> | Action | Microsoft.Network/virtualNetworks/delete | 仮想ネットワークを削除します。 |
> | Action | Microsoft.Network/virtualNetworks/peer/action | 仮想ネットワークを別の仮想ネットワークとピアリングします。 |
> | Action | Microsoft.Network/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/read | 仮想ネットワークの診断設定を取得します。 |
> | Action | Microsoft.Network/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/write | 仮想ネットワークの診断設定を作成または更新します。 |
> | Action | Microsoft.Network/virtualNetworks/providers/Microsoft.Insights/logDefinitions/read | 仮想ネットワークのログ定義を取得します。 |
> | Action | Microsoft.Network/virtualNetworks/providers/Microsoft.Insights/metricDefinitions/read | PingMesh に使用可能なメトリックを取得します。 |
> | Action | Microsoft.Network/virtualNetworks/read | 仮想ネットワークの定義を取得します。 |
> | Action | Microsoft.Network/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | 仮想ネットワークのピアリング プロキシを削除します。 |
> | Action | Microsoft.Network/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | 仮想ネットワークのピアリング プロキシの定義を取得します。 |
> | Action | Microsoft.Network/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | 仮想ネットワーク ピアリング プロキシを作成するか、既存の仮想ネットワーク ピアリング プロキシを更新します。 |
> | Action | Microsoft.Network/virtualNetworks/subnets/delete | 仮想ネットワーク サブネットを削除します。 |
> | Action | Microsoft.Network/virtualNetworks/subnets/join/action | 仮想ネットワークに参加します。 |
> | Action | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | ストレージ アカウントや SQL Database などのリソースをサブネットに結合します。 |
> | Action | Microsoft.Network/virtualNetworks/subnets/read | 仮想ネットワーク サブネットの定義を取得します。 |
> | Action | Microsoft.Network/virtualNetworks/subnets/resourceNavigationLinks/delete | リソース ナビゲーション リンクを削除します。 |
> | Action | Microsoft.Network/virtualNetworks/subnets/resourceNavigationLinks/read | リソース ナビゲーション リンクの定義を取得します。 |
> | Action | Microsoft.Network/virtualNetworks/subnets/resourceNavigationLinks/write | リソース ナビゲーション リンクを作成するか、既存のリソース ナビゲーション リンクを更新します。 |
> | Action | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/delete | サービスの関連付けリンクを削除します。 |
> | Action | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/details/read | サービスの関連付けリンクの詳細定義を取得します。 |
> | Action | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/read | サービスの関連付けリンクの定義を取得します。 |
> | Action | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/validate/action | サービスの関連付けリンクを検証します。 |
> | Action | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/write | サービスの関連付けリンクを作成するか、既存のサービスの関連付けリンクを更新します。 |
> | Action | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | 仮想ネットワーク サブネットのすべての仮想マシンへの参照を取得します。 |
> | Action | Microsoft.Network/virtualNetworks/subnets/write | 仮想ネットワーク サブネットを作成するか、既存の仮想ネットワーク サブネットを更新します。 |
> | Action | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/delete | タグ付きのトラフィック コンシューマーを削除します。 |
> | Action | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/read | タグ付きのトラフィック コンシューマーの定義を取得します。 |
> | Action | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/validate/action | タグ付きのトラフィック コンシューマーを検証します。 |
> | Action | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/write | タグ付きのトラフィック コンシューマーを作成するか、既存のタグ付きのトラフィック コンシューマーを更新します |
> | Action | Microsoft.Network/virtualNetworks/usages/read | 仮想ネットワークの各サブネットの IP の使用状況を取得します。 |
> | Action | Microsoft.Network/virtualNetworks/virtualMachines/read | 仮想ネットワークのすべての仮想マシンへの参照を取得します。 |
> | Action | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | 仮想ネットワーク ピアリングを削除します。 |
> | Action | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | 仮想ネットワーク ピアリングの定義を取得します。 |
> | Action | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | 仮想ネットワーク ピアリングを作成するか、既存の仮想ネットワーク ピアリングを更新します。 |
> | Action | Microsoft.Network/virtualNetworks/write | 仮想ネットワークを作成するか、既存の仮想ネットワークを更新します。 |
> | Action | Microsoft.Network/virtualNetworkTaps/delete | 仮想ネットワーク タップを削除します。 |
> | Action | Microsoft.Network/virtualNetworkTaps/join/action | 仮想ネットワーク タップに参加します。 |
> | Action | Microsoft.Network/virtualNetworkTaps/read | 仮想ネットワーク タップを取得します。 |
> | Action | Microsoft.Network/virtualNetworkTaps/write | 仮想ネットワーク タップを作成または更新します。 |
> | Action | Microsoft.Network/virtualWans/delete | 仮想 WAN を削除します。 |
> | Action | Microsoft.Network/virtualWans/read | 仮想 WAN を取得します。 |
> | Action | Microsoft.Network/virtualWans/virtualHubProxies/delete | 仮想ハブ プロキシを削除します。 |
> | Action | Microsoft.Network/virtualWans/virtualHubProxies/read | 仮想ハブ プロキシの定義を取得します。 |
> | Action | Microsoft.Network/virtualWans/virtualHubProxies/write | 仮想ハブ プロキシを作成または更新します。 |
> | Action | Microsoft.Network/virtualWans/virtualHubs/read | 仮想 WAN を参照するすべての仮想ハブを取得します。 |
> | Action | Microsoft.Network/virtualwans/vpnconfiguration/action | VPN 構成を取得します。 |
> | Action | Microsoft.Network/virtualWans/vpnSiteProxies/delete | VPN サイト プロキシを削除します。 |
> | Action | Microsoft.Network/virtualWans/vpnSiteProxies/read | VPN サイト プロキシの定義を取得します。 |
> | Action | Microsoft.Network/virtualWans/vpnSiteProxies/write | VPN サイト プロキシを作成または更新します。 |
> | Action | Microsoft.Network/virtualWans/vpnSites/read | 仮想 WAN を参照するすべての VPN サイトを取得します。 |
> | Action | Microsoft.Network/virtualWans/write | 仮想 WAN ハブを作成または更新します。 |
> | Action | Microsoft.Network/vpnGateways/delete | VpnGateway を削除します。 |
> | Action | Microsoft.Network/vpnGateways/read | VpnGateway を取得します。 |
> | Action | microsoft.network/vpnGateways/vpnConnections/read | VpnConnection を取得します。 |
> | Action | microsoft.network/vpnGateways/vpnConnections/write | VpnConnection を設定します。 |
> | Action | Microsoft.Network/vpnGateways/write | VpnGateway を設定します。 |
> | Action | Microsoft.Network/vpnsites/delete | VPN サイト リソースを削除します。 |
> | Action | Microsoft.Network/vpnsites/read | VPN サイト リソースを取得します。 |
> | Action | Microsoft.Network/vpnsites/write | VPN サイト リソースを作成または更新します。 |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | 指定された名前空間リソース名を NotificationHub サービス内で使用できるかどうかを確認します。 |
> | Action | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | 名前空間の承認規則の説明の一覧を取得します。 |
> | Action | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | 名前空間の承認規則を削除します。 名前空間の既定の承認規則は削除できません。  |
> | Action | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | 名前空間への接続文字列を取得します。 |
> | Action | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | 名前空間の承認規則の説明の一覧を取得します。 |
> | Action | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | 名前空間の承認規則では、Primary/SecondaryKey を再生成します。再生成する必要があるキーを指定します。 |
> | Action | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | 名前空間レベルの承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権、プライマリ キー、セカンダリ キーを更新できます。 |
> | Action | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | 指定された NotificationHub 名を名前空間内で使用できるかどうかを確認します。 |
> | Action | Microsoft.NotificationHubs/Namespaces/Delete | 名前空間リソースを削除します。 |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | Notification Hub の承認規則の一覧を取得します。 |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | Notification Hub の承認規則を削除します。 |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | Notification Hub への接続文字列を取得します。 |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | Notification Hub の承認規則の一覧を取得します。 |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | Notification Hub の承認規則では、Primary/SecondaryKey を再生成します。再生成する必要があるキーを指定します。 |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | Notification Hub の承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権、プライマリ キー、セカンダリ キーを更新できます。 |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | テスト プッシュ通知を送信します。 |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | Notification Hub リソースを削除します。 |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | 名前空間のメトリック リソースの説明の一覧を取得します。 |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | Notification Hub の PNS の資格情報をすべて取得します。 これには、WNS、MPNS、APNS、GCM、Baidu の各資格情報が含まれます。 |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | Notification Hub リソースの説明の一覧を取得します。 |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | Notification Hub を作成し、そのプロパティを更新します。 プロパティには、主に、PNS 資格情報、 承認規則、TTL が含まれます。 |
> | Action | Microsoft.NotificationHubs/Namespaces/read | 名前空間リソースの説明の一覧を取得します。 |
> | Action | Microsoft.NotificationHubs/Namespaces/write | 名前空間リソースを作成し、そのプロパティを更新します。 名前空間のタグとキャパシティは、更新できるプロパティです。 |
> | Action | Microsoft.NotificationHubs/operationResults/read | Notification Hubs プロバイダーの操作の結果を返します |
> | Action | Microsoft.NotificationHubs/operations/read | Notification Hubs プロバイダーでサポートされている操作の一覧を返します |
> | Action | Microsoft.NotificationHubs/register/action | NotifciationHubs リソース プロバイダーにサブスクリプションを登録し、名前空間と NotificationHubs を作成できるようにします。 |
> | Action | Microsoft.NotificationHubs/unregister/action | NotifciationHubs リソース プロバイダーのサブスクリプションを登録解除し、名前空間と NotificationHubs を作成できるようにします |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.OffAzure/HyperVSites/clusters/read | Hyper-V クラスターのプロパティを取得します。 |
> | Action | Microsoft.OffAzure/HyperVSites/clusters/read | Hyper-V クラスターのプロパティを取得します。 |
> | Action | Microsoft.OffAzure/HyperVSites/clusters/write | Hyper-V クラスターを作成または更新します。 |
> | Action | Microsoft.OffAzure/HyperVSites/clusters/write | Hyper-V クラスターを作成または更新します。 |
> | Action | Microsoft.OffAzure/HyperVSites/delete | Hyper-V サイトを削除します。 |
> | Action | Microsoft.OffAzure/HyperVSites/delete | Hyper-V サイトを削除します。 |
> | Action | Microsoft.OffAzure/HyperVSites/hosts/read | Hyper-V ホストのプロパティを取得します。 |
> | Action | Microsoft.OffAzure/HyperVSites/hosts/read | Hyper-V ホストのプロパティを取得します。 |
> | Action | Microsoft.OffAzure/HyperVSites/hosts/write | Hyper-V ホストを作成または更新します。 |
> | Action | Microsoft.OffAzure/HyperVSites/hosts/write | Hyper-V ホストを作成または更新します。 |
> | Action | Microsoft.OffAzure/HyperVSites/jobs/read | Hyper-V ジョブのプロパティを取得します。 |
> | Action | Microsoft.OffAzure/HyperVSites/jobs/read | Hyper-V ジョブのプロパティを取得します。 |
> | Action | Microsoft.OffAzure/HyperVSites/machines/read | Hyper-V マシンのプロパティを取得します。 |
> | Action | Microsoft.OffAzure/HyperVSites/machines/read | Hyper-V マシンのプロパティを取得します。 |
> | Action | Microsoft.OffAzure/HyperVSites/machines/stop/action | Hyper-V マシンを停止します。 |
> | Action | Microsoft.OffAzure/HyperVSites/machines/stop/action | Hyper-V マシンを停止します。 |
> | Action | Microsoft.OffAzure/HyperVSites/operationsstatus/read | Hyper-V 操作の状態のプロパティを取得します。 |
> | Action | Microsoft.OffAzure/HyperVSites/operationsstatus/read | Hyper-V 操作の状態のプロパティを取得します。 |
> | Action | Microsoft.OffAzure/HyperVSites/read | Hyper-V サイトのプロパティを取得します。 |
> | Action | Microsoft.OffAzure/HyperVSites/read | Hyper-V サイトのプロパティを取得します。 |
> | Action | Microsoft.OffAzure/HyperVSites/refresh/action | Hyper-V サイト内のオブジェクトを更新します。 |
> | Action | Microsoft.OffAzure/HyperVSites/refresh/action | Hyper-V サイト内のオブジェクトを更新します。 |
> | Action | Microsoft.OffAzure/HyperVSites/runasaccounts/read | Hyper-V の実行のプロパティをアカウントとして取得します。 |
> | Action | Microsoft.OffAzure/HyperVSites/runasaccounts/read | Hyper-V の実行のプロパティをアカウントとして取得します。 |
> | Action | Microsoft.OffAzure/HyperVSites/usage/read | Hyper-V サイトの使用状況を取得します。 |
> | Action | Microsoft.OffAzure/HyperVSites/usage/read | Hyper-V サイトの使用状況を取得します。 |
> | Action | Microsoft.OffAzure/HyperVSites/write | Hyper-V サイトを作成または更新します。 |
> | Action | Microsoft.OffAzure/HyperVSites/write | Hyper-V サイトを作成または更新します。 |
> | Action | Microsoft.OffAzure/Operations/read | 公開された操作を読み取ります。 |
> | Action | Microsoft.OffAzure/VMwareSites/delete | VMware サイトを削除します。 |
> | Action | Microsoft.OffAzure/VMwareSites/delete | VMware サイトを削除します。 |
> | Action | Microsoft.OffAzure/VMwareSites/jobs/read | VMware ジョブのプロパティを取得します。 |
> | Action | Microsoft.OffAzure/VMwareSites/jobs/read | VMware ジョブのプロパティを取得します。 |
> | Action | Microsoft.OffAzure/VMwareSites/machines/read | VMware マシンのプロパティを取得します。 |
> | Action | Microsoft.OffAzure/VMwareSites/machines/read | VMware マシンのプロパティを取得します。 |
> | Action | Microsoft.OffAzure/VMwareSites/machines/stop/action | VMware マシンを停止します。 |
> | Action | Microsoft.OffAzure/VMwareSites/machines/stop/action | VMware マシンを停止します。 |
> | Action | Microsoft.OffAzure/VMwareSites/operationsstatus/read | VMware 操作の状態のプロパティを取得します。 |
> | Action | Microsoft.OffAzure/VMwareSites/operationsstatus/read | VMware 操作の状態のプロパティを取得します。 |
> | Action | Microsoft.OffAzure/VMwareSites/read | VMware サイトのプロパティを取得します。 |
> | Action | Microsoft.OffAzure/VMwareSites/read | VMware サイトのプロパティを取得します。 |
> | Action | Microsoft.OffAzure/VMwareSites/refresh/action | VMware サイト内のオブジェクトを更新します。 |
> | Action | Microsoft.OffAzure/VMwareSites/refresh/action | VMware サイト内のオブジェクトを更新します。 |
> | Action | Microsoft.OffAzure/VMwareSites/runasaccounts/read | VMware の実行のプロパティをアカウントとして取得します。 |
> | Action | Microsoft.OffAzure/VMwareSites/runasaccounts/read | VMware の実行のプロパティをアカウントとして取得します。 |
> | Action | Microsoft.OffAzure/VMwareSites/usage/read | VMware サイトの使用状況を取得します。 |
> | Action | Microsoft.OffAzure/VMwareSites/usage/read | VMware サイトの使用状況を取得します。 |
> | Action | Microsoft.OffAzure/VMwareSites/vcenters/read | VMware vCenter のプロパティを取得します。 |
> | Action | Microsoft.OffAzure/VMwareSites/vcenters/read | VMware vCenter のプロパティを取得します。 |
> | Action | Microsoft.OffAzure/VMwareSites/vcenters/write | VMware vCenter を作成または更新します。 |
> | Action | Microsoft.OffAzure/VMwareSites/vcenters/write | VMware vCenter を作成または更新します。 |
> | Action | Microsoft.OffAzure/VMwareSites/write | VMware サイトを作成または更新します。 |
> | Action | Microsoft.OffAzure/VMwareSites/write | VMware サイトを作成または更新します。 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.OperationalInsights/linkTargets/read | Azure サブスクリプションに関連付けられていない既存のアカウントを一覧表示します。 この Azure サブスクリプションをワークスペースにリンクするには、この操作で返される顧客 ID を、"ワークスペースの作成" 操作の顧客 ID プロパティで使用します。 |
> | Action | Microsoft.OperationalInsights/register/action | リソース プロバイダーにサブスクリプションを登録します。 |
> | Action | Microsoft.OperationalInsights/workspaces/analytics/query/action | 新しいエンジンを使用して検索します。 |
> | Action | Microsoft.OperationalInsights/workspaces/analytics/query/schema/read | 検索スキーマ V2 を取得します。 |
> | Action | Microsoft.OperationalInsights/workspaces/api/query/action | 新しいエンジンを使用して検索します。 |
> | Action | Microsoft.OperationalInsights/workspaces/api/query/schema/read | 検索スキーマ V2 を取得します。 |
> | Action | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | 構成スコープを削除します。 |
> | Action | Microsoft.OperationalInsights/workspaces/configurationScopes/read | 構成スコープを取得します。 |
> | Action | Microsoft.OperationalInsights/workspaces/configurationScopes/write | 構成スコープを設定します。 |
> | Action | Microsoft.OperationalInsights/workspaces/datasources/delete | ワークスペースのデータソースを削除します。 |
> | Action | Microsoft.OperationalInsights/workspaces/datasources/read | ワークスペースのデータソースを取得します。 |
> | Action | Microsoft.OperationalInsights/workspaces/datasources/write | ワークスペースのデータソースを作成または更新します。 |
> | Action | Microsoft.OperationalInsights/workspaces/delete | ワークスペースを削除します。 ワークスペースが、作成時に既存のワークスペースにリンクされていた場合、リンク先のワークスペースは削除されません。 |
> | Action | Microsoft.OperationalInsights/workspaces/generateregistrationcertificate/action | ワークスペースの登録証明書を生成します。 この証明書を使用して、Microsoft System Center Operation Manager をワークスペースに接続します。 |
> | Action | Microsoft.OperationalInsights/workspaces/intelligencepacks/disable/action | 指定されたワークスペースのインテリジェンス パックを無効にします。 |
> | Action | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | 指定されたワークスペースのインテリジェンス パックを有効にします。 |
> | Action | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | 指定されたワークスペースで表示されるすべてのインテリジェンス パックを一覧表示します。また、そのワークスペースでパックが有効になっているか無効になっているかも示します。 |
> | Action | Microsoft.OperationalInsights/workspaces/linkedServices/delete | 指定されたワークスペースのリンクされたサービスを削除します。 |
> | Action | Microsoft.OperationalInsights/workspaces/linkedServices/read | 指定されたワークスペースのリンクされたサービスを取得します。 |
> | Action | Microsoft.OperationalInsights/workspaces/linkedServices/write | 指定されたワークスペースのリンクされたサービスを作成または更新します。 |
> | Action | Microsoft.OperationalInsights/workspaces/listKeys/action | ワークスペースの一覧キーを取得します。 これらのキーを使用して、Microsoft Operational Insights エージェントをワークスペースに接続します。 |
> | Action | Microsoft.OperationalInsights/workspaces/listKeys/read | ワークスペースの一覧キーを取得します。 これらのキーを使用して、Microsoft Operational Insights エージェントをワークスペースに接続します。 |
> | Action | Microsoft.OperationalInsights/workspaces/managementGroups/read | このワークスペースに接続されている System Center Operations Manager 管理グループの名前とメタデータを取得します。 |
> | Action | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | ワークスペースのメトリック定義を取得します。 |
> | Action | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | ワークスペースのユーザーの通知設定を削除します。 |
> | Action | Microsoft.OperationalInsights/workspaces/notificationSettings/read | ワークスペースのユーザーの通知設定を取得します。 |
> | Action | Microsoft.OperationalInsights/workspaces/notificationSettings/write | ワークスペースのユーザーの通知設定を設定します。 |
> | Action | Microsoft.OperationalInsights/workspaces/purge/action | ワークスペースから指定されたデータを削除します。 |
> | Action | Microsoft.OperationalInsights/workspaces/read | 既存のワークスペースを取得します。 |
> | Action | Microsoft.OperationalInsights/workspaces/savedSearches/delete | 保存された検索クエリを削除します。 |
> | Action | Microsoft.OperationalInsights/workspaces/savedSearches/read | 保存された検索クエリを取得します。 |
> | Action | Microsoft.OperationalInsights/workspaces/savedSearches/write | 保存された検索クエリを作成します。 |
> | Action | Microsoft.OperationalInsights/workspaces/schema/read | ワークスペースの検索スキーマを取得します。  検索スキーマには、公開されているフィールドとその種類が含まれます。 |
> | Action | Microsoft.OperationalInsights/workspaces/search/action | 検索クエリを実行します。 |
> | Action | Microsoft.OperationalInsights/workspaces/sharedKeys/action | ワークスペースの共有キーを取得します。 これらのキーを使用して、Microsoft Operational Insights エージェントをワークスペースに接続します。 |
> | Action | Microsoft.OperationalInsights/workspaces/sharedKeys/read | ワークスペースの共有キーを取得します。 これらのキーを使用して、Microsoft Operational Insights エージェントをワークスペースに接続します。 |
> | Action | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/delete | ストレージ構成を削除します。 これにより、Microsoft Operational Insights はストレージ アカウントからデータを読み取らなくなります。 |
> | Action | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/read | ストレージ構成を取得します。 |
> | Action | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/write | 新しいストレージ構成を作成します。 これらの構成を使用して、既存のストレージ アカウント内の場所からデータを取得します。 |
> | Action | Microsoft.OperationalInsights/workspaces/usages/read | ワークスペースで読み取られたデータの量など、ワークスペースの使用状況データを取得します。 |
> | Action | Microsoft.OperationalInsights/workspaces/write | 新しいワークスペースを作成するか、既存のワークスペースの顧客 ID を指定して既存のワークスペースにリンクします。 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.OperationsManagement/managementAssociations/delete | 既存の管理関連付けを削除します。 |
> | Action | Microsoft.OperationsManagement/managementAssociations/read | 既存の管理関連付けを取得します。 |
> | Action | Microsoft.OperationsManagement/managementAssociations/write | 新しい管理関連付けを作成します。 |
> | Action | Microsoft.OperationsManagement/managementConfigurations/delete | 既存の管理構成を削除します。 |
> | Action | Microsoft.OperationsManagement/managementConfigurations/read | 既存の管理構成を取得します。 |
> | Action | Microsoft.OperationsManagement/managementConfigurations/write | 新しい管理構成を作成します。 |
> | Action | Microsoft.OperationsManagement/register/action | リソース プロバイダーにサブスクリプションを登録します。 |
> | Action | Microsoft.OperationsManagement/solutions/delete | 既存の OMS ソリューションを削除します。 |
> | Action | Microsoft.OperationsManagement/solutions/read | 既存の OMS ソリューションを取得します。 |
> | Action | Microsoft.OperationsManagement/solutions/write | 新しい OMS ソリューションを作成します。 |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.PolicyInsights/asyncOperationResults/read | 非同期操作の結果を取得します。 |
> | Action | Microsoft.PolicyInsights/policyEvents/queryResults/action | ポリシー イベントに関する情報のクエリを実行します。 |
> | Action | Microsoft.PolicyInsights/policyStates/queryResults/action | ポリシーの状態に関する情報のクエリを実行します。 |
> | Action | Microsoft.PolicyInsights/policyStates/summarize/action | ポリシーの最新の状態に関する概要情報のクエリを実行します。 |
> | Action | Microsoft.PolicyInsights/policyStates/triggerEvaluation/action | 選択したスコープの新たなコンプライアンス評価をトリガーします。 |
> | Action | Microsoft.PolicyInsights/register/action | Policy Insights リソース プロバイダーを登録して、それに対するアクションを有効にします。 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.Portal/dashboards/delete | サブスクリプションからダッシュボードを削除します。 |
> | Action | Microsoft.Portal/dashboards/read | サブスクリプションのダッシュボードを読み取ります。 |
> | Action | Microsoft.Portal/dashboards/write | サブスクリプションのダッシュボードを追加または変更します。 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.PowerBIDedicated/capacities/checkNameAvailability/action | 指定された Power BI 専用容量の名前が有効であり使用されていないことを確認します。 |
> | Action | Microsoft.PowerBIDedicated/capacities/delete | Power BI 専用容量を削除します。 |
> | Action | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Action | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Action | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/logDefinitions/read | Power BI 専用容量の使用可能なログを取得します。 |
> | Action | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/metricDefinitions/read | Power BI 専用容量で使用可能なメトリックを取得します。 |
> | Action | Microsoft.PowerBIDedicated/capacities/read | 指定された Power BI 専用容量の情報を取得します。 |
> | Action | Microsoft.PowerBIDedicated/capacities/resume/action | 容量を再開します。 |
> | Action | Microsoft.PowerBIDedicated/capacities/skus/read | 容量の使用可能な SKU 情報を取得します。 |
> | Action | Microsoft.PowerBIDedicated/capacities/suspend/action | 容量を一時停止します。 |
> | Action | Microsoft.PowerBIDedicated/capacities/write | 指定された Power BI 専用容量を作成または更新します。 |
> | Action | Microsoft.PowerBIDedicated/locations/operationresults/read | 指定された操作の結果の情報を取得します。 |
> | Action | Microsoft.PowerBIDedicated/locations/operationstatuses/read | 指定された操作の状態の情報を取得します。 |
> | Action | Microsoft.PowerBIDedicated/operations/read | 操作の情報を取得します。 |
> | Action | Microsoft.PowerBIDedicated/register/action | Power BI 専用リソース プロバイダーを登録します。 |
> | Action | Microsoft.PowerBIDedicated/skus/read | SKU の情報を取得します。 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp は、サービスによって使用される内部操作です。 |
> | Action | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp は、サービスによって使用される内部操作です。 |
> | Action | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Action | Microsoft.RecoveryServices/locations/backupStatus/action | Recovery Services コンテナーのバックアップの状態を確認します。 |
> | Action | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | 機能を検証します。 |
> | Action | Microsoft.RecoveryServices/locations/operationStatus/read | 特定の操作の操作の状態を取得します |
> | Action | Microsoft.RecoveryServices/operations/read | リソース プロバイダーの操作の一覧を返します。 |
> | Action | Microsoft.RecoveryServices/register/action | 指定されたリソース プロバイダーにサブスクリプションを登録します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupconfig/read | Recovery Services コンテナーの構成を返します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupconfig/write | Recovery Services コンテナーの構成を更新します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupEngines/read | コンテナーに登録されているすべてのバックアップ管理サーバーを返します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/delete | バックアップ保護の意図を削除します |
> | Action | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | バックアップ保護の意図を取得します |
> | Action | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | バックアップの保護インテントを作成します |
> | Action | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | 操作の状態を返します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | すべての保護可能なコンテナーを取得します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/delete | 登録済みコンテナーを削除します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | コンテナー内のワークロードを照会します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | コンテナー内のすべての項目を取得します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | 保護コンテナーに対して実行された操作の結果を取得します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | 保護された項目のバックアップを実行します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | 保護された項目を削除します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | 保護された項目に対して実行された操作の結果を取得します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | 保護された項目に対して実行された操作の状態を返します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | 保護された項目のオブジェクトの詳細を返します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | 保護された項目のインスタント項目回復をプロビジョニングします。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | 保護された項目の復旧ポイントを取得します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | 保護された項目の復旧ポイントを復元します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | 保護された項目のインスタント項目回復を取り消します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | バックアップ保護項目を作成します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | すべての登録済みコンテナーを返します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | 登録済みコンテナーを作成します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | コンテナーの一覧を更新します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | ジョブを取り消します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | ジョブ操作の結果を返します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupJobs/read | すべてのジョブ オブジェクトを返します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | ジョブをエクスポートします。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | "ジョブのエクスポート" 操作の結果を返します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Recovery Services コンテナーのバックアップ管理メタデータを返します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Recovery Services コンテナーに対するバックアップ操作の結果を返します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupOperations/read | Recovery Services コンテナーに対するバックアップ操作の状態を返します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupPolicies/delete | 保護ポリシーを削除します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | ポリシー操作の結果を取得します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | ポリシー操作の状態を取得します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupPolicies/read | すべての保護ポリシーを返します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupPolicies/write | 保護ポリシーを作成します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | すべての保護可能な項目の一覧を返します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | すべての保護された項目の一覧を返します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | サブスクリプションに属するすべてのコンテナーを返します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | すべてのバックアップ保護の意図を一覧表示します |
> | Action | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/action | Recovery Services コンテナーのセキュリティ PIN 情報を返します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Recovery Services コンテナーのストレージ構成を返します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupstorageconfig/write | Recovery Services コンテナーのストレージ構成を更新します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Recovery Services の保護された項目と保護されたサーバーの概要を返します。 |
> | Action | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | 保護された項目に対する操作を検証します |
> | Action | Microsoft.RecoveryServices/Vaults/certificates/write | "リソース証明書を更新" 操作では、リソース/コンテナー資格情報証明書を更新します。 |
> | Action | Microsoft.RecoveryServices/Vaults/delete | "コンテナーの削除" 操作では、指定された "コンテナー" 型の Azure リソースを削除します。 |
> | Action | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | "拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。 |
> | Action | Microsoft.RecoveryServices/Vaults/extendedInformation/read | "拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。 |
> | Action | Microsoft.RecoveryServices/Vaults/extendedInformation/write | "拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。 |
> | Action | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Recovery Services コンテナーのアラートを取得します。 |
> | Action | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | アラートを解決する。 |
> | Action | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | Recovery Services コンテナーの通知構成を取得します。 |
> | Action | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | Recovery Services コンテナーへのメール通知を構成します。 |
> | Action | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/diagnosticSettings/read | Azure Backup 診断です。 |
> | Action | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/diagnosticSettings/write | Azure Backup 診断です。 |
> | Action | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/logDefinitions/read | Azure Backup ログです。 |
> | Action | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/metricDefinitions/read | Azure Backup メトリックです。 |
> | Action | Microsoft.RecoveryServices/Vaults/read | "コンテナーの取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトを取得します。 |
> | Action | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | "コンテナーの登録を解除" 操作を使用すると、コンテナーの登録を解除できます。 |
> | Action | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | "操作結果を取得" 操作を使用すると、非同期で送信された操作の状態と結果を取得できます。 |
> | Action | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | " コンテナーを取得" 操作を使用すると、リソースの登録済みコンテナーを取得できます。 |
> | Action | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | "サービス コンテナーを登録" 操作を使用すると、コンテナーを Recovery Services に登録できます。 |
> | Action | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | アラート設定を読み取ります |
> | Action | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | アラート設定を作成または更新します |
> | Action | Microsoft.RecoveryServices/vaults/replicationEvents/read | イベントを読み取ります |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | ファブリックの一貫性を確認します。 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | ファブリックをすべて削除します |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | プロセス サーバー イメージをデプロイします。 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/migratetoaad/action | ファブリックを AAD に移行します |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/read | ファブリックを読み取ります |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | ゲートウェイを再関連付けします。 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | ファブリックを削除します。 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | ファブリックの証明書を更新します。 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationLogicalNetworks/read | 論理ネットワークを読み取ります |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | ネットワークを読み取ります |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | ネットワーク マッピングをすべて削除します |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | ネットワーク マッピングを読み取ります |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | ネットワーク マッピングを作成または更新します |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | 保護可能な項目を検出します。 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | 保護コンテナーを読み取ります |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | 保護コンテナーを削除します。 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | 保護可能な項目を読み取ります |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | 復旧ポイントを適用します。 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | 保護された項目をすべて削除します |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | フェールオーバーのコミット。 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | 計画されたフェールオーバー。 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | 保護された項目を読み取ります |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | レプリケーションの復旧ポイントを読み取ります |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | 保護された項目を削除します。 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | レプリケーションを修復します。 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | 保護された項目を再保護します。 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/action | フィードバックを送信します |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/read | ターゲット コンピューティング サイズを読み取ります |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | テスト フェールオーバー |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | テスト フェールオーバーのクリーンアップ。 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | フェールオーバー |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | モビリティ サービスを更新します。 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | 保護された項目を作成または更新します |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | 保護コンテナー マッピングをすべて削除します |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | 保護コンテナー マッピングを読み取ります |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | 保護コンテナー マッピングを削除します。 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | 保護コンテナー マッピングを作成または更新します |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | 保護コンテナーを切り替えます。 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | 保護コンテナーを作成または更新します |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | Recovery Services プロバイダーをすべて削除します |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Recovery Services プロバイダーを読み取ります |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | プロバイダーを更新します。 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | Recovery Services プロバイダーを削除します。 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | Recovery Services プロバイダーを作成または更新します |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | ストレージの分類を読み取ります |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | ストレージ分類マッピングをすべて削除します |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | ストレージ分類マッピングを読み取ります |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | ストレージ分類マッピングを作成または更新します |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | vCenter をすべて削除します |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | vCenter を読み取ります |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | vCenter を作成または更新します |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/write | ファブリックを作成または更新します |
> | Action | Microsoft.RecoveryServices/vaults/replicationJobs/cancel/action | ジョブを取り消します。 |
> | Action | Microsoft.RecoveryServices/vaults/replicationJobs/read | ジョブを読み取ります |
> | Action | Microsoft.RecoveryServices/vaults/replicationJobs/restart/action | ジョブを再実行します。 |
> | Action | Microsoft.RecoveryServices/vaults/replicationJobs/resume/action | ジョブを再開します。 |
> | Action | Microsoft.RecoveryServices/vaults/replicationNetworkMappings/read | ネットワーク マッピングを読み取ります |
> | Action | Microsoft.RecoveryServices/vaults/replicationNetworks/read | ネットワークを読み取ります |
> | Action | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | ポリシーをすべて削除します |
> | Action | Microsoft.RecoveryServices/vaults/replicationPolicies/read | ポリシーを読み取ります |
> | Action | Microsoft.RecoveryServices/vaults/replicationPolicies/write | ポリシーを作成または更新します |
> | Action | Microsoft.RecoveryServices/vaults/replicationProtectedItems/read | 保護された項目を読み取ります |
> | Action | Microsoft.RecoveryServices/vaults/replicationProtectionContainerMappings/read | 保護コンテナー マッピングを読み取ります |
> | Action | Microsoft.RecoveryServices/vaults/replicationProtectionContainers/read | 保護コンテナーを読み取ります |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/delete | 復旧計画をすべて削除します |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | フェールオーバーのコミットの復旧計画。 |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | 計画されたフェールオーバーの復旧計画。 |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | 復旧計画を読み取ります |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | 再保護の復旧計画。 |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | テスト フェールオーバーの復旧計画。 |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | テスト フェールオーバーのクリーンアップの復旧計画。 |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | フェールオーバーの復旧計画。 |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | 復旧計画を作成または更新します |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryServicesProviders/read | Recovery Services プロバイダーを読み取ります |
> | Action | Microsoft.RecoveryServices/vaults/replicationStorageClassificationMappings/read | ストレージ分類マッピングを読み取ります |
> | Action | Microsoft.RecoveryServices/vaults/replicationStorageClassifications/read | ストレージの分類を読み取ります |
> | Action | Microsoft.RecoveryServices/vaults/replicationUsages/read | コンテナー レプリケーションの使用状況を読み取ります |
> | Action | Microsoft.RecoveryServices/vaults/replicationVaultHealth/read | コンテナー レプリケーションの正常性を読み取ります |
> | Action | Microsoft.RecoveryServices/vaults/replicationVaultHealth/refresh/action | コンテナーの正常性を更新します |
> | Action | Microsoft.RecoveryServices/vaults/replicationvCenters/read | vCenter を読み取ります |
> | Action | Microsoft.RecoveryServices/Vaults/tokenInfo/read | Recovery Services コンテナーのトークン情報を返します。 |
> | Action | Microsoft.RecoveryServices/vaults/usages/read | コンテナーの使用状況を読み取ります |
> | Action | Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services コンテナーの使用状況の詳細を返します。 |
> | Action | Microsoft.RecoveryServices/Vaults/vaultTokens/read | "コンテナー トークン" 操作を使用すると、コンテナー レベルのバックエンド操作のコンテナー トークンを取得できます。 |
> | Action | Microsoft.RecoveryServices/Vaults/write | "コンテナーの作成" 操作では、"コンテナー" 型の Azure リソースを作成します。 |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.Relay/checkNameAvailability/action | 特定のサブスクリプションで名前空間を使用できるかどうかを確認します。 |
> | Action | Microsoft.Relay/checkNamespaceAvailability/action | 特定のサブスクリプションで名前空間を使用できるかどうかを確認します。 この API は非推奨です。代わりに CheckNameAvailabiltiy を使用してください。 |
> | Action | Microsoft.Relay/namespaces/authorizationRules/action | 名前空間の承認規則を更新します。 この API は使用されていません。 名前空間の承認規則を更新するには、代わりに PUT 呼び出しを使用してください。 この操作は、API バージョン 2017-04-01 ではサポートされていません。 |
> | Action | Microsoft.Relay/namespaces/authorizationRules/delete | 名前空間の承認規則を削除します。 名前空間の既定の承認規則は削除できません。  |
> | Action | Microsoft.Relay/namespaces/authorizationRules/listkeys/action | 名前空間への接続文字列を取得します。 |
> | Action | Microsoft.Relay/namespaces/authorizationRules/read | 名前空間の承認規則の説明の一覧を取得します。 |
> | Action | Microsoft.Relay/namespaces/authorizationRules/regenerateKeys/action | リソースのプライマリ キーまたはセカンダリ キーを再生成します。 |
> | Action | Microsoft.Relay/namespaces/authorizationRules/write | 名前空間レベルの承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権、プライマリ キー、セカンダリ キーを更新できます。 |
> | Action | Microsoft.Relay/namespaces/Delete | 名前空間リソースを削除します。 |
> | Action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | ディザスター リカバリーのプライマリ名前空間の承認規則キーを取得します。 |
> | Action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | ディザスター リカバリーのプライマリ名前空間の承認規則を取得します。 |
> | Action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/breakPairing/action | ディザスター リカバリーを無効にし、プライマリ名前空間からセカンダリ名前空間への変更の複製を停止します。 |
> | Action | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | 指定されたサブスクリプションで名前空間別名を使用できるかどうかを確認します。 |
> | Action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | 名前空間に関連付けられているディザスター リカバリー構成を削除します。 この操作は、プライマリ名前空間からのみ呼び出すことができます。 |
> | Action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | geo DR フェールオーバーを呼び出し、セカンダリ名前空間を指すように名前空間の別名を再構成します。 |
> | Action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | 名前空間に関連付けられているディザスター リカバリーの構成を取得します。 |
> | Action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | 名前空間に関連付けられているディザスター リカバリーの構成を作成または更新します。 |
> | Action | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/action | HybridConnection を更新する操作です。 この操作は、API バージョン 2017-04-01 ではサポートされていません。 承認規則。 承認規則を更新するには、PUT 呼び出しを使用してください。 |
> | Action | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/delete | HybridConnection の承認規則を削除する操作。 |
> | Action | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/listkeys/action | HybridConnection への接続文字列を取得します。 |
> | Action | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/read |  HybridConnection 承認規則の一覧を取得します。 |
> | Action | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/action | リソースのプライマリ キーまたはセカンダリ キーを再生成します。 |
> | Action | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/write | HybridConnection の承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権を更新できます。 |
> | Action | Microsoft.Relay/namespaces/HybridConnections/Delete | HybridConnection リソースを削除する操作。 |
> | Action | Microsoft.Relay/namespaces/HybridConnections/read | HybridConnection リソースの説明の一覧を取得します。 |
> | Action | Microsoft.Relay/namespaces/HybridConnections/write | HybridConnection プロパティを作成または更新します。 |
> | Action | Microsoft.Relay/namespaces/messagingPlan/read | 名前空間のメッセージング プランを取得します。<br>この API は非推奨です。<br>より新しい API バージョンでは、MessagingPlan リソースにより公開されるプロパティは (親) 名前空間リソースに移動されています。<br>この操作は、API バージョン 2017-04-01 ではサポートされていません。 |
> | Action | Microsoft.Relay/namespaces/messagingPlan/write | 名前空間のメッセージング プランを更新します。<br>この API は非推奨です。<br>より新しい API バージョンでは、MessagingPlan リソースにより公開されるプロパティは (親) 名前空間リソースに移動されています。<br>この操作は、API バージョン 2017-04-01 ではサポートされていません。 |
> | Action | Microsoft.Relay/namespaces/operationresults/read | 名前空間の操作の状態を取得します。 |
> | Action | Microsoft.Relay/namespaces/providers/Microsoft.Insights/metricDefinitions/read | 名前空間のメトリック リソースの説明の一覧を取得します。 |
> | Action | Microsoft.Relay/namespaces/read | 名前空間リソースの説明の一覧を取得します。 |
> | Action | Microsoft.Relay/namespaces/removeAcsNamepsace/action | ACS 名前空間を削除します。 |
> | Action | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/action | WcfRelay を更新する操作です。 この操作は、API バージョン 2017-04-01 ではサポートされていません。 承認規則。 承認規則を更新するには、PUT 呼び出しを使用してください。 |
> | Action | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/delete | WcfRelay の承認規則を削除する操作。 |
> | Action | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/listkeys/action | WcfRelay への接続文字列を取得します。 |
> | Action | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/read |  WcfRelay 承認規則の一覧を取得します。 |
> | Action | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/action | リソースのプライマリ キーまたはセカンダリ キーを再生成します。 |
> | Action | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/write | WcfRelay の承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権を更新できます。 |
> | Action | Microsoft.Relay/namespaces/WcfRelays/Delete | WcfRelay リソースを削除する操作。 |
> | Action | Microsoft.Relay/namespaces/WcfRelays/read | WcfRelay リソースの説明の一覧を取得します。 |
> | Action | Microsoft.Relay/namespaces/WcfRelays/write | WcfRelay のプロパティを作成または更新します。 |
> | Action | Microsoft.Relay/namespaces/write | 名前空間リソースを作成し、そのプロパティを更新します。 名前空間のタグとキャパシティは、更新できるプロパティです。 |
> | Action | Microsoft.Relay/operations/read | 操作を取得します。 |
> | Action | Microsoft.Relay/register/action | Relay リソース プロバイダーにサブスクリプションを登録し、リレー リソースを作成できるようにします。 |
> | Action | Microsoft.Relay/unregister/action | Relay リソース プロバイダーにサブスクリプションを登録し、リレー リソースを作成できるようにします。 |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.ResourceHealth/AvailabilityStatuses/current/read | 指定されたリソースの利用状況を取得します。 |
> | Action | Microsoft.ResourceHealth/AvailabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Action | Microsoft.ResourceHealth/events/read | 指定されたサブスクリプションについてサービスの正常性イベントを取得します。 |
> | Action | Microsoft.Resourcehealth/healthevent/action | 指定されたリソースの正常性の状態の変更を表します。 |
> | Action | Microsoft.Resourcehealth/healthevent/Activated/action | 指定されたリソースの正常性の状態の変更を表します。 |
> | Action | Microsoft.Resourcehealth/healthevent/InProgress/action | 指定されたリソースの正常性の状態の変更を表します。 |
> | Action | Microsoft.Resourcehealth/healthevent/Pending/action | 指定されたリソースの正常性の状態の変更を表します。 |
> | Action | Microsoft.Resourcehealth/healthevent/Resolved/action | 指定されたリソースの正常性の状態の変更を表します。 |
> | Action | Microsoft.Resourcehealth/healthevent/Updated/action | 指定されたリソースの正常性の状態の変更を表します。 |
> | Action | Microsoft.ResourceHealth/impactedResources/read | 指定されたサブスクリプションについて影響を受けるリソースを取得します。 |
> | Action | Microsoft.ResourceHealth/register/action | Microsoft ResourceHealth にサブスクリプションを登録します。 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.Resources/checkPolicyCompliance/action | 指定されたリソースのコンプライアンス状態をリソース ポリシーと照合します。 |
> | Action | Microsoft.Resources/checkResourceName/action | リソース名の妥当性を確認します。 |
> | Action | Microsoft.Resources/deployments/cancel/action | デプロイを取り消します。 |
> | Action | Microsoft.Resources/deployments/delete | デプロイを削除します。 |
> | Action | Microsoft.Resources/deployments/operations/read | デプロイ操作を取得または一覧表示します。 |
> | Action | Microsoft.Resources/deployments/read | デプロイを取得または一覧表示します。 |
> | Action | Microsoft.Resources/deployments/validate/action | デプロイを検証します。 |
> | Action | Microsoft.Resources/deployments/write | デプロイを作成または更新します。 |
> | Action | Microsoft.Resources/links/delete | リソース リンクを削除します。 |
> | Action | Microsoft.Resources/links/read | リソース リンクを取得または一覧表示します。 |
> | Action | Microsoft.Resources/links/write | リソース リンクを作成または更新します。 |
> | Action | Microsoft.Resources/marketplace/purchase/action | Marketplace からリソースを購入します。 |
> | Action | Microsoft.Resources/providers/read | プロバイダーの一覧を取得します。 |
> | Action | Microsoft.Resources/resources/read | フィルターに基づいてリソースの一覧を取得します。 |
> | Action | Microsoft.Resources/subscriptions/locations/read | サポートされる場所の一覧を取得します。 |
> | Action | Microsoft.Resources/subscriptions/operationresults/read | サブスクリプション操作の結果を取得します。 |
> | Action | Microsoft.Resources/subscriptions/providers/read | リソース プロバイダーを取得または一覧表示します。 |
> | Action | Microsoft.Resources/subscriptions/read | サブスクリプションの一覧を取得します。 |
> | Action | Microsoft.Resources/subscriptions/resourceGroups/delete | リソース グループとそのすべてのリソースを削除します。 |
> | Action | Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read | デプロイ操作を取得または一覧表示します。 |
> | Action | Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read | デプロイ操作の状態を取得または一覧表示します。 |
> | Action | Microsoft.Resources/subscriptions/resourcegroups/deployments/read | デプロイを取得または一覧表示します。 |
> | Action | Microsoft.Resources/subscriptions/resourcegroups/deployments/write | デプロイを作成または更新します。 |
> | Action | Microsoft.Resources/subscriptions/resourceGroups/moveResources/action | リソース グループ間でリソースを移動します。 |
> | Action | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Action | Microsoft.Resources/subscriptions/resourcegroups/resources/read | リソース グループのリソースを取得します。 |
> | Action | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | リソース グループ間でのリソースの移動を検証します。 |
> | Action | Microsoft.Resources/subscriptions/resourceGroups/write | リソース グループを作成または更新します。 |
> | Action | Microsoft.Resources/subscriptions/resources/read | サブスクリプションのリソースを取得します。 |
> | Action | Microsoft.Resources/subscriptions/tagNames/delete | サブスクリプション タグを削除します。 |
> | Action | Microsoft.Resources/subscriptions/tagNames/read | サブスクリプション タグを取得または一覧表示します。 |
> | Action | Microsoft.Resources/subscriptions/tagNames/tagValues/delete | サブスクリプション タグの値を削除します。 |
> | Action | Microsoft.Resources/subscriptions/tagNames/tagValues/read | サブスクリプション タグの値を取得または一覧表示します。 |
> | Action | Microsoft.Resources/subscriptions/tagNames/tagValues/write | サブスクリプション タグの値を追加します。 |
> | Action | Microsoft.Resources/subscriptions/tagNames/write | サブスクリプション タグを追加します。 |
> | Action | Microsoft.Resources/tenants/read | テナントの一覧を取得します。 |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.Scheduler/jobcollections/delete | ジョブ コレクションを削除します。 |
> | Action | Microsoft.Scheduler/jobcollections/disable/action | ジョブ コレクションを無効にします。 |
> | Action | Microsoft.Scheduler/jobcollections/enable/action | ジョブ コレクションを有効にします。 |
> | Action | Microsoft.Scheduler/jobcollections/jobs/delete | ジョブを削除します。 |
> | Action | Microsoft.Scheduler/jobcollections/jobs/generateLogicAppDefinition/action | Scheduler ジョブに基づいてロジック アプリ定義を生成します。 |
> | Action | Microsoft.Scheduler/jobcollections/jobs/jobhistories/read | ジョブ履歴を取得します。 |
> | Action | Microsoft.Scheduler/jobcollections/jobs/read | ジョブを取得します。 |
> | Action | Microsoft.Scheduler/jobcollections/jobs/run/action | ジョブを実行します。 |
> | Action | Microsoft.Scheduler/jobcollections/jobs/write | ジョブを作成または更新します。 |
> | Action | Microsoft.Scheduler/jobcollections/read | ジョブ コレクションを取得します。 |
> | Action | Microsoft.Scheduler/jobcollections/write | ジョブ コレクションを作成または更新します。 |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.Search/checkNameAvailability/action | サービス名を使用できるかどうかを確認します。 |
> | Action | Microsoft.Search/operations/read | Microsoft.Search プロバイダーのすべての使用可能な操作の一覧を取得します。 |
> | Action | Microsoft.Search/register/action | 検索リソース プロバイダーにサブスクリプションを登録し、検索サービスを作成できるようにします。 |
> | Action | Microsoft.Search/searchServices/createQueryKey/action | クエリ キーを作成します。 |
> | Action | Microsoft.Search/searchServices/delete | 検索サービスを削除します。 |
> | Action | Microsoft.Search/searchServices/deleteQueryKey/delete | クエリ キーを削除します。 |
> | Action | Microsoft.Search/searchServices/diagnosticSettings/read | リソースの診断設定の読み取りを取得します。 |
> | Action | Microsoft.Search/searchServices/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Action | Microsoft.Search/searchServices/listAdminKeys/action | 管理者キーを読み取ります。 |
> | Action | Microsoft.Search/searchServices/listQueryKeys/read | 指定された Azure Search サービスのクエリ API キーの一覧を返します。 |
> | Action | Microsoft.Search/searchServices/logDefinitions/read | 検索サービスで使用可能なログを取得します。 |
> | Action | Microsoft.Search/searchServices/metricDefinitions/read | 検索サービスで使用可能なメトリックを取得します。 |
> | Action | Microsoft.Search/searchServices/read | 検索サービスを読み取ります。 |
> | Action | Microsoft.Search/searchServices/regenerateAdminKey/action | 管理者キーを再生成します。 |
> | Action | Microsoft.Search/searchServices/start/action | 検索サービスを開始します。 |
> | Action | Microsoft.Search/searchServices/stop/action | 検索サービスを停止します。 |
> | Action | Microsoft.Search/searchServices/write | 検索サービスを作成または更新します。 |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.Security/advancedThreatProtectionSettings/read | リソースの Advanced Threat Protection 設定を取得します。 |
> | Action | Microsoft.Security/advancedThreatProtectionSettings/write | リソースの Advanced Threat Protection 設定を更新します。 |
> | Action | Microsoft.Security/alerts/read | すべての使用可能なセキュリティ アラートを取得します。 |
> | Action | Microsoft.Security/applicationWhitelistings/read | アプリケーション ホワイトリストを取得します。 |
> | Action | Microsoft.Security/applicationWhitelistings/write | 新しいアプリケーション ホワイトリストを作成するか、既存のアプリケーション ホワイトリストを更新します。 |
> | Action | Microsoft.Security/complianceResults/read | リソースのコンプライアンス結果を取得します。 |
> | Action | Microsoft.Security/locations/alerts/activate/action | セキュリティ アラートをアクティブ化します。 |
> | Action | Microsoft.Security/locations/alerts/dismiss/action | セキュリティ アラートを無視します |
> | Action | Microsoft.Security/locations/alerts/read | すべての使用可能なセキュリティ アラートを取得します。 |
> | Action | Microsoft.Security/locations/jitNetworkAccessPolicies/delete | Just-In-Time ネットワーク アクセス ポリシーを削除します |
> | Action | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | Just-In-Time ネットワーク アクセス ポリシー要求を開始します。 |
> | Action | Microsoft.Security/locations/jitNetworkAccessPolicies/read | Just-In-Time ネットワーク アクセス ポリシーを取得します。 |
> | Action | Microsoft.Security/locations/jitNetworkAccessPolicies/write | 新しい Just-In-Time ネットワーク アクセス ポリシーを作成するか、既存のポリシーを更新します。 |
> | Action | Microsoft.Security/locations/read | セキュリティ データの場所を取得します。 |
> | Action | Microsoft.Security/locations/tasks/activate/action | セキュリティ推奨事項をアクティブにします。 |
> | Action | Microsoft.Security/locations/tasks/dismiss/action | セキュリティ推奨事項を無視します。 |
> | Action | Microsoft.Security/locations/tasks/read | 利用可能なすべてのセキュリティ推奨事項を取得します。 |
> | Action | Microsoft.Security/locations/tasks/resolve/action | セキュリティ推奨事項を解決します。 |
> | Action | Microsoft.Security/locations/tasks/start/action | セキュリティ推奨事項を開始します。 |
> | Action | Microsoft.Security/policies/read | セキュリティ ポリシーを取得します。 |
> | Action | Microsoft.Security/policies/write | セキュリティ ポリシーを更新します。 |
> | Action | Microsoft.Security/pricings/delete | スコープの価格設定を削除します。 |
> | Action | Microsoft.Security/pricings/read | スコープの価格設定を取得します。 |
> | Action | Microsoft.Security/pricings/write | スコープの価格設定を更新します。 |
> | Action | Microsoft.Security/register/action | Azure Security Center にサブスクリプションを登録します。 |
> | Action | Microsoft.Security/securityContacts/delete | セキュリティ連絡先を削除します。 |
> | Action | Microsoft.Security/securityContacts/read | セキュリティ連絡先を取得します。 |
> | Action | Microsoft.Security/securityContacts/write | セキュリティ連絡先を更新します。 |
> | Action | Microsoft.Security/securitySolutions/delete | セキュリティ ソリューションを削除します。 |
> | Action | Microsoft.Security/securitySolutions/read | セキュリティ ソリューションを取得します。 |
> | Action | Microsoft.Security/securitySolutions/write | 新しいセキュリティ ソリューションを作成するか、既存のセキュリティ ソリューションを更新します。 |
> | Action | Microsoft.Security/securitySolutionsReferenceData/read | セキュリティ ソリューションの参照データを取得します。 |
> | Action | Microsoft.Security/securityStatuses/read | Azure リソースのセキュリティ正常性状態を取得します。 |
> | Action | Microsoft.Security/securityStatusesSummaries/read | スコープのセキュリティの状態の概要を取得します。 |
> | Action | Microsoft.Security/tasks/read | 利用可能なすべてのセキュリティ推奨事項を取得します。 |
> | Action | Microsoft.Security/webApplicationFirewalls/delete | Web アプリケーション ファイアウォールを削除します。 |
> | Action | Microsoft.Security/webApplicationFirewalls/read | Web アプリケーション ファイアウォールを取得します。 |
> | Action | Microsoft.Security/webApplicationFirewalls/write | 新しい Web アプリケーション ファイアウォールを作成するか、既存のWeb アプリケーション ファイアウォールを更新します。 |
> | Action | Microsoft.Security/workspaceSettings/connect/action | ワークスペースの設定の再接続の設定を変更します。 |
> | Action | Microsoft.Security/workspaceSettings/delete | ワークスペースの設定を削除します。 |
> | Action | Microsoft.Security/workspaceSettings/read | ワークスペースの設定を取得します。 |
> | Action | Microsoft.Security/workspaceSettings/write | ワークスペースの設定を更新します。 |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.SecurityGraph/diagnosticsettings/delete | 診断設定を削除します。 |
> | Action | Microsoft.SecurityGraph/diagnosticsettings/read | 診断設定を読み取ります。 |
> | Action | Microsoft.SecurityGraph/diagnosticsettings/write | 診断設定を書き込みます。 |
> | Action | Microsoft.SecurityGraph/diagnosticsettingscategories/read | 診断設定のカテゴリを読み取ります。 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.ServiceBus/checkNameAvailability/action | 特定のサブスクリプションで名前空間を使用できるかどうかを確認します。 |
> | Action | Microsoft.ServiceBus/checkNamespaceAvailability/action | 特定のサブスクリプションで名前空間を使用できるかどうかを確認します。 この API は非推奨です。代わりに CheckNameAvailabiltiy を使用してください。 |
> | Action | Microsoft.ServiceBus/namespaces/authorizationRules/action | 名前空間の承認規則を更新します。 この API は使用されていません。 名前空間の承認規則を更新するには、代わりに PUT 呼び出しを使用してください。 この操作は、API バージョン 2017-04-01 ではサポートされていません。 |
> | Action | Microsoft.ServiceBus/namespaces/authorizationRules/delete | 名前空間の承認規則を削除します。 名前空間の既定の承認規則は削除できません。  |
> | Action | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | 名前空間への接続文字列を取得します。 |
> | Action | Microsoft.ServiceBus/namespaces/authorizationRules/read | 名前空間の承認規則の説明の一覧を取得します。 |
> | Action | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | リソースのプライマリ キーまたはセカンダリ キーを再生成します。 |
> | Action | Microsoft.ServiceBus/namespaces/authorizationRules/write | 名前空間レベルの承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権、プライマリ キー、セカンダリ キーを更新できます。 |
> | Action | Microsoft.ServiceBus/namespaces/Delete | 名前空間リソースを削除します。 |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | ディザスター リカバリーのプライマリ名前空間の承認規則キーを取得します。 |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | ディザスター リカバリーのプライマリ名前空間の承認規則を取得します。 |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | ディザスター リカバリーを無効にし、プライマリ名前空間からセカンダリ名前空間への変更の複製を停止します。 |
> | Action | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | 指定されたサブスクリプションで名前空間別名を使用できるかどうかを確認します。 |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | 名前空間に関連付けられているディザスター リカバリー構成を削除します。 この操作は、プライマリ名前空間からのみ呼び出すことができます。 |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | geo DR フェールオーバーを呼び出し、セカンダリ名前空間を指すように名前空間の別名を再構成します。 |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | 名前空間に関連付けられているディザスター リカバリーの構成を取得します。 |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | 名前空間に関連付けられているディザスター リカバリーの構成を作成または更新します。 |
> | Action | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | 名前空間に関連付けられている Event Grid フィルターを削除します。 |
> | Action | Microsoft.ServiceBus/namespaces/eventGridFilters/read | 名前空間に関連付けられている Event Grid フィルターを取得します。 |
> | Action | Microsoft.ServiceBus/namespaces/eventGridFilters/write | 名前空間に関連付けられている Event Grid フィルターを作成または更新します。 |
> | Action | Microsoft.ServiceBus/namespaces/eventhubs/read | EventHub リソースの説明の一覧を取得します。 |
> | Action | Microsoft.ServiceBus/namespaces/messagingPlan/read | 名前空間のメッセージング プランを取得します。<br>この API は非推奨です。<br>より新しい API バージョンでは、MessagingPlan リソースにより公開されるプロパティは (親) 名前空間リソースに移動されています。<br>この操作は、API バージョン 2017-04-01 ではサポートされていません。 |
> | Action | Microsoft.ServiceBus/namespaces/messagingPlan/write | 名前空間のメッセージング プランを更新します。<br>この API は非推奨です。<br>より新しい API バージョンでは、MessagingPlan リソースにより公開されるプロパティは (親) 名前空間リソースに移動されています。<br>この操作は、API バージョン 2017-04-01 ではサポートされていません。 |
> | Action | Microsoft.ServiceBus/namespaces/migrate/action | 名前空間の操作を移行します。 |
> | Action | Microsoft.ServiceBus/namespaces/migrationConfigurations/delete | 移行構成を削除します。 |
> | Action | Microsoft.ServiceBus/namespaces/migrationConfigurations/read | 移行および保留中のレプリケーション操作の状態を示す移行構成を取得します |
> | Action | Microsoft.ServiceBus/namespaces/migrationConfigurations/revert/action | Standard から Premium 名前空間への移行を元に戻します |
> | Action | Microsoft.ServiceBus/namespaces/migrationConfigurations/upgrade/action | Standard 名前空間に関連付けられている DNS を移行が完了した Premium 名前空間に割り当て、Standard から Premium 名前空間へ同期中のリソースを停止します |
> | Action | Microsoft.ServiceBus/namespaces/migrationConfigurations/write | 移行構成を作成または更新します。 これにより、Standard から Premium 名前空間へのリソースの同期が開始します |
> | Action | Microsoft.ServiceBus/namespaces/operationresults/read | 名前空間の操作の状態を取得します。 |
> | Action | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | 名前空間の診断設定リソースの説明の一覧を取得します。 |
> | Action | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | 名前空間の診断設定リソースの説明の一覧を取得します。 |
> | Action | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/logDefinitions/read | 名前空間のログ リソースの説明の一覧を取得します。 |
> | Action | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/metricDefinitions/read | 名前空間のメトリック リソースの説明の一覧を取得します。 |
> | Action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/action | キューを更新する操作です。 この操作は、API バージョン 2017-04-01 ではサポートされていません。 承認規則。 承認規則を更新するには、PUT 呼び出しを使用してください。 |
> | Action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/delete | キューの承認規則を削除する操作。 |
> | Action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/listkeys/action | キューへの接続文字列を取得します。 |
> | Action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/read |  キューの承認規則の一覧を取得します。 |
> | Action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/regenerateKeys/action | リソースのプライマリ キーまたはセカンダリ キーを再生成します。 |
> | Action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/write | キューの承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権を更新できます。 |
> | Action | Microsoft.ServiceBus/namespaces/queues/Delete | キュー リソースを削除する操作。 |
> | Action | Microsoft.ServiceBus/namespaces/queues/read | キュー リソースの説明の一覧を取得します。 |
> | Action | Microsoft.ServiceBus/namespaces/queues/write | キューのプロパティを作成または更新します。 |
> | Action | Microsoft.ServiceBus/namespaces/read | 名前空間リソースの説明の一覧を取得します。 |
> | Action | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | ACS 名前空間を削除します。 |
> | Action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/action | トピックを更新する操作です。 この操作は、API バージョン 2017-04-01 ではサポートされていません。 承認規則。 承認規則を更新するには、PUT 呼び出しを使用してください。 |
> | Action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/delete | トピックの承認規則を削除する操作。 |
> | Action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/listkeys/action | トピックへの接続文字列を取得します。 |
> | Action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/read |  トピックの承認規則の一覧を取得します。 |
> | Action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/regenerateKeys/action | リソースのプライマリ キーまたはセカンダリ キーを再生成します。 |
> | Action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/write | トピックの承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権を更新できます。 |
> | Action | Microsoft.ServiceBus/namespaces/topics/Delete | トピック リソースを削除する操作。 |
> | Action | Microsoft.ServiceBus/namespaces/topics/read | トピック リソースの説明の一覧を取得します。 |
> | Action | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | TopicSubscription リソースを削除する操作。 |
> | Action | Microsoft.ServiceBus/namespaces/topics/subscriptions/read | TopicSubscription リソースの説明の一覧を取得します。 |
> | Action | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | 規則リソースを削除する操作。 |
> | Action | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/read | 規則リソースの説明の一覧を取得します。 |
> | Action | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/write | 規則のプロパティを作成または更新します。 |
> | Action | Microsoft.ServiceBus/namespaces/topics/subscriptions/write | TopicSubscription のプロパティを作成または更新します。 |
> | Action | Microsoft.ServiceBus/namespaces/topics/write | トピックのプロパティを作成または更新します。 |
> | Action | Microsoft.ServiceBus/namespaces/write | 名前空間リソースを作成し、そのプロパティを更新します。 名前空間のタグとキャパシティは、更新できるプロパティです。 |
> | Action | Microsoft.ServiceBus/operations/read | 操作を取得します。 |
> | Action | Microsoft.ServiceBus/register/action | ServiceBus リソース プロバイダーにサブスクリプションを登録し、ServiceBus リソースを作成できるようにします。 |
> | Action | Microsoft.ServiceBus/sku/read | SKU リソースの説明の一覧を取得します。 |
> | Action | Microsoft.ServiceBus/sku/regions/read | SkuRegions リソースの説明の一覧を取得します。 |
> | Action | Microsoft.ServiceBus/unregister/action | ServiceBus リソース プロバイダーにサブスクリプションを登録し、ServiceBus リソースを作成できるようにします。 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.ServiceFabric/clusters/applications/delete | アプリケーションを削除します。 |
> | Action | Microsoft.ServiceFabric/clusters/applications/read | アプリケーションを読み取ります。 |
> | Action | Microsoft.ServiceFabric/clusters/applications/services/delete | サービスを削除します。 |
> | Action | Microsoft.ServiceFabric/clusters/applications/services/partitions/read | パーティションを読み取ります。 |
> | Action | Microsoft.ServiceFabric/clusters/applications/services/partitions/replicas/read | レプリカを読み取ります。 |
> | Action | Microsoft.ServiceFabric/clusters/applications/services/read | サービスを読み取ります。 |
> | Action | Microsoft.ServiceFabric/clusters/applications/services/statuses/read | サービスの状態を読み取ります。 |
> | Action | Microsoft.ServiceFabric/clusters/applications/services/write | サービスを作成または更新します。 |
> | Action | Microsoft.ServiceFabric/clusters/applications/write | アプリケーションを作成または更新します。 |
> | Action | Microsoft.ServiceFabric/clusters/applicationTypes/delete | アプリケーションの種類を削除します。 |
> | Action | Microsoft.ServiceFabric/clusters/applicationTypes/read | アプリケーションの種類を読み取ります。 |
> | Action | Microsoft.ServiceFabric/clusters/applicationTypes/versions/delete | アプリケーションの種類のバージョンを削除します。 |
> | Action | Microsoft.ServiceFabric/clusters/applicationTypes/versions/read | アプリケーションの種類のバージョンを読み取ります。 |
> | Action | Microsoft.ServiceFabric/clusters/applicationTypes/versions/write | アプリケーションの種類のバージョンを作成または更新します。 |
> | Action | Microsoft.ServiceFabric/clusters/applicationTypes/write | アプリケーションの種類を作成または更新します。 |
> | Action | Microsoft.ServiceFabric/clusters/delete | クラスターを削除します。 |
> | Action | Microsoft.ServiceFabric/clusters/nodes/read | ノードを読み取ります。 |
> | Action | Microsoft.ServiceFabric/clusters/read | クラスターを読み取ります。 |
> | Action | Microsoft.ServiceFabric/clusters/statuses/read | クラスターの状態を読み取ります。 |
> | Action | Microsoft.ServiceFabric/clusters/write | クラスターを作成または更新します。 |
> | Action | Microsoft.ServiceFabric/locations/clusterVersions/read | クラスターのバージョンを読み取ります。 |
> | Action | Microsoft.ServiceFabric/locations/environments/clusterVersions/read | 特定の環境のクラスターのバージョンを読み取ります。 |
> | Action | Microsoft.ServiceFabric/locations/operationresults/read | 操作結果を読み取ります。 |
> | Action | Microsoft.ServiceFabric/locations/operations/read | 場所別に操作を読み取ります。 |
> | Action | Microsoft.ServiceFabric/operations/read | 使用可能な操作を読み取ります。 |
> | Action | Microsoft.ServiceFabric/register/action | アクションを登録します。 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.SignalRService/checknameavailability/action | 新しい SignalR サービスで名前を使用できるかどうかを確認します。 |
> | Action | Microsoft.SignalRService/register/action | "Microsoft.SignalRService" リソース プロバイダーをサブスクリプションに登録します。 |
> | Action | Microsoft.SignalRService/SignalR/delete | SignalR 全体を削除します。 |
> | Action | Microsoft.SignalRService/SignalR/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Action | Microsoft.SignalRService/SignalR/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Action | Microsoft.SignalRService/SignalR/providers/Microsoft.Insights/metricDefinitions/read | SignalR の使用可能なメトリックを取得します。 |
> | Action | Microsoft.SignalRService/SignalR/read | 管理ポータルで、または API を介して SignalR の設定と構成を表示します。 |
> | Action | Microsoft.SignalRService/SignalR/write | 管理ポータルで、または API を介して SignalR の設定と構成を変更します。 |
> | Action | Microsoft.SignalRService/unregister/action | "Microsoft.SignalRService" リソース プロバイダーのサブスクリプションの登録を解除します。 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.Solutions/applicationDefinitions/delete | アプリケーション定義を削除します。 |
> | Action | Microsoft.Solutions/applicationDefinitions/read | アプリケーション定義の一覧を取得します。 |
> | Action | Microsoft.Solutions/applicationDefinitions/write | アプリケーション定義を追加または変更します。 |
> | Action | Microsoft.Solutions/applications/delete | アプリケーションを削除します。 |
> | Action | Microsoft.Solutions/applications/read | アプリケーションの一覧を取得します。 |
> | Action | Microsoft.Solutions/applications/write | アプリケーションを作成します。 |
> | Action | Microsoft.Solutions/locations/operationStatuses/read | リソースの操作の状態を読み取ります。 |
> | Action | Microsoft.Solutions/register/action | ソリューションに登録します。 |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.Sql/checkNameAvailability/action | 指定されたサブスクリプションの世界的なプロビジョニングに対して特定のサーバー名が使用可能かどうかを確認します。 |
> | Action | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | 拡張サーバー BLOB 監査ポリシー設定操作の結果を取得します。 |
> | Action | Microsoft.Sql/locations/auditingSettingsOperationResults/read | サーバー BLOB 監査ポリシーの設定操作の結果を取得します。 |
> | Action | Microsoft.Sql/locations/capabilities/read | 指定された場所でのこのサブスクリプションの機能を取得します。 |
> | Action | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | データベース操作の状態を取得します。 |
> | Action | Microsoft.Sql/locations/databaseOperationResults/read | データベース操作の状態を取得します。 |
> | Action | Microsoft.Sql/locations/deletedServerAsyncOperation/read | 削除されるサーバーでの進行中の操作を取得します。 |
> | Action | Microsoft.Sql/locations/deletedServerOperationResults/read | 削除されるサーバーでの進行中の操作を取得します。 |
> | Action | Microsoft.Sql/locations/deletedServers/read | 削除されるサーバーの一覧を返すか、指定された削除されるサーバーのプロパティを取得します。 |
> | Action | Microsoft.Sql/locations/deletedServers/recover/action | 削除されたサーバーを復旧します。 |
> | Action | Microsoft.Sql/locations/deleteVirtualNetworkOrSubnets/action | 仮想ネットワークまたはサブネットに関連付けられた仮想ネットワーク規則を削除します。 |
> | Action | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | エラスティック プールの非同期操作に対する Azure 非同期操作を取得します。 |
> | Action | Microsoft.Sql/locations/elasticPoolOperationResults/read | エラスティック プールの操作の結果を取得します。 |
> | Action | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | 拡張サーバー BLOB 監査ポリシー設定操作の結果を取得します。 |
> | Action | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | 拡張サーバー BLOB 監査ポリシー設定操作の結果を取得します。 |
> | Action | Microsoft.Sql/locations/instanceFailoverGroups/delete | 既存のインスタンス フェールオーバー グループを削除します。 |
> | Action | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | 既存のインスタンス フェールオーバー グループで計画されたフェールオーバーを実行します。 |
> | Action | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | 既存のインスタンス フェールオーバー グループで強制フェールオーバーを実行します。 |
> | Action | Microsoft.Sql/locations/instanceFailoverGroups/read | インスタンス フェールオーバー グループの一覧を返すか、指定されたインスタンス フェールオーバー グループのプロパティを取得します。 |
> | Action | Microsoft.Sql/locations/instanceFailoverGroups/write | 指定されたパラメーターでインスタンス フェールオーバー グループを作成するか、指定されたインスタンス フェールオーバー グループのプロパティまたはタグを更新します。 |
> | Action | Microsoft.Sql/locations/longTermRetentionBackups/read | 特定の場所にある各サーバー上の各データベースについて、長期保有バックアップを一覧表示します。 |
> | Action | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read | 特定のサーバー上の各データベースについて、長期保有バックアップを一覧表示します。 |
> | Action | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete | 長期保有バックアップを削除します。 |
> | Action | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read | 特定のデータベースについて、長期保有バックアップを一覧表示します。 |
> | Action | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | 管理されたデータベースの復元操作を完了します。 |
> | Action | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | 管理されたデータベースの Transparent Data Encryption での進行中の操作を取得します。 |
> | Action | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | 管理されたデータベースの Transparent Data Encryption での進行中の操作を取得します。 |
> | Action | Microsoft.Sql/locations/networkInterfaceAzureAsyncOperation/read | 指定されたネットワーク インターフェイスの Azure 非同期操作の詳細を返します。 |
> | Action | Microsoft.Sql/locations/networkInterfaceOperationResults/read | 指定されたネットワーク インターフェイス操作の詳細を返します。 |
> | Action | Microsoft.Sql/locations/read | 指定されたサブスクリプションで使用可能な場所を取得します。 |
> | Action | Microsoft.Sql/locations/syncAgentOperationResults/read | 同期エージェント リソース操作の結果を取得します。 |
> | Action | Microsoft.Sql/locations/syncDatabaseIds/read | 特定のリージョンおよびサブスクリプションの同期データベース ID を取得します。 |
> | Action | Microsoft.Sql/locations/syncGroupOperationResults/read | 同期グループ リソース操作の結果を取得します。 |
> | Action | Microsoft.Sql/locations/syncMemberOperationResults/read | 同期メンバー リソース操作の結果を取得します。 |
> | Action | Microsoft.Sql/locations/usages/read | ある場所でのこのサブスクリプションの使用状況メトリックのコレクションを取得します。 |
> | Action | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | 指定された仮想ネットワーク規則の Azure 非同期操作の詳細を返します。  |
> | Action | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | 指定された仮想ネットワーク規則の操作の詳細を返します。  |
> | Action | Microsoft.Sql/managedInstances/administrators/delete | マネージド インスタンスの既存の管理者を削除します。 |
> | Action | Microsoft.Sql/managedInstances/administrators/read | Managed Instance の管理者の一覧を取得します。 |
> | Action | Microsoft.Sql/managedInstances/administrators/write | 指定されたパラメーターでマネージド インスタンスの管理者を作成または更新します。 |
> | Action | Microsoft.Sql/managedInstances/databases/delete | 既存の管理されたデータベースを削除します。 |
> | Action | Microsoft.Sql/managedInstances/databases/read | 既存の管理されたデータベースを取得します。 |
> | Action | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | 指定された管理されたデータベースで構成されているデータベース脅威検出ポリシーの詳細を取得します。 |
> | Action | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | 指定された管理されたデータベースのデータベース脅威検出ポリシーを変更します。 |
> | Action | Microsoft.Sql/managedInstances/databases/securityEvents/read | 管理されたデータベースのセキュリティ イベントを取得します。 |
> | Action | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | 指定された管理されたデータベースでのデータベース Transparent Data Encryption の詳細を取得します。 |
> | Action | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | 指定された管理されたデータベースのデータベース Transparent Data Encryption を変更します。 |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/delete | 指定されたデータベースの脆弱性評価を削除します。 |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/read | 指定されたデータベースで構成されている脆弱性評価の詳細を取得します。 |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/delete | 指定されたデータベースの脆弱性評価規則ベースラインを削除します。 |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/read | 指定されたデータベースの脆弱性評価規則ベースラインを取得します。 |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/write | 指定されたデータベースの脆弱性評価規則ベースラインを変更します。 |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/export/action | 既存のスキャン結果を人間が読める形式に変換します。 既に存在する場合は何も行われません。 |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/initiateScan/action | データベースの脆弱性評価スキャンを実行します。 |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/read | データベース脆弱性評価スキャン レコードの一覧を返すか、指定されたスキャン ID のスキャン レコードを取得します。 |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/write | 指定されたデータベースの脆弱性評価を変更します。 |
> | Action | Microsoft.Sql/managedInstances/databases/write | 新しいデータベースを作成するか、既存のデータベースを更新します。 |
> | Action | Microsoft.Sql/managedInstances/delete | 既存のマネージド インスタンスを削除します。 |
> | Action | Microsoft.Sql/managedInstances/encryptionProtector/read | サーバー暗号化の保護機能の一覧を返すか、指定されたサーバー暗号化の保護機能のプロパティを取得します。 |
> | Action | Microsoft.Sql/managedInstances/encryptionProtector/write | 指定されたサーバー暗号化の保護機能のプロパティを更新します。 |
> | Action | Microsoft.Sql/managedInstances/keys/delete | 既存の Azure SQL Managed Instance キーを削除します。 |
> | Action | Microsoft.Sql/managedInstances/keys/read | マネージド インスタンス キーの一覧を返すか、指定されたマネージド インスタンス キーのプロパティを取得します。 |
> | Action | Microsoft.Sql/managedInstances/keys/write | 指定されたパラメーターでキーを作成するか、指定されたマネージド インスタンス キーのプロパティまたはタグを更新します。 |
> | Action | Microsoft.Sql/managedInstances/metricDefinitions/read | マネージド インスタンスのメトリック定義を取得します。 |
> | Action | Microsoft.Sql/managedInstances/metrics/read | Managed Instance のメトリックを取得します。 |
> | Action | Microsoft.Sql/managedInstances/read | マネージド インスタンスの一覧を返すか、指定されたマネージド インスタンスのプロパティを取得します。 |
> | Action | Microsoft.Sql/managedInstances/securityAlertPolicies/read | 指定された管理サーバーで構成されている管理サーバー脅威検出ポリシーの詳細を取得します。 |
> | Action | Microsoft.Sql/managedInstances/securityAlertPolicies/write | 指定された管理サーバーの管理サーバー脅威検出ポリシーを変更します。 |
> | Action | Microsoft.Sql/managedInstances/tdeCertificates/action | TDE 証明書を作成/更新します |
> | Action | Microsoft.Sql/managedInstances/write | 指定されたパラメーターでマネージド インスタンスを作成するか、指定されたマネージド インスタンスのプロパティまたはタグを更新します。 |
> | Action | Microsoft.Sql/operations/read | 使用可能な REST 操作を取得します。 |
> | Action | Microsoft.Sql/register/action | Microsoft SQL Database リソース プロバイダーにサブスクリプションを登録し、Microsoft SQL Database を作成できるようにします。 |
> | Action | Microsoft.Sql/servers/administratorOperationResults/read | サーバー管理者で進行中の操作を取得します。 |
> | Action | Microsoft.Sql/servers/administrators/delete | サーバー管理者を削除します。 |
> | Action | Microsoft.Sql/servers/administrators/read | サーバー管理者の詳細を取得します。 |
> | Action | Microsoft.Sql/servers/administrators/write | サーバー管理者を作成または更新します。 |
> | Action | Microsoft.Sql/servers/advisors/read | サーバーで使用できる Advisor の一覧を返します。 |
> | Action | Microsoft.Sql/servers/advisors/recommendedActions/read | サーバーの指定された Advisor の推奨アクションの一覧を返します。 |
> | Action | Microsoft.Sql/servers/advisors/recommendedActions/write | サーバーに推奨アクションを適用します。 |
> | Action | Microsoft.Sql/servers/advisors/write | サーバー レベルでの Advisor の自動実行の状態を更新します。 |
> | Action | Microsoft.Sql/servers/auditingPolicies/read | 指定されたサーバーで構成されている既定のサーバー テーブル監査ポリシーの詳細を取得します。 |
> | Action | Microsoft.Sql/servers/auditingPolicies/write | 指定されたサーバーの既定のサーバー テーブル監査を変更します。 |
> | Action | Microsoft.Sql/servers/auditingSettings/operationResults/read | サーバー BLOB 監査ポリシーの設定操作の結果を取得します。 |
> | Action | Microsoft.Sql/servers/auditingSettings/read | 指定されたサーバーで構成されているサーバー BLOB 監査ポリシーの詳細を取得します。 |
> | Action | Microsoft.Sql/servers/auditingSettings/write | 指定されたサーバーのサーバー BLOB 監査を変更します。 |
> | Action | Microsoft.Sql/servers/automaticTuning/read | サーバーの自動調整の設定を返します。 |
> | Action | Microsoft.Sql/servers/automaticTuning/write | サーバーの自動調整の設定を更新し、更新された設定を返します。 |
> | Action | Microsoft.Sql/servers/backupLongTermRetentionVaults/delete | 既存のバックアップ アーカイブ コンテナーを削除します。 |
> | Action | Microsoft.Sql/servers/backupLongTermRetentionVaults/read | この操作を使用して、バックアップ長期リテンション期間コンテナーを取得します。 このサーバーに登録されているコンテナーに関する情報を返します。 |
> | Action | Microsoft.Sql/servers/backupLongTermRetentionVaults/write | バックアップ長期リテンション期間コンテナーをサーバーに登録します。 |
> | Action | Microsoft.Sql/servers/communicationLinks/delete | 既存のサーバー通信リンクを削除します。 |
> | Action | Microsoft.Sql/servers/communicationLinks/read | 指定されたサーバーの通信リンクの一覧を返します。 |
> | Action | Microsoft.Sql/servers/communicationLinks/write | サーバー通信リンクを作成または更新します。 |
> | Action | Microsoft.Sql/servers/connectionPolicies/read | 指定されたサーバーのサーバー接続ポリシーの一覧を返します。 |
> | Action | Microsoft.Sql/servers/connectionPolicies/write | サーバー接続ポリシーを作成または更新します。 |
> | Action | Microsoft.Sql/servers/databases/advisors/read | データベースで使用できる Advisor の一覧を返します。 |
> | Action | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | データベースの指定された Advisor の推奨アクションの一覧を返します。 |
> | Action | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | データベースに推奨アクションを適用します。 |
> | Action | Microsoft.Sql/servers/databases/advisors/write | データベース レベルでの Advisor の自動実行の状態を更新します。 |
> | Action | Microsoft.Sql/servers/databases/auditingPolicies/read | 指定されたデータベースで構成されているテーブル監査ポリシーの詳細を取得します。 |
> | Action | Microsoft.Sql/servers/databases/auditingPolicies/write | 指定されたデータベースのテーブル監査ポリシーを変更します。 |
> | Action | Microsoft.Sql/servers/databases/auditingSettings/read | 指定されたデータベースで構成されている BLOB 監査ポリシーの詳細を取得します。 |
> | Action | Microsoft.Sql/servers/databases/auditingSettings/write | 指定されたデータベースの BLOB 監査ポリシーを変更します。 |
> | Action | Microsoft.Sql/servers/databases/auditRecords/read | データベースの BLOB 監査レコードを取得します。 |
> | Action | Microsoft.Sql/servers/databases/automaticTuning/read | データベースの自動調整の設定を返します。 |
> | Action | Microsoft.Sql/servers/databases/automaticTuning/write | データベースの自動調整の設定を更新し、更新された設定を返します。 |
> | Action | Microsoft.Sql/servers/databases/azureAsyncOperation/read | データベース操作の状態を取得します。 |
> | Action | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | 指定されたデータベースのバックアップ アーカイブ ポリシーの一覧を返します。 |
> | Action | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | データベース バックアップ アーカイブ ポリシーを作成または更新します。 |
> | Action | Microsoft.Sql/servers/databases/connectionPolicies/read | 指定されたデータベースで構成されている接続ポリシーの詳細を取得します。 |
> | Action | Microsoft.Sql/servers/databases/connectionPolicies/write | 指定されたデータベースの接続ポリシーを変更します。 |
> | Action | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | データベースのデータ マスク ポリシーの一覧を返します。 |
> | Action | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | 指定されたデータベースのデータ マスク ポリシー ルールを削除します。 |
> | Action | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | 指定されたデータベースで構成されているデータ マスク ポリシー ルールの詳細を取得します。 |
> | Action | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | 指定されたデータベースのデータ マスク ポリシー ルールを変更します。 |
> | Action | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | 指定されたデータベースのデータ マスク ポリシーを変更します。 |
> | Action | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | 選択したステップ ID のデータ ウェアハウス クエリの分散クエリ ステップ情報を返します。 |
> | Action | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | 選択したクエリ ID のデータ ウェアハウス分散クエリ情報を返します。 |
> | Action | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | 実行中のクエリと中断されたクエリを含む SQL Data Warehouse インスタンスのユーザー アクティビティを取得します |
> | Action | Microsoft.Sql/servers/databases/delete | 既存のデータベースを削除します。 |
> | Action | Microsoft.Sql/servers/databases/export/action | Azure SQL Database をエクスポートします。 |
> | Action | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | 指定されたデータベースで構成されている拡張 BLOB 監査ポリシーの詳細を取得します。 |
> | Action | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | 指定されたデータベースの拡張 BLOB 監査ポリシーを変更します。 |
> | Action | Microsoft.Sql/servers/databases/extensions/read | データベースの拡張機能のコレクションを取得します。 |
> | Action | Microsoft.Sql/servers/databases/extensions/write | 指定されたデータベースの拡張機能を変更します。 |
> | Action | Microsoft.Sql/servers/databases/geoBackupPolicies/read | 指定されたデータベースの geo バックアップ ポリシーを取得します。 |
> | Action | Microsoft.Sql/servers/databases/geoBackupPolicies/write | データベースの geo バックアップ ポリシーを作成または更新します。 |
> | Action | Microsoft.Sql/servers/databases/importExportOperationResults/read | 進行中のインポート/エクスポート操作を取得します。 |
> | Action | Microsoft.Sql/servers/databases/maintenanceWindowOptions/read | 選択したデータベースに使用可能なメンテナンス期間の一覧を取得します。 |
> | Action | Microsoft.Sql/servers/databases/maintenanceWindows/read | 選択したデータベースのメンテナンス期間の設定を取得します。 |
> | Action | Microsoft.Sql/servers/databases/maintenanceWindows/write | 選択したデータベースのメンテナンス期間の設定を行います。 |
> | Action | Microsoft.Sql/servers/databases/metricDefinitions/read | データベースの利用可能なメトリックの種類を返します。 |
> | Action | Microsoft.Sql/servers/databases/metrics/read | データベースのメトリックを返します。 |
> | Action | Microsoft.Sql/servers/databases/move/action | Azure SQL Database の名前を変更します。 |
> | Action | Microsoft.Sql/servers/databases/operationResults/read | データベース操作の状態を取得します。 |
> | Action | Microsoft.Sql/servers/databases/operations/cancel/action | Azure SQL Database のまだ終了していない保留中非同期操作を取り消します。 |
> | Action | Microsoft.Sql/servers/databases/operations/read | データベースで実行された操作の一覧を返します。 |
> | Action | Microsoft.Sql/servers/databases/pause/action | Azure SQL Datawarehouse データベースを一時停止します。 |
> | Action | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Action | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Action | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | データベースの利用可能なログを取得します。 |
> | Action | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/metricDefinitions/read | データベースの利用可能なメトリックの種類を返します。 |
> | Action | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | 指定されたパラメーターに対応するクエリ テキストのコレクションを返します。 |
> | Action | Microsoft.Sql/servers/databases/queryStore/read | データベースのクエリ ストアの設定の現在値を返します。 |
> | Action | Microsoft.Sql/servers/databases/queryStore/write | データベースのクエリ ストア設定を更新します。 |
> | Action | Microsoft.Sql/servers/databases/read | データベースの一覧を返すか、指定されたデータベースのプロパティを取得します。 |
> | Action | Microsoft.Sql/servers/databases/replicationLinks/delete | レプリケーション関係を強制的に終了します。データが失われる可能性があります。 |
> | Action | Microsoft.Sql/servers/databases/replicationLinks/failover/action | プライマリからすべての変更を同期した後にフェールオーバーします。このデータベースがレプリケーション関係のプライマリになり、リモート プライマリがセカンダリになります。 |
> | Action | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | 即座にフェールオーバーします。このデータベースがレプリケーション関係のプライマリになり、リモート プライマリがセカンダリになります。データが失われる可能性があります。 |
> | Action | Microsoft.Sql/servers/databases/replicationLinks/read | レプリケーション リンクの一覧を返すか、指定されたレプリケーション リンクのプロパティを取得します。 |
> | Action | Microsoft.Sql/servers/databases/replicationLinks/unlink/action | レプリケーション関係を強制的に終了するか、パートナーとの同期後に終了します。 |
> | Action | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | リンクのレプリケーション モードを同期モードまたは非同期モードに更新します。 |
> | Action | Microsoft.Sql/servers/databases/restorePoints/action | 新しい復元ポイントを作成します。 |
> | Action | Microsoft.Sql/servers/databases/restorePoints/delete | データベースの復元ポイントを削除します。 |
> | Action | Microsoft.Sql/servers/databases/restorePoints/read | データベースの復元ポイントを返します。 |
> | Action | Microsoft.Sql/servers/databases/resume/action | Azure SQL Datawarehouse データベースを再開します。 |
> | Action | Microsoft.Sql/servers/databases/schemas/read | データベースのスキーマの一覧を取得します。 |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/read | テーブルの列の一覧を取得します。 |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | 指定された列の機密ラベルを削除します。 |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | 指定された列の機密ラベルを取得します。 |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | 指定された列の機密ラベルを作成または更新します。 |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/read | データベースのテーブルの一覧を取得します。 |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | データベースのインデックス推奨事項の一覧を取得します。 |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | インデックス推奨事項を適用します。 |
> | Action | Microsoft.Sql/servers/databases/securityAlertPolicies/read | 指定されたデータベースで構成されている脅威検出ポリシーの詳細を取得します。 |
> | Action | Microsoft.Sql/servers/databases/securityAlertPolicies/write | 指定されたデータベースの脅威検出ポリシーを変更します。 |
> | Action | Microsoft.Sql/servers/databases/securityMetrics/read | データベースのセキュリティ メトリックのコレクションを取得します。 |
> | Action | Microsoft.Sql/servers/databases/sensitivityLabels/read | 指定されたデータベースの機密ラベルを一覧表示します。 |
> | Action | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | パフォーマンスを向上させたり、コストを削減したりするために、クエリ実行の統計に基づいて、データベースのスケールアップまたはスケールダウンに関する提案を返します。 |
> | Action | Microsoft.Sql/servers/databases/skus/read | データベースの使用可能な SKU のコレクションを取得します。 |
> | Action | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | 同期グループの同期を取り消します。 |
> | Action | Microsoft.Sql/servers/databases/syncGroups/delete | 既存の同期グループを削除します。 |
> | Action | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | 同期ハブ データベース スキーマの一覧を返します。 |
> | Action | Microsoft.Sql/servers/databases/syncGroups/logs/read | 同期グループのログの一覧を返します。 |
> | Action | Microsoft.Sql/servers/databases/syncGroups/read | 同期グループの一覧を返すか、指定された同期グループのプロパティを取得します。 |
> | Action | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | 同期ハブのデータベース スキーマを更新します。 |
> | Action | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | 同期ハブのスキーマ更新操作の結果を取得します。 |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | 既存の同期メンバーを削除します。 |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | 同期メンバーの一覧を返すか、指定された同期メンバーのプロパティを取得します。 |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | 同期メンバーのスキーマを更新します。 |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | 同期メンバーのスキーマ更新操作の結果を取得します。 |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | 同期メンバー データベース スキーマの一覧を返します。 |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | 指定されたパラメーターで同期メンバーを作成するか、指定され同期メンバーのプロパティを更新します。 |
> | Action | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | 同期グループの同期をトリガーします。 |
> | Action | Microsoft.Sql/servers/databases/syncGroups/write | 指定されたパラメーターで同期グループを作成するか、指定され同期グループのプロパティを更新します。 |
> | Action | Microsoft.Sql/servers/databases/topQueries/queryText/action | 選択したクエリ ID の Transact-SQL テキストを返します。 |
> | Action | Microsoft.Sql/servers/databases/topQueries/read | 選択した期間の選択したクエリの集計されたランタイム統計を返します。 |
> | Action | Microsoft.Sql/servers/databases/topQueries/statistics/read | 選択した期間の選択したクエリの集計されたランタイム統計を返します。 |
> | Action | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | Transparent Data Encryption での進行中の操作を取得します。 |
> | Action | Microsoft.Sql/servers/databases/transparentDataEncryption/read | 指定されたデータベースの Transparent Data Encryption セキュリティ機能の状態と詳細を取得します。 |
> | Action | Microsoft.Sql/servers/databases/transparentDataEncryption/write | Transparent Data Encryption の状態を変更します。 |
> | Action | Microsoft.Sql/servers/databases/upgradeDataWarehouse/action | Azure SQL Datawarehouse データベースをアップグレードします。 |
> | Action | Microsoft.Sql/servers/databases/usages/read | Azure SQL Database の使用状況の情報を取得します。 |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | 指定されたデータベースの脆弱性評価を削除します。 |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/read | 指定されたデータベースで構成されている脆弱性評価の詳細を取得します。 |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | 指定されたデータベースの脆弱性評価規則ベースラインを削除します。 |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/read | 指定されたデータベースの脆弱性評価規則ベースラインを取得します。 |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/write | 指定されたデータベースの脆弱性評価規則ベースラインを変更します。 |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | 既存のスキャン結果を人間が読める形式に変換します。 既に存在する場合は何も行われません。 |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | データベースの脆弱性評価スキャンを実行します。 |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/read | データベース脆弱性評価スキャン レコードの一覧を返すか、指定されたスキャン ID のスキャン レコードを取得します。 |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | 指定されたデータベースの脆弱性評価を変更します。 |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | データベースの脆弱性評価スキャンを実行します。 |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | データベース脆弱性評価スキャンの実行操作の結果を取得します。 |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | 指定されたデータベースで構成されている脆弱性評価の詳細を取得します。 |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | 指定されたデータベースの脆弱性評価を変更します。 |
> | Action | Microsoft.Sql/servers/databases/write | 指定されたパラメーターでデータベースを作成するか、指定されたデータベースのプロパティまたはタグを更新します。 |
> | Action | Microsoft.Sql/servers/delete | 既存のサーバーを削除します。 |
> | Action | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | 指定されたサーバーの既存のディザスター リカバリー構成を削除します。 |
> | Action | Microsoft.Sql/servers/disasterRecoveryConfiguration/failover/action | DisasterRecoveryConfiguration の構成をフェールオーバーします。 |
> | Action | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | DisasterRecoveryConfiguration のフェールオーバーを強制します。 |
> | Action | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | このサーバーを含むディザスター リカバリー構成のコレクションを取得します。 |
> | Action | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | サーバーのディザスター リカバリー構成を変更します。 |
> | Action | Microsoft.Sql/servers/elasticPoolEstimates/read | このサーバー用に既に作成されているエラスティック プール推定値の一覧を返します。 |
> | Action | Microsoft.Sql/servers/elasticPoolEstimates/write | 提供されているデータベースの一覧用に、新しいエラスティック プール推定値を作成します。 |
> | Action | Microsoft.Sql/servers/elasticPools/advisors/read | エラスティック プールで使用できる Advisor の一覧を返します。 |
> | Action | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/read | エラスティック プールの指定された Advisor の推奨アクションの一覧を返します。 |
> | Action | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | エラスティック プールに推奨アクションを適用します。 |
> | Action | Microsoft.Sql/servers/elasticPools/advisors/write | エラスティック プール レベルでの Advisor の自動実行の状態を更新します。 |
> | Action | Microsoft.Sql/servers/elasticPools/databases/read | エラスティック プールのデータベースの一覧を取得します。 |
> | Action | Microsoft.Sql/servers/elasticPools/delete | 既存のエラスティック プールを削除します。 |
> | Action | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | 指定されたエラスティック データベース プールのアクティビティと詳細を取得します。 |
> | Action | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | エラスティック データベース プールに含まれる指定されたデータベースのアクティビティと詳細を取得します。 |
> | Action | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | エラスティック データベース プールの利用可能なメトリックの種類を返します。 |
> | Action | Microsoft.Sql/servers/elasticPools/metrics/read | エラスティック データベース プールのメトリックを返します。 |
> | Action | Microsoft.Sql/servers/elasticPools/operations/cancel/action | Azure SQL エラスティック プールのまだ終了していない保留中非同期操作を取り消します。 |
> | Action | Microsoft.Sql/servers/elasticPools/operations/read | エラスティック プールで実行された操作の一覧を返します。 |
> | Action | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Action | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Action | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read | エラスティック データベース プールの利用可能なメトリックの種類を返します。 |
> | Action | Microsoft.Sql/servers/elasticPools/read | 指定されたサーバーのエラスティック プールの詳細を取得します。 |
> | Action | Microsoft.Sql/servers/elasticPools/skus/read | エラスティック プールの使用可能な SKU のコレクションを取得します。 |
> | Action | Microsoft.Sql/servers/elasticPools/write | 新しいエラスティック プールを作成するか、既存のエラスティック プールのプロパティを変更します。 |
> | Action | Microsoft.Sql/servers/encryptionProtector/read | サーバー暗号化の保護機能の一覧を返すか、指定されたサーバー暗号化の保護機能のプロパティを取得します。 |
> | Action | Microsoft.Sql/servers/encryptionProtector/write | 指定されたサーバー暗号化の保護機能のプロパティを更新します。 |
> | Action | Microsoft.Sql/servers/extendedAuditingSettings/read | 指定されたサーバーで構成されている拡張サーバー BLOB 監査ポリシーの詳細を取得します。 |
> | Action | Microsoft.Sql/servers/extendedAuditingSettings/write | 指定されたサーバーの拡張サーバー BLOB 監査を変更します。 |
> | Action | Microsoft.Sql/servers/failoverGroups/delete | 既存のフェールオーバー グループを削除します。 |
> | Action | Microsoft.Sql/servers/failoverGroups/failover/action | 既存のフェールオーバー グループで計画されたフェールオーバーを実行します。 |
> | Action | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | 既存のフェールオーバー グループで強制フェールオーバーを実行します。 |
> | Action | Microsoft.Sql/servers/failoverGroups/read | フェールオーバー グループの一覧を返すか、指定されたフェールオーバー グループのプロパティを取得します。 |
> | Action | Microsoft.Sql/servers/failoverGroups/write | 指定されたパラメーターでフェールオーバー グループを作成するか、指定されたフェールオーバー グループのプロパティまたはタグを更新します。 |
> | Action | Microsoft.Sql/servers/firewallRules/delete | 既存のサーバー ファイアウォール規則を削除します。 |
> | Action | Microsoft.Sql/servers/firewallRules/read | サーバー ファイアウォール規則の一覧を返すか、指定されたサーバー ファイアウォール規則のプロパティを取得します。 |
> | Action | Microsoft.Sql/servers/firewallRules/write | 指定されたパラメーターでサーバー ファイアウォール規則を作成するか、指定された規則のプロパティを更新するか、新しいサーバー ファイアウォール規則ですべての既存規則を上書きします。 |
> | Action | Microsoft.Sql/servers/import/action | サーバーに新しいデータベースを作成するか、DacPac パッケージからスキーマとデータをデプロイします。 |
> | Action | Microsoft.Sql/servers/importExportOperationResults/read | 進行中のインポート/エクスポート操作を取得します。 |
> | Action | Microsoft.Sql/servers/keys/delete | 既存のサーバー キーを削除します。 |
> | Action | Microsoft.Sql/servers/keys/read | サーバー キーの一覧を返すか、指定されたサーバー キーのプロパティを取得します。 |
> | Action | Microsoft.Sql/servers/keys/write | 指定されたパラメーターでキーを作成するか、指定されたサーバー キーのプロパティまたはタグを更新します。 |
> | Action | Microsoft.Sql/servers/networkInterfaces/read | 指定されたネットワーク インターフェイスのプロパティを返します。 |
> | Action | Microsoft.Sql/servers/networkInterfaces/write | 指定されたパラメーターでネットワーク インターフェイスを作成するか、指定されたネットワーク インターフェイスのプロパティまたはタグを更新します。 |
> | Action | Microsoft.Sql/servers/operationResults/read | 進行中のサーバー操作を取得します。 |
> | Action | Microsoft.Sql/servers/providers/Microsoft.Insights/metricDefinitions/read | サーバーで使用可能なメトリックの種類を返します。 |
> | Action | Microsoft.Sql/servers/read | サーバーの一覧を返すか、指定されたサーバーのプロパティを取得します。 |
> | Action | Microsoft.Sql/servers/recommendedElasticPools/databases/read | 指定されたサーバーの推奨エラスティック データベース プールのメトリックを取得します。 |
> | Action | Microsoft.Sql/servers/recommendedElasticPools/read | リソース使用率の履歴に基づいて、コストを削減したり、パフォーマンスを向上させたりするために、エラスティック データベース プールの推奨事項を取得します。 |
> | Action | Microsoft.Sql/servers/recoverableDatabases/read | この操作は、最新の既知の良好なバックアップ ポイントにデータベースを復元するために、ライブ データベースのディザスター リカバリーに使用します。 最新の良好なバックアップに関する情報が返されますが、データベースを実際に復元するわけではありません。 |
> | Action | Microsoft.Sql/servers/replicationLinks/read | レプリケーション リンクの一覧を返すか、指定されたレプリケーション リンクのプロパティを取得します。 |
> | Action | Microsoft.Sql/servers/restorableDroppedDatabases/read | 指定されたサーバーで破棄されたデータベースのうち、まだリテンション期間ポリシーの範囲内にあるデータベースの一覧を取得します。 |
> | Action | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | サーバーの脅威検出ポリシーの書き込み操作の結果を取得します。 |
> | Action | Microsoft.Sql/servers/securityAlertPolicies/read | 指定されたサーバーで構成されているサーバーの脅威検出ポリシーの詳細を取得します。 |
> | Action | Microsoft.Sql/servers/securityAlertPolicies/write | 指定されたサーバーの脅威検出ポリシーを変更します。 |
> | Action | Microsoft.Sql/servers/serviceObjectives/read | 指定されたサーバーで利用可能なサービス レベルの目標 (パフォーマンス レベルとも呼ばれます) の一覧を取得します。 |
> | Action | Microsoft.Sql/servers/syncAgents/delete | 既存の同期エージェントを削除します。 |
> | Action | Microsoft.Sql/servers/syncAgents/generateKey/action | 同期エージェントの登録キーを生成します。 |
> | Action | Microsoft.Sql/servers/syncAgents/linkedDatabases/read | 同期エージェントのリンクされたデータベースの一覧を返します。 |
> | Action | Microsoft.Sql/servers/syncAgents/read | 同期エージェントの一覧を返すか、指定された同期エージェントのプロパティを取得します。 |
> | Action | Microsoft.Sql/servers/syncAgents/write | 指定されたパラメーターで同期エージェントを作成するか、指定され同期エージェントのプロパティを更新します。 |
> | Action | Microsoft.Sql/servers/tdeCertificates/action | TDE 証明書を作成/更新します |
> | Action | Microsoft.Sql/servers/usages/read | サーバーの DTU クォータと、サーバー内のすべてのデータベースによる現在の DTU 消費量を返します。 |
> | Action | Microsoft.Sql/servers/virtualNetworkRules/delete | 既存の仮想ネットワーク ルールを削除します。 |
> | Action | Microsoft.Sql/servers/virtualNetworkRules/read | 仮想ネットワーク ルールの一覧を返すか、指定された仮想ネットワーク ルールのプロパティを取得します。 |
> | Action | Microsoft.Sql/servers/virtualNetworkRules/write | 指定されたパラメーターで仮想ネットワーク ルールを作成するか、指定された仮想ネットワーク ルールのプロパティまたはタグを更新します。 |
> | Action | Microsoft.Sql/servers/write | 指定されたパラメーターでサーバーを作成するか、指定されたサーバーのプロパティまたはタグを更新します。 |
> | Action | Microsoft.Sql/unregister/action | Microsoft SQL Database リソース プロバイダーのサブスクリプションを登録解除し、Microsoft SQL Database を作成できるようにします。 |
> | Action | Microsoft.Sql/virtualClusters/read | 仮想クラスターの一覧を返すか、指定された仮想クラスターのプロパティを取得します。 |
> | Action | Microsoft.Sql/virtualClusters/write | 仮想クラスターのタグを更新します。 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.Storage/checknameavailability/read | アカウント名が有効であり、使用されていないことを確認します。 |
> | Action | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | 仮想ネットワークまたはサブネットを削除していることを Microsoft.Storage に通知します。 |
> | Action | Microsoft.Storage/locations/usages/read | 指定されたサブスクリプションのリソースの制限と現在の使用回数を返します。 |
> | Action | Microsoft.Storage/operations/read | 非同期操作の状態をポーリングします。 |
> | Action | Microsoft.Storage/register/action | ストレージ リソース プロバイダーにサブスクリプションを登録し、ストレージ アカウントを作成できるようにします。 |
> | Action | Microsoft.Storage/skus/read | Microsoft.Storage でサポートされている SKU を一覧表示します。 |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action | BLOB コンテンツを追加した結果を返します。 |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | BLOB を削除した結果を返します。 |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | BLOB または BLOB の一覧を返します。 |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action | BLOB コマンドの結果を返します |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | BLOB の書き込みの結果を返します。 |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | BLOB コンテナーの訴訟ホールドをクリアします。 |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/delete | コンテナーを削除した結果を返します。 |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | BLOB コンテナーの不変ポリシーを削除します。 |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action | BLOB コンテナーの不変ポリシーを拡張します。 |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action | BLOB コンテナーの不変ポリシーをロックします。 |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/read | BLOB コンテナーの不変ポリシーを取得します。 |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | BLOB コンテナーの不変ポリシーを設定します。 |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/read | コンテナーまたはコンテナーの一覧を返します。 |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | BLOB コンテナーの訴訟ホールドを設定します。 |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/write | BLOB コンテナーの設定またはリースの結果を返します。 |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Blob service のユーザーの委任キーを返します |
> | Action | Microsoft.Storage/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Action | Microsoft.Storage/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Action | Microsoft.Storage/storageAccounts/blobServices/providers/Microsoft.Insights/logDefinitions/read | BLOB のログ定義を取得します |
> | Action | Microsoft.Storage/storageAccounts/blobServices/providers/Microsoft.Insights/metricDefinitions/read | Microsoft ストレージ メトリックの定義の一覧を取得します。 |
> | Action | Microsoft.Storage/storageAccounts/blobServices/read | Blob service のプロパティまたは統計情報を返します。 |
> | Action | Microsoft.Storage/storageAccounts/blobServices/write | Blob service のプロパティの設定の結果を返します。 |
> | Action | Microsoft.Storage/storageAccounts/delete | 既存のストレージ アカウントを削除します。 |
> | Action | Microsoft.Storage/storageAccounts/fileServices/fileShare/delete | ユーザーがファイル共有を削除できるようにします。 |
> | Action | Microsoft.Storage/storageAccounts/fileServices/fileShare/read | ユーザーがファイル共有を読み取ることができるようにします。 |
> | Action | Microsoft.Storage/storageAccounts/fileServices/fileShare/write | ユーザーがファイル共有に書き込めるようにします。 |
> | Action | Microsoft.Storage/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Action | Microsoft.Storage/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Action | Microsoft.Storage/storageAccounts/fileServices/providers/Microsoft.Insights/logDefinitions/read | File のログ定義を取得します |
> | Action | Microsoft.Storage/storageAccounts/fileServices/providers/Microsoft.Insights/metricDefinitions/read | Microsoft ストレージ メトリックの定義の一覧を取得します。 |
> | Action | Microsoft.Storage/storageAccounts/listAccountSas/action | 指定されたストレージ アカウントのアカウント SAS トークンを返します。 |
> | Action | Microsoft.Storage/storageAccounts/listkeys/action | 指定されたストレージ アカウントのアクセス キーを返します。 |
> | Action | Microsoft.Storage/storageAccounts/listServiceSas/action | 指定されたストレージ アカウントのサービス SAS トークンを返します。 |
> | Action | Microsoft.Storage/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Action | Microsoft.Storage/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Action | Microsoft.Storage/storageAccounts/providers/Microsoft.Insights/metricDefinitions/read | Microsoft ストレージ メトリックの定義の一覧を取得します。 |
> | Action | Microsoft.Storage/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Action | Microsoft.Storage/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Action | Microsoft.Storage/storageAccounts/queueServices/providers/Microsoft.Insights/logDefinitions/read | Queue のログ定義を取得します |
> | Action | Microsoft.Storage/storageAccounts/queueServices/providers/Microsoft.Insights/metricDefinitions/read | Microsoft ストレージ メトリックの定義の一覧を取得します。 |
> | Action | Microsoft.Storage/storageAccounts/queueServices/queues/delete | キューを削除した結果を返します。 |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | メッセージを追加した結果を返します。 |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | メッセージを削除した結果を返します。 |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | メッセージを処理した結果を返します。 |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | メッセージを返します。 |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | メッセージの書き込みの結果を返します。 |
> | Action | Microsoft.Storage/storageAccounts/queueServices/queues/read | キューまたはキューの一覧を返します。 |
> | Action | Microsoft.Storage/storageAccounts/queueServices/queues/write | キューの書き込みの結果を返します。 |
> | Action | Microsoft.Storage/storageAccounts/queueServices/read | Queue サービスのプロパティまたは統計情報を返します。 |
> | Action | Microsoft.Storage/storageAccounts/queueServices/write | Queue サービスのプロパティを設定した結果を返します。 |
> | Action | Microsoft.Storage/storageAccounts/read | ストレージ アカウントの一覧を返すか、指定されたストレージ アカウントのプロパティを取得します。 |
> | Action | Microsoft.Storage/storageAccounts/regeneratekey/action | 指定されたストレージ アカウントのアクセス キーを再生成します。 |
> | Action | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | ストレージ アカウントの診断設定を作成または更新します。 |
> | Action | Microsoft.Storage/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Action | Microsoft.Storage/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Action | Microsoft.Storage/storageAccounts/tableServices/providers/Microsoft.Insights/logDefinitions/read | Table のログ定義を取得します |
> | Action | Microsoft.Storage/storageAccounts/tableServices/providers/Microsoft.Insights/metricDefinitions/read | Microsoft ストレージ メトリックの定義の一覧を取得します。 |
> | Action | Microsoft.Storage/storageAccounts/write | 指定されたパラメーターを使用してストレージ アカウントを作成するか、プロパティまたはタグを更新します。または、指定されたストレージ アカウントのカスタム ドメインを追加します。 |
> | Action | Microsoft.Storage/usages/read | 指定されたサブスクリプションのリソースの制限と現在の使用回数を返します。 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | microsoft.storagesync/locations/checkNameAvailability/action | ストレージ同期サービスについて名前を使用できるかどうかを確認します |
> | Action | microsoft.storagesync/storageSyncServices/delete | ストレージ同期サービスを削除します。 |
> | Action | microsoft.storagesync/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read | ストレージ同期サービスで使用可能なメトリックを取得します。 |
> | Action | microsoft.storagesync/storageSyncServices/read | ストレージ同期サービスを読み取ります。 |
> | Action | microsoft.storagesync/storageSyncServices/registeredServers/delete | 登録済みサーバーを削除します。 |
> | Action | microsoft.storagesync/storageSyncServices/registeredServers/providers/Microsoft.Insights/metricDefinitions/read | 登録済みサーバーで使用可能なメトリックを取得します。 |
> | Action | microsoft.storagesync/storageSyncServices/registeredServers/read | 登録済みサーバーを読み取ります。 |
> | Action | microsoft.storagesync/storageSyncServices/registeredServers/write | 登録済みサーバーを作成または更新します。 |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | クラウド エンドポイントを削除します。 |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | 非同期バックアップ呼び出しの場所 API です。 |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | バックアップの後にこのアクションを呼び出します。 |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | 復元の後にこのアクションを呼び出します。 |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | バックアップの前にこのアクションを呼び出します。 |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | 復元の前にこのアクションを呼び出します。 |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | クラウド エンドポイントを読み取ります。 |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action | ハートビートを復元します。 |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | クラウド エンドポイントを作成または更新します。 |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/delete | 同期グループを削除します。 |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/providers/Microsoft.Insights/metricDefinitions/read | 同期グループで使用可能なメトリックを取得します。 |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/read | 同期グループを読み取ります。 |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | サーバー エンドポイントを削除します。 |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/providers/Microsoft.Insights/metricDefinitions/read | サーバー エンドポイントで使用可能なメトリックを取得します。 |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | サーバー エンドポイントを読み取ります。 |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | このアクションを呼び出して、サーバーにファイルを戻します。 |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | サーバー エンドポイントを作成または更新します。 |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/write | 同期グループを作成または更新します。 |
> | Action | microsoft.storagesync/storageSyncServices/write | ストレージ同期サービスを作成または更新します。 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.StorSimple/managers/accessControlRecords/delete | アクセス制御レコードを削除します。 |
> | Action | Microsoft.StorSimple/managers/accessControlRecords/operationResults/read | 操作結果を一覧表示するか、取得します。 |
> | Action | Microsoft.StorSimple/managers/accessControlRecords/read | アクセス制御レコードを一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/managers/accessControlRecords/write | アクセス制御レコードを作成または更新します。 |
> | Action | Microsoft.StorSimple/managers/alerts/read | アラートを一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/managers/backups/read | バックアップ セットを一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/managers/bandwidthSettings/delete | 既存の帯域幅設定を削除します (8000 シリーズのみ)。 |
> | Action | Microsoft.StorSimple/managers/bandwidthSettings/operationResults/read | 操作結果を一覧表示します。 |
> | Action | Microsoft.StorSimple/managers/bandwidthSettings/read | 帯域幅設定を一覧表示します (8000 シリーズのみ)。 |
> | Action | Microsoft.StorSimple/managers/bandwidthSettings/write | 新しい帯域幅設定を作成するか、帯域幅設定を更新します (8000 シリーズのみ)。 |
> | Action | Microsoft.StorSimple/managers/certificates/write | 証明書を作成または更新します。 |
> | Action | Microsoft.StorSimple/Managers/certificates/write | "リソース証明書を更新" 操作では、リソース/コンテナー資格情報証明書を更新します。 |
> | Action | Microsoft.StorSimple/managers/clearAlerts/action | デバイス マネージャーに関連付けられているすべてのアラートをクリアします。 |
> | Action | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | クラウド アプライアンスでサポートされている構成を一覧表示します。 |
> | Action | Microsoft.StorSimple/managers/configureDevice/action | デバイスを構成します。 |
> | Action | Microsoft.StorSimple/managers/delete | デバイス マネージャーを削除します。 |
> | Action | Microsoft.StorSimple/Managers/delete | "コンテナーの削除" 操作では、指定された "コンテナー" 型の Azure リソースを削除します。 |
> | Action | Microsoft.StorSimple/managers/devices/alertSettings/operationResults/read | 操作結果を一覧表示するか、取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/alertSettings/read | アラート設定を一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/alertSettings/write | アラート設定を作成または更新します。 |
> | Action | Microsoft.StorSimple/managers/devices/authorizeForServiceEncryptionKeyRollover/action | デバイスのサービス暗号化キー ロールオーバーに対して承認する |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/backup/action | 手動バックアップを実行して、ポリシーで保護されているすべてのボリュームのオンデマンド バックアップを作成します。 |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/delete | 既存のバックアップ ポリシーを削除します (8000 シリーズのみ)。 |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/operationResults/read | 操作結果を一覧表示します。 |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/read | バックアップ ポリシーを一覧表示します (8000 シリーズのみ)。 |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/delete | 既存のスケジュールを削除します。 |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/operationResults/read | 操作結果を一覧表示します。 |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/read | スケジュールを一覧表示します。 |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/write | 新しいスケジュールを作成するか、スケジュールを更新します。 |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/write | 新しいバックアップ ポリシーを作成するか、バックアップ ポリシーを更新します (8000 シリーズのみ)。 |
> | Action | Microsoft.StorSimple/managers/devices/backups/delete | バックアップ セットを削除します。 |
> | Action | Microsoft.StorSimple/managers/devices/backups/elements/clone/action | バックアップ要素を使用して、共有またはボリュームを複製します。 |
> | Action | Microsoft.StorSimple/managers/devices/backups/elements/operationResults/read | 操作結果を一覧表示するか、取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/backups/operationResults/read | 操作結果を一覧表示するか、取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/backups/read | バックアップ セットを一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/backups/restore/action | バックアップ セットからすべてのボリュームを復元します。 |
> | Action | Microsoft.StorSimple/managers/devices/backupScheduleGroups/delete | バックアップ スケジュール グループを削除します。 |
> | Action | Microsoft.StorSimple/managers/devices/backupScheduleGroups/operationResults/read | 操作結果を一覧表示するか、取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/backupScheduleGroups/read | バックアップ スケジュール グループを一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/backupScheduleGroups/write | バックアップ スケジュール グループを作成または更新します。 |
> | Action | Microsoft.StorSimple/managers/devices/chapSettings/delete | CHAP 設定を削除します。 |
> | Action | Microsoft.StorSimple/managers/devices/chapSettings/operationResults/read | 操作結果を一覧表示するか、取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/chapSettings/read | CHAP 設定を一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/chapSettings/write | CHAP 設定を作成または更新します。 |
> | Action | Microsoft.StorSimple/managers/devices/deactivate/action | デバイスを非アクティブ化します。 |
> | Action | Microsoft.StorSimple/managers/devices/delete | デバイスを削除します。 |
> | Action | Microsoft.StorSimple/managers/devices/disks/read | ディスクを一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/download/action | デバイスの更新プログラムをダウンロードします。 |
> | Action | Microsoft.StorSimple/managers/devices/failover/action | デバイスをフェールオーバーします。 |
> | Action | Microsoft.StorSimple/managers/devices/failover/operationResults/read | 操作結果を一覧表示するか、取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/failoverTargets/read | デバイスのフェールオーバー ターゲットを一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/backup/action | ファイル サーバーのバックアップを作成します。 |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/delete | ファイル サーバーを削除します。 |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/metrics/read | メトリックを一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/metricsDefinitions/read | メトリック定義を一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/operationResults/read | 操作結果を一覧表示するか、取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/read | ファイル サーバーを一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/shares/delete | 共有を削除します。 |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/shares/metrics/read | メトリックを一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/shares/metricsDefinitions/read | メトリック定義を一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/shares/operationResults/read | 操作結果を一覧表示するか、取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/shares/read | 共有を一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/shares/write | 共有を作成または更新します。 |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/write | ファイル サーバーを作成または更新します。 |
> | Action | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | ハードウェア コンポーネント グループのコントローラーの電源の状態を変更します。 |
> | Action | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/operationResults/read | 操作結果を一覧表示します。 |
> | Action | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/read | ハードウェア コンポーネント グループを一覧表示します。 |
> | Action | Microsoft.StorSimple/managers/devices/install/action | デバイスに更新プログラムをインストールします。 |
> | Action | Microsoft.StorSimple/managers/devices/installUpdates/action | デバイスに更新プログラムをインストールします (8000 シリーズのみ)。 |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/backup/action | iSCSI サーバーのバックアップを作成します。 |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/delete | iSCSI サーバーを削除します。 |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/delete | ディスクを削除します。 |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metrics/read | メトリックを一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metricsDefinitions/read | メトリック定義を一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/operationResults/read | 操作結果を一覧表示するか、取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/read | ディスクを一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/write | ディスクを作成または更新します。 |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/metrics/read | メトリックを一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/metricsDefinitions/read | メトリック定義を一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/operationResults/read | 操作結果を一覧表示するか、取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/read | iSCSI サーバーを一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/write | iSCSI サーバーを作成または更新します。 |
> | Action | Microsoft.StorSimple/managers/devices/jobs/cancel/action | 実行中のジョブを取り消します。 |
> | Action | Microsoft.StorSimple/managers/devices/jobs/operationResults/read | 操作結果を一覧表示します。 |
> | Action | Microsoft.StorSimple/managers/devices/jobs/read | ジョブを一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/listFailoverSets/action | 既存のデバイスのフェールオーバー セットを一覧表示します (8000 シリーズのみ)。 |
> | Action | Microsoft.StorSimple/managers/devices/listFailoverTargets/action | デバイスのフェールオーバー ターゲットを一覧表示します (8000 シリーズのみ)。 |
> | Action | Microsoft.StorSimple/managers/devices/metrics/read | メトリックを一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/metricsDefinitions/read | メトリック定義を一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigration/action | 移行が正常に完了したことを確認し、移行をコミットします。 |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigrationStatus/read | 移行の確認の状態を一覧表示します。 |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action | 移行の確認の状態を取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | 移行推定ジョブの状態を取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action | 移行の状態を取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/import/action | 移行のためにソース構成をインポートします。 |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationEstimate/read | 移行見積もりを一覧表示します。 |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationStatus/read | 移行状態を一覧表示します。 |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/operationResults/read | 操作結果を一覧表示します。 |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigration/action | ソース構成を使用して移行を開始します。 |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action | 移行プロセスの継続時間を推定するジョブを開始します。 |
> | Action | Microsoft.StorSimple/managers/devices/networkSettings/operationResults/read | 操作結果を一覧表示します。 |
> | Action | Microsoft.StorSimple/managers/devices/networkSettings/read | ネットワーク設定を一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/networkSettings/write | 新しいネットワーク設定を作成するか、ネットワーク設定を更新します。 |
> | Action | Microsoft.StorSimple/managers/devices/operationResults/read | 操作結果を一覧表示するか、取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/publicEncryptionKey/action | デバイス マネージャーの公開暗号化キーを一覧表示します。 |
> | Action | Microsoft.StorSimple/managers/devices/publishSupportPackage/action | Microsoft サポートによるトラブルシューティングのために、デバイスのサポート パッケージを発行します。 |
> | Action | Microsoft.StorSimple/managers/devices/read | デバイスを一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/scanForUpdates/action | デバイスで更新プログラムをスキャンします。 |
> | Action | Microsoft.StorSimple/managers/devices/securitySettings/operationResults/read | 操作結果を一覧表示するか、取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/securitySettings/read | セキュリティ設定を一覧表示します。 |
> | Action | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | デバイスのリモート管理証明書を同期します。 |
> | Action | Microsoft.StorSimple/managers/devices/securitySettings/update/action | セキュリティ設定を更新します。 |
> | Action | Microsoft.StorSimple/managers/devices/securitySettings/write | 新しいセキュリティ設定を作成するか、セキュリティ設定を更新します。 |
> | Action | Microsoft.StorSimple/managers/devices/sendTestAlertEmail/action | 構成済みの電子メール受信者にテスト アラート メールを送信します。 |
> | Action | Microsoft.StorSimple/managers/devices/shares/read | 共有を一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/timeSettings/operationResults/read | 操作結果を一覧表示します。 |
> | Action | Microsoft.StorSimple/managers/devices/timeSettings/read | 時刻設定を一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/timeSettings/write | 新しい時刻設定を作成するか、時刻設定を更新します。 |
> | Action | Microsoft.StorSimple/managers/devices/updates/operationResults/read | 操作結果を一覧表示するか、取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/updateSummary/read | 更新の概要を一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/delete | 既存のボリューム コンテナーを削除します (8000 シリーズのみ)。 |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/metrics/read | メトリックを一覧表示します。 |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/metricsDefinitions/read | メトリック定義を一覧表示します。 |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/operationResults/read | 操作結果を一覧表示します。 |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/read | ボリューム コンテナーを一覧表示します (8000 シリーズのみ)。 |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/delete | 既存のボリュームを削除します。 |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metrics/read | メトリックを一覧表示します。 |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metricsDefinitions/read | メトリック定義を一覧表示します。 |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/operationResults/read | 操作結果を一覧表示します。 |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/read | ボリュームを一覧表示します。 |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/write | 新しいボリュームを作成するか、ボリュームを更新します。 |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/write | 新しいボリューム コンテナーを作成するか、ボリューム コンテナーを更新します (8000 シリーズのみ)。 |
> | Action | Microsoft.StorSimple/managers/devices/volumes/read | ボリュームを一覧表示します。 |
> | Action | Microsoft.StorSimple/managers/devices/write | デバイスを作成または更新します。 |
> | Action | Microsoft.StorSimple/managers/encryptionSettings/read | 暗号化設定を一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/managers/extendedInformation/delete | 拡張資格情報コンテナーの情報を削除します |
> | Action | Microsoft.StorSimple/Managers/extendedInformation/delete | "拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。 |
> | Action | Microsoft.StorSimple/managers/extendedInformation/read | 拡張資格情報コンテナーの情報を一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/Managers/extendedInformation/read | "拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。 |
> | Action | Microsoft.StorSimple/managers/extendedInformation/write | 拡張資格情報コンテナーの情報を作成または更新します。 |
> | Action | Microsoft.StorSimple/Managers/extendedInformation/write | "拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。 |
> | Action | Microsoft.StorSimple/managers/features/read | 機能を一覧表示します。 |
> | Action | Microsoft.StorSimple/managers/fileservers/read | ファイル サーバーを一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/managers/getActivationKey/action | デバイス マネージャーのアクティブ化キーを取得します。 |
> | Action | Microsoft.StorSimple/managers/getEncryptionKey/action | デバイス マネージャーの暗号化キーを取得します。 |
> | Action | Microsoft.StorSimple/managers/iscsiservers/read | iSCSI サーバーを一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/managers/jobs/read | ジョブを一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/managers/listActivationKey/action | StorSimple デバイス マネージャーのアクティブ化キーを取得します。 |
> | Action | Microsoft.StorSimple/managers/listPublicEncryptionKey/action | StorSimple デバイス マネージャーの公開暗号化キーを一覧表示します。 |
> | Action | Microsoft.StorSimple/managers/metrics/read | メトリックを一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/managers/metricsDefinitions/read | メトリック定義を一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/managers/migrateClassicToResourceManager/action | マネージャーをクラシックから Resource Manager に移行します。 |
> | Action | Microsoft.StorSimple/managers/migrationSourceConfigurations/read | 移行ソース構成を一覧表示します (8000 シリーズのみ)。 |
> | Action | Microsoft.StorSimple/managers/operationResults/read | 操作結果を一覧表示するか、取得します。 |
> | Action | Microsoft.StorSimple/managers/provisionCloudAppliance/action | 新しいクラウド アプライアンスを作成します。 |
> | Action | Microsoft.StorSimple/managers/read | デバイス マネージャーを一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/Managers/read | "コンテナーの取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトを取得します。 |
> | Action | Microsoft.StorSimple/managers/regenarateRegistationCertificate/action | デバイス マネージャーの登録証明書を再生成します。 |
> | Action | Microsoft.StorSimple/managers/regenerateActivationKey/action | デバイス マネージャーのアクティブ化キーを再生成します。 |
> | Action | Microsoft.StorSimple/managers/storageAccountCredentials/delete | ストレージ アカウントの資格情報を削除します。 |
> | Action | Microsoft.StorSimple/managers/storageAccountCredentials/operationResults/read | 操作結果を一覧表示するか、取得します。 |
> | Action | Microsoft.StorSimple/managers/storageAccountCredentials/read | ストレージ アカウントの資格情報を一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/managers/storageAccountCredentials/write | ストレージ アカウントの資格情報を作成または更新します。 |
> | Action | Microsoft.StorSimple/managers/storageDomains/delete | ストレージ ドメインを削除します。 |
> | Action | Microsoft.StorSimple/managers/storageDomains/operationResults/read | 操作結果を一覧表示するか、取得します。 |
> | Action | Microsoft.StorSimple/managers/storageDomains/read | ストレージ ドメインを一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/managers/storageDomains/write | ストレージ ドメインを作成または更新します。 |
> | Action | Microsoft.StorSimple/managers/write | デバイス マネージャーを作成または更新します。 |
> | Action | Microsoft.StorSimple/Managers/write | "コンテナーの作成" 操作では、"コンテナー" 型の Azure リソースを作成します。 |
> | Action | Microsoft.StorSimple/operations/read | 操作を一覧表示または取得します。 |
> | Action | Microsoft.StorSimple/register/action | プロバイダー Microsoft.StorSimple を登録します。 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.StreamAnalytics/locations/quotas/Read | Stream Analytics サブスクリプションのクォータを読み取ります。 |
> | Action | Microsoft.StreamAnalytics/operations/Read | Stream Analytics の操作を読み取ります。 |
> | Action | Microsoft.StreamAnalytics/Register/action | Stream Analytics リソース プロバイダーにサブスクリプションを登録します。 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/Delete | Stream Analytics ジョブを削除します。 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/functions/Delete | Stream Analytics ジョブ関数を削除します。 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/functions/operationresults/Read | Stream Analytics ジョブ関数の操作の結果を読み取ります。 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/functions/Read | Stream Analytics ジョブ関数を読み取ります。 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/action | Stream Analytics ジョブ関数の既定の定義を取得します。 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/functions/Test/action | Stream Analytics ジョブ関数をテストします。 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/functions/Write | Stream Analytics ジョブ関数を書き込みます。 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/inputs/Delete | Stream Analytics ジョブ入力を削除します。 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/inputs/operationresults/Read | Stream Analytics ジョブ入力の操作の結果を読み取ります。 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/inputs/Read | Stream Analytics ジョブ入力を読み取ります。 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/inputs/Sample/action | Stream Analytics ジョブ入力をサンプリングします。 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/inputs/Test/action | Stream Analytics ジョブ入力をテストします。 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/inputs/Write | Stream Analytics ジョブ入力を書き込みます。 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/metricdefinitions/Read | メトリック定義を読み取ります。 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/operationresults/Read | Stream Analytics ジョブの操作の結果を読み取ります。 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/outputs/Delete | Stream Analytics ジョブ出力を削除します。 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/outputs/operationresults/Read | Stream Analytics ジョブ出力の操作の結果を読み取ります。 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/outputs/Read | Stream Analytics ジョブ出力を読み取ります。 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/outputs/Test/action | Stream Analytics ジョブ出力をテストします。 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/outputs/Write | Stream Analytics ジョブ出力を書き込みます。 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read | 診断設定を読み取ります。 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write | 診断設定を書き込みます。 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/logDefinitions/read | streamingjobs の利用可能なログを取得します。 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | streamingjobs の利用可能なメトリックを取得します。 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/Read | Stream Analytics ジョブを読み取ります。 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/Start/action | Stream Analytics ジョブを開始します。 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/Stop/action | Stream Analytics ジョブを停止します。 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/transformations/Delete | Stream Analytics ジョブ変換を削除します。 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/transformations/Read | Stream Analytics ジョブ変換を読み取ります。 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/transformations/Write | Stream Analytics ジョブ変換を書き込みます。 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/Write | Stream Analytics ジョブを書き込みます。 |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.Subscription/CreateSubscription/action | Azure サブスクリプションを作成します。 |
> | Action | Microsoft.Subscription/SubscriptionDefinitions/read | 管理グループ内の Azure サブスクリプションの定義を取得します。 |
> | Action | Microsoft.Subscription/SubscriptionDefinitions/write | Azure サブスクリプションの定義を作成します。 |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.Support/register/action | サポート リソース プロバイダーに登録します。 |
> | Action | Microsoft.Support/supportTickets/read | サポート チケットの詳細 (ステータス、重大度、連絡先の詳細、やり取りなど) を取得するか、サブスクリプション全体のサポート チケットの一覧を取得します。 |
> | Action | Microsoft.Support/supportTickets/write | サポート チケットを作成または更新します。 技術、課金、クォータ、またはサブスクリプション管理に関する問題のサポート チケットを作成できます。 既存のサポート チケットの重大度、連絡先の詳細、やり取りを更新できます。 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | アクセス ポリシーを削除します。 |
> | Action | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | アクセス ポリシーのプロパティを取得します。 |
> | Action | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | 環境に対する新しいアクセス ポリシーを作成するか、既存のアクセス ポリシーを更新します。 |
> | Action | Microsoft.TimeSeriesInsights/environments/delete | 環境を削除します。 |
> | Action | Microsoft.TimeSeriesInsights/environments/eventsources/delete | イベント ソースを削除します。 |
> | Action | Microsoft.TimeSeriesInsights/environments/eventsources/eventsources/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Action | Microsoft.TimeSeriesInsights/environments/eventsources/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Action | Microsoft.TimeSeriesInsights/environments/eventsources/providers/Microsoft.Insights/metricDefinitions/read | イベント ソースで使用可能なメトリックを取得します。 |
> | Action | Microsoft.TimeSeriesInsights/environments/eventsources/read | イベント ソースのプロパティを取得します。 |
> | Action | Microsoft.TimeSeriesInsights/environments/eventsources/write | 環境に対する新しいイベント ソースを作成するか、既存のイベント ソースを更新します。 |
> | Action | Microsoft.TimeSeriesInsights/environments/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Action | Microsoft.TimeSeriesInsights/environments/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Action | Microsoft.TimeSeriesInsights/environments/providers/Microsoft.Insights/metricDefinitions/read | 環境で使用可能なメトリックを取得します。 |
> | Action | Microsoft.TimeSeriesInsights/environments/read | 環境のプロパティを取得します。 |
> | Action | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | 参照データ セットを削除します。 |
> | Action | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | 参照データ セットのプロパティを取得します。 |
> | Action | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | 環境に対する新しい参照データ セットを作成するか、既存の参照データ セットを更新します。 |
> | Action | Microsoft.TimeSeriesInsights/environments/status/read | 環境の状態、およびそれに関連するイングレスなどの操作の状態を取得します。 |
> | Action | Microsoft.TimeSeriesInsights/environments/write | 新しい環境を作成するか、既存の環境を更新します。 |
> | Action | Microsoft.TimeSeriesInsights/register/action | Time Series Insights リソース プロバイダーにサブスクリプションを登録し、Time Series Insights 環境を作成できるようにします。 |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.VisualStudio/Account/Delete | アカウントを削除します。 |
> | Action | Microsoft.VisualStudio/Account/Extension/Read | アカウント/拡張機能を読み取ります。 |
> | Action | Microsoft.VisualStudio/Account/Project/Read | アカウント/プロジェクトを読み取ります。 |
> | Action | Microsoft.VisualStudio/Account/Project/Write | アカウント/プロジェクトを設定します。 |
> | Action | Microsoft.VisualStudio/Account/Read | アカウントを読み取ります。 |
> | Action | Microsoft.VisualStudio/Account/Write | アカウントを設定します。 |
> | Action | Microsoft.VisualStudio/Extension/Delete | 拡張機能を削除します。 |
> | Action | Microsoft.VisualStudio/Extension/Read | 拡張機能を読み取ります。 |
> | Action | Microsoft.VisualStudio/Extension/Write | 拡張機能を設定します。 |
> | Action | Microsoft.VisualStudio/Project/Delete | プロジェクトを削除します。 |
> | Action | Microsoft.VisualStudio/Project/Read | プロジェクトを読み取ります。 |
> | Action | Microsoft.VisualStudio/Project/Write | プロジェクトを設定します。 |
> | Action | Microsoft.VisualStudio/Register/Action | Microsoft.VisualStudio プロバイダーに Azure サブスクリプションを登録します。 |

## <a name="microsoftweb"></a>microsoft.web

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | microsoft.web/apimanagementaccounts/apiacls/read | API Management アカウントの API ACL を取得します。 |
> | Action | microsoft.web/apimanagementaccounts/apis/apiacls/delete | API Management アカウント API の API ACL を削除します。 |
> | Action | microsoft.web/apimanagementaccounts/apis/apiacls/read | API Management アカウント API の API ACL を取得します。 |
> | Action | microsoft.web/apimanagementaccounts/apis/apiacls/write | API Management アカウント API の API ACL を更新します。 |
> | Action | microsoft.web/apimanagementaccounts/apis/connectionacls/read | API Management アカウントの API の Connectionacls を取得します。 |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/confirmconsentcode/action | 同意コード API Management アカウントの API 接続を確認します。 |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/delete | API Management アカウントの API 接続の Connectionacls を削除します。 |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/read | API Management アカウントの API 接続の Connectionacls を取得します。 |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/write | API Management アカウントの API 接続の Connectionacls を更新します。 |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/delete | API Management アカウントの API 接続を削除します。 |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/getconsentlinks/action | API Management アカウントの API 接続の同意リンクを取得します。 |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/listconnectionkeys/action | 接続キー API Management アカウントの API 接続を一覧表示します。 |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/listsecrets/action | シークレット API Management アカウントの API 接続を一覧表示します。 |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/read | API Management アカウントの API 接続を取得します。 |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/write | API Management アカウントの API 接続を更新します。 |
> | Action | microsoft.web/apimanagementaccounts/apis/delete | API Management アカウントの API を削除します。 |
> | Action | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/delete | API Management アカウントの API のローカライズされた定義を削除します。 |
> | Action | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/read | API Management アカウントの API のローカライズされた定義を取得します。 |
> | Action | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/write | API Management アカウントの API のローカライズされた定義を更新します。 |
> | Action | microsoft.web/apimanagementaccounts/apis/read | API Management アカウントの API を取得します。 |
> | Action | microsoft.web/apimanagementaccounts/apis/write | API Management アカウントの API を更新します。 |
> | Action | microsoft.web/apimanagementaccounts/connectionacls/read | API Management アカウントの Connectionacls を取得します。 |
> | Action | microsoft.web/availablestacks/read | 使用可能なスタックを取得します。 |
> | Action | microsoft.web/billingmeters/read | 請求測定の一覧を取得します。 |
> | Action | Microsoft.Web/certificates/Delete | 既存の証明書を削除します。 |
> | Action | Microsoft.Web/certificates/Read | 証明書の一覧を取得します。 |
> | Action | Microsoft.Web/certificates/Write | 新しい証明書を追加するか、既存の証明書を更新します。 |
> | Action | microsoft.web/checknameavailability/read | リソース名を使用できるかどうかを確認します。 |
> | Action | microsoft.web/classicmobileservices/read | 従来の Mobile Services を取得します。 |
> | Action | Microsoft.Web/connectionGateways/Delete | 接続ゲートウェイを削除します。 |
> | Action | Microsoft.Web/connectionGateways/Join/Action | 接続ゲートウェイを接続します。 |
> | Action | Microsoft.Web/connectionGateways/ListStatus/Action | 接続ゲートウェイの状態を一覧表示します。 |
> | Action | Microsoft.Web/connectionGateways/Move/Action | 接続ゲートウェイを移動します。 |
> | Action | Microsoft.Web/connectionGateways/Read | 接続ゲートウェイの一覧を取得します。 |
> | Action | Microsoft.Web/connectionGateways/Write | 接続ゲートウェイを作成または更新します。 |
> | Action | microsoft.web/connections/confirmconsentcode/action | 接続の同意コードを確認します。 |
> | Action | Microsoft.Web/connections/Delete | 接続を削除します。 |
> | Action | Microsoft.Web/connections/Join/Action | 接続に参加します。 |
> | Action | microsoft.web/connections/listconsentlinks/action | 接続の同意リンクを一覧表示します。 |
> | Action | Microsoft.Web/connections/Move/Action | 接続を移動します。 |
> | Action | Microsoft.Web/connections/Read | 接続の一覧を取得します。 |
> | Action | Microsoft.Web/connections/Write | 接続を作成または更新します。 |
> | Action | Microsoft.Web/customApis/Delete | カスタム API を削除します。 |
> | Action | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | WSDL から API の定義を抽出します。 |
> | Action | Microsoft.Web/customApis/Join/Action | カスタム API を結合します。 |
> | Action | Microsoft.Web/customApis/listWsdlInterfaces/Action | カスタム API の WSDL インターフェイスを一覧表示します。 |
> | Action | Microsoft.Web/customApis/Move/Action | カスタム API を移動します。 |
> | Action | Microsoft.Web/customApis/Read | カスタム API の一覧を取得します。 |
> | Action | Microsoft.Web/customApis/Write | カスタム API を作成または更新します。 |
> | Action | Microsoft.Web/deletedSites/Read | 削除された Web アプリのプロパティを取得します。 |
> | Action | microsoft.web/deploymentlocations/read | デプロイの場所を取得します。 |
> | Action | Microsoft.Web/geoRegions/Read | 地理的リージョンの一覧を取得します。 |
> | Action | microsoft.web/hostingenvironments/capacities/read | ホスティング環境の容量を取得します。 |
> | Action | Microsoft.Web/hostingEnvironments/Delete | App Service Environment を削除します。 |
> | Action | microsoft.web/hostingenvironments/detectors/read | ホスティング環境の検出機能を取得します。 |
> | Action | microsoft.web/hostingenvironments/diagnostics/read | ホスティング環境の診断を取得します。 |
> | Action | microsoft.web/hostingenvironments/inboundnetworkdependenciesendpoints/read | すべての受信依存関係のネットワーク エンドポイントを取得します。 |
> | Action | microsoft.web/hostingenvironments/metricdefinitions/read | ホスティング環境のメトリック定義を取得します。 |
> | Action | microsoft.web/hostingenvironments/multirolepools/metricdefinitions/read | ホスティング環境のマルチロール プールのメトリック定義を取得します。 |
> | Action | microsoft.web/hostingenvironments/multirolepools/metrics/read | ホスティング環境のマルチロール プールのメトリックを取得します。 |
> | Action | Microsoft.Web/hostingEnvironments/multiRolePools/providers/Microsoft.Insights/metricDefinitions/Read | App Service 環境のマルチロールで使用可能なメトリックを取得します。 |
> | Action | Microsoft.Web/hostingEnvironments/multiRolePools/Read | App Service Environment のフロントエンド プールのプロパティを取得します。 |
> | Action | microsoft.web/hostingenvironments/multirolepools/skus/read | ホスティング環境のマルチロール プールの SKU を取得します。 |
> | Action | microsoft.web/hostingenvironments/multirolepools/usages/read | ホスティング環境のマルチロール プールの使用状況を取得します。 |
> | Action | Microsoft.Web/hostingEnvironments/multiRolePools/Write | App Service Environment に新しいフロントエンド プールを作成するか、既存のフロントエンド プールを更新します。 |
> | Action | microsoft.web/hostingenvironments/operations/read | ホスティング環境の操作を取得します。 |
> | Action | microsoft.web/hostingenvironments/outboundnetworkdependenciesendpoints/read | すべての送信依存関係のネットワーク エンドポイントを取得します。 |
> | Action | microsoft.web/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Action | microsoft.web/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Action | Microsoft.Web/hostingEnvironments/Read | App Service Environment のプロパティを取得します。 |
> | Action | Microsoft.Web/hostingEnvironments/reboot/Action | App Service Environment 内のすべてのマシンを再起動します。 |
> | Action | microsoft.web/hostingenvironments/resume/action | ホスティング環境を再開します。 |
> | Action | microsoft.web/hostingenvironments/serverfarms/read | ホスティング環境の App Service プランを取得します。 |
> | Action | microsoft.web/hostingenvironments/sites/read | ホスティング環境の Web アプリを取得します。 |
> | Action | microsoft.web/hostingenvironments/suspend/action | ホスティング環境を中断します。 |
> | Action | microsoft.web/hostingenvironments/usages/read | ホスティング環境の使用状況を取得します。 |
> | Action | microsoft.web/hostingenvironments/workerpools/metricdefinitions/read | ホスティング環境のワーカー プールのメトリック定義を取得します。 |
> | Action | microsoft.web/hostingenvironments/workerpools/metrics/read | ホスティング環境のワーカー プールのメトリックを取得します。 |
> | Action | Microsoft.Web/hostingEnvironments/workerPools/providers/Microsoft.Insights/metricDefinitions/Read | App Service 環境のワーカー プールで使用可能なメトリックを取得します。 |
> | Action | Microsoft.Web/hostingEnvironments/workerPools/Read | App Service Environment のワーカー プールのプロパティを取得します。 |
> | Action | microsoft.web/hostingenvironments/workerpools/skus/read | ホスティング環境のワーカー プールの SKU を取得します。 |
> | Action | microsoft.web/hostingenvironments/workerpools/usages/read | ホスティング環境のワーカー プールの使用状況を取得します。 |
> | Action | Microsoft.Web/hostingEnvironments/workerPools/Write | App Service Environment に新しいワーカー プールを作成するか、既存のワーカー プールを更新します。 |
> | Action | Microsoft.Web/hostingEnvironments/Write | 新しい App Service Environment を作成するか、既存の App Service Environment を更新します。 |
> | Action | microsoft.web/ishostingenvironmentnameavailable/read | ホスティング環境名を使用できるかどうかを取得します。 |
> | Action | microsoft.web/ishostnameavailable/read | ホスト名を使用できるかどうかを確認します。 |
> | Action | microsoft.web/isusernameavailable/read | ユーザー名を使用できるかどうかを確認します。 |
> | Action | Microsoft.Web/listSitesAssignedToHostName/Read | ホスト名に割り当てられたサイトの名前を取得します。 |
> | Action | microsoft.web/locations/apioperations/read | API 操作の場所を取得します。 |
> | Action | microsoft.web/locations/connectiongatewayinstallations/read | 接続ゲートウェイのインストールの場所を取得します。 |
> | Action | microsoft.web/locations/extractapidefinitionfromwsdl/action | 場所の WSDL から API の定義を抽出します。 |
> | Action | microsoft.web/locations/listwsdlinterfaces/action | 場所の WSDL インターフェイスを一覧表示します。 |
> | Action | microsoft.web/locations/managedapis/apioperations/read | 場所のマネージ API 操作を取得します。 |
> | Action | Microsoft.Web/locations/managedapis/Join/Action | マネージド API を結合します。 |
> | Action | microsoft.web/locations/managedapis/read | マネージド API の場所を取得します。 |
> | Action | microsoft.web/operations/read | 操作を取得します。 |
> | Action | microsoft.web/publishingusers/read | 発行ユーザーを取得します。 |
> | Action | microsoft.web/publishingusers/write | 発行ユーザーを更新します。 |
> | Action | Microsoft.Web/recommendations/Read | サブスクリプションの推奨事項の一覧を取得します。 |
> | Action | microsoft.web/register/action | Microsoft.Web リソース プロバイダーをサブスクリプションに登録します。 |
> | Action | microsoft.web/resourcehealthmetadata/read | Resource Health のメタデータを取得します。 |
> | Action | microsoft.web/serverfarms/capabilities/read | App Service プランの機能を取得します。 |
> | Action | Microsoft.Web/serverfarms/Delete | 既存の App Service プランの削除 |
> | Action | microsoft.web/serverfarms/firstpartyapps/settings/delete | App Service プランのファースト パーティ アプリの設定を削除します。 |
> | Action | microsoft.web/serverfarms/firstpartyapps/settings/read | App Service プランのファースト パーティ アプリの設定を取得します。 |
> | Action | microsoft.web/serverfarms/firstpartyapps/settings/write | App Service プランのファースト パーティ アプリの設定を更新します。 |
> | Action | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/delete | App Service プランのハイブリッド接続名前空間のリレーを削除します。 |
> | Action | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/read | App Service プランのハイブリッド接続名前空間のリレーを取得します。 |
> | Action | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/sites/read | App Service プランのハイブリッド接続の名前空間のリレーの Web アプリを取得します。 |
> | Action | microsoft.web/serverfarms/hybridconnectionplanlimits/read | App Service プランのハイブリッド接続プランの制限を取得します。 |
> | Action | microsoft.web/serverfarms/hybridconnectionrelays/read | App Service プランのハイブリッド接続リレーを取得します。 |
> | Action | microsoft.web/serverfarms/metricdefinitions/read | App Service プランのメトリック定義を取得します。 |
> | Action | microsoft.web/serverfarms/metrics/read | App Service プランのメトリックを取得します。 |
> | Action | microsoft.web/serverfarms/operationresults/read | App Service プランの操作の結果を取得します。 |
> | Action | microsoft.web/serverfarms/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Action | microsoft.web/serverfarms/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Action | Microsoft.Web/serverfarms/providers/Microsoft.Insights/metricDefinitions/Read | App Service プランで使用可能なメトリックを取得します。 |
> | Action | Microsoft.Web/serverfarms/Read | App Service プランのプロパティを取得します。 |
> | Action | Microsoft.Web/serverfarms/restartSites/Action | App Service プランのすべての Web アプリを再起動します。 |
> | Action | microsoft.web/serverfarms/sites/read | App Service プランの Web アプリを取得します。 |
> | Action | microsoft.web/serverfarms/skus/read | App Service プランの SKU を取得します。 |
> | Action | microsoft.web/serverfarms/usages/read | App Service プランの使用状況を取得します。 |
> | Action | microsoft.web/serverfarms/virtualnetworkconnections/gateways/write | App Service プランの仮想ネットワーク接続のゲートウェイを更新します。 |
> | Action | microsoft.web/serverfarms/virtualnetworkconnections/read | App Service プランの仮想ネットワーク接続を取得します。 |
> | Action | microsoft.web/serverfarms/virtualnetworkconnections/routes/delete | App Service プランの仮想ネットワーク接続のルートを削除します。 |
> | Action | microsoft.web/serverfarms/virtualnetworkconnections/routes/read | App Service プランの仮想ネットワーク接続のルートを取得します。 |
> | Action | microsoft.web/serverfarms/virtualnetworkconnections/routes/write | App Service プランの仮想ネットワーク接続のルートを更新します。 |
> | Action | microsoft.web/serverfarms/workers/reboot/action | App Service プランの worker を再起動します。 |
> | Action | Microsoft.Web/serverfarms/Write | 新しい App Service プランを作成するか、既存の App Service プランを更新します。 |
> | Action | microsoft.web/sites/analyzecustomhostname/read | カスタム ホスト名を分析します。 |
> | Action | Microsoft.Web/sites/applySlotConfig/Action | ターゲット スロットの Web アプリ スロット構成を現在の Web アプリに適用します。 |
> | Action | Microsoft.Web/sites/backup/Action | Web アプリの新しいバックアップを作成します。 |
> | Action | microsoft.web/sites/backup/read | Web アプリのバックアップを取得します。 |
> | Action | microsoft.web/sites/backup/write | Web アプリのバックアップを更新します。 |
> | Action | microsoft.web/sites/backups/action | Azure Storage 内の BLOB から復元できる既存のアプリのバックアップを検出します。 |
> | Action | microsoft.web/sites/backups/delete | Web アプリのバックアップを削除します。 |
> | Action | microsoft.web/sites/backups/list/action | Web アプリのバックアップを一覧表示します。 |
> | Action | Microsoft.Web/sites/backups/Read | Web アプリのバックアップのプロパティを取得します。 |
> | Action | microsoft.web/sites/backups/restore/action | Web アプリのバックアップを復元します。 |
> | Action | microsoft.web/sites/backups/write | Web アプリのバックアップを更新します。 |
> | Action | microsoft.web/sites/config/delete | Web アプリの構成を削除します。 |
> | Action | Microsoft.Web/sites/config/list/Action | Web アプリのセキュリティに関する設定 (発行資格情報、アプリ設定、接続文字列など) を一覧表示します。 |
> | Action | Microsoft.Web/sites/config/Read | Web アプリの構成設定を取得します。 |
> | Action | Microsoft.Web/sites/config/Write | Web アプリの構成設定を更新します。 |
> | Action | microsoft.web/sites/containerlogs/action | Web アプリの zip 圧縮されたコンテナーのログを取得します。 |
> | Action | microsoft.web/sites/continuouswebjobs/delete | Web アプリの継続的な Web ジョブを削除します。 |
> | Action | microsoft.web/sites/continuouswebjobs/read | Web アプリの継続的な Web ジョブを取得します。 |
> | Action | microsoft.web/sites/continuouswebjobs/start/action | Web アプリの継続的な Web ジョブを開始します。 |
> | Action | microsoft.web/sites/continuouswebjobs/stop/action | Web アプリの継続的な Web ジョブを停止します。 |
> | Action | Microsoft.Web/sites/Delete | 既存の Web アプリの削除 |
> | Action | microsoft.web/sites/deployments/delete | Web アプリのデプロイを削除します。 |
> | Action | microsoft.web/sites/deployments/log/read | Web アプリのデプロイのログを取得します。 |
> | Action | microsoft.web/sites/deployments/read | Web アプリのデプロイを取得します。 |
> | Action | microsoft.web/sites/deployments/write | Web アプリのデプロイを更新します。 |
> | Action | microsoft.web/sites/detectors/read | Web アプリの検出機能を取得します。 |
> | Action | microsoft.web/sites/diagnostics/analyses/execute/Action | Web アプリ診断の分析を実行します。 |
> | Action | microsoft.web/sites/diagnostics/analyses/read | Web アプリ診断の分析を取得します。 |
> | Action | microsoft.web/sites/diagnostics/aspnetcore/read | ASP.NET Core アプリの Web アプリ診断を取得します。 |
> | Action | microsoft.web/sites/diagnostics/autoheal/read | Web アプリ診断の Autoheal を取得します。 |
> | Action | microsoft.web/sites/diagnostics/deployment/read | Web アプリ診断のデプロイを取得します。 |
> | Action | microsoft.web/sites/diagnostics/deployments/read | Web アプリ診断のデプロイを取得します。 |
> | Action | microsoft.web/sites/diagnostics/detectors/execute/Action | Web アプリ診断の検出機能を実行します。 |
> | Action | microsoft.web/sites/diagnostics/detectors/read | Web アプリ診断の検出機能を取得します。 |
> | Action | microsoft.web/sites/diagnostics/failedrequestsperuri/read | URI ごとに Web アプリ診断の失敗した要求を取得します。 |
> | Action | microsoft.web/sites/diagnostics/frebanalysis/read | Web アプリの診断の FREB 分析を取得します。 |
> | Action | microsoft.web/sites/diagnostics/loganalyzer/read | Web アプリ診断ログ アナライザーを取得します。 |
> | Action | microsoft.web/sites/diagnostics/read | Web アプリ診断のカテゴリを取得します。 |
> | Action | microsoft.web/sites/diagnostics/runtimeavailability/read | Web アプリの診断の実行時の可用性を取得します。 |
> | Action | microsoft.web/sites/diagnostics/servicehealth/read | Web アプリの診断のサービス正常性を取得します。 |
> | Action | microsoft.web/sites/diagnostics/sitecpuanalysis/read | Web アプリ診断のサイト CPU 分析を取得します。 |
> | Action | microsoft.web/sites/diagnostics/sitecrashes/read | Web アプリ診断のサイト クラッシュを取得します。 |
> | Action | microsoft.web/sites/diagnostics/sitelatency/read | Web アプリ診断のサイト待機時間を取得します。 |
> | Action | microsoft.web/sites/diagnostics/sitememoryanalysis/read | Web アプリ診断のサイト メモリ分析を取得します。 |
> | Action | microsoft.web/sites/diagnostics/siterestartsettingupdate/read | Web アプリ診断のサイト再起動設定更新を取得します。 |
> | Action | microsoft.web/sites/diagnostics/siterestartuserinitiated/read | Web アプリ診断のサイト再起動ユーザー開始を取得します。 |
> | Action | microsoft.web/sites/diagnostics/siteswap/read | Web アプリ診断のサイト スワップを取得します。 |
> | Action | microsoft.web/sites/diagnostics/threadcount/read | Web アプリ診断のスレッド数を取得します。 |
> | Action | microsoft.web/sites/diagnostics/workeravailability/read | Web アプリの診断の Workeravailability を取得します。 |
> | Action | microsoft.web/sites/diagnostics/workerprocessrecycle/read | Web アプリの診断のワーカー プロセスのリサイクルを取得します。 |
> | Action | microsoft.web/sites/domainownershipidentifiers/read | Web アプリのドメイン所有権識別子を取得します。 |
> | Action | microsoft.web/sites/domainownershipidentifiers/write | Web アプリのドメイン所有権識別子を更新します。 |
> | Action | microsoft.web/sites/functions/action | Web アプリの関数。 |
> | Action | microsoft.web/sites/functions/delete | Web アプリの関数を削除します。 |
> | Action | microsoft.web/sites/functions/listsecrets/action | Web アプリの関数のシークレットを一覧表示します。 |
> | Action | microsoft.web/sites/functions/masterkey/read | Web アプリの関数のマスター キーを取得します。 |
> | Action | microsoft.web/sites/functions/read | Web アプリの関数を取得します。 |
> | Action | microsoft.web/sites/functions/token/read | Web アプリの関数のトークンを取得します。 |
> | Action | microsoft.web/sites/functions/write | Web アプリの関数を更新します。 |
> | Action | microsoft.web/sites/hostnamebindings/delete | Web アプリのホスト名バインドを削除します。 |
> | Action | microsoft.web/sites/hostnamebindings/read | Web アプリのホスト名バインドを取得します。 |
> | Action | microsoft.web/sites/hostnamebindings/write | Web アプリのホスト名バインドを更新します。 |
> | Action | microsoft.web/sites/hybridconnection/delete | Web アプリのハイブリッド接続を削除します。 |
> | Action | microsoft.web/sites/hybridconnection/read | Web アプリのハイブリッド接続を取得します。 |
> | Action | microsoft.web/sites/hybridconnection/write | Web アプリのハイブリッド接続を更新します。 |
> | Action | microsoft.web/sites/hybridconnectionnamespaces/relays/delete | Web アプリのハイブリッド接続名前空間リレーを削除します。 |
> | Action | microsoft.web/sites/hybridconnectionnamespaces/relays/listkeys/action | Web アプリのハイブリッド接続名前空間リレーのキーを一覧表示します。 |
> | Action | microsoft.web/sites/hybridconnectionnamespaces/relays/read | Web アプリのハイブリッド接続名前空間リレーを取得します。 |
> | Action | microsoft.web/sites/hybridconnectionnamespaces/relays/write | Web アプリのハイブリッド接続名前空間リレーを更新します。 |
> | Action | microsoft.web/sites/hybridconnectionrelays/read | Web アプリのハイブリッド接続リレーを取得します。 |
> | Action | microsoft.web/sites/instances/deployments/delete | Web アプリのインスタンスデプロイを削除します。 |
> | Action | microsoft.web/sites/instances/deployments/read | Web アプリのインスタンスのデプロイを取得します。 |
> | Action | microsoft.web/sites/instances/extensions/log/read | Web アプリのインスタンス拡張機能ログを取得します。 |
> | Action | microsoft.web/sites/instances/extensions/read | Web アプリのインスタンス拡張機能を取得します。 |
> | Action | microsoft.web/sites/instances/processes/delete | Web アプリのインスタンスのプロセスを削除します。 |
> | Action | microsoft.web/sites/instances/processes/read | Web アプリのインスタンスのプロセスを取得します。 |
> | Action | microsoft.web/sites/instances/read | Web アプリのインスタンスを取得します。 |
> | Action | microsoft.web/sites/listsyncfunctiontriggerstatus/action | Web アプリの同期関数のトリガーの状態を一覧表示します。 |
> | Action | microsoft.web/sites/metricdefinitions/read | Web アプリのメトリック定義を取得します。 |
> | Action | microsoft.web/sites/metrics/read | Web アプリのメトリックを取得します。 |
> | Action | microsoft.web/sites/metricsdefinitions/read | Web アプリのメトリック定義を取得します。 |
> | Action | microsoft.web/sites/migratemysql/action | MySql の Web アプリを移行します。 |
> | Action | microsoft.web/sites/migratemysql/read | Web アプリ移行 MySql を取得します。 |
> | Action | microsoft.web/sites/networktrace/action | Web アプリのネットワーク トレース。 |
> | Action | microsoft.web/sites/newpassword/action | Web アプリの Newpassword。 |
> | Action | microsoft.web/sites/operationresults/read | Web アプリの操作の結果を取得します。 |
> | Action | microsoft.web/sites/operations/read | Web アプリの操作を取得します。 |
> | Action | microsoft.web/sites/perfcounters/read | Web アプリのパフォーマンス カウンターを取得します。 |
> | Action | microsoft.web/sites/premieraddons/delete | Web アプリのプレミア アドオンを削除します。 |
> | Action | microsoft.web/sites/premieraddons/read | Web アプリのプレミア アドオンを取得します。 |
> | Action | microsoft.web/sites/premieraddons/write | Web アプリのプレミア アドオンを更新します。 |
> | Action | microsoft.web/sites/processes/read | Web アプリのプロセスを取得します。 |
> | Action | microsoft.web/sites/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Action | microsoft.web/sites/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Action | microsoft.web/sites/providers/Microsoft.Insights/logDefinitions/read | Web アプリの利用可能なログを取得します。 |
> | Action | Microsoft.Web/sites/providers/Microsoft.Insights/metricDefinitions/Read | Web アプリで使用可能なメトリックを取得します。 |
> | Action | microsoft.web/sites/publiccertificates/delete | Web アプリのパブリック証明書を削除します。 |
> | Action | microsoft.web/sites/publiccertificates/read | Web アプリのパブリック証明書を取得します。 |
> | Action | microsoft.web/sites/publiccertificates/write | Web アプリのパブリック証明書を更新します。 |
> | Action | Microsoft.Web/sites/publish/Action | Web アプリを発行します。 |
> | Action | Microsoft.Web/sites/publishxml/Action | Web アプリの発行プロファイル XML を取得します。 |
> | Action | microsoft.web/sites/publishxml/read | Web アプリの発行 XML を取得します。 |
> | Action | Microsoft.Web/sites/Read | Web アプリのプロパティを取得します。 |
> | Action | microsoft.web/sites/recommendationhistory/read | Web アプリの推奨事項の履歴を取得します。 |
> | Action | microsoft.web/sites/recommendations/disable/action | Web アプリの推奨事項を無効にします。 |
> | Action | Microsoft.Web/sites/recommendations/Read | Web アプリの推奨事項の一覧を取得します。 |
> | Action | microsoft.web/sites/recover/action | Web アプリを復旧します。 |
> | Action | Microsoft.Web/sites/resetSlotConfig/Action | Web アプリの構成をリセットします。 |
> | Action | microsoft.web/sites/resourcehealthmetadata/read | Web アプリの Resource Health のメタデータを取得します。 |
> | Action | Microsoft.Web/sites/restart/Action | Web アプリを再起動します。 |
> | Action | microsoft.web/sites/restore/read | Web アプリの復元を取得します。 |
> | Action | microsoft.web/sites/restore/write | Web アプリを復元します。 |
> | Action | microsoft.web/sites/restorefromdeletedwebapp/action | 削除されたアプリから Web アプリを復元します。 |
> | Action | microsoft.web/sites/restoresnapshot/action | Web アプリのスナップショットを復元します。 |
> | Action | microsoft.web/sites/siteextensions/delete | Web アプリのサイト拡張機能を削除します。 |
> | Action | microsoft.web/sites/siteextensions/read | Web アプリのサイト拡張機能を取得します。 |
> | Action | microsoft.web/sites/siteextensions/write | Web アプリのサイト拡張機能を更新します。 |
> | Action | microsoft.web/sites/slots/analyzecustomhostname/read | Web アプリとスロットのカスタム ホスト名の分析を取得します。 |
> | Action | Microsoft.Web/sites/slots/applySlotConfig/Action | ターゲット スロットの Web アプリ スロット構成を現在のスロットに適用します。 |
> | Action | Microsoft.Web/sites/slots/backup/Action | Web アプリ スロットの新しいバックアップを作成します。 |
> | Action | microsoft.web/sites/slots/backup/read | Web アプリのスロット バックアップを取得します。 |
> | Action | microsoft.web/sites/slots/backup/write | Web アプリとスロットのバックアップを更新します。 |
> | Action | microsoft.web/sites/slots/backups/delete | Web アプリのスロット バックアップを削除します。 |
> | Action | microsoft.web/sites/slots/backups/list/action | Web アプリとスロットのバックアップを一覧表示します。 |
> | Action | Microsoft.Web/sites/slots/backups/Read | Web アプリ スロットのバックアップのプロパティを取得します。 |
> | Action | microsoft.web/sites/slots/backups/restore/action | Web アプリとスロットのバックアップを復元します。 |
> | Action | microsoft.web/sites/slots/config/delete | Web アプリとスロットの構成を削除します。 |
> | Action | Microsoft.Web/sites/slots/config/list/Action | Web アプリ スロットのセキュリティに関する設定 (発行資格情報、アプリ設定、接続文字列など) を一覧表示します。 |
> | Action | Microsoft.Web/sites/slots/config/Read | Web アプリ スロットの構成設定を取得します。 |
> | Action | Microsoft.Web/sites/slots/config/Write | Web アプリ スロットの構成設定を更新します。 |
> | Action | microsoft.web/sites/slots/continuouswebjobs/delete | Web アプリとスロットの継続的な Web ジョブを削除します。 |
> | Action | microsoft.web/sites/slots/continuouswebjobs/read | Web アプリとスロットの継続的な Web ジョブを取得します。 |
> | Action | microsoft.web/sites/slots/continuouswebjobs/start/action | Web アプリとスロットの継続的な Web ジョブを開始します。 |
> | Action | microsoft.web/sites/slots/continuouswebjobs/stop/action | Web アプリとスロットの継続的な Web ジョブを停止します。 |
> | Action | Microsoft.Web/sites/slots/Delete | 既存の Web アプリ スロットを削除します。 |
> | Action | microsoft.web/sites/slots/deployments/delete | Web アプリとスロットのデプロイを削除します。 |
> | Action | microsoft.web/sites/slots/deployments/log/read | Web アプリとスロットのデプロイのログを取得します。 |
> | Action | microsoft.web/sites/slots/deployments/read | Web アプリとスロットのデプロイを取得します。 |
> | Action | microsoft.web/sites/slots/deployments/write | Web アプリとスロットのデプロイを更新します。 |
> | Action | microsoft.web/sites/slots/diagnostics/analyses/execute/Action | Web アプリ スロット診断の分析を実行します。 |
> | Action | microsoft.web/sites/slots/diagnostics/analyses/read | Web アプリ スロット診断の分析を取得します。 |
> | Action | microsoft.web/sites/slots/diagnostics/aspnetcore/read | ASP.NET Core アプリの Web アプリ スロット診断を取得します。 |
> | Action | microsoft.web/sites/slots/diagnostics/autoheal/read | Web アプリ スロット診断の Autoheal を取得します。 |
> | Action | microsoft.web/sites/slots/diagnostics/deployment/read | Web アプリ スロット診断のデプロイを取得します。 |
> | Action | microsoft.web/sites/slots/diagnostics/deployments/read | Web アプリ スロット診断のデプロイを取得します。 |
> | Action | microsoft.web/sites/slots/diagnostics/detectors/execute/Action | Web アプリ スロット診断の検出機能を実行します。 |
> | Action | microsoft.web/sites/slots/diagnostics/detectors/read | Web アプリ スロット診断の検出機能を取得します。 |
> | Action | microsoft.web/sites/slots/diagnostics/frebanalysis/read | Web アプリ スロット診断の FREB 分析を取得します。 |
> | Action | microsoft.web/sites/slots/diagnostics/loganalyzer/read | Web アプリ スロット診断ログ アナライザーを取得します。 |
> | Action | microsoft.web/sites/slots/diagnostics/read | Web アプリ スロット診断を取得します。 |
> | Action | microsoft.web/sites/slots/diagnostics/runtimeavailability/read | Web アプリ スロット診断の実行時の可用性を取得します。 |
> | Action | microsoft.web/sites/slots/diagnostics/servicehealth/read | Web アプリ スロット診断の Service Health を取得します。 |
> | Action | microsoft.web/sites/slots/diagnostics/sitecpuanalysis/read | Web アプリ スロット診断のサイト CPU 分析を取得します。 |
> | Action | microsoft.web/sites/slots/diagnostics/sitecrashes/read | Web アプリ スロット診断のサイト クラッシュを取得します。 |
> | Action | microsoft.web/sites/slots/diagnostics/sitelatency/read | Web アプリ スロット診断のサイト待機時間を取得します。 |
> | Action | microsoft.web/sites/slots/diagnostics/sitememoryanalysis/read | Web アプリ スロット診断のサイト メモリ分析を取得します。 |
> | Action | microsoft.web/sites/slots/diagnostics/siterestartsettingupdate/read | Web アプリ スロット診断のサイト再起動設定更新を取得します。 |
> | Action | microsoft.web/sites/slots/diagnostics/siterestartuserinitiated/read | Web アプリ スロット診断のサイト再起動ユーザー開始を取得します。 |
> | Action | microsoft.web/sites/slots/diagnostics/siteswap/read | Web アプリ スロット診断のサイト スワップを取得します。 |
> | Action | microsoft.web/sites/slots/diagnostics/threadcount/read | Web アプリ スロット診断のスレッド数を取得します。 |
> | Action | microsoft.web/sites/slots/diagnostics/workeravailability/read | Web アプリ スロット診断の Workeravailability を取得します。 |
> | Action | microsoft.web/sites/slots/diagnostics/workerprocessrecycle/read | Web アプリ スロット診断のワーカー プロセス リサイクルを取得します。 |
> | Action | microsoft.web/sites/slots/domainownershipidentifiers/read | Web アプリ スロットのドメイン所有権識別子を取得します。 |
> | Action | microsoft.web/sites/slots/hostnamebindings/delete | Web アプリとスロットのホスト名バインドを削除します。 |
> | Action | microsoft.web/sites/slots/hostnamebindings/read | Web アプリとスロットのホスト名バインドを取得します。 |
> | Action | microsoft.web/sites/slots/hostnamebindings/write | Web アプリとスロットのホスト名バインドを更新します。 |
> | Action | microsoft.web/sites/slots/hybridconnection/delete | Web アプリとスロットのハイブリッド接続を削除します。 |
> | Action | microsoft.web/sites/slots/hybridconnection/read | Web アプリとスロットのハイブリッド接続を取得します。 |
> | Action | microsoft.web/sites/slots/hybridconnection/write | Web アプリとスロットのハイブリッド接続を更新します。 |
> | Action | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/delete | Web アプリ スロットのハイブリッド接続名前空間リレーを削除します。 |
> | Action | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/write | Web アプリ スロットのハイブリッド接続名前空間リレーを更新します。 |
> | Action | microsoft.web/sites/slots/hybridconnectionrelays/read | Web アプリ スロットのハイブリッド接続リレーを取得します。 |
> | Action | microsoft.web/sites/slots/instances/deployments/read | Web アプリとスロットのインスタンスのデプロイを取得します。 |
> | Action | microsoft.web/sites/slots/instances/processes/delete | Web アプリ スロットのインスタンス プロセスを削除します。 |
> | Action | microsoft.web/sites/slots/instances/processes/read | Web アプリとスロットのインスタンスのプロセスを取得します。 |
> | Action | microsoft.web/sites/slots/instances/read | Web アプリとスロットのインスタンスを取得します。 |
> | Action | microsoft.web/sites/slots/metricdefinitions/read | Web アプリとスロットのメトリック定義を取得します。 |
> | Action | microsoft.web/sites/slots/metrics/read | Web アプリとスロットのメトリックを取得します。 |
> | Action | microsoft.web/sites/slots/migratemysql/read | Web アプリ スロットの移行 MySql を取得します。 |
> | Action | microsoft.web/sites/slots/networktrace/action | Web アプリ スロットのネットワーク トレースです。 |
> | Action | microsoft.web/sites/slots/newpassword/action | Web アプリとスロットの Newpassword。 |
> | Action | microsoft.web/sites/slots/operationresults/read | Web アプリとスロットの操作の結果を取得します。 |
> | Action | microsoft.web/sites/slots/operations/read | Web アプリ スロットの操作を取得します。 |
> | Action | microsoft.web/sites/slots/perfcounters/read | Web アプリ スロットのパフォーマンス カウンターを取得します。 |
> | Action | microsoft.web/sites/slots/phplogging/read | Web アプリとスロットの Phplogging を取得します。 |
> | Action | microsoft.web/sites/slots/premieraddons/delete | Web アプリとスロットのプレミア アドオンを削除します。 |
> | Action | microsoft.web/sites/slots/premieraddons/read | Web アプリとスロットのプレミア アドオンを取得します。 |
> | Action | microsoft.web/sites/slots/premieraddons/write | Web アプリとスロットのプレミア アドオンを更新します。 |
> | Action | microsoft.web/sites/slots/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Action | microsoft.web/sites/slots/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Action | microsoft.web/sites/slots/providers/Microsoft.Insights/logDefinitions/read | Web アプリ スロットの利用可能なログを取得します。 |
> | Action | Microsoft.Web/sites/slots/providers/Microsoft.Insights/metricDefinitions/Read | Web アプリ スロットで使用可能なメトリックを取得します。 |
> | Action | microsoft.web/sites/slots/publiccertificates/delete | Web アプリ スロットのパブリック証明書を削除します。 |
> | Action | microsoft.web/sites/slots/publiccertificates/read | Web アプリ スロットのパブリック証明書を取得します。 |
> | Action | microsoft.web/sites/slots/publiccertificates/write | Web アプリ スロットのパブリック証明書を作成または更新します。 |
> | Action | Microsoft.Web/sites/slots/publish/Action | Web アプリ スロットを発行します。 |
> | Action | Microsoft.Web/sites/slots/publishxml/Action | Web アプリ スロットの発行プロファイル XML を取得します。 |
> | Action | Microsoft.Web/sites/slots/Read | Web アプリのデプロイ スロットのプロパティを取得します。 |
> | Action | microsoft.web/sites/slots/recover/action | Web アプリ スロットを復旧します。 |
> | Action | Microsoft.Web/sites/slots/resetSlotConfig/Action | Web アプリ スロット構成をリセットします。 |
> | Action | microsoft.web/sites/slots/resourcehealthmetadata/read | Web アプリ スロットの Resource Health のメタデータを取得します。 |
> | Action | Microsoft.Web/sites/slots/restart/Action | Web アプリ スロットを再起動します。 |
> | Action | microsoft.web/sites/slots/restore/read | Web アプリとスロットの復元を取得します。 |
> | Action | microsoft.web/sites/slots/restore/write | Web アプリ スロットを復元します。 |
> | Action | microsoft.web/sites/slots/restorefromdeletedwebapp/action | 削除されたアプリから Web アプリ スロットを復元します。 |
> | Action | microsoft.web/sites/slots/restoresnapshot/action | Web アプリ スロットのスナップショットを復元します。 |
> | Action | microsoft.web/sites/slots/siteextensions/delete | Web アプリ スロットのサイト拡張機能を削除します。 |
> | Action | microsoft.web/sites/slots/siteextensions/read | Web アプリ スロットのサイト拡張機能を取得します。 |
> | Action | microsoft.web/sites/slots/siteextensions/write | Web アプリ スロットのサイト拡張機能を更新します。 |
> | Action | Microsoft.Web/sites/slots/slotsdiffs/Action | Web アプリとスロットの構成の違いを取得します。 |
> | Action | Microsoft.Web/sites/slots/slotsswap/Action | Web アプリのデプロイ スロットを入れ替えます。 |
> | Action | microsoft.web/sites/slots/snapshots/read | Web アプリ スロットのスナップショットを取得します。 |
> | Action | Microsoft.Web/sites/slots/sourcecontrols/Delete | Web アプリ スロットのソース管理の構成設定を削除します。 |
> | Action | Microsoft.Web/sites/slots/sourcecontrols/Read | Web アプリ スロットのソース管理の構成設定を取得します。 |
> | Action | Microsoft.Web/sites/slots/sourcecontrols/Write | Web アプリ スロットのソース管理の構成設定を更新します。 |
> | Action | Microsoft.Web/sites/slots/start/Action | Web アプリ スロットを起動します。 |
> | Action | Microsoft.Web/sites/slots/stop/Action | Web アプリ スロットを停止します。 |
> | Action | microsoft.web/sites/slots/sync/action | Web アプリとスロットを同期します。 |
> | Action | microsoft.web/sites/slots/triggeredwebjobs/delete | Web アプリとスロットのトリガーされた Web ジョブを削除します。 |
> | Action | microsoft.web/sites/slots/triggeredwebjobs/read | Web アプリとスロットのトリガーされた Web ジョブを取得します。 |
> | Action | microsoft.web/sites/slots/triggeredwebjobs/run/action | Web アプリとスロットのトリガーされた Web ジョブを実行します。 |
> | Action | microsoft.web/sites/slots/usages/read | Web アプリとスロットの使用状況を取得します。 |
> | Action | microsoft.web/sites/slots/virtualnetworkconnections/delete | Web アプリとスロットの仮想ネットワーク接続を削除します。 |
> | Action | microsoft.web/sites/slots/virtualnetworkconnections/gateways/write | Web アプリとスロットの仮想ネットワーク接続のゲートウェイを更新します。 |
> | Action | microsoft.web/sites/slots/virtualnetworkconnections/read | Web アプリとスロットの仮想ネットワーク接続を取得します。 |
> | Action | microsoft.web/sites/slots/virtualnetworkconnections/write | Web アプリとスロットの仮想ネットワーク接続を更新します。 |
> | Action | microsoft.web/sites/slots/webjobs/read | Web アプリとスロットの Web ジョブを取得します。 |
> | Action | Microsoft.Web/sites/slots/Write | 新しい Web アプリ スロットを作成するか、既存の Web アプリ スロットを更新します。 |
> | Action | Microsoft.Web/sites/slotsdiffs/Action | Web アプリとスロットの構成の違いを取得します。 |
> | Action | Microsoft.Web/sites/slotsswap/Action | Web アプリのデプロイ スロットを入れ替えます。 |
> | Action | microsoft.web/sites/snapshots/read | Web アプリのスナップショットを取得します。 |
> | Action | Microsoft.Web/sites/sourcecontrols/Delete | Web アプリのソース管理の構成設定を削除します。 |
> | Action | Microsoft.Web/sites/sourcecontrols/Read | Web アプリのソース管理の構成設定を取得します。 |
> | Action | Microsoft.Web/sites/sourcecontrols/Write | Web アプリのソース管理の構成設定を更新します。 |
> | Action | Microsoft.Web/sites/start/Action | Web アプリを起動します。 |
> | Action | Microsoft.Web/sites/stop/Action | Web アプリを停止します。 |
> | Action | microsoft.web/sites/sync/action | Web アプリを同期します。 |
> | Action | microsoft.web/sites/syncfunctiontriggers/action | Web アプリの関数トリガーを同期します。 |
> | Action | microsoft.web/sites/triggeredwebjobs/delete | Web アプリのトリガーされた Web ジョブを削除します。 |
> | Action | microsoft.web/sites/triggeredwebjobs/history/read | Web アプリのトリガーされた Web ジョブの履歴を取得します。 |
> | Action | microsoft.web/sites/triggeredwebjobs/read | Web アプリのトリガーされた Web ジョブを取得します。 |
> | Action | microsoft.web/sites/triggeredwebjobs/run/action | Web アプリのトリガーされた Web ジョブを実行します。 |
> | Action | microsoft.web/sites/usages/read | Web アプリの使用状況を取得します。 |
> | Action | microsoft.web/sites/virtualnetworkconnections/delete | Web アプリの仮想ネットワーク接続を削除します。 |
> | Action | microsoft.web/sites/virtualnetworkconnections/gateways/read | Web アプリの仮想ネットワーク接続のゲートウェイを取得します。 |
> | Action | microsoft.web/sites/virtualnetworkconnections/gateways/write | Web アプリの仮想ネットワーク接続のゲートウェイを更新します。 |
> | Action | microsoft.web/sites/virtualnetworkconnections/read | Web アプリの仮想ネットワーク接続を取得します。 |
> | Action | microsoft.web/sites/virtualnetworkconnections/write | Web アプリの仮想ネットワーク接続を更新します。 |
> | Action | microsoft.web/sites/webjobs/read | Web アプリの Web ジョブを取得します。 |
> | Action | Microsoft.Web/sites/Write | 新しい Web アプリを作成するか、既存の Web アプリを更新します。 |
> | Action | microsoft.web/skus/read | SKU を取得します。 |
> | Action | microsoft.web/sourcecontrols/read | ソース管理を取得します。 |
> | Action | microsoft.web/sourcecontrols/write | ソース管理を更新します。 |
> | Action | microsoft.web/unregister/action | サブスクリプションへの Microsoft.Web リソース プロバイダーの登録を解除します。 |
> | Action | microsoft.web/validate/action | 検証します。 |
> | Action | microsoft.web/verifyhostingenvironmentvnet/action | ホスティング環境の Vnet を検証します。 |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tdCol2BreakAll"]
> | アクションの種類 | Operation | 説明 |
> | --- | --- | --- |
> | Action | Microsoft.WorkloadMonitor/components/read | 操作のリソースを読み取ります。 |
> | Action | Microsoft.WorkloadMonitor/healthInstances/read | 操作のリソースを読み取ります。 |
> | Action | Microsoft.WorkloadMonitor/Operations/read | 操作のリソースを読み取ります。 |
> | Action | Microsoft.WorkloadMonitor/workloadInsights/delete | workloadInsights リソースを削除します |
> | Action | Microsoft.WorkloadMonitor/workloadInsights/delete | workloadInsights リソースを削除します |
> | Action | Microsoft.WorkloadMonitor/workloadInsights/read | workloadInsights リソースを読み取ります |
> | Action | Microsoft.WorkloadMonitor/workloadInsights/read | workloadInsights リソースを読み取ります |
> | Action | Microsoft.WorkloadMonitor/workloadInsights/write | workloadInsights リソースを書き込みます |
> | Action | Microsoft.WorkloadMonitor/workloadInsights/write | workloadInsights リソースを書き込みます |
> | Action | Microsoft.WorkloadMonitor/workloads/delete |  |
> | Action | Microsoft.WorkloadMonitor/workloads/delete |  |
> | Action | Microsoft.WorkloadMonitor/workloads/read | ワークロードのリソースを読み取ります。 |
> | Action | Microsoft.WorkloadMonitor/workloads/read | ワークロードのリソースを読み取ります。 |
> | Action | Microsoft.WorkloadMonitor/workloads/write | ワークロードのリソースを書き込みます。 |
> | Action | Microsoft.WorkloadMonitor/workloads/write | ワークロードのリソースを書き込みます。 |

## <a name="next-steps"></a>次の手順

- [カスタム ロール](custom-roles.md)
- [組み込みのロール](built-in-roles.md)
