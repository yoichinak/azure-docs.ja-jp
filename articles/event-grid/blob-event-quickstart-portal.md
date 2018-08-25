---
title: Azure portal を使用した Azure Event Grid の BLOB ストレージ イベント | Microsoft Docs
description: Azure Event Grid と Azure portal を使用して BLOB ストレージ アカウントを作成し、そのイベントをサブスクライブします。
services: event-grid
keywords: ''
author: tfitzmac
ms.author: tomfitz
ms.date: 07/05/2018
ms.topic: quickstart
ms.service: event-grid
ms.openlocfilehash: 423995aecc6e5f29464ad140349ba27f89c75b5d
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2018
ms.locfileid: "39068745"
---
# <a name="create-and-route-blob-storage-events-with-the-azure-portal-and-event-grid"></a>Azure portal と Event Grid を使用した BLOB ストレージ イベントの作成とルーティング

Azure Event Grid は、クラウドのイベント処理サービスです。 この記事では、Azure portal を使用して BLOB ストレージ アカウントを作成し、その BLOB ストレージのイベントをサブスクライブし、イベントをトリガーして結果を表示します。 通常は、イベント データを処理し、アクションを実行するエンドポイントにイベントを送信します。 ただし、この記事では、単純化するために、メッセージを収集して表示する Web アプリにイベントを送信します。

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

最後に、イベント データが Web アプリに送信されたことを確認します。

![結果の表示](./media/blob-event-quickstart-portal/view-results.png)

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

Blob ストレージ イベントを使用するには、[Blob ストレージ アカウント](../storage/common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-storage-accounts)または[汎用 v2 ストレージ アカウント](../storage/common/storage-account-options.md#general-purpose-v2-accounts)のどちらかが必要です。 **汎用 v2 (GPv2)** は、BLOB、Files、Queues、Tables をはじめとする全ストレージ サービスに関して、すべての機能をサポートするストレージ アカウントです。 **BLOB ストレージ アカウント**とは、Azure Storage に BLOB (オブジェクト) として非構造化データを格納するための特殊なストレージ アカウントです。 Blob Storage アカウントは、汎用ストレージ アカウントと同様に、現在使われているすべての優れた耐久性、可用性、スケーラビリティ、およびパフォーマンス機能を共有します。たとえば、ブロック BLOB と追加 BLOB の 100% の API 整合性などです。 ブロックまたは追加 Blob Storage のみを必要とするアプリケーションでは、BLOB ストレージ アカウントを使用することをお勧めします。 

1. [Azure ポータル](https://portal.azure.com/)にサインインします。

1. BLOB ストレージを作成するには、**[リソースの作成]** を選択します。 

   ![リソースの作成](./media/blob-event-quickstart-portal/create-resource.png)

1. 使用可能なオプションをフィルター選択するために **[ストレージ]** を選択し、**[ストレージ アカウント - Blob、File、Table、Queue]** を選択します。

   ![[ストレージ] を選択する](./media/blob-event-quickstart-portal/create-storage.png)

1. アカウントの一意の名前を含む、BLOB ストレージの値を指定します。 アカウントの種類として、**[BLOB ストレージ]** を選択します。 場所として、Event Grid をサポートするいずれかの[場所](overview.md)を選択します。 値の指定が完了したら、**[作成]** を選択します。

   ![手順の開始](./media/blob-event-quickstart-portal/provide-blob-values.png)

## <a name="create-a-message-endpoint"></a>メッセージ エンドポイントの作成

BLOB ストレージのイベントをサブスクライブする前に、イベント メッセージのエンドポイントを作成しましょう。 通常、エンドポイントは、イベント データに基づくアクションを実行します。 このクイック スタートを簡素化するために、イベント メッセージを表示する[構築済みの Web アプリ](https://github.com/dbarkol/azure-event-grid-viewer)をデプロしします。 デプロイされたソリューションには、App Service プラン、App Service Web アプリ、および GitHub からのソース コードが含まれています。

1. **[Deploy to Azure]\(Azure にデプロイ\)** を選択して、ソリューションをサブスクリプションにデプロイします。 Azure portal で、パラメーターの値を指定します。

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fdbarkol%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. デプロイが完了するまでに数分かかる場合があります。 デプロイが成功した後で、Web アプリを表示して、実行されていることを確認します。 Web ブラウザーで `https://<your-site-name>.azurewebsites.net` にアクセスします

1. サイトは表示されますが、イベントはまだ送信されていません。

   ![新しいサイトを表示する](./media/blob-event-quickstart-portal/view-site.png)

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-the-blob-storage"></a>BLOB ストレージのサブスクライブ

どのイベントを追跡し、どこにイベントを送信するかは、トピックを購読することによって Event Grid に伝えます。

1. ポータルで、対象の BLOB ストレージを選択し、**[イベント]** を選択します。

   ![[イベント] を選択する](./media/blob-event-quickstart-portal/select-events.png)

1. イベントをビューアー アプリに送信するには、エンドポイントの Web hook を使用します。 **[その他のオプション]** を選択し、**[webhook]** を選択します。

   ![[webhook] を選択する](./media/blob-event-quickstart-portal/select-web-hook.png)

1. イベント サブスクリプションには、BLOB ストレージの値があらかじめ入力されています。 Web hook エンドポイントに対して、Web アプリの URL を入力し、ホーム ページの URL に `api/updates` を追加します。 サブスクリプションに名前を付けます。 完了したら、**[作成]** を選択します。

   ![ログの選択](./media/blob-event-quickstart-portal/create-subscription.png)

1. Web アプリをもう一度表示し、その Web アプリにサブスクリプションの検証イベントが送信されたことに注目します。 目のアイコンを選択してイベント データを展開します。 Event Grid は検証イベントを送信するので、エンドポイントはイベント データを受信することを確認できます。 Web アプリには、サブスクリプションを検証するコードが含まれています。

   ![サブスクリプション イベントの表示](./media/blob-event-quickstart-portal/view-subscription-event.png)

では、イベントをトリガーして、Event Grid がメッセージをエンドポイントに配信するようすを見てみましょう。

## <a name="send-an-event-to-your-endpoint"></a>エンドポイントへのイベントの送信

BLOB ストレージのイベントをトリガーするには、ファイルをアップロードします。 このファイルは、特定のコンテンツを必要としません。 この記事では、testfile.txt という名前のファイルがあると仮定していますが、任意のファイルを使用できます。

1. BLOB ストレージのために、**[BLOB]** を選択します。

   ![BLOB の選択](./media/blob-event-quickstart-portal/select-blobs.png)

1. **[+ コンテナー]** を選択します。 コンテナーに名前を付け、任意のアクセス レベルを使用します。

   ![コンテナーの追加](./media/blob-event-quickstart-portal/add-container.png)

1. 新しいコンテナーを選択します。

   ![コンテナーの選択](./media/blob-event-quickstart-portal/select-container.png)

1. ファイルをアップロードするには、**[アップロード]** を選択します。

   ![[アップロード] を選択する](./media/blob-event-quickstart-portal/upload-file.png)

1. テスト ファイルを参照し、それをアップロードします。

1. 以上でイベントがトリガーされ、そのメッセージが、Event Grid によってサブスクライブ時に構成したエンドポイントに送信されました。 Web アプリを表示して、BLOB 作成イベントが受信されたことを確認します。 

  ```json
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/eventgroup/providers/Microsoft.Storage/storageAccounts/demoblob0625",
    "subject": "/blobServices/default/containers/eventcontainer/blobs/testfile.txt",
    "eventType": "Microsoft.Storage.BlobCreated",
    "eventTime": "2018-06-25T22:50:41.1823131Z",
    "id": "89a2f9da-c01e-00bb-13d6-0c599506e4e3",
    "data": {
      "api": "PutBlockList",
      "clientRequestId": "41341a9b-e977-4a91-9000-c64125039047",
      "requestId": "89a2f9da-c01e-00bb-13d6-0c5995000000",
      "eTag": "0x8D5DAEE13C8F9ED",
      "contentType": "text/plain",
      "contentLength": 4,
      "blobType": "BlockBlob",
      "url": "https://demoblob0625.blob.core.windows.net/eventcontainer/testfile.txt",
      "sequencer": "00000000000000000000000000001C24000000000004712b",
      "storageDiagnostics": {
        "batchId": "ef633252-32fd-464b-8f5a-0d10d68885e6"
      }
    },
    "dataVersion": "",
    "metadataVersion": "1"
  }
  ```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

引き続きこのイベントを使用する場合は、この記事で作成したリソースをクリーンアップしないでください。 それ以外の場合は、この記事で作成したリソースを削除してください。

リソース グループを選択し、**[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次の手順

カスタム トピックを作成し、イベントをサブスクライブする方法がわかったら、Event Grid でできることについて、さらに情報を収集しましょう。

- [Event Grid について](overview.md)
- [Blob Storage のイベントをカスタム Web エンドポイントにルーティングする](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Azure Event Grid と Logic Apps で仮想マシンの変更を監視する](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [ビッグ データをデータ ウェアハウスにストリーミングする](event-grid-event-hubs-integration.md)
