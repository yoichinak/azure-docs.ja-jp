---
title: アプリケーションの登録 - Azure Cognitive Services | Microsoft Docs
description: Azure Custom Decision Service で新しいアプリを登録する方法に関するステップバイステップ ガイド
services: cognitive-services
author: slivkins
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/09/2018
ms.author: slivkins
ms.reviewer: marcozo;alekh;marossi
ms.openlocfilehash: 2aa8fbe77c11df4434eefa4c92d8529d5ca1d885
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376917"
---
# <a name="register-your-application"></a>アプリケーションの登録

アプリケーションで Custom Decision Service を使用するには、ポータルでこのサービスを登録します。 この記事では、その方法について説明します。

1. Custom Decision Service の[フロント ページ](https://ds.microsoft.com/)に移動します。 次の図に強調表示されているように、リボンの **[My Portal]\(マイ ポータル\)** をクリックします。

    ![[My Portal]\(マイ ポータル\)](./media/portal.png)

    まだサインインしていない場合は、[Microsoft アカウント](https://account.microsoft.com/account)を使用してサインインするように求められます。 ポータルにサインインすると、Microsoft アカウントがページの右上隅に表示されます。

2. アプリケーションを追加するために、**[新しいアプリ]** ボタンをクリックします。

3. ダイアログ ボックスで、アプリケーションのアプリ ID を選択します。 Custom Decision Service では、アプリケーションごとに一意の ID が必要です。 この ID が既に他のユーザーによって使用されている場合は、別の ID を選択するように求められます。

4. Action Set API を指定します。 この設定は、アプリケーションの利用可能なコンテンツを Custom Decision Service に伝達する RSS または Atom フィードです。 フィードの名前を入力し、提供元の URL を入力します。 この手順を後で実行するには、**[フィード]** ボタンをクリックし、**[新しいフィード]** ボタンをクリックします。 RSS フィードを作成する例については後述します。

5. アプリケーションを登録するために、左下隅にある **[カスタム アプリ]** チェック ボックスをオンにします。 アプリケーション データが記録されている Azure ストレージ アカウントの[接続文字列](../../storage/common/storage-configure-connection-string.md)を入力します。 ストレージ アカウントの作成方法の詳細については、[ストレージ アカウントを作成、管理、削除する方法](../../storage/common/storage-create-storage-account.md)に関するページを参照してください。

### <a name="next-steps"></a>次の手順

* [Web ページ](custom-decision-service-get-started-browser.md)や[スマートフォン アプリ](custom-decision-service-get-started-app.md)の最適化を開始します。
* [チュートリアル](custom-decision-service-tutorial-news.md)に従って、より詳細な例を見ていきます。
* 提供される機能の詳細については、[API リファレンス](custom-decision-service-api-reference.md)を参照してください。