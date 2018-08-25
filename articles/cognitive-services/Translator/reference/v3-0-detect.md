---
title: Microsoft Translator Text API Detect メソッド | Microsoft Docs
description: Microsoft Translator Text API Detect メソッドを使用します。
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 7e81e91230e1ada4423d77d22134b1b64df65d9d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377525"
---
# <a name="text-api-30-detect"></a>テキスト API 3.0: Detect

テキストの一部の言語を識別します。

## <a name="request-url"></a>要求 URL

`POST` 要求の送信先は次のとおりです。

```HTTP
https://api.cognitive.microsofttranslator.com/detect?api-version=3.0
```

## <a name="request-parameters"></a>要求パラメーター

クエリ文字列に渡される要求パラメーターを次に示します。

<table width="100%">
  <th width="20%">Query parameter (クエリ パラメーター)</th>
  <th>説明</th>
  <tr>
    <td>api-version</td>
    <td>*必須のパラメーター*。<br/>クライアントによって要求される API のバージョン。 値は `3.0` とする必要があります。</td>
  </tr>
</table> 

要求ヘッダーには次のものがあります。

<table width="100%">
  <th width="20%">headers</th>
  <th>説明</th>
  <tr>
    <td>_One authorization_<br/>_header_</td>
    <td>*必須の要求ヘッダー*。<br/>[認証に使用できるオプション](./v3-0-reference.md#authentication)に関するページを参照してください。</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>*必須の要求ヘッダー*。<br/>ペイロードのコンテンツ タイプを指定します。 次のいずれかの値になります。`application/json`</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>*必須の要求ヘッダー*。<br/>要求本文の長さです。</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*省略可能*。<br/>要求を一意に識別する、クライアントで生成された GUID。 `ClientTraceId` という名前のクエリ パラメーターを使用してクエリ文字列内にトレース ID を含める場合は、このヘッダーを省略できることに注目してください。</td>
  </tr>
</table> 

## <a name="request-body"></a>要求本文

要求の本文は JSON 配列です。 各配列要素は、`Text` という名前の文字列プロパティを持つ JSON オブジェクトです。 言語の検出は、`Text` プロパティの値に適用されます。 サンプルの要求本文は次のようになります。

```json
[
    { "Text": "Ich würde wirklich gern Ihr Auto um den Block fahren ein paar Mal." }
]
```

次の制限事項が適用されます。

* 配列に含めることができる要素は、最大でも 100 個です。
* 配列要素のテキスト値は、スペースも含めて 10,000 文字を超えてはなりません。
* 要求に含めるテキスト全体では、スペースも含めて 50,000 文字を超えてはなりません。

## <a name="response-body"></a>応答本文

正常な応答は、入力配列内の文字列ごとに 1 つの結果が含まれる JSON 配列となります。 結果オブジェクトには次のプロパティが含まれています。

  * `language`: 検出された言語のコード。

  * `score`: 結果内の信頼度を示す浮動小数点値。 スコアは 0 から 1 の範囲であり、低いスコアは低い信頼度を示します。

  * `isTranslationSupported`: 検出された言語がテキスト翻訳でサポートされている言語の 1 つである場合に true になるブール値。

  * `isTransliterationSupported`: 検出された言語が音訳でサポートされている言語の 1 つである場合に true になるブール値。
  
  * `alternatives`: 利用可能な他の言語の配列。 配列の各要素は、上記にリストしたのと同じプロパティ (`language`、`score`、`isTranslationSupported`、`isTransliterationSupported`) を持つ別のオブジェクトです。

JSON 応答の例を次に示します。

```json
[
  {
    "language": "de",
    "score": 0.92,
    "isTranslationSupported": true,
    "isTransliterationSupported": false,
    "alternatives": [
      {
        "language": "pt",
        "score": 0.23,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      },
      {
        "language": "sk",
        "score": 0.23,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      }
    ]
  }
]
```

## <a name="response-headers"></a>応答ヘッダー

<table width="100%">
  <th width="20%">headers</th>
  <th>説明</th>
  <tr>
    <td>X-RequestId</td>
    <td>要求を識別するサービスによって生成される値。 トラブルシューティングの目的で使用されます。</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>応答状態コード

要求から返される可能性のある HTTP 状態コードを次に示します。 

<table width="100%">
  <th width="20%">状態コード</th>
  <th>説明</th>
  <tr>
    <td>200</td>
    <td>成功。</td>
  </tr>
  <tr>
    <td>400</td>
    <td>クエリ パラメーターの 1 つが欠落しているか無効です。 再試行する前に要求パラメーターを修正してください。</td>
  </tr>
  <tr>
    <td>401</td>
    <td>要求を認証できませんでした。 資格情報が指定され、有効であることを確認してください。</td>
  </tr>
  <tr>
    <td>403</td>
    <td>要求が承認されていません。 詳細なエラー メッセージを確認してください。 これは、多くの場合、試用版サブスクリプションで提供されるすべての無料翻訳が使い果たされたことを示します。</td>
  </tr>
  <tr>
    <td>429</td>
    <td>呼び出し元からの要求が多すぎます。</td>
  </tr>
  <tr>
    <td>500</td>
    <td>予期しないエラーが発生しました。 エラーが解決しない場合は、エラー発生の日時、応答ヘッダー `X-RequestId` からの要求識別子、要求ヘッダー `X-ClientTraceId` からのクライアント識別子を添えてその旨をご報告ください。</td>
  </tr>
  <tr>
    <td>503</td>
    <td>サーバーが一時的に使用できません。 要求をやり直してください。 エラーが解決しない場合は、エラー発生の日時、応答ヘッダー `X-RequestId` からの要求識別子、要求ヘッダー `X-ClientTraceId` からのクライアント識別子を添えてその旨をご報告ください。</td>
  </tr>
</table> 

## <a name="examples"></a>例

次の例では、テキスト翻訳でサポートされている言語を取得する方法を示します。

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/detect?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'What language is this text written in?'}]"
```

---
