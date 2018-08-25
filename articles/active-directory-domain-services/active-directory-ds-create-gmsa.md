---
title: 'Azure Active Directory Domain Services: グループの管理されたサービス アカウントの作成 | Microsoft Docs'
description: Azure Active Directory Domain Services マネージド ドメインを管理する
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: maheshu
ms.openlocfilehash: 9bfd38b2eba3cab5012e5715ad283b9cb7b84a9b
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39505894"
---
# <a name="create-a-group-managed-service-account-gmsa-on-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services のマネージド ドメインでグループの管理されたサービス アカウント (gMSA) を作成する
この記事では、Azure AD Domain Services のマネージド ドメインで管理されたサービス アカウントを作成する方法について説明します。

## <a name="managed-service-accounts"></a>管理されたサービス アカウント
スタンドアロンの管理されたサービス アカウント (sMSA) とは、パスワードが自動的に管理されるマネージド ドメイン アカウントです。 これは、サービス プリンシパル名 (SPN) の管理を簡略化し、他の管理者に管理を委任できるようにします。 この種類の管理されたサービス アカウント (MSA) は、Windows Server 2008 R2 および Windows 7 で導入されました。

グループの管理されたサービス アカウント (gMSA) は、ドメイン上の多数のサーバーに同じ利点を提供します。 相互認証プロトコルを機能させるには、サーバー ファームにホストされているサービスのすべてのインスタンスが同じサービス プリンシパルを使用する必要があります。 gMSA がサービス プリンシパルとして使用される場合、Windows オペレーティング システムは、管理者に依存する代わりにアカウントのパスワードを管理します。

**詳細情報:**
- [グループの管理されたサービス アカウントの概要](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)
- [グループの管理されたサービス アカウントの使用開始](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)


## <a name="using-service-accounts-in-azure-ad-domain-services"></a>Azure AD Domain Services でのサービス アカウントの使用
Azure AD Domain Services のマネージド ドメインは、Microsoft によってロックダウンおよび管理されます。 Azure AD Domain Services でサービス アカウントを使用する場合、いくつかの重要な考慮事項があります。

### <a name="create-service-accounts-within-custom-organizational-units-ou-on-the-managed-domain"></a>マネージド ドメインでカスタム組織単位 (OU) 内にサービス アカウントを作成する
組み込みの 'AADDC ユーザー' または 'AADDC コンピューター' 組織単位には、サービス アカウントを作成できません。 マネージド ドメインに[カスタム OU を作成](active-directory-ds-admin-guide-create-ou.md)し、そのカスタム OU 内にサービス アカウントを作成します。

### <a name="the-key-distribution-services-kds-root-key-is-already-pre-created"></a>キー配布サービス (KDS) ルート キーは既に事前作成されている
キー配布サービス (KDS) ルート キーは、Azure AD Domain Services のマネージド ドメインに作成済みです。 KDS ルート キーを作成する必要はなく、作成する権限もありません。 マネージド ドメインで KDS ルート キーを表示することもできません。

## <a name="sample---create-a-gmsa-using-powershell"></a>サンプル - PowerShell を使用して gMSA を作成する
次の例では、PowerShell を使用してカスタム OU を作成する方法を示します。 ```-Path``` パラメーターを使用して OU を指定することで、その OU 内に gMSA を作成できます。

```powershell
# Create a new custom OU on the managed domain
New-ADOrganizationalUnit -Name "MyNewOU" -Path "DC=CONTOSO100,DC=COM"

# Create a service account 'WebFarmSvc' within the custom OU.
New-ADServiceAccount -Name WebFarmSvc  `
-DNSHostName ` WebFarmSvc.contoso100.com  `
-Path "OU=MYNEWOU,DC=CONTOSO100,DC=com"  `
-KerberosEncryptionType AES128, AES256  ` -ManagedPasswordIntervalInDays 30  `
-ServicePrincipalNames http/WebFarmSvc.contoso100.com/contoso100.com, `
http/WebFarmSvc.contoso100.com/contoso100,  `
http/WebFarmSvc/contoso100.com, http/WebFarmSvc/contoso100  `
-PrincipalsAllowedToRetrieveManagedPassword CONTOSO-SERVER$
```

**PowerShell コマンドレット ドキュメント:**
- [New-ADOrganizationalUnit コマンドレット](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit)
- [New-ADServiceAccount コマンドレット](https://docs.microsoft.com/powershell/module/addsadministration/New-ADServiceAccount)


## <a name="next-steps"></a>次の手順
- [マネージド ドメインでカスタム OU を作成する](active-directory-ds-admin-guide-create-ou.md)
- [グループの管理されたサービス アカウントの概要](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)
- [グループの管理されたサービス アカウントの使用開始](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)
