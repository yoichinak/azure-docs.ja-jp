---
title: Bing Image Search API で Web から画像を取得する | Microsoft Docs
description: Web から必要な画像を検索して取得するには、Bing Image Search API を使います。
services: cognitive-services
author: aahill
manager: cgronlun
ms.assetid: AB1B9898-C94A-4B59-91A1-8623C94BA3D4
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: conceptual
ms.date: 8/8/2018
ms.author: aahi
ms.openlocfilehash: c379cc2dfbe53f83e4d79500cd947879407c8d55
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2018
ms.locfileid: "40082583"
---
# <a name="getting-images-from-the-web-with-the-bing-image-search-api"></a>Bing Image Search API で Web から画像を取得する

Bing Image Search REST API を使うと、次の GET 要求を送信することで、ユーザーの検索語句に関連した画像を Web から取得できます。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

> [!IMPORTANT]
> * すべての要求は、クライアントからではなく、サーバーから実行する必要があります。
> * いずれかの Bing Search API を初めて呼び出す場合は、クライアント ID ヘッダーを含めないでください。 クライアント ID を含めるのは、既に Bing API を呼び出してユーザーとデバイスの組み合わせに対応するクライアント ID が取得済みである場合だけです。
> * 画像は、応答に含まれている順序で表示する必要があります。

## <a name="getting-images-from-a-specific-web-domain"></a>特定の Web ドメインから画像を取得する

特定のドメインから画像を取得するには、[site:](http://msdn.microsoft.com/library/ff795613.aspx) というクエリ演算子を使用します。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

> [!NOTE]
> `site:` 演算子を使用したクエリへの応答には、[safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#safesearch) の設定に関係なく成人向けのコンテンツが含まれることがあります。 `site:` の使用は、対象となるドメイン上のコンテンツを把握している場合に限定してください。

次の例は、Bing が過去 1 週間以内に検出した小さな画像を ContosoSailing.com から取得する方法を示しています。  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="filtering-images"></a>画像のフィルタリング

 Images Search API の既定の動作では、クエリとの関連性が高い画像がすべて返されます。 背景が透明な画像や特定のサイズの画像のみを検索対象とするなど、Bing から返された画像をフィルタリングする必要がある場合は、次のクエリ パラメーターを使用してください。

* [aspect](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#aspect) - 画像を縦横比でフィルタリング (例: 標準またはワイド スクリーン画像など)
* [color](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#color) - ドミナント カラーや白黒で画像をフィルタリング
* [freshness](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#freshness) - 新しさで画像をフィルタリング (例: この 1 週間に Bing によって検出された画像など)
* [height](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#height)、[width](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#width) - 幅と高さで画像をフィルタリング
* [imageContent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagecontent) - 画像をコンテンツでフィルタリング (例: 人の顔が映った画像など)
* [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype) - 画像を種類でフィルタリング (例: クリップ アート、アニメーション GIF、透明な背景)
* [license](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#license) - サイトに関連付けられているライセンスの種類で画像をフィルタリング
* [size](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#size) - 画像をサイズでフィルタリング (最大 200x200 ピクセルの小さな画像など)

特定のドメインから画像を取得するには、[site:](http://msdn.microsoft.com/library/ff795613.aspx) というクエリ演算子を使用します。

    > [!NOTE]
    > Responses to queries using the `site:` operator may include adult content regardless of the [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#safesearch) setting. Only use `site:` if you are aware of the content on the domain.

次の例は、Bing が過去 1 週間以内に検出した小さな画像を ContosoSailing.com から取得する方法を示しています。  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="bing-image-search-response-format"></a>Bing Image Search の応答の形式

Bing からの応答メッセージには、クエリとの関連性が高いと Azure Cognitive Services が判断した画像のリストを格納する [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) 応答が含まれています。 このリスト内の各 [Image](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) オブジェクトには、画像の URL、サイズ、大きさ、エンコード形式、画像のサムネイルの URL、サムネイルの大きさなどなど、画像に関するさまざまな情報が含まれています。

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=73118C8B4E3...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP.GNarK7m...",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3687...",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "en.contoso.org\/wiki\/File:Rich_Passage...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "imageInsightsToken": "ccid_GNarK7ma*mid_CCF85447ADA6...",
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    },
    "imageId": "CCF85447ADA6FFF9E96E7DF0B796F7A86E34593",
    "accentColor": "376094"
},
```

Bing Image Search API を呼び出すと、結果のリストが Bing から返されます。 このリストは、クエリとの関連性が高い結果の総数のサブセットです。 表示できる画像の数の見積もりは、応答の `totalEstimatedMatches` フィールドに格納されます。 ページをめくるように残りの画像を表示する方法について詳しくは、[画像のページ移動](../paging-images.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

まだ Bing Image Search API を試していない方は、[クイック スタート](../quickstarts/csharp.md)をご覧ください。 さらに高度な内容をお求めの方は、[単一ページの Web アプリ](../tutorial-bing-image-search-single-page-app.md)の作成に関するチュートリアルをご覧ください。
