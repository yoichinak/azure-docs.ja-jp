---
title: Azure Active Directory B2C で使用できるアプリケーションの種類 | Microsoft Docs
description: Azure Active Directory B2C で使用できるアプリケーションの種類について説明します。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/13/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e42bc63b0c2b6edf4dc0de204bbac5fe90071a67
ms.sourcegitcommit: fc5555a0250e3ef4914b077e017d30185b4a27e6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39480514"
---
# <a name="applications-types-that-can-be-used-in-active-directory-b2c"></a>Azure Active Directory B2C で使用できるアプリケーションの種類

Azure Active Directory (Azure AD) B2C は、さまざまな最新アプリケーション アーキテクチャの認証をサポートします。 すべての認証は、業界標準のプロトコルである [OAuth 2.0](active-directory-b2c-reference-protocols.md) または [OpenID Connect](active-directory-b2c-reference-protocols.md) に基づいています。 このドキュメントでは、利用する言語やプラットフォームを問わず、作成できるアプリの種類について説明します。 また、アプリケーションの構築を始める前にシナリオの概要を理解することもできます。

Azure AD B2C を使用しているすべてのアプリケーションは、[Azure Portal](https://portal.azure.com/) を使用して [Azure AD B2C テナント](active-directory-b2c-get-started.md)に登録する必要があります。 アプリケーション登録プロセスでは、次のような値が収集されて割り当てられます。

* アプリケーションを一意に識別する **アプリケーション ID**。
* 応答をアプリケーションにリダイレクトして戻すために使用できる **リダイレクト URI**。

Azure AD B2C に送信される各要求は、 **ポリシー**を指定します。 ポリシーは、Azure AD の動作を制御します。 これらのエンドポイントを使用して、高度にカスタマイズ可能なユーザー エクスペリエンスのセットを作成することもできます。 一般的なポリシーとしては、サインアップ ポリシー、サインイン ポリシー、プロファイル編集ポリシーなどがあります。 これらのポリシーを詳しく理解していない場合は、先に進む前に Azure AD B2C の「 [拡張可能ポリシー フレームワーク](active-directory-b2c-reference-policies.md) 」を参照する必要があります。

すべてのアプリケーションによるやり取りは、次のような大まかなパターンに従って行われます。

1. アプリケーションは、[ポリシー](active-directory-b2c-reference-policies.md)を実行するためにユーザーを v2.0 エンドポイントにリダイレクトします。
2. ユーザーがポリシーの定義に従ってポリシーを完了します。
3. アプリケーションが v2.0 エンドポイントからセキュリティ トークンを受け取ります。
4. アプリケーションは、セキュリティ トークンを使って、保護された情報またはリソースにアクセスします。
5. リソース サーバーは、セキュリティ トークンを検証し、アクセスを許可できることを確認します。
6. アプリケーションは、セキュリティ トークンを定期的に更新します。

これらの手順は、構築しているアプリケーションの種類によりわずかに異なることがあります。

## <a name="web-applications"></a>Web アプリケーション

サーバーでホストされ、ブラウザーを通じてアクセスされる Web アプリケーション (.NET、PHP、Java、Ruby、Python、Node.js など) に対して、Azure AD B2C は、すべてのユーザー エクスペリエンスで [OpenID Connect](active-directory-b2c-reference-protocols.md) をサポートします。 これには、サインイン、サインアップ、およびプロファイル管理が含まれます。 Azure AD B2C の OpenID Connect の実装では、Web アプリケーションは、これらのユーザーエクスペリエンスを Azure AD に認証要求を発行することで開始します。 要求の結果は `id_token`です。 このセキュリティ トークンは、ユーザーの ID を表します。 また、要求の形式でユーザーに関する情報も提供します。

```
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

アプリケーションで利用できるトークンと要求の種類の詳細については、[Azure AD B2C トークン リファレンス](active-directory-b2c-reference-tokens.md)のページを参照してください。

Web アプリケーションでは、[ポリシー](active-directory-b2c-reference-policies.md)を実行するたびに、次に示す大まかな手順が実行されます。

1. ユーザーが Web アプリケーションに移動する。
2. Web アプリケーションが実行するポリシーを示す Azure AD B2C にユーザーをリダイレクトする。
3. ユーザーがポリシーを完了する。
4. Azure AD B2C が `id_token` をブラウザーに返す。
5. `id_token` がリダイレクト URI にポストされる。
6. `id_token` が検証され、セッション cookie が設定される。
7. セキュリティで保護されたページがユーザーに返される。

ユーザーの ID を確認するには、Azure AD から受け取った公開署名キーを使用して `id_token` を検証するだけで十分です。 これにより、以降のページ要求でユーザーを識別するために使用できるセッション Cookie も設定されます。

このシナリオを実際に確認するには、[作業開始](active-directory-b2c-overview.md)に関するセクションのいずれかの Web アプリケーション サインイン コード サンプルを試してください。

Web サーバー アプリケーションは、サインインをシンプルにするだけでなく、他のバックエンド Web サービスにアクセスすることが必要な場合もあります。 このような場合、Web アプリケーションでは少し異なる [OpenID Connect フロー](active-directory-b2c-reference-oidc.md) を実行し、承認コードと更新トークンを使用してトークンを取得することができます。 このシナリオについては、次の「 [Web API](#web-apis)」セクションで説明します。

## <a name="web-apis"></a>Web API

Azure AD B2C を使用して、アプリケーションの RESTful Web API などの Web サービスをセキュリティで保護できます。 Web API では、OAuth 2.0 を使用し、トークンによって受信 HTTP 要求を認証することで、データをセキュリティで保護することができます。 Web API の呼び出し元は、HTTP 要求の承認ヘッダーの中にトークンを追加します。

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Web API はトークンを使用して API の呼び出し元の ID を検証し、トークン内にエンコードされているクレームから呼び出し元に関する情報を抽出します。 アプリで利用できるトークンと要求の種類の詳細については、 [Azure AD B2C トークン リファレンス](active-directory-b2c-reference-tokens.md)のページを参照してください。

> [!NOTE]
> 現在の Azure AD B2C では、Web API の既知のクライアントによってアクセスされる Web API のみをサポートしています。 たとえば、完成したアプリには、iOS アプリケーション、Android アプリケーション、バックエンド Web API を含めることができます。 このアーキテクチャは完全にサポートされます。 別の iOS アプリケーションなどのパートナー クライアントが同じ Web API にアクセスできるようにすることは、現時点ではサポートされていません。 完成したアプリケーションのすべてのコンポーネントは、1 つのアプリケーション ID を共有する必要があります。
>
>

Web API は、Web アプリケーション、デスクトップ アプリケーション、モバイル アプリケーション、シングル ページ アプリケーション、サーバー サイド デーモン、それ以外の Web API など、多くの種類のクライアントからトークンを受信できます。 ここでは、Web API を呼び出す Web アプリケーションの完全なフローの例を示します。

1. Web アプリケーションがポリシーを実行し、ユーザーがユーザー エクスペリエンスを完了する。
2. Azure AD B2C が `access_token` と承認コードをブラウザーに返す。
3. ブラウザーが `access_token` と承認コードをリダイレクト URI にポストする。
4. Web サーバーが `access token` を検証し、セッション cookie を設定する。
5. `access_token` が、承認コード、アプリケーション クライアント ID、および資格情報と共に Azure AD B2C に提供される。
6. `access_token` と `refresh_token` が Web サーバーに返される。
7. Authorization ヘッダーに `access_token` が含まれる Web API が呼び出される。
8. Web API がトークンを検証する。
9. セキュリティで保護されたデータが Web サーバーに返される。

承認コード、更新トークン、およびトークンの取得手順については、 [OAuth 2.0 プロトコル](active-directory-b2c-reference-oauth-code.md)に関するページを参照してください。

Azure AD B2C を使用して Web API をセキュリティ保護する方法の詳細については、Web API チュートリアルの「 [作業開始](active-directory-b2c-overview.md)」セクションを参照してください。

## <a name="mobile-and-native-applications"></a>モバイルおよびネイティブ アプリケーション

モバイル アプリケーションやデスクトップ アプリケーションなどのデバイスにインストールされているアプリケーションは、多くの場合、ユーザーの代わりにバックエンド サービスや Web API にアクセスする必要があります。 カスタマイズされた ID 管理エクスペリエンスをネイティブ アプリケーションに追加し、Azure AD B2C と [OAuth 2.0 の承認コード フロー](active-directory-b2c-reference-oauth-code.md)を使用して、バックエンド サービスを安全に呼び出すことができます。  

このフローでは、アプリケーションが[ポリシー](active-directory-b2c-reference-policies.md)を実行し、ユーザーがポリシーを完了した後、Azure AD から `authorization_code` を受け取ります。 `authorization_code` は、現在サインインしているユーザーに代わってバックエンド サービスを呼び出すためのアプリケーションのアクセス許可を表します。 これにより、アプリケーションはバックグラウンドで `authorization_code` を `id_token` および `refresh_token` と交換できます。  アプリケーションは `id_token` を使用し、HTTP 要求でバックエンド Web API への認証を行うことができます。 また、古い `id_token` の有効期限が切れたときは、`refresh_token` を使用して新しいものを取得できます。

> [!NOTE]
> 現在の Azure AD B2C では、アプリケーション独自のバックエンド Web サービスにアクセスするために使用されるトークンのみをサポートしています。 たとえば、完成したアプリケーションには、iOS アプリケーション、Android アプリケーション、バックエンド Web API を含めることができます。 このアーキテクチャは完全にサポートされます。 iOS アプリケーションが OAuth 2.0 アクセス トークンを使用してパートナーの Web API にアクセスできるようにすることは、現時点ではサポートされていません。 完成したアプリケーションのすべてのコンポーネントは、1 つのアプリケーション ID を共有する必要があります。
>
>

## <a name="current-limitations"></a>現時点での制限事項

Azure AD B2C では、現時点で以下の種類のアプリはサポートしていませんが、今後サポート対象に含まれる予定です。 

### <a name="daemonsserver-side-applications"></a>デーモン/サーバー側アプリケーション

長時間実行されるプロセスを含んだアプリケーションや、ユーザーの介入なしで動作するアプリケーションも、セキュリティで保護されたリソース (Web API など) にアクセスする必要があります。 これらのアプリケーションは、OAuth 2.0 クライアント資格情報フローを使用することで、(ユーザーの委任 ID ではなく) アプリケーションの ID を使って認証を行い、トークンを取得することができます。 クライアント資格情報フローは、On-Behalf-Of フローと同じではなく、On-Behalf-Of フローは、サーバー対サーバー認証には使用しないようにする必要があります。

クライアント資格情報フローは現在 Azure AD B2C によってサポートされていませんが、Azure AD を使用してクライアント資格情報フローを設定することができます。 Azure AD B2C テナントは、Azure AD のエンタープライズ テナントと同じいくつかの機能を持っています。  クライアント資格情報フローは、Azure AD B2C テナントの Azure AD の機能を使用してサポートされています。 

クライアント資格情報フローを設定するには、「[Azure Active Directory v2.0 と OAuth 2.0 クライアント資格情報フロー](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-client-creds)」を参照してください。 認証に成功した結果として、書式設定されたトークンを受信し、「[Azure AD トークン リファレンス](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)」で説明されているように Azure AD でそれを使用することができます。


### <a name="web-api-chains-on-behalf-of-flow"></a>Web API チェーン (On-Behalf-Of フロー)

多くのアーキテクチャには別のダウンストリーム Web API を呼び出す必要がある Web API が含まれ、その場合は両方とも Azure AD B2C によってセキュリティ保護されます。 このシナリオは、Web API バックエンドのあるネイティブ クライアントで一般的なものです。 その後は、Azure AD Graph API などの Microsoft オンライン サービスを呼び出します。

このように Web API を連鎖的に呼び出すシナリオは、OAuth 2.0 JWT Bearer Credential Grant (On-Behalf-Of フロー) を使用してサポートできます。  ただし、現時点では、Azure AD B2C に On-Behalf-Of フローは実装されていません。
