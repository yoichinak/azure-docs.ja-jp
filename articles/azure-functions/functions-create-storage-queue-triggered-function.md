---
title: キュー メッセージでトリガーされる Azure 関数の作成 | Microsoft Docs
description: Azure Functions を使用して、Azure Storage キューに送信されたメッセージによって呼び出されるサーバーレスの関数を作成します。
services: azure-functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.assetid: 361da2a4-15d1-4903-bdc4-cc4b27fc3ff4
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/28/2018
ms.author: glenga
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 1c11c50dedda84bcb968f84ffbb43b49fbc93ff4
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38586837"
---
# <a name="create-a-function-triggered-by-azure-queue-storage"></a>Azure Queue Storage によってトリガーされる関数の作成

Azure Storage キューにメッセージが送信されたときにトリガーされる関数を作成する方法について説明します。

![ログ内のメッセージを表示します。](./media/functions-create-storage-queue-triggered-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>前提条件

- [Microsoft Azure ストレージ エクスプローラーをダウンロードしてインストールする](http://storageexplorer.com/)。

- Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="create-an-azure-function-app"></a>Azure Function App の作成

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Function App が正常に作成されました。](./media/functions-create-first-azure-function/function-app-create-success.png)

次に、新しい Function App で関数を作成します。

<a name="create-function"></a>

## <a name="create-a-queue-triggered-function"></a>キューによってトリガーされる関数の作成

1. Function App を展開し、**[関数]** の横にある **[+]** ボタンをクリックします。 これが Function App で初めての関数の場合、**[カスタム関数]** を選びます。 関数テンプレートの完全なセットが表示されます。

    ![Azure Portal での関数のクイック スタート ページ](./media/functions-create-storage-queue-triggered-function/add-first-function.png)

2. 検索フィールドに、「`queue`」と入力し、Queue storage トリガー テンプレート用の目的の言語を選択します。

    ![ストレージ キューのトリガー テンプレートを選択します。](./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal.png)

3. 画像の下の表に指定した設定を使用してください。
    ![ストレージ キューによってトリガーされる関数を構成します。](./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal-2.png)
    
    | Setting | 推奨値 | Description |
    |---|---|---|
    | **名前** | Function App 内で一意 | このキューによってトリガーされる関数の名前。 |
    | **キュー名**   | myqueue-items    | ストレージ アカウント内の接続先のキューの名前。 |
    | **ストレージ アカウント接続** | AzureWebJobStorage | Function App によって既に使用されているストレージ アカウント接続を使用するか、新しく作成できます。  |    

3. **[作成]** をクリックして関数を作成します。

次に、Azure Storage アカウントに接続し、**myqueue-items** ストレージ キューを作成します。

## <a name="create-the-queue"></a>キューを作成する

1. 関数で、**[統合]** をクリックし、**[ドキュメント]** を展開して、**[アカウント名]** と **[アカウント キー]** の両方をコピーします。 Azure Storage Explorer でこれらの資格情報を使用して、ストレージ アカウントに接続します。 ストレージ アカウントに既に接続している場合は、手順 4 に進みます。

    ![ストレージ アカウント接続の資格情報を取得します。](./media/functions-create-storage-queue-triggered-function/functions-storage-account-connection.png)

1. [Microsoft Azure Storage Explorer](http://storageexplorer.com/) ツールを実行し、左側の接続アイコンをクリックして、**[Use a storage account name and key] \(ストレージ アカウント名とキーを使用)** を選択し、**[次へ]** をクリックします。

    ![ストレージ アカウント エクスプローラー ツールを実行します。](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-connect-1.png)

1. 手順 1 の **[アカウント名]** と **[アカウント キー]** を入力し、**[次へ]** をクリックし、**[接続]** をクリックします。

    ![ストレージ資格情報を入力し、接続します。](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-connect-2.png)

1. 接続されたストレージ アカウントを展開し、**[キュー]** を右クリックして、**[キューの作成]** をクリックし、「`myqueue-items`」と入力して、Enter キーを押します。

    ![ストレージ キューを作成します。](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-create-queue.png)

これでストレージ キューが作成されたので、キューにメッセージを追加して、関数をテストできます。

## <a name="test-the-function"></a>関数をテストする

1. Azure Portal に戻り、関数を参照して、ページ下部の **[ログ]** を展開して、ログ ストリーミングが一時停止していないことを確認します。

1. Storage Explorer で、ストレージ アカウント、**[キュー]**、**[myqueue-items]** の順に展開し、**[メッセージの追加]** をクリックします。

    ![メッセージをキューに追加します。](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-add-message.png)

1. "Hello World!" メッセージを **[メッセージ テキスト]** に入力し、**[OK]** をクリックします。

1. 数秒間待ってから、関数ログに戻り、新しいメッセージがキューから読み取られていることを確認します。

    ![ログ内のメッセージを表示します。](./media/functions-create-storage-queue-triggered-function/functions-queue-storage-trigger-view-logs.png)

1. Storage Explorer に戻り、**[更新]** をクリックし、メッセージが処理され、キュー内になくなったことを確認します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>次の手順

メッセージがストレージ キューに追加されたときに実行される関数を作成しました。

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Queue Storage トリガーの詳細については、「[Azure Functions における Storage キュー バインド](functions-bindings-storage-queue.md)」を参照してください。
