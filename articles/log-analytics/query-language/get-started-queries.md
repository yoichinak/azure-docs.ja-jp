---
title: Azure Log Analytics のクエリの概要 | Microsoft Docs
description: この記事では、Log Analytics のクエリの基本的な記述方法のチュートリアルを提供します。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/06/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 71d50a55d9c584b61a1412bb03a03ad99f1bb96c
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39632914"
---
# <a name="get-started-with-queries-in-log-analytics"></a>Log Analytics のクエリの概要


> [!NOTE]
> このチュートリアルを完了する前に、[Analytics ポータルの概要](get-started-analytics-portal.md)に関するチュートリアルを完了する必要があります。


このチュートリアルでは、Azure Log Analytics クエリの記述方法について説明します。 以下の方法について説明します。

- クエリの構造の概要
- クエリ結果を並べ替える
- クエリ結果をフィルター処理する
- 時間の範囲を指定する
- 結果に含めるフィールドを選択する
- カスタム フィールドの定義と使用
- 結果の集計とグループ化


## <a name="writing-a-new-query"></a>新しいクエリの記述
クエリは、テーブル名または *search* コマンドから始めることができます。 まずテーブル名から始めます。これは、テーブル名でクエリの明確な範囲が定義され、クエリのパフォーマンスと結果の関連性の両方が向上するためです。

> [!NOTE]
> Azure Log Analytics のクエリ言語では大文字と小文字が区別されます。 通常、言語のキーワードは小文字で記述されます。 クエリ内でテーブルまたは列の名前を使用する場合は、スキーマ ウィンドウの表示に従って大文字と小文字を正しく指定します。

### <a name="table-based-queries"></a>テーブルベースのクエリ
Azure Log Analytics では、データはテーブルに編成され、各テーブルは複数の列で構成されます。 すべてのテーブルと列は、Analytics ポータルのスキーマ ウィンドウに表示されます。 目的のテーブルを指定子、データの一部を見てみます。

```OQL
SecurityEvent
| take 10
```

上記のクエリは、順不同で *SecurityEvent* から 10 個の結果を返します。 これは、テーブルの概要を把握し、その構造と内容を理解する場合に非常に一般的な方法です。 それでは、どのように構築されているかを見てみましょう。

* クエリはテーブル名 *SecurityEvent* で始まります。この部分で、クエリの範囲を定義します。
* 複数のコマンドは、パイプ (|) 文字で区切ります。そのため、最初のコマンドの出力は次のコマンドの入力になります。 パイプでつないだ要素は、任意の数を追加できます。
* パイプの後には **take** コマンドがあります。このコマンドは、テーブルから特定の数の任意のレコードを返します。

実際は `| take 10` を追加しなくてもクエリを実行できます。この方法は有効ですが、最大 10,000 件の結果が返される可能性があります。

### <a name="search-queries"></a>検索クエリ
検索クエリはあまり構造化されていないため、一般的に、任意の列に特定の値が含まれるレコードを検索する場合に適しています。

```OQL
search in (SecurityEvent) "Cryptographic"
| take 10
```

このクエリで、*SecurityEvent* テーブルの "Cryptographic" というフレーズが含まれるレコードが検索されます。 このようなレコードの中から、10 個のレコードが返され、表示されます。 `in (SecurityEvent)` の部分を省略して `search "Cryptographic"` を実行すると、*すべての*テーブルに対して検索が実行されます。この処理には時間がかかり、効率が良くありません。

> [!NOTE]
> 既定では、_過去 24 時間_の時間の範囲が設定されています。 別の範囲を使用するには、時刻の選択ツール (*[実行]* ボタンの横にあります) を使用するか、明示的な時間の範囲フィルターをクエリに追加します。

## <a name="sort-and-top"></a>sort と top
**take** は少数のレコードを取得する場合に便利ですが、結果は順不同で選択され、表示されます。 順序が設定されたビューを取得するには、優先する列で**並べ替え**ます。

```
SecurityEvent   
| sort by TimeGenerated desc
```

ただし、返される結果が多すぎる場合や、処理に時間もかかる場合があります。 上記のクエリで、SecurityEvent テーブル*全体*が TimeGenerated 列で並べ替えられます。 Analytics ポータルでは、表示できるレコードの上限は 10,000 個に制限されています。 Analytics ポータルでは、表示が 10,000 レコードに制限されます。

最新の 10 レコードのみを取得するには、**top** を使用する方法が最適です。サーバー側でテーブル全体が並べ替えられてから、上位のレコードが返されます。

```OQL
SecurityEvent
| top 10 by TimeGenerated
```

降順が既定の並べ替え順です。そのため、通常は **desc** の引数を省略します。出力は次のようになります。

![top 10](media/get-started-queries/top10.png)


## <a name="where-filtering-on-a-condition"></a>where: 条件に基づいてフィルター処理する
フィルターは、その名前のとおり、特定の条件に基づいてデータをフィルター処理します。 これは、クエリ結果を関連する情報に制限する最も一般的な方法です。

クエリにフィルターを追加するには、**where** 演算子に続けて 1 つまたは複数の条件を使用します。 たとえば、次のクエリは、_Level_ が _8_ に等しい *SecurityEvent* レコードのみを返します。

```OQL
SecurityEvent
| where Level == 8
```

フィルター条件を記述する場合は、次の式を使用できます。

| 式 | 説明 | 例 |
|:---|:---|:---|
| == | 等価性をチェックします<br>(大文字と小文字を区別します) | `Level == 8` |
| =~ | 等価性をチェックします<br>(大文字と小文字は区別されません) | `EventSourceName =~ "microsoft-windows-security-auditing"` |
| !=、<> | 非等価性をチェックします<br>(両方の式は同じです) | `Level != 4` |
| *and*、*or* | 条件の間に必要です| `Level == 16 or CommandLine != ""` |

複数の条件でフィルター処理するには、**and** を使用します。

```OQL
SecurityEvent
| where Level == 8 and EventID == 4672
```

または、複数の **where** 要素をパイプでつなぎます。

```OQL
SecurityEvent
| where Level == 8 
| where EventID == 4672
```
    
> [!NOTE]
> 値の型は異なる可能性があるため、正しい型に基づいて比較を実行するために型をキャストする必要があります。 たとえば、SecurityEvent *Level* 列は String 型なので、数値演算子を使用する前に、*int* または *long* などの数値型にキャストする必要があります。`SecurityEvent | where toint(Level) >= 10`

## <a name="specify-a-time-range"></a>時間の範囲を指定する

### <a name="time-picker"></a>時刻の選択ツール
時刻の選択ツールは左上にあります。これは、過去 24 時間のレコードのみのクエリを実行していることを示しています。 これは、すべてのクエリに適用される既定の時間範囲です。 過去 1 時間のレコードのみを取得するには、_[過去 1 時間]_ を選択してクエリを再度実行します。

![時刻の選択](media/get-started-queries/timepicker.png)


### <a name="time-filter-in-query"></a>クエリの時間フィルター
また、時間フィルターをクエリに追加して、独自の時間の範囲を定義することもできます。 テーブル名の直後に時間フィルターを配置することをお勧めします。 

```OQL
SecurityEvent
| where TimeGenerated > ago(30m) 
| where toint(Level) >= 10
```

上記の時間フィルター `ago(30m)` は "30 分前" を意味するので、このクエリで過去 30 分間のレコードのみが返されます。 他の時間単位には、日 (2d)、分 (25m)、秒 (10s) があります。


## <a name="project-and-extend-select-and-compute-columns"></a>プロジェクトと拡張: 列の選択と計算
結果に含める特定の列を選択するには、**project** を使用します。

```OQL
SecurityEvent 
| top 10 by TimeGenerated 
| project TimeGenerated, Computer, Activity
```

前述の例では、次の出力が生成されます。

![Log Analytics の project の結果](media/get-started-queries/project.png)

また、**project** を使用して列の名前を変更し、新しい列を定義することもできます。 次の例では、project を使用して次の処理を行います。

* 元の列の *Computer* および *TimeGenerated* のみを選択します。
* *Activity* 列の名前を *EventDetails* に変更します。
* *EventCode* という名前の新しい列を作成します。 **substring ()** 関数は、Activity フィールドの先頭の 4 文字のみを取得するために使用されます。


```OQL
SecurityEvent
| top 10 by TimeGenerated 
| project Computer, TimeGenerated, EventDetails=Activity, EventCode=substring(Activity, 0, 4)
```

**extend** は、元の列をすべて結果セットに保存し、追加の列を定義します。 次のクエリでは、**extend** を使用して *localtime* 列を追加します。この列には、ローカライズされた TimeGenerated 値が含まれています。

```OQL
SecurityEvent
| top 10 by TimeGenerated
| extend localtime = TimeGenerated-8h
```

## <a name="summarize-aggregate-groups-of-rows"></a>summarize: 行のグループを集計する
**summarize** を使用して、1 つまたは複数の列に従ってレコードのグループを特定し、それらのグループに集計を適用します。 **summarize** の最も一般的な用途は、各グループの結果の数を返す  *count* です。

次のクエリでは、過去 1 時間のすべての *Perf* レコードを確認し、*ObjectName* でグループ化し、各グループのレコードをカウントします。 
```OQL
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName
```

複数の次元でグループを定義することが理にかなっている場合があります。 これらの値の一意の組み合わせで、それぞれ別のグループが定義されます。

```OQL
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName, CounterName
```

もう 1 つの一般的な用途は、各グループに対して数学的または統計的計算を実行する場合です。 以下の例では、各コンピューターの平均  *CounterValue* を計算します。

```OQL
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer
```

残念ながら、異なるパフォーマンス カウンターが混在するので、このクエリの結果は意味がありません。 より意味のある結果にするには、*CounterName* と *Computer* の組み合わせごとに別に平均を計算する必要があります。

```OQL
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer, CounterName
```

### <a name="summarize-by-a-time-column"></a>時間列で要約する
時間列または別の連続する値に基づいて結果をグループ化することもできます。 ただし、これらは一意の値なので、`by TimeGenerated` を要約するだけでは、時間の範囲全体に 1 ミリ秒ごとのグループが作成されます。 

連続する値に基づいてグループを作成するには、**bin** を使用して範囲を管理しやすい単位に分割することをお勧めします。 次のクエリでは、特定のコンピューター上の空きメモリ (*[利用可能な MB]*) を測定する *Perf* レコードを分析します。 過去 2 日間の各 1 時間の平均値が計算されます。

```OQL
Perf 
| where TimeGenerated > ago(2d)
| where Computer == "ContosoAzADDS2" 
| where CounterName == "Available MBytes" 
| summarize count() by bin(TimeGenerated, 1h)
```

出力をよりわかりやすくするには、時系列で空きメモリを示す時間グラフとして表示するように選択します。

![時系列の Log Analytics メモリ](media/get-started-queries/chart.png)



## <a name="next-steps"></a>次の手順

- [検索クエリの記述方法](search-queries.md)を参照してください。