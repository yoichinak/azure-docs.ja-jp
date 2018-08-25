---
title: 'チュートリアル: Azure Active Directory と Veritas Enterprise Vault.cloud SSO の統合| Microsoft Docs'
description: Azure Active Directory と Veritas Enterprise Vault.cloud SSO の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: jeedes
ms.openlocfilehash: 4ff282b3db4689ceaf5fa27b57c82cb05025712e
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449099"
---
# <a name="tutorial-azure-active-directory-integration-with-veritas-enterprise-vaultcloud-sso"></a>チュートリアル: Azure Active Directory と Veritas Enterprise Vault.cloud SSO の統合

このチュートリアルでは、Veritas Enterprise Vault.cloud SSO と Azure Active Directory (Azure AD) を統合する方法について説明します。

Veritas Enterprise Vault.cloud SSO と Azure AD の統合には、次の利点があります。

- Veritas Enterprise Vault.cloud SSO にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで Veritas Enterprise Vault.cloud SSO に自動サインオン (シングル サインオン) できるようになります。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Veritas Enterprise Vault.cloud SSO と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Veritas Enterprise Vault.cloud SSO でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Veritas Enterprise Vault.cloud SSO の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-veritas-enterprise-vaultcloud-sso-from-the-gallery"></a>ギャラリーからの Veritas Enterprise Vault.cloud SSO の追加
Azure AD への Veritas Enterprise Vault.cloud SSO の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Veritas Enterprise Vault.cloud SSO を追加する必要があります。

**ギャラリーから Veritas Enterprise Vault.cloud SSO を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**Veritas Enterprise Vault.cloud SSO**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/veritas-tutorial/tutorial_veritas_search.png)

1. 結果ウィンドウで **[Veritas Enterprise Vault.cloud SSO]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/veritas-tutorial/tutorial_veritas_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Veritas Enterprise Vault.cloud SSO で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Veritas Enterprise Vault.cloud SSO ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Veritas Enterprise Vault.cloud SSO の関連ユーザーの間で、リンク関係が確立されている必要があります。

Veritas Enterprise Vault.cloud SSO で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Veritas Enterprise Vault.cloud SSO で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Veritas Enterprise Vault.cloud SSO のテスト ユーザーの作成](#creating-a-veritas-enterprise-vaultcloud-sso-test-user)** - Veritas Enterprise Vault.cloud SSO で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Veritas Enterprise Vault.cloud SSO アプリケーションでシングル サインオンを構成します。

**Veritas Enterprise Vault.cloud SSO で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Veritas Enterprise Vault.cloud SSO** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/veritas-tutorial/tutorial_veritas_samlbase.png)

1. **[Veritas Enterprise Vault.cloud SSO のドメインと URL]** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/veritas-tutorial/tutorial_veritas_url.png)

    a. **[サインオン URL]** ボックスに、`https://personal.ap.archive.veritas.com/CID=<CUSTOMERID>` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスでは、データセンターに応じた URL を使用します。

    | データセンター| URL |
    |----------|----|
    | 北米| `https://auth.lax.archivecloud.net` |
    | ヨーロッパ | `https://auth.ams.archivecloud.net` |
    | アジア太平洋| `https://auth.syd.archivecloud.net`|

    c. **[応答 URL]** ボックスでは、データセンターに応じた URL を使用します。

    | データセンター| URL |
    |----------|----|
    | 北米| `https://auth.lax.archivecloud.net` |
    | ヨーロッパ | `https://auth.ams.archivecloud.net` |
    | アジア太平洋| `https://auth.syd.archivecloud.net`|
    
    > [!NOTE] 
    > これは実際の値ではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Veritas Enterprise Vault.cloud SSO クライアント サポート チーム](https://www.veritas.com/support/.html)に問い合わせてください。 

1. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure single sign-on](./media/veritas-tutorial/tutorial_veritas_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/veritas-tutorial/tutorial_general_400.png)

1. **[Veritas Enterprise Vault.cloud SSO 構成]** セクションで、**[Veritas Enterprise Vault.cloud SSO の構成]** をクリックして **[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![Configure single sign-on](./media/veritas-tutorial/tutorial_veritas_configure.png) 

1. **Veritas Enterprise Vault.cloud SSO** 側にシングルサインオンを構成するには、ダウンロードされた**証明書 (Base64)** および **SAML シングル サインオン サービス URL** を [Veritas Enterprise Vault.cloud SSO サポート チーム](https://www.veritas.com/support/.html)に送信する必要があります。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/veritas-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/veritas-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/veritas-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/veritas-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-veritas-enterprise-vaultcloud-sso-test-user"></a>Veritas Enterprise Vault.cloud SSO のテスト ユーザーの作成

このセクションでは、Veritas Enterprise Vault.cloud SSO で Britta Simon というユーザーを作成します。 [Veritas Enterprise Vault.cloud SSO サポート チーム](https://www.veritas.com/support/.html)と連携して、Veritas Enterprise Vault.cloud SSO プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Veritas Enterprise Vault.cloud SSO へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を Veritas Enterprise Vault.cloud SSO に割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Veritas Enterprise Vault.cloud SSO]** を選択します。

    ![Configure single sign-on](./media/veritas-tutorial/tutorial_veritas_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Veritas Enterprise Vault.cloud SSO] タイルをクリックすると、自動的に Veritas Enterprise Vault.cloud SSO アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/veritas-tutorial/tutorial_general_01.png
[2]: ./media/veritas-tutorial/tutorial_general_02.png
[3]: ./media/veritas-tutorial/tutorial_general_03.png
[4]: ./media/veritas-tutorial/tutorial_general_04.png

[100]: ./media/veritas-tutorial/tutorial_general_100.png

[200]: ./media/veritas-tutorial/tutorial_general_200.png
[201]: ./media/veritas-tutorial/tutorial_general_201.png
[202]: ./media/veritas-tutorial/tutorial_general_202.png
[203]: ./media/veritas-tutorial/tutorial_general_203.png

