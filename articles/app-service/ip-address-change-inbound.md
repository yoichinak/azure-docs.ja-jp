---
title: 受信 IP アドレスの変更に備える方法 - Azure
description: 受信 IP アドレスの変更が予定されている場合に、変更後もアプリが動作し続けるようにするための方法を説明します。
services: app-service\web
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 06/28/2018
ms.author: cephalin
ms.openlocfilehash: 28741e858b0c938ec8b2b2ff983106c6b08e18fc
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39578220"
---
# <a name="how-to-prepare-for-an-inbound-ip-address-change"></a>受信 IP アドレスの変更に備える方法

お使いの Azure App Service アプリの受信 IP アドレスが変更されるという通知を受け取った場合は、この記事の手順に従います。

## <a name="determine-if-you-have-to-do-anything"></a>何もする必要がないかどうかを判断する

* オプション 1: App Service アプリにカスタム ドメインがない場合は、何のアクションも必要ありません。

* オプション 2: ドメイン登録ポータル (サードパーティの DNS プロバイダーまたは Azure DNS) で CNAME レコード (URI をポイントしている DNS レコード) のみが構成されている場合は、何のアクションも必要ありません。

* オプション 3: ドメイン登録ポータル (サードパーティの DNS プロバイダーまたは Azure DNS) で A レコード (IP アドレスを直接ポイントしている DNS レコード) が構成されている場合は、既存の IP アドレスを新しい IP アドレスに置き換えます。 新しい IP アドレスは、次のセクションの手順に従って確認できます。

* オプション 4: アプリケーションが、アプリの IP アドレスを必要とするロード バランサー、IP フィルター、その他の IP メカニズムの背後にある場合は、既存の IP アドレスを新しい IP アドレスで置き換えます。 新しい IP アドレスは、次のセクションの手順に従って確認できます。

## <a name="find-the-new-inbound-ip-address-in-the-azure-portal"></a>Azure portal で新しい受信 IP アドレスを見つける

アプリに割り当てられる新しい受信 IP アドレスは、ポータルの **[仮想 IP アドレス]** フィールドで確認できます。 現在はこの新しい IP アドレスと古い IP アドレスの両方がアプリに接続されていますが、後で古い IP アドレスが切断されます。

1.  [Azure Portal](https://portal.azure.com)を開きます。

2.  左側のナビゲーション メニューで、**[App Services]** を選択します。

3.  一覧から App Service アプリを選択します。

4.  アプリが関数アプリの場合、「[Function app inbound IP addresses](../azure-functions/ip-addresses.md#function-app-inbound-ip-address)」 (関数アプリの受信 IP アドレス) を参照してください。

4.  **[設定]** ヘッダーの下で、左側のナビゲーションにある **[プロパティ]** をクリックし、**[仮想 IP アドレス]** というラベルのセクションを見つけます。

5. この IP アドレスをコピーし、ドメインのレコードや IP メカニズムを再構成します。

## <a name="next-steps"></a>次の手順

この記事では、Azure によって開始された IP アドレスの変更に備える方法について説明しました。 Azure App Service での IP アドレスの詳細については、「[Azure App Service における受信 IP アドレスと送信 IP アドレス](app-service-ip-addresses.md)」を参照してください。
