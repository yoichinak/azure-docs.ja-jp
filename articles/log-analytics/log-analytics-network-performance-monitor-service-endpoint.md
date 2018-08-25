---
title: Azure Log Analytics のネットワーク パフォーマンス モニター ソリューション | Microsoft Docs
description: Network Performance Monitor のサービス接続モニター機能を使って、TCP ポートが開いている任意のエンドポイントへのネットワーク接続を監視します。
services: log-analytics
documentationcenter: ''
author: abshamsft
manager: carmonm
editor: ''
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/20/2018
ms.author: abshamsft
ms.component: na
ms.openlocfilehash: 3c9352e8e4aee7817b1195c15f74503e86e597ea
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37434921"
---
# <a name="service-connectivity-monitor"></a>サービス接続モニター

[Network Performance Monitor](log-analytics-network-performance-monitor.md) のサービス接続モニター機能を使って、TCP ポートが開いている任意のエンドポイントへのネットワーク接続を監視することができます。 対象となるエンドポイントには、Web サイト、SaaS アプリケーション、PaaS アプリケーション、および SQL データベースが含まれます。 

サービス接続モニターを使用して次の機能を実行できます。 

- 複数のブランチ オフィスまたは場所からアプリケーションおよびネットワーク サービスへのネットワーク接続を監視します。 アプリケーションやネットワーク サービスには、Office 365、Dynamics CRM、内部の基幹業務アプリケーション、SQL データベースなどが含まれます。
- 組み込みテストを使って、Office 365 および Dynamics365 エンドポイントへのネットワーク接続を監視します。 
- エンドポイントへの接続時に発生した応答時間、ネットワーク待機時間、パケット損失を確認します。
- アプリケーション パフォーマンスの低下がネットワークに起因するものなのか、それともアプリケーション プロバイダー側の問題によるものなのかを確認します。
- トポロジ マップ上の各ホップの影響を受けている待機時間を表示することによって、アプリケーション パフォーマンスの低下の原因となっている可能性があるネットワーク上のホット スポットを特定します。


![サービス接続モニター](media/log-analytics-network-performance-monitor/service-endpoint-intro.png)


## <a name="configuration"></a>構成 
Network Performance Monitor の構成を開くには、[Network Performance Monitor ソリューション](log-analytics-network-performance-monitor.md)を開き、**[構成]** を選びます。

![Network Performance Monitor の構成](media/log-analytics-network-performance-monitor/npm-configure-button.png)


### <a name="configure-operations-management-suite-agents-for-monitoring"></a>監視用に Operations Management Suite エージェントを構成する
監視に使うノードで次のファイアウォール規則を有効にして、ノードからサービス エンドポイントまでのトポロジを探索できるようにします。 

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
```

### <a name="create-service-connectivity-monitor-tests"></a>サービス接続モニター テストを作成する 

サービス エンドポイントへのネットワーク接続を監視するためのテストを作成します。

1. **[サービス接続モニター]** タブを選択します。
2. **[テストの追加]** を選び、テストの名前と説明を入力します。 
3. テストの種類を選択します。<br>

    * HTTP/S 要求に応答するサービス (outlook.office365.com、bing.com など) への接続を監視するには、**[Web]** を選びます。<br>
    * TCP 要求に応答し、HTTP/S 要求には応答しないサービス (SQL サーバー、FTP サーバー、SSH ポートなど) への接続を監視するには、**[ネットワーク]** を選びます。 
4. ネットワーク待機時間、パケット損失、トポロジ検出などのネットワーク測定を実行したくない場合は、**[ネットワークの測定を実行します]** チェック ボックスをオフにします。 この機能のメリットを最大限に得るには、オンにままにしておきます。 
5. **[ターゲット]** に、ネットワーク接続を監視する URL/FQDN/IP アドレスを入力します。
6. **[ポート番号]** に、ターゲット サービスのポート番号を入力します。 
7. **[テストの頻度]** に、テストを実行する頻度の値を入力します。 
8. サービスへのネットワーク接続を監視するノードを選択します。 

    >[!NOTE]
    > Windows サーバー ベースのノードの場合、この機能では、TCP ベースの要求を使用してネットワーク測定が実行されます。 Windows クライアント ベースのノードの場合、この機能では、ICMP ベースの要求を使用してネットワーク測定が実行されます。 ノードが Windows クライアント ベースのときに、対象アプリケーションが ICMP ベースの着信要求をブロックすることがあります。 ソリューションはネットワーク測定を実行できません。 このような場合は、Windows サーバー ベースのノードを使うことをお勧めします。 

9. 選んだ項目の正常性イベントを作成したくない場合は、**[このテストでカバーされるターゲットで稼働状況の監視を有効にする]** をオフにします。 
10. 監視条件を選択します。 しきい値を入力して、正常性イベントの生成に関するカスタムしきい値を設定できます。 選択したネットワーク ペア/サブネットワーク ペアに対して選択したしきい値を条件の値が上回ると、正常性イベントが生成されます。 
11. **[保存]** を選んで構成を保存します。 

    ![サービス接続モニターのテスト構成](media/log-analytics-network-performance-monitor/service-endpoint-configuration.png)



## <a name="walkthrough"></a>チュートリアル 

Network Performance Monitor のダッシュボード ビューに移動します。 作成したさまざまなテストの正常性の概要については、**[サービス接続の監視]** ページで確認できます。 

![サービス接続モニター ページ](media/log-analytics-network-performance-monitor/service-endpoint-blade.png)

タイルを選び、**[テスト]** ページでテストの詳細を確認します。 左側のテーブルでは、すべてのテストについて、特定の時点での正常性と、サービス応答時間、ネットワーク待機時間、およびパケット損失の値を確認できます。 過去の別の時点のスナップショットを表示するには、[ネットワーク状態の記録機能] コントロールを使います。 テーブルで調査するテストを選びます。 右側のウィンドウのグラフでは、損失、待機時間、および応答時間の値に関する過去の傾向を確認できます。 **[テストの詳細]** リンクを選ぶと、各ノードからのパフォーマンスを確認できます。

![サービス接続モニターのテスト](media/log-analytics-network-performance-monitor/service-endpoint-tests.png)

**[テスト ノード]** ビューでは、各ノードからのネットワーク接続を確認できます。 パフォーマンスが低下しているノードを選びます。 これは、アプリケーションの実行速度が低下しているノードです。

アプリケーションの応答時間とネットワーク待機時間の相関関係を観察して、アプリケーション パフォーマンスの低下がネットワークに起因するものなのか、それともアプリケーション プロバイダー側の問題によるものなのかを確認します。 

* **アプリケーションの問題:** 応答時間の急増が見られる一方、ネットワーク待機時間は一貫している場合は、ネットワークは正常に動作しており、問題の原因はアプリケーション側にあると考えられます。 

    ![サービス接続モニターのアプリケーションの問題](media/log-analytics-network-performance-monitor/service-endpoint-application-issue.png)

* **ネットワークの問題:** 応答時間の急増に伴ってネットワーク待機時間も急増している場合、応答時間の増加は、ネットワーク待機時間の増加に起因していると考えられます。 

    ![サービス接続モニターのネットワークの問題](media/log-analytics-network-performance-monitor/service-endpoint-network-issue.png)

問題の原因がネットワークであると判断した後は、**[トポロジ]** ビュー リンクを選び、トポロジ マップ上で問題のホップを確認します。 次の図に例を示します。 ノードとアプリケーション エンドポイント間の合計待機時間である 105 ミリ秒のうち、96 ミリ秒は赤で囲まれたホップに起因しています。 問題のホップを特定したら、是正措置を実行できます。 

![サービス接続モニターのテスト](media/log-analytics-network-performance-monitor/service-endpoint-topology.png)

## <a name="diagnostics"></a>診断 

異常が確認された場合は、次の手順に従います。

* サービス応答時間、ネットワーク損失、および待機時間が NA と表示される場合は、次の 1 つ以上が原因として考えられます。

    - アプリケーションがダウンしている。
    - サービスへのネットワーク接続のチェックに使用されているノードがダウンしている。
    - テスト構成で入力したターゲットが正しくない。
    - ノードにネットワーク接続がない。

* 有効なサービス応答時間が表示されているものの、ネットワーク損失と待機時間が NA と表示される場合は、次の 1 つ以上が原因として考えられます。

    - サービスへのネットワーク接続のチェックに使用されたノードが Windows クライアント コンピューターである場合は、ターゲット サービスが ICMP 要求をブロックしているか、またはネットワーク ファイアウォールがノードからの ICMP 要求をブロックしている。
    - テスト構成で **[ネットワークの測定を実行します]** チェック ボックスがオフになっている。 

* サービス応答時間が NA で、ネットワーク損失と待機時間が有効な場合は、ターゲット サービスが Web アプリケーションではないことが考えられます。 テスト構成を編集し、テストの種類として、**[Web]** ではなく **[ネットワーク]** を選んでください。 

* アプリケーションの実行速度が遅い場合は、アプリケーション パフォーマンスの低下がネットワークに起因するものなのか、それともアプリケーション プロバイダー側の問題によるものなのかを確認します。


## <a name="next-steps"></a>次の手順
詳細なネットワーク パフォーマンスのデータ レコードを表示するために、[ログを検索](log-analytics-log-searches.md)します。
