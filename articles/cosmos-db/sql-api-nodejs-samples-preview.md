---
title: Azure Cosmos DB の Node.js サンプル | Microsoft Docs
description: CRUD 操作など、Azure Cosmos DB の一般的なタスクについては、GitHub の Node.js のサンプルを参照してください。
keywords: Node.js のサンプル
services: cosmos-db
author: deborahc
manager: kfile
editor: monicar
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: na
ms.topic: sample
ms.date: 07/31/18
ms.author: deborahc
ms.openlocfilehash: 28ea4b45c68336c78dc17b5c6753147e063139d6
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39609074"
---
# <a name="azure-cosmos-db-nodejs-examples"></a>Azure Cosmos DB Node.js のサンプル
> [!div class="op_single_selector"]
> * [.NET のサンプル](sql-api-dotnet-samples.md)
> * [Java のサンプル](sql-api-java-samples.md)
> * [非同期 Java のサンプル](sql-api-async-java-samples.md)
> * [Node.js のサンプル](sql-api-nodejs-samples.md)
> * [Node.js のサンプル - v2.0 プレビュー](sql-api-nodejs-samples-preview.md)
> * [Python のサンプル](sql-api-python-samples.md)
> * [Azure のコード サンプル ギャラリー](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Azure Cosmos DB のリソースで CRUD 操作などの一般的な操作を実行するサンプル ソリューションは、[azure-cosmos-js](https://github.com/Azure/azure-cosmos-js/tree/master/samples) GitHub リポジトリに含まれています。 この記事では、次の内容について説明します。

* 各 Node.js サンプル プロジェクト ファイルのタスクへのリンク。
* 関連する API リファレンス コンテンツへのリンク。

**前提条件**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [Visual Studio サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)こともできます: Visual Studio サブスクリプションにより、有料の Azure サービスで使用できるクレジットが毎月提供されます。

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

[JavaScript SDK](sql-api-sdk-node.md) も必要となります。
   
   > [!NOTE]
   > 各サンプルは自己完結型であり、自身をセットアップし、自身をクリーンアップします。 そのため、一連のサンプルで、[Containers.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest#create) の呼び出しが複数回発行されます。 これが行われるたびに、作成するコンテナーのパフォーマンス レベルに従い、1 時間分の使用量がサブスクリプションに課金されます。
   > 
   > 

## <a name="database-examples"></a>データベースのサンプル
[DatabaseManagement](https://github.com/Azure/azure-cosmos-js/tree/master/samples/DatabaseManagement) プロジェクトの [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement/app.js) ファイルは、次のタスクを実行する方法を示しています。

| タスク | API リファレンス |
| --- | --- |
| [データベースの作成 (データベースが存在しない場合)](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/DatabaseManagement/app.js#L35-L37) |[Databases.createIfNotExists](https://docs.microsoft.com/javascript/api/%40azure/cosmos/databases?view=azure-node-latest#createifnotexists) |
| [アカウントのデータベースの一覧表示](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/DatabaseManagement/app.js#L40-L42) |[Databases.readAll](https://docs.microsoft.com/javascript/api/%40azure/cosmos/databases?view=azure-node-latest#readall) |
| [ID でのデータベースの読み取り](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/DatabaseManagement/app.js#L40-L42) |[Database.read](https://docs.microsoft.com/javascript/api/%40azure/cosmos/database?view=azure-node-latest#read) |
| [データベースの削除](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/DatabaseManagement/app.js#L57-L60) |[Database.delete](https://docs.microsoft.com/javascript/api/%40azure/cosmos/database?view=azure-node-latest#delete) |

## <a name="container-examples"></a>コンテナーの例
[ContainerManagement](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ItemManagement) プロジェクトの [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement/app.js) ファイルは、次のタスクを実行する方法を示しています。

| タスク | API リファレンス |
| --- | --- |
| [コンテナーの作成 (コンテナーが存在しない場合)](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ContainerManagement/app.js#L36-L37) |[Containers.createIfNotExists](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest#createifnotexists) |
| [アカウントのコンテナーの一覧表示](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ContainerManagement/app.js#L36-L37) |[Containers.readAll](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest#readall) |
| [ID でのコレクションの読み取り](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ContainerManagement/app.js#L47-L51) |[Container.read](https://docs.microsoft.com/javascript/api/%40azure/cosmos/container?view=azure-node-latest#read) |
| [コンテナーの削除](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ContainerManagement/app.js#L54-L55) |[Container.delete](https://docs.microsoft.com/javascript/api/%40azure/cosmos/container?view=azure-node-latest#delete) |

## <a name="item-examples"></a>項目の例
[ItemManagement](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ItemManagement) プロジェクトの [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement/app.js) ファイルは、次のタスクを実行する方法を示しています。

| タスク | API リファレンス |
| --- | --- |
| [項目の作成](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L49-L56) |[Items.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest#create) |
| [コンテナー内のすべての項目の読み取り](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L59-L64) |[Items.readAll](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest#readall) |
| [ID での項目の読み取り](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L59-L64) |[Item.read](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest#read) |
| [項目の読み取り (項目が変更されている場合のみ)](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L73-L94) |[Item.read](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest#read)<br/>[RequestOptions.accessCondition](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#accesscondition) |
| [ドキュメントのクエリ](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L97-L118) |[Items.query](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest#query) |
| [項目の置換](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L131-L136) |[Item.replace](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest#replace) |
| [条件付き ETag チェックによる項目の置換](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L139-L160) |[Item.replace](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest#replace)<br/>[RequestOptions.accessCondition](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#accesscondition) |
| [項目の削除](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L162-L164) |[Item.delete](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest#delete) |

## <a name="indexing-examples"></a>インデックス作成のサンプル
[IndexManagement](https://github.com/Azure/azure-cosmos-js/tree/master/samples/IndexManagement) プロジェクトの [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement/app.js) ファイルは、次のタスクを実行する方法を示しています。

| タスク | API リファレンス |
| --- | --- |
| [特定のドキュメントの手動でのインデックス作成](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L135-L177) |[RequestOptions.indexingDirective: 'include'](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#indexingdirective) |
| [インデックスからの特定のドキュメントの手動での除外](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L90-L131) |[RequestOptions.indexingDirective: 'exclude'](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#indexingdirective) |
| [一括インポートでの非同期インデックス作成の使用または大量のコンテナーの読み取り](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L183-L214) |[IndexingMode.Lazy](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexingmode?view=azure-node-latest) |
| [インデックスからのパスの除外](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L352-L429) |[IndexingPolicy.ExcludedPath](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexingpolicy?view=azure-node-latest#excludedpaths) |
| [範囲の操作中での文字列のパスのスキャンの許可](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L219-L275) |[FeedOptions.EnableScanInQuery](https://docs.microsoft.com/javascript/api/%40azure/cosmos/feedoptions?view=azure-node-latest#enablescaninquery) |
| [文字列のパスでの範囲インデックスの作成](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L281-L346) |[IndexKind.Range](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexkind?view=azure-node-latest)、[IndexingPolicy](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexingpolicy?view=azure-node-latest)、[Items.query](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest#query) |
| [既定の indexPolicy のコンテナーを作成し、オンラインで更新する](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L435-L507) |[Containers.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest#create)


インデックス作成の詳細については、「[Azure Cosmos DB インデックス作成ポリシー](indexing-policies.md)」をご覧ください。

## <a name="server-side-programming-examples"></a>サーバー側プログラミングのサンプル
[ServerSideScripts](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ServerSideScripts) プロジェクトの [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/app.js) ファイルは、次のタスクを実行する方法を示しています。

| タスク | API リファレンス |
| --- | --- |
| [ストアド プロシージャの作成](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ServerSideScripts/JS/upsert.js#L12-L72) |[StoredProcedures.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/storedprocedures?view=azure-node-latest) |
| [ストアド プロシージャの実行](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ServerSideScripts/app.js#L44) |[StoredProcedure.execute](https://docs.microsoft.com/javascript/api/%40azure/cosmos/storedprocedure?view=azure-node-latest#execute) |

サーバー側プログラミングの詳細については、「[Azure Cosmos DB server-side programming: Stored procedures, database triggers, and UDFs (Azure Cosmos DB のサーバー側プログラミング: ストアド プロシージャ、データベース トリガー、UDF)](programming.md)」をご覧ください。

