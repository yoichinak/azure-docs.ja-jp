---
title: Azure IoT Hub の device-to-cloud メッセージの理解 | Microsoft Docs
description: 開発者ガイド - IoT Hub での device-to-cloud メッセージの使用方法。 テレメトリとそれ以外の情報の両方のデータ送信およびルーティングを使用したメッセージの配信について説明します。
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dobett
ms.openlocfilehash: be87b00f27f0d0b25cd77a0634ab1c653a85e5ac
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2018
ms.locfileid: "39126444"
---
# <a name="send-device-to-cloud-messages-to-iot-hub"></a>device-to-cloud メッセージを IoT Hub に送信する

時系列のテレメトリとアラートを、デバイスからソリューション バックエンドに送信するには、device-to-cloud メッセージを、デバイスから IoT hub に送信します。 IoT Hub でサポートされるその他のデバイスからクラウドのオプションの詳細については、「[device-to-cloud 通信に関するガイダンス][lnk-d2c-guidance]」をご覧ください。

デバイスからのクラウドへのメッセージはデバイス向けのエンドポイント (**/devices/{deviceId}/messages/events**) を介して送信されます。 次にルーティング ルールに従って、IoT Hub 上のサービス向けエンドポイントの 1 つにメッセージをルーティングします。 ルーティング ルールは device-to-cloud メッセージのヘッダーとボディーを使用して、メッセージのルーティング先を決定します。 既定では、メッセージは [Event Hubs][lnk-event-hubs] と互換性のある組み込みサービス向けエンドポイント (**messages/events**) にルーティングされます。 このため、標準的な [Event Hubs の統合と SDK][lnk-compatible-endpoint] を使用して、ソリューション バックエンドで device-to-cloud メッセージを受信できます。

IoT Hub は、ストリーミング メッセージング パターンを使用して、D2C メッセージングを実装しています。 IoT Hub の D2C メッセージは[Service Bus][lnk-servicebus] *メッセージ* というよりはむしろ [Event Hubs][lnk-event-hubs] の *イベント* であり、複数のリーダーで読み取り可能なサービスを経由する、大量のイベントが存在します。

IoT Hub を使用した device-to-cloud メッセージングには、次のような特徴があります。

* device-to-cloud メッセージには持続性があり、最長で 7 日間、IoT hub の既定の **messages/events** エンドポイントに保持されます。
* device-to-cloud メッセージは最大 256 KB で、バッチとしてグループ化して送信を最適化できます。 バッチは最大で 256 KB です。
* 「[IoT Hub へのアクセスの制御][lnk-devguide-security]」のセクションで説明されているように、IoT Hub ではデバイスごとに認証とアクセス制御を行うことができます。
* IoT Hub では、最大 10 個のカスタム エンドポイントを作成できます。 IoT Hub で構成されているルートに基づいて、エンドポイントにメッセージが配信されます。 詳細については、[ルーティング ルール](iot-hub-devguide-query-language.md#device-to-cloud-message-routes-query-expressions)に関するセクションをご覧ください。
* IoT Hub により、何百万もの接続されているデバイスを同時に有効にできます (「[クォータと調整][lnk-quotas]」をご覧ください)。
* IoT Hub では、任意のパーティション分割は許可されていません。 D2C メッセージは、発信元の **deviceId**に基づいてパーティション分割されます。

IoT Hub と Event Hubs の違いについての詳細は、「[Azure IoT Hub と Azure Event Hubs の比較][lnk-comparison]」をご覧ください。

## <a name="send-non-telemetry-traffic"></a>非テレメトリ トラフィックの送信

多くの場合、デバイスからは、テレメトリが送信されるだけでなく、ソリューション バックエンドでの個別の実行と処理を必要とするメッセージと要求が送信されます。 たとえば、バック エンドで特定のアクションをトリガーする必要がある重大なアラートです。 メッセージのヘッダーまたはメッセージ本文内の値に基づいて、これらの処理を専用に行うエンドポイントに対して、このようなメッセージを送信する[ルーティング ルール][lnk-devguide-custom]を記述できます。

この種類のメッセージを処理する最適な方法の詳細については、[IoT Hub のデバイスからクラウドへのメッセージを処理する方法][lnk-d2c-tutorial]に関するチュートリアルをご覧ください。

## <a name="route-device-to-cloud-messages"></a>device-to-cloud メッセージのルーティング

バックエンド アプリケーションへの device-to-cloud メッセージのルーティングには、2 つのオプションがあります。

* 組み込みの [Event Hubs と互換性のあるエンドポイント][lnk-compatible-endpoint]を使用して、ハブから受け取った device-to-cloud メッセージをバックエンド アプリで読み取ることができます。 組み込みの Event Hubs と互換性のあるエンドポイントの詳細については、[組み込みのエンドポイントから device-to-cloud メッセージを読み取る][lnk-devguide-builtin]に関する記事をご覧ください。
* IoT hub でのカスタム エンドポイントにメッセージを送信するには、ルーティング ルールを使用します。 カスタム エンドポイントによって、バックエンドのアプリが、Event Hubs、Service Bus キューまたは Service Bus トピックを使用して、device-to-cloud メッセージを読み取ることができます。 ルーティングとカスタム エンドポイントの詳細については、[device-to-cloud メッセージへのカスタム エンドポイントとルーティング ルールの使用][lnk-devguide-custom]に関する記事をご覧ください。

## <a name="anti-spoofing-properties"></a>なりすまし対策のプロパティ

D2C メッセージでのデバイスのなりすましを回避するために、IoT Hub では、すべてのメッセージに次のプロパティを持つスタンプが使用されます。

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

最初の 2 つには、「[デバイス ID プロパティ][lnk-device-properties]」で説明されている発信元デバイスの **deviceId** と **generationId** が含まれています。

**ConnectionAuthMethod** プロパティには、次のプロパティを使用してシリアル化された JSON オブジェクトが含まれています。

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>次の手順

device-to-cloud メッセージの送信に使用できる SDK については、[Azure IoT SDK][lnk-sdks] に関する記事をご覧ください。

[クイック スタート][lnk-get-started]では、デバイスからクラウドへのメッセージをシミュレートされたデバイスから送信する方法を示します。 詳細については、[ルートを使用した IoT Hub の device-to-cloud メッセージの処理][lnk-d2c-tutorial]に関するチュートリアルをご覧ください。

[lnk-devguide-builtin]: iot-hub-devguide-messages-read-builtin.md
[lnk-devguide-custom]: iot-hub-devguide-messages-read-custom.md
[lnk-comparison]: iot-hub-compare-event-hubs.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[lnk-get-started]: quickstart-send-telemetry-node.md

[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-compatible-endpoint]: iot-hub-devguide-messages-read-builtin.md
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-d2c-tutorial]: tutorial-routing.md
