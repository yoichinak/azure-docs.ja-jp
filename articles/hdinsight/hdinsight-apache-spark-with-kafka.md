---
title: Kafka に対する Apache Spark ストリーミング - Azure HDInsight
description: Apache Spark を使用して、DStreams による Apache Kafka 内外へのデータのストリームを行う方法について説明します。 この例では、Jupyter ノートブックを使用して HDInsight 上の Spark からデータをストリームします。
keywords: kafka example,kafka zookeeper,spark streaming kafka,spark streaming kafka example
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: jasonh
ms.openlocfilehash: 607ad43f5cae3915b964caf816bd5fb5e3302ddf
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39592977"
---
# <a name="apache-spark-streaming-dstream-example-with-kafka-on-hdinsight"></a>HDInsight 上の Kafka を用いた Apache Spark ストリーミング (DStream) の例

Apache Spark を使用して、DStreams による HDInsight 上の Apache Kafka 内外へのデータのストリームを行う方法について説明します。 この例では、Spark クラスター上で実行する Jupyter ノートブックを使用します。

> [!NOTE]
> このドキュメントの手順では、HDInsight の Spark クラスターと HDInsight の Kafka クラスターの両方を含む Azure リソース グループを作成します。 これらのクラスターは両方とも、Spark クラスターが Kafka クラスターと直接通信できるように、Azure Virtual Network 内に配置します。
>
> このドキュメントの手順を完了したら、余分に課金されないようにするためにクラスターは削除してください。

> [!IMPORTANT]
> この例では DStreams を使用します。DStreams は古い Spark ストリーミング テクノロジです。 新しい Spark ストリーミング機能を使用する例については、[Kafka を使った Spark 構造化ストリーミング](hdinsight-apache-kafka-spark-structured-streaming.md)に関するドキュメントを参照してください。

## <a name="create-the-clusters"></a>クラスターの作成

HDInsight の Apache Kafka では、パブリック インターネットを介した Kafka ブローカーへのアクセスは提供されていません。 Kafka と通信するすべてのものは、Kafka クラスター内のノードと同じ Azure 仮想ネットワークに存在している必要があります。 この例では、Kafka クラスターと Spark クラスターの両方を Azure 仮想ネットワーク内に配置します。 次の図に、クラスター間の通信フローを示します。

![Azure 仮想ネットワークにおける Spark クラスターと Kafka クラスターの図](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Kafka 自体は仮想ネットワーク内の通信に制限されていますが、クラスターの SSH や Ambari などの他のサービスにはインターネット経由でアクセスすることができます。 HDInsight で使用できるパブリック ポートの詳細については、「[HDInsight で使用されるポートと URI](hdinsight-hadoop-port-settings-for-services.md)」を参照してください。

Azure 仮想ネットワーク、Kafka、および Spark クラスターは手動で作成できますが、Azure Resource Manager テンプレートを使用する方が簡単です。 次の手順に従って、Azure 仮想ネットワーク、Kafka クラスター、および Spark クラスターを Azure サブスクリプションにデプロイします。

1. 次のボタンを使用して Azure にサインインし、Azure Portal でテンプレートを開きます。
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Azure Resource Manager テンプレートは、**https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json** にあります。

    > [!WARNING]
    > HDInsight で Kafka の可用性を保証するには、クラスターに少なくとも 3 つのワーカー ノードが必要です。 このテンプレートは、3 つのワーカー ノードが含まれる Kafka クラスターを作成します。

    このテンプレートは、Kafka と Spark の両方の HDInsight 3.6 クラスターを作成します。

2. 以下の情報を使用して、**[カスタム デプロイ]** セクションに各エントリを入力します。
   
    ![HDInsight のカスタム デプロイ](./media/hdinsight-apache-spark-with-kafka/parameters.png)
   
    * **[リソース グループ]**: グループを作成するか、または既存のグループを選択します。 このグループに HDInsight クラスターが含まれます。

    * **[場所]**: 地理的に近い場所を選択します。

    * **[Base Cluster Name] \(ベース クラスター名)**: この値は、Spark クラスターと Kafka クラスターのベース名として使用されます。 たとえば、「**hdi**」と入力すると、__spark-hdi__ という名前の Spark クラスターと、**kafka-hdi** という名前の Kafka クラスターが作成されます。

    * **[Cluster Login User Name] \(クラスター ログイン ユーザー名)**: Spark クラスターと Kafka クラスターの管理者のユーザー名。

    * **[クラスター ログイン パスワード]**: Spark クラスターと Kafka クラスターの管理者のユーザー パスワード。

    * **[SSH ユーザー名]**: Spark クラスターおよび Kafka クラスターの作成に使用する SSHユーザー。

    * **[SSH パスワード]**: Spark クラスターおよび Kafka クラスター用の SSH ユーザーのパスワード。

3. **使用条件**を読み、**[上記の使用条件に同意する]** をオンにします。

4. 最後に、**[ダッシュボードにピン留めする]** をオンにし、**[購入]** をクリックします。 クラスターの作成には約 20 分かかります。

リソースが作成されると、概要ページが表示されます。

![vnet とクラスターのリソース グループ概要](./media/hdinsight-apache-spark-with-kafka/groupblade.png)

> [!IMPORTANT]
> 各 HDInsight クラスターの名前が **spark-BASENAME** および **kafka-BASENAME** であることに注目してください。BASENAME はテンプレートで指定した名前です。 これらの名前は、後の手順でクラスターに接続するときに使用します。

## <a name="use-the-notebooks"></a>ノートブックを使用する

このドキュメントで説明した例のコードは、[https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka) で入手できます。

この例を完了するには、`README.md` の手順に従ってください。

## <a name="delete-the-cluster"></a>クラスターを削除する

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

このドキュメントの手順では両方のクラスターを同じ Azure リソース グループに作成したため、Azure Portal でこのリソース グループを削除するだけで済みます。 グループを削除することにより、このドキュメントに従って作成したすべてのリソース、Azure Virtual Network、クラスターで使用したストレージ アカウントが削除されます。

## <a name="next-steps"></a>次の手順

この例では、Spark を使用して Kafka に対するデータの読み取りと書き込みを行う方法について説明しました。 次のリンクを使用することで、Kafka のその他の活用方法を知ることができます。

* [HDInsight での Apache Kafka の使用](kafka/apache-kafka-get-started.md)
* [MirrorMaker を使用した HDInsight での Kafka のレプリカの作成](kafka/apache-kafka-mirroring.md)
* [HDInsight での Kafka に Apache Storm を使用する](hdinsight-apache-storm-with-kafka.md)

