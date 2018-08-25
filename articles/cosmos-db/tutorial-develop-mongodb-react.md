---
title: Azure を対象とした MongoDB、React、Node.js のチュートリアル | Microsoft Docs
description: このチュートリアル シリーズでは、React と Node.js で MongoDB に使われる API をそのまま使用して、Azure Cosmos DB を対象とした MongoDB アプリを作成する方法について、動画を交えながら説明しています。
services: cosmos-db
author: johnpapa
manager: kfile
editor: ''
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/05/2017
ms.author: jopapa
ms.custom: mvc
ms.openlocfilehash: eac537c5b068b795fee7fb845eb7a98fb11316d3
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2018
ms.locfileid: "41919463"
---
# <a name="create-a-mongodb-app-with-react-and-azure-cosmos-db"></a>React と Azure Cosmos DB を使って MongoDB アプリを作成する  

このビデオ チュートリアルでは、React フロントエンドでヒーローの追跡アプリを作成する方法を複数のパートにわたって紹介しています。 このアプリは、サーバーに Node と Express を使用し、[MongoDB API](mongodb-introduction.md) で Azure Cosmos DB に接続した後、アプリのサーバー部分に React フロントエンドを接続するものです。 また、Azure Portal からポイントアンドクリック方式で Azure Cosmos DB をスケーリングする方法や、アプリをインターネットにデプロイしてだれでもお気に入りのヒーローを追跡できるようにする方法も紹介しています。 

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) は、MongoDB クライアント接続をサポートしています。そのため MongoDB を Azure Cosmos DB に置き換えても、MongoDB アプリに使われるコードはそのまま利用できるうえ、クラウドへの簡単なデプロイ、スケーリング、超高速の読み取りと書き込みなど、さまざまな利点がもたらされます。  

このマルチパート チュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * はじめに
> * プロジェクトを設定する
> * React で UI を作成する
> * Azure Portal を使用して Azure Cosmos DB アカウントを作成する
> * Mongoose を使って Azure Cosmos DB に接続する
> * React、Create、Update、Delete の各操作をアプリに追加する

これと同じアプリを Angular で作成する方法については、 [Angular のチュートリアル ビデオ シリーズ](tutorial-develop-mongodb-nodejs.md)をご覧ください。

## <a name="prerequisites"></a>前提条件
* [Node.js](https://www.nodejs.org)

### <a name="finished-project"></a>完全なプロジェクト
完成したアプリケーションは、[GitHub から](https://github.com/Azure-Samples/react-cosmosdb)ダウンロードできます。

## <a name="introduction"></a>はじめに 

この動画では、Burke Holland が Azure Cosmos DB の概要を説明し、このビデオ シリーズで作成するアプリについて簡単に紹介しています。 

> [!VIDEO https://www.youtube.com/embed/58IflnJbYJc]

## <a name="project-setup"></a>プロジェクトの設定

この動画では、Express と React を同じプロジェクトで設定する方法を紹介します。 その後、プロジェクトに含まれるコードを実際に紹介しながら Burke が解説します。

> [!VIDEO https://www.youtube.com/embed/ytFUPStJJds]

## <a name="build-the-ui"></a>UI の作成

この動画では、アプリケーションのユーザー インターフェイス (UI) を React で作成する方法を紹介します。 

> [!NOTE]
> この動画で言及されている CSS は、[react-cosmosdb GitHub リポジトリ](https://github.com/Azure-Samples/react-cosmosdb/blob/master/src/index.css)にあります。

> [!VIDEO https://www.youtube.com/embed/SzHzX0fTUUQ]

## <a name="connect-to-azure-cosmos-db"></a>Azure Cosmos DB への接続

この動画では、Azure Portal から Azure Cosmos DB アカウントを作成して MongoDB と Mongoose のパッケージをインストールし、Azure Cosmos DB 接続文字列を使って、新しく作成したアカウントにアプリを接続する方法を紹介します。 

> [!VIDEO https://www.youtube.com/embed/0U2jV1thfvs]

## <a name="read-and-create-heroes-in-the-app"></a>アプリでのヒーローの読み取りと作成

この動画では、Cosmos DB データベースにヒーローを作成したり、ヒーローを読み取ったりする方法のほか、Postman と React UI でそれらのメソッドをテストする方法について紹介します。 

> [!VIDEO https://www.youtube.com/embed/AQK9n_8fsQI] 

## <a name="delete-and-update-heroes-in-the-app"></a>アプリでのヒーローの削除と更新

この動画では、アプリからヒーローを削除したり更新したりする方法のほか、更新した結果を UI に表示する方法について取り上げます。 

> [!VIDEO https://www.youtube.com/embed/YmaGT7ztTQM] 

## <a name="complete-the-app"></a>アプリの仕上げ

いよいよアプリの仕上げに入ります。この動画で、UI をバックエンド API に接続する方法をご覧ください。 

> [!VIDEO https://www.youtube.com/embed/TcSm2ISfTu8]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このアプリの使用を続けない場合は、以下の手順に従い、このチュートリアルで作成したすべてのリソースを Azure Portal で削除してください。 

1. Azure Portal の左側のメニューで、**[リソース グループ]** をクリックしてから、作成したリソースの名前をクリックします。 
2. リソース グループのページで **[削除]** をクリックし、削除するリソースの名前をテキスト ボックスに入力してから **[削除]** をクリックします。

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * React、Node、Express、Azure Cosmos DB を使ってアプリを作成する 
> * Azure Cosmos DB アカウントを作成する
> * Azure Cosmos DB アカウントにアプリを接続する
> * Postman を使ってアプリをテストする
> * アプリケーションを実行してヒーローをデータベースに追加する

このチュートリアル シリーズには今後も、アプリケーションをデプロイしたりデータをグローバルにレプリケートしたりする方法についての動画が追加される予定ですので、定期的にチェックしてください。

次のチュートリアルに進み、MongoDB のデータを Azure Cosmos DB にインポートする方法を学習しましょう。  

> [!div class="nextstepaction"]
> [MongoDB データを Azure Cosmos DB にインポートする](mongodb-migrate.md)
 
