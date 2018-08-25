---
title: 'Azure AD ドメイン サービス: パスワード同期を有効にする | Microsoft Docs'
description: Azure Active Directory Domain Services の概要
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 8731f2b2-661c-4f3d-adba-2c9e06344537
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2017
ms.author: maheshu
ms.openlocfilehash: 261cb45bd3b7b7f284eff3397aca141917a82502
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39504857"
---
# <a name="enable-password-synchronization-to-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services とのパスワード同期を有効にする
前のタスクでは、Azure Active Directory (Azure AD) テナントに対して Azure Active Directory Domain Services を有効にしました。 次のタスクでは、NT LAN Manager (NTLM) および Kerberos 認証に必要な資格情報ハッシュを Azure AD Domain Services との間で同期できるようにします。 資格情報の同期が設定されると、ユーザーは自社の資格情報を使用して、マネージド ドメインにサインインできます。

対象となるユーザー アカウントがクラウド専用のアカウントであるか、オンプレミス ディレクトリとの間で Azure AD Connect を使って同期されたアカウントであるかによって、必要な手順は異なります。

<br>
| **ユーザー アカウントの種類** | **実行する手順** |
| --- | --- |
| **オンプレミス ディレクトリとの間で同期されるユーザー アカウント** |**&#x2713;** [この記事の手順に従う](active-directory-ds-getting-started-password-sync-synced-tenant.md#task-5-enable-password-synchronization-to-your-managed-domain-for-user-accounts-synced-with-your-on-premises-ad) | 
| **Azure AD に作成されたクラウド ユーザー アカウント** |**&#x2713;** [クラウド専用ユーザー アカウントのパスワードをマネージド ドメインとの間で同期する](active-directory-ds-getting-started-password-sync.md) |
<br>

> [!TIP]
> **両方の手順を実行することが必要になる場合があります。**
> Azure AD テナントにクラウドのみのユーザーとオンプレミス AD からのユーザーとが混在している場合、両方の手順を実行する必要があります。
>

## <a name="task-5-enable-password-synchronization-to-your-managed-domain-for-user-accounts-synced-with-your-on-premises-ad"></a>タスク 5: オンプレミス AD との間で同期されたユーザー アカウントのパスワード同期をマネージド ドメインとの間で有効にする
同期対象の Azure AD テナントは、Azure AD Connect を使用して組織のオンプレミス ディレクトリと同期するように設定されます。 Azure AD Connect は、既定では NTLM および Kerberos 資格情報ハッシュを Azure AD と同期しません。 Azure AD Domain Services を使用するには、NTLM および Kerberos 認証に必要な資格情報ハッシュを同期するように Azure AD Connect を構成する必要があります。 以下の手順では、必要な資格情報ハッシュをオンプレミス ディレクトリから Azure AD テナントに対して同期できるようにします。

> [!NOTE]
> 
  **オンプレミス ディレクトリとの間で同期されたユーザー アカウントが組織に存在する場合、マネージド ドメインを使用するためには、NTLM と Kerberos のハッシュ同期を有効にする必要があります。** 同期の対象となるユーザー アカウントは、オンプレミス ディレクトリで作成されたアカウントであり、Azure AD テナントとの間で Azure AD Connect を使って同期されます。
>
>

### <a name="install-or-update-azure-ad-connect"></a>Azure AD Connect のインストールまたは更新
Azure AD Connect の最新の推奨リリースをドメイン参加コンピューターにインストールします。 Azure AD Connect の既存のインスタンスが設定されている場合は、Azure AD Connect の最新バージョンを使用するように更新する必要があります。 既に修正されている既知の問題/バグを回避するために、常に最新バージョンの Azure AD Connect を使用してください。

**[Azure AD Connect のダウンロード](http://www.microsoft.com/download/details.aspx?id=47594)**

推奨バージョン: **1.1.614.0** - 2017 年 9 月 5 日公開

> [!WARNING]
> 従来のパスワードの資格情報 (NTLM/Kerberos 認証で必要) で Azure AD テナントとの同期を有効にするには、Azure AD Connect の最新の推奨リリースをインストールする必要があります。 この機能は、旧リリースの Azure AD Connect または従来の DirSync ツールでは使用できません。
>
>

Azure AD Connect のインストール手順については、[Azure AD Connect の概要](../active-directory/active-directory-aadconnect.md)に関する記事をご覧ください。

### <a name="enable-synchronization-of-ntlm-and-kerberos-credential-hashes-to-azure-ad"></a>NTLM と Kerberos の資格情報ハッシュの Azure AD との同期を有効にする
以下の PowerShell スクリプトをすべての AD フォレストで実行してください。 このスクリプトを使用すると、オンプレミスのすべてのユーザーの NTLM と Kerberos パスワード ハッシュを Azure AD テナントとの間で同期させることができます。 また、Azure AD Connect の完全同期が開始されます。

```powershell
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"  
$azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"  
Import-Module adsync  
$c = Get-ADSyncConnector -Name $adConnector  
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1  
$c.GlobalParameters.Remove($p.Name)  
$c.GlobalParameters.Add($p)  
$c = Add-ADSyncConnector -Connector $c  
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false   
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true  
```

ディレクトリのサイズ (ユーザーやグループの数など) によっては、資格情報ハッシュが Azure AD と同期されるまでに時間がかかります。 資格情報ハッシュが Azure AD と同期されるとすぐに、Azure AD Domain Services のマネージド ドメインでパスワードを使用できるようになります。

<br>

## <a name="related-content"></a>関連コンテンツ
* [AAD ドメイン サービスとのパスワード同期を有効にする (クラウド専用 Azure AD ディレクトリの場合)](active-directory-ds-getting-started-password-sync.md)
* 
  [Azure AD Domain Services のマネージド ドメインの管理](active-directory-ds-admin-guide-administer-domain.md)
* 
  [Azure AD Domain Services のマネージド ドメインに Windows 仮想マシンを参加させる](active-directory-ds-admin-guide-join-windows-vm.md)
* 
  [Azure AD Domain Services のマネージド ドメインに Red Hat Enterprise Linux 仮想マシンを参加させる](active-directory-ds-admin-guide-join-rhel-linux-vm.md)
