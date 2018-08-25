---
title: LUIS の反復的アプリ設計について -Azure | Microsoft Docs
description: LUIS アプリでは、最適なデータ抽出を実現できるように、LUIS をトレーニングする反復処理を設計する必要があります。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/12/2018
ms.author: diberry
ms.openlocfilehash: 947350a1d0dcfb0caaca654768c3660fc1b146e1
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225443"
---
# <a name="authoring-cycle"></a>作成サイクル
LUIS は、モデル変更、発話の例、公開、エンドポイント クエリからのデータ収集という反復サイクルの中で最も適切に学習します。 

![作成サイクル](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-model"></a>LUIS モデルの構築
このモデルの目的は、ユーザーが求めていること (意図) と、回答の決定に役立つ詳細情報 (エンティティ) が質問のどの部分に示されているのかを明らかにすることです。 

関連性のある単語およびフレーズと一般的な語順を決定するためには、モデルがアプリのドメインに固有である必要があります。 

モデルには、意図とエンティティが含まれます。 

## <a name="add-training-examples"></a>トレーニングするための例を追加する
LUIS には、意図内の発話例が必要です。 これらの例には、発話が意味する意図を判断できるくらい十分なバリエーションの単語の選択と語順が必要になります。 各発話例では、必要なあらゆるデータがエンティティとしてラベル付けされている必要があります。 

発話を **None** 意図に割り当てることによって、アプリのドメインに無関係な発話を無視するように LUIS に指示します。 発話から抽出する必要のない単語またはフレーズにはラベル付けをする必要がありません。 無視する単語またはフレーズにはラベルを付けません。 
<!--
## Not just yet
Do not add features such as a [phrase list](luis-concept-feature.md) feature in your first cycle. Phrase lists are phrases that would be specific to your app's subject area.  
-->
## <a name="train-and-publish-the-app"></a>アプリをトレーニングして公開する
必要なエンティティがラベル付けされている 10 から 15 のさまざまな発話を意図ごとに用意したら、LUIS をトレーニングし、公開してエンドポイントを取得します。 アプリを作成し、必要な[エンドポイント領域](luis-reference-regions.md)で利用できるように公開します。 

## <a name="https-endpoint-testing"></a>HTTPS エンドポイントのテスト
LUIS アプリは、**[[公開]](luis-how-to-publish-app.md)** ページに記載されている HTTPS エンドポイントからテストできます。 エンドポイントからテストすると、LUIS では、信頼度が低い発話を確認用に選択できます。  

## <a name="recycle"></a>リサイクル
作成サイクルが完了したら、もう一度開始できます。 LUIS によって信頼度が低いとマークされたエンドポイントの発話の見直しから開始します。 意図とエンティティの両方について、これらの発話をチェックします。 発話を確認したら、確認リストが空になります。  

## <a name="batch-testing"></a>バッチ テスト
バッチ テストは、LUIS によってどれだけの数の発話例がスコア付けされているかを確認する方法です。 これらの例は、LUIS にとって初めての内容である必要があり、LUIS に検出させる意図とエンティティに正しくラベル付けされている必要があります。 テスト結果は、その発話セットで LUIS がどの程度の能力を発揮できるか示します。 

## <a name="next-steps"></a>次の手順

[コラボレーション](luis-concept-collaborator.md)の概念を学習します。