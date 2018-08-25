---
title: クイック スタート - Azure での Analysis Services サーバーのファイアウォールの構成 | Microsoft Docs
description: Azure で Analysis Services サーバー インスタンスのファイアウォールを構成する方法について説明します。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c16a189e72edd94cf6fc60580d89dd4cfd1742e8
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443445"
---
# <a name="quickstart-configure-server-firewall---portal"></a>クイック スタート: サーバーのファイアウォールの構成 - ポータル

このクイック スタートでは、Azure Analysis Services サーバーのファイアウォールを構成できます。 ファイアウォールを有効にし、IP アドレス範囲をサーバーにアクセスするコンピューターのみに構成することは、サーバーとデータの保護の重要な部分です。

## <a name="prerequisites"></a>前提条件

- サブスクリプションの Analysis Services サーバー。 詳しくは、「[クイック スタート: サーバーの作成 - ポータル](analysis-services-create-server.md)」または「[クイック スタート: サーバーの作成 - PowerShell](analysis-services-create-powershell.md)」をご覧ください
- クライアント コンピューターの 1 つまたは複数の IP アドレス範囲 (必要な場合)。

## <a name="log-in-to-the-azure-portal"></a>Azure Portal にログインする 

[ポータルにログインします](https://portal.azure.com)

## <a name="configure-a-firewall"></a>ファイアウォールを構成する

1. サーバーをクリックして [概要] ページを開きます。 
2. **[設定]** > **[ファイアウォール]** > **[ファイアウォールを有効にする]** で、**[オン]** をクリックします。
3. Power BI サービスからの DirectQuery アクセスを許可するには、**[Allow accesss from Power BI]\(Power BI からのアクセスを許可する\)** で **[オン]** をクリックします。  
4. (省略可能) 1 つまたは複数の IP アドレス範囲を指定します。 各範囲の名前、開始 IP アドレス、終了 IP アドレスを入力します。 
5. **[Save]** をクリックします。

     ![ファイアウォールの設定](./media/analysis-services-qs-firewall/aas-qs-firewall.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

不要になった場合は、IP アドレス範囲を削除するか、ファイアウォールを無効にします。

## <a name="next-steps"></a>次の手順
このクイック スタートでは、サーバーのファイアウォールを構成する方法を説明しました。 サーバーが存在し、ファイアウォールで保護されたので、ポータルからそれに基本的なサンプル データ モデルを追加できます。 サンプル モデルがあると、モデル データベース ロールの構成とクライアント接続のテストについて理解するのに役立ちます。 詳しくは、サンプル モデルの追加のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [チュートリアル: サーバーにサンプル モデルを追加する](analysis-services-create-sample-model.md)
