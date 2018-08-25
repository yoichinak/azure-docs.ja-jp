---
title: インクルード ファイル
description: インクルード ファイル
services: service-bus-messaging
author: sethmanheim
ms.service: service-bus-messaging
ms.topic: include
ms.date: 06/04/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 0ff1e31e52c7db5c41f92cb9e4cb1a17f28dea6f
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38755862"
---
次の表は、Service Bus メッセージングに固有のクォータ情報の一覧です。 Service Bus の料金やその他のクォータについては、「 [Service Bus 料金](https://azure.microsoft.com/pricing/details/service-bus/) 」の概要を参照してください。

| クォータ名 | Scope (スコープ) | メモ | 値 |
| --- | --- | --- | --- | --- |
| Azure サブスクリプションごとの Basic/Standard 名前空間の最大数 |名前空間 |追加の Basic/Standard 名前空間に関する後続の要求はポータルで拒否されます。 |100|
| Azure サブスクリプションごとの Premium 名前空間の最大数 |名前空間 |追加の Premium 名前空間に関する後続の要求はポータルで拒否されます。 |10 |
| キュー/トピック サイズ |エンティティ |キューおよびトピック作成時に定義 <br/><br/> 後続の受信メッセージが拒否され、呼び出し元のコードが例外を受け取ります。 |1、2、3、4、または 5 GB。<br /><br />Premium SKU では、[パーティション分割](/azure/service-bus-messaging/service-bus-partitioning)が有効な Standard と同じように、最大キュー/トピック サイズは 80 GB です。 |
| 名前空間の同時接続数 |名前空間 |追加の接続に関する後続の要求は拒否され、呼び出し元のコードが例外を受け取ります。 REST 操作は、TCP 同時接続数に加算されません。 |NetMessaging: 1,000<br /><br />AMQP: 5,000 |
| キュー/トピック/サブスクリプション エンティティの同時受信要求数 |エンティティ |以後の受信要求が拒否され、呼び出し元のコードが例外を受け取ります。 このクォータは、1 つのトピックのすべてのサブスクリプションの同時受信操作の合計数に適用されます。 |5,000 |
| 名前空間あたりのトピック/キュー数 |名前空間 |以後、名前空間でのトピックまたはキューの新規作成要求が拒否されます。 その結果、([Azure Portal][Azure portal] で構成されていれば) エラー メッセージが生成されます。 管理 API から呼び出された場合は、呼び出し元のコードが例外を受け取ります。 |10,000<br /><br />名前空間のトピックとキューの合計数は、10,000 以下にする必要があります。 |
| 名前空間あたりの[パーティション分割されたトピック/キュー](/azure/service-bus-messaging/service-bus-partitioning)数 |名前空間 |以後、名前空間でのパーティション分割されたトピックまたはキューの新規作成要求が拒否されます。 その結果、([Azure Portal][Azure portal] で構成されていれば) エラー メッセージが生成されます。 管理 API から呼び出された場合は、呼び出し元のコードが **QuotaExceededException** 例外を受け取ります。 |Basic レベルと Standard レベル: 100<br/><br/>パーティション分割されたエンティティは [Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md) レベルではサポートされていません。<br/><br />パーティション分割された各キューまたはトピックは、名前空間あたり 10,000 エンティティのクォータに対してカウントされます。 |
| メッセージング エンティティのパスの最大サイズ: キューまたはトピック |エンティティ |- |260 文字 |
| メッセージング エンティティ名の最大サイズ: 名前空間、サブスクリプション、またはサブスクリプション規則 |エンティティ |- |50 文字 |
| メッセージ [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) の最大サイズ | エンティティ |- | 128 |
| キュー/トピック/サブスクリプション エンティティのメッセージ サイズ |エンティティ |これらのクォータを超える受信メッセージは拒否され、呼び出し元のコードが例外を受け取ります。 |メッセージの最大サイズ: 256 KB ([Standard レベル](../articles/service-bus-messaging/service-bus-premium-messaging.md))/1 MB ([Premium レベル](../articles/service-bus-messaging/service-bus-premium-messaging.md))。 <br /><br />システムのオーバーヘッドのため、この制限はこれらの値よりも小さくなります。<br /><br />ヘッダーの最大サイズ: 64 KB<br /><br />プロパティ バッグ内のヘッダー プロパティの最大サイズ: **バイト/int.MaxValue**<br /><br />プロパティ バッグ内のプロパティの最大サイズ: 明示的な制限はありません。 ヘッダーの最大サイズによって制限されます。 |
| キュー/トピック/サブスクリプション エンティティのメッセージ プロパティ サイズ |エンティティ |**SerializationException** 例外が生成されます。 |各プロパティのメッセージ プロパティの最大サイズは 32 K です。すべてのプロパティの合計サイズが 64 K を超えることはできません。この制限は、[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) のヘッダー全体に適用されます。このヘッダーには、ユーザー プロパティとシステム プロパティ ([SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber)、[Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label)、[MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid) など) の両方が含まれます。 |
| トピックごとのサブスクリプション数 |エンティティ |以後、そのトピックに対するサブスクリプションの新規作成要求は拒否されます。 その結果、ポータルで構成されている場合は、エラー メッセージが表示されます。 管理 API から呼び出された場合は、呼び出し元のコードが例外を受け取ります。 |2,000 |
| トピックごとの SQL フィルターの数 |エンティティ |以後、そのトピックに追加のフィルターを作成する要求は拒否され、呼び出し元のコードが例外を受け取ります。 |2,000 |
| トピックごとの関連付けフィルターの数 |エンティティ |以後、そのトピックに追加のフィルターを作成する要求は拒否され、呼び出し元のコードが例外を受け取ります。 |100,000 |
| SQL フィルター/アクションのサイズ |名前空間 |以後、追加のフィルターを作成する要求は拒否され、呼び出し元のコードが例外を受け取ります。 |フィルター条件文字列の最大長: 1,024 (1 K)。<br /><br />規則アクション文字列の最大長: 1,024 (1 K)。<br /><br />規則アクションごとの式の最大数: 32。 |
| 名前空間、キュー、トピックごとの [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 規則の数 |エンティティ、名前空間 |以後、追加の規則を作成する要求は拒否され、呼び出し元のコードが例外を受け取ります。 |規則の最大数: 12。 <br /><br /> Service Bus 名前空間に構成されている規則は、その名前空間内のすべてのキューおよびトピックに適用されます。 |
| トランザクションあたりのメッセージ数 | トランザクション | それ以上の受信メッセージは拒否され、呼び出し元のコードが "1 回のトランザクションで 100 を超えるメッセージを送信することができない" ことを示す例外を受け取ります。 | 100 <br /><br /> **Send()** 操作および **SendAsync()** 操作両方に対して。 |

[Azure portal]: https://portal.azure.com