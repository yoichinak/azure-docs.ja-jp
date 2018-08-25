---
title: 'チュートリアル: Azure Active Directory と Jamf Pro の統合 | Microsoft Docs'
description: Azure Active Directory と Jamf Pro の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2018
ms.author: jeedes
ms.openlocfilehash: 94b8b935728110cd5dd07b2066e8320274e3b082
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39428419"
---
# <a name="tutorial-azure-active-directory-integration-with-jamf-pro"></a>チュートリアル: Azure Active Directory と Jamf Pro の統合

このチュートリアルでは、Jamf Pro と Azure Active Directory (Azure AD) を統合する方法について説明します。

Jamf Pro と Azure AD の統合には、次の利点があります。

- Jamf Pro にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Jamf Pro にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Jamf Pro と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Jamf Pro シングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Jamf Pro の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-jamf-pro-from-the-gallery"></a>ギャラリーからの Jamf Pro の追加
Azure AD への Jamf Pro の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Jamf Pro を追加する必要があります。

**ギャラリーから Jamf Pro を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**Jamf Pro**」と入力し、結果パネルで **[Jamf Pro]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Jamf Pro](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Jamf Pro で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Jamf Pro ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Jamf Pro の関連ユーザーの間で、リンク関係が確立されている必要があります。

Jamf Pro で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Jamf Pro テスト ユーザーの作成](#create-a-jamf-pro-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Jamf Pro で作成します。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にして、Jamf Pro アプリケーションでシングル サインオンを構成します。

**Jamf Pro で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure portal の **Jamf Pro** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_samlbase.png)

1. **[Jamf Pro Domain and URLs]\(Jamf Pro のドメインと URL\)** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![Jamf Pro のドメインと URL のシングル サインオン情報](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_url.png)

    a. **[識別子 (エンティティ ID)]** ボックスに、次のパターンで URL を入力します。`https://<subdomain>.jamfcloud.com/saml/metadata`

    b. **[応答 URL]** ボックスに、`https://<subdomain>.jamfcloud.com/saml/SSO` のパターンを使用して URL を入力します。

1. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![Jamf Pro のドメインと URL のシングル サインオン情報](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_url1.png)

    **[サインオン URL]** ボックスに、`https://<subdomain>.jamfcloud.com` のパターンを使用して URL を入力します。
     
    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 実際の識別子の値を Jamf Pro ポータルの **[シングル サインオン]** セクションから取得します。これについては、このチュートリアルで後ほど説明します。 実際の**サブドメイン**の値を識別子の値から抽出し、サインオン URL と応答 URL 内のその**サブドメイン**情報を使用できます。

1. **[SAML 署名証明書]** セクションで、コピー ボタンをクリックして **[アプリのフェデレーション メタデータ URL]** をコピーし、メモ帳に貼り付けます。

    ![証明書のダウンロードのリンク](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/jamfprosamlconnector-tutorial/tutorial_general_400.png)
    
1. 別の Web ブラウザーのウィンドウで、Jamf Pro 企業サイトに管理者としてログインします。

1. ページの右上隅の**設定アイコン**をクリックします。

    ![Jamf Pro の構成](./media/jamfprosamlconnector-tutorial/configure1.png)

1. **[シングル サインオン]** をクリックします。

    ![Jamf Pro の構成](./media/jamfprosamlconnector-tutorial/configure2.png)

1. **[Single sign-on]\(シングル サインオン\)** ページで、次の手順を実行します。

    ![Jamf Pro シングル](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_single.png)

    a. **[Jamf Pro Server]\(Jamf Pro サーバー\)** を選択してシングル サインオン アクセスを有効にします。

    b. **[Allow bypass for all users]\(すべてのユーザーにバイパスを許可する\)** を選択すると、ユーザーは認証のために ID プロバイダーのログイン ページへとリダイレクトされなくなり、Jamf Pro に直接ログインできるようになります。 ユーザーが ID プロバイダーを経由して Jamf Pro にアクセスしようとした場合は、IdP によって開始される SSO 認証と承認が発生します。

    c. **[USER MAPPING: SAML]\(ユーザー マッピング: SAML\)** の **[NameID]** オプションを選択します。 既定では、この設定は **[NameID]** に設定されていますが、カスタム属性を定義することもできます。

    d. **[USER MAPPING: JAMF PRO]\(ユーザー マッピング: JAMF PRO\)** の **[Email]\(電子メール\)** を選択します。 Jamf Pro は、IdP によって送信された SAML 属性を次の方法でマップします: ユーザー別およびグループ別。 ユーザーが Jamf Pro にアクセスしようとすると、既定では、Jamf Pro がユーザーに関する情報を ID プロバイダーから取得し、それを Jamf Pro のユーザー アカウントと照合します。 受信したユーザー アカウントが Jamf Pro 内に存在しない場合は、グループ名のマッチングが発生します。

    e. 値 `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` を **[GROUP ATTRIBUTE NAME]\(グループ属性名\)** テキスト ボックスに貼り付けます。
 
1. 同じページで、**[シングル サインオン]** セクションの下の **[IDENTITY PROVIDER]\(ID プロバイダー\)** まで下にスクロールし、次の手順に従います。

    ![Jamf Pro の構成](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. **[IDENTITY PROVIDER]\(ID プロバイダー\)** ドロップダウン メニューから **[Other]\(その他\)** を選択します。

    b. **[OTHER PROVIDER]\(その他のプロバイダー\)** ボックスに、**[Azure AD]** を入力します。

    c. **[メタデータ URL]** を **[IDENTITY PROVIDER METADATA SOURCE]\(ID プロバイダーのメタデータ ソース\)** ドロップダウンからオプションとして選択し、次のテキストボックスに、Azure portal からコピーした **[アプリのフェデレーション メタデータ URL]** の値を貼り付けます。

    d. **エンティティ ID** の値をコピーし、Azure portal の **[Jamf Pro Domain and URLs]\(Jamf Pro のドメインと URL\)** セクションの **[識別子 (エンティティ ID)]** テキストボックスに貼り付けます。

    >[!NOTE]
    > ここで、隠されている値はサブドメインの部分です。この値を使用して、Azure portal 上で、**[Jamf Pro Domain and URLs]\(Jamf Pro ドメインおよび URL\)** セクションのサインオン URL と応答 URL を完了します。

    e. **[Save]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/jamfprosamlconnector-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/jamfprosamlconnector-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/jamfprosamlconnector-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/jamfprosamlconnector-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-jamf-pro-test-user"></a>Jamf Pro テスト ユーザーの作成

Azure AD ユーザーが Jamf Pro にログインできるようにするには、ユーザーを Jamf Pro にプロビジョニングする必要があります。 Jamf Pro の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Jamf Pro の企業サイトに管理者としてログインします。

1. ページの右上隅の**設定アイコン**をクリックします。

    ![従業員の追加](./media/jamfprosamlconnector-tutorial/configure1.png)

1. **[Jamf Pro User Accounts & Groups]\(Jamf Pro ユーザー アカウントとグループ\)** をクリックします。

    ![従業員の追加](./media/jamfprosamlconnector-tutorial/user1.png)

1. **[新規]** をクリックします。

    ![従業員の追加](./media/jamfprosamlconnector-tutorial/user2.png)

1. **[Create Standard Account]\(標準アカウントの作成\)** を選択します。

    ![従業員の追加](./media/jamfprosamlconnector-tutorial/user3.png)

1. **[新しいアカウント]** ダイアログで、次の手順に従います。

    ![従業員の追加](./media/jamfprosamlconnector-tutorial/user4.png)

    a. **[ユーザー名]** テキストボックスに、フル ネームの「BrittaSimon」を入力します。

    b. **[ACCESS LEVEL]\(アクセス レベル\)**、**[PRIVILEGE SET]\(特権セット\)**、および **[ACCESS STATUS]\(アクセスの状態\)** に対して組織に従って適切なオプションを選択します。
    
    c. **[FULL NAME]\(フル ネーム\)** テキストボックスに、フル ネームの「Britta Simon」を入力します。

    d. **[メール アドレス]** テキストボックスに、Britta Simon アカウントのメール アドレスを入力します。

    e. **[パスワード]** テキストボックスに、そのユーザーのパスワードを入力します。

    f. **[VERIFY PASSWORD]\(パスワードの確認\)** テキストボックスに、そのユーザーのパスワードを入力します。

    g. **[Save]** をクリックします。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Jamf Pro へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Jamf Pro に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Jamf Pro]** を選択します。

    ![アプリケーションの一覧の [Jamf Pro] リンク](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Jamf Pro のタイルをクリックすると、自動的に Jamf Pro アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/jamfprosamlconnector-tutorial/tutorial_general_01.png
[2]: ./media/jamfprosamlconnector-tutorial/tutorial_general_02.png
[3]: ./media/jamfprosamlconnector-tutorial/tutorial_general_03.png
[4]: ./media/jamfprosamlconnector-tutorial/tutorial_general_04.png

[100]: ./media/jamfprosamlconnector-tutorial/tutorial_general_100.png

[200]: ./media/jamfprosamlconnector-tutorial/tutorial_general_200.png
[201]: ./media/jamfprosamlconnector-tutorial/tutorial_general_201.png
[202]: ./media/jamfprosamlconnector-tutorial/tutorial_general_202.png
[203]: ./media/jamfprosamlconnector-tutorial/tutorial_general_203.png

