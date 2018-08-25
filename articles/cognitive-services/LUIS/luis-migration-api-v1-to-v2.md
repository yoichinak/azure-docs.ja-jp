---
title: v1 から v2 への API 移行ガイド | Microsoft Docs
titleSuffix: Azure
description: 最新の API セットに移行する方法を説明します。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/01/2018
ms.author: diberry
ms.openlocfilehash: cb68ad57302f151975c1ca6616a24db9804a6a68
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223057"
---
# <a name="api-v2-migration-guide"></a>API v2 移行ガイド
バージョン 1 の[エンドポイント](https://aka.ms/v1-endpoint-api-docs)と[オーサリング](https://aka.ms/v1-authoring-api-docs) API は非推奨となる予定です。 このガイドでは、バージョン 2 の[エンドポイント](https://aka.ms/luis-endpoint-apis)と[オーサリング](https://aka.ms/luis-authoring-apis) API に移行する方法について説明します。 

## <a name="new-azure-regions"></a>新しい Azure リージョン
LUIS では、LUIS API 用の新しい[リージョン](https://aka.ms/LUIS-regions)が用意されています。 LUIS はリージョン グループごとに異なる Web サイトを提供します。 アプリケーションは、クエリを実行するリージョンと同じリージョンに作成する必要があります。 アプリケーションによって、リージョンが自動的に移行されることはありません。 アプリはリージョンからエクスポートして、新しいリージョンで使用できるようにインポートします。

## <a name="authoring-route-changes"></a>オーサリング ルートの変更
オーサリング API ルートは、前は **prog** ルートが使用されていましたが、**api** ルートを使用するように変更されました。


| version | ルート |
|--|--|
|1|/luis/v1.0/**prog**/apps|
|2|/luis/**api**/v2.0/apps|


## <a name="endpoint-route-changes"></a>エンドポイント ルートの変更
エンドポイント API には、新しい querystring パラメーターと、異なる応答があります。 verbose フラグが true の場合、topScoringIntent に加え、すべての意図が、スコアに関係なく intents という名前の配列で返されます。

| version | GET ルート |
|--|--|
|1|/luis/v1/application?ID={appId}&q={q}|
|2|/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]|


v1 エンドポイントの成功応答:
```JSON
{
  "odata.metadata":"https://dialogice.cloudapp.net/odata/$metadata#domain","value":[
    {
      "id":"bccb84ee-4bd6-4460-a340-0595b12db294","q":"turn on the camera","response":"[{\"intent\":\"OpenCamera\",\"score\":0.976928055},{\"intent\":\"None\",\"score\":0.0230718572}]"
    }
  ]
}
```

v2 エンドポイントの成功応答:
```JSON
{
  "query": "forward to frank 30 dollars through HSBC",
  "topScoringIntent": {
    "intent": "give",
    "score": 0.3964121
  },
  "entities": [
    {
      "entity": "30",
      "type": "builtin.number",
      "startIndex": 17,
      "endIndex": 18,
      "resolution": {
        "value": "30"
      }
    },
    {
      "entity": "frank",
      "type": "frank",
      "startIndex": 11,
      "endIndex": 15,
      "score": 0.935219169
    },
    {
      "entity": "30 dollars",
      "type": "builtin.currency",
      "startIndex": 17,
      "endIndex": 26,
      "resolution": {
        "unit": "Dollar",
        "value": "30"
      }
    },
    {
      "entity": "hsbc",
      "type": "Bank",
      "startIndex": 36,
      "endIndex": 39,
      "resolution": {
        "values": [
          "BankeName"
        ]
      }
    }
  ]
}
```

## <a name="key-management-no-longer-in-api"></a>API でのキー管理が不要
サブスクリプション エンドポイント キー API は非推奨であり、410 GONE を返します。

| version | ルート |
|--|--|
|1|/luis/v1.0/prog/subscriptions|
|1|/luis/v1.0/prog/subscriptions/{subscriptionKey}|

Azure Portal で Azure [エンドポイント キー](luis-how-to-azure-subscription.md) が生成されます。 そのキーは、**[[Publish]\(公開\)](luis-how-to-manage-keys.md)** ページで LUIS アプリに割り当てます。 実際のキー値を確認する必要はありません。 LUIS では、サブスクリプション名を使用して、割り当てが行われます。 

## <a name="new-versioning-route"></a>新しいバージョン管理ルート
v2 モデルは、[バージョン](luis-how-to-manage-versions.md)に含まれています。 バージョン名は、ルートの 10 文字です。 既定のバージョンは "0.1" です。

| version | ルート |
|--|--|
|1|/luis/v1.0/**prog**/apps/{appId}/entities|
|2|/luis/**api**/v2.0/apps/{appId}/**versions**/{versionId}/entities|

## <a name="metadata-renamed"></a>メタデータ名の変更
LUIS メタデータを返す API のいくつかが新しい名前に変更されました。

| v1 ルート名 | v2 ルート名 |
|--|--|
|PersonalAssistantApps |assistants|
|applicationcultures|cultures|
|applicationdomains|domains|
|applicationusagescenarios|usagescenarios|


## <a name="sample-renamed-to-suggest"></a>"sample" から "suggest" への名前変更
LUIS では、モデルを強化する可能性がある既存の[エンドポイント発話](luis-how-to-review-endoint-utt.md)から、発話が提案されます。 以前のバージョンでは、その提案は **sample** でした。 新しいバージョンでは、名前が sample から **suggest** に変更されています。 これは、LUIS Web サイトでは**[エンドポイントの発話の確認](luis-how-to-review-endoint-utt.md)** と呼ばれます。

| version | ルート |
|--|--|
|1|/luis/v1.0/**prog**/apps/{appId}/entities/{entityId}/**sample**|
|1|/luis/v1.0/**prog**/apps/{appId}/intents/{intentId}/**sample**|
|2|/luis/**api**/v2.0/apps/{appId}/**versions**/{versionId}/entities/{entityId}/**suggest**|
|2|/luis/**api**/v2.0/apps/{appId}/**versions**/{versionId}/intents/{intentId}/**suggest**|


## <a name="create-app-from-prebuilt-domains"></a>事前構築済みドメインからのアプリの作成
[事前構築済みのドメイン](luis-how-to-use-prebuilt-domains.md)により、定義済みドメイン モデルが提供されます。 事前構築済みのドメインを使用すると、一般的なドメインの LUIS アプリケーションを迅速に開発できます。 この API により、事前構築済みのドメインに基づいて、新しいアプリを作成することができます。 応答は新しい appID です。

|v2 ルート|Verb|
|--|--|
|/luis/api/v2.0/apps/customprebuiltdomains  |get、post|
|/luis/api/v2.0/apps/customprebuiltdomains/{culture}  |get|

## <a name="importing-1x-app-into-2x"></a>1.x アプリから 2.x へのインポート
エクスポートされた 1.x アプリの JSON には、[LUIS][LUIS] 2.0 にインポートする前に変更しなければならない領域があります。 

### <a name="prebuilt-entities"></a>事前構築済みのエンティティ 
[事前構築済みのエンティティ](luis-prebuilt-entities.md)は変更されました。 V2 の事前構築済みのエンティティが使用されていることを確認してください。 これには、datetime の代わりの [datetimeV2](luis-prebuilt-entities.md#use-a-prebuilt-datetimev2-entity) の使用も含まれます。 

### <a name="actions"></a>アクション
アクション プロパティが有効ではなくなりました。 これは空にする必要があります 

### <a name="labeled-utterances"></a>ラベル付きの発話
V1 のラベル付きの発話では、単語またはフレーズの先頭と末尾にスペースを挿入できました。 そのスペースが削除されました。 

## <a name="common-reasons-for-http-response-status-codes"></a>HTTP 応答状態コードの一般的な理由
[LUIS API の応答コード](luis-reference-response-codes.md)に関するページをご覧ください。

## <a name="next-steps"></a>次の手順

v2 API のドキュメントを使用して、LIUS [エンドポイント](https://aka.ms/luis-endpoint-apis)および[オーサリング](https://aka.ms/luis-authoring-apis) API に対する既存の REST 呼び出しを更新します。 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions