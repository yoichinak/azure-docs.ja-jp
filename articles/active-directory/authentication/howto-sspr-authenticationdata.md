---
title: Azure AD SSPR データ要件 |Microsoft Docs
description: Azure AD のセルフ サービスによるパスワード リセットのデータ要件とそれらを満たす方法
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 5d8fe6282d956d7f399aff9f7aa250c5061dc887
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2018
ms.locfileid: "39159568"
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>エンド ユーザーによる登録を必要としないパスワード リセットをデプロイする

Azure Active Directory (Azure AD) のセルフサービスによるパスワードのリセット (SSPR) をデプロイするには、認証データが存在する必要があります。 ユーザーに自分で認証データを入力させている組織もあります。 しかし、多くの組織では Active Directory に既に存在するデータと同期する方法が採用されています。 次の条件が満たされていれば、ユーザーが介入しなくても、同期されたデータは Azure AD と SSPR で利用できるようになります。
   * オンプレミスのディレクトリ内のデータが正しい形式になっていること。
   * [簡単設定を使って Azure AD Connect](./../connect/active-directory-aadconnect-get-started-express.md) が構成されていること。

正常に動作させるには、電話番号の形式が "*+<国コード> <電話番号>*" (例: +1 4255551234) になっている必要があります。

> [!NOTE]
> 国番号と電話番号の間にスペースを入れる必要があります。
>
> パスワードのリセットは内線番号をサポートしていません。 +1 4255551234X12345 の形式であっても、電話がかけられる前に内線番号は削除されます。

## <a name="fields-populated"></a>取り込まれるフィールド

Azure AD Connect で既定の設定を使用する場合、次のマッピングが行われます。

| オンプレミスの Active Directory | Azure AD |
| --- | --- |
| telephoneNumber | 会社電話 |
| mobile | 携帯電話 |

ユーザーが携帯電話番号を認証すると、Azure AD の [認証の連絡先情報] の下にある [電話番号] フィールドにもその番号が入力されます。

## <a name="authentication-contact-info"></a>認証の連絡先情報

グローバル管理者は、次のスクリーンショットに示すように、ユーザーの認証の連絡先情報を手動で設定できます。

![連絡先][Contact]

[電話番号] フィールドに電話番号が入力され、SSPR ポリシーの携帯電話が有効になると、その番号がパスワード リセット登録ページに表示され、また、パスワード リセット ワークフロー中に表示されます。

パスワード リセットには、[代替の電話] フィールドは使用されません。

[電子メール] フィールドにメール アドレスが入力され、SSPR ポリシーの電子メールが有効になると、その電子メールがパスワード リセット登録ページに表示され、また、パスワード リセット ワークフロー中に表示されます。

[連絡用メール アドレス] フィールドにメール アドレスが入力され、SSPR ポリシーの電子メールが有効になった場合、その電子メールはパスワード リセット登録ページには**表示されません**が、パスワード リセット ワークフロー中に表示されます。

## <a name="security-questions-and-answers"></a>セキュリティの質問と回答

セキュリティの質問と回答は、Azure AD テナントに安全に格納されており、ユーザーは [SSPR 登録ポータル](https://aka.ms/ssprsetup)を介してのみアクセスできます。 管理者は、別のユーザーの質問と回答の内容を表示したり、設定したり、変更したりすることはできません。

## <a name="what-happens-when-a-user-registers"></a>ユーザーの登録時に発生すること

ユーザーが登録するとき、登録ページには次のフィールドが設定されます。

* **認証用電話**
* **認証用電子メール**
* **セキュリティの質問と回答**

**[携帯電話]** または **[連絡用メール アドレス]** に値が指定されている場合、ユーザーはそれらの値を使用してすぐにパスワードをリセットすることができます。これはユーザーがサービスに登録していない場合でも実行できます。 さらに、これらの値は、ユーザーが初めて登録するときに表示され、ユーザーは必要に応じてそれらを変更することができます。 ユーザーが正常に登録された後、これらの値は、それぞれ **[認証用電話]** フィールドと **[認証用メール]** フィールドの固定値になります。

## <a name="set-and-read-the-authentication-data-through-powershell"></a>PowerShell を使用した認証データの設定と読み取り

PowerShell を使用して、次のフィールドを設定することができます。

* **連絡用メール アドレス**
* **携帯電話**
* **会社電話** - オンプレミス ディレクトリと同期していない場合にのみ設定できます

### <a name="use-powershell-version-1"></a>PowerShell バージョン 1 を使う

操作を開始するには、[Azure AD PowerShell モジュールをダウンロードしてインストールする](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule)必要があります。 インストールした後、次の手順を使って各フィールドを構成できます。

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>PowerShell バージョン 1 を使って認証データを設定する

```
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>PowerShell バージョン 1 を使って認証データを読み取る

```
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>[認証用電話] と [認証用電子メール] オプションを読み取る

PowerShell バージョン 1 で **[認証用電話]** と **[認証用電子メール]** を読み取るには、次のコマンドを使います。

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>PowerShell バージョン 2 を使う

始めるには、[Azure AD バージョン 2 PowerShell モジュールをダウンロードしてインストールする](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0)必要があります。 インストールした後、次の手順を使って各フィールドを構成できます。

Install-Module をサポートする PowerShell の最新バージョンから簡単にインストールするには、次のコマンドを実行します  (最初の行では、モジュールが既にインストールされているかどうかを確認しています)。

```
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>PowerShell バージョン 2 を使って認証データを設定する

```
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>PowerShell バージョン 2 を使って認証データを読み取る

```
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>次の手順

* [SSPR のロールアウトを正常に完了する方法](howto-sspr-deployment.md)
* [パスワードのリセットまたは変更](../user-help/active-directory-passwords-update-your-own-password.md)
* [セルフサービスのパスワード リセットのための登録](../user-help/active-directory-passwords-reset-register.md)
* [ライセンスに関する質問](concept-sspr-licensing.md)
* [ユーザーが使用できる認証方法](concept-sspr-howitworks.md#authentication-methods)
* [SSPR のポリシー オプション](concept-sspr-policy.md)
* [パスワード ライトバックの概要とその必要性](howto-sspr-writeback.md)
* [SSPR でアクティビティをレポートする方法](howto-sspr-reporting.md)
* [SSPR のすべてのオプションとその意味](concept-sspr-howitworks.md)
* [不具合が発生していると思われるSSPR のトラブルシューティング方法](active-directory-passwords-troubleshoot.md)
* [質問したい内容に関する説明がどこにもない。](active-directory-passwords-faq.md)

[Contact]: ./media/howto-sspr-authenticationdata/user-authentication-contact-info.png "グローバル管理者によるユーザーの認証の連絡先情報の変更"
