---
title: アプリケーションに Azure Video Indexer ウィジェットを埋め込む | Microsoft Docs
description: ''
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/31/2018
ms.author: juliako
ms.openlocfilehash: 4ce1eedac69b06f491510be93fcfbdfbc879ff7e
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2018
ms.locfileid: "39398134"
---
# <a name="embed-video-indexer-widgets-into-your-applications"></a>アプリケーションに Video Indexer ウィジェットを埋め込む

Video Indexer では、**コグニティブな分析情報**と**プレーヤー**という 2 種類のウィジェットのアプリケーションへの埋め込みがサポートされています。 

* **コグニティブな分析情報**ウィジェットには、ビデオのインデックス作成プロセスから抽出されたビジュアルな分析情報がすべて含まれます。 
    分析情報ウィジェットでは、次の省略可能な URL パラメーターがサポートされています。

    |Name|定義|説明|
    |---|---|---|
    |widgets|コンマで区切られた文字列|レンダリングする分析情報を制御できます。 <br/>例: **widgets=people,brands** の場合、人物とブランドの UI 分析情報のみがレンダリングされます<br/>使用可能なオプション: people、keywords、annotations、brands、sentiments、transcript、search | 
* **プレーヤー** ウィジェットでは、アダプティブ ビット レートを使用してビデオをストリームできます。

    プレーヤー ウィジェットでは、次の省略可能な URL パラメーターがサポートされています。

    |Name|定義|説明|
    |---|---|---|
    |t|開始からの秒数|プレーヤーで、指定した時点から再生を開始します。<br/>例: t=60|
    |captions|言語コード|ウィジェットを読み込むときに指定された言語のキャプションを取り込んで、キャプション メニューで使用できるようにします。<br/>例: captions=en-Us|
    |showCaptions|ブール値|既に有効になっているキャプションとともにプレーヤーを読み込みます。<br/>例: showCaptions=true|
    |type||オーディオ プレーヤーのスキンをアクティブにします (ビデオ部分は削除されます)。<br/>例: type=audio|
    |autoplay|ブール値|プレーヤーが読み込まれたときに再生を開始するかどうかを指定します (既定値は true)。<br/>例: autoplay=false|
    |language|言語コード|プレーヤーでローカライズを制御するかどうかを指定します (既定値は en-US)<br/>例: language=de-DE|

## <a name="embedding-public-content"></a>パブリック コンテンツの埋め込み

1. [Video Indexer](https://api-portal.videoindexer.ai/) アカウントにサインインします。 
2. ビデオの下に表示される "埋め込み" ボタンをクリックします。

    ![ウィジェット](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    ボタンをクリックすると、画面に埋め込みモーダルが表示され、アプリケーションに埋め込むウィジェットを選択できます。
    ウィジェット (**プレーヤー**または**コグニティブな分析情報**) を選択すると、アプリケーションに貼り付けるための埋め込みコードが生成されます。
 
3. 目的のウィジェットの種類 (**コグニティブな分析情報**または**プレーヤー**) を選択します。
4. 埋め込みコードをコピーし、アプリケーションに追加します。 

    ![ウィジェット](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

## <a name="embedding-private-content"></a>プライベート コンテンツの埋め込み

(前のセクションで示した) 埋め込みポップアップから埋め込みコードを取得できるのは、**パブリック** ビデオの場合のみです。 

**プライベート** ビデオを埋め込む場合は、**iframe** の **src** 属性でアクセス トークンを渡す必要があります。

     https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<VideoId>/?accessToken=<accessToken>
    
[**Get Insights Widget**](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-insights-widget?) API を使用してコグニティブな分析情報ウィジェットのコンテンツを取得するか、または [**Get Video Access Token**](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) を使用してそれを上に示した URL にクエリ パラメーターとして追加します。 この URL を、**iframe** の **src** 値として指定します。

埋め込んだウィジェットで (Web アプリケーションにあるような) 分析情報の編集機能を提供したい場合は、編集アクセス許可を持つアクセス トークンを渡す必要があります。 [**Get Insights Widget**](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-insights-widget?) または [**Get Video Access Token**](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) を、**&allowEdit=true** を指定して使用します。 

## <a name="widgets-interaction"></a>ウィジェットの対話

**コグニティブな分析情報**ウィジェットは、アプリケーション上でビデオと対話できます。 このセクションでは、この対話を実現する方法を示します。

![ウィジェット](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>クロスオリジン通信

Video Indexer ウィジェットが他のコンポーネントと通信できるようにするために、Video Indexer サービスは次のことを行います。

- クロス オリジン通信の HTML5 メソッド **postMessage** を使用します。 
- VideoIndexer.ai からのメッセージの発生元を検証します。 

独自のプレーヤー コードを実装して**コグニティブな分析情報**ウィジェットとの統合を行う場合は、VideoIndexer.ai からのメッセージの発生元をお客様の責任で検証する必要があります。

### <a name="embed-both-types-of-widgets-in-your-application--blog-recommended"></a>両方の種類のウィジェットをアプリケーション/ブログに埋め込む (推奨) 

このセクションでは、2 つの Video Indexer ウィジェット間の対話を実現して、ユーザーがアプリケーションで分析情報コントロールをクリックしたときにプレーヤーが関連する場面にジャンプするようにする方法を示します。

    <script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script> 

1. **プレーヤー** ウィジェットの埋め込みコードをコピーします。
2. **コグニティブな分析情報**ウィジェットの埋め込みコードをコピーします。
3. 2 つのウィジェット間の通信を処理する[**メディエーター ファイル**](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js)を追加します。

    <script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>

これで、ユーザーがアプリケーションで分析情報コントロールをクリックすると、プレーヤーが関連する場面にジャンプします。

詳細については、[このデモ](https://api-portal.videoindexer.ai/demo-all-widgets)を参照してください。

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>コグニティブな分析情報ウィジェットを埋め込み、Azure Media Player を使用してコンテンツを再生する

このセクションでは、[AMP プラグイン](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js)を使用して**コグニティブな分析情報**ウィジェットと Azure Media Player インスタンス間の対話を実現する方法を示します。
 
1. AMP プレーヤー用の Video Indexer プラグインを追加します。

        <script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>


2. Video Indexer プラグインを含む Azure Media Player をインスタンス化します。

        // Init Source
        function initSource() {
            var tracks = [{
            kind: 'captions',
            // Here is how to load vtt from VI, you can replace it with your vtt url.
            src: this.getSubtitlesUrl("c4c1ad4c9a", "English"),
            srclang: 'en',
            label: 'English'
            }];

            myPlayer.src([
            {
                "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
                "type": "application/vnd.ms-sstr+xml"
            }
            ], tracks);
        }

        // Init your AMP instance
        var myPlayer = amp('vid1', { /* Options */
            "nativeControlsForTouch": false,
            autoplay: true,
            controls: true,
            width: "640",
            height: "400",
            poster: "",
            plugins: {
            videobreakedown: {}
            }
        }, function () {
            // Activate the plugin
            this.videobreakdown({
            videoId: "c4c1ad4c9a",
            syncTranscript: true,
            syncLanguage: true
            });

            // Set the source dynamically
            initSource.call(this);
        });

3. **コグニティブな分析情報**ウィジェットの埋め込みコードをコピーします。

これで Azure Media Player と通信できるようになります。

詳細については、[このデモ](https://api-portal.videoindexer.ai/demo-your-amp)を参照してください。

### <a name="embed-video-indexer-cognitive-insights-widget-and-use-your-own-player-could-be-any-player"></a>Video Indexer のコグニティブな統計情報ウィジェットを埋め込み、独自のプレーヤー (任意のプレーヤー) を使用する

独自のプレーヤーを使用する場合、通信を実現するには、お客様自身がプレーヤーを操作する必要があります。 

1. ビデオ プレーヤーを挿入します。

    たとえば、標準の HTML5 プレーヤーを挿入します。

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. コグニティブな分析情報ウィジェットを埋め込みます。
3. "メッセージ" イベントをリッスンして、プレーヤー用の通信を実装します。 例: 

        <script>
    
            (function(){
            // Reference your player instance
            var playerInstance = document.getElementById('vid1');
        
            function jumpTo(evt) {
              var origin = evt.origin || evt.originalEvent.origin;
        
              // Validate that event comes from the videobreakdown domain.
              if ((origin === "https://www.videobreakdown.com") && evt.data.time !== undefined){
                
                // Here you need to call your player "jumpTo" implementation
                playerInstance.currentTime = evt.data.time;
               
                // Confirm arrival to us
                if ('postMessage' in window) {
                  evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
                }
              }
            }
        
            // Listen to message event
            window.addEventListener("message", jumpTo, false);
          
            }())    
        
        </script>


詳細については、[このデモ](https://api-portal.videoindexer.ai/demo-your-player)を参照してください。

## <a name="adding-subtitles"></a>字幕の追加

Video Indexer の分析情報と独自の AMP プレーヤーを埋め込む場合は、**GetVttUrl** メソッドを使用してクローズド キャプション (字幕) を取得できます。 (前に示した) Video Indexer の AMP プラグイン **getSubtitlesUrl** から javascript メソッドを呼び出すこともできます。 

## <a name="customizing-embeddable-widgets"></a>埋め込み可能なウィジェットのカスタマイズ

### <a name="cognitive-insights-widget"></a>コグニティブな分析情報ウィジェット
必要な分析情報の種類を、(API または Web アプリから) 取得した埋め込みコードに追加される次の URL パラメーターに値として指定することで選択できます。

**&widgets=** \<必要なウィジェットのリスト>

使用可能な値は、people、keywords、sentiments、transcript、search です。

たとえば、people および search 分析情報のみを含むウィジェットを埋め込みたい場合、iframe の埋め込み URL は次のようになります。https://www.videoindexer.ai/embed/insights/c4c1ad4c9a/?widgets=people,search

iframe ウィンドウのタイトルも、iframe の URL に **&title=**<YourTitle> を指定することでカスタマイズできます  (これにより、html \<title> 値がカスタマイズされます)。
たとえば、iframe ウィンドウに "MyInsights" というタイトルを付けたい場合、URL はhttps://www.videoindexer.ai/embed/insights/c4c1ad4c9a/?title=MyInsights のようになります。 このオプションは、新しいウィンドウで分析情報を開く必要がある場合にのみ該当することに注意してください。

### <a name="player-widget"></a>プレーヤー ウィジェット
Video Indexer プレーヤーを埋め込む場合は、iframe のサイズを指定することで、プレーヤーのサイズを選択できます。

例:

    <iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/{id}” frameborder="0" allowfullscreen />

既定では、ビデオのアップロード時に選択されたソース言語とともにビデオから抽出されたビデオ トランスクリプトに基づいて、Video Indexer プレーヤーでクローズド キャプションが自動生成されます。

別の言語で埋め込みたい場合は、埋め込みプレーヤーの URL に **&captions=< Language | ”all” | “false” >** を追加します。または、使用可能なすべての言語のキャプションを使用する場合は “all” を値として指定します。
既定でキャプションが表示されるようにするには、**&showCaptions=true** を渡します。

埋め込み URL は https://www.videoindexer.ai/embed/player/9a296c6ec3/?captions=italian のようになります。 キャプションを無効にする場合は、captions パラメーターの値として “false” を渡します。

自動再生 – 既定では、プレーヤーでビデオの再生が開始されます。 上の埋め込み URL に &autoplay=false を渡すことにより、これをオフにできます。

## <a name="next-steps"></a>次の手順

Video Indexer の分析情報を表示および編集する方法の詳細については[この](video-indexer-view-edit.md)記事を参照してください。

## <a name="see-also"></a>関連項目

[Video Indexer の概要](video-indexer-overview.md)
