---
title: Azure IoT Hub (.NET) を使用したクラウドからデバイスへのメッセージ | Microsoft Docs
description: Azure IoT SDK for .NET を使用して、クラウドからデバイスへのメッセージを Azure IoT Hub からデバイスへ送信する方法。 クラウドからデバイスへのメッセージを受信するためにデバイス アプリを変更し、クラウドからデバイスへのメッセージを送信するためにバックエンド アプリを変更します。
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/24/2017
ms.author: elioda
ms.openlocfilehash: e2090977813f5335f2170ff543bde19cd8bf123b
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185796"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>デバイスに IoT Hub でクラウドからメッセージを送信する (.NET)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>はじめに
Azure IoT Hub は、何百万ものデバイスとソリューション バックエンドの間に信頼性のある保護された双方向通信を確立するのに役立つ、フル マネージドのサービスです。 [IoT Hub の概要]チュートリアルには、IoT Hub の作成方法、IoT Hub でデバイス ID をプロビジョニングする方法、およびデバイスからクラウドへのメッセージを送信するデバイス アプリをコード化する方法が示されています。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

このチュートリアルは、[IoT Hub の概要]に関するページのチュートリアルに基づいて作成されており、 次の方法について説明します。

* ソリューション バックエンドから IoT Hub を介して単一のデバイスにクラウドからデバイスへのメッセージを送信する。
* クラウドからデバイスへのメッセージをデバイスで受信する。
* IoT Hub からデバイスに送信されたメッセージの配信確認 ("*フィードバック*") を、ソリューション バックエンドから要求する。

クラウドからデバイスへのメッセージの詳細については、[IoT Hub 開発者ガイド][IoT Hub developer guide - C2D]を参照してください。

このチュートリアルの最後に、次の 2 つの .NET コンソール アプリを実行します。

* **SimulatedDevice**。「[IoT Hub の概要]」で作成されたアプリケーションの修正バージョン。これは、IoT Hub に接続し、クラウドからデバイスへのメッセージを受け取ります。
* **SendCloudToDevice**。クラウドからデバイスへのメッセージを IoT Hub を介してデバイス アプリに送信し、その配信確認を受け取ります。

> [!NOTE]
> IoT Hub には、[Azure IoT device SDK] を介した多数のデバイス プラットフォームや言語 (C、Java、Javascript など) に対する SDK サポートがあります。 このチュートリアルのコード (一般的には Azure IoT Hub) にデバイスを接続するための詳しい手順については、[IoT Hub 開発者ガイド]を参照してください。
> 
> 

このチュートリアルを完了するには、以下が必要です。

* Visual Studio 2015 または Visual Studio 2017
* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 (アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成できます)。

## <a name="receive-messages-in-the-device-app"></a>デバイス アプリでメッセージを受信する
このセクションでは、[IoT Hub の概要]に関するページで作成したデバイス アプリを、クラウドからデバイスへのメッセージを IoT Hub から受信するように変更します。

1. Visual Studio の **SimulatedDevice** プロジェクトで、次のメソッドを **Program** クラスに追加します。
   
        private static async void ReceiveC2dAsync()
        {
            Console.WriteLine("\nReceiving cloud to device messages from service");
            while (true)
            {
                Message receivedMessage = await deviceClient.ReceiveAsync();
                if (receivedMessage == null) continue;
   
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received message: {0}", Encoding.ASCII.GetString(receivedMessage.GetBytes()));
                Console.ResetColor();
   
                await deviceClient.CompleteAsync(receivedMessage);
            }
        }
   
    `ReceiveAsync` メソッドは、受信したメッセージを、そのメッセージがデバイスによって受信されたとき非同期で返します。 指定可能なタイムアウト期間が経過したら、*null* が返されます (ここでは、既定の 1 分が使用されます)。 アプリは、*null* を受信したら、新しいメッセージを受信するために待機を続ける必要があります。 `if (receivedMessage == null) continue` 行があるのは、この要件があるためです。
   
    `CompleteAsync()` への呼び出しにより、メッセージが正常に処理されたことが IoT Hub に通知されます。 メッセージはデバイスのキューから安全に削除することができます。 デバイスのアプリケーションがメッセージの処理を完了できなくなる何らかの事態が生じると、IoT Hub はそれをもう一度送信します。 そのため、同じメッセージを複数回受信した場合に生成される結果が毎回同じになるように、デバイス アプリ内のメッセージ処理ロジックを*べき等*にすることが重要です。 アプリケーションはメッセージを一時的に破棄することもできます。この場合、IoT Hub は将来の使用に備えてメッセージをキュー内に保持します。 または、メッセージを拒否することもできます。この場合、メッセージはキューから永久に削除されます。 クラウドからデバイスへのメッセージのライフサイクルの詳細については、「[Azure IoT Hub 開発者ガイド][IoT Hub developer guide - C2D]」を参照してください。
   
   > [!NOTE]
   > トランスポートとして MQTT や AMQP ではなく HTTPS を使用している場合、`ReceiveAsync` メソッドは即時に返されます。 HTTPS を使用するクラウドからデバイスへのメッセージに関してサポートされているパターンは、メッセージを低頻度 (25 分未満の間隔) でチェックするデバイスに断続的に接続されます。 HTTPS 受信の発行が多くなれば、IoT Hub で要求が調整されます。 MQTT、AMQP、および HTTPS のサポートの相違点と、IoT Hub スロットルの詳細については、[IoT Hub 開発者ガイド][IoT Hub developer guide - C2D]を参照してください。
   > 
   > 
2. **Main** メソッドの `Console.ReadLine()` 行の直前に次のメソッドを追加します。
   
        ReceiveC2dAsync();

> [!NOTE]
> わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用環境のコードでは、MSDN の記事「 [Transient Fault Handling (一時的な障害の処理)]」で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。
> 
> 

## <a name="send-a-cloud-to-device-message"></a>C2D メッセージを送信する
このセクションでは、デバイスからクラウドへのメッセージを、デバイス アプリに送信する .NET コンソール アプリを作成します。

1. 現在の Visual Studio ソリューションで、**コンソール アプリケーション** プロジェクト テンプレートを使用して、Visual C# デスクトップ アプリ プロジェクトを作成します。 プロジェクトに **SendCloudToDevice**という名前を付けます。
   
    ![Visual Studio での新しいプロジェクト][20]
2. ソリューション エクスプローラーでソリューションを右クリックし、 **[ソリューション用 NuGet パッケージの管理]** をクリックします。 
   
    この操作によって、**[NuGet パッケージの管理]** ウィンドウが開きます。
3. **Microsoft.Azure.Devices** を検索し、**[インストール]** をクリックして使用条件に同意します。 
   
    これによりパッケージのダウンロードとインストールが実行され、[Azure IoT サービス SDK NuGet パッケージ]への参照が追加されます。

4. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。
   
        using Microsoft.Azure.Devices;
5. **Program** クラスに次のフィールドを追加します。 プレースホルダーの値を「[IoT Hub の概要]」で取得した IoT hub の接続文字列に置き換えます。
   
        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
6. **Program** クラスに次のメソッドを追加します。
   
        private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message(Encoding.ASCII.GetBytes("Cloud to device message."));
            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }
   
    このメソッドは、クラウドからデバイスへの新しいメッセージを ID `myFirstDevice`を持つデバイスに送信します。 [IoT Hub の概要]に関するページで使用したパラメーターに変更を加えた場合にのみ、このパラメーターを変更します。
7. 最後に、**Main** メソッドに次の行を追加します。
   
        Console.WriteLine("Send Cloud-to-Device message\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
   
        Console.WriteLine("Press any key to send a C2D message.");
        Console.ReadLine();
        SendCloudToDeviceMessageAsync().Wait();
        Console.ReadLine();
8. Visual Studio 内でソリューションを右クリックし、 **[スタートアップ プロジェクトの設定]** を選択します。**[マルチ スタートアップ プロジェクト]** を選択してから、**ReadDeviceToCloudMessages**、**SimulatedDevice**、**SendCloudToDevice** の **[開始]** アクションを選択します。
9. **F5**キーを押します。 3 つのすべてのアプリケーションが開始されます。 **[SendCloudToDevice]** ウィンドウを選択して **Enter** キーを押します。 デバイス アプリによってメッセージが受信されていることがわかります。
   
   ![アプリによるメッセージの受信][21]

## <a name="receive-delivery-feedback"></a>配信フィードバックの受信
クラウドからデバイスへの各メッセージに対して IoT Hub からの配信 (または有効期限) 確認を要求できます。 このオプションによって、ソリューション バックエンドで再試行または補正ロジックを簡単に通知できるようになります。 クラウドからデバイスへのメッセージに対するフィードバックの詳細については、「[Azure IoT Hub 開発者ガイド][IoT Hub developer guide - C2D]」を参照してください。

このセクションでは、フィードバックを要求し、それを IoT Hub から受信するように、**SendCloudToDevice** アプリを変更します。

1. Visual Studio の **SendCloudToDevice** プロジェクトで、次のメソッドを **Program** クラスに追加します。
   
        private async static void ReceiveFeedbackAsync()
        {
            var feedbackReceiver = serviceClient.GetFeedbackReceiver();
   
            Console.WriteLine("\nReceiving c2d feedback from service");
            while (true)
            {
                var feedbackBatch = await feedbackReceiver.ReceiveAsync();
                if (feedbackBatch == null) continue;
   
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received feedback: {0}", string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
                Console.ResetColor();
   
                await feedbackReceiver.CompleteAsync(feedbackBatch);
            }
        }
   
    この受信パターンは、クラウドからデバイスへのメッセージをデバイス アプリから受信するために使用するものと同じであることに注意してください。
2. **Main** メソッドの `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` 行の直後に次のメソッドを追加します。
   
        ReceiveFeedbackAsync();
3. クラウドからデバイスへのメッセージの配信に対するフィードバックを要求するには、**SendCloudToDeviceMessageAsync** メソッドでプロパティを指定する必要があります。 `var commandMessage = new Message(...);` 行の直後に次の行を追加します。
   
        commandMessage.Ack = DeliveryAcknowledgement.Full;
4. **F5**キーを押してアプリを実行します。 3 つすべてのアプリケーションが開始されていることが確認できます。 **[SendCloudToDevice]** ウィンドウを選択して **Enter** キーを押します。 デバイス アプリがメッセージを受信し、その数秒後に、**SendCloudToDevice** アプリケーションがフィードバック メッセージを受信します。
   
   ![アプリによるメッセージの受信][22]

> [!NOTE]
> わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用環境のコードでは、MSDN の記事「 [Transient Fault Handling (一時的な障害の処理)]」で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。
> 
> 

## <a name="next-steps"></a>次の手順
このチュートリアルでは、クラウドからデバイスへのメッセージを送受信する方法を学習しました。 

IoT Hub を使用する完全なエンド ツー エンド ソリューションの例については、[Azure IoT リモート監視ソリューション アクセラレータ]に関するページをご覧ください。

IoT Hub を使用したソリューションの開発に関する詳細については、[IoT Hub 開発者ガイド]をご覧ください。

<!-- Images -->
[20]: ./media/iot-hub-csharp-csharp-c2d/create-identity-csharp1.png
[21]: ./media/iot-hub-csharp-csharp-c2d/sendc2d1.png
[22]: ./media/iot-hub-csharp-csharp-c2d/sendc2d2.png

<!-- Links -->

[Azure IoT サービス SDK NuGet パッケージ]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Transient Fault Handling (一時的な障害の処理)]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md

[IoT Hub 開発者ガイド]: iot-hub-devguide.md
[IoT Hub の概要]: quickstart-send-telemetry-dotnet.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure IoT リモート監視ソリューション アクセラレータ]: https://docs.microsoft.com/azure/iot-suite/
[Azure IoT device SDK]: iot-hub-devguide-sdks.md