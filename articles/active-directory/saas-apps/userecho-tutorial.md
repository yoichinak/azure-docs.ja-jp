---
title: 'チュートリアル: Azure Active Directory と UserEcho の統合 | Microsoft Docs'
description: Azure Active Directory と UserEcho の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bedd916b-8f69-4b50-9b8d-56f4ee3bd3ed
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: ecdd37db662c6861e35f80bfbf4ac8ff7e0d08c9
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39443273"
---
# <a name="tutorial-azure-active-directory-integration-with-userecho"></a>チュートリアル: Azure Active Directory と UserEcho の統合

このチュートリアルでは、UserEcho と Azure Active Directory (Azure AD) を統合する方法について説明します。

UserEcho と Azure AD の統合には、次の利点があります。

- UserEcho にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に UserEcho にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と UserEcho の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- UserEcho でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの UserEcho の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-userecho-from-the-gallery"></a>ギャラリーからの UserEcho の追加
Azure AD への UserEcho の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に UserEcho を追加する必要があります。

**ギャラリーから UserEcho を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「 **UserEcho**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/userecho-tutorial/tutorial_userecho_search.png)

1. 結果パネルで **[UserEcho]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/userecho-tutorial/tutorial_userecho_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、UserEcho で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する UserEcho ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと UserEcho の関連ユーザーの間で、リンク関係が確立されている必要があります。

UserEcho で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

UserEcho で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[UserEcho のテスト ユーザーの作成](#creating-a-userecho-test-user)** - UserEcho で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、UserEcho アプリケーションでシングル サインオンを構成します。

**UserEcho で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **UserEcho** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/userecho-tutorial/tutorial_userecho_samlbase.png)

1. **[UserEcho のドメインと URL]** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/userecho-tutorial/tutorial_userecho_url.png)

    a. **[サインオン URL]** ボックスに、`https://<companyname>.userecho.com/` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<companyname>.userecho.com/saml/metadata/` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[UserEcho クライアント サポート チーム](https://feedback.userecho.com/)に連絡してください。 

1. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure single sign-on](./media/userecho-tutorial/tutorial_userecho_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/userecho-tutorial/tutorial_general_400.png)

1. **[UserEcho Configuration (UserEcho 構成)]** セクションで、**[Configure UserEcho (UserEcho を構成する)]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから**サインアウト URL と SAML シングル サインオン サービス URL** をコピーします。

    ![Configure single sign-on](./media/userecho-tutorial/tutorial_userecho_configure.png) 

1. 別の Web ブラウザー ウィンドウで、管理者として UserEcho 企業サイトにサインオンします。

1. 上部にあるツール バーで、ユーザー名をクリックして、メニューを展開し、 **[Setup (セットアップ)]** をクリックします。
   
    ![Configure single sign-on](./media/userecho-tutorial/tutorial_userecho_06.png) 

1. **[Integrations (統合)]** をクリックします。
   
    ![Configure single sign-on](./media/userecho-tutorial/tutorial_userecho_07.png) 

1. **[Website (Web サイト)]**、**[Single sign-on (SAML2) (シングル サインオン (SAML2))]** の順にクリックします。
   
    ![Configure single sign-on](./media/userecho-tutorial/tutorial_userecho_08.png) 

1. **[Single sign-on (SAML2) (シングル サインオン (SAML2))]** ページで、次の手順に従います。
   
    ![Configure single sign-on](./media/userecho-tutorial/tutorial_userecho_09.png)
    
    a. **[SAML-enabled]** で **[Yes]** を選択します。
    
    b. Azure Portal からコピーした **SAML シングル サインオン サービス URL** を **[SAML SSO URL]** ボックスに貼り付けます。
    
    c. Azure Portal からコピーした**サインアウト URL** を **[Remote logoout URL (リモート ログアウト URL)]** ボックスに貼り付けます。
    
    d. ダウンロードした証明書をメモ帳で開き、その内容をコピーして、**[X.509 Certificate]** ボックスに貼り付けます。
    
    e. **[Save]** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/userecho-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/userecho-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/userecho-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/userecho-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-userecho-test-user"></a>UserEcho のテスト ユーザーの作成

このセクションの目的は、UserEcho で Britta Simon というユーザーを作成することです。

**UserEcho で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. UserEcho 企業サイトに管理者としてサインオンします。

1. 上部にあるツール バーで、ユーザー名をクリックして、メニューを展開し、 **[Setup (セットアップ)]** をクリックします。
   
    ![Configure single sign-on](./media/userecho-tutorial/tutorial_userecho_06.png)

1. **[Users (ユーザー)]** をクリックして、**[Users (ユーザー)]** セクションを展開します。
   
    ![Configure single sign-on](./media/userecho-tutorial/tutorial_userecho_10.png)

1. **[ユーザー]** をクリックします。
   
    ![Configure single sign-on](./media/userecho-tutorial/tutorial_userecho_11.png)

1. **[Invite a new user (新しいユーザーを招待)]** をクリックします。
   
    ![Configure single sign-on](./media/userecho-tutorial/tutorial_userecho_12.png)

1. **[Invite a new user (新しいユーザーを招待)]** ダイアログで、次の手順に従います。
   
    ![Configure single sign-on](./media/userecho-tutorial/tutorial_userecho_13.png)

    a. **[名前]** ボックスに、ユーザーの氏名 (Britta Simon など) を入力します。
    
    b.  **[Email]\(メール\)** ボックスに、ユーザーのメール アドレス (Brittasimon@contoso.com など) を入力します。
    
    c. **[招待]** をクリックします。

Britta に招待状が送信され、UserEcho の使用を開始できるようになります。 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に UserEcho へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**UserEcho に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[UserEcho]** を選択します。

    ![Configure single sign-on](./media/userecho-tutorial/tutorial_userecho_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD の SSO 構成をテストすることです。  

アクセス パネルで UserEcho のタイルをクリックすると、自動的に UserEcho アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/userecho-tutorial/tutorial_general_01.png
[2]: ./media/userecho-tutorial/tutorial_general_02.png
[3]: ./media/userecho-tutorial/tutorial_general_03.png
[4]: ./media/userecho-tutorial/tutorial_general_04.png

[100]: ./media/userecho-tutorial/tutorial_general_100.png

[200]: ./media/userecho-tutorial/tutorial_general_200.png
[201]: ./media/userecho-tutorial/tutorial_general_201.png
[202]: ./media/userecho-tutorial/tutorial_general_202.png
[203]: ./media/userecho-tutorial/tutorial_general_203.png

