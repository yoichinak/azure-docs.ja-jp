---
title: Azure Service Fabric CLI (sfctl) スクリプトのデプロイのサンプル
description: Azure Service Fabric CLI を使用した Azure Service Fabric クラスターへのアプリケーションのデプロイ
services: service-fabric
documentationcenter: ''
author: TylerMSFT
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 04/16/2018
ms.author: twhitney
ms.custom: mvc
ms.openlocfilehash: d3e619750c45ac2d8e0b942a304aadfa05301624
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2018
ms.locfileid: "39069507"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Service Fabric クラスターへのアプリケーションのデプロイ

このサンプル スクリプトは、アプリケーション パッケージをクラスター イメージ ストアにコピーし、クラスターにそのアプリケーションの種類を登録し、そのアプリケーションの種類からアプリケーション インスタンスを作成します。 また、この時点で既定のサービスも作成されます。

必要に応じて、[Service Fabric CLI](../service-fabric-cli.md) をインストールします。

## <a name="sample-script"></a>サンプル スクリプト

[!code-sh[main](../../../cli_scripts/service-fabric/deploy-application/deploy-application.sh "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

終了したら、[remove](cli-remove-application.md) スクリプトを使用してアプリケーションを削除することができます。 remove スクリプトは、アプリケーション インスタンスを削除し、アプリケーションの種類の登録を解除し、イメージ ストアからアプリケーション パッケージを削除します。

## <a name="next-steps"></a>次の手順

詳細については、[Service Fabric CLI ドキュメント](../service-fabric-cli.md)をご覧ください。

その他の Azure Service Fabric 用 Service Fabric CLI サンプルは、[Service Fabric CLI のサンプル](../samples-cli.md)のページにあります。
