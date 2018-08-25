---
title: 'チュートリアル: Azure Active Directory と Panopto の統合 | Microsoft Docs'
description: Azure Active Directory と Panopto の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 89c88e23-93ce-4970-9baa-1104c4e8fe4a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 60d4b9f5343aac978c1e32aad39f237d565b2176
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421476"
---
# <a name="tutorial-azure-active-directory-integration-with-panopto"></a>チュートリアル: Azure Active Directory と Panopto の統合

このチュートリアルでは、Panopto と Azure Active Directory (Azure AD) を統合する方法について説明します。

Panopto と Azure AD の統合には、次の利点があります。

- Panopto にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Panopto にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Panopto と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Panopto でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Panopto の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-panopto-from-the-gallery"></a>ギャラリーからの Panopto の追加
Azure AD への Panopto の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Panopto を追加する必要があります。

**ギャラリーから Panopto を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**Panopto**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/panopto-tutorial/tutorial_panopto_search.png)

1. 結果パネルで **[Panopto]** を選択し、**[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/panopto-tutorial/tutorial_panopto_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Panopto で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Panopto ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Panopto の関連ユーザーの間で、リンク関係が確立されている必要があります。

Panopto で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Panopto で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Panopto テスト ユーザーの作成](#creating-a-panopto-test-user)** - Panopto で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Panopto アプリケーションでシングル サインオンを構成します。

**Panopto で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Panopto** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/panopto-tutorial/tutorial_panopto_samlbase.png)

1. **[Panopto のドメインと URL]** セクションで、次の手順に従います。

    ![Configure single sign-on](./media/panopto-tutorial/tutorial_panopto_url.png)

    **[サインオン URL]** ボックスに、`https://<tenant-name>.panopto.com` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これは実際の値ではありません。 この値は実際のサインオン URL で更新します。 この値を取得するには、[Panopto クライアント サポート チーム](mailto:support@panopto.com‎)に問い合わせてください。 
 
1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![Configure single sign-on](./media/panopto-tutorial/tutorial_panopto_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/panopto-tutorial/tutorial_general_400.png)

1. **[Panopto の構成]** セクションで、**[Panopto の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML エンティティ ID と SAML シングル サインオン サービス URL** をコピーします。

    ![Configure single sign-on](./media/panopto-tutorial/tutorial_panopto_configure.png) 

1. 別の Web ブラウザーのウィンドウで、Panopto 企業サイトに管理者としてログインします。

1. 左側のツールバーで、**[システム]**、**[ID プロバイダー]** の順にクリックします。
   
   ![システム](./media/panopto-tutorial/ic777670.png "システム")
1. **[プロバイダーを追加する]** を追加します。
   
   ![ID プロバイダー](./media/panopto-tutorial/ic777671.png "ID プロバイダー")
   
1. [SAML プロバイダー] セクションで、次の手順に従います。
   
    ![SaaS の構成](./media/panopto-tutorial/ic777672.png "SaaS の構成")
    
    a. **[プロバイダーの種類]** 一覧で **[SAML20]** を選択します。    
    
    b. **[インスタンス名]** テキストボックスに、インスタンスの名前を入力します。

    c. **[わかりやすい説明]** テキストボックスに、わかりやすい説明を入力します。
    
    d. **[Bounce Page URL]** テキストボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    e. **[発行者]** テキストボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。

    f. Azure Portal からダウンロードした base-64 でエンコードされた証明書を開き、その内容をクリップボードにコピーして **[公開キー]** テキストボックスに貼り付けます。

1. **[Save]** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/panopto-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/panopto-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/panopto-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/panopto-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-panopto-test-user"></a>Panopto テスト ユーザーの作成

Panopto へのユーザー プロビジョニングの構成にあたって必要な操作はありません。  
割り当てられたユーザーがアクセス パネルを使用して Panopto にログインしようとすると、そのユーザーが存在するかどうかが Panopto によって確認されます。  

使用可能なユーザー アカウントがない場合、ユーザー アカウントは Panopto により自動的に作成されます。

>[!NOTE]
>Panopto から提供されている他の Panopto ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。
>
>

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Panopto へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Panopto に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Panopto]** を選択します。

    ![Configure single sign-on](./media/panopto-tutorial/tutorial_panopto_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Panopto のタイルをクリックすると、Panopto アプリケーションのページに自動的にログインします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/panopto-tutorial/tutorial_general_01.png
[2]: ./media/panopto-tutorial/tutorial_general_02.png
[3]: ./media/panopto-tutorial/tutorial_general_03.png
[4]: ./media/panopto-tutorial/tutorial_general_04.png

[100]: ./media/panopto-tutorial/tutorial_general_100.png

[200]: ./media/panopto-tutorial/tutorial_general_200.png
[201]: ./media/panopto-tutorial/tutorial_general_201.png
[202]: ./media/panopto-tutorial/tutorial_general_202.png
[203]: ./media/panopto-tutorial/tutorial_general_203.png

