---
title: ページ上のリンクがアプリケーション プロキシ アプリケーションに対して機能しない | Microsoft Docs
description: Azure AD と統合したアプリケーション プロキシ アプリケーション上の壊れたリンクに関する問題をトラブルシューティングする方法
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
ms.date: 05/18/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 09fa527f8a1e03d568dec67369651bbcee2a26f2
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365823"
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>ページ上のリンクがアプリケーション プロキシ アプリケーションに対して機能しない

この記事では、Azure Active Directory アプリケーション プロキシ アプリケーション上のリンクが正しく機能しない理由をトラブルシューティングする方法について説明します。

## <a name="overview"></a>概要 
アプリケーション プロキシ アプリを発行した後、そのアプリケーション内では、既定では、発行されたルート URL 内に含まれている宛先へのリンクしか機能しません。 アプリケーション内のリンクは機能せず、アプリケーションの内部 URL には、アプリケーション内のリンクのすべての宛先が含まれていない可能性があります。

**この問題が発生する理由** アプリケーション内のリンクをクリックすると、アプリケーション プロキシはその URL を同じアプリケーション内の内部 URL か、または外部から使用可能な URL のどちらかとして解決しようとします。 リンクが同じアプリケーション内にない内部 URL を指している場合、そのリンクはこれらのバケットのどちらにも属せず、見つからないというエラーが発生します。

## <a name="ways-you-can-resolve-broken-links"></a>壊れたリンクを解決する方法

この問題を解決するには、次の 3 つの方法があります。 以下の選択肢は、複雑さが低い順に示されています。

1.  内部 URL が、アプリケーションの関連するすべてのリンクを含むルートであることを確認します。 これで、すべてのリンクを同じアプリケーション内で発行されたコンテンツとして解決できます。

    内部 URL は変更するが、ユーザーのランディング ページを変更したくない場合は、ホーム ページ URL を前に発行した内部 URL に変更します。 これを行うには、[Azure Active Directory] -&gt; [アプリの登録] に移動し、アプリケーションを選択し、[プロパティ] を選択します。 このプロパティ タブには、目的のランディング ページになるように調整できるフィールド [ホーム ページ URL] が表示されます。

2.  アプリケーションで完全修飾ドメイン名 (FQDN) を使用している場合は、[カスタム ドメイン](manage-apps/application-proxy-configure-custom-domain.md)を使用してアプリケーションを発行します。 この機能を使用すると、同じ URL を内部と外部の両方に使用できます。

    このオプションでは、内部 URL へのアプリケーション内のリンクが外部からも認識されるため、アプリケーション内のリンクがアプリケーション プロキシ経由で外部からアクセス可能なことが保証されます。 すべてのリンクが引き続き、発行済みのアプリケーションに属している必要があります。 ただし、このオプションでは、各リンクが同じアプリケーションに属している必要はなく、複数のアプリケーションに属することができます。

3.  これらのオプションのどちらも実現できない場合は、URL 変換/書き換えを実行する新機能をプレビューできます。 この機能では、アプリケーションの HTML 本文内に存在する内部 URL またはリンクが、発行済みの外部のアプリ プロキシ URL に変換または "マッピング" されます。 この変換は、HTML または CSS 内のリンクに対してのみ機能し、リンクが JS 経由で生成されている場合は役立ちません。 

そのため、可能な場合は[カスタム ドメイン](manage-apps/application-proxy-configure-custom-domain.md) ソリューションを使用することを強くお勧めします。 プレビューに参加したい場合は、applicationId を記入して <aadapfeedback@microsoft.com> 宛にメールでご連絡ください。

## <a name="next-steps"></a>次の手順
[既存のオンプレミス プロキシ サーバーと連携する](manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)

