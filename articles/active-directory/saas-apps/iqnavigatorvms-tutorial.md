---
title: 'チュートリアル: Azure Active Directory と IQNavigator VMS の統合 | Microsoft Docs'
description: Azure Active Directory と IQNavigator VMS の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: a8a09b25-dfa5-4c31-aea2-53bf1853b365
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: jeedes
ms.openlocfilehash: f568f33de348289334c4b4c346e9525e28cce51c
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39445124"
---
# <a name="tutorial-azure-active-directory-integration-with-iqnavigator-vms"></a>チュートリアル: Azure Active Directory と IQNavigator VMS の統合

このチュートリアルでは、IQNavigator VMS と Azure Active Directory (Azure AD) を統合する方法について説明します。

IQNavigator VMS と Azure AD の統合には、次の利点があります。

- IQNavigator VMS にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に IQNavigator VMS にサインオン (シングル サインオン) できるようにします
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

IQNavigator VMS と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- IQNavigator VMS でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの IQNavigator VMS の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-iqnavigator-vms-from-the-gallery"></a>ギャラリーからの IQNavigator VMS の追加
Azure AD への IQNavigator VMS の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に IQNavigator VMS を追加する必要があります。

**ギャラリーから IQNavigator VMS を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに入力「**IQNavigator VMS**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_search.png)

1. 結果ウィンドウで **IQNavigator VMS** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、IQNavigator VMS で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する IQNavigator VMS ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと IQNavigator VMS の関連ユーザーの間で、リンク関係が確立されている必要があります。

IQNavigator VMS で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

IQNavigator VMS で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[IQNavigator VMS テスト ユーザーの作成](#creating-a-iqnavigator-vms-test-user)** - IQNavigator VMS で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、IQNavigator VMS アプリケーションでシングル サインオンを構成します。

**IQNavigator VMS で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **IQNavigator VMS** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![Configure single sign-on](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_samlbase.png)

1. **[IQNavigator VMS のドメインと URL]** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_url.png)

    a. **[識別子]** ボックスに、URL として「`iqn.com`」と入力します。

    b. **[応答 URL]** ボックスに、`https://<subdomain>.iqnavigator.com/security/login?client_name=https://sts.window.net/<instance name>` のパターンを使用して URL を入力します。

1. **[詳細な URL 設定の表示]** をオンにして、次の手順を実行します。

    ![Configure single sign-on](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_url1.png)

    **[リレー状態]** ボックスに、`https://<subdomain>.iqnavigator.com` のパターンで URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を実際の応答 URL とリレー状態で更新してください。 これらの値を取得するには、[IQNavigator VMS クライアント サポート チーム](https://www.beeline.com/iqn-product-support/)に問い合わせてください。

1. **[SAML 署名証明書]** セクションで、コピー ボタンをクリックして **[App Federation Metadata Url]\(アプリケーション フェデレーション メタデータ URL\)** をコピーし、メモ帳に貼り付けます。
    
    ![Configure single sign-on](./media/iqnavigatorvms-tutorial/tutorial_metadataurl.png)

1. IQNavigator アプリケーションでは、名前識別子の要求で一意のユーザー識別子の値が必要です。 顧客は、名前識別子要求の適切な値をマップできます。 ここでは、デモのために user.UserPrincipalName をマップしました。 ただし、組織の設定に従って、正しい値をマップする必要があります。

    ![Configure single sign-on](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_attribute.png)

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/iqnavigatorvms-tutorial/tutorial_general_400.png)

1. **[IQNavigator VMS 構成]** セクションで、**[IQNavigator VMS の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![Configure single sign-on](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_configure.png)

1. **IQNavigator VMS** 側でシングル サインオンを構成するには、**アプリのフェデレーション メタデータ URL**、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** を [IQNavigator VMS サポート チーム](https://www.beeline.com/iqn-product-support/)に送る必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/iqnavigatorvms-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/iqnavigatorvms-tutorial/create_aaduser_02.png)

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。

    ![Azure AD のテスト ユーザーの作成](./media/iqnavigatorvms-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/iqnavigatorvms-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。

### <a name="creating-a-iqnavigator-vms-test-user"></a>IQNavigator VMS テスト ユーザーの作成

このセクションの目的は、IQNavigator VMS で Britta Simon というユーザーを作成することです。 [IQNavigator VMS サポート チーム](https://www.beeline.com/iqn-product-support/)と協力して、IQNavigator VMS アカウントにユーザーを追加します。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に IQNavigator VMS へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200]

**Britta Simon を IQNavigator VMS に割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201]

1. アプリケーションの一覧で **[IQNavigator VMS]** を選択します。

    ![Configure single sign-on](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_app.png)

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで IQNavigator VMS のタイルをクリックすると、自動的に IQNavigator VMS アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/iqnavigatorvms-tutorial/tutorial_general_01.png
[2]: ./media/iqnavigatorvms-tutorial/tutorial_general_02.png
[3]: ./media/iqnavigatorvms-tutorial/tutorial_general_03.png
[4]: ./media/iqnavigatorvms-tutorial/tutorial_general_04.png

[100]: ./media/iqnavigatorvms-tutorial/tutorial_general_100.png

[200]: ./media/iqnavigatorvms-tutorial/tutorial_general_200.png
[201]: ./media/iqnavigatorvms-tutorial/tutorial_general_201.png
[202]: ./media/iqnavigatorvms-tutorial/tutorial_general_202.png
[203]: ./media/iqnavigatorvms-tutorial/tutorial_general_203.png

