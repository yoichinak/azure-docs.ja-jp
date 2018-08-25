---
title: 認証を含む Azure ストレージ サービス REST API 操作の呼び出し | Microsoft Docs
description: 認証を含む Azure ストレージ サービス REST API 操作の呼び出し
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/22/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 78e2620ba6e5e29a1f1ac9719b709d5a2f468122
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39529829"
---
# <a name="using-the-azure-storage-rest-api"></a>Azure Storage REST API の使用

この記事では、Blob Storage サービス REST API を使う方法と、サービスの呼び出しを認証する方法について説明します。 REST について何も知らず、REST をどうやって呼び出せばよいかわからない開発者の観点から書かれています。 REST 呼び出しに関するリファレンス ドキュメントを参照し、実際の REST 呼び出しへの変換方法 (フィールドの対応) を確認します。 REST 呼び出しの設定方法を学習した後は、この知識を利用して他のストレージ サービス REST API を使うことができます。

## <a name="prerequisites"></a>前提条件 

このアプリケーションは、ストレージ アカウントの BLOB ストレージ内のコンテナーを一覧表示します。 この記事のコードを試すには、次のものが必要です。 

* 次のワークロードを指定して [Visual Studio 2017](https://www.visualstudio.com/visual-studio-homepage-vs.aspx) をインストールします。
    - Azure の開発

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

* 汎用ストレージ アカウント。 ストレージ アカウントがまだない場合は、[ストレージ アカウントの作成](storage-quickstart-create-account.md)に関するページを参照してください。

* この記事の例では、ストレージ アカウント内のコンテナーの一覧を表示する方法を示します。 出力を表示するには、始める前に、ストレージ アカウントの BLOB ストレージにコンテナーをいくつか追加してください。

## <a name="download-the-sample-application"></a>サンプル アプリケーションのダウンロード

サンプル アプリケーションは、C# で記述されたコンソール アプリケーションです。

アプリケーションのコピーを開発環境にダウンロードするには、[git](https://git-scm.com/) を使います。 

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-rest-api-with-auth.git
```

このコマンドは、ローカルの git フォルダーにリポジトリを複製します。 Visual Studio ソリューションを開くには、storage-dotnet-rest-api-with-auth フォルダーを探して開き、StorageRestApiAuth.sln をダブルクリックします。 

## <a name="what-is-rest"></a>REST とは

REST は *Representational State Transfer* の頭文字です。 具体的な定義は、[Wikipedia](http://en.wikipedia.org/wiki/Representational_state_transfer) で確認してください。

基本的には、REST とは、API を呼び出すとき、または API を呼び出すことができるようにするときに、利用できるアーキテクチャです。 両側で行われていること、および REST 呼び出しを送受信するときに使われている他のソフトウェアからは、独立しています。 Mac、Windows、Linux、Android の携帯電話やタブレット、iPhone、iPod、Web サイトで動作するアプリケーションを作成でき、すべてのプラットフォームで同じ REST API を使うことができます。 REST API を呼び出すときは、データを受け渡すことができます。 REST API では、どのようなプラットフォームから呼び出されたかということは大事ではありません。重要なのは、要求で渡される情報と、応答で提供されるデータです。

REST の使い方の知識は、役に立つスキルです。 Azure 製品チームは、新しい機能を頻繁にリリースします。 多くの場合、新しい機能は、REST インターフェイスを使ってアクセスすることはできますが、**すべての**ストレージ クライアント ライブラリまたは UI (Azure Portal など) で公開されるようにはまだなっていません。 常に最新かつ最高の機能を使いたい場合は、REST を学習する必要があります。 また、独自のライブラリを作成して Azure Storage を操作したい場合や、SDK またはストレージ クライアント ライブラリがないプログラミング言語で Azure Storage にアクセスしたい場合は、REST API を使うことができます。

## <a name="about-the-sample-application"></a>サンプル アプリケーションについて

このサンプル アプリケーションは、ストレージ アカウントのコンテナーを一覧表示します。 REST API ドキュメント内の情報と実際のコードがどのように関連付けられているかを理解すると、他の REST 呼び出しがわかりやすくなります。 

「[Blob Service REST API](/rest/api/storageservices/Blob-Service-REST-API)」(Blob service REST API) を見ると、Blob Storage に対して実行できるすべての操作がわかります。 ストレージ クライアント ライブラリは REST API のラッパーであり、REST API を直接使わずにストレージに簡単にアクセスできます。 ただし、前述のように、ストレージ クライアント ライブラリではなく REST API を使うことが必要な場合があります。

## <a name="rest-api-reference-list-containers-api"></a>REST API リファレンス: List Containers API

次のセクションのコードで要求や応答の一部のフィールドの基になっているものを理解できるように、[ListContainers](/rest/api/storageservices/List-Containers2) 操作の REST API リファレンスのページを見ておきます。

**要求メソッド**: GET。 この動詞は、要求オブジェクトのプロパティとして指定する HTTP メソッドです。 この動詞の他の値としては、呼び出す API に応じて HEAD、PUT、DELETE などがあります。

**要求 URI**: https://myaccount.blob.core.windows.net/?comp=list  これは、Blob Storage アカウント エンドポイント `http://myaccount.blob.core.windows.net` とリソース文字列 `/?comp=list` から作成されています。

[URI パラメーター](/rest/api/storageservices/List-Containers2#uri-parameters): ListContainers を呼び出すときに使うことができる追加のクエリ パラメーターがあります。 そのうちの 2 つは、呼び出しの *timeout* (タイムアウト、秒単位) と、フィルター処理に使われる *prefix* (プレフィックス) です。

もう 1 つの便利なパラメーターは *maxresults* (最大結果数) であり、コンテナーがこの値より多い場合、応答本文には次の要求で返される次のコンテナーを示す *NextMarker* 要素が含まれます。 この機能を使うには、次の要求を行うときに、URI の *marker* パラメーターとして *NextMarker* の値を渡します。 この機能は結果のページ分割に似ています。 

追加のパラメーターを使うには、次の例のように、パラメーターと値をリソース文字列に追加します。

```
/?comp=list&timeout=60&maxresults=100
```

[要求ヘッダー](/rest/api/storageservices/List-Containers2#request-headers)**:** このセクションには、必須および省略可能な要求ヘッダーの一覧が示されています。 必須のヘッダーは、*Authorization* ヘッダー、*x-ms-date* (要求の UTC 時刻を含みます)、*x-ms-version* (使う REST API のバージョンを指定します) の 3 つです。 ヘッダーに *x-ms-client-request-id* を含めるかどうかは自由です。このフィールドには任意の値を設定できます。ログが有効になっていると、この値がストレージ分析ログに書き込まれます。

[要求本文](/rest/api/storageservices/List-Containers2#request-body)**:** ListContainers には要求本文はありません。 要求本文は、BLOB をアップロードするときにすべての PUT 操作で使われます。また、SetContainerAccessPolicy では、保存されているアクセス ポリシーの XML リストを送信して適用できます。 保存されているアクセス ポリシーについては、「[Shared Access Signatures (SAS) の使用](storage-dotnet-shared-access-signature-part-1.md)」をご覧ください。

[応答状態コード](/rest/api/storageservices/List-Containers2#status-code)**:** 知っておく必要があるすべての状態コードについて説明されています。 この例では、HTTP 状態コード 200 は正常です。 HTTP 状態コードの完全な一覧については、「[Status Code Definitions](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)」(状態コードの定義) をご覧ください。 ストレージ REST API に固有のエラー コードについては、「[REST API の一般的なエラー コード](/rest/api/storageservices/common-rest-api-error-codes)」をご覧ください。

[応答ヘッダー](/rest/api/storageservices/List-Containers2#response-headers)**:** "*コンテンツ タイプ*"、*x-ms-request-id* (渡した要求 ID、ある場合)、*x-ms-version* (使われた Blob service のバージョンを示します)、*Date* (UTC、要求が行われた日時を示します) などがあります。

[応答本文](/rest/api/storageservices/List-Containers2#response-body): このフィールドは、要求されたデータを提供する XML 構造体です。 この例では、応答はコンテナーのリストとそのプロパティです。

## <a name="creating-the-rest-request"></a>REST 要求の作成

始める前にいくつか注意事項があります。セキュリティのため、運用環境で実行するときは HTTP ではなく HTTPS を使う必要があります。 ただし、この演習では、要求と応答のデータを見ることができるように、HTTP を使う必要があります。 実際の REST 呼び出しで要求と応答の情報を表示するには、[Fiddler](http://www.telerik.com/fiddler) または同様のアプリケーションをダウンロードできます。 Visual Studio のソリューションでは、ストレージ アカウントの名前とキーはクラスにハードコーディングされており、ListContainersAsyncREST メソッドは、REST 要求のさまざまなコンポーネントの作成に使われるメソッドにストレージ アカウントの名前とキーを渡します。 実際のアプリケーションでは、ストレージ アカウントの名前とキーは、構成ファイルまたは環境変数に格納されているか、または Azure Key Vault から取得します。

このサンプル プロジェクトでは、Authorization ヘッダーを作成するためのコードは別のクラスになっており、クラス全体をコピーして独自のソリューションに追加し、"そのまま" 使うことができます。 Authorization ヘッダーのコードは、Azure Storage に対するほとんどの REST API 呼び出しで機能します。

HttpRequestMessage オブジェクトである要求を作成するには、Program.cs の ListContainersAsyncREST を使います。 要求を作成する手順は次のとおりです。 

* サービスの呼び出しに使われる URI を作成します。 
* HttpRequestMessage オブジェクトを作成して、ペイロードを設定します。 この例では何も渡さないので、ListContainersAsyncREST のペイロードは null です。
* 要求ヘッダー x-ms-date と x-ms-version を追加します。
* Authorization ヘッダーを取得して追加します。

次のような基本情報が必要です。 

*  ListContainers の場合、**メソッド**は `GET` です。 この値は、要求をインスタンス化するときに設定されます。 
*  **リソース**は呼び出されている API を示す URI のクエリ部分であり、値は `/?comp=list` です。 前述のように、リソースは、[ListContainers API](/rest/api/storageservices/List-Containers2) に関する情報を示す参照ドキュメントのページにあります。
*  URI は、そのストレージ アカウントの Blob サービス エンドポイントを作成してリソースを連結することにより、作成されます。 **要求の URI** の最終的な値は、`http://contosorest.blob.core.windows.net/?comp=list` です。
*  ListContainers の場合、**要求本文**は null であり、余分な**ヘッダー**はありません。

他の API では、*ifMatch* などの他のパラメーターを渡すことがあります。 IfMatch を使う場合の例は、PutBlob を呼び出すときです。 この場合、ifMatch には eTag を設定し、指定した eTag が BLOB の現在の eTag と一致する場合にのみ、BLOB を更新します。 eTag を取得した後で他のユーザーが BLOB を更新している場合、変更はオーバーライドされません。 

最初に、`uri` と `payload` を設定します。 

```csharp
// Construct the URI. This will look like this:
//   https://myaccount.blob.core.windows.net/resource
String uri = string.Format("http://{0}.blob.core.windows.net?comp=list", storageAccountName);

// Set this to whatever payload you desire. Ours is null because 
//   we're not passing anything in.
Byte[] requestPayload = null;
```

次に、要求をインスタンス化し、メソッドを `GET` に設定して、URI を指定します。

```csharp 
//Instantiate the request message with a null payload.
using (var httpRequestMessage = new HttpRequestMessage(HttpMethod.Get, uri)
{ Content = (requestPayload == null) ? null : new ByteArrayContent(requestPayload) })
{
```

要求ヘッダー x-ms-date と x-ms-version を追加します。 コード内のこの場所では、呼び出しに必要な他の要求ヘッダーも追加します。 この例では、追加ヘッダーはありません。 余分なヘッダーで渡す API の例は SetContainerACL です。 Blob Storage の場合は、"x-ms-blob-public-access" という名前のヘッダーとアクセス レベルの値を追加します。

```csharp
    // Add the request headers for x-ms-date and x-ms-version.
    DateTime now = DateTime.UtcNow;
    httpRequestMessage.Headers.Add("x-ms-date", now.ToString("R", CultureInfo.InvariantCulture));
    httpRequestMessage.Headers.Add("x-ms-version", "2017-07-29");
    // If you need any additional headers, add them here before creating
    //   the authorization header. 
```

Authorization ヘッダーを作成するメソッドを呼び出し、要求ヘッダーに追加します。 Authorization ヘッダーを作成する方法については後で説明します。 メソッドの名前は GetAuthorizationHeader で、次はそのコード スニペットです。

```csharp
    // Get the authorization header and add it.
    httpRequestMessage.Headers.Authorization = AzureStorageAuthenticationHelper.GetAuthorizationHeader(
        storageAccountName, storageAccountKey, now, httpRequestMessage);
```

この時点で、`httpRequestMessage` には Authorization ヘッダーが設定された REST 要求が含まれています。 

## <a name="call-the-rest-api-with-the-request"></a>要求で REST API を呼び出す

要求が完成したので、SendAsync を呼び出して REST 要求を送信できます。 SendAsync は、API を呼び出し、戻った応答を取得します。 応答の StatusCode を調べ (200 は正常)、応答を解析します。 この例では、コンテナーの XML リストを取得しています。 GetRESTRequest メソッドを呼び出して要求を作成するコードを確認し、要求を実行してから、コンテナーの一覧の応答を調べてみましょう。

```csharp 
    // Send the request.
    using (HttpResponseMessage httpResponseMessage = 
      await new HttpClient().SendAsync(httpRequestMessage, cancellationToken))
    {
        // If successful (status code = 200), 
        //   parse the XML response for the container names.
        if (httpResponseMessage.StatusCode == HttpStatusCode.OK)
        {
            String xmlString = await httpResponseMessage.Content.ReadAsStringAsync();
            XElement x = XElement.Parse(xmlString);
            foreach (XElement container in x.Element("Containers").Elements("Container"))
            {
                Console.WriteLine("Container name = {0}", container.Element("Name").Value);
            }
        }
    }
}
```

SendAsync への呼び出しを行うときに、[Fiddler](https://www.telerik.com/fiddler) などのネットワーク スニファーを実行すると、要求と応答の情報を確認できます。 例を見てみましょう。 ストレージ アカウントの名前は、*contosorest* です。

**要求:**

```
GET /?comp=list HTTP/1.1
```

**要求ヘッダー:**

```
x-ms-date: Thu, 16 Nov 2017 23:34:04 GMT
x-ms-version: 2014-02-14
Authorization: SharedKey contosorest:1dVlYJWWJAOSHTCPGiwdX1rOS8B4fenYP/VrU0LfzQk=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**実行後に返される状態コードと応答ヘッダー:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 3e889876-001e-0039-6a3a-5f4396000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 00:23:42 GMT
Content-Length: 1511
```

**応答本文 (XML):** ListContainers の場合、コンテナーの一覧とそのプロパティが表示されます。

```xml  
<?xml version="1.0" encoding="utf-8"?>
<EnumerationResults 
  ServiceEndpoint="http://contosorest.blob.core.windows.net/">
  <Containers>
    <Container>
      <Name>container-1</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:39:48 GMT</Last-Modified>
        <Etag>"0x8D46CBD5A7C301D"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-2</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:40:50 GMT</Last-Modified>
        <Etag>"0x8D46CBD7F49E9BD"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-3</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:41:10 GMT</Last-Modified>
        <Etag>"0x8D46CBD8B243D68"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-4</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:41:25 GMT</Last-Modified>
        <Etag>"0x8D46CBD93FED46F"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
        </Properties>
      </Container>
      <Container>
        <Name>container-5</Name>
        <Properties>
          <Last-Modified>Thu, 16 Mar 2017 22:41:39 GMT</Last-Modified>
          <Etag>"0x8D46CBD9C762815"</Etag>
          <LeaseStatus>unlocked</LeaseStatus>
          <LeaseState>available</LeaseState>
        </Properties>
      </Container>
  </Containers>
  <NextMarker />
</EnumerationResults>
```

要求を作成し、サービスを呼び出して、結果を解析する方法がわかったので、次に Authorization ヘッダーを作成する方法を見てみましょう。 Authorization ヘッダーの作成は複雑ですが、いったん動作するようになったコードは、すべてのストレージ サービス REST API で使うことができます。

## <a name="creating-the-authorization-header"></a>Authorization ヘッダーの作成

> [!TIP]
> Azure Storage で、BLOB およびキュー サービス (プレビュー) のために Azure Active Directory (Azure AD) の統合がサポートされるようになりました。 Azure AD では、Azure Storage への要求を承認するためのよりシンプルなエクスペリエンスを提供します。 Azure AD を使用して REST 操作を承認する方法の詳細については、「[Authenticate with Azure Active Directory (Preview)](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory)」 (Azure Active Directory (AAD) での認証 (プレビュー)) を参照してください。 Azure Storage との Azure AD の統合の概要については、「[Authenticate access to Azure Storage using Azure Active Directory (Preview)](storage-auth-aad.md)」 (Azure Active Directory を使用した Azure Storage へのアクセスの認証 (プレビュー)) を参照してください。

[Azure ストレージ サービスの認証](/rest/api/storageservices/Authorization-for-the-Azure-Storage-Services)を実行する方法を (コードではなく) 概念的に説明した記事があります。
その記事から本当に必要な部分を取り出し、コードを示します。

最初に、共有キー認証を使います。 Authorization ヘッダーの形式は次のようになります。

```  
Authorization="SharedKey <storage account name>:<signature>"  
```

signature フィールドはハッシュ ベースのメッセージ認証コード (HMAC) であり、要求から作成され、SHA256 アルゴリズムを使って計算された後、Base64 エンコードを使ってエンコードされます。 よろしいでしょうか  (頑張ってください、"*正規化*" という言葉さえまだ耳にしていないのですから)。

次のコード スニペットは、共有キー シグネチャ文字列の形式を示しています。

```csharp  
StringToSign = VERB + "\n" +  
               Content-Encoding + "\n" +  
               Content-Language + "\n" +  
               Content-Length + "\n" +  
               Content-MD5 + "\n" +  
               Content-Type + "\n" +  
               Date + "\n" +  
               If-Modified-Since + "\n" +  
               If-Match + "\n" +  
               If-None-Match + "\n" +  
               If-Unmodified-Since + "\n" +  
               Range + "\n" +  
               CanonicalizedHeaders +  
               CanonicalizedResource;  
```

これらのフィールドの大部分はほとんど使われません。 Blob Storage の場合、VERB、md5、コンテンツの長さ、正規化されたヘッダー、および正規化されたリソースを指定します。 それ以外は空白のままでかまいません (ただし、空白であることがわかるように、`\n` を入れておきます)。

CanonicalizedHeaders および CanonicalizedResource とは何でしょうか。 よい質問です。 実際、正規化 (canonicalized) とはどのような意味でしょうか。 Microsoft Word では、canonicalized は単語として認識すらされません。 [Wikipedia での正規化の説明](http://en.wikipedia.org/wiki/Canonicalization)は次のようなものです。"*コンピューター サイエンスでは、正規化 (canonicalization、standardization、normalization など) とは、複数の方法で表現できるデータを 1 つの "標準的な" つまり正規の形式に変換する処理のことです。*" つまり、項目のリスト (正規化されたヘッダーの場合のヘッダーなど) を取得して、必要な形式に標準化することです。 基本的に、Microsoft で決定されている形式に一致させる必要があります。

それでは 2 つの正規化されたフィールドについて説明します。これらは Authorization ヘッダーを作成するために必要なものです。

**正規化されたヘッダー**

この値を作成するには、"x-ms-" で始まるヘッダーを取得して並べ替えてから、`[key:value\n]` インスタンスの文字列に書式設定して、1 つの文字列に連結します。 この例では、正規化されたヘッダーは次のようになります。 

```
x-ms-date:Fri, 17 Nov 2017 00:44:48 GMT\nx-ms-version:2017-07-29\n
```

この出力を作成するために使われるコードを次に示します。

```csharp 
private static string GetCanonicalizedHeaders(HttpRequestMessage httpRequestMessage)
{
    var headers = from kvp in httpRequestMessage.Headers
                    where kvp.Key.StartsWith("x-ms-", StringComparison.OrdinalIgnoreCase)
                    orderby kvp.Key
                    select new { Key = kvp.Key.ToLowerInvariant(), kvp.Value };

    StringBuilder sb = new StringBuilder();

    // Create the string in the right format; this is what makes the headers "canonicalized" --
    //   it means put in a standard format. http://en.wikipedia.org/wiki/Canonicalization
    foreach (var kvp in headers)
    {
        StringBuilder headerBuilder = new StringBuilder(kvp.Key);
        char separator = ':';

        // Get the value for each header, strip out \r\n if found, then append it with the key.
        foreach (string headerValues in kvp.Value)
        {
            string trimmedValue = headerValues.TrimStart().Replace("\r\n", String.Empty);
            headerBuilder.Append(separator).Append(trimmedValue);

            // Set this to a comma; this will only be used 
            //   if there are multiple values for one of the headers.
            separator = ',';
        }
        sb.Append(headerBuilder.ToString()).Append("\n");
    }
    return sb.ToString();
}      
```

**正規化されたリソース**

シグネチャ文字列のこの部分は、要求の対象となるストレージ アカウントを表します。 要求の URI は `<http://contosorest.blob.core.windows.net/?comp=list>` であり、実際のアカウント名が含まれることを思い出してください (この場合は `contosorest`)。 この例では次のような結果が返されます。

```
/contosorest/\ncomp:list
```

クエリ パラメーターがある場合は、それも含まれます。 次に示すコードでは、追加のクエリ パラメーターと、複数の値を持つクエリ パラメーターも処理しています。 ここではすべての REST API で動作するコードを作成しているので、ListContainers メソッドでは必要ないものも含めて、可能性のあるすべての場合に対応しています。

```csharp 
private static string GetCanonicalizedResource(Uri address, string storageAccountName)
{
    // The absolute path will be "/" because for we're getting a list of containers.
    StringBuilder sb = new StringBuilder("/").Append(storageAccountName).Append(address.AbsolutePath);

    // Address.Query is the resource, such as "?comp=list".
    // This ends up with a NameValueCollection with 1 entry having key=comp, value=list.
    // It will have more entries if you have more query parameters.
    NameValueCollection values = HttpUtility.ParseQueryString(address.Query);

    foreach (var item in values.AllKeys.OrderBy(k => k))
    {
        sb.Append('\n').Append(item).Append(':').Append(values[item]);
    }

    return sb.ToString();
}
```

正規化された文字列が設定されたので、Authorization ヘッダー自体の作成方法を説明します。 最初に、この記事で前に示した StringToSign の形式で、メッセージのシグネチャの文字列を作成します。 この概念はコードのコメントを使った方が簡単に説明できるので、Authorization ヘッダーを返す最後のメソッドを次に示します。

```csharp
internal static AuthenticationHeaderValue GetAuthorizationHeader(
    string storageAccountName, string storageAccountKey, DateTime now,
    HttpRequestMessage httpRequestMessage, string ifMatch = "", string md5 = "")
{
    // This is the raw representation of the message signature.
    HttpMethod method = httpRequestMessage.Method;
    String MessageSignature = String.Format("{0}\n\n\n{1}\n{5}\n\n\n\n{2}\n\n\n\n{3}{4}",
                method.ToString(),
                (method == HttpMethod.Get || method == HttpMethod.Head) ? String.Empty
                  : httpRequestMessage.Content.Headers.ContentLength.ToString(),
                ifMatch,
                GetCanonicalizedHeaders(httpRequestMessage),
                GetCanonicalizedResource(httpRequestMessage.RequestUri, storageAccountName),
                md5);

    // Now turn it into a byte array.
    byte[] SignatureBytes = Encoding.UTF8.GetBytes(MessageSignature);

    // Create the HMACSHA256 version of the storage key.
    HMACSHA256 SHA256 = new HMACSHA256(Convert.FromBase64String(storageAccountKey));

    // Compute the hash of the SignatureBytes and convert it to a base64 string.
    string signature = Convert.ToBase64String(SHA256.ComputeHash(SignatureBytes));

    // This is the actual header that will be added to the list of request headers.
    AuthenticationHeaderValue authHV = new AuthenticationHeaderValue("SharedKey",
        storageAccountName + ":" + Convert.ToBase64String(SHA256.ComputeHash(SignatureBytes)));
    return authHV;
}
```

このコードを実行したときの結果の MessageSignature は次のようになります。

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 01:07:37 GMT\nx-ms-version:2017-07-29\n/contosorest/\ncomp:list
```

Authorization ヘッダーの最終的な値を次に示します。

```
SharedKey contosorest:Ms5sfwkA8nqTRw7Uury4MPHqM6Rj2nfgbYNvUKOa67w=
```

Authorization ヘッダーは、応答を送信する前に要求ヘッダーに配置される最後のヘッダーです。

ストレージ サービス REST API の呼び出しに使われる要求の作成に使うことができるクラスとして、知っておく必要のあるすべてのこととコードが含まれます。

## <a name="how-about-another-example"></a>別の例 

コンテナー *container-1* の ListBlobs を呼び出すようにコードを変更する方法を見てみましょう。 これはコンテナーを一覧表示するコードとほぼ同じであり、唯一の違いは URI と応答を解析する方法です。 

[ListBlobs](/rest/api/storageservices/List-Blobs) のリファレンス ドキュメントを見ると、メソッドが *GET* であり、RequestURI は次のようなものであることがわかります。

```
https://myaccount.blob.core.windows.net/container-1?restype=container&comp=list
```

ListContainersAsyncREST で、ListBlobs の API に URI を設定するコードを変更します。 コンテナーの名前は **container-1** です。

```csharp
String uri = 
    string.Format("http://{0}.blob.core.windows.net/container-1?restype=container&comp=list",
      storageAccountName);

```

次に、応答を処理する部分で、コンテナーではなく BLOB を探すようにコードを変更します。

```csharp
foreach (XElement container in x.Element("Blobs").Elements("Blob"))
{
    Console.WriteLine("Blob name = {0}", container.Element("Name").Value);
}
```

このサンプルを実行すると、次のような結果が得られます。

**正規化されたヘッダー:**

```
x-ms-date:Fri, 17 Nov 2017 05:16:48 GMT\nx-ms-version:2017-07-29\n
```

**正規化されたリソース:**

```
/contosorest/container-1\ncomp:list\nrestype:container
```

**メッセージのシグネチャ:**

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 05:16:48 GMT
  \nx-ms-version:2017-07-29\n/contosorest/container-1\ncomp:list\nrestype:container
```

**Authorization ヘッダー:**

```
SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
```

次の値は [Fiddler](http://www.telerik.com/fiddler) からのものです。

**要求:**

```
GET http://contosorest.blob.core.windows.net/container-1?restype=container&comp=list HTTP/1.1
```

**要求ヘッダー:**

```
x-ms-date: Fri, 17 Nov 2017 05:16:48 GMT
x-ms-version: 2017-07-29
Authorization: SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**実行後に返される状態コードと応答ヘッダー:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 7e9316da-001e-0037-4063-5faf9d000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 05:20:21 GMT
Content-Length: 1135
```

**応答本文 (XML):** この XML 応答では、BLOB の一覧とそのプロパティが示されています。 

```xml
<?xml version="1.0" encoding="utf-8"?>
<EnumerationResults 
    ServiceEndpoint="http://contosorest.blob.core.windows.net/" ContainerName="container-1">
    <Blobs>
        <Blob>
            <Name>DogInCatTree.png</Name>
            <Properties><Last-Modified>Fri, 17 Nov 2017 01:41:14 GMT</Last-Modified>
            <Etag>0x8D52D5C4A4C96B0</Etag>
            <Content-Length>419416</Content-Length>
            <Content-Type>image/png</Content-Type>
            <Content-Encoding />
            <Content-Language />
            <Content-MD5 />
            <Cache-Control />
            <Content-Disposition />
            <BlobType>BlockBlob</BlobType>
            <LeaseStatus>unlocked</LeaseStatus>
            <LeaseState>available</LeaseState>
            <ServerEncrypted>true</ServerEncrypted>
            </Properties>
        </Blob>
        <Blob>
            <Name>GuyEyeingOreos.png</Name>
            <Properties>
                <Last-Modified>Fri, 17 Nov 2017 01:41:14 GMT</Last-Modified>
                <Etag>0x8D52D5C4A25A6F6</Etag>
                <Content-Length>167464</Content-Length>
                <Content-Type>image/png</Content-Type>
                <Content-Encoding />
                <Content-Language />
                <Content-MD5 />
                <Cache-Control />
                <Content-Disposition />
                <BlobType>BlockBlob</BlobType>
                <LeaseStatus>unlocked</LeaseStatus>
                <LeaseState>available</LeaseState>
                <ServerEncrypted>true</ServerEncrypted>
            </Properties>
            </Blob>
        </Blobs>
    <NextMarker />
</EnumerationResults>
```

## <a name="summary"></a>まとめ

この記事では、Blob Storage REST API に対して要求を行って、コンテナーの一覧またはコンテナー内の BLOB の一覧を取得する方法を学習しました。 また、REST API 呼び出しの承認シグネチャを作成する方法、それを REST 要求で使う方法、および応答を調べる方法についても学習しました。

## <a name="next-steps"></a>次の手順

* [Blob service の REST API](/rest/api/storageservices/blob-service-rest-api)
* [ファイル サービスの REST API](/rest/api/storageservices/file-service-rest-api)
* [キュー サービスの REST API](/rest/api/storageservices/queue-service-rest-api)