---
title: Media Services リリース ノート | Microsoft Docs
description: Media Services リリース ノート
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 3ca2d7af-1cf0-45fa-9585-3b73f3ee057d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: media
ms.devlang: dotnet
ms.topic: article
ms.date: 07/24/2018
ms.author: juliako
ms.openlocfilehash: 220ff194ab5f8fa49ba7603ecd91122459d4dc1e
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2018
ms.locfileid: "39249225"
---
# <a name="azure-media-services-release-notes"></a>Azure Media Services リリース ノート
この Azure Media Services のリリース ノートには、以前のリリースからの変更と既知の問題が要約されています。

> [!NOTE]
> お客様に影響する問題の修正に尽力できるように、製品に関するご意見、ご要望をお寄せください。 問題の報告または質問を行うには、[Azure Media Services MSDN フォーラム]に投稿してください。
> 
> 

## <a name="a-idissuescurrently-known-issues"></a><a id="issues"/>現在の既知の問題
### <a name="a-idgeneralissuesmedia-services-general-issues"></a><a id="general_issues"/>Media Services の全般的な問題

| 問題 | 説明 |
| --- | --- |
| REST API で一般的な HTTP ヘッダーがいくつか提供されていない。 |REST API を使用して Media Services アプリケーションを開発している場合、いくつかの一般的な HTTP フィールド (CLIENT-REQUEST-ID、REQUEST-ID、および RETURN-CLIENT-REQUEST-ID を含む) がサポートされていないことに気付きます。 ヘッダーは、今後の更新プログラムで追加される予定です。 |
| パーセント エンコーディングが利用できない。 |Media Services は、ストリーミング コンテンツ (たとえば、http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters) の URL を構築する際に、IAssetFile.Name プロパティの値を使用します。 このため、パーセント エンコーディングは利用できません。 Name プロパティの値には、[パーセント エンコーディング予約文字](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters) (!*'();:@&=+$,/?%#[]") は使用できません。 また、ファイル名拡張子で使用できる "." は 1 つのみです。 |
| Azure Storage SDK Version 3.x に含まれる ListBlobs メソッドが失敗する。 |Media Services は、 [2012-02-12](https://docs.microsoft.com/rest/api/storageservices/Version-2012-02-12) バージョンに基づいて SAS URL を生成します。 Storage SDK を使用して、BLOB コンテナー内の BLOB を一覧する場合は、Storage SDK Version 2.x に含まれる [CloudBlobContainer.ListBlobs](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx) メソッドを使用してください。 |
| Media Services 調整メカニズムが、サービスに対して過剰な要求を作成するアプリケーションのリソース使用を制限する。 サービスが "サービスを利用できません" 503 HTTP 状態コードを返すことがある。 |詳細については、[Media Services エラー コード](media-services-encoding-error-codes.md)に関するページの 503 HTTP 状態コードの説明を参照してください。 |
| パブリック REST バージョン 2 では、クエリ結果が 1000 件に制限されているため、エンティティにクエリを実行すると、上限の 1000 個のエンティティが一度に返される。 |[こちらの .NET の例](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities)と[こちらの REST API の例](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities)に示すように、Skip および Take (.NET)/top (REST) を使用してください。 |
| 一部のクライアントは、スムーズ ストリーミング マニフェストで繰り返しタグに遭遇することがあります。 |詳細については、[こちらのセクション](media-services-deliver-content-overview.md#known-issues)をご覧ください。 |
| Media Services .NET SDK オブジェクトをシリアル化できないため、Azure Redis Cache と連携しない。 |SDK AssetCollection オブジェクトをシリアル化して、Azure Redis Cache に追加しようとすると、例外がスローされます。 |


## <a name="a-idrestversionhistoryrest-api-version-history"></a><a id="rest_version_history"/>REST API バージョン履歴
Media Services REST API バージョン履歴の詳細については、[Azure Media Services REST API リファレンス]をご覧ください。

## <a name="july-2018"></a>2018 年 7 月

最新のサービス リリースでは、ジョブが失敗したときにサービスが返すエラー メッセージに対して書式設定 (メッセージを 2 行以上に分割する方法) の軽微な変更を行いました。

## <a name="may-2018"></a>2018 年 5 月 

2018 年 5 月 12日以降は、ライブ チャネルで RTP/MPEG-2 トランスポート ストリーム取り込みプロトコルがサポートされなくなります。 RTP/MPEG-2 から RTMP またはフラグメント化 MP4 (Smooth Streaming) 取り込みプロトコルに移行してください。

## <a name="october-2017-release"></a>2017 年 10 月のリリース
> [!IMPORTANT] 
> Media Services では、Azure Access Control Service 認証キーのサポートが廃止されます。 2018 年 6 月 22 日以降、Access Control Service キーを使用して、コードによる Media Services バックエンドでの認証を行うことができなくなります。 [Azure Active Directory (Azure AD) ベースの認証](media-services-use-aad-auth-to-access-ams-api.md)で Azure AD を使用するようにコードを更新してください。 Azure Portal にも、この変更に関する警告が表示されます。

### <a name="updates-for-october-2017"></a>2017 年 10 月の更新
#### <a name="sdks"></a>SDK
* Azure AD 認証をサポートするために、.NET SDK が更新されました。 Azure AD への迅速な移行を促すために、Access Control Service 認証のサポートが、Nuget.org の最新の .NET SDK から削除されました。 
* Azure AD 認証をサポートするために、JAVA SDK が更新されました。 Azure AD 認証のサポートが、Java SDK に追加されました。 Media Services で Java SDK を使用する方法については、「[Azure Media Services 用 Java クライアント SDK の概要](media-services-java-how-to-use.md)」を参照してください

#### <a name="file-based-encoding"></a>ファイル ベースのエンコード
* Premium Encoder を使用して、H.265 High Efficiency Video Coding (HEVC) ビデオ コーデックにコンテンツをエンコードできるようになりました。 H.264 などの他のコーデックではなく、H.265 を選択しても料金への影響はありません。 HEVC 特許ライセンスについては、[オンライン サービス条件](https://azure.microsoft.com/support/legal/)に関するページをご覧ください。
* H.265 (HEVC) ビデオ コーデックでエンコードされたソース ビデオ (iOS11 や GoPro Hero 6 を使用してキャプチャされたビデオなど) については、Premium Encoder または Standard Encoder を使用して、そのビデオをエンコードできるようになりました。 特許ライセンスについては、[オンライン サービス条件](https://azure.microsoft.com/support/legal/)に関するページをご覧ください。
* 複数の言語のオーディオ トラックが含まれたコンテンツの場合、言語の値は、対応するファイル形式の仕様 (ISO MP4 など) に従って正しくラベル付けされている必要があります。 これにより、Standard Encoder を使用して、そのコンテンツをストリーミング用にエンコードできます。 作成されたストリーミング ロケーターには、使用可能なオーディオ言語の一覧が表示されます。
* Standard Encoder で、オーディオのみの新しいシステム プリセット、"AAC Audio" と "AAC Good Quality Audio" がサポートされるようになりました。 どちらもステレオ高品位オーディオ コーディング (AAC) 出力を生成し、ビットレートはそれぞれ 128 kbps と 192 kbps です。
* Premium Encoder で、入力として QuickTime/MOV ファイル形式がサポートされるようになりました。 ビデオ コーデックは、[こちらの GitHub 記事に記載された Apple ProRes タイプ](https://docs.microsoft.com/azure/media-services/media-services-media-encoder-standard-formats)のいずれかである必要があります。 オーディオは、AAC またはパルス符号変調 (PCM) のいずれかである必要があります。 Premium Encoder では、たとえば、QuickTime/MOV ファイルにラップされた DVC/DVCPro ビデオは入力としてサポートされません。 Standard Encoder は、こうしたビデオ コーデックをサポートしています。
* エンコーダーのバグ修正は次のとおりです。

    * 入力資産を使用して、ジョブを送信できるようになりました。 ジョブの完了後、資産を変更し (資産内でのファイルの追加、削除、名前変更など)、追加のジョブを送信できます。
    * Standard Encoder によって生成される JPEG サムネイルの品質が向上しました。
    * Standard Encoder による、非常に短いビデオでの入力メタデータとサムネイル生成処理が向上しました。
    * Standard Encoder で使用される H.264 デコーダーが改善され、まれに生成される特定のアーティファクトが排除されます。 

#### <a name="media-analytics"></a>メディア分析
Azure Media Redactor の一般公開: このメディア プロセッサでは、選択された個人の顔を不鮮明にすることで、個人が特定されないようにします。これは治安の確保やニュース メディアのシナリオでの使用に適しています。 

この新しいプロセッサの概要については、[こちらのブログ記事](https://azure.microsoft.com/blog/azure-media-redactor/)をご覧ください。 ドキュメントと設定については、「[Azure Media Analytics で顔を編集する](media-services-face-redaction.md)」を参照してください。



## <a name="june-2017-release"></a>2017 年 6 月のリリース

Media Services では、[Azure AD ベースの認証](media-services-use-aad-auth-to-access-ams-api.md)がサポートされています。

> [!IMPORTANT]
> 現在 Media Services では、Access Control Service 認証モデルがサポートされています。 Access Control Service 承認は 2018 年 6 月 1 日に非推奨となる予定です。 できるだけ早く Azure AD 認証モデルに移行することをお勧めします。

## <a name="march-2017-release"></a>2017 年 3 月のリリース

Standard Encoder を使って、エンコード タスクを作成するときに "アダプティブ ストリーミング" プリセット文字列を指定することにより、[ビットレート ラダーを自動生成](media-services-autogen-bitrate-ladder-with-mes.md)できるようになりました。 Media Services でのストリーミング用にビデオをエンコードするには、"アダプティブ ストリーミング" プリセットを使用します。 特定のシナリオ向けにエンコード プリセットをカスタマイズするには、[このプリセット](media-services-mes-presets-overview.md)で始めることができます。

Media Encoder Standard または Media Encoder Premium Workflow を使って、[fMP4 チャンクを生成するエンコード タスクを作成](media-services-generate-fmp4-chunks.md)できるようになりました。 

## <a name="february-2017-release"></a>2017 年 2 月のリリース

2017 年 4 月 1 日以降、アカウント内の 90 日前より古いジョブ レコードすべてが、関連付けられているタスク レコードと共に自動的に削除されます。 この削除は、レコードの合計数が、最大クォータより小さい場合でも実行されます。 ジョブ/タスクの情報をアーカイブするには、「[Media Services .NET SDK を使用するアセットと関連エンティティの管理](media-services-dotnet-manage-entities.md)」で説明されているコードをご利用いただけます。

## <a name="january-2017-release"></a>2017 年 1 月のリリース

Media Services では、ストリーミング エンドポイントは、コンテンツをクライアント プレーヤー アプリケーションや、再配布のためのコンテンツ配信ネットワーク (CDN) に直接配信するストリーミング サービスを表します。 Media Services は、シームレスな Azure Content Delivery Network 統合もサポートしています。 StreamingEndpoint サービスからの送信ストリームには、ライブ ストリーム、ビデオ オンデマンド、または Media Services アカウントの資産のプログレッシブ ダウンロードを使用します。 各 Media Services アカウントには、既定のストリーミング エンドポイントが含まれています。 追加のストリーミング エンドポイントをアカウントで作成できます。 

ストリーミング エンドポイントには 1.0 と 2.0 の 2 つのバージョンがあります。 2017 年 1 月 10 日以降、新しく作成された Media Services アカウントには、バージョン 2.0 が既定のストリーミング エンドポイントとして含まれます。 このアカウントに追加する追加のストリーミング エンドポイントも、バージョン 2.0 になります。 この変更は、既存のアカウントには影響しません。 既存のストリーミング エンドポイントはバージョン 1.0 になり、バージョン 2.0 にアップグレードできます。 この変更により、動作、課金、および機能が変更されます。 詳しくは、「[ストリーミング エンドポイントの概要](media-services-streaming-endpoints-overview.md)」をご覧ください。

2.15 バージョン以降の Media Services では、ストリーミング エンドポイントのエンティティに次のプロパティが追加されました。

* CdnProvider 
* CdnProfile
* FreeTrialEndTime 
* StreamingEndpointVersion 

これらのプロパティの詳細については、「[StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint)」を参照してください。 

## <a name="december-2016-release"></a>2016 年 12 月のリリース

 Media Services を使用して、サービスのテレメトリ/メトリック データにアクセスできるようになりました。 現在のバージョンの Media Services を使用して、ライブ チャネル エンティティ、ストリーミング エンドポイント エンティ、およびアーカイブ エンティティのテレメトリ データを取得できます。 詳細については、[Media Services テレメトリ](media-services-telemetry-overview.md)に関するページをご覧ください。

## <a name="a-idjulychanges16july-2016-release"></a><a id="july_changes16"/>2016 年 7 月のリリース
### <a name="updates-to-the-manifest-file-ism-generated-by-encoding-tasks"></a>エンコード タスクによって生成されたマニフェスト ファイル (* ISM) への更新
エンコード タスクが Media Encoder Standard または Media Encoder Premium に送信されると、そのエンコード タスクによって、[ストリーミング マニフェスト ファイル](media-services-deliver-content-overview.md) (*.ism) が出力アセットに生成されます。 最新のサービス リリースでは、このストリーミング マニフェスト ファイルの構文が更新されました。

> [!NOTE]
> ストリーミング マニフェスト (.ism) ファイルの構文は、内部使用のため予約されています。 これは、今後のリリースで変更されることがあります。 このファイルのコンテンツは変更または操作しないでください。
> 
> 

### <a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>エンコード タスクが 1 つ以上の MP4 ファイルを出力すると、新しいクライアント マニフェスト (*.ISMC) ファイルが出力アセットに生成される
最新のサービス リリース以降、1 つ以上の MP4 ファイルを生成するエンコード タスクが完了すると、出力アセットにも、ストリーミング クライアント マニフェスト (*.ismc) ファイルが追加されます。 この .ismc ファイルは、動的ストリーミングのパフォーマンス向上に役立ちます。 

> [!NOTE]
> クライアント マニフェスト (.ismc) ファイルの構文は、内部使用のため予約されています。 これは、今後のリリースで変更されることがあります。 このファイルのコンテンツは変更または操作しないでください。
> 
> 

詳細については、 [このブログ](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/)をご覧ください。

### <a name="known-issues"></a>既知の問題
一部のクライアントは、スムーズ ストリーミング マニフェストで繰り返しタグに遭遇することがあります。 詳細については、[こちらのセクション](media-services-deliver-content-overview.md#known-issues)をご覧ください。

## <a id="apr_changes16"></a>2016 年 4 月のリリース
### <a name="media-analytics"></a>メディア分析
 Media Services に強力なビデオ インテリジェンスとして Media Analytics が導入されました。 詳細については、[Media Services Analytics の概要](media-services-analytics-overview.md)に関するページをご覧ください。

### <a name="apple-fairplay-preview"></a>Apple FairPlay (プレビュー)
Media Services を使用して、Apple FairPlay で HTTP ライブ ストリーミング (HLS) コンテンツを動的に暗号化できるようになりました。 また、Media Services ライセンス配信サービスを使用して、FairPlay ライセンスをクライアントに配信することもできます。 詳細については、「Azure Media Services を使用して Apple FairPlay で保護された HLS コンテンツをストリーミングする」を参照してください。

## <a id="feb_changes16"></a>2016 年 2 月のリリース
最新バージョンの Media Services SDK for .NET (3.5.3) には、Google Widevine 関連のバグ修正が含まれています。 Widevine で暗号化された複数の資産で AssetDeliveryPolicy を再利用できませんでした。 このバグ修正の一環として、WidevineBaseLicenseAcquisitionUrl プロパティが SDK に追加されました。

    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
    {
        {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},

    };

## <a id="jan_changes_16"></a>2016 年 1 月のリリース
エンコード予約ユニットは、エンコーダー名と混同しないように名前が変更されました。

Basic、Standard、および Premium エンコード予約ユニットの名前は、それぞれ S1、S2、および S3 予約ユニットに変更されました。 現在 Basic エンコード予約ユニットをご利用の場合は、Azure Portal (および請求書) に S1 というラベルが表示されます。 Standard および Premium をご利用の場合は、それぞれ S2、S3 というラベルが表示されます。 

## <a id="dec_changes_15"></a>2015 年 12 月のリリース

### <a name="media-encoder-deprecation-announcement"></a>Media Encoder 廃止のお知らせ

 Media Encoder は Media Encoder Standard のリリースから 12 か月ほど経過した時点で非推奨となる予定です。

### <a name="azure-sdk-for-php"></a>Azure SDK for PHP
Azure SDK チームは [Azure SDK for PHP](http://github.com/Azure/azure-sdk-for-php) パッケージの新しいリリースを公開しました。これには Media Services の更新プログラムと新機能が含まれています。 具体的には、Media Services SDK for PHP で、最新の[コンテンツ保護](media-services-content-protection-overview.md)機能がサポートされるようになりました。 つまり、AES と DRM (PlayReady と Widevine) による動的暗号化 (トークン制限あり/なし) 機能です。 また、[エンコード ユニット](media-services-dotnet-encoding-units.md)のスケーリングにも対応しています。

詳細については、次を参照してください。

* 次の[コード サンプル](http://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)は、すぐに使い始めるときに役立ちます。
  * **vodworkflow_aes.php**: この PHP ファイルは、AES-128 動的暗号化とキー配信サービスの使用方法を示します。 これは、「[AES-128 動的暗号化とキー配信サービスの使用](media-services-protect-with-aes128.md)」で説明されている .NET サンプルに基づきます。
  * **vodworkflow_aes.php**: この PHP ファイルは、PlayReady 動的暗号化とライセンス配信サービスの使用方法を示します。 これは、「[PlayReady または Widevine の動的共通暗号化を使用する](media-services-protect-with-playready-widevine.md)」で説明されている .NET サンプルに基づきます。
  * **scale_encoding_units.php**: この PHP ファイルは、エンコード予約ユニットのスケーリング方法を示します。

## <a id="nov_changes_15"></a>2015 年 11 月のリリース
 Media Services が、クラウドで Widevine ライセンス配信サービスを提供するようになりました。 詳細については、 [このブログ](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/)をご覧ください。 また、[こちらのチュートリアル](media-services-protect-with-playready-widevine.md)および [GitHub リポジトリ](http://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm)もご覧ください。 

Media Services によって提供される Widevine ライセンス配信サービスはプレビュー期間中です。 詳細については、 [このブログ](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/)をご覧ください。

## <a id="oct_changes_15"></a>2015 年 10 月のリリース
Media Services は、現在、ブラジル南部、インド西部、インド南部、およびインド中部のデータ センターで利用可能です。 Azure Portal を使用して [Media Services アカウントを作成](media-services-portal-create-account.md)し、「[Media Services のドキュメント](https://azure.microsoft.com/documentation/services/media-services/)」Web ページで示すさまざまなタスクを実行できるようになりました。 こうしたデータ センターでは Live Encoding は有効ではありません。 また、すべての種類のエンコード予約ユニットが、このようなデータ センターで使用できるわけではありません。

* ブラジル南部: Standard および Basic エンコード予約ユニットのみ使用可能。
* インド西部、インド南部、およびインド中部: Basic エンコード予約ユニットのみ使用可能。

## <a id="september_changes_15"></a>2015 年 9 月のリリース
Media Services で、Widevine Modular DRM テクノロジを使用してビデオ オン デマンドとライブ ストリームの両方を保護できるようになりました。 次の配信サービス パートナーを通して Widevine ライセンスを提供できます。
* [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/) 
* [EZDRM](http://ezdrm.com/) 
* [castLabs](http://castlabs.com/company/partners/azure/) 

詳細については、 [このブログ](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)をご覧ください。
  
[Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (バージョン 3.5.1 以降) または REST API を使用して、Widevine を使用するように AssetDeliveryConfiguration を構成できます。 
* Media Services に、Apple ProRes ビデオのサポートが追加されました。 Apple ProRes やその他のコーデックを使用する QuickTime ソース ビデオをアップロードできるようになりました。 詳細については、 [このブログ](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/)をご覧ください。
* Media Encoder Standard を使用して、サブクリップとライブ アーカイブ抽出を実行できるようになりました。 詳細については、 [このブログ](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/)をご覧ください。
* 次のフィルター処理の更新が行われました。 
  
  * オーディオ専用フィルター付きの Apple HLS 形式を使用できるようになりました。 この更新を使用して、(audio-only=false) を URL に指定することで、オーディオ専用トラックを削除できます。
  * アセット用のフィルターを定義するときに、複数のフィルター (最大 3 つ) を 1 つの URL で組み合わせることができます。
    
    詳細については、 [このブログ](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)をご覧ください。
* Media Services で HLS バージョン 4 の I フレームをサポートするようになりました。 I フレームのサポートにより、早送りと巻き戻しの操作が最適化されます。 既定では、すべての HLS バージョン 4 出力に、I フレームの再生リスト (EXT-X-I-FRAME-STREAM-INF) が含まれます。
詳細については、 [このブログ](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)をご覧ください。

## <a id="august_changes_15"></a>2015 年 8 月のリリース
* Media Services SDK for Java バージョン 0.8.0 リリースと新しいサンプルを利用できるようになりました。 詳細については、次を参照してください。
    
* Azure Media Player が複数のオーディオ ストリームに対応するよう更新されました。 詳細については、 [このブログの投稿](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/)を参照してください。

## <a id="july_changes_15"></a>2015 年 7 月のリリース
* Media Encoder Standard の一般公開が発表されました。 詳細については、 [このブログの投稿](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)を参照してください。
  
    [こちらのセクション](http://go.microsoft.com/fwlink/?LinkId=618336)で説明されているように、Media Encoder Standard ではプリセットを使用しています。 4K エンコードのプリセットを使用する場合は、Premium の予約ユニットを取得する必要があります。 詳細については、[エンコードのスケール](media-services-scale-media-processing-overview.md)に関するページをご覧ください。
* ライブ リアルタイム キャプションが、Media Services と Media Player と共に使用されました。 詳細については、 [このブログの投稿](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/)を参照してください。

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK の更新
Media Services .NET SDK が 3.4.0.0 にバージョン アップしました。 次の更新が行われました。 

* ライブ アーカイブのサポートが実装されました。 ライブ アーカイブを含む資産はダウンロードできません。
* 動的フィルターのサポートが実装されました。
* ユーザーがストレージ コンテナーを維持しながら資産を削除できる機能が実装されました。
* チャネルの再試行ポリシーに関連するバグが修正されました。
* Media Encoder Premium Workflow が有効になりました。

## <a id="june_changes_15"></a>2015 年 6 月のリリース
### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK の更新
Media Services .NET SDK が 3.3.0.0 にバージョン アップしました。 次の更新が行われました。 

* OpenID Connect 検出仕様のサポートが追加されました。
* ID プロバイダー側でのキーのロールオーバー処理のサポートが追加されました。

(Azure AD、Google、および Salesforce のように) OpenID Connect 検出ドキュメントを公開する ID プロバイダーを使用する場合は、OpenID Connect 検出仕様から JSON Web トークン (JWT) を検証するための署名キーを取得するよう Media Services に指示できます。 

詳細については、[Media Services での OpenID Connect 検出仕様の JSON Web キーを使用した JWT 認証の操作](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/)に関するページをご覧ください。

## <a id="may_changes_15"></a>2015 年 5 月のリリース
次の新しい機能が発表されました。

* [Media Services によるライブ エンコードのプレビュー](media-services-manage-live-encoder-enabled-channels.md)
* [動的マニフェスト](media-services-dynamic-manifest-overview.md)
* [Azure Media Hyperlapse メディア プロセッサのプレビュー](https://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)

## <a id="april_changes_15"></a>2015 年 4 月のリリース
### <a name="general-media-services-updates"></a>Media Services の全般的な更新
* [Media Player](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/) が発表されました。
* Media Services REST 2.10 以降、RTMP (Real-Time Message Protocol) を取り込むように構成されたチャネルが、プライマリとセカンダリの取り込み URL を使用して作成されます。 詳細については、[チャネル取り込みの構成](media-services-live-streaming-with-onprem-encoders.md#channel_input)に関するページをご覧ください。
* Azure Media Indexer が更新されました。
* スペイン語のサポートが追加されました。
* XML 形式の新しい構成が追加されました。

詳細については、 [このブログ](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/)をご覧ください。

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK の更新
Media Services .NET SDK が 3.2.0.0 にバージョン アップしました。 次の更新が行われました。

* 重大な変更: TokenRestrictionTemplate.Issuer と TokenRestrictionTemplate.Audience が文字列型に変更されました。
* カスタム再試行ポリシーの作成に関する更新が行われました。
* ファイルのアップロードおよびダウンロードに関連するバグが修正されました。
* MediaServicesCredentials クラスが、認証するためのプライマリとセカンダリのアクセス制御エンドポイントを受け入れるようになりました。

## <a id="march_changes_15"></a>2015 年 3 月のリリース
### <a name="general-media-services-updates"></a>Media Services の全般的な更新
* Media Services が、Content Delivery Network 統合を提供するようになりました。 統合をサポートするために、CdnEnabled プロパティが StreamingEndpoint に追加されました。 CdnEnabled はバージョン 2.9 以降の REST API でご利用いただけます。 詳細については、「 [StreamingEndpoint」](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint)をご覧ください。 CdnEnabled はバージョン 3.1.0.2 以降の .NET SDK でご利用いただけます。 詳細については、「 [StreamingEndpoint」](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint\(v=azure.10\).aspx)をご覧ください。
* Media Encoder Premium Workflow が発表されました。 詳細については、「[Introducing Premium encoding in Azure Media Services (Azure Media Services への Premium エンコードの導入)](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)」を参照してください。

## <a id="february_changes_15"></a>2015 年 2 月のリリース
### <a name="general-media-services-updates"></a>Media Services の全般的な更新
Media Services REST API は、現在、バージョン 2.9 です。 このバージョン以降、Content Delivery Network をストリーミング エンドポイントと統合できます。 詳細については、「 [StreamingEndpoint」](https://msdn.microsoft.com/library/dn783468.aspx)をご覧ください。

## <a id="january_changes_15"></a>2015 年 1 月のリリース
### <a name="general-media-services-updates"></a>Media Services の全般的な更新
動的な暗号化による Content Protection の一般公開が発表されました。 詳細については、[Media Services での DRM テクノロジの一般公開によるストリーミング セキュリティの強化](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/)に関するページをご覧ください。

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK の更新
Media Services .NET SDK が 3.1.0.1 にバージョン アップしました。

このリリースでは、既定の Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate コンストラクターが旧式としてマークされています。 新しいコンス トラクターは、TokenType を引数として受け取ります。

    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


## <a id="december_changes_14"></a>2014 年 12 月のリリース
### <a name="general-media-services-updates"></a>Media Services の全般的な更新
* Media Indexer に更新と新機能がいくつか追加されました。 詳細については、「[Azure Media Indexer version 1.1.6.7 release notes (Azure Media Indexer バージョン 1.1.6.7 のリリース ノート)](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/)」を参照してください。
* エンコード予約ユニットの更新に使用できる新しい REST API が追加されました。 詳細については、[EncodingReservedUnitType と REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype) に関するページをご覧ください。
* キー配信サービスの CORS サポートが追加されました。
* 承認ポリシーへのクエリ オプションのパフォーマンスが向上しました。
* 中国のデータ センターで、[キー配信 URL](https://docs.microsoft.com/rest/api/media/operations/contentkey#get_delivery_service_url) が顧客単位となりました (他のデータ センターと同じです)。
* HLS 自動ターゲット期間が追加されました。 ライブ ストリーミングの実行中、HLS は常に動的にパッケージ化されます。 既定では、HLS セグメントのパッケージ率 (FragmentsPerSegment) は、キーフレーム間隔 (KeyFrameInterval) に基づいて Media Services によって自動計算されます。 この方法は、ライブ エンコーダーから受信する画像グループ (GOP) とも呼ばれます。 詳細については、「[Azure Media Services を使用したライブ ストリーミングの概要](http://msdn.microsoft.com/library/azure/dn783466.aspx)」を参照してください。

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK の更新
[Media Services .NET SDK](http://www.nuget.org/packages/windowsazure.mediaservices/) が 3.1.0.0 にバージョン アップしました。 次の更新が行われました。

* .NET SDK の依存関係が .NET 4.5 Framework にアップグレードされました。
* エンコード予約ユニットの更新に使用できる新しい API が追加されました。 詳細については、[.NET を使用した予約ユニットの種類の更新とエンコード予約ユニットの拡張](media-services-dotnet-encoding-units.md)に関するページをご覧ください。
* トークン認証に JWT サポートが追加されました。 詳細については、[Media Services と動的暗号化での JWT トークン認証](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)に関するページをご覧ください。
* PlayReady ライセンス テンプレートに BeginDate と ExpirationDate の相対オフセットが追加されました。

## <a id="november_changes_14"></a>2014 年 11 月のリリース
* Media Services を使用して、SSL 接続経由で Live Smooth Streaming (fMP4) コンテンツを取り込むことができます。 SSL 経由で取り込むには、取り込み URL を HTTPS に更新する必要があります。 現在のところ、Media Services ではカスタム ドメインを使用した SSL はサポートされていません。 ライブ ストリーミングの詳細については、[Azure Media Services ライブ ストリーミングの操作](http://msdn.microsoft.com/library/azure/dn783466.aspx)に関するページをご覧ください。
* RTMP ライブ ストリームについては、現在、SSL 接続経由で取り込むことはできません。
* SSL 経由でのストリーミングを実行できるのは、コンテンツの配信元となるストリーミング エンドポイントが 2014 年 9 月 10 日より後に作成されている場合のみです。 ストリーミング URL の基になるストリーミング エンドポイントの作成日が 2014 年 9 月 10 日より後である場合は、URL に "streaming.mediaservices.windows.net" (新形式) が含まれています。 "origin.mediaservices.windows.net" (旧形式) を含むストリーミング URL では、SSL がサポートされません。 URL が旧形式である場合、SSL トリーミングに対応するには、[新しいストリーミング エンドポイントを作成](media-services-portal-manage-streaming-endpoints.md)してください。 SSL でコンテンツをストリーミングするには、新しいストリーミング エンドポイントに基づいた URL を使用します。

## <a id="october_changes_14"></a>2014 年 10 月のリリース
### <a id="new_encoder_release"></a>Media Services Encoder のリリース
 Media Services Azure Media Encoder の新しいリリースが発表されました。 最新の Media Encoder では、出力 GB についてのみ課金されます。 それ以外は、新しいエンコーダーの機能は前のエンコーダーと互換性があります。 詳細については、[Media Services の価格詳細]に関するページをご覧ください。

### <a id="oct_sdk"></a>Media Services .NET SDK
Media Services SDK for .NET 拡張機能は、現在、バージョン 2.0.0.3 です。

Media Services SDK for .NET は、現在、バージョン 3.0.0.8 です。 次の更新が行われました。

* 再試行ポリシーのクラスでリファクタリングが実装されました。
* ユーザー エージェント文字列が HTTP 要求ヘッダーに追加されました。
* NuGet のビルド復元ステップが追加されました。
* リポジトリからの x509 証明書を使用するようにシナリオ テストが修正されました。
* チャンネルとストリーミング エンドを更新するときの検証設定が追加されました。

### <a name="new-github-repository-to-host-media-services-samples"></a>Media Services のサンプルをホストする新しい GitHub リポジトリ
サンプルは、[Media Services のサンプル GitHub リポジトリ](https://github.com/Azure/Azure-Media-Services-Samples)にあります。

## <a id="september_changes_14"></a>2014 年 9 月のリリース
Media Services REST メタデータは、現在、バージョン 2.7 です。 最新の REST 更新プログラムの詳細については、[Media Services REST API リファレンス](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)をご覧ください。

Media Services SDK for .NET は、現在、バージョン 3.0.0.7 です

### <a id="sept_14_breaking_changes"></a>重大な変更
* オリジンの名前が [StreamingEndpoint] に変更されました。
* Azure Portal を使用して MP4 ファイルをエンコードし、その後、発行する際の既定の動作が変更されました。

### <a id="sept_14_GA_changes"></a>一般公開リリースに含まれる新機能/シナリオ
* Media Indexer メディア プロセッサが導入されました。 詳細については、[Media Indexer によるメディア ファイルのインデックス作成](http://msdn.microsoft.com/library/azure/dn783455.aspx)に関するページをご覧ください。
* [StreamingEndpoint] エンティティを使用して、カスタム ドメイン (ホスト) 名を追加できます。
  
    Media Services ストリーミング エンドポイント名としてカスタム ドメイン名を使用するには、カスタム ホスト名をストリーミング エンドポイントに追加します。 Media Services REST API または .NET SDK を使用して、カスタム ホスト名を追加します。
  
    次の考慮事項が適用されます。
  
  * カスタム ドメイン名の所有権を保持している必要があります。
  * ドメイン名の所有権は、Media Services によって検証する必要があります。 ドメインを検証するには、MediaServicesAccountId 親ドメインをマップする CName を作成して、DNS mediaservices-dns-zone を検証します。
  * カスタム ホスト名 (たとえば、sports.contoso.com) を Media Services StreamingEndpoint ホスト名 (たとえば、amstest.streaming.mediaservices.windows.net) にマップする別の CName を作成する必要があります。

    詳細については、[StreamingEndpoint](http://msdn.microsoft.com/library/azure/dn783468.aspx) に関する記事の CustomHostNames プロパティを参照してください。

### <a id="sept_14_preview_changes"></a>パブリック プレビュー リリースに含まれる新機能/シナリオ
* ライブ ストリーミングのプレビュー。 詳細については、「[Azure Media Services を使用したライブ ストリーミングの概要](http://msdn.microsoft.com/library/azure/dn783466.aspx)」を参照してください。
* キー配信サービス。 詳細については、「[AES-128 動的暗号化とキー配信サービスの使用](http://msdn.microsoft.com/library/azure/dn783457.aspx)」を参照してください。
* AES 動的暗号化。 詳細については、「[AES-128 動的暗号化とキー配信サービスの使用](http://msdn.microsoft.com/library/azure/dn783457.aspx)」を参照してください。
* PlayReady ライセンス配信サービス。 
* PlayReady 動的暗号化。 
* Media Services PlayReady ライセンス テンプレート。 詳細については、「[Media Services PlayReady ライセンス テンプレートの概要]」を参照してください。
* ストレージ暗号化資産のストリーミング。 詳細については、[ストレージ暗号化コンテンツのストリーミング](http://msdn.microsoft.com/library/azure/dn783451.aspx)に関するページをご覧ください。

## <a id="august_changes_14"></a>2014 年 8 月のリリース
資産をエンコードすると、エンコード ジョブの完了時に出力資産が生成されます。 このリリースまでは、Media Services エンコーダーは、出力資産に関するメタデータを生成していました。 このリリース以降、エンコーダーは入力資産に関するメタデータも生成します。 詳細については、「[入力メタデータ]」および「[出力メタデータ]」を参照してください。

## <a id="july_changes_14"></a>2014 年 7 月のリリース
Azure Media Services パッケージおよび暗号化機能で次のバグが修正されました。

* ライブ アーカイブ資産を HLS に変換すると、オーディオしか再生されない: この問題は修正され、現在オーディオとビデオの両方を再生できます。
* 資産が HLS および AES 128 ビット エンベロープ暗号化にパッケージされていると、パッケージ化されたストリームが Android デバイスで再生されない: このバグは修正され、パッケージ化されたストリームは、HLS をサポートする Android デバイスで再生されます。

## <a id="may_changes_14"></a>2014 年 5 月のリリース
### <a id="may_14_changes"></a>Media Services の全般的な更新
[ダイナミック パッケージ]を使用して、HLS バージョン 3 をストリーミングできるようになりました。 HLS バージョン 3 をストリーミングするには、*.ism/manifest(format=m3u8-aapl-v3) をオリジン ロケーター パスに追加します。 詳細については、[このフォーラム](https://social.msdn.microsoft.com/Forums/en-US/13b8a776-9519-4145-b9ed-d2b632861fde/dynamic-packaging-to-hls-v3)を参照してください。

動的パッケージが、PlayReady による Smooth Streaming 静的暗号化に基づく、PlayReady による HLS (バージョン 3 と バージョン 4) 暗号化の配信もサポートするようになりました。 PlayReady による Smooth Streaming の暗号化方法の詳細については、[PlayReady による Smooth Streaming の保護](http://msdn.microsoft.com/library/azure/dn189154.aspx)に関するページをご覧ください。

### <a name="may_14_donnet_changes"></a>Media Services .NET SDK の更新
Media Services .NET SDK が 3.0.0.5 にバージョン アップしました。 次の更新が行われました。

* メディア資産のアップロードおよびダウンロードが迅速になり、復元性が向上しました。
* 再試行ロジックと一時的な例外の処理が改善されました。 
  
  * ファイルのクエリ、保存、変更、アップロード、またはダウンロード時に発生する例外に関する一時的なエラーの検出と再試行ロジックが改善されました。 
  * Web の例外が返されるとき (たとえば、Access Control Service トークンの要求時)、致命的なエラーが以前より早く失敗するようになりました。

詳細については、「[Media Services SDK for .NET の再試行ロジック]」を参照してください。

## <a id="april_changes_14"></a>2014 年 4 月の Encoder のリリース
### <a name="april_14_enocer_changes"></a>Media Services Encoder の更新
* Grass Valley EDIUS 非線形エディターを使用して作成された AVI ファイルを取り込むことができるようになりました。 このプロセスでは、ビデオは、Grass Valley HQ/HQX コーデックを使用してすばやく圧縮されます。 詳細については、「[Grass Valley announces EDIUS 7 streaming through the cloud (Grass Valley 社、クラウドを介した EDIUS 7 ストリーミングを発表)]」を参照してください。
*  Media Services Encoder によって生成されたファイルの名前付け規則を指定できるようになりました。 詳細については、[Media Services Encoder 出力ファイル名の制御](http://msdn.microsoft.com/library/azure/dn303341.aspx)に関するページをご覧ください。
*  ビデオ オーバーレイおよびオーディオ オーバーレイのサポートが追加されました。 詳細については、[オーバーレイの作成](http://msdn.microsoft.com/library/azure/dn640496.aspx)に関するページをご覧ください。
*  複数のビデオ セグメントを結合できるようになりました。 詳細については、[ビデオ セグメントの結合](http://msdn.microsoft.com/library/azure/dn640504.aspx)に関するページをご覧ください。
* オーディオが MPEG-1 Audio Layer 3 (MP3) でエンコードされるという、MP4 のコード変換に関するバグが修正されました。

## <a id="jan_feb_changes_14"></a>2014 年 1 月と 2 月のリリース
### <a name="jan_fab_14_donnet_changes"></a>Media Services .NET SDK 3.0.0.1、3.0.0.2、および 3.0.0.3
3.0.0.1 および 3.0.0.2 における変更:

* OrderBy ステートメントでの LINQ クエリの使用に関する問題が修正されました。
* [GitHub] のテスト ソリューションが、ユニットベースのテストとシナリオベースのテストに分割されました。

変更の詳細については、[Media Services .NET SDK 3.0.0.1 および 3.0.0.2 のリリース](http://gtrifonov.com/2014/02/07/windows-azure-media-services-net-sdk-3-0-0-2-release/index.html)に関するページをご覧ください。

バージョン 3.0.0.3 では次の点が変更されました。

* バージョン 3.0.3.0 を使用するように Azure Storage の依存関係がアップグレードされました。
* 3.0.*.* リリースの下位互換性の問題が修正されました。

## <a id="december_changes_13"></a>2013 年 12 月のリリース
### <a name="dec_13_donnet_changes"></a>Media Services .NET SDK 3.0.0.0
> [!NOTE]
> 3.0.x.x リリースには、2.4.x.x リリースとの下位互換性がありません。
> 
> 

現在、Media Services SDK の最新バージョンは 3.0.0.0 です。 NuGet から最新パッケージをダウンロードするか、[GitHub] からビットを取得できます。

Media Services SDK バージョン 3.0.0.0 以降、[Azure AD Access Control Service](http://msdn.microsoft.com/library/hh147631.aspx) トークンを再利用できます。 詳細については、[Media Services SDK for .NET での Media Services への接続](http://msdn.microsoft.com/library/azure/jj129571.aspx)に関するページの Access Control Service トークンの再利用に関するセクションをご覧ください。

### <a name="dec_13_donnet_ext_changes"></a>Media Services .NET SDK 拡張機能 2.0.0.0
 Media Services .NET SDK 拡張機能は、コードを簡素化し、Media Services による開発を容易にする一連の拡張メソッドとヘルパー機能です。 [Media Services .NET SDK 拡張機能](https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev)から最新のビットを取得できます。

## <a id="november_changes_13"></a>2013 年 11 月のリリース
### <a name="nov_13_donnet_changes"></a>Media Services .NET SDK の変更点
このバージョン以降、Media Services SDK for .NET は、Media Services REST API レイヤーへの呼び出しが行われるときに発生する可能性がある、一時的な障害エラーを処理します。

## <a id="august_changes_13"></a>2013 年 8 月のリリース
### <a name="aug_13_powershell_changes"></a>Azure SDK ツールに含まれる Media Services PowerShell コマンドレット
次の Media Services PowerShell コマンドレットが、[Azure SDK ツール](https://github.com/Azure/azure-sdk-tools)に追加されました。

* Get-AzureMediaServices 

    次に例を示します。`Get-AzureMediaServicesAccount`
* New-AzureMediaServicesAccount 
  
    次に例を示します。`New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`
* New-AzureMediaServicesKey 
  
    次に例を示します。`New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`
* Remove-AzureMediaServicesAccount 
  
    次に例を示します。`Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`

## <a id="june_changes_13"></a>2013 年 6 月のリリース
### <a name="june_13_general_changes"></a>Media Services の変更点
ここで説明する次の変更点は、2013 年 6 月の Media Services リリースに含まれている更新内容です。

* 複数のストレージ アカウントを Media Service アカウントにリンクする機能。 
    * StorageAccount
    * Asset.StorageAccountName および Asset.StorageAccount
* Job.Priority を更新する機能。 
* 通知関連のエンティティとプロパティ: 
    * JobNotificationSubscription
    * NotificationEndPoint
    * ジョブ
* Asset.Uri 
* Locator.Name 

### <a name="june_13_dotnet_changes"></a>Media Services .NET SDK の変更点
2013 年 6 月の Media Services SDK リリースには、次の変更が追加されました。 最新の Media Services SDK は、GitHub から入手可能です。

* Media Services SDK Version 2.3.0.0 以降、複数のストレージ アカウントを 1 つの Media Services アカウントにリンクできます。 次の API がこの機能をサポートしています。
  
    * IStorageAccount 型
    * Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts プロパティ
    * StorageAccount プロパティ
    * StorageAccountName プロパティ
  
    詳細については、「[複数のストレージ アカウントでの Media Services 資産の管理](http://msdn.microsoft.com/library/azure/dn271889.aspx)」を参照してください。
* 通知関連の API。 バージョン 2.2.0.0 以降、Azure Queue Storage 通知をリッスンできます。 詳細については、[Media Services ジョブ通知の処理](http://msdn.microsoft.com/library/azure/dn261241.aspx)に関するページをご覧ください。
  
    * Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions プロパティ
    * Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint 型
    * Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription 型
    * Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection 型
    * Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType 型
* Storage クライアント SDK 2.0 への依存関係 (Microsoft.WindowsAzure.StorageClient.dll)
* OData 5.5 への依存関係 (Microsoft.Data.OData.dll)

## <a id="december_changes_12"></a>2012 年 12 月のリリース
### <a name="dec_12_dotnet_changes"></a>Media Services .NET SDK の変更点
* IntelliSense: 多くの型で不足していた IntelliSense ドキュメントが追加されました。
* Microsoft.Practices.TransientFaultHandling.Core: SDK がこのアセンブリの旧バージョンにまだ依存していた問題が修正されました。 現在、SDK はこのアセンブリのバージョン 5.1.1209.1 を参照しています。

2012 年 11 月の SDK で見つかった問題が修正されました。

* IAsset.Locators.Count: このカウントは、すべてのロケーターが削除された後、新しい IAsset インターフェイスで正しく報告されるようになりました。
* IAssetFile.ContentFileSize: この値は、IAssetFile.Upload(filepath) よるアップロード後に適切に設定されるようになりました。
* IAssetFile.ContentFileSize: このプロパティは、資産ファイルの作成時に設定できるようになりました。 以前は、読み取り専用でした。
* IAssetFile.Upload( filepath): 複数のファイルが資産にアップロードされたときに、この同期アップロード メソッドが次のエラーをスローする問題が修正されました。 エラーは、「Server failed to authenticate the request. Make sure the value of Authorization header is formed correctly including the signature.(サーバーが要求を認証できませんでした。認証ヘッダーの値が、署名を含め正しく形成されていることを確認してください。)」
* IAssetFile.UploadAsync: ファイルの同時アップロードが 5 ファイルに制限されるという問題が修正されました。
* IAssetFile.UploadProgressChanged: このイベントは、SDK によって提供されるようになりました。
* IAssetFile.DownloadAsync(string, BlobTransferClient, ILocator, CancellationToken): このメソッドのオーバーロードが提供されるようになりました。
* IAssetFile.DownloadAsync: ファイルの同時ダウンロードが 5 ファイルに制限されるという問題が修正されました。
* IAssetFile.Delete(): IAssetFile のファイルがアップロードされていない場合に、delete 呼び出しで例外がスローされる可能性がある問題が修正されました。
* Jobs: ジョブ テンプレートを使用して "MP4 から Smooth Streams へのタスク" と "PlayReady 保護タスク" を連結すると、タスクがまったく作成されないという問題が修正されました。
* EncryptionUtils.GetCertificateFromStore(): このメソッドは、証明書構成の問題に基づく証明書検出エラーを原因とする null 参照例外をスローしなくなりました。

## <a id="november_changes_12"></a>2012 年 11 月のリリース
ここで説明する変更点は、2012 年 11 月 (バージョン 2.0.0.0) SDK に含まれている更新内容です。 こうした変更により、2012 年 6 月のプレビュー SDK リリース向けに記述されたコードについて、変更または書き換えが必要になる可能性があります。

* 資産
  
    * IAsset.Create(assetName) は、"*唯一の*" 資産作成関数です。 IAsset.Create は、現在、メソッド呼び出しの一部としてファイルをアップロードしません。 アップロードには IAssetFile を使用してください。
    * IAsset.Publish メソッドと AssetState.Publish 列挙値は、サービス SDK から削除されました。 尾の値に依存しているコードはすべて書き換える必要があります。
* FileInfo
  
    * このクラスは削除され、IAssetFile によって置き換えられました。
  
* IAssetFiles
  
    * IAssetFile は FileInfo にとって代わり、異なる動作を実行します。 使用するには、IAssetFiles オブジェクトをインスタント化してから、Media Services SDK または Storage SDK を使用してファイルをアップロードします。 次の IAssetFile.Upload オーバーロードを使用できます。
  
        * IAssetFile.Upload(filePath): この同期メソッドはスレッドをブロックします。単一のファイルをアップロードするときにのみお勧めします。
        * IAssetFile.UploadAsync(filePath, blobTransferClient, locator, cancellationToken): この非同期メソッドは、推奨されるアップロード メカニズムです。 
    
            既知のバグ: キャンセル トークンを使用すると、アップロードがキャンセルされます。 タスクのキャンセル状態は多数あります。 例外を適切にキャッチし、処理する必要があります。
* ロケーター
  
    * オリジン固有のバージョンは削除されました。 SAS 固有の context.Locators.CreateSasLocator(asset, accessPolicy) は、一般公開されるまでに非推奨となるか、削除されます。 更新後の動作については、新機能に関するトピックのロケーターのセクションをご覧ください。

## <a id="june_changes_12"></a>2012 年 6 月のプレビュー リリース
以下は、SDK の 11 月のリリースでの新機能です。

* エンティティの削除
  
    * IAsset、IAssetFile、ILocator、IAccessPolicy、および IContentKey オブジェクトは、Collection、つまり cloudMediaContext.ObjCollection.Delete(objInstance) で削除する必要がありましたが、現在は代わりに、オブジェクト レベルの IObject.Delete() で削除されます。
* ロケーター
  
    * 現在、ロケーターは CreateLocator メソッドを使用して作成する必要があります。 また、作成する特定の種類のロケーターの引数として、LocatorType.SAS または LocatorType.OnDemandOrigin 列挙値を使用しなければなりません。
    * コンテンツ用に使用できる URI を取得しやすくする新しいプロパティがロケーターに追加されました。 このロケーターの再設計により、今後のサードパーティの拡張性を確保するための柔軟性が向上し、メディア クライアント アプリケーションがさらに使いやすくなりました。
* 非同期メソッドのサポート
  
    * すべてのメソッドに非同期のサポートが追加されました。

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Azure Media Services MSDN フォーラム]: http://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[Azure Media Services REST API リファレンス]: https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference
[Media Services の価格詳細]: http://azure.microsoft.com/pricing/details/media-services/
[入力メタデータ]: http://msdn.microsoft.com/library/azure/dn783120.aspx
[出力メタデータ]: http://msdn.microsoft.com/library/azure/dn783217.aspx
[Deliver content]: http://msdn.microsoft.com/library/azure/hh973618.aspx
[Index media files with the Azure Media Indexer]: http://msdn.microsoft.com/library/azure/dn783455.aspx
[StreamingEndpoint]: http://msdn.microsoft.com/library/azure/dn783468.aspx
[Work with Media Services live streaming]: http://msdn.microsoft.com/library/azure/dn783466.aspx
[Use AES-128 dynamic encryption and the key delivery service]: http://msdn.microsoft.com/library/azure/dn783457.aspx
[Use PlayReady dynamic encryption and the license delivery service]: http://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: http://azure.microsoft.com/services/preview/
[Media Services PlayReady ライセンス テンプレートの概要]: http://msdn.microsoft.com/library/azure/dn783459.aspx
[Stream storage-encrypted content]: http://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure portal]: https://portal.azure.com
[ダイナミック パッケージ]: http://msdn.microsoft.com/library/azure/jj889436.aspx
[Nick Drouin's blog]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protect Smooth Streaming with PlayReady]: http://msdn.microsoft.com/library/azure/dn189154.aspx
[Media Services SDK for .NET の再試行ロジック]: http://msdn.microsoft.com/library/azure/dn745650.aspx
[Grass Valley announces EDIUS 7 streaming through the cloud (Grass Valley 社、クラウドを介した EDIUS 7 ストリーミングを発表)]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Control Media Services Encoder output file names]: http://msdn.microsoft.com/library/azure/dn303341.aspx
[Create overlays]: http://msdn.microsoft.com/library/azure/dn640496.aspx
[Stitch video segments]: http://msdn.microsoft.com/library/azure/dn640504.aspx
[Azure Media Services .NET SDK 3.0.0.1 and 3.0.0.2 releases]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Azure AD Access Control Service]: http://msdn.microsoft.com/library/hh147631.aspx
[Connect to Media Services with the Media Services SDK for .NET]: http://msdn.microsoft.com/library/azure/jj129571.aspx
[Media Services .NET SDK extensions]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[Azure SDK tools]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[Manage Media Services assets across multiple Storage accounts]: http://msdn.microsoft.com/library/azure/dn271889.aspx
[Handle Media Services job notifications]: http://msdn.microsoft.com/library/azure/dn261241.aspx

