---
title: Azure Stream Analytics に入力としてデータをストリーム配信する
description: Azure Stream Analytics でデータ接続を設定する方法について説明します。 入力には、イベントからのデータ ストリームだけでなく、参照データも含まれます。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/27/2018
ms.openlocfilehash: 9fa71c221b276e2173694e2c1e86673e52677e63
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2018
ms.locfileid: "39389825"
---
# <a name="stream-data-as-input-into-stream-analytics"></a>Stream Analytics に入力としてデータをストリーム配信する

Stream Analytics は、Azure データ ストリームとの統合にきわめて優れており、次の 3 種類のリソースを入力とすることができます。
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure BLOB Storage](https://azure.microsoft.com/services/storage/blobs/) 

これらの入力ソースは、Stream Analytics ジョブと同じ Azure サブスクリプションに存在していても、異なるサブスクリプションに存在していてもかまいません。

### <a name="compression"></a>圧縮
Stream Analytics では、すべてのデータ ストリーム入力ソースにわたる圧縮がサポートされています。 現在サポートされている参照の種類は、[なし]、[GZip]、[Deflate] 圧縮です。 参照データの圧縮はサポートされていません。 入力形式が、圧縮された Avro データである場合、透過的に処理されます。 Avro のシリアル化に圧縮の種類を指定する必要はありません。 

## <a name="create-edit-or-test-inputs"></a>入力の作成、編集、またはテスト
[Azure Portal](https://portal.azure.com) を使用して、ストリーミング ジョブに[新しい入力を作成](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-quick-create-portal#configure-input-to-the-job)したり、既存の入力を表示 (または編集) したりできます。 サンプル データからの入力接続のテストと[クエリのテスト](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-manage-job#test-your-query)も実行できます。 クエリを記述する際は、FROM 句に入力の一覧を指定します。 ポータルの **[クエリ]** ページで、使用できる入力の一覧を取得できます。 複数の入力を使用する場合は、それらを `JOIN` するか、複数の `SELECT` クエリを記述します。


## <a name="stream-data-from-event-hubs"></a>Event Hubs からのデータのストリーム配信

Azure Event Hubs はスケーラブルな発行/サブスクライブ イベント インジェスターを提供します。 1 秒間に数百万件のイベントを取り込むことができるため、接続されたデバイスとアプリケーションで生成される大量のデータを処理および分析できます。 Event Hubs と Stream Analytics を一緒に使用することで、リアルタイム分析用のエンド ツー エンドのソリューションが提供されます。 Event Hubs で Azure にイベントをリアルタイムでフィードし、Stream Analytics ジョブでこれらのイベントをリアルタイムで処理できます。 たとえば、Web クリック、センサーの読み取り、オンライン ログ イベントを Event Hubs に送信できます。 続いて、リアルタイムのフィルター処理、集計、相関関係に入力データ ストリームとして Event Hubs を使用する Stream Analytics ジョブを作成できます。

`EventEnqueuedUtcTime` は、イベントが Event Hubs に到着したときのタイムスタンプであり、Event Hubs から Stream Analytics に送信されるイベントの既定のタイムスタンプです。 イベント ペイロードのタイムスタンプを利用してデータをストリームとして処理するには、[TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) キーワードを使用する必要があります。

### <a name="consumer-groups"></a>コンシューマー グループ
Stream Analytics イベント ハブの各入力は、独自のコンシューマー グループを持つように構成する必要があります。 ジョブに自己結合または複数の入力が含まれる場合、一部の入力は複数の閲覧者ダウンストリームによって読み取られる可能性があります。 この状況は 1 つのコンシューマー グループの閲覧者数に影響を与えます。 閲覧者の数を各パーティションのコンシューマー グループ別に 5 人とする Event Hubs の上限を回避するには、Stream Analytics ジョブごとにコンシューマー グループを指定するのが最良事例となります。 イベント ハブごとに 20 個のコンシューマー グループという制限もあります。 詳細については、[Event Hub レシーバーでの Azure Stream Analytics のトラブルシューティング](stream-analytics-event-hub-consumer-groups.md)をご覧ください。

### <a name="stream-data-from-event-hubs"></a>Event Hubs からのデータのストリーム配信
次の表に示したのは、Azure Portal の **[新しい入力]** ページで Event Hubs からのデータ入力をストリーム配信するときに使用される各プロパティの説明です。

| プロパティ | 説明 |
| --- | --- |
| **入力のエイリアス** |この入力を参照するジョブのクエリで使用するわかりやすい名前。 |
| **サブスクリプション** | イベント ハブ リソースが存在するサブスクリプションを選択します。 | 
| **イベント ハブの名前空間** | [イベント ハブの名前空間] は、一連のメッセージング エンティティのコンテナーです。 新しいイベント ハブを作成するときは、名前空間も作成します。 |
| **イベント ハブ名** | 入力として使用するイベント ハブの名前。 |
| **イベント ハブ ポリシー名** | イベント ハブへのアクセスを提供する共有アクセス ポリシー。 各共有アクセス ポリシーには、名前、設定したアクセス許可、アクセス キーが含まれています。 Event Hub の設定を手動で入力するオプションを選択しない限り、このオプションは自動的に設定されます。|
| **イベント ハブ コンシューマー グループ** (推奨) | Stream Analytics ジョブごとに個別のコンシューマー グループを使用することを強くお勧めします。 イベント ハブからデータを取り込むために使用するコンシューマー グループが、この文字列によって識別されます。 コンシューマー グループが指定されていない場合、Stream Analytics ジョブは $Default コンシューマー グループを使用します。  |
| **イベントのシリアル化の形式** | 入ってくるデータ ストリームのシリアル化形式 (JSON、CSV、または Avro)。  JSON 形式が仕様に準拠しており、10 進数の先頭に 0 が含まれていないことを確認します。 |
| **Encoding** | 現在のところ、UTF-8 が、唯一サポートされているエンコード形式です。 |
| **イベントの圧縮タイプ** | 受信データ ストリームを読み取る際に使用される圧縮タイプ。[なし] (既定値)、[GZip]、[Deflate] などがあります。 |

イベント ハブ ストリーム入力からデータが送信される場合、Stream Analytics クエリでは次のメタデータ フィールドにアクセスできます。

| プロパティ | 説明 |
| --- | --- |
| **EventProcessedUtcTime** |Stream Analytics によってイベントが処理された日時。 |
| **EventEnqueuedUtcTime** |Event Hubs でイベントを受信した日時。 |
| **PartitionId** |入力アダプターの 0 から始まるパーティション ID。 |

たとえば、これらのフィールドを使用して、次の例のようにクエリを記述できます。

```sql
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
```

> [!NOTE]
> IoT Hub ルートのエンドポイントとして Event Hub を使用する場合は、[GetMetadataPropertyValue 関数](https://msdn.microsoft.com/library/azure/mt793845.aspx)を使用して IoT Hub メタデータにアクセスすることができます。
> 

## <a name="stream-data-from-iot-hub"></a>IoT Hub からのデータのストリーム配信
Azure IoT Hub は、IoT シナリオ向けに最適化されたスケーラブルな発行/サブスクライブ イベント インジェスターです。

IoT Hub から Stream Analytics に着信するイベントの既定のタイムスタンプは、このイベントが IoT Hub に到達したときのタイムスタンプです。このタイムスタンプが `EventEnqueuedUtcTime` です。 イベント ペイロードのタイムスタンプを利用してデータをストリームとして処理するには、[TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) キーワードを使用する必要があります。

### <a name="consumer-groups"></a>コンシューマー グループ
Stream Analytics IoT Hub の各入力は、独自のコンシューマー グループを持つように構成する必要があります。 ジョブに自己結合または複数の入力が含まれる場合、一部の入力は複数の閲覧者ダウンストリームによって読み取られる可能性があります。 この状況は 1 つのコンシューマー グループの閲覧者数に影響を与えます。 閲覧者の数を各パーティションのコンシューマー グループ別に 5 人とする Azure IoT Hub の上限を回避するには、Stream Analytics ジョブごとにコンシューマー グループを指定するのが最良事例となります。

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>データ ストリーム入力として IoT Hub を構成する
次の表に、IoT Hub をストリーム入力として構成する場合に使用する、Azure Portal の **[新しい入力]** ページにある各プロパティの説明を示しています。

| プロパティ | 説明 |
| --- | --- |
| **入力のエイリアス** | この入力を参照するジョブのクエリで使用するわかりやすい名前。|
| **サブスクリプション** | IoT Hub リソースが存在するサブスクリプションを選択します。 | 
| **IoT Hub** | 入力として使用する IoT Hub の名前。 |
| **エンドポイント** | IoT Hub のエンドポイント。|
| **共有アクセス ポリシー名** | IoT Hub へのアクセスを提供する共有アクセス ポリシー。 各共有アクセス ポリシーには、名前、設定したアクセス許可、アクセス キーが含まれています。 |
| **共有アクセス ポリシー キー** | IoT Hub へのアクセスを承認するために使用する共有アクセス キー。  IoT Hub の設定を手動で入力するオプションを選択しない限り、このオプションは自動的に事前設定されます。 |
| **コンシューマー グループ** | Stream Analytics ジョブごとに異なるコンシューマー グループを使用することを強くお勧めします。 IoT Hub からのデータを取り込むために使用するコンシューマー グループです。 明示的に指定されない限り、Stream Analytics は $Default コンシューマー グループを使用します。  |
| **イベントのシリアル化の形式** | 入ってくるデータ ストリームのシリアル化形式 (JSON、CSV、または Avro)。  JSON 形式が仕様に準拠しており、10 進数の先頭に 0 が含まれていないことを確認します。 |
| **Encoding** | 現在のところ、UTF-8 が、唯一サポートされているエンコード形式です。 |
| **イベントの圧縮タイプ** | 受信データ ストリームを読み取る際に使用される圧縮タイプ。[なし] (既定値)、[GZip]、[Deflate] などがあります。 |


IoT Hub からのストリーム データを使用する場合、Stream Analytics クエリで次のメタデータ フィールドにアクセスできます。

| プロパティ | 説明 |
| --- | --- |
| **EventProcessedUtcTime** | イベントが処理された日時。 |
| **EventEnqueuedUtcTime** | IoT Hub でイベントを受信した日時。 |
| **PartitionId** | 入力アダプターの 0 から始まるパーティション ID。 |
| **IoTHub.MessageId** | IoT Hub での双方向通信の関連付けに使用する ID。 |
| **IoTHub.CorrelationId** | IoT Hub でのメッセージ応答とフィードバックに使用する ID。 |
| **IoTHub.ConnectionDeviceId** | このメッセージの送信に使用される認証 ID。 この値は、IoT Hub により servicebound メッセージにスタンプされます。 |
| **IoTHub.ConnectionDeviceGenerationId** | このメッセージの送信に使用された認証済みデバイスの世代 ID。 この値は、IoT Hub により servicebound メッセージにスタンプされます。 |
| **IoTHub.EnqueuedTime** | IoT Hub でメッセージを受信した時刻。 |
| **IoTHub.StreamId** | 送信元デバイスにより追加されたカスタム イベント プロパティ。 |


## <a name="stream-data-from-blob-storage"></a>Blob Storage からのデータのストリーム配信
大量の非構造化データをクラウドに保存する場合は、Azure Blob Storage を使用するとコスト効率の高いスケーラブルなソリューションが実現します。 BLOB ストレージのデータは、通常は保存データとみなされますが、Stream Analytics でデータ ストリームとして処理できます。 

ログの処理は、Blob Storage を Stream Analytics の入力として使用する場合によく使用されるシナリオです。 このシナリオでは、利用統計情報ファイルがシステムから取得されます。この情報を解析および処理して意味のあるデータを抽出する必要があります。

Stream Analytics の Blob Storage イベントの既定のタイムスタンプは BLOB が最後に変更されたときのタイムスタンプです。このタイムスタンプが `BlobLastModifiedUtcTime` です。 イベント ペイロードのタイムスタンプを利用してデータをストリームとして処理するには、[TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) キーワードを使用する必要があります。 Stream Analytics ジョブは、BLOB ファイルが使用可能な場合に、毎秒 Azure Blob Storage 入力からデータをプルします。 BLOB ファイルが使用不可能な場合は、最大で 90 秒の時間遅延がある指数関数的バックオフがあります。

CSV 形式の入力については、データ セットのフィールドを定義するためのヘッダー行が "*必須*" です。また、ヘッダー行のフィールドはすべて一意であることが必要です。

イベント ハブのキャプチャや IoT Hub の Azure Storage コンテナーのカスタム エンドポイントによって生成された AVRO メッセージを逆シリアル化する機能は、現在 Stream Analytics ではサポートされていません。

> [!NOTE]
> Stream Analytics では、既存の BLOB ファイルにコンテンツを追加できません。 Stream Analytics は各ファイルを 1 回だけ表示します。ジョブがデータを読み取った後にファイルで発生した変更は処理されません。 ベスト プラクティスとして、すべての BLOB ファイルのデータを一度にアップロードし、追加の新しいイベントを、別の新しい BLOB ファイルに追加することをお勧めします。
> 

### <a name="configure-blob-storage-as-a-stream-input"></a>ストリーム入力として Blob Storage を構成する 

次の表に、Blob Storage をストリーム入力として構成する場合に使用する、Azure Portal の **[新しい入力]** ページにある各プロパティの説明を示しています。

| プロパティ | 説明 |
| --- | --- |
| **入力のエイリアス** | この入力を参照するジョブのクエリで使用するわかりやすい名前。 |
| **サブスクリプション** | IoT Hub リソースが存在するサブスクリプションを選択します。 | 
| **ストレージ アカウント** | BLOB ファイルが配置されるストレージ アカウントの名前。 |
| **ストレージ アカウント キー** | ストレージ アカウントに関連付けられている秘密キー。 Blob Storage の設定を手動で入力するオプションを選択しない限り、このオプションは自動的に事前設定されます。 |
| **コンテナー** | BLOB 入力のコンテナーです。 コンテナーにより、Microsoft Azure Blob service に格納される BLOB が論理的にグループ化されます。 BLOB を Azure Blob Storage サービスにアップロードするとき、その BLOB のコンテナーを指定する必要があります。 コンテナーには、**[既存のものを使用]** を選択できるほか、**[新規作成]** を選択して新しいコンテナーを作成することもできます。|
| **パス パターン** (省略可能) | 指定されたコンテナー内に BLOB を配置するために使用されるファイル パス。 このパス内に、3 つの変数 (`{date}`、`{time}`、`{partition}`) の 1 つ以上のインスタンスを指定できます。<br/><br/>例 1: `cluster1/logs/{date}/{time}/{partition}`<br/><br/>例 2: `cluster1/logs/{date}`<br/><br/>`*` 文字はパス プレフィックスの許容値ではありません。 許容値は、有効な <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">Azure BLOB 文字</a>のみです。 コンテナー名またはファイル名を含めないでください。 |
| **日付形式** (省略可能) | パスで日付変数を使用する場合は、ファイルを編成する日付形式です。 例: `YYYY/MM/DD` |
| **時刻形式** (省略可能) |  パスで時刻変数を使用する場合は、ファイルを編成する時刻形式です。 現在唯一サポートされている値は `HH` (時) です。 |
| **イベントのシリアル化の形式** | 入ってくるデータ ストリームのシリアル化形式 (JSON、CSV、または Avro)。  JSON 形式が仕様に準拠しており、10 進数の先頭に 0 が含まれていないことを確認します。 |
| **Encoding** | CSV と JSON では、現在のところ、UTF-8 が唯一サポートされているエンコード形式です。 |
| **圧縮** | 受信データ ストリームを読み取る際に使用される圧縮タイプ。[なし] (既定値)、[GZip]、[Deflate] などがあります。 |

データが Blob Storage のソースから送信される場合、Stream Analytics クエリでは次のメタデータ フィールドにアクセスできます。

| プロパティ | 説明 |
| --- | --- |
| **BlobName** |イベントに起因する入力 BLOB の名前。 |
| **EventProcessedUtcTime** |Stream Analytics によってイベントが処理された日時。 |
| **BlobLastModifiedUtcTime** |BLOB が最後に変更された日時。 |
| **PartitionId** |入力アダプターの 0 から始まるパーティション ID。 |

たとえば、これらのフィールドを使用して、次の例のようにクエリを記述できます。

```sql
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
```

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [クイック スタート: Azure Portal を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
