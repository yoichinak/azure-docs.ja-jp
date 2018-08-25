---
title: Azure IoT Hub を使用してジョブのスケジュールを設定する (Node) | Microsoft Docs
description: 複数のデバイスでダイレクト メソッドを呼び出す Azure IoT Hub ジョブをスケジュールする方法。 Azure IoT SDK for Node.js を使用して、シミュレートされたデバイス アプリと、ジョブを実行するサービス アプリを実装します。
author: juanjperez
manager: cberlin
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 10/06/2017
ms.author: juanpere
ms.openlocfilehash: 2161cd12f8bdb6aa3018d81a7864816a97ed122a
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187752"
---
# <a name="schedule-and-broadcast-jobs-node"></a>ジョブのスケジュールとブロードキャスト (Node)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub は、数百万台のデバイスをスケジュールおよび更新するジョブをバックエンド アプリで作成したり追跡したりできるようにするフル マネージドのサービスです。  ジョブは次のアクションに使用できます。

* 必要なプロパティを更新する
* タグを更新する
* ダイレクト メソッドを呼び出す

概念的には、ジョブはこれらのアクションのいずれかをラップし、デバイス ツイン クエリで定義される一連のデバイスに対して実行の進行状況を追跡します。  たとえば、バックエンド アプリでは、ジョブを使用して、10,000 台のデバイスに対して reboot メソッドを呼び出すことができます。これは、デバイス ツイン クエリで指定され、将来の時刻にスケジュールされます。  次に、このアプリケーションを使用して、これらの各デバイスが reboot メソッドを受信し実行する進行状況を追跡できます。

これらの各機能について詳しくは、次の記事をご覧ください。

* デバイス ツインとプロパティ: [デバイス ツインの概要][lnk-get-started-twin]に関する記事と[デバイス ツインのプロパティの使用方法に関するチュートリアル][lnk-twin-props]
* ダイレクト メソッド: [ダイレクト メソッドに関する IoT Hub 開発者ガイド][lnk-dev-methods]と[ダイレクト メソッドに関するチュートリアル][lnk-c2d-methods]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

このチュートリアルでは、次の操作方法について説明します。

* ダイレクト メソッドを持つ Node.js シミュレート対象デバイス アプリを作成します。このアプリは **lockDoor** を有効にし、ソリューション バックエンドから呼び出すことができます。
* ジョブを使用してシミュレート対象デバイス アプリで **lockDoor** ダイレクト メソッドを呼び出し、デバイス ジョブを使用して必要なプロパティを更新する Node.js コンソール アプリを作成します。

このチュートリアルを完了すると、次の 2 つの Node.js アプリが完成します。

**simDevice.js**。デバイス ID で IoT ハブに接続し、**lockDoor** ダイレクト メソッドを受信します。

**scheduleJobService.js**。シミュレート対象デバイス アプリでダイレクト メソッドを呼び出し、ジョブを使用してデバイス ツインの必要なプロパティを更新します。

このチュートリアルを完了するには、以下が必要です。

* Node.js バージョン 4.0.x 以降。 <br/>  「[Prepare your development environment (開発環境を準備する)][lnk-dev-setup]」では、このチュートリアルのために Node.js を Windows または Linux にインストールする方法が説明されています。
* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 (アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成できます)。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>シミュレート対象デバイス アプリの作成
このセクションでは、クラウドによって呼び出されたダイレクト メソッドに応答する Node.js コンソール アプリを作成します。このアプリはシミュレートされた **lockDoor** メソッドをトリガーします。

1. **simDevice** という名前の新しい空のフォルダーを作成します。  コマンド プロンプトで次のコマンドを使用して、**simDevice** フォルダー内に新しい package.json ファイルを作成します。  次の既定値をすべてそのまま使用します。
   
    ```
    npm init
    ```
2. コマンド プロンプトで、**simDevice** フォルダーに移動し、次のコマンドを実行して、**azure-iot-device** Device SDK パッケージと **azure-iot-device-mqtt** パッケージをインストールします。
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. テキスト エディターを使用して、**simDevice** フォルダーに新しい **simDevice.js** ファイルを作成します。
4. **simDevice.js** ファイルの先頭に、次の 'require' ステートメントを追加します。
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. **connectionString** 変数を追加し、それを使用して **Client** インスタンスを作成します。  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. **lockDoor** メソッドを処理する次の関数を追加します。
   
    ```
    var onLockDoor = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        console.log('Locking Door!');
    };
    ```
7. **lockDoor** メソッドのハンドラーを登録する次のコードを追加します。
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub. Register handler for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
    });
    ```
8. **simDevice.js** ファイルを保存して閉じます。

> [!NOTE]
> わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用環境のコードでは、[一時的な障害処理][lnk-transient-faults]に関する MSDN の記事で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。
> 
> 

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>ダイレクト メソッドを呼び出し、デバイス ツインのプロパティを更新するジョブのスケジュール
このセクションでは、ダイレクト メソッドを使用してデバイスでリモート **lockDoor** を開始する Node.js コンソール アプリを作成し、デバイス ツインのプロパティを更新します。

1. **scheduleJobService** という名前の新しい空のフォルダーを作成します。  コマンド プロンプトで次のコマンドを使用して、**scheduleJobService** フォルダー内に新しい package.json ファイルを作成します。  次の既定値をすべてそのまま使用します。
   
    ```
    npm init
    ```
2. コマンド プロンプトで、**scheduleJobService** フォルダーに移動し、次のコマンドを実行して、**azure-iothub** Device SDK パッケージと **azure-iot-device-mqtt** パッケージをインストールします。
   
    ```
    npm install azure-iothub uuid --save
    ```
3. テキスト エディターを使用して、**scheduleJobService** フォルダーに新しい **scheduleJobService.js** ファイルを作成します。
4. **dmpatterns_gscheduleJobServiceetstarted_service.js** ファイルの先頭に、次の 'require' ステートメントを追加します。
   
    ```
    'use strict';
   
    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```
5. 次の変数宣言を追加して、プレース ホルダーの値を置き換えます。
   
    ```
    var connectionString = '{iothubconnectionstring}';
    var queryCondition = "deviceId IN ['myDeviceId']";
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  300;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```
6. ジョブの実行を監視するために使用する次の関数を追加します。
   
    ```
    function monitorJob (jobId, callback) {
        var jobMonitorInterval = setInterval(function() {
            jobClient.getJob(jobId, function(err, result) {
            if (err) {
                console.error('Could not get job status: ' + err.message);
            } else {
                console.log('Job: ' + jobId + ' - status: ' + result.status);
                if (result.status === 'completed' || result.status === 'failed' || result.status === 'cancelled') {
                clearInterval(jobMonitorInterval);
                callback(null, result);
                }
            }
            });
        }, 5000);
    }
    ```
7. 次のコードを追加して、デバイス メソッドを呼び出すジョブをスケジュールします。
   
    ```
    var methodParams = {
        methodName: 'lockDoor',
        payload: null,
        responseTimeoutInSeconds: 15 // Timeout after 15 seconds if device is unable to process method
    };
   
    var methodJobId = uuid.v4();
    console.log('scheduling Device Method job with id: ' + methodJobId);
    jobClient.scheduleDeviceMethod(methodJobId,
                                queryCondition,
                                methodParams,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule device method job: ' + err.message);
        } else {
            monitorJob(methodJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor device method job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```
8. 次のコードを追加して、デバイス ツインを更新するようにジョブをスケジュールします。
   
    ```
    var twinPatch = {
       etag: '*', 
       properties: {
           desired: {
               building: '43', 
               floor: 3
           }
       }
    };
   
    var twinJobId = uuid.v4();
   
    console.log('scheduling Twin Update job with id: ' + twinJobId);
    jobClient.scheduleTwinUpdate(twinJobId,
                                queryCondition,
                                twinPatch,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule twin update job: ' + err.message);
        } else {
            monitorJob(twinJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor twin update job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```
9. **scheduleJobService.js** ファイルを保存して閉じます。

## <a name="run-the-applications"></a>アプリケーションの実行
これで、アプリケーションを実行する準備が整いました。

1. コマンド プロンプトで、**simDevice** フォルダーに移動し、次のコマンドを実行して再起動のダイレクト メソッドのリッスンを開始します。
   
    ```
    node simDevice.js
    ```
2. コマンド プロンプトで、**scheduleJobService** フォルダーに移動し、次のコマンドを実行して、ドアをロックしてツインを更新するジョブをトリガーします。
   
    ```
    node scheduleJobService.js
    ```
3. ダイレクト メソッドに対するデバイスの応答がコンソールに表示されます。

## <a name="next-steps"></a>次の手順
このチュートリアルでは、ジョブを使用して、デバイスへのダイレクト メソッドと、デバイス ツインのプロパティの更新をスケジュールしました。

ファームウェアのリモートでのワイヤレス更新などの IoT Hub による他のデバイス管理パターンを確認するには、次の記事を参照してください。

[チュートリアル: ファームウェアを更新する方法][lnk-fwupdate]

引き続き IoT Hub の使用方法を確認するには、[Azure IoT Edge の使用][lnk-iot-edge]に関する記事を参照してください。

[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-props]: tutorial-device-twins.md
[lnk-c2d-methods]: quickstart-control-device-node.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: tutorial-firmware-update.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
