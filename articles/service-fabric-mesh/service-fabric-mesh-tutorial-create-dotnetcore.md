---
title: チュートリアル - マルチサービス Web アプリケーションを作成してデバッグし、Service Fabric mesh にデプロイする | Microsoft Docs
description: このチュートリアルでは、バックエンド Web サービスと通信する ASP.NET Core Web サイトで構成されるマルチサービス Azure Service Fabric mesh アプリケーションを作成して、ローカルでデバッグし、Azure に発行します。
services: service-fabric-mesh
documentationcenter: .net
author: TylerMSFT
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/17/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: d48d7625221dfb96e0119ef0d42b3b0a8d04baba
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185671"
---
# <a name="tutorial-create-debug-and-deploy-a-multi-service-web-application-to-service-fabric-mesh"></a>チュートリアル: マルチサービス Web アプリケーションを作成してデバッグし、Service Fabric mesh にデプロイする

このチュートリアルは、シリーズの第 1 部です。 ASP.NET Web フロントエンドと ASP.NET Core Web API バックエンド サービスを含む Azure Service Fabric mesh アプリケーションを作成する方法を学びます。 次に、ローカル開発クラスターでアプリをデバッグし、アプリを Azure に発行します。 作業が完了すると、Azure Service Fabric mesh で実行している Service Fabric mesh アプリケーションでサービス間の呼び出しを行う簡単な To Do アプリが完成します。

To Do アプリケーションを手動で作成しない場合は、完成したアプリケーションの[ソース コードをダウンロード](https://github.com/azure-samples/service-fabric-mesh)して、[ローカルでのアプリのデバッグ](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)に進んでもかまいません。

シリーズの第 1 部で学習する内容は次のとおりです。

> [!div class="checklist"]
> * ASP.NET Web フロントエンドで構成される Service Fabric mesh アプリケーションを作成する。
> * To Do 項目を表すモデルを作成する。
> * バックエンド サービスを作成し、そこから データを取得する。
> * バックエンド サービスのモデル ビュー コントローラー パターンの一部としてコントローラーと DataContext を追加する。
> * To Do 項目を表示する Web ページを作成する。
> * バックエンド サービスを識別する環境変数を作成する。

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * Service Fabric mesh アプリケーションをビルドする
> * [アプリをローカルでデバッグする](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [アプリを Azure に発行する](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)できます。

* Service Fabric ランタイム、SDK、Docker、Visual Studio 2017 がインストールされた[開発環境の設定](service-fabric-mesh-howto-setup-developer-environment-sdk.md)が済んでいることを確認します。

* 現在、このチュートリアルのアプリをビルドするときは、英語のロケールを使用する必要があります。

## <a name="create-a-service-fabric-mesh-project"></a>Service Fabric mesh プロジェクトを作成する

Visual Studio を実行し、**[ファイル]** > **[新規]** > **[プロジェクト]** の順に選択します。

**[新しいプロジェクト]** ダイアログの上部にある **[検索]** ボックスに「`mesh`」と入力します。 **[Service Fabric Mesh Application]\(Service Fabric mesh アプリケーション\)** テンプレートを選択します  (テンプレートが表示されない場合は、[開発環境の設定](service-fabric-mesh-howto-setup-developer-environment-sdk.md)に関するページの説明に従って、mesh SDK と VS Tools プレビューをインストールしたことを確認してください)。  

**[名前]** ボックスに「`todolistapp`」と入力し、**[場所]** ボックスにプロジェクト ファイルを格納する場所のフォルダー パスを設定します。

**[ソリューションのディレクトリを作成]** がオンになっていることを確認し、**[OK]** をクリックして Service Fabric mesh プロジェクトを作成します。

![Visual Studio: 新しい Service Fabric mesh プロジェクト ダイアログ](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-project.png)

次は、**[新しい Service Fabric サービス]** ダイアログが表示されます。

### <a name="create-the-web-front-end-service"></a>Web フロントエンド サービスを作成する

**[新しい Service Fabric サービス]** ダイアログで、プロジェクトの種類として **[ASP.NET Core]** を選択し、**[Container OS]\(コンテナー OS\)** が **[Windows]** に設定されていることを確認します。

**[サービス名]** を「**WebFrontEnd**」に設定します。 **[OK]** をクリックして、ASP.NET Core サービスを作成します。

![Visual Studio: 新しい Service Fabric mesh プロジェクト ダイアログ](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-service-fabric-service.png)

次は、**[新しい ASP.NET Core Web アプリケーション]** ダイアログが表示されます。 **[新しい ASP.NET Core Web アプリケーション]** ダイアログで、**[Web アプリケーション]** を選択してから **[OK]** をクリックします。

![Visual Studio: 新しい ASP.NET Core アプリケーション](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-aspnetcore-app.png)

これで、Service Fabric mesh アプリケーションが用意できました。 次は、To Do 情報のモデルを作成します。

## <a name="create-the-to-do-items-model"></a>To Do 項目のモデルを作成する

To Do 項目は、わかりやすくするためにメモリ内のリストに格納されます。 To Do 項目のクラス ライブラリと、To Do 項目を保持するリストを作成します。 このとき **todolistapp** が読み込まれている状態の Visual Studio で、**[ファイル]** > **[追加]** > **[新しいプロジェクト]** の順に選択します。

**[新しいプロジェクト]** ダイアログの上部にある **[検索]** ボックスに「`C# .net core class`」と入力します。 **[Class Library (.NET Core)]\(クラス ライブラリ (.NET Core)\)** テンプレートを選択します。

**[名前]** ボックスに「`Model`」と入力します。 **[OK]** をクリックして、クラス ライブラリを作成します。

ソリューション エクスプローラーで、**[Model]** の **[Class1.cs]** を右クリックし、**[名前の変更]** を選択します。 **ToDoItem.cs** クラスの名前を変更します。 すべての参照の名前を変更するかどうかの選択を求めるメッセージが表示された場合は、**[はい]** をクリックします。

空の `class ToDoItem` の内容を次のコードで置き換えます。

```csharp
public class ToDoItem
{
    public string Description { get; set; }
    public int Index { get; set; }
    public bool Completed { get; set; }

    public ToDoItem(string description)
    {
        Description = description;
        Index = 0;
    }

    public static ToDoItem Load(string description, int index, bool completed)
    {
        ToDoItem newItem = new ToDoItem(description)
        {
            Index = index,
            Completed = completed
        };

        return newItem;
    }
}
```

このクラスは、個々の To Do 項目を表します。

Visual Studio で、**[Model]** クラス ライブラリを右クリックし、**[追加]** > **[クラス]** の順に選択して、To Do 項目を保持するリストを作成します。 **[新しい項目の追加]** ダイアログ ボックスが表示されます。 **[名前]** を「`ToDoList.cs`」に設定し、**[追加]** をクリックします。

**ToDoList.cs** 内の空の `class ToDoList` を次のコードで置き換えます。

```csharp
public class ToDoList
{
    private List<ToDoItem> _items;

    public string Name { get; set; }
    public IEnumerable<ToDoItem> Items { get => _items; }

    public ToDoList(string name)
    {
        Name = name;
        _items = new List<ToDoItem>();
    }

    public ToDoItem Add(string description)
    {
        var item = new ToDoItem(description);
        _items.Add(item);
        item.Index = _items.IndexOf(item);
        return item;
    }
    public void Add(ToDoItem item)
    {
        _items.Add(item);
        item.Index = _items.Count - 1;
    }

    public ToDoItem RemoveAt(int index)
    {
        if (index >= 0 && index < _items.Count)
        {
            var result = _items[index];
            _items.RemoveAt(index);

            // Reorder items
            for (int i = index; i < _items.Count; i++)
            {
                _items[i].Index = i;
            }

            return result;
        }
        else
        {
            throw new IndexOutOfRangeException();
        }
    }
}
```

次は、To Do 項目を追跡する Service Fabric サービスを作成します。

## <a name="create-the-back-end-service"></a>バックエンド サービスを作成する

Visual Studio の **[ソリューション エクスプローラー]** ウィンドウで **[todolistapp]** を右クリックし、**[追加]** > **[新しい Service Fabric サービス]** の順にクリックします。

**[新しい Service Fabric サービス]** ダイアログが表示されます。 プロジェクトの種類として **[ASP.NET Core]** を選択し、**[Container OS]\(コンテナー OS\)** が **[Windows]** に設定されていることを確認します。

**[サービス名]** を「**ToDoService**」に設定します。 **[OK]** をクリックして、ASP.NET Core サービスを作成します。 次に、**[新しい ASP.NET Core Web アプリケーション]** ダイアログが表示されます。 ダイアログ内で **[API]** を選択し、**[OK]** を選択すると、サービスのプロジェクトがソリューションに追加されます。

![Visual Studio: 新しい ASP.NET Core アプリケーション](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-webapi.png)

バックエンド サービスは UI を提供していないので、サービスを起動した際のブラウザーの起動はオフにします。 **ソリューション エクスプローラー**で **ToDoService** を右クリックし、**[プロパティ]** を選択します。 表示された [プロパティ] ウィンドウで、左側の **[デバッグ]** タブを選択し、**[ブラウザーの起動]** をオフにします。 **Ctrl + S** キーを押して、変更を保存します。

このサービスは To Do 情報を維持するので、Model クラス ライブラリへの参照を追加します。 ソリューション エクスプローラーで **[ToDoService]** を右クリックし、**[追加]** > **[参照]** の順に選択します。**[参照マネージャー]** ダイアログ ボックスが表示されます。

**[参照マネージャー]** で、**[Model]** チェック ボックスをオンにし、**[OK]** をクリックします。

### <a name="add-a-data-context"></a>データ コンテキストを追加する

次に、データ モデルからのデータの提供を調整するデータ コンテキストを作成します。

データ コンテキスト クラスを追加するには、ソリューション エクスプローラーで **[ToDoService]** を右クリックし、**[追加]** > **[クラス]** の順に選択します。
表示される **[新しい項目の追加]** ダイアログで、**[クラス]** が選択されていることを確認し、**[名前]** を「`DataContext`」に設定して **[追加]** をクリックします。

**DataContext.cs** 内の空の `class DataContext` の内容を次のコードで置き換えます。

```csharp
public static class DataContext
{
    public static Model.ToDoList ToDoList { get; } = new Model.ToDoList("Azure learning List");

    static DataContext()
    {
        ToDoList = new Model.ToDoList("Main List");

        // Seed to-do list

        ToDoList.Add(Model.ToDoItem.Load("Learn about microservices", 0, true));
        ToDoList.Add(Model.ToDoItem.Load("Learn about Service Fabric", 1, true));
        ToDoList.Add(Model.ToDoItem.Load("Learn about Service Fabric Mesh", 2, false));
    }
}
```

この最小限のデータ コンテキストでは、サンプルの To Do 項目がいくつか入力され、それらにアクセスできるようになります。

### <a name="add-a-controller"></a>コントローラーを追加する

**ToDoService** プロジェクトが作成されたときに、HTTP 要求を処理して HTTP 応答を作成する既定のコントローラーが、テンプレートによって提供されます。 **ソリューション エクスプローラー** の **[ToDoService]** で、**[Controllers]** フォルダーを展開し、**[ValuesController.cs]** ファイルを表示します。 

**[ValuesController.cs]** を右クリックして、**[名前の変更]** をクリックします。 ファイルの名前を `ToDoController.cs` に変更します。 すべての参照の名前を変更するかどうかを尋ねるメッセージが表示された場合は、**[はい]** をクリックします。

**ToDoController.cs** ファイルを開き、`class ToDoController` の内容を次のコードで置き換えます。

```csharp
[Route("api/[controller]")]
public class ToDoController : Controller
{
    // GET api/todo
    [HttpGet]
    public IEnumerable<Model.ToDoItem> Get()
    {
        return DataContext.ToDoList.Items;
    }

    // GET api/todo/5
    [HttpGet("{index}")]
    public Model.ToDoItem Get(int index)
    {
        return DataContext.ToDoList.Items.ElementAt(index);
    }

    //// POST api/values
    //[HttpPost]
    //public void Post([FromBody]string value)
    //{
    //}

    //// PUT api/values/5
    //[HttpPut("{id}")]
    //public void Put(int id, [FromBody]string value)
    //{
    //}

    // DELETE api/values/5
    [HttpDelete("{index}")]
    public void Delete(int index)
    {
    }
}
```

このチュートリアルは別のサービスとの通信に重点を置いているので、追加、削除などは実装しません。

## <a name="create-the-web-page-that-displays-to-do-items"></a>To Do 項目を表示する Web ページを作成する

バックエンド サービスを実装できたら、サービスが提供する To Do 項目を表示するための Web サイトのコードを作成します。 次の手順は **WebFrontEnd** プロジェクトで実施します。

To Do 項目を表示する Web ページでは、**ToDoItem** クラスとリストへのアクセスが必要です。
**ソリューション エクスプローラー** で、**[WebFrontEnd]** を右クリックし、**[追加]** > **[参照]** を選択して、Model プロジェクトへの参照を追加します。**[参照マネージャー]** ダイアログ ボックスが表示されます。

**[参照マネージャー]** で、**[Model]** チェック ボックスをオンにし、**[OK]** をクリックします。

**ソリューション エクスプローラー**で、**[WebFrontEnd]** > **[Pages]** > **[Index.cshtml]** の順に移動して、Index.cshtml ページを開きます。 **Index.cshtml** を開きます。

ファイル全体の内容を、To Do 項目を表示するためのシンプルなテーブルを定義する次の HTML で置き換えます。

```HTML
@page
@model IndexModel
@{
    ViewData["Title"] = "Home page";
}

<div>
    <table class="table-bordered">
        <thead>
            <tr>
                <th>Description</th>
                <th>Done?</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var item in Model.Items)
            {
                <tr>
                    <td>@item.Description</td>
                    <td>@item.Completed</td>
                </tr>
            }
        </tbody>
    </table>
</div>
```

**Index.cshtml**、**Index.cshtml.cs** の順に開き、**ソリューション エクスプローラー**内のインデックス ページのコードを開きます。 **Index.cshtml.cs** の上部に `using System.Net.Http;` を追加します。

`public class IndexModel` の内容を次のコードで置き換えます。

```csharp
public class IndexModel : PageModel
{
    public Model.ToDoItem[] Items = new Model.ToDoItem[] { };

    public void OnGet()
    {
        HttpClient client = new HttpClient();

        using (HttpResponseMessage response = client.GetAsync(backendUrl).GetAwaiter().GetResult())
        {
            if (response.StatusCode == System.Net.HttpStatusCode.OK)
            {
                Items = Newtonsoft.Json.JsonConvert.DeserializeObject<Model.ToDoItem[]>(response.Content.ReadAsStringAsync().Result);
            }
        }
    }

    private static string backendDNSName = $"{Environment.GetEnvironmentVariable("ServiceName")}";
    private static Uri backendUrl = new Uri($"http://{backendDNSName}:{Environment.GetEnvironmentVariable("ApiHostPort")}/api/todo");
}
```

### <a name="create-environment-variables"></a>環境変数を作成する

バックエンド サービスと通信するには、バックエンド サービスの URL が必要です。 このチュートリアルでは次のコードの抜粋 (IndexModel の一部として上部で定義済みです) を使用し、URL を構成する環境変数を読み取ります。

```csharp
private static string backendDNSName = $"{Environment.GetEnvironmentVariable("ServiceName")}";
private static Uri backendUrl = new Uri($"http://{backendDNSName}:{Environment.GetEnvironmentVariable("ApiHostPort")}/api/todo");
```

URL は、サービス名とポートで構成されます。 これらのすべての情報は、**ToDoService** プロジェクトの service.yaml ファイルに存在します。

**ソリューション エクスプローラー**で、**[ToDoService]** プロジェクトに移動し、**[Service Resources]\(サービスのリソース\)** > **[service.yaml]** の順に開きます。

![図 1 - ToDoService の service.yaml ファイル](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-serviceyaml-port.png)

* サービス名である `ToDoService` は、`services:` 内の `name:` の次にあります。上の図の (1) を参照してください。
* ポートである `20008` は、`endpoints:` 内の `port:` の次にあります。上の図の (2) を参照してください。 プロジェクトのポート番号は異なる場合があります。

次に、サービス名とポート番号を表す環境変数を WebFrontEnd プロジェクト内で定義し、バックエンド サービスを呼び出せるようにします。

**ソリューション エクスプローラー**で、**[WebFrontEnd]** > **[Service Resources]\(サービスのリソース\)** > **[service.yaml]** の順に移動して、バックエンド サービスのアドレスを指定する変数を定義します。

service.yaml ファイルを開き、次の変数を `environmentVariables` に追加します。 ここで重要になるのはスペースです。追加する変数は、`environmentVariables:` の他の変数とインデントを揃えてください。

> [!IMPORTANT]
> service.yaml ファイル内の変数をインデントするには、タブではなくスペースを使用する必要があります。そうしないと、コンパイルができません。 Visual Studio では、環境変数を作成すると、タブが挿入されることがあります。 タブはすべてスペースに置き換えてください。 **[ビルド]** デバッグ出力にエラーが表示されていても、アプリは起動されます。 ただし、タブをスペースに変換しない限り機能しません。 service.yaml ファイルでタブが使用されていないことを確認するには、Visual Studio のエディターで **[編集]**  > **[詳細設定]**  > **[空白の表示]** の順に選択し、空白を可視化します。

**WebFrontEnd** プロジェクトの **service.yaml** ファイルは次のようなものにする必要がありますが、`ApiHostPort` の値は異なるものになると考えられます。

![WebFrontEnd プロジェクトの Service.yaml](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-serviceyaml-envvars.png)

これで、Service Fabric mesh アプリケーションのイメージを、バックエンドの Web サービスと共に、ローカル クラスターにビルドおよびデプロイする準備が整いました。

## <a name="next-steps"></a>次の手順

チュートリアルのこの部分で学習した内容は次のとおりです。

> [!div class="checklist"]
> * ASP.NET Web フロントエンドで構成される Service Fabric mesh アプリケーションを作成する。
> * To Do 項目を表すモデルを作成する。
> * バックエンド サービスを作成し、そこから データを取得する。
> * バックエンド サービスのモデル ビュー コントローラー パターンの一部としてコントローラーと DataContext を追加する。
> * To Do 項目を表示する Web ページを作成する。
> * バックエンド サービスを識別する環境変数を作成する。

次のチュートリアルに進みます。
> [!div class="nextstepaction"]
> [ローカルで実行している Service Fabric mesh アプリケーションをデバッグする](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
