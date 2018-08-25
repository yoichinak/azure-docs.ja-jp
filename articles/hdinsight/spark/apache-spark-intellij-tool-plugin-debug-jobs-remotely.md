---
title: 'Azure Toolkit for IntelliJ: HDInsight Spark でアプリケーションをリモートでデバッグする '
description: Azure Toolkit for IntelliJ の HDInsight ツールを使用して、HDInsight クラスター上で VPN を介して実行される Spark アプリケーションをリモートでデバッグする方法について解説します。
services: hdinsight
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.openlocfilehash: d71186dcfd5528de48151804492d8ae51e808a07
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39618922"
---
# <a name="use-azure-toolkit-for-intellij-to-debug-spark-applications-remotely-in-hdinsight-through-vpn"></a>Azure Toolkit for IntelliJ を使用して HDInsight で VPN を介して Spark アプリケーションをリモートでデバッグする

SSH を使用して Spark アプリケーションをリモートでデバッグすることをお勧めします。 その方法については、「[Azure Toolkit for IntelliJ を使用して HDInsight クラスター上で SSH により Spark アプリケーションをリモートでデバッグする](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh)」を参照してください。

この記事では、Azure Toolkit for IntelliJ の HDInsight ツールを使用して HDInsight Spark クラスター上で Spark ジョブを送信し、デスクトップ コンピューターからリモートでデバッグするための詳細な手順を紹介します。 これらの作業を行うために必要な手順の概要は以下のとおりです。

1. サイト間またはポイント対サイトの Azure 仮想ネットワークを作成します。 このドキュメントの手順では、サイト間ネットワークを使用することを想定しています。
1. サイト間仮想ネットワークの一部である HDInsight で Spark クラスターを作成します。
1. クラスターのヘッド ノードとデスクトップの間の接続を確認します。
1. IntelliJ IDEA で Scala アプリケーションを作成し、リモート デバッグ用に構成します。
1. アプリケーションを実行し、デバッグします。

## <a name="prerequisites"></a>前提条件
* **Azure サブスクリプション**。 詳しくは、[Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
* **HDInsight での Apache Spark クラスター**。 手順については、「 [Create Apache Spark clusters in Azure HDInsight (Azure HDInsight での Apache Spark クラスターの作成)](apache-spark-jupyter-spark-sql.md)」を参照してください。
* **Oracle Java Development Kit**。 [Oracle の Web サイト](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)からインストールできます。
* **IntelliJ IDEA**。 この記事では、バージョン 2017.1 を使用します。 [JetBrains の Web サイト](https://www.jetbrains.com/idea/download/)からインストールできます。
* **Azure Toolkit for IntelliJ のHDInsight ツール**。 IntelliJ 用の HDInsight ツールは、Azure Toolkit for IntelliJ に付属しています。 Azure Toolkit をインストールする手順については、[Azure Toolkit for IntelliJ のインストール](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation)に関するページをご覧ください。
* **IntelliJ IDEA から Azure サブスクリプションにサインインします**。 「[Azure Toolkit for IntelliJ を使用して HDInsight クラスター向けの Spark アプリケーションを作成する](apache-spark-intellij-tool-plugin.md)」の手順に従います。
* **例外の回避策**。 リモート デバッグを行うために Windows コンピューター上で Spark Scala アプリケーションを実行しているときに、例外が発生する場合があります。 この例外は [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) に説明があり、Windows に WinUtils.exe ファイルがないことが原因で発生します。 このエラーを回避するには、[実行可能ファイルをダウンロード](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe)して、**C:\WinUtils\bin** のような場所に保存する必要があります。 **HADOOP_HOME** 環境変数を追加し、この変数の値を **C\WinUtils** に設定します。

## <a name="step-1-create-an-azure-virtual-network"></a>手順 1. Azure 仮想ネットワークを作成する
次のリンクの手順に従って Azure 仮想ネットワークを作成し、デスクトップ コンピューターと仮想ネットワークの間の接続を確認します。

* [Azure Portal を使用してサイト間 VPN 接続を持つ VNet を作成する](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [PowerShell を使用してサイト間 VPN 接続を持つ VNet を作成する](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [PowerShell を使用して仮想ネットワークへのポイント対サイト接続を構成する](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="step-2-create-an-hdinsight-spark-cluster"></a>手順 2. HDInsight Spark クラスターを作成する
先ほど作成した Azure 仮想ネットワークに属する Apache Spark クラスターも Azure HDInsight で作成することをお勧めします。 詳しくは、[HDInsight での Linux ベースの Hadoop クラスターの作成](../hdinsight-hadoop-provision-linux-clusters.md)に関するページを参照してください。 オプションの構成の一環として、前の手順で作成した Azure 仮想ネットワークを選択します。

## <a name="step-3-verify-the-connectivity-between-the-cluster-head-node-and-your-desktop"></a>手順 3. クラスターのヘッド ノードとデスクトップの間の接続を確認する
1. ヘッド ノードの IP アドレスを取得します。 クラスターの Ambari UI を開きます。 クラスター ブレードから、**[ダッシュボード]** を選択します。

    ![Ambari で [ダッシュボード] を選択する](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/launch-ambari-ui.png)
1. Ambari UI から、**[Hosts]\(ホスト\)** を選択します。

    ![Ambari で [Hosts]\(ホスト\) を選択する](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/ambari-hosts.png)
1. ヘッド ノード、ワーカー ノード、Zookeeper ノードの一覧が表示されます。 ヘッド ノードには **hn*** プレフィックスが付いています。 最初のヘッド ノードを選択します。

    ![Ambari でヘッド ノードを見つける](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/cluster-headnodes.png)
1. 開いたページの下部にある **[Summary]\(概要\)** ウィンドウから、ヘッド ノードの **IP アドレス**と**ホスト名**をコピーします。

    ![Ambari で IP アドレスを見つける](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/headnode-ip-address.png)
1. ヘッド ノードの IP アドレスとホスト名を、Spark ジョブの実行とリモート デバッグに使用するコンピューターの **hosts** ファイルに追加します。 これで、ホスト名と IP アドレスのどちらを使用してもヘッド ノードと通信できるようになります。

   a. 管理者特権のアクセス許可を使用してメモ帳のファイルを開きます。 **[ファイル]** メニューから **[開く]** を選択し、hosts ファイルの場所を見つけます。 Windows コンピューター上での場所は **C:\Windows\System32\Drivers\etc\hosts** です。

   b. 以下の情報を **hosts** ファイルに追加します。

           # For headnode0
           192.xxx.xx.xx hn0-nitinp
           192.xxx.xx.xx hn0-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net

           # For headnode1
           192.xxx.xx.xx hn1-nitinp
           192.xxx.xx.xx hn1-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
1. HDInsight クラスターで使用されている Azure 仮想ネットワークに接続したコンピューターから、ヘッド ノードに対して IP アドレスとホスト名を使用して ping を実行できることを確認します。
1. [SSH を使用した HDInsight クラスターへの接続](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するセクションの手順に従い、SSH を使用してクラスターのヘッド ノードに接続します。 クラスターのヘッド ノードから、デスクトップ コンピューターの IP アドレスに ping を実行します。 コンピューターに割り当てられた以下の両 IP アドレスへの接続をテストします。

    - ネットワーク接続用の IP アドレス
    - Azure 仮想ネットワーク用の IP アドレス

1. 他のヘッド ノードに対して同じ手順を繰り返します。

## <a name="step-4-create-a-spark-scala-application-by-using-hdinsight-tools-in-azure-toolkit-for-intellij-and-configure-it-for-remote-debugging"></a>手順 4. Azure Toolkit for IntelliJ の HDInsight ツールを使用して Spark Scala アプリケーションを作成し、リモート デバッグ用に構成する
1. IntelliJ IDEA を開き、新しいプロジェクトを作成します。 **[新しいプロジェクト]** ダイアログ ボックスで、次の操作を行います。

    ![IntelliJ IDEA で新しいプロジェクト テンプレートを選択する](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-hdi-scala-app.png)

    a. **[HDInsight]** > **[Spark on HDInsight (Scala)]\(HDInsight の Spark (Scala)\)** を選択します。

    b. **[次へ]** を選択します。
1. 次の **[新しいプロジェクト]** ダイアログ ボックスで以下の手順を実行し、**[Finish]\(完了\)** を選択します。

    - プロジェクト名とプロジェクトの場所を入力します。

    - **[Project SDK]\(プロジェクトのSDK\)** ボックスの一覧で、Spark 2.x クラスターの場合は **Java 1.8** を選択し、Spark 1.x クラスターの場合は **Java 1.7** を選択します。

    - **[Spark version]\(Spark バージョン\)** ボックスの一覧には、Spark SDK と Scala SDK の適切なバージョンが組み合わされて表示されます。 Spark クラスターのバージョンが 2.0 より前の場合は、**Spark 1.x** を選択します。 それ以外の場合は、**Spark 2.x** を選択します。 この例では、**Spark 2.0.2 (Scala 2.11.8)** を使用します。
  
   ![プロジェクト SDK と Spark バージョンを選択する](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-project-details.png)
  
1. Spark プロジェクトでは、アーティファクトが自動的に作成されます。 次の操作を実行して、アーティファクトを表示します。

    a. **[File]\(ファイル\)** メニューの **[Project Structure]\(プロジェクトの構造\)** を選択します。

    b. **[Project Structure]\(プロジェクトの構造)** ダイアログ ボックスで、**[Artifacts]\(アーティファクト)** を選択すると、作成された既定のアーティファクトが表示されます。 プラス記号 (**+**) をクリックして、独自のアーティファクトを作成することもできます。

   ![JAR の作成](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/default-artifact.png)


1. プロジェクトにライブラリを追加します。 ライブラリを追加するには、以下の手順を実行します。

    a. プロジェクト ツリーのプロジェクト名を右クリックし、**[Open Module Settings]\(モジュール設定を開く\)** を選択します。 

    b. **[Project Structure]\(プロジェクトの構造\)** ダイアログ ボックスで、**[Libraries]\(ライブラリ\)**、(**+**) 記号、**[From Maven]\(Maven から\)** の順に選択します。

    ![ライブラリの追加](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/add-library.png)

    c. **[Download Library from Maven Repository (Maven リポジトリからライブラリをダウンロード)]** ダイアログ ボックスで、以下のライブラリを検索して追加します。

   * `org.scalatest:scalatest_2.10:2.2.1`
   * `org.apache.hadoop:hadoop-azure:2.7.1`
1. クラスターのヘッド ノードから `yarn-site.xml` と `core-site.xml` をコピーし、プロジェクトに追加します。 ファイルのコピーには次のコマンドを使用します。 [Cygwin](https://cygwin.com/install.html) を使用して以下の `scp` コマンドを実行し、クラスターのヘッド ノードからファイルをコピーできます。

        scp <ssh user name>@<headnode IP address or host name>://etc/hadoop/conf/core-site.xml .

    クラスター ヘッド ノードの IP アドレスとホスト名をデスクトップ上の hosts ファイルに追加済みであるため、以下のように `scp` コマンドを使用できます。

        scp sshuser@hn0-nitinp:/etc/hadoop/conf/core-site.xml .
        scp sshuser@hn0-nitinp:/etc/hadoop/conf/yarn-site.xml .

    これらのファイルをプロジェクトに追加するには、プロジェクト ツリー内の **/src** フォルダー (例: `<your project directory>\src`) の下にこれらのファイルをコピーします。
1. 以下の変更を行うために `core-site.xml` ファイルを更新します。

   a. 暗号化されたキーを置換します。 `core-site.xml` ファイルには、クラスターに関連付けられたストレージ アカウント用の暗号化されたキーが含まれます。 プロジェクトに追加した `core-site.xml` ファイルで、暗号化されたキーを、既定のストレージ アカウントに関連付けられた実際のストレージ キーに置き換えます。 詳しくは、「[ストレージ アクセス キーの管理](../../storage/common/storage-create-storage-account.md#manage-your-storage-account)」を参照してください。

           <property>
                 <name>fs.azure.account.key.hdistoragecentral.blob.core.windows.net</name>
                 <value>access-key-associated-with-the-account</value>
           </property>
   b. 以下のエントリを `core-site.xml` から削除します。

           <property>
                 <name>fs.azure.account.keyprovider.hdistoragecentral.blob.core.windows.net</name>
                 <value>org.apache.hadoop.fs.azure.ShellDecryptionKeyProvider</value>
           </property>

           <property>
                 <name>fs.azure.shellkeyprovider.script</name>
                 <value>/usr/lib/python2.7/dist-packages/hdinsight_common/decrypt.sh</value>
           </property>

           <property>
                 <name>net.topology.script.file.name</name>
                 <value>/etc/hadoop/conf/topology_script.py</value>
           </property>
   c. ファイルを保存します。
1. アプリケーションに main クラスを追加します。 **Project Explorer** で、**[src]** を右クリックし、**[New]\(新規\)** をポイントして、**[Scala class]\(Scala クラス\)** を選択します。

    ![main クラスを選択する](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code.png)
1. **[Create New Scala Class]\(新規 Scala クラスの作成\)** ダイアログ ボックスで、名前を指定し、**[Kind]\(種類\)** ボックスの一覧で **[Object]\(オブジェクト\)** を選択して、**[OK]** をクリックします。

    ![新しい Scala クラスを作成する](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code-object.png)
1. 以下のコードを `MyClusterAppMain.scala` ファイルに貼り付けます。 このコードは Spark コンテキストを作成し、`SparkSample` オブジェクトから `executeJob` メソッドを開きます。

        import org.apache.spark.{SparkConf, SparkContext}

        object SparkSampleMain {
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("SparkSample")
                                      .set("spark.hadoop.validateOutputSpecs", "false")
            val sc = new SparkContext(conf)

            SparkSample.executeJob(sc,
                                   "wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
                                   "wasb:///HVACOut")
          }
        }

1. 手順 8. と手順 9. を繰り返して、`*SparkSample` という名前の新しい Scala オブジェクトを追加します。 以下のコードをこのクラスに追加します。 このコードは HVAC.csv (すべての HDInsight Spark クラスターで使用可能) からデータを読み取ります。 このコードは、CSV ファイルの 7 番目の列で 1 桁の数字のみが含まれる行を取得し、出力をクラスター用の既定のストレージ コンテナーの下にある **/HVACOut** に書き込みます。

        import org.apache.spark.SparkContext

        object SparkSample {
         def executeJob (sc: SparkContext, input: String, output: String): Unit = {
           val rdd = sc.textFile(input)

           //find the rows which have only one digit in the 7th column in the CSV
           val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

           val s = sc.parallelize(rdd.take(5)).cartesian(rdd).count()
           println(s)

           rdd1.saveAsTextFile(output)
           //rdd1.collect().foreach(println)
         }
        }
1. 手順 8. と手順 9. を繰り返して、`RemoteClusterDebugging` という名前の新しいクラスを追加します。 このクラスは、アプリケーションのデバッグに使用する Spark テスト フレームワークを実装します。 以下のコードを `RemoteClusterDebugging` クラスに追加します。

        import org.apache.spark.{SparkConf, SparkContext}
        import org.scalatest.FunSuite

        class RemoteClusterDebugging extends FunSuite {

         test("Remote run") {
           val conf = new SparkConf().setAppName("SparkSample")
                                     .setMaster("yarn-client")
                                     .set("spark.yarn.am.extraJavaOptions", "-Dhdp.version=2.4")
                                     .set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")
                                     .setJars(Seq("""C:\workspace\IdeaProjects\MyClusterApp\out\artifacts\MyClusterApp_DefaultArtifact\default_artifact.jar"""))
                                     .set("spark.hadoop.validateOutputSpecs", "false")
           val sc = new SparkContext(conf)

           SparkSample.executeJob(sc,
             "wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
             "wasb:///HVACOut")
         }
        }

     注意すべき重要な点がいくつかあります。

      * `.set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")`では、指定されたパスのクラスター ストレージで Spark アセンブリ JAR が使用可能であることを確認します。
      * `setJars` では、アーティファクト JAR が作成される場所を指定します。 通常は `<Your IntelliJ project directory>\out\<project name>_DefaultArtifact\default_artifact.jar` です。
1. `*RemoteClusterDebugging` クラスで `test` キーワードを右クリックし、**[Create RemoteClusterDebugging Configuration]\(RemoteClusterDebugging 構成の作成\)** を選択します。

    ![リモート構成を作成する](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-remote-config.png)

1. **[Create RemoteClusterDebugging Configuration]\(RemoteClusterDebugging 構成の作成\)** ダイアログ ボックスで、構成の名前を指定し、**[Test name]\(テスト名\)** として **[Test kind]\(テストの種類\)** を選択します。 他の値はすべて規定の設定のままにします。 **[Apply]\(適用\)** を選択し、次に **[OK]** を選択します。

    ![構成の詳細を追加する](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/provide-config-value.png)
1. メニュー バーに、**[Remote Run]\(リモート実行\)** の構成のドロップダウン リストが表示されます。

    ![[Remote run]\(リモート実行\) ドロップダウン リスト](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/config-run.png)

## <a name="step-5-run-the-application-in-debug-mode"></a>手順 5. デバッグ モードでアプリケーションを実行する
1. IntelliJ IDEA プロジェクトで、`SparkSample.scala` を開き、`val rdd1` の次にブレークポイントを作成します。 **[Create Breakpoint for]\(ブレークポイントの作成対象\)** ポップアップ メニューで **[line in function executeJob]\(executeJob 関数内の行\)** を選択します。

    ![Add a breakpoint](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-breakpoint.png)
1. アプリケーションを実行するには、**[Remote Run]\(リモート実行\)** の構成のドロップダウンの横にある **[Debug Run]\(デバッグを実行\)** ボタンを選択します。

    ![[Debug Run]\(デバッグを実行\) ボタンを選択する](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-run-mode.png)
1. プログラムの実行がブレークポイントに到達すると、下部のウィンドウに **[Debugger]\(デバッガー\)** タブが表示されます。

    ![[Debugger]\(デバッガー\) タブを表示する](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch.png)
1. ウォッチ式を追加するには、(**+**) アイコンを選択します。

    ![+ アイコンを選択する](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable.png)

    この例では、変数 `rdd1` が作成される前にアプリケーションは停止しています。 このウォッチ式を使用して、変数 `rdd` の最初の 5 行を見ることができます。 **[Enter]** を選択します。

    ![Run the program in debug mode](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable-value.png)

    前の画像からわかるように、実行時に大量のデータを照会し、アプリケーションがどのように進行するかをデバッグできます。 たとえば、前の画像で示されている出力では、出力の最初の行がヘッダーであることがわかります。 この出力に基づいて、必要に応じてヘッダー行をスキップするようにアプリケーション コードを変更できます。
1. この時点で **[Resume Program]\(プログラムの再開\)** アイコンを選択すると、アプリケーションの実行を続けることができます。

    ![[Resume Program]\(プログラムの再開\) を選択する](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-continue-run.png)
1. アプリケーションが正常に完了すると、次のような出力が表示されます。

    ![コンソール出力](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-complete.png)

## <a name="seealso"></a>次のステップ
* [概要: Azure HDInsight での Apache Spark](apache-spark-overview.md)

### <a name="demo"></a>デモ
* Scala プロジェクトの作成 (ビデオ): [Spark Scala アプリケーションの作成](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* リモート デバッグ (ビデオ): [Azure Toolkit for IntelliJ を使用して HDInsight クラスター上で Spark アプリケーションをリモートでデバッグする](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>シナリオ
* [Spark と BI: HDInsight で BI ツールと Spark を使用して対話型データ分析を実行する](apache-spark-use-bi-tools.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して HVAC データを基に建物の温度を分析する](apache-spark-ipython-notebook-machine-learning.md)
* [Spark with Machine Learning: Use Spark in HDInsight to predict food inspection results (Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する)](apache-spark-machine-learning-mllib-ipython.md)
* [Website log analysis using Spark in HDInsight (HDInsight での Spark を使用した Web サイト ログ分析)](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>アプリケーションの作成と実行
* [Scala を使用してスタンドアロン アプリケーションを作成する](../hdinsight-apache-spark-create-standalone-application.md)
* [Livy を使用して Spark クラスターでジョブをリモートで実行する](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>ツールと拡張機能
* [Azure Toolkit for IntelliJ を使用して HDInsight クラスター向けの Spark アプリケーションを作成する](apache-spark-intellij-tool-plugin.md)
* [Azure Toolkit for IntelliJ を使用して SSH を介して Spark アプリケーションをリモートでデバッグする](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Hortonworks Sandbox と IntelliJ 用 HDInsight ツールを使用する](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Azure Toolkit for Eclipse の HDInsight ツールを使用して Spark アプリケーションを作成する](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [HDInsight の Spark クラスターで Zeppelin Notebook を使用する](apache-spark-zeppelin-notebook.md)
* [HDInsight 用の Spark クラスターの Jupyter Notebook で使用可能なカーネル](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>リソースの管理
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](apache-spark-job-debugging.md)
