---
title: Azure Data Catalog の新機能 | Microsoft Docs
description: この記事では、Azure Data Catalog に追加された新機能の概要を説明します。
services: data-catalog
documentationcenter: ''
author: steelanddata
manager: NA
editor: ''
tags: ''
ms.assetid: 1201f8d4-6f26-4182-af3f-91e758a12303
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.date: 01/18/2018
ms.author: maroche
ms.openlocfilehash: 3d2c0d21d0cf4cc0654c8f7ee2debee4f0320461
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39577064"
---
# <a name="whats-new-in-azure-data-catalog"></a>Azure Data Catalog の新機能
**Azure Data Catalog** の更新プログラムは定期的にリリースされます。 一部のリリースではバックエンド サービス機能に重点を置いているため、すべてのリリースにユーザー向けの新機能が含まれているわけではありません。 ここでは、Azure Data Catalog サービスに追加されたユーザー向けの新機能について説明します。

## <a name="whats-new-for-november-2017"></a>2017 年 11 月の新機能 
2017 年 11 月の時点で、Azure Data Catalog には次の機能が追加されています。

* Data Catalog ポータル内のビジネス用語集の特定の用語に対する直接リンクのサポート。 ユーザーは、ビジネス用語集からリンクをコピーし、ドキュメント、メール、レポート、または他の場所に埋め込んで、用語集の用語の定義に直接リンクできます。
* Azure Active Directory サービス プリンシパルのサポート。 Data Catalog 管理者は、クライアント アプリケーションがサービス プリンシパルを使ってカタログにアクセスするのを承認することができ、ユーザーやセキュリティ グループにアクセス許可を付与するのと同様に、それらのアプリケーションに固有のアクセス許可を付与できます。 詳しくは、「[Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](../active-directory/develop/app-objects-and-service-principals.md)」をご覧ください。
* Data Catalog データ ソース登録ツールを使って Azure SQL Database および Azure SQL Data Warehouse のデータ ソースに接続するときの、Azure Active Directory 認証のサポート。 詳しくは、「[Azure Active Directory 認証を使用して SQL Database または SQL Data Warehouse を認証する](../sql-database/sql-database-aad-authentication.md)」をご覧ください。


## <a name="whats-new-for-september-2017"></a>2017 年 9 月の新機能 
2017 年 9 月の時点で、Azure Data Catalog には次の機能が追加されています。

* データ ソースの登録ツールを使用して関連するテーブルを登録するときに、結合リレーションシップのメタデータを DB2 データ ソースから抽出できるようになりました。
* データ ソースの登録ツールを使用して MongoDB バージョン 3.4 のデータ ソースを登録できるようになりました。
* データ カタログから、データベースまたはその他のコンテナーを削除するときに、1 回の操作に含まれるオブジェクトのすべてのメタデータを削除できるようになりました。
* Data Catalog ポータルでの検索を絞り込む際に、最大 1,000 個のタグ、ビジネス用語集の用語、その他の検索ファセットを表示できるようになりました。


## <a name="whats-new-for-august-2017"></a>2017 年 8 月の新機能 
2017 年 8 月の時点で、Azure Data Catalog には次の機能が追加されています。

*   新しい開発者サンプルが利用できるようになり、Data Catalog REST API を使用したリレーションシップのメタデータの作成および管理ができるようになりました。 *"Data Catalog へのリレーションシップ情報のインポート"* のサンプルを、[Data Catalog のコード サンプル ページ](https://azure.microsoft.com/resources/samples/?service=data-catalog&sort=0)から入手できます。 
* データ ソースの登録ツールを使用して関連するテーブルを登録するときに、結合リレーションシップのメタデータを Teradata データ ソースから抽出できるようになりました。
* データ ソースの登録ツールを使用して SQL Server データ ソースを登録するときに、SQL Server テーブル値関数 (TVF) オブジェクトがサポートされました。
* 更新と調整を重ね、Data Catalog ポータルのパフォーマンスと使いやすさを改善しました。

## <a name="whats-new-for-july-2017"></a>2017 年 7 月の新機能 
2017 年 7 月の時点で、Azure Data Catalog には次の機能が追加されています。
*   次の操作を含む、許可されたメタデータ操作のよりきめ細かい制御がサポートされました。
    - カタログ管理者は、ユーザーがタグおよび関連するメタデータをカタログに投稿できる機能を制限できるため、カタログへのアクセスを読み取り専用にできます。
    - カタログ管理者は、ユーザーがカタログに新しいデータ ソースを登録できる機能を制限できます。
    - カタログ管理者は、ユーザーがカタログのデータ資産メタデータの所有権を得られる機能を制限できます。
    - Azure Active Directory セキュリティ グループとユーザーに、アクセス許可の管理を容易にするための許可を与えることができます。
* 登録済みのデータ資産間のリレーションシップ、および Data Catalog ポータル上の関連するデータ資産の検出がサポートされるようになりました。これには、次のサポートも含まれます。
    - Data Catalog データ ソースの登録ツールを使用した SQL Server (Azure SQL Database を含む)、Oracle および MySQL データ ソースからのリレーションシップのメタデータの抽出。
    - Data Catalog ポータル上の資産メタデータ閲覧時に関連するデータ資産を検出。
    - Data Catalog REST API を使用したデータ資産間のリレーションシップの定義、検出および管理操作。

Data Catalog のアクセス許可を管理する方法の詳細については、「[データ カタログとデータ資産へのアクセスをセキュリティで保護する方法](data-catalog-how-to-secure-catalog.md)」をご覧ください。
Data Catalog のリレーションシップの詳細については、「[Azure Data Catalog で関連するデータ資産を表示する方法を説明します。](data-catalog-how-to-view-related-data-assets.md)」をご覧ください。

## <a name="whats-new-for-june-2017"></a>2017 年 6 月の新機能 
2017 年 6 月の時点で、Azure Data Catalog には次の機能が追加されています。
*   Sybase、Apache Cassandra および MongoDB のデータ ソースのサポート。 ユーザーは、Cassandra と MongoDB のデータベースとテーブル、および Sybase のデータベース、テーブルおよびビューの登録と検出ができるようになりました。

> [!NOTE]
> レコードや配列などの複雑なデータ型の列を含む MongoDB および Cassandra のテーブルを登録すると、これらの列は Data Catalog に追加されるメタデータには含まれなくなります。

## <a name="whats-new-for-may-2017"></a>2017 年 5 月の新機能 
2017 年 5 月の時点で、Azure Data Catalog には次の機能が追加されています。
*   ビジネス用語集の用語の一括インポートに使用できる新しい開発者向けサンプル。 用語集の一括インポートのサンプルは、「[Data Catalog 開発者向けサンプル](https://docs.microsoft.com/azure/data-catalog/data-catalog-samples)」ページで入手できます。 
*   Azure Data Catalog ポータルでの ODBC 接続情報の編集のサポート。 データ資産の所有者と Data Catalog の管理者は、登録済みの ODBC データ ソースの接続情報を編集することができます。データ ソースを再登録する必要はありません。
*   ビジネス用語集の定義と説明でクリッカブル URL のサポート。 ビジネス用語集の用語の説明と定義のプロパティに URL が含まれている場合、その URL は Data Catalog ポータルではハイパーリンクとして表示されます。
*   エキスパートのメール アドレスに加え、エキスパートの名前の表示のサポート。 Data Catalog ポータルでデータ資産のプロパティにエキスパートを表示すると、Azure Active Directory からエキスパートのフルネームがツールヒントに表示されます。

## <a name="whats-new-for-april-2017"></a>2017 年 4 月の新機能 
2017 年 4 月の時点で、Azure Data Catalog には次の機能が追加されています。
*   ODBC データ ソースのサポート。 ユーザーは Data Catalog データ ソース登録ツールを使用して、ODBC のデータベース、テーブル、ビューの登録と検出ができるようになりました。

## <a name="whats-new-for-march-2017"></a>2017 年 3 月の新機能 
2017 年 3 月の時点で、Azure Data Catalog には次の機能が追加されています。
*   Data Catalog の管理者に対する AAD セキュリティ グループの使用のサポート。
*   Azure Data Catalog で新しい Azure リージョンが利用できるようになりました。 お客様は Azure Data Catalog を、米国東部、米国西部、西ヨーロッパ、オーストラリア東部、北ヨーロッパおよび東南アジアに加え、米国中西部リージョンでもプロビジョニングできます。 詳細については、「 [Azure のリージョン](https://azure.microsoft.com/regions/)」をご覧ください。

## <a name="whats-new-for-february-2017"></a>2017 年 2 月の新機能 
2017 年 2 月の時点で、Azure Data Catalog には次の機能が追加されています。
*   Data Catalog データ ソース登録ツールでの詳細設定のサポート。 ユーザーは、大規模なデータ ソースを登録するときに、コマンド タイムアウト値を指定できます。
*   FTP および PostgreSQL データ ソースのサポート。 ユーザーは、FTP のファイルとディレクトリ、および PostgreSQL のデータベース、テーブル、ビューの登録と検出ができるようになりました。

## <a name="whats-new-for-january-2017"></a>2017 年 1 月の新機能 
2017 年 1 月の時点で、Azure Data Catalog には次の機能が追加されています。
*   Azure Data Catalog は [CSA STAR](https://www.microsoft.com/en-us/trustcenter/compliance/csa-star-certification) に準拠しています。
*   [Excel 2016 と Power Query for Excel での取得と変換](https://support.office.com/article/Introduction-to-Microsoft-Power-Query-for-Excel-6E92E2F4-2079-4E1F-BAD5-89F6269CD605)との統合。 Excel ユーザーは、Excel 内から Azure Data Catalog を使用してクエリの共有とクエリの検出ができます。 この機能は、Power BI Pro ライセンスを持つユーザーが利用できます。

## <a name="whats-new-for-december-2016"></a>2016 年 12 月の新機能
2016 年 12 月の時点で、Azure Data Catalog には次の機能が追加されています。
*   Azure Data Catalog は [HIPAA](https://www.microsoft.com/en-us/TrustCenter/Compliance/hipaa) および [EU Model Clauses](https://www.microsoft.com/en-us/TrustCenter/Compliance/EU-Model-Clauses) に準拠しています。
*   データ ソース接続情報の編集をサポート。 データ資産の所有者と Data Catalog の管理者は、登録済みのデータ ソースの接続情報を編集することができます。データ ソースを再登録する必要はありません。
*   Salesforce.com のデータ ソースのサポート。 ユーザーは Salesforce オブジェクトの登録と検出ができるようになりました。


## <a name="whats-new-for-november-2016"></a>2016 年 11 月の新機能
2016 年 11 月の時点で、Azure Data Catalog には次の機能が追加されています。
*   Azure Data Catalog は [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/compliance/iso-iec-27001) および [ISO/IEC 27018](https://www.microsoft.com/en-us/TrustCenter/Compliance/iso-iec-27018) に準拠しています。
*   Data Catalog ポータルおよび REST API を使用した ODBC データ ソースの手動登録のサポート。

## <a name="whats-new-for-september-2016"></a>2016 年 9 月の新機能
2016 年 9 月の時点で、Azure Data Catalog には次の機能が追加されています。

* IBM DB2 データ ソースのサポート。 DB2 のデータベース、テーブル、ビューの登録と検出を実行できるようになりました。
* Azure Cosmos DB データ ソースのサポート。 Cosmos DB データベースとコレクションの登録と検出を実行できるようになりました。
* Data Catalog ポータルでのカタログ名のカスタマイズのサポート。 カタログ管理者が、ポータルのタイトルに表示されるテキスト (組織名など) を指定できるようになりました。

## <a name="whats-new-for-august-2016"></a>2016 年 8 月の新機能
2016 年 8 月の時点で、Azure Data Catalog には次の機能が追加されています。

* SQL Server マスター データ サービス (MDS) データ ソースの登録のための機能拡張。 Data Catalog データ ソース登録ツールを使用して MDS エンティティを登録する際に、プレビューとデータ プロファイルを含めることができるようになりました。
* 管理者定義の組織用検索条件の保存のサポート。 Data Catalog 管理者が Data Catalog ポータルで検索条件を保存する際に、検索条件を個人用に保存するか、カタログのすべてのユーザー用に保存するかを選択できるようになりました。 組織用に保存した検索条件はカタログのすべてのユーザーに共有され、データ ソース検出の標準化された開始点となります。
* Data Catalog ポータルでのプロパティ表示の更新。 すべてのデータ資産のプロパティをサイズ変更可能な 1 つのウィンドウで表示および管理できるようになり、エクスペリエンスの一貫性と検出のしやすさが向上しました。

## <a name="whats-new-for-july-2016"></a>2016 年 7 月の新機能
2016 年 7 月の時点で、Azure Data Catalog には次の機能が追加されています。

* SQL Server マスター データ サービス (MDS) データ ソースのサポート。 MDS のモデルとエンティティの登録と検出を実行できるようになりました。
* SQL Server のストアド プロシージャのサポート。 SQL Server データ ソースのストアド プロシージャ オブジェクトの登録と検出を実行できるようになりました。
* Azure Data Catalog ポータルとデータ ソース登録ツールで対応する言語が追加されました (合計で 18 の言語に対応)。 Azure Data Catalog ユーザー エクスペリエンスは、Windows または Web ブラウザーに指定された言語設定に基づいてローカライズされます。
* Data Catalog ポータルのホームページの更新と改良 (パフォーマンスの向上、ユーザー エクスペリエンスの効率化など)。

## <a name="whats-new-for-june-2016"></a>2016 年 6 月の新機能
2016 年 6 月の時点で、Azure Data Catalog には次の機能が追加されています。

* Data Catalog ポータルでのデータ資産の検出時に、リスト ビュー内の列のサイズ変更をサポート。 タグや説明など、長い資産メタデータの個々の列サイズを変更して、読みやすくできるようになりました。
* Data Catalog ポータルの [開く] メニューへの Power Query for Excel の追加。 Excel 2016、または [Power Query for Excel](https://support.office.com/article/Introduction-to-Microsoft-Power-Query-for-Excel-6E92E2F4-2079-4E1F-BAD5-89F6269CD605) アドインがインストールされている Excel 2010 と Excel 2013 でサポートされているデータ ソースを開けるようになりました。
* Azure Table Storage データ ソースのサポート。 Azure Storage データ ソースのテーブル オブジェクトの登録と検出を実行できるようになりました。

## <a name="whats-new-for-may-2016"></a>2016 年 5 月の新機能
2016 年 5 月の時点で、Azure Data Catalog には次の機能が追加されています。

* カタログ管理者がビジネス用語と階層を定義し、一般的なビジネス語彙を作成できるビジネス用語集。 ユーザーは、登録したデータ資産に用語集の用語でタグを付けて、カタログの内容を検出しやすく、わかりやすくすることができます。 詳細については、「 [管理タグ付け用のビジネス用語集を設定する方法](data-catalog-how-to-business-glossary.md)  
* Data Catalog ビジネス用語集の機能強化により、ユーザーが 1 回の操作で複数の用語集の用語を更新できるようになっています。 ユーザーは、複数の用語を選択して次の各フィールドを編集できます。
  * 親の用語: 新しい親の用語を選択することができ、選択した用語はいずれも選択した親の用語の子になるように更新されます。 選択したすべての用語が同じ親を持つ場合、その親はテキスト ボックスに表示され、それ以外の場合、親の用語フィールドには空白が設定されます。   
  * タグと関係者: 複数のデータ資産にタグ付けする場合と同じ操作方法で、複数の用語集の用語に対してタグと関係者を追加および削除できます。

> [!NOTE]
> ビジネス用語集は、Azure Data Catalog の Standard Edition でのみ使用できます。 無料エディションには、管理タグ付けまたはビジネス用語集の機能がありません。

## <a name="whats-new-for-march-2016"></a>2016 年 3 月の新機能
2016 年 3 月の時点で、Azure Data Catalog には次の機能が追加されています。

* Azure Data Catalog サービスの検索機能およびカタログ資産管理機能にプログラムでアクセスするために統合された REST API エンドポイント。 この検索 API エンドポイントとカタログ API エンドポイントは 2016 年 3 月 21 日に非推奨となり、提供が終了します。 API のセマンティクスに対する変更はありません。 エンドポイント URI だけが変更されます。 詳細については、[Azure Data Catalog の REST API リファレンス](https://msdn.microsoft.com/library/azure/mt267595.aspx)のページを参照してください。 API サンプルについては、[Azure Data Catalog 開発者向けサンプル](data-catalog-samples.md)のページを参照してください。

## <a name="whats-new-for-february-2016"></a>2016 年 2 月の新機能
2016 年 2 月の時点で、Azure Data Catalog には次の機能が追加されています。

* Azure Data Catalog データ ソース登録ツールでのデータ ソース選択の操作性が再設計されました。 データ ソース登録ツールが更新され、Azure Data Catalog がサポートするデータ ソースからの特定と選択が簡単になりました。
* Azure Data Catalog ポータルとデータ ソース登録ツールが追加の 10 言語に対応するようになりました。 英語に加え、Azure Data Catalog 環境はドイツ語、スペイン語、フランス語、イタリア語、日本語、韓国語、ポルトガル語 (ブラジル)、ロシア語、簡体字中国語、繁体字中国語で利用できます。 Azure Data Catalog ユーザー エクスペリエンスは、Windows またはユーザーの Web ブラウザーに指定された言語設定に基づいてローカライズされます。
* ビジネス継続性と障害復旧のための Azure Data Catalog データの geo レプリケーションがサポートされるようになりました。 データ ソースのメタデータとクラウドソースの注釈を含むすべての Azure Data Catalog コンテンツが、お客様への追加コストなしで 2 つの Azure リージョン間でレプリケートされるようになりました。 Azure リージョンは、「[ビジネス継続性とディザスター リカバリー (BCDR): Azure のペアになっているリージョン](../best-practices-availability-paired-regions.md)」に記載された対応表に従い、500 マイル以上離れた Azure リージョンと事前にペアリングされています。
* Azure Data Catalog によって使用される Azure サブスクリプションの変更のサポート。 Azure Data Catalog の管理者は、Azure Data Catalog ポータルの [設定] ページを使用して、課金用に別の Azure サブスクリプションを選択できます。

## <a name="whats-new-for-january-2016"></a>2016 年 1 月の新機能
2016 年 1 月の時点で、Azure Data Catalog には次の機能が追加されています。

* 追加のデータ ソースを手動で登録するためのサポート。 Azure Data Catalog ポータルで [手動エントリの作成] を使用したり、Azure Data Catalog REST API を使用して次のデータ ソースを登録したりできるようになりました。
  * OData - 関数、エンティティ セット、エンティティ コンテナー
  * HTTP - ファイル、エンドポイント、レポート、サイト
  * ファイル システム - ファイル
  * SharePoint - リスト
  * FTP - ファイル、ディレクトリ
  * Salesforce.com - オブジェクト
  * DB2 - テーブル、ビュー、データベース
  * PostgreSQL - テーブル、ビュー、データベース
* Azure SQL DB と Azure SQL Data Warehouse を含む SQL Server データ ソースに対する "SQL Server Data Tools で開く" のサポート。  
* SAP HANA のビューおよびパッケージを登録して検出するためのサポート。 Azure Data Catalog データ ソース登録ツールを使用して SAP HANA データ ソースを登録し、Azure Data Catalog ポータルを使用して登録済み SAP HANA データ ソースの注釈付けや検出を行うことができます。
* Azure Data Catalog ポータルでデータ資産をピン留め/ピン留め解除する機能。 データ資産のピン留めを選択することで、再検出/再利用しやすくなります。
* Azure Data Catalog ポータルの新たに再設計されたホーム ページ。 新しいホーム ページには、最近公開またはピン留めされた資産、保存された検索など、現在のユーザーのアクティビティに関する詳細な情報のほか、カタログ全体でのアクティビティに関する詳細な情報も含まれています。
* Azure Data Catalog ポータルでの固定のユーザー設定のサポート。 ユーザー エクスペリエンスの設定 (グリッドまたはタイル ビュー、ページあたりの結果の数、検索結果の強調表示のオンまたはオフなど) はユーザー セッション間で永続化されます。
* Azure Data Catalog が、2 つの新しい Azure リージョンで利用できるようになりました。 お客様は Azure Data Catalog を、米国東部、米国西部、西ヨーロッパ、オーストラリア東部に加え、北ヨーロッパおよび東南アジア リージョンでもプロビジョニングできます。 詳細については、「 [Azure のリージョン](https://azure.microsoft.com/regions/)」をご覧ください。

> [!NOTE]
> "SQL Server Data Tools で開く" には、Visual Studio 2013 Update 4 と SQL Server Tooling がインストールされている必要があります。 SQL Server Data Tools の最新バージョンをインストールするには、「 [SQL Server Data Tools (SSDT) のダウンロード](https://msdn.microsoft.com/library/mt204009.aspx)」を参照してください。


## <a name="whats-new-for-december-2015"></a>2015 年 12 月の新機能
2015 年 12 月の時点で、Azure Data Catalog には次の機能が追加されています。

* Azure SQL Data Warehouse データ ソースに対するデータ プロファイルのサポート。 Azure SQL Data Warehouse のテーブルとビューを登録するとき、ユーザーはデータ ソースから抽出されたメタデータと共にデータ プロファイル メトリックを含めることができます。
* MySQL のオブジェクトおよびデータベースを登録して検出するためのサポート。 ユーザーは Azure Data Catalog データ ソース登録ツールを使用して MySQL データ ソースを登録し、Azure Data Catalog ポータルを使用して登録済み MySQL データ ソースの注釈付けや検出を行うことができます。
* Teradata データ ソース用の SPNEGO および Windows 認証のサポート。 Teradata のテーブルとビューの登録時に、LDAP および TD2 認証だけでなく、SPNEGO および Windows 認証を使用して Teradata に接続することを選択できます。
* Azure Data Lake Store のデータ ソースのサポート。 ユーザーは、Azure Data Catalog を使用して Azure Data Lake Store のデータ ソースを登録および検出できるようになりました。
* Azure Data Catalog データ ソース登録ツールでのネットワーク プロキシ設定の手動指定のサポート。 ユーザーはツールの [ようこそ] ページで [プロキシ設定を変更します] を選択し、ツールで使用されるプロキシ アドレスとポートを指定することができます。

## <a name="whats-new-for-november-2015"></a>2015 年 11 月の新機能
2015 年 11 月の時点で、Azure Data Catalog には次の機能が追加されています。

* SQL Server (Azure SQL Database を含む) と Oracle データ ソースの Azure Data Catalog ポータルの中から接続文字列の表示とコピーを行う機能。 SQL Server または Oracle のテーブル、ビュー、またはデータベースの接続情報に含まれる [接続文字列の表示] リンクをクリックすることで、データ ソースに接続するために使用される接続文字列を確認できます。 SQL Server データ ソースには、ADO.NET、ODBC、OLEDB、および JDBC 接続文字列があります。 Oracle データ ソースには、ODBC および OLEDB 接続文字列があります。
* Teradata のテーブルとビューの登録時にデータ プロファイルを含めることのサポート。
* SQL Server (Azure SQL DB と Azure SQL Data Warehouse を含む)、Server Analysis Services、Azure Storage、および HDFS ソースに対する [Power BI Desktop で開く] のサポート。  
* Teradata データ ソース用の LDAP 認証のサポート。 Teradata のテーブルとビューの登録時に、TD2 認証ではなく LDAP 認証を使用して Teradata に接続することを選択できます。
* Teradata データ ソースに対する [Excel で開く] のサポート。
* Azure Data Catalog ポータルでの最近検索した語句のサポート。 ポータルで検索するときに、最近使用した検索語句から選択することで、検索時間を短縮できます。
* Teradata データ ソースのプレビューのサポート。 Teradata のテーブルとビューを登録するとき、ユーザーはデータ ソースから抽出されたメタデータのスナップショット レコードを含めることができます。
* Azure SQL Data Warehouse データ ソースに対する [Excel で開く] のサポート。
* 手動で登録されたデータ資産に対する列レベルのスキーマの定義と編集のサポート。 Azure Data Catalog ポータルを使用してデータ資産を手動で作成した後、ユーザーはデータ資産のプロパティで列定義を追加できます。
* Azure Data Catalog を検索するときの "has" クエリのサポート。これにより、特定のメタデータを持つ登録済みデータ資産を検索できます。 Azure Data Catalog のクエリ構文には次のものが含まれるようになりました。

| クエリ構文 | 目的 |
| --- | --- |
| `has:previews` |プレビューを含むデータ資産を検索します。 |
| `has:documentation` |ドキュメントが提供されているデータ資産を検索します。 |
| `has:tableDataProfiles` |テーブル レベルのデータ プロファイル情報でデータ資産を検索します。 |
| `has:columnsDataProfiles` |列レベルのデータ プロファイル情報でデータ資産を検索します。 |


> [!NOTE]
> [Power BI Desktop で開く] を使用するには、Power BI Desktop アプリケーションの現在のバージョンをインストールしておく必要があります。 この機能を使用して問題やエラーが発生した場合は、[PowerBI.com](https://powerbi.com) から提供される Power BI Desktop の最新バージョンを使用していることを確認してください。


## <a name="whats-new-for-october-2015"></a>2015 年 10 月の新機能
2015 年 10 月の時点で、Azure Data Catalog には次の機能が追加されています。

* 登録済みのデータ ソースデータに関するデータ プレビューおよびデータ プロファイルの保存時に暗号化をサポートします。 Azure Data Catalog では、サービスに登録されたデータ ソースの任意のプレビュー レコードおよびデータ プロファイルを透過的に暗号化し、Catalog 管理者がキーを管理する必要はありません。
* Teradata データ ソースのサポート。 Teradata のテーブルとビューの登録と検出を実行できるようになりました。
* オンプレミス Hive データ ソースのサポート。 ユーザーは、Hadoop オンプレミス データ ソースで Apache Hive 用の Hive テーブルを登録し、検出できるようになりました。
* Azure Data Catalog ポータルにおける保存された検索のサポート。 ユーザーは検索語句とフィルター選択項目を保存することで、簡単に以前の検索を繰り返し、カタログのコンテンツの便利なビューを定義することができます。 また、ユーザーは既定の検索として保存した検索をマークすることもできます。 ユーザーが Azure Data Catalog ポータルのホーム ページまたは [概要] ページから "虫眼鏡" の検索アイコンをクリックすると、既定値としてフラグが付けられた保存した検索が直接示されます。
* Azure Data Catalog ポータルで、登録済みのデータ資産とコンテナーに関するリッチ テキスト ドキュメントがサポートされるようになりました。 ユーザーは、タグや説明が不十分なシナリオで、データ資産 (テーブル、ビュー、レポートなど) とコンテナー (データベース、モデルなど) に関するドキュメントを提供できます。
* 既知のデータ ソースの種類を手動で登録できるようになりました。 ユーザーは、Azure Data Catalog でサポートされるすべてのデータ ソースの種類について、Azure Data Catalog ポータルを使用してデータ ソース情報を手動で入力できます。
* Azure Active Directory セキュリティ グループを承認できるようになりました。 カタログ管理者は、ユーザー アカウントだけでなく、セキュリティ グループに対するカタログ アクセスを有効にできます。これにより、Azure Data Catalog へのアクセスの管理がより簡単になります。
* Azure Data Catalog ポータルから、Excel で Hive データ ソースを開けるようになりました。

> [!NOTE]
> 現在のリリースでは、Teradata TD2 認証のみがサポートされています。 今後、他の認証メカニズムもサポートされる予定です。

> [!NOTE]
> Hive データ ソースを "Excel で開く" 機能を使用するには、ユーザーが Hive 用の ODBC ドライバーをインストール済みである必要があります。

## <a name="whats-new-for-september-2015"></a>2015 年 9 月の新機能
2015 年 9 月の時点で、Azure Data Catalog には次の機能が追加されています。

* Hive データ ソースの登録時に、データ プロファイルを含めることができるようになりました。
* Catalog API のプログラムによる検出がサポートされるようになったため、Azure Data Catalog とアプリケーションの統合が容易になります。
* Azure Data Catalog ポータルでの新しい "概要" データ ソースの検出エクスペリエンス。 ユーザーが Azure Data Catalog ポータルの [検出] ページで検索語句を入力していない場合、最も頻繁に使用されるタグ、エキスパート、データ ソースの種類、およびオブジェクトの種類を含むカタログ コンテンツの概要が示されます。
* Azure SQL Data Warehouse のオブジェクトおよびデータベースを登録して検出するためのサポート。 Azure SQL Data Warehouse の詳細については、「 [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/)」を参照してください。
* SQL Server Analysis Services モデルおよび SQL Server Reporting Services サーバーをコンテナーとして登録して検出するためのサポート。 SSAS および SSRS のオブジェクトの登録時に、Azure Data Catalog は、レポートや他のオブジェクトだけでなく、SSAS モデルと SSRS サーバーのエントリも作成します。 コンテナーは、Azure Data Catalog ポータルを使用して検出し、注釈を付けることができます。 カタログのコンテンツを検索し、フィルター処理するだけでなく、モデルまたはサーバーのコンテンツを検索し、フィルター処理することもできます。
* HTTP/HTTPS を介した SQL Server Analysis Services オブジェクトの登録および検出をサポートします。 ユーザーは、サーバー名ではなく URL (https://servername/olap/msmdpump.dll) など) を使用して SSAS サーバーに接続できるようになりました。さらに、Windows 認証に加えて、基本認証と匿名接続も使用することができます。 SSAS への HTTP/HTTPS 接続の詳細については、「[インターネット インフォメーション サービス (IIS) 8.0 上の Analysis Services への HTTP アクセスの構成](https://msdn.microsoft.com/library/gg492140.aspx)」を参照してください。
* HDInsight で Hive データ ソースをサポートします。 ユーザーは、HDInsight データ ソースにおいて、Hadoop の Apache Hive に対する Hive テーブルを登録および検出できるようになりました。 HDInsight での Hive の詳細については、 [HDInsight ドキュメント センター](../hdinsight/hadoop/hdinsight-use-hive.md)をご覧ください。
* Oracle データベースと HDFS クラスターをコンテナーとして登録し、検出できるようになりました。 Oracle のテーブルとビューまたは HDFS の登録時に、Azure Data Catalog は、データベース、テーブル、およびビューのエントリを作成します。 データベースは、Azure Data Catalog ポータルを使用して検出し、注釈を付けることができます。 カタログのコンテンツを検索し、フィルター処理するだけでなく、データベースまたはクラスターのコンテンツを検索し、フィルター処理することもできます。
* 不明なデータ ソースを手動で登録できるようになりました。 データ ソース登録ツールで明示的にサポートされていないデータ ソースに注釈を付け、検出できるように、Azure Data Catalog ポータルを使用してデータ ソース情報を手動で入力できます。
* SQL Server データベースをコンテナーとして登録し、検出できるようになりました。 SQL Server のテーブルとビューの登録時に、Azure Data Catalog は、データベース、テーブル、およびビューのエントリを作成します。 データベースは、Azure Data Catalog ポータルを使用して検出し、注釈を付けることができます。 カタログのコンテンツを検索し、フィルター処理するだけでなく、データベースのコンテンツを検索し、フィルター処理することもできます。

> [!NOTE]
> 9 月 18 日のリリースより前に登録された SSAS および SSRS のオブジェクトについては、モデルまたはサーバーのエントリをカタログに追加する前に、データ ソース登録ツールを使用して再登録する必要があります。 データ ソースを再登録しても、Azure Data Catalog ポータルでユーザーによって追加された注釈に影響はありません。

> [!NOTE]
> 9 月 11 日以前に登録された Oracle のテーブルおよびビューと、HDFS のファイルおよびディレクトリについては、データベースまたはクラスターのエントリをカタログに追加する前に、データ ソース登録ツールで再登録する必要があります。 データ ソースを再登録しても、Azure Data Catalog ポータルでユーザーによって追加された注釈に影響はありません。

> [!NOTE]
> 9 月 4 日以前に登録された SQL Server のテーブルとビューについては、データベース エントリをカタログに追加する前に、データ ソース登録ツールで再登録する必要があります。 データ ソースを再登録しても、Azure Data Catalog ポータルでユーザーによって追加された注釈に影響はありません。

## <a name="whats-new-for-august-2015"></a>2015 年 8 月の新機能
2015 年 8 月の時点で、Azure Data Catalog には次の機能が追加されています。

* SQL Server と Oracle のデータ ソースのデータプロファイルのサポート。 SQL Server と Oracle のテーブルとビューを登録するときに、登録するオブジェクトのデータ プロファイル情報を含めることを選択できます。 データ プロファイルには、オブジェクト レベルと列レベルの統計情報が含まれます。
* Hadoop HDFS データ ソースのサポート。 HDFS のファイルとディレクトリを登録および検出できるようになりました。
* 登録されているデータ ソースに対するアクセス要求情報の提供のサポート。 任意の登録済みデータ資産について、ユーザーは電子メール リンクや URL などのアクセス要求方法を提供し、既存のツールおよびプロセスと簡単に統合できます。
* 登録されているデータ資産に対してどのようなユーザーがどのようなメタデータを提供したのかを簡単に検出できるようにするための、タグとエキスパートに関するヒント。
* 上部のナビゲーション バーに新しい [ユーザー] ボタンとメニューを追加しました。 このメニューを使用すると、ユーザーは Azure Data Catalog へのログオンに使用したアカウントを確認でき、必要な場合はサインアウトできます。 このメニューでは、Azure Data Catalog REST API を使用する開発者に役に立つカタログ名も表示されます。
* Standard Edition のみ: データ資産に所有者を追加するとき、Azure Data Catalog は所有者としてユーザー アカウントとセキュリティ グループの両方をサポートするようになりました。 選択したデータ資産の所有者としてセキュリティ グループを追加する場合、グループの表示名またはグループの UPN 電子メール アドレス (ある場合) のいずれかを入力できます。
* Azure BLOB Storage データ ソースのサポート。 ユーザーは、Azure Storage の BLOB およびディレクトリを登録および検出できるようになりました。

