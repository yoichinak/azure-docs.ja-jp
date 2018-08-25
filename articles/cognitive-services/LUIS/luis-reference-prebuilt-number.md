---
title: LUIS 作成済みエンティティ number リファレンス - Azure | Microsoft Docs
titleSuffix: Azure
description: この記事には、Language Understanding (LUIS) での number 作成済みエンティティについての情報が含まれます。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: c1a263f21ae249ea80c0798ac81818c9e9cf1319
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2018
ms.locfileid: "39236807"
---
# <a name="number-entity"></a>number エンティティ
情報の定量化、表現、説明に数値を使用する方法はたくさんあります。 この記事では、可能な例の一部についてのみ説明します。 LUIS はユーザーの発話のバリエーションを解釈し、一貫性のある数値を返します。 このエンティティは既にトレーニングされているので、数値を含む発話の例をアプリケーション意図に追加する必要はありません。 

## <a name="types-of-number"></a>number の種類
number は [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml) Github リポジトリから管理されます。

## <a name="examples-of-number-resolution"></a>number の解決の例

| 発話        | エンティティ   | 解決策 |
| ------------- |:----------------:| --------------:|
| ```one thousand times```  | ```"one thousand"``` |   ```"1000"```      | 
| ```1,000 people```        | ```"1,000"```    |   ```"1000"```      |
| ```1/2 cup```         | ```"1 / 2"```    |    ```"0.5"```      |
|  ```one half the amount```     | ```"one half"```     |    ```"0.5"```      |
| ```one hundred fifty orders``` | ```"one hundred fifty"``` | ```"150"``` |
| ```one hundred and fifty books``` | ```"one hundred and fifty"``` | ```"150"```|
| ```a grade of one point five```| ```"one point five"``` |  ```"1.5"``` |
| ```buy two dozen eggs```    | ```"two dozen"``` | ```"24"``` |


LUIS が返す JSON 応答の `resolution` フィールドには、**`builtin.number`** エンティティの認識された値が含まれます。

## <a name="resolution-for-prebuilt-number"></a>作成済みの number の解決
次の例では、"two dozen" (2 ダース) という発話に対する解決である 24 という値を含む LUIS からの JSON 応答を示します。

```JSON
{
  "query": "order two dozen eggs",
  "topScoringIntent": {
    "intent": "OrderFood",
    "score": 0.105443209
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.105443209
    },
    {
      "intent": "OrderFood",
      "score": 0.9468431361
    },
    {
      "intent": "Help",
      "score": 0.000399122015
    },
  ],
  "entities": [
    {
      "entity": "two dozen",
      "type": "builtin.number",
      "startIndex": 6,
      "endIndex": 14,
      "resolution": {
        "value": "24"
      }
    }
  ]
}
```

## <a name="next-steps"></a>次の手順

[currency](luis-reference-prebuilt-currency.md)、[ordinal](luis-reference-prebuilt-ordinal.md)、[percentage](luis-reference-prebuilt-percentage.md) について学習します。 