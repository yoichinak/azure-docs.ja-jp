---
title: Azure API Management での最初の API のインポートと発行 | Microsoft Docs
description: API Management で最初の API をインポートおよび発行する方法を説明します。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: 538977b9057a5699d61d6c2cc44209367e3550e2
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38722849"
---
# <a name="import-and-publish-your-first-api"></a>最初の API のインポートと発行 

このチュートリアルでは、http://conferenceapi.azurewebsites.net?format=json に存在する "OpenAPI の仕様" のバックエンド API をインポートする方法を示します。 このバックエンド API は Microsoft によって提供され、Azure でホストされています。 

バックエンド API が API Management (APIM) にインポートされると、APIM API はバックエンド API のファサードになります。 バックエンド API のインポート時に、ソース API と APIM API は同一です。 APIM を使用すると、バックエンド API に触れることなく必要に応じてファサードをカスタマイズできます。 詳しくは、「[API を変換および保護する](transform-api.md)」をご覧ください。 

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * 最初の API のインポート
> * Azure Portal での API のテスト
> * 開発者ポータルでの API のテスト

![新しい API](./media/api-management-get-started/created-api.png)

## <a name="prerequisites"></a>前提条件

[Azure API Management インスタンスの作成](get-started-create-service-instance.md)に関するクイック スタートを完了します。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>バックエンド API のインポートと発行

このセクションでは、OpenAPI の仕様のバックエンド API をインポートおよび発行する方法を示します。
 
1. **[API Management]** で **[API]** を選びます。
2. 一覧から **[OpenAPI の仕様]** を選択します。

    ![API の作成](./media/api-management-get-started/create-api.png)

    API の値は、作成時に、または後で **[設定]** タブに移動して設定できます。フィールドの横にある赤色の星印は、必須フィールドを示します。

    以下の表の値を使用して、最初の API を作成します。

    |設定|値|説明|
    |---|---|---|
    |**OpenAPI の仕様**|http://conferenceapi.azurewebsites.net?format=json|API を実装しているサービスを参照します。 要求は、API Management によってこのアドレスに転送されます。|
    |**[表示名]**|*Demo Conference API\(デモ会議 API\)*|サービス URL の入力後に Tab キーを押すと、json の内容に基づいてこのフィールドに値が入力されます。 <br/>この名前は開発者ポータルに表示されます。|
    |**名前**|*demo-conference-api*|API の一意の名前を指定します。 <br/>サービス URL の入力後に Tab キーを押すと、json の内容に基づいてこのフィールドに値が入力されます。|
    |**説明**|API の任意の説明を指定します。|サービス URL の入力後に Tab キーを押すと、json の内容に基づいてこのフィールドに値が入力されます。|
    |**[URL スキーム]**|*HTTPS*|API へのアクセスに使用できるプロトコルを決定します。 |
    |**API URL サフィックス**|*conference*|サフィックスは、API Management サービスのベース URL に付加されます。 API Management では API がサフィックスによって識別されるため、サフィックスは、特定の発行者のすべての API で一意である必要があります。|
    |**成果物**|*無制限*|製品には、1 つまたは複数の API が関連付けられています。 1 つの Product に多数の API を追加し、開発者ポータルを通じて開発者に提供できます。 <br/>API の発行は、API に成果物 (この例では "*無制限*") を関連付けることで行います。 この新しい API を成果物に追加するには、成果物名を入力します (後から **[設定]** ページで行うこともできます)。 この手順を複数回繰り返して、API を複数の成果物に追加できます。<br/>API へのアクセス権を取得するためには、まず開発者が成果物をサブスクライブ必要があります。 サブスクライブすると、その成果物の API に適したサブスクリプション キーを受け取ります。 <br/> APIM インスタンスを作成した場合は、既に管理者になっているため、すべての製品をサブスクライブしています。<br/> 各 API Management インスタンスは、**スターター**および**無制限**という 2 つのサンプル成果物を既定で備えています。 |
    |この API をバージョン管理しますか?||バージョン管理について詳しくは、「[API の複数のバージョンを発行する](api-management-get-started-publish-versions.md)」をご覧ください。|
    
    >[!NOTE]
    > API を発行するには、API を成果物に関連付ける必要があります。 この作業は **[設定] ページ**から行うことができます。
    
3. **[作成]** を選択します。

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Azure Portal での新しい APIM API のテスト

Azure Portal には、API の操作を表示およびテストするための便利な環境が用意されており、操作を直接呼び出すことができます。  
1. 前の手順 (**[API]** タブ) で作成した API を選びます。
2. **[テスト]** タブをクリックします。![API をテストする](./media/api-management-get-started/test-api.png)
3. **[GetSpeakers]** をクリックします。
    このページには、クエリ パラメーターのフィールド (この例では何も表示されません) とヘッダーが表示されます。 この API に関連付けられている成果物のサブスクリプション キーの場合、ヘッダーの 1 つは "Ocp-Apim-Subscription-Key" です。 キーが自動的に入力されます。
4. **[送信]** をクリックします。

    バックエンドは **200 OK** といくつかのデータで応答します。

## <a name="call-operation"></a>開発者ポータルから操作を呼び出す

操作を**開発者ポータル**から呼び出して API をテストすることもできます。

1. **開発者ポータル**に移動します。
![開発者ポータル](./media/api-management-get-started/developer-portal.png)

2. **[APIS]** を選択し、**[Demo Conference API]\(デモ会議 API\)**、**[GetSpeakers]** の順にクリックします。
    
    このページには、クエリ パラメーターのフィールド (この例では何も表示されません) とヘッダーが表示されます。 この API に関連付けられている成果物のサブスクリプション キーの場合、ヘッダーの 1 つは "Ocp-Apim-Subscription-Key" です。 APIM インスタンスを作成した場合は、既に管理者になっているので、キーが自動的に入力されます。
3. **[テスト]** をクリックします。
4. **[送信]** をクリックします。
    
    操作が呼び出された後、開発者ポータルに応答が表示されます。  

## <a name="next-steps"> </a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * 最初の API のインポート
> * Azure Portal での API のテスト
> * 開発者ポータルでの API のテスト

次のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [成果物を作成して発行する](api-management-howto-add-products.md)
