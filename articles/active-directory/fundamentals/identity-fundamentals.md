---
title: Azure ID 管理の基礎 | Microsoft Docs
description: ユーザーが企業のアプリケーションやデータにいつ、どのようにアクセスしたかを制御し可視化する最良の方法は、現時点ではクラウド ベース識別です。
keywords: ''
author: eross-msft
manager: mtillman
ms.reviewer: jsnow
ms.author: lizross
ms.date: 08/07/2018
ms.topic: overview
ms.prod: ''
ms.service: active-directory
ms.component: fundamentals
ms.technology: ''
ms.assetid: ''
ms.custom: it-pro
ms.openlocfilehash: 327cecd129befb56c33d7fcf2d59ee5b58a18549
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2018
ms.locfileid: "42022420"
---
# <a name="fundamentals-of-azure-identity-management"></a>Azure ID 管理の基礎

企業のデジタル リソースは、企業ネットワーク外部のクラウドやデバイスに置かれるものがますます増え、クラウド ベースの優れた ID とアクセス管理ソリューションが不可欠になっています。 ユーザーが企業のアプリケーションやデータにいつ、どのようにアクセスしたかを制御し可視化する最良の方法は、現時点ではクラウド ベース識別です。

Microsoft は、10 年以上にわたってクラウド ベースの ID を保護してきました。そして現在、[Azure Active Directory (AD)](active-directory-whatis.md) によって、同じ保護システムをお客様にご利用いただけるようになりました。 Azure AD を使用すると、エンタープライズ管理者はこれまでにない優れたセキュリティとガバナンスで、ユーザーと管理者の説明責任を簡単に確保できます。

Azure AD Premium は、高度な保護機能を備えた、クラウド ベースの ID およびアクセス管理ソリューションです。これにより、すべてのアプリに対する 1 つの安全な ID、([Microsoft インテリジェンス セキュリティ グラフ](https://www.microsoft.com/security/intelligence)によって強化された) ID 保護、Privileged Identity Management が実現します。 Azure AD Premium は、よくある監視およびレポート ツールではなく、ユーザーの ID をリアルタイムで保護したり、組織のデータを保護するためにリスク ベースで適応性の高いアクセス ポリシーを作成できるようにしたりできます。

Azure AD の ID 管理および保護の概要については、この簡単なビデオをご覧ください。
>[!VIDEO https://www.youtube.com/embed/9LGIJ2-FKIM]

Microsoft は、どこでも使用できる ID だけでなく、組織の IT の自動化、セキュリティ支援、管理のための一連のツールも提供しています。 クラウド コンピューティングの登場後も、ヘルプデスクへのユーザー パスワードのリセット依頼、ユーザー グループ管理、アプリケーションの要求など、IT タスクの管理と制御の需要はまだ存在します。 さらに複雑なことに、従業員は個人のデバイスを職場に持ち込み、利用できる状態の SaaS アプリケーションを使用しています。そのため、企業のデータセンターやパブリック クラウド プラットフォーム全体のアプリケーションに対する制御を維持することが非常に困難になっています。

[!INCLUDE [identity](../../../includes/azure-ad-licenses.md)]

## <a name="connect-on-premises-active-directory-with-azure-ad-and-office-365"></a>オンプレミスの Active Directory を Azure AD と Office 365 に接続する
オンプレミスの Active Directory に大規模な投資を行った組織は、オンプレミスのディレクトリと Azure AD を[ハイブリッド ID 管理](https://aka.ms/aadframework)に統合することによって、その投資をクラウドに拡張することができます。 こうすると、場所に関係なくリソースにアクセスするための共通 ID を提供できるため、ユーザーの生産性が向上します。 ユーザーと組織は、シングル サインオン (SSO) を使用して、オンプレミス リソースとクラウド サービス (Office 365 など) の両方にアクセスできます。

[Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) は、統合の実施に必要な唯一のツールです。 Azure AD Connect は、ID 同期のニーズをサポートするための機能を備えた、DirSync や Azure AD Sync などの古いバージョンの ID 統合ツールに代わる機能です。Azure AD Connect を使用すると、次の機能によってオンプレミスと Azure AD 間の ID 管理と同期が可能になります。

- 同期 - ユーザーやグループなどのオブジェクトを作成するためのコンポーネントです。 さらに、オンプレミス ユーザーやオンプレミス グループの ID 情報をクラウド側と一致させる働きをします。 ユーザーが Azure AD でパスワードを更新したときにオンプレミスのディレクトリを同期した状態にしておくために、パスワード ライトバックも有効にすることができます。
- 認証 - 新しいコントロール プレーンとして Azure AD を採用する場合、認証がクラウド アクセスの基盤です。 正しい認証方法の選択は、Azure AD ハイブリッド ID ソリューションのセットアップにおける重要な決定です。 クラウド認証 (パスワード ハッシュ同期/パススルー認証) とフェデレーション認証 (AD FS) のどちらを社内で採用するかについては、[こちらのガイド](https://aka.ms/auth-options)を参考にしてください。
- 正常性の監視 - [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health) により、厳重に監視し、Azure Portal でこのアクティビティを一元的に表示できます。

## <a name="increase-productivity-and-reduce-helpdesk-costs-with-self-service-and-single-sign-on-experiences"></a>セルフサービスとシングル サインオン エクスペリエンスで生産性の向上とヘルプデスクのコスト削減を実現する

従業員は、記憶するユーザー名とパスワードが 1 つで、すべてのデバイスからのエクスペリエンスが一貫している場合に生産性が向上します。 また、[忘れたパスワードのリセット](https://docs.microsoft.com/azure/active-directory/active-directory-passwords)、アプリケーションへのアクセスの要求などのタスクを、ヘルプデスクの支援を待たずにセルフサービスで実行できると、時間の節約にもなります。

Azure AD は[オンプレミスの Active Directory をクラウドに拡張](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)するため、ユーザーは、ドメイン参加デバイスと会社のリソースにも、業務で使用する必要があるすべての Web アプリケーションと SaaS アプリケーションにもプライマリ組織アカウントを使用できます。 複数のユーザー名とパスワードのセットを覚える必要がなくなるだけでなく、組織のグループ メンバーシップや従業員としての地位に基づいて、ユーザーのアプリケーション アクセスも自動的にプロビジョニング (またはプロビジョニング解除) されるようにすることができます。 また、ギャラリー アプリ、または [Azure AD アプリケーション プロキシ](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started)で開発および公開した独自のオンプレミス アプリについてそのアクセスを制御できます。

## <a name="manage-and-control-access-to-corporate-resources"></a>会社のリソースへのアクセスを管理および制御する
Microsoft ID およびアクセス管理ソリューションは、IT が企業のデータ センター全体とクラウドのアプリケーションとリソースへのアクセスを保護するのに役立ち、[他要素認証](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next)や[条件付きアクセス ポリシー](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)などの追加レベルの検証を可能にします。 高度なセキュリティ報告、監査、および警告によって疑わしいアクティビティを監視し、潜在的なセキュリティ上の問題を軽減できます。

Azure AD Premium の条件付きアクセス ポリシーを使用すると、エンタープライズ管理者は、Azure AD に接続されたどんなアプリケーション (SaaS アプリ、クラウドで実行されているカスタム アプリ、またはオンプレミス Web アプリケーション) に対しても、ポリシー ベースのアクセス規則を作成できます。 Azure AD はこれらのポリシーをリアルタイムで評価し、ユーザーがアプリケーションにアクセスしようとするたびにポリシーを適用します。 Azure ID 保護ポリシーを使用すると、疑わしいアクティビティが検出された場合に自動的に対処できます。 たとえば、リスクの高いユーザーへのアクセスのブロック、多要素認証の適用、資格情報が侵害されたと思われる場合のユーザー パスワードのリセットなどです。


## <a name="azure-active-directory-privileged-identity-management"></a>Azure Active Directory Privileged Identity Management

Azure Active Directory Premium P2 契約に含まれている [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) を使用すると、管理者アカウントと、Azure Active Directory およびその他の Microsoft オンライン サービス内のリソースへのアクセスを検出、制限、および監視することができます。 また、必要とされる正確な期間にわたって、オンデマンドの管理アクセスを管理するためにも役立ちます。

Privileged Identity Management は、管理者が多要素認証される一時的な特権の昇格を要求できるように、オンデマンドで管理者権限を適用できます。そのアカウントが通常のユーザーの状態に戻るまでの期間は、事前に構成できます。

## <a name="benefits-of-azure-identity"></a>Azure ID の利点

Azure の ID 管理では、次のことが可能です。

-   エンタープライズ全体でユーザーごとに 1 つの ID を作成して管理し、ユーザー、グループ、デバイスが [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) と同期された状態を維持する。

-   何千もの事前統合された SaaS アプリを含むアプリケーションにシングル サインオン アクセスを提供したり、[Azure AD アプリケーション プロキシ](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started)を使用してオンプレミス SaaS アプリケーションへのセキュリティで保護されたリモート アクセスを提供したりする。

-   ルール ベースの[多要素認証](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next)をオンプレミス アプリケーションとクラウド アプリケーションの両方に適用することによって、アプリケーション アクセスのセキュリティを有効にする。

-   [セルフサービスによるパスワードのリセット](https://docs.microsoft.com/azure/active-directory/active-directory-passwords)、[MyApps ポータル](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-user-help)を使用したグループおよびアプリケーション アクセス要求によって、ユーザーの生産性を向上させる。

-   世界規模でエンタープライズ レベルでクラウド ベースの ID およびアクセス管理ソリューションの[高可用性および信頼性](https://docs.microsoft.com/azure/architecture/resiliency/high-availability-azure-applications)の利点を活用する。

## <a name="next-steps"></a>次の手順
[Azure ID ソリューションの詳細を確認する](https://docs.microsoft.com/azure/active-directory/understand-azure-identity-solutions)
