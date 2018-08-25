---
title: Microsoft Translator Text API Transliterate メソッド | Microsoft Docs
description: Microsoft Translator Text API Transliterate メソッドを使用します。
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: fdd6fa9236f0c02685198b6de3228c444993dad6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377520"
---
# <a name="text-api-30-transliterate"></a>Text API 3.0: Transliterate

ある言語のテキストを、あるスクリプトから別のスクリプトに変換します。

## <a name="request-url"></a>要求 URL

`POST` 要求の送信先は次のとおりです。

```HTTP
https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0
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
  <tr>
    <td>language</td>
    <td>*必須のパラメーター*。<br/>スクリプト間の変換を行うテキストの言語を指定します。 指定可能な言語は、[サポートされている言語](.\v3-0-languages.md)をサービスに照会することによって得られる `transliteration` スコープにリストされます。</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td>*必須のパラメーター*。<br/>入力テキストによって使用されるスクリプトを指定します。 選択した言語で使用可能な入力スクリプトを確認するには、`transliteration` スコープを使用して [サポートされている言語](.\v3-0-languages.md)を検索します。</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td>*必須のパラメーター*。<br/>出力スクリプトを指定します。 選択した、入力言語と入力スクリプトの組み合わせに対して使用可能な出力スクリプトを確認するには、`transliteration` スコープを使用して [サポートされている言語](.\v3-0-languages.md)を検索します。</td>
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

要求の本文は JSON 配列です。 各配列要素は、`Text` という名前の文字列プロパティ (変換する文字列を表す) を持つ JSON オブジェクトです。

```json
[
    {"Text":"こんにちは"},
    {"Text":"さようなら"}
]
```

次の制限事項が適用されます。

* 配列に含めることができる要素は、最大でも 10 個です。
* 配列要素のテキスト値は、スペースも含めて 1,000 文字を超えてはなりません。
* 要求に含めるテキスト全体では、スペースも含めて 5,000 文字を超えてはなりません。

## <a name="response-body"></a>応答本文

正常な応答は、入力配列内の要素ごとに 1 つの結果が含まれる JSON 配列となります。 結果オブジェクトには次のプロパティが含まれています。

  * `text`: 入力文字列を出力スクリプトに変換した結果である文字列。
  
  * `script`: 出力で使用されるスクリプトを指定する文字列。

JSON 応答の例を次に示します。

```json
[
    {"text":"konnnichiha","script":"Latn"},
    {"text":"sayounara","script":"Latn"}
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

次の例では、2 つの日本語文字列をローマ字化された日本語に変換する方法を示します。

# <a name="curltabcurl"></a>[curl](#tab/curl)

この例の要求に対する JSON ペイロードを次に示します。

```
[{"text":"こんにちは","script":"jpan"},{"text":"さようなら","script":"jpan"}]
```

Unicode 文字をサポートしていないコマンドライン ウィンドウで cUrl を使用する場合は、次の JSON ペイロードを取得し、それを `request.txt` という名前のファイルに保存します。 必ずファイルは `UTF-8` エンコードで保存してください。

```
curl -X POST "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=Jpan&toScript=Latn" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d @request.txt
```

---
