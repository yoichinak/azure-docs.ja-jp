---
title: Project URL Preview の Node.js のクイック スタート - Microsoft Cognitive Services | Microsoft Docs
description: Azure 上で Microsoft Cognitive Services の URL Preview の使用を開始します。
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/16/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 195033d2740b11873baae095cec028dc8d19ce49
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376245"
---
# <a name="url-preview-node-quickstart"></a>URL Preview の Node のクイック スタート

次の Node の例では、SwiftKey Web サイト (https://swiftkey.com/en) の URL プレビューを作成します。

## <a name="prerequisites"></a>前提条件

[Cognitive Services Labs](https://aka.ms/answersearchsubscription) で無料試用版のアクセス キーを取得します

## <a name="code-scenario"></a>シナリオのコードを書く 

次のコードを使用して URL Preview データを取得します。
これは、次の手順で実装されます。
1. エンドポイントをホストとパスで指定する変数を宣言します。
2. プレビューするクエリ URL を指定し、クエリ パラメーターを追加します。  
3. 応答のハンドラー関数を作成します。
4. 要求を作成し、*Ocp-Apim-Subscription-Key* ヘッダーを追加する検索関数を定義します。
5. 検索機能を実行します。 

このデモの完全なコードを次に示します。

````
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'YOUR-ACCESS-KEY'; 
let host = 'api.labs.cognitive.microsoft.com';
let path = '/urlpreview/v7.0/search';

let mkt = 'en-US';
let q = 'https://swiftkey.com/';

let params = '?q=' + encodeURI(q);

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

````

## <a name="next-steps"></a>次の手順
- [C# のコード例](csharp.md)
- [Java のクイック スタート](java-quickstart.md)
- [JavaScript のクイック スタート](javascript.md)
- [Python のクイック スタート](python-quickstart.md)