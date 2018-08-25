---
title: アクティビティ ログ アラートで使用される webhook スキーマについて理解する
description: アクティビティ ログ アラートがアクティブになったときに webhook URL に投稿される JSON のスキーマについて説明します。
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: johnkem
ms.component: alerts
ms.openlocfilehash: 3935da72cb747a642ee1f360dc5318fc2d34e763
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263241"
---
# <a name="webhooks-for-azure-activity-log-alerts"></a>Azure アクティビティ ログ アラートのための webhook
アクション グループの定義の一部として、アクティビティ ログ アラート通知を受信するように webhook エンドポイントを構成することができます。 webhook を使用すると、後処理やカスタム アクションのために、これらの通知を他のシステムにルーティングすることができます。 この記事では、webhook に対する HTTP POST のペイロードの概要について説明します。

アクティビティ ログ アラートについて詳しくは、「[アクティビティ ログ アラートの作成](monitoring-activity-log-alerts.md)」をご覧ください。

アクション グループについて詳しくは、[アクション グループを作成](monitoring-action-groups.md)する方法をご覧ください。

## <a name="authenticate-the-webhook"></a>webhook の認証
webhook は、認証のためにトークンベースの承認を使用することもできます。 webhook URI は `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`のようなトークン ID を使用して保存されます。

## <a name="payload-schema"></a>ペイロード スキーマ
POST 操作に含まれる JSON ペイロードは、ペイロードの data.context.activityLog.eventSource フィールドによって異なります。

### <a name="common"></a>一般
```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Operation",
                "correlationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "eventSource": "Administrative",
                "eventTimestamp": "2017-03-29T15:43:08.0019532+00:00",
                "eventDataId": "8195a56a-85de-4663-943e-1a2bf401ad94",
                "level": "Informational",
                "operationName": "Microsoft.Insights/actionGroups/write",
                "operationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "status": "Started",
                "subStatus": "",
                "subscriptionId": "52c65f65-0518-4d37-9719-7dbbfc68c57a",
                "submissionTimestamp": "2017-03-29T15:43:20.3863637+00:00",
                ...
            }
        },
        "properties": {}
    }
}
```
### <a name="administrative"></a>管理
```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "authorization": {
                    "action": "Microsoft.Insights/actionGroups/write",
                    "scope": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions"
                },
                "claims": "{...}",
                "caller": "me@contoso.com",
                "description": "",
                "httpRequest": "{...}",
                "resourceId": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions",
                "resourceGroupName": "CONTOSO-TEST",
                "resourceProviderName": "Microsoft.Insights",
                "resourceType": "Microsoft.Insights/actionGroups"
            }
        },
        "properties": {}
    }
}

```
### <a name="servicehealth"></a>ServiceHealth
```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
    "status": "Activated",
    "context": {
        "activityLog": {
        "channels": "Admin",
        "correlationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
        "description": "Active: Virtual Machines - Australia East",
        "eventSource": "ServiceHealth",
        "eventTimestamp": "2017-10-18T23:49:25.3736084+00:00",
        "eventDataId": "6fa98c0f-334a-b066-1934-1a4b3d929856",
        "level": "Informational",
        "operationName": "Microsoft.ServiceHealth/incident/action",
        "operationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
        "properties": {
            "title": "Virtual Machines - Australia East",
            "service": "Virtual Machines",
            "region": "Australia East",
            "communication": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
            "incidentType": "Incident",
            "trackingId": "0NIH-U2O",
            "impactStartTime": "2017-10-18T02:48:00.0000000Z",
            "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"}],\"ServiceName\":\"Virtual Machines\"}]",
            "defaultLanguageTitle": "Virtual Machines - Australia East",
            "defaultLanguageContent": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
            "stage": "Active",
            "communicationId": "636439673646212912",
            "version": "0.1.1"
        },
        "status": "Active",
        "subscriptionId": "45529734-0ed9-4895-a0df-44b59a5a07f9",
        "submissionTimestamp": "2017-10-18T23:49:28.7864349+00:00"
        }
    },
    "properties": {}
    }
}
```

サービス正常性通知のアクティビティ ログ アラートの特定のスキーマについて詳しくは、「[サービス正常性通知](monitoring-service-notifications.md)」をご覧ください。 また、[既存の問題管理ソリューションでサービス正常性の webhook 通知を構成する](../service-health/service-health-alert-webhook-guide.md)方法についてご確認ください。

その他のすべてのアクティビティ ログ アラートの特定のスキーマについて詳しくは、[Azure アクティビティ ログの概要](monitoring-overview-activity-logs.md)に関するページをご覧ください。

| 要素名 | 説明 |
| --- | --- |
| status |メトリック アラートで使用されます。 アクティビティ ログ アラートでは常に "activated" に設定されます。 |
| context |イベントのコンテキスト。 |
| resourceProviderName |影響を受けるリソースのリソース プロバイダー。 |
| conditionType |常に "Event" です。 |
| name |アラート ルールの名前。 |
| id |アラートのリソース ID。 |
| description  |アラートの作成時に設定したアラートの説明。 |
| subscriptionId |Azure サブスクリプション ID。 |
| timestamp |要求を処理した Azure サービスによってイベントが生成された時刻。 |
| resourceId |影響を受けるリソースのリソース ID。 |
| resourceGroupName |影響を受けるリソースのリソース グループの名前。 |
| properties |イベントの詳細を含む `<Key, Value>` ペア (つまり、`Dictionary<String, String>`) のセット。 |
| event |イベントに関するメタデータを含む要素。 |
| authorization |イベントのロールベースのアクセス制御プロパティ。 これらのプロパティには通常、action、role、scope が含まれます。 |
| category |イベントのカテゴリ。 サポートされる値は Administrative、Alert、Security、ServiceHealth、Recommendation です。 |
| caller |操作、UPN 要求、または可用性に基づく SPN 要求を実行したユーザーの電子メール アドレス。 一部のシステム呼び出しでは、null の場合があります。 |
| correlationId |通常は GUID (文字列形式)。 correlationId を含むイベントは、より大きな同じアクションに属し、通常は correlationId を共有します。 |
| eventDescription |イベントを説明する静的テキスト。 |
| eventDataId |イベントの一意識別子。 |
| eventSource |イベントを生成した Azure サービスまたはインフラストラクチャの名前。 |
| httpRequest |要求には通常、clientRequestId、clientIpAddress、および HTTP メソッド (たとえば PUT) が含まれます。 |
| level |Critical、Error、Warning、または Informational のいずれかの値。 |
| operationId |通常、単一の操作に対応する複数のイベントで共有される GUID。 |
| operationName |操作の名前。 |
| properties |イベントのプロパティ。 |
| status |文字列 をオンにします。 操作の状態。 一般的な値は Started、In Progress、Succeeded、Failed、Active、Resolved です。 |
| subStatus |通常、対応する REST 呼び出しの HTTP 状態コードが含まれます。 また、subStatus を説明する他の文字列を含めることもできます。 一般的な subStatus の値は、OK (HTTP 状態コード: 200)、Created (HTTP 状態コード: 201)、Accepted (HTTP 状態コード: 202)、No Content (HTTP 状態コード: 204)、Bad Request (HTTP 状態コード: 400)、Not Found (HTTP 状態コード: 404)、Conflict (HTTP 状態コード: 409)、Internal Server Error (HTTP 状態コード: 500)、Service Unavailable (HTTP 状態コード: 503)、Gateway Timeout (HTTP 状態コード: 504) です。 |

## <a name="next-steps"></a>次の手順
* [アクティビティ ログについて詳しく学習します](monitoring-overview-activity-logs.md)。
* [Azure アラートで Azure Automation スクリプト (Runbook) を実行します](http://go.microsoft.com/fwlink/?LinkId=627081)。
* [ロジック アプリを使用して、Azure アラートから Twilio 経由で SMS を送信します](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)。 この例はメトリック アラートのためのものですが、変更を加えてアクティビティ ログ アラートで使用できます。
* [ロジック アプリを使用して、Azure アラートから Slack メッセージを送信します](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)。 この例はメトリック アラートのためのものですが、変更を加えてアクティビティ ログ アラートで使用できます。
* [ロジック アプリを使用して、Azure アラートから Azure キューにメッセージを送信します](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)。 この例はメトリック アラートのためのものですが、変更を加えてアクティビティ ログ アラートで使用できます。
