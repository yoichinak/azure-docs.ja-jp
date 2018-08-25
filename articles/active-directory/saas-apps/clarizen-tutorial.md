---
title: 'チュートリアル: Azure Active Directory と Clarizen の統合 | Microsoft Docs'
description: Azure Active Directory と Clarizen の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: jeedes
ms.openlocfilehash: 510bf383848725f3864c40af02c2b309370237f0
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39438088"
---
# <a name="tutorial-azure-active-directory-integration-with-clarizen"></a>チュートリアル: Azure Active Directory と Clarizen の統合

このチュートリアルでは、Azure Active Directory (Azure AD) を Clarizen と統合する方法について説明します。 この統合には次のようなメリットがあります。

- Clarizen にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Clarizen にサインイン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

このチュートリアルのシナリオは、主に次の 2 つの作業で構成されています。

1. ギャラリーからの Clarizen の追加。
1. Azure AD シングル サインオンの構成とテスト。

サービスとしてのソフトウェア (SaaS) アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Clarizen と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- シングル サインオンが有効な Clarizen サブスクリプション

このチュートリアルの手順をテストするには、次の推奨事項に従います。

- テスト環境で Azure AD のシングル サインオンをテストします。 必要な場合を除き、運用環境は使用しないでください。
- Azure AD のテスト環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="add-clarizen-from-the-gallery"></a>ギャラリーからの Clarizen の追加
Azure AD への Clarizen の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Clarizen を追加します。

1. [Azure Portal](https://portal.azure.com) の左側のウィンドウで、**Azure Active Directory** のアイコンをクリックします。

    ![Azure Active Directory のアイコン][1]

1. **[エンタープライズ アプリケーション]** をクリックします。 次に、**[すべてのアプリケーション]** をクリックします。

    ![[エンタープライズ アプリケーション] と [すべてのアプリケーション] のクリック][2]

1. ダイアログ ボックスの上部にある **[追加]** ボタンをクリックします。

    ![[追加] ボタン][3]

1. 検索ボックスに、「**Clarizen**」と入力します。

    ![検索ボックスに「Clarizen」と入力](./media/clarizen-tutorial/tutorial_clarizen_000.png)

1. 結果ウィンドウで **[Clarizen]** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

    ![結果ウィンドウでの Clarizen の選択](./media/clarizen-tutorial/tutorial_clarizen_0001.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
次のセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Clarizen で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Clarizen ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Clarizen の関連ユーザーの間で、リンク関係が確立されている必要があります。 このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Clarizen の **[Username (ユーザー名)]** の値として割り当てます。

Clarizen で Azure AD のシングル サインオンを構成してテストするには、次の要素を完了します。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Clarizen テスト ユーザーの作成](#create-a-clarizen-test-user)** - Clarizen で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
Azure Portal で Azure AD のシングル サインオンを有効にし、Clarizen アプリケーションでシングル サインオンを構成します。

1. Azure Portal の **Clarizen** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオン] のクリック][4]

1. **[シングル サインオン]** ダイアログ ボックスで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![[SAML ベースのサインオン] の選択](./media/clarizen-tutorial/tutorial_clarizen_01.png)

1. **[Clarizen のドメインと URL]** セクションで、次の手順を実行します。

    ![識別子と応答 URL のボックス](./media/clarizen-tutorial/tutorial_clarizen_02.png)

    a. **[識別子]** ボックスに、値として「**Clarizen**」と入力します。

    b. **[応答 URL]** ボックスに、**https://<company name>.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx** というパターンを使用して URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL を使用する必要があります。 ここでは、識別子に一意の文字列値を使用することをお勧めします。 実際の値を取得するには、[Clarizen サポート チーム](https://success.clarizen.com/hc/en-us/requests/new)に連絡してください。

1. **[SAML 署名証明書**] セクションで、**[新しい証明書の作成]** をクリックします。

    ![[新しい証明書の作成] のクリック](./media/clarizen-tutorial/tutorial_clarizen_03.png)    

1. **[新しい証明書の作成]** ダイアログ ボックスで、カレンダー アイコンをクリックし、期限日を選択します。 その後、 **[保存]** をクリックします。

    ![期限日の選択と保存](./media/clarizen-tutorial/tutorial_general_300.png)

1. **[SAML 署名証明書]** セクションで、**[Make new certificate active (新しい証明書を有効にする)]** をクリックし、**[保存]** をクリックします。

    ![新しい証明書を有効にするチェック ボックスの選択](./media/clarizen-tutorial/tutorial_clarizen_04.png)

1. **[Rollover certificate (ロールオーバー証明書)]** ダイアログ ボックスで、**[OK]** をクリックします。

    ![[OK] をクリックして証明書の有効化を確定](./media/clarizen-tutorial/tutorial_general_400.png)

1. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![[Certificate (Base64) (証明書 (Base64))] をクリックしてダウンロードを開始](./media/clarizen-tutorial/tutorial_clarizen_05.png)

1. **[Clarizen 構成]** セクションで、**[Clarizen の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。

    ![[Clarizen の構成] のクリック](./media/clarizen-tutorial/tutorial_clarizen_06.png)

    ![ファイルと URL が含まれた [サインオンの構成] ウィンドウ](./media/clarizen-tutorial/tutorial_clarizen_07.png)

1. 別の Web ブラウザー ウィンドウで、Clarizen 企業サイトに管理者としてサインインします。

1. 自分のユーザー名をクリックし、**[Settings (設定)]** をクリックします。

    ![ユーザー名の下にある [Settings (設定)] のクリック](./media/clarizen-tutorial/tutorial_clarizen_001.png "設定")

1. **[Global Settings (グローバル設定)]** タブをクリックします。次に、**[Federated Authentication (フェデレーション認証)]** の横の **[edit (編集)]** をクリックします。

    ![[Global Settings (グローバル設定)] タブ](./media/clarizen-tutorial/tutorial_clarizen_002.png "グローバル設定")

1. **[Federated Authentication (フェデレーション認証)]** ダイアログ ボックスで、次の手順を実行します。

    ![[Federated Authentication (フェデレーション認証)] ダイアログ ボックス](./media/clarizen-tutorial/tutorial_clarizen_003.png "フェデレーション認証")

    a. **[Enable Federated Authentication (フェデレーション認証を有効にする)]** をオンにします。

    b. **[アップロード]** をクリックして、ダウンロードした証明書をアップロードします。

    c. **[Sign-in URL (サインイン URL)]** ボックスに、Azure AD アプリケーション構成ウィンドウの **[SAML Single Sign-on Service URL (SAML シングル サインオン サービス URL)]** の値を入力します。

    d. **[Sign-out URL (サインアウト URL)]** ボックスに、Azure AD アプリケーション構成ウィンドウの **[Sign-Out URL (サインアウト URL)]** の値を入力します。

    e. **[POST を使用]** を選択します。

    f. **[Save]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
Azure Portal で Britta Simon というテスト ユーザーを作成します。

![Azure AD のテスト ユーザーの名前と電子メール アドレス][100]

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のアイコンをクリックします。

    ![Azure Active Directory のアイコン](./media/clarizen-tutorial/create_aaduser_01.png)

1. **[ユーザーとグループ]** をクリックし、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。

    ![[ユーザーとグループ] と [すべてのユーザー] のクリック](./media/clarizen-tutorial/create_aaduser_02.png)

1. ダイアログ ボックスの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログ ボックスを開きます。

    ![[追加] ボタン](./media/clarizen-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![名前、電子メール アドレス、パスワードが入力済みの [ユーザー] ダイアログ ボックス](./media/clarizen-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、Britta Simon アカウントの電子メール アドレスを入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。

### <a name="create-a-clarizen-test-user"></a>Clarizen テスト ユーザーの作成

このセクションの目的は、Clarizen で Britta Simon というユーザーを作成することです。 Clarizen では、自動ユーザー プロビジョニングがサポートされています。この設定は、既定で有効になっています。 自動ユーザー プロビジョニングの構成方法の詳細については、[こちら](clarizen-provisioning-tutorial.md)を参照してください。

**ユーザーを手動で作成する必要がある場合、次の手順を実行します。**

Clarizen への Azure AD ユーザーのサインインを有効にするには、ユーザー アカウントをプロビジョニングする必要があります。 Clarizen の場合、プロビジョニングは手動で行います。

1. Clarizen 企業サイトに管理者としてサインインします。

1. **[ユーザー]** をクリックします。

    ![[People (ユーザー)] のクリック](./media/clarizen-tutorial/create_aaduser_001.png "ユーザー")

1. **[ユーザーの招待]** をクリックします。

    ![[Invite User (ユーザーの招待)] ボタン](./media/clarizen-tutorial/create_aaduser_002.png "ユーザーの招待")

1. **[Invite People (ユーザーの招待)]** ダイアログ ボックスで、次の手順を実行します。

    ![[Invite People (ユーザーの招待)] ダイアログ ボックス](./media/clarizen-tutorial/create_aaduser_003.png "ユーザーの招待")

    a. **[Email (電子メール)]** ボックスに、Britta Simon アカウントの電子メール アドレスを入力します。

    b. **[招待]** をクリックします。

    > [!NOTE]
    > Azure Active Directory アカウント所有者がメールを受信し、リンクに従ってアカウントを確認するとそのアカウントがアクティブになります。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
Britta Simon に Clarizen へのアクセスを許可することで、Britta Simon アカウントが Azure シングル サインオンを使用できるようにします。

![割り当て済みのテスト ユーザー][200]

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]**、**[すべてのアプリケーション]** の順にクリックします。

    ![[エンタープライズ アプリケーション] と [すべてのアプリケーション] のクリック][201]

1. アプリケーションの一覧で **[Clarizen]** を選択します。

    ![一覧での Clarizen の選択](./media/clarizen-tutorial/tutorial_clarizen_50.png)

1. 左側のウィンドウで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] のクリック][202]

1. **[追加]** をクリックします。 次に、**[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[追加] ボタンと [割り当ての追加] ダイアログ ボックス][203]

1. **[ユーザーとグループ]** ダイアログ ボックスで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログ ボックスで、**[選択]** ボタンをクリックします。

1. **[割り当ての追加]** ダイアログ ボックスで、**[割り当て]** ボタンをクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト
アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Clarizen] タイルをクリックすると、Clarizen アプリケーションに自動的にサインインします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
* [[ユーザー プロビジョニングの構成]](clarizen-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/clarizen-tutorial/tutorial_general_01.png
[2]: ./media/clarizen-tutorial/tutorial_general_02.png
[3]: ./media/clarizen-tutorial/tutorial_general_03.png
[4]: ./media/clarizen-tutorial/tutorial_general_04.png

[100]: ./media/clarizen-tutorial/tutorial_general_100.png

[200]: ./media/clarizen-tutorial/tutorial_general_200.png
[201]: ./media/clarizen-tutorial/tutorial_general_201.png
[202]: ./media/clarizen-tutorial/tutorial_general_202.png
[203]: ./media/clarizen-tutorial/tutorial_general_203.png