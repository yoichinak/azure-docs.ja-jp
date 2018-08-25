---
title: Azure AD アプリケーション プロキシを使用したアプリの発行 | Microsoft Docs
description: Azure Portal で Azure AD アプリケーション プロキシを使用して、オンプレミス アプリケーションをクラウドに発行します。
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
ms.custom: it-pro
ms.openlocfilehash: 1224642bb7e6fc0c51b3f839a78449132db5b4bb
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364259"
---
# <a name="publish-applications-using-azure-ad-application-proxy"></a>Azure AD アプリケーション プロキシを使用してアプリケーションを発行する

Azure Active Directory (AD) アプリケーション プロキシを使用して、オンプレミス アプリケーションを発行してインターネット経由でアクセスできるようにすることで、リモート ワーカーをサポートできます。 これらのアプリケーションを Azure ポータルを通して発行して、ネットワークの外部からセキュリティで保護されたリモート アクセスを提供できます。

この記事では、アプリケーション プロキシを使用してオンプレミス アプリを発行する手順を説明します。 この記事を完了すると、ユーザーがアプリにリモートでアクセスできるようになります。 さらに、シングル サインオン、個人用設定の情報、セキュリティ要件などのアプリケーションの追加機能を構成する準備が整います。

アプリケーション プロキシを初めて使用する場合は、「[オンプレミス アプリケーションへの安全なリモート アクセスを実現する方法](application-proxy.md)」で、この機能の詳細を確認してください。

## <a name="before-you-begin"></a>開始する前に

この記事は、コネクタを既にインストールし、登録した状態を想定しています。 これらの手順をまだ実行する必要がある場合は、[アプリケーション プロキシの概要とコネクタのインストール](application-proxy-enable.md) を参照してください。

## <a name="publish-an-on-premises-app-for-remote-access"></a>リモート アクセスを行うためのオンプレミス アプリの発行

アプリケーション プロキシを使用してアプリケーションを発行するには、次の手順に従います。 コネクタをダウンロードして組織用に構成していない場合は、先に「[アプリケーション プロキシの概要とコネクタのインストール](application-proxy-enable.md)」に移動してください。その後、アプリを発行してください。

> [!TIP]
> アプリケーション プロキシを初めてテストする場合は、パスワード ベースの認証が設定されているアプリケーションを選択してください。 アプリケーション プロキシは、これ以外の種類の認証もサポートしていますが、短時間で操作を完了するには、パスワード ベースのアプリが最も適しています。 

1. [Azure Portal](https://portal.azure.com/) に管理者としてサインインします。
2. **[Azure Active Directory]** > **[エンタープライズ アプリケーション]** > **[新しいアプリケーション]** を選択します。

  ![エンタープライズ アプリケーションの追加](./media/application-proxy-publish-azure-portal/add-app.png)

3. **[すべて]** を選択し、**[オンプレミスのアプリケーション]** を選択します。  

  ![独自のアプリケーションの追加](./media/application-proxy-publish-azure-portal/add-your-own.png)

4. アプリケーションについて以下の情報を入力します。

   - **名前**: アクセス パネルと Azure ポータルに表示されるアプリケーションの名前。 

   - **内部 URL**: プライベート ネットワークの内部からアプリケーションにアクセスするための URL。 バックエンド サーバー上の特定のパスを指定して発行できます。この場合、サーバーのそれ以外のパスは発行されません。 この方法では、同じサーバー上の複数のサイトを別々のアプリとして発行し、それぞれに独自の名前とアクセス規則を付与することができます。

     > [!TIP]
     > パスを発行する場合は、アプリケーションに必要な画像、スクリプト、スタイル シートが、すべてそのパスに含まれていることを確認してください。 たとえば、アプリが https://yourapp/app にあり、 https://yourapp/media にあるイメージを使用している場合、 https://yourapp/ をパスとして発行する必要があります。 この内部 URL は、ユーザーに表示されるランディング ページである必要はありません。 詳細については、「[発行されたアプリのカスタム ホーム ページを設定する](application-proxy-configure-custom-home-page.md)」を参照してください。

   - **外部 URL**: ネットワークの外部からアプリにアクセスするためにユーザーが移動するアドレス。 既定のアプリケーション プロキシ ドメインを使用しない場合は、[Azure AD アプリケーション プロキシのカスタム ドメイン](application-proxy-configure-custom-domain.md)に関する記事を参照してください。
   - **事前認証**: ユーザーにアプリケーションへのアクセス権を付与する前にアプリケーション プロキシがユーザーを認証する方法。 

     - Azure Active Directory: アプリケーション プロキシによってユーザーが Azure AD のサインイン ページにリダイレクトされます。これにより、ディレクトリとアプリケーションに対するユーザーのアクセス許可が認証されます。 このオプションは、条件付きアクセスや Multi-Factor Authentication などの Azure AD のセキュリティ機能を活用できるように、既定のままにしておくことをお勧めします。
     - パススルー: アプリケーションにアクセスするための Azure Active Directory に対するユーザーの認証は必要ありません。 ただし、バックエンドで認証要件を設定できます。
   - **コネクタ グループ**: コネクタは、アプリケーションへのリモート アクセスを処理します。コネクタ グループを使用して、コネクタとアプリをリージョン、ネットワーク、または目的別に整理できます。 まだコネクタ グループを作成していない場合、アプリは **[既定]** グループに割り当てられます。

>[!NOTE]
>アプリケーションで接続に WebSocket が使用されている場合、WebSocket をサポートしているバージョン 1.5.612.0 以降のコネクタを使用していることと、割り当てられているコネクタ グループではこれらのコネクタのみを使用していることを確認してください。

   ![アプリケーションの作成](./media/application-proxy-publish-azure-portal/configure-app.png)
5. 必要に応じて、追加設定を構成します。 ほとんどのアプリケーションでは、これらの設定は既定の状態のままにしてください。 
   - **バックエンド アプリケーションのタイムアウト**: アプリケーションの認証と接続に時間がかかる場合のみ、この値を **[遅い]** に設定します。 
   - **ヘッダーの URL を変換する**: 認証要求でアプリケーションの元のホスト ヘッダーが必要でない場合を除き、この値は **[はい]** のままにします。
   - **[Translate URLs in Application Body]\(アプリケーションの本文内の URL を変換する\)**: 他のオンプレミス アプリケーションへのハードコーディングされた HTML リングがあり、カスタム ドメインを使用しない場合を除き、この値は **[いいえ]** のままにします。 詳細については、[Azure AD アプリケーション プロキシを使用したリンクの変換](application-proxy-configure-hard-coded-link-translation.md)に関する記事を参照してください。
   
   ![アプリケーションの作成](./media/application-proxy-publish-azure-portal/additional-settings.png)

6. **[追加]** を選択します。


## <a name="add-a-test-user"></a>テスト ユーザーの選択 

アプリが正常に発行されたことをテストするために、テスト ユーザー アカウントを追加します。 このアカウントが企業ネットワーク内からアプリにアクセスする権限を既に持っていることを確認します。

1. [クイック スタート] ブレードに戻り、**[Assign a user for testing (テスト用のユーザーの割り当て)]** を選択します。

  ![テスト用のユーザーの割り当て](./media/application-proxy-publish-azure-portal/assign-user.png)

2. [ユーザーとグループ] ブレードで、**[追加]** を選択します。

  ![ユーザーまたはグループの選択](./media/application-proxy-publish-azure-portal/add-user.png)

3. [割り当ての追加] ブレードで、**[ユーザーとグループ]** を選択し、追加するアカウントを選択します。 
4. **[割り当て]** を選択します。

## <a name="test-your-published-app"></a>発行したアプリのテスト

ブラウザーで、発行手順中に構成した外部 URL に移動します。 スタート画面が表示され、設定したテスト アカウントでサインインできるはずです。

![発行したアプリのテスト](./media/application-proxy-publish-azure-portal/test-app.png)


## <a name="next-steps"></a>次の手順
- [コネクタのダウンロード](application-proxy-enable.md)と[コネクタ グループの作成](application-proxy-connector-groups.md)を行って、アプリケーションを別のネットワークや場所に発行します。

- 新しく発行したアプリに対する[シングル サインオンを設定](application-proxy-configure-single-sign-on-password-vaulting.md)します。
