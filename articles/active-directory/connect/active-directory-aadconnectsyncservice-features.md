---
title: Azure AD Connect 同期サービスの機能と構成 | Microsoft Docs
description: Azure AD Connect 同期サービスのサービス側の機能について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 213aab20-0a61-434a-9545-c4637628da81
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/25/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: f721c371687addfe48d753e7289df78c2be1f3c7
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2018
ms.locfileid: "36958002"
---
# <a name="azure-ad-connect-sync-service-features"></a>Azure AD Connect 同期サービスの機能
Azure AD Connect の同期機能には 2 つのコンポーネントがあります。

* **Azure AD Connect Sync** という名前のオンプレミスのコンポーネント: **同期エンジン**とも呼ばれます。
* Azure AD 内にあるサービス: **Azure AD Connect 同期サービス**

このトピックでは、 **Azure AD Connect 同期サービス** の各種機能のしくみと、Windows PowerShell を使用してそれらを構成する方法について説明します。

これらの設定は、 [Windows PowerShell 用 Azure Active Directory モジュール](https://aka.ms/aadposh)で構成されます。 これは、Azure AD Connect とは別にダウンロードしてインストールします。 このトピックで紹介するコマンドレットは、 [2016 年 3 月のリリース (ビルド 9031.1)](http://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx#Version_9031_1)で導入されたものです。 このトピックに記載されているコマンドレットがないか、同じ結果が生成されない場合は、最新バージョンが実行されていることを確認してください。

Azure AD ディレクトリ内の構成を確認するには、 `Get-MsolDirSyncFeatures`を実行します。  
![Get-MsolDirSyncFeatures result](./media/active-directory-aadconnectsyncservice-features/getmsoldirsyncfeatures.png)

これらの設定の多くは、Azure AD Connect でのみ変更できます。

以下の設定は、 `Set-MsolDirSyncFeature`で設定できます。

| DirSyncFeature | Comment (コメント) |
| --- | --- |
| [EnableSoftMatchOnUpn](#userprincipalname-soft-match) |プライマリ SMTP アドレスに加えて userPrincipalName でオブジェクトを結合できます。 |
| [SynchronizeUpnForManagedUsers](#synchronize-userprincipalname-updates) |同期エンジンに管理対象ユーザー/ライセンス ユーザー (非フェデレーション ユーザー) の userPrincipalName 属性の更新を許可します。 |

いったん機能を有効にすると、後でもう一度無効にすることはできません。

> [!NOTE]
> 2016 年 8 月 24 日以降、" *重複属性の回復性* " 機能は、新しい Azure AD ディレクトリに対して既定で有効になります。 また、この機能はロールアウトされ、この日付より前に作成されたディレクトリでも有効になります。 お使いのディレクトリでこの機能が有効になるときに電子メール通知を受け取ります。
> 
> 

以下の設定は、Azure AD Connect によって構成されており、 `Set-MsolDirSyncFeature`で変更することはできません。

| DirSyncFeature | コメント |
| --- | --- |
| DeviceWriteback |[Azure AD Connect: デバイスの書き戻しの有効化](active-directory-aadconnect-feature-device-writeback.md) |
| DirectoryExtensions |[Azure AD Connect 同期: ディレクトリ拡張機能](active-directory-aadconnectsync-feature-directory-extensions.md) |
| [DuplicateProxyAddressResiliency<br/>DuplicateUPNResiliency](#duplicate-attribute-resiliency) |エクスポート時に、別のオブジェクトとの重複がある場合、オブジェクト全体が失敗するのではなく、属性を検疫できます。 |
| パスワード ハッシュの同期 |[Azure AD Connect Sync によるパスワード ハッシュ同期の導入](active-directory-aadconnectsync-implement-password-hash-synchronization.md) |
|パススルー認証|[Azure Active Directory パススルー認証によるユーザー サインイン](active-directory-aadconnect-pass-through-authentication.md)|
| UnifiedGroupWriteback |[プレビュー: グループの書き戻し](active-directory-aadconnect-feature-preview.md#group-writeback) |
| UserWriteback |現在、サポートされていません。 |

## <a name="duplicate-attribute-resiliency"></a>重複属性の回復性
UPN や proxyAddress が重複している場合、そのオブジェクトのプロビジョニングが失敗する代わりに、重複している属性を "検疫" し、一時的な値を割り当てます。 競合が解決されると、一時的な UPN は自動的に適切な値に変更されます。 詳細については、「 [ID 同期と重複属性の回復性](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md)」を参照してください。

## <a name="userprincipalname-soft-match"></a>UserPrincipalName のあいまい一致
この機能を有効にすると、[プライマリ SMTP アドレス](https://support.microsoft.com/kb/2641663)に加えて UPN にもあいまい一致が有効になります。プライマリ SMTP アドレスでは、あいまい一致が常に有効になっています。 あいまい一致は、Azure AD 内の既存のクラウド ユーザーをオンプレミスのユーザーと照合するために使用されます。

オンプレミスの AD アカウントを、クラウドで作成された既存のアカウントと照合し、Exchange Online を使用していない場合、この機能は役立ちます。 このような状況では、通常、クラウドで SMTP 属性を設定する理由がありません。

新たに作成した Azure AD ディレクトリでは、この機能は既定で有効になっています。 この機能が有効になっているかどうかを確認するには、次のコマンドレットを実行します。  

```
Get-MsolDirSyncFeatures -Feature EnableSoftMatchOnUpn
```

この機能が Azure AD ディレクトリに対して有効になっていない場合は、次のコマンドレットを実行して有効にすることができます。  

```
Set-MsolDirSyncFeature -Feature EnableSoftMatchOnUpn -Enable $true
```

## <a name="synchronize-userprincipalname-updates"></a>userPrincipalName の更新の同期
これまで、オンプレミスから同期サービスを使用して UserPrincipalName 属性を更新することは、次の 2 つの条件が両方とも当てはまらない限り、できませんでした。

* ユーザーが管理対象ユーザー (非フェデレーション ユーザー) である。
* ユーザーにライセンスが割り当てられていない。

詳細については、「 [Office 365、Azure、Intune などの組織アカウントのユーザー名が、オンプレミスの UPN または代替ログイン ID と一致しない](https://support.microsoft.com/kb/2523192)」を参照してください。

この機能を有効にすると、userPrincipalName がオンプレミスで変更されたときに、パスワード ハッシュ同期を使用している場合は、同期エンジンによって userPrincipalName が更新されます。フェデレーションを使用している場合、この機能はサポートされません。

新たに作成した Azure AD ディレクトリでは、この機能は既定で有効になっています。 この機能が有効になっているかどうかを確認するには、次のコマンドレットを実行します。  

```
Get-MsolDirSyncFeatures -Feature SynchronizeUpnForManagedUsers
```

この機能が Azure AD ディレクトリに対して有効になっていない場合は、次のコマンドレットを実行して有効にすることができます。  

```
Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $true
```

この機能を有効にすると、既存の userPrincipalName の値はそのまま維持されます。 次に serPrincipalName 属性をオンプレミスで変更したときに、ユーザーに関する通常の差分同期によって UPN が更新されます。  

## <a name="see-also"></a>関連項目
* [Azure AD Connect Sync](active-directory-aadconnectsync-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)

