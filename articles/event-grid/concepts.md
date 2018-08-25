---
title: Azure Event Grid の概念
description: Azure Event Grid とその概念について説明します。 Event Grid のいくつかの主要コンポーネントを定義します。
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: tomfitz
ms.openlocfilehash: 2a288cdb96a1e1ff7e261d4782f7e02aee12868f
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621203"
---
# <a name="concepts-in-azure-event-grid"></a>Azure Event Grid の概念

この記事では、Azure Event Grid の主要な概念について説明します。

## <a name="events"></a>events

イベントは、システム内で発生した何かを完全に記述する最小限の情報です。 すべてのイベントは、イベントの発生元、イベントの発生時間、一意識別子などの一般的な情報を持っています。 各イベントには、特定の種類のイベントにのみ関連する情報も含まれます。 たとえば、Azure Storage に作成される新しいファイルに関するイベントには、`lastTimeModified` 値などのファイルの詳細が含まれます。 または、Event Hubs イベントには、キャプチャ ファイルの URL が含まれます。 

各イベントは、64 KB のデータに制限されます。

イベントで送信されるプロパティについては、[Azure Event Grid イベント スキーマ](event-schema.md)を参照してください。

## <a name="publishers"></a>発行元

発行元は、Event Grid にイベントを送信することを決定するユーザーまたは組織です。 Microsoft では、いくつかの Azure サービスのためのイベントを発行しています。 お客様独自のアプリケーションからイベントを発行することができます。 Azure を使用しないでサービスをホストしている組織は、Event Grid からイベントを発行できます。

## <a name="event-sources"></a>イベント ソース

イベント ソースは、イベントの発生場所です。 各イベント ソースは、1 つまたは複数のイベントの種類に関連付けられます。 たとえば、Azure Storage は、BLOB 作成イベントのイベント ソースです。 IoT Hub は、デバイスによって作成されたイベントのためのイベント ソースです。 お客様のアプリケーションは、お客様が定義するカスタム イベントのイベント ソースです。 イベント ソースは、Event Grid にイベントを送信します。

サポートされている任意の Event Grid ソースの実装方法については、「[Event sources in Azure Event Grid (Azure Event Grid 内のイベント ソース)](event-sources.md)」を参照してください。

## <a name="topics"></a>トピック

Event Grid のトピックには、ソースがイベントを送信するエンドポイントが含まれます。 発行元は、イベント グリッド トピックを作成し、イベント ソースにトピックが 1 つ必要か、あるいは複数のイベント ソースが必要かを決定します。 トピックは、関連するイベントのコレクションに使用されます。 サブスクライバーは、特定の種類のイベントに応答するために、どのトピックをサブスクライブするかを決定します。

システム トピックは、Azure サービスが提供する組み込みのトピックです。 発行元がトピックを所有するため、Azure サブスクリプションにシステム トピックは表示されませんが、トピックをサブスクライブすることはできます。 サブスクライブするには、イベントを受信するリソースに関する情報を入力します。 リソースにアクセスできる限り、そのイベントをサブスクライブすることができます。

カスタム トピックは、アプリケーションとサード パーティのトピックです。 カスタムのトピックを作成した、またはカスタムのトピックへのアクセス権を割り当てられた場合は、サブスクリプション内にそのカスタム トピックが表示されます。

アプリケーションを設計するときに、作成するトピック数を柔軟に決定することができます。 大規模なソリューションの場合、関連するイベントのカテゴリごとにカスタム トピックを作成します。 たとえば、ユーザー アカウントの変更と注文の処理に関連するイベントを送信するアプリケーションがあるものとします。 イベント ハンドラーで両方のイベント カテゴリが必要になることはまずありません。 このような場合は、2 つのカスタム トピックを作成し、イベント ハンドラーが自分と関係のあるトピックをサブスクライブできるようにします。 小規模なソリューションの場合、すべてのイベントを 1 つのトピックに送信することをお勧めします。 イベントのサブスクライバーは、必要なイベントの種類をフィルター処理できます。

## <a name="event-subscriptions"></a>イベントのサブスクリプション

サブスクリプションによって、どのトピックのイベントを受信するかが Event Grid に通知されます。 サブスクリプションを作成する際に、イベントを処理するためのエンドポイントを入力します。 エンドポイントに送信されるイベントをフィルター処理できます。 イベントの種類またはサブジェクトのパターンでフィルター処理できます。 詳細については、「[Event Grid subscription schema](subscription-creation-schema.md)」(Event Grid のサブスクリプション スキーマ) を参照してください。

サブスクリプションを作成する例については、以下を参照してください。

* [Event Grid 用の Azure CLI サンプル](cli-samples.md)
* [Event Grid に関する Azure PowerShell のサンプル](powershell-samples.md)
* [Event Grid 用の Azure Resource Manager テンプレート](template-samples.md)

現在の Event Grid サブスクリプションの取得については、「[Event Grid サブスクリプションのクエリを実行する](query-event-subscriptions.md)」を参照してください。

## <a name="event-handlers"></a>イベント ハンドラー

Event Grid から考えると、イベント ハンドラーはイベントの送信先です。 ハンドラーは、さらにいくつかのアクションを行ってイベントを処理します。 Event Grid は、複数の種類のハンドラーをサポートします。 サポートされている Azure サービスまたは独自の Webhook を、ハンドラーとして使用できます。 Event Grid は、ハンドラーの種類に応じたさまざまなメカニズムに従って、イベントの配信を保証します。 HTTP Webhook イベント ハンドラーでは、ハンドラーが状態コード `200 – OK` を返すまでイベントが再試行されます。 Azure Storage Queue では、Queue サービスがキューにメッセージのプッシュを正常に処理できるようになるまで、イベントが再試行されます。

サポートされている任意の Event Grid ハンドラーの実装方法については、「[Event handlers in Azure Event Grid (Azure Event Grid 内のイベント ハンドラー)](event-handlers.md)」を参照してください。

## <a name="security"></a>セキュリティ

Event Grid は、トピックのサブスクライブと発行をセキュリティで保護します。 サブスクライブするときは、リソースまたは Event Grid トピックに対する十分なアクセス許可が必要です。 発行するときは、トピック用の SAS トークンまたはキー認証が必要です。 詳細については、「[Event Grid security and authentication](security-authentication.md)」(Event Grid のセキュリティと認証) を参照してください。

## <a name="event-delivery"></a>イベント配信

イベントがサブスクライバーのエンドポイントによって受信されたことを Event Grid が確認できない場合、イベントは再配信されます。 詳細については、[Event Grid のメッセージの配信と再試行](delivery-and-retry.md)に関する記事を参照してください。

## <a name="batching"></a>バッチ処理

カスタム トピックを使用する場合は、イベントを常に配列内で発行する必要があります。 低スループットのシナリオでは 1 つのバッチで対応できますが、高ボリュームのユース ケースでは、効率性を向上するために、発行ごとに複数のイベントを 1 つのバッチにまとめることをお勧めします。 バッチのサイズは最大 1 MB に指定できます。 各イベントが 64 KB を超えないようにしてください。

## <a name="next-steps"></a>次の手順

* Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Event Grid の使用をすぐに開始するには、[Azure Event Grid でのカスタム イベントの作成とルーティング](custom-event-quickstart.md)に関する記事を参照してください。
