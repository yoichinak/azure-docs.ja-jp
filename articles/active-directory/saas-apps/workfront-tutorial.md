---
title: 'チュートリアル: Azure Active Directory と Workfront の統合 | Microsoft Docs'
description: Azure Active Directory と Workfront の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: aab8bd2f-f9dd-42da-a18e-d707865687d7
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: jeedes
ms.openlocfilehash: 155aa8ac1ee01ba46297e66763e0c0501ead32e2
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2018
ms.locfileid: "42144113"
---
# <a name="tutorial-azure-active-directory-integration-with-workfront"></a>チュートリアル: Azure Active Directory と Workfront の統合

このチュートリアルでは、Workfront と Azure Active Directory (Azure AD) を統合する方法について説明します。

Workfront と Azure AD の統合には、次の利点があります。

- Workfront にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に Workfront にサインオン (シングル サインオン) できるようにします
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Workfront と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Workfront でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Workfront の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-workfront-from-the-gallery"></a>ギャラリーからの Workfront の追加
Azure AD への Workfront の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Workfront を追加する必要があります。

**ギャラリーから Workfront を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**Workfront**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/workfront-tutorial/tutorial_workfront_search.png)

1. 結果ウィンドウで **Workfront** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/workfront-tutorial/tutorial_workfront_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Workfront で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Workfront ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Workfront の関連ユーザーの間で、リンク関係が確立されている必要があります。

Workfront で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

Workfront で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Workfront テスト ユーザーの作成](#creating-a-workfront-test-user)** - Workfront で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Workfront アプリケーションでシングル サインオンを構成します。

**Workfront で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Workfront** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/workfront-tutorial/tutorial_workfront_samlbase.png)

1. **[Workfront のドメインと URL]** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/workfront-tutorial/tutorial_workfront_url.png)

    a. **[サインオン URL]** ボックスに、`https://<companyname>.attask-ondemand.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<companyname>.attasksandbox.com/SAML2` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[Workfront クライアント サポート チーム](https://www.workfront.com/services-and-support)に問い合わせてください。 
 
1. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure single sign-on](./media/workfront-tutorial/tutorial_workfront_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/workfront-tutorial/tutorial_general_400.png)

1. **[Workfront 構成]** セクションで、**[Workfront の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[Quick Reference]\(クイック リファレンス\)** セクションから、**サインアウト URL と SAML シングル サインオン サービス URL** をコピーします。

    ![Configure single sign-on](./media/workfront-tutorial/tutorial_workfront_configure.png) 

1. Workfront 企業サイトに管理者としてサインオンします。

1. **[Single Sign On Configuration]** に移動します。

1. **[Single Sign-On (シングル サインオン)]** ダイアログ ボックスで、次の手順を実行します。
    
    ![Configure single sign-on][23]
   
    a. **[Type]** で **[SAML 2.0]** を選択します。
   
    b. **サービス プロバイダー ID** を選択します。
   
    c. **SAML シングル サインオン サービス URL** を **[Login Portal URL]\(ログイン ポータル URL\)** ボックスに貼り付けます。
   
    d. **シングル サインアウト サービス URL** を **[Sign-Out URL]\(サインアウト URL\)** ボックスに貼り付けます。
   
    e. **パスワード変更 URL** を **[Change Password URL]\(パスワード変更 URL\)** ボックスに貼り付けます。
   
    f. **[Save]** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/workfront-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/workfront-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/workfront-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/workfront-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-workfront-test-user"></a>Workfront テスト ユーザーの作成

このセクションの目的は、Workfront で Britta Simon というユーザーを作成することです。

**Workfront で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. Workfront 企業サイトに管理者としてサインオンします。
1. 上部のメニューで **[People]** をクリックします。
1. **[New Person]** をクリックします。 
1. [New Person] ダイアログで、次の手順を実行します。
   
    ![Workfront テスト ユーザーを作成する][21] 
   
    a. **[First Name]\(名\)** ボックスに「Britta」と入力します。
   
    b. **[Last Name]\(姓\)** ボックスに「Simon」と入力します。
   
    c. **[Email Address]** ボックスに、Britta Simon の Azure Active Directory の電子メール アドレスを入力します。
   
    d. **[Add Person]** をクリックします。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Workfront へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Workfront に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Workfront]** を選択します。

    ![Configure single sign-on](./media/workfront-tutorial/tutorial_workfront_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Workfront] タイルをクリックすると、Workfront アプリケーションのログイン ページが表示されます。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/workfront-tutorial/tutorial_general_01.png
[2]: ./media/workfront-tutorial/tutorial_general_02.png
[3]: ./media/workfront-tutorial/tutorial_general_03.png
[4]: ./media/workfront-tutorial/tutorial_general_04.png
[21]:./media/workfront-tutorial/tutorial_attask_08.png
[23]:./media/workfront-tutorial/tutorial_attask_06.png
[100]: ./media/workfront-tutorial/tutorial_general_100.png

[200]: ./media/workfront-tutorial/tutorial_general_200.png
[201]: ./media/workfront-tutorial/tutorial_general_201.png
[202]: ./media/workfront-tutorial/tutorial_general_202.png
[203]: ./media/workfront-tutorial/tutorial_general_203.png

