---
title: Azure Content Moderator で画像をモデレートする | Microsoft Docs
description: Content Moderator API コンソールで画像モデレーションを使ってみましょう。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: fec54826c70ae10e56c68406f629c56639985295
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373109"
---
# <a name="moderate-images-from-the-api-console"></a>API コンソールで画像をモデレートする

Azure Content Moderator の [Image Moderation API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) を使用して、画像コンテンツのスキャンとレビューのモデレーション ワークフローを開始します。 モデレーション ジョブは、不適切な表現がないかコンテンツをスキャンし、コンテンツをカスタム ブラックリストと共有ブラックリストに対して比較します。

## <a name="use-the-api-console"></a>API コンソールを使用する
オンライン コンソールで API を試すには、サブスクリプション キーが必要です。 これは、**[設定]** タブの **[Ocp-Apim-Subscription-Key]** ボックス内にあります。 詳細については、[概要](overview.md)に関するページを参照してください。

1.  [[Image Moderation API reference]\(Image Moderation API リファレンス\)](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)に移動します。

  画像モデレーションの **[Image - Evaluate]\(Image - Evaluate\)** ページが開きます。

2. **[Open API testing console]\(API テスト コンソールを開く\)** で、実際の場所に最もあてはまるリージョンを選択します。 

  ![試用版: [Image - Evaluate]\(Image - Evaluate\) ページのリージョン選択肢](images/test-drive-region.png)
  
  **Image - Evaluate** API コンソールが開きます。

3. **[Ocp-Apim-Subscription-Key]** ボックスにサブスクリプション キーを入力します。

  ![試用版: [Image - Evaluate]\(Image - Evaluate\) コンソールのサブスクリプション キー](images/try-image-api-1.PNG)

4. **[要求本文]** ボックスで、既定のサンプル画像を使用するか、スキャンする画像を指定します。 画像そのものをバイナリ ビット データとして送信するか、画像の公的にアクセス可能な URL を指定します。 

  この例では、**[要求本文]** ボックスに指定されたパスを使用し、**[送信]** を選択します。 

   ![試用版: [Image - Evaluate]\(Image - Evaluate\) コンソールの [要求本文]](images/try-image-api-2.PNG)

  これはこの URL にある画像です。

  ![試用版: [Image - Evaluate]\(Image - Evaluate\) コンソールのサンプル画像](images/sample-image.jpg) 

5. **[送信]** を選択します。

6. API によって、各分類の確率スコアが返されます。 また、画像が条件と一致するかどうかの判断 (**true** または **false**) も返されます。 

  ![試用版: [Image - Evaluate]\(Image - Evaluate\) コンソールの確率スコアと条件判断](images/try-image-api-3.PNG)

## <a name="face-detection"></a>顔検出

Image Moderation API を使用して、画像で顔を検索します。 このオプションが役立つのは、プライバシーに関する問題があり、特定の顔がプラットフォームに掲載されないようにする場合です。 

1.  [[Image Moderation API reference]\(Image Moderation API リファレンス\)](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) の左側のメニューで、**[イメージ]** の下にある **[Find Faces]\(顔を検出\)** を選択します。 

  **[Image - Find Faces]\(Image - Find Faces\)** ページが開きます。

2.  **[Open API testing console]\(API テスト コンソールを開く\)** で、実際の場所に最もあてはまるリージョンを選択します。 

  ![試用版: [Image - Find Faces]\(Image - Find Faces\) ページのリージョン選択肢](images/test-drive-region.png)

  **[Image - Find Faces]\(Image - Find Faces\)** API コンソールが開きます。

3. スキャンする画像を指定します。 画像そのものをバイナリ ビット データとして送信するか、画像の公的にアクセス可能な URL を指定します。 この例では、CNN ストーリーで使用された画像にリンクします。

  ![試用版: [Image - Find Faces]\(Image - Find Faces\) のサンプル画像](images/try-image-api-face-image.jpg)

  ![試用版: [Image - Find Faces]\(Image - Find Faces\) のサンプル要求](images/try-image-api-face-request.png)

4. **[送信]** を選択します。 この例では、API が 2 つの顔を見つけて、画像内のそれらの座標を返します。

   ![試用版: [Image - Find Faces]\(Image - Find Faces\) サンプルの [応答のコンテンツ] ボックス](images/try-image-api-face-response.png)

## <a name="text-detection-via-ocr-capability"></a>OCR 機能によるテキスト検出

Content Moderator OCR 機能を使用して、画像内のテキストを検出できます。

1. [[Image Moderation API reference]\(Image Moderation API リファレンス\)](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) の左側のメニューで、**[イメージ]** の下にある **[OCR]** を選択します。 

  **[Image - OCR]\(Image - OCR\)** ページが開きます。

2. **[Open API testing console]\(API テスト コンソールを開く\)** で、実際の場所に最もあてはまるリージョンを選択します。 

  ![[Image - OCR]\(Image - OCR\) ページのリージョン選択肢](images/test-drive-region.png)

  **[Image - OCR]\(Image - OCR\)** API コンソールが開きます。

3. **[Ocp-Apim-Subscription-Key]** ボックスにサブスクリプション キーを入力します。

4. **[要求本文]** ボックスで、既定のサンプル画像を使用します。 これは、前のセクションで使用されたのと同じ画像です。

5. **[送信]** を選択します。 抽出されたテキストが次のように JSON で表示されます。

  ![[Image - OCR]\(Image - OCR\) のサンプル [応答のコンテンツ] ボックス](images/try-image-api-ocr.PNG)

## <a name="next-steps"></a>次の手順

コード内で REST API を使用するか、アプリケーションと統合するための、[画像モデレートに関する .NET のクイック スタート](image-moderation-quickstart-dotnet.md)を利用して作業を開始します。
