---
title: 'チュートリアル: Azure Active Directory と Qlik Sense Enterprise の統合 | Microsoft Docs'
description: Azure Active Directory と Qlik Sense Enterprise の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 8c27e340-2b25-47b6-bf1f-438be4c14f93
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/06/2018
ms.author: jeedes
ms.openlocfilehash: a8816451b45171e0ba8cbd7acc937201c587c481
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627952"
---
# <a name="tutorial-azure-active-directory-integration-with-qlik-sense-enterprise"></a>チュートリアル: Azure Active Directory と Qlik Sense Enterprise の統合

このチュートリアルでは、Qlik Sense Enterprise と Azure Active Directory (Azure AD) を統合する方法について説明します。

Qlik Sense Enterprise と Azure AD の統合には、次の利点があります。

- Qlik Sense Enterprise にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Qlik Sense Enterprise にサインオン (シングル サインオン) する機能を有効にすることができます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Qlik Sense Enterprise と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Qlik Sense Enterprise でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Qlik Sense Enterprise の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>ギャラリーからの Qlik Sense Enterprise の追加
Azure AD への Qlik Sense Enterprise の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Qlik Sense Enterprise を追加する必要があります。

**ギャラリーから Qlik Sense Enterprise を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Qlik Sense Enterprise**」と入力し、結果パネルで **[Qlik Sense Enterprise]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Qlik Sense Enterprise](./media/qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Qlik Sense Enterprise で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Qlik Sense Enterprise ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Qlik Sense Enterprise の関連ユーザーの間で、リンク関係が確立されている必要があります。

Qlik Sense Enterprise で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Qlik Sense Enterprise で Azure AD のシングル サインオンを構成し、テストするには、次の要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Qlik Sense Enterprise のテスト ユーザーの作成](#create-a-qlik-sense-enterprise-test-user)** - Qlik Sense Enterprise で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Qlik Sense Enterprise アプリケーションでシングル サインオンを構成します。

**Qlik Sense Enterprise で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Qlik Sense Enterprise** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![[シングル サインオン] ダイアログ ボックス](./media/qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_samlbase.png)

3. **[Qlik Sense Enterprise のドメインと URL]** セクションで、次の手順を実行します。

    ![[Qlik Sense Enterprise のドメインと URL] のシングル サインオン情報](./media/qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_url.png)

    a. **[サインオン URL]** ボックスに、`https://<Qlik Sense Fully Qualifed Hostname>:4443/azure/hub` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、次のパターンを使用して URL を入力します。
    | |
    |--|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qlikpoc.com`|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qliksense.com`|
    | |

    c. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。

    `https://<Qlik Sense Fully Qualifed Hostname>:4443/samlauthn/`

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値は実際のサインオン URL、識別子、応答 URL に変更してください。これらの値の取得方法については、このチュートリアルの後半の説明を参照するか、[Qlik Sense Enterprise クライアント サポート チーム](https://www.qlik.com/us/services/support)に問い合わせてください。

4. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_certificate.png)

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/qliksense-enterprise-tutorial/tutorial_general_400.png)

6. フェデレーション メタデータ XML ファイルを準備して、Qlik Sense サーバーにアップロードできるようにします。

    > [!NOTE]
    > IdP メタデータを Qlik Sense サーバーにアップロードする前にファイルを編集して情報を削除し、Azure AD と Qlik Sense サーバーの間の処理が正しく行われるようにする必要があります。

    ![QlikSense][qs24]

    a. Azure Portal からダウンロードした FederationMetaData.xml ファイルを、テキスト エディターで開きます。

    b. **RoleDescriptor** を検索します。  エントリは 4 つあります (開始要素タグと終了要素タグのペアが 2 つあります)。

    c. RoleDescriptor タグと、タグ間のすべての情報をファイルから削除します。

    d. このファイルは、このドキュメントで後ほど使用するため、わかりやすい場所に保存してください。

7. Qlik Sense Qlik Management Console (QMC) に、仮想プロキシ構成を作成できるユーザーとして移動します。

8. QMC で、メニュー項目の **[Virtual Proxies]** をクリックします。

    ![QlikSense][qs6]

9. 画面の下部にある **[Create new]** ボタンをクリックします。

    ![QlikSense][qs7]

10. [Virtual Proxies (仮想プロキシ)] 編集画面が表示されます。  画面の右側のメニューで、構成オプションを表示できます。

    ![QlikSense][qs9]

11. [Identification (識別)] メニュー オプションを選択し、Azure 仮想プロキシ構成の識別情報を入力します。

    ![QlikSense][qs8]  

    a. **[Description]** フィールドは、仮想プロキシ構成のフレンドリ名です。  値として説明を入力します。

    b. **[Prefix]** フィールドでは、Azure AD シングル サインオンで Qlik Sense に接続するための仮想プロキシ エンドポイントを指定します。  この仮想プロキシに一意のプレフィックス名を入力します。

    c. **[Session inactivity timeout (minutes)]** は、この仮想プロキシを経由する接続のタイムアウトです。

    d. **[Session cookie header name]** は、認証が成功した後にユーザーが受け取る Qlik Sense セッション用のセッション識別子を格納する Cookie 名です。  この名前は一意である必要があります。

12. [Authentication (認証)] メニュー オプションをクリックして表示します。  [Authentication (認証)] 画面が表示されます。

    ![QlikSense][qs10]

    a. **[Anonymous access mode]** ドロップ ダウンで、匿名ユーザーによる仮想プロキシを介した Qlik Sense へのアクセスを許可するかどうかを設定します。  既定のオプションは、[No anonymous user (匿名ユーザーを許可しない)] です。

    b. **[Authentication method]** ドロップ ダウンで、仮想プロキシで使用する認証スキームを設定します。  ドロップダウン リストから SAML を選択してください。  その結果、さらにオプションが表示されます。

    c. **[SAML host URI]** フィールドに、ユーザーが SAML 仮想プロキシを介して Qlik Sense にアクセスする際に入力するホスト名を入力します。  ホスト名は、Qlik Sense サーバーの URI です。

    d. **[SAML entity ID]** に、[SAML host URI] フィールドに入力したのと同じ値を入力します。

    e. **[SAML IdP metadata]** に、以前に **Azure AD 構成からのフェデレーション メタデータの編集**に関するセクションで編集したファイルを指定します。  **IdP メタデータをアップロードする前に、このファイルを編集する必要があります**。Azure AD と Qlik Sense サーバーの間で処理が正しく行われるように、ファイルの情報を削除してください。  **まだファイルを編集していない場合は、上記の手順に従ってください。**  ファイルを編集済みの場合は、[Browse (参照)] ボタンをクリックし、編集したメタデータ ファイルを選択して、仮想プロキシ構成にアップロードします。

    f. これらは Azure AD が Qlik Sense サーバーに送信する **UserID** を表します。  スキーマ リファレンス情報は、構成が終了した後に Azure アプリの画面から取得できます。  名前属性を使用するには、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`」と入力します。

    g. **ユーザー ディレクトリ**用の値を入力します。この値は、ユーザーが Azure AD を介して Qlik Sense サーバーで認証を行うときに、ユーザーにアタッチされます。  ハードコーディングされた値は**角かっこ []** で囲む必要があります。  Azure AD SAML アサーション内で送信される属性を使用するには、属性の名前をこのボックスに角かっこ **なし** で入力します。

    h. **[SAML signing algorithm]** で、仮想プロキシ構成用のサービス プロバイダー (この場合は Qlik Sense サーバー) 証明書の署名を設定します。  Microsoft Enhanced RSA and AES Cryptographic Provider を使用して生成された、信頼された証明書を Qlik Sense サーバーで使用する場合は、SAML 署名アルゴリズムを **[SHA-256]** に変更します。

    i. [SAML attribute mapping (SAML 属性マッピング)] セクションでは、セキュリティ規則での使用を目的とした、Qlik Sense への追加の属性 (グループなど) の送信を許可します。

13. **[LOAD BALANCING]** メニュー オプションをクリックして表示します。  [Load balancing (負荷分散)] 画面が表示されます。

    ![QlikSense][qs11]

14. **[Add new server node]** ボタンをクリックし、エンジン ノードまたは Qlik Sense が負荷分散のためにセッションを送信する複数のノードを選択して、**[Add]** ボタンをクリックします。

    ![QlikSense][qs12]

15. [Advanced (詳細設定)] メニュー オプションをクリックして表示します。 [Advanced (詳細設定)] 画面が表示されます。

    ![QlikSense][qs13]

    [Host white list (ホスト名の許可リスト)] では、Qlik Sense サーバーへの接続時に受け入れられるホスト名を指定します。  **ユーザーが Qlik Sense サーバーへ接続する際に指定するホスト名を入力します。** ホスト名は、[SAML host URI (SAML ホスト URI)] の値から "https://" を除いたものです。

16. **[Apply]** ボタンをクリックします。

    ![QlikSense][qs14]

17. [OK] をクリックし、仮想プロキシにリンクされているプロキシが再起動されることを示す警告メッセージを受け入れます。

    ![QlikSense][qs15]

18. 画面の右側に、[Associated items (関連項目)] メニューが表示されます。  **[Proxies]** メニュー オプションをクリックします。

    ![QlikSense][qs16]

19. [Proxies (プロキシ)] 画面が表示されます。  下部にある **[Link]** ボタンをクリックして、仮想プロキシにプロキシをリンクさせます。

    ![QlikSense][qs17]

20. この仮想プロキシ接続をサポートするプロキシ ノードを選択し、**[Link]** ボタンをクリックします。  リンクを作成すると、そのプロキシが [Associated proxies (関連プロキシ)] の下に一覧表示されます。

    ![QlikSense][qs18]
  
    ![QlikSense][qs19]

21. 約 5 ～ 10 秒後に、QMC の更新メッセージが表示されます。  **[Refresh QMC]** ボタンをクリックします。

    ![QlikSense][qs20]

22. QMC が更新されたら、**[Virtual Proxies]** メニュー項目をクリックします。 新しい SAML 仮想プロキシのエントリが画面の表に表示されます。  仮想プロキシのエントリをクリックします。

    ![QlikSense][qs51]

23. 画面の下部にある [Download SP metadata (SP メタデータのダウンロード)] ボタンがアクティブになります。  **[Download SP metadata]** ボタンをクリックして、メタデータをファイルに保存します。

    ![QlikSense][qs52]

24. SP メタデータ ファイルを開きます。  **entityID** エントリと **AssertionConsumerService** エントリを確認します。  これらの値は、Azure AD アプリケーション構成の**識別子**、**サインオン URL**、**応答 URL** に対応しています。 一致しない場合は Azure AD アプリケーションの構成の **[Qlik Sense Enterprise のドメインと URL]** セクションにこれらの値を貼り付けて、Azure AD アプリケーションの構成ウィザードで置換する必要があります。

    ![QlikSense][qs53]

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

   ![Azure Active Directory のボタン](./media/qliksense-enterprise-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

   ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/qliksense-enterprise-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

   ![[追加] ボタン](./media/qliksense-enterprise-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

   ![[ユーザー] ダイアログ ボックス](./media/qliksense-enterprise-tutorial/create_aaduser_04.png)

   a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

   b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

   c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

   d. **Create** をクリックしてください。

### <a name="create-a-qlik-sense-enterprise-test-user"></a>Qlik Sense Enterprise のテスト ユーザーの作成

このセクションでは、Qlik Sense Enterprise で Britta Simon というユーザーを作成します。 [Qlik Sense Enterprise クライアント サポート チーム](https://www.qlik.com/us/services/support)と連携し、Qlik Sense Enterprise プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Qlik Sense Enterprise へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200]

**Britta Simon を Qlik Sense Enterprise に割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201]

2. アプリケーションの一覧で、 **[Qlik Sense Enterprise]** を選択します。

    ![アプリケーションの一覧の Qlik Sense Enterprise のリンク](./media/qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Qlik Sense Enterprise] タイルをクリックすると、自動的に Qlik Sense Enterprise アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/qliksense-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/qliksense-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/qliksense-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/qliksense-enterprise-tutorial/tutorial_general_04.png

[100]: ./media/qliksense-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/qliksense-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/qliksense-enterprise-tutorial/tutorial_general_201.png
[202]: ./media/qliksense-enterprise-tutorial/tutorial_general_202.png
[203]: ./media/qliksense-enterprise-tutorial/tutorial_general_203.png

[qs6]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs24]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs51]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png