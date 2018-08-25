---
title: Azure HDInsight ドメイン参加済みクラスター内の Apache Hadoop Oozie ワークフロー
description: Linux ベースの HDInsight ドメイン参加済み Enterprise セキュリティ パッケージで Hadoop Oozie を使用します。 Oozie ワークフローを定義し、Oozie ジョブを送信する方法について説明します。
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
editor: jasonwhowell
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/26/2018
ms.openlocfilehash: a7f17aafd7798c1bada9fef01a6c8f1022d291f4
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39616852"
---
# <a name="run-apache-oozie-in-domain-joined-hdinsight-hadoop-clusters"></a>ドメイン参加済み HDInsight Hadoop クラスターで Apache Oozie を実行する
Oozie は Hadoop ジョブを管理するワークフローおよび調整システムです。 Oozie は Hadoop スタックと統合されており、次のジョブをサポートしています。
- Apache MapReduce
- Apache Pig
- Apache Hive
- Apache Sqoop

Oozie を使って、Java プログラムやシェル スクリプトなどの、システムに固有のジョブをスケジュールすることもできます。

## <a name="prerequisite"></a>前提条件
- ドメイン参加済み Azure HDInsight Hadoop クラスター。 [ドメイン参加済み HDInsight クラスターの構成](./apache-domain-joined-configure-using-azure-adds.md)に関するページを参照してください。

    > [!NOTE]
    > ドメインに参加していないクラスター上で Oozie を使用するための詳細な手順については、[Linux ベースの Azure HDInsight での Hadoop Oozie ワークフローの使用](../hdinsight-use-oozie-linux-mac.md)に関するページを参照してください。

## <a name="connect-to-a-domain-joined-cluster"></a>ドメイン参加済みクラスターへの接続

Secure Shell (SSH) の詳細については、「[SSH を使用して HDInsight (Hadoop) に接続する](../hdinsight-hadoop-linux-use-ssh-unix.md)」を参照してください。

1. SSH を使って HDInsight クラスターに接続します。  
 ```bash
ssh [DomainUserName]@<clustername>-ssh.azurehdinsight.net
 ```

2. Kerberos 認証が成功したかどうかを確認するには、`klist` コマンドを使用します。 そうでない場合は、`kinit` を使用して Kerberos 認証を開始します。

3. Azure Data Lake Storage にアクセスするために必要な OAuth トークンを登録するには、HDInsight ゲートウェイにサインインします。   
     ```bash
     curl -I -u [DomainUserName@Domain.com]:[DomainUserPassword] https://<clustername>.azurehdinsight.net
     ```

    **200 OK** の状態応答コードは、登録の成功を示します。 承認されていない応答 (401 など) が受信された場合は、ユーザー名とパスワードを確認します。

## <a name="define-the-workflow"></a>ワークフローの定義
Oozie ワークフローの定義は、Hadoop プロセス定義言語 (hPDL) を使って記述します。 hPDL は XML プロセス定義言語です。 次の手順に従ってワークフローを定義します。

1.  ドメイン ユーザーのワークスペースを設定します。
 ```bash
hdfs dfs -mkdir /user/<DomainUser>
cd /home/<DomainUserPath>
cp /usr/hdp/<ClusterVersion>/oozie/doc/oozie-examples.tar.gz .
tar -xvf oozie-examples.tar.gz
hdfs dfs -put examples /user/<DomainUser>/
 ```
`DomainUser` をドメイン ユーザー名に置き換えます。 `DomainUserPath` をドメイン ユーザーのホーム ディレクトリのパスに置き換えます。 `ClusterVersion` をクラスターの Hortonworks Data Platform (HDP) バージョンに置き換えます。

2.  次のステートメントを使用して、新しいファイルを作成し、編集します。
 ```bash
nano workflow.xml
 ```

3. nano エディターが開いたら、ファイルの内容として次の XML を入力します。
 ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <workflow-app xmlns="uri:oozie:workflow:0.4" name="map-reduce-wf">
       <credentials>
          <credential name="metastore_token" type="hcat">
             <property>
                <name>hcat.metastore.uri</name>
                <value>thrift://hn0-<clustername>.<Domain>.com:9083</value>
             </property>
             <property>
                <name>hcat.metastore.principal</name>
                <value>hive/_HOST@<Domain>.COM</value>
             </property>
          </credential>
          <credential name="hs2-creds" type="hive2">
             <property>
                <name>hive2.server.principal</name>
                <value>${jdbcPrincipal}</value>
             </property>
             <property>
                <name>hive2.jdbc.url</name>
                <value>${jdbcURL}</value>
             </property>
          </credential>
       </credentials>
       <start to="mr-test" />
       <action name="mr-test">
          <map-reduce>
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <prepare>
                <delete path="${nameNode}/user/${wf:user()}/examples/output-data/mrresult" />
             </prepare>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
                <property>
                   <name>mapred.mapper.class</name>
                   <value>org.apache.oozie.example.SampleMapper</value>
                </property>
                <property>
                   <name>mapred.reducer.class</name>
                   <value>org.apache.oozie.example.SampleReducer</value>
                </property>
                <property>
                   <name>mapred.map.tasks</name>
                   <value>1</value>
                </property>
                <property>
                   <name>mapred.input.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/input-data/text</value>
                </property>
                <property>
                   <name>mapred.output.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/output-data/mrresult</value>
                </property>
             </configuration>
          </map-reduce>
          <ok to="myHive2" />
          <error to="fail" />
       </action>
       <action name="myHive2" cred="hs2-creds">
          <hive2 xmlns="uri:oozie:hive2-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <jdbc-url>${jdbcURL}</jdbc-url>
             <script>${hiveScript2}</script>
             <param>hiveOutputDirectory2=${hiveOutputDirectory2}</param>
          </hive2>
          <ok to="myHive" />
          <error to="fail" />
       </action>
       <action name="myHive" cred="metastore_token">
          <hive xmlns="uri:oozie:hive-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
             </configuration>
             <script>${hiveScript1}</script>
             <param>hiveOutputDirectory1=${hiveOutputDirectory1}</param>
          </hive>
          <ok to="end" />
          <error to="fail" />
       </action>
       <kill name="fail">
          <message>Oozie job failed, error message[${wf:errorMessage(wf:lastErrorNode())}]</message>
       </kill>
       <end name="end" />
    </workflow-app>
 ```
4. `clustername` をクラスターの名前に置き換えます。 

5. ファイルを保存するには、Ctrl + X キーを押します。 「`Y`」を入力します。 次に、**Enter** キーを押します。

    このワークフローは、次の 2 つの部分に分かれています。
    *   **資格情報セクション:**  このセクションでは、Oozie アクションを認証するために使用される資格情報を取得します。

       この例では、Hive アクション用の認証を使用します。 詳細については、[アクションの認証](https://oozie.apache.org/docs/4.2.0/DG_ActionAuthentication.html)に関するページを参照してください。

       資格情報サービスでは、Oozie アクションが、Hadoop サービスにアクセスするためのユーザーを偽装することが許可されます。

    *   **アクション セクション:**  このセクションには、map-reduce、Hive server 2、および Hive server 1 という 3 つのアクションがあります。

      - map-reduce アクションは、集計された単語数を出力する、map-reduce 用の Oozie パッケージにある例を実行します。

       - Hive server 2 および Hive server 1 アクションは、HDInsight に付属しているサンプルの Hive テーブルに対するクエリを実行します。

        Hive アクションは、アクション要素内のキーワード `cred` を使用して、認証用の資格情報セクションで定義されている資格情報を使用します。

6. 次のコマンドを使用して、`workflow.xml` ファイルを `/user/<domainuser>/examples/apps/map-reduce/workflow.xml` にコピーします。
     ```bash
    hdfs dfs -put workflow.xml /user/<domainuser>/examples/apps/map-reduce/workflow.xml
     ```

7. `domainuser` をドメインのユーザー名に置き換えます。

## <a name="define-the-properties-file-for-the-oozie-job"></a>oozie ジョブのプロパティ ファイルを定義する

1. 次のステートメントを使用して、ジョブ プロパティのための新しいファイルを作成および編集します。

   ```bash
   nano job.properties
   ```

2. nano エディターが開いたら、ファイルの内容として次の XML を使います。

   ```bash
       nameNode=adl://home
       jobTracker=headnodehost:8050
       queueName=default
       examplesRoot=examples
       oozie.wf.application.path=${nameNode}/user/[domainuser]/examples/apps/map-reduce/workflow.xml
       hiveScript1=${nameNode}/user/${user.name}/countrowshive1.hql
       hiveScript2=${nameNode}/user/${user.name}/countrowshive2.hql
       oozie.use.system.libpath=true
       user.name=[domainuser]
       jdbcPrincipal=hive/hn0-<ClusterShortName>.<Domain>.com@<Domain>.COM
       jdbcURL=[jdbcurlvalue]
       hiveOutputDirectory1=${nameNode}/user/${user.name}/hiveresult1
       hiveOutputDirectory2=${nameNode}/user/${user.name}/hiveresult2
   ```
  
   a. `domainuser` をドメインのユーザー名に置き換えます。  
   b. `ClusterShortName` をクラスターの短い名前に置き換えます。 たとえば、クラスター名が https:// *[example link]* sechadoopcontoso.azurehdisnight.net である場合、`clustershortname` はクラスターの最初の 6 文字である **sechad** です。  
   c. `jdbcurlvalue` を Hive 構成の JDBC URL に置き換えます。 たとえば、jdbc:hive2://headnodehost:10001/;transportMode=http とします。      
   d. ファイルを保存するには、Ctrl + X キー、`Y` キー、**Enter** キーの順に押します。

   このプロパティ ファイルは、Oozie ジョブの実行時にローカルに存在する必要があります。

## <a name="create-custom-hive-scripts-for-oozie-jobs"></a>Oozie ジョブのカスタム Hive スクリプトの作成
以下に示すように、Hive server 1 と Hive server 2 のための 2 つの Hive スクリプトを作成できます。
### <a name="hive-server-1-file"></a>Hive server 1 のファイル
1.  Hive server 1 アクション用のファイルを作成して編集します。
    ```bash
    nano countrowshive1.hql
    ```

2.  スクリプトを作成します。
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemake from hivesampletable limit 2;
    ```

3.  ファイルを Hadoop 分散ファイル システム (HDFS) に保存します。
    ```bash
    hdfs dfs -put countrowshive1.hql countrowshive1.hql
    ```

### <a name="hive-server-2-file"></a>Hive server 2 のファイル
1.  Hive server 2 アクション用のファイルを作成して編集します。
    ```bash
    nano countrowshive2.hql
    ```

2.  スクリプトを作成します。
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemodel from hivesampletable limit 2;
    ```

3.  ファイルを HDFS に保存します。
    ```bash
    hdfs dfs -put countrowshive2.hql countrowshive2.hql
    ```

## <a name="submit-oozie-jobs"></a>Oozie ジョブの送信
ドメイン参加済みクラスターの Oozie ジョブの送信は、ドメインに参加していないクラスター内の Oozie ジョブの送信と同様です。

詳細については、「[Hadoop で Oozie を使用して Linux ベースの Azure HDInsight でワークフローを定義して実行する](../hdinsight-use-oozie-linux-mac.md)」を参照してください。

## <a name="results-from-an-oozie-job-submission"></a>Oozie ジョブの送信の結果
Oozie ジョブはユーザーの代わりに実行されます。 そのため、Apache YARN と Apache Ranger の両方の監査ログには、これらのジョブが偽装されたユーザーとして実行されたことが示されています。 Oozie ジョブのコマンド ライン インターフェイス出力は次のコードのようになります。


```bash
    Job ID : 0000015-180626011240801-oozie-oozi-W
    ------------------------------------------------------------------------------------------------
    Workflow Name : map-reduce-wf
    App Path      : adl://home/user/alicetest/examples/apps/map-reduce/wf.xml
    Status        : SUCCEEDED
    Run           : 0
    User          : alicetest
    Group         : -
    Created       : 2018-06-26 19:25 GMT
    Started       : 2018-06-26 19:25 GMT
    Last Modified : 2018-06-26 19:30 GMT
    Ended         : 2018-06-26 19:30 GMT
    CoordAction ID: -
    
    Actions
    ------------------------------------------------------------------------------------------------
    ID                      Status  Ext ID          ExtStatus   ErrCode
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@:start:    OK  -           OK      -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@mr-test    OK  job_1529975666160_0051  SUCCEEDED   -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@myHive2    OK  job_1529975666160_0053  SUCCEEDED   -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@myHive OK  job_1529975666160_0055  SUCCEEDED   -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@end    OK  -           OK      -
    -----------------------------------------------------------------------------------------------
```

Hive server 2 アクションの Ranger 監査ログには、Oozie がユーザーに代わってアクションを実行したことが示されています。 Ranger や YARN のビューは、クラスター管理者にのみ表示されます。

## <a name="configure-user-authorization-in-oozie"></a>Oozie でのユーザーの承認の構成
Oozie はそれ自体に、ユーザーが他のユーザーのジョブを停止または削除することをブロックできるユーザーの承認の構成を持っています。 この構成を有効にするには、`oozie.service.AuthorizationService.security.enabled` を `true` に設定します。 

詳細については、[Oozie のインストールと構成](https://oozie.apache.org/docs/3.2.0-incubating/AG_Install.html)に関するページを参照してください。

Ranger プラグインが使用できないか、またはサポートされていない Hive server 1 などのコンポーネントの場合は、粒度の粗い HDFS 承認のみが可能です。 きめ細かい承認は、Ranger プラグインを通してのみ使用できます。

## <a name="get-the-oozie-web-ui"></a>Oozie Web UI の取得
Oozie Web UI は、クラスターでの Oozie ジョブの状態を表示する Web ベースのビューを提供します。 Web UI を取得するには、ドメイン参加済みクラスターで次の手順を実行します。

1. [エッジ ノード](../hdinsight-apps-use-edge-node.md)を追加し、[SSH Kerberos 認証](../hdinsight-hadoop-linux-use-ssh-unix.md)を有効にします。

2. [Oozie Web UI](../hdinsight-use-oozie-linux-mac.md) の手順に従ってエッジ ノードへの SSH トンネリングを有効にし、Web UI にアクセスします。

## <a name="next-steps"></a>次の手順
* [Hadoop で Oozie を使用して Linux ベースの Azure HDInsight でワークフローを定義して実行する](../hdinsight-use-oozie-linux-mac.md)
* [時間ベースの Oozie コーディネーターを使用する](../hdinsight-use-oozie-coordinator-time.md)
* [SSH を使用して HDInsight (Hadoop) に接続する](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)
