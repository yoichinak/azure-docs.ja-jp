---
title: モデルのテストと再トレーニング - Custom Vision Service - Azure Cognitive Services | Microsoft Docs
description: イメージをテストし、それを使ってモデルを再トレーニングする方法について説明します。
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 1933b1a45844ac99308baebe59b49687a957abfa
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35375269"
---
# <a name="test-and-retrain-a-model-with-custom-vision-service"></a>Custom Vision Service を使用したモデルのテストと再トレーニング

モデルをトレーニングした後、ローカルに保存されたイメージまたはオンライン イメージを使用してすばやくテストできます。 テストでは、最後にトレーニングしたイテレーションが使用されます。

## <a name="test-your-model"></a>モデルのテスト

1. [Custom Vision Web ページ](https://customvision.ai)からプロジェクトを選択します。 上部メニュー バー右側の **[Quick Test]**(簡単なテスト) を選択します。 このアクションによって、**[Quick Test]**(簡単なテスト) というラベルが付いたウィンドウが開きます。

    ![[Quick Test] (簡単なテスト) ボタンは、ウィンドウの右上隅に表示されます。](./media/test-your-model/quick-test-button.png)

2. **[Quick Test]**(簡単なテスト) ウィンドウ内で **[Submit Image]**(イメージの送信) フィールドをクリックし、テストに使用するイメージの URL を入力します。 代わりにローカルに保存されたイメージを使用する場合は、**[Browse local files]**(ローカル ファイルの参照) ボタンをクリックし、ローカルのイメージ ファイルを選択します。

    ![イメージの送信ページのイメージ](./media/test-your-model/submit-image.png)

選択したイメージがページの途中に表示されます。 その後、イメージの下に **[Tags]**(タグ) と **[Confidence]**(信頼度) というラベルが付いた 2 列のテーブル形式で結果が表示されます。 結果を確認した後、**[Quick Test]**(簡単なテスト) ウィンドウを閉じることができます。

このテスト イメージをモデルに追加し、モデルの再トレーニングを行えるようになります。

## <a name="use-the-predicted-image-for-training"></a>トレーニングに予測されるイメージを使用する

以前に送信したイメージをトレーニングに使用するには、次の手順に従います。

1. 分類器に送信したイメージを表示するには、[Custom Vision Web ページ](https://customvision.ai)を開き、__[Predictions]__(予測) タブを選択します。

    ![予測タブのイメージ](./media/test-your-model/predictions-tab.png)

    > [!TIP]
    > 既定のビューには、現在のイテレーションのイメージが表示されます。 __[Iteration]__(イテレーション) ドロップ ダウン フィールドを使用すると、以前のイテレーションで送信されたイメージを表示できます。

2. イメージの上にポインターを置き、分類器が予測したタグを表示します。

    > [!TIP]
    > イメージは、最も分類器に貢献したイメージが一番上になるように順位付けされます。 異なる並べ替えを選択するには、__[Sort]__(並べ替え) セクションを使用します。

    トレーニング データにイメージを追加するには、イメージとタグを選択してから __[保存して閉じる]__ を選択します。 イメージは __[Predictions]__(予測) から削除され、トレーニング イメージに追加されます。 __[Training Images]__(トレーニング イメージ) タブを選択すると、これを表示できます。

    ![タグ付けページのイメージ](./media/test-your-model/tag-image.png)

3. __[Train]__(トレーニング) ボタンを使用して、分類器を再トレーニングします。

## <a name="next-steps"></a>次の手順

[分類器の改善](getting-started-improving-your-classifier.md)