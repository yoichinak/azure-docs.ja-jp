---
title: 'Azure Cosmos DB: SQL Java API、SDK、およびリソース | Microsoft Docs'
description: リリース日、提供終了日、Azure Cosmos DB SQL Java SDK の各バージョン間の変更など、SQL Java API と SDK に関するあらゆる詳細を提供します。
services: cosmos-db
author: rnagpal
manager: kfile
editor: cgronlun
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 06/29/2018
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d7d00d6236b601d145be03e6086bec2d72faafcd
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2018
ms.locfileid: "37344939"
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>SQL API 用 Azure Cosmos DB Java SDK: リリース ノートとリソース
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET Change Feed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST リソース プロバイダー](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

SQL API Java SDK は、同期操作をサポートしています。 非同期サポートの場合は、[SQL API Async Java SDK](sql-api-sdk-async-java.md) を使用してください。 

<table>

<tr><td>**SDK のダウンロード**</td><td>[Maven](http://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)</td></tr>

<tr><td>**API ドキュメント**</td><td>[Java API リファレンス ドキュメント](/java/api/com.microsoft.azure.documentdb)</td></tr>

<tr><td>**SDK への協力**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-java/)</td></tr>

<tr><td>**作業の開始**</td><td>[Java SDK の開始](sql-api-java-get-started.md)</td></tr>

<tr><td>**Web アプリ チュートリアル**</td><td>[Azure Cosmos DB を使用した Web アプリケーションの開発](sql-api-java-application.md)</td></tr>

<tr><td>**サポートされている最小ランタイム**</td><td>[JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)</td></tr>
</table></br>

## <a name="release-notes"></a>リリース ノート

### <a name="a-name11621162"></a><a name="1.16.2"/>1.16.2
* ストリーミングのフェールオーバーに対するサポートが追加されました。
* カスタム メタデータのサポートが追加されました。
* セッション処理ロジックが強化されました。
* パーティション キー範囲のキャッシュのバグを修正しました。
* 直接モードでの NPE バグを修正しました。

### <a name="a-name11611161"></a><a name="1.16.1"/>1.16.1
* 一意なインデックスのサポートが追加されました。
* フィード オプションに、継続トークンのサイズを制限するためのサポートが追加されました。
* JSON のシリアル化のバグを修正しました (タイムスタンプ)。
* JSON のシリアル化のバグを修正しました (列挙)。
* com.fasterxml.jackson.core:jackson-databind への依存関係が 2.9.5 にアップグレードされました。

### <a name="a-name11601160"></a><a name="1.16.0"/>1.16.0
* 直接モードの接続プールが改善されました。
* orderby 以外のクロス パーティション クエリのプリフェッチが向上しました。
* UUID の生成が強化されました。
* セッション整合性ロジックが改善されました。
* マルチポリゴンのサポートが追加されました
* コレクションに対するパーティション キー範囲の統計のサポートが追加されました。
* マルチリージョンのサポートでのバグを修正します。

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
* JSON シリアル化のパフォーマンスが向上しました。
* この SDK バージョンでは、https://aka.ms/cosmosdb-emulator からダウンロードできる Azure Cosmos DB エミュレーターの最新バージョンが必要です。

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
* Microsoft フレンド ライブラリの内部変更。

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* 単一のパーティション キーの範囲の読み取りに関する問題を修正しました。
* 短い名前のデータベースに影響する ResourceID の解析に関する問題を修正しました。
* パーティション キーのエンコードが原因で発生する問題を修正しました。

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* パーティション分割中に処理を要求する重要なバグ修正です。
* Strong および BoundedStaleness 整合性レベルの問題を修正しました。

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* ConsistentPrefix と呼ばれている新しい一貫性レベルに対応するようになりました。
* セッション モードのコレクション読み取りのバグを修正しました。

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* 2,500 RU/秒という低いパーティション分割コレクションと、100 RU/秒ずつ増加するスケールのサポートを有効にしました。
* 一部のクエリで NullRef 例外が発生する可能性があるネイティブ アセンブリのバグを修正しました。

### <a name="a-name196196"></a><a name="1.9.6"/>1.9.6
* ゲートウェイ モードでクエリに例外を引き起こす可能性のあるクエリ エンジン構成のバグが修正されました。
* コレクションの作成直後の要求に対して "所有者リソースが見つかりません" という例外を引き起こす可能性のある、セッション コンテナー内のいくつかのバグを修正しました。

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* 集計クエリ (COUNT、MIN、MAX、SUM、および AVG) のサポートを追加しました。 [集計のサポート](sql-api-sql-query.md#Aggregates)に関するトピックを参照してください。
* Change Feed のサポートを追加しました。
* RequestOptions.setPopulateQuotaInfo を介したコレクション クォータ情報のサポートを追加しました。
* RequestOptions.setScriptLoggingEnabled を介したストアド プロシージャ スクリプトのログ記録のサポートを追加しました。
* スロットル エラーが発生した場合に DirectHttps モードのクエリがハングすることがあるバグを修正しました。
* セッションの整合性モードのバグを修正しました。
* 要求レートが高いときに HttpContext の NullReferenceException を引き起こす可能性のあるバグを修正しました。
* DirectHttps モードのパフォーマンスを改善しました。

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* ConnectionPolicy.setProxy() API による簡易クライアント インスタンス ベースのプロキシのサポートを追加しました。
* DocumentClient インスタンスを適切にシャットダウンするために、DocumentClient.close() API を追加しました。
* ゲートウェイではなく、ネイティブ アセンブリからクエリ プランを派生することで、直接接続モードでのクエリのパフォーマンスを改善しました。
* ユーザーが POJO で JsonIgnoreProperties を定義しなくて済むように、FAIL_ON_UNKNOWN_PROPERTIES = false を設定しました。
* SLF4J を使用するためにログ記録をリファクタリングしました。
* 整合性リーダーの他のいくつかのバグを修正しました。

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* 直接接続モードでの接続リークを防ぐために、接続管理のバグを修正しました。
* NullReferenece 例外がスローされる場合がある TOP クエリのバグを修正しました。
* 内部キャッシュのネットワーク呼び出しの数を減らすことでパフォーマンスを改善しました。
* トラブルシューティングを適切に行うために、状態コード、ActivityID、要求 URI を DocumentClientException に追加しました。

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* 安定性を確保するために接続管理の問題を修正しました。

### <a name="a-name191191"></a><a name="1.9.1"/>1.9.1
* BoundedStaleness 一貫性レベルのサポートを追加しました。
* パーティション分割コレクションの CRUD 操作のための直接接続のサポートを追加しました。
* SQL を使用したデータベース クエリのバグを修正しました。
* セッション キャッシュでセッション トークンが正しく設定されない場合があるというバグを修正しました。

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* クロス パーティションの並列クエリのサポートを追加しました。
* パーティション分割コレクションの TOP/ORDER BY クエリのサポートを追加しました。
* 強力な一貫性のサポートを追加しました。
* 直接接続を使用する際の名前ベースの要求のサポートを追加しました。
* すべての要求再試行で ActivityId の一貫性が維持されるように修正しました。
* 同じ名前のコレクションを再作成する際のセッション キャッシュに関連するバグを修正しました。
* ジオフェンシング空間クエリに対してコレクションのインデックス作成ポリシーを指定する際の Polygon および LineString データ型を追加しました。
* Java 1.8 の Java ドキュメントに関する問題を修正しました。

### <a name="a-name181181"></a><a name="1.8.1"/>1.8.1
* 単一のパーティション コレクションをキャッシュし、パーティション キーの要求は余計にフェッチしないように、PartitionKeyDefinitionMap のバグを修正しました。
* 無効なパーティション キー値が指定された場合に再試行されないように、バグを修正しました。

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* 複数リージョンのデータベース アカウントのサポートを追加しました。
* 最大再試行回数と最大再試行待機時間をカスタマイズするオプションと共に、調整された要求での自動再試行のサポートを追加しました。  RetryOptions と ConnectionPolicy.getRetryOptions() をご覧ください。
* IPartitionResolver に基づくカスタム パーティション分割コードを非推奨にしました。 大量のストレージとスループットを必要とする場合、パーティション分割コレクションをお使いください。

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* レート制限の再試行ポリシー サポートを追加しました。  

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* ドキュメントの有効期限 (TTL) サポートを追加しました。

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* [パーティション分割コレクション](partition-data.md)と[ユーザー定義のパフォーマンス レベル](performance-levels.md)を実装しました。

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* 他の SDK と一貫性を維持するため、リトル エンディアンのハッシュ値を生成する HashPartitionResolver のバグを修正しました。

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* ハッシュおよび範囲パーティション リゾルバーを追加して、複数のパーティションにわたってシャーディング アプリケーションを支援します。

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* Upsert を実装します。 Upsert 機能をサポートするために新しい upsertXXX メソッドが追加されました。
* ID ベースのルーティングを実装します。 パブリック API の変更なし、すべて内部の変更。

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* 他の SDK とバージョン番号をそろえるため、このリリースはスキップされました

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* 地理空間インデックスをサポートします
* すべてのリソースの id プロパティを検証します。 リソースの ID には ?、/、#、\, 文字を使えず、終わりの文字をスペースにできません。
* ResourceResponse に新しいヘッダーの「インデックス変換の進行状況」を追加します。

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* V2 インデックス作成ポリシーを実装する

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK

## <a name="release-and-retirement-dates"></a>リリース日と提供終了日
Microsoft は、新しい/サポートされるバージョンに速やかに移行する目的で、SDK の提供終了を少なくともその **12 か月** 前に通知します。

新しい機能と最適化は現在の SDK にのみ追加されます。そのため、常に可能な限り最新の SDK バージョンにアップグレードすることが推奨されます。

提供終了の SDK を使用した Cosmos DB への要求は、サービスによって拒否されます。

> [!WARNING]
> バージョン **1.0.0** 以前のすべてのバージョンの SQL SDK for Java は、**2016 年 2 月 29 日**で提供が終了します。
> 
> 

<br/>

| バージョン | リリース日 | 提供終了日 |
| --- | --- | --- |
| [1.16.2](#1.16.2) |2018 年 6 月 29 日 |--- |
| [1.16.1](#1.16.1) |2018 年 5 月 16 日 |--- |
| [1.16.0](#1.16.0) |2018 年 3 月 15 日 |--- |
| [1.15.0](#1.15.0) |2017 年 11 月 14 日 |--- |
| [1.14.0](#1.14.0) |2017 年 10 月 28 日 |--- |
| [1.13.0](#1.13.0) |2017 年 8 月 25 日 |--- |
| [1.12.0](#1.12.0) |2017 年 7 月 11 日 |--- |
| [1.11.0](#1.11.0) |2017 年 5 月 10 日 |--- |
| [1.10.0](#1.10.0) |2017 年 3 月 11 日 |--- |
| [1.9.6](#1.9.6) |2017 年 2 月 21 日 |--- |
| [1.9.5](#1.9.5) |2017 年 1 月 31 日 |--- |
| [1.9.4](#1.9.4) |2016 年 11 月 24 日 |--- |
| [1.9.3](#1.9.3) |2016 年 10 月 30 日 |--- |
| [1.9.2](#1.9.2) |2016 年 10 月 28 日 |--- |
| [1.9.1](#1.9.1) |2016 年 10 月 26 日 |--- |
| [1.9.0](#1.9.0) |2016 年 10 月 3 日 |--- |
| [1.8.1](#1.8.1) |2016 年 6 月 30 日 |--- |
| [1.8.0](#1.8.0) |2016 年 6 月 14 日 |--- |
| [1.7.1](#1.7.1) |2016 年 4 月 30 日 |--- |
| [1.7.0](#1.7.0) |2016 年 4 月 27 日 |--- |
| [1.6.0](#1.6.0) |2016 年 3 月 29 日 |--- |
| [1.5.1](#1.5.1) |2015 年 12 月 31 日 |--- |
| [1.5.0](#1.5.0) |2015 年 12 月 4 日 |--- |
| [1.4.0](#1.4.0) |2015 年 10 月 5 日 |--- |
| [1.3.0](#1.3.0) |2015 年 10 月 5 日 |--- |
| [1.2.0](#1.2.0) |2015 年 8 月 5 日 |--- |
| [1.1.0](#1.1.0) |2015 年 7 月 9 日 |--- |
| [1.0.1](#1.0.1) |2015 年 5 月 12 日 |--- |
| [1.0.0](#1.0.0) |2015 年 4 月 7 日 |--- |
| 0.9.5-prelease |2015 年 3 月 9 日 |2016 年 2 月 29 日 |
| 0.9.4-prelease |2015 年 2 月 17 日 |2016 年 2 月 29 日 |
| 0.9.3-prelease |2015 年 1 月 13 日 |2016 年 2 月 29 日 |
| 0.9.2-prelease |2014 年 12 月 19 日 |2016 年 2 月 29 日 |
| 0.9.1-prelease |2014 年 12 月 19 日 |2016 年 2 月 29 日 |
| 0.9.0-prelease |2014 年 12 月 10 日 |2016 年 2 月 29 日 |

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>関連項目
Cosmos DB の詳細については、[Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) サービス ページをご覧ください。

