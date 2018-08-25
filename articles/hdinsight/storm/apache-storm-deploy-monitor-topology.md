---
title: Azure HDInsight での Apache Storm トポロジのデプロイと管理
description: Windows ベースの HDInsight で Storm ダッシュボードを使用して Apache Storm トポロジをデプロイ、監視、管理する方法について説明します。 Hadoop Tools for Visual Studio を使用します。
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.topic: conceptual
ms.date: 03/01/2017
ms.author: jasonh
ROBOTS: NOINDEX
ms.openlocfilehash: b71dd51547365503ac4ce76f07d6c783d26acd50
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2018
ms.locfileid: "42145860"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-windows-based-hdinsight"></a>Windows ベースの HDInsight での Apache Storm トポロジのデプロイと管理

Storm ダッシュボードでは、Web ブラウザーを使用して Apache Storm トポロジを HDInsight クラスターに簡単にデプロイして実行できます。 また、ダッシュボードを使用して実行中のトポロジを監視、管理できます。 Visual Studio を使用する場合、HDInsight Tools for Visual Studio でも Visual Studio と同様の機能が提供されます。

Storm ダッシュボードと HDInsight Tools の Storm 機能は、共に Storm REST API に依存し、これを使用して独自の監視と管理ソリューションを作成できます。

> [!IMPORTANT]
> このドキュメントの手順では、オペレーティング システムとして Windows を使用する HDInsight クラスター上の Storm が必要です。 Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Windows での HDInsight の提供終了](../hdinsight-component-versioning.md#hdinsight-windows-retirement)に関する記事を参照してください。
>
> Linux を使用する HDInsight クラスターを使用して Storm トポロジをデプロイおよび管理する方法については、「[Linux ベースの HDInsight での Apache Storm トポロジのデプロイと管理](apache-storm-deploy-monitor-topology-linux.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* **HDInsight 上の Apache Storm** - クラスターを作成する手順については、「[HDInsight での Apache Storm の使用](apache-storm-tutorial-get-started-linux.md)」をご覧ください。

* **Storm ダッシュボード**用: HTML5 をサポートする最新の Web ブラウザー。

* **Visual Studio** 用: Azure SDK 2.5.1 以降と HDInsight Tools for Visual Studio。 HDInsight Tools for Visual Studio のインストールと構成については、「[HDInsight Tools for Visual Studio の使用開始](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)」をご覧ください。

    下記いずれかのバージョンの Visual Studio

  * Visual Studio 2012 Update 4

  * Visual Studio 2013 Update 4 または Visual Studio 2013 Community

  * Visual Studio 2015 (任意のエディション)

  * Visual Studio 2017 (任意のエディション)

## <a name="storm-dashboard"></a>Storm ダッシュボード

Storm ダッシュボードは、Storm クラスターで使用できる Web ページです。 URL は、**https://&lt;clustername>.azurehdinsight.net/** (**clustername** はお使いの HDInsight クラスターの Storm の名前) です。

Storm ダッシュボードの上部で、 **[トポロジの送信**] を選択します。 ページの指示に従ってサンプル トポロジを実行するか、作成したトポロジをアップロードして実行します。

![送信トポロジ ページ][storm-dashboard-submit]

### <a name="storm-ui"></a>Storm UI

Storm ダッシュボードで、 **[Storm UI]** リンクを選択します。 クラスターの情報と、実行中のトポロジに関する情報が表示されます。

![Storm UI][storm-dashboard-ui]

> [!NOTE]
> いくつかのバージョンの Internet Explorer では、Storm UI に初めてアクセスした後、Storm UI が更新されない場合があります。 たとえば、送信した新しいトポロジが表示されない場合や、以前に非アクティブ化したトポロジがアクティブと表示される場合があります。 Microsoft はこの問題を確認しており、解決に取り組んでいます。

#### <a name="main-page"></a>メイン ページ

Storm UI のメイン ページには、次の情報が表示されます。

* **クラスターの概要**: Storm クラスターの基本情報

* **トポロジの概要**: 実行中のトポロジの一覧 このセクションのリンクを使用して、各トポロジの詳細情報を表示します。

* **スーパーバイザの概要**: Storm のスーパーバイザに関する情報

* **Nimbus 構成**: クラスターの Nimbus 構成

#### <a name="topology-summary"></a>トポロジの概要

**[トポロジの概要]** セクションのリンクを選択すると、トポロジに関する次の情報が表示されます。

* **トポロジの概要**: トポロジの基本情報

* **トポロジのアクション**: トポロジで実行できる管理アクション

  * **アクティブ化**: アクティブ化が解除されたトポロジの処理を再開します

  * **アクティブ化の解除**: 実行中のトポロジを一時停止します

  * **再調整**: トポロジの並列処理を調整します。 クラスターのノード数を変更した場合は、実行中のトポロジを再調整する必要があります。 この操作で、クラスター内のノード数の増減に合わせて、トポロジの並列処理を調整できます。

      詳細については、「[Understanding the parallelism of a Storm topology (Storm トポロジの並列処理)](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)」をご覧ください。

  * **強制終了**: 指定したタイムアウト後に Storm トポロジを停止します。

* **トポロジの統計**: トポロジに関する統計。 **[Window]** 列にあるリンクを使用して、ページで残りのエントリの時間枠を設定します。

* **スパウト**: トポロジで使用するスパウト。 このセクションにあるリンクを使用して、各スパウトの詳細情報を表示します。

* **ボルト**: トポロジで使用するボルト。 このセクションにあるリンクを使用して、各ボルトの詳細情報を表示します。

* **トポロジの構成**: 選択したトポロジの構成

#### <a name="spout-and-bolt-summary"></a>スパウトとボルトの概要

**[スパウト]** または **[ボルト]** セクションでアイテムを選択すると、そのアイテムに関する次の情報が表示されます。

* **コンポーネントの概要**: スパウトやボルトの基本情報

* **スパウト/ボルトの統計**: スパウトやボルトの統計。 **[Window]** 列にあるリンクを使用して、ページで残りのエントリの時間枠を設定します。

* **入力の状態** (ボルトのみ): ボルトが消費する入力ストリームに関する情報

* **出力の状態**: このスパウトやボルトから出力されるストリームに関する情報

* **エグゼキュータ**: スパウトやボルトのインスタンスに関する情報 特定のエグゼキュータの **[ポート]** エントリを選択して、このインスタンスで生成された診断情報のログを閲覧します

* **エラー**: このスパウトやボルトのエラー情報。

## <a name="hdinsight-tools-for-visual-studio"></a>HDInsight Tools for Visual Studio

HDInsight Tools は、C# またはハイブリッド トポロジを Storm クラスターに送信する際に使用できます。 次の例では、サンプル アプリケーションを使用します。 HDInsight Tools を使用して独自のトポロジを作成する方法の詳細については、「 [Visual Studio を使用して HDInsight で Apache Storm の C# トポロジを開発する](apache-storm-develop-csharp-visual-studio-topology.md)」をご覧ください。

次の手順を使用して、サンプルをお使いの HDInsight クラスターにデプロイし、トポロジを表示、管理します。

1. HDInsight Tools for Visual Studio の最新バージョンをまだインストールしていない場合は、「[HDInsight Tools for Visual Studio の使用開始](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)」をご覧ください。

2. Visual Studio で、**[ファイル]** > **[新規]** > **[プロジェクト]** を選択します。

3. **[新しいプロジェクト]** ダイアログで、**[インストール済]** > **[テンプレート]** の順に展開して **[HDInsight]** を選択します。 テンプレートの一覧から **[Storm Sample]** を選択します。 ダイアログ ボックスの下部に、アプリケーションの名前を入力します。

    ![image](./media/apache-storm-deploy-monitor-topology/sample.png)

4. **[ソリューション エクスプローラー]** で、プロジェクトを右クリックして **[HDInsight の Storm に送信]** を選択します。

   > [!NOTE]
   > メッセージが表示されたら、Azure サブスクリプションのログイン資格情報を入力します。 2 つ以上のサブスクリプションをお持ちの場合は、HDInsight クラスターの Storm があるサブスクリプションにログインします。

5. **[Storm クラスター]** ドロップダウン リストから HDInsight クラスターの Storm を選択して、**[送信]** を選択します。 送信が成功したかどうかは、 **[出力]** ウィンドウを使用して確認できます。

6. トポロジが正常に送信されたら、クラスターの **Storm トポロジ** が表示されます。 実行中のトポロジに関する情報を表示するには、一覧からトポロジを選択します。

    ![Visual Studio モニター](./media/apache-storm-deploy-monitor-topology/vsmonitor.png)

   > [!NOTE]
   > また、**Storm トポロジ**は、**[サーバー エクスプローラー]** で **[Azure]** > **[HDInsight]** の順に展開して、HDInsight クラスターの Storm を右クリックして **[View Storm Topologies]** を選択して表示できます。

    情報を表示するスパウトまたはボルト コンポーネントの形状を選択します。 各アイテムを選択すると新しいウィンドウが開きます。

   > [!NOTE]
   > トポロジの名前は、トポロジのクラス名 (この場合は `HelloWord`) にタイムスタンプを追加したものです。

7. **[トポロジの概要]** ビューで、**[強制終了]** を選択してトポロジを停止します。

   > [!NOTE]
   > Storm トポロジは停止されるまで、またはクラスターが削除されるまで実行し続けます。


## <a name="rest-api"></a>REST API

Storm UI は、REST API を基に構築されているため、REST API を使用して同様の管理や監視機能を実行できます。 REST API を使用して、Storm トポロジの管理や監視用のカスタム ツールを作成できます。

詳細については、「 [Storm UI REST API](https://github.com/apache/storm/blob/0.9.3-branch/STORM-UI-REST-API.md)」を参照してください。 以下は、HDInsight での Apache Storm で REST API を使用する場合の情報です。

### <a name="base-uri"></a>ベース URI

HDInsight クラスターの REST API のベース URI は、**https://&lt;clustername>.azurehdinsight.net/stormui/api/v1/** (**clustername** は HDInsight クラスターの Storm の名前) です。

### <a name="authentication"></a>Authentication

REST API への要求では、HDInsight クラスターの管理者名とパスワードを使用して、 **基本認証**を使用する必要があります。

> [!NOTE]
> 基本認証はクリア テキストを使用して送信されるため、 **常に** HTTPS を使用してクラスターとの通信を保護する必要があります。

### <a name="return-values"></a>戻り値

REST API から返される情報は、クラスターと同じ Azure Virtual Network 上にあるクラスターや仮想マシン上でのみ使用できます。 たとえば、Zookeeper サービスに返された完全修飾ドメイン名 (FQDN) は、インターネットからはアクセスできません。

## <a name="next-steps"></a>次の手順

これまでに、Storm ダッシュボードを使用してトポロジをデプロイし、監視する方法について説明しました。続けて、次を学習します。

* [Visual Studio の HDInsight ツールを使用して C# トポロジを開発する](apache-storm-develop-csharp-visual-studio-topology.md)

* [Maven を使用した Java ベースのトポロジを開発する](apache-storm-develop-java-topology.md)

その他の Storm トポロジ例は、「 [HDInsight 上の Storm に関するトポロジ例](apache-storm-example-topology.md)」をご覧ください。

[hdinsight-dashboard]: ./media/apache-storm-deploy-monitor-topology/dashboard-link.png
[storm-dashboard-submit]: ./media/apache-storm-deploy-monitor-topology/submit.png
[storm-dashboard-ui]: ./media/apache-storm-deploy-monitor-topology/storm-ui-summary.png
