---
title: 'チュートリアル: Azure Active Directory と HappyFox の統合 | Microsoft Docs'
description: Azure Active Directory と HappyFox の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 8204ee77-f64b-4fac-b64a-25ea534feac0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: jeedes
ms.openlocfilehash: 95def1ce278b0a816f19e3cd4e1b47bd3f68f1a9
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39440291"
---
# <a name="tutorial-azure-active-directory-integration-with-happyfox"></a>チュートリアル: Azure Active Directory と HappyFox の統合

このチュートリアルでは、HappyFox と Azure Active Directory (Azure AD) を統合する方法について説明します。

HappyFox と Azure AD の統合には、次の利点があります。

- HappyFox にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に HappyFox にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

HappyFox と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- HappyFox でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから HappyFox を追加する
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-happyfox-from-the-gallery"></a>ギャラリーから HappyFox を追加する
Azure AD への HappyFox の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に HappyFox を追加する必要があります。

**ギャラリーから HappyFox を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**HappyFox**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/happyfox-tutorial/tutorial_happyfox_search.png)

1. 結果ウィンドウで **[HappyFox]** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/happyfox-tutorial/tutorial_happyfox_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、HappyFox で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する HappyFox ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと HappyFox の関連ユーザーの間で、リンク関係が確立されている必要があります。

HappyFox で、Azure AD の **[ユーザー名]** の値を **[ユーザー名]** の値として割り当ててリンク関係を確立します。

HappyFox で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[HappyFox テスト ユーザーの作成](#creating-a-happyfox-test-user)** - HappyFox で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure ポータルで Azure AD のシングル サインオンを有効にして、 HappyFox アプリケーションでシングル サインオンを構成します。

**で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **HappyFox** アプリケーションの統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/happyfox-tutorial/tutorial_happyfox_samlbase.png)

1. **[HappyFox Domain and URLs]\(HappyFox のドメインと URL\)** セクションで、次の手順に従います。

    ![Configure single sign-on](./media/happyfox-tutorial/tutorial_happyfox_url.png)

    a. **[サインオン URL]** ボックスに、`https://<subdomain>.happyfox.com/` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<subdomain>.happyfox.com/saml/metadata/` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[HappyFox クライアント サポート チーム](https://support.happyfox.com/home)に問い合わせてください。 
 
1. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure single sign-on](./media/happyfox-tutorial/tutorial_happyfox_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/happyfox-tutorial/tutorial_general_400.png)

1. **[HappyFox Configuration]\(HappyFox 構成\)** セクションで、**[Configure HappyFox]\ (HappyFox を構成する\)** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![Configure single sign-on](./media/happyfox-tutorial/tutorial_happyfox_configure.png) 

1. HappyFox スタッフ ポータルにサインインして **[管理]** に移動し、**[統合]** タブをクリックします。

    ![Configure single sign-on](./media/happyfox-tutorial/header.png) 

1. [統合] タブで、**[SAML 統合]** にある **[構成]** をクリックし、[シングル サインオンの設定] を開きます。

    ![Configure single sign-on](./media/happyfox-tutorial/configure.png) 

1. [SAML configuration]\(SAML 構成\) セクションで、**[SSO Target URL]\(SSO ターゲット URL\)** テキストボックスに Azure Portal からコピーした **SAML のシングル サインオン サービスの URL** を貼り付けます。

    ![Configure single sign-on](./media/happyfox-tutorial/targeturl.png)

1. Azure Portal からダウンロードした証明書をメモ帳で開き、**[IdP Signature]\(IdP 署名\)** セクションにコンテンツを貼り付けます。
 
    ![Configure single sign-on](./media/happyfox-tutorial/cert.png)

1. **[設定の保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/happyfox-tutorial/savesettings.png)

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/happyfox-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/happyfox-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/happyfox-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/happyfox-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-happyfox-test-user"></a>HappyFox テスト ユーザーを作成する

アプリケーションでは、ジャストインタイムのユーザー プロビジョニングがサポートされ、認証後にユーザーがアプリケーションに自動的に作成されます。
        
### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に HappyFox へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**HappyFox に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[HappyFox]** を選択します。

    ![Configure single sign-on](./media/happyfox-tutorial/tutorial_happyfox_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

1. アクセス パネルで [HappyFox] タイルをクリックすると、HappyFox アプリケーションのログイン ページが表示されます。 サインイン ページに **‘SAML’** ボタンが表示されます。

    ![プラグイン](./media/happyfox-tutorial/saml.png) 

1. **'SAML'** ボタンをクリックして、Azure AD アカウントを使って HappyFox にログインします

アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事をご覧ください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/happyfox-tutorial/tutorial_general_01.png
[2]: ./media/happyfox-tutorial/tutorial_general_02.png
[3]: ./media/happyfox-tutorial/tutorial_general_03.png
[4]: ./media/happyfox-tutorial/tutorial_general_04.png

[100]: ./media/happyfox-tutorial/tutorial_general_100.png

[200]: ./media/happyfox-tutorial/tutorial_general_200.png
[201]: ./media/happyfox-tutorial/tutorial_general_201.png
[202]: ./media/happyfox-tutorial/tutorial_general_202.png
[203]: ./media/happyfox-tutorial/tutorial_general_203.png

