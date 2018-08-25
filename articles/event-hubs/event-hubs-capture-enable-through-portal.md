---
title: ポータルを使用した Azure Event Hubs Capture の有効化 | Microsoft Docs
description: Azure Portal を使用して Event Hubs Capture 機能を有効にします。
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: ff80bc2452c9826a5c51c146a957fddc72d2dbc2
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2018
ms.locfileid: "41919457"
---
# <a name="enable-event-hubs-capture-using-the-azure-portal"></a>Azure Portal を使用して Event Hubs Capture を有効にする

Azure [Event Hubs Capture][capture-overview] を使用すると、[Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) アカウントまたは [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) アカウントを選んで、Event Hubs のストリーミング データを自動的に配布することができます。

[Azure Portal](https://portal.azure.com) を使用して、イベント ハブの作成時に Capture を構成することができます。 データは、Azure [Blob Storage](https://azure.microsoft.com/services/storage/blobs/) コンテナーまたは [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) アカウントにキャプチャすることができます。

詳細については、[Event Hubs Capture の概要][capture-overview]に関するページを参照してください。

## <a name="capture-data-to-an-azure-storage-account"></a>Azure ストレージ アカウントへのデータのキャプチャ  

Capture は、イベント ハブを作成するときに、**[イベント ハブの作成]** ポータル画面の **[オン]** ボタンをクリックすることによって有効にできます。 その後、**[Capture Provider]\(キャプチャ プロバイダー\)** ボックスの **[Azure Storage]** をクリックして、ストレージ アカウントとコンテナーを指定します。 Event Hubs Capture ではストレージによるサービス間サービス認証が使用されるため、ストレージ接続文字列を指定する必要はありません。 リソース ピッカーにより、ストレージ アカウントのリソース URI が自動的に選択されます。 Azure Resource Manager を使用している場合は、この URI を文字列として明示的に指定する必要があります。

既定の時間ウィンドウは 5 分です。 最小値は 1、最大値は 15 です。 **サイズ** ウィンドウの範囲は 10 ～ 500 MB です。

![][1]

## <a name="capture-data-to-an-azure-data-lake-store-account"></a>Azure Data Lake Store アカウントへのデータのキャプチャ

Azure Data Lake Store にデータをキャプチャするには、次の手順で Data Lake Store アカウントを作成し、イベント ハブを作成します。

### <a name="create-an-azure-data-lake-store-account-and-folders"></a>Azure Data Lake Store アカウントとフォルダーの作成

1. 「[Azure Portal で Azure Data Lake Store の使用を開始する](../data-lake-store/data-lake-store-get-started-portal.md)」の手順に従って Data Lake Store アカウントを作成します。
2. 「[Event Hubs にアクセス許可を割り当てる](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs)」セクションの手順に従って、Event Hubs からのデータをキャプチャするフォルダーを Data Lake Store アカウントに作成し、Event Hubs にアクセス許可を割り当てて、Data Lake Store アカウントにデータを書き込むことができるようにします。  

### <a name="create-an-event-hub"></a>イベント ハブの作成

1. イベント ハブは、先ほど作成した Azure Data Lake Store と同じ Azure サブスクリプションに存在する必要がある点に注意してください。 **[イベント ハブの作成]** ポータル ページの **[Capture]\(キャプチャ\)** の **[オン]** ボタンをクリックしてイベント ハブを作成します。 
2. **[イベント ハブの作成]** ポータル ページで、**[Capture Provider]\(キャプチャ プロバイダー\)** ボックスの **[Azure Data Lake Store]** を選択します。
3. **[Data Lake Store の選択]** で、先ほど作成した Data Lake Store アカウントを指定し、作成したデータ フォルダーのパスを **[Data Lake Path]\(Data Lake のパス\)** フィールドに入力します。

    ![][3]

## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>既存のイベント ハブに対する Capture の追加または構成

Capture は、Event Hubs 名前空間内の既存のイベント ハブに対して構成することができます。 既存のイベント ハブで Capture を有効にするか、Capture の設定を変更するには、名前空間をクリックして概要画面を読み込み、Capture の有効化または Capture 設定の変更を行う対象のイベント ハブをクリックします。 最後に、次の図に示すように、開いているページの左側にある **[キャプチャ]** オプションをクリックして設定を編集します。

### <a name="azure-blob-storage"></a>Azure Blob Storage

![][2]

### <a name="azure-data-lake-store"></a>Azure Data Lake Store

![][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>次の手順

- Event Hubs Capture の詳細については、[Event Hubs Capture の概要][capture-overview]に関するページを参照してください。
- Event Hubs Capture は Azure Resource Manager テンプレートを使用して構成することもできます。 詳細については、[Azure Resource Manager テンプレートを使用した Capture の有効化](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)に関するページを参照してください。
- [Azure Portal で Azure Data Lake Store の使用を開始する](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md