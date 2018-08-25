---
title: アプリケーションに同意すると、予期しないエラーが発生する | Microsoft Docs
description: アプリケーションに同意する処理の最中に発生する可能性があるエラーとそれらに対処する方法について説明します
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
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 78c2bd9f7d4f2b89ffdf4056edc394edbe761795
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2018
ms.locfileid: "39363239"
---
# <a name="unexpected-error-when-performing-consent-to-an-application"></a>アプリケーションに同意すると、予期しないエラーが発生する

この記事では、アプリケーションに同意する処理の最中に発生する可能性があるエラーについて説明します。 エラー メッセージが含まれていない、予期しない同意プロンプトをトラブルシューティングをする場合は、「[Azure AD の認証シナリオ](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios)」を参照してください。

Azure Active Directory と統合する多くのアプリケーションが機能するためには、他のリソースへのアクセス許可が必要です。 これらのリソースを Azure Active Directory に統合する際にも、共通同意フレームワークを使用してリソースへのアクセス許可がしばしば要求されます。 同意プロンプトが表示されます。これは、通常は、アプリケーションを初めて使用するときに発生しますが、その後のアプリケーションの使用時にも発生する可能性があります。

アプリケーションが求めるアクセス許可にユーザーが同意する場合は、特定の条件が true でなければなりません。 これらの条件が満たされていない場合は、以下のエラーが発生する可能性があります。

## <a name="requesting-not-authorized-permissions-error"></a>許可されていないアクセス許可を要求するエラー
* **AADSTS90093:** &lt;clientAppDisplayName&gt; が、付与するように許可されていない 1 つまたは複数のアクセス許可を要求しています。 あなたの代わりにこのアプリケーションに同意できる管理者に問い合わせてください。

このエラーは、管理者のみが付与できるアクセス許可を要求するアプリケーションを、会社の管理者ではないユーザーが使用しようとしたときに発生します。 このエラーは、組織を代表してアプリケーションにアクセス許可を付与できる管理者が解決できます。

## <a name="policy-prevents-granting-permissions-error"></a>ポリシーがアクセス許可の付与を妨げるエラー
* **AADSTS90093:** 管理者 &lt;tenantDisplayName&gt; がアクセス許可を要求している&lt;アプリの名前&gt;を付与するのを妨げるポリシーを設定しています。 あなたの代わりにこのアプリケーションに同意できる管理者 &lt;tenantDisplayName&gt; に問い合わせてください。

このエラーは、ユーザーがアプリケーションに同意する機能を、会社の管理者がオフにしており、管理者以外のユーザーが同意を必要とするアプリケーションを使用しようとした場合に発生します。 このエラーは、組織を代表してアプリケーションにアクセス許可を付与できる管理者が解決できます。

## <a name="intermittent-problem-error"></a>一時的な問題によるエラー
* **AADSTS90090:** &lt;clientAppDisplayName&gt; に付与しようとしたアクセス許可を記録するときに、サインイン プロセスで一時的な問題が発生したと考えられます。 後でもう一度やり直してください。

このエラーは、サービス側に一時的な問題が発生したことを示します。 再度アプリケーションへの同意を試みることにより解決できる可能性があります。

## <a name="resource-not-available-error"></a>リソース使用不可エラー
* **AADSTS65005:** アプリ &lt;clientAppDisplayName&gt; が使用不可のリソース &lt;resourceAppDisplayName&gt; にアクセスするためのアクセス許可を要求しました。 

アプリケーション開発者に問い合わせください。

##  <a name="resource-not-available-in-tenant-error"></a>テナントでのリソース使用不可エラー
* **AADSTS65005:** &lt;clientAppDisplayName&gt; が組織 &lt;tenantDisplayName&gt; で使用できないリソース &lt;resourceAppDisplayName&gt; へのアクセスを要求しています。 

このリソースが使用できることを確認するか、または管理者 &lt;tenantDisplayName&gt; に問い合わせてください。

## <a name="permissions-mismatch-error"></a>アクセス許可の不一致エラー
* **AADSTS65005:** アプリがリソース &lt;resourceAppDisplayName&gt; へのアクセスに同意するよう要求しました。 この要求は、アプリがアプリの登録中に事前に構成された方法と一致しないため失敗しました。 アプリのベンダーに問い合わせてください。**

これらのエラーは、ユーザーが同意しようとしたアプリケーションが、組織のディレクトリ (テナント) に見つからないリソース アプリケーションにアクセスするためのアクセス許可を要求するときに発生します。 この状況は、さまざまな理由で発生する可能性があります。

-   クライアント アプリケーションの開発者によるアプリケーションの構成が正しくないため、無効なリソースへのアクセスの要求が発生します。 この場合、アプリケーション開発者がクライアント アプリケーションの構成を更新して、この問題を解決する必要があります。

-   ターゲット リソース アプリケーションを表すサービス プリンシパルが組織に存在しないか、または過去に存在したが削除されました。 この問題を解決するには、クライアント アプリケーションがアクセス許可を要求できるように、リソース アプリケーションのサービス プリンシパルを組織内にプロビジョニングする必要があります。 サービス プリンシパルは、アプリケーションの種類に応じて、以下を含むさまざまな方法でプロビジョニングできます。

    -   リソース アプリケーション (Microsoft が公開したアプリケーション) のサブスクリプションの取得

    -   リソース アプリケーションへの同意

    -   Azure Portal を介したアプリケーションのアクセス許可の付与

    -   Azure AD アプリケーション ギャラリーからのアプリケーションの追加

## <a name="next-steps"></a>次の手順 

[Azure Active Directory (v1 エンドポイント) のアプリ、アクセス許可、および同意](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)<br>

[Azure Active Directory (v2.0 エンドポイント) のスコープ、アクセス許可、および同意](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


