---
title: 'チュートリアル: Azure Active Directory と Sugar CRM の統合 | Microsoft Docs'
description: Azure Active Directory と Sugar CRM の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 3331b9fc-ebc0-4a3a-9f7b-bf20ee35d180
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: jeedes
ms.openlocfilehash: 1acaf5e530f5d5563901d8d498901ecc1bffecdb
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39427394"
---
# <a name="tutorial-azure-active-directory-integration-with-sugar-crm"></a>チュートリアル: Azure Active Directory と Sugar CRM の統合

このチュートリアルでは、Sugar CRM と Azure Active Directory (Azure AD) を統合する方法について説明します。

Sugar CRM と Azure AD の統合には、次の利点があります。

- Sugar CRM にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に Sugar CRM にサインオン (シングル サインオン) できるように、設定が可能です
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Sugar CRM と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Sugar CRM でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Sugar CRM の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-sugar-crm-from-the-gallery"></a>ギャラリーからの Sugar CRM の追加
Azure AD への Sugar CRM の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Sugar CRM を追加する必要があります。

**ギャラリーから Sugar CRM を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに「**Sugar CRM**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/sugarcrm-tutorial/tutorial_sugarcrm_search.png)

1. 結果ウィンドウで **Sugar CRM** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/sugarcrm-tutorial/tutorial_sugarcrm_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Sugar CRM で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Sugar CRM ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Sugar CRM の関連ユーザーの間で、リンク関係が確立されている必要があります。

Sugar CRM で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

Sugar CRM で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Sugar CRM テスト ユーザーの作成](#creating-a-sugar-crm-test-user)** - Sugar CRM で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Sugar CRM アプリケーションでシングル サインオンを構成します。

**Sugar CRM で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Sugar CRM** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/sugarcrm-tutorial/tutorial_sugarcrm_samlbase.png)

1. **[Sugar CRM のドメインと URL]** セクションで、次の手順に従います。

    ![Configure single sign-on](./media/sugarcrm-tutorial/tutorial_sugarcrm_url.png)

    **[サインオン URL]** ボックスに、次の形式で URL を入力します。
    | |
    |--|
    | `https://<companyname>.sugarondemand.com` |
    | `https://<companyname>.trial.sugarcrm` |

    > [!NOTE] 
    > この値は実際のものではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Sugar CRM クライアント サポート チーム](https://support.sugarcrm.com/)に問い合わせてください。 
 
1. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure single sign-on](./media/sugarcrm-tutorial/tutorial_sugarcrm_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/sugarcrm-tutorial/tutorial_general_400.png)

1. **[Sugar CRM 構成]** セクションで **[Sugar CRM の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **クイック リファレンス セクション**から、**サインアウト URL と SAML シングル サインオン サービス URL** をコピーします。

    ![Configure single sign-on](./media/sugarcrm-tutorial/tutorial_sugarcrm_configure.png) 

1. 別の Web ブラウザー ウィンドウで、Sugar CRM 企業サイトに管理者としてログインします。

1. **[Admin]** に移動します。
   
    ![管理](./media/sugarcrm-tutorial/ic795888.png "Admin")

1. **[Administration]** セクションで、**[Password Management]** をクリックします。
   
    ![Administration](./media/sugarcrm-tutorial/ic795889.png "Administration")

1. **[Enable SAML Authentication]** を選択します。
   
    ![Administration](./media/sugarcrm-tutorial/ic795890.png "Administration")

1. **[SAML Authentication]** セクションで、次の手順に従います。
   
    ![SAML Authentication](./media/sugarcrm-tutorial/ic795891.png "SAML Authentication")  
 
    a. **[Login URL]\(ログイン URL\)** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。
  
    b. **[SLO URL]\(SLO URL\)** ボックスに、Azure Portal からコピーした**サインアウト URL** の値を貼り付けます。
  
    c. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、証明書全体を **[X.509 Certificate]** テキスト ボックスに貼り付けます。
  
    d. **[Save]** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/sugarcrm-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/sugarcrm-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/sugarcrm-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/sugarcrm-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-sugar-crm-test-user"></a>Sugar CRM テスト ユーザーの作成

Azure AD ユーザーが Sugar CRM にログインできるようにするには、そのユーザーを Sugar CRM にプロビジョニングする必要があります。

Sugar CRM の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **Spring CM** 企業サイトに管理者としてログインします。

1. **[Admin]** に移動します。
   
    ![管理](./media/sugarcrm-tutorial/ic795888.png "Admin")

1. **[Administration]** セクションで、**[User Management]** をクリックします。
   
    ![Administration](./media/sugarcrm-tutorial/ic795893.png "Administration")

1. **[Users]\>[Create New User]** の順に選択します。
   
    ![Create New User](./media/sugarcrm-tutorial/ic795894.png "Create New User")

1. **[User Profile]** タブで、次の手順に従います。
   
    ![New User](./media/sugarcrm-tutorial/ic795895.png "New User")

    a. 関連するテキスト ボックスに、プロビジョニングする有効な Azure Active Directory アカウントの**ユーザー名**、**姓**、および**メール アドレス**を入力します。
  
1. **[Status]** として、**[Active]** を選択します。

1. [Password] タブで、次の手順に従います。
   
    ![New User](./media/sugarcrm-tutorial/ic795896.png "New User")

    a. 該当するテキスト ボックスにパスワードを入力します。

    b. **[Save]** をクリックします。

>[!NOTE]
>Sugar CRM から提供されている他の Sugar CRM ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Sugar CRM へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Sugar CRM に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Sugar CRM]** を選びます。

    ![Configure single sign-on](./media/sugarcrm-tutorial/tutorial_sugarcrm_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。

アクセス パネルで Sugar CRM のタイルをクリックすると、自動的に Sugar CRM アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sugarcrm-tutorial/tutorial_general_01.png
[2]: ./media/sugarcrm-tutorial/tutorial_general_02.png
[3]: ./media/sugarcrm-tutorial/tutorial_general_03.png
[4]: ./media/sugarcrm-tutorial/tutorial_general_04.png

[100]: ./media/sugarcrm-tutorial/tutorial_general_100.png

[200]: ./media/sugarcrm-tutorial/tutorial_general_200.png
[201]: ./media/sugarcrm-tutorial/tutorial_general_201.png
[202]: ./media/sugarcrm-tutorial/tutorial_general_202.png
[203]: ./media/sugarcrm-tutorial/tutorial_general_203.png

