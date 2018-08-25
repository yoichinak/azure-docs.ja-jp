---
title: ID 管理に役立つ Azure のセキュリティ機能 | Microsoft Docs
description: " この記事は、ID 管理に役立つ Azure のコア セキュリティ機能の概要を説明します。 Microsoft ID およびアクセス管理ソリューションは、IT が企業のデータ センター全体とクラウドのアプリケーションとリソースへのアクセスを保護するのに役立ち、多要素認証や条件付きアクセス ポリシーなどの追加レベルの検証を可能にします。 "
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 5aa0a7ac-8f18-4ede-92a1-ae0dfe585e28
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: cbf6ac736db9935f5ec52fa4507dab1e56bcde43
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611189"
---
# <a name="azure-identity-management-security-overview"></a>Azure ID 管理のセキュリティの概要
Microsoft ID およびアクセス管理ソリューションは、IT が企業のデータ センター全体とクラウドのアプリケーションとリソースへのアクセスを保護するのに役立ちます。 この保護によって、多要素認証や条件付きアクセス ポリシーなどの追加レベルの検証が可能になります。 高度なセキュリティ報告、監査、および警告によって疑わしいアクティビティを監視し、潜在的なセキュリティ上の問題を軽減できます。 [Azure Active Directory Premium](../active-directory/active-directory-editions.md) は、クラウド上の何千ものサービスとしてのソフトウェア (SaaS) アプリケーションへのシングル サインオン (SSO)、およびオンプレミスで実行する Web アプリケーションへのアクセスを提供します。

Azure Active Directory (Azure AD) のセキュリティ上の利点を活用することで、次のことが可能になります。

* ハイブリッドのエンタープライズ全体の各ユーザーに個別の ID を作成して管理し、ユーザー、グループ、およびデバイスが同期された状態を維持する
* 何千もの事前統合された SaaS アプリを含むアプリケーションに SSO アクセスを提供する。
* ルール ベースの多要素認証をオンプレミス アプリケーションとクラウド アプリケーションの両方に適用することによって、アプリケーション アクセスのセキュリティを有効にする。
* Azure AD アプリケーション プロキシを通じてオンプレミス Web アプリケーションへの安全なリモート アクセスをプロビジョニングする。

この記事の目的は、ID 管理に役立つ Azure のコア セキュリティ機能の概要を説明することです。 それぞれの詳細について説明する記事へのリンクも用意されているため、さらに詳しく学習できます。  

この記事は、次の Azure ID 管理のコア機能の説明に重点を置いています。

* シングル サインオン
* リバース プロキシ
* Multi-Factor Authentication
* セキュリティの監視、アラート、および機械学習ベースのレポート
* コンシューマーの ID とアクセスの管理
* デバイス登録
* Privileged Identity Management
* Identity Protection
* ハイブリッド ID 管理

## <a name="single-sign-on"></a>シングル サインオン
SSO とは、1 つのユーザー アカウントを使って 1 回サインインするだけで作業に必要なすべてのアプリケーションとリソースにアクセスできる機能です。 いったんサインインすると、もう一度認証 (パスワードの入力など) を求められることなく、必要なすべてのアプリケーションにアクセスできます。

多くの組織では、ユーザーの生産性向上のため、Office 365、Box、Salesforce などの SaaS アプリケーションに依存しています。 従来は、IT スタッフが各 SaaS アプリケーションのユーザー アカウントを個別に作成し、更新する必要がありました。さらに、ユーザーは、各 SaaS アプリケーションのパスワードを覚える必要がありました。

Azure AD はオンプレミスの Active Directory 環境をクラウドに拡張して、ユーザーがプライマリ組織アカウントを使用してドメイン参加デバイスおよび会社のリソースにサインインするだけでなく、それぞれの業務に必要なすべての Web アプリケーションおよび SaaS アプリケーションにもサインインできるようにします。

これにより、ユーザーが複数のユーザー名とパスワードのセットを管理する必要がなくなるだけでなく、組織のグループや従業員としての地位に基づいてアプリケーションのアクセスを自動的にプロビジョニングまたはプロビジョニング解除することが可能になります。 Azure AD にはセキュリティおよびアクセス管理コントロールが導入されており、SaaS アプリケーション間でユーザー アクセスを一元的に管理できます。

詳細情報:

* [シングル サインオンの概要](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../active-directory/manage-apps/what-is-single-sign-on.md)
* [SaaS アプリと Azure Active Directory シングル サインオンを統合する](../active-directory/manage-apps/configure-single-sign-on-portal.md)

## <a name="reverse-proxy"></a>リバース プロキシ
Azure AD アプリケーション プロキシを使用すると、[SharePoint](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) サイト、[Outlook Web アプリ](https://technet.microsoft.com/library/jj657718.aspx)、[IIS](http://www.iis.net/) ベースのアプリなどのオンプレミス アプリケーションをプライベート ネットワーク内で発行し、ネットワーク外部のユーザーにセキュリティで保護されたアクセスを提供できます。 アプリケーション プロキシでは、多くの種類のオンプレミス Web アプリケーションに対応するリモート アクセスと SSO を、Azure AD がサポートする数千もの SaaS アプリケーションとともに利用できます。 従業員は、自宅から自分のデバイスでアプリケーションにサインインし、このクラウド ベースのプロキシを使用して認証を行うことができます。

詳細情報:

* [Azure AD アプリケーション プロキシの有効化](../active-directory/manage-apps/application-proxy-enable.md)
* [Azure AD アプリケーション プロキシを使用してアプリケーションを発行する](../active-directory/active-directory-application-proxy-publish.md)
* [アプリケーション プロキシを使用したシングル サインオン](../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
* [条件付きアクセスの使用](../active-directory/manage-apps/application-proxy-integrate-with-sharepoint-server.md)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication
Azure Multi-Factor Authentication は、複数の確認方法の使用を要求することで、ユーザーのサインインとトランザクションに重要な 2 つ目のセキュリティ レイヤーを追加する認証方法です。 Multi-Factor Authentication を使えば、シンプルなサインイン プロセスを好むユーザーのニーズに応えながら、データやアプリケーションへのアクセスを効果的に保護できます。 電話やテキスト メッセージ、モバイル アプリによる通知のほか、確認コードやサード パーティの OAuth トークンなど、一連の照合方法を通じて確実な認証を行うことができます。

詳細情報:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Azure Multi-Factor Authentication とは](../active-directory/authentication/multi-factor-authentication.md)
* [Azure Multi-Factor Authentication のしくみ](../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>セキュリティの監視、アラート、および機械学習ベースのレポート
セキュリティの監視とアラートや、整合性のないアクセス パターンを識別する機械学習ベースのレポートを使用して、ビジネスを保護できます。 Azure AD のアクセスおよび使用状況レポートを使うことで、組織のディレクトリの完全性とセキュリティを視覚的に確認できます。 ディレクトリ管理者は、この情報を使用して、リスクを軽減するために適切に計画できるように、セキュリティ上のリスクがある箇所をより適切に確認できます。

Azure portal では、レポートは次のカテゴリに分類されます。

* **異常レポート**: 異常と考えられるサインイン イベントが含まれます。 この目的は、このようなアクティビティを認識し、イベントが不審であるかどうかを判断できるようにすることです。
* **統合アプリケーション レポート**: 組織内のクラウド アプリケーションの使用状況を明らかにします。 Azure AD は、何千ものクラウド アプリケーションとの統合を提供します。
* **エラー レポート**: 外部アプリケーションにアカウントをプロビジョニングするときに発生することがあるエラーを示します。
* **ユーザー固有レポート**: 特定のユーザーのデバイス サインイン アクティビティ データを示します。
* **アクティビティ ログ**: 過去 24 時間、過去 7 日間、または過去 30 日間のすべての監査イベントの記録、グループのアクティビティの変更、およびパスワードのリセットと登録のアクティビティが含まれます。

詳細情報:

* [アクセスおよび使用状況レポートの表示](../active-directory/active-directory-view-access-usage-reports.md)
* [Azure Active Directory レポートの使用の開始](../active-directory/active-directory-reporting-getting-started.md)
* [Azure Active Directory レポート ガイド](../active-directory/active-directory-reporting-guide.md)

## <a name="consumer-identity-and-access-management"></a>コンシューマーの ID とアクセスの管理
Azure AD B2C は、数億個の ID まで拡張可能な、コンシューマー向けアプリケーション用の可用性の高いグローバルな ID 管理サービスです。 モバイルと Web の両方のプラットフォームにわたる統合を実現できます。 コンシューマーは、既に持っているソーシャル アカウントを使用するか、新たな資格情報を作成して、すべてのアプリケーションにサインインできます。その場合のエクスペリエンスは、カスタマイズすることができます。

これまで、開発したアプリケーションにコンシューマーがサインアップおよびサインインすることを望むアプリケーション開発者は、独自のコードを記述していました。 開発者は、オンプレミスのデータベースまたはシステムを使用して、ユーザー名とパスワードを保存していました。 Azure AD B2C では、セキュリティ保護された標準ベースのプラットフォームと機能豊富で拡張可能なポリシーのセットを使用して、より簡単にコンシューマーの ID 管理をアプリケーションに統合できます。

Azure AD B2C を使用すると、コンシューマーは、既存のソーシャル アカウント (Facebook、Google、Amazon、LinkedIn) を使用するか、または新しい資格情報 (電子メール アドレスとパスワードまたはユーザー名とパスワード) を作成することによって、アプリケーションにサインアップできます。

詳細情報:

* [Azure Active Directory B2C とは](https://azure.microsoft.com/services/active-directory-b2c/)
* [Azure Active Directory B2C プレビュー: アプリケーションにコンシューマーをサインアップおよびサインインする](../active-directory-b2c/active-directory-b2c-overview.md)
* [Azure Active Directory B2C プレビュー: アプリケーションの種類](../active-directory-b2c/active-directory-b2c-apps.md)

## <a name="device-registration"></a>デバイス登録
Azure AD Device Registration は、デバイスに基づいて[条件付きでアクセス](../active-directory/active-directory-conditional-access-device-registration-overview.md)を許可するというシナリオの基礎となる機能です。 デバイスが登録されると、ユーザーがサインインしたときにデバイスを認証するために使用される ID が、Azure AD のデバイス登録によって指定されます。 認証済みのデバイスおよびデバイスの属性を使用して、クラウドおよびオンプレミスでホストされるアプリケーションに条件付きアクセス ポリシーを適用できます。

Intune などのモバイル デバイス管理ソリューションと組み合わせて使用すると、Azure AD 内のデバイスの属性は、デバイスに関する情報が追加されて更新されます。 これにより、条件付きアクセス規則を作成できます。この規則に従い、デバイスからのアクセス時にセキュリティおよび法令遵守の基準を満たす必要があります。

詳細情報:

* [Azure AD デバイス登録の使用](../active-directory/active-directory-conditional-access-device-registration-overview.md)
* [Azure AD への Windows ドメイン参加済みデバイスの自動デバイス登録](../active-directory/active-directory-conditional-access-automatic-device-registration.md)
* [Azure AD への Windows ドメイン参加済みデバイスの自動登録の設定](../active-directory/active-directory-conditional-access-automatic-device-registration-setup.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management
Azure AD Privileged Identity Management を使用すると、特権 ID と、Azure AD や他の Microsoft オンライン サービス (Office 365 や Microsoft Intune など) のリソースへのアクセスを管理、制御、監視できます。

ユーザーは、Azure や Office 365 のリソース、または他の SaaS アプリで、特権操作を実行することが必要になる場合があります。 通常は、組織がユーザーに Azure AD で永続的な特権アクセスを付与する必要があります。 しかし、このようなアクセスでは、ユーザーが管理者特権を使用して実行している内容を組織が十分に監視できないため、クラウドでホストされているリソースのセキュリティ リスクが増大します。 また、特権アクセスを持つユーザー アカウントが侵害された場合に、その 1 つの侵害が組織のクラウド セキュリティ全体に影響を与える可能性もあります。 Azure AD Privileged Identity Management はこのリスクの軽減に役立ちます。

Azure AD Privileged Identity Management を使用することで、次のことが可能になります。

* Azure AD の管理者であるユーザーを特定する。
* Office 365 や Intune などの Microsoft サービスへのオンデマンドのジャスト イン タイム (JIT) な管理アクセスを可能にする。
* 管理者のアクセス履歴と管理者の割り当ての変更に関するレポートを取得する。
* 特権ロールへのアクセスに関するアラートを受け取る。

詳細情報:

* [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md)
* [Azure AD Privileged Identity Management におけるロール](../active-directory/privileged-identity-management/pim-roles.md)
* [Azure AD Privileged Identity Management: ユーザー ロールを追加または削除する方法](../active-directory/privileged-identity-management/pim-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Identity Protection
Azure AD Identity Protection は、リスク イベントや組織の ID に影響する潜在的な脆弱性に関する統合ビューを提供するセキュリティ サービスです。 Identity Protection は、Azure AD 異常アクティビティ レポートで使用可能な既存の Azure AD の異常検出機能を活用します。 Identity Protection には、リアルタイムで異常を検出できる新しいリスク イベントの種類も導入されています。

詳細情報:

* [Azure AD Identity Protection](../active-directory/active-directory-identityprotection.md)
* [Channel 9: Azure AD and Identity Show: Identity Protection Preview (Channel 9: Azure AD および Identity ショー: Identity Protection プレビュー)](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-management"></a>ハイブリッド ID 管理
Microsoft の ID 管理はオンプレミスとクラウドにまたがり、場所に関係なく、すべてのリソースの認証と権限付与を 1 つのユーザー ID で行います。

詳細情報:

* [ハイブリッド ID ホワイト ペーパー](http://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
* [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Azure AD のチーム ブログ](https://blogs.technet.microsoft.com/ad/)
