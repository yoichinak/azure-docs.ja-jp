---
title: Azure Cosmos DB のデータベース移行ツール | Microsoft Docs
description: オープン ソースの Azure Cosmos DB データ移行ツールを使用して、MongoDB、SQL Server、Table Storage、Amazon DynamoDB、CSV、JSON ファイルなどのさまざまなソースからデータを Azure Cosmos DB にインポートする方法について説明します。 CSV から JSON への変換についても説明します。
keywords: csv を json へ、データベース移行ツール、csv を json へ
services: cosmos-db
author: deborahc
manager: kfile
editor: monicar
ms.service: cosmos-db
ms.devlang: na
ms.topic: tutorial
ms.date: 03/30/2018
ms.author: dech
ms.custom: mvc
ms.openlocfilehash: 43092a12535ed18a5c91f924e3fdf72ad6d47d7b
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2018
ms.locfileid: "41919579"
---
# <a name="azure-cosmos-db-data-migration-tool"></a>Azure Cosmos DB: データ移行ツール

このチュートリアルでは、Azure Cosmos DB データ移行ツールを使用して、さまざまなソースからデータを Azure Cosmos DB コレクションおよびテーブルにインポートする方法について説明します。 JSON ファイル、CSV ファイル、SQL、MongoDB、Azure Table Storage、Amazon DynamoDB、さらには Azure Cosmos DB SQL API コレクションからもインポートできます。Azure Cosmos DB で使用するためにそのデータをコレクションやテーブルに移行します。 データ移行ツールは、SQL API の 1 つの単一パーティション コレクションから複数パーティション コレクションに移行する場合にも使用できます。

Azure Cosmos DB で使用する API を教えてください。 
* **[SQL API](documentdb-introduction.md)** - データ移行ツールで提供される任意のソース オプションを使用して、データをインポートできます。
* **[Table API](table-introduction.md)** - データ移行ツールまたは AzCopy を使用してデータをインポートできます。 詳細については「[Import data for use with the Azure Cosmos DB Table API](table-import.md)」(Azure Cosmos DB Table API で使用するデータのインポート) を参照してください。
* **[MongoDB API](mongodb-introduction.md)**  - データ移行ツールでは現在、Azure Cosmos DB MongoDB API はソースやターゲットとしてサポートされていません。 Azure Cosmos DB 内の MongoDB API コレクションとの間でデータを移行する必要がある場合は、「[Azure Cosmos DB: How to migrate data for the MongoDB API (Azure Cosmos DB: MongoDB API のデータを移行する方法)](mongodb-migrate.md)」の手順をご覧ください。 なお、データ移行ツールを使用して MongoDB から Azure Cosmos DB SQL API コレクションにデータをエクスポートし、SQL API で使用することは可能です。 
* **[Graph API](graph-introduction.md)** - 現時点では、Graph API アカウントのインポート ツールとしてデータ移行ツールはサポートされていません。 

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * データ移行ツールのインストール
> * さまざまなデータ ソースからのデータのインポート
> * Azure Cosmos DB から JSON へのエクスポート

## <a id="Prerequisites"></a>前提条件
この記事の手順を実行する前に、次のソフトウェアがインストールされていることを確認してください。

* [Microsoft .NET Framework 4.51](https://www.microsoft.com/download/developer-tools.aspx) 以降

* スループットを上げる: データの移行にかかる時間は、個別のコレクションまたは一連のコレクションに対して設定したスループットの量に依存します。 大規模なデータ移行では、スループットが上がっていることを確認します。 移行が完了したら、コストを節約するためにスループットを下げます。 Azure Portal でスループットを上げることの詳細については、Azure Cosmos DB のパフォーマンス レベルと価格レベルに関するページを参照してください。

## <a id="Overviewl"></a>概要
データ移行ツールはオープン ソース ソリューションで、次のような各種ソースからデータを Azure Cosmos DB にインポートします。

* JSON ファイル
* MongoDB
* SQL Server
* CSV ファイル
* Azure Table Storage
* Amazon DynamoDB
* hbase
* Azure Cosmos DB コレクション

このインポート ツールにはグラフィカル ユーザー インターフェイス (dtui.exe) が搭載されていますが、コマンド ライン (dt.exe) から実行することもできます。 さらに、UI でインポートを設定した後で関連コマンドを出力するオプションもあります。 インポート時には、表形式ソース データ (SQL Server や CSV ファイルなど) を変換して、階層関係 (サブドキュメント) を作成できます。 最後まで目を通し、ソース オプション、各ソースからインポートするためのサンプル コマンド、ターゲット オプション、およびインポート結果の表示に関する詳細を確認してください。

## <a id="Install"></a>インストール
移行ツールのソース コードは、GitHub の[このリポジトリ](https://github.com/azure/azure-documentdb-datamigrationtool)で入手できます。 ソリューションをローカルにダウンロードしてコンパイルするか、[プリコンパイル済みのバイナリをダウンロード](https://cosmosdbportalstorage.blob.core.windows.net/datamigrationtool/2018.02.28-1.8.1/dt-1.8.1.zip)してから、次のいずれかを実行できます。

* **Dtui.exe**: グラフィカル インターフェイス バージョンのツール
* **Dt.exe**: コマンド ライン バージョンのツール

## <a name="select-data-source"></a>データ ソースの選択

ツールのインストールが完了したら、次にデータをインポートします。 インポートするデータの種類を確認してください。

* [JSON ファイル](#JSON)
* [MongoDB](#MongoDB)
* [MongoDB エクスポート ファイル](#MongoDBExport)
* [SQL Server](#SQL)
* [CSV ファイル](#CSV)
* [Azure Table Storage](#AzureTableSource)
* [Amazon DynamoDB](#DynamoDBSource)
* [BLOB](#BlobImport)
* [Azure Cosmos DB コレクション](#SQLSource)
* [HBase](#HBaseSource)
* [Azure Cosmos DB の一括インポート](#SQLBulkImport)
* [Azure Cosmos DB シーケンシャル レコード インポート](#DocumentDSeqTarget)


## <a id="JSON"></a>JSON ファイルのインポート
JSON ファイル ソース インポーター オプションを使用して、1 つ以上の単一ドキュメント JSON ファイル、またはそれぞれに JSON ドキュメントの配列が含まれた JSON ファイルをインポートできます。 インポートする JSON ファイルが含まれたフォルダーを追加する際は、サブフォルダー内のファイルを再帰的に検索できます。

![JSON ファイル ソース オプションのスクリーンショット - データベース移行ツール](./media/import-data/jsonsource.png)

JSON ファイルをインポートするためのコマンド ライン サンプルを以下にいくつか示します。

    #Import a single JSON file
    dt.exe /s:JsonFile /s.Files:.\Sessions.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

    #Import a directory of JSON files
    dt.exe /s:JsonFile /s.Files:C:\TESessions\*.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

    #Import a directory (including sub-directories) of JSON files
    dt.exe /s:JsonFile /s.Files:C:\LastFMMusic\**\*.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Music /t.CollectionThroughput:2500

    #Import a directory (single), directory (recursive), and individual JSON files
    dt.exe /s:JsonFile /s.Files:C:\Tweets\*.*;C:\LargeDocs\**\*.*;C:\TESessions\Session48172.json;C:\TESessions\Session48173.json;C:\TESessions\Session48174.json;C:\TESessions\Session48175.json;C:\TESessions\Session48177.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:subs /t.CollectionThroughput:2500

    #Import a single JSON file and partition the data across 4 collections
    dt.exe /s:JsonFile /s.Files:D:\\CompanyData\\Companies.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:comp[1-4] /t.PartitionKey:name /t.CollectionThroughput:2500

## <a id="MongoDB"></a>MongoDB からのインポート

> [!IMPORTANT]
> MongoDB 対応の Azure Cosmos DB アカウントにインポートする場合は、こちらの[指示](mongodb-migrate.md)に従ってください。
> 
> 

MongoDB ソース インポーター オプションを使用して、個々の MongoDB コレクションからインポートできます。必要に応じて、クエリを使用してドキュメントをフィルター処理したり、プロジェクションを使用してドキュメント構造を変更したりすることもできます。  

![MongoDB ソース オプションのスクリーンショット](./media/import-data/mongodbsource.png)

接続文字列は、標準的な MongoDB 形式です。

    mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database>

> [!NOTE]
> [確認] を使用して、[接続文字列] フィールドで指定した MongoDB インスタンスにアクセスできることを確認してください。
> 
> 

データのインポート元となるコレクションの名前を入力します。 必要に応じて、クエリ (例: {pop: {$gt: 5000}}) やプロジェクション (例: {loc:0}) を直接入力するか、そのためのファイルを指定して、インポートするデータのフィルター処理と整形を実行できます。

MongoDB からインポートするためのコマンド ライン サンプルを以下にいくつか示します。

    #Import all documents from a MongoDB collection
    dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZips /t.IdField:_id /t.CollectionThroughput:2500

    #Import documents from a MongoDB collection which match the query and exclude the loc field
    dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /s.Query:{pop:{$gt:50000}} /s.Projection:{loc:0} /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZipsTransform /t.IdField:_id/t.CollectionThroughput:2500

## <a id="MongoDBExport"></a>MongoDB エクスポート ファイルのインポート

> [!IMPORTANT]
> MongoDB 対応の Azure Cosmos DB アカウントにインポートする場合は、こちらの[指示](mongodb-migrate.md)に従ってください。
> 
> 

MongoDB エクスポート JSON ファイル ソース インポーター オプションを使用して、mongoexport ユーティリティによって生成された 1 つ以上の JSON ファイルをインポートできます。  

![MongoDB エクスポート ソース オプションのスクリーンショット](./media/import-data/mongodbexportsource.png)

インポートする MongoDB エクスポート JSON ファイルが含まれたフォルダーを追加する際は、サブフォルダー内のファイルを再帰的に検索できます。

MongoDB エクスポート JSON ファイルからインポートするためのコマンド ライン サンプルを以下に示します。

    dt.exe /s:MongoDBExport /s.Files:D:\mongoemployees.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:employees /t.IdField:_id /t.Dates:Epoch /t.CollectionThroughput:2500

## <a id="SQL"></a>SQL Server からのインポート
SQL ソース インポーター オプションを使用して、個々の SQL Server データベースからインポートできます。また、必要に応じて、クエリを使用してインポートするレコードをフィルター処理できます。 さらに、入れ子の区切り記号を指定して、ドキュメント構造を変更することもできます (詳細は後ほど説明します)。  

![SQL ソース オプションのスクリーンショット - データベース移行ツール](./media/import-data/sqlexportsource.png)

接続文字列の形式は、標準的な SQL 接続文字列の形式です。

> [!NOTE]
> [確認] を使用して、[接続文字列] フィールドで指定した SQL Server インスタンスにアクセスできることを確認してください。
> 
> 

[入れ子の区切り記号] プロパティは、インポート時に階層関係 (サブドキュメント) を作成する場合に使用します。 次の SQL クエリについて考えてみましょう。

*select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'*

返される結果 (一部) は次のとおりです。

![SQL クエリ結果のスクリーンショット](./media/import-data/sqlqueryresults.png)

Address.AddressType や Address.Location.StateProvinceName などのエイリアスに注目してください。 入れ子の区切り記号 "." を指定することで、インポート時に Address や Address.Location のサブドキュメントが作成されています。 Azure Cosmos DB で生成されるドキュメントの例を以下に示します。

*{ "id": "956", "Name": "Finer Sales and Service", "Address": { "AddressType": "Main Office", "AddressLine1": "#500-75 O'Connor Street", "Location": { "City": "Ottawa", "StateProvinceName": "Ontario" }, "PostalCode": "K4B 1S2", "CountryRegionName": "Canada" } }*

SQL Server からインポートするためのコマンド ライン サンプルを以下にいくつか示します。

    #Import records from SQL which match a query
    dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, * from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Stores /t.IdField:Id /t.CollectionThroughput:2500

    #Import records from sql which match a query and create hierarchical relationships
    dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /s.NestingSeparator:. /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:StoresSub /t.IdField:Id /t.CollectionThroughput:2500

## <a id="CSV"></a>CSV ファイルをインポートして CSV を JSON に変換する
CSV ファイル ソース インポーター オプションを使用して、1 つ以上の CSV ファイルをインポートできます。 インポートする CSV ファイルが含まれたフォルダーを追加する際は、サブフォルダー内のファイルを再帰的に検索できます。

![CSV ソース オプションのスクリーンショット - CSV から JSON へ](media/import-data/csvsource.png)

SQL ソースの場合と同様、[入れ子の区切り記号] プロパティは、インポート時に階層関係 (サブドキュメント) を使用するために使用されます。 次のような CSV のヘッダー行とデータ行について考えてみましょう。

![CSV サンプル レコードのスクリーンショット - CSV から JSON へ](./media/import-data/csvsample.png)

DomainInfo.Domain_Name や RedirectInfo.Redirecting などのエイリアスに注目してください。 入れ子の区切り記号 "." を指定することで、インポート時に DomainInfo や RedirectInfo のサブドキュメントが作成されます。 Azure Cosmos DB で生成されるドキュメントの例を以下に示します。

*{ "DomainInfo": { "Domain_Name": "ACUS.GOV", "Domain_Name_Address": "http://www.ACUS.GOV" }, "Federal Agency": "Administrative Conference of the United States", "RedirectInfo": { "Redirecting": "0", "Redirect_Destination": "" }, "id": "9cc565c5-ebcd-1c03-ebd3-cc3e2ecd814d" }*

インポート ツールでは、CSV ファイル内の引用符なしの値の型情報が推測されます (引用符で囲まれた値は、常に文字列として扱われます)。  型は、数値型、DateTime 型、ブール型の順に識別されます。  

CSV のインポートに関して注意することが他に 2 つあります。

1. 既定では、引用符なしの値は常にタブやスペースで切られますが、引用符で囲まれた値はそのまま保持されます。 この動作は、[引用符付きの値をトリミングする] チェックボックスまたはコマンド ライン オプション /s.TrimQuoted を使用してオーバーライドすることができます。
2. 既定では、引用符なしの null は、null 値として扱われます。 この動作は、[引用符なしの NULL を文字列として扱う] チェックボックスまたはコマンド ライン オプション /s.NoUnquotedNulls を使用してオーバーライドすることができます (つまり、引用符なしの null を "null" という文字列として扱います)。

CSV インポート用のコマンド ライン サンプルを以下に示します。

    dt.exe /s:CsvFile /s.Files:.\Employees.csv /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Employees /t.IdField:EntityID /t.CollectionThroughput:2500

## <a id="AzureTableSource"></a>Azure テーブル ストレージからのインポート
Azure Table Storage ソース インポーター オプションを使用して、個々の Azure Table Storage テーブルからインポートできます。 必要に応じて、インポートするテーブル エンティティをフィルター処理できます。 

Azure Table Storage からインポートしたデータは、Table API で使用するために Azure Cosmos DB テーブルとエンティティに出力するか、または SQL API で使用するためにコレクションとドキュメントに出力できます。 ただし、コマンドライン ユーティリティでは、Table API をターゲットとしてのみ指定できるので、データ移行ツールのユーザー インターフェイスを使用して Table API にエクスポートすることはできません。 詳細については、「[Azure Cosmos DB Table API で使用するデータのインポート](table-import.md)」を参照してください。 

![Azure テーブル ストレージ ソース オプションのスクリーンショット](./media/import-data/azuretablesource.png)

Azure テーブル ストレージの接続文字列の形式は次のとおりです。

    DefaultEndpointsProtocol=<protocol>;AccountName=<Account Name>;AccountKey=<Account Key>;

> [!NOTE]
> [確認] を使用して、[接続文字列] フィールドで指定した Azure テーブル ストレージ インスタンスにアクセスできることを確認してください。
> 
> 

データのインポート元となる Azure テーブルの名前を入力します。 必要に応じて、 [フィルター](../vs-azure-tools-table-designer-construct-filter-strings.md)を指定することもできます。

Azure テーブル ストレージ ソース インポーター オプションには、次の追加オプションがあります。

1. 内部フィールドを含める
   1. すべて - 内部フィールド (PartitionKey、RowKey、Timestamp) をすべて含めます。
   2. なし - すべての内部フィールドを除外します。
   3. RowKey - RowKey フィールドのみを含めます。
2. 列の選択
   1. Azure テーブル ストレージのフィルターでは、プロジェクションはサポートされません。 特定の Azure テーブル エンティティ プロパティのみをインポートする場合は、[列の選択] リストに追加します。 これにより、他のエンティティ プロパティはすべて無視されます。

Azure Table Storage からインポートするためのコマンド ライン サンプルを以下に示します。

    dt.exe /s:AzureTable /s.ConnectionString:"DefaultEndpointsProtocol=https;AccountName=<Account Name>;AccountKey=<Account Key>" /s.Table:metrics /s.InternalFields:All /s.Filter:"PartitionKey eq 'Partition1' and RowKey gt '00001'" /s.Projection:ObjectCount;ObjectSize  /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:metrics /t.CollectionThroughput:2500

## <a id="DynamoDBSource"></a>Amazon DynamoDB からのインポート
Amazon DynamoDB のソース インポーター オプションを使用すると、個々の Amazon DynamoDB テーブルからインポートし、必要に応じてインポートするエンティティをフィルターすることができます。 インポートの設定が可能な限り簡単になるように、いくつかのテンプレートが用意されています。

![Amazon DynamoDB ソース オプションのスクリーンショット - データベース移行ツール](./media/import-data/dynamodbsource1.png)

![Amazon DynamoDB ソース オプションのスクリーンショット - データベース移行ツール](./media/import-data/dynamodbsource2.png)

Amazon DynamoDB の接続文字列の形式は次のとおりです。

    ServiceURL=<Service Address>;AccessKey=<Access Key>;SecretKey=<Secret Key>;

> [!NOTE]
> [確認] を使用して、[接続文字列] フィールドで指定した Amazon DynamoDB インスタンスにアクセスできることを確認してください。
> 
> 

Amazon DynamoDB からインポートするためのコマンド ライン サンプルを以下に示します。

    dt.exe /s:DynamoDB /s.ConnectionString:ServiceURL=https://dynamodb.us-east-1.amazonaws.com;AccessKey=<accessKey>;SecretKey=<secretKey> /s.Request:"{   """TableName""": """ProductCatalog""" }" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<Azure Cosmos DB Endpoint>;AccountKey=<Azure Cosmos DB Key>;Database=<Azure Cosmos DB Database>;" /t.Collection:catalogCollection /t.CollectionThroughput:2500

## <a id="BlobImport"></a>Azure Blob Storage からのインポート
JSON ファイル、MongoDB のエクスポート ファイル、および CSV ファイル ソースのインポーター オプションを使用すると、Azure BLOB ストレージから 1 つ以上のファイルをインポートできます。 BLOB コンテナーの URL とアカウント キーを指定した後に、正規表現を使用してインポートするファイルを選択してください。

![BLOB ファイル ソース オプションのスクリーンショット](./media/import-data/blobsource.png)

Azure Blob Storage から JSON ファイルをインポートするためのコマンド ライン サンプルを以下に示します。

    dt.exe /s:JsonFile /s.Files:"blobs://<account key>@account.blob.core.windows.net:443/importcontainer/.*" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:doctest

## <a id="SQLSource"></a>SQL API コレクションからのインポート
Azure Cosmos DB ソース インポーターのオプションを使用して、1 つ以上の Azure Cosmos DB コレクションからデータをインポートできます。また、必要に応じて、クエリを使用してドキュメントをフィルター処理できます。  

![Azure Cosmos DB ソース オプションのスクリーンショット](./media/import-data/documentdbsource.png)

Azure Cosmos DB の接続文字列の形式は次のとおりです。

    AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;

Azure Cosmos DB アカウントの接続文字列は、「[Azure Cosmos DB アカウントの管理方法](manage-account.md)」の説明に従って、Azure Portal の [キー] ページから取得できます。ただし、データベースの名前は、次の形式で接続文字列に追加する必要があります。

    Database=<CosmosDB Database>;

> [!NOTE]
> [確認] を使用して、[接続文字列] フィールドで指定した Azure Cosmos DB インスタンスにアクセスできることを確認してください。
> 
> 

1 つの Azure Cosmos DB コレクションからインポートするには、データのインポート元のコレクション名を入力します。 複数の Azure Cosmos DB コレクションからインポートするには、1 つ以上のコレクション名に一致する正規表現を指定します (例: collection01 | collection02 | collection03)。 必要に応じて、クエリを直接入力するか、そのためのファイルを指定して、インポートするデータのフィルター処理と整形を実行できます。

> [!NOTE]
> コレクション フィールドは正規表現に対応しているため、コレクション名に正規表現文字を含む 1 つのコレクションからインポートする場合は、これらの文字を適宜エスケープする必要があります。
> 
> 

Azure Cosmos DB ソース インポーター オプションには、次の詳細オプションがあります。

1. 内部フィールドを含める: エクスポート内の Azure Cosmos DB ドキュメント システム プロパティ (_rid、_ts など) を含めるかどうかを指定します。
2. エラー発生時の再試行回数: 一時的なエラー (ネットワーク接続の中断など) が発生した場合に Azure Cosmos DB への接続を再試行する回数を指定します。
3. 再試行の間隔: 一時的なエラー (ネットワーク接続の中断など) が発生した場合に Azure Cosmos DB への接続を次に再試行するまでの待機時間を指定します。
4. 接続モード: Azure Cosmos DB で使用する接続モードを指定します。 使用できる選択肢は、DirectTcp、DirectHttps、およびゲートウェイです。 Direct という語が付いている接続モードの方が高速です。これに対して、ゲートウェイ モードはポート 443 のみを使用するため、ファイアウォールとの適合性が高いという特徴があります。

![Azure Cosmos DB ソース詳細オプションのスクリーンショット](./media/import-data/documentdbsourceoptions.png)

> [!TIP]
> インポート ツールの接続モードは既定で [DirectTcp] に設定されています。 ファイアウォールの問題が発生した場合は、ポート 443 のみを使用する必要があるため、接続モードを [ゲートウェイ] に切り替えてください。
> 
> 

Azure Cosmos DB からインポートするためのコマンド ライン サンプルを以下にいくつか示します。

    #Migrate data from one Azure Cosmos DB collection to another Azure Cosmos DB collections
    dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:TEColl /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:TESessions /t.CollectionThroughput:2500

    #Migrate data from multiple Azure Cosmos DB collections to a single Azure Cosmos DB collection
    dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:comp1|comp2|comp3|comp4 /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:singleCollection /t.CollectionThroughput:2500

    #Export an Azure Cosmos DB collection to a JSON file
    dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:StoresSub /t:JsonFile /t.File:StoresExport.json /t.Overwrite /t.CollectionThroughput:2500

> [!TIP]
> Azure Cosmos DB データ インポート ツールは、[Azure Cosmos DB Emulator](local-emulator.md) のデータのインポートもサポートしています。 ローカル エミュレーターからデータをインポートする場合は、エンドポイントを `https://localhost:<port>` に設定します。 
> 
> 

## <a id="HBaseSource"></a>HBase からのインポート
HBase のソース インポーター オプションを使用すると、HBase テーブルのデータをインポートし、必要に応じてデータをフィルターすることができます。 インポートの設定が可能な限り簡単になるように、いくつかのテンプレートが用意されています。

![HBase のソース オプションのスクリーンショット](./media/import-data/hbasesource1.png)

![HBase のソース オプションのスクリーンショット](./media/import-data/hbasesource2.png)

HBase Stargate の接続文字列の形式は次のとおりです。

    ServiceURL=<server-address>;Username=<username>;Password=<password>

> [!NOTE]
> [確認] を使用して、[接続文字列] フィールドで指定した HBase インスタンスにアクセスできることを確認してください。
> 
> 

HBase からインポートするためのコマンド ライン サンプルを以下に示します。

    dt.exe /s:HBase /s.ConnectionString:ServiceURL=<server-address>;Username=<username>;Password=<password> /s.Table:Contacts /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:hbaseimport

## <a id="SQLBulkTarget"></a>SQL API へのインポート (一括インポート)
Azure Cosmos DB 一括インポーターを使用すると、Azure Cosmos DB ストアド プロシージャによって使用可能な任意のソース オプションからインポートできるようになり、効率が向上します。 このツールは、1 つの Azure Cosmos DB コレクション (単一パーティション) へのインポートと、シャード化されたインポート (データが複数の単一パーティションの Azure Cosmos DB コレクションにパーティション分割される) のどちらもサポートします。 データのパーティション分割の詳細については、[Azure Cosmos DB でのパーティション分割とスケーリング](partition-data.md)に関する記事をご覧ください。 このツールでは、ストアド プロシージャの作成と実行、およびターゲット コレクションからの削除が実行されます。  

![Azure Cosmos DB 一括オプションのスクリーンショット](./media/import-data/documentdbbulk.png)

Azure Cosmos DB の接続文字列の形式は次のとおりです。

    AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;

Azure Cosmos DB アカウントの接続文字列は、「[Azure Cosmos DB アカウントの管理方法](manage-account.md)」の説明に従って、Azure Portal の [キー] ページから取得できます。ただし、データベースの名前は、次の形式で接続文字列に追加する必要があります。

    Database=<CosmosDB Database>;

> [!NOTE]
> [確認] を使用して、[接続文字列] フィールドで指定した Azure Cosmos DB インスタンスにアクセスできることを確認してください。
> 
> 

1 つのコレクションにインポートするには、データのインポート元のコレクション名を入力し、[追加] ボタンをクリックします。 複数のコレクションにインポートするには、各コレクション名を個別に入力するか、次の構文を使用して複数のコレクションを指定します。*collection_prefix*[開始インデックス - 終了インデックス]。 この構文を使用して複数のコレクションを指定する場合は、次の点に注意してください。

1. 範囲名のパターンでサポートされるのは整数のみです。 たとえば、collection[0-3] と指定すると、collection0、collection1、collection2、collection3 が作成されます。
2. 省略構文を使用することができます。collection[3] は、手順 1. で説明したのと同じコレクションのセットを生成します。
3. 複数の代入値を指定することができます。 たとえば、collection[0-1] [0-9] と指定すると、0 か 1 で始まる 20 のコレクション名を生成します (collection01、..02、..03)。

コレクション名を指定したら、コレクションのひつよなスループット (400 RU ～ 10,000 RU) を選択します。 インポートのパフォーマンスを最大限に高めるには、高いスループットを選択してください。 パフォーマンス レベルの詳細については、[Azure Cosmos DB のパフォーマンス レベル](performance-levels.md)に関する記事をご覧ください。

> [!NOTE]
> パフォーマンス スループットの設定は、コレクションの作成にのみ適用されます。 指定されたコレクションが既に存在する場合、そのスループットは変更されません。
> 
> 

複数のコレクションにインポートする場合、インポート ツールはハッシュ ベースのシャーディングをサポートします。 このシナリオでは、パーティション キーとして使用するドキュメント プロパティを指定します (パーティション キーが空白のままだと、ドキュメントはターゲット コレクション全体にランダムにシャーディングされます)。

必要に応じて、インポートする際に Azure Cosmos DB ドキュメントの ID プロパティとして使用するインポート ソースのフィールドを指定できます (ドキュメントにこのプロパティが含まれていない場合は、インポート ツールでは ID プロパティの値として GUID が生成されます)。

インポート時に利用できる詳細オプションは多数あります。 まず、ツールには既定の一括インポート用ストアド プロシージャ (BulkInsert.js) が用意されていますが、独自のインポート用ストアド プロシージャを指定することもできます。

 ![Azure Cosmos DB 一括挿入ストアド プロシージャ オプションのスクリーンショット](./media/import-data/bulkinsertsp.png)

また、日付型をインポートする際 (たとえば SQL Server や MongoDB から) に、次の 3 つのインポート オプションから選択できます。

 ![Azure Cosmos DB 日付時刻インポート オプションのスクリーンショット](./media/import-data/datetimeoptions.png)

* 文字列: 文字列値として保持します。
* エポック: エポック番号値として保持します。
* 両方: 文字列値およびエポック番号値の両方を保持します。 このオプションにより、サブドキュメントが作成されます。例: "date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

Azure Cosmos DB 一括インポーターには、次の詳細オプションがあります。

1. バッチ サイズ: ツールのバッチ サイズは既定で 50 に設定されています。  インポートするドキュメントが大きい場合は、バッチ サイズを減らすことを検討してみてください。 反対に、インポートするドキュメントが小さい場合は、バッチ サイズを増やすことを検討してみてください。
2. スクリプトの最大サイズ (バイト単位): スクリプトの最大サイズは既定で 512 KB に設定されています。
3. 自動 ID 生成を無効にする: インポートするすべてのドキュメントに ID フィールドが含まれている場合は、このオプションを選択してパフォーマンスを向上できます。 一意の ID フィールドがないドキュメントはインポートされません。
4. 既存のドキュメントの更新: 既定では、ID が競合する既存のドキュメントは置き換えられません。 このオプションを選択すると、ID が一致する既存のドキュメントを上書きできます。 この機能は、既存のドキュメントを更新するスケジュールされたデータ移行に役立ちます。
5. エラー発生時の再試行回数: 一時的なエラー (ネットワーク接続の中断など) が発生した場合に Azure Cosmos DB への接続を再試行する回数を指定します。
6. 再試行の間隔: 一時的なエラー (ネットワーク接続の中断など) が発生した場合に Azure Cosmos DB への接続を次に再試行するまでの待機時間を指定します。
7. 接続モード: Azure Cosmos DB で使用する接続モードを指定します。 使用できる選択肢は、DirectTcp、DirectHttps、およびゲートウェイです。 Direct という語が付いている接続モードの方が高速です。これに対して、ゲートウェイ モードはポート 443 のみを使用するため、ファイアウォールとの適合性が高いという特徴があります。

![Azure Cosmos DB 一括インポート詳細オプションのスクリーンショット](./media/import-data/docdbbulkoptions.png)

> [!TIP]
> インポート ツールの接続モードは既定で [DirectTcp] に設定されています。 ファイアウォールの問題が発生した場合は、ポート 443 のみを使用する必要があるため、接続モードを [ゲートウェイ] に切り替えてください。
> 
> 

## <a id="SQLSeqTarget"></a>SQL API へのインポート (シーケンシャル レコードのインポート)
Azure Cosmos DB シーケンシャル レコード インポーターを使用して、レコードで使用可能な任意のソース オプションからレコード単位でインポートできます。 ストアド プロシージャのクォータに達した既存のコレクションにインポートしている場合に、このオプションを選択できます。 ツールは、1 つの Azure Cosmos DB コレクション (単一パーティションと複数パーティションの両方) へのインポートと、シャード化されたインポート (データが複数の単一パーティションや複数パーティションの Azure Cosmos DB コレクションにパーティション分割される) のどちらもサポートします。 データのパーティション分割の詳細については、[Azure Cosmos DB でのパーティション分割とスケーリング](partition-data.md)に関する記事をご覧ください。

![Azure Cosmos DB シーケンシャル レコード インポート オプションのスクリーンショット](./media/import-data/documentdbsequential.png)

Azure Cosmos DB の接続文字列の形式は次のとおりです。

    AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;

Azure Cosmos DB アカウントの接続文字列は、「[Azure Cosmos DB アカウントの管理方法](manage-account.md)」の説明に従って、Azure Portal の [キー] ページから取得できます。ただし、データベースの名前は、次の形式で接続文字列に追加する必要があります。

    Database=<Azure Cosmos DB Database>;

> [!NOTE]
> [確認] を使用して、[接続文字列] フィールドで指定した Azure Cosmos DB インスタンスにアクセスできることを確認してください。
> 
> 

1 つのコレクションにインポートするには、データのインポート先のコレクション名を入力し、[追加] をクリックします。 複数のコレクションにインポートするには、各コレクション名を個別に入力するか、次の構文を使用して複数のコレクションを指定します。*collection_prefix*[開始インデックス - 終了インデックス]。 この構文を使用して複数のコレクションを指定する場合は、次の点に注意してください。

1. 範囲名のパターンでサポートされるのは整数のみです。 たとえば、collection[0-3] と指定すると、collection0、collection1、collection2、collection3 が作成されます。
2. 省略構文を使用することができます。collection[3] は、手順 1. で説明したのと同じコレクションのセットを生成します。
3. 複数の代入値を指定することができます。 たとえば、collection[0-1] [0-9] と指定すると、0 か 1 で始まる 20 のコレクション名を作成します (collection01、..02、..03)。

コレクション名を指定したら、コレクションのひつよなスループット (400 RU ～ 250,000 RU) を選択します。 インポートのパフォーマンスを最大限に高めるには、高いスループットを選択してください。 パフォーマンス レベルの詳細については、[Azure Cosmos DB のパフォーマンス レベル](performance-levels.md)に関する記事をご覧ください。 スループットが 10,000 RU を超えるコレクションへのインポートには、パーティション キーが必要になります。 250,000 を上回る RU を選択する場合は、アカウントの制限を引き上げるように、ポータルで要求を送信する必要があります。

> [!NOTE]
> スループットの設定は、コレクションまたはデータベースの作成にのみ適用されます。 指定されたコレクションが既に存在する場合、そのスループットは変更されません。
> 
> 

複数のコレクションにインポートする場合、インポート ツールはハッシュ ベースのシャーディングをサポートします。 このシナリオでは、パーティション キーとして使用するドキュメント プロパティを指定します (パーティション キーが空白のままだと、ドキュメントはターゲット コレクション全体にランダムにシャーディングされます)。

必要に応じて、インポートする際に Azure Cosmos DB ドキュメントの ID プロパティとして使用するインポート ソースのフィールドを指定できます (ドキュメントにこのプロパティが含まれていない場合は、インポート ツールでは ID プロパティの値として GUID が生成されます)。

インポート時に利用できる詳細オプションは多数あります。 まず、日付型をインポートする際 (たとえば SQL Server や MongoDB から) に、次の 3 つのインポート オプションから選択できます。

 ![Azure Cosmos DB 日付時刻インポート オプションのスクリーンショット](./media/import-data/datetimeoptions.png)

* 文字列: 文字列値として保持します。
* エポック: エポック番号値として保持します。
* 両方: 文字列値およびエポック番号値の両方を保持します。 このオプションにより、サブドキュメントが作成されます。例: "date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

Azure Cosmos DB シーケンシャル レコード インポーターには、次の詳細オプションがあります。

1. 並列要求の数: 並列要求の数は既定で 2 に設定されています。 インポートするドキュメントが小さい場合は、並列要求の数を増やすことを検討してみてください。 この数が大きすぎると、インポート時にレートが制限される場合があります。
2. 自動 ID 生成を無効にする: インポートするすべてのドキュメントに ID フィールドが含まれている場合は、このオプションを選択してパフォーマンスを向上できます。 一意の ID フィールドがないドキュメントはインポートされません。
3. 既存のドキュメントの更新: 既定では、ID が競合する既存のドキュメントは置き換えられません。 このオプションを選択すると、ID が一致する既存のドキュメントを上書きできます。 この機能は、既存のドキュメントを更新するスケジュールされたデータ移行に役立ちます。
4. エラー発生時の再試行回数: 一時的なエラー (ネットワーク接続の中断など) が発生した場合に Azure Cosmos DB への接続を再試行する回数を指定します。
5. 再試行の間隔: 一時的なエラー (ネットワーク接続の中断など) が発生した場合に Azure Cosmos DB への接続を次に再試行するまでの待機時間を指定します。
6. 接続モード: Azure Cosmos DB で使用する接続モードを指定します。 使用できる選択肢は、DirectTcp、DirectHttps、およびゲートウェイです。 Direct という語が付いている接続モードの方が高速です。これに対して、ゲートウェイ モードはポート 443 のみを使用するため、ファイアウォールとの適合性が高いという特徴があります。

![Azure Cosmos DB シーケンシャル レコード インポート詳細オプションのスクリーンショット](./media/import-data/documentdbsequentialoptions.png)

> [!TIP]
> インポート ツールの接続モードは既定で [DirectTcp] に設定されています。 ファイアウォールの問題が発生した場合は、ポート 443 のみを使用する必要があるため、接続モードを [ゲートウェイ] に切り替えてください。
> 
> 

## <a id="IndexingPolicy"></a>インデックス作成ポリシーの指定
移行ツールを使用してインポート中に Azure Cosmos DB SQL API コレクションを作成するときに、コレクションのインデックス作成ポリシーを指定できます。 Azure Cosmos DB の一括インポートと Azure Cosmos DB のシーケンシャル レコード オプションの詳細オプション セクションで、[インデックス作成ポリシー] セクションに移動します。

![Azure Cosmos DB インデックス作成ポリシー詳細オプションのスクリーンショット](./media/import-data/indexingpolicy1.png)

[インデックス作成ポリシー] の詳細オプションを使用すると、インデックス作成ポリシー ファイルを選択するか、インデックス作成ポリシーを手動で入力するか、既定のテンプレート セットから選択することができます (インデックス作成ポリシー テキスト ボックスを右クリックします)。

ツールには、次のポリシー テンプレートが用意されています。

* [既定]。 このポリシーは、文字列に対しては等値クエリを実行し、数値に対しては ORDER BY、範囲、および等値クエリを使用する場合に最適です。 [範囲] よりもインデックスのストレージ オーバーヘッドが少なくいポリシーです。
* [範囲]。 このポリシーは、数値と文字列の両方に対してORDER BY、範囲、等値クエリを使用する場合に最適です。 [既定] または [ハッシュ] よりもインデックスのストレージ オーバーヘッドが高いポリシーです。

![Azure Cosmos DB インデックス作成ポリシー詳細オプションのスクリーンショット](./media/import-data/indexingpolicy2.png)

> [!NOTE]
> インデックス作成ポリシーを指定しないと、既定のポリシーが適用されます。 インデックス作成ポリシーの詳細については、[Azure Cosmos DB インデックス作成ポリシー](indexing-policies.md)に関する記事をご覧ください。
> 
> 

## <a name="export-to-json-file"></a>JSON ファイルへのエクスポート
Azure Cosmos DB JSON エクスポーターを使用して、使用可能な任意のソース オプションを、JSON ドキュメントの配列が含まれた JSON ファイルにエクスポートできます。 エクスポートはツールによって処理されますが、その結果生成される移行コマンドを表示し、自分でコマンドを実行することもできます。 結果の JSON ファイルは、ローカルまたは Azure BLOB ストレージに保存できます。

![Azure Cosmos DB JSON ローカル ファイル エクスポート オプションのスクリーンショット](./media/import-data/jsontarget.png)

![Azure Cosmos DB JSON Azure Blob ストレージ エクスポート オプションのスクリーンショット](./media/import-data/jsontarget2.png)

必要に応じて、出力される JSON に prettify を実行できます。これにより、生成されるドキュメントのサイズは大きくなりますが、内容は人間が判読しやすいものになります。

    Standard JSON export
    [{"id":"Sample","Title":"About Paris","Language":{"Name":"English"},"Author":{"Name":"Don","Location":{"City":"Paris","Country":"France"}},"Content":"Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.","PageViews":10000,"Topics":[{"Title":"History of Paris"},{"Title":"Places to see in Paris"}]}]

    Prettified JSON export
    [
     {
    "id": "Sample",
    "Title": "About Paris",
    "Language": {
      "Name": "English"
    },
    "Author": {
      "Name": "Don",
      "Location": {
        "City": "Paris",
        "Country": "France"
      }
    },
    "Content": "Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.",
    "PageViews": 10000,
    "Topics": [
      {
        "Title": "History of Paris"
      },
      {
        "Title": "Places to see in Paris"
      }
    ]
    }]

## <a name="advanced-configuration"></a>詳細な構成
詳細な構成画面では、発生したエラーが書き込まれるログ ファイルの場所を指定します。 このページには、次の規則が適用されます。

1. ファイル名を指定しない場合は、すべてのエラーは結果ページに返されます。
2. ディレクトリを指定せずにファイル名を指定した場合は、現在の環境のディレクトリにファイルが作成 (または上書き) されます。
3. 既存のファイルを選択した場合は、ファイルが上書きされます。既存ファイルに追加するオプションはありません。
4. 次に、ログに記録するメッセージとして、すべてのメッセージ、重要なメッセージ、エラーがないメッセージのいずれかを選択します。 最後に、画面上の転送メッセージが進行状況と共に更新される頻度を指定します。

   ![詳細な構成画面のスクリーンショット](./media/import-data/AdvancedConfiguration.png)

## <a name="confirm-import-settings-and-view-command-line"></a>インポート設定の確認とコマンド ラインの表示
1. ソース情報、ターゲット情報、詳細構成を指定した後、移行の概要を確認し、必要に応じて、生成された移行コマンドの表示やコピーを実行します (コマンドのコピーは、インポート操作を自動化するのに役立ちます)。
   
    ![概要画面のスクリーンショット](./media/import-data/summary.png)
   
    ![概要画面のスクリーンショット](./media/import-data/summarycommand.png)
2. ソースとターゲットのオプションに問題がなければ、 **[インポート]** をクリックします。 経過時間、転送済みファイル数、およびエラーに関する情報 ([詳細構成]でファイル名を指定しなかった場合) がインポート処理の進行と共に更新されます。 処理が完了したら、結果をエクスポートできます (インポート エラーに対処する場合など)。
   
    ![Azure Cosmos DB JSON エクスポート オプションのスクリーンショット](./media/import-data/viewresults.png)
3. 新たにインポートを開始する場合は、既存の設定 (接続文字列の情報やソースとターゲットの選択など) をそのまま使用することも、すべての値をリセットすることもできます。
   
    ![Azure Cosmos DB JSON エクスポート オプションのスクリーンショット](./media/import-data/newimport.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次のタスクを行いました。

> [!div class="checklist"]
> * データ移行ツールのインストール
> * さまざまなデータ ソースからのデータのインポート
> * Azure Cosmos DB から JSON へのエクスポート

次のチュートリアルに進み、Azure Cosmos DB を使用してデータにクエリを実行する方法を学ぶことができます。 

> [!div class="nextstepaction"]
>[データにクエリを実行する方法](../cosmos-db/tutorial-query-sql-api.md)
