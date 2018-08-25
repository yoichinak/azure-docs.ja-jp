---
title: 異常検出のための Java アプリ - Microsoft Cognitive Services | Microsoft Docs
description: Microsoft Cognitive Services の Anomaly Detection API を使用する Java アプリについて説明します。 API に元のデータ ポイントを送信し、予期される値と異常なポイントを取得します。
services: cognitive-services
author: wenya
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: wenya
ms.openlocfilehash: 228d440da358eba1322e2228c54f21e925e36ecd
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38597950"
---
# <a name="anomaly-detection-java-application"></a>異常検出のための Java アプリケーション

この記事では、Anomaly Detection API を呼び出すシンプルな Java アプリケーションの使い方を示します。  
例では、サブスクリプション キーを使用して、Anomaly Detection API に時系列データを送信し、その後 API から、データ ポイントごとの異常ポイントと予期された値をすべて取得します。

## <a name="prerequisites"></a>前提条件

### <a name="platform-requirements"></a>プラットフォームの要件

このチュートリアルは、[IntelliJ IDEA](https://www.jetbrains.com/idea) を使用して開発されています。 [Java Development Kit (JDK)](http://www.oracle.com/technetwork/java/javase/downloads/index.html) バージョン 1.8 以降と、最新の [Apache Maven](http://maven.apache.org/) ビルド ツールをインストールする必要もあります。

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>異常検出にサブスクライブしてサブスクリプション キーを取得する 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]
 

## <a name="download-the-tutorial-project"></a>チュートリアル プロジェクトをダウンロードする

1. [Java リポジトリ](https://github.com/MicrosoftAnomalyDetection/java-sample)の MicrosoftAnomalyDetection に移動してます。
2. [Clone or download] ボタンをクリックします。
3. [Download ZIP] をクリックして、チュートリアル プロジェクトの ZIP ファイルをダウンロードします。

<a name="Step1"></a>
### <a name="open-the-tutorial-project"></a>チュートリアル プロジェクトを開く

1. チュートリアル プロジェクトの .zip ファイルを抽出します。
2. IntelliJ IDEA で、**[ファイル] > [開く]** をクリックします。[ファイルを開く] または [Project] ダイアログ ボックスが表示されます。
3. 抽出されたプロジェクトのルート パスを選択し、[OK] をクリックします。
4. [Projects] パネルで、**[src] > [main] > [java]** と展開します。
5. [com.microsoft.cognitiveservice.anomalydetection.Main.java] をダブルクリックして、ファイルをエディターに読み込みます。

<a name="Step2"></a>
### <a name="replace-subscriptionkey-and-uri-region"></a>subscriptionKey と URI リージョンを置き換える

```
// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
public static final String subscriptionKey = "<Subscription Key>";

public static final String uriBase = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection";

```

<a name="Step3"></a>
### <a name="build-and-run-the-tutorial-project"></a>チュートリアル プロジェクトをビルドして実行する

1. com.microsoft.cognitiveservice.anomalydetection.Main.java のソース コード タブ内で、任意の場所を右クリックしてメニューを表示します。 
2. [Run'Main.main()'] を選択します
3. サンプルの要求の結果が返されて、ターミナルに表示されます。

### <a name="result-of-the-tutorial-project"></a>チュートリアル プロジェクトの結果

[!INCLUDE [diagrams](../includes/diagrams.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [REST API リファレンス](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
