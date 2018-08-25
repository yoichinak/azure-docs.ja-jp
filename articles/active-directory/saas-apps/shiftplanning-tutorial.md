---
title: 'チュートリアル: Azure Active Directory と Humanity の統合 | Microsoft Docs'
description: Azure Active Directory と Humanity の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 6aa771e9-31c6-48d1-8dde-024bebc06943
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2017
ms.author: jeedes
ms.openlocfilehash: 5858300027d77b6057e059960f1c997b4bfc1e56
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39429010"
---
# <a name="tutorial-azure-active-directory-integration-with-humanity"></a>チュートリアル: Azure Active Directory と Humanity の統合

このチュートリアルでは、Humanity と Azure Active Directory (Azure AD) を統合する方法について説明します。

Humanity と Azure AD の統合には、次の利点があります。

- Humanity にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Humanity にサインオン (シングル サインオン) できるようにすることが可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Humanity と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Humanity でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Humanity の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-humanity-from-the-gallery"></a>ギャラリーからの Humanity の追加
Azure AD への Humanity の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Humanity を追加する必要があります。

**ギャラリーから Humanity を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**Humanity**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/shiftplanning-tutorial/tutorial_humanity_search.png)

1. 結果ウィンドウで **[Humanity]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/shiftplanning-tutorial/tutorial_humanity_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Humanity で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Humanity ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Humanity の関連ユーザーの間で、リンク関係が確立されている必要があります。

Humanity で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

Humanity で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Humanity テスト ユーザーの作成](#creating-a-humanity-test-user)** - Humanity で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Humanity アプリケーションでシングル サインオンを構成します。

**Humanity で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Humanity** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/shiftplanning-tutorial/tutorial_humanity_samlbase.png)

1. **[Humanity のドメインと URL]** セクションで、次の手順に従います。

    ![Configure single sign-on](./media/shiftplanning-tutorial/tutorial_humanity_url.png)

    a. **[サインオン URL]** ボックスに、`https://company.humanity.com/includes/saml/` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://company.humanity.com/app/` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[Humanity クライアント サポート チーム](https://www.humanity.com/support/)に問い合わせてください。 
 
1. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure single sign-on](./media/shiftplanning-tutorial/tutorial_humanity_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/shiftplanning-tutorial/tutorial_general_400.png)

1. **[Humanity 構成]** セクションで、**[Humanity の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[Quick Reference]\(クイック リファレンス\)** セクションから **SAML シングル サインオン サービスの URL とサインアウト URL** をコピーします。

    ![Configure single sign-on](./media/shiftplanning-tutorial/tutorial_humanity_configure.png) 

1. 別の Web ブラウザー ウィンドウで、**Humanity** 企業サイトに管理者としてログインします。

1. 上部のメニューで **[Admin]** をクリックします。
   
    ![管理](./media/shiftplanning-tutorial/iC786619.png "Admin")

1. **[Integration]** の **[Single Sign-On]** をクリックします。
   
    ![シングル サインオン](./media/shiftplanning-tutorial/iC786620.png "Single Sign-On")

1. **[Single Sign-On Options]** セクションで、次の手順に従います。
   
    ![シングル サインオン](./media/shiftplanning-tutorial/iC786905.png "Single Sign-On")
   
    a. **[SAML Enabled]** を選択します。

    b. **[Allow Password Login]** を選択します。

    c. **SAML シングル サインオン サービス URL** 値を **[SAML Issuer URL]\(SAML 発行者 URL\)** ボックスに貼り付けます。

    d. **サインアウト URL** 値を **[Remote Logout URL]\(リモート ログアウト URL\)** ボックスに貼り付けます。
   
    e. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、 **[X.509 証明書]** テキストボックスに貼り付けます。

1. **[設定の保存]** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/shiftplanning-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/shiftplanning-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/shiftplanning-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/shiftplanning-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-humanity-test-user"></a>Humanity テスト ユーザーの作成

Azure AD ユーザーが Humanity にログインできるようにするには、ユーザーを Humanity にプロビジョニングする必要があります。 Humanity の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **Humanity** 企業サイトに管理者としてログインします。

1. **[Admin]** をクリックします。
   
    ![管理](./media/shiftplanning-tutorial/iC786619.png "Admin")

1. **[Staff]** をクリックします。
   
    ![Staff](./media/shiftplanning-tutorial/ic786623.png "Staff")

1. **[Actions]\(アクション\)** の **[Add Employee]\(従業員の追加\)** をクリックします。
   
    ![Add Employees](./media/shiftplanning-tutorial/iC786624.png "Add Employees")

1. **[Add Employees]** セクションで、次の手順に従います。
   
    ![Save Employees](./media/shiftplanning-tutorial/iC786625.png "Save Employees")
   
    a. プロビジョニングする有効な AAD アカウントの**姓**、**名**、**電子メール アドレス**を、対応するボックスに入力します。

    b. **[Save Employees]** をクリックします。

>[!NOTE]
>Humanity から提供されている他の Humanity ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Humanity へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を Humanity に割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Humanity]** を選択します。

    ![Configure single sign-on](./media/shiftplanning-tutorial/tutorial_humanity_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Humanity] タイルをクリックすると、Humanity アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/shiftplanning-tutorial/tutorial_general_01.png
[2]: ./media/shiftplanning-tutorial/tutorial_general_02.png
[3]: ./media/shiftplanning-tutorial/tutorial_general_03.png
[4]: ./media/shiftplanning-tutorial/tutorial_general_04.png

[100]: ./media/shiftplanning-tutorial/tutorial_general_100.png

[200]: ./media/shiftplanning-tutorial/tutorial_general_200.png
[201]: ./media/shiftplanning-tutorial/tutorial_general_201.png
[202]: ./media/shiftplanning-tutorial/tutorial_general_202.png
[203]: ./media/shiftplanning-tutorial/tutorial_general_203.png

