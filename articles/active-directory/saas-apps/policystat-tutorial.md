---
title: 'チュートリアル: Azure Active Directory と PolicyStat の統合 | Microsoft Docs'
description: Azure Active Directory と PolicyStat の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: af5eb0f1-1c8e-4809-b0c4-8ccfb915ca42
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 571b1723c1c064415e4d8cbee3620799af14d508
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39428266"
---
# <a name="tutorial-azure-active-directory-integration-with-policystat"></a>チュートリアル: Azure Active Directory と PolicyStat の統合

このチュートリアルでは、PolicyStat と Azure Active Directory (Azure AD) を統合する方法について説明します。

PolicyStat と Azure AD の統合には、次の利点があります。

- PolicyStat にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで PolicyStat に自動的にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

PolicyStat と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- PolicyStat でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから PolicyStat を追加する
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-policystat-from-the-gallery"></a>ギャラリーから PolicyStat を追加する
Azure AD への PolicyStat の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に PolicyStat を追加する必要があります。

**ギャラリーから PolicyStat を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**PolicyStat**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/policystat-tutorial/tutorial_policystat_search.png)

1. 結果ウィンドウで **[PolicyStat]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/policystat-tutorial/tutorial_policystat_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、PolicyStat で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する PolicyStat ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと PolicyStat の関連ユーザーの間で、リンク関係が確立されている必要があります。

PolicyStat で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

PolicyStat で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[PolicyStat テスト ユーザーの作成](#creating-a-policystat-test-user)** - PolicyStat で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、PolicyStat アプリケーションでシングル サインオンを構成します。

**PolicyStat で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **PolicyStat** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/policystat-tutorial/tutorial_policystat_samlbase.png)

1. **[PolicyStat のドメインと URL]** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/policystat-tutorial/tutorial_policystat_url.png)

    a. **[サインオン URL]** ボックスに、`https://<companyname>.policystat.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<companyname>.policystat.com/saml2/metadata/` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[PolicyStat クライアント サポート チーム](http://www.policystat.com/support/)に問い合わせてください。 
 
1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![Configure single sign-on](./media/policystat-tutorial/tutorial_policystat_certificate.png) 

1. このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで PolicyStat に対する認証を行えるようにする方法を説明します。

    PolicyStat アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを **[SAML トークン属性]** の構成に追加する必要があります。  

     次のスクリーンショットは、その例を示しています。

     ![属性](./media/policystat-tutorial/tutorial_policystat_attribute.png "Attributes")

1. 必要な属性のマッピングを追加するには、次の手順を実行します。

    | 属性名    |   属性値 |
    |------------------- | -------------------- |
    | uid | ExtractMailPrefix([mail]) |
    
    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![Configure single sign-on](./media/policystat-tutorial/tutorial_policystat_04.png)

    ![Configure single sign-on](./media/policystat-tutorial/tutorial_policystat_addatribute.png)
    
    b. **[属性名]** ボックスに、「**uid**」と入力します。

    c. **[属性値]** ボックスで、**[ExtractMailPrefix()]** を選択します。    
   
    d. **[メール]** 一覧で **[User.mail]** を選択します。
    
    e. **[OK]** をクリックします。

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/policystat-tutorial/tutorial_general_400.png)

1. 別の Web ブラウザーのウィンドウで、PolicyStat 企業サイトに管理者としてログインします。

1. **[Admin]** タブをクリックし、左側のナビゲーション ウィンドウの **[Single Sign-On Configuration]** をクリックします。
   
    ![管理者メニュー](./media/policystat-tutorial/ic808633.png "管理者メニュー")

1. **[Setup]** セクションで、**[Enable Single Sign-on Integration]** を選択します。
   
    ![シングル サインオンの構成](./media/policystat-tutorial/ic808634.png "シングル サインオンの構成")

1. **[Configure Attributes]** をクリックし、**[Configure Attributes]** セクションで次の手順に従います。
   
    ![シングル サインオンの構成](./media/policystat-tutorial/ic808635.png "シングル サインオンの構成")
   
    a. **[Username Attribute]** ボックスに、「**uid**」と入力します。

    b. **[First Name Attribute]\(名属性\)** ボックスにユーザー "**Britta**" の**名**を入力します。

    c. **[Last Name Attribute]\(姓属性\)** ボックスにユーザー "**Simon**" の**姓**を入力します。

    d. **[Email Attribute]\(電子メール属性\)** ボックスにユーザー **BrittaSimon@contoso.com** の**電子メール アドレス**を入力します。

    e. **[変更を保存]** をクリックします。

1. **[Your IDP Metadata]** をクリックし、**[Your IDP Metadata]** セクションで次の手順に従います。
   
    ![シングル サインオンの構成](./media/policystat-tutorial/ic808636.png "シングル サインオンの構成")
   
    a. ダウンロードしたメタデータ ファイルの内容をコピーし、**[Your Identity Provider Metadata]\(ID プロバイダーのメタデータ\)** テキスト ボックスに貼り付けます。

    b. **[変更を保存]** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/policystat-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/policystat-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/policystat-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/policystat-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-policystat-test-user"></a>PolicyStat テスト ユーザーの作成

Azure AD ユーザーが PolicyStat にログインできるようにするには、ユーザーを PolicyStat にプロビジョニングする必要があります。  

PolicyStat は、ジャストインタイム ユーザー プロビジョニングをサポートしています。 つまり、PolicyStat にユーザーを手動で追加する必要はありません。 ユーザーはシングル サインオン (SSO) の最初のログイン時に自動的に追加されます。

>[!NOTE]
>PolicyStat から提供されている他の PolicyStat ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。
> 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に PolicyStat へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**PolicyStat に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[PolicyStat]** を選択します。

    ![Configure single sign-on](./media/policystat-tutorial/tutorial_policystat_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [PolicyStat] タイルをクリックすると、自動的に PolicyStat アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/policystat-tutorial/tutorial_general_01.png
[2]: ./media/policystat-tutorial/tutorial_general_02.png
[3]: ./media/policystat-tutorial/tutorial_general_03.png
[4]: ./media/policystat-tutorial/tutorial_general_04.png

[100]: ./media/policystat-tutorial/tutorial_general_100.png

[200]: ./media/policystat-tutorial/tutorial_general_200.png
[201]: ./media/policystat-tutorial/tutorial_general_201.png
[202]: ./media/policystat-tutorial/tutorial_general_202.png
[203]: ./media/policystat-tutorial/tutorial_general_203.png

