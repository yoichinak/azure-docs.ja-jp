---
title: Microsoft アプリケーションへのサインインに関する問題 | Microsoft Docs
description: Azure AD を使用したファースト パーティの Microsoft アプリケーション (Office 365 など) にサインインする際に直面する一般的な問題をトラブルシューティングする
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: fdc368c052e57d93b15e511bdce0b2baccb3aa84
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364077"
---
## <a name="problems-signing-in-to-a-microsoft-application"></a>Microsoft アプリケーションへのサインインに関する問題

Microsoft アプリケーション (Office 365 Exchange、SharePoint、Yammer など) の割り当てと管理は、サード パーティの SaaS アプリケーションや、シングル サインオンで Azure AD と統合する他のアプリケーションとは少し異なる方法で行います。

Microsoft が公開したアプリケーションにユーザーがアクセスする方法は、主に 3 つあります。

-   Office 365 またはその他の有料のスイートのアプリケーションでは、**ライセンスの割り当て**によってユーザーにアクセス権が付与されます。ライセンスの割り当ては、ユーザー アカウントに直接、またはグループ ベースのライセンス割り当て機能を使用してグループを通じて行われます。

-   Microsoft またはサード パーティが誰でも使用できるように無料で公開するアプリケーションでは、**ユーザーの同意**によってユーザーにアクセス権が付与されます。 つまり、ユーザーは自分の Azure AD Work または School アカウントでアプリケーションにサインインし、そのアカウントの限定されたデータ セットにアクセスすることが許可されます。

-   Microsoft またはサード パーティが誰でも使用できるよう無料で公開するアプリケーションでは、**管理者の同意**によってユーザーにアクセス権を付与することもできます。 つまり、管理者は組織の全員がそのアプリケーションを使用する可能性があると判断しているため、グローバル管理者アカウントを使用してアプリケーションにサインインし、組織の全員にアクセス権を付与します。

問題をトラブルシューティングする方法の詳細については、最初に「[アプリケーション アクセスに関して考慮すべき一般的問題](#general-problem-areas-with-application-access-to-consider)」を参照してから、「[チュートリアル: Microsoft アプリケーション アクセスをトラブルシューティングする手順](#walkthrough-steps-to-troubleshoot-microsoft-application-access)」を参照してください。

## <a name="general-problem-areas-with-application-access-to-consider"></a>アプリケーション アクセスに関して考慮すべき一般的問題

一般的な問題領域の一覧を次に示します。どこから始めるべきかわかっている場合は、そこから詳細をご覧ください。ただし、お急ぎの場合は「[チュートリアル: Microsoft アプリケーション アクセスをトラブルシューティングする手順](#walkthrough-steps-to-troubleshoot-microsoft-application-access)」を参照してください。

-   [ユーザーのアカウントに関する問題](#problems-with-the-users-account)

-   [グループに関する問題](#problems-with-groups)

-   [条件付きアクセス ポリシーに関する問題](#problems-with-conditional-access-policies)

-   [アプリケーションの承認に関する問題](#problems-with-application-consent)

## <a name="steps-to-troubleshoot-microsoft-application-access"></a>Microsoft アプリケーション アクセスをトラブルシューティングする手順

ユーザーが Microsoft アプリケーションにサインインできないときによくある問題を次に示します。

-   最初にチェックすべき一般的な問題

  * ユーザーが**正しい URL** にサインインしていて、ローカル アプリケーション URL にサインインしていないことを確認します。

  * ユーザーのアカウントが**ロックアウトされていない**ことを確認します。

  * Azure Active Directory に**ユーザーのアカウントが存在する**ことを確認します。 [ユーザー アカウントが Azure Active Directory に存在するかどうかを調べる](#problems-with-the-users-account)

  * ユーザーのアカウントがサインイン用に**有効になっている**ことを確認します。[ユーザーのアカウントの状態を確認する](#problems-with-the-users-account)

  * ユーザーの**パスワードが期限切れになったり、忘れられたりしていない**ことを確認します。 [ユーザーのパスワードをリセットする](#reset-a-users-password)か、[セルフサービスのパスワード リセットを有効にします](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

   * **多要素認証**がユーザー アクセスをブロックしていないことを確認します。 [ユーザーの多要素認証の状態を確認する](#check-a-users-multi-factor-authentication-status)か、[ユーザーの認証の連絡先情報を確認します](#check-a-users-authentication-contact-info)

   * **条件付きアクセス ポリシー**または **ID 保護**ポリシーがユーザー アクセスをブロックしていないことを確認します。 [特定の条件付きアクセス ポリシーを確認する](#problems-with-conditional-access-policies)か、[特定アプリケーションの条件付きアクセス ポリシーを確認する](#check-a-specific-applications-conditional-access-policy)か、[特定の条件付きアクセス ポリシーを無効にします](#disable-a-specific-conditional-access-policy)

   * ユーザーの**認証の連絡先情報**が最新のものであり、多要素認証または条件付きアクセス ポリシーを適用できることを確認します。 [ユーザーの多要素認証の状態を確認する](#check-a-users-multi-factor-authentication-status)か、[ユーザーの認証の連絡先情報を確認します](#check-a-users-authentication-contact-info)

-   ライセンスを必要とする **Microsoft** **アプリケーション** (Office365 など) で、上記の一般的な問題に該当しない場合に調べるいくつかの具体的問題を以下に示します。

   * ユーザーに**ライセンスが割り当てられている**ことを確認します。 [ユーザーに割り当てられているライセンスを確認する](#check-a-users-assigned-licenses)か、[グループに割り当てられているライセンスを確認](#check-a-groups-assigned-licenses)します。

   * ライセンスが**静的グループ****に割り当てられている**場合は、ユーザーがそのグループの**メンバー**であることを確認します。 [ユーザーのグループ メンバーシップを確認する](#check-a-users-group-memberships)

   * ライセンスが**動的グループ****に割り当てられている**場合は、**動的グループ ルールが正しく設定されている**ことを確認します。 [動的グループのメンバーシップ条件を確認する](#check-a-dynamic-groups-membership-criteria)

   * ライセンスが**動的グループ****に割り当てられている**場合は、動的グループがそのメンバーシップの**処理を完了済み**であることと、**ユーザーがメンバーである**ことを確認します (これには時間が多少かかる場合があります)。 [ユーザーのグループ メンバーシップを確認する](#check-a-users-group-memberships)

   *  ライセンスが割り当てられていることを確認したら、ライセンスの**期限が切れていない**ことを確認します。

   *  ライセンスが、アクセスしようとしている**アプリケーション**のものであることを確認します。

-   ライセンスを必要としない **Microsoft** **アプリケーション**の場合に調べるべきその他の点を、次に示します。

   * アプリケーションが**ユーザーレベルのアクセス許可** (たとえば「このユーザーのメールボックスにアクセスする」) を要求している場合は、そのユーザーがアプリケーションにサインインしていて、**ユーザーレベルの同意操作**を実行して自分のデータにアプリケーションがアクセスできるようにしていることを確認します。

   * アプリケーションが**管理者レベルのアクセス許可** (たとえば「すべてのユーザーのメールボックスにアクセスする」) を要求している場合は、グローバル管理者が組織の**すべてのユーザーを代表して管理者レベルの同意操作**を行っていることを確認します。

## <a name="problems-with-the-users-account"></a>ユーザーのアカウントに関する問題

アプリケーションへのアクセスが、そのアプリケーションに割り当てられているユーザーの問題によりブロックされる場合があります。 ユーザーとそのアカウントの設定に関する問題をトラブルシューティングして解決するための方法を次に示します。

-   [ユーザー アカウントが Azure Active Directory に存在するかどうかを調べる](#check-if-a-user-account-exists-in-azure-active-directory)

-   [ユーザーのアカウントの状態を確認する](#check-a-users-account-status)

-   [ユーザーのパスワードをリセットする](#reset-a-users-password)

-   [セルフ サービスのパスワード リセットを有効にする](#enable-self-service-password-reset)

-   [ユーザーの多要素認証の状態を確認する](#check-a-users-multi-factor-authentication-status)

-   [ユーザーの認証の連絡先情報を確認する](#check-a-users-authentication-contact-info)

-   [ユーザーのグループ メンバーシップを確認する](#check-a-users-group-memberships)

-   [ユーザーに割り当てられているライセンスを確認する](#check-a-users-assigned-licenses)

-   [ユーザーにライセンスを割り当てる](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>ユーザー アカウントが Azure Active Directory に存在するかどうかを調べる

ユーザーのアカウントが存在するかどうかを確認するには、次の手順に従います。

1.  [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**としてサインインします。

2.  左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4.  ナビゲーション メニューで **[ユーザーとグループ]** をクリックします。

5.  **[すべてのユーザー]** をクリックします。

6.  対象のユーザーを**検索**し、**行をクリック**して選択します。

7.  ユーザー オブジェクトのプロパティを調べて、予想した通りに表示されていることと、紛失したデータがないことを確認します。

### <a name="check-a-users-account-status"></a>ユーザーのアカウントの状態を確認する

ユーザーのアカウントの状態を確認するには、次の手順に従います。

1.  [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**としてサインインします。

2.  左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4.  ナビゲーション メニューで **[ユーザーとグループ]** をクリックします。

5.  **[すべてのユーザー]** をクリックします。

6.  対象のユーザーを**検索**し、**行をクリック**して選択します。

7.  **[Profile]** をクリックします。

8.  **[設定]** で、**[サインインのブロック]** が **[いいえ]** に設定されていることを確認します。

### <a name="reset-a-users-password"></a>ユーザーのパスワードをリセットする

ユーザーのパスワードをリセットするには、次の手順に従います。

1.  [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**としてサインインします。

2.  左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4.  ナビゲーション メニューで **[ユーザーとグループ]** をクリックします。

5.  **[すべてのユーザー]** をクリックします。

6.  対象のユーザーを**検索**し、**行をクリック**して選択します。

7.  ユーザー ウィンドウの上部にある **[パスワードのリセット]** をクリックします。

8.  表示される **[パスワードのリセット]** ウィンドウ上の **[パスワードのリセット]** をクリックします。

9.  **一時パスワード**をコピーするか、ユーザーの**新しいパスワードを入力**します。

10. この新しいパスワードをユーザーに知らせます。ユーザーは次の Azure Active Directory サインイン時にこのパスワードを変更する必要があります。

### <a name="enable-self-service-password-reset"></a>セルフ サービスのパスワード リセットを有効にする

セルフ サービスのパスワード リセットを有効にするには、次のデプロイ手順を実行します。

-   [Azure Active Directory パスワードをユーザーがリセットできるようにする](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started#enable-users-to-reset-their-azure-ad-passwords)

-   [オンプレミス Active Directory パスワードをユーザーがリセットまたは変更できるようにする](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords)

### <a name="check-a-users-multi-factor-authentication-status"></a>ユーザーの多要素認証の状態を確認する

ユーザーの多要素認証の状態を確認するには、次の手順に従います。

1.  [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**としてサインインします。

2.  左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4.  ナビゲーション メニューで **[ユーザーとグループ]** をクリックします。

5.  **[すべてのユーザー]** をクリックします。

6.  ウィンドウの上部にある **[多要素認証]** をクリックします。

7.  **多要素認証管理ポータル**が読み込まれたら、自分が **[ユーザー]** タブに存在していることを確認します。

8.  ユーザーの一覧で、検索、フィルター処理、または並べ替えによってユーザーを見つけます。

9.  ユーザーの一覧からユーザーを選択し、多要素認証を必要に応じて**有効**、**無効**、または**適用**します。

  * **注**: ユーザーが**適用**状態の場合は、一時的に**無効**に設定してユーザーが自分のアカウントに戻れるようにできます。 ユーザーがアカウントに戻ったら、ユーザーの状態を再度**有効**に変更して、ユーザーの連絡先情報を次のサインイン時に再登録するように要求します。 あるいは、[ユーザーの認証の連絡先情報を確認する](#check-a-users-authentication-contact-info)の手順に従って、このユーザーのデータを検証または設定することができます。

### <a name="check-a-users-authentication-contact-info"></a>ユーザーの認証の連絡先情報を確認する

多要素認証、条件付きアクセス、ID 保護、およびパスワード リセットで使用されるユーザーの認証の連絡先情報を確認するには、次の手順に従います。

1.  [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**としてサインインします。

2.  左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4.  ナビゲーション メニューで **[ユーザーとグループ]** をクリックします。

5.  **[すべてのユーザー]** をクリックします。

6.  対象のユーザーを**検索**し、**行をクリック**して選択します。

7.  **[Profile]** をクリックします。

8.  下の **[認証の連絡先情報]** までスクロールします。

9.  登録されたユーザーのデータを**レビュー**して、必要に応じて更新します。

### <a name="check-a-users-group-memberships"></a>ユーザーのグループ メンバーシップを確認する

ユーザーのグループ メンバーシップを確認するには、次の手順に従います。

1.  [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**としてサインインします。

2.  左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4.  ナビゲーション メニューで **[ユーザーとグループ]** をクリックします。

5.  **[すべてのユーザー]** をクリックします。

6.  対象のユーザーを**検索**し、**行をクリック**して選択します。

7.  **[グループ]** をクリックすると、ユーザーがメンバーになっているグループが表示されます。

### <a name="check-a-users-assigned-licenses"></a>ユーザーに割り当てられているライセンスを確認する

ユーザーに割り当てられているライセンスを確認するには、次の手順に従います。

1.  [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**としてサインインします。

2.  左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4.  ナビゲーション メニューで **[ユーザーとグループ]** をクリックします。

5.  **[すべてのユーザー]** をクリックします。

6.  対象のユーザーを**検索**し、**行をクリック**して選択します。

7.  **[ライセンス]** をクリックして、ユーザーに現在割り当てられているライセンスを確認します。

### <a name="assign-a-user-a-license"></a>ユーザーにライセンスを割り当てる 

ユーザーにライセンスを割り当てるには、次の手順に従います。

1.  [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**としてサインインします。

2.  左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4.  ナビゲーション メニューで **[ユーザーとグループ]** をクリックします。

5.  **[すべてのユーザー]** をクリックします。

6.  対象のユーザーを**検索**し、**行をクリック**して選択します。

7.  **[ライセンス]** をクリックして、ユーザーが現在割り当てられているライセンスを確認します。

8.  **[割り当て]** ボタンをクリックします。

9.  **1 つまたは複数の製品**を利用可能な製品の一覧から選択します。

10. **省略可能** より細かい単位で製品を割り当てるには、**割り当てオプション**項目をクリックします。 完了したら **[OK]** をクリックします。

11. **[割り当て]** ボタンをクリックしてこれらのライセンスをこのユーザーに割り当てます。

## <a name="problems-with-groups"></a>グループに関する問題

アプリケーションへのアクセスが、そのアプリケーションに割り当てられているグループの問題によりブロックされる場合があります。 グループおよびグループ メンバーシップに関する問題をトラブルシューティングして解決するための方法を次に示します。

-   [グループのメンバーシップを確認する](#check-a-groups-membership)

-   [動的グループのメンバーシップ条件を確認する](#check-a-dynamic-groups-membership-criteria)

-   [グループに割り当てられているライセンスを確認する](#check-a-groups-assigned-licenses)

-   [グループのライセンスを再処理する](#reprocess-a-groups-licenses)

-   [グループにライセンスを割り当てる](#assign-a-group-a-license)

### <a name="check-a-groups-membership"></a>グループのメンバーシップを確認する

グループのメンバーシップを確認するには、次の手順に従います。

1.  [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**としてサインインします。

2.  左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4.  ナビゲーション メニューで **[ユーザーとグループ]** をクリックします。

5.  **[すべてのグループ]** をクリックします。

6.  対象のグループを**検索**し、**行をクリック**して選択します。

7.  **[メンバー]** をクリックしてこのグループに割り当てられているユーザーの一覧を確認します。

### <a name="check-a-dynamic-groups-membership-criteria"></a>動的グループのメンバーシップ条件を確認する 

動的グループのメンバーシップ条件を確認するには、次の手順に従います。

1.  [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**としてサインインします。

2.  左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4.  ナビゲーション メニューで **[ユーザーとグループ]** をクリックします。

5.  **[すべてのグループ]** をクリックします。

6.  対象のグループを**検索**し、**行をクリック**して選択します。

7.  **[動的メンバーシップ ルール]** をクリックします。

8.  このグループに定義された**簡易**または**詳細**ルールを調べて、このグループのメンバーにしたいユーザーがこれらの条件を満たしていることを確認します。

### <a name="check-a-groups-assigned-licenses"></a>グループに割り当てられているライセンスを確認する

グループに割り当てられているライセンスを確認するには、次の手順に従います。

1.  [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**としてサインインします。

2.  左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4.  ナビゲーション メニューで **[ユーザーとグループ]** をクリックします。

5.  **[すべてのグループ]** をクリックします。

6.  対象のグループを**検索**し、**行をクリック**して選択します。

7.  **[ライセンス]** をクリックして、グループに現在割り当てられているライセンスを確認します。

### <a name="reprocess-a-groups-licenses"></a>グループのライセンスを再処理する

グループに割り当てられているライセンスを再処理するには、次の手順に従います。

1.  [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**としてサインインします。

2.  左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4.  ナビゲーション メニューで **[ユーザーとグループ]** をクリックします。

5.  **[すべてのグループ]** をクリックします。

6.  対象のグループを**検索**し、**行をクリック**して選択します。

7.  **[ライセンス]** をクリックして、グループに現在割り当てられているライセンスを確認します。

8.  **[再処理]** ボタンをクリックして、このグループのメンバーに割り当てられているライセンスが最新であることを確認します。 このとき、グループの大きさと複雑さによっては、時間がかかる場合があります。

   >[!NOTE]
   >もっと速くするには、一時的に直接ユーザーにライセンスを割り当てることを検討してください。 [ユーザーにライセンスを割り当てます](#problems-with-application-consent)。
   >
   >

### <a name="assign-a-group-a-license"></a>グループにライセンスを割り当てる

グループにライセンスを割り当てるには、次の手順に従います。

1.  [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**としてサインインします。

2.  左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4.  ナビゲーション メニューで **[ユーザーとグループ]** をクリックします。

5.  **[すべてのグループ]** をクリックします。

6.  対象のグループを**検索**し、**行をクリック**して選択します。

7.  **[ライセンス]** をクリックして、グループに現在割り当てられているライセンスを確認します。

8.  **[割り当て]** ボタンをクリックします。

9.  **1 つまたは複数の製品**を利用可能な製品の一覧から選択します。

10. **省略可能** より細かい単位で製品を割り当てるには、**割り当てオプション**項目をクリックします。 完了したら **[OK]** をクリックします。

11. **[割り当て]** ボタンをクリックして、これらのライセンスをこのグループに割り当てます。 このとき、グループの大きさと複雑さによっては、時間がかかる場合があります。

   >[!NOTE]
   >もっと速くするには、一時的に直接ユーザーにライセンスを割り当てることを検討してください。 [ユーザーにライセンスを割り当てます](#problems-with-application-consent)。
   > 
   >

## <a name="problems-with-conditional-access-policies"></a>条件付きアクセス ポリシーに関する問題

### <a name="check-a-specific-conditional-access-policy"></a>特定の条件付きアクセス ポリシーを確認する

1 つの条件付きアクセス ポリシーを確認または検証する方法

1.  [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**としてサインインします。

2.  左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4.  ナビゲーション メニューで **[エンタープライズ アプリケーション]** をクリックします。

5.  **[条件付きアクセス]** ナビゲーション項目をクリックします。

6.  検査の対象とするポリシーをクリックします。

7.  特定の条件、割り当て、またはユーザーのアクセスをブロックしてしまう他の設定がないことを確認します。

   >[!NOTE]
   >このポリシーを一時的に無効にしてサインインに影響していないことを確認できます。そのためには、**[ポリシーの有効化]** を **[いいえ]** に設定して **[保存]** ボタンをクリックします。
   >
   >

### <a name="check-a-specific-applications-conditional-access-policy"></a>特定のアプリケーションの条件付きアクセス ポリシーを確認する

1 つのアプリケーションで現在構成されている条件付きアクセス ポリシーを確認または検証する方法

1.  [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**としてサインインします。

2.  左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4.  ナビゲーション メニューで **[エンタープライズ アプリケーション]** をクリックします。

5.  **[すべてのアプリケーション]** をクリックします。

6.  対象のアプリケーション、またはユーザーがサインインしようとしているアプリケーションを、アプリケーション表示名またはアプリケーション ID で検索します。

     >[!NOTE]
     >探しているアプリケーションが見つからない場合は、**[フィルター]** ボタンをクリックして、リストの範囲を**すべてのアプリケーション**に広げます。 もっと多くの列を表示したい場合は、**[列]** ボタンをクリックして、アプリケーションについてのその他の詳細を追加します。
     >
     >

7.  **[条件付きアクセス]** ナビゲーション項目をクリックします。

8.  検査の対象とするポリシーをクリックします。

9.  特定の条件、割り当て、またはユーザーのアクセスをブロックしてしまう他の設定がないことを確認します。

     >[!NOTE]
     >このポリシーを一時的に無効にしてサインインに影響していないことを確認できます。そのためには、**[ポリシーの有効化]** を **[いいえ]** に設定して **[保存]** ボタンをクリックします。
     >
     >

### <a name="disable-a-specific-conditional-access-policy"></a>特定の条件付きアクセス ポリシーを無効にする

1 つの条件付きアクセス ポリシーを確認または検証する方法

1.  [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**としてサインインします。

2.  左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4.  ナビゲーション メニューで **[エンタープライズ アプリケーション]** をクリックします。

5.  **[条件付きアクセス]** ナビゲーション項目をクリックします。

6.  検査の対象とするポリシーをクリックします。

7.  ポリシーを無効にするには、**[ポリシーの有効化]** を **[いいえ]** に設定して **[保存]** ボタンをクリックします。

## <a name="problems-with-application-consent"></a>アプリケーションの承認に関する問題

適切なアクセス許可の承認操作が行われていないために、アプリケーションへのアクセスがブロックされる場合があります。 アプリケーションの承認の問題をトラブルシューティングして解決するための方法を次に示します。

-   [ユーザー レベルの同意操作を実行する](#perform-a-user-level-consent-operation)

-   [任意のアプリケーションで管理者レベルの同意操作を実行する](#perform-administrator-level-consent-operation-for-any-application)

-   [シングルテナント アプリケーションで管理者レベルの同意を実行する](#perform-administrator-level-consent-for-a-single-tenant-application)

-   [マルチテナント アプリケーションで管理者レベルの同意を実行する](#perform-administrator-level-consent-for-a-multi-tenant-application)

### <a name="perform-a-user-level-consent-operation"></a>ユーザー レベルの同意操作を実行する

-   アクセス許可を要求する任意の Open ID 接続対応アプリケーションで、アプリケーションのサインイン画面に移動して、サインインしているユーザーのアプリケーションに対するユーザー レベルの同意を実行します。

-   これをプログラムで実行したい場合は、[個々のユーザーの同意を要求する](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#requesting-individual-user-consent)を参照してください。

### <a name="perform-administrator-level-consent-operation-for-any-application"></a>任意のアプリケーションで管理者レベルの同意操作を実行する

-   **V1 アプリケーション モデルを使用して開発されたアプリケーション**の場合のみ、アプリケーションのサインイン URL の末尾に “**?prompt=admin\_consent**” を追加して、この管理者レベルの同意を強制的に行わせることができます。

-   **V2 アプリケーション モデルを使用して開発されたアプリケーション**の場合、[管理者の同意エンドポイントを使用する](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint)の**アクセス許可をディレクトリ管理者から要求する**セクションの下の手順に従って、この管理者レベルの同意を強制的に行わせることができます。

### <a name="perform-administrator-level-consent-for-a-single-tenant-application"></a>シングルテナント アプリケーションで管理者レベルの同意を実行する

-   アクセス許可を要求する**シングルテナント アプリケーション** (自分の組織内で開発しているか所有しているアプリケーションなど) の場合、**管理者レベルの同意**操作をすべてのユーザーを代表して実行するには、グローバル管理者としてサインインし、**[アプリケーション レジストリ] -&gt; [すべてのアプリケーション] -&gt; [アプリの選択] -&gt; [必要なアクセス許可]** ウィンドウの上部にある **[アクセス許可の付与]** をクリックします。

-   **V1 または V2 アプリケーション モデルを使用して開発されたアプリケーション**の場合、[管理者の同意エンドポイントを使用する](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint)の**アクセス許可をディレクトリ管理者から要求する**セクションの下の手順に従って、この管理者レベルの同意を強制的に行わせることができます。

### <a name="perform-administrator-level-consent-for-a-multi-tenant-application"></a>マルチテナント アプリケーションで管理者レベルの同意を実行する

-   アクセス許可を要求する**マルチテナント アプリケーション** (サード パーティ、または Microsoft が開発するアプリケーションなど) の場合は、**管理者レベルの同意**操作を実行できます。 グローバル管理者としてサインインして、**[エンタープライズ アプリケーション] -&gt; [すべてのアプリケーション] -&gt; [アプリの選択] -&gt; [アクセス許可]** ウィンドウ (まもなく利用可能) の下の **[アクセス許可の付与]** をクリックします。

-   [管理者の同意エンドポイントを使用する](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint)の**アクセス許可をディレクトリ管理者から要求する** セクションの下の指示に従って、この管理者レベル同意を強制的に行わせることもできます。

## <a name="next-steps"></a>次の手順
[管理者の同意エンドポイントを使用する](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint)

