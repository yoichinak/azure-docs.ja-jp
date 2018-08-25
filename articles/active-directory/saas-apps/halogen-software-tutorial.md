---
title: 'チュートリアル: Azure Active Directory と Halogen Software の統合 | Microsoft Docs'
description: Azure Active Directory と Halogen Software の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 2ca2298d-9a0c-4f14-925c-fa23f2659d28
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: a7be918118d86da7e1134f5ce46e5f163ba62601
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39432849"
---
# <a name="tutorial-azure-active-directory-integration-with-halogen-software"></a>チュートリアル: Azure Active Directory と Halogen Software の統合

このチュートリアルでは、Halogen Software と Azure Active Directory (Azure AD) を統合する方法について説明します。

Halogen Software と Azure AD の統合には、次の利点があります。

- Halogen Software にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Halogen Software にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Halogen Software と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- シングル サインオン対応の Halogen Software サブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Halogen Software の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-halogen-software-from-the-gallery"></a>ギャラリーからの Halogen Software の追加

Azure AD への Halogen Software の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Halogen Software を追加する必要があります。

**ギャラリーから Halogen Software を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**Halogen Software**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/halogen-software-tutorial/tutorial_halogensoftware_search.png)

1. 結果ウィンドウで **Halogen Software** を選択し、**[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/halogen-software-tutorial/tutorial_halogensoftware_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Halogen Software で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Halogen Software ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Halogen Software の関連ユーザーの間で、リンクの関係が確立されている必要があります。

Halogen Software で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Halogen Software で Azure AD のシングル サインオンを構成し、テストするには、次の要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Halogen Software のテスト ユーザーの作成](#creating-a-halogen-software-test-user)** - Halogen Software で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Halogen Software アプリケーションでシングル サインオンを構成します。

**Halogen Software で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Halogen Software** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/halogen-software-tutorial/tutorial_halogensoftware_samlbase.png)

1. **[Halogen Software のドメインと URL]** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/halogen-software-tutorial/tutorial_halogensoftware_url.png)

    a. **[サインオン URL]** ボックスに、`https://global.hgncloud.com/<companyname>` のパターンを使用して URL を入力します。

    b. **[Identifier]\(識別子\)** ボックスに、`https://global.halogensoftware.com/<companyname>`、`https://global.hgncloud.com/<companyname>` のパターンで URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[Halogen Software クライアント サポート チーム](https://support.halogensoftware.com/)に問い合わせてください。 
 


1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![Configure single sign-on](./media/halogen-software-tutorial/tutorial_halogensoftware_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/halogen-software-tutorial/tutorial_general_400.png)

1. 別のブラウザー ウィンドウで、管理者として **Halogen Software** アプリケーションにサインオンします。

1. **[Options]** タブをクリックします。 
   
    ![What is Azure AD Connect][12]

1. 左のナビゲーション ウィンドウで、 **[SAML Configuration]** をクリックします。 
   
    ![What is Azure AD Connect][13]

1. **[SAML 構成]** ページで、次の手順を実行します。 

    ![What is Azure AD Connect][14]

     a. **[Unique Identifier]** で **[NameID]** を選択します。

     b. **[Unique Identifier Maps To]** で **[Username]** を選択します。
  
     c. ダウンロードしたメタデータ ファイルをアップロードするには、**[Browse]** をクリックしてファイルを選択し、**[Upload File]** をクリックします。
 
     d. 構成をテストするには、**[Run Test]** をクリックします。 
    
    >[!NOTE]
    >"*The SAML test is complete.Please close this window*" というメッセージが表示されるまで待機する必要があります。 次に、開いているブラウザー ウィンドウを閉じます。 **[Enable SAML]** チェック ボックスは、テストが完了した場合にのみ有効にします。 
     
     e. **[Enable SAML]** を選択します。
    
     f. **[変更を保存]** をクリックします。 

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。


### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/halogen-software-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/halogen-software-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/halogen-software-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/halogen-software-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と名前を入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-halogen-software-test-user"></a>Halogen Software のテスト ユーザーの作成

このセクションの目的は、Halogen Software で Britta Simon というユーザーを作成することです。

**Halogen Software で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. 管理者として **Halogen Software** アプリケーションにサインオンします。

1. **[User Center]** タブをクリックし、**[Create User]** をクリックします。
   
    ![What is Azure AD Connect][300]  

1. **[New User]** ダイアログ ページで、次の手順に従います。
   
    ![What is Azure AD Connect][301]

    a. **[First Name]\(名\)** ボックスに、ユーザーの名を入力します (この例では **Britta**)。
    
    b. **[Last Name]\(姓\)** ボックスに、ユーザーの姓を入力します (この例では **Simon**)。 

    c. **[Username]** ボックスに、Azure Portal でのユーザー名「**Britta Simon**」を入力します。

    d. **[Password]** ボックスに Britta のパスワードを入力します。
    
    e. **[Save]** をクリックします。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Halogen Software へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Halogen Software に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Halogen Software]** を選択します。

    ![Configure single sign-on](./media/halogen-software-tutorial/tutorial_halogensoftware_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD の SSO 構成をテストすることです。

アクセス パネルで Halogen Software のタイルをクリックすると、自動的に Halogen Software アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/halogen-software-tutorial/tutorial_general_01.png
[2]: ./media/halogen-software-tutorial/tutorial_general_02.png
[3]: ./media/halogen-software-tutorial/tutorial_general_03.png
[4]: ./media/halogen-software-tutorial/tutorial_general_04.png

[12]: ./media/halogen-software-tutorial/tutorial_halogen_12.png

[13]: ./media/halogen-software-tutorial/tutorial_halogen_13.png

[14]: ./media/halogen-software-tutorial/tutorial_halogen_14.png

[100]: ./media/halogen-software-tutorial/tutorial_general_100.png

[200]: ./media/halogen-software-tutorial/tutorial_general_200.png
[201]: ./media/halogen-software-tutorial/tutorial_general_201.png
[202]: ./media/halogen-software-tutorial/tutorial_general_202.png
[203]: ./media/halogen-software-tutorial/tutorial_general_203.png

[300]: ./media/halogen-software-tutorial/tutorial_halogen_300.png

[301]: ./media/halogen-software-tutorial/tutorial_halogen_301.png
