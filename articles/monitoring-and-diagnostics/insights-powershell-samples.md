---
title: Azure Monitor の PowerShell クイック スタート サンプル
description: PowerShell を使って、自動スケール、アラート、webhook、アクティビティ ログの検索などの Azure Monitor の機能にアクセスします。
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 2/14/2018
ms.author: robb
ms.component: ''
ms.openlocfilehash: c6189291a9e944acde751a66cdb58f2052c73999
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2018
ms.locfileid: "42140331"
---
# <a name="azure-monitor-powershell-quick-start-samples"></a>Azure Monitor の PowerShell クイック スタート サンプル
この記事では、Azure Monitor の機能にアクセスするために役立つ PowerShell のサンプル コマンドを紹介します。

> [!NOTE]
> Azure Monitor は、2016 年 9 月 25 日まで "Azure Insights" と呼ばれていたサービスの新しい名前です。 ただし、名前空間と、それに伴って次のコマンドには、引き続き "insights" という語が含まれています。
> 
> 

## <a name="set-up-powershell"></a>PowerShell のセットアップ
コンピューターで実行するために PowerShell をセットアップします (まだセットアップしていない場合)。 詳細については、[PowerShell をインストールして構成する方法](/powershell/azure/overview)に関するページを参照してください。

## <a name="examples-in-this-article"></a>この記事の例
この記事の各例は、Azure Monitor コマンドレットの使用方法を示しています。 「[Azure Monitor (Insights) Cmdlets (Azure Monitor (Insights) コマンドレット)](https://docs.microsoft.com/powershell/module/azurerm.insights)」で、Azure Monitor PowerShell コマンドレットのすべてのリストを確認することもできます。

## <a name="sign-in-and-use-subscriptions"></a>サインインとサブスクリプションの使用
まず、Azure サブスクリプションにログインします。

```PowerShell
Connect-AzureRmAccount
```

サインイン画面が表示されます。 アカウントにサインインすると、アカウント、テナント ID、既定のサブスクリプション ID が表示されます。 すべての Azure コマンドレットは、既定のサブスクリプションのコンテキストで動作します。 アクセスできるサブスクリプションのリストを表示するには、次のコマンドを使用します。

```PowerShell
Get-AzureRmSubscription
```

作業コンテキストを別のサブスクリプションに変更するには、次のコマンドを使用します。

```PowerShell
Set-AzureRmContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-activity-log-for-a-subscription"></a>サブスクリプションのアクティビティ ログの取得
`Get-AzureRmLog` コマンドレットを使用します。  一般的な例を次に示します。

指定した日時のログ エントリを取得します。

```PowerShell
Get-AzureRmLog -StartTime 2016-03-01T10:30
```

範囲内の日時のログ エントリを取得します。

```PowerShell
Get-AzureRmLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

特定のリソース グループのログ エントリを取得します。

```PowerShell
Get-AzureRmLog -ResourceGroup 'myrg1'
```

範囲内の日時の特定リソース プロバイダーのログ エントリを取得します。

```PowerShell
Get-AzureRmLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

特定の呼び出し元が含まれたすべてのログ エントリを取得します。

```PowerShell
Get-AzureRmLog -Caller 'myname@company.com'
```

次のコマンドは、アクティビティ ログから最後の 1000 件のイベントを取得します。

```PowerShell
Get-AzureRmLog -MaxEvents 1000
```

`Get-AzureRmLog` は、他にも多くのパラメーターをサポートしています。 詳細については、 `Get-AzureRmLog` のリファレンスをご覧ください。

> [!NOTE]
> `Get-AzureRmLog` は、15 日間の履歴のみを提供します。 **-MaxEvents** パラメーターを使用すると、15 日間を超えて最後の N 件のイベントを照会できます。 15 日より前のイベントにアクセスするには、REST API または SDK (SDK を使用した C# のサンプル) を使用します。 **StartTime** を指定しない場合、既定値は **EndTime** から 1 時間引いた値になります。 **EndTime**を指定しない場合、既定値は現在の時刻です。 時刻はすべて UTC 形式です。
> 
> 

## <a name="retrieve-alerts-history"></a>アラートの履歴の取得
すべてのアラート イベントを表示するには、次の例を使用して Azure Resource Manager のログを照会します。

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

特定のアラート ルールの履歴を表示するには、そのアラート ルールのリソース ID を渡して `Get-AzureRmAlertHistory` コマンドレットを使用します。

```PowerShell
Get-AzureRmAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

`Get-AzureRmAlertHistory` コマンドレットは、さまざまなパラメーターをサポートしています。 詳細については、「 [Get-AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx)」をご覧ください。

## <a name="retrieve-information-on-alert-rules"></a>アラート ルールに関する情報の取得
以下のコマンドは、いずれも "montest" という名前のリソース グループに影響を及ぼします。

アラート ルールのすべてのプロパティを表示します。

```PowerShell
Get-AzureRmAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

リソース グループに対するすべてのアラートを取得します。

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest
```

ターゲット リソースに設定されたすべてのアラート ルールを取得します。 たとえば、VM に設定されたすべてのアラート ルールを取得します。

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzureRmAlertRule` は、他のパラメーターもサポートしています。 詳細については、「 [Get-AlertRule](https://msdn.microsoft.com/library/mt282459.aspx) 」をご覧ください。

## <a name="create-metric-alerts"></a>メトリック アラートの作成
`Add-AlertRule` コマンドレットを使用して、アラート ルールを作成、更新、または無効化できます。

電子メール プロパティと webhook プロパティは、それぞれ `New-AzureRmAlertRuleEmail` と `New-AzureRmAlertRuleWebhook` を使用して作成できます。 アラート ルール コマンドレットでは、これらのプロパティをアラート ルールの **Actions** プロパティにアクションとして割り当てます。

メトリックを使用するアラートを作成する際に使用されるパラメーターと値を次の表に示します。

| パラメーター | value |
| --- | --- |
| Name |simpletestdiskwrite |
| このアラート ルールの場所 |米国東部 |
| ResourceGroup |montest |
| TargetResourceId |/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig |
| 作成されたアラートの MetricName |\PhysicalDisk(_Total)\Disk Writes/sec。正確なメトリック名を取得する方法については、`Get-MetricDefinitions` コマンドレットを参照してください。 |
| operator |GreaterThan |
| しきい値 (このメトリックの場合、数/秒) |1 |
| WindowSize (hh:mm:ss 形式) |00:05:00 |
| アグリゲーター (メトリックの統計。この例では Average を使用) |平均 |
| カスタム電子メール (文字列配列) |'foo@example.com'、'bar@example.com' |
| 所有者、共同作成者、および閲覧者への電子メールの送信 |-SendToServiceOwners |

Email アクションを作成する

```PowerShell
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Webhook アクションを作成する

```PowerShell
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

クラシック VM の CPU 使用率メトリックのアラート ルールを作成する

```PowerShell
Add-AzureRmMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

アラート ルールを取得する

```PowerShell
Get-AzureRmAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

Add アラート コマンドレットは、指定されたプロパティのアラート ルールが既に存在する場合に、そのルールの更新も実行します。 アラート ルールを無効にするには、 **-DisableRule**パラメーターを含めます。

## <a name="get-a-list-of-available-metrics-for-alerts"></a>アラートで使用可能なメトリックのリストの取得
`Get-AzureRmMetricDefinition` コマンドレットを使用して、特定のリソースのすべてのメトリックのリストを表示できます。

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id>
```

次の例では、メトリックの名前 (Name) と単位 (Unit) を含むテーブルを生成します。

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

`Get-AzureRmMetricDefinition` で使用できるオプションの詳細な一覧については、「 [Get-MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx)」を参照してください。

## <a name="create-and-manage-activity-log-alerts"></a>アクティビティ ログ アラートの作成と管理
アクティビティ ログ アラートは、`Set-AzureRmActivityLogAlert` コマンドレットを使用して設定できます。 アクティビティ ログ アラートは、最初に条件のディクショナリとして条件を定義してから、それらの条件を使用するアラートを作成する必要があります。

```PowerShell

$condition1 = New-AzureRmActivityLogAlertCondition -Field 'category' -Equals 'Administrative'
$condition2 = New-AzureRmActivityLogAlertCondition -Field 'operationName' -Equals 'Microsoft.Compute/virtualMachines/write'
$additionalWebhookProperties = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
$additionalWebhookProperties.Add('customProperty', 'someValue')
$actionGrp1 = New-AzureRmActionGroup -ActionGroupId 'actiongr1' -WebhookProperties $dict
Set-AzureRmActivityLogAlert -Location 'Global' -Name 'alert on VM create' -ResourceGroupName 'myResourceGroup' -Scope '/' -Action $actionGrp1 -Condition $condition1, $condition2

```

その他の webhook プロパティは省略可能です。 `Get-AzureRmActivityLogAlert` を使用して、アクティビティ ログ アラートの内容を戻すことができます。

## <a name="create-and-manage-autoscale-settings"></a>自動スケール設定の作成と管理
リソース (Web アプリ、VM、Cloud Services、または仮想マシン スケール セット) は、そのリソース用に構成された自動スケール設定を 1 つだけ使用できます。
ただし、各自動スケール設定では複数のプロファイルを使用できます。 たとえば、パフォーマンス ベースのスケール プロファイルを使用し、2 つ目のプロファイルとしてスケジュール ベースのプロファイルを使用できます。 各プロファイルには、複数のルールを構成できます。 自動スケールの詳細については、 [アプリケーションの自動スケールの方法](../cloud-services/cloud-services-how-to-scale-portal.md)に関する記事を参照してください。

使用する手順は次のとおりです。

1. ルールを作成します。
2. 前の手順で作成したルールをプロファイルにマッピングしてプロファイルを作成します。
3. 省略可能: Webhook プロパティと電子メール プロパティを構成して、自動スケールの通知を作成します。
4. これまでの手順で作成したプロファイルと通知をマッピングし、ターゲット リソースでの名前を指定して自動スケール設定を作成します。

以下の例は、CPU 使用率メトリックを使用して、Windows オペレーティング システム ベースの仮想マシン スケール セットの自動スケール設定を作成する方法を示しています。

まず、インスタンス数を増やしてスケールアウトするルールを作成します。

```PowerShell
$rule1 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionValue 1
```        

次に、インスタンス数を減らしてスケールインするルールを作成します。

```PowerShell
$rule2 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionValue 1
```

ルールのプロファイルを作成します。

```PowerShell
$profile1 = New-AzureRmAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Webhook プロパティを作成します。

```PowerShell
$webhook_scale = New-AzureRmAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

前に作成した電子メールと Webhook を含めて、自動スケール設定の通知プロパティを作成します。

```PowerShell
$notification1= New-AzureRmAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

最後に、自動スケール設定を作成して、前に作成したプロファイルを追加します。 

```PowerShell
Add-AzureRmAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

自動スケール設定の管理の詳細については、「 [Get-AutoscaleSetting](https://msdn.microsoft.com/library/mt282461.aspx)」を参照してください。

## <a name="autoscale-history"></a>自動スケールの履歴
次の例は、最近の自動スケール イベントとアラート イベントを表示する方法を示しています。 アクティビティ ログの検索を使用して、自動スケールの履歴を表示します。

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

`Get-AzureRmAutoScaleHistory` コマンドレットを使用して、自動スケールの履歴を取得できます。

```PowerShell
Get-AzureRmAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

詳細については、「 [Get-AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx)」を参照してください。

### <a name="view-details-for-an-autoscale-setting"></a>自動スケール設定の詳細の表示
`Get-Autoscalesetting` コマンドレットを使用して、自動スケール設定の詳細を取得できます。

次の例では、リソース グループ "myrg1" のすべての自動スケール設定の詳細を表示します。

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

次の例では、リソース グループ "myrg1" の自動スケール設定の詳細を表示します。具体的には、"MyScaleVMSSSetting" という名前の自動スケール設定の詳細を表示します。

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>自動スケール設定の削除
`Remove-Autoscalesetting` コマンドレットを使用して、自動スケール設定を削除できます。

```PowerShell
Remove-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-activity-log"></a>アクティビティ ログのログ プロファイルの管理
"*ログ プロファイル*" を作成し、アクティビティ ログのデータをストレージ アカウントにエクスポートできます。また、ストレージ アカウントのデータ リテンション期間を構成することもできます。 必要に応じて、データをイベント ハブにストリーミングすることもできます。 この機能は現在プレビュー段階にあり、サブスクリプションごとに作成できるログ プロファイルは 1 つに限られます。 現在のサブスクリプションで以下のコマンドレットを使用して、ログ プロファイルを作成し、管理できます。 また、特定のサブスクリプションを選択することもできます。 PowerShell では現在のサブスクリプションが既定で使用されますが、`Set-AzureRmContext` を使用してサブスクリプションをいつでも変更できます。 そのサブスクリプション内の任意のストレージ アカウントまたはイベント ハブにデータをルーティングするようにアクティビティ ログを構成できます。 データは、JSON 形式で BLOB ファイルとして書き込まれます。

### <a name="get-a-log-profile"></a>ログ プロファイルの取得
既存のログ プロファイルを取得するには、 `Get-AzureRmLogProfile` コマンドレットを使用します。

### <a name="add-a-log-profile-without-data-retention"></a>データ リテンション期間を指定しないログ プロファイルの追加
```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>ログ プロファイルの削除
```PowerShell
Remove-AzureRmLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>データ リテンション期間を指定したログ プロファイルの追加
データを保持する日数を正の整数で指定して、 **-RetentionInDays** プロパティを指定できます。

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>リテンション期間とイベント ハブを指定したログ プロファイルの追加
データは、ストレージ アカウントにルーティングするだけでなく、イベント ハブにストリーミングすることもできます。 このプレビュー リリースでは、ストレージ アカウント構成は必須ですが、イベント ハブ構成は省略可能です。

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>診断ログの構成
多くの Azure サービスで、次の 1 つ以上の操作を実行できるように追加のログとテレメトリが提供されます。 
 - Azure Storage アカウントにデータを保存するための構成
 - Event Hubs への送信
 - Log Analytics ワークスペースへの送信 

操作は、リソース レベルでのみ実行できます。 ストレージ アカウントまたはイベント ハブは、診断設定が構成されているターゲット リソースと同じリージョンに存在する必要があります。

### <a name="get-diagnostic-setting"></a>診断設定の取得
```PowerShell
Get-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

診断設定の無効化

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

リテンション期間を指定せずに診断設定を有効にする

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

リテンション期間を指定して診断設定を有効にする

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

特定のログ カテゴリのリテンション期間を指定して診断設定を有効にする

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Event Hubs の診断設定を有効にする

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Enable $true
```

Log Analytics の診断設定を有効にする

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -WorkspaceId /subscriptions/s1/resourceGroups/insights-integration/providers/providers/microsoft.operationalinsights/workspaces/myWorkspace -Enabled $true

```

WorkspaceId プロパティはワークスペースの "*リソース ID*" を受け取ることに注意してください。 次のコマンドを使用して、Log Analytics ワークスペースのリソース ID を取得できます。

```PowerShell
(Get-AzureRmOperationalInsightsWorkspace).ResourceId

```

これらのコマンドは、複数の送信先にデータを送信するように結合することができます。
