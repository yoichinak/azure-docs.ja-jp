---
title: 'チュートリアル: Azure Active Directory と 15Five の統合 | Microsoft Docs'
description: Azure Active Directory と 15Five の間のシングル サインオンを構成する方法を説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 2fb301c2-7d7a-4046-8ee1-7dc9e7684806
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: jeedes
ms.openlocfilehash: 3cc0d9122fd7335bc29c7f35c1163cb39d3481b8
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054303"
---
# <a name="tutorial-azure-active-directory-integration-with-15five"></a>チュートリアル: Azure Active Directory と 15Five の統合

このチュートリアルでは、15Five と Azure Active Directory (Azure AD) を統合する方法について説明します。

15Five と Azure AD の統合には、次の利点があります。

- 15Five にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に 15Five にサインオン (シングル サインオン) できるようにします
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

15Five と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- 15Five でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの 15Five の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-15five-from-the-gallery"></a>ギャラリーからの 15Five の追加
Azure AD への 15Five の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に 15Five を追加する必要があります。

**ギャラリーから 15Five を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

4. [検索] ボックスに、「**15Five**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/15five-tutorial/tutorial_15five_search.png)

5. 結果ウィンドウで **[15Five]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/15five-tutorial/tutorial_15five_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、15Five で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する 15Five ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと 15Five の関連ユーザーの間で、リンク関係が確立されている必要があります。

15Five で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

In 15Five, assign the value of the で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[In 15Five, assign the value of the テスト ユーザーの作成](#creating-a-15five-test-user)** - In 15Five, assign the value of the で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure ポータルで Azure AD のシングル サインオンを有効にし、15Five アプリケーションでシングル サインオンを構成します。

**15Fiveで Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure ポータルの **15Five** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/15five-tutorial/tutorial_15five_samlbase.png)

3. **[15Five のドメインと URL]** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/15five-tutorial/tutorial_15five_url.png)

    a. **[サインオン URL]** ボックスに、`https://<companyname>.15five.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<companyname>.15five.com/saml2/metadata/` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[15Five クライアント サポート チーム](https://www.15five.com/contact/)に連絡してください。 
 
4. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![Configure single sign-on](./media/15five-tutorial/tutorial_15five_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/15five-tutorial/tutorial_general_400.png)

6. **15Five**側にシングル サインオンを構成するには、ダウンロードした**メタデータ XML** を [15Five サポート チーム](https://www.15five.com/contact/)に送信する必要があります。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/15five-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/15five-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/15five-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/15five-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-15five-test-user"></a>15Five テスト ユーザーの作成

Azure AD ユーザーが 15Five にログインできるようにするには、ユーザーを 15Five にプロビジョニングする必要があります。 15Five の場合、プロビジョニングは手動で行います。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>ユーザー プロビジョニングを構成するには、次の手順に従います。
1. **15Five** 企業サイトに管理者としてログインします。

2. **[会社の管理]** に移動します。
   
    ![会社の管理](./media/15five-tutorial/IC784675.png "Manage Company")

3. **[ユーザー] \> [ユーザーを追加]** の順にクリックします。
   
    ![ユーザー](./media/15five-tutorial/IC784676.png "People")

4. [新しいユーザーの追加] セクションで、次の手順に従います。
   
    ![新しいユーザーの追加](./media/15five-tutorial/IC784677.png "Add New Person")
   
    a. 関連するテキスト ボックスに、プロビジョニングする有効な Azure Active Directory アカウントの**名**、**姓**、**役職**、**電子メール アドレス**を入力します。

    b. **[Done]** をクリックします。
   
    > [!NOTE]
    > アカウントがアクティブになる前に、Azure AD アカウント所有者に、アカウント確認用のリンクを含む電子メールが送信されます。
   
### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に 15Five へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**15Five に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[15Five]** を選択します。

    ![Configure single sign-on](./media/15five-tutorial/tutorial_15five_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [15Five] タイルをクリックすると、15Five アプリケーションのログイン ページが表示されます。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/15five-tutorial/tutorial_general_01.png
[2]: ./media/15five-tutorial/tutorial_general_02.png
[3]: ./media/15five-tutorial/tutorial_general_03.png
[4]: ./media/15five-tutorial/tutorial_general_04.png

[100]: ./media/15five-tutorial/tutorial_general_100.png

[200]: ./media/15five-tutorial/tutorial_general_200.png
[201]: ./media/15five-tutorial/tutorial_general_201.png
[202]: ./media/15five-tutorial/tutorial_general_202.png
[203]: ./media/15five-tutorial/tutorial_general_203.png

