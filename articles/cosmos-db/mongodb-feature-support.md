---
title: MongoDB に対する Azure Cosmos DB 機能のサポート | Microsoft Docs
description: MongoDB 3.4 に対して Azure Cosmos DB の MongoDB API で提供される機能サポートについて説明します。
services: cosmos-db
author: alekseys
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: na
ms.topic: overview
ms.date: 11/15/2017
ms.author: alekseys
ms.openlocfilehash: 2c86cbe2ac9a0611873aca35480af92304abe5b5
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928694"
---
# <a name="mongodb-api-support-for-mongodb-features-and-syntax"></a>MongoDB の機能と構文に対する MongoDB API サポート

Azure Cosmos DB は、Microsoft のグローバルに分散されたマルチモデル データベース サービスです。 データベースの MongoDB API との通信は、オープン ソースで公開されている任意の MongoDB クライアント [ドライバー](https://docs.mongodb.org/ecosystem/drivers)を使って行うことができます。 MongoDB [Wire Protocol](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol) に従うことで、MongoDB API で既存のクライアント ドライバーを使用できるようになります。

Azure Cosmos DB の MongoDB API を使用すれば、使い慣れた MongoDB API を活用できます。[グローバル配信](distribute-data-globally.md)、[自動シャーディング](partition-data.md)、可用性や待ち時間の保証、すべてのフィールドの自動インデックス作成、保存時の暗号化、バックアップを始めとする Azure Cosmos DB のエンタープライズ機能も、すべて利用できます。

## <a name="mongodb-protocol-support"></a>MongoDB のプロトコル サポート

Azure Cosmos DB MongoDB API は、既定で MongoDB Server バージョン **3.2** と互換性があります。 以下に、サポートされている演算子およびすべての制限事項や例外の一覧を示します。 現在、MongoDB バージョン **3.4** で追加された機能やクエリ演算子は、プレビュー機能として使用できます。 これらのプロトコルを認識するクライアント ドライバーはすべて、MongoDB API を使用して Cosmos DB に接続できる必要があります。

また、現在、[MongoDB 集計パイプライン](#aggregation-pipeline)も、別個のプレビュー機能として使用できます。

## <a name="mongodb-query-language-support"></a>MongoDB クエリ言語のサポート

Azure Cosmos DB の MongoDB API は、MongoDB クエリ言語の構成要素を包括的にサポートしています。 以下に、現在サポートされている操作、演算子、ステージ、コマンド、およびオプションの詳細な一覧を示します。

## <a name="database-commands"></a>データベース コマンド

Azure Cosmos DB は、MongoDB API のすべてのアカウントで、以下のデータベース コマンドをサポートしています。

### <a name="query-and-write-operation-commands"></a>クエリおよび書き込み操作コマンド
- 削除
- find
- findAndModify
- getLastError
- getMore
- insert
- update

### <a name="authentication-commands"></a>認証コマンド
- logout
- authenticate
- getnonce

### <a name="administration-commands"></a>管理コマンド
- dropDatabase
- listCollections
- drop
- create
- filemd5
- createIndexes
- listIndexes
- dropIndexes
- connectionStatus
- reIndex

### <a name="diagnostics-commands"></a>診断コマンド
- buildInfo
- collStats
- dbStats
- hostInfo
- listDatabases
- whatsmyuri

<a name="aggregation-pipeline"/>

## <a name="aggregation-pipelinea"></a>集計パイプライン</a>

Azure Cosmos DB は、パブリック プレビューで集計パイプラインをサポートしています。 パブリック プレビューの利用を開始する手順については、[Azure ブログ](https://aka.ms/mongodb-aggregation)を参照してください。

### <a name="aggregation-commands"></a>集計コマンド
- aggregate
- count
- distinct

### <a name="aggregation-stages"></a>集計ステージ
- $project
- $match
- $limit
- $skip
- $unwind
- $group
- $sample
- $sort
- $lookup
- $out
- $count
- $addFields

### <a name="aggregation-expressions"></a>集計式

#### <a name="boolean-expressions"></a>ブール式
- $and
- $or
- $not

#### <a name="set-expressions"></a>設定式
- $setEquals
- $setIntersection
- $setUnion
- $setDifference
- $setIsSubset
- $anyElementTrue
- $allElementsTrue

#### <a name="comparison-expressions"></a>比較式
- $cmp
- $eq
- $gt
- $gte
- $lt
- $lte
- $ne

#### <a name="arithmetic-expressions"></a>算術式
- $abs
- $add
- $ceil
- $divide
- $exp
- $floor
- $ln
- $log
- $log10
- $mod
- $multiply
- $pow
- $sqrt
- $subtract
- $trunc

#### <a name="string-expressions"></a>文字列式
- $concat
- $indexOfBytes
- $indexOfCP
- $split
- $strLenBytes
- $strLenCP
- $strcasecmp
- $substr
- $substrBytes
- $substrCP
- $toLower
- $toUpper

#### <a name="array-expressions"></a>配列式
- $arrayElemAt
- $concatArrays
- $filter
- $indexOfArray
- $isArray
- $range
- $reverseArray
- $size
- $slice
- $in

#### <a name="date-expressions"></a>日付式
- $dayOfYear
- $dayOfMonth
- $dayOfWeek
- $year
- $month
- $week
- $hour
- $minute
- $second
- $millisecond
- $isoDayOfWeek
- $isoWeek

#### <a name="conditional-expressions"></a>条件式
- $cond
- $ifNull

## <a name="aggregation-accumulators"></a>集計アキュムレータ
- $sum
- $avg
- $first
- $last
- $max
- $min
- $push
- $addToSet

## <a name="operators"></a>演算子

以下の演算子が、対応するそれらの使用例でサポートされています。 下記のクエリで使用されているこのサンプル ドキュメントを考慮に入れてください。

```json
{
  "Volcano Name": "Rainier",
  "Country": "United States",
  "Region": "US-Washington",
  "Location": {
    "type": "Point",
    "coordinates": [
      -121.758,
      46.87
    ]
  },
  "Elevation": 4392,
  "Type": "Stratovolcano",
  "Status": "Dendrochronology",
  "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
}
```

演算子 | 例 |
--- | --- |
$eq | ``` { "Volcano Name": { $eq: "Rainier" } } ``` |  | -
$gt | ``` { "Elevation": { $gt: 4000 } } ``` |  | -
$gte | ``` { "Elevation": { $gte: 4392 } } ``` |  | -
$lt | ``` { "Elevation": { $lt: 5000 } } ``` |  | -
$lte | ``` { "Elevation": { $lte: 5000 } } ``` | | -
$ne | ``` { "Elevation": { $ne: 1 } } ``` |  | -
$in | ``` { "Volcano Name": { $in: ["St. Helens", "Rainier", "Glacier Peak"] } } ``` |  | -
$nin | ``` { "Volcano Name": { $nin: ["Lassen Peak", "Hood", "Baker"] } } ``` | | -
$or | ``` { $or: [ { Elevation: { $lt: 4000 } }, { "Volcano Name": "Rainier" } ] } ``` |  | -
$and | ``` { $and: [ { Elevation: { $gt: 4000 } }, { "Volcano Name": "Rainier" } ] } ``` |  | -
$not | ``` { "Elevation": { $not: { $gt: 5000 } } } ```|  | -
$nor | ``` { $nor: [ { "Elevation": { $lt: 4000 } }, { "Volcano Name": "Baker" } ] } ``` |  | -
$exists | ``` { "Status": { $exists: true } } ```|  | -
$type | ``` { "Status": { $type: "string" } } ```|  | -
$mod | ``` { "Elevation": { $mod: [ 4, 0 ] } } ``` |  | -
$regex | ``` { "Volcano Name": { $regex: "^Rain"} } ```|  | -

### <a name="notes"></a>メモ

$regex クエリでは、左固定の式でインデックス検索が可能です。 ただし、'i' 修飾子 (大文字と小文字の区別なし) や 'm' 修飾子 (複数行) を使用すると、すべての式でコレクション スキャンが発生します。
'$' または '|' を含める必要がある場合、2 つ (以上) の正規表現クエリを作成することをお勧めします。 たとえば、元のクエリとして ```find({x:{$regex: /^abc$/})``` がある場合、```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})``` のように変更する必要があります。
最初の部分では、インデックスを使用して検索を ^ abc で始まるドキュメントに制限し、2 番目の部分で入力そのものを照合します。 バー演算子 '|' は "or" 関数として機能します。そのためクエリ ```find({x:{$regex: /^abc|^def/})``` は、フィールド 'x' の値が "abc" または "def" で始まるドキュメントに一致します。 インデックスを利用するには、```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })``` のように、クエリを 2 つの異なるクエリに分割し、$or 演算子で結合することをお勧めします。

### <a name="update-operators"></a>更新演算子

#### <a name="field-update-operators"></a>フィールド更新演算子
- $inc
- $mul
- $rename
- $setOnInsert
- $set
- $unset
- $min
- $max
- $currentDate

#### <a name="array-update-operators"></a>配列更新演算子
- $addToSet
- $pop
- $pullAll
- $pull (注: 条件付きの $pull はサポートされていません)
- $pushAll
- $push
- $each
- $slice
- $sort
- $position

#### <a name="bitwise-update-operator"></a>ビット単位更新演算子
- $bit

### <a name="geospatial-operators"></a>地理空間演算子

演算子 | 例 
--- | --- |
$geoWithin | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | [はい]
$geoIntersects |  ```{ "Location.coordinates": { $geoIntersects: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | [はい]
$near | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | [はい]
$nearSphere | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | [はい]
$geometry | ```{ "Location.coordinates": { $geoWithin: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | [はい]
$minDistance | ```{ "Location.coordinates": { $nearSphere : { $geometry: {type: "Point", coordinates: [ -121, 46 ]}, $minDistance: 1000, $maxDistance: 1000000 } } }``` | [はい]
$maxDistance | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | [はい]
$center | ```{ "Location.coordinates": { $geoWithin: { $center: [ [-121, 46], 1 ] } } }``` | [はい]
$centerSphere | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | [はい]
$box | ```{ "Location.coordinates": { $geoWithin: { $box:  [ [ 0, 0 ], [ -122, 47 ] ] } } }``` | [はい]
$polygon | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | [はい]

## <a name="additional-operators"></a>その他の演算子

演算子 | 例 | メモ 
--- | --- | --- |
$all | ```{ "Location.coordinates": { $all: [-121.758, 46.87] } }``` | 
$elemMatch | ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } } }``` |  
$size | ```{ "Location.coordinates": { $size: 2 } }``` | 
$comment |  ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } }, $comment: "Negative values"}``` | 
$text |  | サポートされていません。 代わりに $regex を使用してください。

## <a name="unsupported-operators"></a>サポートされていない演算子

```$where``` と ```$eval``` の演算子は、Azure Cosmos DB ではサポートされていません。

### <a name="methods"></a>メソッド

以下のメソッドがサポートされています。

#### <a name="cursor-methods"></a>カーソル メソッド

方法 | 例 | メモ 
--- | --- | --- |
cursor.sort() | ```cursor.sort({ "Elevation": -1 })``` | 並べ替えキーを持たないドキュメントは返されない

## <a name="unique-indexes"></a>一意なインデックス

Azure Cosmos DB では、既定で、データベースに書き込まれるドキュメントのすべてのフィールドにインデックスが付けられます。 一意なインデックスによって、特定のフィールドの値が、コレクション内のすべてのドキュメントにわたって重複していないことが保証されます。これは、既定の "_id" キーで一意性が保持される方法と似ています。 これで、'unique' 制約を含めて createIndex コマンドを使用すれば、Azure Cosmos DB でカスタム インデックスを作成できます。

一意なインデックスは、MongoDB API のすべてのアカウントで使用できます。

## <a name="time-to-live-ttl"></a>Time-to-live (TTL)

Azure Cosmos DB では、ドキュメントのタイムスタンプに基づく相対的な Time-to-live (TTL) がサポートされます。 TTL は、[Azure Portal](https://portal.azure.com) から、MongoDB API コレクションに対して有効にすることができます。

## <a name="user-and-role-management"></a>ユーザーとロールの管理

Azure Cosmos DB では、ユーザーとロールはまだサポートされていません。 Azure Cosmos DB は、ロール ベース アクセス制御 (RBAC) と、[Azure Portal](https://portal.azure.com) (接続文字列 ページ) から取得できる、読み取り/書き込みおよび読み取り専用のパスワード/キーをサポートしています。

## <a name="replication"></a>レプリケーション

Cosmos azure DB では、最下位のレイヤーで、自動のネイティブ レプリケーションがサポートされています。 このロジックは、低待機時間のグローバルなレプリケーションも実現するために拡張されています。 Azure Cosmos DB Cosmos では、手動のレプリケーション コマンドはサポートされていません。

## <a name="write-concern"></a>書き込み確認

一部の MongoDB の API では、書き込み操作中に必要とされる応答の数を指定する、[書き込み確認](https://docs.mongodb.com/manual/reference/write-concern/)の指定がサポートされています。 Cosmos DB が背景でレプリケーションを処理する方法により、既定ですべての書き込みが自動的にクォーラムになります。 クライアント コードによって指定される書き込み確認はすべて無視されます。 詳細については、[整合性レベルを使用して可用性とパフォーマンスを最大化する方法](consistency-levels.md)に関するページを参照してください。

## <a name="sharding"></a>シャーディング

Azure Cosmos DB は、自動のサーバー側シャーディングをサポートしています。 Azure Cosmos DB では、手動のシャーディング コマンドはサポートされていません。

## <a name="next-steps"></a>次の手順

- MongoDB データベース用の API で [Studio 3T を使用](mongodb-mongochef.md)する方法について説明します。
- MongoDB データベース用の API で [Robo 3T を使用](mongodb-robomongo.md)する方法について説明します。
- MongoDB のプロトコルに対応する Azure Cosmos DB の[サンプル](mongodb-samples.md)を体験します。
