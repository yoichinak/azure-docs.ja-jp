---
title: Jupyter をローカルにインストールして Azure HDInsight の Spark に接続する
description: Jupyter Notebook をローカル コンピューターにインストールして Apache Spark クラスターに接続する方法を説明します。
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: jasonh
ms.openlocfilehash: 5e1a089f24a3223220b703bd4225e2750c7cae72
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39619245"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Jupyter Notebook をコンピューターにインストールして HDInsight の Apache Spark に接続する

この記事では、カスタム PySpark (Python の場合) カーネルと Spark (Scala の場合) カーネル、Spark マジックと共に Jupyter Notebook をインストールし、HDInsight クラスターにノートブックを接続する方法について説明します。 さまざまな理由から、Jupyter をローカル コンピューターにインストールすることが必要になるケースがあります。またローカル コンピューターへのインストールには、いくつかの課題もあります。 この点について詳しくは、この記事の最後のセクション「[Jupyter をローカル コンピューターにインストールする理由](#why-should-i-install-jupyter-on-my-computer)」を参照してください。

Jupyter と Spark マジックは大きく 3 つの手順でコンピューターにインストールします。

* Jupyter Notebook をインストールする
* PySpark カーネルと Spark カーネルおよび Spark マジックをインストールする
* HDInsight 上の Spark クラスターにアクセスするように Spark マジックを構成する

HDInsight クラスターの Jupyter Notebook で使用できるカスタム カーネルと Spark マジックの詳細については、 [HDInsight の Apache Spark Linux クラスターと Jupyter Notebook で使用可能なカーネル](apache-spark-jupyter-notebook-kernels.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件
ここに記載したのは、Jupyter のインストールに関する前提条件ではありません。 Jupyter Notebook のインストール後にノートブックを HDInsight クラスターに接続するための前提条件です。

* Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
* HDInsight での Apache Spark クラスター。 手順については、「 [Create Apache Spark clusters in Azure HDInsight (Azure HDInsight での Apache Spark クラスターの作成)](apache-spark-jupyter-spark-sql.md)」を参照してください。

## <a name="install-jupyter-notebook-on-your-computer"></a>Jupyter Notebook をコンピューターにインストールする

Jupyter Notebook をインストールする前に Python をインストールする必要があります。 Python と Jupyter はどちらも、[Anaconda ディストリビューション](https://www.continuum.io/downloads)に同梱されています。 Anaconda をインストールすると、Python のディストリビューションがインストールされます。 Jupyter 環境は、Anaconda のインストール後に適切なコマンドを実行して追加します。

1. ご使用のプラットフォーム用の [Anaconda インストーラー](https://www.continuum.io/downloads) をダウンロードし、セットアップ プログラムを実行します。 セットアップ ウィザードを実行する過程で、Anaconda を PATH 変数に追加するためのオプションを忘れずに選択してください。
1. 次のコマンドを実行して Jupyter をインストールします。

        conda install jupyter

    Jupyter のインストールについて詳しくは、[Anaconda を使用した Jupyter のインストール](http://jupyter.readthedocs.io/en/latest/install.html)に関するページをご覧ください。

## <a name="install-the-kernels-and-spark-magic"></a>カーネルと Spark マジックをインストールする

Spark マジック、PySpark カーネル、Spark カーネルのインストール方法については、GitHub の [sparkmagic ドキュメント](https://github.com/jupyter-incubator/sparkmagic#installation)で説明されているインストール手順を参照してください。 Spark マジックのドキュメントでは、最初の手順として Spark マジックをインストールするように求められます。 このリンク先のページにある最初の手順を、接続する HDInsight クラスターのバージョンに応じて以下のコマンドに置き換えてください。 その後、Spark マジックのドキュメントにある残りの手順を実行します。 異なるカーネルをインストールする必要がある場合は、Spark マジックのインストール方法を説明したセクションの手順 3. を実行してください。

* クラスター v3.4 の場合は、`pip install sparkmagic==0.2.3` を実行して sparkmagic 0.2.3 をインストールします。

* クラスター v3.5 と v3.6 の場合は、`pip install sparkmagic==0.11.2` を実行して sparkmagic 0.11.2 をインストールします。

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>HDInsight の Spark クラスターに接続するように Spark マジックを構成する

このセクションでは、先ほどインストールした Spark マジックに対し、Apache Spark クラスターに接続するための構成を行います。Apache Spark クラスターは、あらかじめ Azure HDInsight に作成しておく必要があります。

1. Jupyter の構成情報は通常、ユーザーの home ディレクトリに格納されます。 home ディレクトリは、任意の OS プラットフォームで以降に示したコマンドを入力して検索できます。

    Python シェルを起動します。 コマンド ウィンドウで次のように入力します。

        python

    Python シェルで、次のコマンドを入力して home ディレクトリを特定します。

        import os
        print(os.path.expanduser('~'))

1. home ディレクトリに移動して **.sparkmagic** というフォルダーを作成します (まだ存在しない場合)。
1. このフォルダーに **config.json** というファイルを作成し、次の JSON スニペットを追加します。

        {
          "kernel_python_credentials" : {
            "username": "{USERNAME}",
            "base64_password": "{BASE64ENCODEDPASSWORD}",
            "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
          },
          "kernel_scala_credentials" : {
            "username": "{USERNAME}",
            "base64_password": "{BASE64ENCODEDPASSWORD}",
            "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
          }
        }

1. **{USERNAME}****{CLUSTERDNSNAME}****{BASE64ENCODEDPASSWORD}** は、それぞれ適切な値に置き換えてください。 base64 エンコード パスワードは、ご利用のプログラミング言語のユーティリティまたはオンライン ユーティリティを使用して、実際のパスワードから生成してください。

1. `config.json` で右側のハートビート設定を構成します。 この設定は、前に追加した `kernel_python_credentials` と`kernel_scala_credentials` のスニペットと同じレベルに追加する必要があります。 ハートビート設定を追加する方法と場所の例については、こちらの [ config.json のサンプル](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json)を参照してください。

    * `sparkmagic 0.2.3` (クラスター V3.4) の場合は、次が含まれます。

            "should_heartbeat": true,
            "heartbeat_refresh_seconds": 5,
            "heartbeat_retry_seconds": 1

    * `sparkmagic 0.11.2` (クラスター V3.5 と v3.6) の場合は、次が含まれます。

            "heartbeat_refresh_seconds": 5,
            "livy_server_heartbeat_timeout_seconds": 60,
            "heartbeat_retry_seconds": 1

    >[!TIP]
    >ハートビートが送信され、セッションがリークしないことが確認されます。 コンピューターがスリープ状態になるかシャットダウンすると、ハートビートは送信されず、セッションがクリーンアップされます。 クラスター v3.4 の場合、この動作を無効にするには、Ambari UI から Livy config `livy.server.interactive.heartbeat.timeout` を `0` に設定します。 クラスター v3.5 の場合、上記の 3.5 構成を設定しないと、セッションは削除されません。

1. Jupyter を起動します。 コマンド プロンプトから次のコマンドを使用します。

        jupyter notebook

1. Jupyter Notebook を使用してクラスターに接続できること、カーネルに備わっている Spark マジックを使用できることを確認します。 次の手順に従います。

    a. 新しい Notebook を作成します。 右隅にある **[新規]** をクリックします。 既定のカーネル **Python2** に加え、新たにインストールする 2 つの新しいカーネル (**PySpark** と **Spark**) が確認できます。 **[PySpark]** をクリックします。

    ![Jupyter Notebook のカーネル](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "Jupyter Notebook のカーネル")

    b. 次のコード スニペットを実行します。

        %%sql
        SELECT * FROM hivesampletable LIMIT 5

    出力結果が正しく得られた場合、HDInsight クラスターへの接続テストは完了です。

    >[!TIP]
    >別のクラスターに接続するようにノートブックの構成を更新する必要がある場合は、前の手順 3. を参照して、config.json の一連の値を更新してください。

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Jupyter をローカル コンピューターにインストールする理由
Jupyter をローカル コンピューターにインストールし、HDInsight 上の Spark クラスターに接続するケースとしては、さまざまな理由が考えられます。

* Azure HDInsight の Spark クラスターで Jupyter Notebook が既に使用できる状態になっていても、ご使用のコンピューターに Jupyter をインストールすることで、ノートブックをローカルで作成し、稼働中のクラスターに対してアプリケーションをテストしたうえで、クラスターにノートブックをアップロードするという選択肢が得られます。 ノートブックをクラスターにアップロードする手段としては、クラスターで実行されている Jupyter Notebook を使用してアップロードする方法と、クラスターに関連付けられているストレージ アカウントの /HdiNotebooks フォルダーにそれらを保存する方法とがあります。 クラスターにノートブックを保存する方法の詳細については、 [Jupyter Notebook の格納場所](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)に関するセクションを参照してください。
* ノートブックがローカルで利用できると、アプリケーションの要件に応じて異なる Spark クラスターに接続することができます。
* GitHub を使用してソース管理システムを導入し、ノートブックのバージョン管理を行うことができます。 複数のユーザーが同じノートブックで作業するコラボレーション環境を実現することもできます。
* クラスターをセットアップしなくてもローカルでノートブックを使用できます。 クラスターは、クラスターとの間でノートブックをテストする目的でのみ必要となります。ノートブックや開発環境を手動で管理するうえでクラスターは必要ありません。
* 独自のローカル開発環境の方が、クラスター上の Jupyter 環境よりも構成しやすい場合があります。  リモートのクラスターを構成しなくても、ローカルにインストールされているすべてのソフトウェアを有効活用することができます。

> [!WARNING]
> Jupyter がローカル コンピューターにインストールされている場合、同じ Spark クラスター上で複数のユーザーが同時に同じノートブックを実行できます。 そのような状況では、複数の Livy セッションが作成されます。 問題が発生してデバッグが必要となった場合、それは Livy セッションとユーザーの対応関係を追跡する複雑な作業となります。
>
>

## <a name="seealso"></a>関連項目
* [概要: Azure HDInsight での Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>シナリオ
* [Spark と BI: HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](apache-spark-use-bi-tools.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して HVAC データを基に建物の温度を分析する](apache-spark-ipython-notebook-machine-learning.md)
* [Spark with Machine Learning: Use Spark in HDInsight to predict food inspection results (Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する)](apache-spark-machine-learning-mllib-ipython.md)
* [Website log analysis using Spark in HDInsight (HDInsight での Spark を使用した Web サイト ログ分析)](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>アプリケーションの作成と実行
* [Scala を使用してスタンドアロン アプリケーションを作成する](apache-spark-create-standalone-application.md)
* [Livy を使用して Spark クラスターでジョブをリモートで実行する](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>ツールと拡張機能
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark Scala アプリケーションを作成し、送信する](apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark アプリケーションをリモートでデバッグする](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight の Spark クラスターで Zeppelin Notebook を使用する](apache-spark-zeppelin-notebook.md)
* [HDInsight 用の Spark クラスターの Jupyter Notebook で使用可能なカーネル](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](apache-spark-jupyter-notebook-use-external-packages.md)

### <a name="manage-resources"></a>リソースの管理
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](apache-spark-job-debugging.md)
