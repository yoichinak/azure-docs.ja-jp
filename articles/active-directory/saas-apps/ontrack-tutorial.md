---
title: 'チュートリアル: Azure Active Directory と OnTrack の統合 | Microsoft Docs'
description: Azure Active Directory と OnTrack の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d2cafba2-3b4a-4471-ba34-80f6a96ff2b9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: jeedes
ms.openlocfilehash: 82e0788ad2f1e49cb593e504adc1e826516d4616
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39424727"
---
# <a name="tutorial-azure-active-directory-integration-with-ontrack"></a>チュートリアル: Azure Active Directory と OnTrack の統合

このチュートリアルでは、OnTrack と Azure Active Directory (Azure AD) を統合する方法について説明します。

OnTrack と Azure AD の統合には、次の利点があります。

- OnTrack にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に OnTrack にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

OnTrack と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- OnTrack でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの OnTrack の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-ontrack-from-the-gallery"></a>ギャラリーからの OnTrack の追加
Azure AD への OnTrack の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に OnTrack を追加する必要があります。

**ギャラリーから OnTrack を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**OnTrack**」と入力し、結果パネルで **OnTrack** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の OnTrack](./media/ontrack-tutorial/tutorial_ontrack_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、OnTrack で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する OnTrack ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと OnTrack の関連ユーザーの間で、リンク関係が確立されている必要があります。

OnTrack で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

OnTrack で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[OnTrack のテスト ユーザーの作成](#create-an-ontrack-test-user)** - OnTrack で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure ポータルで Azure AD のシングル サインオンを有効にし、OnTrack アプリケーションでシングル サインオンを構成します。

**OnTrack で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure ポータルの **OnTrack** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/ontrack-tutorial/tutorial_ontrack_samlbase.png)

1. **[OnTrack のドメインと URL]** セクションで、次の手順を実行します。

    ![[OnTrack のドメインと URL] のシングル サインオン情報](./media/ontrack-tutorial/tutorial_ontrack_url.png)

    a. **[識別子]** ボックスに、次のように入力します。
    
    テスト環境の場合は、次の URL を入力します。`https://staging.insigniagroup.com/sso`

    運用環境の場合は、次の URL を入力します。`https://oeaccessories.com/sso`

    b. **[応答 URL]** ボックスに、次のように入力します。
    
    テスト環境の場合は、次の URL を入力します。`https://indie.staging.insigniagroup.com/sso/autonation.aspx`

    運用環境の場合は、次の URL を入力します。`https://igaccessories.com/sso/autonation.aspx`

1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/ontrack-tutorial/tutorial_ontrack_certificate.png)

1. OnTrack アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 このアプリケーションには、次の要求を構成します。 この属性の値は、アプリケーション統合ページの **[User Attributer]** セクションで管理できます。 

    ![Configure single sign-on](./media/ontrack-tutorial/tutorial_attribute.png)

1. **[Single sign-on]\(シングル サインオン\)** ダイアログの **[User Attributes]\(ユーザー属性\)** セクションで、上記の図に示すように SAML トークン属性を構成し、次の手順を実行します。
    
    | 属性名 | 属性値 |
    | -------------- | ----------------|    
    | User-Role      | "42F432" |
    | Hyperion-Code  | "12345" |

    > [!NOTE]
    > **User-Role** 属性と **Hyperion-Code** 属性は、それぞれ Autonation ユーザー ロールと Dealer コードにマップされます。 これらの値は例なので、統合するための正しいコードを使用してください。 これらの値は、[Autonation サポート](mailto:CustomerService@insigniagroup.com)に問い合わせることができます。
    
    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![Configure single sign-on](./media/ontrack-tutorial/tutorial_attribute_04.png) 

    ![Configure single sign-on](./media/ontrack-tutorial/tutorial_attribute_05.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[値]** 一覧から、その行に対して表示される値を入力します。
    
    d. **[OK]** をクリックします。

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/ontrack-tutorial/tutorial_general_400.png)

1. **OnTrack** 側にシングル サインオンを構成するには、ダウンロードした**メタデータ XML** を [OnTrack サポート チーム](mailto:CustomerService@insigniagroup.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/ontrack-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/ontrack-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/ontrack-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/ontrack-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-an-ontrack-test-user"></a>OnTrack テスト ユーザーを作成する

このセクションでは、OnTrack で Britta Simon というユーザーを作成します。 [OnTrack サポート チーム](mailto:CustomerService@insigniagroup.com)と連携し、OnTrack プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に OnTrack へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**OnTrack に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[OnTrack]** を選択します。

    ![アプリケーションの一覧の OnTrack のリンク](./media/ontrack-tutorial/tutorial_ontrack_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [OnTrack] タイルをクリックすると、自動的に OnTrack アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ontrack-tutorial/tutorial_general_01.png
[2]: ./media/ontrack-tutorial/tutorial_general_02.png
[3]: ./media/ontrack-tutorial/tutorial_general_03.png
[4]: ./media/ontrack-tutorial/tutorial_general_04.png

[100]: ./media/ontrack-tutorial/tutorial_general_100.png

[200]: ./media/ontrack-tutorial/tutorial_general_200.png
[201]: ./media/ontrack-tutorial/tutorial_general_201.png
[202]: ./media/ontrack-tutorial/tutorial_general_202.png
[203]: ./media/ontrack-tutorial/tutorial_general_203.png

