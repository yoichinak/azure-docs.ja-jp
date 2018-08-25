---
title: Azure AD v2.0 エンドポイントの変更点 | Microsoft Docs
description: 元の Azure AD と v2.0 エンドポイントの比較。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5060da46-b091-4e25-9fa8-af4ae4359b6c
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: celested
ms.reviewer: elisol, jmprieur, hirsin
ms.custom: aaddev
ms.openlocfilehash: 0e344f6e9dfee3793320dc9cb79e3231c2eeda87
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39580655"
---
# <a name="whats-different-about-the-v20-endpoint"></a>v2.0 エンドポイントの変更点

Azure Active Directory (Azure AD) を使い慣れている場合、または以前にアプリを Azure AD に統合した場合、v2.0 エンドポイントには、予想していない可能性のある違いがいくつかあります。 この記事では、これらの違いについて説明します。

> [!NOTE]
> Azure AD のシナリオと機能のすべてが、v2.0 エンドポイントでサポートされるわけではありません。 v2.0 エンドポイントを使用する必要があるかどうかを判断するには、 [v2.0 の制限事項](active-directory-v2-limitations.md)に関するページをお読みください。
>

## <a name="microsoft-accounts-and-azure-ad-accounts"></a>Microsoft アカウントと Azure AD アカウント

v2.0 エンドポイントを使用すると、開発者は、1 つの認証エンドポイントを使用して Microsoft アカウントと Azure AD アカウントの両方からのサインインを受け付けるアプリを作成できます。 これにより、まったくアカウントに依存しないアプリを作成できます。これは、ユーザーがサインインするのに使用するアカウントの種類を考慮する必要がないことを意味します。 特定のセッションで使用されているアカウントの種類をアプリで確認するようにもできますが、そうする必要はありません。

たとえば、アプリで [Microsoft Graph](https://graph.microsoft.io)を呼び出す場合、エンタープライズ環境のユーザーは、SharePoint サイトや Directory データなどの追加の機能とデータを使用できます。 ただし、 [ユーザーのメールを読む](https://graph.microsoft.io/docs/api-reference/v1.0/resources/message)などの多くの操作について、Microsoft アカウントと Azure AD アカウントの両方に対して完全に同じコードを作成できます。 

Microsoft アカウントと Azure AD アカウントのアプリを 1 つの単純なプロセスで統合できるようになりました。 1 組のエンドポイント、1 つのライブラリ、1 回のアプリ登録によって、コンシューマー向けおよびエンタープライズ向けの両方の環境にアクセスできます。 v2.0 エンドポイントの詳細については、 [概要](active-directory-appmodel-v2-overview.md)のページを参照してください。

## <a name="new-app-registration-portal"></a>新しいアプリ登録ポータル

v2.0 エンドポイントで動作するアプリを登録するには、Microsoft のアプリ登録ポータル [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) を使用する必要があります。 このポータルでは、アプリケーション ID の取得、アプリのサインイン ページの外観のカスタマイズなどを行うことができます。 ポータルにアクセスするために必要なのは、Microsoft の強化されたアカウント (個人アカウントまたは職場/学校アカウントのいずれか) だけです。

## <a name="one-app-id-for-all-platforms"></a>1 つのアプリ ID ですべての製品に対応

Azure AD を使用していた場合は、1 つのプロジェクトに対して異なる複数のアプリが登録されている可能性があります。 たとえば、Web サイトと iOS アプリの両方を構築する場合は、2 つの異なるアプリケーション ID を使用して、それぞれを別々に登録する必要がありました。 Azure AD アプリの登録ポータルでは、登録時にこの区別を強制していました。

![古いアプリケーションの登録 UI](./media/azure-ad-endpoint-comparison/old_app_registration.PNG)

同様に、Web サイトとバックエンド Web API を使用していた場合は、異なるアプリとして Azure AD にそれぞれ登録されている可能性があります。 また、iOS アプリと Android アプリを使用していた場合は、2 つの異なるアプリが登録されている可能性があります。 アプリケーションの各々のコンポーネントを登録することで、開発者やその顧客が予期していなかった動作につながる場合があります。

* 各コンポーネントは、各顧客の Azure AD テナントに別個のアプリとして表示されます。
* テナント管理者が、アプリに対してポリシー適用、アクセスの管理、削除などを試みる場合は、アプリの各コンポーネントに対して実行する必要があります。
* 顧客がアプリケーションに同意した場合、各コンポーネントは個別のアプリケーションとして同意画面に表示されます。

v2.0 エンドポイントでは、プロジェクトのすべてのコンポーネントを単一のアプリ登録として登録でき、プロジェクト全体に対して 1 つのアプリケーション ID を使用できるようになりました。 それぞれのプロジェクトに複数の "プラットフォーム" を追加し、追加した各プラットフォームに対して適切なデータを提供できます。 もちろんアプリは要件に応じて必要な数だけ作成できますが、大半の場合、必要なアプリケーション ID は 1 つだけです。

マイクロソフトが目指しているのは、アプリの管理と開発のエクスペリエンスをいっそう簡素化し、より高度に統合されたビューで作業中の各プロジェクトの状況を把握できるようにすることです。

## <a name="scopes-not-resources"></a>リソースではなくスコープ

Azure AD では、アプリは**リソース**またはトークンの受信者として動作できます。 リソースには、リソースで識別できる多数の**スコープ**または **oAuth2Permissions** を定義できます。それによりクライアント アプリは、そのリソースに特定のスコープのセットのトークンを要求できます。 リソースの例として、Azure AD Graph API があります。

* リソース識別子、または `AppID URI`:`https://graph.windows.net/`
* スコープ、または `OAuth2Permissions`:`Directory.Read`、`Directory.Write` などです。 

このすべてが v2.0 エンドポイントに当てはまります。 アプリはリソースとして動作し、スコープを定義することができ、URI によって識別できます。 また、クライアント アプリは、スコープへのアクセスを要求できます。 ただし、クライアントがそれらのアクセス許可を要求する方法が変更されました。 以前は、Azure AD への OAuth 2.0 承認要求は、次のようになっていました。

```
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https%3A%2F%2Fgraph.windows.net%2F
...
```

ここで、 **resource** パラメーターは、クライアント アプリが認証を求めている対象のリソースを示します。 Azure AD は、Azure Portal での静的な構成に基づいて、アプリによって要求されたアクセス許可を計算し、適切なトークンを発行していました。 現在は、同じ OAuth 2.0 承認要求が次のようになっています。

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

ここで、**scope** パラメーターは、アプリが承認を要求している対象のリソースとアクセス許可を示します。 要求されているリソースはまだ要求内に存在していて、scope パラメーターの各値の中に、ただ含まれています。 このように scope パラメーターを使用すると、v2.0 エンドポイントの OAuth 2.0 仕様への準拠を高め、一般的な業界の慣行に近づけることができます。 また、アプリで[増分同意](#incremental-and-dynamic-consent)を実行できます。次のセクションでこれについて説明します。

## <a name="incremental-and-dynamic-consent"></a>増分および動的な同意

Azure AD サービスに登録されるアプリは、以前、アプリ作成時に Azure Portal で要求された OAuth 2.0 のアクセス許可を指定する必要がありました。

![アクセス許可の登録 UI](./media/azure-ad-endpoint-comparison/app_reg_permissions.PNG)

アプリが要求したアクセス許可は **静的に**構成されていました。 これにより、Azure Portal 内にアプリの構成が存在するようにでき、コードを適切に維持できますが、開発者にとっては、それがいくつかの問題を引き起こしています。

* アプリの作成時に、そのアプリで必要になる可能性のあるすべてのアクセス許可がわかっている必要があります。 後からアクセス許可を追加することは困難なプロセスでした。
* また、アプリでアクセスする可能性のあるすべてのリソースが事前にわかっている必要があります。 任意の数のリソースにアクセスするアプリを作成することは、困難でした。
* アプリは、ユーザーの初回サインインの時点で、そのアプリで必要になる可能性があるすべてのアクセス許可を要求する必要があります。 アクセス許可のリストは長くなる場合があり、エンドユーザーが初回サインイン時にアプリのアクセスを承認することを阻げる要因となっていました。

v2.0 エンドポイントでは、アプリの通常の使用状況で、アプリに必要なアクセス許可を実行時に **動的に**指定できます。 これを行うには、任意の時点でアプリに必要なスコープを承認要求の `scope` パラメーターで指定できます。

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

この要求は、アプリで Azure AD ユーザーのディレクトリ データの読み取りおよび書き込みのためのアクセス許可を求めています。 ユーザーが過去にこの特定のアプリでこれらのアクセス許可に同意した場合、資格情報を入力してアプリにサインインできます。 ユーザーがこれらのアクセス許可のいずれにも同意していない場合、v2.0 エンドポイントはこれらのアクセス許可に対する同意をユーザーに求めます。 詳細については、 [アクセス許可、同意、スコープ](v2-permissions-and-consent.md)の説明を参照してください。

`scope` パラメーターを使用してアプリからアクセス許可を動的に要求できるため、ユーザーの操作を完全に制御できます。 必要に応じて、同意の操作を初期段階に組み込み、1 回の初期認証要求ですべてのアクセス許可を求めます。 または、アプリで多数のアクセス許可が必要な場合は、時間の経過と共に、ユーザーがアプリの特定の機能を使用するときにアクセス許可を集めることができます。

## <a name="well-known-scopes"></a>既知のスコープ

### <a name="offline-access"></a>オフライン アクセス

v2.0 エンドポイントを使用するアプリでは、アプリ向けで既知の新たなアクセス許可 (`offline_access` スコープ) を使用する必要がある場合があります。 すべてのアプリは、ユーザーがアプリを積極的に利用しない可能性がある場合でも、長期間ユーザーに代わってリソースにアクセスするために必要な、このアクセス許可を要求する必要があります。 `offline_access` スコープは、コンテンツ ダイアログで "データにオフラインでアクセスします" と表示されます。 `offline_access` アクセス許可を要求すると、Web アプリで v2.0 エンドポイントから OAuth 2.0 の refresh_tokens を受け取ることができます。 refresh_tokens は、長期間維持され、アクセスの期間を延長するために新しい OAuth 2.0 の access_tokens と交換することができます。 

アプリが `offline_access` スコープを要求しない場合、refresh_tokens を受け取ることはありません。 つまり、OAuth 2.0 承認コード フローの authorization_code を使用すると、`/token` エンドポイントから access_token だけが取得されます。 その access_token は、短時間 (通常は 1 時間) 有効ですが、最終的には期限が切れます。 その時点で、アプリはユーザーを `/authorize` エンドポイントにリダイレクトして、新しい authorization_code を取得する必要があります。 このリダイレクト中に、アプリの種類によっては、ユーザーが資格情報を再入力したり、アクセス許可に再同意したりする必要がある場合もあります。

OAuth 2.0、refresh_tokens、および access_tokens の詳細については、[v2.0 プロトコルのリファレンス](active-directory-v2-protocols.md)を参照してください。

### <a name="openid-profile-and-email"></a>OpenID、プロファイル、および電子メール

これまでは、Azure AD を使用する最も基本的な OpenID 接続サインイン フローによって、結果の id_token 内でユーザーに関する多くの情報が多数提供されていました。 id_token 内の要求には、ユーザー名、推奨ユーザー名、電子メール アドレス、オブジェクト ID などを含めることができます。

アプリが `openid` のスコープでアクセスできる情報は、現在では制限されています。 `openid` スコープでアプリに許可されるのは、ユーザーのサインインと、ユーザーのためにアプリ固有の ID を受信することだけです。 アプリでユーザーに関する個人データを取得する場合は、アプリからユーザーに追加のアクセス許可を要求する必要があります。 2 つの新しいスコープである `email` スコープと `profile` スコープを使用すると、追加のアクセス許可を要求できます。

`email` スコープでは、アプリは id_token の `email` 要求を使用して、ユーザーのプライマリ電子メール アドレスにアクセスできます。 

`profile` スコープでは、アプリに名前、推奨ユーザー名、オブジェクト ID など、ユーザーに関するその他のすべての基本的な情報へのアクセスを許可します。

これにより、アプリの公開を最小限にとどめてコードが書けます。アプリがジョブを実行するために必要な情報セットのみをユーザーに要求できます。 これらのスコープの詳細については、[v2.0 スコープのリファレンス](v2-permissions-and-consent.md)を参照してください。

## <a name="token-claims"></a>トークン要求

v2.0 エンドポイントによって発行されるトークン内の要求は、一般に使用できる Azure AD エンドポイントによって発行されるトークンと同じにはなりません。 新しいサービスに移行するアプリで、id_tokens または access_tokens 内に特定の要求が存在すると想定しないでください。 v2.0 トークンによって発行される特定の要求の詳細については、 [v2.0 トークン リファレンス](v2-id-and-access-tokens.md)を参照してください。

## <a name="limitations"></a>制限事項

v2.0 エンドポイントを使用する場合に注意する必要があるいくつかの制限があります。 これらの制限事項が実際のシナリオに当てはまるかどうかについては、[v2.0 の制限事項に関するドキュメント](active-directory-v2-limitations.md)を参照してください。
