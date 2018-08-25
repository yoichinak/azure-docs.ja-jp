---
title: Azure SQL Data Warehouse のベスト プラクティス | Microsoft Docs
description: Azure SQL Data Warehouse のソリューションを開発する際に知っておく必要がある推奨事項とベスト プラクティス。
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/18/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: c39adbfbb85173f7ac3fa129e7551efab6ddefd6
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2018
ms.locfileid: "31798694"
---
# <a name="best-practices-for-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse のベスト プラクティス
この記事には、Azure SQL Data Warehouse で最適なパフォーマンスを実現するのに役立つベスト プラクティスがまとめられています。  この記事で取り上げている概念には、基本的なため、簡単に説明できるものから、高度なため、この記事では軽く紹介するだけのものまであります。  この記事の目的は、基本的なガイダンスを提供し、データ ウェアハウスを構築するときに重視する必要がある重要な領域に対する認識を高めることです。  各セクションでは、概念と、その概念について詳しく説明している詳細な記事を紹介します。

Azure SQL Data Warehouse を使ってみるだけの場合でも、この記事は手に負えないと思わないでください。  一連のトピックは、だいたい重要な順に並んでいます。  初めは最初のいくつかの概念に注力するだけでも、効果があります。  SQL Data Warehouse に使い慣れてきたら、戻ってきて、その他の概念もいくつか確認してください。  すべてを理解するまでに時間はかかりません。

読み込みのガイダンスについては、[データの読み込みのガイダンス](guidance-for-loading-data.md)に関するページを参照してください。

## <a name="reduce-cost-with-pause-and-scale"></a>一時停止とスケールでコストを削減する
一時停止とスケーリングを通じてコストを削減する方法については、[コンピューティングの管理](sql-data-warehouse-manage-compute-overview.md)に関するページを参照してください。 


## <a name="maintain-statistics"></a>統計を管理する
列を自動的に検出し、その列に関する統計を作成または更新する SQL Server とは異なり、SQL Data Warehouse では、統計を手動で管理する必要があります。  この点は今後変更する予定ですが、現時点では、SQL Data Warehouse プランを確実に最適化するには、統計を管理する必要があります。  オプティマイザーによって作成されたプランの有効性は、使用可能な統計によって決まります。  **統計を開始する簡単な方法は、すべての列のサンプリングされた統計を作成することです。**  データに大幅な変更が発生したときに統計を更新することも同様に重要です。  控えめなアプローチとしては、毎日または各読み込みの後に統計を更新することが挙げられます。  パフォーマンスと統計を作成および更新するコストの間には常にトレードオフの関係が存在します。 すべての統計を管理するのは時間がかかりすぎる場合は、統計を作成する列や頻繁に更新する列を限定することをお勧めします。  たとえば、新しい値が毎日追加される日付列を更新します。 **結合に使用されている列、WHERE 句で使用されている列、および GROUP BY に含まれている列に関する統計を作成すると、最も大きなメリットが得られます。**

[テーブル統計の管理][Manage table statistics]、[CREATE STATISTICS][CREATE STATISTICS]、[UPDATE STATISTICS][UPDATE STATISTICS] に関するページもご覧ください。

## <a name="group-insert-statements-into-batches"></a>INSERT ステートメントをバッチにグループ化する
INSERT ステートメントで小さなテーブルに 1 回だけ読み込む場合や、検索を定期的に再読み込みする場合は、 `INSERT INTO MyLookup VALUES (1, 'Type 1')`などのステートメントで、ニーズに十分応えることができます。  ただし、1 日を通して数千や数百万行を読み込む必要がある場合、シングルトンの INSERT だけでは対応できない可能性があります。  代わりに、ファイルに書き込み、定期的に別のプロセスを利用してそのファイルを読み込むプロセスを作成します。

[INSERT][INSERT] に関するページもご覧ください。

## <a name="use-polybase-to-load-and-export-data-quickly"></a>PolyBase を使用して、データの読み込みとエクスポートをすばやく実行する
SQL Data Warehouse では、Azure Data Factory、PolyBase、BCP など、さまざまなツールを使用したデータの読み込みとエクスポートをサポートしています。  パフォーマンスが重要でない少量のデータについては、どのツールでもニーズに十分応えることができます。  ただし、大量のデータを読み込んだり、エクスポートしたりする場合や、高速なパフォーマンスが必要な場合は、PolyBase が最適な選択肢です。  PolyBase は、SQL Data Warehouse の MPP (超並列処理) アーキテクチャを活用するように設計されています。そのため、他のツールよりも速く大量のデータを読み込むことや、エクスポートすることができます。  PolyBase の読み込みは、CTAS または INSERT INTO を使用して実行できます。  **CTAS を使用すると、トランザクション ログが最小限に抑えられ、データを一番速く読み込むことができます。**  Azure Data Factory では、PolyBase の読み込みもサポートしています。  PolyBase では、Gzip ファイルなど、さまざまなファイル形式をサポートしています。  **gzip テキスト ファイルを使用する場合にスループットを最大限引き上げるには、ファイルを 60 個以上に分割して、読み込みの並列処理を最大化します。**  全体のスループットを引き上げるには、データを同時に読み込むことを検討してください。

[データのロード][Load data]に関するページ、[PolyBase の使用ガイド][Guide for using PolyBase]、「[Azure SQL Data Warehouse loading patterns and strategies (Azure SQL Data Warehouse の読み込みパターンと戦略)][Azure SQL Data Warehouse loading patterns and strategies]」、[Azure Data Factory を使用したデータの読み込み][Load Data with Azure Data Factory]、[Azure Data Factory を使用したデータ移動][Move data with Azure Data Factory]、[CREATE EXTERNAL FILE FORMAT][CREATE EXTERNAL FILE FORMAT]、[Create table as select (CTAS)][Create table as select (CTAS)] に関するページもご覧ください。

## <a name="load-then-query-external-tables"></a>外部テーブルを読み込んで、クエリを実行する
外部テーブルとも呼ばれる Polybase は、最も高速にデータを読み込むことができますが、クエリには適していません。 SQL Data Warehouse の Polybase テーブルが現在サポートしているのは、Azure BLOB ファイルと Azure Data Lake ストレージのみです。 このファイルには、それをバックアップするためのコンピューティング リソースがありません。  つまり、SQL Data Warehouse ではこの作業をオフロードできないため、データを読み取るには、ファイルを tempdb に読み込んで、ファイル全体を読み取る必要があります。  したがって、このデータに対して複数のクエリを実行する場合は、データを一度読み込んで、クエリがローカル テーブルを使うように指定することをお勧めします。

[PolyBase の使い方ガイド][Guide for using PolyBase]もご覧ください。

## <a name="hash-distribute-large-tables"></a>ハッシュで大規模なテーブルを分散させる
既定では、テーブルはラウンド ロビン分散です。  そのため、ユーザーはテーブルの分散方法を決定することなくテーブルの作成を簡単に開始できます。  ラウンド ロビン テーブルは一部のワークロードでは十分なパフォーマンスを示しますが、多くの場合、分散列を選択すると、パフォーマンスが大幅に向上します。  列で分散したテーブルのパフォーマンスがラウンド ロビン テーブルをはるかに上回る最も一般的な例としては、2 つの大規模なファクト テーブルが結合されている場合が挙げられます。  たとえば、orders テーブルが order_id で分散されており、transactions テーブルも order_id で分散されている場合に、orders テーブルを transactions テーブルに order_id で結合すると、このクエリはパススルー クエリになり、データの移動処理が行われなくなります。  手順が減るため、クエリは高速になります。  また、データの移動の減少もクエリの高速化に貢献します。  ここでは、大まかにのみ説明します。 分散テーブルを読み込む場合は、受信データを分散キーで並べ替えないでください。読み込みが遅くなります。  分散列を選択するとパフォーマンスがどのように向上するのかや、CREATE TABLES ステートメントの WITH 句で分散テーブルを定義する方法の詳細については、次のリンクを参照してください。

[テーブルの概要][Table overview]、[テーブル分散][Table distribution]、[テーブル分散の選択][Selecting table distribution]、[CREATE TABLE][CREATE TABLE]、[CREATE TABLE AS SELECT][CREATE TABLE AS SELECT] に関するページもご覧ください。

## <a name="do-not-over-partition"></a>パーティション分割しすぎないようにする
データをパーティション分割すると、パーティション切り替えを利用してデータを管理したり、パーティションを除外してスキャンを最適化したりできるため、非常に有用ですが、パーティションが多すぎると、クエリの速度が低下する場合があります。  多くの場合、高い粒度でパーティション分割する戦略は、SQL Server では効果的ですが、SQL Data Warehouse では効果的ではありません。  パーティションが多すぎると、各パーティションの行数が 100 万を下回る場合に、クラスター化列ストア インデックスの効果が減少する可能性もあります。  SQL Data Warehouse では、バックグラウンドでデータを 60 個のデータベースにパーティション分割します。そのため、パーティションが 100 個あるテーブルを作成すると、実質的にはパーティションが 6000 個になることに留意してください。  ワークロードはそれぞれに異なるため、パーティション分割を試して、自分のワークロードに最適な数を判断することをお勧めします。  SQL Server の場合よりも粒度を下げることを検討してください。  たとえば、日単位ではなく、週単位や月単位のパーティションを使用します。

[テーブル パーティション][Table partitioning]に関するページもご覧ください。

## <a name="minimize-transaction-sizes"></a>トランザクション サイズを最小限に抑える
トランザクションで INSERT、UPDATE、DELETE ステートメントを実行して、失敗した場合は、ロールバックする必要があります。  ロールバック時間が長くならないようにするには、できる限りトランザクション サイズを最小限に抑えます。  そのためには、INSERT、UPDATE、DELETE ステートメントを複数に分割します。  たとえば、INSERT に 1 時間かかると予測される場合は、可能であれば、INSERT を 4 つに分割すると、それぞれの実行時間は 15 分になります。  CTAS、TRUNCATE、DROP TABLE、空のテーブルへの INSERT など、特殊な最小ログ記録のケースを活用すると、ロールバックのリスクが軽減されます。  ロールバックを回避するもう 1 つの方法としては、データ管理のためのパーティション切り替えなど、メタデータのみの操作を使用します。  たとえば、DELETE ステートメントを実行して、テーブル内の order_date が 2001 年 10 月のすべての行を削除する代わりに、月単位でデータをパーティション分割し、該当するデータを含むパーティションを別のテーブルの空のパーティションに切り替えします (ALTER TABLE の例を参照してください)。  パーティション分割されていないテーブルについては、DELETE を使用する代わりに、CTAS を使用して、テーブルに保持するデータを書き込むことを検討してください。  CTAS にかかる時間が同じ場合でも、トランザクション ログが最小限に抑えられ、必要なときにすばやく取り消すことができるため、CTAS は非常に安全に実行できる操作です。

[トランザクションの概要][Understanding transactions]、[トランザクションの最適化][Optimizing transactions]、[テーブル パーティション][Table partitioning]、[TRUNCATE TABLE][TRUNCATE TABLE]、[ALTER TABLE][ALTER TABLE]、[Create table as select (CTAS)][Create table as select (CTAS)] に関するページもご覧ください。

## <a name="use-the-smallest-possible-column-size"></a>できる限り最小の列サイズを使用する
DDL を定義するときに、データをサポートする最小のデータ型を使用すると、クエリのパフォーマンスが向上します。  これは、CHAR および VARCHAR 列の場合に特に重要です。  列の最長の値が 25 文字の場合は、列を VARCHAR(25) として定義します。  すべての文字列を既定の長さで定義しないようにします。  さらに、VARCHAR で済む場合は、NVARCHAR を使用せずに、列を VARCHAR として定義します。

[テーブルの概要][Table overview]、[テーブルのデータ型][Table data types]、[CREATE TABLE][CREATE TABLE] に関するページもご覧ください。

## <a name="use-temporary-heap-tables-for-transient-data"></a>一時的なデータには一時的なヒープ テーブルを使用する
データを一時的に SQL Data Warehouse に読み込む際は、ヒープ テーブルを使用すると、プロセス全体が高速になる場合があります。  さまざまな変換を実行する前にデータをステージングするためにのみ読み込む場合は、ヒープ テーブルにテーブルを読み込むと、データをクラスター化列ストア テーブルに読み込む場合よりもはるかに高速に読み込まれます。  さらに、テーブルを永続記憶域に読み込むよりも、データを一時テーブルに読み込んだ方が読み込み速度が大幅に向上します。  一時テーブルは、"#" で始まり、作成元のセッションからしかアクセスできないため、一部のシナリオでしか動作しません。   ヒープ テーブルは、CREATE TABLE の WITH 句で定義します。  一時テーブルを使用する場合は、その一時テーブルの統計も必ず作成してください。

[一時テーブル][Temporary tables]、[CREATE TABLE][CREATE TABLE]、[CREATE TABLE AS SELECT][CREATE TABLE AS SELECT] に関するページもご覧ください。

## <a name="optimize-clustered-columnstore-tables"></a>クラスター化列ストア テーブルを最適化する
クラスター化列ストア インデックスは、SQL Data Warehouse にデータを格納する最も効率的な方法の 1 つです。  既定では、SQL Data Warehouse のテーブルは、クラスター化列ストアとして作成されます。  列ストア テーブルに対するクエリのパフォーマンスを最大限に引き出すには、セグメントの質が高いことが重要です。  行を列ストア テーブルに書き込む際にメモリ負荷が発生すると、列ストア セグメントの質が低下する可能性があります。  セグメントの質は、圧縮後の行グループに含まれる行の数を使って判断できます。  クラスター化列ストア テーブルのセグメントの品質を検出して向上させる詳細な手順については、[テーブル インデックス][Table indexes]に関するページの「[列ストア インデックスの品質の低さの原因][Causes of poor columnstore index quality]」を参照してください。  列ストア セグメントの質を高めることが非常に重要であるため、中規模または大規模リソース クラスのユーザー ID を使用してデータを読み込むことをお勧めします。 低い[データ ウェアハウス ユニット](what-is-a-data-warehouse-unit-dwu-cdwu.md)を使用すると、大きいリソース クラスを読み込みユーザーに割り当てることになります。

通常、テーブルあたりの行数が 100 万を超え、各 SQL Data Warehouse テーブルが 60 個にパーティション分割されるまで、列ストア テーブルは圧縮された列ストア セグメントにデータをプッシュしません。そのため、経験上、テーブルの行数が 6,000 万を超えない限り、列ストア テーブルはクエリにとってメリットがありません。  6,000 万行未満のテーブルについては、列ストア インデックスを作成してもメリットがありません。  また、デメリットもありません。  さらに、データをパーティション分割する場合は、クラスター化列ストア インデックスの恩恵を受けるには、各パーティションに 100 万行が必要なことを考慮に入れる必要があります。  テーブルに 100 個のパーティションがある場合に、クラスター化列ストアの恩恵を受けるには、少なくとも 60 億行必要です (60 個のディストリビューション * 100 個のパーティション * 100 万行)。  この例でテーブルに 60 億行もない場合は、パーティションの数を減らすか、代わりにヒープ テーブルを使用することを検討してください。  列ストア テーブルの代わりに、ヒープ テーブルとセカンダリ インデックスを使用してパフォーマンスが向上するかどうかを試してみる価値もあります。

列ストア テーブルに対してクエリを実行する場合は、必要な列のみを選択すると、クエリの実行速度が向上します。  

[テーブル インデックス][Table indexes]、[列ストア インデックス][Columnstore indexes guide]、[列ストア インデックスの再構築][Rebuilding columnstore indexes]に関するページもご覧ください。

## <a name="use-larger-resource-class-to-improve-query-performance"></a>大きなリソース クラスを使用して、クエリのパフォーマンスを向上させる
SQL Data Warehouse では、クエリにメモリを割り当てる方法としてリソース グループを使用します。  既定では、ディストリビューションごとに 100 MB のメモリが与えられる小規模リソース クラスにすべてのユーザーが割り当てられます。  常に 60 個のディストリビューションが存在し、各ディストリビューションに最低 100 MB が割り当てられるため、システム全体のメモリの割り当ての合計は 6,000 MB (6 GB 弱) です。  大規模な結合やクラスター化列ストア テーブルへの読み込みなど、特定のクエリについては、割り当てるメモリを増やすと効果的です。  純粋なスキャンなどのクエリでは、効果はありません。  一方で、より大きなリソース クラスを利用すると、同時実行に影響します。そのため、すべてのユーザーをより大きなリソース クラスに移行する前に、その点を考慮する必要があります。

「[ワークロード管理用のリソース クラス](resource-classes-for-workload-management.md)」も参照してください。

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>小さいリソース クラスを使用して、同時実行を増やす
ユーザー クエリの遅延が長いと感じている場合は、ユーザーが大きなリソース クラスで実行しており、同時実行スロットを大量に使用していることが原因で、他のクエリがキューに配置されている可能性があります。  ユーザー クエリがキューに配置されているかどうかを確認するには、 `SELECT * FROM sys.dm_pdw_waits` を実行して、行が返されるかどうかを確認します。

「[ワークロード管理用のリソース クラス](resource-classes-for-workload-management.md)」と [sys.dm_pdw_waits][sys.dm_pdw_waits] に関するページも参照してください。

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>DMV を使用して、クエリを監視および最適化する
SQL Data Warehouse には、クエリの実行を監視するために使用できる DMV がいくつか用意されています。  以下の監視に関する記事では、実行中のクエリの詳細を確認する方法についての詳細な手順を説明しています。  これらの DMV でクエリをすばやく見つけるには、クエリで LABEL オプションを使用すると便利です。

「[DMV を利用してワークロードを監視する][Monitor your workload using DMVs]」、[ラベル][LABEL]、[OPTION][OPTION]、[sys.dm_exec_sessions][sys.dm_exec_sessions]、[sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests]、[sys.dm_pdw_request_steps][sys.dm_pdw_request_steps]、[sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests]、[sys.dm_pdw_dms_workers]、[DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN]、[sys.dm_pdw_waits][sys.dm_pdw_waits] に関するページもご覧ください。

## <a name="other-resources"></a>その他のリソース
一般的な問題と解決方法については、[トラブルシューティング][Troubleshooting]に関する記事もご覧ください。

この記事で目的のトピックが見つからない場合は、ページの左側にある [Search for docs] を使用して、すべての Azure SQL Data Warehouse ドキュメントで検索を実行してみてください。  [Azure SQL Data Warehouse フォーラム][Azure SQL Data Warehouse MSDN Forum]は、他のユーザーや SQL Data Warehouse 製品グループに質問できる場所です。  Microsoft では、このフォーラムを積極的に監視し、お客様からの質問に他のユーザーや Microsoft のスタッフが回答しているかどうかを確認しています。  Stack Overflow で質問したい方のために、[Azure SQL Data Warehouse Stack Overflow フォーラム][Azure SQL Data Warehouse Stack Overflow Forum]も用意しています。

最後に、[Azure SQL Data Warehouse のフィードバック][Azure SQL Data Warehouse Feedback] ページを使用して、機能に関するご要望を是非お寄せください。  要望の追加や他の要求への投票は、機能の優先順位を決める際に役立ちます。

<!--Image references-->

<!--Article references-->
[Create a support ticket]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Create table as select (CTAS)]: ./sql-data-warehouse-develop-ctas.md
[Table overview]: ./sql-data-warehouse-tables-overview.md
[Table data types]: ./sql-data-warehouse-tables-data-types.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Table indexes]: ./sql-data-warehouse-tables-index.md
[Causes of poor columnstore index quality]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuilding columnstore indexes]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Table partitioning]: ./sql-data-warehouse-tables-partition.md
[Manage table statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary tables]: ./sql-data-warehouse-tables-temporary.md
[Guide for using PolyBase]: ./guidance-for-loading-data.md
[Load data]: ./design-elt-data-loading.md
[Move data with Azure Data Factory]: ../data-factory/transform-data-using-machine-learning.md
[Load data with Azure Data Factory]: ../data-factory/load-azure-sql-data-warehouse.md
[Load data with bcp]: /sql/tools/bcp-utility
[Load data with PolyBase]: ./load-data-wideworldimportersdw.md
[Monitor your workload using DMVs]: ./sql-data-warehouse-manage-monitor.md
[Pause compute resources]: ./sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Resume compute resources]: ./sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[Scale compute resources]: ./sql-data-warehouse-manage-compute-overview.md#scale-compute
[Understanding transactions]: ./sql-data-warehouse-develop-transactions.md
[Optimizing transactions]: ./sql-data-warehouse-develop-best-practices-transactions.md
[Troubleshooting]: ./sql-data-warehouse-troubleshoot.md
[LABEL]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->
[ALTER TABLE]: https://msdn.microsoft.com/library/ms190273.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[CREATE TABLE AS SELECT]: https://msdn.microsoft.com/library/mt204041.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: https://msdn.microsoft.com/library/mt204017.aspx
[INSERT]: https://msdn.microsoft.com/library/ms174335.aspx
[OPTION]: https://msdn.microsoft.com/library/ms190322.aspx
[TRUNCATE TABLE]: https://msdn.microsoft.com/library/ms177570.aspx
[UPDATE STATISTICS]: https://msdn.microsoft.com/library/ms187348.aspx
[sys.dm_exec_sessions]: https://msdn.microsoft.com/library/ms176013.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_request_steps]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: https://msdn.microsoft.com/library/mt203889.aspx
[sys.dm_pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_waits]: https://msdn.microsoft.com/library/mt203893.aspx
[Columnstore indexes guide]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->
[Selecting table distribution]: https://blogs.msdn.microsoft.com/sqlcat/2015/08/11/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Azure SQL Data Warehouse Feedback]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Data Warehouse MSDN Forum]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Azure SQL Data Warehouse Stack Overflow Forum]:  http://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL Data Warehouse loading patterns and strategies]: http://blogs.msdn.microsoft.com/sqlcat/2017/05/17/azure-sql-data-warehouse-loading-patterns-and-strategies/
