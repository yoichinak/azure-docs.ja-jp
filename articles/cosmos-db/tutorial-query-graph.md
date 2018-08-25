---
title: Azure Cosmos DB でグラフ データのクエリを実行する方法 | Microsoft Docs
description: Azure Cosmos DB でグラフ データのクエリを実行する方法を学習する
services: cosmos-db
author: luisbosquez
manager: kfile
editor: ''
tags: ''
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: lbosq
ms.custom: mvc
ms.openlocfilehash: ad38976f439c399d839d6c5ee9dcd6ade7726c71
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2018
ms.locfileid: "37081909"
---
# <a name="tutorial-query-azure-cosmos-db-graph-api-by-using-gremlin"></a>チュートリアル: Gremlin を使って Azure Cosmos DB Graph API を照会する

Azure Cosmos DB [Graph API](graph-introduction.md) は [Gremlin](https://github.com/tinkerpop/gremlin/wiki) クエリをサポートしています。 この記事では、使用を開始できるようにサンプル ドキュメントとクエリを提供します。 詳しい Gremlin のリファレンスについては、[Gremlin のサポート](gremlin-support.md)に関する記事を参照してください。

この記事に含まれるタスクは次のとおりです。 

> [!div class="checklist"]
> * Gremlin を使用してデータのクエリを実行する

## <a name="prerequisites"></a>前提条件

クエリを実行するには、Azure Cosmos DB アカウントがあり、コンテナーにグラフ データがあることが必要です。 どちらもない場合には、 [5 分でできるクイックスタート](create-graph-dotnet.md)か[開発者向けチュートリアル](tutorial-query-graph.md)を実行して、アカウントを作成し、データベースにデータを設定します。 次のクエリを実行するには、[Gremlin コンソール](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console)、またはお気に入りの Gremlin ドライバーを使用します。

## <a name="count-vertices-in-the-graph"></a>グラフの頂点を数える

次のスニペットはグラフ内の頂点の数をカウントする方法を示しています。

```
g.V().count()
```

## <a name="filters"></a>フィルター

Gremlin の `has` ステップと `hasLabel` ステップを使用してフィルターを実行できます。また、`and`、`or`、`not` を使用してそれらを組み合わせて複雑なフィルターを作成できます。 Azure Cosmos DB では、頂点と角度内のすべてのプロパティのインデックスをスキーマに依存せずに作成することができ、高速クエリを実現します。

```
g.V().hasLabel('person').has('age', gt(40))
```

## <a name="projection"></a>プロジェクション

`values` ステップを使用してクエリ結果の特定のプロパティを投影できます。

```
g.V().hasLabel('person').values('firstName')
```

## <a name="find-related-edges-and-vertices"></a>関連するエッジと頂点を検索する

ここまでは、あらゆるデータベースで使用できるクエリ操作のみを見てきました。 関連するエッジと頂点にナビゲートする必要がある場合、グラフではトラバーサル操作が高速かつ効率的です。 Thomas の友人をすべて探しましょう。 これには、 Gremlin の `outE` ステップを使用して Thomas のエッジ (外側) すべてを検索してから、Gremlin の `inV` ステップを使用してそれらのエッジの頂点 (内側) をトラバーサルします。

```cs
g.V('thomas').outE('knows').inV().hasLabel('person')
```

次のクエリは、`outE` と `inV` を 2 回呼び出し、2 つのホップを実行して Thomas の "友人の友人" をすべて検索します。 

```cs
g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```

Gremlin を使用すると、さらに複雑なクエリを作成したり、強力なグラフ トラバーサル ロジックを実装したりできます。これには、フィルター式の組み合わせ、`loop` ステップを使用したループの実行、`choose` ステップを使用した条件付きナビゲーションの実装などが含まれます。 他にどのようなことができるかについて詳しくは、[Gremlin のサポート](gremlin-support.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次の手順を行いました。

> [!div class="checklist"]
> * Graph を使用してクエリを実行する方法を学習しました。 

これで、"概念" セクションに進み、Cosmos DB の詳細について学習できるようになりました。

> [!div class="nextstepaction"]
> [グローバル配信](distribute-data-globally.md) 

