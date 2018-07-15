---
title: .NET を使用した Azure Media Services コンテンツの発行 | Microsoft Docs
description: ストリーミング URL の構築に使用するロケーターを作成する方法について説明します。 コード サンプルは C# で記述され、Media Services SDK for .NET を利用しています。
author: juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: c53b1f83-4cb1-4b09-840f-9c145b7d6f8d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako
ms.openlocfilehash: 224c9cf5ef9925645de1d94dc5bc03c15ba91432
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
ms.locfileid: "33783211"
---
# <a name="publish-azure-media-services-content-using-net"></a>.NET を使用した Azure Media Services コンテンツの発行
> [!div class="op_single_selector"]
> * [REST](media-services-rest-deliver-streaming-content.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [ポータル](media-services-portal-publish.md)
> 
> 

## <a name="overview"></a>概要
オンデマンド ストリーミング ロケーターを作成してストリーミング URL を構築することで、アダプティブ ビットレート MP4 セットをストリーミングできます。 [アセットをエンコードする](media-services-encode-asset.md) トピックで、アダプティブ ビットレート MP4 セットへのエンコード方法を説明しています。 

> [!NOTE]
> コンテンツが暗号化されている場合は、ロケーターを作成する前に資産配信ポリシーを構成します ( [こちらの](media-services-dotnet-configure-asset-delivery-policy.md) トピックをご覧ください)。 
> 
> 

また、オンデマンド ストリーミング ロケーターを使って、プログレッシブ ダウンロードができる MP4 ファイルの URL を作成できます。  

このトピックでは、オンデマンド ストリーミング ロケーターを作成してアセットを発行し、Smooth、MPEG DASH、HLS ストリーミング URL を作成する方法について説明します。 また、プログレッシブ ダウンロードを行う URL を作成する方法についても説明します。 

## <a name="create-an-ondemand-streaming-locator"></a>オンデマンド ストリーミング ロケーターを作成する
オンデマンド ストリーミング ロケーターを作成して URL を取得するには、次の手順に従います。

1. コンテンツが暗号化されている場合は、アクセス ポリシーを定義します。
2. オンデマンド ストリーミング ロケーターを作成します。
3. ストリーミングする場合は、資産のストリーミング マニフェスト ファイル (.ism) を取得します。 
   
   プログレッシブ ダウンロードをする場合は、資産内の MP4 ファイルの名前を取得します。  
4. マニフェスト ファイルまたは MP4 ファイルへの URL を作成します。 


>[!NOTE]
>さまざまな AMS ポリシー (ロケーター ポリシーや ContentKeyAuthorizationPolicy など) に 1,000,000 ポリシーの制限があります。 常に同じ日数、アクセス許可を使用する場合は、同じポリシー ID を使用します。 たとえば、長期間存在するように意図されたロケーターのポリシー (非アップロード ポリシー) に対してです。 詳細については、 [こちらの](media-services-dotnet-manage-entities.md#limit-access-policies) トピックを参照してください。

### <a name="use-media-services-net-sdk"></a>Media Services .NET SDK を使用する
ストリーミング URL を構築する 

```csharp
    private static void BuildStreamingURLs(IAsset asset)
    {

        // Create a 30-day readonly access policy. 
          // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

        // Create a locator to the streaming content on an origin. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();

        // Get a reference to the streaming manifest file from the  
        // collection of files in the asset. 
        var manifestFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                                    EndsWith(".ism")).
                                    FirstOrDefault();

        // Create a full URL to the manifest file. Use this for playback
        // in streaming media clients. 
        string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
        Console.WriteLine("URL to manifest for client streaming using Smooth Streaming protocol: ");
        Console.WriteLine(urlForClientStreaming);
        Console.WriteLine("URL to manifest for client streaming using HLS protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=m3u8-aapl)");
        Console.WriteLine("URL to manifest for client streaming using MPEG DASH protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=mpd-time-csf)"); 
        Console.WriteLine();
    }
```

次のような出力が表示されます。

    URL to manifest for client streaming using Smooth Streaming protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest
    URL to manifest for client streaming using HLS protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)
    URL to manifest for client streaming using MPEG DASH protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)


> [!NOTE]
> SSL 接続経由でコンテンツのストリーミングもできます。 そのためには、ストリーミング URL の先頭が HTTPS になっていることをご確認ください。 現在のところ、AMS ではカスタム ドメインを使用した SSL はサポートされていません。
> 
> 

プログレッシブ ダウンロード URL を作成します。 

```csharp
    private static void BuildProgressiveDownloadURLs(IAsset asset)
    {
        // Create a 30-day readonly access policy. 
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

        // Create an OnDemandOrigin locator to the asset. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();

        // Get MP4 files.
        IEnumerable<IAssetFile> mp4AssetFiles = asset
            .AssetFiles
            .ToList()
            .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Create a full URL to the MP4 files. Use this to progressively download files.
        foreach (var pd in mp4AssetFiles)
            Console.WriteLine(originLocator.Path + pd.Name);
    }
```
次のような出力が表示されます。

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4

    . . . 

### <a name="use-media-services-net-sdk-extensions"></a>Media Services .NET SDK Extensions の使用
次のコードでは、ロケーターを作成してアダプティブのスムーズ ストリーミング、HLS、MPEG-DASH URL を生成する.NET SDK Extensions のメソッドを呼び出します。
```csharp
    // Create a loctor.
    _context.Locators.Create(
        LocatorType.OnDemandOrigin,
        inputAsset,
        AccessPermissions.Read,
        TimeSpan.FromDays(30));

    // Get the streaming URLs.
    Uri smoothStreamingUri = inputAsset.GetSmoothStreamingUri();
    Uri hlsUri = inputAsset.GetHlsUri();
    Uri mpegDashUri = inputAsset.GetMpegDashUri();

    Console.WriteLine(smoothStreamingUri);
    Console.WriteLine(hlsUri);
    Console.WriteLine(mpegDashUri);
```

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>次の手順
* [資産をダウンロードする](media-services-deliver-asset-download.md)
* [資産配信ポリシーを構成する](media-services-dotnet-configure-asset-delivery-policy.md)

