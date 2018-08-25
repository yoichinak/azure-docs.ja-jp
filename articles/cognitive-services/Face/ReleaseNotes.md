---
title: Face API サービスのリリース ノート | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Face API サービスのリリース ノートには、さまざまなバージョンのリリース変更履歴が含まれています。
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 918b3ea5dbaaa44e4eee1a679354c7becffd4686
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377669"
---
# <a name="face-api-release-notes"></a>Face API リリース ノート

この記事は、Microsoft Face API Service バージョン 1.0 に関連します。

### <a name="release-changes-in-may-2018"></a>2018 年 5 月のリリース変更

* `gender` 属性が大幅に改善され、`age`、`glasses`、`facialHair`、`hair`、`makeup` 属性も改善されました。 [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` パラメーターをとおしてそれらを使用します。 

* [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)、[FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)、[LargeFaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)、[PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)、[LargePersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) で入力画像ファイルのサイズ上限が 4 MB から 6 MB に増えました。

### <a name="release-changes-in-march-2018"></a>2018 年 3 月のリリース変更

* 100 万規模のコンテナー、[LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) と [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) を追加しました。 詳細は「[How to use the large-scale feature](Face-API-How-to-Topics/how-to-use-large-scale.md)」 (大規模な機能を使用する方法) にあります。

* [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) `maxNumOfCandidatesReturned` パラメーターを [1, 5] から [1, 100] に増やしました。指定しなければ、10 です。

### <a name="release-changes-in-may-2017"></a>2017 年 5 月のリリース変更

* `hair`、`makeup`、`accessory`、`occlusion`、`blur`、`exposure`、`noise` 属性を [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` パラメーターに追加しました。

* PersonGroup と [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) で 1 万人に対応しました。

* [PersonGroup Person - List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) がオプション パラメーターの `start` と `top` で改ページに対応しました。

* さまざまな FaceLists と PersonGroup のさまざまな人に対する顔の追加/削除で同時実行に対応しました。

### <a name="release-changes-in-march-2017"></a>2017 年 3 月のリリース変更
* [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` パラメーターに `emotion` 属性を追加しました。

* [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) と [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) で `targetFace` として [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) から返された顔を長方形で再検出できない問題を解消しました。

* 厳密に 36 x 36 から 4096 x 4096 ピクセルになるように、検出可能な顔サイズを修正しました。

### <a name="release-changes-in-november-2016"></a>2016 年 11 月のリリース変更
* 同一人物確認や同様の照合に [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) または [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) を使用するとき、保存する顔の数を増やすために Face ストレージ Standard サブスクリプションを追加しました。 保存した画像には、顔 1000 個あたり 0.5 ドルの料金がかかります。この料金は日割りで課金されます。 Free レベルのサブスクリプションは引き続き合計 1,000 人に制限されます。

### <a name="release-changes-in-october-2016"></a>2016 年 10 月のリリース変更
* [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) と [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) で、targetFace の複数の顔に対するエラー メッセージが 'There are more than one face in the image' から 'There is more than one face in the image' に変更されました。

### <a name="release-changes-in-july-2016"></a>2016 年 7 月のリリース変更
* [Face - Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) が Face to Person のオブジェクト認証に対応しました。

* `mode` というオプション パラメーターが追加され、[Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) で `matchPerson` と `matchFace` という 2 つの作業モードを選択できるようになりました。指定しなければ、`matchPerson` です。

* `confidenceThreshold` というオプション パラメーターを追加しました。[Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) で、ある顔がある Person オブジェクトに属するかどうかを示すしきい値をこのパラメーターで設定できます。

* [PersonGroup - List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248) にオプション パラメーターの `start` と `top` を追加しました。一覧表示する開始点と合計 PersonGroups 数を指定できます。

### <a name="v10-changes-from-v0"></a>V1.0 の V0 からの変更点
* サービス ルート エンドポイントが ```https://westus.api.cognitive.microsoft.com/face/v0/``` から ```https://westus.api.cognitive.microsoft.com/face/v1.0/``` に更新されました。 変更は [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)、[Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)、[Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)、[Face - Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) に適用されます。

* 顔の検出可能な最小サイズが 36 x 36 ピクセルに変更されました。 36 x 36 ピクセルより小さな顔は検出されません。

* Face V0 では、PersonGroup データと Person データが非推奨になりました。 それらのデータには Face V1.0 サービスでアクセスできません。

* 2016 年 6 月 30 日をもって Face API の V0 エンドポイントが非推奨になりました。
