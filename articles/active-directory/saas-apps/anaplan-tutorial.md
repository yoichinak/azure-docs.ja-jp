---
title: 'チュートリアル: Azure Active Directory と Anaplan の統合 | Microsoft Docs'
description: Azure Active Directory と Anaplan の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 4a9c2914-6c8c-4a88-93e3-3753afb40e6b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: jeedes
ms.openlocfilehash: 4936e8d3c48486247677cf072513b7e450f1bf17
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2018
ms.locfileid: "39044035"
---
# <a name="tutorial-azure-active-directory-integration-with-anaplan"></a>チュートリアル: Azure Active Directory と Anaplan の統合

このチュートリアルでは、Anaplan と Azure Active Directory (Azure AD) を統合する方法について説明します。

Anaplan と Azure AD の統合には、次の利点があります。

- Anaplan にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Anaplan にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Anaplan と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Anaplan でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Anaplan の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-anaplan-from-the-gallery"></a>ギャラリーからの Anaplan の追加
Azure AD への Anaplan の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Anaplan を追加する必要があります。

**ギャラリーから Anaplan を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

4. [検索] ボックスに、「 **Anaplan**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/anaplan-tutorial/tutorial_anaplan_search.png)

5. 結果ウィンドウで **[Anaplan]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/anaplan-tutorial/tutorial_anaplan_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Anaplan で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Anaplan ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Anaplan の関連ユーザーの間で、リンク関係が確立されている必要があります。

Anaplan で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Anaplan で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Anaplan のテスト ユーザーの作成](#creating-an-anaplan-test-user)** - Anaplan で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Anaplan アプリケーションでシングル サインオンを構成します。

**Anaplan で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Anaplan** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/anaplan-tutorial/tutorial_anaplan_samlbase.png)

3. **[Anaplan のドメインと URL]** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/anaplan-tutorial/tutorial_anaplan_url.png)

    a. **[サインオン URL]** ボックスに、`https://sdp.anaplan.com/frontdoor/saml/<tenant name>` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<subdomain>.anaplan.com` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[Anaplan クライアント サポート チーム](mailto:support@anaplan.com)に問い合わせてください。 
 
4. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![Configure single sign-on](./media/anaplan-tutorial/tutorial_anaplan_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/anaplan-tutorial/tutorial_general_400.png)

6. **[Anaplan 構成]** セクションで、**[Anaplan の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![Configure single sign-on](./media/anaplan-tutorial/tutorial_anaplan_configure.png) 

7. **Anaplan** 側でシングル サインオンを構成するには、ダウンロードした**メタデータ XML**、**SAML エンティティ ID**、**SAML シングル サインオン サービス URL**、および**サインアウト URL** を [Anaplan サポート チーム](mailto:support@anaplan.com)に送る必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/anaplan-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/anaplan-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/anaplan-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/anaplan-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-an-anaplan-test-user"></a>Anaplan のテスト ユーザーの作成

このセクションでは、Anaplan で Britta Simon というユーザーを作成します。 [Anaplan サポート チーム](mailto:support@anaplan.com)と連携して、Anaplan プラットフォームにユーザーを追加してください。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Anaplan へのアクセスを許可することで、Britta Simon が Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Anaplan に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Anaplan]** を選択します。

    ![Configure single sign-on](./media/anaplan-tutorial/tutorial_anaplan_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Anaplan のタイルをクリックすると、自動的に Anaplan アプリケーションにサインオンします。

アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/anaplan-tutorial/tutorial_general_01.png
[2]: ./media/anaplan-tutorial/tutorial_general_02.png
[3]: ./media/anaplan-tutorial/tutorial_general_03.png
[4]: ./media/anaplan-tutorial/tutorial_general_04.png

[100]: ./media/anaplan-tutorial/tutorial_general_100.png

[200]: ./media/anaplan-tutorial/tutorial_general_200.png
[201]: ./media/anaplan-tutorial/tutorial_general_201.png
[202]: ./media/anaplan-tutorial/tutorial_general_202.png
[203]: ./media/anaplan-tutorial/tutorial_general_203.png

