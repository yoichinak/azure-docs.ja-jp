---
title: ドメイン参加済み HDInsight での Hive ポリシーの構成 - Azure
description: ドメイン参加済みの Azure HDInsight サービスで Hive 用の Apache Ranger ポリシーを構成する方法について説明します。
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
editor: jasonwhowell
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/17/2018
ms.openlocfilehash: 8579c3c13ace1f97d2400a4fc6e2e9a63c2c4d26
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39599692"
---
# <a name="configure-hive-policies-in-domain-joined-hdinsight"></a>ドメイン参加済み HDInsight での Hive ポリシーの構成
Hive 用 Apache Ranger ポリシーを構成する方法について説明します。 この記事では、hivesampletable へのアクセスを制限する 2 つの Ranger ポリシーを作成します。 hivesampletable は HDInsight クラスターに付属しています。 ポリシーを構成したら、Excel と ODBC ドライバーを使用して HDInsight の Hive テーブルに接続します。

## <a name="prerequisites"></a>前提条件
* ドメイン参加済み HDInsight クラスター。 [ドメイン参加済み HDInsight クラスターの構成](apache-domain-joined-configure.md)に関する記事をご覧ください。
* Office 2016、Office 2013 Professional Plus、Office 365 Pro Plus、Excel 2013 Standalone、または Office 2010 Professional Plus がインストールされたワークステーション。

## <a name="connect-to-apache-ranger-admin-ui"></a>Apache Ranger 管理 UI への接続
**Ranger 管理 UI に接続するには**

1. ブラウザーから Ranger 管理 UI に接続します。 URL は https://&lt;ClusterName >.azurehdinsight.net/Ranger/ です。

   > [!NOTE]
   > Ranger では、Hadoop クラスターとは異なる資格情報を使用します。 ブラウザーで Hadoop のキャッシュされた資格情報が使用されないように、新しい InPrivate ブラウザー ウィンドウを使用して Ranger 管理 UI に接続してください。
   >
   >
2. クラスター管理者のドメイン ユーザー名とパスワードを使用してログインします。

    ![HDInsight のドメイン参加済み Ranger のホーム ページ](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png)

    現在、Ranger は Yarn および Hive でのみ機能します。

## <a name="create-domain-users"></a>ドメイン ユーザーの作成
hiveruser1 と hiveuser2 を作成する方法については、「[ドメイン参加済み HDInsight クラスターの作成](apache-domain-joined-configure-using-azure-adds.md#create-a-domain-joined-hdinsight-cluster)」を参照してください。 このチュートリアルでは、この 2 つのユーザー アカウントを使用します。

## <a name="create-ranger-policies"></a>Ranger ポリシーの作成
このセクションでは、hivesampletable にアクセスするための 2 つの Ranger ポリシーを作成します。 異なる列セットに対する select 権限を付与します。 両方のユーザーは[ドメイン参加済み HDInsight クラスターの構成](apache-domain-joined-configure-using-azure-adds.md#create-a-domain-joined-hdinsight-cluster)で作成されました。 次のセクションでは、2 つのポリシーを Excel でテストします。

**Ranger ポリシーを作成するには**

1. Ranger 管理 UI を開きます。 「[Apache Ranger 管理 UI への接続](#connect-to-apache-ranager-admin-ui)」をご覧ください。
2. **[Hive]** で **&lt;クラスター名>_hive** をクリックします。 構成済みの 2 つのポリシーが表示されます。
3. **[Add New Policy]** をクリックし、次の値を入力します。

   * Policy Name: read-hivesampletable-all
   * Hive Database: default
   * table: hivesampletable
   * Hive Column: *
   * Select User: hiveuser1
   * Permissions: select

     ![HDInsight のドメイン参加済み Ranger での Hive ポリシーの構成](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png).

     > [!NOTE]
     > [Select User] にドメイン ユーザーが設定されていない場合は、Ranger が AAD と同期するまでしばらく待ってください。
     >
     >
4. **[Add]** をクリックしてポリシーを保存します。
5. 最後の 2 つの手順を繰り返して、次のプロパティを設定したもう 1 つのポリシーを作成します。

   * Policy Name: read-hivesampletable-devicemake
   * Hive Database: default
   * table: hivesampletable
   * Hive Column: clientid、devicemake
   * Select User: hiveuser2
   * Permissions: select

## <a name="create-hive-odbc-data-source"></a>Hive ODBC データ ソースの作成
手順については、「[Hive ODBC データ ソースの作成](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)」をご覧ください。  

 | プロパティ  |説明 |
 | --- | --- |
 | データ ソース名 | データ ソースに名前を付けます。 |
 | Host | 「&lt;HDInsightClusterName>.azurehdinsight.net」と入力します。 たとえば、「myHDICluster.azurehdinsight.net」と入力します。 |
 | ポート | **443** を使用します。 (このポートは 563 から 443 に変更されました)。 |
 | Database | **既定値**を使用します。 |
 | Hive サーバーの種類 | **Hive Server 2** を選択します。 |
 | メカニズム | **Azure HDInsight サービス**を選択します。 |
 | HTTP パス | 空白のままにします。 |
 | ユーザー名 | 「hiveuser1@contoso158.onmicrosoft.com」を入力します。 ドメイン名が異なる場合は、ドメイン名を更新します。 |
 | パスワード | hiveuser1 のパスワードを入力します。 |

データ ソースを保存する前に、必ず **[Test]** をクリックしてください。

## <a name="import-data-into-excel-from-hdinsight"></a>HDInsight から Excel へのデータのインポート
最後のセクションで 2 つのポリシーを構成しました。  hiveuser1 にはすべての列に対する select 権限があり、hiveuser2 には 2 つの列に対する select 権限があります。 このセクションでは、2 人のユーザーを偽装して Excel にデータをインポートします。

1. Excel で新しいブックまたは既存のブックを開きます。
2. **[データ]** タブの **[その他のデータ ソース]** をクリックし、**[データ接続ウィザード]** をクリックして **データ接続ウィザード**を開きます。

    ![データ接続ウィザードを開く][img-hdi-simbahiveodbc.excel.dataconnection]
3. データ ソースとして **[ODBC DSN]** を選択し、**[次へ]** をクリックします。
4. ODBC データ ソースから、前の手順で作成したデータ ソース名を選択し、**[次へ]** をクリックします。
5. ウィザードでクラスターのパスワードを再入力し、**[OK]** をクリックします。 **[データベースとテーブルの選択]** ダイアログが開くのを待ちます。 この処理には数秒かかります。
6. **[hivesampletable]** を選択し、**[次へ]** をクリックします。
7. **[完了]** をクリックします。
8. **[データのインポート]** ダイアログでは、クエリを変更または指定できます。 これを行うには、 **[プロパティ]** をクリックします。 この処理には数秒かかります。
9. **[定義]** タブをクリックします。コマンド テキストを次に示します。

       SELECT * FROM "HIVE"."default"."hivesampletable"

   定義した Ranger ポリシーにより、hiveuser1 にはすべての列に対する select 権限があります。  そのため、このクエリは hiveuser1 の資格情報では機能しますが、hiveuser2 の資格情報では機能しません。

   ![接続プロパティ][img-hdi-simbahiveodbc-excel-connectionproperties]
10. **[OK]** をクリックして [接続プロパティ] ダイアログを閉じます。
11. **[OK]** をクリックして **[データのインポート]** ダイアログを閉じます。  
12. hiveuser1 のパスワードを再入力し、**[OK]** をクリックします。 データが Excel にインポートされるまでに、数秒かかります。 インポートが完了すると、11 列のデータが表示されます。

最後のセクションで作成した 2 つ目のポリシー (read-hivesampletable-devicemake) をテストするには

1. Excel で新しいシートを追加します。
2. 最後の手順に従ってデータをインポートします。  ここで行う唯一の変更は、hiveuser1 ではなく hiveuser2 の資格情報を使用することです。 hiveuser2 には 2 つの列を表示する権限しかないため、インポートは失敗します。 次のエラーが表示されます。

        [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
3. 同じ手順に従ってデータをインポートします。 今回は、hiveuser2 の資格情報を使用し、次の select ステートメント

        SELECT * FROM "HIVE"."default"."hivesampletable"

    を次のように変更します。

        SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"

    インポートが完了すると、インポートされた 2 列のデータが表示されます。

## <a name="next-steps"></a>次の手順
* ドメイン参加済み HDInsight クラスターの構成については、[ドメイン参加済み HDInsight クラスターの構成](apache-domain-joined-configure.md)に関する記事を参照してください。
* ドメイン参加済み HDInsight クラスターの管理については、「[ドメイン参加済み HDInsight クラスターの管理](apache-domain-joined-manage.md)」をご覧ください。
* SSH を使用してドメイン参加済み HDInsight クラスターで Hive クエリを実行する方法については、[HDInsight で SSH キーを使用する](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)に関するページをご覧ください。
* Hive JDBC を使用して Hive に接続する方法については、「[Hive の JDBC ドライバーを使用して Azure HDInsight の Hive に接続する](../hadoop/apache-hadoop-connect-hive-jdbc-driver.md)」をご覧ください。
* Hive ODBC を使用して Excel を Hadoop に接続する方法については、「[Microsoft Hive ODBC ドライバーを使用した Excel から Hadoop への接続](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)」をご覧ください。
* Power Query を使用して Excel を Hadoop に接続する方法については、「[Power Query を使用した Excel から Hadoop への接続](../hadoop/apache-hadoop-connect-excel-power-query.md)」をご覧ください。
