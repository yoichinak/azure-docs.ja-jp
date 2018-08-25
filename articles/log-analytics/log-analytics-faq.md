---
title: Log Analytics についてよく寄せられる質問 | Microsoft Docs
description: Azure Log Analytics サービスについてよく寄せられる質問とその回答
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ad536ff7-2c60-4850-a46d-230bc9e1ab45
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: eb1a60ff533e9e24f3dc80057129da47a2d9a726
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128532"
---
# <a name="log-analytics-faq"></a>Log Analytics についてよく寄せられる質問
この Microsoft FAQ は、Microsoft Azure の Log Analytics についてよく寄せられる質問の一覧です。 Log Analytics に関して何か追加の質問がある場合は、[ディスカッション フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights)にアクセスして質問を投稿してください。 よく寄せられる質問については、すばやく簡単に見つけることができるように、この記事に追加していきます。

## <a name="general"></a>全般

### <a name="q-does-log-analytics-use-the-same-agent-as-azure-security-center"></a>Q. Log Analytics で使用されるエージェントは Azure Security Center のエージェントと同じですか?

A. 2017 年 6 月上旬より、Azure Security Center では、Microsoft Monitoring Agent を使用してデータの収集と格納を行っています。 詳細については、[Azure Security Center のプラットフォームの移行についての FAQ](../security-center/security-center-platform-migration-faq.md) に関するページを参照してください。

### <a name="q-what-checks-are-performed-by-the-ad-and-sql-assessment-solutions"></a>Q. AD および SQL Assessment ソリューションでは、どのようなチェックが行われますか?

A. 次のクエリは、現在実行されているすべてのチェックの説明を示します。

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

結果を Excel にエクスポートしてレビューすることができます。

### <a name="q-why-do-i-see-something-different-than-oms-in-the-system-center-operations-manager-console"></a>Q: System Center Operations Manager コンソールに OMS とは異なる内容が表示されるのはなぜですか?

A: 使用している Operations Manager の更新プログラム ロールアップに応じて、*System Center Advisor*、*Operational Insights*、または *Log Analytics* のノードが表示される可能性があります。

管理パックでは *OMS* のテキスト文字列が更新されており、手動でインポートする必要があります。 現在のテキストと機能を表示するには、最新の System Center Operations Manager 更新プログラム ロールアップ KB 記事の手順に従い、コンソールを更新してください。

### <a name="q-is-there-an-on-premises-version-of-log-analytics"></a>Q: オンプレミス バージョンの Log Analytics はありますか?

A: いいえ。 Log Analytics は、大量のデータを処理および格納するスケーラブルなクラウド サービスです。 

### <a name="q-how-do-i-troubleshoot-if-log-analytics-is-no-longer-collecting-data"></a>Q. Log Analytics がデータを収集しなくなった場合にトラブルシューティングするにはどうすればよいですか?

A: 2018 年 4 月 2 日より前に作成された、*Free* 価格レベルのサブスクリプションとワークスペースの場合、1 日のデータ送信量が 500 MB を超えると、その日はそれ以上データが収集されなくなります。 1 日の制限に達したことが、Log Analytics がデータの収集を停止したり、データがないように見えたりする一般的な原因です。  

Log Analytics は*ハートビート*の種類のイベントを作成し、データ収集が停止するかどうかを判定するために使用できます。 

1 日の制限に達し、データがなくなっているかどうかを確認するには、検索で `Heartbeat | summarize max(TimeGenerated)` のクエリを実行します。

特定のコンピューターを確認するには、クエリ `Heartbeat | where Computer=="contosovm" | summarize max(TimeGenerated)` を実行します。

データ収集が停止したとき、選択された時間の範囲によっては、レコードが何も返されません。   

次の表は、データ収集が停止する原因と、データ収集を再開するための推奨されるアクションを示しています。

| データ収集が停止する原因                       | データ収集を再開するには |
| -------------------------------------------------- | ----------------  |
| 無料のデータの制限に達した<sup>1</sup>       | 収集が自動的に再開される次の月まで待つか、または<br> 有料の価格レベルに変更する |
| 次のために、Azure サブスクリプションが中断された状態にある <br> 無料試用版が終了した <br> Azure パスの期限が切れた <br> 1 月の使用制限に達した (たとえば、MSDN または Visual Studio サブスクリプションで)                          | 有料のサブスクリプションに変換する <br> 有料のサブスクリプションに変換する <br> 制限を削除するか、または制限がリセットされるまで待つ |

<sup>1</sup> ワークスペースで *Free* 価格レベルを使用している場合、サービスへの 1 日あたりのデータ送信は 500 MB に制限されます。 1 日の制限に達した場合、データ収集は翌日まで停止します。 データ収集が停止されている間に送信されたデータはインデックスが作成されず、検索には使用できません。 データ収集が再開されたとき、処理は送信された新しいデータに対してのみ実行されます。 

Log Analytics は UTC 時間を使用し、各日は UTC 午前 0 時に開始されます。 ワークスペースが 1 日の制限に達した場合、処理は次の UTC 日の最初の 1 時間の間に再開されます。

### <a name="q-how-can-i-be-notified-when-data-collection-stops"></a>Q. データ収集が停止したときに通知を受けるにはどうすればよいですか?

A: データ収集が停止したときに通知を受けるには、[新しいログ アラートの作成](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)に関する記事で説明されている手順を使用します。

データ収集が停止したときのアラートを作成する場合は、次のように設定します。

- **[アラートの条件を定義します]** では、リソース ターゲットとして Log Analytics ワークスペースを指定します。
- **[アラートの条件]** では、以下を指定します。
   - **[シグナル名]** では、**[カスタム ログ検索]** を選択します。
   - **[検索クエリ]**: `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **[アラート ロジック]** は "*結果の数*" **に基づき、****[条件]** は**しきい値**の *0* "*より大きい*" です
   - **[期間]** は *30* 分、**[アラートの頻度]** は *10* 分間隔に設定します
- **[アラートの詳細を定義します]** では、以下を指定します。
   - **[名前]** を *[Data collection stopped] \(データ収集が停止した)* に
   - **[重大度]**: *[警告]*

既存の[アクション グループ](../monitoring-and-diagnostics/monitoring-action-groups.md)を指定するか、アクション グループを新たに作成して、ログ アラートが条件に一致する場合に、ハートビートが 15 分以上なければ通知が送られるようにします。

## <a name="configuration"></a>構成
### <a name="q-can-i-change-the-name-of-the-tableblob-container-used-to-read-from-azure-diagnostics-wad"></a>Q. Azure 診断 (WAD) からの読み取りに使用されるテーブル/BLOB コンテナーの名前は変更できますか?

A. いいえ。Azure ストレージ内の任意のテーブルまたはコンテナーから読み取ることは現在不可能です。

### <a name="q-what-ip-addresses-does-the-log-analytics-service-use-how-do-i-ensure-that-my-firewall-only-allows-traffic-to-the-log-analytics-service"></a>Q. Log Analytics サービスはどのような IP アドレスを使用しますか? ファイアウォールが確実に Log Analytics サービスへのトラフィックのみを許可するようにするにはどうすればよいですか?

A. Log Analytics サービスは、Azure に基づいて構築されています。 Log Analytics の IP アドレスは、[Microsoft Azure データセンターの IP 範囲](http://www.microsoft.com/download/details.aspx?id=41653)内にあります。

サービスのデプロイが行われると、Log Analytics サービスの実際の IP アドレスは変更されます。 ファイアウォールの通過を許可する DNS 名については、[ネットワーク要件](log-analytics-concept-hybrid.md#network-firewall-requirements)に関するセクションをご覧ください。

### <a name="q-i-use-expressroute-for-connecting-to-azure-does-my-log-analytics-traffic-use-my-expressroute-connection"></a>Q. Azure への接続に ExpressRoute を使用しています。 Log Analytics トラフィックは、この ExpressRoute 接続を使用しますか?

A. 各種の ExpressRoute トラフィックについては、[ExpressRoute のドキュメント](../expressroute/expressroute-faqs.md#supported-services)を参照してください。

Log Analytics へのトラフィックには、パブリックピアリング ExpressRoute 回路が使用されます。

### <a name="q-is-there-a-simple-and-easy-way-to-move-an-existing-log-analytics-workspace-to-another-log-analytics-workspaceazure-subscription"></a>Q. 既存の Log Analytics ワークスペースを別の Log Analytics ワークスペース/Azure サブスクリプションに移動するためのシンプルで簡単な方法はありますか?

A. `Move-AzureRmResource` コマンドレットを使用すると、Log Analytics ワークスペースを移動できるほか、Automation アカウントを Azure サブスクリプション間で移動することもできます。 詳細については、「[Move-AzureRmResource](http://msdn.microsoft.com/library/mt652516.aspx)」を参照してください。

この変更は、Azure ポータルで行うこともできます。

ある Log Analytics ワークスペースから別のワークスペースにデータを移行したり、Log Analytics データが格納されているリージョンを変更したりすることはできません。

### <a name="q-how-do-i-add-log-analytics-to-system-center-operations-manager"></a>Q: System Center Operations Manager に Log Analytics を追加するにはどうすればよいですか?

A: 最新の更新プログラム ロールアップに更新し、管理パックをインポートすると、Operations Manager を Log Analytics に接続できます。

>[!NOTE]
>Log Analytics への Operations Manager 接続は、System Center Operations Manager 2012 SP1 以降でのみ使用できます。

### <a name="q-how-can-i-confirm-that-an-agent-is-able-to-communicate-with-log-analytics"></a>Q: エージェントが Log Analytics と通信できることを確認するにはどうすればよいですか?

A: エージェントが OMS と通信できることを確認するには、[コントロール パネル]、[システムとセキュリティ]、**[Microsoft Monitoring Agent]** の順に選択します。

**[Azure Log Analytics (OMS)]** タブの緑色のチェック マークを確認します。 緑色のチェック マーク アイコンは、エージェントが Azure サービスと通信できることを示します。

黄色の警告アイコンは、エージェントと Log Analytics の通信に問題が発生していることを示します。 1 つの一般的な原因は、Microsoft Monitoring Agent サービスが停止したことです。 サービス コントロール マネージャーを使用してサービスを再開します。

### <a name="q-how-do-i-stop-an-agent-from-communicating-with-log-analytics"></a>Q: エージェントの Log Analytics との通信を停止するにはどうすればよいですか?

A: System Center Operations Manager では、OMS で管理されたコンピューターの一覧からコンピューターを削除します。 Operations Manager はエージェントの構成を更新して、Log Analytics に報告しなくなるようにします。 Log Analytics に直接接続されているエージェントの場合は、[コントロール パネル]、[システムとセキュリティ]、**[Microsoft Monitoring Agent]** の順に選択して、エージェントの通信を停止できます。
**[Azure Log Analytics (OMS)]** に表示されているすべてのワークスペースを削除します。

### <a name="q-why-am-i-getting-an-error-when-i-try-to-move-my-workspace-from-one-azure-subscription-to-another"></a>Q: ワークスペースを Azure サブスクリプション間で移動しようとするとエラーが表示されるのはなぜですか?

A: Azure Portal を使用している場合は、移動用にワークスペースのみが選択されていることを確認してください。 ソリューションを選択しないでください。これらは、ワークスペースが移動された後に自動的に移動されます。 

両方の Azure サブスクリプションにアクセス許可があることを確認してください。

## <a name="agent-data"></a>エージェント データ
### <a name="q-how-much-data-can-i-send-through-the-agent-to-log-analytics-is-there-a-maximum-amount-of-data-per-customer"></a>Q. エージェント経由で Log Analytics に送信できるデータ量はどのくらいですか? 顧客ごとの最大データ量はありますか?
A. 無料プランには、1 つのワークスペースにつき 1 日 500 MB という上限があります。 Standard プランと Premium プランの場合、アップロードされるデータ量に上限はありません。 Log Analytics はクラウド サービスとして、顧客から送信される量が 1 日あたり数 TB であったとしても、それを処理するように自動的にスケールアップするように設計されています。

Log Analytics エージェントは、小さなフットプリントを保証するように設計されました。 データ量は、有効にしているソリューションによって異なります。 [[使用状況]](log-analytics-usage.md) ページでデータ量に関する詳細情報を見つけたり、ソリューションごとの内訳を確認したりできます。

詳しくは、OMS エージェントのリソース使用率 (フットプリント) の評価結果が示されている[お客様のブログ](http://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html)をご覧ください。

### <a name="q-how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-log-analytics"></a>Q. データを Log Analytics に送信するときに、どれくらいのネットワーク帯域幅が Microsoft Management Agent (MMA) によって使用されますか?

A. 帯域幅は、送信されるデータ量に関する機能です。 データは、ネットワーク経由で送信されるときに圧縮されます。

### <a name="q-how-much-data-is-sent-per-agent"></a>Q. エージェントごとにどれくらいのデータが送信されますか?

A. エージェントごとに送信されるデータ量は、次のものによって異なります。

* 有効にしているソリューション
* 収集されているログとパフォーマンス カウンターの数
* ログ内のデータ量

いくつかのサーバーをデプロイし、一般的なデータ量を計測する場合は、無料価格レベルがお勧めします。 全体的な使用状況は [[使用状況]](log-analytics-usage.md) ページに表示されます。

WireData エージェントを実行できるコンピューターの場合、どれくらいのデータが送信されているかを確認するには次のクエリを使用します。

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```

## <a name="next-steps"></a>次の手順
* ドキュメントで [Log Analytics の詳細を確認し](log-analytics-get-started.md)、お試しください。
