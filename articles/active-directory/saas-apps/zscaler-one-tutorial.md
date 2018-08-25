---
title: 'チュートリアル: Azure Active Directory と Zscaler One の統合 | Microsoft Docs'
description: Azure Active Directory と Zscaler One の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f352e00d-68d3-4a77-bb92-717d055da56f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: a7a6abe3a9af98574ffd0a00d21fd0247785f7a5
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39438292"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-one"></a>チュートリアル: Azure Active Directory と Zscaler One の統合

このチュートリアルでは、Zscaler One と Azure Active Directory (Azure AD) を統合する方法について説明します。

Zscaler One と Azure AD の統合には、次の利点があります。

- Zscaler One にアクセスするユーザーを Azure AD で管理できます
- ユーザーが Azure AD アカウントで Zscaler One に自動的にサインオン (シングル サインオン) できるように設定することが可能です
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Zscaler One と Azure AD の統合を構成するには、次の項目が必要です。

- Azure AD サブスクリプション
- Zscaler One でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Zscaler One の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-zscaler-one-from-the-gallery"></a>ギャラリーからの Zscaler One の追加
Azure AD への Zscaler One の統合を構成するには、管理対象の SaaS アプリ一覧に Zscaler One をギャラリーから追加する必要があります。

**ギャラリーから Zscaler One を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**Zscaler One**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/zscaler-one-tutorial/tutorial_zscalerone_search.png)

1. 結果ウィンドウで **Zscaler One** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/zscaler-one-tutorial/tutorial_zscalerone_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Zscaler One で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Zscaler One ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Zscaler One の関連ユーザーの間で、リンク関係が確立されている必要があります。

Zscaler One で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

Zscaler One で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[プロキシ設定の構成](#configuring-proxy-settings)** - Internet Explorer でプロキシ設定を構成します
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Zscaler One のテスト ユーザーの作成](#creating-a-zscaler-one-test-user)** - Zscaler One で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Zscaler One アプリケーションでシングル サインオンを構成します。

**Zscaler One で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Zscaler One** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/zscaler-one-tutorial/tutorial_zscalerone_samlbase.png)

1. **[Zscaler One のドメインと URL]** セクションで、次の手順に従います。

    ![Configure single sign-on](./media/zscaler-one-tutorial/tutorial_zscalerone_url.png)

    [サインオン URL] ボックスに、ユーザーが Zscaler One アプリケーションへのサインオンに使用する URL を入力します。

    > [!NOTE] 
    > この値は実際のサインオン URL で更新する必要があります。 これらの値を取得するには、[Zscaler One クライアント サポート チーム](https://www.zscaler.com/company/contact)に問い合わせてください。

1. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure single sign-on](./media/zscaler-one-tutorial/tutorial_zscalerone_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/zscaler-one-tutorial/tutorial_general_400.png)

1. **[Zscaler One 構成]** セクションで、**[Zscaler One の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![Configure single sign-on](./media/zscaler-one-tutorial/tutorial_zscalerone_configure.png) 

1. 別の Web ブラウザー ウィンドウで、Zscaler One 企業サイトに管理者としてログインします。

1. 上部のメニューで **[管理]** をクリックします。
   
    ![Administration](./media/zscaler-one-tutorial/ic800206.png "Administration")

1. **[管理者とロールの管理]** をクリックし、**[ユーザーと認証の管理]** をクリックします。   
            
    ![ユーザーと認証の管理](./media/zscaler-one-tutorial/ic800207.png "Manage Users & Authentication")

1. **[組織の認証オプションの選択]** セクションで、次の手順を実行します。   
                
    ![Authentication](./media/zscaler-one-tutorial/ic800208.png "Authentication")
   
    a. **[SAML シングル サインオンを使用した認証]** を選択します。

    b. **[SAML シングル サインオン パラメーターの構成]** をクリックします。

1. **[SAML シングル サインオン パラメーターの構成]** ダイアログ ページで、次の手順に従い、**[完了]** をクリックします

    ![シングル サインオン](./media/zscaler-one-tutorial/ic800209.png "Single Sign-On")
    
    a. Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を、**[URL of the SAML Portal to which users are sent for authentication]\(ユーザーが認証に送られる SAML ポータルの URL\)** ボックスに貼り付けます。
    
    b. **[ログイン名を含む属性]** ボックスに「**NameID**」と入力します。
    
    c. ダウンロードした証明書をアップロードするには、 **[Zscaler pem]** をクリックします。
    
    d. **[SAML 自動プロビジョニングを有効にする]** を選択します。

1. **[ユーザー認証の構成]** ダイアログ ページで、次の手順に従います。

    ![Administration](./media/zscaler-one-tutorial/ic800210.png "Administration")
    
    a. **[Save]** をクリックします。

    b. **[今すぐ認証する]** をクリックします。

## <a name="configuring-proxy-settings"></a>プロキシ設定の構成
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Internet Explorer でプロキシ設定を構成するには

1. **Internet Explorer**を開始します。

1. **[ツール]** メニューの **[インターネット オプション]** を選択し、**[インターネット オプション]** ダイアログを開きます。   
    
     ![インターネット オプション](./media/zscaler-one-tutorial/ic769492.png "Internet Options")

1. **[接続]** タブをクリックします。   
  
     ![接続](./media/zscaler-one-tutorial/ic769493.png "Connections")

1. **[LAN の設定]** をクリックして **[LAN の設定]** ダイアログを開きます。

1. [プロキシ サーバー] セクションで、次の手順を実行します。   
   
    ![プロキシ サーバー](./media/zscaler-one-tutorial/ic769494.png "Proxy server")

    a. **[LAN にプロキシ サーバーを使用する]** をオンにします。

    b. [アドレス] ボックスに「 **gateway.zscalerone.net**」と入力します。

    c. [ポート] ボックスに「 **80**」と入力します。

    d. **[ローカル アドレスにはプロキシ サーバーを使用しない]** を選択します。

    e. **[OK]** をクリックして **[ローカル エリア ネットワーク (LAN) の設定]** ダイアログを閉じます。

1. **[OK]** をクリックして **[インターネット オプション]** ダイアログを閉じます。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/zscaler-one-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/zscaler-one-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/zscaler-one-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/zscaler-one-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-zscaler-one-test-user"></a>Zscaler One テスト ユーザーの作成

Azure AD ユーザーが Zscaler One にログインできるようにするには、そのユーザーを Zscaler One にプロビジョニングする必要があります。 Zscaler One の場合、プロビジョニングは手動で行います。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>ユーザー プロビジョニングを構成するには、次の手順に従います。

1. **Zscaler One** テナントにログインします。

1. **[管理]** をクリックします。   
   
    ![Administration](./media/zscaler-one-tutorial/ic781035.png "Administration")

1. **[ユーザー管理]** をクリックします。   
        
     ![Add](./media/zscaler-one-tutorial/ic781036.png "Add")

1. **[ユーザー]** タブで、**[追加]** をクリックします。
      
    ![Add](./media/zscaler-one-tutorial/ic781037.png "Add")

1. [ユーザーの追加] セクションで、次の手順を実行します。
        
    ![ユーザーの追加](./media/zscaler-one-tutorial/ic781038.png "Add User")
   
    a. プロビジョニングする有効な Azure AD アカウントの **[UserID]\(ユーザー ID\)**、**[User Display Name]\(ユーザー表示名\)**、**[Password]\(パスワード\)**、**[Confirm Password]\(確認パスワード\)** を入力し、**[Groups]\(グループ\)** と **[Department]\(部署\)** を選びます。

    b. **[Save]** をクリックします。

> [!NOTE]
> Zscaler One から提供されている他の Zscaler One ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Zscaler One へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を Zscaler One に割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で、**[Zscaler One]** を選択します。

    ![Configure single sign-on](./media/zscaler-one-tutorial/tutorial_zscalerone_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Zscaler One のタイルをクリックすると、自動的に Zscaler One アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/zscaler-one-tutorial/tutorial_general_01.png
[2]: ./media/zscaler-one-tutorial/tutorial_general_02.png
[3]: ./media/zscaler-one-tutorial/tutorial_general_03.png
[4]: ./media/zscaler-one-tutorial/tutorial_general_04.png

[100]: ./media/zscaler-one-tutorial/tutorial_general_100.png

[200]: ./media/zscaler-one-tutorial/tutorial_general_200.png
[201]: ./media/zscaler-one-tutorial/tutorial_general_201.png
[202]: ./media/zscaler-one-tutorial/tutorial_general_202.png
[203]: ./media/zscaler-one-tutorial/tutorial_general_203.png

