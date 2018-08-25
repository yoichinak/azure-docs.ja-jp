---
title: 'Azure Cosmos DB: .NET と MongoDB API を使用して Xamarin.Forms アプリを構築する | Microsoft Docs'
description: Azure Cosmos DB MongoDB API への接続とクエリに使用できる Xamarin コード サンプルについて説明します。
services: cosmos-db
author: codemillmatt
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.custom: quickstart, xamarin
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 06/20/2018
ms.author: masoucou
ms.openlocfilehash: 42e2a7f57cbe40dbef8469e4e9ca020f4346a897
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38309897"
---
# <a name="quickstart-build-a-mongodb-api-xamarinforms-app-with-net-and-the-azure-portal"></a>クイック スタート: .NET と Azure portal を使用して MongoDB API Xamarin.Forms アプリを構築する

Azure Cosmos DB は、Microsoft のグローバルに配布されるマルチモデル データベース サービスです。 Azure Cosmos DB の中核をなすグローバル配布と水平方向のスケール機能を活用して、ドキュメント、キー/値、およびグラフ データベースをすばやく作成および照会できます。

このクイックスタートでは、Azure Portal を使用して、Azure Cosmos DB [MongoDB API](mongodb-introduction.md) アカウント、ドキュメント データベース、コレクションを作成する方法を説明します。 その後、[MongoDB .NET ドライバー](https://docs.mongodb.com/ecosystem/drivers/csharp/)を使用して、TODO Xamarin.Forms アプリを構築します。

## <a name="prerequisites-to-run-the-sample-app"></a>サンプル アプリを実行するための前提条件

サンプルを実行するには、[Visual Studio](https://www.visualstudio.com/downloads/) または [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) と、有効な Azure CosmosDB アカウントが必要です。

まだ Visual Studio を持っていない場合は、[Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) をダウンロードし、セットアップで **[.NET によるモバイル開発]** のワークロードをインストールしてください。

Mac で作業したい場合は、[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) をダウンロードして、セットアップを実行します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<a id="create-account"></a>

## <a name="create-a-database-account"></a>データベース アカウントの作成

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

この記事で説明されているサンプルは、MongoDB.Driver バージョン 2.6.1 と互換性があります。

## <a name="clone-the-sample-app"></a>サンプル アプリの複製

まず、サンプル MongoDB API アプリを GitHub からダウンロードします。 このアプリには、MongoDB のドキュメント ストレージ モデルを使った TODO アプリが実装されています。

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-xamarin-getting-started.git
    ```

git を使いたくない場合は、[プロジェクトを ZIP ファイルとしてダウンロード](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-xamarin-getting-started/archive/master.zip)することもできます。

## <a name="review-the-code"></a>コードの確認

この手順は省略可能です。 コード内のデータベース リソースの作成方法に関心がある場合は、次のスニペットを確認できます。 関心がない場合は、「[接続文字列の更新](#update-your-connection-string)」に進んでください。

以下のスニペットはすべて、パス src/TaskList.Core/Services/MongoService.cs にある `MongoService` クラスから取得されています。

* Mongo クライアントを初期化します。
    ```cs
    MongoClientSettings settings = MongoClientSettings.FromUrl(
        new MongoUrl(APIKeys.ConnectionString)
    );

    settings.SslSettings =
        new SslSettings() { EnabledSslProtocols = SslProtocols.Tls12 };

    MongoClient mongoClient = new MongoClient(settings);
    ```

* データベースとコレクションへの参照を取得します。 データベースとコレクションがまだ存在しない場合、MongoDB .NET SDK が両方を自動的に作成します。
    ```cs
    string dbName = "MyTasks";
    string collectionName = "TaskList";

    var db = mongoClient.GetDatabase(dbName);

    var collectionSettings = new MongoCollectionSettings {
        ReadPreference = ReadPreference.Nearest
    };

    tasksCollection = db.GetCollection<MyTask>(collectionName, collectionSettings);
    ```
* すべてのドキュメントをリストとして取得します。
    ```cs
    var allTasks = await TasksCollection
                    .Find(new BsonDocument())
                    .ToListAsync();
    ```

* 特定のドキュメントのクエリを行います。
    ```cs
    public async Task<List<MyTask>> GetIncompleteTasksDueBefore(DateTime date)
    {
        var tasks = await TasksCollection
                        .AsQueryable()
                        .Where(t => t.Complete == false)
                        .Where(t => t.DueDate < date)
                        .ToListAsync();

        return tasks;
    }
    ```

* タスクを作成して、MongoDB コレクションに挿入します。
    ```cs
    public async Task CreateTask(MyTask task)
    {
        await TasksCollection.InsertOneAsync(task);
    }
    ```

* MongoDB コレクション内のタスクを更新します。
    ```cs
    public async Task UpdateTask(MyTask task)
    {
        await TasksCollection.ReplaceOneAsync(t => t.Id.Equals(task.Id), task);
    }
    ```

* MongoDB コレクションからタスクを削除します。
    ```cs
    public async Task DeleteTask(MyTask task)
    {
        await TasksCollection.DeleteOneAsync(t => t.Id.Equals(task.Id));
    }
    ```

<a id="update-your-connection-string"></a>

## <a name="update-your-connection-string"></a>接続文字列を更新する

ここで Azure Portal に戻り、接続文字列情報を取得し、アプリにコピーします。

1. [Azure Portal](http://portal.azure.com/) で、Azure Cosmos DB アカウントの左のナビゲーションから、**[接続文字列]** をクリックし、**[読み取り/書き込みキー]** をクリックします。 次の手順では、画面の右側にあるコピー ボタンを使って、プライマリ接続文字列をコピーします。

2. **TaskList.Core** プロジェクトの **Helpers** ディレクトリにある **APIKeys.cs** ファイルを開きます。

3. ポータルから (コピー ボタンを使って) **プライマリ接続文字列**の値をコピーし、**APIKeys.cs** ファイルの **ConnectionString** フィールドの値に設定します。

これで、Azure Cosmos DB と通信するために必要なすべての情報でアプリを更新しました。

## <a name="run-the-app"></a>アプリの実行

### <a name="visual-studio-2017"></a>Visual Studio 2017

1. Visual Studio の**ソリューション エクスプローラー**で各プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。
2. **[Restore all NuGet packages]\(すべての NuGet パッケージの復元\)** をクリックします。
3. **TaskList.Android** を右クリックして、**[スタートアップ プロジェクトとして設定]** を選択します。
4. F5 キーを押してアプリケーションのデバッグを開始します。
5. iOS で実行する場合は、まず、コンピューターを Mac に接続します (方法については、こちらの[説明](https://docs.microsoft.com/en-us/xamarin/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio)をご覧ください)。
6. **TaskList.iOS** プロジェクトを右クリックして、**[スタートアップ プロジェクトとして設定]** を選択します。
7. F5 キーを押してアプリケーションのデバッグを開始します。

### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1. プラットフォームのドロップダウン リストで、実行するプラットフォームに応じて、TaskList.iOS または TaskList.Android のいずれかを選択します。
2. Command + Enter キーを押してアプリケーションのデバッグを開始します。

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal での SLA の確認

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Azure Cosmos DB アカウントを作成し、MongoDB 用 API を使用して Xamarin.Forms アプリを実行する方法を学習しました。 これで、Cosmos DB アカウントに追加のデータをインポートできます。

> [!div class="nextstepaction"]
> [MongoDB API 用に Azure Cosmos DB にデータをインポートする](mongodb-migrate.md)