---
title: Azure Log Analytics のネットワーク パフォーマンス モニター ソリューション | Microsoft Docs
description: Network Performance Monitor の ExpressRoute モニター機能を使用して、ブランチ オフィスと Azure の間のエンド ツー エンド接続とパフォーマンスを Azure ExpressRoute 経由で監視します。
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
ms.openlocfilehash: 27169193a468d98be879164b80e63fffde419002
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38633351"
---
# <a name="expressroute-monitor"></a>ExpressRoute モニター

[Network Performance Monitor](log-analytics-network-performance-monitor.md) の Azure ExpressRoute モニター機能を使用して、ブランチ オフィスと Azure の間のエンド ツー エンド接続とパフォーマンスを Azure ExpressRoute 経由で監視できます。 主な利点は次のとおりです。 

- サブスクリプションに関連付けられている ExpressRoute 回線の自動検出。
- ExpressRoute の帯域幅使用率、回線での損失と待ち時間、ピアリングと Azure Virtual Network のレベルの追跡。
- ExpressRoute 回線のネットワーク トポロジの検出。

![ExpressRoute モニター](media/log-analytics-network-performance-monitor/expressroute-intro.png)

## <a name="configuration"></a>構成 
Network Performance Monitor の構成を開くには、[Network Performance Monitor ソリューション](log-analytics-network-performance-monitor.md)を開き、**[構成]** を選びます。

### <a name="configure-network-security-group-rules"></a>ネットワーク セキュリティ グループ規則を構成する 
Network Performance Monitor による監視に使われる Azure 内のサーバーについては、Network Performance Monitor の代理トランザクションに使われるポートの TCP トラフィックを許可するようにネットワーク セキュリティ グループ (NSG) 規則を構成します。 既定のポートは 8084 です。 この構成により、Azure VM にインストールされている Operations Management Suite エージェントは、オンプレミスの監視エージェントと通信できます。 

NSG の詳細については、 [ネットワーク セキュリティ グループ](../virtual-network/manage-network-security-group.md)に関するページを参照してください。 

>[!NOTE]
> この手順を続行する前に、オンプレミスのサーバー エージェントと Azure サーバー エージェントをインストールし、EnableRules.ps1 PowerShell スクリプトを実行します。 

 
### <a name="discover-expressroute-peering-connections"></a>ExpressRoute ピアリング接続の検出 
 
1. **[ExpressRoute ピアリング]** ビューを選びます。
2. **[今すぐ検出する]** を選び、この Azure Log Analytics ワークスペースにリンクされている Azure サブスクリプション内の仮想ネットワークに接続されているすべての ExpressRoute プライベート ピアリングを検出します。

    >[!NOTE]
    > このソリューションでは、現時点では ExpressRoute プライベート ピアリングしか検出されません。 

    >[!NOTE]
    > 検出されるのは、この Log Analytics ワークスペースにリンクされているサブスクリプションに関連付けられている仮想ネットワークに接続されているプライベート ピアリングのみです。 ExpressRoute が、このワークスペースにリンクされているサブスクリプションの外部の仮想ネットワークに接続されている場合は、これらのサブスクリプションに Log Analytics ワークスペースを作成します。 その後、Network Performance Monitor を使ってこれらのピアリングを監視します。 

    ![ExpressRoute Monitor の構成](media/log-analytics-network-performance-monitor/expressroute-configure.png)
 
 検出が完了した後、検出されたプライベート ピアリング接続がテーブルに一覧表示されます。 これらのピアリングの監視は、最初は無効状態になっています。 

### <a name="enable-monitoring-of-the-expressroute-peering-connections"></a>ExpressRoute ピアリング接続の監視を有効にする 

1. 監視するプライベート ピアリング接続を選びます。
2. 右側のウィンドウで、**[このピアリングを監視する]** チェック ボックスをオンにします。 
3. この接続の正常性イベントを作成する場合は、**[このピアリングの正常性監視を有効にする]** をオンにします。 
4. 監視条件を選択します。 しきい値を入力して、正常性イベントの生成に関するカスタムしきい値を設定できます。 ピアリング接続に対して選択したしきい値を条件の値が上回ると、正常性イベントが生成されます。 
5. **[エージェントの追加]** を選び、このピアリング接続を監視するために使う監視エージェントを選びます。 接続の両端にエージェントを追加したことを確認します。 このピアリングに接続されている仮想ネットワークには少なくとも 1 つのエージェントが必要です。 また、少なくとも 1 つのオンプレミスのエージェントがこのピアリングに接続されている必要もあります。 
6. **[保存]** を選んで構成を保存します。 

   ![ExpressRoute の監視の構成](media/log-analytics-network-performance-monitor/expressroute-configure-discovery.png)


規則を有効にして値とエージェントを選んだ後、値が反映されて **[ExpressRoute の監視]** タイルが表示されるまで、30 ～ 60 分間待ちます。 監視タイルが表示されると、ExpressRoute 回線と接続リソースは Network Performance Monitor によって監視されています。 

>[!NOTE]
> この機能は、新しいクエリ言語にアップグレードされたワークスペースで確実に機能します。

## <a name="walkthrough"></a>チュートリアル 

Network Performance Monitor ダッシュ ボードには、ExpressRoute 回線とピアリング接続の正常性の概要が表示されます。 

![ネットワーク パフォーマンス モニターのダッシュボード](media/log-analytics-network-performance-monitor/npm-dashboard-expressroute.png) 

### <a name="circuits-list"></a>回線の一覧 

監視対象の全 ExpressRoute 回線の一覧を表示するには、[ExpressRoute 回線] タイルを選びます。 いずれかの回線を選択すると、その正常性状態のほか、パケット損失、帯域幅使用率、待ち時間の各トレンド グラフを表示できます。 これらのグラフは対話操作が可能です。 グラフのプロット対象となる時間枠を自分で選んでカスタマイズすることができます。 データ ポイントを拡大し、細かく表示するには、グラフ上の領域でマウスをドラッグします。 

![ExpressRoute 回線の一覧](media/log-analytics-network-performance-monitor/expressroute-circuits.png) 

### <a name="trends-of-loss-latency-and-throughput"></a>パケット損失、待ち時間、スループットのトレンド 

帯域幅使用状況、待ち時間、損失の各グラフは対話操作が可能です。 これらのグラフの任意のセクションをマウス操作で拡大することができます。 帯域幅、待機時間、および損失データを他の間隔で表示することもできます。 左上の **[アクション]** ボタンで  **[日付/時刻]** を選びます。 

![ExpressRoute の待機時間](media/log-analytics-network-performance-monitor/expressroute-latency.png) 

### <a name="peerings-list"></a>ピアリング一覧 

プライベート ピアリング上の仮想ネットワークに対するすべての接続の一覧を表示するには、ダッシュボードの **[プライベート ピアリング]** タイルを選びます。 ここでいずれかの仮想ネットワークの接続を選択すると、その正常性状態のほか、パケット損失、帯域幅使用率、待ち時間の各トレンド グラフを表示できます。 

![ExpressRoute ピアリング](media/log-analytics-network-performance-monitor/expressroute-peerings.png) 

### <a name="circuit-topology"></a>回線トポロジ 

回線トポロジを表示するには、**[トポロジ]** タイルを選びます。 この操作により、選択した回線またはピアリングのトポロジ ビューが表示されます。 トポロジ ダイアグラムには、ネットワーク上の各セグメントの待ち時間が表示され、各レイヤー 3 ホップがダイアグラムのノードで表現されます。 いずれかのホップを選ぶと、そのホップについてのさらに詳しい情報が表示されます。 視認性を高めて表示範囲をオンプレミスのホップにまで広げるには、**[フィルター]** の下のスライダー バーを動かします。 スライダー バーを左右に動かすと、トポロジ グラフに表示されるホップ数が増減します。 各セグメントの待ち時間が視覚的に確認できるので、ネットワーク上のセグメントの中で、待ち時間の長いセグメントを短時間で切り分けることができます。 

![ExpressRoute のトポロジ](media/log-analytics-network-performance-monitor/expressroute-topology.png)

### <a name="detailed-topology-view-of-a-circuit"></a>回線の詳細なトポロジ ビュー 

このビューには、仮想ネットワーク接続が表示されます。 

![ExpressRoute の仮想ネットワーク接続](media/log-analytics-network-performance-monitor/expressroute-vnet.png)
 

### <a name="diagnostics"></a>診断 

Network Performance Monitor は、いくつかの回線接続の問題を診断するのに役立ちます。 問題の一部を次に示します。 

**回線がダウンしている。** Network Performance Monitor は、オンプレミスのリソースと Azure 仮想ネットワーク間の接続が失われるとすぐに通知します。 この通知により、ユーザーからエスカレーションを受ける前に予防的なアクションを実行し、ダウンタイムを短縮できます。

![ExpressRoute 回線がダウンしている](media/log-analytics-network-performance-monitor/expressroute-circuit-down.png)
 

**トラフィックが目的の回線経由で流れていない。** Network Performance Monitor は、意図した ExpressRoute 回線をトラフィックが流れないときは常に通知します。 この問題は、回路がダウンし、トラフィックがバックアップ ルート経由で流れている場合に発生することがあります。 ルーティングの問題がある場合にも発生する可能性があります。 この情報は、ルーティング ポリシーの構成の問題を予防的に管理し、最適かつ最も安全なルートが使用されるようにするために役立ちます。 

 

**トラフィックがプライマリ回線経由で流れていない。** Network Performance Monitor は、トラフィックがセカンダリの ExpressRoute 回線経由で流れている場合に通知します。 この場合は接続性の問題は発生しませんが、プライマリ回線の問題の予防的なトラブルシューティングを行うと、より入念に準備を整えることができます。 

 
![ExpressRoute トラフィック フロー](media/log-analytics-network-performance-monitor/expressroute-traffic-flow.png)


**ピーク時の使用率によるパフォーマンス低下。** 帯域幅使用率の傾向を待ち時間の傾向と関連付けて、Azure ワークロードのパフォーマンス低下が帯域幅使用率のピークによるものかどうかを識別します。 その後、それに応じてアクションを実行することができます。

![ExpressRoute の帯域幅の使用率](media/log-analytics-network-performance-monitor/expressroute-peak-utilization.png)

 

## <a name="next-steps"></a>次の手順
詳細なネットワーク パフォーマンスのデータ レコードを表示するために、[ログを検索](log-analytics-log-searches.md)します。
