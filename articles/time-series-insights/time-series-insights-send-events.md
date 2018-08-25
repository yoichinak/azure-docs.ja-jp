---
title: Azure Time Series Insights 環境にイベントを送信する方法 | Microsoft Docs
description: このチュートリアルでは、イベント ハブを作成および構成し、サンプル アプリケーションを実行して Azure Time Series Insights に表示されるイベントをプッシュする方法について説明します。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/09/2018
ms.openlocfilehash: 30b83c54d314934f1de170955eec22e7b2a264b8
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39629754"
---
# <a name="send-events-to-a-time-series-insights-environment-using-event-hub"></a>イベント ハブを使用して Time Series Insights 環境にイベントを送信する
この記事では、イベント ハブを作成および構成し、サンプル アプリケーションを実行してイベントをプッシュする方法について説明します。 JSON 形式のイベントを含む既存のイベント ハブがある場合は、このチュートリアルをスキップし、[Time Series Insights](https://insights.timeseries.azure.com) で環境を表示してください。

## <a name="configure-an-event-hub"></a>イベント ハブを構成する
1. イベント ハブを作成するには、イベント ハブに関する[ドキュメント](../event-hubs/event-hubs-create.md)の手順に従います。

2. 検索バーで「**イベント ハブ**」を検索します。 返された一覧の **[イベント ハブ]** をクリックします。

3. イベント ハブを選択するには、その名前をクリックします。

4. 構成ウィンドウ中央の **[エンティティ]** で、再び **[イベント ハブ]** をクリックします。

5. イベント ハブの名前を選択して構成します。

  ![イベント ハブ コンシューマー グループの選択](media/send-events/consumer-group.png)

6. **[エンティティ]** で **[コンシューマー グループ]** を選択します。
 
7. Time Series Insights のイベント ソースで排他的に使用されるコンシューマー グループを作成していることを確認します。

   > [!IMPORTANT]
   > このコンシューマー グループがその他のサービス (Stream Analytics ジョブや別の Time Series Insights 環境など) で使用されていないことを確認してください。 コンシューマー グループが他のサービスで使用されている場合、この環境および他のサービスの読み取り操作は悪影響を受けます。 "$Default" をコンシューマー グループとして使用している場合、他の閲覧者によって再利用される可能性があります。

8. **[設定]** の見出しにある **[共有アクセス ポリシー]** を選択します。

9. イベント ハブに、CSharp サンプルでイベントの送信に使用される **MySendPolicy** を作成します。

  ![[共有アクセス ポリシー] を選択して [追加] ボタンをクリックする](media/send-events/shared-access-policy.png)  

  ![[新しい共有アクセス ポリシーの追加]](media/send-events/shared-access-policy-2.png)  

## <a name="add-time-series-insights-reference-data-set"></a>Time Series Insights の参照データ セットの追加 
TSI で参照データを使用すると、利用統計情報データにコンテキストが与えられます。  そのコンテキストによってデータに意味が加わり、フィルターや集計が容易になります。  TSI は参照データをイングレス時に結合し、このデータをさかのぼって結合することはできません。  したがって、データを持つイベント ソースを追加する前に参照データを追加することが重要です。  場所またはセンサーのタイプなどのデータは、スライスやフィルターを容易にするためにデバイス/タグ/センサー ID に結合する場合がある有用なディメンションです。  

> [!IMPORTANT]
> 履歴データをアップロードする場合は、参照データ セットを構成しておくことが重要です。

履歴データを TSI に一括アップロードするとき、参照データがあることを確認します。  TSI は、結合されたイベント ソースにデータがある場合は、そのイベント ソースからすぐに読み取りを開始することに注意してください。  特にイベント ソース内にデータが存在する場合は、参照データが準備できるまで、イベント ソースを TSI に結合するのを待った方が有益です。 あるいは、参照データ セットが準備できるまで待ってから、データをそのイベント ソースにプッシュすることもできます。

参照データを管理するために、TSI エクスプローラーには Web ベースのユーザー インターフェイスが存在し、プログラムによる C# API が存在します。 TSI エクスプローラーには、ファイルをアップロードしたり既存の参照データ セットを JSON または CSV 形式で貼り付けるためのビジュアル ユーザー エクスペリエンスが備わっています。 API を使用して、必要なときにカスタム アプリを構築できます。

Time Series Insights での参照データの管理の詳細については、[参照データの関連記事](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set)に関するセクションを参照してください。

## <a name="create-time-series-insights-event-source"></a>Time Series Insights のイベント ソースを作成する
1. イベント ソースを作成していない場合は、[こちらの手順](time-series-insights-how-to-add-an-event-source-eventhub.md)に従って、イベント ソースを作成します。

2. タイムスタンプ プロパティ名として **deviceTimestamp** を指定します。このプロパティは、C# のサンプルで実際のタイムスタンプとして使用されます。 タイムスタンプ プロパティ名は大文字と小文字が区別されます。また、JSON としてイベント ハブに送信される際、値は __yyyy-MM-ddTHH:mm:ss.FFFFFFFK__ 形式にする必要があります。 このプロパティがイベントに存在しない場合は、イベント ハブにエンキューされた時刻が使用されます。

  ![イベント ソースの作成](media/send-events/event-source-1.png)

## <a name="sample-code-to-push-events"></a>イベントをプッシュするサンプル コード
1. **MySendPolicy** という名前のイベント ハブ ポリシーに移動します。 ポリシー キーを含む**接続文字列**をコピーします。

  ![MySendPolicy の接続文字列のコピー](media/send-events/sample-code-connection-string.png)

2. 3 つの各デバイスにつき 600 件のイベントを送信する次のコードを実行します。 `eventHubConnectionString` は、実際の接続文字列で更新してください。

```csharp
using System;
using System.Collections.Generic;
using System.Globalization;
using System.IO;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.Rdx.DataGenerator
{
    internal class Program
    {
        private static void Main(string[] args)
        {
            var from = new DateTime(2017, 4, 20, 15, 0, 0, DateTimeKind.Utc);
            Random r = new Random();
            const int numberOfEvents = 600;

            var deviceIds = new[] { "device1", "device2", "device3" };

            var events = new List<string>();
            for (int i = 0; i < numberOfEvents; ++i)
            {
                for (int device = 0; device < deviceIds.Length; ++device)
                {
                    // Generate event and serialize as JSON object:
                    // { "deviceTimestamp": "utc timestamp", "deviceId": "guid", "value": 123.456 }
                    events.Add(
                        String.Format(
                            CultureInfo.InvariantCulture,
                            @"{{ ""deviceTimestamp"": ""{0}"", ""deviceId"": ""{1}"", ""value"": {2} }}",
                            (from + TimeSpan.FromSeconds(i * 30)).ToString("o"),
                            deviceIds[device],
                            r.NextDouble()));
                }
            }

            // Create event hub connection.
            var eventHubConnectionString = @"Endpoint=sb://...";
            var eventHubClient = EventHubClient.CreateFromConnectionString(eventHubConnectionString);

            using (var ms = new MemoryStream())
            using (var sw = new StreamWriter(ms))
            {
                // Wrap events into JSON array:
                sw.Write("[");
                for (int i = 0; i < events.Count; ++i)
                {
                    if (i > 0)
                    {
                        sw.Write(',');
                    }
                    sw.Write(events[i]);
                }
                sw.Write("]");

                sw.Flush();
                ms.Position = 0;

                // Send JSON to event hub.
                EventData eventData = new EventData(ms);
                eventHubClient.Send(eventData);
            }
        }
    }
}

```
## <a name="supported-json-shapes"></a>サポートされている JSON 構造
### <a name="sample-1"></a>サンプル 1

#### <a name="input"></a>入力

単純な JSON オブジェクト。

```json
{
    "id":"device1",
    "timestamp":"2016-01-08T01:08:00Z"
}
```
#### <a name="output---one-event"></a>出力 - 1 つのイベント

|id|timestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|

### <a name="sample-2"></a>サンプル 2

#### <a name="input"></a>入力
2 つの JSON オブジェクトを含む JSON 配列。 各 JSON オブジェクトはイベントに変換されます。
```json
[
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    },
    {
        "id":"device2",
        "timestamp":"2016-01-17T01:17:00Z"
    }
]
```
#### <a name="output---two-events"></a>出力 - 2 つのイベント

|id|timestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|
|device2|2016-01-08T01:17:00Z|

### <a name="sample-3"></a>サンプル 3

#### <a name="input"></a>入力

2 つの JSON オブジェクトを含む入れ子になった JSON 配列を含む JSON オブジェクト。
```json
{
    "location":"WestUs",
    "events":[
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z"
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z"
        }
    ]
}

```
#### <a name="output---two-events"></a>出力 - 2 つのイベント
"location" プロパティは各イベントにコピーされます。

|location|events.id|events.timestamp|
|--------|---------------|----------------------|
|WestUs|device1|2016-01-08T01:08:00Z|
|WestUs|device2|2016-01-08T01:17:00Z|

### <a name="sample-4"></a>サンプル 4

#### <a name="input"></a>入力

2 つの JSON オブジェクトを含む入れ子になった JSON 配列を含む JSON オブジェクト。 この入力は、グローバル プロパティが複雑な JSON オブジェクトによって表現できることを示しています。

```json
{
    "location":"WestUs",
    "manufacturer":{
        "name":"manufacturer1",
        "location":"EastUs"
    },
    "events":[
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z",
            "data":{
                "type":"pressure",
                "units":"psi",
                "value":108.09
            }
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z",
            "data":{
                "type":"vibration",
                "units":"abs G",
                "value":217.09
            }
        }
    ]
}
```
#### <a name="output---two-events"></a>出力 - 2 つのイベント

|location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
|---|---|---|---|---|---|---|---|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
|WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|



## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [Time Series Insights エクスプローラー](https://insights.timeseries.azure.com)で環境を表示します。
