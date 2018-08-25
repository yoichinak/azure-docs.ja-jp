---
title: 'チュートリアル: Azure Active Directory と People の統合 | Microsoft Docs'
description: Azure Active Directory と People の間でシングル サインオンを構成する方法について確認します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 7c9b6202-11dd-4bb6-a679-8fb0a7a0ef4e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: jeedes
ms.openlocfilehash: eac41b0c3def42f2417e7c033c645d8785a5f08b
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431370"
---
# <a name="tutorial-azure-active-directory-integration-with-people"></a>チュートリアル: Azure Active Directory と People の統合

このチュートリアルでは、People と Azure Active Directory (Azure AD) を統合する方法について説明します。

People と Azure AD の統合には、次の利点があります。

- People にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に People にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

People と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- People でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの People の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-people-from-the-gallery"></a>ギャラリーからの People の追加
Azure AD への People の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に People を追加する必要があります。

**ギャラリーから People を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「 **People**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/people-tutorial/tutorial_people_search.png)

1. 結果パネルで **[People]** を選択し、**[追加]** ボタンをクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/people-tutorial/tutorial_people_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、People で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する People ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと People の関連ユーザーの間で、リンク関係が確立されている必要があります。

People で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

People で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[People テスト ユーザーの作成](#creating-a-people-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを People で作成します。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、People アプリケーションでシングル サインオンを構成します。

**People で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **People** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/people-tutorial/tutorial_people_samlbase.png)

1. **[People のドメインと URL]** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/people-tutorial/tutorial_people_url.png)

    a. **[サインオン URL]** ボックスに、`https://<company name>.peoplehr.net` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに次の URL を入力します。`https://www.peoplehr.com`

    c. **[応答 URL]** ボックスに、`https://<company name>.peoplehr.net/Pages/Saml/ConsumeAzureAD.aspx` のパターンを使用して URL を入力します。
    
    > [!NOTE]
    > これらは実際の値ではありません。 実際の応答 URLとサインオン URL でこれらの値を更新します。 これらの値を取得するには、[People クライアント サポート チーム](mailto:customerservices@peoplehr.com)に連絡してください。 

1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![Configure single sign-on](./media/people-tutorial/tutorial_people_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/people-tutorial/tutorial_general_400.png)
    
1. アプリケーションに合わせて SSO を構成するには、管理者として People テナントにサインオンする必要があります。
   
1. 左側にあるメニューで、**[設定]** をクリックします。

    ![Configure single sign-on](./media/people-tutorial/tutorial_people_001.png)

1. **[会社]** をクリックします。

    ![Configure single sign-on](./media/people-tutorial/tutorial_people_002.png)

1. **[シングル サインオン SAML メタデータ ファイルのアップロード]** で **[参照]** をクリックし、ダウンロードしたメタデータ ファイルをアップロードします。

    ![Configure single sign-on](./media/people-tutorial/tutorial_people_003.png)

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/people-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/people-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/people-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/people-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-people-test-user"></a>People テスト ユーザーの作成

このセクションでは、People で Britta Simon というユーザーを作成します。 [People クライアント サポート チーム](mailto:customerservices@peoplehr.com)と協力して、People プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に People へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**People に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[People]** を選択します。

    ![Configure single sign-on](./media/people-tutorial/tutorial_people_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD の SSO 構成をテストすることです。

アクセス パネルで [People] タイルをクリックすると、自動的に People アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/people-tutorial/tutorial_general_01.png
[2]: ./media/people-tutorial/tutorial_general_02.png
[3]: ./media/people-tutorial/tutorial_general_03.png
[4]: ./media/people-tutorial/tutorial_general_04.png

[100]: ./media/people-tutorial/tutorial_general_100.png

[200]: ./media/people-tutorial/tutorial_general_200.png
[201]: ./media/people-tutorial/tutorial_general_201.png
[202]: ./media/people-tutorial/tutorial_general_202.png
[203]: ./media/people-tutorial/tutorial_general_203.png

