---
title: Azure Active Directory B2C で暗黙的フローを使用するシングルページ アプリ | Microsoft Docs
description: Azure Active Directory B2C で OAuth 2.0 暗黙的フローを使用して、シングルページ アプリケーションを直接構築する方法を説明します。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/06/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ee341fd3b54d748849da34cd11db30e5ea758fb1
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445271"
---
# <a name="azure-ad-b2c-single-page-app-sign-in-by-using-oauth-20-implicit-flow"></a>Azure AD B2C: OAuth 2.0 暗黙的フローを使用したシングルページ アプリへのサインイン

最新アプリの多くには、主に JavaScript で記述されたシングル ページ アプリのフロントエンドがあります。 アプリが、AngularJS、Ember.js、Durandal.js などのフレームワークを使用して記述されていることもよくあります。 主にブラウザーで実行される、シングル ページ アプリなどの JavaScript アプリには、認証に関していくつかの追加の課題があります。

* これらのアプリのセキュリティ特性は、従来のサーバーベースの Web アプリケーションとは大きく異なります。
* 多くの承認サーバーや ID プロバイダーでは、クロス オリジン リソース共有 (CORS) 要求をサポートしていません。
* アプリからブラウザーにフル ページがリダイレクトされると、ユーザー エクスペリエンスに大きな悪影響が及ぶ場合があります。

これらのアプリケーションをサポートするために、Azure Active Directory B2C (Azure AD B2C) は OAuth 2.0 暗黙的フローを使用します。 OAuth 2.0 承認の暗黙的許可フローは、[OAuth 2.0 仕様のセクション 4.2](http://tools.ietf.org/html/rfc6749) で説明されています。 暗黙的フローでは、アプリは Azure Active Directory (Azure AD) 承認エンドポイントから直接トークンを受け取るため、サーバー間の交換は実行されません。 すべての認証ロジックとセッション処理は、追加のページ リダイレクトなしに、JavaScript クライアント内ですべて行うことができます。

Azure AD B2C によって、標準の OAuth 2.0 暗黙的フローが、単純な認証と承認以上まで拡張されます。 Azure AD B2C には、[ポリシー パラメーター](active-directory-b2c-reference-policies.md)が導入されています。 ポリシー パラメーターと共に OAuth 2.0 を使用して、サインアップ、サインイン、プロファイル管理などのユーザー エクスペリエンスをアプリに追加できます。 この記事では、暗黙的フローと Azure AD を使用して、シングルページ アプリケーションにこれらの各エクスペリエンスを実装する方法を説明します。 作業の開始に役立てるために、[Node.JS](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi) や [.NET](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi) のサンプルを参照してください。

この記事の HTTP 要求例では、サンプルの Azure AD B2C ディレクトリ **fabrikamb2c.onmicrosoft.com** を使用します。 また、マイクロソフト独自のサンプル アプリケーションとポリシーを使用します。 それらの値を利用して、要求を試すことができます。または、独自の値で置き換えることもできます。
[独自の Azure AD B2C ディレクトリ、アプリケーション、ポリシーの取得方法](#use-your-own-b2c-tenant)について学習してください。


## <a name="protocol-diagram"></a>プロトコルのダイアグラム

暗黙的サインイン フローは、次の図のようになっています。 各手順については、この記事の後の方で詳しく説明します。

![OpenID Connect のスイムレーン](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>認証要求を送信する
Web アプリでユーザーを認証し、ポリシーを実行する必要があるときは、ユーザーを `/authorize` エンドポイントにリダイレクトさせます。 これは、ユーザーがポリシーに応じてアクションを実行する、フローの対話的な部分です。 ユーザーは、Azure AD エンドポイントから ID トークンを取得します。

この要求で、クライアントは、`scope` パラメーターで、ユーザーから取得する必要のあるアクセス許可を指定します。 `p` パラメーターでは、実行するポリシーを指定します。 3 つの例を以下に示します (読みやすいように改行してあります)。それぞれ異なるポリシーが使用されています。 各要求の動作を感覚的に理解するために、要求をブラウザーに貼り付け、実行してみてください。

### <a name="use-a-sign-in-policy"></a>サインイン ポリシーを使用する
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

### <a name="use-a-sign-up-policy"></a>サインアップ ポリシーを使用する
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

### <a name="use-an-edit-profile-policy"></a>プロファイル編集ポリシーを使用する
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| パラメーター | 必須 | 説明 |
| --- | --- | --- |
| client_id |必須 |[Azure Portal](https://portal.azure.com) でアプリに割り当てられたアプリケーション ID。 |
| response_type |必須 |OpenID Connect サインインでは、 `id_token` を指定する必要があります。 応答の種類として `token` を含めることもできます。 `token` を使用する場合、アプリは承認エンドポイントへ 2 度目の要求を行うことなく、すぐに承認エンドポイントからアクセス トークンを受け取ることができます。  応答の種類 `token` を使用する場合は、`scope` パラメーターに、トークンを発行するリソースを示すスコープを含める必要があります。 |
| redirect_uri |推奨 |アプリのリダイレクト URI。アプリは、この URI で認証応答を送受信することができます。 ポータルで登録したいずれかのリダイレクト URI と完全に一致させる必要があります (ただし、URL エンコードが必要)。 |
| response_mode |推奨 |結果として得られたトークンをアプリに返す際に使用するメソッドを指定します。  暗黙的フローの場合は `fragment` を使用します。 |
| scope |必須 |スコープのスペース区切りリスト。 1 つのスコープ値が、要求されている両方のアクセス許可を Azure AD に示します。 `openid` スコープは、ユーザーをサインインさせ、ID トークンの形式でユーザーに関するデータを取得するためのアクセス許可を示します。 (これについては、記事の後の方でさらに説明します。)Web アプリの場合、 `offline_access` スコープは任意です。 これは、アプリがリソースに長時間アクセスするには更新トークンが必要になることを示します。 |
| state |推奨 |要求に含まれ、トークンの応答でも返される値。 使用したい任意の内容の文字列を指定できます。 通常、クロスサイト リクエスト フォージェリ攻撃を防ぐために、ランダムに生成された一意の値が使用されます。 この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページなど) に関する情報をエンコードする目的にも使用されます。 |
| nonce |必須 |要求に追加する (アプリによって生成された) 値。この値が、最終的な ID トークンに要求として追加されます。 アプリでこの値を確認することにより、トークン再生攻撃を緩和することができます。 通常この値はランダム化された一意の文字列になっており、要求の送信元を特定する際に使用できます。 |
| p |必須 |実行するポリシー。 Azure AD B2C テナントで作成されたポリシーの名前です。 ポリシー名の値は、**b2c\_1\_** で始まっている必要があります。 詳細については、「[Azure AD B2C 組み込みのポリシー](active-directory-b2c-reference-policies.md)」を参照してください。 |
| prompt |省略可能 |必要とされている、ユーザーとの対話の種類。 現在有用な値は `login` のみです。 これによってユーザーは、その要求で資格情報の入力を強制されます。 シングル サインオンは作用しません。 |

この時点で、ユーザーはポリシーのワークフローを完了するよう求められます。 ユーザー名とパスワードを入力したり、ソーシャル ID でサインインしたり、ディレクトリにサインアップしたりするなど、いくつかの手順が必要なことがあります。 ユーザー アクションは、ポリシーがどのように定義されているかによって異なります。

ユーザーがポリシーを完了すると、Azure AD はユーザーが `redirect_uri` に使用した値でアプリに応答を返します。 これには、`response_mode` パラメーターで指定されたメソッドが使用されます。 応答は、実行されたポリシーとは無関係に、ユーザー アクションの各シナリオでまったく同じになります。

### <a name="successful-response"></a>成功応答
`response_mode=fragment` と `response_type=id_token+token` を使用している成功応答は、次のようになります (読みやすいように改行してあります)。

```
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
```

| パラメーター | 説明 |
| --- | --- |
| access_token |アプリが要求したアクセス トークン。  アクセス トークンはデコードしないようにする必要があります。そうしないと検証が行われます。 不明瞭な文字列として処理できます。 |
| token_type |トークン タイプ値。 Azure AD でサポートされるのは Bearer タイプのみです。 |
| expires_in |アクセス トークンが有効な時間の長さ (秒単位)。 |
| scope |トークンが有効である範囲。 スコープは、後で使用できるようトークンをキャッシュするためにも使用できます。 |
| id_token |アプリが要求した ID トークン。 この ID トークンを使用してユーザーの本人性を確認し、そのユーザーとのセッションを開始することができます。 ID トークンとその内容の詳細については、「[Azure AD B2C トークン リファレンス](active-directory-b2c-reference-tokens.md)」を参照してください。 |
| state |要求に `state` パラメーターが含まれている場合、同じ値が応答にも含まれることになります。 アプリは、要求と応答の `state` 値が同一であることを検証する必要があります。 |

### <a name="error-response"></a>エラー応答
アプリ側でエラーを適切に処理できるように、エラー応答をリダイレクト URI に送信することもできます。

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| パラメーター | 説明 |
| --- | --- |
| error |発生したエラーの種類を分類するために使用されるエラー コード文字列。 このエラー コードは、エラー処理にも使用できます。 |
| error_description |認証エラーの根本的な原因を特定しやすいように記述した具体的なエラー メッセージ。 |
| state |前の表の詳しい説明を参照してください。 要求に `state` パラメーターが含まれている場合、同じ値が応答にも含まれることになります。 アプリは、要求と応答の `state` 値が同一であることを検証する必要があります。|

## <a name="validate-the-id-token"></a>ID トークンの検証
ID トークンを受信してもユーザーの認証には不十分です。 ID トークンの署名を検証し、アプリの要件ごとにトークン内の要求を確認する必要があります。 Azure AD B2C は、[JSON Web トークン (JWT)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) と公開キー暗号を使用してトークンに署名し、それらが有効であることを証明します。

使用する言語に応じて、JWT を検証するためにさまざまなオープン ソース ライブラリを利用できます。 独自の検証ロジックを実装するより、利用できるオープンソース ライブラリを試すことを検討してください。 この記事の情報が、これらのライブラリを適切に使用する方法を学ぶ助けになります。

Azure AD B2C には、OpenID Connect メタデータ エンドポイントがあります。 アプリはこのエンドポイントを使用して、実行時に Azure AD B2C に関する情報を取得できます。 この情報には、エンドポイント、トークンの内容、トークンの署名キーが含まれます。 Azure AD B2C テナントにはポリシー別の JSON メタデータ ドキュメントがあります。 たとえば、fabrikamb2c.onmicrosoft.com テナントの b2c_1_sign_in ポリシーのメタデータ ドキュメントは、次の場所にあります。

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

この構成ドキュメントのプロパティの 1 つは `jwks_uri` です。 同じポリシーの値は次のようになります。

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`

どのポリシーが ID トークンの署名に使用されたかと、どこからメタデータを取得できるかは、2 つの方法で判断できます。 最初の方法では、ポリシー名が `id_token` の `acr` 要求に含まれています。 ID トークンの要求を解析する方法については、「[Azure AD B2C トークン リファレンス](active-directory-b2c-reference-tokens.md)」を参照してください。 別の方法は次のようになります。要求を発行するときに、`state` パラメーターの値に含まれるポリシーを符号化します。 その後、`state` パラメーターを復号化して、どのポリシーが使用されたかを確認します。 どちらの方法も有効です。

OpenID Connect メタデータ エンドポイントからメタデータ ドキュメントを取得したら、このエンドポイントにある RSA-256 公開キーを利用し、ID トークンの署名を検証できます。 このエンドポイントには、特定の時点で、それぞれが `kid` によって識別されるキーが複数存在すると表示される場合があります。 `id_token` のヘッダーにも `kid` 要求が含まれています。 これが、これらのキーのうち、どれが ID トークンの署名に使用されたかを示しています。 [トークンの検証](active-directory-b2c-reference-tokens.md#token-validation)を含む詳細については、[Azure AD B2C トークン リファレンス](active-directory-b2c-reference-tokens.md)を参照してください。
<!--TODO: Improve the information on this-->

ID トークンの署名の検証後に、いくつかの要求で検証が必要になります。 例: 

* トークン再生攻撃を防止するために、`nonce` 要求を検証します。 その値が、サインイン要求で指定した値と一致していることが必要です。
* ID トークンが自分のアプリのために発行されたことを確認するために、`aud` 要求を検証します。 その値がアプリのアプリケーション ID と一致している必要があります。
* ID トークンの有効期限が切れていないことを確認するために、`iat` 要求と `exp` 要求を検証します。

他にも実行する必要があるいくつかの検証については、[OpenID Connect Core の仕様](http://openid.net/specs/openid-connect-core-1_0.html)で詳しく説明されています。シナリオに応じてその他の要求も検証することができます。 以下に一般的な検証の例をいくつか挙げます。

* ユーザーまたは組織がアプリにサインアップ済みであることを確認する。
* ユーザーが適切な承認や特権を持っていることを確認する。
* Azure Multi-Factor Authentication の使用など、特定の強度の認証が行われたことを確認する。

ID トークンに含まれる要求の詳細については、[Azure AD B2C トークン リファレンス](active-directory-b2c-reference-tokens.md)を参照してください。

ID トークンの検証を完了したら、ユーザーとのセッションを開始できます。 アプリでは、ID トークン内の要求を使用してユーザーに関する情報を取得できます。 取得した情報は、表示、記録、承認などに利用することができます。

## <a name="get-access-tokens"></a>アクセス トークンを取得する
Web アプリで必要なのはポリシーを実行することだけの場合、以降のセクションの一部を省略できます。 以降のセクションの情報が当てはまるのは、認証される呼び出しを Web API に対して行う必要があり、Azure AD B2C によって保護されている Web アプリのみです。

ユーザーをシングル ページ アプリにサインインさせたので、Azure AD によってセキュリティ保護されている Web API を呼び出すためのアクセス トークンを取得できます。 応答の種類として `token` を使用して既にトークンを取得済みの場合でも、このメソッドを使用してその他のリソースのトークンを取得できます。再度のサインインのためにユーザーをリダイレクトする必要はありません。

一般的な Web アプリ フローでは、`/token` エンドポイントへの要求を作成してトークンの取得を行います。  ただし、エンドポイントは CORS 要求をサポートしていないため、AJAX 呼び出しではトークンの取得や更新を行えません。 代わりに、非表示の HTML iframe 要素で暗黙的フローを使用して、他の Web API 用の新しいトークンを取得できます。 次に例を示します (読みやすいように改行してあります)。

```

https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
&response_mode=fragment
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
&p=b2c_1_sign_in
```

| パラメーター | 必須 | 説明 |
| --- | --- | --- |
| client_id |必須 |[Azure Portal](https://portal.azure.com) でアプリに割り当てられたアプリケーション ID。 |
| response_type |必須 |OpenID Connect サインインでは、 `id_token` を指定する必要があります。  応答の種類として `token` を含めることもできます。 ここで `token` を使用する場合、アプリは承認エンドポイントへ 2 度目の要求を行うことなく、すぐに承認エンドポイントからアクセス トークンを受け取ることができます。 応答の種類 `token` を使用する場合は、`scope` パラメーターに、トークンを発行するリソースを示すスコープを含める必要があります。 |
| redirect_uri |推奨 |アプリのリダイレクト URI。アプリは、この URI で認証応答を送受信することができます。 ポータルで登録したいずれかのリダイレクト URI と完全に一致させる必要があります (ただし、URL エンコードが必要)。 |
| scope |必須 |スコープのスペース区切りリスト。  トークンを取得するには、意図したリソースに必要なすべてのスコープを含めます。 |
| response_mode |推奨 |結果として得られたトークンをアプリに返す際に使用するメソッドを指定します。  `query`、`form_post`、または `fragment` を指定できます。 |
| state |推奨 |要求に含まれ、トークンの応答として返される値。  使用したい任意の内容の文字列を指定できます。  通常、クロスサイト リクエスト フォージェリ攻撃を防ぐために、ランダムに生成された一意の値が使用されます。  この状態は、認証要求の前にアプリ内のユーザーの状態に関する情報をエンコードする目的にも使用されます。 たとえば、ユーザーがいるページまたはビューです。 |
| nonce |必須 |要求に追加する (アプリによって生成された) 値。この値が、最終的な ID トークンに要求として追加されます。  アプリでこの値を確認することにより、トークン再生攻撃を緩和することができます。 通常この値は、要求の送信元を特定する、ランダム化された一意の文字列です。 |
| prompt |必須 |非表示の iframe のトークンを更新および取得するには、`prompt=none` を使用して、iframe がサインイン ページに停滞せずにすぐに応答できるようにします。 |
| login_hint |必須 |非表示の iframe のトークンを更新および取得するには、ある時点でそのユーザーが持っている可能性がある複数のセッションを区別できるように、このヒントにそのユーザーのユーザー名を含めます。 `preferred_username` 要求を使用すると、以前のサインインからユーザー名を抽出できます。 |
| domain_hint |必須 |`consumers` または `organizations` を指定できます。  非表示の iframe のトークンを更新および取得するには、要求に `domain_hint` 値を含める必要があります。  使用する値を決定するには、以前のサインイン時の ID　トークンから `tid` 要求を抽出します。  `tid` 要求の値が `9188040d-6c67-4c5b-b112-36a304b66dad` の場合、`domain_hint=consumers` を使用します。  それ以外の場合は、 `domain_hint=organizations`を指定します。 |

`prompt=none` パラメーターを設定することによって、この要求はすぐに成功または失敗のいずれかになり、アプリケーションに戻ります。  成功すると、`response_mode` パラメーターで指定された方法を使用して、指定されたリダイレクト URI でアプリに応答が送信されます。

### <a name="successful-response"></a>成功応答
`response_mode=fragment` を使用した場合の成功応答は次のようになります。

```
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
```

| パラメーター | 説明 |
| --- | --- |
| access_token |アプリが要求したトークン。 |
| token_type |トークンの種類は常にベアラーになります。 |
| state |要求に `state` パラメーターが含まれている場合、同じ値が応答にも含まれることになります。 アプリは、要求と応答の `state` 値が同一であることを検証する必要があります。 |
| expires_in |アクセス トークンの有効期間 (秒)。 |
| scope |アクセス トークンが有効である範囲。 |

### <a name="error-response"></a>エラー応答
アプリ側でエラーを適切に処理できるように、エラー応答をリダイレクト URI に送信することもできます。  `prompt=none` の場合、予期されるエラーは次のようになります。

```
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| パラメーター | 説明 |
| --- | --- |
| error |発生したエラーの種類を分類するために使用できるエラー コード文字列。 この文字列はエラーへの対応に利用することもできます。 |
| error_description |認証エラーの根本的な原因を特定しやすいように記述した具体的なエラー メッセージ。 |

Iframe 要求でこのエラーを受信した場合、ユーザーは対話形式でもう一度サインインして新しいトークンを取得する必要があります。 これは、アプリケーションにとって合理的な方法で処理できます。

## <a name="refresh-tokens"></a>更新トークン
ID トークンとアクセス トークンは、どちらも短時間で期限切れになります。 これらのトークンを定期的に更新するようにアプリを準備する必要があります。  どちらの種類のトークンを更新する場合も、Azure AD のステップを制御する `prompt=none` パラメーターを使用して、前の例で使用したのと同じ非表示の iframe 要求を実行します。  新しい `id_token` を取得するには、必ず `response_type=id_token`、`scope=openid`、および `nonce` パラメーターを使用します。

## <a name="send-a-sign-out-request"></a>サインアウト要求を送信する
ユーザーをアプリからサインアウトさせる場合は、サインアウトする Azure AD にユーザーをリダイレクトします。そうしない場合、ユーザーは資格情報を再入力しなくてもアプリで再認証されることがあります。 これは、Azure AD のシングル サインオン セッションが有効であるためです。

ユーザーを単純に、「[ID トークンの検証](#validate-the-id-token)」で説明したのと同じ OpenID Connect メタデータ ドキュメントに列挙されている `end_session_endpoint` にリダイレクトすることができます。 例: 

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| パラメーター | 必須 | 説明 |
| --- | --- | --- |
| p |必須 |ユーザーをアプリケーションからサインアウトさせるために使用するポリシー。 |
| post_logout_redirect_uri |推奨 |サインアウトの正常終了後にユーザーをリダイレクトする URL。これが含まれていない場合、Azure AD B2C はユーザーに一般的なメッセージを表示します。 |

> [!NOTE]
> ユーザーを `end_session_endpoint` にリダイレクトすると、ユーザーの Azure AD B2C でのシングル サインオン状態の一部が消去されます。 ただしそれによって、そのユーザーが自分のソーシャル ID プロバイダー セッションからサインアウトされることはありません。 ユーザーがその後のサインインで同じ ID プロバイダー を選択した場合は、そのユーザーは資格情報を入力しなくても再認証されます。 ユーザーが Azure AD B2C アプリケーションからサインアウトする場合、たとえば、Facebook アカウントから完全なサインアウトするのを望んでいるとは限りません。 ただしローカル アカウントについては、そのユーザーのセッションは適切に終了されます。
> 
> 

## <a name="use-your-own-azure-ad-b2c-tenant"></a>独自の Azure AD B2C テナントを使用する
これらの要求を自分で試すには、次の 3 つの手順を完了します。 この記事で使用した値の例を、独自の値に置き換えます。

1. [Azure AD B2C テナントを作成](active-directory-b2c-get-started.md)します。 要求で独自のテナントの名前を使用します。
2. [アプリケーションを作成し](active-directory-b2c-app-registration.md)、アプリケーション ID と `redirect_uri` 値を取得します。 アプリ内に Web アプリまたは Web API を含めます。 必要に応じて、アプリケーション シークレットを作成できます。
3. [ポリシーを作成し](active-directory-b2c-reference-policies.md) 、ポリシー名を取得します。

## <a name="samples"></a>サンプル

* [Node.js を使用してシングルページ アプリを作成する](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi)
* [.NET を使用してシングルページ アプリを作成する](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi)

