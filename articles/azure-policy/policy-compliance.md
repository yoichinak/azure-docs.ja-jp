---
title: Azure Policy でのコンプライアンス データの取得
description: Azure Policy の評価と効果によって、コンプライアンスが決まります。 コンプライアンスの詳細を取得する方法を説明します。
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 07/29/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: f2283125aff705aae87b6260b48deee01aa12f0d
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2018
ms.locfileid: "39343554"
---
# <a name="getting-compliance-data"></a>コンプライアンス データの取得

Azure Policy の最大の利点の 1 つは、サブスクリプション内のリソースまたはサブスクリプションの[管理グループ](../azure-resource-manager/management-groups-overview.md)を介して提供される分析情報と制御です。 この制御は、さまざまな方法で実行できます。たとえば、リソースが誤った場所に作成されるのを防いだり、一般的で一貫性のあるタグの使用を強制したり、既存のリソースの適切な構成と設定を監査したりすることができます。 どの場合でも Policy によってデータが生成され、環境のコンプライアンス状態の理解に役立てることができます。

ポリシーとイニシアティブ割り当てによって生成されたコンプライアンス情報には、次のようないくつかの方法でアクセスすることができます。

- [Azure portal](#portal) を使用して
- [コマンド ライン](#command_line) スクリプトを通じて

コンプライアンスの報告方法を説明する前に、コンプライアンス情報がいつ更新されるかと、評価サイクルをトリガーする頻度とイベントについて説明します。

## <a name="evaluation-triggers"></a>評価のトリガー

完了した評価サイクルの結果は、`PolicyStates` および `PolicyEvents` 操作を通じて `Microsoft.PolicyInsights` リソース プロバイダーに反映されます。 Policy Insights REST API のオプションと機能の詳細については、「[Policy Insights](/rest/api/policy-insights/)」を参照してください。

割り当てられたポリシーとイニシアティブの評価は、次のようなさまざまなイベントの結果として発生します。

- ポリシーまたはイニシアティブがスコープに新たに割り当てられる。 この場合、定義されたスコープに割り当てが適用されるまでに、約 30 分かかります。 適用されると、新たに割り当てられたポリシーまたはイニシアティブに従って、そのスコープ内のリソースに対する評価サイクルが開始されます。リソースは、ポリシーまたはイニシアティブによって使用されている効果に応じて、準拠または非準拠としてマークされます。 大きなスコープのリソースに対する大きなポリシーまたはイニシアティブの評価には時間がかかるため、評価サイクルがいつ完了するかを事前に予想することはできません。 完了すると、更新されたコンプライアンス結果をポータルと SDK で使用できるようになります。
- 既にスコープに割り当てられているポリシーまたはイニシアティブが更新される。 このシナリオでの評価サイクルとタイミングは、スコープへの新しい割り当ての場合と同じです。
- Resource Manager、REST、Azure CLI、または Azure PowerShell を介した割り当てで、リソースがスコープにデプロイされる。 このシナリオでは、個々のリソースに対する効果的なイベント (追加、監査、拒否、展開) とコンプライアンス ステータスの情報が、約 15 分後にポータルおよび SDKで利用可能です。 このイベントによって、他のリソースの評価が行われることはありません。
- 標準コンプライアンス評価サイクル。 24 時間に 1 回、割り当てが自動的に再評価されます。 大きなスコープのリソースに対する大きなポリシーまたはイニシアティブの評価には時間がかかるため、評価サイクルがいつ完了するかを事前に予想することはできません。 完了すると、更新されたコンプライアンス結果をポータルと SDK で使用できるようになります。

## <a name="how-compliance-works"></a>コンプライアンスのしくみ

割り当てのリソースがポリシーや統括ルールに従っていない場合、そのリソースは非準拠になります。 次の表は、さまざまなポリシーの効果での条件の評価と、その結果であるコンプライアンスの状態を示しています。

| リソースの状態 | 効果 | ポリシーの評価 | コンプライアンスの状態 |
| --- | --- | --- | --- |
| Exists | Deny、Audit、Append\*、DeployIfNotExist\*、AuditIfNotExist\* | True | 非準拠 |
| Exists | Deny、Audit、Append\*、DeployIfNotExist\*、AuditIfNotExist\* | False | 対応 |
| 新規 | Audit、AuditIfNotExist\* | True | 非準拠 |
| 新規 | Audit、AuditIfNotExist\* | False | 対応 |

\* Append、DeployIfNotExist、AuditIfNotExist の各効果では、IF ステートメントが TRUE である必要があります。
また、非準拠となるには、既存の条件が FALSE である必要があります。 TRUE のとき、IF 条件は関連するリソースの既存の条件の評価をトリガーします。

たとえば、ContosoRG というリソース グループがあり、このリソース グループの一部のストレージ アカウント (赤で強調表示されているアカウント) がパブリック ネットワークに公開されているとします。

![パブリック ネットワークに公開されているストレージ アカウント](media/policy-insights/resource-group01.png)

この例では、セキュリティ リスクに注意する必要があります。 これで作成されたポリシー割り当てが ContosoRG リソース グループ内のすべてのストレージ アカウントに対して評価されます。 3 つの非準拠のストレージ アカウントを監査し、結果としてそれらの状態を**非準拠**に変更します。

![監査された非準拠のストレージ アカウント](media/policy-insights/resource-group03.png)

## <a name="portal"></a>ポータル

Azure portal には、環境のコンプライアンス状態を視覚化して理解するためのグラフィカルなエクスペリエンスが表示されます。 **[ポリシー]** ページの **[概要]** オプションでは、ポリシーとイニシアティブの両方のコンプライアンスに関して、利用可能なスコープの詳細が表示されます。 コンプライアンス状態と割り当てごとの数の他に、過去 7 日間のコンプライアンスを示すグラフも含まれています。 **[コンプライアンス]** ページには、この同じ情報の大部分 (グラフを除く) が含まれていますが、フィルター処理と並べ替えのオプションが追加されています。

![ポリシーのコンプライアンスのページ](media/policy-compliance/compliance-page.png)

ポリシーまたはイニシアティブは異なるスコープに割り当てることができるため、各割り当てのスコープとそのスコープに割り当てられた定義の種類が表に示されます。 各割り当ての準拠していないポリシーおよび準拠していないリソースの数も提供されます。 表のポリシーまたはイニシアティブをクリックすると、その特定の割り当てのコンプライアンスに関する詳しい情報が表示されます。

![ポリシーのコンプライアンスの詳細](media/policy-compliance/compliance-details.png)

**[準拠していないリソース]** タブのリソース一覧には、現在の割り当ての既存のリソースの評価状態が反映されますが、リソースを作成する要求によってトリガーされたイベント (追加、監査、拒否、デプロイ) は、**[イベント]** タブの下に表示されます。

![ポリシーのコンプライアンスのイベント](media/policy-compliance/compliance-events.png)

詳細情報を収集するイベントの行を右クリックし、**[アクティビティ ログの表示]** を選択します。 アクティビティ ログ ページが開き、事前にフィルター処理されて、割り当ておよびイベントの詳細が検索して表示されます。 アクティビティ ログは、これらのイベントに関する追加のコンテキストと情報を提供します。

![ポリシー コンプライアンスのアクティビティ ログ](media/policy-compliance/compliance-activitylog.png)

## <a name="command-line"></a>コマンド ライン

ポータルで利用できるのと同じ情報は、REST API を直接使用して ([ARMClient](https://github.com/projectkudu/ARMClient) を含めて)、または Azure PowerShell と REST API を使用して、取得することができます。 REST API の完全な詳細情報については、[Policy Insights](/rest/api/policy-insights/) リファレンスを参照してください。 REST API のリファレンス ページには、各操作に緑色の [使ってみる] ボタンがあり、ブラウザーで直接試すことができます。

Azure PowerShell で次の例を使用するには、この例のコードを使用して認証トークンを構築します。 次に、後で解析できる JSON オブジェクトを取得するために、例の $restUri を目的の文字列に置き換えます。

```azurepowershell-interactive
# Login first with Connect-AzureRmAccount if not using Cloud Shell

$azContext = Get-AzureRmContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Define the REST API to communicate with
# Use double quotes for $restUri as some endpoints take strings passed in single quotes
$restUri = "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04"

# Invoke the REST API
$response = Invoke-RestMethod -Uri $restUri -Method POST -Headers $authHeader

# View the response object (as JSON)
$response
```

### <a name="summarize-results"></a>結果の要約

REST API を使用すると、管理グループ、サブスクリプション、リソース グループ、リソース、イニシアティブ、ポリシー、サブスクリプション レベルの割り当て、またはリソース グループ レベルの割り当てごとに要約を実行できます。 Policy Insight の [Summarize For Subscription](/rest/api/policy-insights/policystates/summarizeforsubscription) を使用した、サブスクリプション レベルの要約の例を次に示します。

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04
```

出力はサブスクリプションを要約しています。 以下の出力例では、要約されたコンプライアンスは **value.results.nonCompliantResources** と **value.results.nonCompliantPolicies** の下にあります。 この要求では、より詳細な情報が提供されます。たとえば、非準拠として数えられている各割り当てや、各割り当ての定義情報などです。 階層内の各ポリシー オブジェクトは、そのレベルで追加の詳細情報を取得するために使用することができる **queryResultsUri** を提供しています。

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
        "results": {
            "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false",
            "nonCompliantResources": 15,
            "nonCompliantPolicies": 1
        },
        "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77",
            "policySetDefinitionId": "",
            "results": {
                "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77'",
                "nonCompliantResources": 15,
                "nonCompliantPolicies": 1
            },
            "policyDefinitions": [{
                "policyDefinitionReferenceId": "",
                "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "effect": "deny",
                "results": {
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'",
                    "nonCompliantResources": 15
                }
            }]
        }]
    }]
}
```

### <a name="query-for-resources"></a>リソースのクエリ

上の例では、**value.policyAssignments.policyDefinitions.results.queryResultsUri** によって、特定のポリシー定義に準拠していないすべてのリソースを取得するためのサンプル URI が提供されました。 **$filter** の値を見ると、IsCompliant は false に等しく (eq)、PolicyAssignmentId がポリシー定義に対して指定され、その後 PolicyDefinitionId 自体が指定されています。
フィルターに PolicyAssignmentId を含める理由は、PolicyDefinitionId がさまざまなスコープの複数のポリシーまたはイニシアティブ割り当てに存在する可能性があるためです。 PolicyAssignmentId と PolicyDefinitionId の両方を指定することにより、必要とする結果を明示することができます。 以前は、PolicyStates に **latest** を使用しました (Summarize For Subscription 演算子の **policyStatesSummaryResource** に許可されている唯一の値)。この場合、**from** および **to** 時間枠が直前の 24 時間に自動的に設定されます。

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

以下の応答例は、簡潔に示すために、準拠していないリソースを 1 つだけ表示するようにトリミングされています (@odata.count は実際には 15 であり、上の例の準拠していないリソースの数と一致していることに注意してください)。 詳細な応答では、リソース、ポリシー (またはイニシアティブ)、および割り当てに関するいくつかのデータが提供されます。 ポリシー定義にどの割り当てパラメーターが渡されたかを確認することもできることに注意してください。

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 15,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
        "timestamp": "2018-05-19T04:41:09Z",
        "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Compute/virtualMachines/linux",
        "policyAssignmentId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Authorization/policyAssignments/37ce239ae4304622914f0c77",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "effectiveParameters": "",
        "isCompliant": false,
        "subscriptionId": "{subscriptionId}",
        "resourceType": "/Microsoft.Compute/virtualMachines",
        "resourceLocation": "westus2",
        "resourceGroup": "RG-Tags",
        "resourceTags": "tbd",
        "policyAssignmentName": "37ce239ae4304622914f0c77",
        "policyAssignmentOwner": "tbd",
        "policyAssignmentParameters": "{\"tagName\":{\"value\":\"costCenter\"},\"tagValue\":{\"value\":\"Contoso-Test\"}}",
        "policyAssignmentScope": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags",
        "policyDefinitionName": "1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "policyDefinitionAction": "deny",
        "policyDefinitionCategory": "tbd",
        "policySetDefinitionId": "",
        "policySetDefinitionName": "",
        "policySetDefinitionOwner": "",
        "policySetDefinitionCategory": "",
        "policySetDefinitionParameters": "",
        "managementGroupIds": "",
        "policyDefinitionReferenceId": ""
    }]
}
```

### <a name="view-events"></a>イベントの表示

リソースを作成または更新すると、ポリシーの評価結果が生成されます。 これらの結果は "_ポリシー イベント_" と呼ばれます。 サブスクリプションに関連する最近のポリシー イベントを表示するには、次の URI を使用します。

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2018-04-04
```

次のような結果が返されます。

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
        "NumAuditEvents": 16
    }]
}
```

ポリシー イベントについてクエリを実行する方法の詳細については、[ポリシー イベント](/rest/api/policy-insights/policyevents)に関する API リファレンスの記事を参照してください。

### <a name="azure-powershell"></a>Azure PowerShell

Policy 用の Azure PowerShell モジュールは PowerShell ギャラリーで [AzureRM.PolicyInsights](https://www.powershellgallery.com/packages/AzureRM.PolicyInsights) として入手することができます。 PowerShellGet では、`Install-Module -Name AzureRM.PolicyInsights` を使用してモジュールをインストールできます (最新の [Azure PowerShell](/powershell/azure/install-azurerm-ps) がインストールされていることを確認してください)。

```powershell
# Install from PowerShell Gallery via PowerShellGet
Install-Module -Name AzureRM.PolicyInsights

# Import the downloaded module
Import-Module AzureRM.PolicyInsights

# Login with Connect-AzureRmAccount if not using Cloud Shell
Connect-AzureRmAccount
```

モジュールには次の 3 つのコマンドレットがあります。

- `Get-AzureRmPolicyStateSummary`
- `Get-AzureRmPolicyState`
- `Get-AzureRmPolicyEvent`

例: 準拠していないリソースの数が最も多い、割り当てられているポリシーの状態の要約を取得する。

```powershell
PS > Get-AzureRmPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

例: 直前に評価されたリソースの状態レコードを取得する (既定では降順のタイムスタンプによって)。

```powershell
PS > Get-AzureRmPolicyState -Top 1

Timestamp                  : 5/22/2018 3:47:34 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/networkInterfaces/linux316
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/networkInterfaces
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

例: 準拠していないすべての仮想ネットワーク リソースの詳細情報を取得する。

```powershell
PS > Get-AzureRmPolicyState -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'"

Timestamp                  : 5/22/2018 4:02:20 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

例: 特定の日付以降に発生した、準拠していない仮想ネットワーク リソースに関連するイベントを取得する。

```powershell
PS > Get-AzureRmPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2018-05-19'

Timestamp                  : 5/19/2018 5:18:53 AM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : eastus
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
TenantId                   : {tenantId}
PrincipalOid               : {principalOid}
```

**PrincipalOid** フィールドは、Azure PowerShell コマンドレット `Get-AzureRmADUser` を使用して特定のユーザーを取得するために使用できます。 **{principalOid}** を、前の例で取得した応答に置き換えます。

```powershell
PS > (Get-AzureRmADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="log-analytics"></a>Log Analytics

サブスクリプションに関連付けられた `AzureActivity` ソリューションを持つ [Log Analytics](../log-analytics/log-analytics-overview.md) ワークスペースがある場合は、単純な Kusto クエリと `AzureActivity` テーブルを使用して、評価サイクルでの非準拠の結果を表示することもできます。 これは、Log Analytics で非準拠の詳細情報を使用すると、特定のリソースやリソース グループでの非準拠や、非準拠項目のしきい値 (過去 24 時間で 10 件を超えるなど) を監視するようにアラートを構成できるということでもあります。

![Log Analytics を使用したポリシーのコンプライアンス](media/policy-compliance/compliance-loganalytics.png)

## <a name="next-steps"></a>次の手順

- [ポリシー定義の構造](policy-definition.md)を確認します。
- 「[Policy の効果について](policy-effects.md)」を確認します。
- 「[Azure 管理グループのリソースを整理する](../azure-resource-manager/management-groups-overview.md)」で、管理グループとは何かを確認します。