---
title: Apache Kafka 用 Azure Event Hubs へのストリーム配信 | Microsoft Docs
description: Kafka プロトコルおよび API を使用して Event Hubs にストリーム配信します。
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2018
ms.author: bahariri
ms.openlocfilehash: ec6061cac7188f3f94fa1ec0bf138b9398387099
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413622"
---
# <a name="stream-into-event-hubs-for-the-apache-kafka"></a>Apache Kafka 用 Event Hubs へのストリーム配信
このクイック スタートでは、プロトコル クライアントを変更したり、独自のクラスターを実行したりせずに、Kafka 対応 Event Hubs にストリーム配信する方法を示します。 プロデューサーとコンシューマーを使用して、アプリケーションの構成変更だけで Kafka 対応 Event Hubs と通信する方法を説明します。 Azure Event Hubs では、[Apache Kafka バージョン 1.0](https://kafka.apache.org/10/documentation.html) がサポートされています。

> [!NOTE]
> このサンプルは [GitHub](https://github.com/Azure/azure-event-hubs) で入手できます。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、次の前提条件を満たしている必要があります。

* Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)を作成してください。
* [Java Development Kit (JDK) 1.7 以降](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Maven バイナリ アーカイブの[ダウンロード](http://maven.apache.org/download.cgi)と[インストール](http://maven.apache.org/install.html)
* [Git](https://www.git-scm.com/)
* [Kafka 対応 Event Hubs 名前空間](event-hubs-create.md)

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Kafka 対応 Event Hubs 名前空間の作成

1. [Azure portal][Azure portal] にサインインし、画面の左上にある **[リソースの作成]** をクリックします。

2. Event Hubs を検索し、以下に示すオプションを選択します。
    
    ![ポータルでの Event Hubs の検索](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. 一意の名前を指定し、名前空間で Kafka を有効にします。 **Create** をクリックしてください。
    
    ![名前空間の作成](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.png)
 
4. 名前空間が作成されたら、**[設定]** タブで **[共有アクセス ポリシー]** をクリックして接続文字列を取得します。

    ![[共有アクセス ポリシー] をクリックする](./media/event-hubs-create/create-event-hub7.png)

5. 既定値の **RootManageSharedAccessKey** を選択することも、新しいポリシーを追加することもできます。 ポリシー名をクリックして、接続文字列をコピーします。 
    
    ![ポリシーの選択](./media/event-hubs-create/create-event-hub8.png)
 
6. この接続文字列を Kafka アプリケーション構成に追加します。

これで、Kafka プロトコルを使用するアプリケーションからイベントを Event Hubs にストリーミングできます。

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Event Hubs で Kafka を使用してメッセージを送受信する

1. [Azure Event Hubs リポジトリ](https://github.com/Azure/azure-event-hubs)を複製します。

2. `azure-event-hubs/samples/kafka/quickstart/producer` に移動します。

3. `src/main/resources/producer.config` で次のようにプロデューサーの構成の詳細を更新します。

    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
4. プロデューサー コードを実行し、Kafka 対応 Event Hubs にストリーム配信します。
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
5. `azure-event-hubs/samples/kafka/quickstart/consumer` に移動します。

6. `src/main/resources/consumer.config` で次のようにコンシューマーの構成の詳細を更新します。
   
    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

7. Kafka クライアントを使用して、Kafka 対応 Event Hubs からコンシューマー コードとプロセスを実行します。

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Event Hubs Kafka クラスターにイベントがある場合は、コンシューマーからそれらの受信を開始します。

## <a name="next-steps"></a>次の手順
この記事では、プロトコル クライアントを変更したり、独自のクラスターを実行したりせずに、Kafka 対応 Event Hubs にストリーム配信する方法を紹介しました。 さらに学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Event Hubs での Kafka MirrorMaker の使用](event-hubs-kafka-mirror-maker-tutorial.md)
