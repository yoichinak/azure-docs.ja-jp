---
title: Azure Log Analytics の Alert Management ソリューション | Microsoft Docs
description: 管理対象となる環境内のすべてのアラートは、Log Analytics のアラート管理ソリューションを使用して分析できます。  これは Log Analytics 内で生成されたアラートの統合に加えて、接続されている System Center Operations Manager 管理グループからのアラートを Log Analytics にインポートします。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: fe5d534e-0418-4e2f-9073-8025e13271a8
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/19/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: eb61a48e8c479db4742d65187b202655f29b032d
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131049"
---
# <a name="alert-management-solution-in-azure-log-analytics"></a>Azure Log Analytics の Alert Management ソリューション

![Alert Management icon](media/log-analytics-solution-alert-management/icon.png)

Log Analytics リポジトリ内のアラートはすべて、アラート管理ソリューションを使用して分析できます。  アラートはさまざまなソースから取得されている可能性があり、[Log Analytics によって作成された](log-analytics-alerts.md)ものや、[Nagios や Zabbix からインポートされた](log-analytics-linux-agents.md)モノが含まれます。  アラートは、[接続された System Center Operations Manager 管理グループ](log-analytics-om-agents.md)からもインポートされます。

## <a name="prerequisites"></a>前提条件
このソリューションでは、Log Analytics リポジトリ内の **Alert** タイプのすべてのレコードが分析されます。そのため、これらのレコードを収集するために必要な構成をすべて行う必要があります。

- Log Analytics のアラートの場合は、[アラート ルールを作成](log-analytics-alerts.md)して、リポジトリに直接アラート レコードを作成します。
- Nagios と Zabbix のアラートの場合は、[これらのサーバーを構成](log-analytics-linux-agents.md)して、Log Analytics にアラートを送信します。
- System Center Operations Manager のアラートの場合は、[Log Analytics ワークスペースに Operations Manager 管理グループを接続](log-analytics-om-agents.md)します。  System Center Operations Manager で作成されたすべてのアラートが Log Analytics にインポートされます。  

## <a name="configuration"></a>構成
「[Add solutions (ソリューションの追加)](log-analytics-add-solutions.md)」で説明されているプロセスを使用して、Log Analytics ワークスペースに Alert Management ソリューションを追加します。  さらに手動で構成する必要はありません。

## <a name="management-packs"></a>管理パック
System Center Operations Manager 管理グループが Log Analytics ワークスペースに接続されている場合は、このソリューションを追加したときに次の管理パックが System Center Operations Manager にインストールされます。  これらの管理パックに伴う構成や保守は不要です。  

* Microsoft System Center Advisor Alert Management (Microsoft.IntelligencePacks.AlertManagement)

ソリューション管理パックの更新方法の詳細については、「 [Operations Manager を Log Analytics に接続する](log-analytics-om-agents.md)」を参照してください。

## <a name="data-collection"></a>データ収集
### <a name="agents"></a>エージェント
次の表は、このソリューションの接続先としてサポートされているソースとその説明です。

| 接続先ソース | サポート | 説明 |
|:--- |:--- |:--- |
| [Windows エージェント](log-analytics-windows-agent.md) | いいえ  |直接の Windows エージェントでは、アラートは生成されません。  Log Analytics のアラートは、Windows エージェントによって収集されたイベントやパフォーマンス データから生成されます。 |
| [Linux エージェント](log-analytics-linux-agents.md) | いいえ  |直接の Linux エージェントでは、アラートは生成されません。  Log Analytics のアラートは、Linux エージェントによって収集されたイベントやパフォーマンス データから生成されます。  Nagios と Zabbix のアラートは、Linux エージェントを必要とするこれらのサーバーから収集されます。 |
| [System Center Operations Manager 管理グループ](log-analytics-om-agents.md) |[はい] |Operations Manager エージェントで生成されたアラートは管理グループに配信された後、Log Analytics に転送されます。<br><br>Operations Manager エージェントから Log Analytics への直接接続は必要ありません。 アラート データは管理グループから Log Analytics リポジトリに転送されます。 |


### <a name="collection-frequency"></a>収集の頻度
- アラート レコードは、リポジトリに格納されるとすぐにソリューションで利用可能になります。
- アラート データは 3 分おきに Operations Manager 管理グループから Log Analytics に送信されます。  

## <a name="using-the-solution"></a>ソリューションの使用
Log Analytics ワークスペースに Alert Management ソリューションを追加すると、ダッシュボードに **[Alert Management]** タイルが追加されます。  このタイルには、過去 24 時間に生成された現在アクティブなアラートの数とグラフが表示されます。  この時間範囲を変更することはできません。

![Alert Management tile](media/log-analytics-solution-alert-management/tile.png)

**[Alert Management]** (アラート管理) タイルをクリックすると、**[Alert Management]** (アラート管理) ダッシュボードが表示されます。  ダッシュボードには、次の表に示した列が存在します。  それぞれの列には、特定のスコープと時間範囲について、その列の基準に該当するアラート数の上位 10 件が表示されます。  ログ検索を実行してアラート全件を取得するには、列の一番下にある **[See all]** (すべて表示) をクリックするか、列ヘッダーをクリックします。

| 分割 | 説明 |
|:--- |:--- |
| 重大なアラート |重大度が "重大" であるすべてのアラートがその名前別に表示されます。  アラート名をクリックするとログの検索が実行され、そのアラートに該当するすべてのレコードが返されます。 |
| 警告アラート |重大度が "警告" であるすべてのアラートがその名前別に表示されます。  アラート名をクリックするとログの検索が実行され、そのアラートに該当するすべてのレコードが返されます。 |
| アクティブ SCOM アラート |Operations Manager によって収集された *[Closed]\(終了\)* 状態を除くすべてのアラートが、その生成元ごとにグループ化されて表示されます。 |
| すべてのアクティブなアラート |重大度に関係なくすべてのアラートがその名前別に表示されます。 対象となるのは *[Closed]\(終了\)* 状態以外の Operations Manager アラートだけです。 |

右へスクロールすると、使用頻度の高いいくつかのクエリがダッシュボードに一覧表示されます。そのクエリをクリックすると、アラート データを探すための[ログ検索](log-analytics-log-searches.md)が実行されます。

![Alert Management dashboard](media/log-analytics-solution-alert-management/dashboard.png)


## <a name="log-analytics-records"></a>Log Analytics のレコード
アラート管理ソリューションでは、 **Alert**タイプのすべてのレコードが分析されます。  Log Analytics によって生成されたアラートや、Nagios または Zabbix から収集されたアラートが直接収集されるわけではありません。

アラートは System Center Operations Manager からインポートされ、タイプを **Alert**、SourceSystem を **OpsManager** として、それぞれ対応するレコードが作成されます。  これらのレコードは、次の表に示したプロパティを持ちます。  

| プロパティ | 説明 |
|:--- |:--- |
| type |*アラート:* |
| SourceSystem |*OpsManager* |
| AlertContext |アラートが生成されるきっかけとなったデータ項目の詳細 (XML 形式)。 |
| AlertDescription |アラートの詳しい説明。 |
| AlertId |アラートの GUID。 |
| AlertName |アラートの名前。 |
| AlertPriority |アラートの優先度。 |
| AlertSeverity |アラートの重大度。 |
| AlertState |アラートの最新の解決状態。 |
| LastModifiedBy |アラートを最後に変更したユーザーの名前。 |
| ManagementGroupName |アラートが生成された管理グループの名前。 |
| RepeatCount |同じ監視対象オブジェクトについて、同じアラートが前回の解決以降に生成された回数。 |
| ResolvedBy |アラートを解決したユーザーの名前。 アラートが未解決の場合は空になります。 |
| SourceDisplayName |アラートの生成元となった監視オブジェクトの表示名。 |
| SourceFullName |アラートの生成元となった監視オブジェクトの完全名。 |
| TicketId |アラートのチケット ID (System Center Operations Manager 環境がアラートのチケット割り当てプロセスに統合されている場合)。  チケット ID が割り当てられていない場合は空になります。 |
| TimeGenerated |アラートが作成された日付と時刻。 |
| TimeLastModified |アラートが最後に変更された日付と時刻。 |
| TimeRaised |アラートが生成された日付と時刻。 |
| TimeResolved |アラートが解決された日付と時刻。 アラートが未解決の場合は空になります。 |

## <a name="sample-log-searches"></a>サンプル ログ検索
以下の表は、このソリューションによって収集されたアラート レコードを探すログ検索の例です。 

| クエリ | 説明 |
|:---|:---|
| Alert &#124; where SourceSystem == "OpsManager" and AlertSeverity == "error" and TimeRaised > ago(24h) |過去 24 時間以内に発生した重大なアラート |
| Alert &#124; where AlertSeverity == "warning" and TimeRaised > ago(24h) |過去 24 時間以内に発生した警告アラート |
| Alert &#124; where SourceSystem == "OpsManager" and AlertState != "Closed" and TimeRaised > ago(24h) &#124; summarize Count = count() by SourceDisplayName |過去 24 時間以内に発生したアクティブなアラートが存在するソース |
| Alert &#124; where SourceSystem == "OpsManager" and AlertSeverity == "error" and TimeRaised > ago(24h) and AlertState != "Closed" |過去 24 時間以内に発生し、まだ解決されていない重大なアラート |
| Alert &#124; where SourceSystem == "OpsManager" and TimeRaised > ago(24h) and AlertState == "Closed" |過去 24 時間以内に発生したものの、既に解決されている重大なアラート |
| Alert &#124; where SourceSystem == "OpsManager" and TimeRaised > ago(1d) &#124; summarize Count = count() by AlertSeverity |過去 1 日以内に発生したアラートを重大度に基づいてグループ化 |
| Alert &#124; where SourceSystem == "OpsManager" and TimeRaised > ago(1d) &#124; sort by RepeatCount desc |過去 1 日以内に発生したアラートを RepeatCount の値で並べ替え |



## <a name="next-steps"></a>次の手順
* Log Analytics におけるアラートの生成について詳しくは、 [Log Analytics のアラート](log-analytics-alerts.md) に関するページを参照してください。
