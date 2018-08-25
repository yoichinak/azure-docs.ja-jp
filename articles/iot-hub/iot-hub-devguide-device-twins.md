---
title: Azure IoT Hub デバイス ツインについて | Microsoft Docs
description: 開発者ガイド - デバイス ツインを使用して、IoT Hub とデバイス間で状態と構成を同期する
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: elioda
ms.openlocfilehash: e94b023275f3992bea9595195688eebf42f0f229
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186790"
---
# <a name="understand-and-use-device-twins-in-iot-hub"></a>IoT Hub のデバイス ツインの理解と使用

"*デバイス ツイン*" は、デバイスの状態に関する情報 (メタデータ、構成、状態など) を格納する JSON ドキュメントです。 Azure IoT Hub は、IoT Hub に接続する各デバイスにデバイス ツインを保持します。 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

この記事では、次の内容について説明します。

* デバイス ツインの構造: "*タグ*"、"*必要なプロパティ*"、"*報告されるプロパティ*"。
* デバイス ツインでデバイス アプリとバックエンドが実行できる操作。

次の場合にデバイス ツインを使用します。

* デバイス固有のメタデータをクラウドに格納します。 例: 自動販売機の設置場所。
* デバイス アプリで利用できる機能や状態などの現在の状態に関する情報を報告する。 たとえば、デバイスが携帯電話または WiFi を介して IoT ハブに接続されている場合。
* デバイス アプリとバックエンドの間で実行時間の長いワークフロー の状態を同期する。 たとえば、ソリューション バックエンドがインストールする新しいファームウェアのバージョンを指定し、デバイス アプリが更新プロセスのさまざまな段階を報告する場合。
* デバイス メタデータ、構成、または状態を照会する。

報告されるプロパティ、device-to-cloud メッセージ、ファイルのアップロードのどれを使用するべきかの指針については、「[device-to-cloud 通信に関するガイダンス][lnk-d2c-guidance]」を参照してください。
必要なプロパティ、ダイレクト メソッド、cloud-to-device メッセージの使用に関する指針については、「[cloud-to-device 通信に関するガイダンス][lnk-c2d-guidance]」を参照してください。

## <a name="device-twins"></a>デバイス ツイン
デバイス ツインには、デバイスに関連する次のような情報が格納されます。

* デバイスの状態と構成を同期するために使用するデバイスとバックエンド。
* 実行時間の長い操作にクエリを行い、ターゲットを設定するために使用するソリューション バックエンド。

デバイス ツインのライフサイクルは、対応する[デバイス ID][lnk-identity] にリンクされます。 IoT Hub でデバイス ID を作成または削除したときに、デバイス ツインは暗黙的に作成または削除されます。

デバイス ツインは次の情報を含む JSON ドキュメントです。

* **タグ**。 ソリューション バックエンドで読み書きできる JSON ドキュメントのセクションです。 タグは、デバイス アプリには表示されません。
* **必要なプロパティ**。 デバイスの構成や状態を同期するために、報告されるプロパティと共に使用します。 ソリューション バックエンドは必要なプロパティを設定でき、デバイス アプリはそれらを読み取れます。 デバイス アプリでは、必要なプロパティに対する変更を知らせる通知を受け取ることもできます。
* **報告されるプロパティ**。 デバイスの構成や状態を同期するために、必要なプロパティと共に使用します。 デバイス アプリは報告されるプロパティを設定でき、ソリューション バックエンドはそれらを読み取りクエリを実行できます。
* **デバイス ID のプロパティ**。 デバイス ツインの JSON ドキュメントのルートには、[ID レジストリ][lnk-identity]に格納される対応するデバイス ID の読み取り専用プロパティが含まれます。

![][img-twin]

次の例は、デバイス ツインの JSON ドキュメントを示しています。

```json
{
    "deviceId": "devA",
    "etag": "AAAAAAAAAAc=", 
    "status": "enabled",
    "statusReason": "provisioned",
    "statusUpdateTime": "0001-01-01T00:00:00",
    "connectionState": "connected",
    "lastActivityTime": "2015-02-30T16:24:48.789Z",
    "cloudToDeviceMessageCount": 0, 
    "authenticationType": "sas",
    "x509Thumbprint": {     
        "primaryThumbprint": null, 
        "secondaryThumbprint": null 
    }, 
    "version": 2, 
    "tags": {
        "$etag": "123",
        "deploymentLocation": {
            "building": "43",
            "floor": "1"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata" : {...},
            "$version": 1
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            }
            "batteryLevel": 55,
            "$metadata" : {...},
            "$version": 4
        }
    }
}
```

ルート オブジェクトには、デバイス ID プロパティと、`tags` のコンテナー オブジェクトと、`reported` プロパティと `desired` プロパティの両方があります。 `properties` コンテナーには、「[デバイス ツインのメタデータ][lnk-twin-metadata]」と「[オプティミスティック同時実行制御][lnk-concurrency]」セクションで説明しているいくつかの読み取り専用の要素 (`$metadata`、`$etag`、`$version`) が含まれています。

### <a name="reported-property-example"></a>報告されるプロパティの例
前の例では、デバイス アプリによって報告される `batteryLevel` プロパティがデバイス ツインに含まれています。 このプロパティでは、最後に報告されたバッテリ レベルに基づいて、デバイス上でクエリや操作を実行できます。 別の例では、デバイス アプリにデバイスの報告機能や接続オプションが備わっています。

> [!NOTE]
> ソリューション バックエンドにおける関心の対象が、最後に確認されているプロパティの値であるような状況では、報告されたプロパティを使用した方が簡単です。 ソリューション バックエンドでタイムスタンプが付けられた連続するイベントの形式でデバイス テレメトリ (時系列など) を処理する必要がある場合は、[device-to-cloud メッセージ][lnk-d2c]を使用します。

### <a name="desired-property-example"></a>必要なプロパティの例
上記の例では、ソリューション バックエンドとデバイス アプリが `telemetryConfig` デバイス ツインの必要なプロパティと報告されたプロパティを使用して、デバイスのテレメトリ構成を同期しています。 例: 

1. ソリューション バックエンドでは、必要なプロパティが必要な構成値で設定されます。 以下は、ドキュメント内の必要なプロパティ セットを使用した部分です。

    ```json
    ...
    "desired": {
        "telemetryConfig": {
            "sendFrequency": "5m"
        },
        ...
    },
    ...
    ```

2. 接続されている場合や、最初に再接続されたときに、変更があるとデバイス アプリに直ちに通知されます。 その後、デバイス アプリが更新された構成 (またはエラー条件。`status` プロパティを使用) を報告します。 報告されるプロパティの部分を次に示します。

    ```json
    ...
    "reported": {
        "telemetryConfig": {
            "sendFrequency": "5m",
            "status": "success"
        }
        ...
    }
    ...
    ```

3. ソリューション バックエンドは、デバイス ツインに[クエリを実行][lnk-query]することによって、多数のデバイス間で行われる構成操作の結果を追跡できます。

> [!NOTE]
> 上記のスニペットは、デバイス構成とその状態をエンコードするための例の 1 つであり、読みやすいように最適化されています。 IoT Hub は、デバイス ツインの必要なプロパティや報告されたプロパティに対して、デバイス ツイン用の特定のスキーマを強制しません。
> 
> 

ファームウェアの更新など実行時間の長い操作は、デバイス ツインを使って同期することができます。 デバイス間の実行時間の長い操作を同期、追跡するためのプロパティの使用方法については、「[必要なプロパティを使用してデバイスを構成する][lnk-twin-properties]」を参照してください。

## <a name="back-end-operations"></a>バック エンドの操作
ソリューション バックエンドは、HTTPS を介して公開される次のアトミック操作を使用して、デバイス ツインを操作します。

* **デバイス ツインを ID で取得する**。 この操作は、タグ、必要なシステム プロパティ、報告されるシステム プロパティを含むデバイス ツインのドキュメントを返します。
* **デバイス ツインを部分的に更新する**。 この操作では、ソリューション バックエンドがデバイス ツインのタグまたは必要なプロパティを部分的に更新できます。 部分的な更新は JSON ドキュメントとして表され、プロパティが追加または更新されます。 `null` に設定されたプロパティが削除されます。 次の例では、`{"newProperty": "newValue"}` の値を持つ新しい必要なプロパティが作成され、`existingProperty` の既存の値が `"otherNewValue"` で上書きされ、`otherOldProperty` が削除されます。 それ以外、既にある必要なプロパティまたはタグは変更されません。

    ```json
    {
        "properties": {
            "desired": {
                "newProperty": {
                    "nestedProperty": "newValue"
                },
                "existingProperty": "otherNewValue",
                "otherOldProperty": null
            }
        }
    }
    ```

* **必要なプロパティの置換** この操作では、ソリューション バックエンドによって既存の必要なプロパティをすべて完全に上書きし、`properties/desired` の新しい JSON ドキュメントに置き換えられます。
* **タグの置換** この操作では、ソリューション バックエンドによって既存のタグをすべて完全に上書きし、`tags` の新しい JSON ドキュメントに置き換えられます。
* **ツイン通知の受信** この操作は、ソリューション バックエンドでツインが変更されたときに通知できるようにします。 そのためには、IoT ソリューションでルートを作成し、データ ソースの値を *twinChangeEvents* に設定する必要があります。 既定では、ツイン通知は送信されません。つまり、このようなルートは事前に存在しません。 変化率が高すぎる場合、または内部エラーなどの理由のために、IoT Hub はすべての変更を含む 1 つの通知のみを送信する場合があります。 そのため、アプリケーションに信頼性の高い監査とすべての中間状態のログ記録が必要な場合は、device-to-cloud メッセージを使用する必要があります。 ツイン通知メッセージには、プロパティおよび本文が含まれます。

    - Properties

    | Name | 値 |
    | --- | --- |
    $content-type | application/json |
    $iothub-enqueuedtime |  通知が送信された時刻 |
    $iothub-message-source | twinChangeEvents |
    $content-encoding | utf-8 |
    deviceId | デバイスの ID |
    hubName | IoT Hub の名前 |
    operationTimestamp | 操作の [ISO8601] タイムスタンプ |
    iothub-message-schema | deviceLifecycleNotification |
    opType | "replaceTwin" または "updateTwin" |

    メッセージのシステム プロパティには、`'$'` シンボルが付きます。

    - 本文
        
    このセクションには、すべてのツイン変更が JSON 形式で含まれています。 修正プログラムと同じ形式を使用しますが、すべてのツイン セクション (タグ、properties.reported、properties.desired) を含めることができ、"$metadata" 要素を含みます。 たとえば、次のように入力します。

    ```json
    {
        "properties": {
            "desired": {
                "$metadata": {
                    "$lastUpdated": "2016-02-30T16:24:48.789Z"
                },
                "$version": 1
            },
            "reported": {
                "$metadata": {
                    "$lastUpdated": "2016-02-30T16:24:48.789Z"
                },
                "$version": 1
            }
        }
    }
    ```

上述の操作はすべて[オプティミスティック同時実行制御][lnk-concurrency]をサポートしており、[セキュリティ][lnk-security]に関する記事で定義されているとおり、**ServiceConnect** アクセス許可を必要とします。

これらの操作の他に、ソリューション バックエンドでは以下を実行できます。

* SQL ライクな [IoT Hub クエリ言語][lnk-query]を使用して、デバイス ツインを照会します。
* [ ジョブ][lnk-jobs]を使用して、大量のデバイス ツインで操作を実行します。

## <a name="device-operations"></a>デバイスの操作
デバイス アプリは、次のアトミック操作を使用して、デバイス ツインを操作します。

* **デバイス ツインを取得する**。 この操作は、タグ、必要なシステム プロパティ、報告されるシステム プロパティを含む、現在接続されているデバイスのデバイス ツインのドキュメントを返します。
* **報告されるプロパティの部分的な更新** この操作では、現在接続されているデバイスの報告されるプロパティを部分的に更新できます。 この操作には、必要なプロパティを部分的に更新する際にソリューション バックエンドで使用されるものと同じ JSON 更新フォーマットが使用されます。
* **必要なプロパティの監視** 現在接続されているデバイスでは、必要なプロパティの更新が行われたときに通知をするように設定できます。 デバイスは、ソリューション バックエンドによって実行される更新 (部分的または完全な置換) と同じフォームを受け取ります。

[セキュリティ][lnk-security]に関する記事に定義されているように、上述の操作にはすべて **DeviceConnect** アクセス許可が必要です。

[Azure IoT device SDK][lnk-sdks] を使用すると、多数の言語とプラットフォームで上述の操作を簡単に使用できます。 必要なプロパティを同期させるための IoT Hub プリミティブの詳細については、「[デバイスの再接続フロー][lnk-reconnection]」を参照してください。

## <a name="tags-and-properties-format"></a>タグやプロパティの形式
タグ、必要なプロパティ、報告されるプロパティは JSON オブジェクトであり、次のような制限があります。

* JSON オブジェクトのすべてのキーは、大文字小文字が区別される 64 バイトの UTF-8 UNICODE 文字列です。 UNICODE 制御文字列 (セグメント C0 と C1)、`'.'`、`' '`、`'$'` は使用できません。
* JSON オブジェクトのすべての値には、ブール値、数値、文字列、オブジェクトの JSON 型を使用できます。 配列は使用できません。 整数の最大値は 4503599627370495 であり、整数の最小値は -4503599627370496 です。
* タグ、必要なプロパティ、および報告されるプロパティのすべての JSON オブジェクトは、深さ 5 まで許容されます。 たとえば、次のオブジェクトは有効です。

    ```json
    {
        ...
        "tags": {
            "one": {
                "two": {
                    "three": {
                        "four": {
                            "five": {
                                "property": "value"
                            }
                        }
                    }
                }
            }
        },
        ...
    }
    ```

* すべての文字列値の長さは、最大で 4 KB まで許容されます。

## <a name="device-twin-size"></a>デバイス ツインのサイズ
読み取り専用の要素を除き、IoT Hub では `tags`、`properties/desired`、`properties/reported` の各合計値に対して強制的に 8 KB のサイズ制限が適用されます。
このサイズは、UNICODE 制御文字 (セグメント C0 と C1) を除くすべての文字と、文字列定数以外で使用されるスペースをカウントして計算されます。
ドキュメントのサイズが上述の制限を超えると、IoT Hub はすべての操作を拒否して、エラーを返します。

## <a name="device-twin-metadata"></a>デバイス ツインのメタデータ
IoT Hub は、各 JSON オブジェクトが最後に更新されたときのタイムスタンプを、デバイス ツインの必要なプロパティと報告されるプロパティで保持します。 タイムスタンプは UTC であり、[ISO8601] 形式の `YYYY-MM-DDTHH:MM:SS.mmmZ` でエンコードされます。
例: 

```json
{
    ...
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": {
                        "$lastUpdated": "2016-03-30T16:24:48.789Z"
                    },
                    "$lastUpdated": "2016-03-30T16:24:48.789Z"
                },
                "$lastUpdated": "2016-03-30T16:24:48.789Z"
            },
            "$version": 23
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            }
            "batteryLevel": "55%",
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": "5m",
                    "status": {
                        "$lastUpdated": "2016-03-31T16:35:48.789Z"
                    },
                    "$lastUpdated": "2016-03-31T16:35:48.789Z"
                }
                "batteryLevel": {
                    "$lastUpdated": "2016-04-01T16:35:48.789Z"
                },
                "$lastUpdated": "2016-04-01T16:24:48.789Z"
            },
            "$version": 123
        }
    }
    ...
}
```

この情報は、オブジェクトのキーによって削除される更新を保持するために、JSON の構造のリーフだけでなくすべてのレベルで保持されます。

## <a name="optimistic-concurrency"></a>オプティミスティック同時実行制御
タグ、必要なプロパティ、報告されたプロパティはすべて、オプティミスティック同時実行制御をサポートします。
タグは、[RFC7232] に従って ETag を持ちます。これは、タグの JSON 表現を表します。 ETag を使用すると、条件付き更新操作をソリューション バックエンドから実行し、一貫性を保持できます。

デバイス ツインの必要なプロパティと報告されるプロパティには ETag はありませんが、`$version` の値によって確実に増分されます。 ETag と同様、更新側がバージョンを使用することで更新プログラムの一貫性を確保することができます。 たとえば、報告されたプロパティ用のデバイス アプリ、または目的のプロパティのソリューション バックエンドです。

バージョンは、監視エージェント (必要なプロパティを監視するデバイス アプリなど) が取得操作に関する結果と更新の通知の間の競合を調整する場合に便利です。 詳細は、「[デバイスの再接続フロー][lnk-reconnection]」を参照してください。

## <a name="device-reconnection-flow"></a>デバイスの再接続フロー
IoT Hub では、必要なプロパティの更新通知は接続されていないデバイス用に保持されません。 つまり、接続しているデバイスは必要なプロパティの完全なドキュメントを取得し、さらに更新通知にサブスクライブする必要があります。 更新通知と完全取得で競合が発生する場合、次のフローを実行する必要があります。

1. デバイス アプリを IoT hub に接続する。
2. デバイス アプリを、必要なプロパティの更新通知にサブスクライブする。
3. デバイス アプリで、必要なプロパティの完全なドキュメントを取得する。

`$version` が取得された完全なドキュメントのバージョン以下の場合、デバイス アプリではすべての通知を無視できます。 IoT Hub ではバージョンが常に増分されるため、このようなアプローチが可能になります。

> [!NOTE]
> このロジックは [Azure IoT device SDK][lnk-sdks] に既に実装されています。 上述の説明は、デバイス アプリが Azure IoT device SDK を使用できず、MQTT インターフェイスを直接プログラミングしなければならない場合にのみ適用されます。
> 
> 

## <a name="additional-reference-material"></a>参考資料
IoT Hub 開発者ガイド内の他の参照トピックは次のとおりです。

* [IoT Hub エンドポイント][lnk-endpoints]: 各 IoT ハブがランタイムと管理の操作のために公開する、さまざまなエンドポイントについて説明します。
* [調整とクォータ][lnk-quotas]: IoT Hub サービスに適用されるクォータと、サービスを使用するときに想定される調整の動作について説明します。
* [Azure IoT device SDK とサービス SDK][lnk-sdks]: IoT Hub とやりとりするデバイスとサービス アプリの両方を開発する際に使用できるさまざまな言語の SDK を紹介します。
* [デバイス ツイン、ジョブ、メッセージ ルーティングの IoT Hub クエリ言語][lnk-query]: デバイス ツインとジョブに関する情報を IoT Hub から取得するために使用できる IoT Hub クエリ言語について説明します。
* [IoT Hub の MQTT サポート][lnk-devguide-mqtt]: IoT Hub での MQTT プロトコルのサポートについて詳しく説明します。

## <a name="next-steps"></a>次の手順
デバイス ツインの詳細を理解したら、次の IoT Hub 開発者ガイド トピックも参考にしてください。

* [IoT Hub のモジュール ツインの理解と使用][lnk-module-twins]
* [デバイスでダイレクト メソッドを呼び出す][lnk-methods]
* [複数デバイスでのジョブをスケジュール設定する][lnk-jobs]

この記事で説明した概念を試すには、次の IoT Hub のチュートリアルをご覧ください。

* [デバイス ツインの使用方法][lnk-twin-tutorial]
* [デバイス ツインのプロパティの使用方法][lnk-twin-properties]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-identity]: iot-hub-devguide-identity-registry.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-security]: iot-hub-devguide-security.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[ISO8601]: https://en.wikipedia.org/wiki/ISO_8601
[RFC7232]: https://tools.ietf.org/html/rfc7232
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: tutorial-device-twins.md
[lnk-twin-metadata]: iot-hub-devguide-device-twins.md#device-twin-metadata
[lnk-concurrency]: iot-hub-devguide-device-twins.md#optimistic-concurrency
[lnk-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow
[lnk-module-twins]:iot-hub-devguide-module-twins.md

[img-twin]: media/iot-hub-devguide-device-twins/twin.png
