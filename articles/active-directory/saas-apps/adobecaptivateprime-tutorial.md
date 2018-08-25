---
title: 'チュートリアル: Azure Active Directory と Adobe Captivate Prime の統合 | Microsoft Docs'
description: Azure Active Directory と Adobe Captivate Prime の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2f95b226-1465-47f4-b8b7-de4b0772abbc
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2018
ms.author: jeedes
ms.openlocfilehash: bbeae2cadde3e64f17b20eafabaf5e2dbf5a5cc6
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2018
ms.locfileid: "39044075"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-captivate-prime"></a>チュートリアル: Azure Active Directory と Adobe Captivate Prime

このチュートリアルでは、Adobe Captivate Prime と Azure Active Directory (Azure AD) を統合する方法について説明します。

Adobe Captivate Prime と Azure AD の統合には、次の利点があります。

- Adobe Captivate Prime にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Adobe Captivate Prime にサインオン (シングル サインオン) するよう指定できます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Adobe Captivate Prime と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Adobe Captivate Prime でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Adobe Captivate Prime の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-adobe-captivate-prime-from-the-gallery"></a>ギャラリーからの Adobe Captivate Prime の追加
Azure AD への Adobe Captivate Prime の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Adobe Captivate Prime を追加する必要があります。

**ギャラリーから Adobe Captivate Prime を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Adobe Captivate Prime**」と入力し、結果パネルで **Adobe Captivate Prime** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Adobe Captivate Prime](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Adobe Captivate Prime で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Adobe Captivate Prime ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Adobe Captivate Prime の関連ユーザーの間で、リンク関係が確立されている必要があります。

Adobe Captivate Prime での Azure AD シングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Adobe Captivate Prime のテスト ユーザーの作成](#create-an-adobe-captivate-prime-test-user)** - Adobe Captivate Prime で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Adobe Captivate Prime アプリケーションにシングル サインオンを構成します。

**Adobe Captivate Prime との Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **Adobe Captivate Prime** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_samlbase.png)

3. **[Adobe Captivate Prime のドメインと URL]** セクションで、次の手順を実行します。

    ![[Adobe Captivate Prime のドメインと URL] のシングル サインオン情報](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_url.png)

    a. **[識別子]** ボックスに次の URL を入力します。`https://captivateprime.adobe.com`

    b. **[応答 URL]** ボックスに、次の URL を入力します。`https://captivateprime.adobe.com/saml/SSO`

4. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/adobecaptivateprime-tutorial/tutorial_general_400.png)

6. **[プロパティ]** タブに移動し、**ユーザー アクセス URL** をコピーしてメモ帳に貼り付けます。

    ![ユーザー アクセスのリンク](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_appprop.png)

7. **Adobe Captivate Prime** 側でシングル サインオンを構成するには、ダウンロードした**メタデータ XML** とコピーした**ユーザー アクセス URL** を、[Adobe Captivate Prime サポート チーム](mailto:captivateprimesupport@adobe.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/adobecaptivateprime-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/adobecaptivateprime-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/adobecaptivateprime-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/adobecaptivateprime-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
  
### <a name="create-an-adobe-captivate-prime-test-user"></a>Adobe Captivate Prime のテスト ユーザーの作成

このセクションでは、Adobe Captivate Prime で Britta Simon というユーザーを作成します。 [Adobe Captivate Prime サポート チーム](mailto:captivateprimesupport@adobe.com)と協力して、Adobe Captivate Prime プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Adobe Captivate Prime へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Adobe Captivate Prime に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Adobe Captivate Prime]** を選択します。

    ![アプリケーションの一覧の [Adobe Captivate Prime] リンク](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Adobe Captivate Prime] タイルをクリックすると、自動的に Adobe Captivate Prime アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/adobecaptivateprime-tutorial/tutorial_general_01.png
[2]: ./media/adobecaptivateprime-tutorial/tutorial_general_02.png
[3]: ./media/adobecaptivateprime-tutorial/tutorial_general_03.png
[4]: ./media/adobecaptivateprime-tutorial/tutorial_general_04.png

[100]: ./media/adobecaptivateprime-tutorial/tutorial_general_100.png

[200]: ./media/adobecaptivateprime-tutorial/tutorial_general_200.png
[201]: ./media/adobecaptivateprime-tutorial/tutorial_general_201.png
[202]: ./media/adobecaptivateprime-tutorial/tutorial_general_202.png
[203]: ./media/adobecaptivateprime-tutorial/tutorial_general_203.png

