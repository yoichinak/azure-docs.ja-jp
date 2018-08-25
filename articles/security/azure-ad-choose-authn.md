---
title: Azure AD ハイブリッド ID ソリューションの適切な認証方法を選択する | Microsoft Docs
description: このガイドは、CEO、CIO、CISO、チーフ ID アーキテクト、エンタープライズ アーキテクト、セキュリティと IT に関する意思決定者など、中規模から大規模な組織で Azure AD ハイブリッド ID ソリューションの認証方法の選択を担当するユーザーの役に立ちます。
services: active-directory
keywords: ''
author: martincoetzer
ms.author: martincoetzer
ms.date: 04/12/2018
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.openlocfilehash: 7acf18c4624373dff8994a1996e9082770b90270
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39283689"
---
# <a name="choose-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>Azure Active Directory ハイブリッド ID ソリューションの適切な認証方法を選択する 

この記事は、組織が完全な Azure Active Directory (Azure AD) ハイブリッド ID ソリューションを実装するのに役立つ一連の記事の、最初のものです。 このソリューションは、[ハイブリッド ID デジタル変換フレームワーク](https://aka.ms/aadframework)として大枠が決められています。 堅牢かつ安全なハイブリッド ID ソリューションを実装するために組織が注力できる、ビジネスに関する成果と目標が扱われます。 

フレームワークの最初のビジネス成果では、ユーザーがクラウド アプリにアクセスするときに組織が認証プロセスをセキュリティで保護するための要件が詳しく説明されています。 認証でセキュリティ保護されたビジネス成果の最初のビジネス目標は、ユーザーがオンプレミスのユーザー名とパスワードを使用してクラウド アプリにサインインできるようにすることです。 このサインイン プロセスとユーザー認証方法により、クラウド内でのすべてのことが可能になります。

正しい認証方法の選択は、クラウドにアプリを移行しようとしている組織にとって最大の関心事です。 次の理由により、この決定を軽く考えてはなりません。

1. クラウドに移行する必要がある組織が最初に決定することです。 

2. 認証方法は、クラウドにおける組織のプレゼンスの重要なコンポーネントです。 これによって、クラウドのすべてのデータとリソースへのアクセスが制御されます。

3. これは、Azure AD での他の高度なセキュリティやユーザー エクスペリエンス機能すべての基盤となります。

4. 認証方法を実装した後に変更するのは困難です。

ID は IT セキュリティの新しいコントロール プレーンです。 したがって、認証は、新しいクラウド環境に対する組織のアクセスの保護です。 組織には、セキュリティを強化し、クラウド アプリを侵入者から保護する ID コントロール プレーンが必要です。

### <a name="out-of-scope"></a>対象範囲外
オンプレミスに既存のディレクトリ フットプリントを持たない組織は、この記事の対象範囲外です。 通常、このような組織は企業はクラウド内にのみ ID を作成し、ハイブリッド ID ソリューションを必要としません。 クラウド専用の ID はクラウドにのみ存在し、対応するオンプレミスの ID とは関連付けられません。

## <a name="authentication-methods"></a>認証方法
新しいコントロール プレーンとして Azure AD ハイブリッド ID ソリューションを採用する場合、認証がクラウド アクセスの基盤です。 正しい認証方法の選択は、Azure AD ハイブリッド ID ソリューションのセットアップにおける最初の重要な決定です。 Azure AD Connect (クラウドでのユーザーのプロビジョニングも行います) を使用することで構成される認証方法を実装します。

認証方法を選ぶには、時間、既存のインフラストラクチャ、複雑さ、および選んだ方法の実装にかかるコストを考慮する必要があります。 これらの要因は組織ごとに異なり、時間の経過とともに変化する場合があります。 

>[!VIDEO https://www.youtube.com/embed/YtW2cmVqSEw]

Azure AD は、ハイブリッド ID ソリューションに対して次の認証方法をサポートします。

### <a name="cloud-authentication"></a>クラウド認証
この認証方法を選ぶと、Azure AD がユーザーのサインイン プロセスを処理します。 シームレスなシングル サインオン (SSO) と組み合わせることで、ユーザーは資格情報を再入力しなくてもクラウド アプリにサインインできます。 クラウド認証では、2 つのオプションから選ぶことができます。 

**Azure AD のパスワード ハッシュ同期**。 Azure AD でオンプレミスのディレクトリ オブジェクトの認証を有効にする最も簡単な方法です。 ユーザーはオンプレミスで使用しているものと同じユーザー名とパスワードを使用でき、追加のインフラストラクチャを展開する必要はありません。 Identity Protection など、Azure AD の一部のプレミアム機能には、認証方法の選択に関係なく、パスワード ハッシュ同期が必要です。

> [!NOTE] 
> パスワードがクリア テキストで保存されたり、Azure AD の復元可能なアルゴリズムで暗号化されたりすることはありません。 パスワード ハッシュ同期の実際のプロセスについて詳しくは、「[Azure AD Connect 同期を使用したパスワード ハッシュ同期の実装](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization)」をご覧ください。 

**Azure AD パススルー認証**。 1 つ以上のオンプレミス サーバーで実行されているソフトウェア エージェントを使用して、Azure AD 認証サービスに簡単なパスワード検証を提供します。 オンプレミスの Active Directory を使用してサーバーで直接ユーザーが検証され、クラウドでパスワードの検証が行われることはありません。 

オンプレミスのユーザー アカウントの状態、パスワード ポリシー、およびサインイン時間をすぐに適用するセキュリティ要件のある企業は、この認証方法を使用します。 パススルー認証の実際のプロセスについて詳しくは、「[Azure Active Directory パススルー認証によるユーザー サインイン](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication)」をご覧ください。

### <a name="federated-authentication"></a>フェデレーション認証
この認証方法を選ぶと、Azure AD は別の信頼された認証システム (オンプレミスの Active Directory フェデレーション サービス (AD FS) など) に、ユーザーのパスワードを検証する認証プロセスを引き継ぎます。

認証システムでは、追加の高度な認証要件を指定できます。 たとえば、スマートカード ベースの認証やサードパーティの多要素認証です。 詳しくは、「[Azure での Active Directory フェデレーション サービスのデプロイ](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide)」をご覧ください。

次のセクションは、デシジョン ツリーを使用することで組織に適した認証方法を決定するのに役立ちます。 Azure AD ハイブリッド ID ソリューションにクラウド認証とフェデレーション認証のどちらを展開すればよいかを判断できます。

## <a name="decision-tree"></a>デシジョン ツリー

![Azure AD での認証のデシジョン ツリー](media/azure-ad/azure-ad-authn-image1.png)

## <a name="detailed-considerations"></a>詳細な考慮事項

### <a name="cloud-authentication-password-hash-synchronization"></a>クラウド認証: パスワード ハッシュ同期

* **作業量**。 パスワード ハッシュ同期は、展開、メンテナンス、インフラストラクチャに関して最小の作業量を必要とします。  ユーザーに必要なことが、Office 365、SaaS アプリ、およびその他の Azure AD ベースのリソースへのサインインのみである組織に対しては、典型的にこのレベルの作業量が適用されます。 パスワード ハッシュ同期をオンにすると、Azure AD Connect 同期プロセスの一部となって 2 分ごとに実行されます。

* **ユーザー エクスペリエンス**。 ユーザーのサインイン エクスペリエンスを向上させるには、パスワード ハッシュ同期と共にシームレス SSO を展開します。 シームレス SSO によって、ユーザーのサインイン時に不要なプロンプトが表示されないようになります。

* **高度なシナリオ**。 組織は、Azure AD Identity Protection のレポートを使用して、ID からの分析情報を使用することを選択できます。 たとえば、漏洩した資格情報のレポートを使用できます。 Windows Hello for Business は、[パスワード ハッシュ同期を使用するときに特定の要件](https://docs.microsoft.com/windows/access-protection/hello-for-business/hello-identity-verification)があるもう 1 つのオプションです。 

    パスワード ハッシュ同期を使用する多要素認証が必要な組織は、Azure AD の多要素認証を使用する必要があります。 これらの組織は、サード パーティ製またはオンプレミスの多要素認証方法を使用できません。

* **ビジネス継続性**。 クラウド認証と共にパスワード ハッシュ同期を使用することは、すべての Microsoft データセンターに対応するようにスケーリングするクラウド サービスとして、高い可用性を実現します。 パスワード ハッシュ同期が長期間ダウンしないようにするには、2 つめ Azure AD Connect サーバーを、スタンバイ構成のステージング モードで展開します。

* **考慮事項**。 現在、パスワード ハッシュ同期では、オンプレミスのアカウントの状態の変化はすぐには適用されません。 このような状況では、ユーザーは、Azure AD にユーザー アカウントの状態が同期されるまで、クラウド アプリにアクセスできます。 組織がこのような制限を回避する方法の 1 つは、管理者がオンプレミスのユーザー アカウントの状態を一括更新した後に、新しい同期サイクルを実行することです。 たとえば、アカウントを無効にします。

> [!NOTE]
> 現在、パスワードの期限切れとアカウントのロックアウトの状態は、Azure AD と Azure AD Connect では同期されません。 

展開手順については、[パスワード ハッシュ同期の実装](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization)に関する記事をご覧ください。

### <a name="cloud-authentication-pass-through-authentication"></a>クラウド認証: パススルー認証  

* **作業量**。 パススルー認証の場合、既存のサーバーに、1 つまたは複数 (推奨は 3 つ) の軽量のエージェントをインストールする必要があります。 これらのエージェントは、オンプレミスの AD ドメイン コントローラーなど、オンプレミスの Active Directory Domain Services にアクセスできる必要があります。 これらは、インターネットへの発信アクセスと、ドメイン コントローラーへのアクセスが必要です。 このため、境界ネットワーク内にエージェントを展開することはできません。 

    パススルー認証には、ドメイン コントローラーへの制約なしのネットワーク アクセスが必要です。 すべてのネットワーク トラフィックは暗号化され、認証要求に制限されます。 このプロセスの詳細については、パススルー認証での[セキュリティの詳細](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-security-deep-dive)に関する記事をご覧ください。

* **ユーザー エクスペリエンス**。 ユーザーのサインイン エクスペリエンスを向上させるには、パススルー認証と共にシームレス SSO を展開します。 シームレス SSO によって、ユーザーのサインイン後に不要なプロンプトが表示されないようになります。

* **高度なシナリオ**。 パススルー認証では、サインインの時点でオンプレミスのアカウント ポリシーが適用されます。 たとえば、オンプレミスのユーザーのアカウントの状態が、無効、ロックアウト、[パスワード期限切れ](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq#what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication)、またはユーザーに許可されているサインイン時間の超過の場合、アクセスは拒否されます。 

    パススルー認証を使用する多要素認証が必要な組織は、Azure Multi-Factor Authentication (MFA) を使用する必要があります。 これらの組織は、サード パーティ製またはオンプレミスの多要素認証方法を使用できません。 高度な機能では、パススルー認証を選択するかどうかにかかわらず、パスワード ハッシュ同期が必要になります。 たとえば、Identity Protection の漏洩した資格情報のレポートです。

* **ビジネス継続性**。 2 つの追加パススルー認証エージェントを展開することをお勧めします。 Azure AD Connect サーバー上の最初のエージェントに加えて、これらを追加します。 この追加の展開によって、認証要求の高可用性が保証されます。 3 つのエージェントを展開すると、メンテナンスのために 1 つのエージェントを停止しても、まだ 1 つのエージェントの障害に対応できます。 

    パススルー認証だけでなくパスワード ハッシュ同期も展開することの利点は、もう 1 つあります。 それは、プライマリ認証方法を利用できなくなった場合に、バックアップの認証方法として機能することです。

* **考慮事項**。 エージェントがオンプレミスで発生した重大な障害によってユーザーの資格情報を検証できない場合は、パススルー認証のバックアップの認証方法としてパスワード ハッシュ同期を使用できます。 パスワードハッシュ同期へのフェールオーバーは自動的には行われないため、Azure AD Connect を使用してサインイン方法を手動で切り替える必要があります。 

    代替 ID のサポートなど、パススルー認証におけるその他の考慮事項については、[よく寄せられる質問](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq)をご覧ください。

展開手順については、[パススルー認証の実装](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication)に関する記事をご覧ください。

### <a name="federated-authentication"></a>フェデレーション認証

* **作業量**。 フェデレーション認証システムでは、信頼できる外部システムを利用してユーザーを認証します。 フェデレーション システムへの既存の投資を Azure AD ハイブリッド ID ソリューションで再利用したい会社もあります。 フェデレーション システムの管理とメンテナンスは、Azure AD のコントロールの範囲外です。 フェデレーション システムが安全に展開されていて、認証の負荷を処理できるかどうかを確認するのは、フェデレーション システムを使用している組織の責任です。 

* **ユーザー エクスペリエンス**。 フェデレーション認証のユーザー エクスペリエンスは、機能、トポロジ、およびフェデレーション ファーム構成の実装に依存します。 組織によっては、セキュリティ要件に合わせてフェデレーション ファームへのアクセスを調整したり構成したりするために、この柔軟性が必要になります。 たとえば、内部的に接続されているユーザーとデバイスを構成して、資格情報の入力を要求することなく、自動的にユーザーをサインインさせることができます。 この構成が機能するのは、ユーザーが既にデバイスにサインインしているためです。 必要な場合は、高度なセキュリティ機能を利用してユーザーのサインイン プロセスをさらに困難にすることもできます。

* **高度なシナリオ**。 通常、フェデレーション認証ソリューションが必要になるのは、Azure AD によってネイティブにサポートされていない認証要件がある場合です。 [適切なサインイン オプションを選択する](https://blogs.msdn.microsoft.com/samueld/2017/06/13/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365/)のに役立つ詳しい情報をご覧ください。 次の一般的な要件で考えてみましょう。

    * スマートカードまたは証明書を必要とする認証。
    * オンプレミスの MFA サーバーまたはサード パーティの多要素プロバイダー。
    * サード パーティの認証ソリューションを使用する認証。 「[Azure AD のフェデレーション互換性リスト](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility)」をご覧ください。
    * ユーザー プリンシパル名 (UPN) (例: user@domain.com) ではなく、sAMAccountName (例: DOMAIN\username) を必要とするサインイン。

* **ビジネス継続性**。 フェデレーション システムでは、通常、負荷分散されたサーバーのアレイ (ファームとも呼ばれます) が必要になります。 このファームは、認証要求の高可用性を保証するために、内部ネットワークおよび境界ネットワークのトポロジに構成されます。

    プライマリ認証方法を使用できないときのために、フェデレーション認証と共に、パスワード ハッシュ同期をバックアップ認証方法として展開します。 たとえば、オンプレミスのサーバーが利用できない場合です。 大規模なエンタープライズ組織では、認証要求の待機時間を短くするため、geo-DNS で構成された複数のインターネット イングレス ポイントをフェデレーション ソリューションでサポートすることが必要になる場合があります。

* **考慮事項**。 フェデレーション システムでは通常、オンプレミスのインフラストラクチャへのより大きな投資が必要です。 オンプレミスのフェデレーションに既に投資している組織のほとんどは、このオプションを選択します。 または、ビジネス上の理由から、単一の ID プロバイダーを使用することがどうしても必要な場合です。 運用とトラブルシューティングは、クラウド認証ソリューションよりフェデレーション認証の方が複雑です。

Azure AD では検証できないルーティング不可能なドメインの場合、ユーザー ID によるサインインを実装するには追加の構成が必要になります。 この要件は、代替ログイン ID のサポートと呼ばれます。 制限事項と要件については、「[Configuring Alternate Login ID](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)」(代替ログイン ID の構成) をご覧ください。 フェデレーションを使用したサード パーティの多要素認証プロバイダーを使用する場合は、デバイスが Azure AD に接続できるよう、プロバイダーが WS-Trust をサポートしていることを確認してください。

展開手順については、「[Deploying Federation Servers](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/deploying-federation-servers)」(フェデレーション サーバーの展開) をご覧ください。

> [!NOTE] 
> Azure AD ハイブリッド ID ソリューションを展開するときは、Azure AD Connect のサポートされているトポロジの 1 つを実装する必要があります。 サポートされている構成とサポートされていない構成について詳しくは、「[Azure AD Connect のトポロジ](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-topologies)」をご覧ください。

## <a name="architecture-diagrams"></a>アーキテクチャの図

以下の図では、Azure AD ハイブリッド ID ソリューションで使用できる各認証方法に必要な、アーキテクチャ コンポーネントの概要を示します。 これにより、ソリューション間の相違点を比較することができます。

* パスワード ハッシュ同期ソリューションのシンプルさ:

    ![パスワード ハッシュ同期を使用する Azure AD ハイブリッド ID](media/azure-ad/azure-ad-authn-image2.png)

* パススルー認証のエージェントの要件:

    ![パススルー認証を使用する Azure AD ハイブリッド ID](media/azure-ad/azure-ad-authn-image3.png)

* 組織の境界ネットワークと内部ネットワークでのフェデレーションに必要なコンポーネントの概要:

    ![フェデレーション認証を使用する Azure AD ハイブリッド ID](media/azure-ad/azure-ad-authn-image4.png)

## <a name="comparing-methods"></a>方法の比較

|考慮事項|パスワード ハッシュ同期 + シームレス SSO|パススルー認証 + シームレス SSO|AD FS とのフェデレーション|
|:-----|:-----|:-----|:-----|
|認証が行われる場所|クラウド内|クラウド内で、オンプレミスの認証エージェントとのセキュリティで保護されたパスワード検証の交換後|オンプレミスの|
|プロビジョニング システム (Azure AD Connect) 以外のオンプレミスのサーバーの要件|なし|追加の認証エージェントごとに 1 つのサーバー|2 つ以上の AD FS サーバー<br><br>境界/DMZ ネットワークに 2 つ以上の WAP サーバー|
|プロビジョニング システム以外のオンプレミスのインターネットおよびネットワークの要件|なし|認証エージェントを実行しているサーバーからの[発信インターネット アクセス](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start)|境界の WAP サーバーへの[着信インターネット アクセス](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-requirements)<br><br>境界の WAP サーバーから AD FS サーバーへの着信ネットワーク アクセス<br><br>ネットワークの負荷分散|
|SSL 証明書の要件|いいえ |いいえ |[はい]|
|正常性の監視ソリューション|必要なし|エージェントの状態は [Azure Active Directory 管理センター](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication)によって提供される|[Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-adfs)|
|会社のネットワーク内のドメインに参加しているデバイスからクラウドのリソースへのユーザーのシングル サインオン|[シームレス SSO](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso) を使用して実行|[シームレス SSO](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso) を使用して実行|はい|
|サポートされているサインインの種類|UserPrincipalName + パスワード<br><br>[シームレス SSO](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso) を使用した Windows 統合認証<br><br>[代替ログイン ID](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-custom)|UserPrincipalName + パスワード<br><br>[シームレス SSO](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso) を使用した Windows 統合認証<br><br>[代替ログイン ID](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq)|UserPrincipalName + パスワード<br><br>sAMAccountName + パスワード<br><br>Windows 統合認証<br><br>[証明書とスマート カード認証](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br><br>[代替ログイン ID](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|Windows Hello for Business のサポート|[キー信頼モデル](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Intune での証明書信頼モデル](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[キー信頼モデル](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Intune での証明書信頼モデル](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[キー信頼モデル](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[証明書信頼モデル](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|多要素認証のオプション|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Azure MFA サーバー](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy)<br><br>[サード パーティの MFA](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)|
|サポートされるユーザー アカウントの状態|無効なアカウント<br>(最大 30 分の遅延)|無効なアカウント<br><br>アカウントのロックアウト<br><br>パスワード期限切れ<br><br>サインイン時間|無効なアカウント<br><br>アカウントのロックアウト<br><br>パスワード期限切れ<br><br>サインイン時間|
|条件付きアクセスのオプション|[Azure AD 条件付きアクセス](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)|[Azure AD 条件付きアクセス](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)|[Azure AD 条件付きアクセス](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)<br><br>[AD FS の要求規則](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|サポートされる従来のプロトコルのブロック|[はい](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-conditions#legacy-authentication)|[はい](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-conditions#legacy-authentication)|[はい](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|サインイン ページのロゴ、イメージ、説明のカスタマイズ可能性|[Azure AD Premium を使用して可能](https://docs.microsoft.com/azure/active-directory/customize-branding)|[Azure AD Premium を使用して可能](https://docs.microsoft.com/azure/active-directory/customize-branding)|[はい](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-management#customlogo)|
|サポートされる高度なシナリオ|[Smart Password Lockout](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords)<br><br>[漏洩した資格情報レポート](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events)|[Smart Password Lockout](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout)|複数サイトの低待機時間の認証システム<br><br>[AD FS エクストラネットのロックアウト](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-lockout-protection)<br><br>[サード パーティの ID システムとの統合](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility)|

## <a name="recommendations"></a>推奨事項
ID システムによって、クラウドに移行して利用できるようにしたクラウド アプリと基幹業務アプリに対するユーザーのアクセスが保証されます。 承認されたユーザーの生産性を向上させ、不正なユーザーを組織の機密データから締め出すために、認証によってアプリへのアクセスが制御されます。

どの認証方法を選択する場合でも、次の理由により、パスワード ハッシュ同期を使用するか有効にします。

1. **高可用性とディザスター リカバリー**。 パススルー認証とフェデレーションは、オンプレミスのインフラストラクチャに依存します。 パススルー認証の場合、オンプレミスのフットプリントには、パススルー認証エージェントに必要なサーバー ハードウェアとネットワークが含まれます。 フェデレーションの場合、オンプレミスのフットプリントはさらに大きくなります。 認証要求と内部フェデレーション サーバーをプロキシするために、境界ネットワーク内にサーバーが必要になるためです。 

    単一障害点を回避するには、冗長サーバーを展開します。 これにより、いずれかのコンポーネントで障害が発生しても、認証要求サービスが常に提供されるようにします。 また、パススルー認証とフェデレーションは、認証要求に応答するために、両方ともドメイン コントローラーにも依存しますが、ここでも障害が発生する可能性があります。 これらのコンポーネントの多くは、正常性を保つためにメンテナンスが必要です。 メンテナンスの計画や実装が適切でない場合は、システムが停止する可能性が高くなります。 Microsoft の Azure AD クラウド認証サービスはグローバルにスケーリングして常に利用できるため、パスワード ハッシュ同期を使うことで停止を回避してください。

2. **オンプレミスの停止への対応**。  サイバー攻撃や災害によるオンプレミスの停止の影響は、ブランドの評判が損なわれることから、組織が麻痺して攻撃に対処できなくなることまで、大きな範囲に及ぶ可能性があります。 近年でも、オンプレミスのサーバーがダウンする原因となった特定対象へのランサムウェアなど、多くの組織がマルウェア攻撃の被害を受けました。 Microsoft は、この種の攻撃への対処を支援するなかで、組織には 2 つのカテゴリがあることに気付きました。

   * 事前にパスワード ハッシュ同期をオンにしていた組織は、パスワード ハッシュ同期を使用するように認証方法を変更しました。 このような組織は、数時間でオンラインに復帰しました。 Office 365 を介して電子メールにアクセスすることで、問題解決と他のクラウド ベースのワークロードへのアクセスのために作業することができました。

   * 事前にパスワード ハッシュ同期を有効にしていなかった組織は、通信と問題解決のために、信頼されていない外部のコンシューマー向け電子メール システムを利用するしかありませんでした。 このようなケースでは、稼働状態に戻すのに数週間以上かかりました。

3. **ID 保護**。 クラウド内のユーザーを保護する最善の方法の 1 つは、Azure AD Identity Protection です。 Microsoft は常にインターネットを精査して、闇サイトで販売され利用されているユーザーやパスワードのリストを入手しています。 Azure AD はこの情報を使って、組織のユーザー名やパスワードのいずれかが侵害されているかどうかを確認します。 したがって、使用している認証方法がフェデレーション認証かパススルー認証かに関係なく、パスワード ハッシュ同期を有効にすることが重要になります。 漏洩した資格情報は、レポートとして示されます。 ユーザーが漏洩したパスワードでのサインインを試みた場合、この情報を使用してユーザーをブロックするか、パスワードの変更を強制します。

最後に、[Gartner](https://info.microsoft.com/landingIAMGartnerreportregistration.html) によると、Microsoft は最も完備した ID およびアクセス管理機能セットを持っています。 Microsoft は、毎月 [4500 億の認証要求](https://www.microsoft.com/en-us/security/intelligence-report)を処理して、ほぼすべてのデバイスから Office 365 のような何千もの SaaS アプリケーションにアクセスできるようにしています。 

## <a name="conclusion"></a>まとめ

この記事では、組織がクラウド アプリへのアクセスをサポートするために構成して展開できるさまざまな認証オプションの概要を説明しました。 ビジネス、セキュリティ、テクノロジに関するさまざまな要件を満たすため、組織は、パスワード ハッシュ同期、パススルー認証、およびフェデレーション認証のいずれかを選択できます。 

各認証方法を検討してください。 ソリューションを展開するための作業量や、サインイン プロセスでのユーザーのエクスペリエンスは、ビジネス要件に合っているでしょうか。 各認証方法において、組織が高度なシナリオとビジネス継続性機能を必要とするかどうかを評価してください。 最後に、各認証方法の考慮事項を評価してください。 選択した方法を実装する妨げになる項目はありませんか。

## <a name="next-steps"></a>次の手順

今日の脅威は、常に、あらゆる場所に存在します。 適切な認証方法を実装することで、セキュリティ リスクが軽減され、ID が保護されます。

[Azure AD を利用して](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad)、組織に適した認証ソリューションを展開してください。

フェデレーション認証からクラウド認証への移行を検討している場合は、[サインイン方法の変更](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-user-signin#changing-the-user-sign-in-method)についてさらに学習してください。 移行を計画して実施するときは、[これらのプロジェクト展開計画](http://aka.ms/deploymentplans)が役に立ちます。
