---
title: Apache Storm とは - Azure HDInsight
description: Apache Storm を利用すると、データ ストリームをリアルタイムで処理できます。 Azure HDInsight を利用すると、Azure クラウド上に Storm クラスターを簡単に作成できます。 Visual Studio では、C# を使用して Storm ソリューションを作成し、それを HDInsight Storm クラスターにデプロイできます。
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
keywords: Apache Storm のユース ケース,Storm クラスター,Apache Storm とは
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: get-started-article
ms.date: 02/27/2018
ms.author: jasonh
ms.openlocfilehash: e17c1f50fdfb1b34c8a277c26e1e6fa8ba99dd02
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621132"
---
# <a name="what-is-apache-storm-on-azure-hdinsight"></a>Azure HDInsight での Apache Storm とは

[Apache Storm](http://storm.apache.org/) は、分散型でフォールト トレランスに優れたオープンソースの計算システムです。 Storm を使用すると、Hadoop によってデータ ストリームをリアルタイムで処理できます。 また、Storm のソリューションは、最初に正常に処理されなかったデータを再生する機能を備え、保証されたデータ処理を実現します。

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

## <a name="why-use-storm-on-hdinsight"></a>HDInsight で Apache Storm を使用する理由

HDInsight における Storm の機能は次のとおりです。

* __Storm のアップタイムに関して 99% のサービス レベル アグリーメント (SLA)__: 詳細については、[HDInsight の SLA 情報](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/)に関するドキュメントを参照してください。

* 作成中や作成後の Storm クラスターに対してスクリプトを実行することで、簡単にカスタマイズできます。 詳細については、[スクリプト アクションを使った HDInsight クラスターのカスタマイズ](../hdinsight-hadoop-customize-cluster-linux.md)に関する記事を参照してください。

* **さまざまな言語でソリューションを作成**: Storm コンポーネントは、Java、C#、Python など、さまざまな言語で作成することができます。

    * C# トポロジの開発、管理、監視を目的として、Visual Studio と HDInsight を連携させることができます。 詳細については、[HDInsight Tools for Visual Studio を使用した C# Storm トポロジの開発](apache-storm-develop-csharp-visual-studio-topology.md)に関する記事を参照してください。

    * Trident Java インターフェイスをサポートします。 メッセージの厳密に 1 回の処理、トランザクションのデータストア永続化、一般的なストリーム分析操作のセットをサポートする Storm トポロジの作成が可能になります。

* **動的スケーリング**: Storm トポロジの実行に影響を与えることなく、worker ノードを追加または削除することができます。

    > [!NOTE]
    > スケーリング操作を通じて追加された新しいノードを利用するためには、実行中のトポロジを非アクティブ化したり再アクティブ化したりする必要があります。

* **さまざまな Azure サービスを使ってストリーミング パイプラインを作成**: HDInsight の Storm は、Event Hubs、SQL Database、Azure Storage、Azure Data Lake Store など、他の Azure サービスと連携します。

    Azure サービスと連携するソリューションの例については、[HDInsight の Storm を使用して Event Hubs からのイベントを処理する方法](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/)に関するページを参照してください。

リアルタイム分析ソリューションに Apache Storm を利用している企業の一覧については、[Apache Storm を使用している企業](https://storm.apache.org/documentation/Powered-By.html)に関するページを参照してください。

Storm の使用方法については、[HDInsight での Storm の使用][gettingstarted]に関する記事を参照してください。

## <a name="how-does-storm-work"></a>Storm のしくみ

Storm では、一般的な MapReduce ジョブではなく、トポロジを実行します。 Storm トポロジは、有向非巡回グラフ (DAG) に配置された複数のコンポーネントで構成されます。 データはグラフ内のコンポーネント間を流れます。 各コンポーネントでは 1 つ以上のデータ ストリームを使用し、必要に応じて 1 つ以上のストリームを出力できます。 次の図は、基本的なワード カウント トポロジ内のコンポーネント間でデータがどのように流れるかを示しています。

![Storm トポロジでのコンポーネントの配置の例](./media/apache-storm-overview/example-apache-storm-topology-diagram.png)

* スパウト コンポーネントは、データをトポロジに取り込みます。 1 つ以上のストリームをこのトポロジに出力します。

* ボルト コンポーネントは、スパウトまたはその他のボルトから出力されたストリームを使用します。 ボルトは、必要に応じてストリームをトポロジに出力できます。 また、HDFS、Kafka、HBase などの外部サービスまたはストレージへのデータの書き込みも行います。

## <a name="reliability"></a>信頼性

Apache Storm では、データ分析が多数のノードにまたがる場合でも、各受信メッセージが必ず完全に処理されることを保証しています。

Nimbus ノードは Hadoop JobTracker に同様の機能を提供し、Zookeeper を介して、タスクをクラスターの他のノードに割り当てます。 Zookeeper ノードは、クラスターに調整を提供し、Nimbus と worker ノードの Supervisor 処理間の通信を容易にします。 処理中のノードの 1 つがダウンした場合、Nimbus ノードに通知され、タスクと関連付けられているデータが別のノードに割り当てられます。

Apache Storm クラスターの既定の構成では、Nimbus ノードは 1 つだけです。 HDInsight の Storm では、2 つの Nimbus ノードが提供されます。 プライマリ ノードで障害が発生すると、Storm クラスターはプライマリ ノードの復旧中にセカンダリ ノードに切り替わります。 次の図は、HDInsight 上の Storm に使用されるタスク フローの構成を示しています。

![Nimbus、Zookeeper、スーパーバイザのダイアグラム](./media/apache-storm-overview/nimbus.png)

## <a name="ease-of-creation"></a>作成のしやすさ

HDInsight 上に新しい Storm クラスターを数分で作成できます。 Storm クラスターの作成の詳細については、[HDInsight での Storm の使用](apache-storm-tutorial-get-started-linux.md)に関する記事を参照してください。

## <a name="ease-of-use"></a>使いやすさ

* __Secure Shell (SSH) 接続__: SSH を使用してインターネット経由で Storm クラスターのヘッド ノードにアクセスすることができます。 クラスターには、SSH を使用して直接コマンドを実行できます。

  詳細については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

* __Web 接続__: すべての HDInsight クラスターで Ambari Web UI が提供されます。 Ambari Web UI を使用すれば、クラスター上のサービスの監視、構成、管理を簡単に行うことができます。 Storm クラスターには Storm UI も用意されています。 Storm UI を使用すれば、お使いのブラウザーから Storm トポロジの実行を監視および管理できます。

  詳細については、[Ambari Web UI を使用した HDInsight の管理](../hdinsight-hadoop-manage-ambari.md)に関する記事と、[Storm UI を使用した監視と管理](apache-storm-deploy-monitor-topology-linux.md#monitor-and-manage-storm-ui)に関する記事を参照してください。

* __Azure PowerShell と Azure CLI__: PowerShell と CLI はいずれも、HDInsight やその他の Azure サービスを操作するためにクライアント システムから使用できるコマンドライン ユーティリティです。

* __Visual Studio 統合__: Azure Data Lake Tools for Visual Studio には、SCP.NET フレームワークを使用して C# Storm トポロジを作成するためのプロジェクト テンプレートが含まれています。 また、Data Lake Tools には、HDInsight の Storm によるソリューションのデプロイ、監視、管理のためのツールも用意されています。

  詳細については、[HDInsight Tools for Visual Studio を使用した C# Storm トポロジの開発](apache-storm-develop-csharp-visual-studio-topology.md)に関する記事を参照してください。

## <a name="integration-with-other-azure-services"></a>その他の Azure サービスとの統合

* __Azure Data Lake Store__: Storm クラスターで Data Lake Store を使用する例については、[HDInsight の Apache Storm による Azure Data Lake Store の使用](apache-storm-write-data-lake-store.md)に関する記事を参照してください。

* __Event Hubs__: Storm クラスターで Event Hubs を使用する例については、次の例を参照してください。

    * [Process events from Azure Event Hubs with Storm on HDInsight (Java) (HDInsight で Storm を使用して Azure Event Hubs のイベントを処理する (Java))](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/)

    * [HDInsight で Storm を使用して Azure Event Hubs のイベントを処理する (C#)](apache-storm-develop-csharp-event-hub-topology.md)

* __SQL Database____Cosmos DB__、__Event Hubs__、__HBase__: Data Lake Tools for Visual Studio にテンプレート例が含まれています。 詳細については、[HDInsight の Storm 向けの C# トポロジの開発](apache-storm-develop-csharp-visual-studio-topology.md)に関する記事を参照してください。

## <a name="support"></a>サポート

HDInsight の Storm には、完全なエンタープライズレベルの継続性サポートが付属します。 HDInsight の Storm では、99.9% の SLA も保証されています。 つまり、Microsoft では、Storm クラスターが 99.9% 以上の時間にわたって外部に接続されることを保証しています。

詳細については、「[Azure のサポート](https://azure.microsoft.com/support/options/)」を参照してください。

## <a name="apache-storm-use-cases"></a>Apache Storm のユース ケース

以下に、HDInsight で Storm を使用する可能性のある一般的なシナリオをいくつか示します。

* モノのインターネット(IoT)
* 不正行為の検出
* ソーシャル分析
* 抽出、変換、読み込み (ETL: Extraction、Transformation、Loading)
* ネットワーク監視
* Search
* Mobile Engagement

実際のシナリオの詳細については、[企業での Storm の使用事例](https://storm.apache.org/documentation/Powered-By.html)に関するページを参照してください。

## <a name="development"></a>開発

.NET 開発者は、Data Lake Tools for Visual Studio を使い、トポロジの設計と実装を C# で行うことができます。 Java と C# のコンポーネントを使用するハイブリッド トポロジを作成することもできます。

詳細については、[Visual Studio を使用した HDInsight での Storm の C# トポロジの開発](apache-storm-develop-csharp-visual-studio-topology.md)に関する記事を参照してください。

任意の IDE を使用して、Java ソリューションを開発することもできます。 詳細については、[HDInsight での Storm の C# トポロジの開発](apache-storm-develop-java-topology.md)に関する記事を参照してください。

Storm コンポーネントの開発には、Python も使用できます。 詳細については、[HDInsight での Python を使用した Storm トポロジの開発](apache-storm-develop-python-topology.md)に関する記事を参照してください。

## <a name="common-development-patterns"></a>一般的な開発パターン

### <a name="guaranteed-message-processing"></a>メッセージの処理の保証

Apache Storm では、さまざまなレベルでメッセージの処理が保証されています。 たとえば、基本的な Storm アプリケーションは "最低 1 回" の処理を保証できますが、Trident は "厳密に 1 回" の処理を保証できます。

詳細については、apache.org の「 [Guarantees on data processing (データ処理の保証)](https://storm.apache.org/about/guarantees-data-processing.html) 」をご覧ください。

### <a name="ibasicbolt"></a>IBasicBolt

入力タプルを読み込み、0 個以上のタプルを発行し、実行メソッドの終了直後に入力タプルを確認するというパターンが一般的です。 Storm には、このパターンを自動化する [IBasicBolt](http://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/IBasicBolt.html) インターフェイスが用意されています。

### <a name="joins"></a>結合

データ ストリームの結合方法は、アプリケーションによって異なります。 たとえば、複数のストリームの各タプルを 1 つの新しいストリームに結合したり、特定のウィンドウに対してのみタプルのバッチを結合したりする場合があります。 いずれの方法でも、結合は [fieldsGrouping](http://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) を使用して行われます。 フィールドのグループ化は、ボルトに対してタプルがどのようにルーティングされるかを定義する方法です。

次の Java の例では、fieldsGrouping は、MyJoiner ボルトへの、コンポーネント "1"、"2"、"3" から発生したタプルのルーティングに使用されています。

    builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));

### <a name="batches"></a>バッチ

Apache Storm は、"tick タプル" と呼ばれる内部の時間調整メカニズムを備えています。 トポロジ内で tick タプルが出力される頻度を設定できます。

C# コンポーネントからの tick タプルの使用例については、[PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java) を参照してください。

### <a name="caches"></a>キャッシュ

メモリ内キャッシュは、頻繁に使用されるアセットをメモリ内に保持することで、処理速度を上げるためのメカニズムとしてよく使用されます。 トポロジは複数のノード (また各ノード内の複数のプロセス) にまたがって分散されるため、[fieldsGrouping](http://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) の使用を検討してください。 `fieldsGrouping` を使用すると、キャッシュの検索に使用されるフィールドを含んだタプルが常に同じプロセスにルーティングされます。 このグループ化の機能により、プロセス間でのキャッシュ エントリの重複が回避されます。

### <a name="stream-top-n"></a>"トップ N" のストリーミング

トポロジが "トップ N" の値の計算に依存する場合、トップ N の値を並行して計算する必要があります。 その後、これらの計算の出力をグローバル値に結合してください。 この操作を実行するには、[fieldsGrouping](http://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) を使用して並列処理用のフィールド別にルーティングを行います。 その後は、トップ N 値をグローバルに決定するボルトにルーティングできます。

"トップ N" の値の計算例については、[RollingTopWords](https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/org/apache/storm/starter/RollingTopWords.java) の例を参照してください。

## <a name="logging"></a>ログの記録

Storm では、Apache Log4j を使用して情報をログに記録します。 既定では大量のデータがログに記録されるため、情報を調べるのが困難になる可能性があります。 Storm トポロジの一部にログの構成ファイルを含めることにより、ログ記録の動作を制御することができます。

ログ記録を構成する方法を示すトポロジの例については、HDInsight で Storm を使用した [Java ベースの WordCount](apache-storm-develop-java-topology.md) の例を参照してください。

## <a name="next-steps"></a>次の手順

HDInsight 上の Storm を使用したリアルタイム分析ソリューションの詳細について学習します。

* [HDInsight での Apache Storm の使用][gettingstarted]
* [HDInsight での Apache Storm のトポロジ例](apache-storm-example-topology.md)

[stormtrident]: https://storm.apache.org/documentation/Trident-API-Overview.html
[samoa]: http://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.apache.org/documentation/Tutorial.html
[gettingstarted]: apache-storm-tutorial-get-started-linux.md
