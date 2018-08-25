---
title: Visual Studio で Azure Stream Analytics クエリをローカルでテストする
description: この記事では、Visual Studio の Azure Stream Analytics ツールを使ってクエリをローカルでテストする方法について説明します。
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2018
ms.openlocfilehash: db26dd12d7c44f6079ee38364a4e9e240d2e7bc8
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2018
ms.locfileid: "40246616"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio"></a>Visual Studio で Stream Analytics クエリをローカルでテストする

Visual Studio の Azure Stream Analytics ツールを使用して、Stream Analytics ジョブをサンプル データを使ってローカルでテストすることができます。

この[クイック スタート](stream-analytics-quick-create-vs.md)を使用して、Visual Studio を使用して Stream Analytics ジョブを作成する方法を学習します。

## <a name="test-your-query"></a>クエリをテストする

Azure Stream Analytics プロジェクトで **[Script.asaql]** をダブルクリックして、エディターでスクリプトを開きます。 クエリをコンパイルして、構文エラーがないかを確認します。 クエリ エディターでは IntelliSense、構文の色分け、およびエラーのマーカーがサポートされています。

![クエリ エディター](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="add-local-input"></a>ローカル入力の追加

ローカルの静的データに対してクエリを検証するには、入力を右クリックして **[ローカル入力の追加]** を選択します。
   
![ローカル入力の追加](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-01.png)
   
ポップアップ ウィンドウで、ローカル パスからサンプル データを選択し、**[保存]** を選択します。
   
![ローカル入力の追加](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-02.png)
   
**local_EntryStream.json** という名前のファイルが、入力フォルダーに自動的に追加されます。
   
![ローカル入力フォルダー ファイル リスト](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-03.png)
   
クエリ エディターで、**[ローカルで実行]** をクリックします。 または F5 キーを押します。
   
![[ローカルで実行]](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-01.png)
   
出力は、Visual Studio から直接、表形式で表示できます。

![表形式で出力](./media/stream-analytics-vs-tools-local-run/stream-analytics-for-vs-local-result.png)

コンソール出力から出力パスを見つけることができます。 任意のキーを押して、結果フォルダーを開きます。
   
![ローカルの実行](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-02.png)
   
ローカル フォルダー内の結果を確認します。
   
![ローカル フォルダーの結果](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-03.png)
   

### <a name="sample-input"></a>サンプル入力
入力ソースからローカル ファイルへの入力データのサンプルを収集することもできます。 入力構成ファイルを右クリックし、**[サンプル データ]** を選択します。 

![サンプル データ](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-01.png)

Event Hubs または IoT Hub からのデータ ストリーミングのみをサンプリングできます。 他の入力ソースはサポートされていません。 ポップアップ ダイアログ ボックスで、サンプル データを保存するローカル パスを入力し、**[サンプル]** を選択します。

![サンプル データ構成](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-02.png)
 
**[出力]** ウィンドウで進行状況を確認できます。 

![サンプル データ出力](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-03.png)

## <a name="next-steps"></a>次の手順

* [Visual Studio を使用して Azure Stream Analytics ジョブを表示する](stream-analytics-vs-tools.md)
* [クイック スタート: Visual Studio の Azure Stream Analytics ツールを使用した Stream Analytics ジョブの作成](stream-analytics-quick-create-vs.md)
* [チュートリアル: VSTS を使用して CI/CD で Azure Stream Analytics ジョブを配置する](stream-analytics-tools-visual-studio-cicd-vsts.md)
* [Stream Analytics ツールで継続的に統合および開発する](stream-analytics-tools-for-visual-studio-cicd.md)