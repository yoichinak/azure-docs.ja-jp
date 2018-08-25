---
title: Azure Cognitive Services、Microsoft Translator Speech API の Node.js のクイック スタート | Microsoft Docs
description: Azure 上の Microsoft Cognitive Services で Microsoft Translator Speech API の使用をすぐに開始するために役立つ情報とコード サンプルを提供します。
services: cognitive-services
documentationcenter: ''
author: v-jaswel
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jaswel
ms.openlocfilehash: e652ee1e201e60d0d75f10bb8fceb4fbcd205381
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2018
ms.locfileid: "41931335"
---
# <a name="quickstart-for-microsoft-translator-speech-api-with-nodejs"></a>Node.js での Microsoft Translator Speech API のクイック スタート 
<a name="HOLTop"></a>

この記事では、Microsoft Translator Speech API を使用して、.wav ファイルで話されている言葉を翻訳する方法について説明します。

## <a name="prerequisites"></a>前提条件

このコードを実行するには [Node.js 6](https://nodejs.org/en/download/) が必要です。

Node.js 用の [Websocket パッケージ](https://www.npmjs.com/package/websocket)をインストールする必要があります。

下記のコードからコンパイルする実行可能ファイルと同じフォルダーに、"speak.wav" という名前の .wav ファイルを置く必要があります。 この .wav ファイルは、標準の PCM (16 ビット、16 kHz、モノラル形式) である必要があります。 このような .wav ファイルは、[Text to Speech API](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis#text-to-speech) から取得できます。

[Cognitive Services API アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)と **Microsoft Translator Speech API** を用意している必要があります。 [Azure ダッシュボード](https://portal.azure.com/#create/Microsoft.CognitiveServices)の有料サブスクリプション キーが必要です。

## <a name="translate-speech"></a>音声を翻訳する

次のコードは、音声の言語を別の言語に翻訳するものです。

1. 好みの IDE で新しい Node.js プロジェクトを作成します。
2. 下記のコードを追加します。
3. `key` の値を、お使いのサブスクリプションで有効なアクセス キーに置き換えます。
4. プログラムを実行します。

```nodejs
/* To install this dependency, run:
npm install websocket
*/
var wsClient = require('websocket').client;
var fs = require('fs');
/* To install this dependency, run:
npm install stream-buffers
*/
var streamBuffers = require('stream-buffers');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
let key = 'ENTER KEY HERE';

let host = 'wss://dev.microsofttranslator.com';
let path = '/speech/translate';
let params = '?api-version=1.0&from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa';
let uri = host + path + params;

/* The input .wav file is in PCM 16bit, 16kHz, mono format.
You can obtain such a .wav file using the Text to Speech API. See:
https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis#text-to-speech
*/
let input_path = 'speak.wav';

let output_path = 'speak2.wav';

function receive(message) {
    console.log ("Received message of type: " + message.type + ".");

    if (message.type == 'utf8') {
        var result = JSON.parse(message.utf8Data)
        console.log("Response type: " + result.type + ".");
        console.log("Recognized input as: " + result.recognition);
        console.log("Translation: " + result.translation);
    }
    else if (message.type == 'binary') {
/* This is needed to make sure message.binaryData is defined. See:
https://stackoverflow.com/questions/17546953/cant-access-object-property-even-though-it-exists-returns-undefined
*/
        let binary_data = JSON.parse (JSON.stringify (message.binaryData));

        if (binary_data.type == 'Buffer') {
/* Put the binary data in a Buffer - do not write it directly to the file. */
            let buffer = new Buffer(binary_data.data, 'binary');
/* Write the contents of the Buffer to the file. */
            fs.writeFile (output_path, buffer, 'binary', function (error) {
/* fs.writeFile calls the error-handling function even if there is no error, in which case error === null. See:
https://stackoverflow.com/questions/44473868/node-js-fs-writefile-err-returns-null
*/
                if (error !== null) {
                    console.log ("Error: " + error);
                }
            });
        }
    }
}

function send(connection, filename) {
    
    var myReadableStreamBuffer = new streamBuffers.ReadableStreamBuffer({
        frequency: 100,
        chunkSize: 32000
    });

/* Make sure the audio file is followed by silence.
This lets the service know that the audio file is finished.
At 32 bytes per millisecond, this is 100 seconds of silence. */
    myReadableStreamBuffer.put(fs.readFileSync(filename));
    myReadableStreamBuffer.put(new Buffer(3200000));
    myReadableStreamBuffer.stop();

    myReadableStreamBuffer.on('data', function (data) {
        connection.sendBytes(data);
    });

    myReadableStreamBuffer.on('end', function () {
        console.log('Closing connection.');
        connection.close(1000);
    });
}

function connect() {
    var ws = new wsClient();

    ws.on('connectFailed', function (error) {
        console.log('Connection error: ' + error.toString());
    });
                            
    ws.on('connect', function (connection) {
        console.log('Connected.');

        connection.on('message', receive);
        
        connection.on('close', function (reasonCode, description) {
            console.log('Connection closed: ' + reasonCode);
        });

        connection.on('error', function (error) {
            console.log('Connection error: ' + error.toString());
        });
        
        send(connection, input_path);
    });

    ws.connect(uri, null, null, { 'Ocp-Apim-Subscription-Key' : key });
}

connect();
```

**音声翻訳の結果**

正常に実行されると、"speak2.wav" という名前のファイルが作成されます。 このファイルには、"speak.wav" で話されてる言葉の翻訳が含まれています。

[先頭に戻る](#HOLTop)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Translator Speech のチュートリアル](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>関連項目 

[Translator Speech の概要](../overview.md)
[API リファレンス](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)
