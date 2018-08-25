---
title: Azure トラフィック分析についてよく寄せられる質問 | Microsoft Docs
description: トラフィック分析についてよく寄せられる質問の回答を確認します。
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: jdial
ms.openlocfilehash: 9f7fb5009468dccae50190ee40a53d11040d0348
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2018
ms.locfileid: "37903749"
---
# <a name="traffic-analytics-frequently-asked-questions"></a>トラフィック分析についてよく寄せられる質問

この記事では、Azure Network Watcher のトラフィック分析についてよく寄せられる質問の多くをまとめました。

## <a name="what-are-the-prerequisites-to-use-traffic-analytics"></a>トラフィック分析を使用する前提条件は何ですか?

トラフィック分析の前提条件は次のとおりです。

- Network Watcher 対応のサブスクリプション。
- 監視対象のネットワーク セキュリティ グループ (NSG) に対して有効になっている NSG フロー ログ。
- 未処理フロー ログを格納するための Azure Storage アカウント。
- 読み取りと書き込みのアクセス権がある Log Analytics ワークスペース

トラフィック分析を有効にするには、アカウントが次のいずれかを満たしている必要があります。

- アカウントには、サブスクリプション レベルで、アカウント管理者、サービス管理者、または共同管理者のいずれかのロールが割り当てられている必要があります。
- アカウントには、サブスクリプション スコープで、所有者、共同作成者、閲覧者、またはネットワーク共同作成者のいずれかのロールベースのアクセス制御 (RBAC) ロールが割り当てられている必要があります。
- アカウントに上記のどのロールも割り当てられていない場合、サブスクリプション レベルで次のアクションが割り当てられるカスタム ロールを割り当てる必要があります。
            
    - Microsoft.Network/applicationGateways/read
    - Microsoft.Network/connections/read
    - Microsoft.Network/loadBalancers/read 
    - Microsoft.Network/localNetworkGateways/read 
    - Microsoft.Network/networkInterfaces/read 
    - Microsoft.Network/networkSecurityGroups/read 
    - Microsoft.Network/publicIPAddresses/read
    - Microsoft.Network/routeTables/read
    - Microsoft.Network/virtualNetworkGateways/read 
    - Microsoft.Network/virtualNetworks/read
        
サブスクリプションのユーザーに割り当てられているロールを確認するには、次の手順を実行します。

1. **Login-AzureRmAccount** を使用して Azure にサインインします。 

2. **Select-AzureRmSubscription** を使用して、必要なサブスクリプションを選択します。 

3. 指定したユーザーに割り当てられているすべてのロールを一覧表示するには、**Get-AzureRmRoleAssignment -SignInName [ユーザーの電子メール アドレス] -IncludeClassicAdministrators** を使用します 

出力が表示されない場合は、各サブスクリプション管理者に連絡して、コマンドを実行するアクセス権を取得してください。 詳細については、[Azure PowerShell を使用したロールベースのアクセス制御の管理](https://docs.microsoft.com/en-us/azure/role-based-access-control/role-assignments-powershell)に関するページを参照してください。


## <a name="in-which-azure-regions-are-traffic-analytics-available"></a>トラフィック分析はどの Azure リージョンで使用できますか?

米国中西部、米国東部、米国東部 2、米国中北部、米国中南部、米国中部、米国西部、米国西部 2、西ヨーロッパ、北ヨーロッパ、英国西部、英国南部、オーストラリア東部、オーストラリア南東部、および東南アジアのサポートされているいずれかのリージョンの NSG でトラフィック分析をご利用いただけます。 Log Analytics ワークスペースは、米国中西部、米国東部、西ヨーロッパ、英国南部、オーストラリア南東部、または東南アジア リージョンに存在する必要があります。

## <a name="can-the-nsgs-i-enable-flow-logs-for-be-in-different-regions-than-my-workspace"></a>フロー ログを有効化する NSG は、自分のワークスペースとは別のリージョンに存在できますか?

はい。これらの NSG は、Log Analytics ワークスペースとは異なるリージョンに存在することができます。

## <a name="can-multiple-nsgs-be-configured-within-a-single-workspace"></a>1 つのワークスペース内に複数の NSG を構成できますか?

はい。

## <a name="can-i-use-an-existing-workspace"></a>既存のワークスペースを使用できますか?

はい。 既存のワークスペースを選択する場合は、それが新しいクエリ言語に移行されていることを確認してください。 そのワークスペースのアップグレードを望まない場合は、新しいワークスペースを作成する必要があります。 新しいクエリ言語の詳細については、「[新しいログ検索への Azure Log Analytics のアップグレード](../log-analytics/log-analytics-log-search-upgrade.md)」をご覧ください。

## <a name="can-my-azure-storage-account-be-in-one-subscription-and-my-operations-management-suite-workspace-be-in-a-different-subscription"></a>自分の Azure Storage アカウントと自分の Operations Management Suite ワークスペースをそれぞれ別のサブスクリプションに置くことができますか?

はい。自分の Azure Storage アカウントと自分の Operations Management Suite ワークスペースをそれぞれ別のサブスクリプションに置くことができます。

## <a name="can-i-store-raw-logs-in-a-different-subscription"></a>未処理のログを別のサブスクリプションで格納することはできますか?

いいえ。 NSG のフロー ログが有効になっているストレージ アカウントでは、未処理のログを保存できます。 ただし、ストレージ アカウントと未処理のログは、同じサブスクリプションとリージョンに属している必要があります。

## <a name="what-if-i-cant-configure-an-nsg-for-traffic-analytics-due-to-a-not-found-error"></a>"見つかりません" エラーによってトラフィック分析用に NSG を構成できないとどうなりますか?

サポートされているリージョンを選択します。 サポートされていないリージョンを選択すると、[見つかりません] エラーが表示されます。 サポートされているリージョンについては、この記事で前述しています。

## <a name="what-if-i-am-getting-the-status-failed-to-load-under-the-nsg-flow-logs-page"></a>NSG フロー ログ ページの下に "読み込みに失敗しました" という状態が表示される場合はどうすればよいですか?

フロー ログが正常に機能するためには、Microsoft.Insights プロバイダーが登録されている必要があります。 サブスクリプションで Microsoft.Insights プロバイダーが登録されているかどうかが不明な場合は、次のコマンドの *xxxxx-xxxxx-xxxxxx-xxxx* を置き換えて PowerShell で実行してください。

```powershell-interactive
**Select-AzureRmSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
**Register-AzureRmResourceProvider** -ProviderNamespace Microsoft.Insights
```

## <a name="i-have-configured-the-solution-why-am-i-not-seeing-anything-on-the-dashboard"></a>ソリューションを構成しました。 ダッシュボードに何も表示されないのはなぜですか?

ダッシュボードを最初に表示する際は最大で 30 分かかることがあります。 ソリューションで意味がある分析情報が導出されるには、まず十分なデータを集計する必要があります。 その後にレポートが生成されます。 

## <a name="what-if-i-get-this-message-we-could-not-find-any-data-in-this-workspace-for-selected-time-interval-try-changing-the-time-interval-or-select-a-different-workspace"></a>次のメッセージが表示された場合はどうすればよいですか? "選択した時間内のデータがこのワークスペースで見つかりませんでした。 時間間隔を変更してみるか、別のワークスペースを選択してください"。

次の方法を試してください。
- 上部のバーで期間を変更します。
- 上部のバーで別の Log Analytics ワークスペースを選択します。
- 30 分後に Traffic Analytics にアクセスしてみます (最近有効にした場合)。
    
問題が解決しない場合は、[User Voice フォーラム](https://feedback.azure.com/forums/217313-networking?category_id=195844)に問題を投稿してください。

## <a name="what-if-i-get-this-message-analyzing-your-nsg-flow-logs-for-the-first-time-this-process-may-take-20-30-minutes-to-complete-check-back-after-some-time-2-if-the-above-step-doesnt-work-and-your-workspace-is-under-the-free-sku-then-check-your-workspace-usage-here-to-validate-over-quota-else-refer-to-faqs-for-further-information"></a>次のメッセージが表示された場合はどうすればよいですか? "NSG フロー ログを初めて分析している可能性があります。 This process may take 20-30 minutes to complete. Check back after some time. 2) If the above step doesn’t work and your workspace is under the free SKU, then check your workspace usage here to validate over quota, else refer to FAQs for further information (上記のステップが機能せず、ワークスペースが無料 SKU にある場合は、ここでワークスペースの使用状況をチェックして超過クォータを検証します。該当しない場合は、詳細について FAQ を参照してください)"。

このメッセージは、次の理由で表示される場合があります。
- トラフィック分析が最近有効化され、意味のある分析情報を導出できる十分なデータをまだ集計していない可能性があります。
- 無料版の Operations Management Suite ワークスペースを使用しており、クォータ制限を超えています。 容量の大きいワークスペースを使用しなければならない可能性があります。
    
問題が解決しない場合は、[User Voice フォーラム](https://feedback.azure.com/forums/217313-networking?category_id=195844)に問題を投稿してください。
    
## <a name="what-if-i-get-this-message-looks-like-we-have-resources-data-topology-and-no-flows-information-meanwhile-click-here-to-see-resources-data-and-refer-to-faqs-for-further-information"></a>次のメッセージが表示された場合はどうすればよいですか? "選択したワークスペースに関して存在するのはリソース データ (トポロジ) のみで、フローの情報はない可能性があります。 Meanwhile, click here to see resources data and refer to FAQs for further information. (まず、ここをクリックしてリソース データを確認し、詳細については FAQ を参照してください。)"

ダッシュボードにはリソース情報が表示されていますが、フロー関連の統計がありません。 リソース間の通信フローがないためにデータが示されない可能性があります。 60 分間待ってから、状態を再確認します。 問題が解決せず、リソース間の通信フローが存在することが確実な場合は、[User Voice フォーラム](https://feedback.azure.com/forums/217313-networking?category_id=195844)に問題を投稿してください。

## <a name="can-i-configure-traffic-analytics-using-powershell-or-an-azure-resource-manager-template-or-client"></a>PowerShell または Azure Resource Manager テンプレートまたはクライアントを使用してトラフィック分析を構成できますか?

バージョン 6.2.1 以降の Windows PowerShell を使用してトラフィック分析を構成できます。 Set コマンドレットを使用して特定の NSG のフロー ログとトラフィック分析を構成するには、[Set-AzureRmNetworkWatcherConfigFlowLog](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermnetworkwatcherconfigflowlog?view=azurermps-6.3.0) を参照してください。 特定の NSG のフロー ログとトラフィック分析状態を取得するには、[Get-AzureRmNetworkWatcherFlowLogStatus](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkwatcherflowlogstatus?view=azurermps-6.3.0) を参照してください。

現在、Azure Resource Manager テンプレートを使用してトラフィック分析を構成することはできません。

Azure Resource Manager クライアントを使用してトラフィック分析を構成するには、次の例を参照してください。

**Set コマンドレットの例:**
```
#Requestbody parameters
$TAtargetUri ="/subscriptions/<NSG subscription id>/resourceGroups/<NSG resource group name>/providers/Microsoft.Network/networkSecurityGroups/<name of NSG>"
$TAstorageId = "/subscriptions/<storage subscription id>/resourcegroups/<storage resource group name> /providers/microsoft.storage/storageaccounts/<storage account name>"
$networkWatcherResourceGroupName = "<network watcher resource group name>"
$networkWatcherName = "<network watcher name>"

$requestBody = 
@"
{
    'targetResourceId': '${TAtargetUri}',
    'properties': 
    {
        'storageId': '${TAstorageId}',
        'enabled': '<true to enable flow log or false to disable flow log>',
        'retentionPolicy' : 
        {
            days: <enter number of days like to retail flow logs in storage account>,
            enabled: <true to enable retention or false to disable retention>
        }
    },
    'flowAnalyticsConfiguration':
    {
                'networkWatcherFlowAnalyticsConfiguration':
      {
        'enabled':,<true to enable traffic analytics or false to disable traffic analytics>
        'workspaceId':'bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb',
        'workspaceRegion':'<workspace region>',
        'workspaceResourceId':'/subscriptions/<workspace subscription id>/resourcegroups/<workspace resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>'
        
      }

    }
}
"@
$apiversion = "2016-09-01"

armclient login
armclient post "https://management.azure.com/subscriptions/<NSG subscription id>/resourceGroups/<network watcher resource group name>/providers/Microsoft.Network/networkWatchers/<network watcher name>/configureFlowlog?api-version=${apiversion}" $requestBody
```
**Get コマンドレットの例:**
```
#Requestbody parameters
$TAtargetUri ="/subscriptions/<NSG subscription id>/resourceGroups/<NSG resource group name>/providers/Microsoft.Network/networkSecurityGroups/<NSG name>"


$requestBody = 
@"
{
    'targetResourceId': '${TAtargetUri}'
}
“@


armclient login
armclient post "https://management.azure.com/subscriptions/<NSG subscription id>/resourceGroups/<network watcher resource group name>/providers/Microsoft.Network/networkWatchers/<network watcher name>/queryFlowLogStatus?api-version=${apiversion}" $requestBody
```



## <a name="how-is-traffic-analytics-priced"></a>トラフィック分析はどのように課金されますか?

トラフィック分析が測定されます。 測定は、サービスによるフロー ログ データの処理と、その結果として生成された拡張ログの Log Analytics ワークスペースへの格納に基づいています。 

たとえば、[料金プラン](https://azure.microsoft.com/en-us/pricing/details/network-watcher/)に従い、米国中西部リージョンの場合を考えます。ストレージ アカウントに格納され、Traffic Analytics によって処理されるフロー ログ データが 10 GB で、Log Analytics ワークスペースに取り込まれる拡張ログが 1 GB の場合、適用される料金は 10 x 2.3$ + 1 x 2.76$ = 25.76$ になります。

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-geo-map-view"></a>geo マップ ビューでキーボードを使用して操作するにはどうすればよいですか?

geo マップ ページには主に次の 2 つのセクションがあります。
    
- **バナー**: geo マップの上部のバナーには、トラフィック分布フィルター (たとえば、デプロイ、各国からのトラフィック、悪意のある、など) を選択するボタンがあります。 ボタンを選択すると、それに対応するフィルターがマップに適用されます。 たとえば、[アクティブ] ボタンを選択すると、マップでデプロイ内のアクティブなデータセンターが強調表示されます。
- **マップ**: バナーの下にある [マップ] セクションには、Azure のデータセンターや各国のトラフィック分布が表示されます。
    
### <a name="keyboard-navigation-on-the-banner"></a>バナーでのキーボード ナビゲーション
    
- 既定では、バナーについて geo マップ ページで選択されているのは [Azure DC] フィルターです。
- 別のフィルターに移動するには、`Tab` または `Right arrow` キーを使用します。 戻るには、`Shift+Tab` または `Left arrow` キーを使用します。 前方の移動は、左から右、次に上から下の順になります。
- `Enter` または `Down` 方向キーを押して、選択したフィルターを適用します。 フィルターの選択とデプロイに基づいて、[マップ] セクションの 1 つまたは複数のノードが強調表示されます。
- バナーとマップを切り替えるには、`Ctrl+F6` を押します。
        
### <a name="keyboard-navigation-on-the-map"></a>マップでのキーボード ナビゲーション
    
- バナーでフィルターを選択してから `Ctrl+F6` を押すと、マップ ビューで強調されたノードの 1 つ (**Azure データセンター**または**国/リージョン**) にフォーカスが移動します。
- マップ内の他の強調表示されたノードに移動するには、前方移動に `Tab` キーまたは `Right arrow` キーを使用します。 後方移動には、`Shift+Tab` キーまたは `Left arrow` キーを使用します。
- マップ内で強調表示されたノードを選択するには、`Enter` キーまたは `Down arrow` キーを使用します。
- そのようなノードを選択すると、そのノードの **[Information Tool Box]\(情報ツール ボックス\)** にフォーカスが移動します。 既定では、**[Information Tool Box]\(情報ツール ボックス\)** の [閉じる] ボタンにフォーカスが移動します。 **[ボックス]** ビュー内で移動する際は、前方には `Right arrow` キー、後方には `Left arrow` キーを使用します。 `Enter` を押すと、**[Information Tool Box]\(情報ツール ボックス\)** でフォーカスがあるボタンを選択したのと同じ効果があります。
- **[Information Tool Box]\(情報ツール ボックス\)** にフォーカスがあるときに `Tab`を押すと、フォーカスは、選択したノードと同じ大陸のエンドポイントに移動します。 `Right arrow` キーと `Left arrow` キーを使用して、これらのエンドポイント間を移動できます。
- 他のフロー エンドポイントまたは大陸クラスターに移動するには、前方移動では `Tab`、後方移動では `Shift+Tab` を使用します。
- フォーカスが**大陸クラスター**にある場合は、`Enter` または `Down` 方向キーを使用して、大陸クラスター内のエンドポイントを強調表示します。 大陸クラスターのエンドポイントと情報ボックスの [閉じる] ボタンを移動するには、前方移動では `Right arrow` キー、後方移動では `Left arrow` キーをそれぞれ使用します。 任意のエンドポイントで、`Shift+L` を使用すると、選択したノードからエンドポイントへの接続線に切り替えることができます。 もう一度 `Shift+L` を押して、選択したエンドポイントに移動できます。
        
### <a name="keyboard-navigation-at-any-stage"></a>任意の段階のキーボード ナビゲーション
    
- `Esc` を押すと、展開された選択範囲が折りたたまれます。
- `Up arrow` キーは `Esc` と同じ動作を実行します。 `Down arrow` キーは `Enter` と同じ動作を実行します。
- `Shift+Plus` を使用して拡大、`Shift+Minus` を使用して縮小します。

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-virtual-network-topology-view"></a>仮想ネットワーク トポロジ ビューでキーボードを使用して操作するにはどうすればよいですか?

仮想ネットワーク トポロジ ページには、主に 2 つのセクションがあります。
    
- **バナー**: 仮想ネットワーク トポロジの上部バナーには、トラフィック分布フィルター (たとえば、接続された仮想ネットワーク、切断された仮想ネットワーク、パブリック IP など) を選択するボタンがあります。 ボタンを選択すると、それに対応するフィルターがトポロジに適用されます。 たとえば、[アクティブ] ボタンを選択すると、マップでデプロイ内のアクティブな仮想ネットワークが強調表示されます。
- **トポロジ**: バナーの下の [トポロジ] セクションには、仮想ネットワーク間全体のトラフィック分布が表示されます。
    
### <a name="keyboard-navigation-on-the-banner"></a>バナーでのキーボード ナビゲーション
    
- 既定では、バナーの仮想ネットワーク トポロジ ページでの選択は、[接続された VNet] フィルターです。
- 別のフィルターに移動する場合、前方に移動するには `Tab` キーを使用します。 後方に移動するには、`Shift+Tab` キーを使用します。 前方の移動は、左から右、次に上から下の順になります。
- 選択したフィルターを適用するには、`Enter` を押します。 フィルターの選択とデプロイに基づいて、[トポロジ] セクションの 1 つまたは複数のノード (仮想ネットワーク) が強調表示されます。
- バナーとトポロジーを切り替えるには、`Ctrl+F6` を押します。
        
### <a name="keyboard-navigation-on-the-topology"></a>トポロジでのキーボード ナビゲーション
    
- バナーでフィルターを選択してから `Ctrl+F6` を押すと、トポロジ ビューで強調されたノードの 1 つ (**VNet**) にフォーカスが移動します。
- トポロジ ビュー内の他の強調表示されたノードに移動するには、`Shift+Right arrow` キーを使用して前方に移動します。 
- 強調表示されたノードの **[Information Tool Box]\(情報ツール ボックス\)** にフォーカスが移動します。 既定では、**[Information Tool Box]\(情報ツール ボックス\)** の **[詳細]** ボタンにフォーカスが移動します。 **[ボックス]** ビュー内で移動する場合、前方には `Right arrow` キー、後方には `Left arrow` キーを使用します。 `Enter` を押すと、**[Information Tool Box]\(情報ツール ボックス\)** でフォーカスがあるボタンを選択したのと同じ効果があります。
- このようなノードを選択する際は、`Shift+Left arrow` キーを押して、すべての接続に 1 つずつアクセスできます。 フォーカスはその接続の **[Information Tool Box]\(情報ツール ボックス\)** に移動します。 いずれの時点でも、`Shift+Right arrow` を再度押して、フォーカスをノードに戻すことができます。
    

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-subnet-topology-view"></a>サブネット トポロジ ビューでキーボードを使用して操作するにはどうすればよいですか?

仮想サブネットワーク トポロジ ページには、主に 2 つのセクションがあります。
    
- **バナー**: 仮想サブネット ネットワーク トポロジの上部のバナーには、トラフィック分布フィルター (たとえば、アクティブ、中、ゲートウェイのサブネットなど) を選択するボタンがあります。 ボタンを選択すると、それに対応するフィルターがトポロジに適用されます。 たとえば、[アクティブ] ボタンを選択すると、マップでデプロイ内のアクティブな仮想サブネットワークが強調表示されます。
- **トポロジ**: バナーの下の [トポロジ] セクションには、仮想サブネットワーク間全体のトラフィック分布が表示されます。
    
### <a name="keyboard-navigation-on-the-banner"></a>バナーでのキーボード ナビゲーション
    
- 既定では、バナーの仮想サブネットワーク トポロジ ページでの選択は、[サブネット] フィルターです。
- 別のフィルターに移動する場合、前方に移動するには `Tab` キーを使用します。 後方に移動するには、`Shift+Tab` キーを使用します。 前方の移動は、左から右、次に上から下の順になります。
- 選択したフィルターを適用するには、`Enter` を押します。 フィルターの選択とデプロイに基づいて、[トポロジ] セクションの 1 つまたは複数のノード (サブネット) が強調表示されます。
- バナーとトポロジーを切り替えるには、`Ctrl+F6` を押します。
        
### <a name="keyboard-navigation-on-the-topology"></a>トポロジでのキーボード ナビゲーション
    
- バナーでフィルターを選択してから `Ctrl+F6` を押すと、トポロジ ビューで強調されたノードの 1 つ (**サブネット**) にフォーカスが移動します。
- トポロジ ビュー内の他の強調表示されたノードに移動するには、`Shift+Right arrow` キーを使用して前方に移動します。 
- 強調表示されたノードの **[Information Tool Box]\(情報ツール ボックス\)** にフォーカスが移動します。 既定では、**[Information Tool Box]\(情報ツール ボックス\)** の **[詳細]** ボタンにフォーカスが移動します。 **[ボックス]** ビュー内で移動する際は、前方には `Right arrow` キー、後方には `Left arrow` キーを使用します。 `Enter` を押すと、**[Information Tool Box]\(情報ツール ボックス\)** でフォーカスがあるボタンを選択したのと同じ効果があります。
- このようなノードを選択する際は、`Shift+Left arrow` キーを押して、すべての接続に 1 つずつアクセスできます。 フォーカスはその接続の **[Information Tool Box]\(情報ツール ボックス\)** に移動します。 いずれの時点でも、`Shift+Right arrow` を再度押して、フォーカスをノードに戻すことができます。    

