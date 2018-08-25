---
title: Azure Active Directory v2.0 エンドポイントの制限事項および制約事項 | Microsoft Docs
description: Azure AD v2.0 エンドポイントに関する制限事項と制約事項のリスト
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: a99289c0-e6ce-410c-94f6-c279387b4f66
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: celested
ms.reviewer: hirsin, dastrock
ms.custom: aaddev
ms.openlocfilehash: edf0b52e5889fe8fa875de65fcaa8c2a22df1a7f
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39590751"
---
# <a name="should-i-use-the-v20-endpoint"></a>v2.0 エンドポイントの使用が適しているかどうかを判断するには

Azure Active Directory (Azure AD) と統合されるアプリケーションを構築する場合は、v2.0 エンドポイントと認証プロトコルがニーズを満たすかどうかを判断する必要があります。 Azure AD の元のエンドポイントは引き続き完全にサポートされ、いくつかの点では v2.0 よりも機能が豊富です。 ただし、v2.0 エンドポイントは、開発者に[大きなメリット](azure-ad-endpoint-comparison.md)を提供します。

現時点での開発者向けの推奨は、下記のようにシンプルです。

* アプリケーションで個人の Microsoft アカウントをサポートする必要がある場合は、v2.0 エンドポイントを使用します。 ただしその準備として、この記事で説明する制限事項を必ず理解してください。
* アプリケーションでサポートする必要があるのが Microsoft の職場と学校アカウントのみである場合は、v2.0 エンドポイントを使用しないでください。 代わりに、[Azure AD 開発者ガイド](azure-ad-developers-guide.md)に関するページをご覧ください。

v2.0 エンドポイントはこの一覧に記載された制限事項をなくすように進化するため、v2.0 エンドポイントのみを使用すればよくなる予定です。 当面は、この記事を参照して、v2.0 エンドポイントがニーズを満たしているかどうかを判断してください。 v2.0 エンドポイントの現在の状態を反映するように、この記事を引き続き更新する予定です。 v2.0 の機能に対して、要件を再確認してください。

v2.0 エンドポイントを使用していない既存の Azure AD アプリを使用している場合も、ゼロから始める必要はありません。 将来的に、既存の Azure AD アプリケーションを v2.0 エンドポイントと共に使用する方法が提供される予定です。

## <a name="restrictions-on-app-types"></a>アプリの種類に関する制限事項

現時点では、次の種類のアプリは、v2.0 エンドポイントでサポートされていません。 サポートされているアプリの種類については、[Azure Active Directory v2.0 エンドポイントのアプリの種類](v2-app-types.md)に関する記事を参照してください。

### <a name="standalone-web-apis"></a>スタンドアロン Web API

v2.0 エンドポイントを使用すると [OAuth 2.0 で保護された Web API をビルド](v2-app-types.md#web-apis)できます。 ただし、その Web API は、同じアプリケーション ID を持つアプリケーションからしかトークンを受け取ることができません。 別のアプリケーション ID を持つクライアントから Web API にアクセスすることはできません。 クライアントは、Web API へのアクセス許可を要求することも取得することもできません。

同じアプリケーション ID を持つクライアントからのトークンを受け付ける Web API のビルド方法については、「[使用の開始](active-directory-appmodel-v2-overview.md#getting-started)」セクションに掲載されている v2.0 エンドポイントの Web API サンプルを参照してください。

## <a name="restrictions-on-app-registrations"></a>アプリの登録に関する制限事項

現在、v2.0 エンドポイントと統合するアプリごとに、新しい [Microsoft アプリケーション登録ポータル](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)にアプリの登録を作成する必要があります。 既存の Azure AD または Microsoft アカウント アプリは、v2.0 エンドポイントと互換性がありません。 アプリケーション登録ポータル以外のポータルに登録されているアプリは、v2.0 エンドポイントと互換性がありません。 今後、既存のアプリケーションを v2.0 アプリとして使用する方法を提供する予定です。 現時点では、既存のアプリを移行して v2.0 エンドポイントで機能させる方法はありません。

また、[アプリケーション登録ポータル](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)で作成したアプリケーションの登録には、次の注意点があります。

* アプリケーション ID ごとに許可されるアプリ シークレットは 2 種類のみです。
* ユーザーが個人の Microsoft アカウントで登録したアプリの登録は、1 つの開発者アカウントでのみ表示および管理できます。 複数の開発者間で共有することはできません。 アプリケーションの登録を複数の開発者の間で共有したい場合は、Azure AD アカウントで登録ポータルにサインインすることでアプリケーションを作成できます。
* 許可されるリダイレクト URI の形式には、いくつかの制限事項があります。 リダイレクト URI の詳細については、次のセクションを参照してください。

## <a name="restrictions-on-redirect-uris"></a>リダイレクト URI に関する制限事項

アプリケーション登録ポータルで登録されるアプリは、限られたリダイレクト URI 値に限定されています。 Web アプリと Web サービスのリダイレクト URI はスキーム `https` で始める必要があり、すべてのリダイレクト URI 値で 1 つの DNS ドメインを共有する必要があります。 たとえば、次のリダイレクト URI のいずれかを含む Web アプリを登録することはできません。

`https://login-east.contoso.com`  
`https://login-west.contoso.com`

登録システムによって、既存のリダイレクト URI の DNS 名全体と、追加するリダイレクト URI の DNS 名が比較されます。 次のいずれかの条件に当てはまる場合、DNS 名を追加する要求は失敗します。  

* 新しいリダイレクト URI の DNS 名全体が、既存のリダイレクト URI の DNS 名と一致しない場合
* 新しいリダイレクト URI の DNS 名全体が、既存のリダイレクト URI のサブドメインではない場合

たとえば、アプリに次のリダイレクト URI が含まれているとします。

`https://login.contoso.com`

次のように、これに追加できます。

`https://login.contoso.com/new`

この場合、DNS は完全に一致します。 または、次を行うことができます。

`https://new.login.contoso.com`

この場合、login.contoso.com の DNS サブドメインを参照しています。 リダイレクト URI として login-east.contoso.com と login-west.contoso.com を持つアプリが必要な場合は、これらのリダイレクト URI を以下の順番で追加する必要があります。

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

後の 2 つのリダイレクト URI を追加できるのは、それらが 1 つ目の contoso.com というリダイレクト URI のサブドメインであるためです。 この制限は、今後のリリースで解消される予定です。

また、特定のアプリケーションが持つことのできる返信 URL は 20 個だけであることに注意してください。

アプリケーション登録ポータルでアプリを登録する方法については、「[v2.0 エンドポイントを使用してアプリケーションを登録する方法](quickstart-v2-register-an-app.md)」を参照してください。

## <a name="restrictions-on-libraries-and-sdks"></a>ライブラリと SDK に関する制限事項

現時点では、v2.0 エンドポイントのライブラリ サポートは制限されています。 運用環境のアプリケーションで v2.0 エンドポイントを使用する場合は、次の選択肢があります。

* Web アプリケーションを構築する場合は、Microsoft が一般提供しているサーバー側のミドルウェアを使用して、サインインとトークンの検証を安全に実行できます。 このようなミドルウェアには、OWIN の ASP.NET 用 Open ID Connect ミドルウェアや Node.js 用 Passport プラグインなどがあります。 Microsoft のミドルウェアを使用するコード サンプルについては、「[使用の開始](active-directory-appmodel-v2-overview.md#getting-started)」セクションを参照してください。
* デスクトップまたはモバイル アプリケーションをビルドする場合は、Microsoft の認証ライブラリ (MSAL) プレビューのいずれかを使用できます。 これらのライブラリは、運用環境でサポートされているプレビュー版なので、実稼働アプリケーションで使用しても安全です。 プレビューの使用条件と使用可能なライブラリの詳細については、[認証ライブラリのリファレンス](reference-v2-libraries.md)に関するページをご覧ください。
* Microsoft ライブラリの対象ではないプラットフォームでは、アプリケーション コードで直接プロトコル メッセージを送受信することで v2.0 エンドポイントと統合できます。 v2.0 の OpenID Connect プロトコルと OAuth プロトコルについては、このような統合の実行に役立つように、[明確に文書化](active-directory-v2-protocols.md)されています。
* オープン ソースの Open ID Connect および OAuth のライブラリを使用して、v2.0 エンドポイントと統合できます。 v2.0 のプロトコルは通常、大幅な変更を加えなくても、多数のオープンソース プロトコル ライブラリと互換性があります。 これらのライブラリが使用可能かどうかは、言語とプラットフォームによって異なります。 [Open ID Connect](http://openid.net/connect/) および [OAuth 2.0](http://oauth.net/2/) の Web サイトでは、一般的な実装のリストを公開しています。 詳細については、「[Azure Active Directory (AD) v2.0 と認証ライブラリ](reference-v2-libraries.md)」、および v2.0 エンドポイントでテスト済みのオープンソース クライアント ライブラリとサンプルの一覧を参照してください。

## <a name="restrictions-on-protocols"></a>プロトコルに関する制限事項

v2.0 エンドポイントは SAML と WS-Federation をサポートしていません。OpenID 接続と OAuth 2.0 のみをサポートしています。 OAuth プロトコルの一部の機能がまだ v2.0 エンドポイントには組み込まれていません。

現時点では、v2.0 エンドポイントでは、次のプロトコルの機能は "*利用できません*"。

* 現時点では、`email` 要求は、省略可能な要求が構成され、その要求の中でスコープに scope=email が指定された場合にのみ返されます。 ただし、この動作は、v2.0 エンドポイントが Open ID Connect と OAuth2.0 標準にさらに準拠するように更新されたときに変更されます。
* v2.0 エンドポイントに OpenID Connect UserInfo エンドポイントは実装されていません。 ただし、このエンドポイントで受け取る可能性のあるすべてのユーザー プロファイル データは、Microsoft Graph `/me` エンドポイントから使用できます。
* v2.0 エンドポイントで ID トークン内のロールまたはグループの要求の発行はサポートされていません。
* [OAuth 2.0 リソース所有者パスワード資格情報の付与](https://tools.ietf.org/html/rfc6749#section-4.3)は、v2.0 エンドポイントでサポートされていません。

また、v2.0 エンドポイントは、SAML と WS-Federation プロトコルのいかなる形式もサポートしていません。

v2.0 エンドポイントでサポートされているプロトコル機能の範囲について詳しく理解するには、[OpenID Connect および OAuth 2.0 プロトコルに関するリファレンス](active-directory-v2-protocols.md)を参照してください。

## <a name="restrictions-for-work-and-school-accounts"></a>職場および学校のアカウントの制限

Windows アプリケーションで Active Directory Authentication Library (ADAL) を使用している場合は、Windows 統合認証を活用している可能性がありますが、これは Security Assertion Markup Language (SAML) アサーション付与を使用しています。 この付与により、フェデレーション Azure AD テナントのユーザーは資格情報を入力せずに、オンプレミスの Active Directory インスタンスで自動的に認証できます。 現在、SAML アサーションの付与は、v2.0 のエンドポイントではサポートされていません。
