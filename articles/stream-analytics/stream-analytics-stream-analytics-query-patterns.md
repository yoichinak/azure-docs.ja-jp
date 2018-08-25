---
title: Azure Stream Analytics での一般的なクエリ パターン
description: この記事では、Azure Stream Analytics ジョブで役に立つさまざまな一般的クエリ パターンとデザインについて説明します。
services: stream-analytics
author: jseb225
manager: kfile
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: 7f171fa1eb8c91b55119d0308b57fe3d3e70261b
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39578893"
---
# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>一般的 Stream Analytics 使用状況パターンのクエリ例

## <a name="introduction"></a>はじめに
Azure Stream Analytics のクエリは SQL に類似したクエリ言語で表現されます。 言語の構造については、「[Stream Analytics query language reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)」(Stream Analytics クエリ言語リファレンス) ガイドで確認できます。 

クエリのデザインでは、イベント データを 1 つの入力ストリームから別の出力データ ストアに移動する単純なパススルー ロジックを表すことができます。 または、TollApp サンプルのように、豊富なパターン マッチングとテンポラル解析を行って、さまざまな時間枠にわたる集計を計算することができます。 複数の入力からのデータを結合してストリーミング イベントを結合し、静的な参照データに対する参照を行ってイベントの値を多様化することができます。 複数の出力にデータを書き込むこともできます。

この記事では、実際のシナリオに基づいて、いくつかの一般的なクエリ パターンの対処方法について説明します。 このドキュメントは作成中であり、継続的に新しいパターンで更新されます。

## <a name="work-with-complex-data-types-in-json-and-avro"></a>JSON および AVRO での複合データ型の操作 
Azure Stream Analytics では、CSV、JSON、および Avro データ形式のイベントの処理をサポートします。
JSON と Avro のどちらも、入れ子になったオブジェクト (レコード) や配列などの複合型を含むことができます。 これらの複雑なデータ型の操作については、[JSON および AVRO データの解析](stream-analytics-parsing-json.md)に関する記事をご覧ください。


## <a name="query-example-convert-data-types"></a>クエリの例: データ型の変換
**説明**: 入力ストリームのプロパティの型を定義します。
たとえば、自動車の重量は入力ストリームでは文字列ですが、**合計**を計算するために **INT** に変換する必要があります。

**入力**:

| Make | Time | Weight |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |"1000" |
| Honda |2015-01-01T00:00:02.0000000Z |"2000" |

**出力**:

| Make | Weight |
| --- | --- |
| Honda |3000 |

**解決策**:

    SELECT
        Make,
        SUM(CAST(Weight AS BIGINT)) AS Weight
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)

**説明**: **Weight** フィールドの **CAST** ステートメントを使用してそのデータ型を指定します。 サポートされるデータ型の一覧については、「[Data types (Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835065.aspx)」(データ型 (Azure Stream Analytics)) をご覧ください。

## <a name="query-example-use-likenot-like-to-do-pattern-matching"></a>クエリ例: Like/Not like を使用してパターン マッチングを行う
**説明**: イベントのフィールド値が特定のパターンと一致することを確認します。
たとえば、結果が A で始まり 9 で終わるライセンス プレートを返すかどうかを検査します。

**入力**:

| Make | LicensePlate | Time |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**出力**:

| Make | LicensePlate | Time |
| --- | --- | --- |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**解決策**:

    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LicensePlate LIKE 'A%9'

**説明**: **LIKE** ステートメントを使用して **LicensePlate** フィールドの値を検査します。 値は A で始まり、0 個以上の文字列が続き、9 で終わります。 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>クエリ例: 異なるケース/値に異なるロジックを指定する (CASE ステートメント)
**説明**: 特定の条件に基づいて、フィールドに異なる計算を適用します。
たとえば、通過した自動車の台数を製造元ごとに表す文字列情報を指定します。このとき、台数が 1 のときだけ異なる計算を適用するものとします。

**入力**:

| Make | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**出力**:

| CarsPassed | Time |
| --- | --- | --- |
| 1 Honda |2015-01-01T00:00:10.0000000Z |
| 2 Toyotas |2015-01-01T00:00:10.0000000Z |

**解決策**:

    SELECT
        CASE
            WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
            ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
        END AS CarsPassed,
        System.TimeStamp AS Time
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)

**説明**:**CASE** 式は、式を一連の単純な式と比較して結果を決定します。 この例では、カウント 1 の自動車は、カウントが 1 以外の自動車とは異なる文字列の説明を返します。 

## <a name="query-example-send-data-to-multiple-outputs"></a>クエリ例: 複数の出力にデータを送信する
**説明**: 1 つのジョブから複数の出力ターゲットにデータを送信します。
たとえば、しきい値に基づくアラートのデータを分析し、すべてのイベントを Blob Storage にアーカイブします。

**入力**:

| Make | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**出力 1**:

| Make | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**出力 2**:

| Make | Time | Count |
| --- | --- | --- |
| Toyota |2015-01-01T00:00:10.0000000Z |3 |

**解決策**:

    SELECT
        *
    INTO
        ArchiveOutput
    FROM
        Input TIMESTAMP BY Time

    SELECT
        Make,
        System.TimeStamp AS Time,
        COUNT(*) AS [Count]
    INTO
        AlertOutput
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
    HAVING
        [Count] >= 3

**説明**: **INTO** 句は、Stream Analytics に対して、このステートメントからのデータを書き込む出力を指定します。
1 番目のクエリは、受け取ったデータを **ArchiveOutput** という名前の出力にパススルーします。
2 番目のクエリは、簡単な集計とフィルター処理を行い、結果を下流のアラート システムに送信します。

また、共通テーブル式 (CTE) (**WITH** ステートメントなど) の結果を複数の出力ステートメントに再利用することもできます。 このオプションには、入力ソースに対して開くリーダーが少なくて済むという追加の利点があります。
For example: 

    WITH AllRedCars AS (
        SELECT
            *
        FROM
            Input TIMESTAMP BY Time
        WHERE
            Color = 'red'
    )
    SELECT * INTO HondaOutput FROM AllRedCars WHERE Make = 'Honda'
    SELECT * INTO ToyotaOutput FROM AllRedCars WHERE Make = 'Toyota'

## <a name="query-example-count-unique-values"></a>クエリ例: 一意の値をカウントする
**説明**: ストリームに出現するフィールドの、値ごとの件数を一定間隔でカウントします。
たとえば、料金所を通過した自動車の、メーカーごとの台数を 2 秒間隔でカウントする場合などです。

**入力**:

| Make | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**出力:**

| CountMake | Time |
| --- | --- |
| 2 |2015-01-01T00:00:02.000Z |
| 1 |2015-01-01T00:00:04.000Z |

**解決策:**

````
SELECT
     COUNT(DISTINCT Make) AS CountMake,
     System.TIMESTAMP AS TIME
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
````


**説明:**
**COUNT(DISTINCT Make)** は、特定の時間枠内での、**Make** 列の個別の値の数を返します。

## <a name="query-example-determine-if-a-value-has-changed"></a>クエリ例: 値が変化したかどうかを判定する
**説明**: 前の値が現在の値と異なるかどうかを判定します。
たとえば、前に有料道路を走っていた自動車のメーカーが現在の自動車と同じであるかどうかなどです。

**入力**:

| Make | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |

**出力**:

| Make | Time |
| --- | --- |
| Toyota |2015-01-01T00:00:02.0000000Z |

**解決策**:

    SELECT
        Make,
        Time
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make

**説明**: **LAG** を使用して入力ストリームで 1 つ前のイベントを調べて、**Make** の値を取得します。 次に、現在のイベントの **Make** の値と比較し、異なる場合はイベントを出力します。

## <a name="query-example-find-the-first-event-in-a-window"></a>クエリ例: 期間内の最初のイベントを検索する
**説明**: 10 分間隔で最初の自動車を検索します。

**入力**:

| LicensePlate | Make | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**出力**:

| LicensePlate | Make | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |

**解決策**:

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) = 1

問題を変更して、10 分ごとに特定のメーカーの最初の自動車を検索します。

| LicensePlate | Make | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**解決策**:

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) OVER (PARTITION BY Make) = 1

## <a name="query-example-find-the-last-event-in-a-window"></a>クエリ例: 期間内の最後のイベントを検索する
**説明**: 10 分間隔で最後の自動車を検索します。

**入力**:

| LicensePlate | Make | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**出力**:

| LicensePlate | Make | Time |
| --- | --- | --- |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**解決策**:

    WITH LastInWindow AS
    (
        SELECT 
            MAX(Time) AS LastEventTime
        FROM 
            Input TIMESTAMP BY Time
        GROUP BY 
            TumblingWindow(minute, 10)
    )
    SELECT 
        Input.LicensePlate,
        Input.Make,
        Input.Time
    FROM
        Input TIMESTAMP BY Time 
        INNER JOIN LastInWindow
        ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
        AND Input.Time = LastInWindow.LastEventTime

**説明**: クエリには 2 つの手順があります。 最初の手順では、10 分間隔で最新のタイムスタンプを検索します。 2 番目の手順では、最初のクエリの結果と元のストリームを結合し、各期間で最後のタイムスタンプに一致するイベントを検索します。 

## <a name="query-example-detect-the-absence-of-events"></a>クエリ例: イベントがないことを検出する
**説明**: 特定の条件と一致する値がストリームに存在しないことを確認します。
たとえば、同じ製造元の 2 台の自動車が、最後の 90 秒で続けて有料道路に進入したことを把握するには、どうすればよいのでしょうか。

**入力**:

| Make | LicensePlate | Time |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Honda |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Toyota |DEF-987 |2015-01-01T00:00:03.0000000Z |
| Honda |GHI-345 |2015-01-01T00:00:04.0000000Z |

**出力**:

| Make | Time | CurrentCarLicensePlate | FirstCarLicensePlate | FirstCarTime |
| --- | --- | --- | --- | --- |
| Honda |2015-01-01T00:00:02.0000000Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000Z |

**解決策**:

    SELECT
        Make,
        Time,
        LicensePlate AS CurrentCarLicensePlate,
        LAG(LicensePlate, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarLicensePlate,
        LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarTime
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make

**説明**: **LAG** を使用して入力ストリームで 1 つ前のイベントを調べて、**Make** の値を取得します。 これを現在のイベントの **Make** の値と比較し、同じ場合はイベントを出力します。 また、**LAG** を使用して前の自動車のデータを取得することもできます。

## <a name="query-example-detect-the-duration-between-events"></a>クエリ例: イベントの間隔を検出する
**説明**: 特定のイベントの間隔を検出します。 たとえば、Web クリック ストリームから、ある機能に費やされた時間を調べます。

**入力**:  

| User | Feature | Event | Time |
| --- | --- | --- | --- |
| user@location.com |RightMenu |開始 |2015-01-01T00:00:01.0000000Z |
| user@location.com |RightMenu |End |2015-01-01T00:00:08.0000000Z |

**出力**:  

| User | Feature | 時間 |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**解決策**:

````
    SELECT
        [user], feature, DATEDIFF(second, LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'), Time) as duration
    FROM input TIMESTAMP BY Time
    WHERE
        Event = 'end'
````

**説明**: **LAST** 関数を使用して、イベントの種類が **Start** であった最後の **TIME** 値を取得します。 **LAST** 関数は **PARTITION BY [user]** が使用し、一意のユーザーごとに結果が計算されることを示します。 このクエリでは、**Start** イベントと **Stop** イベントの時間差の最大しきい値を 1 時間としていますが、必要に応じて構成可能です **(LIMIT DURATION(hour, 1)**。

## <a name="query-example-detect-the-duration-of-a-condition"></a>クエリ例: 条件の期間を検出する
**説明**: 条件が発生していた時間の長さを調べます。
たとえば、バグのためにすべての自動車の重量が正しくない (20,000 ポンドを超過) 結果になった場合、バグが継続した期間を計算する必要があります。

**入力**:

| Make | Time | Weight |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |2000 |
| Toyota |2015-01-01T00:00:02.0000000Z |25000 |
| Honda |2015-01-01T00:00:03.0000000Z |26000 |
| Toyota |2015-01-01T00:00:04.0000000Z |25000 |
| Honda |2015-01-01T00:00:05.0000000Z |26000 |
| Toyota |2015-01-01T00:00:06.0000000Z |25000 |
| Honda |2015-01-01T00:00:07.0000000Z |26000 |
| Toyota |2015-01-01T00:00:08.0000000Z |2000 |

**出力**:

| StartFault | EndFault |
| --- | --- |
| 2015-01-01T00:00:02.000Z |2015-01-01T00:00:07.000Z |

**解決策**:

````
    WITH SelectPreviousEvent AS
    (
    SELECT
    *,
        LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previousTime,
        LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previousWeight
    FROM input TIMESTAMP BY [time]
    )

    SELECT 
        LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previousWeight < 20000 ) [StartFault],
        previousTime [EndFault]
    FROM SelectPreviousEvent
    WHERE
        [weight] < 20000
        AND previousWeight > 20000
````

**説明**: **LAG** を使用して 24 時間の入力ストリームに着目し、重量の上限を 20000 として、**StartFault** と **StopFault** の範囲で該当するインスタンスを探します。

## <a name="query-example-fill-missing-values"></a>クエリ例: 欠落値を入力する
**説明**: 欠落値があるイベントのストリームの場合、定期的な間隔でイベントのストリームを生成します。
たとえば、最近検出されたデータ ポイントを報告する 5 秒ごとのイベントを生成します。

**入力**:

| t | value |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**出力 (最初の 10 行)**:

| windowend | lastevent.t | lastevent.value |
| --- | --- | --- |
| 2014-01-01T14:01:00.000Z |2014-01-01T14:01:00.000Z |1 |
| 2014-01-01T14:01:05.000Z |2014-01-01T14:01:05.000Z |2 |
| 2014-01-01T14:01:10.000Z |2014-01-01T14:01:10.000Z |3 |
| 2014-01-01T14:01:15.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:20.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:25.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:30.000Z |2014-01-01T14:01:30.000Z |5 |
| 2014-01-01T14:01:35.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:40.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:45.000Z |2014-01-01T14:01:35.000Z |6 |

**解決策**:

    SELECT
        System.Timestamp AS windowEnd,
        TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
        input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)


**説明**: このクエリは、5 秒ごとにイベントを生成し、それまでに受信した最後のイベントを出力します。 [ホッピング ウィンドウ](https://msdn.microsoft.com/library/dn835041.aspx "ホッピング ウィンドウ -- Azure Stream Analytics") 期間は、クエリが最新のイベントを検出するためにさかのぼる期間 (この例では 300 秒) を指定します。


## <a name="query-example-correlate-two-event-types-within-the-same-stream"></a>クエリの例: 同じストリーム内で 2 つのイベントの種類を関連付ける
**説明**: 場合によっては、特定の期間中に発生した複数のイベントの種類に基づいて、アラートを生成する必要があります。
たとえば、家庭用電子レンジの IoT シナリオにおいて、ファン温度が 40 未満、直近 3 分間の最大電力が 10 未満の場合にアラートを生成する必要があるとします。

**入力**:

| time | deviceId | sensorName | value |
| --- | --- | --- | --- |
| "2018-01-01T16:01:00" | "Oven1" | "temp" |120 |
| "2018-01-01T16:01:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:02:00" | "Oven1" | "temp" |100 |
| "2018-01-01T16:02:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:03:00" | "Oven1" | "temp" |70 |
| "2018-01-01T16:03:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:04:00" | "Oven1" | "temp" |50 |
| "2018-01-01T16:04:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:05:00" | "Oven1" | "temp" |30 |
| "2018-01-01T16:05:00" | "Oven1" | "power" |8 |
| "2018-01-01T16:06:00" | "Oven1" | "temp" |20 |
| "2018-01-01T16:06:00" | "Oven1" | "power" |8 |
| "2018-01-01T16:07:00" | "Oven1" | "temp" |20 |
| "2018-01-01T16:07:00" | "Oven1" | "power" |8 |
| "2018-01-01T16:08:00" | "Oven1" | "temp" |20 |
| "2018-01-01T16:08:00" | "Oven1" | "power" |8 |

**出力**:

| eventTime | deviceId | temp | alertMessage | maxPowerDuringLast3mins |
| --- | --- | --- | --- | --- | 
| "2018-01-01T16:05:00" | "Oven1" |30 | "発熱体がショートしています" |15 |
| "2018-01-01T16:06:00" | "Oven1" |20 | "発熱体がショートしています" |15 |
| "2018-01-01T16:07:00" | "Oven1" |20 | "発熱体がショートしています" |15 |

**解決策**:

````
WITH max_power_during_last_3_mins AS (
    SELECT 
        System.TimeStamp AS windowTime,
        deviceId,
        max(value) as maxPower
    FROM
        input TIMESTAMP BY t
    WHERE 
        sensorName = 'power' 
    GROUP BY 
        deviceId, 
        SlidingWindow(minute, 3) 
)

SELECT 
    t1.t AS eventTime,
    t1.deviceId, 
    t1.value AS temp,
    'Short circuit heating elements' as alertMessage,
    t2.maxPower AS maxPowerDuringLast3mins
    
INTO resultsr

FROM input t1 TIMESTAMP BY t
JOIN max_power_during_last_3_mins t2
    ON t1.deviceId = t2.deviceId 
    AND t1.t = t2.windowTime
    AND DATEDIFF(minute,t1,t2) between 0 and 3
    
WHERE
    t1.sensorName = 'temp'
    AND t1.value <= 40
    AND t2.maxPower > 10
````

**説明**: 1 つ目の `max_power_during_last_3_mins` クエリでは、[スライディング ウィンドウ](https://msdn.microsoft.com/azure/stream-analytics/reference/sliding-window-azure-stream-analytics)を使用して、すべてのデバイスの電力センサーの最大値 (直近 3 分間) を特定しています。 2 つ目のクエリでは、1 つ目のクエリと結合して、現在のイベントに関連する直近のウィンドウの電力値を特定しています。 その後、条件が満たされた場合には、デバイスについてのアラートが生成されます。

## <a name="query-example-process-events-independent-of-device-clock-skew-substreams"></a>クエリの例: デバイスのクロックのずれ (サブストリーム) と関係なくイベントを処理します。
**説明**: イベント プロデューサー間またはパーティション間のクロックのずれや、ネットワーク待機時間が原因でイベントが遅れて、あるいは順序がずれて到着することがあります。 次の例では、TollID 2 のデバイス クロックは TollID 1 より 10 秒遅れており、TollID 3 のデバイス クロックは TollID 1 より 5 秒遅れています。 


**入力**:
| LicensePlate | Make | Time | TollID |
| --- | --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:01.0000000Z | 1 |
| YHN 6970 |Toyota |2015-07-27T00:00:05.0000000Z | 1 |
| QYF 9358 |Honda |2015-07-27T00:00:01.0000000Z | 2 |
| GXF 9462 |BMW |2015-07-27T00:00:04.0000000Z | 2 |
| VFE 1616 |Toyota |2015-07-27T00:00:10.0000000Z | 1 |
| RMV 8282 |Honda |2015-07-27T00:00:03.0000000Z | 3 |
| MDR 6128 |BMW |2015-07-27T00:00:11.0000000Z | 2 |
| YZK 5704 |Ford |2015-07-27T00:00:07.0000000Z | 3 |

**出力**:
| TollID | Count |
| --- | --- |
| 1 | 2 |
| 2 | 2 |
| 1 | 1 |
| 3 | 1 |
| 2 | 1 |
| 3 | 1 |

**解決策**:

````
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId

````

**説明**: [TIMESTAMP BY OVER](https://msdn.microsoft.com/azure/stream-analytics/reference/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering) 句は、サブストリームを使用して各デバイスのタイムラインで個別に検索します。 各 TollID の出力イベントは計算されると同時に生成され、同じクロックをすべてのデバイスが参照しているかのように順序が変更されるのではなく、各 TollID ごとにイベントが順序付けられます。


## <a name="get-help"></a>問い合わせ
さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)を参照してください。

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

