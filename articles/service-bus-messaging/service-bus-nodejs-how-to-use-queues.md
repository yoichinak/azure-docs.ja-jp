---
title: Service Bus キューの使用方法 (Node.js) | Microsoft Docs
description: Node.js アプリから Azure の Service Bus キューを使用する方法を学習します。
services: service-bus-messaging
documentationcenter: nodejs
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: 7bc291c3a453b4dec5e6c47ab4a7f2e2ac2b9967
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38232783"
---
# <a name="how-to-use-service-bus-queues-with-nodejs"></a>Node.js で Service Bus キューを使用する方法

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

この記事では、Node.js で Service Bus キューを使用する方法について説明します。 サンプルは JavaScript で記述され、Node.js Azure モジュールを利用しています。 紹介するシナリオは、**キューの作成**、**メッセージの送受信**、**キューの削除**です。 キューの詳細については、「[次のステップ](#next-steps)」のセクションを参照してください。

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-a-nodejs-application"></a>Node.js アプリケーションの作成
空の Node.js アプリケーションを作成します。 Node.js アプリケーションを作成する手順については、「[Node.js アプリケーションの作成と Azure Web サイトへのデプロイ][Create and deploy a Node.js application to an Azure Website]」または「[Node.js クラウド サービス][Node.js Cloud Service]」 (Windows PowerShell の使用) をご覧ください。

## <a name="configure-your-application-to-use-service-bus"></a>Service Bus を使用するようにアプリケーションを構成する
Azure Service Bus を使用するには、Node.js Azure パッケージをダウンロードして使用します。 このパッケージには、Service Bus REST サービスと通信するためのライブラリのセットが含まれています。

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>ノード パッケージ マネージャー (NPM) を使用してパッケージを取得する
1. **Windows PowerShell for Node.js** コマンド ウィンドウを使用して、サンプル アプリケーションを作成した **c:\\node\\sbqueues\\WebRole1** フォルダーに移動します。
2. コマンド ウィンドウに「**npm install azure**」と入力すると、次のような出力が生成されます。

    ```
    azure@0.7.5 node_modules\azure
        ├── dateformat@1.0.2-1.2.3
        ├── xmlbuilder@0.4.2
        ├── node-uuid@1.2.0
        ├── mime@1.2.9
        ├── underscore@1.4.4
        ├── validator@1.1.1
        ├── tunnel@0.0.2
        ├── wns@0.5.3
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)
    ```
3. 手動で **ls** コマンドを実行して、**node_modules** フォルダーが作成されたことを確認することもできます。 このフォルダーで **azure** パッケージを検索します。このパッケージには、Service Bus キューにアクセスするために必要なライブラリが含まれています。

### <a name="import-the-module"></a>モジュールのインポート
メモ帳などのテキスト エディターを使用して、アプリケーションの **server.js** ファイルの先頭に次の内容を追加します。

```javascript
var azure = require('azure');
```

### <a name="set-up-an-azure-service-bus-connection"></a>Azure Service Bus 接続の設定
Azure モジュールは、Service Bus に接続するために必要な情報を得るために、環境変数 `AZURE_SERVICEBUS_CONNECTION_STRING` を読み取ります。 この環境変数が設定されていない場合は、`createServiceBusService` を呼び出すときにアカウント情報を指定する必要があります。

Azure Web サイトの [Azure Portal][Azure portal] で環境変数を設定する例については、「[ストレージを使用する Node.js Web アプリケーション][Node.js Web Application with Storage]」をご覧ください。

## <a name="create-a-queue"></a>キューを作成する
Service Bus キューは、**ServiceBusService** オブジェクトを使用して操作できます。 次のコードでは、**ServiceBusService** オブジェクトを作成します。 **server.js** ファイルの先頭付近の、azure モジュールをインポートするステートメントの後に、このコードを追加します。

```javascript
var serviceBusService = azure.createServiceBusService();
```

**ServiceBusService** オブジェクトの `createQueueIfNotExists` を呼び出すことによって、指定されたキューが返されるか (存在する場合)、指定された名前で新しいキューが作成されます。 次のコードでは、`createQueueIfNotExists` を使用して、`myqueue` という名前のキューを作成、またはキューに接続します。

```javascript
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

`createServiceBusService` メソッドは追加のオプションもサポートしています。これにより、メッセージの有効期間や最大キュー サイズなどの既定のキューの設定をオーバーライドできます。 次の例では、最大キュー サイズを 5 GB に、有効期間を 1 分に設定します。

```javascript
var queueOptions = {
      MaxSizeInMegabytes: '5120',
      DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createQueueIfNotExists('myqueue', queueOptions, function(error){
    if(!error){
        // Queue exists
    }
});
```

### <a name="filters"></a>フィルター
オプションのフィルター操作は、**ServiceBusService** を使用して行われる操作に適用できます。 フィルター操作には、ログや自動的な再試行などが含まれる場合があります。フィルターは、次のシグネチャを持つメソッドを実装するオブジェクトです。

```javascript
function handle (requestOptions, next)
```

要求オプションに対するプリプロセスを行った後で、このメソッドは `next` を呼び出して、次のシグネチャのコールバックを渡す必要があります。

```javascript
function (returnObject, finalCallback, next)
```

このコールバックで、`returnObject` (サーバーへの要求からの応答) の処理の後に、コールバックは `next` を呼び出すか (他のフィルターの処理を続けるためにそれが存在する場合)、単に (サービス呼び出しを終了する) `finalCallback` を呼び出す必要があります。

再試行のロジックを実装する 2 つのフィルター (`ExponentialRetryPolicyFilter` と `LinearRetryPolicyFilter`) が、Azure SDK for Node.js に含まれています。 次のコードは `ExponentialRetryPolicyFilter` を使用する `ServiceBusService` オブジェクトを作成します。

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="send-messages-to-a-queue"></a>メッセージをキューに送信する
メッセージを Service Bus キューに送信するには、アプリケーションで **ServiceBusService** オブジェクトの `sendQueueMessage` メソッドを呼び出します。 Service Bus キューに送信された (またキューから受信された) メッセージは **BrokeredMessage** オブジェクトであり、このオブジェクトには、(**Label**、**TimeToLive** などの) 標準的なプロパティ、アプリケーションに特有のカスタム プロパティの保持に使用するディクショナリ、任意のアプリケーション データの本体が備わっています。 アプリケーションはメッセージとして文字列値を渡すことでメッセージの本文を設定できます。 必須の標準プロパティには既定値が入力されます。

次の例では、`sendQueueMessage` を使用して、`myqueue` という名前のキューにテスト メッセージを送信する方法を示しています。

```javascript
var message = {
    body: 'Test message',
    customProperties: {
        testproperty: 'TestValue'
    }};
serviceBusService.sendQueueMessage('myqueue', message, function(error){
    if(!error){
        // message sent
    }
});
```

Service Bus キューでサポートされているメッセージの最大サイズは、[Standard レベル](service-bus-premium-messaging.md)では 256 KB、[Premium レベル](service-bus-premium-messaging.md)では 1 MB です。 標準とカスタムのアプリケーション プロパティが含まれるヘッダーの最大サイズは 64 KB です。 キューで保持されるメッセージ数には上限がありませんが、キュー 1 つあたりが保持できるメッセージの合計サイズには上限があります。 このキュー サイズは作成時に定義され、上限は 5 GB です。 クォータについて詳しくは、「[Service Bus のクォータ][Service Bus quotas]」をご覧ください。

## <a name="receive-messages-from-a-queue"></a>キューからメッセージを受信する
キューからメッセージを受信するには、**ServiceBusService** オブジェクトの `receiveQueueMessage` メソッドを使用します。 既定では、メッセージは読み取られるときにキューから削除されますが、省略可能な `isPeekLock` パラメーターを **true** に設定することによって、キューからメッセージを削除せずに、メッセージを読み取って (ピークして) ロックできます。

受信操作の中で行われるメッセージの読み取りと削除の既定の動作は、最もシンプルなモデルであり、障害発生時にアプリケーション側でメッセージを処理しないことを許容できるシナリオに最適です。 このことを理解するために、コンシューマーが受信要求を発行した後で、メッセージを処理する前にクラッシュしたというシナリオを考えてみましょう。 Service Bus はメッセージを読み取り済みとしてマークするため、アプリケーションが再起動してメッセージの読み取りを再開すると、クラッシュ前に読み取られていたメッセージは見落とされることになります。

`isPeekLock` パラメーターが **true** に設定されている場合、受信処理が 2 段階の動作になり、メッセージが失われることが許容できないアプリケーションに対応することができます。 Service Bus は要求を受け取ると、次に読み取られるメッセージを検索して、他のコンシューマーが受信できないようロックしてから、アプリケーションにメッセージを返します。 アプリケーションがメッセージの処理を終えた後 (または後で処理するために確実に保存した後)、`deleteMessage` メソッドを呼び出し、削除するメッセージをパラメーターとして指定して、受信処理の第 2 段階を完了します。 `deleteMessage` メソッドによって、メッセージが読み取り中としてマークされ、キューから削除されます。

次の例は `receiveQueueMessage` を使用してメッセージを受信し、処理する方法を示しています。 この例では、最初にメッセージを受信して削除し、次に **true** に設定した `isPeekLock` を使用してメッセージを受信した後、`deleteMessage` を使用してメッセージを削除します。

```javascript
serviceBusService.receiveQueueMessage('myqueue', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
    }
});
serviceBusService.receiveQueueMessage('myqueue', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
            }
        });
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>アプリケーションのクラッシュと読み取り不能のメッセージを処理する方法
Service Bus には、アプリケーションにエラーが発生した場合や、メッセージの処理に問題がある場合に復旧を支援する機能が備わっています。 受信側のアプリケーションが何らかの理由によってメッセージを処理できない場合には、**ServiceBusService** オブジェクトの `unlockMessage` メソッドを呼び出すことができます。 このメソッドが呼び出されると、Service Bus によってキュー内のメッセージのロックが解除され、メッセージが再度受信できる状態に変わります。メッセージを受信するアプリケーションは、以前と同じものでも、別のものでもかまいません。

キュー内でロックされているメッセージにはタイムアウトも設定されています。アプリケーションがクラッシュした場合など、ロックがタイムアウトになる前にアプリケーションがメッセージの処理に失敗した場合には、Service Bus によりメッセージのロックが自動的に解除され、再度受信できる状態に変わります。

メッセージが処理された後、`deleteMessage` メソッドが呼び出される前にアプリケーションがクラッシュした場合は、アプリケーションが再起動する際にメッセージが再配信されます。 一般的に、この動作は *1 回以上の処理* と呼ばれます。つまり、すべてのメッセージが 1 回以上処理されますが、特定の状況では、同じメッセージが再配信される可能性があります。 重複処理が許されないシナリオの場合、重複メッセージの配信を扱うロジックをアプリケーションに追加する必要があります。 通常、この問題はメッセージの **MessageId** プロパティを使用して対処します。このプロパティは配信が試行された後も同じ値を保持します。

## <a name="next-steps"></a>次の手順
キューの詳細については、次のリソースを参照してください。

* [キュー、トピック、サブスクリプション][Queues, topics, and subscriptions]
* GitHub の [Azure SDK for Node][Azure SDK for Node] リポジトリ
* [Node.js デベロッパー センター](https://azure.microsoft.com/develop/nodejs/)

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com

[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Create and deploy a Node.js application to an Azure Website]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Web Application with Storage]:../cosmos-db/table-storage-how-to-use-nodejs.md
[Service Bus quotas]: service-bus-quotas.md
