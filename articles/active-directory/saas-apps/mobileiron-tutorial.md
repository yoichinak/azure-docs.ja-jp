---
title: 'チュートリアル: Azure Active Directory と MobileIron の統合 | Microsoft Docs'
description: Azure Active Directory と MobileIron の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 3e4bbd5b-290e-4951-971b-ec0c1c11aaa2
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/9/2017
ms.author: jeedes
ms.openlocfilehash: 53cec59841fbda49e4e410f069882ea76996f9fb
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39428293"
---
# <a name="tutorial-azure-active-directory-integration-with-mobileiron"></a>チュートリアル: Azure Active Directory と MobileIron の統合

このチュートリアルでは、MobileIron と Azure Active Directory (Azure AD) を統合する方法について説明します。

MobileIron と Azure AD の統合には、次の利点があります。

- MobileIron にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に MobileIron にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

MobileIron と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- MobileIron でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから MobileIron を追加する
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-mobileiron-from-the-gallery"></a>ギャラリーから MobileIron を追加する
Azure AD への MobileIron の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に MobileIron を追加する必要があります。

**ギャラリーから MobileIron を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに **「MobileIron」** と入力し、結果パネルで **[MobileIron]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの MobileIron](./media/mobileiron-tutorial/tutorial_mobileiron_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、MobileIron で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する MobileIron ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと MobileIron の関連ユーザーの間で、リンク関係が確立されている必要があります。

MobileIron で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

MobileIron で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[MobileIron のテスト ユーザーの作成](#create-a-mobileiron-test-user)** - MobileIron で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、MobileIron アプリケーションでシングル サインオンを構成します。

**MobileIron で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **MobileIron** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/mobileiron-tutorial/tutorial_mobileiron_samlbase.png)

1. **[MobileIron のドメインと URL]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![[MobileIron のドメインと URL] のシングル サインオン情報](./media/mobileiron-tutorial/tutorial_mobileiron_url.png)

    a. **[識別子]** ボックスに、`https://www.mobileiron.com/<key>` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<host>.mobileiron.com/saml/SSO/alias/<key>` のパターンを使用して URL を入力します。

1. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[MobileIron のドメインと URL] のシングル サインオン](./media/mobileiron-tutorial/tutorial_mobileiron_url1.png)

    **[サインオン URL]** ボックスに、`https://<host>.mobileiron.com/user/login.html` のパターンを使用して URL を入力します。
    
    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 このチュートリアルで後ほど説明する MobileIron の管理ポータルからキーとホストの値を取得します。

1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/mobileiron-tutorial/tutorial_mobileiron_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/mobileiron-tutorial/tutorial_general_400.png)

1. 別の Web ブラウザー ウィンドウで、MobileIron 企業サイトに管理者としてログインします。

1. **[管理者]** > **[ID]** と移動します。

   * **[Info on Cloud IDP Setup]\(Cloud IDP セットアップの情報\)** フィールドで **[AAD]** オプションを選択します。

    ![[シングル サインオン管理者の構成] ボタン](./media/mobileiron-tutorial/tutorial_mobileiron_admin.png)

1. **キー**および**ホスト**の値をコピーし貼り付けて、Azure Portal での **[MobileIron のドメインと URL]** セクションの URL を完成させます。

    ![[シングル サインオン管理者の構成] ボタン](./media/mobileiron-tutorial/key.png)

1. **[Export metadata file from AAD and import to MobileIron Cloud Field]\(AAD からメタデータ ファイルをエクスポートして MobileIron Cloud Field にインポートする\)** で、**[ファイルの選択]** をクリックして、Azure Portal からダウンロードしたメタデータをアップロードします。 アップロードしたら、**[完了]** をクリックします。
 
    ![[シングル サインオン管理者メタデータの構成] ボタン](./media/mobileiron-tutorial/tutorial_mobileiron_adminmetadata.png)

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/mobileiron-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/mobileiron-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/mobileiron-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/mobileiron-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
  
### <a name="create-a-mobileiron-test-user"></a>MobileIron テスト ユーザーを作成する

Azure AD ユーザーが MobileIron にログインできるようにするには、ユーザーを MobileIron にプロビジョニングする必要があります。  
MobileIron の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. MobileIron 企業サイトに管理者としてログインします。

1. **[ユーザー]** に移動して、**[追加]** > **[単一ユーザー]** とクリックします。

    ![[シングル サインオン ユーザーの構成] ボタン](./media/mobileiron-tutorial/tutorial_mobileiron_user.png)

1. **[単一ユーザー]** ダイアログ ページで、次の手順を実行します。

    ![[シングル サインオン ユーザー追加の構成] ボタン](./media/mobileiron-tutorial/tutorial_mobileiron_useradd.png)

    a. **[電子メール アドレス]** ボックスに、ユーザーの電子メール アドレスを入力します (この例では brittasimon@contoso.com)。

    b. **[名]** ボックスに、ユーザーの名を入力します (例: Britta)。

    c. **[姓]** ボックスに、ユーザーの姓を入力します (例: Simon)。
    
    d. **[Done]** をクリックします。  

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に MobileIron へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**MobileIron に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[MobileIron]** を選択します。

    ![アプリケーションの一覧の [MobileIron] リンク](./media/mobileiron-tutorial/tutorial_mobileiron_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [MobileIron] タイルをクリックすると、MobileIron アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/mobileiron-tutorial/tutorial_general_01.png
[2]: ./media/mobileiron-tutorial/tutorial_general_02.png
[3]: ./media/mobileiron-tutorial/tutorial_general_03.png
[4]: ./media/mobileiron-tutorial/tutorial_general_04.png

[100]: ./media/mobileiron-tutorial/tutorial_general_100.png

[200]: ./media/mobileiron-tutorial/tutorial_general_200.png
[201]: ./media/mobileiron-tutorial/tutorial_general_201.png
[202]: ./media/mobileiron-tutorial/tutorial_general_202.png
[203]: ./media/mobileiron-tutorial/tutorial_general_203.png

