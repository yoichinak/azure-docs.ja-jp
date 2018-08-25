---
title: アプリケーション プロキシ アプリケーション用として動作するコネクタ グループが見つからない | Microsoft Docs
description: Azure AD アプリケーション プロキシを使用したアプリケーション用のコネクタ グループ内に動作するコネクタがない場合に発生する可能性のある問題に対処します
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 82014e1eaadc343ac320b0f4b3091d530228a8c5
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366163"
---
# <a name="no-working-connector-group-found-for-an-application-proxy-application"></a>アプリケーション プロキシ アプリケーション用として動作するコネクタ グループが見つからない

この記事では、Azure Active Directory に統合されたアプリケーション プロキシ アプリケーションに対してコネクタが検出されない場合に発生する一般的な問題の解決方法を説明します。

## <a name="overview-of-steps"></a>手順の概要
アプリケーション用のコネクタ グループ内に動作するコネクタがない場合、以下に示すいくつかの方法で問題を解決できます。

-   グループ内にコネクタがない場合は、以下のように操作します。

    -   適切なオンプレミスのサーバーに新しいコネクタをダウンロードし、このグループに割り当てる

    -   アクティブなコネクタをグループ内に移動する

-   グループ内にアクティブなコネクタがない場合は、以下のように操作します。

    -   コネクタがアクティブになっていない理由を特定し、それを解決する

    -   アクティブなコネクタをグループ内に移動する

問題を調べるには、アプリケーションの [アプリケーション プロキシ] メニューを開き、[コネクタ グループ] の警告メッセージを確認します。 グループ内にコネクタがない場合、警告メッセージには、グループには少なくとも 1 つのコネクタが必要であることが明記されます。 アクティブなコネクタがない場合、警告メッセージにそのことが説明されます。 非アクティブなコネクタがあるのは一般的です。 

   ![Azure portal でのコネクタ グループの選択](./media/application-proxy-connectivity-no-working-connector/no-active-connector.png)

これらの各オプションの詳細については、以下の対応するセクションを参照してください。 これらの手順では、コネクタ管理ページから操作を開始していることを前提としています。 上記のエラー メッセージが表示される場合は、警告メッセージをクリックすることで、このページにアクセスできます。 **Azure Active Directory** にアクセスし、**[エンタープライズ アプリケーション]**、**[アプリケーション プロキシ]** の順にクリックしても、このページにアクセスできます。

   ![Azure portal でのコネクタ グループの管理](./media/application-proxy-connectivity-no-working-connector/app-proxy.png)

## <a name="download-a-new-connector"></a>新しいコネクタをダウンロードする

新しいコネクタをダウンロードするには、ページの上部にある [コネクタのダウンロード] ボタンを使用します。

コネクタをバックエンド アプリケーションに直接視線がある状態でコンピューターにインストールします。 通常、コネクタはアプリケーションと同じサーバーにインストールされます。 ダウンロード後、コネクタがこのメニュー内に表示されます。 コネクタをクリックし、[コネクタ グループ] ドロップダウンを使用して、そのコネクタが正しいグループに属すようにします。 変更を保存します。

   ![Azure portal からコネクタをダウンロードする](./media/application-proxy-connectivity-no-working-connector/download-connector.png)
   
## <a name="move-an-active-connector"></a>アクティブなコネクタを移動する

グループに属し、ターゲットのバックエンド アプリケーションにアクセスできるアクティブなコネクタがある場合は、割り当てられているグループにそのコネクタを移動できます。 移動するには、コネクタをクリックします。 [コネクタ グループ] フィールドで、ドロップダウンを使用して正しいグループを選択し、[保存] をクリックします。

## <a name="resolve-an-inactive-connector"></a>アクティブではないコネクタを解決する

グループ内にアクティブではないコネクタしか存在しない場合、それらのコネクタは、必要なすべてのポートのブロック解除が行われていないコンピューター上に配置されている可能性があります。

この問題の調査方法の詳細については、ポートのトラブルシューティングに関するドキュメントを参照してください。

## <a name="next-steps"></a>次の手順
[Azure AD アプリケーション プロキシ コネクタについて](manage-apps/application-proxy-connectors.md)


