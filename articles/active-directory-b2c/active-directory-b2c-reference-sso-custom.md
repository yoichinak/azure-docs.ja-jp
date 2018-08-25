---
title: Azure Active Directory B2C のカスタム ポリシーを使用した SSO セッション管理 | Microsoft Docs
description: Azure AD B2C でカスタム ポリシーを使用して SSO セッションを管理する方法について説明します。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/20/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 351b48f2e2766b4974a5a41b5e95acfbd63dbfc9
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443224"
---
# <a name="azure-ad-b2c-single-sign-on-sso-session-management"></a>Azure AD B2C: シングル サインオン (SSO) セッション管理

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD B2C を使用すると、管理者は、ユーザーが既に認証された後に Azure AD B2C がそのユーザーと対話する方法を制御できます。 これは、SSO セッション管理を使用して行われます。 たとえば、管理者は、選択した ID プロバイダーを表示するかどうか、ローカル アカウントの詳細をもう一度入力する必要があるかどうかを制御できます。 この記事では、Azure AD B2C の SSO 設定を構成する方法について説明します。

## <a name="overview"></a>概要

SSO セッション管理は 2 つの部分で構成されています。 1 つはユーザーと Azure AD B2C の直接対話を処理し、もう 1 つはユーザーと Facebook などの外部パーティの対話を処理します。 Azure AD B2C は、外部パーティが保持している可能性のある SSO セッションをオーバーライドしたりバイパスしたりしません。 むしろ、Azure AD B2C を経由して外部パーティにアクセスするルートは "記憶される" ため、ユーザーにソーシャル ID プロバイダーやエンタープライズ ID プロバイダーを選択するよう再度求める必要がなくなります。 最終的な SSO の決定は、外部パーティに残ります。

## <a name="how-does-it-work"></a>それはどのように機能しますか?

SSO セッション管理では、カスタム ポリシー内のその他の技術プロファイルと同じセマンティクスが使用されます。 オーケストレーション ステップが実行されると、そのステップに関連付けられた技術プロファイルで `UseTechnicalProfileForSessionManagement` が照会されます。 存在する場合、参照されている SSO セッション プロバイダーを調べて、そのユーザーがセッション参加者であるかどうかを確認します。 参加者の場合は、セッションの再設定には SSO セッション プロバイダーが使用されます。 同様に、オーケストレーション ステップの実行が完了すると、SSO セッション プロバイダーが指定されている場合は、プロバイダーを使用してセッションに情報を格納します。

Azure AD B2C では、使用可能な多数の SSO セッション プロバイダーが定義されています。

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

SSO 管理クラスは、技術プロファイルの `<UseTechnicalProfileForSessionManagement ReferenceId=“{ID}" />` 要素を使用して指定されています。

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

名前が示すように、このプロバイダーは何もしません。 このプロバイダーは、特定の技術プロファイルの SSO 動作を抑制するために使用できます。

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

このプロバイダーは、要求をセッション内に保存するために使用できます。 通常、このプロバイダーは、ローカル アカウントの管理に使用される技術プロファイル内で参照されます。 

> [!NOTE]
> DefaultSSOSessionProvider を使用してセッションに要求を格納するときは、アプリケーションに返す必要のある要求、または後の手順の事前条件で使用される要求がセッションで保管されていること、あるいはディレクトリのユーザー プロファイルからの読み取りで増加していることを確認する必要があります。 これにより、要求がないことによる認証プロセスの失敗を防ぐことができます。

```XML
<TechnicalProfile Id="SM-AAD">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="objectId" />
        <PersistedClaim ClaimTypeReferenceId="newUser" />
        <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
    </PersistedClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true" />
    </OutputClaims>
</TechnicalProfile>
```

セッションに要求を追加するには、技術プロファイルの `<PersistedClaims>` 要素を使用します。 このプロバイダーを使用してセッションを再設定すると、保持されている要求は要求バッグに追加されます。 `<OutputClaims>` は、セッションから要求を取得する場合に使用されます。

### <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

このプロバイダーは、"ID プロバイダーの選択" 画面が表示されないようにする際に使用されます。 これは、通常、外部 ID プロバイダー (Facebook など) 用に構成された技術プロファイルで参照されます。 

```XML
<TechnicalProfile Id="SM-SocialLogin">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

このプロバイダーは、アプリ間だけでなく外部 SAML ID プロバイダー間の Azure AD B2C SAML セッションを管理する場合に使用されます。

```XML
<TechnicalProfile Id="SM-Reflector-SAML">
    <DisplayName>Session Management Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="IncludeSessionIndex">false</Item>
        <Item Key="RegisterServiceProviders">false</Item>
    </Metadata>
</TechnicalProfile>
```

技術プロファイルには 2 つのメタデータ項目があります。

| 項目 | 既定値 | 指定できる値 | 説明
| --- | --- | --- | --- |
| IncludeSessionIndex | true | true または false | セッション インデックスを格納する必要があることをプロバイダーに指示します。 |
| RegisterServiceProviders | true | true または false | アサーションが発行された SAML サービス プロバイダーすべてをプロバイダーが登録する必要があることを示します。 |

SAML ID プロバイダー セッションを保存する際にプロバイダーを使用する場合は、上記の項目を両方とも false にする必要があります。 B2C SAML セッションを保存する際にプロバイダーを使用する場合、上記の項目を true にするか、既定値が true のため省略する必要があります。

>[!NOTE]
> SAML セッションのログアウトを完了するには、`SessionIndex` と `NameID` が必要です。

## <a name="next-steps"></a>次の手順

ご意見とご提案をお待ちしています。 このトピックで問題が発生した場合は、Stack Overflow にタグ ['azure-ad-b2c'](https://stackoverflow.com/questions/tagged/azure-ad-b2c) を使用して投稿してください。 機能に関するご要望については、[フィードバック フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c)で投票してください。

