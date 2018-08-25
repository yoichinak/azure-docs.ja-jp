---
title: Log Analytics の Azure Key Vault ソリューション | Microsoft Docs
description: Log Analytics の Azure Key Vault ソリューションを使用して、Azure Key Vault のログを調査することができます。
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: jochan
editor: ''
ms.assetid: 5e25e6d6-dd20-4528-9820-6e2958a40dae
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/09/2017
ms.author: richrund
ms.component: na
ms.openlocfilehash: 47158d0c2b5a80ceae25b275836d45b63db50ff4
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38309341"
---
# <a name="azure-key-vault-analytics-solution-in-log-analytics"></a>Log Analytics の Azure Key Vault Analytics ソリューション

![Key Vault のシンボル](./media/log-analytics-azure-keyvault/key-vault-analytics-symbol.png)

Log Analytics の Azure Key Vault ソリューションを使用して、Azure Key Vault の AuditEvent ログを調査することができます。

このソリューションを使用するには、Azure Key Vault の診断ログを有効にし、診断を Log Analytics ワークスペースに送信する必要があります。 Azure Blob Storage にログを書き込む必要はありません。

> [!NOTE]
> 2017 年 1 月に、Key Vault から Log Analytics へのログ送信のサポート方法が変更されました。 使用している Key Vault ソリューションのタイトルに *(非推奨)* と表示される場合は、「[古い Key Vault ソリューションからの移行](#migrating-from-the-old-key-vault-solution)」を参照し、必要な手順に従ってください。
>
>

## <a name="install-and-configure-the-solution"></a>ソリューションのインストールと構成
Azure Key Vault ソリューションのインストールと構成は、次の手順で行います。

1. Azure Key Vault ソリューションを有効にします。[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview) から有効にするか、[ソリューション ギャラリーからの Log Analytics ソリューションの追加](log-analytics-add-solutions.md)に関するページで説明されている手順に従って有効にしてください。
2. [ポータル](#enable-key-vault-diagnostics-in-the-portal)か [PowerShell](#enable-key-vault-diagnostics-using-powershell) を使用して、監視する Key Vault リソースの診断ログを有効にします。

### <a name="enable-key-vault-diagnostics-in-the-portal"></a>ポータルで Key Vault 診断を有効にする

1. Azure Portal で、監視する Key Vault リソースに移動します。
2. *[診断ログ]* を選択して、次のページを開きます。

   ![[Azure Key Vault] タイルの画像](./media/log-analytics-azure-keyvault/log-analytics-keyvault-enable-diagnostics01.png)
3. *[診断を有効にする]* をクリックして、次のページを開きます。

   ![[Azure Key Vault] タイルの画像](./media/log-analytics-azure-keyvault/log-analytics-keyvault-enable-diagnostics02.png)
4. 診断を有効にするには、*[状態]* の下の *[オン]* をクリックします。
5. *[Send to Log Analytics]* (Log Analytics に送信) のチェックボックスをクリックします。
6. 既存の Log Analytics ワークスペースを選択するか、ワークスペースを作成します。
7. *AuditEvent* ログを有効にするには、[ログ] の下のチェックボックスをクリックしてください。
8. *[保存]* をクリックして Log Analytics の診断ログを有効にします。

### <a name="enable-key-vault-diagnostics-using-powershell"></a>PowerShell を使用して Key Vault 診断を有効にする
次の PowerShell スクリプトは、`Set-AzureRmDiagnosticSetting` を使用して Key Vault の診断ログを有効にする方法の例を示しています。
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$kv = Get-AzureRmKeyVault -VaultName 'ContosoKeyVault'

Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```



## <a name="review-azure-key-vault-data-collection-details"></a>Azure Key Vault データ収集の詳細の確認
Key Vault の診断ログは、Azure Key Vault ソリューションによって直接収集されます。
Azure Blob Storage にログを記述する必要はありません。データ収集のエージェントも不要です。

次の表は、Azure Key Vault のデータ収集手段とデータ収集方法に関する各種情報をまとめたものです。

| Platform | 直接エージェント | Systems Center Operations Manager エージェント | Azure | Operations Manager が必要か | 管理グループによって送信される Operations Manager エージェントのデータ | 収集の頻度 |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  | 着信時 |

## <a name="use-azure-key-vault"></a>Azure Key Vault の使用
[ソリューションをインストール](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview)すると、Log Analytics の **[概要]** ページの **[Azure Key Vault]** タイルをクリックすることで、Key Vault データが表示されます。

![[Azure Key Vault] タイルの画像](./media/log-analytics-azure-keyvault/log-analytics-keyvault-tile.png)

**[概要]** タイルをクリックした後、収集されたログの概要を表示し、次のカテゴリについて詳細表示することができます。

* すべての Key Vault 操作の時系列ボリューム
* 失敗した操作の時系列ボリューム
* 操作ごとの平均待機時間
* 操作のサービス品質 (所要時間が 1,000 ミリ秒を超える操作の数と所要時間が 1,000 ミリ秒を超える操作の一覧)

![Azure Key Vault ダッシュボードの画像](./media/log-analytics-azure-keyvault/log-analytics-keyvault01.png)

![Azure Key Vault ダッシュボードの画像](./media/log-analytics-azure-keyvault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>いずれかの操作の詳細を表示するには
1. **[概要]** ページの **[Azure Key Vault]** タイルをクリックします。
2. **[Azure Key Vault]** ダッシュボードにあるいずれかのブレードで概要情報を確認し、ログの検索ページで、詳細情報の表示対象をクリックします。

    どのログの検索ページでも、時間、詳細結果、ログ検索履歴を表示することができます。 結果を絞り込むファセットを使用してフィルター処理することもできます。

## <a name="log-analytics-records"></a>Log Analytics のレコード
Azure Key Vault ソリューションによって分析されるのは、Azure 診断の [AuditEvent ログ](../key-vault/key-vault-logging.md)から収集された **KeyVaults** タイプのレコードです。  これらのレコードは、次の表に示したプロパティを持ちます。  

| プロパティ | 説明 |
|:--- |:--- |
| type |*AzureDiagnostics* |
| SourceSystem |*Azure* |
| CallerIpAddress |要求を行ったクライアントの IP アドレス |
| Category | *AuditEvent* |
| CorrelationId |オプションの GUID であり、クライアント側のログとサービス側の (Key Vault) ログを対応付ける場合に渡します。 |
| DurationMs |REST API 要求を処理するのにかかった時間 (ミリ秒単位) です。 この時間にはネットワーク待機時間が含まれません。したがって、クライアント側で測定する時間はこの時間と一致しない場合があります。 |
| httpStatusCode_d |要求によって返された HTTP 状態コード (例: *200*) |
| id_s |要求の一意の ID |
| identity_claim_appid_g | アプリケーション ID の GUID |
| OperationName |操作の名前 (「[Azure Key Vault のログ記録](../key-vault/key-vault-logging.md)」を参照) |
| OperationVersion |クライアントによって要求された REST API バージョン (例: *2015-06-01*) |
| requestUri_s |要求の URI |
| リソース |Key Vault の名前 |
| ResourceGroup |Key Vault のリソース グループ |
| ResourceId |Azure リソース マネージャー リソース ID。 Key Vault のログの場合は、Key Vault リソース ID となります。 |
| ResourceProvider |*MICROSOFT.KEYVAULT* |
| ResourceType | *VAULTS* |
| ResultSignature |HTTP の状態 (例: *OK*) |
| ResultType |REST API 要求の結果 (例: *Success*) |
| SubscriptionId |Key Vault を含んでいるサブスクリプションの Azure サブスクリプション ID |

## <a name="migrating-from-the-old-key-vault-solution"></a>古い Key Vault ソリューションからの移行
2017 年 1 月に、Key Vault から Log Analytics へのログ送信のサポート方法が変更されました。 これらの変更には次の利点があります。
+ ストレージ アカウントを使用する必要がなく、ログは Log Analytics に直接書き込まれます。
+ ログが生成されてから Log Analytics で使用できるまでの待機時間が短縮されます。
+ 構成手順が簡素化されます。
+ すべての種類の Azure 診断の共通形式です。

最新のソリューションを使用するには、次の操作を行います。

1. [Key Vault から Log Analytics に診断が直接送信されるように構成します。](#enable-key-vault-diagnostics-in-the-portal)  
2. 「[ソリューション ギャラリーから Log Analytics ソリューションを追加する](log-analytics-add-solutions.md)」に説明されている手順に従って Azure Key Vault ソリューションを有効にします。
3. 新しいデータ型を使用するように、保存されたクエリ、ダッシュボード、またはアラートを更新します。
  + KeyVaults から AzureDiagnostics に型を変更します。 ResourceType を使用して、Key Vault のログをフィルター処理できます。
  - `KeyVaults` の代わりに `AzureDiagnostics | where ResourceType'=="VAULTS"` を使用してください。
  + フィールド: (フィールド名は大文字小文字が区別されます)
  - 名前に \_s、\_d、または \_g のサフィックスがあるフィールドについては、最初の文字を小文字に変更します。
  - 名前に \_o のサフィックスがあるフィールドについては、入れ子になったフィールド名に基づき、データは個別のフィールドに分割されます。 例: 呼び出し元の UPN を `identity_claim_http_schemas_xmlsoap_org_ws_2005_05_identity_claims_upn_s` のフィールドに格納する
   - フィールド CallerIpAddress は CallerIPAddress に変更されます。
   - フィールド RemoteIPCountry は存在しません。
4. *Key Vault Analytics (非推奨)* ソリューションを削除します。 PowerShell を使用している場合は、次のコードを使用します。`Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "KeyVault" -Enabled $false`

変更前に収集されたデータは、新しいソリューションには表示されません。 元の型とフィールド名を使用して、このデータのクエリを続行できます。

## <a name="troubleshooting"></a>トラブルシューティング
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>次の手順
* [Log Analytics のログ検索機能](log-analytics-log-searches.md)を使用して、詳細な Azure Key Vault データを確認してください。
