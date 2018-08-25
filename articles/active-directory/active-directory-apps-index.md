---
title: Azure Active Directory のアプリケーション構成の管理に関する記事の索引 | Microsoft Azure
description: フェデレーション証明書の有効期限をカスタマイズする方法と、有効期限が近づいている証明書を更新する方法について説明します。
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: cde70518164c386697127b079979b5b3b76ae088
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2018
ms.locfileid: "42144473"
---
# <a name="article-index-for-application-management-in-azure-active-directory"></a>Azure Active Directory のアプリケーション構成の管理に関する記事の索引
このページでは、Azure Active Directory (Azure AD) のさまざまなアプリケーション関連機能について書かれたすべてのドキュメントの包括的な一覧を示します。

主要な機能の領域ごとに、簡単な説明と、探している情報に応じてどの記事を読めばよいかについてのガイダンスが示されています。

## <a name="overview-articles"></a>概要に関する記事
単に Azure AD のアプリケーション管理機能に関する簡単な説明が必要な場合は、以下の記事を最初に読むことをお勧めします。

| 記事の紹介 |  |
|:---:| --- |
| Azure AD によって解決されるアプリケーション管理に関する問題の概要 |[Azure Active Directory (AD) を使ったアプリケーションの管理](manage-apps/what-is-application-management.md) |
| シングル サインオンの有効化、アプリにアクセスできるユーザーの定義、ユーザーがアプリを起動する方法に関連した、Azure AD のさまざまな機能の概要 |[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](manage-apps/what-is-single-sign-on.md) |
| アプリを Azure AD に統合するときに必要なさまざまな手順の概要 |[アプリケーションと Azure Active Directory の統合](manage-apps/plan-an-application-integration.md)<br /><br />[SaaS アプリへのシングル サインオンの有効化](manage-apps/configure-single-sign-on-portal.md)<br /><br />[アプリへのアクセスの管理](manage-apps/what-is-access-management.md) |
| Azure AD でのアプリの表現方法に関する技術的な説明 |[アプリケーションを Azure AD に追加する方法と理由](active-directory-how-applications-are-added.md) |

## <a name="troubleshooting-articles"></a>トラブルシューティングの記事
このセクションでは、すぐにアクセスできるトラブルシューティング ガイドを紹介します。 各機能領域の詳細については、このページの残りの部分を参照してください。

| 機能領域 |  |
|:---:| --- |
| フェデレーション シングル サインオン |[Azure Active Directory のアプリケーションに対する SAML に基づいたシングル サインオンをデバッグする方法](develop/howto-v1-debug-saml-sso-issues.md) |
| パスワードベースのシングル サインオン |[Internet Explorer 用アクセス パネル拡張機能のトラブルシューティング](active-directory-saas-ie-troubleshooting.md) |
| アプリケーション プロキシ |[アプリケーション プロキシのトラブルシューティング](manage-apps/application-proxy-troubleshoot.md) |
| オンプレミス AD と Azure AD 間のシングル サインオン |[パスワード ハッシュ同期のトラブルシューティング](connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md#troubleshoot-password-hash-synchronization)<br /><br />[パスワード管理のトラブルシューティングの方法](authentication/active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| 動的グループ メンバーシップ |[グループの動的メンバーシップのトラブルシューティング](users-groups-roles/groups-troubleshooting.md) |

## <a name="single-sign-on-sso"></a>シングル サインオン (SSO)
### <a name="federated-single-sign-on-sign-into-many-apps-using-one-identity"></a>フェデレーション シングル サインオン: 1 つの ID を使用した多数のアプリへのサインイン
シングル サインオンを使用すると、ユーザーは 1 つの資格情報セットだけを使用して、さまざまなアプリやサービスにアクセスできます。 フェデレーションは、シングル サインオンを可能にする 1 つの方法です。 ユーザーは、フェデレーション アプリにサインインしようとすると、Azure Active Directory によって表示される組織の公式サインイン ページにリダイレクトされます。その後、認証が成功すると、元のアプリにリダイレクトされます。

| 記事の紹介 |  |
|:---:| --- |
| フェデレーションとその他の種類のサインオンの概要 |[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](manage-apps/what-is-single-sign-on.md) |
| 簡略化されたシングル サインオンの構成手順によって Azure AD と事前統合された多数の SaaS アプリ |[Azure AD アプリケーション ギャラリーの概要](manage-apps/what-is-single-sign-on.md#get-started-with-the-azure-ad-application-gallery)<br /><br />[フェデレーションをサポートしている事前統合アプリの完全な一覧](saas-apps/tutorial-list.md)<br /><br />[Azure Active Directory アプリケーション ギャラリーでのアプリケーションの表示](develop/howto-app-gallery-listing.md) |
| [Salesforce](saas-apps/salesforce-tutorial.md)、[ServiceNow](saas-apps/servicenow-tutorial.md)、[Google Apps](saas-apps/google-apps-tutorial.md)、[Workday](saas-apps/workday-tutorial.md) などのアプリ用にシングル サインオンを構成する方法に関する 150 以上のアプリ チュートリアル |[SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](saas-apps/tutorial-list.md) |
| シングル サインオンの構成を手動で設定してカスタマイズする方法 |[Azure Active Directory アプリケーション ギャラリーに含まれていないアプリへのフェデレーション シングル サインオンを構成する方法](application-config-sso-how-to-configure-federated-sso-non-gallery.md)<br /><br />[Azure Active Directory での事前に統合されたアプリの SAML トークンで発行された要求のカスタマイズ](active-directory-saml-claims-customization.md) |
| SAML プロトコルを使用するフェデレーション アプリのトラブルシューティング ガイド |[Azure Active Directory のアプリケーションに対する SAML に基づいたシングル サインオンをデバッグする方法](develop/howto-v1-debug-saml-sso-issues.md) |
| アプリの証明書の有効期限を構成する方法と証明書を更新する方法 |[Azure Active Directory でのフェデレーション シングル サインオンの証明書の管理](manage-apps/manage-certificates-for-federated-single-sign-on.md) |

フェデレーション シングル サインオンは、Azure AD のすべてのエディションで、ユーザー 1 人につき最大 10 個のアプリに使用できます。 [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) では、サポートされるアプリケーション数に制限はありません。 所属する組織が [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) または [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) を所有している場合は、[フェデレーション アプリケーションへのアクセスの割り当てにグループを使用](#managing-access-to-applications)できます。

### <a name="password-based-single-sign-on-account-sharing-and-sso-for-non-federated-apps"></a>パスワード ベースのシングル サインオン: 非フェデレーション アプリのためのアカウント共有と SSO
フェデレーションがサポートされていないアプリケーションへのシングル サインオンを有効にするために、Azure AD にはパスワード管理機能があります。この機能では、SaaS アプリにパスワードを安全に格納し、それらのアプリにユーザーを自動的にサインインさせることができます。 新しく作成したアカウントの資格情報を配布したり、チーム アカウントを複数のユーザーと共有したりすることも簡単にできます。 ユーザーは、アクセスを許可されたアカウントの資格情報を必ずしも知っている必要はありません。

| 記事の紹介 |  |
|:---:| --- |
| パスワード ベースの SSO のしくみと技術的な概要 |[パスワードベースのシングル サインオン](manage-apps/what-is-single-sign-on.md#password-based-single-sign-on) |
| アカウント共有に関連したシナリオの概要と、Azure AD によるこれらの問題の解決方法 |[Azure AD とのアカウントの共有](active-directory-sharing-accounts.md) |
| 定期的に特定のアプリのパスワードを自動的に変更 |[Automated Password Rollover (preview) (自動パスワード ロールオーバー (プレビュー))](https://blogs.technet.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/) |
| Azure AD パスワード管理拡張機能の Internet Explorer バージョンのデプロイおよびトラブルシューティング ガイド |[グループ ポリシーを使用して Internet Explorer 用アクセス パネル拡張機能をデプロイする方法](active-directory-saas-ie-group-policy.md)<br /><br />[Internet Explorer 用アクセス パネル拡張機能のトラブルシューティング](active-directory-saas-ie-troubleshooting.md) |

パスワード ベースのシングル サインオンは、Azure AD のすべてのエディションで、ユーザー 1 人につき最大 10 個のアプリに使用できます。 [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) では、サポートされるアプリケーション数に制限はありません。 所属する組織が [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) または [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) を所有している場合は、[アプリケーションへのアクセスの割り当てにグループを使用](#managing-access-to-applications)できます。 自動パスワード ロールオーバーは、 [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) の機能です。

### <a name="app-proxy-single-sign-on-and-remote-access-to-on-premises-applications"></a>アプリ プロキシ: オンプレミス アプリケーションへのシングル サインオンとリモート アクセス
ネットワーク外部のユーザーとデバイスがアクセスする必要のあるアプリケーションがプライベート ネットワーク内にある場合は、Azure AD アプリケーション プロキシを使用して、これらのアプリへの安全なリモート アクセスを実現することができます。

| 記事の紹介 |  |
|:---:| --- |
| Azure AD アプリケーション プロキシの概要としくみ |[オンプレミス アプリケーションへの安全なリモート アクセスを実現する方法](manage-apps/application-proxy.md) |
| アプリケーション プロキシを構成する方法と最初のアプリを発行する方法に関するチュートリアル |[Azure AD アプリ プロキシを設定する方法](manage-apps/application-proxy-enable.md)<br /><br />[アプリ プロキシ コネクタをサイレント インストールする方法](manage-apps/application-proxy-register-connector-powershell.md)<br /><br />[アプリ プロキシを使用してアプリケーションを発行する方法](manage-apps/application-proxy-publish-azure-portal.md)<br /><br />[Azure AD アプリケーション プロキシでのカスタム ドメインの使用](manage-apps/application-proxy-configure-custom-domain.md) |
| アプリ プロキシを使用して発行されたアプリのシングル サインオンと条件付きアクセスを有効にする方法 |[アプリケーション プロキシを使用したシングル サインオン](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)<br /><br />[条件付きアクセスとアプリケーション プロキシ](manage-apps/application-proxy-integrate-with-sharepoint-server.md) |
| 次のシナリオでアプリケーション プロキシを使用する方法についてのガイダンス |[ネイティブ クライアント アプリケーションをサポートする方法](manage-apps/application-proxy-configure-native-client-application.md)<br /><br />[要求に対応するアプリケーションをサポートする方法](manage-apps/application-proxy-configure-for-claims-aware-applications.md)<br /><br />[コネクタ グループを使用して別のネットワークや場所にアプリケーションを発行する](manage-apps/application-proxy-connector-groups.md) |
| アプリケーション プロキシのトラブルシューティング ガイド |[アプリケーション プロキシのトラブルシューティング](manage-apps/application-proxy-troubleshoot.md) |

アプリケーション プロキシは、Azure AD のすべてのエディションで、ユーザー 1 人につき最大 10 個のアプリに使用できます。 [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) では、サポートされるアプリケーション数に制限はありません。 所属する組織が [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) または [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) を所有している場合は、[アプリケーションへのアクセスの割り当てにグループを使用](#managing-access-to-applications)できます。

[Azure AD ドメイン サービス](../active-directory-domain-services/active-directory-ds-overview.md)もご確認ください。これを使用すると、オンプレミス アプリケーションを Azure に移行すると同時に、引き続きそれらのアプリケーションの ID ニーズを満たすことができます。

### <a name="enabling-single-sign-on-between-azure-ad-and-on-premises-ad"></a>Azure AD とオンプレミス AD 間のシングル サインオンの有効化
組織がオンプレミスの Windows Server Active Directory と、クラウドの Azure Active Directory を保持している場合、これらの 2 つのシステム間でシングル サインオンを有効にすることができます。 Azure AD Connect (この 2 つのシステムを統合するツール) には、ADFS やその他のフェデレーション プロバイダーとのフェデレーションを確立するオプション、パスワード同期を有効にするオプションなど、シングル サインオンを設定するためのオプションが複数用意されています。

| 記事の紹介 |  |
|:---:| --- |
| Azure AD Connect に用意されているシングル サインオン オプションの概要と、ハイブリッド環境の管理に関する情報 |[Azure AD Connect ユーザーのサインオン オプション](active-directory-aadconnect-user-signin.md) |
| オンプレミスの Active Directory と Azure Active Directory 両方を使用している環境を管理するための一般的なガイダンス |[Azure AD ハイブリッド ID の設計上の考慮事項](active-directory-hybrid-identity-design-considerations-overview.md)<br /><br />[オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md) |
| SSO を有効にするためのパスワード同期の使用についてのガイダンス |[Azure AD Connect でのパスワード同期の実装](connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md)<br /><br />[Azure Active Directory 同期ツール使用時にパスワード同期で発生する問題をトラブルシューティングする方法](https://support.microsoft.com/kb/2855271) |
| SSO を有効にするためのパスワード ライトバックの使用についてのガイダンス |[Azure AD でのパスワード管理の概要](authentication/quickstart-sspr.md)<br /><br />[パスワード ライトバックのトラブルシューティングの方法](authentication/active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| SSO を有効にするためのサード パーティ ID プロバイダーの使用についてのガイダンス |[サードパーティ ID プロバイダーを使用してシングル サインオンを実装する](https://aka.ms/ssoproviders) |
| Windows 10 ユーザーが Azure AD 参加によるシングル サインオンの利点を活用する方法 |[Azure Active Directory 参加を使用したクラウド機能の Windows 10 デバイスへの拡張](active-directory-azureadjoin-overview.md) |

Azure AD Connect は、 [Azure Active Directory のすべてのエディション](https://azure.microsoft.com/pricing/details/active-directory/)で使用できます。 Azure AD のセルフサービスによるパスワードのリセットは、[Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) と [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) で使用できます。 オンプレミス AD へのパスワード ライトバックは、 [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) の機能です。

### <a name="conditional-access-enforce-additional-security-requirements-for-high-risk-apps"></a>条件付きアクセス: 危険性の高いアプリに追加のセキュリティ要件を適用
アプリとリソースへのシングル サインオンを設定したら、機密性の高いアプリケーションのセキュリティを強化することができます。そのためには、該当アプリへの各サインインに特定のセキュリティ要件を適用します。 たとえば、Azure AD を使用すると、特定のアプリへのすべてのアクセスで必ず多要素認証が要求されるようにすることができます。そのアプリ自体がその機能をサポートしているかどうかは関係ありません。 条件付きアクセスのもう 1 つの一般的な例として、特に機密性の高いアプリケーションにアクセスする場合に、ユーザーが組織の信頼できるネットワークに接続していることが要求されます。

| 記事の紹介 |  |
|:---:| --- |
| Azure AD、Office365、Intune に提供されている条件付きアクセス機能の概要 |[条件付きアクセス ポリシーを使用したリスクの管理](active-directory-conditional-access-azure-portal.md) |
| 次の種類のリソースで条件付きアクセスを有効にする方法 |[SaaS アプリ向けの条件付きアクセス](conditional-access/app-based-conditional-access.md)<br /><br />[Office 365 サービス用条件付きアクセス](active-directory-conditional-access-device-policies.md)<br /><br />[オンプレミス アプリケーション用条件付きアクセス](active-directory-conditional-access-azure-portal.md)<br /><br />[条件付きアクセスの使用](manage-apps/application-proxy-integrate-with-sharepoint-server.md) |
| デバイス ベースの条件付きアクセス ポリシーを有効にするためにデバイスを Azure Active Directory に登録する方法 |[Azure Active Directory Device Registration の概要](active-directory-conditional-access-device-registration-overview.md)<br /><br />[ドメイン参加済み Windows デバイスの自動デバイス登録を有効にする方法](active-directory-conditional-access-automatic-device-registration.md)<br />— [Windows 8.1 デバイスの手順](active-directory-conditional-access-automatic-device-registration-setup.md)<br />— [Windows 7 デバイスの手順](active-directory-conditional-access-automatic-device-registration-setup.md) |

| 2 段階認証のために Microsoft Authenticator アプリを使用する方法 |[Microsoft Authenticator](user-help/microsoft-authenticator-app-how-to.md) |

条件付きアクセスは、 [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) の機能です。

## <a name="apps--azure-ad"></a>アプリと Azure AD
### <a name="cloud-discovery-find-which-saas-apps-are-being-used-in-your-organization"></a>Cloud Discovery: 組織で使用されている SaaS アプリを検索
Cloud Discovery は、Microsoft Cloud App Security のクラウド アプリ カタログに対するトラフィック ログを分析します。クラウド アプリ カタログは、16,000 を超えるクラウド アプリからなり、これらのクラウド アプリは、70 を超えるリスク要因に基づいてランクとスコアが付けられています。このトラフィック ログの分析により、クラウドの利用状況、シャドウ IT、シャドウ IT が組織に与えるリスクを継続的に確認することができます。

| 記事の紹介 |  |
|:---:| --- |
| 機能の概要 |[Cloud Discovery の設定](/cloud-app-security/set-up-cloud-discovery) |


### <a name="automatically-provision-and-deprovision-user-accounts-in-saas-apps"></a>SaaS アプリのユーザー アカウントの自動プロビジョニングとプロビジョニング解除
Dropbox、Salesforce、ServiceNow などの SaaS アプリケーションで、ユーザー ID の作成、保守、削除を自動化します。 Azure AD と SaaS アプリ間で既存の ID を照合および同期します。また、ユーザーが組織を離れるときに自動的にアカウントを無効にすることでアクセスを制御します。

| 記事の紹介 |  |
|:---:| --- |
| 機能の説明と、一般的な質問に対する回答 |[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](active-directory-saas-app-provisioning.md) |
| Azure AD と SaaS アプリ間で情報を照合する方法の構成 |[属性マッピングのカスタマイズ](active-directory-saas-customizing-attribute-mappings.md)<br><br>[属性マッピングの式の書き方](active-directory-saas-writing-expressions-for-attribute-mappings.md) |
| SCIM プロトコルをサポートしている任意のアプリへの自動プロビジョニングを有効にする方法 |[SCIM を使用して、Azure Active Directory からアプリケーションへのユーザーとグループの自動プロビジョニングを有効にする](manage-apps/use-scim-to-provision-users-and-groups.md) |
| ユーザー プロビジョニングについてのレポートとトラブルシューティングの方法 |[自動ユーザー プロビジョニングについてのレポート](active-directory-saas-provisioning-reporting.md)<br><br>[ユーザー プロビジョニングのトラブルシューティング](active-directory-application-provisioning-content-map.md) |
| 属性値に基づいて、アプリケーションにプロビジョニングされるユーザーを制限する |[スコープ フィルターを使用する属性ベースのアプリ プロビジョニング](active-directory-saas-scoping-filters.md) |

自動化されたユーザー プロビジョニングは、Azure AD のすべてのエディションで、ユーザー 1 人につき最大 10 個のアプリに使用できます。 [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) では、サポートされるアプリケーション数に制限はありません。 所属する組織が [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) または [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) を所有している場合は、[プロビジョニングの対象となるユーザーの管理にグループを使用](#managing-access-to-applications)できます。

### <a name="building-applications-that-integrate-with-azure-ad"></a>Azure AD と統合するアプリケーションの構築
基幹業務 (LoB) アプリケーションを開発または保守している組織や、顧客が Azure Active Directory を使用しているアプリ開発者には、アプリケーションと Azure AD を統合する際に次のチュートリアルが役立ちます。

| 記事の紹介 |  |
|:---:| --- |
| アプリと Azure AD の統合に関する、IT プロフェッショナルとアプリケーション開発者向けのガイダンス |[IT プロフェッショナルの Azure AD のアプリケーション開発用ガイド](active-directory-applications-guiding-developers-for-lob-applications.md)<br /><br />[Azure Active Directory 開発者ガイド](develop/azure-ad-developers-guide.md) |
| アプリケーション ベンダーが自社のアプリを Azure AD アプリ ギャラリーに追加する方法 |[Azure Active Directory アプリケーション ギャラリーでのアプリケーションの表示](develop/howto-app-gallery-listing.md) |
| 開発したアプリケーションへのアクセスを Azure Active Directory を使用して管理する方法 |[開発したアプリケーションのユーザー割り当てを有効にする方法](active-directory-applications-guiding-developers-requiring-user-assignment.md)<br /><br />[Azure AD とアプリケーション: アプリケーションへのユーザーの割り当て](active-directory-applications-guiding-developers-assigning-users.md)<br /><br />[Azure AD とアプリケーション: アプリケーションへのグループの割り当て](active-directory-applications-guiding-developers-assigning-groups.md) |

コンシューマー向けアプリケーションの開発者は、ユーザーを管理するための独自の ID システムを開発しなくて済むように、 [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) の使用に関心を持つ場合があります。 [詳細情報](../active-directory-b2c/active-directory-b2c-overview.md)

## <a name="managing-access-to-applications"></a>アプリケーションへのアクセスの管理
### <a name="using-groups-and-self-service-to-manage-who-has-access-to-which-apps"></a>どのアプリケーションにどのユーザーがアクセスするかを管理するためのグループおよびセルフ サービスの使用
だれがどのリソースにアクセスできるかを管理できるようにするために、Azure Active Directory を使用すると、グループを使用して、大規模に割り当ておよびアクセス許可を設定することができます。 IT は、ユーザーが必要に応じてアクセス許可を簡単に要求できるように、セルフサービス機能を有効にすることもできます。

| 記事の紹介 |  |
|:---:| --- |
| Azure AD アクセス管理機能の概要 |[アプリへのアクセスの管理](manage-apps/what-is-access-management.md)<br /><br />[Azure AD でのアクセス管理のしくみ](fundamentals/active-directory-manage-groups.md)<br /><br />[SaaS アプリケーションへのアクセスをグループで管理する](users-groups-roles/groups-saasapps.md) |
| アプリとグループのセルフサービス管理の有効化 |[セルフサービス アプリケーション管理](active-directory-self-service-application-access.md)<br /><br />[アプリケーション アクセス管理のセルフサービス化に必要な Azure Active Directory の設定](users-groups-roles/groups-self-service-management.md) |
| Azure AD でグループを設定するための手順 |[セキュリティ グループを作成する方法](fundamentals/active-directory-groups-create-azure-portal.md)<br /><br />[グループの所有者を指定する方法](fundamentals/active-directory-accessmanagement-managing-group-owners.md)<br /><br />[Azure Active Directory の専用グループ](active-directory-accessmanagement-dedicated-groups.md) |
| 動的グループを使用した、属性ベースのメンバーシップ規則によるグループのメンバーシップの自動設定 |[動的メンバーシップ: 高度な規則](active-directory-groups-dynamic-membership-azure-portal.md)<br /><br />[グループの動的メンバーシップのトラブルシューティング](users-groups-roles/groups-troubleshooting.md) |

グループ ベースのアプリケーション アクセス管理は、[Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) と [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) で使用できます。 セルフサービス グループ管理、セルフサービス アプリケーション管理、および動的グループは、 [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) の機能です。

### <a name="b2b-collaboration-enable-partner-access-to-applications"></a>B2B コラボレーション: アプリケーションへのパートナー アクセスを有効化
他の企業と提携している場合は、社内アプリケーションへのパートナー アクセスを管理することが必要になる場合があります。 Azure Active Directory B2B コラボレーションを使用すると、アプリをパートナーと簡単かつ安全に共有できます。

| 記事の紹介 |  |
|:---:| --- |
| パートナー、顧客などの外部ユーザーの管理に役立つさまざまな Azure AD 機能の概要 |[Azure Active Directory を使用して外部 ID を管理するための機能の比較](active-directory-b2b-compare-external-identities.md) |
| B2B コラボレーションの概要と使用を開始する方法 |[Azure AD との簡単で安全なクラウド パートナー統合](active-directory-b2b-what-is-azure-ad-b2b.md)<br /><br />[Azure Active Directory (Azure AD) の B2B コラボレーション](active-directory-b2b-collaboration-overview.md) |
| Azure AD B2B コラボレーションの詳細とその使用方法 |[B2B コラボレーション: しくみ](active-directory-b2b-how-it-works.md)<br /><br />[Azure AD B2B コラボレーションの現在の制限事項](active-directory-b2b-current-limitations.md)<br /><br />[Azure AD B2B コラボレーションの使用方法に関する詳細なチュートリアル](active-directory-b2b-detailed-walkthrough.md) |
| Azure AD B2B コラボレーションのしくみの技術的な詳細を含むリファレンス記事 |[パートナー ユーザーを追加するための CSV ファイルの形式](active-directory-b2b-references-csv-file-format.md)<br /><br />[Azure AD B2B コラボレーションの影響を受けるユーザー属性](active-directory-b2b-references-external-user-object-attribute-changes.md)<br /><br />[Azure Active Directory (Azure AD) B2B コラボレーション プレビューの外部ユーザー トークンの形式](active-directory-b2b-references-external-user-token-format.md) |

B2B コラボレーションは、現在、[Azure Active Directory のすべてのエディション](https://azure.microsoft.com/pricing/details/active-directory/)で利用できます。

### <a name="access-panel-a-portal-for-accessing-apps-and-self-service-features"></a>アクセス パネル: アプリやセルフサービス機能にアクセスするためポータル
Azure AD アクセス パネルでは、エンド ユーザーがアプリを起動したり、アプリとグループ メンバーシップを管理できるセルフサービス機能にアクセスしたりすることができます。 下の一覧には、アクセス パネルのほか、SSO 対応アプリにアクセスするためのその他のオプションも示されています。

| 記事の紹介 |  |
|:---:| --- |
| シングル サインオン アプリをユーザーにデプロイするために使用できるさまざまなオプションの比較 |[Azure AD 統合アプリケーションをユーザーにデプロイする](manage-apps/what-is-single-sign-on.md#deploying-azure-ad-integrated-applications-to-users) |
| アクセス パネルとそのモバイル版である MyApps の概要 |[アクセス パネルと MyApps の概要](user-help/active-directory-saas-access-panel-introduction.md)<br />— [iOS](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8)<br />— [Android](https://play.google.com/store/apps/details?id=com.microsoft.myapps) |
| Office 365 Web サイトから Azure AD アプリにアクセスする方法 |[Office 365 アプリ起動ツールの概要](https://support.office.com/en-us/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a) |
| Intune Managed Browser モバイル アプリから Azure AD アプリにアクセスする方法 |[Intune Managed Browser](https://technet.microsoft.com/library/dn878029.aspx)<br />— [iOS](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8)<br />— [Android](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser) |
| シングル サインオンを開始するディープ リンクを使用して Azure AD アプリにアクセスする方法 |[フェデレーション アプリ、パスワードベースのアプリ、または既存のアプリの直接サインオン リンク](manage-apps/what-is-single-sign-on.md#direct-sign-on-links-for-federated-password-based-or-existing-apps) |

アクセス パネルは、 [Azure Active Directory のすべてのエディション](https://azure.microsoft.com/pricing/details/active-directory/)で使用できます。

### <a name="reports-easily-audit-app-access-changes-and-monitor-sign-ins-to-apps"></a>レポート: アプリへのアクセスの変更の監査とアプリへのサインインの監視を容易に実行
Azure Active Directory には、組織のアプリケーションへのアクセスを監視できるように、いくつかのレポートとアラートが用意されています。 アプリへの異常なサインインのアラートを受信したり、アプリケーションへのユーザーのアクセスが変更された日時や理由を追跡したりできます。

| 記事の紹介 |  |
|:---:| --- |
| Azure Active Directory のレポート機能の概要 |[Azure Active Directory レポートの使用の開始](active-directory-reporting-getting-started.md) |
| ユーザーのサインインとアプリ使用状況を監視する方法 |[アクセスおよび使用状況レポートの表示](active-directory-view-access-usage-reports.md) |
| 特定のアプリケーションにアクセスできるユーザーに対して行われた変更の追跡 |[Azure Active Directory 監査レポートのイベント](active-directory-reporting-audit-events.md) |
| Reporting API を使用してこれらのレポートのデータを任意のツールにエクスポート |[Azure AD Reporting API の概要](active-directory-reporting-api-getting-started.md) |

Azure Active Directory の各エディションに付属のレポートを確認するには、 [ここをクリック](active-directory-view-access-usage-reports.md)してください。

## <a name="see-also"></a>関連項目
[Azure Active Directory とは](fundamentals/active-directory-whatis.md)

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/)

[Azure Active Directory Domain Services](https://azure.microsoft.com/services/active-directory-ds/)

[Azure Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/)
