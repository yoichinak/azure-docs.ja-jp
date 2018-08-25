---
title: Azure Active Directory Domain Services の概要 | Microsoft Docs
description: Azure Active Directory Domain Services の概要
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 0d47178f-773e-45f9-9ff4-9e8cffa4ffa2
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/26/2017
ms.author: maheshu
ms.openlocfilehash: 4467c193b5ff70a304b4ec5f632276ca14551b08
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39502000"
---
# <a name="azure-active-directory-ad-domain-services"></a>Azure Active Directory (AD) Domain Services
## <a name="overview"></a>概要
Azure インフラストラクチャ サービスを使用すると、さまざまなコンピューティング ソリューションを機敏にデプロイすることができます。 Azure Virtual Machines では、ほぼ瞬時にデプロイでき、分単位で支払えます。 Windows、Linux、SQL Server、Oracle、IBM、SAP、BizTalk のサポートを使用すると、ほぼすべてのオペレーティング システムですべてのワークロード、すべての言語をデプロイできます。 このようなメリットを生かして、オンプレミスでデプロイされているレガシ アプリケーションを Azure に移行し、運用経費を節減することができます。

オンプレミスのアプリケーションを Azure に移行することの主な特徴は、これらのアプリケーションの ID ニーズに対処することにあります。 ディレクトリ対応アプリケーションは、企業ディレクトリへの読み取りまたは書き込みアクセスのために LDAP に依存することもあれば、エンドユーザーを認証するために Windows 統合認証 (Kerberos または NTLM 認証) に依存することもあります。 Windows Server で実行される基幹業務 (LOB) アプリケーションは、通常、ドメインに参加しているコンピューターにデプロイされるので、グループ ポリシーを使用して安全に管理することができます。 'リフト アンド シフト' 方式でオンプレミスのアプリケーションをクラウドに移行するには、企業 ID インフラストラクチャにおけるこれらの依存関係を解決する必要があります。

Azure にデプロイされたアプリケーションの ID ニーズを満たすために、管理者は多くの場合次に示す解決策のいずれかに頼っています。

* Azure インフラストラクチャ サービスで実行されるワークロードとオンプレミスの企業ディレクトリとの間にサイト間 VPN 接続をデプロイします。
* Azure 仮想マシンを使用してレプリカ ドメイン コントローラーをセットアップすることにより、企業 AD ドメイン/フォレスト インフラストラクチャを拡張します。
* Azure 仮想マシンとしてデプロイされたドメイン コントローラーを使用して Azure にスタンドアロン ドメインをデプロイします。

これらの方法はいずれも、コストが高く、管理オーバーヘッドが大きいという問題があります。 管理者は、Azure 内で仮想マシンを使用してドメイン コントローラーをデプロイする必要があります。 管理者はこれらの仮想マシンについて管理、セキュリティ保護、修正プログラムの適用、監視、バックアップ、およびトラブルシューティングを行う必要があります。 オンプレミスのディレクトリへの VPN 接続に依存すると、Azure にデプロイされたワークロードはネットワークの一時的な障害または停止の影響を受けやすくなります。 ネットワークの停止により、アップタイムが減少し、アプリケーションの信頼性が低下します。

Azure AD ドメイン サービスは、より簡単に目的を達成できる代替え手段として設計されています。

### <a name="watch-an-introductory-video"></a>紹介ビデオを見る

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

## <a name="introducing-azure-ad-domain-services"></a>Azure AD ドメイン サービスの概要

Azure AD Domain Services では、Windows Server Active Directory と完全に互換性のあるマネージド ドメイン サービス (ドメイン参加、グループ ポリシー、LDAP、Kerberos/NTLM 認証) を提供します。 クラウドでドメイン コントローラーのデプロイ、管理、および修正プログラムの適用を行わなくても、これらのドメイン サービスを使用することができます。 Azure AD ドメイン サービスは既存の Azure AD テナントと統合されるので、ユーザーは企業の資格情報を使用してログインできるようになります。 さらに、既存のグループおよびユーザー アカウントを使用してリソースへのアクセスをセキュリティで保護することができます。このため、'リフト アンド シフト' 方式でオンプレミスのリソースを Azure インフラストラクチャ サービスに滑らかに移行することができます。

Azure AD ドメイン サービスの機能は、Azure AD テナントがクラウド専用であっても、オンプレミスの Active Directory と同期しているとしても、シームレスに動作します。

### <a name="azure-ad-domain-services-for-cloud-only-organizations"></a>クラウドのみの組織向けの Azure AD ドメイン サービス

クラウド専用 Azure AD テナント ('管理対象テナント' と呼ばれることもあります) には、オンプレミスの ID フットプリントはありません。 言い換えると、ユーザー アカウント、ユーザーのパスワード、およびグループ メンバーシップはすべてクラウドにネイティブです。つまり、Azure AD で作成および管理されます。 クラウド専用の Azure AD テナントである Contoso について少し検討してみましょう。 次の図に示すように、Contoso の管理者が Azure インフラストラクチャ サービスで仮想ネットワークを構成しました。 アプリケーションとサーバー ワークロードは、この仮想ネットワークで Azure 仮想マシンにデプロイされています。 Contoso は、クラウド専用のテナントであるため、ユーザー ID、ユーザーの資格情報、およびグループ メンバーシップはすべて Azure AD で作成および管理されます。

![Azure AD ドメイン サービスの概要](./media/active-directory-domain-services-overview/aadds-overview.png)

Contoso の IT 管理者は、Azure AD テナントに対して Azure AD ドメイン サービスを有効にし、この仮想ネットワークでドメイン サービスを利用できるようにすることができます。 その後、Azure AD ドメイン サービスによってマネージド ドメインがプロビジョニングされ、仮想ネットワーク内で使用できるようになります。 Contoso の Azure AD テナントで使用できるユーザー アカウント、グループ メンバーシップ、およびユーザー資格情報はすべて、この新規に作成されたドメインでも使用できるようになります。 この機能では、組織のユーザーは会社の資格情報を使用してドメインにサインインすることができます (たとえば、リモート デスクトップを介してドメインに参加しているコンピューターにリモートで接続する場合)。 管理者は、既存のグループ メンバーシップを使用して、ドメイン内のリソースへのアクセスをプロビジョニングできます。 仮想ネットワーク内の仮想マシンにデプロイされたアプリケーションは、ドメインへの参加、LDAP 読み取り、LDAP バインド、NTLM および Kerberos 認証、グループ ポリシーなどの機能を使用できます。

Azure AD ドメイン サービスでプロビジョニングされるマネージド ドメインの主要な特徴のいくつかを次に示します。

* Contoso の IT 管理者は、このドメインまたはこのマネージド ドメインのドメイン コントローラーに対して管理、修正プログラムの適用、または監視を行う必要はありません。
* このドメインの AD レプリケーションを管理する必要はありません。 Contoso の Azure AD テナントからのユーザー アカウント、グループ メンバーシップ、および資格情報は、このマネージド ドメイン内で自動的に利用できるようになります。
* ドメインは Azure AD ドメイン サービスによって管理されるので、Contoso の IT 管理者には、このドメインに対するドメイン管理者特権またはエンタープライズ管理者特権はありません。

### <a name="azure-ad-domain-services-for-hybrid-organizations"></a>ハイブリッド組織向けの Azure AD ドメイン サービス
ハイブリッド IT インフラストラクチャを備える組織は、クラウド リソースとオンプレミスのリソースを組み合わせで利用できます。 このような組織では、ID 情報をオンプレミスのディレクトリから Azure AD テナントへ同期します。 ハイブリッド組織はオンプレミスのアプリケーションのより多く (特にレガシ ディレクトリ対応アプリケーション) をクラウドに移行しようとしているので、そのようなアプリケーションにとって Azure AD ドメイン サービスは有用です。

Litware Corporation では、ID 情報をオンプレミスのディレクトリから Azure AD テナントに同期するために、 [Azure AD Connect](../active-directory/active-directory-aadconnect.md)をデプロイしました。 同期されている ID 情報には、ユーザー アカウント、認証のための資格情報のハッシュ (パスワード同期)、およびグループ メンバーシップが含まれます。

> [!NOTE]
> **パスワード同期は、ハイブリッド組織が Azure AD ドメイン サービスを使用する際に必須となります**。 これは、Azure AD ドメイン サービスで提供されるマネージド ドメインでは、NTLM または Kerberos 認証方法を使用してユーザーを認証する上で、それらのユーザーの資格情報が必要となることによる要件です。
>
>

![Litware Corporation 向けの Azure AD ドメイン サービス](./media/active-directory-domain-services-overview/aadds-overview-synced-tenant.png)

上図では、ハイブリッド IT インフラストラクチャを備えた組織 (Litware Corporation など) が Azure AD ドメイン サービスを使用する方法を示します。 ドメイン サービスを必要とする Litware のアプリケーションおよびサーバー ワークロードは、Azure インフラストラクチャ サービスの仮想ネットワークにデプロイされています。 Litware の IT 管理者は、Azure AD テナントに対して Azure AD ドメイン サービスを有効にし、この仮想ネットワークでマネージド ドメインを利用できるようにすることができます。 Litware はハイブリッド IT インフラストラクチャを備えた組織であるため、ユーザー アカウント、グループ、および資格情報はオンプレミスのディレクトリから Azure AD テナントへ同期されます。 この機能では、ユーザーは会社の資格情報を使用してドメインにサインインすることができます (たとえば、リモート デスクトップを介してドメインに参加しているコンピューターにリモートで接続する場合)。 管理者は、既存のグループ メンバーシップを使用して、ドメイン内のリソースへのアクセスをプロビジョニングできます。 仮想ネットワーク内の仮想マシンにデプロイされたアプリケーションは、ドメインへの参加、LDAP 読み取り、LDAP バインド、NTLM および Kerberos 認証、グループ ポリシーなどの機能を使用できます。

Azure AD ドメイン サービスでプロビジョニングされるマネージド ドメインの主要な特徴のいくつかを次に示します。

* このマネージド ドメインは、スタンドアロンのドメインです。 Litware のオンプレミスのドメインの拡張機能ではありません。
* Litware の IT 管理者が、このマネージド ドメインのドメイン コントローラーに対して管理、修正プログラムの適用、または監視を行う必要はありません。
* このドメインへの AD レプリケーションを管理する必要はありません。 Litware のオンプレミスのディレクトリからのユーザー アカウント、グループ メンバーシップ、および資格情報は、Azure AD Connect を介して Azure AD に同期されます。 これらのユーザー アカウント、グループ メンバーシップ、および資格情報は、このマネージド ドメイン内で自動的に利用できるようになります。
* ドメインは Azure AD ドメイン サービスによって管理されるので、Litware の IT 管理者には、このドメインに対するドメイン管理者特権またはエンタープライズ管理者特権はありません。

## <a name="benefits"></a>メリット
Azure AD ドメイン サービスを使用することで、次の恩恵を享受することができます。

* **シンプル** – 数回のクリックで Azure インフラストラクチャ サービスに展開された仮想マシンの ID のニーズを満たすことができます。 Azure の ID インフラストラクチャをデプロイおよび管理したり、ユーザーのオンプレミス ID インフラストラクチャに接続を設定し直したりする必要はありません。
* **統合化** – Azure AD ドメイン サービスは、Azure AD テナントと密接に統合されます。 このため、最新のアプリケーションと従来のディレクトリ対応アプリケーションの両方のニーズに対処する統合されたクラウド ベースのエンタープライズ ディレクトリとして Azure AD を使用できるようになります。
* **互換** – Azure AD ドメイン サービスが Windows Server Active Directory の実績のあるエンタープライズ グレードのインフラストラクチャに組み込まれています。 そのため、アプリケーションは、Windows Server Active Directory の機能との互換性をより高いレベルで活用できます。 現時点では、Windows Server AD で使用できるすべての機能が Azure AD ドメイン サービスで利用できるわけではありません。 ただし、使用できる機能については、オンプレミスのインフラストラクチャで依存している、対応する Windows Server AD 機能と互換性があります。 LDAP、Kerberos、NTLM、グループ ポリシー、およびドメインへの参加といった各機能は、さまざまな Windows Server リリースでテストおよび改良された完成度の高いサービスを構成します。
* **コスト効果** – Azure AD ドメイン サービスを使用すると、従来のディレクトリ対応アプリケーションをサポートする ID インフラストラクチャを管理することに関連して発生するインフラストラクチャおよび管理の負担を回避できます。 これらのアプリケーションを Azure インフラストラクチャ サービスに移行することで、運用コストの大幅節減を実現することができます。


## <a name="next-steps"></a>次の手順
### <a name="learn-more-about-azure-ad-domain-services"></a>Azure AD Domain Services の詳細
* [機能](active-directory-ds-features.md)
* [デプロイメント シナリオ](active-directory-ds-scenarios.md)
* [Azure AD Domain Services がユースケースに適しているかを調べる方法](active-directory-ds-comparison.md)
* [Azure AD Domain Services を Azure AD ディレクトリと同期させる方法について](active-directory-ds-synchronization.md)

### <a name="get-started-with-azure-ad-domain-services"></a>Azure AD ドメイン サービスの使用開始
* [Azure Portal を使用して Azure AD Domain Services を有効にする](active-directory-ds-getting-started.md)
