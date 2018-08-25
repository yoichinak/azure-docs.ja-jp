---
title: PagerDuty で Azure サービス正常性アラートを構成する | Microsoft Docs
description: PagerDuty インスタンスに送られたサービス正常性イベントについて、個人用に設定された通知を取得します。
author: shawntabrizi
manager: scotthit
editor: ''
services: service-health
documentationcenter: service-health
ms.assetid: ''
ms.service: service-health
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: shtabriz
ms.openlocfilehash: 5f6f3f61b5f7a06ac4056499edfb811780838cdc
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39441879"
---
# <a name="configure-service-health-alerts-with-pagerduty"></a>PagerDuty でサービス正常性アラートを構成する

この記事では、webhook を使用して PagerDuty 経由で Azure サービス正常性通知を設定する方法について説明します。 [PagerDuty](https://www.pagerduty.com/) のカスタムの Microsoft Azure 統合型を使用すると、新規または既存の PagerDuty サービスにサービス正常性アラートを簡単に追加できます。

## <a name="creating-a-service-health-integration-url-in-pagerduty"></a>PagerDuty でサービス正常性統合 URL を作成する
1.  [PagerDuty](https://www.pagerduty.com/) アカウントをサインアップ済みであることを確認した後、サインインします。

1.  PagerDuty の **[Services]** セクションに移動します。

    ![PagerDuty の [Services] セクション](./media/webhook-alerts/pagerduty-services-section.png)

1.  **[Add New Service]** を選択するか、設定済みの既存のサービスを開きます。

1.  **[Integration Settings]** で、次のように選択します。

    a. **[Integration Type]**: Microsoft Azure

    b. **[Integration Name]**:\<名前\>

    ![PagerDuty に表示された [Integration Settings]](./media/webhook-alerts/pagerduty-integration-settings.png)

1.  その他の必須フィールドに入力し、**[Add]** を選択します。

1.  この新しい統合を開き、**[Integration URL]** をコピーして保存します。

    ![PagerDuty に表示された [Integration URL]](./media/webhook-alerts/pagerduty-integration-url.png)

## <a name="create-an-alert-using-pagerduty-in-the-azure-portal"></a>Azure Portal で PagerDuty を使用してアラートを作成する
### <a name="for-a-new-action-group"></a>新しいアクション グループの場合:
1. 「[Azure Portal を使用して新しいアクション グループのサービス正常性通知に関するアラートを作成する](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md)」の手順 1 から 8 を実行します。

1. **[アクション]** の一覧で以下を定義します。

    a. **アクションの種類:** *webhook*

    b. **詳細:** 先ほど保存した PagerDuty の **Integration URL**。

    c. **名前:** webhook の名前、別名、または識別子。

1. 完了したら **[保存]** を選択して、アラートを作成します。

### <a name="for-an-existing-action-group"></a>既存のアクション グループの場合:
1. [Azure Portal](https://portal.azure.com/) で、**[モニター]** を選択します。

1. **[設定]** セクションで **[アクション グループ]** を選択します。

1. 編集するアクション グループを見つけて選択します。

1. **[アクション]** の一覧に以下を追加します。

    a. **アクションの種類:** *webhook*

    b. **詳細:** 先ほど保存した PagerDuty の **Integration URL**。

    c. **名前:** webhook の名前、別名、または識別子。

1. 完了したら **[保存]** を選択して、アクション グループを更新します。

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>HTTP POST 要求によって webhook 統合をテストする
1. 送信するサービス正常性のペイロードを作成します。 サービス正常性 webhook ペイロードの例については、「[Azure アクティビティ ログ アラートのための webhook](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md)」を参照してください。

1. 次のような HTTP POST 要求を作成します。

    ```
    POST        https://events.pagerduty.com/integration/<IntegrationKey>/enqueue

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. `202 Accepted` と共に "イベント ID" を含むメッセージを受け取るはずです。

1. [PagerDuty](https://www.pagerduty.com/) に移動して、統合が正常に設定されたことを確認します。

## <a name="next-steps"></a>次の手順
- [既存の問題管理システム用の webhook 通知を構成する](service-health-alert-webhook-guide.md)方法について学習します。
- [アクティビティ ログ アラート webhook スキーマ](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md)を確認します。 
- [サービス正常性の通知](../monitoring-and-diagnostics/monitoring-service-notifications.md)について学習します。
- [アクション グループ](../monitoring-and-diagnostics/monitoring-action-groups.md)について学習します。