---
title: Bing が返す Web 回答をフィルタリングする | Microsoft Docs
description: Bing Web Search API が返す回答を responseFilter を使用してフィルタリングする方法を示します。
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 8B837DC2-70F1-41C7-9496-11EDFD1A888D
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 01/12/2017
ms.author: scottwhi
ms.openlocfilehash: a5ee6241630ee24a05c2c4b932453bd7946a7508
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374760"
---
# <a name="filtering-the-answers-that-the-search-response-includes"></a>検索応答に含まれる回答をフィルタリングする  

Web のクエリを実行すると、Bing は検索に関連すると考えられるすべてのコンテンツを返します。 たとえば、検索クエリが "sailing+dinghies" の場合、応答に次のような回答が含まれる可能性があります。

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43C...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    },
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA5CA6464E5D...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [...]
        }
    }
}    
```

画像、ビデオ、ニュースなど、特定の種類のコンテンツに関心がある場合は、[responseFilter](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#responsefilter) クエリ パラメーターを使用して、その種類の回答のみを要求することができます。 Bing は、指定された回答に関連するコンテンツを検出すると、それを返します。 応答フィルターは、コンマ区切りの回答の一覧です。 次の例は、`responseFilter` を使用して「sailing dinghies」の画像、ビデオ、ニュースを要求する方法を示しています。 クエリ文字列をエンコードすると、コンマは %2C になります。  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&responseFilter=images%2Cvideos%2Cnews&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

前のクエリに対する応答を次に示します。 ご覧のように、Bing は関連するビデオやニュースの結果は見つけていないので、それらは応答に含まれていません。

```json
{
    "_type" : "SearchResponse",
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3AD78B183C56456C...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "Images",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images"
                }
            }]
        }
    }
}
```

前の応答では、Bing はビデオやニュースの結果を返しませんでしたが、ビデオやニュースのコンテンツが存在しないということではありません。 単に、それらがページに含まれていなかったことを意味します。 ただし、[ページを移動して](./paging-webpages.md)他の結果を表示すれば、後続のページには含まれている可能性があります。 また、直接 [Video Search API](../bing-video-search/search-the-web.md) や [News Search API](../bing-news-search/search-the-web.md) のエンドポイントを呼び出すと、結果が応答に含まれる可能性があります。 

単一の API から結果を取得するために `responseFilter` を使うことはお勧めできません。 単一の Bing API からコンテンツを取得する場合は、その API を直接呼び出します。 たとえば、イメージのみを受信するには、Image Search API のエンドポイントである `https://api.cognitive.microsoft.com/bing/v7.0/images/search`、またはその他の[イメージ](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#endpoints) エンドポイントのいずれかに要求を送信します。 単一の API を呼び出すことが重要なのは、パフォーマンスが向上するためだけでなく、コンテンツに固有の API の方がより詳細な結果を提供するためです。 たとえば、Web Search API では使用できないフィルターを使用して結果をフィルタリングすることができます。  
  
特定のドメインから検索結果を得るには、クエリ文字列に `site:` クエリ演算子を含めます。  

```
https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us
```

> [!NOTE] 
> クエリによっては、`site:` クエリ演算子を使用した場合、[safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#safesearch) 設定にかかわらず、成人向けのコンテンツが応答に含まれることがあります。 `site:` の使用は、そのサイト上のコンテンツを承知していて、なおかつ成人向けのコンテンツが含まれていても問題がないシナリオに限定してください。 
  
## <a name="limiting-the-number-of-answers-in-the-response"></a>応答の回答数を制限する

Bing は、ランキングに基づいて応答に回答を含めます。 たとえば、*sailing+dinghies* クエリを実行すると、Bing は `webpages`、`images`、`videos`、および `relatedSearches` を返します。

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "relatedSearches" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

Bing が返す回答数を上位 2 つの回答 (Web ページと画像) に制限するには、[answerCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#answercount) クエリ パラメーターを 2 に設定します。 

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

応答には、`webPages` と `images` のみが含まれます。

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "rankingResponse" : {...}
}
```

上のクエリに `responseFilter` クエリ パラメーターを追加して Web ページとニュースを設定すると、ニュースがランク付けされなくなり、応答には Web ページのみが含まれます。

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "rankingResponse" : {...}
}
```

## <a name="promoting-answers-that-are-not-ranked"></a>ランク付けされない回答を昇格する

クエリに対して Bing から返される上位ランクの回答が Web ページ、画像、ビデオ、および relatedSearches である場合、応答にはこれらの回答が含まれます。 [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#answercount) を 2 に設定した場合、Bing はランク付けされた上位の 2 つの回答である Web ページと画像を返します。 Bing の応答に画像とビデオを含めたい場合は、[promote](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#promote) クエリ パラメーターを指定し、画像とビデオを設定します。 

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&promote=images%2Cvideos&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

上の要求への応答は次のようになります。 Bing は、上位 2 つの回答である Web ページと画像を返し、ビデオを昇格させて回答に含めます。

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailiing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

`promote` にニュースを設定しても、ニュースの回答はランク付けされていないので、応答にニュースの回答は含まれません。昇格できるのは、ランク付けされている回答のみです。

昇格させる回答は、`answerCount` の制限にはカウントされません。 たとえば、ランク付けされた回答がニュース、画像、およびビデオで、`answerCount` を 1、`promote` をニュースに設定した場合、応答にはニュースと画像が含まれます。 または、ランク付けされた回答がビデオ、画像、およびニュースである場合、応答にはビデオとニュースが含まれます。

`promote` は、`answerCount` クエリ パラメーターを指定する場合のみ使用できます。
