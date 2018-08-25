---
title: Linguistic Analysis API の Analyze メソッド | Microsoft Docs
description: Linguistic Analysis API で Analyze メソッドを使用して特定の自然言語入力を分析する方法。
services: cognitive-services
author: RichardSunMS
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 12/13/2016
ms.author: lesun
ms.openlocfilehash: b17a00f31845bfa05572dff7ca94e9a1ffd69586
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373208"
---
# <a name="analyze-method"></a>Analyze メソッド

**analyze** REST API は、特定の自然言語入力を分析するために使用されます。
入力内の[文とトークン](Sentences-and-Tokens.md)の検出、[品詞のタグ](POS-tagging.md)の検出、または[構成素ツリー](Constituency-Parsing.md)の検出のみが行われる場合もあります。
関連するアナライザーを選択することで、必要な結果を指定できます。
利用可能なすべてのアナライザー一覧については、**[analyzers](AnalyzersMethod.md)** のページを参照してください。

入力文字列の言語を指定する必要がある点に注意してください。

**REST エンドポイント:**
```
https://westus.api.cognitive.microsoft.com/linguistics/v1.0/analyze
```
<br>

## <a name="request-parameters"></a>要求パラメーター

Name | type | 必須 | 説明
-----|-------|----------|------------
**language**    | 文字列 | [はい] | 分析に使用する 2 文字の ISO 言語コード。 たとえば、英語は "en" です。
**analyzerIds** | string のリスト | [はい] | 適用するアナライザーの GUID リスト。 詳細については、Analyzers に関するドキュメントを参照してください。
**text**        | 文字列 | [はい] | 分析対象の未処理の入力。 単語や語句などの短い文字列、完全な文章、完全な段落や会話の場合があります。

<br>
## <a name="response-json"></a>応答 (JSON)
分析出力の配列。要求で指定された属性ごとに 1 つです。

結果は次のようになります。

Name | type | 説明
-----|------|--------------
analyzerId | 文字列 | 指定したアナライザーの GUID
result | オブジェクト | アナライザーの結果

結果の種類は入力アナライザーの種類によって変わることに注意してください。

### <a name="tokens-response-json"></a>トークン応答 (JSON)

Name | type | 説明
-----|------|-------------
result | 文オブジェクトのリスト | テキスト内に指定された文の境界 |
result[x].Offset | int | 各文の開始文字オフセット |
result[x].Len | int | 各文の文字数 |
result[x].Tokens | トークン オブジェクトのリスト | 文内に指定されたトークンの境界 |
result[x].Tokens[y].Offset | int | トークンの開始文字オフセット |
result[x].Tokens[y].Len | int | 各トークンの文字数 |
result[x].Tokens[y].RawToken | 文字列 | 正規化前のそのトークン内の文字 |
result[x].Tokens[y].NormalizedToken | 文字列 | 文字の正規化された形式。[解析ツリー](Constituency-Parsing.md)に問題なく使用できます。たとえば、開始のかっこ文字 '(' は '-LRB-' になります。 |

入力例: `This is a test. Hello.'
JSON の応答例:
```json
[
  {
    "Len": 15,
    "Offset": 0,
    "Tokens": [
      {
        "Len": 4,
        "NormalizedToken": "This",
        "Offset": 0,
        "RawToken": "This"
      },
      {
        "Len": 2,
        "NormalizedToken": "is",
        "Offset": 5,
        "RawToken": "is"
      },
      {
        "Len": 1,
        "NormalizedToken": "a",
        "Offset": 8,
        "RawToken": "a"
      },
      {
        "Len": 4,
        "NormalizedToken": "test",
        "Offset": 10,
        "RawToken": "test"
      },
      {
        "Len": 1,
        "NormalizedToken": ".",
        "Offset": 14,
        "RawToken": "."
      }
    ]
  },
  {
    "Len": 6,
    "Offset": 16,
    "Tokens": [
      {
        "Len": 5,
        "NormalizedToken": "Hello",
        "Offset": 16,
        "RawToken": "Hello"
      },
      {
        "Len": 1,
        "NormalizedToken": ".",
        "Offset": 21,
        "RawToken": "."
      }
    ]
  }
]
```


### <a name="pos-tags-response-json"></a>POS タグ応答 (JSON)

結果は文字列リストのリストになります。
文ごとに POS タグのリストがあり、トークンごとに 1 つの POS タグがあります。
各 POS タグに対応するトークンを検索するには、トークン化オブジェクトも要求する必要があります。

### <a name="constituency-tree-response-json"></a>構成素ツリー応答 (JSON)

結果は文字列のリストです。入力で見つかった文ごとに 1 つの解析ツリーです。
解析ツリーは、かっこで囲まれた形式で表されます。

<br>

## <a name="example"></a>例

`POST /analyze`

要求の本文: JSON ペイロード
```json
{
  "language": "en",
  "analyzerIds": [
    "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04",
    "22A6B758-420F-4745-8A3C-46835A67C0D2" ],
  "text": "Hi, Tom! How are you today?" 
}
```

応答: JSON
```json
[
  {
    "analyzerId": "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04", 
    "result": [ ["NNP",",","NNP","."], ["WRB","VBP","PRP","NN","."] ]
  },
  {
    "analyzerId": "22A6B758-420F-4745-8A3C-46835A67C0D2", 
    "result":["(TOP (S (NNP Hi) (, ,) (NNP Tom) (. !)))","(TOP (SBARQ (WHADVP (WRB How)) (SQ (VP (VBP are)) (NP (PRP you)) (NN today) (. ?))))"]
  }
]
```

