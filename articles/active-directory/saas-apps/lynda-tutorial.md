---
title: 'チュートリアル: Azure Active Directory と Lynda.com の統合 | Microsoft Docs'
description: Azure Active Directory と Lynda.com の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f6c92789-8b64-4049-bac9-8cb928398433
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: jeedes
ms.openlocfilehash: 12f61e30321514b6e1283e04c5723d9fe2a4e054
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449606"
---
# <a name="tutorial-azure-active-directory-integration-with-lyndacom"></a>チュートリアル: Azure Active Directory と Lynda.com の統合

このチュートリアルでは、Lynda.com と Azure Active Directory (Azure AD) を統合する方法について説明します。

Lynda.com と Azure AD の統合には、次の利点があります。

- Lynda.com にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に Lynda.com にサインオン (シングル サインオン) できるようにします
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Lynda.com と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Lynda.com でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Lynda.com の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-lyndacom-from-the-gallery"></a>ギャラリーからの Lynda.com の追加
Azure AD への Lynda.com の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Lynda.com を追加する必要があります。

**ギャラリーから Lynda.com を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**Lynda.com**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/lynda-tutorial/tutorial_lynda.com_search.png)

1. 結果ウィンドウで **Lynda.com** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/lynda-tutorial/tutorial_lynda.com_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Lynda.com で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Lynda.com ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Lynda.com の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Lynda.com の **[Username]** の値として割り当てます。

Lynda.com で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Lynda.com テスト ユーザーの作成](#creating-a-lyndacom-test-user)** - Lynda.com で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Lynda.com アプリケーションでシングル サインオンを構成します。

**Lynda.com で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Lynda.com** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/lynda-tutorial/tutorial_lynda.com_samlbase.png)

1. **[Lynda.com のドメインと URL]** セクションで、次の手順に従います。

    ![Configure single sign-on](./media/lynda-tutorial/tutorial_lynda.com_url.png)

    **[サインオン URL]** ボックスに、`https://<subdomain>.lynda.com/Shibboleth.sso/InCommon?providerId=<url>&target=<url> ` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これは実際の値ではありません。 実際のサインオン URL でこの値を更新してください。 これらの値を取得するには、[Lynda.com クライアント サポート チーム](https://www.linkedin.com/help/lynda/ask)に問い合わせてください。 
 
1. **[SAML 署名証明書]** セクションで、**[メタデータ XML]** をクリックし、コンピューターに XML ファイルを保存します。

    ![Configure single sign-on](./media/lynda-tutorial/tutorial_lynda.com_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/lynda-tutorial/tutorial_general_400.png)

1. **Lynda.com** 側にシングル サインオンを構成するには、ダウンロードした**メタデータ XML** を [Lynda.com サポート](https://www.linkedin.com/help/lynda/ask)に送信する必要があります。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/lynda-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/lynda-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/lynda-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/lynda-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-lyndacom-test-user"></a>Lynda.com テスト ユーザーの作成

Lynda.com へのユーザー プロビジョニングの構成にあたって必要な操作はありません。  
割り当て済みユーザーがアクセス パネルを使用して Lynda.com にログインしようとすると、そのユーザーが存在するかどうかが Lynda.com によって確認されます。  

使用可能なユーザー アカウントがない場合、ユーザー アカウントは Lynda.com により自動的に作成されます。

>[!NOTE]
>Lynda.com から提供されている他の Lynda.com ユーザー アカウント作成ツールや API を使用して、AAD ユーザー アカウントをプロビジョニングできます。 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Lynda.com へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Lynda.com に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Lynda.com]** を選択します。

    ![Configure single sign-on](./media/lynda-tutorial/tutorial_lynda.com_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/lynda-tutorial/tutorial_general_01.png
[2]: ./media/lynda-tutorial/tutorial_general_02.png
[3]: ./media/lynda-tutorial/tutorial_general_03.png
[4]: ./media/lynda-tutorial/tutorial_general_04.png

[100]: ./media/lynda-tutorial/tutorial_general_100.png

[200]: ./media/lynda-tutorial/tutorial_general_200.png
[201]: ./media/lynda-tutorial/tutorial_general_201.png
[202]: ./media/lynda-tutorial/tutorial_general_202.png
[203]: ./media/lynda-tutorial/tutorial_general_203.png

