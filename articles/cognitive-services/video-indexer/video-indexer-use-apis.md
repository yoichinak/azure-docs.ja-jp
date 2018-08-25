---
title: Azure Video Indexer API を使用する | Microsoft Docs
description: この記事では、Video Indexer API 基本的な使用方法について説明します。
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/25/2018
ms.author: juliako
ms.openlocfilehash: 73359955861b88f2bc5ca297c32fa78c2632148c
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449481"
---
# <a name="use-azure-video-indexer-api"></a>Azure Video Indexer API を使用する

> [!Note]
> Video Indexer V1 API は 2018 年 8 月 1 日に非推奨になりました。 今後は Video Indexer v2 API を使用してください。 <br/>Video Indexer v2 API を使用して開発する場合は、[こちら](https://api-portal.videoindexer.ai/)の手順を参照してください。 

Video Indexer は、Microsoft が提供するさまざまなオーディオおよびビデオの人工知能 (AI) テクノロジが 1 つに統合されたサービスです。このサービスを利用すると、開発がより簡単になります。 API は、開発者がクラウド プラットフォームの規模、世界的な展開、可用性、信頼性を気にすることなく、Media AI テクノロジの使用に集中できるように設計されています。 この API を使用すると、ファイルをアップロードし、詳細なビデオの分析情報を取得し、アプリケーションに埋め込む分析情報とプレーヤー ウィジェットの URL を取得するなどのタスクを実行できます。

Video Indexer アカウントを作成する場合、無料試用アカウント (一定分数の無料インデックス作成を利用可能) または有料オプション (クォータによる制限がありません) を選択できます。 無料試用アカウントで Video Indexer 使用すると、Web サイト ユーザーは最大 600 分間の無料インデックス作成、API ユーザーは最大 2,400 分間の無料インデックス作成を利用できます。 有料オプションでは、[Azure サブスクリプションと Azure Media Services アカウント](connect-to-azure.md)に接続する Video Indexer アカウントを作成します。 Media アカウント関連の料金と同様に、インデックス作成時間 (分単位) の料金がかかります。 

この記事では、開発者が [Video Indexer API](https://api-portal.videoindexer.ai/) を利用する方法について説明します。 Video Indexer サービスのより詳しい概要については、[概要](video-indexer-overview.md)に関する記事を参照してください。

## <a name="subscribe-to-the-api"></a>API にサブスクライブする

1. サインインします。

    Video Indexer での開発を始めるには、まず [Video Indexer](https://api-portal.videoindexer.ai/) ポータルにサインインする必要があります。 
    
    ![サインアップ](./media/video-indexer-use-apis/video-indexer-api01.png)

    > [!Important]
    > * Video Indexer へのサインアップ時と同じプロバイダーを使用する必要があります。
    > * 個人用の Google アカウントと Microsoft (Outlook/Live) アカウントは試用アカウントにのみ使用できます。 Azure に接続するアカウントには、Azure AD が必要です。
    > * 1 つの電子メール アドレスで有効にすることができるアカウントは 1 つのみです。 ユーザーが user@gmail.com を使用して LinkedIn にサインインした後に、user@gmail.com を使用して Google にサインインしようとすると、ユーザーは既に存在しているというエラー ページが表示されます。


2. サブスクライブします。

    [[製品]](https://api-portal.videoindexer.ai/products) タブを選択します。次に、[承認] を選択し、サブスクライブします。 
    
    ![サインアップ](./media/video-indexer-use-apis/video-indexer-api02.png)

    > [!NOTE]
    > 新しいユーザーは自動的に Authorization にサブスクライブされます。
    
    サブスクライブすると、サブスクリプション、プライマリ キー、セカンダリ キーが表示されます。 キーは保護する必要があります。 キーはサーバー コードでのみ使用してください。 クライアント側 (.js、.html など) では使用できないようにします。

    ![サインアップ](./media/video-indexer-use-apis/video-indexer-api03.png)

## <a name="obtain-access-token-using-the-authorization-api"></a>Authorization API を使用してアクセス トークンを取得する

Authorization API にサブスクライブすると、アクセス トークンを取得できるようになります。 これらのアクセス トークンは、Operations API の認証を受けるために使用されます。 

Operations API の各呼び出しは、呼び出しの承認スコープと一致するアクセス トークンに関連付けられている必要があります。

- ユーザー レベル - ユーザー レベルのアクセス トークンを使用すると、**ユーザー** レベルに対して操作を実行できます。 たとえば、関連付けられたアカウントの取得などです。
- アカウント レベル - アカウント レベルのアクセス トークンを使用すると、**アカウント** レベルまたは**ビデオ** レベルに対して操作を実行できます。 たとえば、動画のアップロード、すべてのビデオの一覧表示、動画の分析情報の取得などです。
- ビデオ レベル - ビデオ レベルのアクセス トークンを使用すると、特定の**ビデオ**に対して操作を実行できます。 たとえば、ビデオの分析情報、キャプションのダウンロード、ウィジェットの取得などです。 

**allowEdit=true/false** を指定することで、これらのトークンを読み取り専用にするか編集を許可するかを制御できます。

**アカウント**操作と**ビデオ**操作の両方がカバーされるので、ほとんどのサーバー間シナリオにおそらく同じ**アカウント** トークンを使用します。 ただし、クライアント側 (JavaScript など) から Video Indexer を呼び出す予定がある場合は、クライアントがアカウント全体へのアクセス権を取得できないように、**ビデオ** アクセス トークンを使用することもあります。 また、(たとえば、**Get Insights ウィジェット**または **Get Player ウィジェット**を使用して) VideoIndexer クライアント コードをクライアントに埋め込む場合にも、**ビデオ** アクセス トークンを指定する必要があります。

わかりやすくするために、ユーザー トークンを最初に取得せずに、**Authorization** API の **GetAccounts** を使用してアカウントを取得することができます。 有効なトークンを持つアカウントの取得を要求することもできます。これで、アカウント トークンを取得する追加の呼び出しをスキップすることができます。

アクセス トークンの有効期間は 1 時間です。 Operations API を使用する前に、アクセス トークンが有効であることを確認してください。 期限切れの場合は、もう一度 Authorization API を呼び出して新しいアクセス トークンを取得します。
 
これで API との統合を開始する準備が整いました。 [各 Video Indexer REST API の詳細な説明](http://api-portal.videoindexer.ai/)を参照してください。

## <a name="location"></a>Location

すべての操作 API には Location パラメーターが必要です。このパラメーターは、呼び出しがルーティングされ、アカウントが作成されたリージョンを示します。

次の表で説明する値が適用されます。 **パラメーター値**は、API を使用するときに渡す値です。

|**名前**|**パラメーター値**|**説明**|
|---|---|---|
|試用版|試用|試用アカウントに使用されます。|
|米国西部|westus2|Azure 米国西部 2 リージョンに使用されます。|
|北ヨーロッパ |northeurope|Azure 北ヨーロッパ リージョンに使用されます。|
|東アジア|eastasia|Azure 東アジア リージョンに使用されます。|

## <a name="account-id"></a>Account ID 

アカウント ID パラメーターは、すべての操作 API 呼び出しに必要です。 アカウント ID は、次のいずれかの方法で取得できる GUID です。

* Video Indexer ポータルを使用して次の手順でアカウント ID を取得します。

    1. [videoindexer](https://www.videoindexer.ai/) にサインインします。
    2. **[設定]** ページを表示します。
    3. アカウント ID をコピーします。

        ![Account ID](./media/video-indexer-use-apis/account-id.png)

* API を使用してプログラムでアカウント ID を取得します。

    [Get accounts](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Accounts?) API を使用します。
    
    > [!TIP]
    > `generateAccessTokens=true` を定義することで、アカウントのアクセス トークンを生成できます。
    
* アカウントのプレーヤー ページの URL からアカウント ID を取得します。

    ビデオを見ると、`accounts` セクションの後と `videos` セクションの前に ID が表示されます。

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Recommendations

このセクションでは、Video Indexer API を使用する際の推奨事項をいくつか示します。

- ビデオをアップロードする予定がある場合は、ファイルをパブリック ネットワークの場所 (OneDrive など) に配置することをお勧めします。 ビデオのリンクを取得し、アップロード ファイルのパラメーターとしてその URL を指定します。 

    Video Indexer に指定する URL は、メディア (オーディオまたはビデオ) ファイルを指している必要があります。 OneDrive で生成されるリンクは、ファイルを含む HTML ページの場合があります。 URL をブラウザーに貼り付けると、簡単に確認できます。ファイルのダウンロードが開始された場合は、適切な URL である可能性があります。 ブラウザーに何かが描画される場合は、ファイルへのリンクではなく HTML ページへのリンクである可能性があります。
    
- 指定されたビデオのビデオ分析情報を取得する API を呼び出すと、応答コンテンツとして詳細な JSON 出力を取得できます。 [返される JSON の詳細については、こちらのトピックを参照してください](video-indexer-output-json.md)。

## <a name="code-sample"></a>サンプル コード

次の C# コード スニペットは、すべての Video Indexer API の使用方法を示しています。

```csharp
var apiUrl = "https://api.videoindexer.ai";
var accountId = "..."; 
var location = "westus2";
var apiKey = "..."; 

System.Net.ServicePointManager.SecurityProtocol = System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

// create the http client
var handler = new HttpClientHandler(); 
handler.AllowAutoRedirect = false; 
var client = new HttpClient(handler);
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey); 

// obtain account access token
var accountAccessTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/AccessToken?allowEdit=true").Result;
var accountAccessToken = accountAccessTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// upload a video
var content = new MultipartFormDataContent();
Debug.WriteLine("Uploading...");
// get the video from URL
var videoUrl = "VIDEO_URL"; // replace with the video URL

// as an alternative to specifying video URL, you can upload a file.
// remove the videoUrl parameter from the query string below and add the following lines:
  //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
  //byte[] buffer =newbyte[video.Length];
  //video.Read(buffer, 0, buffer.Length);
  //content.Add(newByteArrayContent(buffer));

var uploadRequestResult = client.PostAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos?accessToken={accountAccessToken}&name=some_name&description=some_description&privacy=private&partition=some_partition&videoUrl={videoUrl}", content).Result;
var uploadResult = uploadRequestResult.Content.ReadAsStringAsync().Result;

// get the video id from the upload result
var videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
Debug.WriteLine("Uploaded");
Debug.WriteLine("Video ID: " + videoId);           

// obtain video access token            
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
var videoTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/Videos/{videoId}/AccessToken?allowEdit=true").Result;
var videoAccessToken = videoTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// wait for the video index to finish
while (true)
{
  Thread.Sleep(10000);

  var videoGetIndexRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/Index?accessToken={videoAccessToken}&language=English").Result;
  var videoGetIndexResult = videoGetIndexRequestResult.Content.ReadAsStringAsync().Result;

  var processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

  Debug.WriteLine("");
  Debug.WriteLine("State:");
  Debug.WriteLine(processingState);

  // job is finished
  if (processingState != "Uploaded" && processingState != "Processing")
  {
      Debug.WriteLine("");
      Debug.WriteLine("Full JSON:");
      Debug.WriteLine(videoGetIndexResult);
      break;
  }
}

// search for the video
var searchRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/Search?accessToken={accountAccessToken}&id={videoId}").Result;
var searchResult = searchRequestResult.Content.ReadAsStringAsync().Result;
Debug.WriteLine("");
Debug.WriteLine("Search:");
Debug.WriteLine(searchResult);

// get insights widget url
var insightsWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/InsightsWidget?accessToken={videoAccessToken}&widgetType=Keywords&allowEdit=true").Result;
var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
Debug.WriteLine("Insights Widget url:");
Debug.WriteLine(insightsWidgetLink);

// get player widget url
var playerWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/PlayerWidget?accessToken={videoAccessToken}").Result;
var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
Debug.WriteLine("");
Debug.WriteLine("Player Widget url:");
Debug.WriteLine(playerWidgetLink);

```

## <a name="next-steps"></a>次の手順

[出力 JSON の詳細を調べる](video-indexer-output-json.md)

## <a name="see-also"></a>関連項目

[Video Indexer の概要](video-indexer-overview.md)
