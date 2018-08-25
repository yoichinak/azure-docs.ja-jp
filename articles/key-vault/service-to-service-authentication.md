---
title: .NET を使用した Azure Key Vault に対するサービス間認証
description: .NET を使用して Azure Key Vault の認証を受けるために Microsoft.Azure.Services.AppAuthentication ライブラリを使用します。
keywords: Azure Key Vault 認証 ローカル資格情報
author: bryanla
manager: mbaldwin
services: key-vault
ms.author: bryanla
ms.date: 11/15/2017
ms.topic: article
ms.prod: ''
ms.service: key-vault
ms.technology: ''
ms.assetid: 4be434c4-0c99-4800-b775-c9713c973ee9
ms.openlocfilehash: b158414e7a2954981534fe6fb26c987eb2f4ce67
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2018
ms.locfileid: "42145187"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>.NET を使用した Azure Key Vault に対するサービス間認証

Azure Key Vault の認証を受けるには、Azure Active Directory (AD) の資格情報として、共有シークレットまたは証明書のいずれかが必要です。 このような資格情報を管理するのは煩雑な作業になるため、資格情報をソース ファイルまたは構成ファイルに組み込んでアプリにバンドルしたくなります。

.NET 用の `Microsoft.Azure.Services.AppAuthentication` ライブラリを使うと、この問題が簡略化されます。 これにより、ローカルでの開発中は認証に開発者の資格情報が使用されます。 その後、ソリューションを Azure にデプロイすると、このライブラリは、自動的にアプリケーションの資格情報に切り替わります。  

ローカルでの開発中に開発者の資格情報を使用するという方法は、Azure AD 資格情報を作成したり、開発者間で資格情報を共有したりする必要がないため、セキュリティの面で有利です。

`Microsoft.Azure.Services.AppAuthentication` ライブラリによって認証が自動的に管理される結果、資格情報ではなくソリューションに重点を置くことができます。

`Microsoft.Azure.Services.AppAuthentication` ライブラリでは、Microsoft Visual Studio、Azure CLI、Azure AD の統合認証を使用したローカル開発がサポートされます。 Azure App Services または Azure 仮想マシン (VM) にデプロイされると、このライブラリは自動的に[管理対象サービス ID](/azure/active-directory/msi-overview) (MSI) を使用します。 コードまたは構成を変更する必要はありません。 MSI を利用できない場合や、ローカル開発中に開発者のセキュリティ コンテキストを特定できない場合、このライブラリは、Azure AD の[クライアントの資格情報](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)を直接使用することもサポートします。

<a name="asal"></a>
## <a name="using-the-library"></a>ライブラリの使用

.NET アプリケーションの場合、管理対象サービス ID (MSI) を利用する最も簡単な方法は、`Microsoft.Azure.Services.AppAuthentication` パッケージを経由する方法です。 次のようにして使い始めることができます。

1. [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) NuGet パッケージに対する参照をアプリケーションに追加します。

2. 次のコードを追加します。

    ``` csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;

    // ...

    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(
    azureServiceTokenProvider.KeyVaultTokenCallback));

    // or

    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync(
       "https://management.azure.com/").ConfigureAwait(false);
    ```

`AzureServiceTokenProvider` クラスは、トークンをメモリ内にキャッシュし、有効期限の直前に Azure AD から取得します。 このため、`GetAccessTokenAsync` メソッドを呼び出す前に有効期限を確認する必要がなくなります。 トークンが必要になった時点で、いつでもメソッドを呼び出すことができます。 

`GetAccessTokenAsync` メソッドには、リソース識別子が必要です。 詳細については、「[Azure サービスは管理対象サービス ID をサポートしますか](https://docs.microsoft.com/azure/active-directory/msi-overview#which-azure-services-support-managed-service-identity)」を参照してください。


<a name="samples"></a>
## <a name="samples"></a>サンプル

次のサンプルは、`Microsoft.Azure.Services.AppAuthentication` ライブラリの動作について示しています。

1. 
  [実行時に管理対象サービス ID (MSI) を使用して Azure Key Vault からシークレットを取得する](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)。

2. [MSI を使用して Azure VM から Azure Resource Manager テンプレートをプログラムでデプロイする](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet)。

3. [.NET Core サンプルと MSI を使用して、Azure Linux VM から Azure サービスを呼び出す](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/)。


<a name="local"></a>
## <a name="local-development-authentication"></a>ローカル開発用における認証

ローカル開発の場合、次の 2 つの主要な認証シナリオがあります。

- [Azure サービスに対する認証](#authenticating-to-azure-services)
- [カスタム サービスに対する認証](#authenticating-to-custom-services)

ここでは、各シナリオの要件とサポートされているツールについて説明します。


### <a name="authenticating-to-azure-services"></a>Azure サービスに対する認証

ローカル コンピューターは、管理対象サービス ID (MSI) をサポートしていません。  その結果、`Microsoft.Azure.Services.AppAuthentication` ライブラリは、ローカル開発環境で実行するために開発者の資格情報を使用します。 ソリューションを Azure にデプロイすると、このライブラリは、MSI を使用して OAuth 2.0 クライアント資格情報の付与フローに切り替えます。  このため、同じコードをローカルでも、リモートでも、心配せずにテストできます。

ローカル開発では、`AzureServiceTokenProvider` は、**Visual Studio**、**Azure コマンド ライン インターフェイス** (CLI)、**Azure AD 統合認証**を使用してトークンをフェッチします。 このライブラリは、各オプションを順番に試行し、最初に成功したオプションを使用します。 どのオプションも機能しない場合、詳しい情報と共に `AzureServiceTokenProviderException` 例外がスローされます。

### <a name="authenticating-with-visual-studio"></a>Visual Studio での認証

Visual Studio を使用するには、次の点をご確認ください。

1. [Visual Studio 2017 v15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/) またはそれ以降をインストール済みであること。

2. [Visual Studio 用のアプリ認証拡張機能](https://go.microsoft.com/fwlink/?linkid=862354) がインストールされていること。
 
3. Visual Studio にサインイン済み、かつローカル開発に使用するアカウントが選択済みであること。 **[ツール]**&nbsp;>&nbsp;**[オプション]**&nbsp;>&nbsp;**[Azure サービスの認証]** を使用してローカル開発アカウントを選択します。 

トークン プロバイダー ファイルに関するエラーなど、Visual Studio の使用に関して問題が発生した場合は、これらの手順をよくご確認ください。 

また、開発者トークンの再認証が必要となる場合もあります。  これを行うには、**[ツール]**&nbsp;>&nbsp;**[オプション]**>**[Azure&nbsp;サービスの認証]**&nbsp; に移動し、選択したアカウントの下にある **[再認証]** リンクを探します。  それを選択して認証してください。 

### <a name="authenticating-with-azure-cli"></a>Azure CLI での認証

ローカル開発に Azure CLI を使用するには、次のようにします。

1. [Azure CLI v2.0.12](/cli/azure/install-azure-cli) またはそれ以降をインストールします。 それより前のバージョンは、アップグレードします。 

2. **az login** を使用して Azure にサインインします。

`az account get-access-token` を使用してアクセスを確認します。  エラーが発生した場合は、手順 1 が正常に完了したことを確認してください。 

Azure CLI が既定のディレクトリにインストールされていない場合は、`AzureServiceTokenProvider` が Azure CLI のパスを見つけられないというエラーが出ることがあります。  **AzureCLIPath** 環境変数を使用して、Azure CLI のインストール フォルダーを定義してください。 `AzureServiceTokenProvider` は、必要な場合に、**AzureCLIPath** 環境変数に指定されたディレクトリを **Path** 環境変数に追加します。

複数のアカウントを使用して Azure CLI にサインインしている場合、または使用しているアカウントで複数のサブスクリプションにアクセスできる場合は、使用する特定のサブスクリプションを指定する必要があります。  これを行うには、次のコマンドを使用します。

```
az account set --subscription <subscription-id>
```

このコマンドは、エラーが発生した場合にのみ出力を生成します。  現在のアカウント設定を確認するには、次のコマンドを使用します。

```
az account list
```

### <a name="authenticating-with-azure-ad-integrate-authentication"></a>Azure AD 統合認証での認証

Azure AD の認証を使用するには、次の点を確認します。

- ご利用のオンプレミスの Active Directory が [Azure AD と同期している](/azure/active-directory/connect/active-directory-aadconnect)こと。

- ご自分のコードが、ドメインに参加しているマシンで実行されていること。


### <a name="authenticating-to-custom-services"></a>カスタム サービスに対する認証

サービスから Azure サービスを呼び出すとき、Azure サービスがユーザーとアプリケーションの両方にアクセスすることを許可するので、前の手順が動作します。  

カスタム サービスを呼び出すサービスを作成するときには、ローカル開発における認証に Azure AD のクライアント資格情報を使用します。  2 つのオプションがあります。 

1.  次のようにサービス プリンシパルを使用して Azure にサインインします。

    1.  [サービス プリンシパルを作成します](/cli/azure/create-an-azure-service-principal-azure-cli)。

    2.  Azure CLI を使用してサインインします。

        ```
        az login --service-principal -u <principal-id> --password <password>
           --tenant <tenant-id> --allow-no-subscriptions
        ```

        サービス プリンシパルには、サブスクリプションへのアクセス権がない可能性があるため、`--allow-no-subscriptions` 引数を使用します。

2.  環境変数を使用して、サービス プリンシパルの詳細を指定します。  詳細については、「[サービス プリンシパルを使用してアプリケーションを実行する](#running-the-application-using-a-service-principal)」を参照してください。

Azure にサインインした後、`AzureServiceTokenProvider` はサービス プリンシパルを使用してローカル開発用のトークンを取得します。

これは、ローカル開発に対してのみ適用されます。 ソリューションを Azure にデプロイすると、ライブラリは MSI 認証に切り替わります。

<a name="msi"></a>
## <a name="running-the-application-using-a-managed-service-identity"></a>管理対象サービス ID を使用してアプリケーションを実行する 

Azure App Service 上または MSI が有効な Azure VM 上でコードを実行すると、ライブラリは自動的に管理対象サービス ID を使用します。 コードに変更を加える必要はありません。 


<a name="sp"></a>
## <a name="running-the-application-using-a-service-principal"></a>サービス プリンシパルを使用してアプリケーションを実行する 

認証のために、Azure AD のクライアント資格情報を作成する必要がある場合があります。 たとえば、次のような場合です。

1. コードはローカル開発環境で実行されるが、開発者の ID の下ではない。  たとえば、Service Fabric では、ローカル開発用に [NetworkService アカウント](/azure/service-fabric/service-fabric-application-secret-management)を使用します。
 
2. コードはローカル開発環境で実行されるが、開発者自身はカスタム サービスに対して認証を行っているため、自分の開発者 ID は使用できない。 
 
3. コードが、Azure Batch など、管理対象サービス ID をまだサポートしていない Azure コンピューティング リソース上で実行されている。

Azure AD にサインインするために証明書を使用するには、次のようにします。

1. [サービス プリンシパルの証明書](/azure/azure-resource-manager/resource-group-authenticate-service-principal)を作成します。 

2. _LocalMachine_ または _CurrentUser_ ストアのいずれかに証明書をデプロイします。 

3. **AzureServicesAuthConnectionString** という名前の環境変数を次のように設定します。

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={LocalMachine or CurrentUser}
    ```
 
    _{AppId}_、_{TenantId}_、_{Thumbprint}_ は、手順 1 で生成された値に置き換えます。

    **CertificateStoreLocation** には、デプロイ計画に基づいて、_CurrentUser_ または _LocalMachine_ のいずれかを指定する必要があります。

4. アプリケーションを実行します。 

Azure AD の共有シークレット資格情報を使用してサインインするには、次のようにします。

1. [パスワード付きのサービス プリンシパル](/azure/azure-resource-manager/resource-group-authenticate-service-principal)を作成し、それに Key Vault へのアクセス権を付与します。 

2. **AzureServicesAuthConnectionString** という名前の環境変数を次のように設定します。

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret} 
    ```

    _{AppId}_、_{TenantId}_、_{ClientSecret}_ は、手順 1 で生成された値に置き換えます。

3. アプリケーションを実行します。 

すべてを適切に設定した後は、それ以上のコード変更は必要ありません。  `AzureServiceTokenProvider` では、環境変数と証明書を使用して Azure AD に対する認証が行われます。 

<a name="connectionstrings"></a>
## <a name="connection-string-support"></a>接続文字列のサポート

既定では、`AzureServiceTokenProvider` は複数の方法を使用してトークンを取得します。 

プロセスを制御するには、接続文字列を `AzureServiceTokenProvider` のコンストラクターに渡すか、*AzureServicesAuthConnectionString* 環境変数に指定して、使用します。 

次のオプションがサポートされています。

| 接続文字列オプション&nbsp;&nbsp; | シナリオ | 説明|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | ローカル開発 | AzureServiceTokenProvider は AzureCli を使用してトークンを取得します。 |
| `RunAs=Developer; DeveloperTool=VisualStudio` | ローカル開発 | AzureServiceTokenProvider は Visual Studio を使用してトークンを取得します。 |
| `RunAs=CurrentUser;` | ローカル開発 | AzureServiceTokenProvider は Azure AD 統合認証を使用してトークンを取得します。 |
| `RunAs=App;` | 管理対象のサービス ID | AzureServiceTokenProvider は管理対象サービス ID を使用してトークンを取得します。 |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint`<br>`   ={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`  | サービス プリンシパル | `AzureServiceTokenProvider` は証明書を使用して Azure AD からトークンを取得します。 |
| `RunAs=App;AppId={AppId};TenantId={TenantId};`<br>`   CertificateSubjectName={Subject};CertificateStoreLocation=`<br>`   {LocalMachine or CurrentUser}` | サービス プリンシパル | `AzureServiceTokenProvider` は証明書を使用して Azure AD からトークンを取得します|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | サービス プリンシパル |`AzureServiceTokenProvider` はシークレットを使用して Azure AD からトークンを取得します。 |


## <a name="next-steps"></a>次のステップ

- 
  [管理対象サービス ID](/azure/app-service/app-service-managed-service-identity) について詳細を参照する。

- [アプリの認証と承認](/azure/app-service/app-service-authentication-overview)を行う他のさまざまな方法について参照する。

- Azure AD の[認証シナリオ](/azure/active-directory/develop/active-directory-authentication-scenarios#web-browser-to-web-application)について詳細を参照する。
