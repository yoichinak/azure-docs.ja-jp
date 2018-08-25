---
title: QnA Maker サービスのビジネス継続性計画を作成する - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: QnA Maker サービスのビジネス継続性計画の作成方法
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: ca6e54b8a8ca8b38e8ef6b1a148f8b2c54bd43da
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376208"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>QnA Maker サービスのビジネス継続性計画を作成する

ビジネス継続性計画の主な目的は、回復性に優れたナレッジ ベース エンドポイントを作成し、そのナレッジ ベース エンドポイントを使用するボットまたはアプリケーションにダウン タイムが発生しないようにすることです。

![QnA Maker の BCP 計画](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

上に示した概念の説明は次のとおりです。

1. [ペアになっている Azure リージョン](https://docs.microsoft.com/en-us/azure/best-practices-availability-paired-regions)に、同一の [QnA Maker サービス](../How-To/set-up-qnamaker-service-azure.md)を 1 つずつ、計 2 つ設定します。

2. プライマリとセカンダリの Azure 検索インデックスを常に同期しておきます。Azure のインデックスをバックアップおよび復元する方法については、[GitHub](https://github.com/pchoudhari/QnAMakerBackupRestore) でサンプルを参照してください。

3. [連続エクスポート](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-export-telemetry)を使って Application Insights をバックアップします。

4. プライマリとセカンダリのスタックを設定したら、[トラフィック マネージャー](https://docs.microsoft.com/en-us/azure/traffic-manager/)を使用して 2 つのエンドポイントを構成し、ルーティング方法を設定します。

5. トラフィック マネージャー エンドポイント用に SSL 証明書を作成する必要があります。 アプリ サービスで [SSL 証明書](https://docs.microsoft.com/en-us/azure/app-service/app-service-web-tutorial-custom-ssl)をバインドします。

6. 最後に、ボットまたはアプリでトラフィック マネージャー エンドポイントを使用します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [QnA Maker のデプロイのための容量の選択](../Tutorials/choosing-capacity-qnamaker-deployment.md)
