---
title: Bing Image Search シングルページ Web アプリ | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: シングルページ Web アプリケーションで Bing Image Search API を使用する方法を説明します。
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 10/04/2017
ms.author: v-brapel
ms.openlocfilehash: 303d7745167d2ea25fda083ed99881ac4e0a7ec7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376413"
---
# <a name="tutorial-visual-search-single-page-web-app"></a>チュートリアル: Visual Search シングルページ Web アプリ

Bing Visual Search API は、Bing.com/images に表示される画像の詳細のようなエクスペリエンスを提供します。 Visual Search を使うと、画像を指定して、見た目が似た画像、ショッピング ソース、画像を含む Web ページなど、画像に関する分析情報を取得できます。 

このチュートリアルでは、Bing Image Search チュートリアルのシングルページ Web アプリを拡張しています ([シングルページ Web アプリ](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md)に関するページを参照)。 このチュートリアルを開始するための完全なソース コードについては、[シングルページ Web アプリ (ソース コード)](../Bing-Image-Search/tutorial-bing-image-search-single-page-app-source.md) に関するページを参照してください。 このチュートリアルの最終的なソース コードについては、[Visual Search シングルページ Web アプリ](tutorial-bing-visual-search-single-page-app-source.md)に関するページを参照してください。

説明するタスクは次のとおりです。

> [!div class="checklist"]
> * 画像の分析情報トークンを使用して Bing Visual Search API を呼び出す
> * 類似した画像を表示する

## <a name="call-bing-visual-search"></a>Bing Visual Search を呼び出す
Bing Image Search のチュートリアルを編集し、スクリプト要素末尾の 409 行目に次のコードを追加します。 このコードは、Bing Visual Search API を呼び出して、結果を表示します。

``` javascript
function handleVisualSearchResponse(){
    if(this.status !== 200){
        console.log(this.responseText);
        return;
    }
    let json = this.responseText;
    let response = JSON.parse(json);
    for (let i =0; i < response.tags.length; i++) {
        let tag = response.tags[i];
        if (tag.displayName === '') {
            for (let j = 0; j < tag.actions.length; j++) {
                let action = tag.actions[j];
                if (action.actionType === 'VisualSearch') {
                    let html = '';
                    for (let k = 0; k < action.data.value.length; k++) {
                        let item = action.data.value[k];
                        let height = 120;
                        let width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
                        html += "<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + "' height=" + height + " width=" + width + "'>";
                    }
                    showDiv("insights", html);
                    window.scrollTo({top: document.getElementById("insights").getBoundingClientRect().top, behavior: "smooth"});
                }
            }
        }
    }
}

function bingVisualSearch(insightsToken){
    let visualSearchBaseURL = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch',
        boundary = 'boundary_ABC123DEF456',
        startBoundary = '--' + boundary,
        endBoundary = '--' + boundary + '--',
        newLine = "\r\n",
        bodyHeader = 'Content-Disposition: form-data; name="knowledgeRequest"' + newLine + newLine;

    let postBody = {
        imageInfo: {
            imageInsightsToken: insightsToken
        }
    }
    let requestBody = startBoundary + newLine;
    requestBody += bodyHeader;
    requestBody += JSON.stringify(postBody) + newLine + newLine;
    requestBody += endBoundary + newLine;       
    
    let request = new XMLHttpRequest();

    try {
        request.open("POST", visualSearchBaseURL);
    } 
    catch (e) {
        renderErrorMessage("Error performing visual search: " + e.message);
    }
    request.setRequestHeader("Ocp-Apim-Subscription-Key", getSubscriptionKey());
    request.setRequestHeader("Content-Type", "multipart/form-data; boundary=" + boundary);
    request.addEventListener("load", handleVisualSearchResponse);
    request.send(requestBody);
}
```

## <a name="capture-insights-token"></a>分析情報トークンをキャプチャする
次のコードを 151 行目の `searchItemsRenderer` オブジェクトに追加します。 このコードは、クリックされたときに `bingVisualSearch` 関数を呼び出す **find similar** リンクを追加します。 この関数は引数として imageInsightsToken を受け取ります。

``` javascript
html.push("<a href='javascript:bingVisualSearch(\"" + item.imageInsightsToken + "\");'>find similar</a><br>");
```

## <a name="display-similar-images"></a>類似した画像を表示する
次の HTML コードを 601 行目に追加します。 このマークアップ コードは、Bing Visual Search API 呼び出しの結果の表示に使用される要素を追加します。

``` html
<div id="insights">
    <h3><a href="#" onclick="return toggleDisplay('_insights')">Similar</a></h3>
    <div id="_insights" style="display: none"></div>
</div>
```

新しい JavaScript コードと HTML 要素をすべて配置すると、検索結果が **find similar** リンクと共に表示されます。 このリンクをクリックすると、選択した画像に類似した画像が **[類似]** セクションに表示されます。 場合によっては、画像を表示するために **[類似]** セクションを展開する必要があります。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Visual Search シングルページ Web アプリのソース](tutorial-bing-visual-search-single-page-app-source.md)
> [Bing Visual Search API リファレンス](https://aka.ms/bingvisualsearchreferencedoc)