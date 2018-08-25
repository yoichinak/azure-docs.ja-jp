---
title: 'チュートリアル: Azure Active Directory と FirmPlay - Employee Advocacy for Recruiting の統合 | Microsoft Docs'
description: Azure Active Directory と FirmPlay - Employee Advocacy for Recruiting の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a6799629-7546-43f8-a966-956db32864b1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: jeedes
ms.openlocfilehash: 6e3d8a3951a42e448eff005b055b3cf5169179f1
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39443451"
---
# <a name="tutorial-azure-active-directory-integration-with-firmplay---employee-advocacy-for-recruiting"></a>チュートリアル: Azure Active Directory と FirmPlay - Employee Advocacy for Recruiting の統合

このチュートリアルでは、FirmPlay - Employee Advocacy for Recruiting と Azure Active Directory (Azure AD) を統合する方法について説明します。

FirmPlay - Employee Advocacy for Recruiting と Azure AD の統合には、次の利点があります。

- FirmPlay - Employee Advocacy for Recruiting にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで FirmPlay - Employee Advocacy for Recruiting に自動的にサインオン (シングル サインオン) できるように設定できます。
- 1 つの中央サイト (Microsoft Azure 管理ポータル) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

FirmPlay - Employee Advocacy for Recruiting と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- FirmPlay - Employee Advocacy for Recruiting でのシングル サインオンが有効なサブスクリプション


> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の試用環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の試用版を入手できます。


## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの FirmPlay - Employee Advocacy for Recruiting の追加
1. Azure AD シングル サインオンの構成とテスト


## <a name="adding-firmplay---employee-advocacy-for-recruiting-from-the-gallery"></a>ギャラリーからの FirmPlay - Employee Advocacy for Recruiting の追加
Azure AD への FirmPlay - Employee Advocacy for Recruiting の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に FirmPlay - Employee Advocacy for Recruiting を追加する必要があります。

**ギャラリーから FirmPlay - Employee Advocacy for Recruiting を追加するには、次の手順を実行します。**

1. **[Microsoft Azure 管理ポータル](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. ダイアログの上部にある **[追加]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに「**FirmPlay - Employee Advocacy for Recruiting**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/firmplay-tutorial/tutorial_firmplay_001.png)

1. 結果ウィンドウで **[FirmPlay - Employee Advocacy for Recruiting]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/firmplay-tutorial/tutorial_firmplay_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、FirmPlay - Employee Advocacy for Recruiting で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する FirmPlay - Employee Advocacy for Recruiting ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと FirmPlay - Employee Advocacy for Recruiting の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を FirmPlay - Employee Advocacy for Recruiting の **[Username (ユーザー名)]** の値として割り当てます。

FirmPlay - Employee Advocacy for Recruiting で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[FirmPlay - Employee Advocacy for Recruiting のテスト ユーザーの作成](#creating-a-firmplay---employee-advocacy-for-recruiting-test-user)** - FirmPlay - Employee Advocacy for Recruiting で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Microsoft Azure 管理ポータルで Azure AD のシングル サインオンを有効にし、FirmPlay - Employee Advocacy for Recruiting アプリケーションでシングル サインオンを構成します。

**FirmPlay - Employee Advocacy for Recruiting での Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Microsoft Azure 管理ポータルの **FirmPlay - Employee Advocacy for Recruiting** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/firmplay-tutorial/tutorial_firmplay_01.png)

1. **[FirmPlay - Employee Advocacy for Recruiting のドメインと URL]** セクションの **[サインオン URL]** ボックスに、`https://<your-subdomain>.firmplay.com/` のパターンを使用して URL を入力します。

    ![Configure single sign-on](./media/firmplay-tutorial/tutorial_firmplay_02.png)

    > [!NOTE] 
    > これは実際の値ではないので注意してください。 この値は実際のサインオン URL で更新する必要があります。 この値を取得するには、[FirmPlay - Employee Advocacy for Recruiting サポート チーム](mailto:engineering@firmplay.com)に問い合わせてください。 

1. **[SAML 署名証明書**] セクションで、**[新しい証明書の作成]** をクリックします。

    ![Configure single sign-on](./media/firmplay-tutorial/tutorial_firmplay_03.png)     

1. **[新しい証明書の作成]** ダイアログで、カレンダー アイコンをクリックし、**期限日**を選択します。 **[保存]** をクリックします。

    ![Configure single sign-on](./media/firmplay-tutorial/tutorial_general_300.png)

1. **[SAML 署名証明書]** セクションで、**[Make new certificate active (新しい証明書を有効にする)]** をクリックし、**[保存]** をクリックします。

    ![Configure single sign-on](./media/firmplay-tutorial/tutorial_firmplay_04.png)

1. ポップアップ表示される **[Rollover certificate (ロール オーバー証明書)]** ウィンドウで、**[OK]** をクリックします。

    ![Configure single sign-on](./media/firmplay-tutorial/tutorial_general_400.png)

1. **[SAML 署名証明書]** セクションで、**[Certificate (base64) (証明書 (base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。 

    ![Configure single sign-on](./media/firmplay-tutorial/tutorial_firmplay_05.png) 

1. **[FirmPlay - Employee Advocacy for Recruiting 構成]** セクションで、**[FirmPlay - Employee Advocacy for Recruiting の構成]** をクリックし、**[サインオンの構成]** ダイアログを開きます。

    ![Configure single sign-on](./media/firmplay-tutorial/tutorial_firmplay_06.png) 

    ![Configure single sign-on](./media/firmplay-tutorial/tutorial_firmplay_07.png)

1. アプリケーション用に構成された SSO を入手するには、[FirmPlay - Employee Advocacy for Recruiting サポート チーム](mailto:engineering@firmplay.com)に連絡し、次のものを情報として提供します。 

    •  ダウンロードした**証明書ファイル**

    •  **SAML シングル サインオン サービス URL**

    •  **SAML エンティティ ID**

    •  **サインアウト URL**
  

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Microsoft Azure 管理ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Microsoft Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/firmplay-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/firmplay-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/firmplay-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/firmplay-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。 



### <a name="creating-a-firmplay---employee-advocacy-for-recruiting-test-user"></a>FirmPlay - Employee Advocacy for Recruiting のテスト ユーザーの作成

このセクションでは、FirmPlay - Employee Advocacy for Recruiting で Britta Simon というユーザーを作成します。 [FirmPlay - Employee Advocacy for Recruiting サポート チーム](mailto:engineering@firmplay.com)と連携し、FirmPlay - Employee Advocacy for Recruiting プラットフォームにユーザーを追加してください。


### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon にFirmPlay - Employee Advocacy for Recruiting へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**FirmPlay - Employee Advocacy for Recruiting に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure 管理ポータルでアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[FirmPlay - Employee Advocacy for Recruiting]** を選択します。

    ![Configure single sign-on](./media/firmplay-tutorial/tutorial_firmplay_50.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    


### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [FirmPlay - Employee Advocacy for Recruiting] タイルをクリックすると、FirmPlay - Employee Advocacy for Recruiting アプリケーションに自動的にサインオンします。


## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/firmplay-tutorial/tutorial_general_01.png
[2]: ./media/firmplay-tutorial/tutorial_general_02.png
[3]: ./media/firmplay-tutorial/tutorial_general_03.png
[4]: ./media/firmplay-tutorial/tutorial_general_04.png

[100]: ./media/firmplay-tutorial/tutorial_general_100.png

[200]: ./media/firmplay-tutorial/tutorial_general_200.png
[201]: ./media/firmplay-tutorial/tutorial_general_201.png
[202]: ./media/firmplay-tutorial/tutorial_general_202.png
[203]: ./media/firmplay-tutorial/tutorial_general_203.png