---
title: 'チュートリアル: Azure Active Directory と Birst Agile Business Analytics の統合 | Microsoft Docs'
description: Azure Active Directory と Birst Agile Business Analytics の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 677183b1-5348-4302-88cc-5c8ab63a3c6c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: f3c9ff07b0cbb7b3f7aa6a23887ef86a0b53af0e
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39427494"
---
# <a name="tutorial-azure-active-directory-integration-with-birst-agile-business-analytics"></a>チュートリアル: Azure Active Directory と Birst Agile Business Analytics の統合

このチュートリアルでは、Birst Agile Business Analytics と Azure Active Directory (Azure AD) を統合する方法について説明します。

Birst Agile Business Analytics と Azure AD の統合には、次の利点があります。

- Birst Agile Business Analytics にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Birst Agile Business Analytics にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Birst Agile Business Analytics の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Birst Agile Business Analytics でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから Birst Agile Business Analytics を追加する
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-birst-agile-business-analytics-from-the-gallery"></a>ギャラリーから Birst Agile Business Analytics を追加する
Azure AD への Birst Agile Business Analytics の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Birst Agile Business Analytics を追加する必要があります。

**ギャラリーから Birst Agile Business Analytics を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「 **Birst Agile Business Analytics**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/birst-tutorial/tutorial_birst_search.png)

1. 結果ウィンドウで **[Birst Agile Business Analytics]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/birst-tutorial/tutorial_birst_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" という名前のテスト ユーザーに基づいて、Birst Agile Business Analytics で Azure AD のシングル サインオンを構成およびテストします。

シングル サインオンを機能させるには、Azure AD が Azure AD のユーザーに対応する Birst Agile Business Analytics のユーザーを認識している必要があります。 言い換えると、Azure AD ユーザーと Birst Agile Business Analytics の関連ユーザーの間で、リンク関係が確立されている必要があります。

Birst Agile Business Analytics で、Azure AD での **[ユーザー名]** の値を **[ユーザー名]** の値として割り当てることによってリンク関係を確立します。

Birst Agile Business Analytics で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Birst Agile Business Analytics テスト ユーザーの作成](#creating-a-birst-agile-business-analytics-test-user)** - Azure AD でのユーザーにリンクされた、Birst Agile Business Analytics での Britta Simon の対応するユーザーを作成します。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Birst Agile Business Analytics アプリケーションでシングル サインオンを構成します。

**Birst Agile Business Analytics で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **[Birst Agile Business Analytics]** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/birst-tutorial/tutorial_birst_samlbase.png)

1. **[Birst Agile Business Analytics Domain and URLs] \(Birst Agile Business Analytics のドメインと URL)** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/birst-tutorial/tutorial_birst_url.png)

     **[サインオン URL]** ボックスに、`https://login.bws.birst.com/SAMLSSO/Services.aspx?birst.idpid=TENANTIDPID` のパターンを使用して URL を入力します。

     この URL は、Birst アカウントが存在するデータセンターによって異なります。 

     * 米国のデータセンターでは、`https://login.bws.birst.com/SAMLSSO/Services.aspx?birst.idpid=TENANTIDPID` というパターンを使用します。 

     * ヨーロッパのデータセンターでは、`https://login.eu1.birst.com/SAMLSSO/Services.aspx?birst.idpid=TENANTIDPID` というパターンを使用します。

    > [!NOTE] 
    > これは実際の値ではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Birst Agile Business Analytics クライアント サポート チーム](mailto:info@birst.com)に問い合わせてください。 
 
1. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure single sign-on](./media/birst-tutorial/tutorial_birst_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/birst-tutorial/tutorial_general_400.png)

1. **[Birst Agile Business Analytics Configuration] \(Birst Agile Business Analytics 構成)** セクションで、 **[Configure Birst Agile Business Analytics] \(Birst Agile Business Analytics の構成)** をクリックして **[Configure sign-on] \(サインオンの構成)** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![Configure single sign-on](./media/birst-tutorial/tutorial_birst_configure.png) 

1. **Birst Agile Business Analytics** 側にシングル サインオンを構成するには、ダウンロードされた**証明書 (Base64)**、**サインアウト URL、SAML エンティティ ID、および SAML シングル サインオン サービス URL** を [Birst Agile Business Analytics サポート チーム](mailto:info@birst.com)に送信する必要があります。 

    > [!NOTE]
    > Birst チームが適切なサーバー (**app2101** など) 上で SSO を設定できるように、この統合には SHA256 アルゴリズムが必要なことをチームに伝えてください (SHA1 はサポートされません)。
  

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/birst-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/birst-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/birst-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/birst-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-birst-agile-business-analytics-test-user"></a>Birst Agile Business Analytics テスト ユーザーの作成

このセクションの目的は、Birst Agile Business Analytics で Britta Simon というユーザーを作成することです。 [Birst Agile Business Analytics サポート チーム](mailto:info@birst.com)と協力して、Birst アカウントでユーザーを追加します。 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Birst Agile Business Analytics へのアクセス権を付与することによって、Britta Simon が Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Birst Agile Business Analytics に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で、 **[Birst Agile Business Analytics]** を選択します。

    ![Configure single sign-on](./media/birst-tutorial/tutorial_birst_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD の SSO 構成をテストすることです。

アクセス パネルで Birst Agile Business Analytics タイルをクリックすると、Birst Agile Business Analytics アプリケーションに自動的にサインオンするはずです。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/birst-tutorial/tutorial_general_01.png
[2]: ./media/birst-tutorial/tutorial_general_02.png
[3]: ./media/birst-tutorial/tutorial_general_03.png
[4]: ./media/birst-tutorial/tutorial_general_04.png

[100]: ./media/birst-tutorial/tutorial_general_100.png

[200]: ./media/birst-tutorial/tutorial_general_200.png
[201]: ./media/birst-tutorial/tutorial_general_201.png
[202]: ./media/birst-tutorial/tutorial_general_202.png
[203]: ./media/birst-tutorial/tutorial_general_203.png

