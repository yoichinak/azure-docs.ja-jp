---
title: 既存のプレーヤーを使用したコンテンツの再生 - Azure | Microsoft Docs
description: このトピックでは、コンテンツの再生に使用できる既存のプレーヤーを示します。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 7e9fcf89-0fb6-4fa4-96cb-666320684d69
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/02/2018
ms.author: juliako
ms.openlocfilehash: 3fe82b98163182c73a144b72da371e8aa195e8cf
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37435859"
---
# <a name="playing-your-content-with-existing-players"></a>既存のプレーヤーによるコンテンツの再生
Azure Media Services は、スムーズ ストリーミング、HTTP ライブ ストリーミング、Mpeg-dash など、人気のある多くのストリーミング形式をサポートします。 このトピックでは、ストリームのテストに使用できる既存のプレーヤーを示します。

### <a name="the-azure-portal-media-services-content-player"></a>Azure Portal Media Services コンテンツ プレーヤー
**Azure** ポータルには、ビデオのテストに使用できるコンテンツ プレーヤーが用意されています。

目的のビデオをクリックし ( [発行済](media-services-portal-publish.md)であることを確認します)、ポータルの下部の **[再生]** ボタンをクリックします。

いくつかの考慮事項が適用されます。

* **Media Services コンテンツ プレーヤー**を既定のストリーミング エンドポイントから再生します。 既定以外のストリーミング エンドポイントから再生する場合は、別のプレーヤーを使用します たとえば、[Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) を使用します。

![AMSPlayer][AMSPlayer]

### <a name="azure-media-player"></a>Azure Media Player
[Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) を使用して、次のいずれかの形式でコンテンツ (平文のコンテンツまたは保護されたコンテンツ) を再生します。

* スムーズ ストリーミング
* MPEG DASH
* HLS
* Progressive MP4

### <a name="flash-player"></a>Flash Player
#### <a name="aes-encrypted-with-token"></a>トークンを使用した AES 暗号化
[http://aestoken.azurewebsites.net](http://aestoken.azurewebsites.net)

### <a name="silverlight-players"></a>Silverlight Players

#### <a name="playready-with-token"></a>トークンを使用した PlayReady
[http://sltoken.azurewebsites.net](http://sltoken.azurewebsites.net)

### <a name="dash-players"></a>DASH Players
[http://dashplayer.azurewebsites.net](http://dashplayer.azurewebsites.net)

[http://dashif.org](http://dashif.org)

### <a name="other"></a>その他
次を使用して HLS URL をテストすることもできます。

* **Safari** (iOS デバイス) または
* **3ivx HLS Player** (Windows)

## <a name="developing-video-players"></a>ビデオ プレーヤーの開発
独自のプレーヤーの開発方法については、「 [ビデオ プレーヤーの開発](media-services-develop-video-players.md)

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png
