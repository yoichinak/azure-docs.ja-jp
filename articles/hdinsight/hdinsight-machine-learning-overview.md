---
title: 機械学習の概要 - Azure HDInsight
description: HDInsight の機械学習オプションについて説明します。
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: jasonh
ms.openlocfilehash: 79c50935d36070b9f6b7cd6dd0af2d09c4a30f68
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39590383"
---
# <a name="machine-learning-on-hdinsight"></a>HDInsight での機械学習

HDInsight ではビッグ データでの機械学習が可能であるため、大量 (数ペタバイト、場合によっては数エクサバイト) の構造化、非構造化、および高速移動データから貴重な洞察を得る機能が提供されます。 HDInsight には、SparkML と MLlib、R、Hive、Microsoft Cognitive Toolkit などのいくつかの機械学習オプションがあります。

## <a name="sparkml-and-mllib"></a>SparkML と MLlib


  [HDInsight Spark](spark/apache-spark-overview.md) は、Azure でホストされる [Spark](http://spark.apache.org/) のサービスであり、ビッグ データ分析を向上させるためのメモリ内処理をサポートする、統合されたオープンソースの並列データ処理フレームワークです。 Spark 処理エンジンは、高速かつ簡単に高度な分析を行うことができるように作成されています。 Spark のメモリ内の分散計算機能により、Machine Learning とグラフ計算に使用される反復的なアルゴリズムに対して、Spark は適切な選択肢となります。 この分散環境にアルゴリズム モデリング機能を提供するスケーラブルな機械学習ライブラリとして、MLlib と SparkML の 2 つがあります。 MLlib には、RDD 上に構築されたオリジナルの API が含まれています。 SparkML は、ML パイプラインを構成するために DataFrames 上に構築されたより高レベルの API を提供する新しいパッケージです。 SparkML はまだ MLlib のすべての機能をサポートしていませんが、Spark の標準の機械学習ライブラリとして MLlib を置き換えています。

Apache Spark 用の Microsoft Machine Learning ライブラリは [MMLSpark](https://github.com/Azure/mmlspark) です。 このライブラリは、Spark 上でのデータ サイエンティストの生産性を高め、実験の速度を向上させ、さらに非常に大規模なデータセットに対してディープ ラーニングを含む最先端の機械学習手法を活用するように設計されています。 文字列のインデックス作成、機械学習アルゴリズムによって予測されるレイアウトへのデータの強制的な移行、特徴ベクトルのアセンブルなどのスケーラブルな ML モデルを構築する場合、MMLSpark は SparkML の低レベルの API の上に 1 つのレイヤーを提供します。 MMLSpark ライブラリはこれらのタスクや、PySpark でモデルを構築するためのその他の一般的なタスクを簡略化します。

## <a name="r"></a>R

[R](https://www.r-project.org/) は現在、世界で最も普及している統計プログラミング言語です。 これは、ユーザー数が 250 万を超え、さらに成長しているコミュニティを抱えるオープンソースのデータ視覚化ツールです。 その増大するユーザー ベースと 8,000 を超える提供パッケージにより、R は、機械学習が必要な多くの企業にとって有望な選択肢です。 大量のデータセットとモデルで使用されるように準備された ML Services を使用して HDInsight クラスターを作成できます。 この機能は、データ サイエンティストや統計学者に、クラスターのセットアップやメンテナンスのオーバーヘッドなしで HDInsight を通してオンデマンドで拡張できる使い慣れた R インターフェイスを提供します。

![R Server での予測のためのトレーニング](./media/hdinsight-machine-learning-overview/r-training.png)

クラスターのエッジ ノードは、クラスターへの接続と R スクリプトの実行に便利な場所です。  また、ScaleR の Hadoop Map Reduce または Spark コンピューティング コンテキストを使用して、クラスターのノード間で R スクリプトを実行するオプションも提供されます。

Spark を使用した HDInsight 上の ML Services により、Spark コンピューティング コンテキストを使用して、クラスターのノード間でトレーニングを並列化できます。 必要に応じて、使用可能なすべてのコアを並列に使用して、エッジ ノード上で直接 R スクリプトを実行できます。 あるいは、クラスター内のすべてのノードにわたって分散された処理を開始するために、エッジ ノードからコードを実行できます。 Spark を使用した HDInsight 上の ML Services ではまた、必要に応じて、オープンソースの R パッケージからの関数の並列化も可能になります。

## <a name="azure-machine-learning-and-hive"></a>Azure Machine Learning と Hive

Azure Machine Learning は、予測分析をモデル化するためのツールだけでなく、予測モデルを、すぐに使用できる Web サービスとしてデプロイするために使用できるフル マネージドのサービスを提供します。 Azure Machine Learning は、予測モデルの作成、テスト、操作可能化、および管理のために使用できる、クラウド内の完全な予測分析ソリューションです。 大規模なアルゴリズム ライブラリの中から選択し、モデルを構築するための Web ベースのスタジオを使用して、ご利用のモデルを簡単に Web サービスとしてデプロイできます。

![Microsoft Azure Machine Learning により高度な分析で Hadoop にアクセスできるようにする](./media/hdinsight-machine-learning-overview/hadoop-azure-ml.png)

[Hive クエリ](../machine-learning/team-data-science-process/create-features-hive.md)を使用して、HDInsight Hadoop クラスター内のデータの特徴を作成します。 *特徴エンジニアリング*は、学習プロセスを容易にする生データの特徴を作成することによって、学習アルゴリズムの予測能力を向上させようとします。 Azure ML から HiveQL クエリを実行し、[データのインポート モジュール](../machine-learning/studio/import-data.md)を使用して、Hive で処理され、Blob Storage に格納されているデータにアクセスできます。

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

[ディープ ラーニング](https://www.microsoft.com/en-us/research/group/dltc/)は、人間の脳の生物学的プロセスから着想を得た、ニューラル ネットワークを使用する機械学習の 1 分野です。 多くの研究者がディープ ラーニングを、人工知能を拡張するための将来性のあるアプローチとして見ています。 ディープ ラーニングの例には、音声言語翻訳機、画像認識システム、および機械推論があります。

ディープ ラーニングにおける独自の機能の進歩に役立つように、Microsoft は無料で、使いやすい、オープンソースの [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) を開発しました。 このツールキットは、ディープ ラーニングを大規模に展開する必要がある世界中の企業や、最新のアルゴリズムと手法に関心がある学生によって、さまざまな Microsoft 製品で使用されています。 

## <a name="see-also"></a>関連項目

### <a name="scenarios"></a>シナリオ

* [Spark と Machine Learning: HDInsight で Spark を使用して HVAC データを基に建物の温度を分析する](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Spark with Machine Learning: Use Spark in HDInsight to predict food inspection results (Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する)](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Mahout を使用して映画の推奨を生成する](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Hive と Azure Machine Learning](../machine-learning/team-data-science-process/create-features-hive.md)
* [Hive と Azure Machine Learning のエンド ツー エンド](../machine-learning/team-data-science-process/hive-walkthrough.md)
* [HDInsight での Spark を使用した機械学習](../machine-learning/team-data-science-process/spark-overview.md)

### <a name="deep-learning-resources"></a>ディープ ラーニングのリソース

* [Spark を使用したディープ ラーニング ツールキット](https://blogs.technet.microsoft.com/machinelearning/2017/04/25/using-microsofts-deep-learning-toolkit-with-spark-on-azure-hdinsight-clusters/)
* [Spark 上で Cognitive Toolkit と Tensorflow を使用した驚異的な並列画像分類](https://blogs.technet.microsoft.com/machinelearning/2017/04/12/embarrassingly-parallel-image-classification-using-cognitive-toolkit-tensorflow-on-azure-hdinsight-spark/)
