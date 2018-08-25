---
title: HBase と Phoenix のバックアップとレプリケーションの設定 - Azure HDInsight
description: HBase と Phoenix のバックアップとレプリケーションを設定します。
services: hdinsight
author: ashishthaps
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: b9557dcd34ad59ae50240b76ae75df4ef3f39a5b
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39592960"
---
# <a name="set-up-backup-and-replication-for-hbase-and-phoenix-on-hdinsight"></a>HDInsight で HBase と Phoenix のバックアップとレプリケーションを設定する

HBase は、データ損失を防ぐための複数の方法をサポートしています。

* `hbase` フォルダーをコピーする
* エクスポート後にインポートする
* テーブルをコピーする
* スナップショット
* レプリケーション

> [!NOTE]
> Apache Phoenix はそのメタデータを HBase テーブルに保存するため、HBase システム カタログ テーブルのバックアップ時にメタデータがバックアップされます。

以下のセクションでは、これらの各方法の使用シナリオについて説明します。

## <a name="copy-the-hbase-folder"></a>hbase フォルダーをコピーする

この方法を使用すると、テーブルまたは列ファミリのサブセットを選択できなくても、すべての HBase データをコピーします。 後述の方法では、さらに制御しやすくなります。

HDInsight の HBase では、クラスターの作成時に選択された既定のストレージとして Azure Storage Blob または Azure Data Lake Store を使用します。 どちらの場合も、HBase はそのデータ ファイルとメタデータ ファイルを次のパスに保存します。

    /hbase

* Azure Storage アカウントでは、`hbase` フォルダーは BLOB コンテナーのルートにあります。

    ```
    wasbs://<containername>@<accountname>.blob.core.windows.net/hbase
    ```

* Azure Data Lake Store では、クラスターのプロビジョニング時に指定したルート パスに `hbase` フォルダーがあります。 通常、このルート パスには `clusters` フォルダーがあり、HDInsight クラスターにちなんだ名前のサブフォルダーが含まれています。

    ```
    /clusters/<clusterName>/hbase
    ```

いずれの場合も、`hbase` フォルダーには、HBase によってディスクにフラッシュされたすべてのデータが含まれていますが、メモリ内のデータは含まれないことがあります。 このフォルダーを HBase データを正確に表すものとして利用できるようにするには、事前にクラスターをシャットダウンする必要があります。

クラスターを削除した後は、データをそのまま残すか、データを新しい場所にコピーすることができます。

* 現在のストレージの場所を指す新しい HDInsight インスタンスを作成します。 新しいインスタンスは、既存のすべてのデータを使用して作成されます。

* `hbase` フォルダーを別の Azure Storage Blob コンテナーまたは Data Lake Store の場所にコピーしてから、そのデータを含む新しいクラスターを開始します。 Azure Storage の場合は [AzCopy](../../storage/common/storage-use-azcopy.md) を使用し、Data Lake Store の場合は [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md) を使用します。

## <a name="export-then-import"></a>エクスポート後にインポートする

エクスポート元 HDInsight クラスターで、(HBase に付属の) Export ユーティリティを使用して、エクスポート元テーブルのデータを既定の接続ストレージにエクスポートします。 その後、エクスポートしたフォルダーをエクスポート先のストレージの場所にコピーし、インポート先の HDInsight クラスターで Import ユーティリティを実行することができます。

テーブルをエクスポートするには、最初にエクスポート元の HDInsight クラスターのヘッド ノードに SSH で接続してから、次の `hbase` コマンドを実行します。

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>"

テーブルをインポートするには、インポート先の HDInsight クラスターのヘッド ノードに SSH で接続してから、次の `hbase` コマンドを実行します。

    hbase org.apache.hadoop.hbase.mapreduce.Import "<tableName>" "/<path>/<to>/<export>"

既定のストレージまたは接続ストレージ オプションのいずれかへの完全なエクスポート パスを指定します。 たとえば、Azure Storage の場合は次のようになります。

    wasbs://<containername>@<accountname>.blob.core.windows.net/<path>

Azure Data Lake Store の構文は次のとおりです。

    adl://<accountName>.azuredatalakestore.net:443/<path>

この方法では、テーブルレベルの粒度が提供されます。 また、含める行の日付範囲を指定することもできます。これにより、処理を段階的に実行できます。 各日付は、Unix エポック以降のミリ秒数で表されます。

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>" <numberOfVersions> <startTimeInMS> <endTimeInMS>

エクスポートする各行のバージョンの数を指定する必要があることに注意してください。 日付範囲にすべてのバージョンを含めるには、`<numberOfVersions>` を、考えられる最大行バージョンより大きな値 (100000 など) に設定します。

## <a name="copy-tables"></a>テーブルをコピーする

CopyTable ユーティリティでは、コピー元テーブルのデータを行単位で、コピー元と同じスキーマを使用した既存のコピー先テーブルにコピーします。 コピー先テーブルは、同じクラスターにあっても別の HBase クラスターにあってもかまいません。

クラスター内で CopyTable を使用するには、コピー元の HDInsight クラスターのヘッドノードに SSH で接続してから、この `hbase` コマンドを実行します。

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> <srcTableName>

CopyTable を使用して別のクラスターのテーブルにコピーするには、コピー先のクラスターのアドレスを指定した `peer` スイッチを追加します。

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> --peer.adr=<destinationAddress> <srcTableName>

コピー先のアドレスは、次の 3 つの部分で構成されています。

    <destinationAddress> = <ZooKeeperQuorum>:<Port>:<ZnodeParent>

* `<ZooKeeperQuorum>` は ZooKeeper ノードのコンマ区切りリストです。次に例を示します。

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net

* HDInsight の `<Port>` の既定値は 2181 で、`<ZnodeParent>` は `/hbase-unsecure` であるため、完成した `<destinationAddress>` は次のようになります。

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net:2181:/hbase-unsecure

使用している HDInsight クラスターについてこれらの値を取得する方法の詳細については、この記事の「[ZooKeeper クォーラム リストを手動で収集する](#manually-collect-the-zookeeper-quorum-list)」を参照してください。

CopyTable ユーティリティでは、コピーする行の時間範囲を指定するパラメーターと、コピーするテーブル内の列ファミリのサブセットを指定するパラメーターもサポートしています。 CopyTable でサポートされているパラメーターの完全な一覧を表示するには、パラメーターを指定せずに CopyTable を実行してください。

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable

CopyTable は、コピー先テーブルにコピーされる、コピー元テーブルの内容全体をスキャンします。 これにより、CopyTable の実行中は HBase クラスターのパフォーマンスが低下する可能性があります。

> [!NOTE]
> テーブル間でのデータのコピーを自動化するには、GitHub の [Azure HBase Utils](https://github.com/Azure/hbase-utils/tree/master/replication) リポジトリにある `hdi_copy_table.sh` スクリプトを参照してください。

### <a name="manually-collect-the-zookeeper-quorum-list"></a>ZooKeeper クォーラム リストを手動で収集する

両方の HDInsight クラスターが同じ仮想ネットワーク内に存在する場合は、前述のとおり、内部ホスト名解決が自動的に行われます。 VPN ゲートウェイによって接続された 2 つの異なる仮想ネットワーク内の HDInsight クラスターに対して CopyTable を使用するには、クォーラム内の Zookeeper ノードのホスト IP アドレスを指定する必要があります。

クォーラムのホスト名を取得するには、次の curl コマンドを実行します。

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/configurations?type=hbase-site&tag=TOPOLOGY_RESOLVED" | grep "hbase.zookeeper.quorum"

curl コマンドで HBase 構成情報を含む JSON ドキュメントを取得し、grep コマンドで次のように "hbase.zookeeper.quorum" エントリのみを返します。

    "hbase.zookeeper.quorum" : "zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net"

クォーラムのホスト名の値は、コロンの右側にある文字列全体です。

これらのホストの IP アドレスを取得するには、前のリストにある各ホストに対して次の curl コマンドを使用します。

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/hosts/<zookeeperHostFullName>" | grep "ip"

この curl コマンドの `<zookeeperHostFullName>` は ZooKeeper ホストの完全 DNS 名です (例: `zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net`)。 コマンドの出力には、次のように、指定したホストの IP アドレスが含まれます。

    100    "ip" : "10.0.0.9",

クォーラム内のすべての ZooKeeper ノードの IP アドレスを収集した後、コピー先のアドレスを再構築します。

    <destinationAddress>  = <Host_1_IP>,<Host_2_IP>,<Host_3_IP>:<Port>:<ZnodeParent>

この例では次のようになります。

    <destinationAddress> = 10.0.0.9,10.0.0.8,10.0.0.12:2181:/hbase-unsecure

## <a name="snapshots"></a>スナップショット

スナップショットを使用すると、HBase データストア内に特定の時点のデータ バックアップを作成できます。 スナップショットは、最小限のオーバーヘッドで、数秒以内に完了します。これは、スナップショット操作が、事実上、そのインスタンスのストレージ内にあるすべてのファイルの名前をキャプチャするメタデータ操作であるからです。 スナップショットの時点では、実際のデータはコピーされません。 スナップショットは、HDFS に保存されているデータの不変的な性質を利用します。ここでは、更新、削除、挿入すべてが新しいデータとして表されます。 スナップショットは、同じクラスター上に復元 ("*複製*") することも、別のクラスターにエクスポートすることもできます。

スナップショットを作成するには、HDInsight HBase クラスターのヘッド ノードに SSH で接続し、`hbase` シェルを起動します。

    hbase shell

HBase シェル内で、テーブルの名前とこのスナップショットの名前を指定した snapshot コマンドを使用します。

    snapshot '<tableName>', '<snapshotName>'

`hbase` シェル内で名前を使用してスナップショットを復元するには、最初にテーブルを無効にしてから、スナップショットを復元してテーブルを再度有効にします。

    disable '<tableName>'
    restore_snapshot '<snapshotName>'
    enable '<tableName>'

スナップショットを新しいテーブルに復元するには、clone_snapshot を使用します。

    clone_snapshot '<snapshotName>', '<newTableName>'

別のクラスターで使用するためにスナップショットを HDFS にエクスポートするには、まず、既に説明したようにスナップショットを作成してから、ExportSnapshot ユーティリティを使用します。 `hbase` シェル内ではなく、SSH セッション内からヘッド ノードに対してこのユーティリティを実行します。

     hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot <snapshotName> -copy-to <hdfsHBaseLocation>

`<hdfsHBaseLocation>` は、エクスポート元クラスターからアクセスできるストレージの場所のいずれかになり、エクスポート先クラスターで使用される hbase フォルダーを指す必要があります。 たとえば、セカンダリ Azure Storage アカウントがエクスポート元クラスターに接続されていて、そのアカウントから、エクスポート先クラスターの既定のストレージで使用されるコンテナーにアクセスできる場合は、次のコマンドを実行します。

    hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'

スナップショットがエクスポートされたら、エクスポート先クラスターのヘッド ノードに SSH で接続し、前に説明したように restore_snapshot コマンドを使用してスナップショットを復元します。

スナップショットは、`snapshot` コマンドの時点でのテーブルの完全バックアップを提供します。 スナップショットでは、時間枠ごとに増分スナップショットを実行することも、スナップショットに含める列ファミリのサブセットを指定することもできません。

## <a name="replication"></a>レプリケーション

HBase レプリケーションでは、レプリケーション元クラスターのオーバーヘッドを最小限に抑えた非同期メカニズムを使用して、レプリケーション元クラスターからレプリケーション先クラスターにトランザクションを自動的にプッシュします。 HDInsight では、次のようなクラスター間のレプリケーションを設定できます。

* レプリケーション元クラスターとレプリケーション先クラスターが同じ仮想ネットワーク内に存在する。
* レプリケーション元クラスターとレプリケーション先クラスターが VPN ゲートウェイによって接続された別々の仮想ネットワークに存在するが、両方のクラスターが地理的に同じ場所に存在する。
* レプリケーション元クラスターとレプリケーション先クラスターが VPN ゲートウェイによって接続された別々の仮想ネットワークに存在していて、各クラスターが地理的に異なる場所に存在する。

レプリケーションを設定する一般的な手順は次のとおりです。

1. レプリケーション元クラスターで、テーブルを作成し、データを入力します。
2. レプリケーション先クラスターで、レプリケーション元テーブルのスキーマを使用して空のレプリケーション先テーブルを作成します。
3. レプリケーション先クラスターをレプリケーション元クラスターへのピアとして登録します。
4. 目的のレプリケーション元テーブルでレプリケーションを有効にします。
5. 既存のデータをレプリケーション元テーブルからレプリケーション先テーブルにコピーします。
6. レプリケーションによって自動的に、レプリケーション元テーブルに対する新しいデータ変更がレプリケーション先テーブルにコピーされます。

HDInsight でレプリケーションを有効にするには、実行中のレプリケーション元 HDInsight クラスターにスクリプト アクションを適用します。 クラスターでレプリケーションを有効にするチュートリアル、または Azure Resource Management テンプレートを使用して仮想ネットワークに作成されたサンプル クラスターでのレプリケーションの実験については、[HBase レプリケーションの構成](apache-hbase-replication.md)に関する記事を参照してください。 その記事では、Phoenix メタデータのレプリケーションを有効にするための手順も説明しています。

## <a name="next-steps"></a>次の手順

* [HBase レプリケーションの構成](apache-hbase-replication.md)
