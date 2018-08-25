---
title: 'チュートリアル: Azure Active Directory と AnswerHub の統合 | Microsoft Docs'
description: Azure Active Directory と AnswerHub の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 818b91d7-01df-4b36-9706-f167c710a73c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 447a3911bc1f021fb1ca2658716de1910b5379b6
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2018
ms.locfileid: "39044082"
---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>チュートリアル: Azure Active Directory と AnswerHub の統合

このチュートリアルでは、AnswerHub と Azure Active Directory (Azure AD) を統合する方法について説明します。

AnswerHub と Azure AD の統合には、次の利点があります。

- Azure AD で誰が AnswerHub にアクセスできるかを制御できます
- ユーザーが自分の Azure AD アカウントで AnswerHub に自動的にサインオンされる (シングル サインオン) ようにできます
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と AnswerHub の統合を構成するには、次の項目が必要です。

- Azure AD サブスクリプション
- AnswerHub でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの AnswerHub の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-answerhub-from-the-gallery"></a>ギャラリーからの AnswerHub の追加
AnswerHub の Azure AD への統合を構成するには、AnswerHub をギャラリーから管理対象 SaaS アプリの一覧に追加する必要があります。

**ギャラリーから AnswerHub を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

4. 検索ボックスに「**AnswerHub**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/answerhub-tutorial/tutorial_answerhub_search.png)

5. 結果ウィンドウで **[AnswerHub]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/answerhub-tutorial/tutorial_answerhub_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" という名前のテスト ユーザーに基づいて、AnswerHub で Azure AD のシングル サインオンを構成およびテストします。

シングル サインオンを機能させるには、Azure AD が Azure AD のユーザーに対応する AnswerHub のユーザーを認識している必要があります。 つまり、Azure AD ユーザーと AnswerHub の関連するユーザーの間のリンク関係が確立されている必要があります。

AnswerHub で、Azure AD での **[ユーザー名]** の値を **[ユーザー名]** の値として割り当てることによってリンク関係を確立します。

AnswerHub で Azure AD のシングル サインオンを構成およびテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[AnswerHub テスト ユーザーの作成](#creating-an-answerhub-test-user)** - Azure AD でのユーザーにリンクされた、AnswerHub での Britta Simon の対応するユーザーを作成します。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、AnswerHub アプリケーションでシングル サインオンを構成します。

**AnswerHub で Azure AD のシングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **[AnswerHub]** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/answerhub-tutorial/tutorial_answerhub_samlbase.png)

3. **[AnswerHub Domain and URLs] \(AnswerHub のドメインと URL)** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/answerhub-tutorial/tutorial_answerhub_url.png)

    a. **[サインオン URL]** ボックスに、`https://<company>.answerhub.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<company>.answerhub.com` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[AnswerHub クライアント サポート チーム](mailto:success@answerhub.com)に問い合わせてください。 
 
4. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure single sign-on](./media/answerhub-tutorial/tutorial_answerhub_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/answerhub-tutorial/tutorial_general_400.png)

6. **[AnswerHub Configuration] \(AnswerHub 構成)** セクションで、**[Configure AnswerHub] \(AnswerHub の構成)** をクリックして **[Configure sign-on] \(サインオンの構成)** ウィンドウを開きます。 **[クイック リファレンス] セクション**から、**サインアウト URL と SAML シングル サインオン サービス URL** をコピーします。

    ![Configure single sign-on](./media/answerhub-tutorial/tutorial_answerhub_configure.png) 

7. 別の Web ブラウザーのウィンドウで、管理者として AnswerHub 企業サイトにログインします。
   
    >[!NOTE]
    >AnswerHub の構成について不明点がある場合は、[AnswerHub サポート チーム](mailto:success@answerhub.com.)にお問い合わせください。
   
8. **[Administration]** に移動します。

9. **[User and Group]** タブをクリックします。

10. 左側のナビゲーション ウィンドウで、**[Social Settings]** セクションの **[SAML Setup]** をクリックします。

11. **[IDP Config]** タブをクリックします。

12. **[IDP Config]** タブで、次の手順を実行します。

     ![SAML のセットアップ](./media/answerhub-tutorial/ic785172.png "SAML Setup")  
  
     a. **[IDP Login URL] \(IDP ログイン URL)** テキスト ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** を貼り付けます。
  
     b. **[IDP Logout URL] \(IDP ログアウト URL)** テキスト ボックスに、Azure Portal からコピーした**サインアウト URL** 値を貼り付けます。
     
     c. **[IDP Name Identifier Format] \(IDP 名前識別子形式)** テキスト ボックスに、Azure Portal の **[ユーザー属性]** セクションで選択したのと同じユーザー識別子の値を入力します。
  
     d. **[Keys and Certificates]** をクリックします。

13. [Keys and Certificates] タブでは、次の手順を実行します。
    
     ![Keys and Certificates (キーと証明書)](./media/answerhub-tutorial/ic785173.png "Keys and Certificates")  
 
     a. Azure Portal からダウンロードした Base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーしてから、それを **[IDP Public Key (x509 Format)] \(IDP 公開キー (x509 形式))** テキスト ボックスに貼り付けます。
  
     b. **[Save]** をクリックします。

14. **[IDP Config]** タブで、**[Save]** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/answerhub-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/answerhub-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/answerhub-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/answerhub-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-an-answerhub-test-user"></a>AnswerHub テスト ユーザーの作成

Azure AD ユーザーが AnswerHub にログインできるようにするには、そのユーザーを AnswerHub にプロビジョニングする必要があります。  
AnswerHub の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **AnswerHub** 企業サイトに管理者としてログインします。

2. **[Administration]** に移動します。

3. **[Users & Groups]** タブをクリックします。

4. 左側のナビゲーション ウィンドウで、**[Manage Users]** セクションの **[Create or import users]** をクリックします。
   
   ![Users & Groups (ユーザーとグループ)](./media/answerhub-tutorial/ic785175.png "Users & Groups")

5. プロビジョニングする有効な Azure Active Directory ユーザー アカウントの**電子メール アドレス**、**ユーザー名**、**パスワード**を対応するボックスに入力し、**[Save]** をクリックします。

>[!NOTE]
>他の AnswerHub ユーザー アカウントの作成ツールまたは AnswerHub から提供されている API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、AnswerHub へのアクセス権を付与することによって、Britta Simon が Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を AnswerHub に割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で、**[AnswerHub]** を選択します。

    ![Configure single sign-on](./media/answerhub-tutorial/tutorial_answerhub_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルの [AnswerHub] タイルをクリックすると、AnswerHub アプリケーションに自動的にサインオンされます。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/answerhub-tutorial/tutorial_general_01.png
[2]: ./media/answerhub-tutorial/tutorial_general_02.png
[3]: ./media/answerhub-tutorial/tutorial_general_03.png
[4]: ./media/answerhub-tutorial/tutorial_general_04.png

[100]: ./media/answerhub-tutorial/tutorial_general_100.png

[200]: ./media/answerhub-tutorial/tutorial_general_200.png
[201]: ./media/answerhub-tutorial/tutorial_general_201.png
[202]: ./media/answerhub-tutorial/tutorial_general_202.png
[203]: ./media/answerhub-tutorial/tutorial_general_203.png

