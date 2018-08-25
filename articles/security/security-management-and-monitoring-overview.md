---
title: Azure セキュリティの管理と監視の概要 | Microsoft Docs
description: この記事では、Azure クラウド サービスと仮想マシンの管理と監視を支援するために Azure が提供するセキュリティ機能とサービスの概要を説明します。
services: security
documentationcenter: na
author: TerryLanfear
manager: StevenPo
editor: TomSh
ms.assetid: 5cf2827b-6cd3-434d-9100-d7411f7ed424
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: terrylan
ms.openlocfilehash: 10c2ed359fa77ad00945ddcfbc55dc0901ba8bff
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38697107"
---
# <a name="azure-security-management-and-monitoring-overview"></a>Azure セキュリティの管理と監視の概要
Azure には、そのクラウド サービスと仮想マシン (VM) の管理と監視を支援するセキュリティ メカニズムが備わっています。 この記事では、これらの主要なセキュリティ機能とサービスの概要を示します。 それぞれの詳細について説明する記事へのリンクが用意されているため、さらに詳しく学習できます。

Microsoft クラウド サービスのセキュリティは、パートナーシップに基づくものであり、お客様と Microsoft が共有する責任です。 Microsoft は、Azure プラットフォームおよびそのデータ センターの (入室に ID カードが必要なドア、フェンス、守衛などのセキュリティ保護の使用による) 物理的なセキュリティの責任を負います。 Azure のソフトウェア レイヤーには強力なレベルのクラウド セキュリティが備わっており、セキュリティ、プライバシー、コンプライアンスに関する顧客のニーズを満たすことができます。

お客様は、自分のデータと ID を所有し、それらとオンプレミス リソースのセキュリティ、および自分が制御しているクラウド コンポーネントのセキュリティを保護する責任を持ちます。 Microsoft は、セキュリティ制御や、データとアプリケーションを保護するための機能をお客様に提供します。 お客様のセキュリティの責任の度合いは、クラウド サービスの種類に応じて決まります。

次の表は、Microsoft とお客様の間の責任の分担をまとめたものです。

![Shared responsibility][1]

セキュリティの管理について詳しくは、「[Azure のセキュリティ管理](azure-security-management.md)」をご覧ください。

## <a name="role-based-access-control"></a>ロールベースのアクセス制御
ロールベースのアクセス制御 (RBAC) は、Azure リソースのアクセス権を詳細に管理できるようにします。 RBAC を使用すると、職務に必要な範囲のアクセス権だけをユーザーに付与することができます。 RBAC は、ユーザーが組織を離れるときに、クラウド内のリソースへのアクセス権を失うようにするためにも役立ちます。

詳細情報:

* [RBAC に関する Active Directory チームのブログ](https://cloudblogs.microsoft.com/enterprisemobility/?product=azure-active-directory)
* [Azure のロールベースのアクセス制御](../role-based-access-control/role-assignments-portal.md)

## <a name="antimalware"></a>マルウェア対策
Azure では、Microsoft、Symantec、Trend Micro、McAfee、Kaspersky などの主要セキュリティ ベンダーのマルウェア対策ソフトウェアを使用できます。 このソフトウェアは、悪意のあるファイル、アドウェア、他の脅威から仮想マシンを保護するのに役立ちます。

Azure Cloud Services および Virtual Machines の Microsoft マルウェア対策は、PaaS ロールと仮想マシンの両方のためのマルウェア対策エージェントをインストールする機能を提供します。 この機能は、System Center Endpoint Protection に基づいており、実績のあるオンプレミス セキュリティ テクノロジをクラウドに持ち込みます。

また、Trend の [Deep Security](http://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/) と [SecureCloud](http://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/) 製品も Azure プラットフォームに緊密に統合されます。 DeepSecurity はウイルス対策ソリューションであり、SecureCloud は暗号化ソリューションです。 DeepSecurity は、拡張機能モデルを使って、VM の内部に展開されます。 Azure portal UI と PowerShell を使うと、スピンアップされている新しい VM 内で DeepSecurity を使うか、既にデプロイされている既存の VM 内で使うかを選択することができます。

Symantec Endpoint Protection (SEP) も、Azure でサポートされています。 ポータルの統合により、お客様は SEP を VM 上で使うように指定できます。 SEP は、Azure Portal を通じて新しい VM にインストールすることができます。また、PowerShell を使って既存の VM にインストールすることもできます。

詳細情報:

* [Azure Virtual Machines へのマルウェア対策ソリューションのデプロイ](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Azure Cloud Services および Virtual Machines 向け Microsoft マルウェア対策](azure-security-antimalware.md)
* [Windows VM に Trend Micro Deep Security をサービスとしてインストールし、構成する方法](../virtual-machines/windows/classic/install-trend.md)
* [Windows VM に Symantec Endpoint Protection をインストールし、構成する方法](../virtual-machines/windows/classic/install-symantec.md)
* [Azure Virtual Machines を保護するための新しいマルウェア対策オプション](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication
Azure Multi-Factor Authentication は、複数の検証方法の使用を要求する認証の方法です。 ユーザーのサインインとトランザクションに重要な第 2 のセキュリティ レイヤーを追加できます。 

Multi-Factor Authentication を使えば、シンプルなサインイン プロセスを好むユーザーのニーズに応えながら、データやアプリケーションへのアクセスを効果的に保護できます。 電話やテキスト メッセージ、モバイル アプリによる通知のほか、確認コードやサード パーティの OATH トークンなど、一連の照合方法を通じて確実な認証を行うことができます。

詳細情報:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Azure Multi-Factor Authentication とは](../active-directory/authentication/multi-factor-authentication.md)
* [Azure Multi-Factor Authentication のしくみ](../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="expressroute"></a>ExpressRoute
Azure ExpressRoute を使うと、接続プロバイダーが提供する専用プライベート接続で、オンプレミスのネットワークを Microsoft クラウドに拡張できます。 ExpressRoute では、Azure、Office 365、CRM Online などの Microsoft クラウド サービスへの接続を確立できます。 以下のものから接続できます。

- 任意の環境間 (VPN IP) ネットワーク。
- ポイント ツー ポイントのイーサネット ネットワーク。
- コロケーション施設の接続プロバイダー経由での仮想クロス接続。 

ExpressRoute 接続はパブリックなインターネットを経由しません。 ExpressRoute 接続は一般的なインターネットでの接続よりも信頼性が高く、高速で、待ち時間が短く、セキュリティの高い接続を提供できます。

詳細情報:

* [ExpressRoute の技術概要](../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>仮想ネットワーク ゲートウェイ
VPN ゲートウェイ (Azure 仮想ネットワーク ゲートウェイとも呼ばれます) は、仮想ネットワークとオンプレミスの場所の間でネットワーク トラフィックを送信するために使用されます。 また、Azure 内で複数の仮想ネットワーク間のトラフィック送信にも使用されます (ネットワーク間)。 VPN ゲートウェイは、Azure とお使いのインフラストラクチャの間の安全なクロスプレミス接続を提供します。

詳細情報:

* [VPN ゲートウェイについて](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Azure のネットワーク セキュリティの概要](security-network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management
ユーザーは、Azure のリソース、または他の SaaS アプリケーションで、特権操作を実行することが必要になる場合があります。 通常は、組織がユーザーに Azure Active Directory (Azure AD) で永続的な特権アクセスを付与します。 

しかし、この措置では、ユーザーが特権アクセスを使用して実行している内容を組織が十分に監視できないため、クラウドでホストされているリソースのセキュリティ リスクが増大します。 また、特権アクセスを持つユーザー アカウントが侵害された場合に、その 1 つの侵害が組織のクラウド セキュリティ全体に影響を与える可能性もあります。 Azure AD Privileged Identity Management では、特権の公開期間を短縮し、使用状況の可視性を向上させることによって、このリスクの解決を支援します。  

Privileged Identity Management は、ロールまたは "ジャスト イン タイム" 管理者アクセス用の一時的管理者の概念を導入します。 この種の管理者は、割り当てられたロールのアクティブ化プロセスを完了する必要があるユーザーです。 このアクティブ化プロセスによって、Azure AD におけるユーザーへのロールの割り当てが、指定された期間だけ、非アクティブからアクティブに変更されます。

詳細情報:

* [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md)
* [Azure AD Privileged Identity Management の使用](../active-directory/privileged-identity-management/pim-getting-started.md)

## <a name="identity-protection"></a>Identity Protection
Azure AD Identity Protection は、ビジネスを保護するために、不審なサインイン アクティビティと潜在的な脆弱性の統合ビューを提供します。 Identity Protection は、次のようなシグナルに基づいて、ユーザーおよび特権 (管理者) ID に対する不審なアクティビティを検出します。

- ブルート フォース攻撃。
- 漏洩した資格情報。
- 未知の場所および感染したデバイスからのサインイン。

Identity Protection は、通知と推奨される修復を提供することで、リスクをリアルタイムで軽減できるようにします。 ユーザー リスクの重大度を計算します。 ユーザーはリスク ベースのポリシーを構成して、アプリケーションのアクセスが将来の脅威から自動的に保護されるようにすることができます。

詳細情報:

* [Azure Active Directory Identity Protection](../active-directory/active-directory-identityprotection.md)
* [Channel 9: Azure AD and Identity Show: Identity Protection Preview (Channel 9: Azure AD および Identity ショー: Identity Protection プレビュー)](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Security Center
Azure Security Center は、脅威の防御、検出、対応を可能にする機能です。 Security Center により、Azure リソースのセキュリティの可視化を向上させ、コントロールすることができます。 Azure サブスクリプション間のセキュリティ監視とポリシー管理を総合的に提供します。 Security Center は、見つけにくい脅威の検出を支援すると共に、さまざまなセキュリティ ソリューションをまとめた広範なエコシステムとして機能します。

Security Center は、Azure リソースのセキュリティの最適化と監視に役立つ次の機能を備えています。

* 以下に従って、Azure サブスクリプション リソースに対するポリシーを定義できるようにします。
  * 会社のセキュリティ ニーズ。
  * 各サブスクリプション内のアプリケーションの種類またはデータの機密度。
* Azure 仮想マシン、ネットワーク、およびアプリケーションの状態を監視します。
* 統合パートナー ソリューションからのアラートなど、優先度の高いセキュリティ アラートの一覧を提供します。 迅速に攻撃を調査するために必要な情報と、修復方法に関する推奨事項も提供します。

詳細情報:

* [Azure Security Center 入門](../security-center/security-center-intro.md)

<!--Image references-->
[1]: ./media/security-management-and-monitoring-overview/shared-responsibility.png
