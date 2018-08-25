---
title: 'チュートリアル: Azure Active Directory と Five9 Plus Adapter (CTI、Contact Center Agents) の統合 | Microsoft Docs'
description: Azure Active Directory と Five9 Plus Adapter (CTI、Contact Center Agents) の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 88dc82ab-be0b-4017-8335-c47d00775d7b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: jeedes
ms.openlocfilehash: 8ee04008b62867c8eba68b1525cf50edec881cbc
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39432635"
---
# <a name="tutorial-azure-active-directory-integration-with-five9-plus-adapter-cti-contact-center-agents"></a>チュートリアル: Azure Active Directory と Five9 Plus Adapter (CTI、Contact Center Agents) の統合

このチュートリアルでは、Five9 Plus Adapter (CTI、Contact Center Agents) と Azure Active Directory (Azure AD) を統合する方法について説明します。

Five9 Plus Adapter (CTI、Contact Center Agents) と Azure AD の統合には、次の利点があります。

- Five9 Plus Adapter (CTI, Contact Center Agents) にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで Five9 Plus Adapter (CTI、Contact Center Agents) に自動的にサインオン (シングル サインオン) できるように、設定が可能です
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Five9 Plus Adapter (CTI、Contact Center Agents) と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Five9 Plus Adapter (CTI、Contact Center Agents) でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Five9 Plus Adapter (CTI、Contact Center Agents) の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-five9-plus-adapter-cti-contact-center-agents-from-the-gallery"></a>ギャラリーからの Five9 Plus Adapter (CTI、Contact Center Agents) の追加
Azure AD への Five9 Plus Adapter (CTI、Contact Center Agents) の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Five9 Plus Adapter (CTI、Contact Center Agents) を追加する必要があります。

**ギャラリーから Five9 Plus Adapter (CTI、Contact Center Agents) を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. [検索] ボックスに、「**Five9 Plus Adapter (CTI, Contact Center Agents)**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/five9-tutorial/tutorial_five9_search.png)

1. 結果ウィンドウで **Five9 Plus Adapter (CTI, Contact Center Agents)** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/five9-tutorial/tutorial_five9_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Five9 Plus Adapter (CTI、Contact Center Agents) で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Five9 Plus Adapter (CTI、Contact Center Agents) ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Five9 Plus Adapter (CTI、Contact Center Agents) の関連ユーザーの間で、リンク関係が確立されている必要があります。

Five9 Plus Adapter (CTI、Contact Center Agents) で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

Five9 Plus Adapter (CTI、Contact Center Agents) で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Five9 Plus Adapter (CTI、Contact Center Agents) テスト ユーザーの作成](#creating-a-five9-plus-adapter-cti-contact-center-agents-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Five9 Plus Adapter (CTI、Contact Center Agents) で作成します。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Five9 Plus Adapter (CTI、Contact Center Agents) アプリケーションでシングル サインオンを構成します。

**Five9 Plus Adapter (CTI、Contact Center Agents) で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure ポータルの **Five9 Plus Adapter (CTI、Contact Center Agents)** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/five9-tutorial/tutorial_five9_samlbase.png)

1. **[Five9 Plus Adapter (CTI, Contact Center Agents) のドメインと URL]** セクションで、次の手順に従います。

    ![Configure single sign-on](./media/five9-tutorial/tutorial_five9_url.png)
    
    a. **[識別子]** ボックスに、次のパターンで URL を入力します。

    |    環境      |       URL      |
    | :-- | :-- |
    | "Five9 Plus Adapter for Microsoft Dynamics CRM" の場合 | `https://app.five9.com/appsvcs/saml/metadata/alias/msdc` |
    | "Five9 Plus Adapter for Zendesk" の場合 | `https://app.five9.com/appsvcs/saml/metadata/alias/zd` |
    | "Five9 Plus Adapter for Agent Desktop Toolkit" の場合 | `https://app.five9.com/appsvcs/saml/metadata/alias/adt` |

    b. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。

    |      環境     |      URL      |
    | :--                  | :--           |
    | "Five9 Plus Adapter for Microsoft Dynamics CRM" の場合 | `https://app.five9.com/appsvcs/saml/SSO/alias/msdc` |
    | "Five9 Plus Adapter for Zendesk" の場合 | `https://app.five9.com/appsvcs/saml/SSO/alias/zd` |
    | "Five9 Plus Adapter for Agent Desktop Toolkit" の場合 | `https://app.five9.com/appsvcs/saml/SSO/alias/adt` |

1. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure single sign-on](./media/five9-tutorial/tutorial_five9_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/five9-tutorial/tutorial_general_400.png)

1. **[Five9 Plus Adapter (CTI, Contact Center Agents) 構成]** セクションで **[Five9 Plus Adapter (CTI, Contact Center Agents) の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![Configure single sign-on](./media/five9-tutorial/tutorial_five9_configure.png) 

1. **Five9 Plus Adapter (CTI、Contact Center Agents)** 側にシングル サインオンを構成するには、ダウンロードされた**証明書 (Base64)、サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** を [Five9 Plus Adapter (CTI、Contact Center Agents) サポート チーム](https://www.five9.com/about/contact)に送信する必要があります。 また、SSO をさらに構成するために、アダプターに従って以下の手順のようにしてください。

    a. "Five9 Plus Adapter for Agent Desktop Toolkit" 管理ガイド: [http://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf](http://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf)
    
    b. "Five9 Plus Adapter for Microsoft Dynamics CRM" 管理ガイド: [http://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf](http://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf)
    
    c. "Five9 Plus Adapter for Zendesk" 管理ガイド: [http://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf](http://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf)


> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/five9-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/five9-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/five9-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/five9-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-five9-plus-adapter-cti-contact-center-agents-test-user"></a>Five9 Plus Adapter (CTI、Contact Center Agents) テスト ユーザーの作成

このセクションでは、Five9 Plus Adapter (CTI、Contact Center Agents) で Britta Simon というユーザーを作成します。 [Five9 Plus Adapter (CTI、Contact Center Agents) サポート チーム](https://www.five9.com/about/contact)と協力し、Five9 Plus Adapter (CTI、Contact Center Agents) プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Five9 Plus Adapter (CTI、Contact Center Agents) へのアクセスを許可することで、Britta Simon が Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Five9 Plus Adapter (CTI、Contact Center Agents) に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Five9 Plus Adapter (CTI, Contact Center Agents)]** を選択します。

    ![Configure single sign-on](./media/five9-tutorial/tutorial_five9_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Five9 Plus Adapter (CTI、Contact Center Agents) のタイルをクリックすると、自動的に Five9 Plus Adapter (CTI、Contact Center Agents) アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/five9-tutorial/tutorial_general_01.png
[2]: ./media/five9-tutorial/tutorial_general_02.png
[3]: ./media/five9-tutorial/tutorial_general_03.png
[4]: ./media/five9-tutorial/tutorial_general_04.png

[100]: ./media/five9-tutorial/tutorial_general_100.png

[200]: ./media/five9-tutorial/tutorial_general_200.png
[201]: ./media/five9-tutorial/tutorial_general_201.png
[202]: ./media/five9-tutorial/tutorial_general_202.png
[203]: ./media/five9-tutorial/tutorial_general_203.png

