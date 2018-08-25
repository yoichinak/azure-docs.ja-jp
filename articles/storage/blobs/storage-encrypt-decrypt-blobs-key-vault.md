---
title: 'チュートリアル: Azure Key Vault を使用した Azure Storage 内の BLOB の暗号化と復号化 | Microsoft Docs'
description: Azure Key Vault で Microsoft Azure Storage のクライアント側暗号化を使用して BLOB を暗号化および復号化する方法を説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: adhurwit
ms.component: blobs
ms.openlocfilehash: f5382644af7e79234d6c3944cf354a3b2a70cc90
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397623"
---
# <a name="tutorial-encrypt-and-decrypt-blobs-in-microsoft-azure-storage-using-azure-key-vault"></a>チュートリアル: Azure Key Vault を使用した Microsoft Azure Storage 内の BLOB の暗号化と復号化
## <a name="introduction"></a>はじめに
このチュートリアルでは、Azure Key Vault でクライアント側ストレージ暗号化を利用する方法について説明します。 これらのテクノロジを使用して、コンソール アプリケーションで BLOB を暗号化および復号化する手順を説明します。

**推定所要時間:** 20 分

Azure Key Vault の概要については、「[Azure Key Vault とは](../../key-vault/key-vault-whatis.md)」をご覧ください。

Azure Storage のクライアント側暗号化の概要については、「[Microsoft Azure Storage のクライアント側の暗号化と Azure Key Vault](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」をご覧ください。

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、以下が必要です。

* Azure ストレージ アカウント
* Visual Studio 2013 以降
* Azure PowerShell

## <a name="overview-of-client-side-encryption"></a>クライアント側暗号化の概要
Azure Storage のクライアント側暗号化の概要については、「[Microsoft Azure Storage のクライアント側の暗号化と Azure Key Vault](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」をご覧ください。

ここでは、クライアント側暗号化のしくみを簡単に説明します。

1. Azure ストレージ クライアント SDK は、1 回使用の対称キーであるコンテンツ暗号化キー (CEK) を生成します。
2. ユーザー データは、この CEK を使用して暗号化されます。
3. CEK は、キー暗号化キー (KEK) を使用してラップ (暗号化) されます。 KEK は、キー識別子によって識別され、非対称キー ペアまたは対称キーのどちらでもよく、ローカルに管理することも、Azure Key Vault に保存することもできます。 Storage クライアント自体が KEK にアクセスすることはありません。 クライアントは、Key Vault によって提供されるキー ラップ アルゴリズムを呼び出すだけです。 ユーザーは、必要に応じてキー ラップ/ラップ解除にカスタム プロバイダーを使用できます。
4. 暗号化されたデータは、Azure Storage サービスにアップロードされます。

## <a name="set-up-your-azure-key-vault"></a>Azure Key Vault のセットアップ
このチュートリアルを続けるには、チュートリアル「[Azure Key Vault の概要](../../key-vault/key-vault-get-started.md)」で説明されている以下の手順を実行する必要があります。

* Key Vault を作成します。
* キーやシークレットを Key Vault に追加します。
* Azure Active Directory にアプリケーションを登録します。
* キーまたはシークレットを使用してアプリケーションを承認します。

アプリケーションを Azure Active Directory に登録するときに生成された ClientID と ClientSecret を記録しておきます。

Key Vault で両方のキーを作成します。 以降のチュートリアルでは、ContosoKeyVault および TestRSAKey1 という名前を使用したものとして説明します。

## <a name="create-a-console-application-with-packages-and-appsettings"></a>パッケージおよび AppSettings でコンソール アプリケーションを作成します。
Visual Studio で、新しいコンソール アプリケーションを作成します。

パッケージ マネージャー コンソールで、必要な nuget パッケージを追加します。

```
Install-Package WindowsAzure.Storage

// This is the latest stable release for ADAL.
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

Install-Package Microsoft.Azure.KeyVault
Install-Package Microsoft.Azure.KeyVault.Extensions
```

App.Config に AppSettings を追加します。

```xml
<appSettings>
    <add key="accountName" value="myaccount"/>
    <add key="accountKey" value="theaccountkey"/>
    <add key="clientId" value="theclientid"/>
    <add key="clientSecret" value="theclientsecret"/>
    <add key="container" value="stuff"/>
</appSettings>
```

次の `using` ディレクティブを追加し、プロジェクトに System.Configuration への参照を追加します。

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Configuration;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.Azure.KeyVault;
using System.Threading;        
using System.IO;
```

## <a name="add-a-method-to-get-a-token-to-your-console-application"></a>コンソール アプリケーションにトークンを取得するメソッドを追加します。
次のメソッドは、Key Vault へのアクセスを認証する必要がある Key Vault クラスによって使用されます。

```csharp
private async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);
    ClientCredential clientCred = new ClientCredential(
        ConfigurationManager.AppSettings["clientId"],
        ConfigurationManager.AppSettings["clientSecret"]);
    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)
        throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

## <a name="access-storage-and-key-vault-in-your-program"></a>プログラムでのストレージおよび Key Vault へのアクセス
Main 関数に次のコードを追加します。

```csharp
// This is standard code to interact with Blob storage.
StorageCredentials creds = new StorageCredentials(
    ConfigurationManager.AppSettings["accountName"],
       ConfigurationManager.AppSettings["accountKey"]);
CloudStorageAccount account = new CloudStorageAccount(creds, useHttps: true);
CloudBlobClient client = account.CreateCloudBlobClient();
CloudBlobContainer contain = client.GetContainerReference(ConfigurationManager.AppSettings["container"]);
contain.CreateIfNotExists();

// The Resolver object is used to interact with Key Vault for Azure Storage.
// This is where the GetToken method from above is used.
KeyVaultKeyResolver cloudResolver = new KeyVaultKeyResolver(GetToken);
```

> [!NOTE]
> Key Vault のオブジェクト モデル
> 
> 実際には 2 種類の Key Vault オブジェクト モデルがあることを理解しておくことが重要です。1 つは REST API (KeyVault 名前空間) に基づくもので、もう 1 つはクライアント側暗号化の拡張機能です。
> 
> Key Vault クライアントは REST API とやり取りして、Key Vault に含まれる 2 種類のものの JSON Web キーとシークレットを認識します。
> 
> Key Vault 拡張機能は、Azure Storage のクライアント側暗号化用に特に作成されたクラスです。 Key Resolver の概念に基づくキー用のインターフェイス (IKey) とクラスを含みます。 IKey には 2 種類の実装 RSAKey と SymmetricKey があり、これらを知っておく必要があります。 現在はたまたま Key Vault に含まれるものと一緒に存在していますが、これらは独立したクラスです (したがって、Key Vault クライアントによって取得されたキーとシークレットは IKey を実装していません)。
> 
> 

## <a name="encrypt-blob-and-upload"></a>BLOB の暗号化とアップロード
次のコードを追加して BLOB を暗号化し、Azure ストレージ アカウントにアップロードします。 **ResolveKeyAsync** メソッドは IKey を返します。

```csharp
// Retrieve the key that you created previously.
// The IKey that is returned here is an RsaKey.
// Remember that we used the names contosokeyvault and testrsakey1.
var rsa = cloudResolver.ResolveKeyAsync("https://contosokeyvault.vault.azure.net/keys/TestRSAKey1", CancellationToken.None).GetAwaiter().GetResult();

// Now you simply use the RSA key to encrypt by setting it in the BlobEncryptionPolicy.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(rsa, null);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

// Reference a block blob.
CloudBlockBlob blob = contain.GetBlockBlobReference("MyFile.txt");

// Upload using the UploadFromStream method.
using (var stream = System.IO.File.OpenRead(@"C:\data\MyFile.txt"))
    blob.UploadFromStream(stream, stream.Length, null, options, null);
```

> [!NOTE]
> BlobEncryptionPolicy コンストラクターを見ると、キーおよびリゾルバーを受け付けられることがわかります。 現在リゾルバーは既定のキーをサポートしていないため、暗号化にリゾルバーを使用できないことに注意してください。
> 
> 

## <a name="decrypt-blob-and-download"></a>BLOB の復号化とダウンロード
Resolver クラスを使用すると、復号化に役立ちます。 暗号化に使用されるキーの ID はメタデータ内の BLOB に関連付けられているので、キーを取得し、キーと BLOB との関連付けを憶えている必要はありません。 キーが Key Vault に残っているのを確認することだけが必要です。   

RSA キーの秘密キーは Key Vault に残っているので、復号化を行うには、CEK を含む BLOB メタデータから暗号化されたキーを復号化のために Key Vault に送信します。

アップロードした BLOB を復号化するには、以下を追加します。

```csharp
// In this case, we will not pass a key and only pass the resolver because
// this policy will only be used for downloading / decrypting.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
    blob.DownloadToStream(np, null, options, null);
```

> [!NOTE]
> キー管理を容易にするリゾルバーとして、他に AggregateKeyResolver および CachingKeyResolver の 2 つがあります。
> 
> 

## <a name="use-key-vault-secrets"></a>Key Vault シークレットの使用
シークレットは基本的に対称キーなので、クライアント側暗号化でシークレットを使用するには SymmetricKey クラスを使用します。 ただし、前に説明したように、Key Vault のシークレットは SymmetricKey に対して正確にマップしていません。 いくつかの点について理解しておく必要があります。

* SymmetricKey のキーは、128、192、256、384、または 512 ビットの固定長でなければなりません。
* SymmetricKey のキーは Base64 でエンコードされている必要があります。
* SymmetricKey として使用される Key Vault シークレットは、Key Vault でのコンテンツ タイプが "application/octet-stream" でなければなりません。

SymmetricKey として使用できるシークレットを Key Vault に作成する PowerShell の例を次に示します。
ハードコーディングされた値 $key は、デモのみを目的としています。 独自のコードでこのキーを生成できます。

```csharp
// Here we are making a 128-bit key so we have 16 characters.
//     The characters are in the ASCII range of UTF8 so they are
//    each 1 byte. 16 x 8 = 128.
$key = "qwertyuiopasdfgh"
$b = [System.Text.Encoding]::UTF8.GetBytes($key)
$enc = [System.Convert]::ToBase64String($b)
$secretvalue = ConvertTo-SecureString $enc -AsPlainText -Force

// Substitute the VaultName and Name in this command.
$secret = Set-AzureKeyVaultSecret -VaultName 'ContoseKeyVault' -Name 'TestSecret2' -SecretValue $secretvalue -ContentType "application/octet-stream"
```

コンソール アプリケーションでは、前と同じ呼び出しを使用して、このシークレットを SymmetricKey として取得できます。

```csharp
SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
    "https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
    CancellationToken.None).GetAwaiter().GetResult();
```
これで終了です。 機能を有効にご活用ください。

## <a name="next-steps"></a>次の手順
C# での Microsoft Azure Storage の使用について詳しくは、「[.NET 用の Microsoft Azure Storage クライアント ライブラリ](https://msdn.microsoft.com/library/azure/dn261237.aspx)」をご覧ください。

BLOB REST API について詳しくは、「[BLOB サービス REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx)」をご覧ください。

Microsoft Azure Storage の最新情報については、[Microsoft Azure Storage チーム ブログ](http://blogs.msdn.com/b/windowsazurestorage/)をご覧ください。
