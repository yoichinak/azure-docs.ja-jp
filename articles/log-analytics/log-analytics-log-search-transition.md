---
title: Azure Log Analytics クエリ言語チート シート | Microsoft Docs
description: この記事は、従来 Log Analytics に使用されてきたクエリ言語の使用経験がある方を対象に、新しいクエリ言語への移行を支援する内容となっています。
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2017
ms.author: bwren
ms.component: na
ms.openlocfilehash: 7c2158d8e6f64c7c356ba40b3bf56684f00cb8c0
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37133512"
---
# <a name="transitioning-to-azure-log-analytics-new-query-language"></a>Azure Log Analytics の新しいクエリ言語への移行
最近、Log Analytics は新しいクエリ言語を実装しました。  この記事は、従来のクエリ言語を使い慣れていて、ある程度のサポートが必要な方を対象に、Log Analytics のこの言語への移行を支援する内容となっています。

## <a name="resources"></a>リソース


## <a name="language-converter"></a>言語コンバーター

従来の Log Analytics クエリ言語を使用した経験がある場合、同じクエリを新しい言語で作成するには、ワークスペースを変換するときにログ検索ポータルにインストールされる言語コンバーターを使用するのが最も簡単です。  コンバーターの使い方は簡単です。最上部のテキスト ボックスに従来のクエリを入力して **[変換]** をクリックするだけです。  クエリは、検索ボタンをクリックして実行できるほか、クエリをコピーして他の場所に貼り付けて使用することもできます。

![言語コンバーター](media/log-analytics-log-search-upgrade/language-converter.png)


## <a name="resources"></a>リソース
[Log Analytics クエリ言語のドキュメント サイト](https://docs.loganalytics.io)には、新しい言語を短時間で学習するために必要なすべてのリソースがあります。  これには、チュートリアル、例、完全な言語リファレンスが含まれます。


## <a name="cheat-sheet"></a>チート シート

次の表は、Azure Log Analytics の新しいクエリ言語と従来のクエリ言語との間で、使用頻度の高いさまざまなクエリを同等のコマンドで比較したものです。

| 説明 | 従来 | new |
|:--|:--|:--|
| すべてのテーブルを検索      | error | search "error" (大文字と小文字の区別なし) |
| テーブルからデータを選択 | Type=Event |  Event |
|                        | Type=Event &#124; select Source, EventLog, EventID | Event &#124; project Source, EventLog, EventID |
|                        | Type=Event &#124; top 100 | Event &#124; take 100 |
| 文字列の比較      | Type=Event Computer=srv01.contoso.com   | Event &#124; where Computer == "srv01.contoso.com" |
|                        | Type=Event Computer=contains("contoso") | Event &#124; where Computer contains "contoso" (大文字と小文字の区別なし)<br>Event &#124; where Computer contains_cs "Contoso" (大文字と小文字の区別あり) |
|                        | Type=Event Computer=RegEx("\@contoso \@\")  | Event &#124; where Computer matches regex ".*contoso*" |
| 日付の比較        | Type=Event TimeGenerated > NOW-1DAYS | Event &#124; where TimeGenerated > ago(1d) |
|                        | Type=Event TimeGenerated>2017-05-01 TimeGenerated<2017-05-31 | Event &amp;#124; where TimeGenerated between (datetime(2017-05-01) . datetime(2017-05-31)) |
| ブール値の比較     | Type=Heartbeat IsGatewayInstalled=false  | Heartbeat \| where IsGatewayInstalled == false |
| 並べ替え                   | Type=Event &#124; sort Computer asc, EventLog desc, EventLevelName asc | Event \| sort by Computer asc, EventLog desc, EventLevelName asc |
| 重複の除外               | Type=Event &#124; dedup Computer \| select Computer | Event &#124; summarize by Computer, EventLog |
| 列の拡張         | Type=Perf CounterName="% Processor Time" &#124; EXTEND if(map(CounterValue,0,50,0,1),"HIGH","LOW") as UTILIZATION | Perf &#124; where CounterName == "% Processor Time" \| extend Utilization = iff(CounterValue > 50, "HIGH", "LOW") |
| 集計            | Type=Event &#124; measure count() as Count by Computer | Event &#124; summarize Count = count() by Computer |
|                                | Type=Perf ObjectName=Processor CounterName="% Processor Time" &#124; measure avg(CounterValue) by Computer interval 5minute | Perf &#124; where ObjectName=="Processor" and CounterName=="% Processor Time" &#124; summarize avg(CounterValue) by Computer, bin(TimeGenerated, 5min) |
| 制限付きの集計 | Type=Event &#124; measure count() by Computer &#124; top 10 | Event &#124; summarize AggregatedValue = count() by Computer &#124; limit 10 |
| 和集合                  | Type=Event or Type=Syslog | union Event, Syslog |
| Join (結合)                   | Type=NetworkMonitoring &#124; join inner AgentIP (Type=Heartbeat) ComputerIP | NetworkMonitoring &#124; join kind=inner (search Type == "Heartbeat") on $left.AgentIP == $right.ComputerIP |



## <a name="next-steps"></a>次の手順
- 新しいクエリ言語を使用した[クエリ記述のチュートリアル](https://go.microsoft.com/fwlink/?linkid=856078)を確認します。
- 新しいクエリ言語で使用されるすべてのコマンド、演算子、関数の詳細については、[クエリ言語リファレンス](https://go.microsoft.com/fwlink/?linkid=856079)を参照してください。  
