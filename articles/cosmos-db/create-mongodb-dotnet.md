---
title: 'Azure Cosmos DB: .NET と MongoDB API による Web アプリの構築 | Microsoft Docs'
description: Azure Cosmos DB MongoDB API への接続とクエリに使用できる .NET コード サンプルについて説明します。
services: cosmos-db
author: slyons
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.custom: quick start connect, mvc
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/22/2018
ms.author: sclyon
ms.openlocfilehash: a87b4c50beea21f9d7721b60ae691eeafaedfc05
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2018
ms.locfileid: "42022835"
---
# <a name="azure-cosmos-db-build-a-mongodb-api-web-app-with-net-and-the-azure-portal"></a>Azure Cosmos DB: .NET と Azure Portal による MongoDB API Web アプリの構築

Azure Cosmos DB は、Microsoft のグローバルに配布されるマルチモデル データベース サービスです。 Azure Cosmos DB の中核をなすグローバル配布と水平方向のスケール機能を活用して、ドキュメント、キー/値、およびグラフ データベースをすばやく作成および照会できます。 

このクイックスタートでは、Azure Portal を使用して、Azure Cosmos DB [MongoDB API](mongodb-introduction.md) アカウント、ドキュメント データベース、コレクションを作成する方法を説明します。 さらに、[MongoDB .NET ドライバー](https://docs.mongodb.com/ecosystem/drivers/csharp/)に基づいたタスク リスト Web アプリを構築し、デプロイします。

## <a name="prerequisites-to-run-the-sample-app"></a>サンプル アプリを実行するための前提条件

サンプルを実行するためには、[Visual Studio](https://www.visualstudio.com/downloads/) および有効な Azure CosmosDB アカウントが必要です。

まだ Visual Studio をお持ちでない場合は、[Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) をダウンロードし、セットアップで **[ASP.NET と Web 開発]** のワークロードをインストールしてください。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

<a id="create-account"></a>
## <a name="create-a-database-account"></a>データベース アカウントの作成

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

この記事で説明されているサンプルは、MongoDB.Driver バージョン 2.6.1 と互換性があります。

## <a name="clone-the-sample-app"></a>サンプル アプリの複製

まず、サンプル MongoDB API アプリを GitHub からダウンロードします。 このアプリには、MongoDB のドキュメント ストレージ モデルを使ったタスク リストが実装されています。

1. コマンド プロンプトを開いて git-samples という名前の新しいフォルダーを作成し、コマンド プロンプトを閉じます。

    ```bash
    md "C:\git-samples"
    ```

2. git bash などの git ターミナル ウィンドウを開いて、`cd` コマンドを使用して、サンプル アプリをインストールする新しいフォルダーに変更します。

    ```bash
    cd "C:\git-samples"
    ```

3. 次のコマンドを実行して、サンプル レポジトリを複製します。 このコマンドは、コンピューター上にサンプル アプリのコピーを作成します。 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-getting-started.git
    ```

git を使いたくない場合は、[プロジェクトを ZIP ファイルとしてダウンロード](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-getting-started/archive/master.zip)することもできます。

## <a name="review-the-code"></a>コードの確認

この手順は省略可能です。 コード内のデータベース リソースの作成方法に関心がある場合は、次のスニペットを確認できます。 関心がない場合は、「[接続文字列の更新](#update-your-connection-string)」に進んでください。 

次のスニペットはすべて DAL ディレクトリ内の Dal.cs ファイルからのものです。

* Mongo クライアントを初期化します。

    ```cs
        MongoClientSettings settings = new MongoClientSettings();
        settings.Server = new MongoServerAddress(host, 10255);
        settings.UseSsl = true;
        settings.SslSettings = new SslSettings();
        settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;

        MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
        MongoIdentityEvidence evidence = new PasswordEvidence(password);

        settings.Credential = new MongoCredential("SCRAM-SHA-1", identity, evidence);

        MongoClient client = new MongoClient(settings);
    ```

* データベースとコレクションを取得します。

    ```cs
    private string dbName = "Tasks";
    private string collectionName = "TasksList";

    var database = client.GetDatabase(dbName);
    var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
    ```

* すべてのドキュメントを取得します。

    ```cs
    collection.Find(new BsonDocument()).ToList();
    ```

* タスクを作成して、MongoDB コレクションに挿入します。

   ```csharp
    public void CreateTask(MyTask task)
    {
        var collection = GetTasksCollectionForEdit();
        try
        {
            collection.InsertOne(task);
        }
        catch (MongoCommandException ex)
        {
            string msg = ex.Message;
        }
    }
   ```
   同様に、[collection.UpdateOne()](https://docs.mongodb.com/stitch/mongodb/actions/collection.updateOne/index.html) メソッドと [collection.DeleteOne()](https://docs.mongodb.com/stitch/mongodb/actions/collection.deleteOne/index.html) メソッドを使用して、ドキュメントの更新と削除を実行できます。 

## <a name="update-your-connection-string"></a>接続文字列を更新する

ここで Azure Portal に戻り、接続文字列情報を取得し、アプリにコピーします。

1. [Azure Portal](http://portal.azure.com/) で、Azure Cosmos DB アカウントの左のナビゲーションから、**[接続文字列]** をクリックし、**[読み取り/書き込みキー]** をクリックします。 次の手順では、画面右側のコピー ボタンを使用して、ユーザー名、パスワード、ホストを Dal.cs ファイルにコピーします。

2. **DAL** ディレクトリ内の **Dal.cs** ファイルを開きます。 

3. ポータルの **username** 値をコピーし (コピー ボタンを使用して)、**Dal.cs** ファイル内の **username** の値に設定します。 

4. 次に、ポータルの **host** 値をコピーし、**Dal.cs** ファイル内の **host** の値に設定します。 

5. 最後に、ポータルの **password** 値をコピーし、**Dal.cs** ファイル内の **password** の値に設定します。 

これで、Azure Cosmos DB と通信するために必要なすべての情報でアプリを更新しました。 
    
## <a name="run-the-web-app"></a>Web アプリの実行

1. Visual Studio の**ソリューション エクスプローラー**でプロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。 

2. NuGet の **[参照]** ボックスに「*MongoDB.Driver*」と入力します。

3. 結果から、**MongoDB.Driver** ライブラリをインストールします。 これにより、MongoDB.Driver パッケージとすべての依存関係がインストールされます。

4. Ctrl + F5 キーを押してアプリケーションを実行します。 ブラウザーにアプリが表示されます。 

5. ブラウザーで、**[作成]** をクリックし、タスク リスト アプリで、いくつか新しいタスクを作成します。

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal での SLA の確認

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Azure Cosmos DB アカウントを作成し、MongoDB の API を使用して Web アプリを実行する方法を説明しました。 これで、Cosmos DB アカウントに追加のデータをインポートできます。 

> [!div class="nextstepaction"]
> [MongoDB API 用に Azure Cosmos DB にデータをインポートする](mongodb-migrate.md)

