---
title: 'チュートリアル: Pingboard を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs'
description: Azure Active Directory を構成して、ユーザー アカウントを Pingboard に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: sakula
ms.assetid: 0b38ee73-168b-42cb-bd8b-9c5e5126d648
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: asmalser
ms.reviewer: asmalser
ms.openlocfilehash: c5cc20b4f20e3a4f746ab15aa5f139c8e7201a2c
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39447168"
---
# <a name="tutorial-configure-pingboard-for-automatic-user-provisioning"></a>チュートリアル: Pingboard を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、Azure Active Directory (Azure AD) から Pingboard にユーザー アカウントを自動的にプロビジョニング/プロビジョニング解除するために必要な手順について説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

*   Azure AD テナント
*   Pingboard テナント ([Pro アカウント](https://pingboard.com/pricing)) 
*   管理者アクセス許可がある Pingboard のユーザー アカウント 

> [!NOTE] 
> Azure AD プロビジョニング統合では、ご利用のアカウントから使用できる [Pingboard API](`https://your_domain.pingboard.com/scim/v2`) が必要です。

## <a name="assign-users-to-pingboard"></a>Pingboard へのユーザーの割り当て

Azure AD では、選択されたアプリケーションへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー アカウント プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーのみが同期されます。 

プロビジョニング サービスを構成して有効にする前に、Pingboard アプリにアクセスする必要がある Azure AD 内のユーザーを決定しておく必要があります。 その後、次の手順でこれらのユーザーを Pingboard アプリに割り当てることができます。

[エンタープライズ アプリケーションにユーザーを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-pingboard"></a>ユーザーを Pingboard に割り当てる際の重要なヒント

Pingboard には、Azure AD ユーザーを 1 人だけ割り当てて、プロビジョニングの構成をテストすることをお勧めします。 その他のユーザーは後で割り当てることができます。

## <a name="configure-user-provisioning-to-pingboard"></a>Pingboard へのユーザー プロビジョニングの構成 

このセクションでは、Pingboard のユーザー アカウント プロビジョニング API に Azure AD を接続する手順を説明します。 Azure AD でのユーザーの割り当てに基づいて、Pingboard での割り当て済みユーザー アカウントの作成、更新、および無効化を行うプロビジョニング サービスを構成することもできます。

> [!TIP]
> Pingboard で SAML ベースのシングル サインオンを有効にするには、[Azure Portal](https://portal.azure.com) で説明されている手順に従ってください。 シングル サインオンは自動プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-account-provisioning-to-pingboard-in-azure-ad"></a>Azure AD で Pingboard への自動ユーザー アカウント プロビジョニングを構成する

1. [Azure Portal](https://portal.azure.com) で、**[Azure Active Directory]** > **[エンタープライズ アプリ]** > **[すべてのアプリケーション]** セクションの順に移動します。

1. シングル サインオンのために Pingboard を既に構成している場合は、検索フィールドで Pingboard のインスタンスを検索します。 構成していない場合は、**[追加]** を選択してアプリケーション ギャラリーで **Pingboard** を検索します。 検索結果から **Pingboard** を選択してアプリケーションの一覧に追加します。

1. Pingboard のインスタンスを選択してから、**[プロビジョニング]** タブを選択します。

1. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![Pingboard のプロビジョニング](./media/pingboard-provisioning-tutorial/pingboardazureprovisioning.png)
    
1. **[管理者資格情報]** セクションで、次の手順を実行します。

    a. **[テナントの URL]** に「`https://your_domain.pingboard.com/scim/v2`」と入力します。"your_domain" は、実際のドメインに置き換えてください。

    b. 管理者アカウントを使用して [Pingboard](https://pingboard.com/) にサインインします。

    c. **[アドオン]** > **[統合]** > **[Azure Active Directory]** の順に選択します。

    d. **[構成]** タブをクリックし、**[Enable user provisioning from Azure]\(Azure からのユーザー プロビジョニングを有効にする\)** に移動します。

    e. **[OAuth Bearer Token]\(OAuth ベアラー トークン\)** からトークンをコピーして、**[シークレット トークン]** に入力します。

1. Azure Portal で、**[テスト接続]** を選択して Azure AD が Pingboard アプリに接続できることを確認します。 接続が失敗した場合、使用中の Pingboard アカウントに Admin アクセス許可があることを確認して、**[テスト接続]** の手順をもう一度試してください。

1. プロビジョニングのエラー通知を受け取るユーザーまたはグループのメール アドレスを **[通知用メール]** に入力し、 その下のチェック ボックスをオンにします。

1. **[保存]** を選択します。 

1. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Pingboard]\(Azure Active Directory ユーザーを Pingboard に同期する\)** を選びます。

1. **[属性マッピング]** セクションで、Azure AD から Pingboard に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Pingboard のユーザー アカウントとの照合に使用されます。 すべての変更をコミットするには、**[保存]** を選択します。 詳細については、[ユーザー プロビジョニング属性マッピングのカスタマイズ](../active-directory-saas-customizing-attribute-mappings.md)に関するページを参照してください。

1. Pingboard に対して Azure AD プロビジョニング サービスを有効にするには、**[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

1. **[保存]** を選択すると、Pingboard に割り当てられたユーザーの初期同期が開始されます。

初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用して、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ ログを取得できます。 これらのログには、Pingboard アプリでプロビジョニング サービスによって実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../active-directory-saas-provisioning-reporting.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
* [シングル サインオンの構成](pingboard-tutorial.md)
