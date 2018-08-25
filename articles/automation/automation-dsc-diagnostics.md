---
title: Log Analytics への Azure Automation State Configuration レポート データの転送
description: この記事では、Desired State Configuration (DSC) レポート データを Azure Automation State Configuration から Log Analytics に送信して、詳しい情報を入手し、きめ細かい管理を実現する方法について説明します。
services: automation
ms.service: automation
ms.component: dsc
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1b3c0cd71508aef9a608e0c41e32cd079e40d4e5
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2018
ms.locfileid: "40003468"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-log-analytics"></a>Log Analytics への Azure Automation State Configuration レポート データの転送

Azure Automation State Configuration では、Desired State Configuration (DSC) ノードの状態データを Log Analytics ワークスペースに送信できます。 Azure Portal または PowerShell を使用して、ノード構成内のノードや個々の DSC リソースのコンプライアンス ステータスを確認できます。 Log Analytics では、次のことが可能です。

- 管理対象ノードや個々のリソースのコンプライアンスに関する情報を取得する
- コンプライアンス ステータスに基づいた電子メールまたは警告をトリガーする
- 管理対象ノードをまたぐ高度なクエリを記述する
- Automation アカウント間でのコンプライアンス ステータスを関連付ける
- ノードの経時的なコンプライアンス履歴を視覚化する

## <a name="prerequisites"></a>前提条件

Log Analytics への Automation State Configuration レポートの送信を開始するには、次のものが必要です。

- 2016 年 11 月以降のリリースの [Azure PowerShell](/powershell/azure/overview) (v2.3.0)。
- Azure Automation アカウント。 詳しくは、「[Azure Automation の概要](automation-offering-get-started.md)」をご覧ください。
- 
  **Automation &amp; Control** サービス プラン付きの Log Analytics ワークスペース。 詳細については、「[Log Analytics の起動と開始](../log-analytics/log-analytics-get-started.md)」を参照してください。
- 1 つ以上の Azure Automation State Configuration ノード。 詳細については、「[Azure Automation State Configuration による管理のためのマシンのオンボード](automation-dsc-onboarding.md)」をご覧ください。

## <a name="set-up-integration-with-log-analytics"></a>Log Analytics との統合のセットアップ

Azure Automation DSC から Log Analytics へのデータのインポートを開始するには、次の手順を実行します。

1. PowerShell で Azure アカウントにログインします。 「[Azure PowerShell でのログイン](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-4.0.0)」をご覧ください。
1. 次の PowerShell コマンドを実行して、Automation アカウントの _ResourceId_ を取得します (Automation アカウントが 1 つ以上ある場合は、構成するアカウントの _ResourceID_ を選択します)。

  ```powershell
  # Find the ResourceId for the Automation Account
  Find-AzureRmResource -ResourceType 'Microsoft.Automation/automationAccounts'
  ```

1. 次の PowerShell コマンドを実行して、Log Analytics ワークスペースの _ResourceId_ を取得します (ワークスペースが 1 つ以上ある場合は、構成するワークスペースの _ResourceID_ を選択します)。

  ```powershell
  # Find the ResourceId for the Log Analytics workspace
  Find-AzureRmResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
  ```

1. 次の PowerShell コマンドを実行します。`<AutomationResourceId>` と `<WorkspaceResourceId>` はそれぞれ前の手順で取得した _ResourceId_ の値と置き換えます。

  ```powershell
  Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Categories 'DscNodeStatus'
  ```

Azure Automation State Configuration から Log Analytics へのデータのインポートを停止するには、次の PowerShell コマンドを実行します。

```powershell
Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Categories 'DscNodeStatus'
```

## <a name="view-the-state-configuration-logs"></a>State Configuration ログを表示する

Automation State Configuration データ用に Log Analytics との統合をセットアップすると、Automation アカウントの **[DSC ノード]** ブレードに **[ログ検索]** ボタンが表示されます。 **[ログ検索]** ボタンをクリックすると、DSC ノード データのログが表示されます。

![[ログ検索] ボタン](media/automation-dsc-diagnostics/log-search-button.png)

**[ログ検索]** ブレードには、各 State Configuration ノードの **DscNodeStatusData** 操作、およびそのノードに適用されたノード構成で呼び出された各 [DSC リソース](/powershell/dsc/resources)の **DscResourceStatusData** 操作が表示されます。

**DscResourceStatusData** 操作には、失敗したすべての DSC リソースのエラー情報が含まれます。

操作のデータを確認するには、一覧からその操作をクリックします。

また、ログは Log Analytics で検索することで確認できます。 「[ログ検索を使用してデータを探す](../log-analytics/log-analytics-log-searches.md)」をご覧ください。
State Configuration のログを検索するには、次のクエリを入力します。`Type=AzureDiagnostics ResourceProvider='MICROSOFT.AUTOMATION' Category='DscNodeStatus'`

操作名でクエリの検索結果を絞り込むこともできます。 次に例を示します。`Type=AzureDiagnostics ResourceProvider='MICROSOFT.AUTOMATION' Category='DscNodeStatus' OperationName='DscNodeStatusData'`

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>State Configuration のコンプライアンス チェックでエラーになったときに電子メールを送信する

お客様からの要望として多いのは、DSC 構成に問題が発生したときに電子メールまたはテキストを送信する機能です。

アラート ルールを作成するには、まずアラートを呼び出す State Configuration レポートのレコードに対するログ検索を作成します。 **[+ New Alert Rule]\(新しいアラート ルール\)** をクリックし、アラート ルールを作成して構成します。

1. Log Analytics の [Overview (概要)] ページで、**[Log Search (ログ検索)]** をクリックします。
1. クエリ フィールドに次のように入力して、アラートに対するログ検索クエリを作成します。`Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   複数の Automation アカウントまたはサブスクリプションからワークスペースへのログをセットアップしてある場合は、サブスクリプションおよび Automation アカウントごとにアラートをグループ化することができます。  
   Automation アカウント名は DscNodeStatusData の検索のリソース フィールドから派生していることもあります。  
1. **[ルールの作成]** 画面を開くには、ページの上部にある **[+ New Alert Rule]\(新しいアラート ルール\)** をクリックします。 アラートの構成オプションについて詳しくは、[アラート ルールの作成](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)に関する記事をご覧ください。

### <a name="find-failed-dsc-resources-across-all-nodes"></a>すべてのノードで失敗した DSC リソースを検索する

Log Analytics を使用する利点の 1 つは、ノード間で失敗したチェックを検索できることです。
失敗した DSC リソースのすべてのインスタンスを見つけるには、次のようにします。

1. Log Analytics の [Overview (概要)] ページで、**[Log Search (ログ検索)]** をクリックします。
1. クエリ フィールドに次のように入力して、アラートに対するログ検索クエリを作成します。`Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>DSC ノードの状態の履歴を表示する

最後に、DSC ノードの状態の履歴を時系列で視覚化できます。  
次のクエリを使うと、DSC ノードの状態を時系列で検索できます。

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`  

これにより、ノードの状態が時系列でグラフに表示されます。

## <a name="log-analytics-records"></a>Log Analytics のレコード

Azure Automation の診断から、Log Analytics に 2 つのカテゴリーのレコードが作成されます。

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| プロパティ | [説明] |
| --- | --- |
| TimeGenerated |コンプライアンス チェックが実行された日時。 |
| OperationName |DscNodeStatusData |
| ResultType |ノードが準拠しているかどうか。 |
| NodeName_s |管理対象ノードの名前。 |
| NodeComplianceStatus_s |ノードが準拠しているかどうか。 |
| DscReportStatus |コンプライアンス チェックが正常に実行されたかどうか。 |
| ConfigurationMode | ノードに構成が適用される方法。 指定できる値は、__"ApplyOnly"__、__"ApplyandMonitior"__、および __"ApplyandAutoCorrect"__ です。 <ul><li>__ApplyOnly__: DSC が構成を適用し、以降は新しい構成がターゲット ノードにプッシュない限り、または新しい構成がサーバーからプルされるまで何もしません。 新しい構成が最初に適用された後、DSC は以前の構成された状態からの誤差を確認しません。 DSC は __ApplyOnly__ が有効になる前に、構成の適用を成功するまで試行します。 </li><li> __ApplyAndMonitor__: これが既定値です。 LCM が任意の新しい構成を適用します。 新しい構成が最初に適用された後、ターゲット ノードが目的の状態から変わった場合、DSC はログに不一致を報告します。 DSC は __ApplyAndMonitor__ が有効になる前に、構成の適用を成功するまで試行します。</li><li>__ApplyAndAutoCorrect__: DSC が任意の新しい構成を適用します。 新しい構成が最初に適用された後、ターゲット ノードが目的の状態から変わった場合、DSC はログに不一致を報告し、現在の構成を再適用します。</li></ul> |
| HostName_s | 管理対象ノードの名前。 |
| IPAddress | 管理対象ノードの IPv4 アドレス。 |
| Category | DscNodeStatus |
| リソース | Azure Automation アカウントの名前。 |
| Tenant_g | 呼び出し元のテナントを識別する GUID です。 |
| NodeId_g |管理対象ノードを識別する GUID。 |
| DscReportId_g |レポートを識別する GUID。 |
| LastSeenTime_t |レポートが最後に閲覧された日時。 |
| ReportStartTime_t |レポートが最後に開始された日時。 |
| ReportEndTime_t |レポートが完了した日時。 |
| NumberOfResources_d |ノードに適用された構成で呼び出された DSC ソース。 |
| SourceSystem | Log Analytics がデータを収集する方法。 Azure 診断の場合、常に *Azure* です。 |
| ResourceId |Azure Automation アカウントを指定します。 |
| ResultDescription | この操作の説明。 |
| SubscriptionId | Automation アカウントの Azure サブスクリプション ID (GUID) です。 |
| ResourceGroup | Automation アカウントのリソース グループの名前です。 |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |コンプライアンス レポートの相関 ID を示す GUID。 |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| プロパティ | [説明] |
| --- | --- |
| TimeGenerated |コンプライアンス チェックが実行された日時。 |
| OperationName |DscResourceStatusData|
| ResultType |リソースが準拠しているかどうか。 |
| NodeName_s |管理対象ノードの名前。 |
| Category | DscNodeStatus |
| リソース | Azure Automation アカウントの名前。 |
| Tenant_g | 呼び出し元のテナントを識別する GUID です。 |
| NodeId_g |管理対象ノードを識別する GUID。 |
| DscReportId_g |レポートを識別する GUID。 |
| DscResourceId_s |DSC リソース インスタンスの名前。 |
| DscResourceName_s |DSC リソースの名前。 |
| DscResourceStatus_s |DSC リソースが適合しているかどうか。 |
| DscModuleName_s |DSC リソースを含む PowerShell モジュールの名前。 |
| DscModuleVersion_s |DSC リソースを含む PowerShell モジュールのバージョン。 |
| DscConfigurationName_s |ノードに適用されている構成の名前。 |
| ErrorCode_s | リソースが失敗した場合のエラー コード。 |
| ErrorMessage_s |リソースが失敗した場合のエラー メッセージ。 |
| DscResourceDuration_d |DSC リソースの実行時間 (秒)。 |
| SourceSystem | Log Analytics がデータを収集する方法。 Azure 診断の場合、常に *Azure* です。 |
| ResourceId |Azure Automation アカウントを指定します。 |
| ResultDescription | この操作の説明。 |
| SubscriptionId | Automation アカウントの Azure サブスクリプション ID (GUID) です。 |
| ResourceGroup | Automation アカウントのリソース グループの名前です。 |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |コンプライアンス レポートの相関 ID を示す GUID。 |

## <a name="summary"></a>まとめ

Automation State Configuration のデータを Log Analytics に送信し、次の対応を行うことで、Automation State Configuration のノードの状態をより理解できるようになります。

- 問題が発生した場合に通知するアラートの設定
- カスタム ビューと検索クエリを使用した Runbook の結果、Runbook ジョブの状態、その他の関連する主要な指標やメトリックの視覚化。  

Log Analytics によって、Automation State Configuration のデータの状態をさらに詳しく把握でき、インシデントにより迅速に対処できるようになります。

## <a name="next-steps"></a>次の手順

- 概要については、[Azure Automation State Configuration](automation-dsc-overview.md) に関するページをご覧ください。
- 使用を開始するには、「[Azure Automation State Configuration の使用](automation-dsc-getting-started.md)」をご覧ください。
- DSC 構成をコンパイルしてターゲット ノードに割り当てることができるようにする方法の詳細については、「[Azure Automation State Configuration での構成のコンパイル](automation-dsc-compile.md)」をご覧ください。
- PowerShell コマンドレットのリファレンスについては、[Azure Automation State Configuration のコマンドレット](/powershell/module/azurerm.automation/#automation)に関するページをご覧ください。
- 料金情報については、[Azure Automation State Configuration の価格](https://azure.microsoft.com/pricing/details/automation/)に関するページをご覧ください。
- 継続的なデプロイ パイプラインで Azure Automation State Configuration を使う例については、「[Automation State Configuration と Chocolatey を使用した仮想マシンへの継続的なデプロイ](automation-dsc-cd-chocolatey.md)」をご覧ください。
- 各種検索クエリの作成方法と、Log Analytics での Automation State Configuration ログの確認方法の詳細については、[Log Analytics でのログ検索](../log-analytics/log-analytics-log-searches.md)に関するページを参照してください。
- Log Analytics とデータ収集ソースの詳細については、「[Log Analytics における Azure Storage データの収集について](../log-analytics/log-analytics-azure-storage.md)」をご覧ください。