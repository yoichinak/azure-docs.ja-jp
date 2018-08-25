---
title: 'チュートリアル: Azure Active Directory と Optimizely の統合 | Microsoft Docs'
description: Azure Active Directory と Optimizely の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 28ef03e1-9aad-4301-af97-d94e853edc74
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 872a441df795e742ac884f97843f179a782aec0d
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421605"
---
# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>チュートリアル: Azure Active Directory と Optimizely の統合

このチュートリアルでは、Optimizely と Azure Active Directory (Azure AD) を統合する方法について説明します。

Optimizely と Azure AD の統合には、次の利点があります。

- Optimizely にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが各自の Azure AD アカウントで Optimizely に自動的にサインオン (シングル サインオン) するように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Optimizely の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Optimizely でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Optimizely の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-optimizely-from-the-gallery"></a>ギャラリーからの Optimizely の追加
Azure AD への Optimizely の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Optimizely を追加する必要があります。

**ギャラリーから Optimizely を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「 **Optimizely**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/optimizely-tutorial/tutorial_optimizely_search.png)

1. 結果ウィンドウで **Optimizely** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/optimizely-tutorial/tutorial_optimizely_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Optimizely で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Optimizely ユーザーが Azure AD で認識されている必要があります。 つまり、Azure AD ユーザーと Optimizely の関連ユーザーの間でリンク関係が確立されている必要があります。

このリンク関係は、Azure AD の **[ユーザー名]** の値を、Optimizely の **[Username]** の値として割り当てることで確立されます。

Optimizely で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Optimizely のテスト ユーザーの作成](#creating-an-optimizely-test-user)** - Optimizely で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Optimizely アプリケーションでシングル サインオンを構成します。

**Optimizely で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **Optimizely** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/optimizely-tutorial/tutorial_optimizely_samlbase.png)

1. **[Optimizely のドメインと URL]** セクションで、次の手順に従います。

    ![Configure single sign-on](./media/optimizely-tutorial/tutorial_optimizely_url.png)

    a. **[サインオン URL]** ボックスに、`https://app.optimizely.net/<instance name>` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`urn:auth0:optimizely:contoso` のパターンで URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子に値を置き換えます。実際の値については後で説明します。 

1. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure single sign-on](./media/optimizely-tutorial/tutorial_optimizely_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/optimizely-tutorial/tutorial_general_400.png)

1. **[Optimizely 構成]** セクションで、**[Optimizely の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![Configure single sign-on](./media/optimizely-tutorial/tutorial_optimizely_configure.png) 

1. **Optimizely** 側にシングル サインオンを構成するには、Optimizely アカウント マネージャーに連絡し、ダウンロードした **証明書 (Base64)** と **SAML シングル サインオン サービス URL** を提供します。 

1. 電子メールに応じて、Optimizely では、[サインオン URL] \(SP によって開始された SSO) と [識別子] \(サービス プロバイダーのエンティティ ID) の値が提供されます。

    a. Optimizely によって提供された **SP 開始 SSO URL** をコピーし、Azure Portal の **[Optimizely のドメインと URL]** セクションの **[サインオン URL]** ボックスに貼り付けます 

    b. Optimizely によって提供された **サービス プロバイダー エンティティ ID** をコピーし、Azure Portal の **[Optimizely のドメインと URL]** セクションの **[識別子]** ボックスに貼り付けます 

1. 別のブラウザー ウィンドウで、Optimizely アプリケーションにサインオンします。

1. 右上隅のアカウント名をクリックし、 **[アカウント設定]** に移動します。
   
    ![Azure AD Single Sign-On](./media/optimizely-tutorial/tutorial_optimizely_09.png)

1. [アカウント] タブで、**[概要]** セクションの [シングル サインオン] にある **[Enable SSO (SSO の有効化)]** チェック ボックスをオンにします。
   
    ![Azure AD Single Sign-On](./media/optimizely-tutorial/tutorial_optimizely_10.png)
    
1. **[保存]**

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/optimizely-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/optimizely-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/optimizely-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/optimizely-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、Britta Simon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-an-optimizely-test-user"></a>Optimizely のテスト ユーザーの作成

このセクションでは、Optimizely で Britta Simon というユーザーを作成します。

1. ホーム ページで、**[コラボレーター]** タブを選択します。

1. 新しいコラボレーターをプロジェクトに追加するには、**[New Collaborator]\(新しいコラボレーター\)** クリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/optimizely-tutorial/create_aaduser_10.png)

1. 電子メール アドレスを入力して、役割を割り当てます。 **[招待]** をクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/optimizely-tutorial/create_aaduser_11.png)

1. 電子メールの招待を受信します。 メール アドレスを使用すると、Optimizely にログインする必要があります。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Optimizely へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようします。

![ユーザーの割り当て][200] 

**Britta Simon を Optimizely に割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Optimizely]** を選択します。

    ![Configure single sign-on](./media/optimizely-tutorial/tutorial_optimizely_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Optimizely] タイルをクリックすると、Optimizely アプリケーションに自動的にサインオンします。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/optimizely-tutorial/tutorial_general_01.png
[2]: ./media/optimizely-tutorial/tutorial_general_02.png
[3]: ./media/optimizely-tutorial/tutorial_general_03.png
[4]: ./media/optimizely-tutorial/tutorial_general_04.png

[100]: ./media/optimizely-tutorial/tutorial_general_100.png

[200]: ./media/optimizely-tutorial/tutorial_general_200.png
[201]: ./media/optimizely-tutorial/tutorial_general_201.png
[202]: ./media/optimizely-tutorial/tutorial_general_202.png
[203]: ./media/optimizely-tutorial/tutorial_general_203.png

