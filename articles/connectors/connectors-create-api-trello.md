---
title: Azure Logic Apps での Trello コネクタ | Microsoft Docs
description: Azure App Service を使用してロジック アプリを作成します。 Trello を使用すると、職場でも家庭でも、すべてのプロジェクトを把握できます。  プロジェクトを管理したり何かを整理するための、簡単で、自由度が高く、柔軟性があり、視覚化された方法です。  Trello に接続してボード、一覧、およびカードを管理する
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: fe7a4377-5c24-4f72-ab1a-6d9d23e8d895
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 8f7fefde5f35c65d707ad96a475935dd0d791259
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296188"
---
# <a name="get-started-with-the-trello-connector"></a>Trello コネクタの使用
Trello を使用すると、職場でも家庭でも、すべてのプロジェクトを把握できます。  プロジェクトを管理したり何かを整理するための、簡単で、自由度が高く、柔軟性があり、視覚化された方法です。  Trello に接続すると、ボード、一覧、およびカードを管理できます。

まず、ロジック アプリを作成します。[ロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する記事を参照してください。

## <a name="create-a-connection-to-trello"></a>Trello への接続を作成する
Trello を使用してロジック アプリを作成するには、まず**接続**を作成してから、次のプロパティの詳細を指定する必要があります。

| プロパティ | 必須 | 説明 |
| --- | --- | --- |
| トークン |はい |Trello 資格情報を提供します |

接続を作成したら、その接続を使用してアクションを実行し、トリガーをリッスンできます。

> [!INCLUDE [Steps to create a connection to Trello](../../includes/connectors-create-api-trello.md)]
> 

## <a name="connector-specific-details"></a>コネクタ固有の詳細

[コネクタの詳細](/connectors/trello/)に関するページに、Swagger で定義されているトリガーとアクション、さらに制限が記載されています。

## <a name="more-connectors"></a>その他のコネクタ
[API リスト](apis-list.md)に戻ります。