---
title: Apache Kafka の開始 - Azure HDInsight クイック スタート
description: このクイックスタートでは、Azure PowerShell を使って Azure HDInsight に Apache Kafka クラスターを作成する方法を説明します。 Kafka のトピック、サブスクライバー、およびコンシューマーについても説明します。
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
ms.custom: mvc,hdinsightactive
ms.topic: quickstart
ms.date: 04/16/2018
ms.openlocfilehash: 25064873b830a8f323a8ee2287a3d69cf9c4013f
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621516"
---
# <a name="quickstart-create-a-kafka-on-hdinsight-cluster"></a>クイック スタート: HDInsight クラスターに Kafka を作成する

Kafka は、オープンソースの分散ストリーミング プラットフォームです。 発行/サブスクライブ メッセージ キューと同様の機能を備えているため、メッセージ ブローカーとして多く使われています。 

このクイック スタートでは、Azure PowerShell を使って [Apache Kafka](https://kafka.apache.org) クラスターを作成する方法について説明します。 Kafka を使って、付属のユーティリティでメッセージを送受信する方法についても説明します。

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

> [!IMPORTANT]
> Kafka API は、同じ仮想ネットワーク内のリソースによってのみアクセスできます。 このクイック スタートでは、SSH を使って直接クラスターにアクセスします。 他のサービス、ネットワーク、または仮想マシンを Kafka に接続するには、まず、仮想ネットワークを作成してから、ネットワーク内にリソースを作成する必要があります。
>
> 詳しくは、[仮想ネットワークを使用した Kafka への接続](apache-kafka-connect-vpn-gateway.md)に関するドキュメントをご覧ください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

* Azure PowerShell。 詳しくは、「[Install and Configure Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)」(Azure PowerShell のインストールと構成) をご覧ください。

* SSH クライアント このドキュメントの手順では、SSH を使って、クラスターに接続します。

    Linux、Unix、および macOS システムでは、既定で `ssh` コマンドが用意されています。 Windows 10 では、次のいずれかの方法を使って、`ssh` コマンドをインストールします。

    * [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart) を使います。 Cloud Shell は `ssh` コマンドを提供し、シェル環境として Bash または PowerShell を使うように構成できます。

    * [Windows Subsystem for Linux をインストールします](https://docs.microsoft.com/windows/wsl/install-win10)。 Microsoft Store から入手可能な Linux ディストリビューションでは、`ssh` コマンドが提供されています。

    > [!IMPORTANT]
    > このドキュメントの手順では、上記のいずれかの SSH クライアントを使っていることを前提としています。 別の SSH クライアントを使っていて、問題が発生した場合は、お使いの SSH クライアントのドキュメントを参照してください。
    >
    > 詳細については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するドキュメントを参照してください。

## <a name="log-in-to-azure"></a>Azure にログインする

`Login-AzureRmAccount` コマンドレットで Azure サブスクリプションにログインし、画面上の指示に従います。

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>リソース グループの作成

[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) を使用して Azure リソース グループを作成します。 リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 次の例では、名前と場所の入力を求められた後、新しいリソース グループが作成されます。

```powershell
$resourceGroup = Read-Input -Prompt "Enter the resource group name"
$location = Read-Input -Prompt "Enter the Azure region to use"

New-AzureRmResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

HDInsight 上の Kafka は Azure マネージド ディスクを使って Kafka のデータを格納しますが、クラスターはログなどの情報を格納するために Azure Storage も使います。 新しいストレージ アカウントを作成するには、[New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) を使います。

```powershell
$storageName = Read-Host -Prompt "Enter the storage account name"

New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroup `
        -Name $storageName `
        -Type Standard_LRS `
        -Location $location
```

HDInsight は、BLOB コンテナーにストレージ アカウント内のデータを格納します。 新しいコンテナーを作成するには、[New-AzureStorageContainer](/powershell/module/Azure.Storage/New-AzureStorageContainer) を使います。

```powershell
$containerName = Read-Host -Prompt "Enter the container name"

$storageKey = (Get-AzureRmStorageAccountKey `
                -ResourceGroupName $resourceGroup `
                -Name $storageName)[0].Value
$storageContext = New-AzureStorageContext `
                    -StorageAccountName $storageName `
                    -StorageAccountKey $storageKey
New-AzureStorageContainer -Name $containerName -Context $storageContext 
```

## <a name="create-a-kafka-cluster"></a>Kafka クラスターの作成

HDInsight クラスターに Kafka を作成するには、[New-AzureRmHDInsightCluster](/powershell/module/AzureRM.HDInsight/New-AzureRmHDInsightCluster) を使います。

```powershell
# Create a Kafka 1.0 cluster
$clusterName = Read-Host -Prompt "Enter the name of the Kafka cluster"
$httpCredential = Get-Credential -Message "Enter the cluster login credentials" `
    -UserName "admin"
$sshCredentials = Get-Credential -Message "Enter the SSH user credentials"

$numberOfWorkerNodes = "4"
$clusterVersion = "3.6"
$clusterType="Kafka"
$clusterOS="Linux"
$disksPerNode=2

$kafkaConfig = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
$kafkaConfig.Add("kafka", "1.0")

New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroup `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $numberOfWorkerNodes `
        -ClusterType $clusterType `
        -OSType $clusterOS `
        -Version $clusterVersion `
        -ComponentVersion $kafkaConfig `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$storageName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageKey `
        -DefaultStorageContainer $clusterName `
        -SshCredential $sshCredentials `
        -DisksPerWorkerNode $disksPerNode
```

> [!WARNING]
> HDInsight クラスターの作成には最大で 20 分かかります。

> [!TIP]
> `-DisksPerWorkerNode` パラメーターは、HDInsight 上の Kafka のスケーラビリティを構成します。 HDInsight 上の Kafka は、クラスターの仮想マシンのローカル ディスクを使って、データを保存します。 Kafka は I/O が多いため、[Azure マネージド ディスク](../../virtual-machines/windows/managed-disks-overview.md)を使ってノードごとに高いスループットと多くの記憶域を提供します。 
>
> マネージド ディスクの種類は、__Standard__ (HDD) または __Premium__ (SSD) です。 ディスクの種類は、ワーカー ノード (Kafka ブローカー) によって使われる VM のサイズによって異なります。 DS および GS シリーズの VM では、Premium ディスクが自動的に使われます。 他の種類の VM はすべて Standard を使います。 `-WorkerNodeSize` パラメーターを使って、VM の種類を設定することができます。 パラメーターについて詳しくは、「[New-AzureRmHDInsightCluster](/powershell/module/AzureRM.HDInsight/New-AzureRmHDInsightCluster)」をご覧ください。


> [!IMPORTANT]
> (クラスターの作成時または作成後のスケーリングで) 32 個を超えるワーカー ノードを使うことを計画している場合は、`-HeadNodeSize` パラメーターを使って、コア数が 8 個以上、RAM が 14 GB 以上の VM サイズを指定する必要があります。
>
> ノードのサイズと関連コストに関する詳細については、「 [HDInsight の価格](https://azure.microsoft.com/pricing/details/hdinsight/)」を参照してください。

## <a name="connect-to-the-cluster"></a>クラスターへの接続

1. Kafka クラスターのプライマリ ヘッド ノードに接続するには、次のコマンドを使います。 `sshuser` を SSH ユーザー名で置き換えます。 `mykafka` を Kafka クラスターの名前に置き換えます

    ```bash
    ssh sshuser@mykafka-ssh.azurehdinsight.net
    ```

2. クラスターに初めて接続すると、ホストの信頼性を確立できないという警告が SSH クライアントに表示されることがあります。 プロンプトが表示されたら、「__yes__」と入力して、__Enter__ キーを押し、SSH クライアントの信頼済みサーバーの一覧にこのホストを追加します。

3. メッセージが表示されたら、SSH ユーザーのパスワードを入力します。

接続されると、次のテキストのような情報が表示されます。

```text
Authorized uses only. All activity may be monitored and reported.
Welcome to Ubuntu 16.04.4 LTS (GNU/Linux 4.13.0-1011-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

83 packages can be updated.
37 updates are security updates.



Welcome to Kafka on HDInsight.

Last login: Thu Mar 29 13:25:27 2018 from 108.252.109.241
ssuhuser@hn0-mykafk:~$
```

## <a id="getkafkainfo"></a>Zookeeper およびブローカーのホスト情報を取得する

Kafka を使うときは、*Zookeeper* ホストと "*ブローカー*" ホストについて理解しておく必要があります。 これらのホストは、Kafka の API や、Kafka に付属するユーティリティの多くで使用されます。

このセクションでは、クラスターで Ambari REST API からホスト情報を取得します。

1. クラスターへの SSH 接続から、次のコマンドを使って `jq` ユーティリティをインストールします。 このユーティリティは JSON ドキュメントを解析するためのものであり、ホスト情報の取得に役立ちます。
   
    ```bash
    sudo apt -y install jq
    ```

2. 環境変数をクラスター名に設定するには、次のコマンドを使用します。

    ```bash
    read -p "Enter the Kafka on HDInsight cluster name: " CLUSTERNAME
    ```

    プロンプトが表示されたら、Kafka クラスター名を入力します。

3. Zookeeper ホスト情報で環境変数を設定するには、次のコマンドを使用します。

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    プロンプトが表示されたら、クラスターのログイン アカウント (SSH アカウントではなく) のパスワードを入力します。

    > [!NOTE]
    > このコマンドはすべての Zookeeper ホストを取得してから、最初の 2 つのエントリのみを返します。 これは、1 つのホストに到達できない場合に、いくらかの冗長性が必要なためです。

4. 環境変数が正しく設定されていることを確認するには、次のコマンドを使用します。

    ```bash
     echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    ```

    このコマンドでは、次のテキストのような情報が返されます。

    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`

5. Kafka ブローカー ホスト情報で環境変数を設定するには、次のコマンドを使用します。

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

    プロンプトが表示されたら、クラスターのログイン アカウント (SSH アカウントではなく) のパスワードを入力します。

6. 環境変数が正しく設定されていることを確認するには、次のコマンドを使用します。

    ```bash   
    echo '$KAFKABROKERS='$KAFKABROKERS
    ```

    このコマンドでは、次のテキストのような情報が返されます。
   
    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`

## <a name="manage-kafka-topics"></a>Kafka トピックの管理

Kafka は、"*トピック*" にデータのストリームを格納します。 `kafka-topics.sh` ユーティリティを使って、トピックを管理できます。

* **トピックを作成するには**、SSH 接続で次のコマンドを使います。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    ```

    このコマンドを使用すると、`$KAFKAZKHOSTS` に格納されているホスト情報を使用して Zookeeper に接続されます。 次に、**test** という名前の Kafka トピックが作成されます。 

    * このトピックに格納されるデータは、8 つのパーティションに分割されます。

    * 各パーティションは、クラスター内の 3 つのワーカー ノード間でレプリケートされます。

        > [!IMPORTANT]
        > 3 つの障害ドメインを提供する Azure リージョンにクラスターを作成した場合は、3 のレプリケーション係数を使います。 そうでない場合は、4 のレプリケーション係数を使います。
        
        3 つの障害ドメインがあるリージョンでは、3 のレプリケーション係数により、レプリカを障害ドメインに分散できます。 2 つの障害ドメインのリージョンでは、4 のレプリケーション係数により、ドメイン全体に均等にレプリカが分散されます。
        
        リージョン内の障害ドメインの数については、[Linux 仮想マシンの可用性](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set)に関するトピックを参照してください。

        > [!IMPORTANT] 
        > Kafka は、Azure 障害ドメインを認識しません。 トピック用にパーティションのレプリカを作成すると、レプリカが適切に分散されず、高可用性が実現しない場合があります。

        高可用性を確保するには、[Kafka パーティション再調整ツール](https://github.com/hdinsight/hdinsight-kafka-tools)を使用してください。 このツールは、Kafka クラスターのヘッド ノードへの SSH 接続から実行する必要があります。

        Kafka データの最高の可用性のために、次のときにトピックのパーティションのレプリカを再調整する必要があります。

        * 新しいトピックまたはパーティションの作成

        * クラスターのスケールアップ

* **トピックの一覧を表示するには**、次のコマンドを使います。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    このコマンドは、Kafka クラスターで使用可能なトピックの一覧を表示します。

* **トピックを削除するには**、次のコマンドを使います。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic topicname --zookeeper $KAFKAZKHOSTS
    ```

    このコマンドは、`topicname` という名前のトピックを削除します。

    > [!WARNING]
    > 前に作成した `test` トピックを削除した場合は、再作しなおす必要があります。 このドキュメントの後半で使います。

`kafka-topics.sh` ユーティリティで使用可能なコマンドの詳細については、次のコマンドを使用します。

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh
```

## <a name="produce-and-consume-records"></a>レコードの生成および消費

Kafka では、トピック内に*レコード*が格納されます。 レコードは、*プロデューサー*によって生成され、*コンシューマー*によって消費されます。 プロデューサーとコンシューマーは "*Kafka ブローカー*" サービスと通信します。 HDInsight クラスターの各ワーカー ノードが、Kafka ブローカー ホストです。

先ほど作成した test トピックにレコードを格納し、コンシューマーを使用してそれらを読み取るには、次の手順に従います。

1. トピックにレコードを書き込むには、SSH 接続から `kafka-console-producer.sh` ユーティリティを使用します。
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```
   
    このコマンドの後ろには空の行があります。

2. 空の行にテキスト メッセージを入力して、Enter キーを押します。 このようにメッセージをいくつか入力してから、**Ctrl + C** キーを使用して通常のプロンプトに戻ります。 各行が、個別のレコードとして Kafka トピックに送信されます。

3. トピックからレコードを読み取るには、SSH 接続から `kafka-console-consumer.sh` ユーティリティを使用します。
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```
   
    このコマンドにより、レコードがトピックから取得され、表示されます。 `--from-beginning` を使用することで、すべてのレコードが取得されるように、コンシューマーにストリームの先頭から開始するように指示しています。

    > [!NOTE]
    > 以前のバージョンの Kafka を使っている場合、`--bootstrap-server $KAFKABROKERS` を `--zookeeper $KAFKAZKHOSTS` に置き換えます。

4. __Ctrl+C__ キーを使用してコンシューマーを停止します。

プロデューサーとコンシューマーをプログラムから作成することもできます。 この API の使用例については、[HDInsight における Kafka のプロデューサー API とコンシューマー API](apache-kafka-producer-consumer-api.md) に関するドキュメントを参照してください。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要がなくなったら、[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) コマンドを使って、リソース グループ、HDInsight、すべての関連リソースを削除できます。

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

> [!WARNING]
> HDInsight クラスターの課金は、クラスターが作成されると開始し、クラスターが削除されると停止します。 課金は分単位なので、クラスターを使わなくなったら必ず削除してください。
> 
> HDInsight クラスター上の Kafka を削除すると、Kafka に格納されているすべてのデータが削除されます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Kafka で Apache Spark を使用する](../hdinsight-apache-kafka-spark-structured-streaming.md)
