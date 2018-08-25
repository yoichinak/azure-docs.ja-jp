---
title: Azure AD アプリのサービス利用規約とプライバシーに関する声明 | Microsoft Docs
description: Azure AD を使用するために登録されているアプリに対してサービス利用規約とプライバシーに関する声明を設定する方法について説明します。
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/23/2018
ms.author: celested
ms.reviwer: lenalepa, sureshja
ms.custom: aaddev
ms.openlocfilehash: cb05139241f92eb930a99c387e2f06cabac35caf
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39580407"
---
# <a name="terms-of-service-and-privacy-statement-for-registered-azure-active-directory-apps"></a>登録済み Azure Active Directory アプリのサービス利用規約とプライバシーに関する声明

Azure Active Directory (Azure AD) アカウントおよび Microsoft アカウントと統合されているアプリをビルドして管理する開発者は、アプリのサービス利用規約とプライバシーに関する声明へのリンクを含める必要があります。 サービス利用規約とプライバシーに関する声明は、ユーザーの同意エクスペリエンスからユーザーに提示されます。 これは、ユーザーがアプリを信頼できることを知るのに役立ちます。 サービス利用規約とプライバシーに関する声明は、ユーザー向けマルチテナント アプリに特に重要です。アプリは複数のディレクトリによって使用され、すべての Microsoft アカウントで利用できます。

お客様は自分のアプリのサービス利用規約とプライバシーに関する声明ドキュメントを作成し、これらのドキュメントへの URL を提供する責任があります。 これらのリンクを提供できないマルチテナント アプリの場合、アプリに対するユーザーの同意エクスペリエンスで、ユーザーがアプリに同意することを防ぐためのアラートが表示されます。

> [!NOTE]
> * シングルテナント アプリでは、アラートは表示されません。
> * この 2 つのリンクの一方または両方が存在しない場合は、アプリにアラートが表示されます。

## <a name="user-consent-experience"></a>ユーザーの同意エクスペリエンス

次の例では、サービス利用規約とプライバシーに関する声明を設定し、これらのリンクを設定していないときに、ユーザーの同意エクスペリエンスが表示されます。

![提供されたプライバシーに関する声明とサービス利用規約を含むスクリーンショット、および含まないスクリーンショット](./media/howto-add-terms-of-service-privacy-statement/user-consent-exp-privacy-statement-terms-service.png)

## <a name="formatting-links-to-the-terms-of-service-and-privacy-statement-documents"></a>サービス利用規約とプライバシーに関する声明のドキュメントへのリンクを書式設定する

自分のアプリのサービス利用規約とプライバシーに関する声明のドキュメントへのリンクを追加する前に、URL が以下のガイドラインに従っていることを確認します。

| ガイドライン     | 説明                           |
|---------------|---------------------------------------|
| 形式        | 有効な URL                             |
| 有効なスキーマ | HTTP および HTTPS</br>HTTPS を推奨 |
| 最大長    | 2048 文字                       |

例: `https://myapp.com/terms-of-service`、`https://myapp.com/privacy-statement`

## <a name="adding-links-to-the-terms-of-service-and-privacy-statement"></a>サービス利用規約とプライバシーに関する声明へのリンクを追加する

サービス利用規約とプライバシーに関する声明の準備ができたら、次のメソッドのいずれかを使用して、自分のアプリにこれらのドキュメントへのリンクを追加できます。
* [Azure portal を使用する](#registered-in-azure-portal)
* [アプリケーション登録ポータル、またはデベロッパー センター内](#registered-in-app-reg-portal)
* [アプリ オブジェクト JSON を使用する](#app-object-json)
* [MSGraph beta REST API を使用する](#msgraph-beta-rest-api)

### <a name="registered-in-azure-portal"></a>Azure portal でアプリを登録した場合
Azure portal でアプリを登録した場合、次の手順に従います。

1.  [Azure portal](https://portal.azure.com/) にサインインします。
2.  **[アプリの登録]**  セクションに移動して、自分のアプリを選択します。
3. アプリの  **[プロパティ]**  セクションを開きます。
4.  **[サービス利用規約 URL]**  と  **[プライバシーに関する声明 URL]**  フィールドに入力します。
5. 変更を保存します。

![サービス利用規約 URL とプライバシーに関する声明 URL を含むアプリのプロパティ セクション](./media/howto-add-terms-of-service-privacy-statement/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="registered-in-app-reg-portal"></a>アプリケーション登録ポータルでアプリを登録する場合
アプリケーション登録ポータルまたはデベロッパー センターでアプリを登録した場合、次の手順に従います。

1.  [アプリケーション登録ポータル](https://apps.dev.microsoft.com/)にサインインします。
2. 自分のアプリを選択して、 **[プロファイル]**  セクションまでスクロールします。
3.  **[サービス利用規約 URL]**  と  **[プライバシーに関する声明 URL]**  フィールドに入力します。
4. 変更を保存します。

![サービス利用規約 URL とプライバシーに関する声明 URL を含むアプリのプロファイル セクション](./media/howto-add-terms-of-service-privacy-statement/app-registration-portal-profile-terms-service-privacy-statement-urls.png)

### <a name="app-object-json"></a>アプリ オブジェクト JSON を使用する
アプリ オブジェクト JSON を直接変更する場合、Azure portal またはアプリケーション登録ポータルでマニフェスト エディターを使用して、自分のアプリのサービス利用規約とプライバシーに関する声明へのリンクを含めることができます。

```json
    "informationalUrls": { 
        "termsOfService": “<your_terms_of_service_url>”, 
        "privacy": “<your_privacy_statement_url>” 
    }
```

### <a name="msgraph-beta-rest-api"></a>MSGraph beta REST API を使用する
プログラムを使用してすべてのアプリを更新するには、MSGraph beta REST API を使用してすべてのアプリを更新し、サービス利用規約とプライバシーに関する声明のドキュメントへのリンクを含めることができます。

```
PATCH https://graph.microsoft.com/beta/applications/{application id}
{ 
    "appId": "{your application id}", 
    "info": { 
        "termsOfServiceUrl": "<your_terms_of_service_url>", 
        "supportUrl": null, 
        "privacyStatementUrl": "<your_privacy_statement_url>", 
        "marketingUrl": null, 
        "logoUrl": null 
    }
}
```

> [!NOTE]
> * 次のフィールド (`supportUrl`、`marketingUrl`、`logoUrl`) に割り当てた既存の値を上書きしないように注意してください。
> * MSGraph beta REST API は、Azure AD アカウントを使ってサインインしたときにのみ動作します。 個人用 Microsoft アカウントはサポートされていません。