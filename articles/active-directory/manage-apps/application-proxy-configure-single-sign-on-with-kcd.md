---
title: アプリケーション プロキシを使用したシングル サインオン | Microsoft Docs
description: Azure AD アプリケーション プロキシを使用してシングル サインオンを提供する方法について説明します。
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: H1Hack27Feb2017, it-pro
ms.openlocfilehash: 4ce76f1156d4d8d85f5e10bb750b012f93ba7afb
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366682"
---
# <a name="kerberos-constrained-delegation-for-single-sign-on-to-your-apps-with-application-proxy"></a>アプリケーション プロキシを使ったアプリへのシングル サインオンの Kerberos の制約付き委任

統合 Windows 認証でセキュリティ保護されているアプリケーション プロキシを使って公開されているオンプレミスのアプリケーションにシングル サインオンを提供できます。 これらのアプリケーションにアクセスするには Kerberos チケットが必要です。 アプリケーション プロキシは、Kerberos の制約付き委任 (KCD) を使って、これらのアプリケーションをサポートします。 

統合 Windows 認証 (IWA) を使用してアプリケーションへのシングル サインオンができるようにするには、ユーザーの権限を借用できるように Active Directory でアプリケーション プロキシ コネクタにアクセス許可を与えます。 コネクタはこのアクセス許可を利用し、ユーザーの代理としてトークンを送受信します。

## <a name="how-single-sign-on-with-kcd-works"></a>KCD を使ったシングル サインオンのしくみ
この図は、IWA を使用するオンプレミス アプリケーションにユーザーがアクセスしようとしたときの流れを説明するものです。

![Microsoft AAD 認証のフロー図](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. ユーザーは、オンプレミスのアプリケーションにアプリケーション プロキシをとおしてアクセスするための URL を入力します。
2. この要求がアプリケーション プロキシによって Azure AD 認証サービスにリダイレクトされて、事前認証が行われます。 この時点で、Azure AD の認証および承認のポリシーのうち、該当するものが適用されます (たとえば多要素認証)。 ユーザーの正当性が確認された場合は、Azure AD によってトークンが作成されてユーザーに送信されます。
3. ユーザーは、このトークンをアプリケーション プロキシに渡します。
4. アプリケーション プロキシはこのトークンを検証し、ユーザー プリンシパル名 (UPN) をトークンから取り出してから、要求、UPN、およびサービス プリンシパル名 (SPN) をコネクタに送信します。この送信は、二重認証済みのセキュリティで保護されたチャネルをとおして行われます。
5. コネクタは、オンプレミス AD との KCD (Kerberos の制約付き委任) ネゴシエーションを実行します。このときに、ユーザーの代理でアプリケーションに対する Kerberos トークンを取得します。
6. Active Directory は、そのアプリケーション用の Kerberos トークンをコネクタに送信します。
7. コネクタは、AD から受信した Kerberos トークンを使用して、元の要求をアプリケーション サーバーに送信します。
8. アプリケーションから応答がコネクタに送信されます。この応答はアプリケーション プロキシ サービスに返され、最終的にユーザーに返されます。

## <a name="prerequisites"></a>前提条件
IWA アプリケーションでのシングル サインオンに着手する前に、ご使用の環境が次の設定と構成に対応していることを確認してください。

* 対象のアプリ (SharePoint Web アプリなど) が統合 Windows 認証を使用するように設定されていること。 詳細については、「[Kerberos 認証のサポートを有効にする](https://technet.microsoft.com/library/dd759186.aspx)」を参照してください。SharePoint の場合は、「[SharePoint 2013 で Kerberos 認証を計画する](https://technet.microsoft.com/library/ee806870.aspx)」を参照してください。
* 対象となるすべてのアプリに[サービス プリンシパル名](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spns-setspn-syntax-setspn-exe.aspx)があること。
* コネクタを実行するサーバーとアプリを実行するサーバーがドメインに参加し、かつ同じドメインまたは信頼する側のドメインに属していること。 ドメインへの参加の詳細については、「 [コンピューターをドメインに参加させる](https://technet.microsoft.com/library/dd807102.aspx)」を参照してください。
* Connector を実行しているサーバーに、ユーザーの TokenGroupsGlobalAndUniversal 属性を読み取るためのアクセス権があること。 既定ではそのように設定されていますが、環境のセキュリティを強化する過程で変更されている可能性があります。

### <a name="configure-active-directory"></a>Active Directory を構成する
Active Directory の構成は、アプリケーション プロキシ コネクタとアプリケーション サーバーが同じドメイン内にあるかどうかによって異なります。

#### <a name="connector-and-application-server-in-the-same-domain"></a>同じドメインにあるコネクタとアプリケーション サーバー
1. Active Directory で、**[ツール]** > **[ユーザーとコンピューター]** の順に移動します。
2. コネクタを実行しているサーバーを選択します。
3. 右クリックし、**[プロパティ]** > **[委任]** を選択します。
4. **[指定されたサービスへの委任でのみこのコンピューターを信頼する]** をクリックします。 
5. **[このアカウントが委任された資格情報を提示できるサービス]** の下で、アプリケーション サーバーの SPN ID の値を追加します。 これで、アプリケーション プロキシ コネクタは、AD において、リストで定義されたアプリケーションに対してユーザーの代理となることができるようになります。

   ![Connector-SVR のプロパティ ウィンドウのスクリーン ショット](./media/application-proxy-configure-single-sign-on-with-kcd/Properties.jpg)

#### <a name="connector-and-application-server-in-different-domains"></a>異なるドメインにあるコネクタとアプリケーション サーバー
1. ドメイン間の KCD を使用するための前提条件の一覧については、「 [ドメイン間の Kerberos の制約付き委任](https://technet.microsoft.com/library/hh831477.aspx)」を参照してください。
2. コネクタ サーバーの `principalsallowedtodelegateto` プロパティを使って、アプリケーション プロキシを有効にし、コネクタ サーバーを代行します。 アプリケーション サーバーは `sharepointserviceaccount`、委任サーバーは `connectormachineaccount` です。 Windows 2012 R2 では、例としてこのコードを使います。

        $connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

        Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

        Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount

Sharepointserviceaccount には、SPS コンピューター アカウントか、SPS アプリ プールの実行に使用されているサービス アカウントを指定できます。

## <a name="configure-single-sign-on"></a>Configure single sign-on 
1. 「 [アプリケーション プロキシを使用したアプリケーションの発行](application-proxy-publish-azure-portal.md)」で説明されている手順に従って、アプリケーションを発行します。 **[事前認証方法]** で **[Azure Active Directory]** が選択されていることを確認してください。
2. アプリケーションがエンタープライズ アプリケーションの一覧に表示されたら、アプリケーションを選択して **[シングル サインオン]** をクリックします。
3. シングル サインオン モードを **[統合 Windows 認証]** に設定します。  
4. アプリケーション サーバーの **[内部アプリケーション SPN]** を入力します。 この例では、公開されたアプリケーションの SPN は、http/www.contoso.com です。 この SPN は、コネクタが委任された資格情報を提供できるサービスの一覧に入っている必要があります。 
5. ユーザーの代わりに使うコネクタに**委任されたログイン ID** を選択します。 詳細については、「[さまざまなオンプレミス ID とクラウド ID の操作](#Working-with-different-on-premises-and-cloud-identities)」をご覧ください。

   ![高度なアプリケーションの構成](./media/application-proxy-configure-single-sign-on-with-kcd/cwap_auth2.png)  


## <a name="sso-for-non-windows-apps"></a>Windows 以外のアプリの SSO

Azure AD Application Proxy での Kerberos 委任フローは、Azure AD がクラウドでユーザーを認証するときから始まります。 要求がオンプレミスに到着すると、Azure AD アプリケーション プロキシ コネクタは、ローカルの Active Directory と交信することで、ユーザーに代わって Kerberos チケットを発行します。 このプロセスは Kerberos の制約付き委任 (KCD) と呼ばれます。 次のフェーズで、要求がこの Kerberos チケットを使用してバックエンド アプリケーションに送信されます。 

このような要求を送信する方法を定義するプロトコルはいくつかあります。 ほとんどの非 Windows サーバーは、SPNEGO でのネゴシエートを予期します。 このプロトコルは Azure AD アプリケーション プロキシでサポートされていますが、既定で無効になっています。 サーバーは、SPNEGO または標準 KCD のどちらかに構成できますが、両方に構成することはできません。

SPNEGO 用にコネクタ マシンを構成する場合は、そのコネクタ グループ内の他のすべてのコネクタも SPNEGO で構成されていることを確認してください。 標準 KCD を使用するアプリケーションは、SPNEGO 用に構成されていない他のコネクタを介してルーティングする必要があります。
 

SPNEGO を有効にするには:

1. 管理者として実行しているコマンド プロンプトを開きます。
2. コマンド プロンプトから、SPNEGO が必要なコネクタ サーバーに対して次のコマンドを実行します。

    ```
    REG ADD "HKLM\SOFTWARE\Microsoft\Microsoft AAD App Proxy Connector" /v UseSpnegoAuthentication /t REG_DWORD /d 1
    net stop WAPCSvc & net start WAPCSvc
    ```

Kerberos について詳しくは、「[All you want to know about Kerberos Constrained Delegation (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd)」(Kerberos の制約付き委任 (KCD) のすべて) をご覧ください。

非 Windows アプリは通常、ドメインのメール アドレスの代わりに、ユーザー名または SAM アカウント名を使います。 お使いのアプリケーションでこの状況が当てはまる場合、指定されたログイン ID フィールドを構成して、クラウド ID をアプリケーション ID に接続する必要があります。 

## <a name="working-with-different-on-premises-and-cloud-identities"></a>さまざまなオンプレミス ID とクラウド ID の操作
アプリケーション プロキシは、ユーザーのクラウド ID とオンプレミス ID は完全に同じであるとみなします。 このようになっていない場合でも、シングル サインオンに KCD を使うことができます。 アプリケーションごとに**指定されたログイン ID** を構成し、シングル サインオンを実行するときに使う ID を指定します。  

この機能により、オンプレミス ID とクラウド ID が異なる多くの組織で、ユーザーに別のユーザー名とパスワードの入力を要求することなく、クラウドからオンプレミスのアプリに SSO させることができます。 次のような組織が含まれます。

* 内部に複数のドメインがあり (joe@us.contoso.com、joe@eu.contoso.com)、クラウドに 1 つのドメインがある (joe@contoso.com)。
* 内部にルーティングできないドメイン名があり (joe@contoso.usa)、クラウドに法的なドメイン名がある。
* 内部でドメイン名を使用していない (joe)。
* オンプレミスとクラウドで異なるエイリアスを使用している。 たとえば、joe-johns@contoso.com と joej@contoso.com です。  

アプリケーション プロキシを使用すると、Kerberos チケットの取得に使用する ID を選択できます。 この設定は、アプリケーションごとに行います。 これらのオプションの中には、電子メール アドレスの形式を受け入れないシステムに適したものもあれば、代替ログイン用に設計されたものもあります。

![委任されたログイン ID パラメーターのスクリーンショット](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_upn.png)

委任されたログイン ID が使用されている場合、その値は組織内のすべてのドメインまたはフォレストで一意でないことがあります。 異なる 2 つのコネクタ グループを使用してこれらのアプリケーションを 2 回発行することで、この問題を回避できます。 これは各アプリケーションのユーザーが異なり、そのコネクタを異なるドメインに参加させることができるためです。

### <a name="configure-sso-for-different-identities"></a>ID が異なる場合の SSO の構成
1. Azure AD Connect の設定を、メイン ID が電子メール アドレス (mail) になるように構成します。 これはカスタマイズ プロセスの一部として、同期設定の **[ユーザー プリンシパル名]** フィールドを変更することで実行します。 これらの設定は、ユーザーが Office365、Windows10 デバイス、および Azure AD を ID ストアとして使用する他のアプリケーションにログインする方法も決定します。  
   ![ユーザーを識別するスクリーンショット - [ユーザー プリンシパル名] ドロップダウン リスト](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. 変更するアプリケーションの [アプリケーションの構成] 設定で、使用する **[委任されたログイン ID]** を選択します。

   * ユーザー プリンシパル名 (joe@contoso.com など)
   * 代替ユーザー プリンシパル名 (joed@contoso.local など)
   * ユーザー プリンシパル名のユーザー名部分 (joe など)
   * 代替ユーザー プリンシパル名のユーザー名部分 (joed など)
   * オンプレミスのソフトウェア アセット管理アカウント名 (ドメイン コントローラーの構成に依存)

### <a name="troubleshooting-sso-for-different-identities"></a>ID が異なる場合の SSO のトラブルシューティング
SSO プロセスにエラーがある場合は、「[トラブルシューティング](../application-proxy-back-end-kerberos-constrained-delegation-how-to.md)」で説明するように、コネクタ コンピューターのイベント ログに表示されます。
ただし、場合によっては、バックエンド アプリケーションが他のさまざまな HTTP 応答に応答している間に、要求が正常に送信されることがあります。 このような場合のトラブルシューティングは、アプリケーション プロキシ セッションのイベント ログで、コネクタ コンピューターのイベント番号 24029 を調べることから始める必要があります。 委任のために使用されたユーザー ID は、イベント詳細の [ユーザー] フィールドに表示されます。 セッション ログを有効にするには、イベント ビューアーで [表示] メニューの **[分析およびデバッグ ログの表示]** を選択します。

## <a name="next-steps"></a>次の手順

* [Kerberos の制約付き委任を使用するようにアプリケーション プロキシ アプリケーションを構成する方法](../application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [アプリケーション プロキシで発生した問題のトラブルシューティングを行う](application-proxy-troubleshoot.md)


最新のニュースと更新プログラムについては、 [アプリケーション プロキシに関するブログ](http://blogs.technet.com/b/applicationproxyblog/)

