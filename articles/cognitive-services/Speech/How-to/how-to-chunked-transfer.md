---
title: オーディオ ストリームをチャンク転送する方法 | Microsoft Docs
description: チャンク転送を使用してオーディオ ストリームを音声サービスに送信する方法
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 7d02340932dfc547893c4c40cbe08978b7b93756
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373789"
---
# <a name="chunked-transfer-encoding"></a>チャンク転送エンコーディング

音声を文字に起こすために、Microsoft Speech Recognition API では、オーディオを 1 つの大きなチャンクとして、または小さなチャンクに切って送信できます。 オーディオのストリーミングを効率化し、文字起こしの待ち時間を減らすために、[チャンク転送エンコーディング](https://en.wikipedia.org/wiki/Chunked_transfer_encoding)を使用してオーディオをサービスにストリームすることをお勧めします。 その他の実装は、ユーザーが感じる待ち時間が長くなる場合があります。 詳しくは、「[Audio Streams](../concepts.md#audio-streams)」(オーディオ ストリーム) をご覧ください。

> [!NOTE]
> 1 つの要求のオーディオが 10 秒を超えてはなりません。また、要求の合計時間が 14 秒を超えてはなりません。
> [!NOTE]
> チャンク転送エンコーディングは、音声サービスの呼び出しに [REST API](../GetStarted/GetStartedREST.md) を使用する場合にのみ指定する必要があります。 [クライアント ライブラリ](../GetStarted/GetStartedClientLibraries.md)を使用するアプリケーションは、チャンク転送エンコードを構成する必要がありません。

次のコードは、チャンク転送エンコーディングを設定し、1024 byte のチャンクに分割されたオーディオ ファイルを送信する方法を示します。

```cs

HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = @"speech.platform.bing.com";
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";

using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{

    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```
