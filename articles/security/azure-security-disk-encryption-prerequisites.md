---
title: Azure Disk Encryption の前提条件 | Microsoft Docs
description: この記事では、IaaS VM 用に Microsoft Azure Disk Encryption を使用する場合の前提条件について説明します。
services: security
documentationcenter: na
author: mestew
manager: MBaldwin
ms.assetid: 1b2daefc-1326-44dd-9c8b-10e413769af7
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/30/2018
ms.author: mstewart
ms.openlocfilehash: 5421858fd7f31f18c2e6a1e3693b67b3c47a6945
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2018
ms.locfileid: "42141126"
---
# <a name="azure-disk-encryption-prerequisites"></a>Azure Disk Encryption の前提条件 
 この記事「Azure Disk Encryption の前提条件」では、Azure Disk Encryption を使用する前に用意する必要がある項目について説明します。 一般的な前提条件に加え、Azure Disk Encryption は、キー コンテナー内の暗号化キーを管理するために [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) と統合され、Azure AD アプリケーションを使用して認証を提供しています。 Key Vault と Azure AD アプリケーションを設定または構成するには、[Azure PowerShell](/powershell/azure/overview) または [Azure CLI](/cli/azure/) を使用できます。

[Azure Disk Encryption の概要](azure-security-disk-encryption-overview.md)に関する記事で説明されているサポート対象シナリオの Azure IaaS VM で Azure Disk Encryption を有効にする前に、以下の前提条件を満たしていることを確認してください。 

> [!NOTE]
> 特定の推奨事項により、データ、ネットワーク、またはコンピューティング リソースの使用量が増え、その結果、ライセンスまたはサブスクリプション コストの追加が必要になる可能性があります。 サポートされているリージョンにおいて Azure でリソースを作成するための有効なアクティブ Azure サブスクリプションが必要です。


## <a name="bkmk_OSs"></a> サポートされているオペレーティング システム
Azure Disk Encryption は、次のオペレーティング システムでサポートされています。

- Windows Server のバージョン: Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2、Windows Server 2016。
    - Windows Server 2008 R2 の場合、Azure で暗号化を有効にする前に、.Net Framework 4.5 をインストールする必要があります。 Windows Update から、オプションの更新プログラムである Windows Server 2008 R2 x64 ベース システム用の Microsoft .NET Framework 4.5.2 ([KB2901983](https://support.microsoft.com/kb/2901983)) を使用してインストールすることができます。    
- Windows クライアントのバージョン: Windows 8 クライアントと Windows 10 クライアント。
- Azure Disk Encryption は、Azure ギャラリー ベースの Linux サーバーのディストリビューションおよびバージョンでのみサポートされます。 現在サポートされているバージョンの一覧については、「[Azure Disk Encryption に関する FAQ](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport)」を参照してください。
- Azure Disk Encryption では、Key Vault と VM が同じ Azure リージョンおよびサブスクリプションに属している必要があります。 リソースをそれぞれ別のリージョンで構成すると、Azure Disk Encryption 機能を有効にする場合にエラーが発生します。

## <a name="bkmk_LinuxPrereq"></a> Linux Iaas VM のその他の前提条件 

- Linux 用の Azure Disk Encryption を使用する場合、[サポートされているイメージ](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport)で OS ディスクの暗号化を有効にするために、VM に 7 GB の RAM が必要です。 OS ディスクの暗号化プロセスが完了すると、より少ないメモリで VM を実行するように構成できます。
- 暗号化を有効にする前に、暗号化するデータ ディスクを /etc/fstab に正しく登録する必要があります。 "/dev/sdX" 形式のデバイス名は、再起動後 (特に暗号化が適用された後) に同じディスクに関連付けることができないので、このエントリに永続的なブロック デバイス名を使用してください。 この動作の詳細については、「[Linux VM デバイス名の変更トラブルシューティング](../virtual-machines/linux/troubleshoot-device-names-problems.md)」を参照してください。
- /etc/fstab 設定がマウントに合わせて正しく構成されていることを確認します。 これらの設定を構成するには、mount -a コマンドを実行するか、VM を再起動してその方法での再マウントをトリガーします。 完了したら、lsblk コマンドの出力を調べて、目的のドライブがまだマウントされていることを確認します。 
    - 暗号化を有効にする前に /etc/fstab ファイルによってドライブが適切にマウントされない場合、Azure Disk Encryption Azure でそれを適切にマウントできません。
    - Azure Disk Encryption プロセスは、暗号化プロセスの一部として、/etc/fstab から独自の構成ファイルにマウント情報を移動します。 データ ドライブの暗号化が完了した後、/etc/fstab にエントリがないことを心配しないでください。
    -  再起動後、新しく暗号化されたディスクが Azure Disk Encryption プロセスによってマウントされる処理には時間がかかります。 再起動後すぐには利用できません。 このプロセスでは、他のプロセスがアクセスできるようになる前に、暗号化されたドライブを起動し、ロックを解除し、マウントする時間が必要です。 システムの特性によっては、再起動後に 1 分以上かかることがあります。

データ ディスクをマウントし、必要な /etc/fstab エントリを作成するために使用できるコマンドの例は、[このスクリプト ファイルの行 197-205](https://github.com/ejarvi/ade-cli-getting-started/blob/master/validate.sh#L197-L205) にあります。 


## <a name="bkmk_GPO"></a> ネットワークとグループ ポリシー

**Azure Disk Encryption 機能を有効にするには、IaaS VM が次のネットワーク エンドポイントの構成要件を満たす必要があります。**
  - Key Vault に接続するためのトークンを取得するには、IaaS VM が Azure Active Directory エンドポイント \[login.microsoftonline.com\] に接続できる必要があります。
  - 暗号化キーを Key Vault に書き込むには、IaaS VM が Key Vault エンドポイントに接続できる必要があります。
  - IaaS VM は、Azure 拡張リポジトリをホストする Azure ストレージ エンドポイントと、VHD ファイルをホストする Azure ストレージ アカウントに接続できる必要があります。
  -  セキュリティ ポリシーで Azure VM からインターネットへのアクセスが制限されている場合は、上記の URI を解決し、IP への送信接続を許可するための特定のルールを構成することができます。 詳細については、「[ファイアウォールの内側にある Azure Key Vault へのアクセス](../key-vault/key-vault-access-behind-firewall.md)」を参照してください。    


**グループ ポリシー:**
 - Azure Disk Encryption ソリューションでは、Windows IaaS VM に対して BitLocker 外部キー保護機能を使用します。 ドメインに参加している VM の場合は、TPM 保護機能を適用するグループ ポリシーをプッシュしないでください。 "互換性のある TPM が装備されていない BitLocker を許可する" のグループ ポリシーについては、「[BitLocker Group Policy Reference](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#a-href-idbkmk-unlockpol1arequire-additional-authentication-at-startup)」(BitLocker グループ ポリシー リファレンス) をご覧ください。

-  カスタム グループ ポリシーを使用するドメインに参加している仮想マシンの Bitlocker ポリシーには、[[BitLocker 回復情報のユーザー記憶域を構成する] -> [256 ビットの回復キーを許可する]](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings) の設定を含める必要があります。 Bitlocker のカスタム グループ ポリシー設定に互換性がない場合、Azure Disk Encryption は失敗します。 正しいポリシー設定がないコンピューターでは、新しいポリシーを適用し、新しいポリシーを強制的に適用して更新し (gpupdate.exe /force)、再起動する処理が必要になる可能性があります。  


## <a name="bkmk_PSH"></a> Azure PowerShell
[Azure PowerShell](/powershell/azure/overview) には、Azure リソースの管理に [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) モデルを使う一連のコマンドレットが用意されています。 これは、[Azure Cloud Shell](../cloud-shell/overview.md) を使用してブラウザーで使用するか、以下の手順でローカル コンピューターにインストールして PowerShell セッションで使用することができます。 既にローカルにインストールされている場合、Azure Disk Encryption を構成するには、最新バージョンの Azure PowerShell SDK を使用します。 [Azure PowerShell リリース](https://github.com/Azure/azure-powershell/releases)の最新バージョンをダウンロードします。

### <a name="install-azure-powershell-for-use-on-your-local-machine-optional"></a>ローカル コンピューターで使用する Azure PowerShell をインストールします (省略可能)。 
1. お使いのオペレーティング システムのリンク先に記載されている指示を実行してから、以下の残りの手順を続行します。      
    - [Windows 用 Azure Powershell をインストールして構成します](/powershell/azure/install-azurerm-ps)。 
        - PowerShellGet、Azure PowerShell をインストールし、AzureRM モジュールを読み込みます。 
    - [macOS および Linux に Azure PowerShell をインストールし、構成します](/powershell/azure/install-azurermps-maclinux)。
        -  PowerShell Core、Azure PowerShell for .NET Core をインストールし、AzureRM.Netcore モジュールを読み込みます。
2. [Azure Active Directory PowerShell モジュール](/powershell/azure/active-directory/install-adv2#installing-the-azure-ad-module)をインストールします。 

     ```powershell
     Install-Module AzureAD
     ```

3. モジュールのインストールされているバージョンを確認します。
      ```powershell
      Get-Module AzureRM -ListAvailable | Select-Object -Property Name,Version,Path
      Get-Module AzureAD -ListAvailable | Select-Object -Property Name,Version,Path
      ```
4. [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) コマンドレットを使用して Azure にログインします。
     
     ```powershell
     Connect-AzureRmAccount
     # For specific instances of Azure, use the -Environment parameter.
     Connect-AzureRmAccount –Environment (Get-AzureRmEnvironment –Name AzureUSGovernment)
    
     <# If you have multiple subscriptions and want to specify a specific one, 
     get your subscription list with Get-AzureRmSubscription and 
     specify it with Set-AzureRmContext.  #>
     Get-AzureRmSubscription
     Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"
     ```

5.  Azure AD [Connect-AzureAD](/powershell/module/azuread/connect-azuread) に接続します。
     
     ```powershell
     Connect-AzureAD
     ```

6. 必要に応じて、[Azure PowerShell の使用](/powershell/azure/get-started-azureps)と [AzureAD](/powershell/module/azuread) に関するページを参照してください。

## <a name="bkmk_CLI"></a> Azure CLI

[Azure CLI 2.0](/cli/azure) は、Azure リソースを管理するためのコマンドライン ツールです。 CLI は、データのクエリを柔軟に実行し、長時間実行される操作を非ブロッキング プロセスとしてサポートし、スクリプトが簡単になるように設計されています。 これは、[Azure Cloud Shell](../cloud-shell/overview.md) を使用してブラウザーで使用することも、ローカル コンピューターにインストールして PowerShell セッションで使用することもできます。

1. ローカル コンピューターで使用する [Azure CLI をインストールします](/cli/azure/install-azure-cli) (省略可能)。

2. インストールされているバージョンを確認します。
     
     ```azurecli-interactive
     az --version
     ``` 

3. [az login](/cli/azure/authenticate-azure-cli) を使用して Azure にサインインします。
     
     ```azurecli
     az login
     
     # If you would like to select a tenant, use: 
     az login --tenant "<tenant>"

     # If you have multiple subscriptions, get your subscription list with az account list and specify with az account set.
     az account list
     az account set --subscription "<subscription name or ID>"
     ```

4. 必要に応じて、「[Azure CLI 2.0 を使ってみる](/cli/azure/get-started-with-azure-cli)」を参照してください。 


## <a name="prerequisite-workflow-for-key-vault-and-the-azure-ad-app"></a>Key Vault と Azure AD アプリの前提条件ワークフロー
1. Key Vault を作成します。 
2. Azure AD アプリケーションとサービス プリンシパルを設定します。
3. Azure AD アプリのキー コンテナー アクセス ポリシーを設定します。
4. キー コンテナーに高度なアクセス ポリシーを設定します。
 
## <a name="bkmk_KeyVault"></a> キー コンテナーを作成する 
Azure Disk Encryption は [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) と統合されており、ディスクの暗号化キーとシークレットは Key Vault サブスクリプションで制御および管理できます。 Azure Disk Encryption ではキー コンテナーを作成するか、既存のコンテナーを使用することができます。 キー コンテナーの詳細については、「[Azure Key Vault の概要](../key-vault/key-vault-get-started.md)」と「[キー コンテナーのセキュリティ保護](../key-vault/key-vault-secure-your-key-vault.md)」を参照してください。 Resource Manager テンプレート、Azure PowerShell、または Azure CLI を使用してキー コンテナーを作成できます。 


>[!WARNING]
>暗号化シークレットがリージョンの境界を越えないようにするため、Azure Disk Encryption では Key Vault と VM を同じリージョンに併置する必要があります。 暗号化する VM と同じリージョン内に Key Vault を作成し、使用します。 


### <a name="bkmk_KVPSH"></a> PowerShell を使用してキー コンテナーを作成する

Azure PowerShell で [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/New-AzureRmKeyVault) コマンドレットを使用してキー コンテナーを作成できます。 Key Vault のその他のコマンドレットについては、[AzureRM.KeyVault](/powershell/module/azurerm.keyvault/) を参照してください。 

1. 必要に応じて、[Azure サブスクリプションに接続します](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH)。 
2. 必要に応じて、[New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup) を使用して新しいリソース グループを作成します。  データセンターの場所を一覧表示するには、[Get-AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocationn) を使用します。 
     
     ```azurepowershell-interactive
     # Get-AzureRmLocation 
     New-AzureRmResourceGroup –Name 'MySecureRG' –Location 'East US'
     ```

3. [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/New-AzureRmKeyVault) を使用して、新しいキー コンテナーを作成します。
    
      ```azurepowershell-interactive
     New-AzureRmKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MySecureRG' -Location 'East US'
     ```

4. 返される**コンテナー名**、**リソース グループ名**、**リソース ID**、**コンテナーの URI**、および**オブジェクト ID** をメモします。これらの情報は、後でディスクを暗号化するときに使用します。 


### <a name="bkmk_KVCLI"></a> Azure CLI を使用してキー コンテナーを作成する
[az keyvault](/cli/azure/keyvault#commands) コマンドを使用して Azure CLI でキー コンテナーを管理できます。 キー コンテナーを作成するには、[az keyvault create](/cli/azure/keyvault#az-keyvault-create) を使用します。

1. 必要に応じて、[Azure サブスクリプションに接続します](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI)。
2. 必要に応じて、[az group create](/cli/azure/group#az-group-create) を使用して新しいリソース グループを作成します。 場所を一覧表示するには、[az account list-locations](/cli/azure/account#az-account-list) を使用します。 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MySecureRG" -l "East US"
     ```

3. [az keyvault create](/cli/azure/keyvault#az-keyvault-create) を使用して、新しいキー コンテナーを作成します。
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MySecureRG" --location "East US"
     ```

4. 返される**コンテナー名** (名前)、**リソース グループ名**、**リソース ID** (ID)、**コンテナーの URI**、および**オブジェクト ID** をメモします。これらの情報は後で使用します。 

### <a name="bkmk_KVRM"></a> Resource Manager テンプレートでキー コンテナーを作成する

[Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)を使用してキー コンテナーを作成できます。

1. Azure クイックスタート テンプレートで、**[Azure に配置する]** をクリックします。
2. サブスクリプション、リソース グループ、リソース グループの場所、キー コンテナー名、オブジェクト ID、法律条項および契約を選択し、**[購入]** をクリックします。 


## <a name="bkmk_ADapp"></a> Azure AD アプリとサービス プリンシパルを設定する 
Azure で実行中の VM に対して暗号化を有効にする必要がある場合、Azure Disk Encryption により、暗号化キーが生成され、Key Vault に書き込まれます。 Key Vault の暗号化キーを管理するには、Azure AD 認証が必要です。 この目的で Azure AD アプリケーションを作成します。 認証には、クライアント シークレット ベースの認証か、[クライアント証明書ベースの Azure AD 認証](../active-directory/active-directory-certificate-based-authentication-get-started.md)を使用できます。


### <a name="bkmk_ADappPSH"></a> Azure PowerShell で Azure AD アプリとサービス プリンシパルを設定する 
次のコマンドを実行するには、[Azure AD PowerShell モジュール](/powershell/azure/active-directory/install-adv2)を取得して使用します。 

1. 必要に応じて、[Azure サブスクリプションに接続します](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH)。
2. [New-AzureRmADApplication](/powershell/module/azurerm.resources/new-azurermadapplication) PowerShell コマンドレットを使用して Azure AD アプリケーションを作成します。 MyApplicationHomePage と MyApplicationUri には、任意の値を指定できます。

     ```azurepowershell-interactive
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzureRmADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. $azureAdApplication.ApplicationId は Azure AD ClientID、$aadClientSecret はクライアント シークレットです。これらは、Azure Disk Encryption を有効にするために後で使用します。 Azure AD のクライアント シークレットは適切に保護してください。 `$azureAdApplication.ApplicationId` を実行すると、ApplicationID が表示されます。


### <a name="bkmk_ADappCLI"></a> Azure CLI で Azure AD アプリとサービス プリンシパルを設定する

Azure CLI で [az ad sp](/cli/azure/ad/sp) コマンドを使用してサービス プリンシパルを管理できます。 詳細については、[Azure サービス プリンシパルの作成](/cli/azure/create-an-azure-service-principal-azure-cli)に関するページを参照してください。

1. 必要に応じて、[Azure サブスクリプションに接続します](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI)。
2. 新しいサービス プリンシパルを作成します。
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  返される appId は、他のコマンドで使用される Azure AD ClientID です。 これは、az keyvault set-policy に使用する SPN でもあります。 パスワードは、あとで Azure Disk Encryption を有効にするために使用するクライアント シークレットです。 Azure AD のクライアント シークレットは適切に保護してください。
 
### <a name="bkmk_ADappRM"></a> Azure portal を使用して Azure AD アプリとサービス プリンシパルを設定する
[ポータルを使用した、リソースにアクセスできる Azure Active Directory アプリケーションとサービス プリンシパルの作成](../azure-resource-manager/resource-group-create-service-principal-portal.md)に関する記事の手順を使用して、Azure AD アプリケーションを作成します。 以下の各手順で、完了する必要がある記事のセクションに直接進むことができます。 

1. [必要なアクセス許可を確認する](../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions)
2. [Azure Active Directory アプリケーションを作成する](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application) 
     - アプリケーションの作成時には任意の名前とサインオン URL を使用できます。
3. [アプリケーション ID と認証キーを取得する](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key)。 
     - 認証キーはクライアント シークレットであり、Set-AzureRmVMDiskEncryptionExtension の AadClientSecret として使用されます。 
        - 認証キーは、Azure AD にサインインするための資格情報としてアプリケーションによって使用されます。 Azure portal では、このシークレットはキーと呼ばれますが、キー コンテナーとは関係がありません。 このシークレットはセキュリティで適切に保護してください。 
     - アプリケーション ID は、あとで Set-AzureRmVMDiskEncryptionExtension の AadClientId と Set-AzureRmKeyVaultAccessPolicy の ServicePrincipalName として使用されます。 

## <a name="bkmk_KVAP"></a> Azure AD アプリのキー コンテナー アクセス ポリシーを設定する
指定した Key Vault に暗号化シークレット情報を書き込むには、シークレットを Key Vault に書き込むアクセス許可を持つ Azure Active Directory アプリケーションのクライアント ID とクライアント シークレットが Azure Disk Encryption に必要です。 

> [!NOTE]
> Azure Disk Encryption では、Azure AD のクライアント アプリケーションに対して、アクセス ポリシー (_WrapKey_ および _Set_ アクセス許可) を構成する必要があります。

### <a name="bkmk_KVAPPSH"></a> Azure PowerShell を使用して Azure AD アプリのキー コンテナー アクセス ポリシーを設定する
Azure AD アプリケーションには、Vault 内のキーまたはシークレットへのアクセス権が必要です。 [Set-AzureKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) コマンドレットで、_–ServicePrincipalName_ パラメーター値としてクライアント ID (アプリケーションの登録時に生成されたもの) を使用して、アプリケーションに権限を付与します。 詳しくは、ブログ投稿「[Azure Key Vault - Step by Step](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx)」(Azure Key Vault - 手順) をご覧ください。 

1. 必要に応じて、[Azure サブスクリプションに接続します](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH)。
2. PowerShell を使用して AD アプリケーションのキー コンテナー アクセス ポリシーを設定します。

     ```azurepowershell-interactive
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $rgname = 'MySecureRG'
     Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname
     ```

### <a name="bkmk_KVAPCLI"></a> Azure CLI を使用して Azure AD アプリのキー コンテナー アクセス ポリシーを設定する
[az keyvault set-policy](https://docs.microsoft.com/cli/azure/keyvault#az-keyvault-set-policy) を使用してアクセス ポリシーを設定します。 詳細については、[CLI 2.0 を使用した Key Vault の管理](../key-vault/key-vault-manage-with-cli2.md#authorize-the-application-to-use-the-key-or-secret)に関するページを参照してください。

1. 必要に応じて、[Azure サブスクリプションに接続します](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI)。
2. 次のコマンドを使用して、Azure CLI アクセス経由で作成したサービス プリンシパルにシークレット キーとラップ キーを渡します。
 
     ```azurecli-interactive
     az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
     ```

### <a name="bkmk_KVAPRM"></a> ポータルを使用して Azure AD アプリのキー コンテナー アクセス ポリシーを設定する

1. キー コンテナーを使用してリソース グループを開きます。
2. キー コンテナーを選択し、**[アクセス ポリシー]** に移動し、**[新規追加]** をクリックします。
3. **[プリンシパルの選択]** で、作成した Azure AD アプリケーションを検索して選択します。 
4. **[キーのアクセス許可]** で、**[暗号化操作]** の **[キーを折り返す]** をオンにします。
5. **[シークレットのアクセス許可]** で、**[シークレットの管理操作]** の **[設定]** をオンにします。
6. **[OK]** をクリックしてアクセス ポリシーを保存します。 

![Azure Key Vault の [暗号化操作] - [キーを折り返す]](./media/azure-security-disk-encryption/keyvault-portal-fig3.png)

![Azure Key Vault の [シークレットのアクセス許可] - [設定] ](./media/azure-security-disk-encryption/keyvault-portal-fig3b.png)

## <a name="bkmk_KVper"></a>キー コンテナーに高度なアクセス ポリシーを設定する
Azure プラットフォームには、Key Vault 内の暗号化キーまたはシークレットへのアクセス権を付与する必要があります。これにより、ボリュームをブートして暗号化する際に、それらの情報を VM に提供できるようになります。 キー コンテナーのディスク暗号化を有効にします。そうしないと、デプロイは失敗します。  

### <a name="bkmk_KVperPSH"></a> Azure PowerShell を使用してキー コンテナーに高度なアクセス ポリシーを設定する
 キー コンテナーの PowerShell コマンドレット [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) を使用してキー コンテナーのディスク暗号化を有効にします。

  - **Key Vault のディスク暗号化を有効にする:** Azure Disk Encryption には EnabledForDiskEncryption が必要です。
      
     ```azurepowershell-interactive 
     Set-AzureRmKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MySecureRG' -EnabledForDiskEncryption
     ```

  - **必要に応じて Key Vault のデプロイを有効にする:** 仮想マシンの作成時など、このキー コンテナーがリソース作成時に参照される場合、Microsoft.Compute リソース プロバイダーがこのキー コンテナーからシークレットを取得できるようにします。

     ```azurepowershell-interactive
      Set-AzureRmKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MySecureRG' -EnabledForDeployment
     ```

  - **必要に応じて Key Vault のテンプレートのデプロイを有効にする:** テンプレートのデプロイでこのキー コンテナーが参照される場合、Azure Resource Manager がこのキー コンテナーからシークレットを取得できるようにします。

     ```azurepowershell-interactive             
     Set-AzureRmKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MySecureRG' -EnabledForTemplateDeployment`
     ```

### <a name="bkmk_KVperCLI"></a> Azure CLI を使用してキー コンテナーに高度なアクセス ポリシーを設定する
[az keyvault update](/cli/azure/keyvault#az-keyvault-update) を使用し、キー コンテナーのディスク暗号化を有効にします。 

 - **Key Vault のディスク暗号化を有効にする:** Enabled-for-disk-encryption が必要です。 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MySecureRG" --enabled-for-disk-encryption "true"
     ```  

 - **必要に応じて Key Vault のデプロイを有効にする:** 資格情報コンテナーからシークレットとして保存されている証明書を取得することを Virtual Machines に許可します。
     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MySecureRG" --enabled-for-deployment "true"
     ``` 

 - **必要に応じて Key Vault のテンプレート デプロイを有効にする:** 資格情報コンテナーからシークレットを取得することをリソース マネージャーに許可します。
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MySecureRG" --enabled-for-template-deployment "true"
     ```


### <a name="bkmk_KVperrm"></a> Azure portal を使用してキー コンテナーに高度なアクセス ポリシーを設定する

1. キー コンテナーを選択し、**[アクセス ポリシー]** に移動し、**[クリックして高度なアクセス ポリシーを表示する]** を選択します。
2. **[ボリューム暗号化に対して Azure Disk Encryption へのアクセスを有効にする]** というボックスをオンにします。
3. 必要に応じて、**[展開に対して Azure Virtual Machines へのアクセスを有効にする]** と **[テンプレートの展開に対して Azure Resource Manager へのアクセスを有効にする]** の一方または両方をオンにします。 
4. **[Save]** をクリックします。

![Azure Key Vault の高度なアクセス ポリシー](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)


## <a name="bkmk_KEK"></a> キー暗号化キーを設定する (省略可能)
暗号化キーのセキュリティに対する追加レイヤーとしてキー暗号化キー (KEK) を使用する場合は、キー コンテナーに KEK を追加します。 キー コンテナーでキー暗号化キーを作成するには、[Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey) コマンドレットを使用します。 オンプレミスのキー管理 HSM から KEK をインポートすることもできます。 詳細については、[Key Vault](../key-vault/key-vault-hsm-protected-keys.md) のドキュメントを参照してください。 キー暗号化キーが指定されている場合、Azure Disk Encryption では、Key Vault への書き込みの前に、そのキーを使用して暗号化シークレットがラップされます。 

* Key Vault シークレットおよび KEK URL のバージョン管理が必要です。 Azure では、このバージョン管理制限が適用されます。 有効なシークレットと KEK URI については、次の例をご覧ください。

  * 有効なシークレット URL の例: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * 有効な KEK URL の例: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure Disk Encryption では、ポート番号を Key Vault シークレットおよび KEK URL の一部として指定することはサポートされません。 サポートされない Key Vault URL とサポートされる Key Vault URL の例については、以下の例を参照してください。

  * 許容できないキー コンテナー URL *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * 許容されるキー コンテナー URL: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="bkmk_KEKPSH"></a> Azure PowerShell でキー暗号化キーを設定する 
PowerShell スクリプトを使用する前に、Azure Disk Encryption の前提条件をよく読み、スクリプトの手順を理解しておく必要があります。 サンプル スクリプトは、実際の環境に合わせて変更が必要な場合があります。 このスクリプトで、すべての Azure Disk Encryption の前提条件が作成され、既存の IaaS VM が暗号化され、キー暗号化キーを使用してディスク暗号化キーがラップされます。 

 ```powershell
 # Step 1: Create a new resource group and key vault in the same location.
     # Fill in 'MyLocation', 'MySecureRG', and 'MySecureVault' with your values.
     # Use Get-AzureRmLocation to get available locations and use the DisplayName.
     # To use an existing resource group, comment out the line for New-AzureRmResourceGroup
     
     $Loc = 'MyLocation';
     $rgname = 'MySecureRG';
     $KeyVaultName = 'MySecureVault'; 
     New-AzureRmResourceGroup –Name $rgname –Location $Loc;
     New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname -Location $Loc;
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $KeyVaultResourceId = (Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname).VaultUri;
     
 # Step 2: Create the AD application and service principal.
     # Fill in 'MyAADClientSecret', "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
     # MyApplicationHomePage and the MyApplicationUri can be any values you wish.
     
     $aadClientSecret =  'MyAADClientSecret';
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force;
     $azureAdApplication = New-AzureRmADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -Password $aadClientSecretSec
     $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId;
     $aadClientID = $azureAdApplication.ApplicationId;
     
 #Step 3: Enable the vault for disk encryption and set the access policy for the Azure AD application.
     
     Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $rgname -EnabledForDiskEncryption;
     Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname;
     
 #Step 4: Create a new key in the key vault with the Add-AzureKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'Software';
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 5: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' with your value. 
     
     $VMName = 'MySecureVM';
     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```

## <a name="bkmk_Cert"></a> 証明書ベースの認証 (省略可能)
証明書認証を使用する場合は、キー コンテナーにアップロードして、それをクライアントに展開することができます。 PowerShell スクリプトを使用する前に、Azure Disk Encryption の前提条件をよく読み、スクリプトの手順を理解しておく必要があります。 サンプル スクリプトは、実際の環境に合わせて変更が必要な場合があります。

     
 ```powershell

 # Fill in "MySecureRG", "MySecureVault", and 'MyLocation' ('My location' only if needed)

   $rgname = 'MySecureRG'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption. No need to set 'My location' in this case.

   $Loc = 'MyLocation'
   New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname -Location $Loc
   Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $rgname -EnabledForDiskEncryption

   #Setting some variables with the key vault information 
   $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId

   # Create the Azure AD application and associate the certificate with it. 
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzureRmADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname
   
   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert".  

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for -EnabledForDeployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $rgname -EnabledForDeployment

   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' with your value.

   $VMName = 'MySecureVM'
   $CertUrl = (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname).ResourceId
   $VM = Get-AzureRmVM -ResourceGroupName $rgname -Name $VMName 
   $VM = Add-AzureRmVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzureRmVM -VM $VM -ResourceGroupName $rgname 

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
 ```

## <a name="bkmk_CertKEK"></a> 証明書ベースの認証と KEK (省略可能)

証明書認証を使用し、暗号化キーを KEK でラップする場合は、例として以下のスクリプトを使用できます。 PowerShell スクリプトを使用する前に、前述の Azure Disk Encryption の前提条件をすべてよく読み、スクリプトの手順を理解しておく必要があります。 サンプル スクリプトは、実際の環境に合わせて変更が必要な場合があります。

> [!IMPORTANT]
> Azure AD の証明書ベースの認証は、現時点では Linux VM ではサポートされていません。



     
 ```powershell
# Fill in 'MySecureRG', 'MySecureVault', and 'MyLocation' (if needed)

   $rgname = 'MySecureRG'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption.

   $Loc = 'MyLocation'
   New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname -Location $Loc
   Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $rgname -EnabledForDiskEncryption

   # Create the Azure AD application and associate the certificate with it.  
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzureRmADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   ## Give access for setting secrets and wraping keys
   Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname

   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert". 

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for deployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $rgname -EnabledForDeployment

   #Setting some variables with the key vault information and generating a KEK 
   # FIll in 'KEKName'
   
   $KEKName ='KEKName'
   $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId
   $KEK = Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name $KEKName -Destination "Software"
   $KeyEncryptionKeyUrl = $KEK.Key.kid



   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' with your value.

   $VMName = 'MySecureVM'
   $CertUrl = (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname).ResourceId
   $VM = Get-AzureRmVM -ResourceGroupName $rgname -Name $VMName 
   $VM = Add-AzureRmVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzureRmVM -VM $VM -ResourceGroupName $rgname 

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

 
## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [Windows で Azure Disk Encryption を有効にする](azure-security-disk-encryption-windows.md)

> [!div class="nextstepaction"]
> [Linux で Azure Disk Encryption を有効にする](azure-security-disk-encryption-linux.md)

