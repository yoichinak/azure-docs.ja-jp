---
title: Text Analytics REST API でキー フレーズを抽出する方法 (Azure 上の Microsoft Cognitive Services) | Microsoft Docs
description: このチュートリアルでは、Azure 上の Microsoft Cognitive Services の Text Analytics REST API を使用して、キー フレーズを抽出する方法を説明します。
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 3/07/2018
ms.author: heidist
ms.openlocfilehash: 78b100e737242fa9f56e50275ef2038d8895349e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373872"
---
# <a name="how-to-extract-key-phrases-in-text-analytics"></a>Text Analytics でキー フレーズを抽出する方法

[キー フレーズ抽出](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) API は、非構造化テキストを評価し、各 JSON ドキュメントに対してキー フレーズのリストを返します。 

この機能は、ドキュメントのコレクション内の要点をすばやく特定する必要がある場合に便利です。 たとえば、「食べ物はおいしくて、すばらしいスタッフがいた」というテキストを入力すると、このサービスは話題の中心として "食べ物" と "すばらしいスタッフ" を返します。

現時点では、キー フレーズ抽出では、英語、ドイツ語、スペイン語、および日本語がサポートされています。 その他の言語はプレビュー段階です。 詳細については、[サポートされている言語](../text-analytics-supported-languages.md)に関するページを参照してください。

## <a name="preparation"></a>準備

キー フレーズの抽出は、操作するテキストのチャンクが大きい場合に最適です。 感情分析では逆で、テキスト ブロックが 小さいほどパフォーマンスが向上します。 両方の操作から最良の結果を得るには、両方の操作に応じて入力を再構築することを検討してください。

JSON ドキュメントは、id、text、language の形式である必要があります。

ドキュメントのサイズは、ドキュメントごとに 5,000 文字未満である必要があり、コレクションあたり最大 1,000 の項目 (ID) を含めることができます。 コレクションは、要求の本文で送信されます。 次の例では、キー フレーズの抽出用に送信するコンテンツを示しています。

```
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },                
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```    
    
## <a name="step-1-structure-the-request"></a>手順 1: 要求を構造化する

要求定義の詳細については、[Text Analytics API を呼び出す方法](text-analytics-how-to-call-api.md)に関するページを参照してください。 確認に便利なように、以下に再度、要点を示します。

+ **POST** 要求を作成します。 この要求については次の API ドキュメントを確認してください: [Key Phrases API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)

+ キー フレーズ抽出用の HTTP エンドポイントを設定します。 そこには、`/keyphrases` リソースが含まれている必要があります: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases`

+ Text Analytics 操作用のアクセス キーが含まれるように要求ヘッダーを設定します。 詳細については、[エンドポイントを見つけてアクセス キーにアクセスする方法](text-analytics-how-to-access-key.md)についてのページを参照してください。

+ 要求本文で、この分析のために準備した JSON ドキュメントのコレクションを提供します。

> [!Tip]
> [Postman](text-analytics-how-to-call-api.md) を使用するか、[ドキュメント](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)に記載されている **API テスト コンソール**を開き、要求を構造化して POST でサービスに投稿します。

## <a name="step-2-post-the-request"></a>手順 2: 要求を投稿する

要求が受信されると分析が実行されます。 サービスは、1 分あたり最大 100 個の要求を受け付けます。 各要求の最大サイズは 1 MB です。

サービスはステートレスであることを思い出してください。 ユーザーのアカウントに保存されるデータはありません。 結果はすぐに、応答で返されます。

## <a name="step-3-view-results"></a>手順 3: 結果を表示する

すべての POST 要求で、ID と検出されたプロパティを含む JSON 形式の応答が返されます。

出力はすぐに返されます。 結果は、JSON を受け付けるアプリケーションにストリームするか、ローカル システム上のファイルに出力を保存してから、そのファイルを、データの並べ替え、検索、および操作が可能なアプリケーションにインポートすることができます。

キー フレーズの抽出の出力例を次に示します。

```
    "documents": [
        {
            "keyPhrases": [
                "year",
                "trail",
                "trip",
                "views"
            ],
            "id": "1"
        },
        {
            "keyPhrases": [
                "marked trails",
                "Worst hike",
                "goners"
            ],
            "id": "2"
        },
        {
            "keyPhrases": [
                "trail",
                "small children",
                "family"
            ],
            "id": "3"
        },
        {
            "keyPhrases": [
                "spectacular views",
                "trail",
                "area"
            ],
            "id": "4"
        },
        {
            "keyPhrases": [
                "places",
                "beautiful views",
                "favorite trail"
            ],
            "id": "5"
        }
```

前述のように、アナライザーは重要ではない単語を検索して破棄し、文の主語または目的語と思われる 1 つの用語または語句を保持します。 

## <a name="summary"></a>まとめ

この記事では、Cognitive Services の Text Analytics を使用するキー フレーズ抽出の概念とワークフローについて説明しました。 要約すると:

+ 選択した言語に対して、[キー フレーズ抽出 API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) を使用できます。
+ 要求本文内の JSON ドキュメントには、id、text、および language のコードが含まれます。
+ POST 要求は、ユーザーのサブスクリプションで有効な、個人用に設定された[アクセス キーとエンドポイント](text-analytics-how-to-access-key.md)を使用して `/keyphrases` エンドポイントに対して行われます。
+ ドキュメント ID ごとのキーワードやキーフレーズで構成される応答出力は、Excel や Power BI を含む JSON を受け取るすべてのアプリにストリーミングすることができます。

## <a name="see-also"></a>関連項目 

 [Text Analytics の概要](../overview.md)  
 [よく寄せられる質問 (FAQ)](../text-analytics-resource-faq.md)</br>
 [Text Analytics 製品ページ](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Text Analytics API](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)
