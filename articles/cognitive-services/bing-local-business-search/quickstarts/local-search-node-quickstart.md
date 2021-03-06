---
title: クイック スタート - Node.js を使用して API にクエリを送信する - Bing Local Business Search
titleSuffix: Azure Cognitive Services
description: このクイック スタートを利用して、Azure Cognitive Service である Bing Local Business Search API への要求の送信を開始します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 05/12/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 35d6aa7d606248796b3405d8fe1dabd64e0996d5
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2021
ms.locfileid: "102430107"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>クイック スタート:Node.js を使用して Bing Local Business Search API にクエリを送信する

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](/bing/search-apis/bing-web-search/create-bing-search-service-resource)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) に関するページを参照してください。

このクイックスタートを利用して、Azure Cognitive Service である Bing Local Business Search API への要求を送信する方法について説明します。 このシンプルなアプリケーションは Node.js で記述されていますが、この API は、HTTP 要求の発行と JSON の解析が可能な任意のプログラミング言語と互換性がある RESTful Web サービスです。

このサンプル アプリケーションでは、検索クエリについて、API からのローカルな応答データを取得します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)
* 最新バージョンの [Node.js](https://nodejs.org/en/download/)。
* [JavaScript Request ライブラリ](https://github.com/request/request)。
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title="Bing Search リソースを作成"  target="_blank">Bing Search リソースを作成</a>し、キーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** をクリックします。


## <a name="code-scenario"></a>シナリオのコードを書く

次のコードでは、要求を定義して送信します。この要求は、次の手順で実装されます。

1. エンドポイントをホストとパスで指定する変数を宣言します。
2. クエリを指定し、クエリ パラメーターを追加します。
3. 応答のハンドラー関数を作成します。
4. 要求を作成し、`Ocp-Apim-Subscription-Key` ヘッダーを追加する search 関数を定義します。
5. search 関数を実行します。


```javascript
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'your-access-key';

let host = 'api.cognitive.microsoft.com/bing';
let path = '/v7.0/localbusinesses/search';

let mkt = 'en-US';
let q = 'hotel in Bellevue';

let params = '?q=' + encodeURI(q) + "&mkt=" + mkt;

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let Search = function () {
    let request_params = {
        method: 'GET',
        hostname: host,
        path: path + params,
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey,
        }
    };

    let req = https.request(request_params, response_handler);
    req.end();
}

Search();

```

## <a name="next-steps"></a>次のステップ

* [Local Business Search (C#) のクイック スタート](local-quickstart.md)
* [Local Business Search (Java) のクイック スタート](local-search-java-quickstart.md)
* [Local Business Search (Python) のクイック スタート](local-search-python-quickstart.md)