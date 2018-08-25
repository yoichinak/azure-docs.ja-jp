---
title: アプリケーションのブランド化ガイドライン | Microsoft Docs
description: Azure Active Directory の開発者向けリソースの包括的なガイド
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 72f4e464-1352-4a49-a18f-c37f58e7d5c4
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: celested
ms.reviewer: arielgo
ms.custom: aaddev, signin_art
ms.openlocfilehash: 78a0b9bf7f49cc41c8d78287bcbe54c5c88f0809
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2018
ms.locfileid: "39597907"
---
# <a name="branding-guidelines-for-applications"></a>アプリケーションのブランド化ガイドライン

この記事では、Azure Active Directory (Azure AD) でアプリケーションを開発するときに使用するブランド化ガイドラインについて説明します。 Azure AD で管理されている職場または学校のアカウント、あるいは個人のアカウントをサインアップやサインインに使用したいと考えているアプリケーションの利用者向けの案内としてお役立てください。

## <a name="personal-accounts-vs-work-or-school-accounts-from-microsoft"></a>Microsoft の個人アカウントと職場または学校アカウント

Microsoft は次の 2 種類のユーザー アカウントを管理しています。

* **個人アカウント** (以前の Windows Live ID): このアカウントは、"*個々の*" ユーザーと Microsoft の関係を表し、コンシューマー向けデバイスや Microsoft のサービスにアクセスする際に使用されます。 このアカウントは個人的に使用するためのものです。
* **職場または学校アカウント:** このアカウントは、Azure Active Directory を使用する組織に代わって Microsoft が管理しています。 このアカウントは、Office 365 や Microsoft の他のビジネス サービスにサインインする際に使用されます。

通常、Microsoft の職場または学校アカウントは、組織 (企業、学校、政府機関) がエンド ユーザー (従業員、学生、公務員) に割り当てます。 このアカウントは、Azure AD プラットフォームのクラウドで直接管理されるか、Windows Server Active Directory などのオンプレミス ディレクトリから Azure AD に同期されます。 職場または学校アカウントの " *管理人* " は Microsoft ですが、アカウントは組織が所有し、管理しています。

## <a name="referring-to-azure-ad-accounts-in-your-application"></a>アプリケーションでの Azure AD アカウントの呼び方

Microsoft は、Azure または Active Directory のブランド名をエンド ユーザーに表示していません。開発者もこれらを表示しないようにする必要があります。

* ユーザーがサインインしたら、できるだけ組織の名前とロゴを使用します。 "組織" のような総称を使用するのではなく、この方法をお勧めします。
* ユーザーがサインインしていないときは、アカウントを "職場または学校アカウント" と呼び、Microsoft のロゴを使用して、Microsoft がこれらのアカウントを管理していることを伝えます。 "企業アカウント"、"ビジネス アカウント"、"会社アカウント" などの言葉はユーザーの混乱を招くので使用しないでください。

## <a name="user-account-pictogram"></a>ユーザー アカウントのピクトグラム

以前のバージョンのガイドラインでは、"ブルー バッジ" のピクトグラムを使用することを推奨していました。 ユーザーと開発者のフィードバックに基づき、現在は代わりに Microsoft のロゴを使用することを推奨しています。 Microsoft のロゴを使用することにより、ユーザーは、アプリケーションにサインインするときに、Office 365 や他の Microsoft ビジネス サービスで使用しているアカウントを再利用できることを理解しやすくなります。

## <a name="signing-up-and-signing-in-with-azure-ad"></a>Azure AD によるサインアップとサインイン

アプリケーションでは、サインアップとサインインに別々のパスを示すことがあります。以下のセクションでは、この 2 つのシナリオの表示に関するガイダンスを示します。

**アプリケーションがエンド ユーザーのサインアップをサポートしている場合 (無料試用版やフリーミアム モデルなど)**: ユーザーが、その職場のアカウントまたは個人のアカウントを使用してアプリケーションにアクセスできるようにするための**サインイン** ボタンを表示できます。 Azure AD は、ユーザーがアプリケーションに初めてアクセスしたときに同意プロンプトを表示します。

**アプリケーションに管理者だけが同意できるアクセス許可が必要な場合、またはアプリケーションに組織のライセンスが必要な場合**: 管理者による取得をユーザー サインインから分離する必要があります。 **"このアプリケーションを入手" ボタン** で管理者をサインインにリダイレクトし、組織のユーザーに代わって同意するよう求めることにより、エンドユーザーにアプリケーションで同意画面が表示されないという追加のメリットもあります。

## <a name="visual-guidance-for-app-acquisition"></a>アプリケーションの取得の表示に関するガイダンス

"アプリケーションの入手" リンクでは、ユーザーを Azure AD のアクセス権の付与 (承認) ページにリダイレクトする必要があります。これにより、組織の管理者は、Microsoft がホストする組織のデータへのアクセス権をアプリケーションに付与できます。 アクセス権の要求方法の詳細については、記事「[Azure Active Directory とアプリケーションの統合](quickstart-v1-integrate-apps-with-azure-ad.md)」を参照してください。

管理者は、アプリケーションに同意したら、ユーザーの Office 365 アプリ起動ツール (ワッフルおよび [https://portal.office.com/myapps](https://portal.office.com/myapps)からアクセス可能) にアプリケーションを追加することを選択できます。 この機能を公表する場合は、"このアプリケーションを組織に追加" のような言葉を使って、次のようなボタンを表示できます。

![アプリケーションの種類とシナリオ](./media/howto-add-branding-in-azure-ad-apps/add-to-my-org.png)

ただし、ボタンに頼るのではなく、説明文を作成することをお勧めします。 例: 

> 「*Office 365 や Microsoft の他のビジネス サービスを既にお使いの場合は、組織のデータへのアクセス権を <your_app_name> に付与できます。これにより、ユーザーは既存の職場アカウントを使用して <your_app_name> にアクセスできるようになります。*」

公式の Microsoft ロゴをダウンロードしてアプリで使用するためには、使用するロゴを右クリックして、コンピューターに保存します。

| Asset                                | PNG 形式 | SVG 形式 |
| ------------------------------------ | ---------- | ---------- |
| Microsoft のロゴ  | ![Microsoft のロゴ PNG](./media/howto-add-branding-in-azure-ad-apps/MS-SymbolLockup_MSSymbol_19.png) | ![Microsoft のロゴ SVG](./media/howto-add-branding-in-azure-ad-apps/MS-SymbolLockup_MSSymbol_19.svg) |

## <a name="visual-guidance-for-sign-in"></a>サインインの表示に関するガイダンス

アプリケーションでは、Azure AD との統合に使用するプロトコルに対応するサインイン エンドポイントにユーザーをリダイレクトするサインイン ボタンを表示する必要があります。 次のセクションでは、ボタンの外観について詳しく説明します。

### <a name="pictogram-and-sign-in-with-microsoft"></a>ピクトグラムと "Microsoft でサインイン"

これは、Microsoft のロゴと "Microsoft でサインイン" という言葉とを関連付けたものであり、アプリケーションがサポートするさまざまな ID プロバイダーの中で Azure AD を一意に表します。 スペースが狭く "Microsoft でサインイン" という文字列が収まりきらない場合は、単に "サインイン" としても問題ありません。 ボタンには、薄い色調または濃い色調を使用できます。

次の図は、アプリで資産を使用する場合に Microsoft が推奨する赤線です。 赤線は "Microsoft アカウントでサインイン" や短縮バージョンの "サインイン" に適用します。

![Microsoft アカウントでサインインの赤線](./media/howto-add-branding-in-azure-ad-apps/Sign-in-with-Microsoft-redlines.png)

公式の画像をダウンロードしてアプリで使用するためには、使用する画像を右クリックして、コンピューターに保存します。

| Asset                                | PNG 形式 | SVG 形式 |
| ------------------------------------ | ---------- | ---------- |
| Microsoft アカウントでサインイン (濃い色調)  | ![濃い色調のサインイン ボタン PNG](./media/howto-add-branding-in-azure-ad-apps/MS-SymbolLockup_SignIn_dark.png) | ![濃い色調の Microsoft アカウントでサインイン ボタン SVG](./media/howto-add-branding-in-azure-ad-apps/MS-SymbolLockup_SignIn_dark.svg) |
| Microsoft アカウントでサインイン (薄い色調) | ![薄い色調のサインイン ボタン PNG](./media/howto-add-branding-in-azure-ad-apps/MS-SymbolLockup_SignIn_light.png) | ![薄い色調の Microsoft アカウントでサインイン ボタン SVG](./media/howto-add-branding-in-azure-ad-apps/MS-SymbolLockup_SignIn_light.svg) |
| サインイン (濃い色調)                 | ![濃い色調のサインイン短縮版ボタン PNG](./media/howto-add-branding-in-azure-ad-apps/MS-SymbolLockup_SignIn_dark_short.png) | ![濃い色調のサインイン短縮版ボタン SVG](./media/howto-add-branding-in-azure-ad-apps/MS-SymbolLockup_SignIn_dark_short.svg) |
| サインイン (薄い色調)                | ![薄い色調のサインイン短縮版ボタン PNG](./media/howto-add-branding-in-azure-ad-apps/MS-SymbolLockup_SignIn_light_short.png) | ![薄い色調のサインイン短縮版ボタン SVG](./media/howto-add-branding-in-azure-ad-apps/MS-SymbolLockup_SignIn_light_short.svg) |


## <a name="branding-dos-and-donts"></a>ブランド化に関する注意事項

"職場または学校アカウント" は、"Microsoft でサインイン" ボタンと組み合わせて**使用してください**。補足的な説明を与えることで、その使用の可否をエンド ユーザーが認識しやすいようにします。 "企業アカウント"、"ビジネス アカウント"、"会社アカウント" などの言葉は**使用しないでください**。

"Office 365 ID" または"Azure ID" を使用し**ないでください**。 Office 365 は、Azure AD を認証に使用しない Microsoft のコンシューマー向け製品の名前でもあります。

Microsoft のロゴを変更 **しない** でください。

Azure または Active Directory のブランドをエンド ユーザーに表示**しない**でください。 ただし、開発者、IT プロフェッショナル、管理者に対しては、これらの用語を使用してもかまいません。

## <a name="navigation-dos-and-donts"></a>ナビゲーションに関する注意事項

ユーザーがサインアウトし、別のユーザー アカウントに切り替える方法を提供してく**ださい**。 ほとんどのユーザーは Microsoft/Facebook/Google/Twitter の個人アカウントを 1 つ持っていますが、多くの場合、ユーザーは複数の組織に関係しています。 間もなく、複数のサインイン ユーザーがサポートされるようになります。
