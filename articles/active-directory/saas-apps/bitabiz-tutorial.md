---
title: 'チュートリアル: Azure Active Directory と BitaBIZ の統合 | Microsoft Docs'
description: Azure Active Directory と BitaBIZ の間でシングル サインオンを構成する方法について確認します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1a51e677-c62b-4aee-9c61-56926aaaa899
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: 2a05a4f1b9162a69e074bf6243236df48c8ce536
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39429664"
---
# <a name="tutorial-azure-active-directory-integration-with-bitabiz"></a>チュートリアル: Azure Active Directory と BitaBIZ の統合

このチュートリアルでは、BitaBIZ と Azure Active Directory (Azure AD) を統合する方法について説明します。

BitaBIZ と Azure AD の統合には、次の利点があります。

- BitaBIZ にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に BitaBIZ にサインオン (シングル サインオン) するよう指定できます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

BitaBIZ と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- BitaBIZ でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの BitaBIZ の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-bitabiz-from-the-gallery"></a>ギャラリーからの BitaBIZ の追加
Azure AD への BitaBIZ の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に BitaBIZ を追加する必要があります。

**ギャラリーから BitaBIZ を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**BitaBIZ**」と入力し、結果パネルで **[BitaBIZ]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの BitaBIZ](./media/bitabiz-tutorial/tutorial_bitabiz_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、BitaBIZ で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する BitaBIZ ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと BitaBIZ の関連ユーザーの間で、リンク関係が確立されている必要があります。

BitaBIZ で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当てて、リンク関係を確立します。

BitaBIZ で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[BitaBIZ のテスト ユーザーの作成](#create-a-bitabiz-test-user)** - BitaBIZ で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、BitaBIZ アプリケーションでシングル サインオンを構成します。

**BitaBIZ で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **BitaBIZ** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/bitabiz-tutorial/tutorial_bitabiz_samlbase.png)

1. **[BitaBIZ のドメインと URL]** セクションで、IDP 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![[BitaBIZ のドメインと URL] のシングル サインオン情報](./media/bitabiz-tutorial/tutorial_bitabiz_url.png)

    **[識別子]** ボックスに、`https://www.bitabiz.com/<instanceId>` の形式で URL を入力します。

    > [!NOTE] 
    > 上記の URL の値は、単なる例です。 この値は、実際の識別子に置き換えてください。これについては後で説明します。

1. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[BitaBIZ のドメインと URL] のシングル サインオン情報](./media/bitabiz-tutorial/tutorial_bitabiz_url1.png)

    **[サインオン URL]** ボックスに、URL として「`https://www.bitabiz.com/dashboard`」を入力します。

1. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/bitabiz-tutorial/tutorial_bitabiz_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/bitabiz-tutorial/tutorial_general_400.png)
    
1. **[BitaBIZ 構成]** セクションで、**[BitaBIZ の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML エンティティ ID と SAML シングル サインオン サービス URL** をコピーします。

    ![BitaBIZ 構成](./media/bitabiz-tutorial/tutorial_bitabiz_configure.png) 

1. 別の Web ブラウザーのウィンドウで、管理者として BitaBIZ テナントにサインオンします。

1. **[SETUP ADMIN]\(管理設定\)** をクリックします。

    ![BitaBIZ 構成](./media/bitabiz-tutorial/settings1.png)

1. **[値の追加]** セクションで **[Microsoft integrations]\(Microsoft 統合\)** をクリックします。

    ![BitaBIZ 構成](./media/bitabiz-tutorial/settings2.png)

1. 下にスクロールして、**[Microsoft Azure AD (シングル サインオンを有効にする)]** セクションに移動し、次の手順を実行します。

    ![BitaBIZ 構成](./media/bitabiz-tutorial/settings3.png)

    a. **[エンティティ ID (Azure AD では "識別子")]** ボックスの値をコピーし、Azure Portal の **[BitaBIZ のドメインと URL]** セクションの **[識別子]** ボックスに貼り付けます。 
    
    b. **[Azure AD Single Sign-On Service URL]\(Azure AD シングル サインオン サービス URL\)** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** を貼り付けます。
    
    c. **[Azure AD SAML Entity ID]\(Azure AD SAML エンティティ ID\)** ボックスに、Azure Portal からコピーした **SAML エンティティ ID** を貼り付けます。

    d. ダウンロードした**証明書 (Base64)** ファイルをメモ帳で開き、その内容をクリップボードにコピーし、**[Azure AD Signing Certificate (Base64 encoded)]\(Azure AD 署名証明書 (Base64 でエンコード済み)\)** ボックスに貼り付けます。

    e. ビジネス メール ドメイン名 (mycompany.com) を **[ドメイン名]** ボックスに追加して、SSO を、このメール ドメインと共に (必須ではありません) 会社のユーザーに割り当てます。
    
    f. BitaBIZ アカウントの **[SSO enabled]\(SSO 有効\)** をオンします。
    
    g. **[Save Azure AD configuration]\(Azure AD 構成を保存する\)** をクリックして、SSO 構成をアクティブ化します。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/bitabiz-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/bitabiz-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/bitabiz-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/bitabiz-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-bitabiz-test-user"></a>BitaBIZ テスト ユーザーの作成

Azure AD ユーザーが BitaBIZ にログインできるようにするには、そのユーザーを BitaBIZ にプロビジョニングする必要があります。  
BitaBIZ の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. BitaBIZ の企業サイトに管理者としてログインします。

1. **[SETUP ADMIN]\(管理設定\)** をクリックします。

    ![BitaBIZ の [ユーザーの追加]](./media/bitabiz-tutorial/settings1.png)

1. **[組織]** セクションの **[ユーザーの追加]** をクリックします。

    ![BitaBIZ の [ユーザーの追加]](./media/bitabiz-tutorial/user1.png)

1. **[Add new employee]\(新しい従業員の追加\)** をクリックします。

    ![BitaBIZ の [ユーザーの追加]](./media/bitabiz-tutorial/user2.png)

1. **[Add new employee]\(新しい従業員の追加\)** ダイアログ ページで、次の手順に従います。

    ![BitaBIZ の [ユーザーの追加]](./media/bitabiz-tutorial/user3.png)

    a. **[名]** ボックスに、ユーザーの名を入力します (この例では Britta)。

    b. **[姓]** ボックスに、ユーザーの姓を入力します (この例では Simon)。

    c. **[Email]\(メール\)** ボックスに、ユーザーのメール アドレス (Brittasimon@contoso.com など) を入力します。

    d. **[Date of employment]\(雇用日\)** で日付を選択します。

    e. ユーザーに対して設定できる他の任意のユーザー属性があります。 詳細については、[従業員の設定ドキュメント](https://help.bitabiz.dk/manage-or-set-up-your-account/on-boarding-employees/new-employee)を参照してください。    
    
    f. **[Save employee]\(従業員を保存\)** をクリックします。
    
    > [!NOTE]
    > Azure Active Directory アカウント所有者が電子メールを受信し、リンクに従ってアカウントを確認すると、そのアカウントがアクティブになります。
    
### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に BitaBIZ へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**BitaBIZ に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[BitaBIZ]** を選択します。

    ![アプリケーションの一覧の BitaBIZ リンク](./media/bitabiz-tutorial/tutorial_bitabiz_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [BitaBIZ] タイルをクリックすると、BitaBIZ アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bitabiz-tutorial/tutorial_general_01.png
[2]: ./media/bitabiz-tutorial/tutorial_general_02.png
[3]: ./media/bitabiz-tutorial/tutorial_general_03.png
[4]: ./media/bitabiz-tutorial/tutorial_general_04.png

[100]: ./media/bitabiz-tutorial/tutorial_general_100.png

[200]: ./media/bitabiz-tutorial/tutorial_general_200.png
[201]: ./media/bitabiz-tutorial/tutorial_general_201.png
[202]: ./media/bitabiz-tutorial/tutorial_general_202.png
[203]: ./media/bitabiz-tutorial/tutorial_general_203.png

