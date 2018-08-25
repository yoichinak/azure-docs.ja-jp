---
title: 'チュートリアル: Azure Active Directory と Blackboard Learn の統合 | Microsoft Docs'
description: Azure Active Directory と Blackboard Learn の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 0b8ca505-61ea-487c-9a3e-fa50c936df0c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2017
ms.author: jeedes
ms.openlocfilehash: 9b7e7a84059f8393e8f900733602e32ca3ad833b
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39423659"
---
# <a name="tutorial-azure-active-directory-integration-with-blackboard-learn"></a>チュートリアル: Azure Active Directory と Blackboard Learn の統合

このチュートリアルでは、Blackboard Learn と Azure Active Directory (Azure AD) を統合する方法について説明します。

Blackboard Learn と Azure AD の統合には、次の利点があります。

- Blackboard Learn にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Blackboard Learn にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Blackboard Learn と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Blackboard Learn でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Blackboard Learn の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-blackboard-learn-from-the-gallery"></a>ギャラリーからの Blackboard Learn の追加
Azure AD への Blackboard Learn の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Blackboard Learn を追加する必要があります。

**ギャラリーから Blackboard Learn を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「 **Blackboard Learn**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/blackboard-learn-tutorial/tutorial_blackboardlearn_search.png)

1. 結果ウィンドウで **[Blackboard Learn]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/blackboard-learn-tutorial/tutorial_blackboardlearn_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Blackboard Learn で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Blackboard Learn ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Blackboard Learn の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンクの関係を確立するには、Azure AD の **[ユーザー名]** の値を、Blackboard Learn の **[Username]** の値として割り当てます。

Blackboard Learn で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Blackboard Learn のテスト ユーザーの作成](#creating-a-blackboard-learn-test-user)** - Blackboard Learn で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Blackboard Learn アプリケーションでシングル サインオンを構成します。

**Blackboard Learn で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Blackboard Learn** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/blackboard-learn-tutorial/tutorial_blackboardlearn_samlbase.png)

1. **[Blackboard Learn のドメインと URL]** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/blackboard-learn-tutorial/tutorial_blackboardlearn_url.png)

    a. **[サインオン URL]** ボックスに、`https://<subdomain>.blackboard.com/` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<subdomain>.blackboard.com/auth-saml/saml/SSO/entity-id/SAML_AD` の形式で URL を入力します。
    
    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[Blackboard Learn クライアント サポート チーム](https://www.blackboard.com/support/index.aspx)に問い合わせてください。 

1. Blackboard Learn アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。
 次のスクリーンショットは、その例を示しています。

    ![Configure single sign-on](./media/blackboard-learn-tutorial/tutorial_attribute.png)

1. **[Single sign-on\(シングル サインオン\)]** ダイアログの **[User Attributes\(ユーザー属性\)]** セクションで、図に示すように SAML トークン属性を構成し、次の手順を実行します。 ここでは、一意のユーザー属性として、Userprincipalname をマッピングしていますが、組織内のユーザーを一意に識別し、Blackboard Learn ユーザー名フィールドにマッピングする適切な値にマッピングすることができます。
           
    | 属性名 | 属性値 |   
    | ---------------| ----------------|
    | urn:oid:1.3.6.1.4.1.5923.1.1.1.6 |user.userprincipalname |

    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![Configure single sign-on](./media/blackboard-learn-tutorial/tutorial_attribute_04.png)
    
    ![Configure single sign-on](./media/blackboard-learn-tutorial/tutorial_attribute_05.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[値]** 一覧から、その行に対して表示される値を入力します。
    
    d. **[OK]** をクリックします。

1. **[SAML 署名証明書]** セクションで、**[メタデータ XML]** をクリックし、コンピューターに XML ファイルを保存します。

    ![Configure single sign-on](./media/blackboard-learn-tutorial/tutorial_blackboardlearn_certificate.png)

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/blackboard-learn-tutorial/tutorial_general_400.png)

1. **[Blackboard Learn 構成]** セクションで、**[Blackboard Learn の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス] セクション**から **SAML エンティティ ID** をコピーします。

    ![Configure single sign-on](./media/blackboard-learn-tutorial/tutorial_blackboardlearn_configure.png) 

1. **Blackboard Learn** 側でシングル サインオンを構成するには、ダウンロードした**メタデータ XML** と **SAML エンティティ ID** を [Blackboard Learn サポート](https://www.blackboard.com/support/index.aspx)に送る必要があります。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/blackboard-learn-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/blackboard-learn-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/blackboard-learn-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/blackboard-learn-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、Britta Simon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-blackboard-learn-test-user"></a>Blackboard Learn のテスト ユーザーの作成
このセクションでは、Blackboard Learn で Britta Simon というユーザーを作成します。 

Blackboard Learn アプリケーションは、ジャスト イン タイム ユーザー プロビジョニングをサポートしています。 「**[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)**」セクションで説明しているように要求が構成されていることを確認してください。
### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Blackboard Learn へのアクセスを許可することで、Britta Simon が Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Blackboard Learn に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Blackboard Learn]** を選択します。

    ![Configure single sign-on](./media/blackboard-learn-tutorial/tutorial_blackboardlearn_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Blackboard Learn] タイルをクリックすると、自動的に Blackboard Learn アプリケーションにサインオンします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/blackboard-learn-tutorial/tutorial_general_01.png
[2]: ./media/blackboard-learn-tutorial/tutorial_general_02.png
[3]: ./media/blackboard-learn-tutorial/tutorial_general_03.png
[4]: ./media/blackboard-learn-tutorial/tutorial_general_04.png

[100]: ./media/blackboard-learn-tutorial/tutorial_general_100.png

[200]: ./media/blackboard-learn-tutorial/tutorial_general_200.png
[201]: ./media/blackboard-learn-tutorial/tutorial_general_201.png
[202]: ./media/blackboard-learn-tutorial/tutorial_general_202.png
[203]: ./media/blackboard-learn-tutorial/tutorial_general_203.png

