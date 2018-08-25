---
title: Azure Machine Learning Web サービスの同時実行数を増やす方法 | Microsoft Docs
description: エンドポイントの追加によって Azure Machine Learning Web サービスの同時実行数を増やす方法を説明します。
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
keywords: Azure Machine Learning、Web サービス、運用、スケーリング、エンドポイント、同時実行
ms.assetid: c2c51d7f-fd2d-4f03-bc51-bf47e6969296
ms.service: machine-learning
ms.component: studio
ms.devlang: NA
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/23/2017
ms.openlocfilehash: 2f950d93c0d923e20451eb1622dd4b1393f343a7
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835901"
---
# <a name="scaling-an-azure-machine-learning-web-service-by-adding-additional-endpoints"></a>エンドポイントの追加による Azure Machine Learning Web サービスのスケーリング
> [!NOTE]
> このトピックでは、**従来**の Machine Learning Web サービスに適用できる手法について説明します。 
> 
> 

既定では、発行される Web サービスはそれぞれ、20 件の同時実行要求をサポートするように構成されますが、200 件までの同時実行要求をサポートするように構成できます。 Azure Machine Learning では、Web サービスで最大限のパフォーマンスを実現するために、設定が自動的に最適化され、ポータルの値は無視されます。 

同時呼び出しの最大数 200 件でサポートされる内容を上回る負荷の API を呼び出す場合は、同じ Web サービスに複数のエンドポイントを作成する必要があります。 その後、それら全体に負荷を無作為に分配できます。

Web サービスのスケーリングは一般的なタスクです。 スケーリングの理由としてあげられるのは、200 件を越える同時要求のサポート、複数のエンドポイントによる可用性の向上、または Web サービス用の個別エンドポイントの提供です。 スケールを大きくするには、次の図に示すように [Azure Machine Learning Web サービス](https://services.azureml.net/) ポータルから同じ Web サービスに追加のエンドポイントを追加します。

新たなエンドポイントの追加の詳細については、「[エンドポイントを作成する](create-endpoint.md)」をご覧ください。

同時呼び出しの数を増やす場合、これに相当する高いレートで API を呼び出さないと悪影響が出ることがありますのでご注意ください。 高い負荷で構成された API に比較的低い負荷を割り当てた場合、不規則なタイムアウトや待機時間の急増が発生することがあります。

同期 API は通常、短い待機時間が望まれる状況で使用されます。 ここでの待機時間とは、API が 1 つの要求を完了するのにかかる時間を指し、ネットワークの遅延に影響しないものです。 たとえば待機時間が 50 ms の API があるとします。 スロットル レベル "高" で同時呼び出しの最大数が 20 件のときに使用可能な容量をすべて使用するには、この API を 1 秒あたり 20 × 1000 / 50 = 400 回呼び出す必要があります。 さらに増やして、同時呼び出しの最大数 200 件にすると、API を 1 秒あたり 4,000 回呼び出すことができ、この場合の待機時間は 50 ms と想定されます。

<!--Image references-->
[1]: ./media/scaling-webservice/machlearn-1.png
[2]: ./media/scaling-webservice/machlearn-2.png
