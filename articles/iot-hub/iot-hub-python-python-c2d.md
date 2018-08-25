---
title: Azure IoT Hub を使用したクラウドからデバイスへのメッセージ (Python) | Microsoft Docs
description: Azure IoT SDK for Python を使って、クラウドからデバイスへのメッセージを Azure IoT Hub からデバイスへ送信する方法。 クラウドからデバイスへのメッセージを受信するためにシミュレートされたデバイス アプリを変更し、クラウドからデバイスへのメッセージを送信するためにバックエンド アプリを変更します。
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: kgremban
ms.openlocfilehash: 316a8cd9ebf58e06ba39ba18fa19ede4b6a62229
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187300"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>IoT Hub を使用したクラウドからデバイスへのメッセージの送信 (Python)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]


## <a name="introduction"></a>はじめに
Azure IoT Hub は、何百万ものデバイスとソリューション バックエンドの間に信頼性のある保護された双方向通信を確立するのに役立つ、フル マネージドのサービスです。 [IoT Hub の概要]チュートリアルには、IoT ハブの作成方法、IoT ハブでデバイス ID をプロビジョニングする方法、およびデバイスからクラウドへのメッセージを送信するシミュレートされたデバイス アプリをコード化する方法が示されています。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

このチュートリアルは、[IoT Hub の概要]に関するページのチュートリアルに基づいて作成されており、 次の方法について説明します。

* ソリューション バックエンドから IoT Hub を介して単一のデバイスにクラウドからデバイスへのメッセージを送信する。
* クラウドからデバイスへのメッセージをデバイスで受信する。
* IoT Hub からデバイスに送信されたメッセージの配信確認 ("*フィードバック*") を、ソリューション バックエンドから要求する。

クラウドからデバイスへのメッセージの詳細については、[IoT Hub 開発者ガイド][IoT Hub developer guide - C2D]を参照してください。

このチュートリアルの最後に、次の 2 つの Python コンソール アプリを実行します。

* **SimulatedDevice.py**。「[IoT Hub の概要]」で作成されたアプリケーションの修正バージョン。これは、IoT Hub に接続し、クラウドからデバイスへのメッセージを受け取ります。
* **SendCloudToDeviceMessage.py**。クラウドからデバイスへのメッセージを IoT Hub を介してシミュレートされたデバイス アプリに送信し、その配信確認を受け取ります。

> [!NOTE]
> IoT Hub には、Azure IoT device SDK を介した多数のデバイス プラットフォームや言語 (C、Java、Javascript など) に対する SDK サポートがあります。 このチュートリアルのコード (一般的には Azure IoT Hub) にデバイスを接続するための詳しい手順については、 [Azure IoT デベロッパー センター]のページを参照してください。
> 

このチュートリアルを完了するには、以下が必要です。

* [Python 2.x または 3.x][lnk-python-download]。 必ず、セットアップに必要な 32 ビットまたは 64 ビットのインストールを使用してください。 インストール中に求められた場合は、プラットフォーム固有の環境変数に Python を追加します。 Python 2.x を使用する場合は、[*pip* (Python パッケージ管理システム) をインストールまたはアップグレード][lnk-install-pip]することが必要な場合があります。
* Windows OS を使用している場合は、[Visual C++ 再頒布可能パッケージ][lnk-visual-c-redist]。これは、Python からネイティブ DLL を使用できるようにするためです。
* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 (アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成できます)。

> [!NOTE]
> `azure-iothub-service-client` と `azure-iothub-device-client` の *pip* パッケージは現在、Windows OS でのみ利用できます。 Linux/Mac OS については、[Python に必要な開発環境の準備][lnk-python-devbox]に関する記事で、Linux と Mac OS の各セクションを参照してください。
> 


## <a name="receive-messages-in-the-simulated-device-app"></a>シミュレートされたデバイス アプリでメッセージを受信する
このセクションでは、デバイスをシミュレートして、クラウドからデバイスへのメッセージを IoT Hub から受信する、Python コンソール アプリを作成します。

1. テキスト エディターを使って、**SimulatedDevice.py** ファイルを作成します。

1. **SimulatedDevice.py** ファイルの先頭に、次の `import` ステートメントと変数を追加します。
   
    ```python
    import time
    import sys
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubMessage, IoTHubMessageDispositionResult, IoTHubError

    RECEIVE_CONTEXT = 0
    WAIT_COUNT = 10
    RECEIVED_COUNT = 0
    RECEIVE_CALLBACKS = 0
    ```

1. 次のコードを **SimulatedDevice.py** ファイルに追加します。 "{deviceConnectionString}" プレースホルダーの値を、「[IoT Hub の概要]」チュートリアルで作成したデバイスのデバイス接続文字列に置き換えます。
   
    ```python
    # choose AMQP or AMQP_WS as transport protocol
    PROTOCOL = IoTHubTransportProvider.AMQP
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

1. 受信したメッセージをコンソールに出力するための次の関数を追加します。
   
    ```python
    def receive_message_callback(message, counter):
        global RECEIVE_CALLBACKS
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        print ( "Received Message [%d]:" % counter )
        print ( "    Data: <<<%s>>> & Size=%d" % (message_buffer[:size].decode('utf-8'), size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        counter += 1
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        return IoTHubMessageDispositionResult.ACCEPTED

    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        client.set_message_callback(receive_message_callback, RECEIVE_CONTEXT)

        return client

    def print_last_message_time(client):
        try:
            last_message = client.get_last_message_receive_time()
            print ( "Last Message: %s" % time.asctime(time.localtime(last_message)) )
            print ( "Actual time : %s" % time.asctime() )
        except IoTHubClientError as iothub_client_error:
            if iothub_client_error.args[0].result == IoTHubClientResult.INDEFINITE_TIME:
                print ( "No message received" )
            else:
                print ( iothub_client_error )
    ```

1. クライアントを初期化してクラウドからデバイスへのメッセージの受信を待機する次のコードを追加します。
   
    ```python
    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        client.set_message_callback(receive_message_callback, RECEIVE_CONTEXT)

        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            while True:
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    status = client.get_send_status()
                    print ( "Send status: %s" % status )
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

        print_last_message_time(client)
    ```

1. 次の main 関数を追加します。
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

1. **SimulatedDevice.py** ファイルを保存して閉じます。


## <a name="send-a-cloud-to-device-message"></a>C2D メッセージを送信する
このセクションでは、クラウドからデバイスへのメッセージをシミュレートされたデバイスのアプリに送信する Python コンソール アプリを作成します。 [IoT Hub の概要]のチュートリアルで追加したデバイスのデバイス ID が必要です。 また、ハブの IoT Hub 接続文字列も必要です ([Azure ポータル] で確認できます)。

1. テキスト エディターを使って、**SendCloudToDeviceMessage.py** ファイルを作成します。

1. **SendCloudToDeviceMessage.py** ファイルの先頭に、次の `import` ステートメントと変数を追加します。
   
    ```python
    import random
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubMessaging, IoTHubMessage, IoTHubError

    OPEN_CONTEXT = 0
    FEEDBACK_CONTEXT = 1
    MESSAGE_COUNT = 1
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

1. 次のコードを **SendCloudToDeviceMessage.py** ファイルに追加します。 "{IoTHubConnectionString}" プレースホルダーの値を、「[IoT Hub の概要]」チュートリアルで作成したハブの IoT Hub 接続文字列に置き換えます。 "{deviceId}" プレースホルダーを、「[IoT Hub の概要]」チュートリアルで追加したデバイスのデバイス ID に置き換えます。
   
    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

1. フィードバック メッセージをコンソールに出力するための次の関数を追加します。
   
    ```python
    def open_complete_callback(context):
        print ( 'open_complete_callback called with context: {0}'.format(context) )

    def send_complete_callback(context, messaging_result):
        context = 0
        print ( 'send_complete_callback called with context : {0}'.format(context) )
        print ( 'messagingResult : {0}'.format(messaging_result) )
    ```

1. デバイスにメッセージを送信し、クラウドからデバイスへのメッセージが配信されたことの確認応答がデバイスからあったときにフィードバック メッセージを処理するための次のコードを追加します。
   
    ```python
    def iothub_messaging_sample_run():
        try:
            iothub_messaging = IoTHubMessaging(CONNECTION_STRING)

            iothub_messaging.open(open_complete_callback, OPEN_CONTEXT)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))
                message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))

                # optional: assign ids
                message.message_id = "message_%d" % i
                message.correlation_id = "correlation_%d" % i
                # optional: assign properties
                prop_map = message.properties()
                prop_text = "PropMsg_%d" % i
                prop_map.add("Property", prop_text)

                iothub_messaging.send_async(DEVICE_ID, message, send_complete_callback, i)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

            iothub_messaging.close()

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubMessaging sample stopped" )
    ```

1. 次の main 関数を追加します。
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client Messaging Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_messaging_sample_run()
    ```

1. **SendCloudToDeviceMessage.py** ファイルを保存して閉じます。


## <a name="run-the-applications"></a>アプリケーションの実行
これで、アプリケーションを実行する準備が整いました。

1. コマンド プロンプトを開き、**Azure IoT Hub Device SDK for Python** をインストールします。

    ```
    pip install azure-iothub-device-client
    ```

1. コマンド プロンプトで次のコマンドを実行し、クラウドからデバイスへのメッセージを待機します。
   
    ```shell
    python SimulatedDevice.py 
    ```
   
    ![シミュレーション済みデバイス アプリを実行する][img-simulated-device]

1. 新しいコマンド プロンプトを開き、**Azure IoT Hub Service SDK for Python** をインストールします。

    ```
    pip install azure-iothub-service-client
    ```

1. コマンド プロンプトで次のコマンドを実行し、クラウドからデバイスへのメッセージを送信して、メッセージのフィードバックを待機します。
   
    ```shell
    python SendCloudToDeviceMessage.py 
    ```
   
    ![クラウドからデバイスへのコマンドを送信するアプリを実行する][img-send-command]
   
1. デバイスで受信したメッセージを確認します。

    ![受信したメッセージ][img-message-recieved]


## <a name="next-steps"></a>次の手順
このチュートリアルでは、クラウドからデバイスへのメッセージを送受信する方法を学習しました。 

IoT Hub を使用する完全なエンド ツー エンド ソリューションの例については、[Azure IoT リモート監視ソリューション アクセラレータ]に関するページをご覧ください。

IoT Hub を使用したソリューションの開発に関する詳細については、[IoT Hub 開発者ガイド]をご覧ください。

<!-- Images -->
[img-simulated-device]: media/iot-hub-python-python-c2d/simulated-device.png
[img-send-command]:  media/iot-hub-python-python-c2d/send-command.png
[img-message-recieved]: media/iot-hub-python-python-c2d/message-recieved.png

<!-- Links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-node-download]: https://nodejs.org/en/download/
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[IoT Hub の概要]: quickstart-send-telemetry-node.md
[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[IoT Hub 開発者ガイド]: iot-hub-devguide.md
[Azure IoT デベロッパー センター]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure ポータル]: https://portal.azure.com
[Azure IoT リモート監視ソリューション アクセラレータ]: https://azure.microsoft.com/documentation/suites/iot-suite/
