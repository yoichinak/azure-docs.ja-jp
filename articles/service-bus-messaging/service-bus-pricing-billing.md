---
title: Service Bus の料金と課金 | Microsoft Docs
description: Service Bus の料金体系の概要です。
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 7c45b112-e911-45ab-9203-a2e5abccd6e0
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/21/2017
ms.author: sethm
ms.openlocfilehash: 4faf37394b8e4f6c4e463acb11aea898a29fef80
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448409"
---
# <a name="service-bus-pricing-and-billing"></a>Service Bus の料金と課金

Service Bus は、Standard と [Premium](service-bus-premium-messaging.md) の各階層で提供されます。 作成する Service Bus サービスの名前空間ごとにサービス階層を選択でき、その名前空間内で作成されるすべてのエンティティにこの階層選択が適用されます。

> [!NOTE]
> Service Bus の現在の価格については、「[Azure Service Bus の価格](https://azure.microsoft.com/pricing/details/service-bus/)」および「[Service Bus に関する FAQ](service-bus-faq.md#pricing)」を参照してください。
>
>

Service Bus では、キューとトピック/サブスクリプションに次の 2 つのメーターが使用されます。

1. **メッセージング操作**: キューまたはトピック/サブスクリプションのサービス エンドポイントに対する API 呼び出しとして定義されます。 このメーターは、キューおよびトピック/サブスクリプションの課金対象の使用における主要な単位として、送受信されるメッセージを置き換えます。
2. **仲介型接続**: 特定の 1 時間のサンプリング期間中にキュー、トピックまたはサブスクリプションに対して開かれた永続的接続の最大数として定義されます。 このメーターは Standard 階層においてのみ適用され、接続ごとの標準料金に対して追加の接続を開くことができます (以前は、接続はキュー/トピック/サブスクリプションあたり 100 に制限されていました)。

**Standard** 階層ではキューおよびトピック/サブスクリプションで実行される操作に対する段階的料金が導入され、最高の使用レベルでボリューム ベースの割引は最高 80% になります。 また、Standard 階層には 1 か月あたり 10 ドルの基本料金があり、1 か月間に 1250 万操作を追加コストなしで実行できます。

**Premium** 階層では、各顧客のワークロードが個別に実行されるように、CPU およびメモリ層でのリソースの分離が提供されます。 このリソースのコンテナーを、*メッセージング ユニット*と呼びます。 各 Premium 名前空間には、1 つ以上のメッセージング ユニットが割り当てられます。 Service Bus の Premium 名前空間ごとに、1 個、2 個、または 4 個のメッセージング ユニットを購入できます。 1 つのワークロードまたはエンティティは、複数のメッセージング ユニットにまたがることができます。課金は 24 時間単位、すなわち日単位ですが、メッセージング ユニットの数は自由に変更できます。 その結果、Service Bus ベースのソリューションのパフォーマンスは、予測可能で反復可能になります。 このパフォーマンスは、より予測可能かつ利用可能なだけでなく、より高速です。

> [!NOTE]
> トピックとサブスクリプションは、Standard または Premium 価格レベルでのみ利用できます。Basic レベルでは、キューのみがサポートされます。

Standard レベルの基本料金は、Azure サブスクリプションごとに毎月 1 回だけ課金されます。 これはつまり、単一の Standard 階層の Service Bus 名前空間を作成した後、追加の基本料金なしで、同じ Azure サブスクリプション下で Standard 名前空間を何個でも作成できます。

[Service Bus の価格](https://azure.microsoft.com/pricing/details/service-bus/)表は、Standard 階層と Premium 階層の機能の違いをまとめたものです。

## <a name="messaging-operations"></a>メッセージング操作

キューおよびトピック/サブスクリプションは、メッセージ単位ではなく、"操作" 単位で課金されます。 操作とは、キューまたはトピック/サブスクリプションのサービス エンドポイントに対して行われたすべての API 呼び出しのことです。 これには、管理、送信/受信、およびセッション状態の操作が含まれます。

| 操作の種類 | 説明 |
| --- | --- |
| 管理 |キューまたはトピック/サブスクリプションに対する作成、読み取り、更新、削除 (CRUD)。 |
| メッセージング |キューまたはトピック/サブスクリプションでメッセージを送受信する。 |
| セッションの状態 |キューまたはトピック/サブスクリプションでセッション状態を取得または設定する。 |

コストの詳細については、「[Service Bus の価格](https://azure.microsoft.com/pricing/details/service-bus/)」のページに記載されている価格を参照してください。

## <a name="brokered-connections"></a>仲介型接続

*仲介型接続*は、キュー、トピックまたはサブスクリプションに対して "永続的に接続された" 多数の送信者/受信者を含む使用パターンに対応します。 永続的に接続された送信者/受信者とは、0 以外の受信タイムアウトで AMQP または HTTP を使用して接続しているユーザーです (たとえば、HTTP ロング ポーリング)。 即時タイムアウトする HTTP の送信者および受信者は、仲介型接続になりません。

接続クォータおよびその他のサービスの制限については、「[Service Bus のクォータ](service-bus-quotas.md)」を参照してください。 仲介型接続の詳細については、このトピックの後の [「FAQ」](#faq)セクションをご覧ください。

Standard 階層では名前空間単位での仲介型接続の制限がなくなり、Azure サブスクリプション全体で仲介型接続の使用量が集計されます。 詳細については、「[仲介型接続](https://azure.microsoft.com/pricing/details/service-bus/)」の表を参照してください。

> [!NOTE]
> Standard メッセージング階層 (の基本料金) には 1,000 の仲介型接続が含まれ、関連付けられている Azure サブスクリプションのすべてのキュー、トピックまたはサブスクリプションで共有できます。
>
>

<br />

> [!NOTE]
> 課金はピーク時の同時接続数に基づき、744 時間/月として時間あたりに配分されます。
>
>

### <a name="premium-tier"></a>Premium 階層

Premium 階層では、仲介型接続は料金がかかりません。

## <a name="faq"></a>FAQ

### <a name="what-are-brokered-connections-and-how-do-i-get-charged-for-them"></a>仲介型接続とはどのようなもので、どのように課金されますか

仲介型接続は、次のいずれかとして定義されます。

1. クライアントから Service Bus のキュー、またはトピック/サブスクリプションへの AMQP 接続。
2. Service Bus のトピックまたはキューからメッセージを受信するための、受信タイムアウト値が 0 より大きい HTTP 呼び出し。

階層に含まれる数量 (Standard 階層では 1,000) を超える分のピーク時同時仲介型接続数に対して、Service Bus により課金されます。 ピークは時間単位で測定され、1 か月を 744 時間として按分されて、毎月の請求期間に追加されます。 含まれる数量 (1 か月あたり 1,000 仲介型接続) は、請求期間の最後に、按分された時間単位ピークの合計に対して適用されます。

例: 

1. 10,000 台のデバイスが単一の AMQP 接続経由で接続し、Service Bus トピックからコマンドを受け取ります。 デバイスは、遠隔測定イベントを Event Hub に送信します。 すべてのデバイスが毎日 12 時間接続する場合、次の接続料金が適用されます (他のすべての Service Bus トピック料金に加えて): 10,000 接続 * 12 時間 * 31 日 / 744 = 5,000 仲介型接続 基本料金に含まれる 1,000 仲介型接続を除く 4,000 仲介型接続に対して 0.03 ドル/仲介型接続が課金され、合計 120 ドルになります。
2. 10,000 台のデバイスが、タイムアウトが 0 ではない HTTP 経由で Service Bus キューからメッセージを受信します。 すべてのデバイスが毎日 12 時間接続する場合、接続料金は次のようになります (他のすべての Service Bus 料金に加えて): 10,000 HTTP 受信接続 * 12 時間/日 * 31 日 / 744 時間 = 5,000 仲介型接続

### <a name="do-brokered-connection-charges-apply-to-queues-and-topicssubscriptions"></a>仲介型接続の料金はキューおよびトピック/サブスクリプションには適用されますか

はい。 送信側システムまたはデバイスの数に関係なく、HTTP を使用したベントの送信には接続料金は発生しません。 0 より大きいタイムアウトを使用する HTTP の受信イベント ("ロング ポーリング" とも呼ばれます) では、仲介型接続料金が発生します。 AMQP 接続では、接続が送信または受信のどちらに使用されるかに関係なく、仲介型接続料金が発生します。 Azure サブスクリプションのすべての Standard 名前空間全体で最初の 1,000 個の仲介型接続は、(基本料金を超える) 追加料金なしで含まれます。 これは多くのサービス間メッセージング シナリオに対して十分であり、通常では、仲介型接続料金は多数のクライアントで AMQP または HTTP のロング ポーリングを使用する場合にのみ発生します。たとえば、より効率的なイベントストリーミングを行う場合、あるいは多数のデバイスまたはアプリケーション インスタンスとの双方向通信を有効にする場合などです。

## <a name="next-steps"></a>次の手順

* Service Bus の価格の詳細については、[Service Bus の価格に関するページ](https://azure.microsoft.com/pricing/details/service-bus/)を参照してください。
* Service Bus の価格および課金に関してよく寄せられる質問については、[Service Bus に関する FAQ](service-bus-faq.md#pricing) をご覧ください。

[Azure portal]: https://portal.azure.com
