---
title: Azure Media Services を使用してビデオを分析する | Microsoft Docs
description: Azure Media Services を使用してビデオを分析するには、このチュートリアルの手順に従います。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/28/2018
ms.author: juliako
ms.openlocfilehash: 314ffce8a9f8dde62cac670099afbc2223df37e4
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972000"
---
# <a name="tutorial-analyze-videos-with-azure-media-services"></a>チュートリアル: Azure Media Services を使用してビデオを分析する 

このチュートリアルでは、Azure Media Services を使用したビデオの分析について説明します。 記録されたビデオまたはオーディオ コンテンツに関する詳細な分析情報を得る必要のある多くのシナリオがあります。 たとえば、より高い顧客満足度を実現するため、組織は音声テキスト変換処理を実行して、カスタマー サポートの記録をインデックスとダッシュボードを含む検索可能なカタログに変換できます。 その後、一般的なクレーム、そのようなクレームのソースの一覧など、ビジネスの分析情報を取得することができます。

このチュートリアルでは、次の操作方法について説明します。    

> [!div class="checklist"]
> * Media Services アカウントを作成する
> * Media Services API にアクセスする
> * サンプル アプリの構成
> * 指定されたビデオを分析するコードを調べる
> * アプリの実行
> * 出力を調べる
> * リソースのクリーンアップ

> [!Note]
> Azure portal を使用し、「[メディア処理のスケール設定の概要](../previous/media-services-scale-media-processing-overview.md)」の説明に従って、Media Services アカウントを 10 個の S3 メディア占有ユニットに設定します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

Visual Studio がインストールされていない場合は、[Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15) を入手できます。

## <a name="download-the-sample"></a>サンプルのダウンロード

次のコマンドを使って、.NET サンプルが含まれる GitHub リポジトリを、お使いのコンピューターに複製します。  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

サンプルは [AnalyzeVideos](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/AnalyzeVideos) フォルダーにあります。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-code-that-analyzes-the-specified-video"></a>指定されたビデオを分析するコードを調べる

このセクションでは、*AnalyzeVideos* プロジェクトの [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/Program.cs) ファイルで定義されている関数を調べます。

サンプルは、次のアクションを実行します。

1. ビデオを分析する変換とジョブを作成します。
2. 入力アセットを作成し、そこにビデオをアップロードします。 アセットは、ジョブの入力として使用されます。
3. ジョブの出力を格納する出力アセットを作成します。 
4. ジョブを送信します。
5. ジョブの状態を確認します。
6. ジョブの実行から生成されるファイルをダウンロードします。 

### <a name="start-using-media-services-apis-with-net-sdk"></a>.NET SDK で Media Services API の使用を開始する

.NET で Media Services API の使用を始めるには、**AzureMediaServicesClient** オブジェクトを作成する必要があります。 オブジェクトを作成するには、クライアントが Azure AD を使用して Azure に接続するために必要な資格情報を指定する必要があります。 この記事の最初に複製したコード内で、ローカル構成ファイルで指定された資格情報に基づいて、**GetCredentialsAsync** 関数が ServiceClientCredentials オブジェクトを作成します。 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>入力アセットを作成し、ローカル ファイルをそれにアップロードする 

**CreateInputAsset** 関数は、新しい入力[アセット](https://docs.microsoft.com/rest/api/media/assets)を作成し、指定されたローカル ビデオ ファイルをそこにアップロードします。 このアセットは、エンコード ジョブへの入力として使われます。 Media Services v3 では、ジョブへの入力としては、アセットを使うか、または HTTPS URL 経由で Media Services アカウントから使用できるようにされたコンテンツを使うことができます。 HTTPS URL からのエンコード方法について詳しくは、[こちら](job-input-from-http-how-to.md)の記事をご覧ください。  

Media Services v3 では、Azure Storage API を使ってファイルをアップロードします。 次の .NET スニペットはその方法を示したものです。

後で示す関数は、次の処理を実行します。

* アセットを作成する 
* 書き込み可能な [SAS URL](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) をアセットの[ストレージ内のコンテナー](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet?tabs=windows#upload-blobs-to-the-container)に取得する
* SAS URL を使用してストレージ内のコンテナーにファイルをアップロードする

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-the-job"></a>ジョブの結果を格納する出力アセットを作成する 

出力[アセット](https://docs.microsoft.com/rest/api/media/assets)には、ジョブの結果が格納されます。 プロジェクトで定義されている **DownloadResults** 関数は、この出力アセットから "output" フォルダーに結果をダウンロードするので、取得したものを確認できます。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-analyzes-videos"></a>ビデオを分析する変換とジョブを作成する

Media Services でコンテンツをエンコードまたは処理するときは、レシピとしてエンコード設定をセットアップするのが一般的なパターンです。 その後、**ジョブ**を送信してビデオにレシピを適用します。 新しいビデオごとに新しいジョブを送信することで、ライブラリ内のすべてのビデオにレシピを適用します。 Media Services でのレシピは**変換**と呼ばれます。 詳しくは、「[Transforms and jobs](transform-concept.md)」(変換とジョブ) をご覧ください。 このチュートリアルで説明されているサンプルでは、指定されたビデオを分析するレシピが定義されています。 

#### <a name="transform"></a>変換

新しい [Transform](https://docs.microsoft.com/rest/api/media/transforms) インスタンスを作成するときは、出力として生成するものを指定する必要があります。 必須のパラメーターは、下記のコードで示すように **TransformOutput** オブジェクトです。 各 **TransformOutput** には **Preset** が含まれます。 **Preset** では、目的の **TransformOutput** の生成に使用されるビデオやオーディオの処理操作の詳細な手順が記述されています。 この例では、**VideoAnalyzerPreset** プリセットが使われていて、言語 ("en-US") がコンストラクターに渡されています。 このプリセットを使用して、ビデオから複数の音声と画像の分析情報を抽出できます。 複数のオーディオ情報をビデオから抽出する必要がある場合は、**AudioAnalyzerPreset** プリセットを使用できます。 

**Transform** を作成するときは、次のコードに示すように、最初に **Get** メソッドを使って変換が既に存在するかどうかを確認する必要があります。  Media Services v3 では、エンティティが存在しない場合 (大文字と小文字の区別がない名前のチェック)、エンティティに対する **Get** メソッドは **null** を返します。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>ジョブ

上で説明したように、[Transform](https://docs.microsoft.com/rest/api/media/transforms) オブジェクトはレシピであり、[Job](https://docs.microsoft.com/rest/api/media/jobs) は **Transform** が特定の入力ビデオまたはオーディオ コンテンツに適用する Media Services への実際の要求です。 **Job** は、入力ビデオの場所や出力先などの情報を指定します。 ビデオの場所は、HTTPS URL、SAS URL、または Media Services アカウント内にあるアセットを使用して指定できます。 

この例では、ジョブの入力はローカル ビデオです。  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>ジョブが完了するのを待つ

ジョブの完了には時間がかかり、完了したら通知を受け取る必要があります。 [ジョブ](https://docs.microsoft.com/rest/api/media/jobs)完了の通知の取得にはさまざまなオプションがあります。 最も簡単なオプション (次に示すもの) は、ポーリングを使うものです。 

潜在的な待機時間により、ポーリングは運用アプリケーション用の推奨されるベスト プラクティスではありません。 アカウントで過剰に使った場合、ポーリングはスロットルされる可能性があります。 開発者は、代わりに Event Grid を使う必要があります。

Event Grid は、高可用性、一貫したパフォーマンス、および動的スケーリングを目的に設計されています。 Event Grid では、アプリはほぼすべての Azure サービスやカスタム ソースのイベントをリッスンし、対応できます。 単純な HTTP ベースのリアクティブ イベント ハンドリングでは、インテリジェントなイベント フィルタリングやイベント ルーティングを使用して、効率的なソリューションを構築できます。 [カスタム Web エンドポイントへのイベントのルーティング](job-state-events-cli-how-to.md)に関するページをご覧ください。

**Job** には通常、**Scheduled**、**Queued**、**Processing**、**Finished** (最終状態) という状態があります。 ジョブでエラーが発生すると、**Error** 状態を取得します。 ジョブがキャンセル処理中の場合は **Canceling** を受け取り、完了すると **Canceled** を受け取ります。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#WaitForJobToFinish)]

### <a name="download-the-result-of-the-job"></a>ジョブの結果をダウンロードする

次の関数は出力[アセット](https://docs.microsoft.com/rest/api/media/assets)から "output" フォルダーに結果をダウンロードするので、ジョブの結果を調べることができます。 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#DownloadResults)]

### <a name="clean-up-resource-in-your-media-services-account"></a>Media Services アカウント内のリソースをクリーンアップする

一般に、再利用を計画しているオブジェクトを除くすべてのものをクリーンアップする必要があります (通常、Transform は再利用し、StreamingLocator は保持します)。 実験後にアカウントをクリーンアップする場合は、再利用する予定がないリソースを削除する必要があります。 たとえば、次のコードはジョブを削除します。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>サンプル アプリを実行する

Ctrl + F5 キーを押して、*AnalyzeVideos* アプリケーションを実行します。

プログラムを実行すると、ジョブはビデオで検出された各顔のサムネイルを生成します。 insights.json ファイルも生成します。

## <a name="examine-the-output"></a>出力を調べる

ビデオ分析の出力ファイルは、insights.json という名前です。 このファイルには、ビデオに関する分析情報が含まれています。 json ファイルに含まれる要素の説明については、「[Media intelligence](intelligence-concept.md)」(メディア インテリジェンス) をご覧ください。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルで作成した Media Services アカウントとストレージ アカウントも含め、リソース グループ内のどのリソースも必要なくなった場合は、前に作成したリソース グループを削除します。 **CloudShell** ツールを使うことができます。

**CloudShell** で、次のコマンドを実行します。

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>マルチスレッド

Azure Media Services v3 SDK は、スレッドセーフではありません。 マルチスレッド アプリケーションで使うときは、スレッドごとに新しい AzureMediaServicesClient オブジェクトを生成する必要があります。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [チュートリアル: ファイルのアップロード、エンコード、ストリーム配信](stream-files-tutorial-with-api.md)
