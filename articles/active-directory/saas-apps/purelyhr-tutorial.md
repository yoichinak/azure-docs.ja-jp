---
title: 'チュートリアル: Azure Active Directory と PurelyHR の統合 | Microsoft Docs'
description: Azure Active Directory と PurelyHR の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 86a9c454-596d-4902-829a-fe126708f739
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: jeedes
ms.openlocfilehash: c02dc274c5d22c16b2bda6d7896ee64c41d6e2ab
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39438996"
---
# <a name="tutorial-azure-active-directory-integration-with-purelyhr"></a>チュートリアル: Azure Active Directory と PurelyHR の統合

このチュートリアルでは、PurelyHR と Azure Active Directory (Azure AD) を統合する方法について説明します。

PurelyHR と Azure AD の統合には、次の利点があります。

- PurelyHR にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが Azure AD アカウントで自動的に PurelyHR にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

PurelyHR と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- PurelyHR でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの PurelyHR の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-purelyhr-from-the-gallery"></a>ギャラリーからの PurelyHR の追加
Azure AD への PurelyHR の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に PurelyHR を追加する必要があります。

**ギャラリーから PurelyHR を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**PurelyHR**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/purelyhr-tutorial/tutorial_purelyhr_search.png)

1. 結果ウィンドウで **[PurelyHR]** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/purelyhr-tutorial/tutorial_purelyhr_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、PurelyHR で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する PurelyHR ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと PurelyHR の関連ユーザーの間で、リンク関係が確立されている必要があります。

PurelyHR で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

PurelyHR で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[PurelyHR テスト ユーザーの作成](#creating-a-purelyhr-test-user)** - PurelyHR で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、PurelyHR アプリケーションでシングル サインオンを構成します。

**PurelyHR で Azure AD のシングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **PurelyHR** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/purelyhr-tutorial/tutorial_purelyhr_samlbase.png)

1. **[PurelyHR のドメインと URL]** セクションで、**IDP 開始モード**でアプリケーションを構成する場合は、次の手順に従います。

    ![Configure single sign-on](./media/purelyhr-tutorial/tutorial_purelyhr_url.png)
   
    **[応答 URL]** ボックスに、`https://<companyID>.purelyhr.com/sso-consume` のパターンを使用して URL を入力します。

1. **SP** 開始モードでアプリケーションを構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにします。

    ![Configure single sign-on](./media/purelyhr-tutorial/tutorial_purelyhr_url1.png)
    
    **[サインオン URL]** ボックスに、`https://<companyID>.purelyhr.com/sso-initiate` のパターンを使用して値を入力します。
     
    > [!NOTE]
    > これらは実際の値ではありません。 実際の応答 URLとサインオン URL でこれらの値を更新します。 これらの値を取得するには、[PurelyHR クライアント サポート チーム](http://support.purelyhr.com/)に連絡してください。 

1. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure single sign-on](./media/purelyhr-tutorial/tutorial_purelyhr_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/purelyhr-tutorial/tutorial_general_400.png)
    
1. **[PurelyHR Configuration (PurelyHR 構成)]** セクションで、**[Configure PurelyHR (PurelyHR を構成する)]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**SAML エンティティ ID と SAML シングル サインオン サービス URL** をコピーします。

    ![Configure single sign-on](./media/purelyhr-tutorial/tutorial_purelyhr_configure.png) 

1. **PurelyHR** 側でシングル サインオンを構成するために、PurelyHR の Web サイトに管理者としてログインします。

1. ツール バーのオプションから**ダッシュボード**を開き、**[SSO Settings]** をクリックします。

1. 以下の説明に従って、各ボックスに値を貼り付けます。

    ![Configure single sign-on](./media/purelyhr-tutorial/purelyhr-dashboard-sso-settings.png)  

    a. Azure Portal からダウンロードした **Certificate(Bas64)** をメモ帳で開き、証明書の値をコピーします。 コピーした値を **[X.509 Certificate]** ボックスに貼り付けます。

    b. **[Idp Issuer URL]** ボックスに、Azure Portal でコピーした **SAML エンティティ ID** を貼り付けます。

    c. **[Idp Endpoint URL]** ボックスに、Azure Portal でコピーした **SAML シングル サインオン サービス URL** を貼り付けます。 

    d. **[Auto-Create Users]** チェック ボックスをオンにして、PurelyHR への自動ユーザー プロビジョニングを有効にします。

    e. **[Save Changes]** をクリックして設定を保存します。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/purelyhr-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/purelyhr-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/purelyhr-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/purelyhr-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-purelyhr-test-user"></a>PurelyHR テスト ユーザーの作成

Azure AD ユーザーが PurelyHR にログインできるようにするには、ユーザーを PurelyHR にプロビジョニングする必要があります。 PurelyHR では、プロビジョニングは自動的に行われます。自動ユーザー プロビジョニングが有効になっている場合、手動の手順は不要です。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に PurelyHR へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を PurelyHR に割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[PurelyHR]** を選択します。

    ![Configure single sign-on](./media/purelyhr-tutorial/tutorial_purelyhr_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで PurelyHR のタイルをクリックすると、自動的に PurelyHR アプリケーションにサインオンします。

アクセス パネルの詳細については、 [アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/purelyhr-tutorial/tutorial_general_01.png
[2]: ./media/purelyhr-tutorial/tutorial_general_02.png
[3]: ./media/purelyhr-tutorial/tutorial_general_03.png
[4]: ./media/purelyhr-tutorial/tutorial_general_04.png

[100]: ./media/purelyhr-tutorial/tutorial_general_100.png

[200]: ./media/purelyhr-tutorial/tutorial_general_200.png
[201]: ./media/purelyhr-tutorial/tutorial_general_201.png
[202]: ./media/purelyhr-tutorial/tutorial_general_202.png
[203]: ./media/purelyhr-tutorial/tutorial_general_203.png

