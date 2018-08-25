---
title: .NET Standard ライブラリを使用して Azure Event Hubs からイベントを受信する | Microsoft Docs
description: .NET standard で EventProcessorHost を使用したメッセージ受信を開始する
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: 03acd63ff00f0a3017297d1998289c8e68f0f290
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2018
ms.locfileid: "41917631"
---
# <a name="get-started-receiving-messages-with-the-event-processor-host-in-net-standard"></a>.NET Standard で EventProcessorHost を使用したメッセージ受信を開始する

> [!NOTE]
> このサンプルは [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) で入手できます。

このチュートリアルでは、**イベント プロセッサ ホスト** ライブラリを使ってイベント ハブからメッセージを受信する .NET Core コンソール アプリケーションを作成する方法を説明します。 [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) ソリューションをそのまま実行するには、文字列をイベント ハブとストレージ アカウントの値に置き換えます。 このチュートリアルの手順に従って独自のものを作成します。

## <a name="prerequisites"></a>前提条件

* [Microsoft Visual Studio 2015 または 2017](http://www.visualstudio.com)。 このチュートリアルの例では Visual Studio 2015 を使用しますが、Visual Studio 2017 もサポートされています。
* [.NET Core Visual Studio 2015 または 2017 ツール](http://www.microsoft.com/net/core)。
* Azure サブスクリプション。
* Azure Event Hubs 名前空間とイベント ハブ。
* Azure ストレージ アカウント。

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Event Hubs 名前空間とイベント ハブを作成する  

最初のステップでは、[Azure Portal](https://portal.azure.com) を使用して イベント ハブ型の名前空間を作成し、アプリケーションがイベント ハブと通信するために必要な管理資格情報を取得します。 名前空間とイベント ハブを作成するには、[こちらの記事](event-hubs-create.md)の手順を済ませた後、このチュートリアルに進みます。  

## <a name="create-an-azure-storage-account"></a>Azure Storage アカウントの作成  

1. [Azure Portal](https://portal.azure.com) にサインインします。  
2. ポータルの左側のナビゲーション ウィンドウで **[リソースの作成]** をクリックし、**[ストレージ]**、**[ストレージ アカウント]** の順にクリックします。  
3. ストレージ アカウント ウィンドウのフィールドを入力して、**[作成]** をクリックします。

    ![[ストレージ アカウントの作成]][1]

4. **[デプロイメントが成功しました]** メッセージが表示されたら、新しいストレージ アカウント名をクリックし、 **[要点]** ウィンドウで **[BLOB]** をクリックします。 **[Blob service]** ダイアログ ボックスが開いたら、上部にある **[+ コンテナー]** をクリックします。 コンテナーの名前を指定して、**[Blob service]** を閉じます。  
5. 左側のウィンドウで **[アクセス キー]** をクリックし、ストレージ コンテナーとストレージ アカウントの名前、および **[key1]** の値をコピーします。 これらの値をメモ帳などに一時的に保存します。  

## <a name="create-a-console-application"></a>コンソール アプリケーションの作成

Visual Studio を起動します。 **[ファイル]** メニューの **[新規作成]** をクリックし、**[プロジェクト]** をクリックします。 .NET Core コンソール アプリケーションを作成します。

![新しいプロジェクト][2]

## <a name="add-the-event-hubs-nuget-package"></a>Event Hubs NuGet パッケージの追加

次の手順に従って、.NET Standard ライブラリ NuGet パッケージの [**Microsoft.Azure.EventHubs**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) と [**Microsoft.Azure.EventHubs.Processor**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) をプロジェクトに追加します。 

1. 新しく作成したプロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します。
2. **[参照]** タブをクリックし、**Microsoft.Azure.EventHubs** を探して、**Microsoft.Azure.EventHubs** パッケージを選びます。 **[インストール]** をクリックし、インストールが完了したら、このダイアログ ボックスを閉じます。
3. 手順 1 と 2 を繰り返し、**Microsoft.Azure.EventHubs.Processor** パッケージをインストールします。

## <a name="implement-the-ieventprocessor-interface"></a>IEventProcessor インターフェイスの実装

1. ソリューション エクスプローラーで、プロジェクトを右クリックし、**[追加]**、**[クラス]** の順にクリックします。 新しいクラスに **SimpleEventProcessor** の名前を付けます。

2. SimpleEventProcessor.cs ファイルを開き、ファイルの先頭に `using` ステートメントを追加します。

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

3. `IEventProcessor` インターフェイスを実装します。 `SimpleEventProcessor` クラスの内容全体を次のコードに置き換えます。

    ```csharp
    public class SimpleEventProcessor : IEventProcessor
    {
        public Task CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
            return Task.CompletedTask;
        }

        public Task OpenAsync(PartitionContext context)
        {
            Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
            return Task.CompletedTask;
        }

        public Task ProcessErrorAsync(PartitionContext context, Exception error)
        {
            Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
            return Task.CompletedTask;
        }

        public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (var eventData in messages)
            {
                var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
                Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
            }

            return context.CheckpointAsync();
        }
    }
    ```

## <a name="write-a-main-console-method-that-uses-the-simpleeventprocessor-class-to-receive-messages"></a>SimpleEventProcessor クラスを使用するメイン コンソール メソッドを記述してメッセージを受信

1. Program.cs ファイルの先頭に次の `using` ステートメントを追加します。

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

2. イベント ハブ接続文字列、イベント ハブ名、ストレージ アカウント コンテナー名、ストレージ アカウント名、およびストレージ アカウント キーの `Program` クラスに定数を追加します。 プレースホルダーを対応する値に置き換えて、次のコードを追加します。

    ```csharp
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    private const string StorageContainerName = "{Storage account container name}";
    private const string StorageAccountName = "{Storage account name}";
    private const string StorageAccountKey = "{Storage account key}";

    private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    ```   

3. 次のように、`Program` クラスに `MainAsync` という名前の新しいメソッドを追加します。

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        Console.WriteLine("Registering EventProcessor...");

        var eventProcessorHost = new EventProcessorHost(
            EventHubName,
            PartitionReceiver.DefaultConsumerGroupName,
            EventHubConnectionString,
            StorageConnectionString,
            StorageContainerName);

        // Registers the Event Processor Host and starts receiving messages
        await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

        Console.WriteLine("Receiving. Press ENTER to stop worker.");
        Console.ReadLine();

        // Disposes of the Event Processor Host
        await eventProcessorHost.UnregisterEventProcessorAsync();
    }
    ```

3. `Main` メソッドに次のコード行を追加します。

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

    Program.cs ファイルは次のようになります。

    ```csharp
    namespace SampleEphReceiver
    {

        public class Program
        {
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";
            private const string StorageContainerName = "{Storage account container name}";
            private const string StorageAccountName = "{Storage account name}";
            private const string StorageAccountKey = "{Storage account key}";

            private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                Console.WriteLine("Registering EventProcessor...");

                var eventProcessorHost = new EventProcessorHost(
                    EventHubName,
                    PartitionReceiver.DefaultConsumerGroupName,
                    EventHubConnectionString,
                    StorageConnectionString,
                    StorageContainerName);

                // Registers the Event Processor Host and starts receiving messages
                await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

                Console.WriteLine("Receiving. Press ENTER to stop worker.");
                Console.ReadLine();

                // Disposes of the Event Processor Host
                await eventProcessorHost.UnregisterEventProcessorAsync();
            }
        }
    }
    ```

4. プログラムを実行し、エラーがないことを確認します。

お疲れさまでした。 イベント プロセッサ ホストを使用して、イベント ハブからメッセージを受信しました。

## <a name="next-steps"></a>次の手順
Event Hubs の詳細については、次のリンク先を参照してください:

* [Event Hubs の概要](event-hubs-what-is-event-hubs.md)
* [イベント ハブの作成](event-hubs-create.md)
* [Event Hubs の FAQ](event-hubs-faq.md)

[1]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/event-hubs-python1.png
[2]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcorercv.png
