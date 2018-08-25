---
title: PowerShell での Azure アプリ ID の作成 | Microsoft Docs
description: Azure PowerShell を使用して、Azure Active Directory アプリケーションやサービス プリンシパルを作成し、ロールベースのアクセス制御によって、リソースへのアクセス権を付与する方法について説明します。 証明書を使ってアプリケーションを認証する方法を示します。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: d2caf121-9fbe-4f00-bf9d-8f3d1f00a6ff
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/10/2018
ms.author: tomfitz
ms.openlocfilehash: df1cf093a154b4f7adf59fae93f90fbc70459788
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038596"
---
# <a name="use-azure-powershell-to-create-a-service-principal-with-a-certificate"></a>Azure PowerShell を使用して資格情報でのサービス プリンシパルを作成する

リソースへのアクセスを必要とするアプリやスクリプトがある場合は、アプリの ID を設定し、アプリを独自の資格情報で認証できます。 この ID は、サービス プリンシパルと呼ばれます。 このアプローチを使用すると、以下のことを実行できます。

* ユーザー自身のアクセス許可とは異なるアクセス許可を、アプリケーション ID に割り当てることができます。 通常、こうしたアクセス許可は、アプリが行う必要があることに制限されます。
* 無人スクリプトを実行するときに、証明書を使用して認証できます。

> [!IMPORTANT]
> サービス プリンシパルを作成する代わりに、アプリケーション ID 用に Azure AD の管理対象サービス ID を使用することを検討します。 Azure AD の MSI は、コードのために ID の作成を簡略化する、Azure Active Directory のパブリック プレビュー機能です。 コードが、Azure AD の MSI をサポートするサービス上で実行され、Azure Active Directory 認証をサポートするリソースにアクセスする場合、Azure AD の MSI は優れた選択肢となります。 Azure AD の MSI の詳細 (どのサービスが現在 MSI をサポートしているかなど) については、「[Azure リソースの管理対象サービス ID](../active-directory/managed-service-identity/overview.md)」を参照してください。

この記事では、証明書を使用して認証するサービス プリンシパルの作成方法について説明します。 パスワードを使用するサービス プリンシパルを設定するには、「[Azure PowerShell で Azure サービス プリンシパルを作成する](/powershell/azure/create-azure-service-principal-azureps)」を参照してください。

このアーティクルには、PowerShell の[最新バージョン](/powershell/azure/get-started-azureps)が必要です。

## <a name="required-permissions"></a>必要なアクセス許可

この記事を完了するには、Azure Active Directory と Azure サブスクリプションの両方で十分なアクセス許可を持っている必要があります。 具体的には、Azure Active Directory でアプリケーションを作成し、ロールにサービス プリンシパルを割り当てることができる必要があります。

自分のアカウントに適切なアクセス許可があるかどうかを確認する最も簡単な方法は、ポータルを使用することです。 [必要なアクセス許可のチェック](resource-group-create-service-principal-portal.md#required-permissions)に関するページを参照してください。

## <a name="create-service-principal-with-self-signed-certificate"></a>自己署名証明書を使用したサービス プリンシパルの作成

以下の例では、単純なシナリオについて説明します。 ここでは、[New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) を使用して自己署名証明書でのサービス プリンシパルを作成し、[New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) を使用して[共同作成者](../role-based-access-control/built-in-roles.md#contributor)ロールをサービス プリンシパルに割り当てます。 ロールの割り当ては、現在選択されている Azure サブスクリプションに制限されます。 別のサブスクリプションを選択するには、[Set-AzureRmContext](/powershell/module/azurerm.profile/set-azurermcontext) を使用します。

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" `
  -Subject "CN=exampleappScriptCert" `
  -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

$sp = New-AzureRMADServicePrincipal -DisplayName exampleapp `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

この例では、新しいサービス プリンシパルが Azure Active Directory 全体に反映されるまでの時間を設けるために、20 秒間スリープします。 スクリプトの待機時間が不足している場合、"プリンシパル {ID} がディレクトリ {DIR-ID} にありません" というエラーが表示されます。 このエラーを解決するには、しばらく待ってから **New-AzureRmRoleAssignment** コマンドを再実行します。

**ResourceGroupName**パラメーターを使用して、特定のリソース グループにロールの割り当てをスコープできます。 **ResourceType**と**ResourceName**パラメーターを使用して、特定のリソースをスコープすることもできます。 

**Windows 10 または Windows Server 2016 を持っていない**場合は、Microsoft スクリプト センターから [Self-signed certificate generator](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) をダウンロードします。 ダウンロードしたファイルを展開し、必要なコマンドレットをインポートします。

```powershell
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```

このスクリプトでは、証明書を生成するために次の 2 行を置き換えます。

```powershell
New-SelfSignedCertificateEx -StoreLocation CurrentUser `
  -Subject "CN=exampleapp" `
  -KeySpec "Exchange" `
  -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>自動化された PowerShell スクリプトから証明書を渡す

サービス プリンシパルとしてサインインするときは常に、AD アプリのディレクトリのテナント ID を指定する必要があります。 テナントは、Azure Active Directory のインスタンスです。

```powershell
$TenantId = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
$ApplicationId = (Get-AzureRmADApplication -DisplayNameStartWith exampleapp).ApplicationId

 $Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -match "CN=exampleappScriptCert" }).Thumbprint
 Connect-AzureRmAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>証明機関の証明書を使用したサービス プリンシパルの作成

次の例では、証明機関から発行された証明書を使用して、サービス プリンシパルを作成します。 割り当ては、指定された Azure サブスクリプションに制限されます。 [共同作成者](../role-based-access-control/built-in-roles.md#contributor)ロールにサービス プリンシパルが追加されます。 ロールの割り当て中にエラーが発生した場合は、割り当てが再試行されます。

```powershell
Param (
 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword
 )

 Connect-AzureRmAccount
 Import-Module AzureRM.Resources
 Set-AzureRmContext -Subscription $SubscriptionId
 
 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force

 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName
 New-AzureRmADSpCredential -ObjectId $ServicePrincipal.Id -CertValue $KeyValue -StartDate $PFXCert.NotBefore -EndDate $PFXCert.NotAfter
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
 
 $NewRole
```

### <a name="provide-certificate-through-automated-powershell-script"></a>自動化された PowerShell スクリプトから証明書を渡す
サービス プリンシパルとしてサインインするときは常に、AD アプリのディレクトリのテナント ID を指定する必要があります。 テナントは、Azure Active Directory のインスタンスです。

```powershell
Param (
 
 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword,
 
 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
 $PFXCert = New-Object `
  -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 `
  -ArgumentList @($CertPath, $CertPassword)
 $Thumbprint = $PFXCert.Thumbprint

 Connect-AzureRmAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

アプリケーション ID とテナント ID は機密情報ではないため、スクリプトに直接埋め込むことができます。 テナント ID を取得する必要がある場合は、次のコマンドを使用します。

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

アプリケーション ID を取得する必要がある場合は、次のコマンドを使用します。

```powershell
(Get-AzureRmADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="change-credentials"></a>資格情報の変更

セキュリティ侵害の発生または資格情報の期限切れのために AD アプリの資格情報を変更するには、[Remove-AzureRmADAppCredential](/powershell/module/azurerm.resources/remove-azurermadappcredential) コマンドレットと [New-AzureRmADAppCredential](/powershell/module/azurerm.resources/new-azurermadappcredential) コマンドレットを使用します。

アプリケーションのすべての資格情報を削除するには、次のコマンドレットを使用します。

```powershell
Get-AzureRmADApplication -DisplayName exampleapp | Remove-AzureRmADAppCredential
```

証明書の値を追加するには、この記事の説明に従って自己署名証明書を作成します。 その後、次のコマンドレットを使用します。

```powershell
Get-AzureRmADApplication -DisplayName exampleapp | New-AzureRmADAppCredential `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
```

## <a name="debug"></a>デバッグ

サービス プリンシパルの作成時に、以下のエラーが発生する場合があります。

* **"Authentication_Unauthorized"** または **"コンテキストにサブスクリプトが見つかりません"** - アカウントが Azure Active Directory でアプリを登録するために[必要なアクセス許可](#required-permissions)を持っていない場合に、このエラーが表示されます。 通常は、Azure Active Directory の管理者ユーザーのみがアプリを登録できるときに、自分のアカウントが管理者でない場合に、このエラーが発生します。管理者に連絡して、自分を管理者ロールに割り当ててもらうか、ユーザーがアプリケーションを登録できるようにしてもらいます。

* アカウントに **「'/subscriptions/{guid} ' をスコープとした 'Microsoft.Authorization/roleAssignments/write' のアクションを実行するためのアクセス権限がありません」:** このエラーは、自分のアカウントが ID にロールを割り当てるのに十分なアクセス許可を持っていない場合に表示されます。 サブスクリプション管理者に連絡して、自分をユーザー アクセス管理者ロールに追加してもらいます。

## <a name="next-steps"></a>次の手順
* パスワードを使用するサービス プリンシパルを設定するには、「[Azure PowerShell で Azure サービス プリンシパルを作成する](/powershell/azure/create-azure-service-principal-azureps)」を参照してください。
* アプリケーションを Azure に統合してリソースを管理する詳しい手順については、「 [Azure Resource Manager API を使用した承認の開発者ガイド](resource-manager-api-authentication.md)」を参照してください。
* アプリケーションとサービス プリンシパルの詳細については、「[アプリケーションおよびサービス プリンシパル オブジェクト](../active-directory/develop/app-objects-and-service-principals.md)」を参照してください。 
* Azure Active Directory 認証の詳細については、「[Azure AD の認証シナリオ](../active-directory/develop/authentication-scenarios.md)」をご覧ください。
