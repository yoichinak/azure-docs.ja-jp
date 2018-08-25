---
title: Azure AD の B2B ユーザーにオンプレミスのアプリケーションへのアクセスを許可する | Microsoft Docs
description: Azure AD B2B コラボレーションを使用してクラウド B2B ユーザーにオンプレミスのアプリケーションへのアクセスを許可する方法について説明します。
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/20/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 9c9cde23f8f8dc557b99d2f1508c923aaa2dce91
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2018
ms.locfileid: "42142426"
---
# <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications"></a>Azure AD の B2B ユーザーにオンプレミスのアプリケーションへのアクセスを許可する

Azure Active Directory (Azure AD) B2B コラボレーション機能を使用して、パートナー組織のゲスト ユーザーを Azure AD に招待している組織の場合、これらの B2B ユーザーにオンプレミスのアプリへのアクセスを提供できるようになりました。 これらのオンプレミス アプリケーションでは、Kerberos の制約付き委任 (KCD) と共に SAML ベースの認証または統合 Windows 認証 (IWA) を使用できます。

## <a name="access-to-saml-apps"></a>SAML アプリケーションへのアクセス

オンプレミス アプリケーションが SAML ベースの認証を使用している場合、Azure Portal を使用して Azure AD B2B コラボレーション ユーザーがそれらのアプリケーションを簡単に利用できるようになります。

以下の両方を行う必要があります。

- 「[Azure Active Directory アプリケーション ギャラリーに含まれていないアプリケーションへのシングル サインオンの構成](../active-directory-saas-custom-apps.md)」の説明に従って、非ギャラリー アプリケーション テンプレートを使用して SAML アプリケーションを統合します。 **[サインオン URL]** 値に使用する URL をメモしておきます。
-  Azure AD アプリケーション プロキシを使用して、**Azure Active Directory** を認証ソースとして構成して、オンプレミス アプリを発行します。 手順については、「[Azure AD アプリケーション プロキシを使用してアプリケーションを発行する](../manage-apps/application-proxy-publish-azure-portal.md)」を参照してください。 

   **[内部 URL]** 設定を構成するときは、ギャラリー以外のアプリケーション テンプレートで指定したサインオン URL を使用します。 このような方法で、ユーザーは組織の境界外からアプリにアクセスできるようになります。 アプリケーション プロキシは、オンプレミス アプリの SAML シングル サインオンを実行します。
 
   ![オンプレミス アプリ設定の内部 URL と認証を表示する](media/hybrid-cloud-to-on-premises/OnPremAppSettings.PNG)

## <a name="access-to-iwa-and-kcd-apps"></a>IWA および KCD アプリへのアクセス

統合 Windows 認証と Kerberos の制約付き委任を使用してセキュリティで保護されたオンプレミス アプリケーションへのアクセス権を B2B ユーザーに付与するには、次のコンポーネントが必要です。

- **Azure AD アプリケーション プロキシを介した認証**。 B2B ユーザーは、オンプレミス アプリケーションに対して認証できる必要があります。 これを行うには、Azure AD アプリケーション プロキシを介してオンプレミス アプリを発行する必要があります。 詳細については、「[アプリケーション プロキシの概要とコネクタのインストール](../manage-apps/application-proxy-enable.md)」と「[Azure AD アプリケーション プロキシを使用してアプリケーションを発行する](../manage-apps/application-proxy-publish-azure-portal.md)」を参照してください。
- **オンプレミス ディレクトリの B2B ユーザー オブジェクトを介した承認**。 アプリケーションは、ユーザー アクセス チェックを実行し、正しいリソースへのアクセス権を付与できる必要があります。 IWA と KCD がこの承認を完了するには、オンプレミスの Windows Server Active Directory 内のユーザー オブジェクトが必要です。 「[KCD を使ったシングル サインオンのしくみ](../manage-apps/application-proxy-configure-single-sign-on-with-kcd.md#how-single-sign-on-with-kcd-works)」で説明されているように、アプリケーション プロキシはこのユーザー オブジェクトを使用してユーザーを偽装し、アプリに対する Kerberos トークンを取得する必要があります。 

   B2B ユーザーのシナリオでは、オンプレミス ディレクトリでの承認に必要なゲスト ユーザー オブジェクトの作成に使用できる方法が 2 つあります。

   - Microsoft Identity Manager (MIM) と [Microsoft Graph 用 MIM 管理エージェント](#create-b2b-guest-user-objects-through-mim-preview)。 
   - [PowerShell スクリプト](#create-b2b-guest-user-objects-through-a-script-preview)。 スクリプトの使用は、MIM を必要としない、より軽い解決策です。 

次の図は、Azure AD アプリケーション プロキシと、オンプレミス ディレクトリ内の B2B ユーザー オブジェクトの生成を連携して、B2B ユーザーにオンプレミス IWA および KCD アプリへのアクセス権を付与する方法の概要を示しています。 番号が付いた手順については、図の下の詳細な説明を参照してください。

![MIM および B2B スクリプトの解決策の図](media/hybrid-cloud-to-on-premises/MIMScriptSolution.PNG)

1.  パートナー組織のユーザー (Fabrikam テナント) が Contoso テナントに招待されます。
2.  ゲスト ユーザー オブジェクトが Contoso テナントに作成されます (たとえば、UPN が guest_fabrikam.com#EXT#@contoso.onmicrosoft.com のユーザー オブジェクト)。
3.  MIMO または B2B PowerShell スクリプトを介して Fabrikam ゲストが Contoso からインポートされます。
4.  Fabrikam ゲスト ユーザー オブジェクト (Guest#EXT#) の表現つまり "フットプリント" が、MIM または B2B PowerShell スクリプトを介してオンプレミス ディレクトリの Contoso.com に作成されます。
5.  ゲスト ユーザーがオンプレミスのアプリケーションの app.contoso.com にアクセスします。
6.  認証要求が、Kerberos の制約付き委任を使用して Application Proxy を介して承認されます。 
7.  ゲスト ユーザー オブジェクトがローカルに存在するため、認証は成功します。

### <a name="lifecycle-management-policies"></a>ライフサイクル管理ポリシー

ライフサイクル管理ポリシーを使用して、オンプレミス B2B ユーザー オブジェクトを管理できます。 例: 

- アプリケーション プロキシ認証中に多要素認証 (MFA) が使用されるようにゲスト ユーザーの MFA ポリシーを設定することができます。 詳細は、「[B2B コラボレーション ユーザーの条件付きアクセス](conditional-access.md)」をご覧ください。
- クラウド B2B ユーザーに対して実行されるスポンサー、アクセス レビュー、アカウントの検証などが、オンプレミス ユーザーに適用されます。 たとえば、ライフサイクル管理ポリシーを使用してクラウド ユーザーが削除された場合、MIM 同期または Azure AD Connect 同期によってオンプレミス ユーザーも削除されます。詳細については、「[Azure AD のアクセス レビューによるゲスト アクセスの管理](../active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)」を参照してください。

### <a name="create-b2b-guest-user-objects-through-mim-preview"></a>MIM を介した B2B ゲスト ユーザー オブジェクトの作成 (プレビュー)

MIM 2016 Service Pack 1 および Microsoft Graph の MIM 管理エージェントを使用してオンプレミス ディレクトリにゲスト ユーザー オブジェクトを作成する方法については、「[Azure AD business-to-business (B2B) collaboration with Microsoft Identity Manager(MIM) 2016 SP1 with Azure Application Proxy (Public Preview)](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016-graph-b2b-scenario)」(Azure アプリケーション プロキシを使用した Azure AD B2B (Business-to-Business) と Microsoft Identity Manager (MIM) 2016 SP1 のコラボレーション (パブリック プレビュー)) を参照してください。

### <a name="create-b2b-guest-user-objects-through-a-script-preview"></a>スクリプトを介した B2B ゲスト ユーザー オブジェクトの作成 (プレビュー)

オンプレミスの Active Directory にゲスト ユーザー オブジェクトを作成する場合の出発点として使用できる PowerShell サンプル スクリプトがあります。

[ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=51495)からスクリプトと Readme ファイルをダウンロードできます。 **スクリプトと Readme を選択して、Azure AD B2B ユーザーの on-prem.zip** ファイルを取得します。

スクリプトを使用する前に、関連する Readme ファイルの前提条件と重要な考慮事項を確認してください。 また、スクリプトはサンプルとしてのみ提供されている点を理解してください。 開発チームまたはパートナーは、スクリプトを実行する前にカスタマイズして確認する必要があります。

## <a name="license-considerations"></a>ライセンスに関する考慮事項

オンプレミス アプリにアクセスする外部ゲスト ユーザーに対して、正しいクライアント アクセス ライセンス (CAL) があることを確認してください。 詳細については、「[クライアント アクセス ライセンスと マネジメント ライセンス](https://www.microsoft.com/licensing/product-licensing/client-access-license.aspx)」の「エクスターナル コネクタ」セクションを参照してください。 具体的なライセンス要件については、マイクロソフトの担当者または地域の販売代理店にお問い合わせください。

## <a name="next-steps"></a>次の手順

- [ハイブリッド組織向けの Azure Active Directory B2B コラボレーション](hybrid-organizations.md)

- Azure AD Connect の概要については、「[オンプレミスのディレクトリと Azure Active Directory の統合](../connect/active-directory-aadconnect.md)」を参照してください。

