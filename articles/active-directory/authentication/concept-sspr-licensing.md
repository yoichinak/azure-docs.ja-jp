---
title: Azure Active Directory セルフ サービス パスワードのライセンス
description: Azure AD のセルフ サービスによるパスワード リセットのライセンス要件
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 83054c505689768c14d168841764a4557c3e1f8b
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2018
ms.locfileid: "39159000"
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Azure AD のセルフ サービスによるパスワード リセットのライセンス要件

Azure Active Directory (Azure AD) には、Free、Basic、Premium P1、および Premium P2 の 4 つのエディションが用意されています。 セルフサービスのパスワード リセットには、その構成要素として、変更、リセット、ロック解除、書き戻しなど、Azure AD の各種エディションに用意されているいくつかの異なる機能が存在します。 この記事では、その相違点について説明します。 各 Azure AD エディションに備わっている機能の詳細は、[Azure Active Directory の価格のページ](https://azure.microsoft.com/pricing/details/active-directory/)でご覧いただけます。

## <a name="compare-editions-and-features"></a>エディションと機能の比較

組織は、そのユーザーに適切なライセンスを割り当てる必要があります。その遵守状況を維持するために、Azure AD のセルフサービスのパスワード リセットは、ユーザー単位でライセンスされます。

* クラウド ユーザーに対するセルフサービスのパスワード変更
   * 私は**クラウド限定ユーザー**で、自分のパスワードを知っています。
      * 自分のパスワードを新しいパスワードに**変更**したいと考えています。
   * この機能は、Azure AD の全エディションに備わっています。

* クラウド ユーザーに対するセルフサービスのパスワード リセット
   * 私は**クラウド限定ユーザー**で、自分のパスワードを忘れてしまいました。
      * 自分が把握しているパスワードに**リセット**したいと考えています。
   * この機能は、Azure AD Basic、Premium P1、Premium P2 の各エディションに備わっています。

* セルフサービスによるパスワードのリセット/変更/ロック解除 (**オンプレミスの書き戻しが可能**)
   * 私は**ハイブリッド ユーザー**です。オンプレミスの Active Directory ユーザー アカウントは、Azure AD Connect を使用して Azure AD アカウントと同期されています。 パスワードを変更したいと思っています/パスワードを忘れてしまいました/ロック アウトされてしまいました。
      * パスワードを変更するか、自分が把握しているパスワードにリセットするか、アカウントのロックを解除して、**なおかつ**、その変更をオンプレミスの Active Directory に同期させたいと考えています。
   * この機能は、Azure AD Premium P1 と Azure AD Premium P2 の各エディションに備わっています。

> [!WARNING]
> スタンドアロンの Office 365 ライセンス プランは、**パスワード ライトバックをサポートしていません**。この機能を動作させるには、Azure AD Premium P1 と Azure AD Premium P2 のどちらかのエディションが必要になります。
>

追加のライセンス情報 (コストを含む) については、以下のページをご覧ください。

* [Azure Active Directory の価格](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory の機能と働き](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)

## <a name="enable-group-or-user-based-licensing"></a>グループまたはユーザー ベースのライセンスの有効化

現在、AzureAD はグループベースのライセンスをサポートしています。 管理者はライセンスを一度に 1 つずつユーザーに割り当てるのではなく、ユーザーのグループに一括して割り当てることができます。 詳細については、[ライセンスの割り当て、検証、および問題の解決](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)に関するページを参照してください。

Microsoft サービスの中には、すべての場所では利用できないものもあります。 ライセンスをユーザーに割り当てる前に、管理者はユーザーの **[利用場所]** プロパティを指定しておく必要があります。 ライセンスの割り当ては、Azure Portal の **[ユーザー]** > **[プロファイル]** > **[設定]** セクションで行います。 *グループでのライセンス割り当てを使用する場合、利用場所が指定されていないユーザーは、ディレクトリの場所を継承します。*

## <a name="next-steps"></a>次の手順

* [SSPR のロールアウトを正常に完了する方法](howto-sspr-deployment.md)
* [パスワードのリセットまたは変更](../user-help/active-directory-passwords-update-your-own-password.md)
* [セルフサービスのパスワード リセットのための登録](../user-help/active-directory-passwords-reset-register.md)
* [SSPR が使用するデータと、ユーザー用に事前設定が必要なデータ](howto-sspr-authenticationdata.md)
* [ユーザーが使用できる認証方法](concept-sspr-howitworks.md#authentication-methods)
* [SSPR のポリシー オプション](concept-sspr-policy.md)
* [パスワード ライトバックの概要とその必要性](howto-sspr-writeback.md)
* [SSPR でアクティビティをレポートする方法](howto-sspr-reporting.md)
* [SSPR のすべてのオプションとその意味](concept-sspr-howitworks.md)
* [不具合が発生していると思われるSSPR のトラブルシューティング方法](active-directory-passwords-troubleshoot.md)
* [質問したい内容に関する説明がどこにもない。](active-directory-passwords-faq.md)
