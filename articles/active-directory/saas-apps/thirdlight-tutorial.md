---
title: 'チュートリアル: Azure Active Directory と ThirdLight の統合 | Microsoft Docs'
description: Azure Active Directory と ThirdLight の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 168aae9a-54ee-4c2b-ab12-650a2c62b901
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 3ab87614e6c435abc5751b1da98f9ff3fb6cedb1
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39439594"
---
# <a name="tutorial-azure-active-directory-integration-with-thirdlight"></a>チュートリアル: Azure Active Directory と ThirdLight の統合

このチュートリアルでは、ThirdLight と Azure Active Directory (Azure AD) を統合する方法について説明します。

ThirdLight と Azure AD の統合には、次の利点があります。

- ThirdLight にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に ThirdLight にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

ThirdLight と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- ThirdLight でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの ThirdLight の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-thirdlight-from-the-gallery"></a>ギャラリーからの ThirdLight の追加
Azure AD への ThirdLight の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に ThirdLight を追加する必要があります。

**ギャラリーから ThirdLight を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**ThirdLight**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/thirdlight-tutorial/tutorial_thirdlight_search.png)

1. 結果ウィンドウで **[ThirdLight]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/thirdlight-tutorial/tutorial_thirdlight_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、ThirdLight で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する ThirdLight ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと ThirdLight の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を ThirdLight の **[Username][Username]\(ユーザー名\)** の値として割り当てます。

ThirdLight で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[ThirdLight テスト ユーザーの作成](#creating-a-thirdlight-test-user)** - ThirdLight で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portalで Azure AD のシングル サインオンを有効にして、ThirdLight アプリケーションでシングル サインオンを構成します。

**ThirdLight で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **ThirdLight** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/thirdlight-tutorial/tutorial_thirdlight_samlbase.png)

1. **[ThirdLight のドメインと URL]** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/thirdlight-tutorial/tutorial_thirdlight_url.png)

    a. **[サインオン URL]** ボックスに、`https://<subdomain>.thirdlight.com/` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<subdomain>.thirdlight.com/saml/sp` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[ThirdLight クライアント サポート チーム](https://www.thirdlight.com/support)に問い合わせてください。 
 
1. **[SAML 署名証明書]** セクションで、**[メタデータ XML]** をクリックし、コンピューターに XML ファイルを保存します。

    ![Configure single sign-on](./media/thirdlight-tutorial/tutorial_thirdlight_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/thirdlight-tutorial/tutorial_general_400.png)

1. 別の Web ブラウザーのウィンドウで、ThirdLight 企業サイトに管理者としてログインします。

1. **[Configuration]\>[System Administration]** の順に移動し、**[SAML2]** をクリックします。
   
    ![System Administration](./media/thirdlight-tutorial/ic805843.png "System Administration")

1. SAML2 構成セクションで、次の手順を実行します。
   
    ![SAML シングル サインオン](./media/thirdlight-tutorial/ic805844.png "SAML Single Sign-On")   

     a.この問題では、ターゲット (またはクラス) ラベルは "tip_amount" です。 **[Enable SAML2 Single Sign-On]** を選択します。
 
     b. **[Source for IdP Metadata]** として、**[Load IdP Metadata from XML]** を選択します。
 
     c. ダウンロードしたメタデータ ファイルを開き、内容をコピーしてから、 **[IdP Metadata XML]** テキストボックスに貼り付けます。 
     
     d. **[Save SAML2 settings]** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/thirdlight-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/thirdlight-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/thirdlight-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/thirdlight-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、Britta Simon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-thirdlight-test-user"></a>ThirdLight テスト ユーザーの作成

Azure AD ユーザーが ThirdLight にログインできるようにするには、そのユーザーを ThirdLight にプロビジョニングする必要があります。  
ThirdLight の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **ThirdLight** 企業サイトに管理者としてログインします。

1. **[Users]** タブに移動します。

1. **[Users and Groups]** を選択します。

1. **[Add new User]** ボタンをクリックします。

1. プロビジョニングする有効な AAD アカウントの **[Username]、[Name or Description]、[Email]、[Choose a Preset or Group of New Members]** を入力します。

1. **Create** をクリックしてください。

>[!NOTE]
>Thirdlight から提供されている他の Thirdlight ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に ThirdLight へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**ThirdLight に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[ThirdLight]** を選択します。

    ![Configure single sign-on](./media/thirdlight-tutorial/tutorial_thirdlight_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [ThirdLight] タイルをクリックすると、自動的に ThirdLight アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/thirdlight-tutorial/tutorial_general_01.png
[2]: ./media/thirdlight-tutorial/tutorial_general_02.png
[3]: ./media/thirdlight-tutorial/tutorial_general_03.png
[4]: ./media/thirdlight-tutorial/tutorial_general_04.png

[100]: ./media/thirdlight-tutorial/tutorial_general_100.png

[200]: ./media/thirdlight-tutorial/tutorial_general_200.png
[201]: ./media/thirdlight-tutorial/tutorial_general_201.png
[202]: ./media/thirdlight-tutorial/tutorial_general_202.png
[203]: ./media/thirdlight-tutorial/tutorial_general_203.png

