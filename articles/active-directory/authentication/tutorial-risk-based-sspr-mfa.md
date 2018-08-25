---
title: リスクベースの MFA および SSPR と Azure Identity Protection
description: このチュートリアルでは、Multi-Factor Authentication とセルフサービスによるパスワードのリセットに対して Azure Identity Protection 統合を有効にして、危険な動作を減らします。
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: fb9ec69476253eaa559fe763dcc2c92994505602
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2018
ms.locfileid: "39163315"
---
# <a name="tutorial-use-risk-events-to-trigger-multi-factor-authentication-and-password-changes"></a>チュートリアル: リスク イベントを使用して Multi-Factor Authentication とパスワードの変更をトリガーする

このチュートリアルでは、Azure Active Directory (Azure AD) Identity Protection の機能を有効にします。この Azure AD Premium P2 機能は、単なる監視およびレポート ツール以上のものです。 組織の ID を保護するために、危険な動作に自動的に対処するリスクベースのポリシーを構成することができます。 これらのポリシーは、自動的にブロックしたり、パスワードの変更の要求や Multi-Factor Authentication の強制などの修復を開始したりすることができます。

Azure AD Identity Protection ポリシーは、既存の条件付きアクセス ポリシーに加えて、追加の保護レイヤーとして使用することができます。 ユーザーがこれらのポリシーの 1 つを必要とする危険な動作をトリガーすることはないかもしれませんが、管理者はユーザーが保護されているとわかっています。

リスク イベントをトリガーする可能性があるのは、次のような操作です。

* 資格情報が漏洩したユーザー
* 匿名の IP アドレスからのサインイン
* 特殊な場所へのあり得ない移動
* 感染しているデバイスからのサインイン
* 不審なアクティビティのある IP アドレスからのサインイン
* 未知の場所からのサインイン

Azure AD Identity Protection の詳細については、[Azure AD Identity Protection の概要](../active-directory-identityprotection.md)に関する記事を参照してください。

> [!div class="checklist"]
> * Azure MFA への登録を有効にする
> * リスクベースのパスワードの変更を有効にする
> * リスクベースの Multi-Factor Authentication を有効にする

## <a name="prerequisites"></a>前提条件

* 少なくとも試用版 Azure AD Premium P2 ライセンスが割り当てられた、動作している Azure AD テナントへのアクセス。
* Azure AD テナントで全体管理者特権を持つアカウント。
* 前のセルフサービスによるパスワードのリセット (SSPR) と Multi-Factor Authentication (MFA) のチュートリアルを完了していること。

## <a name="enable-risk-based-policies-for-sspr-and-mfa"></a>SSPR と MFA に対してリスクベースのポリシーを有効にする

リスクベースのポリシーの有効化は、簡単なプロセスです。 次の手順では、サンプルの構成について説明します。

### <a name="enable-users-to-register-for-multi-factor-authentication"></a>ユーザーが Multi-Factor Authentication に登録できるようにする

Azure AD Identity Protection には、ユーザーを Multi-Factor Authentication に登録させ、現在の登録状況を簡単に識別できるようにする、既定のポリシーが含まれています。 このポリシーを有効にしても、ユーザーが Multi-Factor Authentication の実行を要求されるようにはなりませんが、事前登録を求められます。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. **[すべてのサービス]** をクリックし、**[Azure AD Identity Protection]** に移動します。
1. **[MFA 登録]** をクリックします。
1. [ポリシーの適用] を **[オン]** に設定します。
   1. このポリシーを設定する場合は、Multi-Factor Authentication を使用するための準備方法をすべてのユーザーが登録する必要があります。
1. **[Save]** をクリックします。

   ![Azure AD Identity Protection を使用してサインイン時に MFA に登録するようにユーザーに要求する](./media/tutorial-risk-based-sspr-mfa/risk-based-require-mfa-registration.png)

### <a name="enable-risk-based-password-changes"></a>リスクベースのパスワードの変更を有効にする

Microsoft では、研究者、法執行機関、Microsoft のさまざまなセキュリティ チーム、その他の信頼できる情報源と協力して、ユーザー名とパスワードのペアを調査しています。 それらのペアのいずれかが環境内のアカウントに一致すると、次のポリシーを使用して、リスクベースのパスワード変更がトリガーされます。

1. [ユーザーのリスク ポリシー] をクリックします。
1. **[条件]** の下の **[ユーザーのリスク]** を選択し、**[中以上]** を選択します。
1. [選択]、[完了] の順にクリックします。
1. **[アクセス]** の下の **[アクセスを許可]** を選択し、**[パスワードの変更を必須とする]** を選択します。
1. [選択] をクリックします。
1. [ポリシーの適用] を **[オン]** に設定します。
1. **[保存]**

### <a name="enable-risk-based-multi-factor-authentication"></a>リスクベースの Multi-Factor Authentication を有効にする

ほとんどのユーザーは、追跡できる正常な動作をしています。この規範から外れた場合は、そのユーザーにサインインを許可すると危険であることがあります。 そのユーザーをブロックしたり、Multi-Factor Authentication を実行してユーザーが本人であることを証明するように求めたりすることが必要な場合もあります。 危険なサインインが検出されたときに MFA を必要とするポリシーを有効にするには、次のポリシーを有効にします。

1. [サインインのリスク ポリシー] をクリックします
1. **[条件]** の下の **[ユーザーのリスク]** を選択し、**[中以上]** を選択します。
1. [選択]、[完了] の順にクリックします。
1. **[アクセス]** の下の **[アクセスを許可]** を選択し、**[多要素認証を要求する]** を選択します。
1. [選択] をクリックします。
1. [ポリシーの適用] を **[オン]** に設定します。
1. **[保存]**

## <a name="clean-up-resources"></a>リソースのクリーンアップ

テストを完了し、リスクベースのポリシーを有効にする必要がなくなった場合は、無効にする各ポリシーに戻り、**[ポリシーの適用]** を **[オフ]** に設定します。