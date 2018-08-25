---
title: Azure Functions C# スクリプト開発者向けリファレンス
description: C# スクリプトを使用して Azure Functions を開発する方法について説明します。
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
keywords: Azure Functions, 機能, イベント処理, Webhook, 動的コンピューティング, サーバーなしのアーキテクチャ
ms.service: functions
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 12/12/2017
ms.author: glenga
ms.openlocfilehash: 8206b4a0673c9744abf74e75a06d20e064475349
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2018
ms.locfileid: "39344275"
---
# <a name="azure-functions-c-script-csx-developer-reference"></a>Azure Functions C# スクリプト (.csx) 開発者向けリファレンス

<!-- When updating this article, make corresponding changes to any duplicate content in functions-dotnet-class-library.md -->

この記事では、C# スクリプト (*.csx*) を使用した Azure Functions 開発の概要を示します。

Azure Functions では、C# および C# スクリプト プログラミング言語をサポートします。 [Visual Studio クラス ライブラリ プロジェクトでの C# の使用](functions-develop-vs.md)に関するガイダンスをお探しの場合は、[C# 開発者向けリファレンス](functions-dotnet-class-library.md)のページをご覧ください。

この記事では、「[Azure Functions の開発者向けガイド](functions-reference.md)」を既に読んでいることを前提としています。

## <a name="how-csx-works"></a>.csx のしくみ

Azure Functions の C# スクリプト エクスペリエンスは、[Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki/Introduction) に基づいています。 データは、メソッドの引数を使用して C# 関数に渡されます。 引数名は `function.json` ファイルで指定され、関数のロガーやキャンセル トークンなどにアクセスするための定義済みの名前があります。

*.csx* の形式では、"定型" の記述が少なく、C# 関数のみの記述に重点が置かれています。 名前空間およびクラスにすべてをラップするのではなく、`Run` メソッドを定義するだけです。 通常どおり、すべてのアセンブリ参照と名前空間をファイルの先頭に含めます。

関数アプリの *.csx* ファイルは、インスタンスの初期化時にコンパイルされます。 このコンパイル手順は、C# クラス ライブラリと比較して C# スクリプト関数のコールド スタートに長い時間がかかることなどを意味します。 このコンパイル手順は、C# クラス ライブラリが編集可能でないのに対し、C# スクリプト関数が Azure Portal で編集可能である理由でもあります。

## <a name="binding-to-arguments"></a>引数へのバインド

入力または出力データは、*function.json* 構成ファイルの `name` プロパティを介して C# スクリプト関数パラメーターにバインドされます。 次の例は、キューによってトリガーされる関数の *function.json* ファイルと *run.csx* ファイルを示しています。 キュー メッセージからデータを受信するパラメーターの名前は `myQueueItem` です。これは `name` プロパティの値であるためです。

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem.AsString}");
}
```

`#r` ステートメントについては、[この記事の後半](#referencing-external-assemblies)で説明します。

## <a name="supported-types-for-bindings"></a>バインドでサポートされる型

各バインドには独自にサポートされている型があります。たとえば、BLOB トリガーは文字列パラメーター、POCO パラメーター、`CloudBlockBlob` パラメーター、またはサポートされるその他の複数の型のいずれかで使用できます。 [BLOB バインディングのバインド リファレンスに関する記事](functions-bindings-storage-blob.md#trigger---usage)では、BLOB トリガーでサポートされているすべてのパラメーター型の一覧を示しています。 詳しくは、[トリガーとバインド](functions-triggers-bindings.md)に関する記事と、[各バインドの種類に対応するバインドリファレンスの資料](functions-triggers-bindings.md#next-steps)をご覧ください。

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="referencing-custom-classes"></a>カスタム クラスの参照

カスタムの単純な従来の CLR オブジェクト (POCO) クラスを使用する必要がある場合は、クラス定義を同じファイルに含めることも、別のファイルに格納することもできます。

次の例は、POCO クラス定義を含む *run.csx* の例を示しています。

```csharp
public static void Run(string myBlob, out MyClass myQueueItem)
{
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
    myQueueItem = new MyClass() { Id = "myid" };
}

public class MyClass
{
    public string Id { get; set; }
}
```

POCO クラスでは、各プロパティにゲッターとセッターが定義されている必要があります。

## <a name="reusing-csx-code"></a>.csx コードの再利用

他の *.csx* ファイルで定義されたクラスとメソッドを、*run.csx* ファイルで使用できます。 そのためには、*run.csx* ファイル内で `#load` ディレクティブを使用します。 次の例では、`MyLogger` という名前のログ記録ルーチンが *myLogger.csx* 内で共有され、`#load` ディレクティブを使用して *run.csx* に読み込まれます。

*run.csx*の例:

```csharp
#load "mylogger.csx"

public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Verbose($"Log by run.csx: {DateTime.Now}");
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

*mylogger.csx*の例:

```csharp
public static void MyLogger(TraceWriter log, string logtext)
{
    log.Verbose(logtext);
}
```

共有された *.csx* ファイルの使用は、POCO オブジェクトを使用して関数間で渡されるデータを厳密に型宣言する場合の一般的なパターンです。 次の簡略化された例では、HTTP トリガーとキュー トリガーが `Order` という名前の POCO オブジェクトを共有して、注文データを厳密に型宣言しています。

例: HTTP トリガーの *run.csx*

```cs
#load "..\shared\order.csx"

using System.Net;

public static async Task<HttpResponseMessage> Run(Order req, IAsyncCollector<Order> outputQueueItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function received an order.");
    log.Info(req.ToString());
    log.Info("Submitting to processing queue.");

    if (req.orderId == null)
    {
        return new HttpResponseMessage(HttpStatusCode.BadRequest);
    }
    else
    {
        await outputQueueItem.AddAsync(req);
        return new HttpResponseMessage(HttpStatusCode.OK);
    }
}
```

例: キュー トリガーの *run.csx*

```cs
#load "..\shared\order.csx"

using System;

public static void Run(Order myQueueItem, out Order outputQueueItem,TraceWriter log)
{
    log.Info($"C# Queue trigger function processed order...");
    log.Info(myQueueItem.ToString());

    outputQueueItem = myQueueItem;
}
```

例: *order.csx*

```cs
public class Order
{
    public string orderId {get; set; }
    public string custName {get; set;}
    public string custAddress {get; set;}
    public string custEmail {get; set;}
    public string cartId {get; set; }

    public override String ToString()
    {
        return "\n{\n\torderId : " + orderId +
                  "\n\tcustName : " + custName +             
                  "\n\tcustAddress : " + custAddress +             
                  "\n\tcustEmail : " + custEmail +             
                  "\n\tcartId : " + cartId + "\n}";             
    }
}
```

`#load` ディレクティブで相対パスを使用できます。

* `#load "mylogger.csx"` によって、関数フォルダーにあるファイルが読み込まれます。
* `#load "loadedfiles\mylogger.csx"` によって、関数フォルダー内のフォルダーにあるファイルが読み込まれます。
* `#load "..\shared\mylogger.csx"` によって、関数フォルダーと同じレベル ( *wwwroot*の直下) にあるフォルダーのファイルが読み込まれます。

`#load` ディレクティブは、*.csx* ファイルでのみ機能し、*.cs* ファイルでは機能しません。

## <a name="binding-to-method-return-value"></a>メソッドの戻り値へのバインド

*function.json* 内の名前 `$return` を使用して、出力バインディングにメソッド戻り値を使用できます。 例については、[トリガーとバインディング](functions-triggers-bindings.md#using-the-function-return-value)に関するページを参照してください。

正常な関数の実行によって、常に戻り値が出力バインドに渡される場合のみ、戻り値を使用してください。 それ以外の場合は、次のセクションに示すように `ICollector` または `IAsyncCollector` を使用してください。

## <a name="writing-multiple-output-values"></a>複数の出力値の書き込み

1 つの出力バインドに複数の値を書き込むため、または正常な関数の呼び出しによって出力バインドに渡される値がない場合、[`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) または [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) 型を使用してください。 これらの型は、メソッド完了時に出力バインドに書き込まれる、書き込み専用接続です。

この例では、`ICollector` を使用して複数のキュー メッセージを同じキューに書き込みます。

```csharp
public static void Run(ICollector<string> myQueue, TraceWriter log)
{
    myQueue.Add("Hello");
    myQueue.Add("World!");
}
```

## <a name="logging"></a>ログの記録

出力を C# のストリーミング ログにログ記録するために、`TraceWriter` 型の引数を含めます。 これの名前を `log`にすることをお勧めします。 Azure Functions では `Console.Write` を使用しないでください。 

`TraceWriter` は [Azure Web ジョブ SDK](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/TraceWriter.cs) で定義されます。 `TraceWriter` のログ レベルは、[host.json](functions-host-json.md) で構成できます。

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

> [!NOTE]
> `TraceWriter` の代わりに使用できる新しいログ記録フレームワークについては、**Azure Functions の監視**に関する記事にある、[C# 関数でのログの書き込み](functions-monitoring.md#write-logs-in-c-functions)についての説明をご覧ください。

## <a name="async"></a>非同期

関数を[非同期](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/)にするには、`async` キーワードを使用して `Task` オブジェクトを返します。

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName,
        Stream blobInput,
        Stream blobOutput)
{
    await blobInput.CopyToAsync(blobOutput, 4096);
}
```

非同期関数では `out` パラメーターを使用できません。 出力バインドには、代わりに[関数の戻り値](#binding-to-method-return-value)または[コレクター オブジェクト](#writing-multiple-output-values)を使用します。

## <a name="cancellation-tokens"></a>キャンセル トークン

関数は [CancellationToken](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) パラメーターを受け付けることができます。これにより、オペレーティング システムは、その関数をいつ終了しようとしているかをコードに通知できます。 この通知を使用すれば、関数が予期せず終了してデータが不整合な状態になることを防止できます。

次の例は、関数の終了が迫っているかどうかを確認する方法を示しています。

```csharp
using System;
using System.IO;
using System.Threading;

public static void Run(
    string inputText,
    TextWriter logger,
    CancellationToken token)
{
    for (int i = 0; i < 100; i++)
    {
        if (token.IsCancellationRequested)
        {
            logger.WriteLine("Function was cancelled at iteration {0}", i);
            break;
        }
        Thread.Sleep(5000);
        logger.WriteLine("Normal processing for queue message={0}", inputText);
    }
}
```

## <a name="importing-namespaces"></a>名前空間のインポート

名前空間をインポートする必要がある場合は、 `using` 句を使用して、通常どおりにインポートできます。

```csharp
using System.Net;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

次の名前空間は自動的にインポートされるため、オプションとなります。

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`

## <a name="referencing-external-assemblies"></a>外部アセンブリの参照

フレームワークのアセンブリには、 `#r "AssemblyName"` ディレクティブを使用して参照を追加します。

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

次のアセンブリは、Azure Functions をホストしている環境によって自動的に追加されます。

* `mscorlib`
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`

次のアセンブリは、単純な名前で参照できます (たとえば、`#r "AssemblyName"`)。

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNet.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

## <a name="referencing-custom-assemblies"></a>カスタム アセンブリの参照

カスタム アセンブリを参照するために、*共有*アセンブリまたは*プライベート* アセンブリのいずれかを使用できます。
- 共有アセンブリは、関数アプリ内のすべての関数にわたって共有されます。 カスタム アセンブリを参照するには、そのアセンブリをご自分の[関数アプリのルート フォルダー](functions-reference.md#folder-structure) (wwwroot) 内の `bin` という名前のフォルダーにアップロードします。 
- プライベート アセンブリは、特定の関数のコンテキストの一部であり、異なるバージョンのサイドローディングをサポートします。 プライベート アセンブリを関数ディレクトリ の `bin` フォルダーにアップロードする必要があります。 `#r "MyAssembly.dll"` などのファイル名を使用してアセンブリを参照します。 

関数フォルダーにファイルをアップロードする方法については、[パッケージ管理](#using-nuget-packages)に関するセクションをご覧ください。

### <a name="watched-directories"></a>監視対象のディレクトリ

関数のスクリプト ファイルを含むディレクトリは、アセンブリの変更を自動的に監視されています。 その他のディレクトリでアセンブリの変更を監視するには、[host.json](functions-host-json.md) の `watchDirectories` の一覧にそのディレクトリを追加します。

## <a name="using-nuget-packages"></a>NuGet パッケージを使用する

NuGet パッケージを C# 関数で使用するには、*project.json* ファイルを関数アプリのファイル システムにある関数のフォルダーにアップロードします。 Microsoft.ProjectOxford.Face バージョン 1.1.0 への参照を追加する *project.json* ファイルの例を次に示します。

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

Azure Functions 1.x では .NET Framework 4.6 のみがサポートされているので、次に示すように *project.json* ファイルが `net46` を指定していることを確認します。

*project.json* ファイルをアップロードすると、ランタイムによってパッケージが取得され、パッケージ アセンブリに参照が自動的に追加されます。 `#r "AssemblyName"` ディレクティブを追加する必要はありません。 NuGet パッケージで定義されている型を使用するには、必要な `using` ステートメントを *run.csx* ファイルに追加するだけです。 

Functions ランタイムでは、NuGet は `project.json` と `project.lock.json` を比較して作業を復元します。 ファイルの日付と時刻のタイムスタンプが**一致しない**場合は、NuGet 復元が実行され、NuGet は更新されたパッケージをダウンロードします。 ただし、ファイルの日付と時刻のタイムスタンプが**一致した**場合、NuGet は復元を実行しません。 したがって、NuGet がパッケージの復元をスキップするため、`project.lock.json` はデプロイされません。 ロック ファイルのデプロイを回避するには、`project.lock.json` を `.gitignore` ファイルに追加します。

カスタムの NuGet フィードを使用するには、関数アプリのルートにある *Nuget.Config* ファイルでフィードを指定します。 詳しくは、「[NuGet の動作の構成](/nuget/consume-packages/configuring-nuget-behavior)」をご覧ください。

### <a name="using-a-projectjson-file"></a>Project.json ファイルの使用

1. Azure ポータルで関数を開きます。 [ログ] タブには、パッケージのインストールの出力が表示されます。
2. project.json ファイルをアップロードするには、「Azure Functions developer reference (Azure Functions 開発者向けリファレンス)」の「[関数アプリ ファイルを更新する方法](functions-reference.md#fileupdate)」にあるいずれかの方法を利用してください。
3. *project.json* ファイルがアップロードされた後、関数のストリーミング ログの出力は次の例のようになります。

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189
2016-04-04T19:02:57.189
2016-04-04T19:02:57.455 Packages restored.
```

## <a name="environment-variables"></a>環境変数

環境変数またはアプリ設定値を取得するには、次のコード例のように、 `System.Environment.GetEnvironmentVariable`を使用します。

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    log.Info(GetEnvironmentVariable("AzureWebJobsStorage"));
    log.Info(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
}

public static string GetEnvironmentVariable(string name)
{
    return name + ": " +
        System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
}
```

[System.Configuration.ConfigurationManager.AppSettings](https://docs.microsoft.com/dotnet/api/system.configuration.configurationmanager.appsettings) プロパティは、アプリ設定値を取得するための代替 API ですが、次に示すように `GetEnvironmentVariable` を使用することをお勧めします。

<a name="imperative-bindings"></a> 

## <a name="binding-at-runtime"></a>実行時のバインド

C# および他の .NET 言語では、*function.json* の[*宣言型*](https://en.wikipedia.org/wiki/Declarative_programming)のバインドではなく[命令型](https://en.wikipedia.org/wiki/Imperative_programming)のバインド パターンを使用できます。 命令型のバインドは、設計時ではなくランタイム時にバインド パラメーターを計算する必要がある場合に便利です。 このパターンを使用すると、サポートされている入力バインドと出力バインドに関数コード内でバインドできます。

次のように命令型のバインドを定義します。

- 必要な命令型のバインドの *function.json* にエントリを**含めないで**ください。
- 入力パラメーター [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) または [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs) を渡します。
- 次の C# パターンを使用してデータ バインドを実行します。

```cs
using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
{
    ...
}
```

`BindingTypeAttribute` はバインドを定義する .NET 属性、`T` はそのバインドの種類でサポートされている入力または出力の型です。 `T` を `out` パラメーター型 (`out JObject` など) にすることはできません。 たとえば、Mobile Apps テーブルの出力バインドは [6 種類の出力](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22)をサポートしますが、`T` に使用できるのは [ICollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) または [IAsyncCollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) のみです。

### <a name="single-attribute-example"></a>単一属性の例

次のコード例は、実行時に BLOB パスが定義された [Storage Blob の出力バインド](functions-bindings-storage-blob.md#output)を作成し、この BLOB に文字列を書き込みます。

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    using (var writer = await binder.BindAsync<TextWriter>(new BlobAttribute("samples-output/path")))
    {
        writer.Write("Hello World!!");
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) は [Storage Blob](functions-bindings-storage-blob.md) の入力バインドまたは出力バインドを定義します。[TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) はサポートされている出力バインドの種類です。

### <a name="multiple-attribute-example"></a>複数属性の例

前の例では、関数アプリのメイン ストレージ アカウント接続文字列 (`AzureWebJobsStorage`) のアプリ設定を取得します。 ストレージ アカウントに使用するカスタム アプリ設定を指定するには、[StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) を追加し、属性の配列を `BindAsync<T>()` に渡します。 `IBinder`ではなく、`Binder` パラメーターを使用します。  例: 

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    var attributes = new Attribute[]
    {    
        new BlobAttribute("samples-output/path"),
        new StorageAccountAttribute("MyStorageAccount")
    };

    using (var writer = await binder.BindAsync<TextWriter>(attributes))
    {
        writer.Write("Hello World!");
    }
}
```

次の表に、各バインドの種類の .NET 属性と、それらが定義されているパッケージを示します。

> [!div class="mx-codeBreakAll"]
| バインド | Attribute | 参照の追加 |
|------|------|------|
| Cosmos DB | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.CosmosDB"` |
| Event Hubs | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs)、[`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
| Mobile Apps | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
| Notification Hubs | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
| Service Bus | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs)、[`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
| ストレージ キュー | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs)、[`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Storage Blob | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs)、[`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| ストレージ テーブル | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs)、[`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.Twilio"` |

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [トリガーとバインドの詳細を確認する](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Azure Functions のベスト プラクティスを確認する](functions-best-practices.md)
