---
title: Azure AD Connect 同期でパスワード ハッシュ同期を実装する | Microsoft Docs
description: パスワード ハッシュ同期のしくみとその設定方法について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: f6ba0fccc6fadffb5f4d1a22b2c2fbcc38f9f0df
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494983"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Azure AD Connect 同期を使用したパスワード ハッシュ同期の実装
この記事では、オンプレミスの Active Directory インスタンスから、クラウドベースの Azure Active Directory (Azure AD) インスタンスへの、ユーザー パスワードの同期に必要な情報を提供します。

## <a name="what-is-password-hash-synchronization"></a>パスワード ハッシュ同期とは
パスワードを忘れたために作業ができなくなる可能性は、覚えておかなければならないパスワードの数と関係があります。 覚えておかなければならないパスワードの数が多いほど、パスワードを忘れる確率は高まります。 パスワードのリセットおよびその他のパスワード関連の問題についての質問や電話への対応に、最も多くのヘルプ デスク リソースが費やされています。

パスワード ハッシュ同期は、オンプレミスの Active Directory インスタンスからクラウドベースの Azure AD インスタンスにユーザー パスワード ハッシュのハッシュを同期するときに使用する機能です。
この機能を使用して、Office 365、Microsoft Intune、CRM Online、Azure Active Directory Domain Services (Azure AD DS) などの Azure AD サービスにサインインします。 このサービスにサインインするときに使用するパスワードは、オンプレミスの Active Directory インスタンスにサインインするときに使うものと同じです。

![What is Azure AD Connect](./media/active-directory-aadconnectsync-implement-password-hash-synchronization/arch1.png)

パスワード同期によって、覚えておく必要があるパスワードの数を減らして 1 つだけにすると、 次のような利点があります。

* ユーザーの生産性が向上する。
* ヘルプデスクのコストが削減される。  

また、[Active Directory フェデレーション サービス (AD FS) でフェデレーション](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect)を使用する場合、AD FS インフラストラクチャで障害が発生した際のバックアップとして、オプションでパスワード ハッシュ同期を設定することもできます。

パスワード ハッシュ同期は、Azure AD Connect Sync によって実装されるディレクトリ同期の拡張機能です。環境でパスワード ハッシュ同期を使用するには、以下のことを行う必要があります。

* Azure AD Connect をインストールする。  
* オンプレミスの Active Directory インスタンスと Azure Active Directory インスタンスとの間のディレクトリ同期を構成する。
* パスワード ハッシュ同期を有効にする。

詳細については、「 [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」を参照してください。

> [!NOTE]
> FIPS とパスワード ハッシュ同期に対して構成されている Azure Active Directory Domain Services の詳細については、この記事の「パスワード ハッシュ同期と FIPS」を参照してください。
>
>

## <a name="how-password-hash-synchronization-works"></a>パスワード ハッシュ同期のしくみ
Active Directory ドメイン サービスは、実際のユーザー パスワードをハッシュ値表現の形式で格納します。 ハッシュ値は、一方向の数学関数 ("*ハッシュ アルゴリズム*") の結果として求められます。 一方向の関数の結果をパスワードのプレーンテキスト バージョンに戻す方法はありません。 パスワードのハッシュを使用してオンプレミスのネットワークにサインインすることはできません。

パスワードを同期するには、Azure AD Connect 同期でオンプレミスの Active Directory インスタンスからパスワード ハッシュを抽出します。 Azure Active Directory 認証サービスに同期される前に、パスワード ハッシュには、追加のセキュリティ処理が行われます。 パスワードは、ユーザーごとに、時間順に同期されます。

パスワード ハッシュ同期処理の実際のデータ フローは、表示名や電子メール アドレスなどのユーザー データの同期と似ています。 ただし、パスワードは、他の属性に対する標準のディレクトリ同期ウィンドウよりも頻繁に同期されます。 パスワード ハッシュ同期プロセスは 2 分間隔で実行されます。 このプロセスの頻度を変更することはできません。 パスワードを同期すると、既存のクラウド パスワードが上書きされます。

パスワード ハッシュ同期機能を初めて有効にすると、スコープ内のすべてのユーザーの初回同期が実行されます。 同期するユーザー パスワードのサブセットを明示的に定義することはできません。

オンプレミス パスワードを変更すると、更新されたパスワードは、多くの場合、ほんの数分で同期されます。
同期の試行に失敗すると、パスワード ハッシュ同期機能により自動的に再試行が行われます。 パスワード同期の試行中にエラーが発生した場合、イベント ビューアーにエラーが記録されます。

パスワードの同期によって、現在サインイン中の Azure ユーザーが影響を受けることはありません。
クラウド サービスにログインしている間、パスワード変更が同期された場合、現在のクラウド サービス セッションがその影響をすぐに受けることはありません。 しかしながら、クラウド サービスで認証が再び要求された場合は、新しいパスワードを指定する必要があります。

ユーザーは、企業ネットワークにサインインしているかどうかに関係なく、Azure AD の認証のために、企業の資格情報をもう一度入力する必要があります。 ただし、ユーザーがサインイン時に [サインインしたままにする] \(KMSI) チェックボックスをオンにすると、このようなパターンを最小限に抑えることができます。 このチェックボックスをオンにすると、180 日間認証をバイパスするセッション Cookie が設定されます。 KMSI の動作は、Azure AD 管理者が有効または無効にすることができます。 さらに、ユーザーが企業ネットワークに接続される会社のデバイスを使用するときに、自動的にサインインを行う[シームレス SSO](active-directory-aadconnect-sso.md) をオンにすることで、パスワード プロンプトを削減できます。

> [!NOTE]
> パスワード同期は、Active Directory でオブジェクトの種類がユーザーであるオブジェクトのみがサポートされます。 オブジェクトの種類が INetOrgPerson であるオブジェクトはサポートされません。

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>パスワード ハッシュ同期のしくみの詳しい説明
ここでは、Active Directory と Azure AD の間のパスワード ハッシュ同期のしくみを詳しく説明します。

![パスワードの詳細なフロー](./media/active-directory-aadconnectsync-implement-password-hash-synchronization/arch3.png)


1. AD Connect サーバー上のパスワード ハッシュ同期エージェントは、DC 間でデータを同期するために使用される標準の [MS-DRSR](https://msdn.microsoft.com/library/cc228086.aspx) レプリケーション プロトコルを介して、保存されたパスワード ハッシュ (unicodePwd 属性) を 2 分ごとに DC に要求します。 サービス アカウントには、パスワード ハッシュを取得するために、Replicate Directory Changes (ディレクトリの変更のレプリケート) と Replicate Directory Changes All AD (ディレクトリの変更をすべての AD にレプリケート) の権限が必要になります (インストール時に既定で付与されます)。
2. DC は、送信する前に、RPC セッション キーの [MD5](http://www.rfc-editor.org/rfc/rfc1321.txt) ハッシュであるキーと salt を使用して MD4 パスワード ハッシュを暗号化します。 次に、この結果を RPC 経由でパスワード ハッシュ同期エージェントに送信します。 DC は、DC のレプリケーション プロトコルを使用して同期エージェントにも salt を渡すので、エージェントはエンベロープの暗号化を解除できます。
3.  パスワード ハッシュ同期エージェントは、暗号化されたエンベロープを受け取ると、[MD5CryptoServiceProvider](https://msdn.microsoft.com/library/System.Security.Cryptography.MD5CryptoServiceProvider.aspx) と salt を使用して、受信したデータの暗号化を解除し元の MD4 形式に戻すためのキーを生成します。 パスワード ハッシュ同期エージェントがクリア テキストのパスワードにアクセスすることはありません。 パスワード ハッシュ同期エージェントによる MD5 の使用は、DC とレプリケーション プロトコルとの互換性の維持に限定されており、DC とパスワード ハッシュ同期エージェントの間でオンプレミスでのみ使用されます。
4.  パスワード ハッシュ同期エージェントは、最初にハッシュを 32 バイトの 16 進数文字列に変換し、次にこの文字列を UTF-16 エンコーディングでバイナリに変換することで、16 バイトのバイナリ パスワード ハッシュを 64 バイトに拡張します。
5.  パスワード ハッシュ同期エージェントは、10 バイト長の salt で構成されるユーザーごとの salt を、64 バイトの バイナリに追加し、 元のハッシュの保護を強化します。
6.  それから、パスワード ハッシュ同期エージェントは、MD4 ハッシュとユーザーごとのsalt を結合し、それを [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt) 関数に入力します。 [HMAC-SHA256](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) キー付きハッシュ アルゴリズムの 1000 のイテレーションが使用されます。 
7.  パスワードハッシュ同期エージェントは、返された 32 バイトのハッシュを受け取り、(Azure AD で使用するために) ユーザーごとの salt と SHA256 のイテレーションの数の両方を連結し、SSL 経由で Azure AD Connect から Azure AD にこの文字列を送信します。</br> 
8.  ユーザーが Azure AD にサインインしようとして自分のパスワードを入力すると、パスワードは同じ MD4 + salt + PBKDF2 + HMAC - SHA256 のプロセスを通じて処理されます。 返されたハッシュが Azure AD に格納されたハッシュに一致する場合、ユーザーは正しいパスワードを入力しており、認証も済んでいます。 

>[!Note] 
>元の MD4 ハッシュは Azure AD に送信されません。 代わりに、元の MD4 ハッシュの SHA256 ハッシュが送信されます。 その結果、Azure AD に格納されているハッシュを取得しても、このハッシュをオンプレミスの Pass-the-Hash 攻撃で使用することはできません。

### <a name="how-password-hash-synchronization-works-with-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services とのパスワード ハッシュ同期のしくみ
パスワード ハッシュ同期機能を使用して、オンプレミス パスワードを [Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md) に同期することもできます。 このシナリオでは、Azure Active Directory Domain Services インスタンスは、オンプレミスの Active Directory インスタンスで使用できるすべての方法を使用して、クラウドでユーザーを認証します。 このシナリオのエクスペリエンスは、オンプレミス環境で Active Directory 移行ツール (ADMT) を使用する場合に似ています。

### <a name="security-considerations"></a>セキュリティに関する考慮事項
パスワードを同期するとき、ユーザーのプレーンテキスト形式のパスワードは、パスワード ハッシュ同期機能にも、Azure AD や関連するどのサービスにも公開されません。

ユーザー認証は、組織の独自の Active Directory インスタンスではなく、Azure AD に対して行われます。 どのような形式であれ、パスワード データをオンプレミス環境に残すことについて組織内に懸念がある場合、Azure AD に格納された SHA256 のパスワード データ (元の MD4 ハッシュのハッシュ) は Active Directory に格納されたデータよりも大幅に安全であるという事実を検討してください。 さらに、SHA256 ハッシュの暗号化は解除できないため、組織の Active Directory 環境に戻して、Pass-the-Hash 攻撃で有効なユーザーのパスワードとして表示することはできません。

### <a name="password-policy-considerations"></a>パスワード ポリシーの考慮事項
パスワード ハッシュ同期を有効にすることによって影響を受ける 2 種類のパスワード ポリシーがあります。

* パスワードの複雑性のポリシー
* パスワードの有効期限のポリシー

#### <a name="password-complexity-policy"></a>パスワードの複雑性のポリシー  
パスワード ハッシュ同期が有効になっている場合、オンプレミスの Active Directory インスタンスでのパスワードの複雑性ポリシーによって、同期済みユーザーに対するクラウドでの複雑性ポリシーが上書きされます。 オンプレミスの Active Directory インスタンスからの有効なパスワードすべてを、Azure AD サービスへのアクセスに使用することができます。

> [!NOTE]
> クラウド内で直接作成されたユーザーのパスワードには、引き続きクラウドで定義されているパスワード ポリシーが適用されます。

#### <a name="password-expiration-policy"></a>パスワードの有効期限のポリシー  
ユーザーがパスワード ハッシュ同期のスコープ内にいる場合、クラウド アカウントのパスワードは "*期限なし*" に設定されます。

オンプレミス環境で期限切れになった同期パスワードを利用し、引き続きクラウド サービスにサインインできます。 クラウドのパスワードは、次にオンプレミス環境でパスワードを変更したときに更新されます。

#### <a name="account-expiration"></a>アカウントの有効期限
組織でユーザー アカウント管理の一環として accountExpires 属性を使用する場合、この属性は Azure AD に同期されないことに注意してください。 この結果、パスワード ハッシュ同期用に構成された環境の期限切れの Active Directory アカウントが、Azure AD では引き続きアクティブなままとなります。 アカウントが期限切れの場合はユーザーの Azure AD アカウントを無効にする PowerShell スクリプトをワークフロー アクションでトリガーすることをお勧めします ([Set-AzureADUser](https://docs.microsoft.com/powershell/module/azuread/set-azureaduser?view=azureadps-2.0) コマンドレットを使用)。 逆に、アカウントを有効にしたときは、Azure AD インスタンスを有効にする必要があります。

### <a name="overwrite-synchronized-passwords"></a>同期されたパスワードの上書き
管理者は Windows PowerShell を使用してパスワードを手動でリセットできます。

この場合、新しいパスワードによって同期されたパスワードは上書きされ、クラウドで定義されているすべてのパスワード ポリシーが新しいパスワードに適用されます。

オンプレミスのパスワードを再び変更した場合、新しいパスワードはクラウドに同期され、手動で更新したパスワードを上書きします。

パスワードの同期によって、サインイン中の Azure ユーザーが影響を受けることはありません。 クラウド サービスにログインしている間、パスワード変更が同期された場合、現在のクラウド サービス セッションがその影響をすぐに受けることはありません。 KMSI を使用すると、この差異のある期間が長くなります。 クラウド サービスで認証が再び要求された場合は、新しいパスワードを指定する必要があります。

### <a name="additional-advantages"></a>その他の利点

- 通常、パスワード ハッシュ同期は、フェデレーション サービスよりも実装が簡単です。 パスワード同期は追加のサーバーを必要としないため、ユーザーを認証するために高可用性フェデレーション サービスに依存するという状況を解消できるからです。
- フェデレーションだけでなく、パスワード ハッシュ同期を有効にすることもできます。 これは、フェデレーション サービスで障害が発生した場合に、フォールバックとして使用できます。

## <a name="enable-password-hash-synchronization"></a>パスワード ハッシュ同期を有効にする

>[!IMPORTANT]
>AD FS (または他のフェデレーション テクノロジ) からパスワード ハッシュの同期に移行する場合は、[こちら](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Password%20Hash%20Synchronization.docx)に公開されている詳細なデプロイ ガイドに従うことを強くお勧めします。

**簡単設定**オプションを使用して Azure AD Connect をインストールすると、パスワード ハッシュ同期が自動的に有効になります。 詳細については、「[簡単設定を使用した Azure AD Connect の開始](active-directory-aadconnect-get-started-express.md)」を参照してください。

Azure AD Connect のインストール時にカスタム設定を使用すると、ユーザー サインイン ページでパスワード ハッシュ同期が使用可能になります。 詳細については、「[Azure AD Connect のカスタム インストール](active-directory-aadconnect-get-started-custom.md)」を参照してください。

![パスワード ハッシュ同期を有効にする](./media/active-directory-aadconnect-get-started-custom/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>パスワード ハッシュ同期と FIPS
Federal Information Processing Standard (FIPS) に従ってサーバーがロックされた場合、MD5 は無効になります。

**パスワード ハッシュ同期で MD5 を有効にするには、次の手順を実行します。**

1. %programfiles%\Azure AD Sync\Bin に移動します。
2. miiserver.exe.config を開きます。
3. ファイルの末尾にある configuration/runtime ノードに移動します。
4. `<enforceFIPSPolicy enabled="false"/>`
5. 変更を保存します。

参考までに、このスニペットは次のようになります。

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

セキュリティと FIPS の詳細については、「[AAD Password Sync, encryption and FIPS compliance (AAD パスワード同期、暗号化、および FIPS コンプライアンス)](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/)」を参照してください。

## <a name="troubleshoot-password-hash-synchronization"></a>パスワード ハッシュ同期のトラブルシューティング
パスワード ハッシュ同期に問題がある場合は、[パスワード ハッシュ同期のトラブルシューティング](active-directory-aadconnectsync-troubleshoot-password-hash-synchronization.md)に関するページをご覧ください。

## <a name="next-steps"></a>次の手順
* [Azure AD Connect 同期: 同期オプションのカスタマイズ](active-directory-aadconnectsync-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)
* [ADFS からパスワード ハッシュ同期に移行するための詳細なデプロイ計画の取得](https://aka.ms/authenticationDeploymentPlan)
