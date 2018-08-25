---
title: 'チュートリアル: Azure Active Directory と Bambu by Sprout Social の統合 | Microsoft Docs'
description: Azure Active Directory と Bambu by Sprout Social との間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d2b9ddbc-cab7-40d6-aca1-5b171cab4199
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2017
ms.author: jeedes
ms.openlocfilehash: 1240049fd84633365aa55cd07dfb0e4ef75d24d5
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39447508"
---
# <a name="tutorial-azure-active-directory-integration-with-bambu-by-sprout-social"></a>チュートリアル: Azure Active Directory と Bambu by Sprout Social の統合

このチュートリアルでは、Bambu by Sprout Social と Azure Active Directory (Azure AD) を統合する方法について説明します。

Bambu by Sprout Social と Azure AD の統合には、次の利点があります。

- Bambu by Sprout Social にアクセスできるユーザーを Azure AD で制御することができます。
- ユーザーが自分の Azure AD アカウントで自動的に Bambu by Sprout Social にサインオン (シングル サインオン) できる環境を構築できます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Azure AD と Bambu by Sprout Social の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Bambu by Sprout Social でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の試用環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の試用版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Bambu by Sprout Social の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-bambu-by-sprout-social-from-the-gallery"></a>ギャラリーからの Bambu by Sprout Social の追加
Azure AD への Bambu by Sprout Social の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Bambu by Sprout Social を追加する必要があります。

**ギャラリーから Bambu by Sprout Social を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. ダイアログの上部にある **[新しいアプリケーション]** ボタンをクリックして新しいアプリケーションを追加します。

    ![[アプリケーション]][3]

1. 検索ボックスに「**Bambu by Sprout Social**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/bambubysproutsocial-tutorial/tutorial_bambubysproutsocial_search.png)

1. 結果ウィンドウで **[Bambu by Sprout Social]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/bambubysproutsocial-tutorial/tutorial_bambubysproutsocial_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Bambu by Sprout Social で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Bambu by Sprout Social ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Bambu by Sprout Social の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンクの関係を確立するには、Azure AD の **[ユーザー名]** の値を、Bambu by Sprout Social の **[Username (ユーザー名)]** の値として割り当てます。

Bambu by Sprout Social で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Bambu by Sprout Social テスト ユーザーの作成](#creating-a-bambu-by-sprout-social-test-user)** - Bambu by Sprout Social で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Bambu by Sprout Social アプリケーションでシングル サインオンを構成します。

**Bambu by Sprout Social で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Bambu by Sprout Social** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/bambubysproutsocial-tutorial/tutorial_bambubysproutsocial_samlbase.png)

1. アプリは Azure と事前に統合済みであるため、**[Bambu by Sprout Social のドメインと URL]** セクションで特に手順を実施する必要はありません。 

    ![Configure single sign-on](./media/bambubysproutsocial-tutorial/tutorial_bambubysproutsocial_url.png)

1. **[SAML 署名証明書]** セクションで、**[メタデータ XML]** をクリックし、コンピューターに XML ファイルを保存します。

    ![Configure single sign-on](./media/bambubysproutsocial-tutorial/tutorial_bambubysproutsocial_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/bambubysproutsocial-tutorial/tutorial_general_400.png)
    
1. **[Bambu by Sprout Social 構成]** セクションで、**[Bambu by Sprout Social の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![Configure single sign-on](./media/bambubysproutsocial-tutorial/tutorial_bambubysproutsocial_configure.png) 

1. **Bambu by Sprout Social** 側でシングル サインオンを構成するには、ダウンロードした**メタデータ XML** と **SAML シングル サインオン サービス URL** を [Bambu by Sprout Social のサポート](mailto:support@getbambu.com)に送信する必要があります。 両方の側で SAML SSO 接続を正しく設定するためにサポート チームがこれを設定します。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

<!--### Next steps

To ensure users can sign-in to Bambu by Sprout Social after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Bambu by Sprout Social prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Bambu by Sprout Social in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Bambu by Sprout Social users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->


### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/bambubysproutsocial-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/bambubysproutsocial-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/bambubysproutsocial-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/bambubysproutsocial-tutorial/create_aaduser_04.png) 

    a. **[Name]** ボックスに「**Britta Simon**」と入力します。

    b. **[ユーザー名]** ボックスに、Britta Simon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-bambu-by-sprout-social-test-user"></a>Bambu by Sprout Social テスト ユーザーの作成

アプリケーションでは、ジャストインタイムのユーザー プロビジョニングがサポートされ、認証後にユーザーがアプリケーションに自動的に作成されます。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Bambu by Sprout Social へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようします。

![ユーザーの割り当て][200] 

**Bambu by Sprout Social に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧から **[Bambu by Sprout Social]** を選択します。

    ![Configure single sign-on](./media/bambubysproutsocial-tutorial/tutorial_bambubysproutsocial_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Bambu by Sprout Social] タイルをクリックすると、自動的に Bambu by Sprout Social アプリケーションにサインオンします。 アクセス パネルの詳細については、 [アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bambubysproutsocial-tutorial/tutorial_general_01.png
[2]: ./media/bambubysproutsocial-tutorial/tutorial_general_02.png
[3]: ./media/bambubysproutsocial-tutorial/tutorial_general_03.png
[4]: ./media/bambubysproutsocial-tutorial/tutorial_general_04.png

[100]: ./media/bambubysproutsocial-tutorial/tutorial_general_100.png

[200]: ./media/bambubysproutsocial-tutorial/tutorial_general_200.png
[201]: ./media/bambubysproutsocial-tutorial/tutorial_general_201.png
[202]: ./media/bambubysproutsocial-tutorial/tutorial_general_202.png
[203]: ./media/bambubysproutsocial-tutorial/tutorial_general_203.png
