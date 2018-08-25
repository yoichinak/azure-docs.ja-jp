---
title: Azure IoT Hub モジュール ID とモジュール ツイン (.NET) の概要 | Microsoft Docs
description: モジュール ID を作成し、IoT SDK を使用して .NET のモジュール ツインを更新する方法を説明します。
author: chrissie926
manager: ''
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: 8d5d4ab85d8441998fd384e01f85d1d427d68cc2
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2018
ms.locfileid: "42146236"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-net-back-end-and-net-device"></a>.NET バックエンドおよび .NET デバイスを使用した Azure IoT Hub モジュール ID とモジュール ツインの概要

> [!NOTE]
> [モジュール ID とモジュール ツイン](iot-hub-devguide-module-twins.md)は Azure IoT Hub のデバイス ID とデバイス ツインに類似していますが、より細かい粒度で設定できます。 Azure IoT Hub のデバイス ID とデバイス ツインを使用した場合、バックエンド アプリケーションからデバイスを構成し、デバイスの状態を可視化できるのに対し、モジュール ID とモジュール ツインでは、デバイスの各コンポーネントごとにこれらの機能を実現できます。 複数のコンポーネントで構成され、この機能をサポートしているデバイス (オペレーティング システム ベースのデバイスやファームウェア デバイスなど) であれば、各コンポーネントの状態を可視化し、個別に構成することができます。

このチュートリアルの最後には、次の 2 つの .NET コンソール アプリが完成します。

* **CreateIdentities** は、デバイスの ID、モジュール ID と、関連づけられたセキュリティ キーを作成してデバイスおよびモジュール クライアントと接続します。
* **UpdateModuleTwinReportedProperties** は、更新されたモジュール ツインによって報告されたプロパティを IoT Hub に送信します。

> [!NOTE]
> デバイス上で動作するアプリケーションの作成とソリューションのバックエンドで動作するアプリケーションの開発に利用できる各種 Azure IoT SDK については、「[Azure IoT SDK][lnk-hub-sdks]」を参照してください。

このチュートリアルを完了するには、以下が必要です。

* Visual Studio 2015 または Visual Studio 2017。
* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 (アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成できます)。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

IoT Hub の作成は以上です。以降の作業に必要なホスト名と IoT Hub 接続文字列が得られました。

[!INCLUDE [iot-hub-get-started-create-module-identity-csharp](../../includes/iot-hub-get-started-create-module-identity-csharp.md)]


## <a name="update-the-module-twin-using-net-device-sdk"></a>.NET デバイス SDK を使用してモジュール ツインを更新する

このセクションでは、モジュール ツインによって報告されるプロパティを更新する、シミュレートされたデバイス上に.NET を作成するコンソール アプリを作成します。

1. **Visual Studio プロジェクトを作成する** - Visual Studio で、**コンソール アプリケーション (.NET Framework)** プロジェクト テンプレートを使用して、既存のソリューションに Visual C# Windows Classic Desktop プロジェクトを追加します。 .NET Framework のバージョンが 4.6.1 以降であることを確認してください。 プロジェクトの名前を **UpdateModuleTwinReportedProperties** に設定します。

    ![Visual Studio プロジェクトを作成する][13]

2. **最新の Azure IoT Hub .NET デバイス SDK をインストールする** - 現在、モジュール ID とモジュール ツインはパブリック プレビュー段階です。 これらは、IoT Hub プレリリース デバイス版の SDK でのみ使えます。 Visual Studio で、[ツール] > [Nuget パッケージ マネージャー] > [ソリューションの Nuget パッケージの管理] の順に選択します。 Microsoft.Azure.Devices.Client を検索します。 [プレリリースを含める] チェック ボックスをオンにしてください。 最新のバージョンを選択し、インストールします。 これで、モジュールのすべての機能を使用できるようになりました。 

    ![Azure IoT Hub .NET service SDK V1.16.0-preview-005 をインストールする][14]

3. **モジュールの接続文字列を取得する** -- [Azure Portal][lnk-portal] にログインします。 IoT Hub に移動し、[IoT デバイス] をクリックします。 myFirstDevice を検索して開くと、myFirstModule が作成されていることを確認できます。 モジュールの接続文字列をコピーします。 これは、次の手順で必要になります。

    ![Azure Portal モジュールの詳細][15]

4. **UpdateModuleTwinReportedProperties コンソール アプリを作成する** - **Program.cs** ファイルの最上部に、次の `using` ステートメントを追加します。

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    ```

    **Program** クラスに次のフィールドを追加します。 プレースホルダーの値をモジュールの接続文字列に置き換えます。

    ```csharp
    private const string ModuleConnectionString = "<Your module connection string>";
    private static ModuleClient Client = null;
    static void ConnectionStatusChangeHandler(ConnectionStatus status, ConnectionStatusChangeReason reason)
    {
        Console.WriteLine("Connection Status Changed to {0}; the reason is {1}", status, reason);
    }
    ```

    次の **OnDesiredPropertyChanged** メソッドを **Program** クラスに追加します。

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
        {
            Console.WriteLine("desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
            Console.WriteLine("Sending current time as reported property");
            TwinCollection reportedProperties = new TwinCollection
            {
                ["DateTimeLastDesiredPropertyChangeReceived"] = DateTime.Now
            };

            await Client.UpdateReportedPropertiesAsync(reportedProperties).ConfigureAwait(false);
        }
    ```

    最後に、**Main** メソッドに次の行を追加します。

    ```csharp
    static void Main(string[] args)
    {
        Microsoft.Azure.Devices.Client.TransportType transport = Microsoft.Azure.Devices.Client.TransportType.Amqp;

        try
        {
            Client = ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
            Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
            Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

            Console.WriteLine("Retrieving twin");
            var twinTask = Client.GetTwinAsync();
            twinTask.Wait();
            var twin = twinTask.Result;
            Console.WriteLine(JsonConvert.SerializeObject(twin.Properties)); 

            Console.WriteLine("Sending app start time as reported property");
            TwinCollection reportedProperties = new TwinCollection();
            reportedProperties["DateTimeLastAppLaunch"] = DateTime.Now;

            Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (AggregateException ex)
        {
            Console.WriteLine("Error in sample: {0}", ex);
        }

        Console.WriteLine("Waiting for Events.  Press enter to exit...");
        Console.ReadLine();
        Client.CloseAsync().Wait();
    }
    ```

    このコード サンプルは、モジュール ツインを取得し、報告されたプロパティを AMQP プロトコルを使って更新する方法を示したものです。 パブリック プレビューでは、AMQP はモジュール ツインの操作用にのみサポートされています。

5. 上記の **Main** メソッドの他に、下記のコード ブロックを追加して、モジュールから IoT Hub にイベントを送信できます。
    ```csharp
    Byte[] bytes = new Byte[2];
    bytes[0] = 0;
    bytes[1] = 1;
    var sendEventsTask = Client.SendEventAsync(new Message(bytes));
    sendEventsTask.Wait();
    Console.WriteLine("Event sent to IoT Hub.");
    ```

## <a name="run-the-apps"></a>アプリの実行

これで、アプリを実行する準備が整いました。 Visual Studio のソリューション エクスプローラーでソリューションを右クリックし、 **[スタートアップ プロジェクトの設定]** をクリックします。 コンソール アプリの動作として、**[マルチ スタートアップ プロジェクト]** を選択し、**[起動する]** を選択します。 F5 キーを押してアプリを起動します。 

## <a name="next-steps"></a>次の手順

引き続き IoT Hub の使用方法を確認すると共に、他の IoT のシナリオについて調べるには、次のページを参照してください。

* [デバイス管理の概要][lnk-device-management]
* [IoT Edge の概要][lnk-iot-edge]


<!-- Images. -->
[13]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.JPG
[14]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png
[15]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/module-detail.JPG
<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
