---
title: Azure Portal を使用した Log Analytics での Service Fabric アプリケーションの評価 |Microsoft Docs
description: Azure Portal で Log Analytics の Service Fabric ソリューションを使用して、Service Fabric アプリケーション、マイクロサービス、ノード、およびクラスターのリスクと正常性を評価することができます。
services: log-analytics
documentationcenter: ''
author: niniikhena
manager: jochan
editor: ''
ms.assetid: 9c91aacb-c48e-466c-b792-261f25940c0c
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: nini
ms.component: na
ms.openlocfilehash: 9bc1f7d9eab73a086e664dcc520ecf26befbbf0e
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39432893"
---
# <a name="assess-service-fabric-applications-and-micro-services-with-the-azure-portal"></a>Azure Portal を使用して Service Fabric アプリケーションとマイクロ サービスを評価する

> [!div class="op_single_selector"]
> * [Resource Manager](log-analytics-service-fabric-azure-resource-manager.md)
> * [PowerShell](log-analytics-service-fabric.md)
>
>

![Service Fabric のシンボル](./media/log-analytics-service-fabric/service-fabric-assessment-symbol.png)

この記事では、Log Analytics で Service Fabric のソリューションを使用して、Service Fabric クラスター全体でのトラブルシューティングに役立てる方法について説明します。

Service Fabric ソリューションでは Service Fabric VM からの Azure 診断データを使用しますが、このデータは Azure WAD テーブルから収集されます。 Log Analytics は**Reliable Service イベント**、**アクター イベント**、**操作イベント**、および**カスタム ETW イベント**を含む Service Fabric フレームワークのイベントを読み取ります。 ソリューション ダッシュボードを使用して、Service Fabric 環境における注目すべき問題や関連イベントを表示できます。

ソリューションを開始するには、Service Fabric クラスターを Log Analytics ワークスペースに接続する必要があります。 この場合、3 つのシナリオが考えられます。

1. Service Fabric クラスターをデプロイしていない場合は、「***Log Analytics のワークスペースに接続されている Service Fabric クラスターのデプロイ***」の手順を使用して新しいクラスターをデプロイし、Log Analytics をレポートするように構成します。
1. Service Fabric クラスター上でセキュリティなどの他の管理ソリューションを使用するためにホストからパフォーマンス カウンターを収集する必要がある場合は、「 ***VM 拡張機能がインストールされている Log Analytics ワークスペースに接続されている Service Fabric クラスターのデプロイ***」の手順に従います。
1. 既に Service Fabric クラスターをデプロイしてあり、Log Analytics に接続する場合は、「***Log Analytics への既存のストレージ アカウントの追加***」の手順に従います。

## <a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace"></a>Log Analytics ワークスペースに接続されている Service Fabric クラスターをデプロイします。
このテンプレートは、次の処理を実行します。

1. 既に Log Analytics ワークスペースに接続されている Azure Service Fabric クラスターをデプロイします。 テンプレートをデプロイするときに新しいワークスペースを作成するか、既存の Log Analytics ワークスペースの名前を入力します。
1. 診断ストレージ アカウントを Log Analytics ワークスペースに追加します。
1. Service Fabric ソリューションを Log Analytics ワークスペースで有効にします。

[![Azure へのデプロイ](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-oms%2F%2Fazuredeploy.json)

上のデプロイ ボタンを選択すると、Azure Portal が開き、パラメーターを編集できます。 新しい Log Analytics ワークスペース名を入力する場合は、新しいリソース グループを作成してください。

![Service Fabric](./media/log-analytics-service-fabric/2.png)

![Service Fabric](./media/log-analytics-service-fabric/3.png)

法律条項に同意して **[作成]** をクリックすると、デプロイが開始されます。 展開が完了すると、新しいワークスペースと作成したクラスターが表示され、WADServiceFabric*Event、WADWindowsEventLogs、および WADETWEvent テーブルが追加されます。

![Service Fabric](./media/log-analytics-service-fabric/4.png)

## <a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace-with-vm-extension-installed"></a>VM 拡張機能がインストールされている Log Analytics ワークスペースに接続されている Service Fabric クラスターのデプロイ

このテンプレートは、次の処理を実行します。

1. 既に Log Analytics ワークスペースに接続されている Azure Service Fabric クラスターをデプロイします。 新しいワークスペースを作成するか、既存のワークスペースを使用することができます。
1. 診断ストレージ アカウントを Log Analytics ワークスペースに追加します。
1. Log Analytics ワークスペースで、Service Fabric ソリューションを有効にします。
1. Service Fabric クラスター内の各仮想マシン スケール セットで、MMA エージェント拡張機能をインストールします。 MMA エージェントがインストールされている場合、ノードに関するパフォーマンス メトリックを表示できます。

[![Azure へのデプロイ](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-vmss-oms%2F%2Fazuredeploy.json)

前述の手順に従って必要なパラメーターを入力し、展開を開始します。 もう一度、作成された新しいワークスペース、クラスター、および WAD テーブルが表示されます。

![Service Fabric](./media/log-analytics-service-fabric/5.png)

### <a name="viewing-performance-data"></a>パフォーマンス データの表示

ノードからパフォーマンス データを表示するには、次の処理を行います。


[!INCLUDE [log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

- Azure Portal から Log Analytics ワークスペースを起動します。
  ![Service Fabric](./media/log-analytics-service-fabric/6.png)
- 左側のウィンドウの [設定] に移動して、[データ]、[Windows パフォーマンス カウンター] の順に選択して [選選択したパフォーマンス カウンターを追加する] で ![Service Fabric](./media/log-analytics-service-fabric/7.png) を選択します。
- [ログ検索] で、次のクエリを使用してノードに関する主要なメトリックを詳しく調べます。

    a. 過去 1 時間におけるすべてのノードの平均 CPU 使用率を比較して、どのノードで問題が発生し、どの期間にノードの使用率が急上昇しているかを確認します。

    ```
    Type=Perf ObjectName=Processor CounterName="% Processor Time"|measure avg(CounterValue) by Computer Interval 1HOUR.
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/10.png)

    b. 次のクエリを使用して、各ノードで利用可能なメモリについて同様の線グラフを表示します。

    ```
    Type=Perf ObjectName=Memory CounterName="Available MBytes Memory" | measure avg(CounterValue) by Computer Interval 1HOUR.
    ```

    各ノードの Available MBytes の正確な平均値を示すすべてのノードの一覧を表示するには、次のクエリを使用します。

    ```
    Type=Perf (ObjectName=Memory) (CounterName="Available MBytes") | measure avg(CounterValue) by Computer
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/11.png)

    c. 1 時間ごとの平均値、最小値、最大値、および 75 パーセンタイルの CPU 使用率を調べることによって特定のノードにドリルダウンする場合は、次のクエリを使用できます ([Computer] フィールドを置き換えます)。

    ```
    Type=Perf CounterName="% Processor Time" InstanceName=_Total Computer="BaconDC01.BaconLand.com"| measure min(CounterValue), avg(CounterValue), percentile75(CounterValue), max(CounterValue) by Computer Interval 1HOUR
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/12.png)

Log Analytics でのパフォーマンス メトリックの詳細については、「[Operations Management Suite blog (Operations Management Suite ブログ)](https://blogs.technet.microsoft.com/msoms/tag/metrics/)」をご覧ください。


## <a name="adding-an-existing-storage-account-to-log-analytics"></a>既存のストレージ アカウントの Log Analytics への追加

このテンプレートは、既存のストレージ アカウントを新規または既存の Log Analytics ワークスペースに追加します。

[![Azure へのデプロイ](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Foms-existing-storage-account%2Fazuredeploy.json)

> [!NOTE]
> リソース グループを選択する際に、既存の Log Analytics ワークスペースを使用している場合は、[既存のものを使用] を選択して Log Analytics ワークスペースが含まれたリソース グループを検索します。 そうでない場合は、新規の Log Analytics を作成してください。
> ![Service Fabric](./media/log-analytics-service-fabric/8.png)
>
>

このテンプレートがデプロイされた後は、Log Analytics ワークスペースに接続されたストレージ アカウントを表示することができます。 このインスタンスでは、上記で作成した Exchange ワークスペースにストレージ アカウントをもう 1 つ追加しました。
![Service Fabric](./media/log-analytics-service-fabric/9.png)

## <a name="view-service-fabric-events"></a>Service Fabric イベントの表示

展開が完了し、Service Fabric ソリューションがワークスペースで有効になったら、Log Analytics ポータルで **[Service Fabric]** タイルを選択して Service Fabric ダッシュ ボードを起動します。 ダッシュボードには、次の表に示した列が存在します。 それぞれの列には、特定の時間の範囲について、その列の基準に該当するイベント数の上位 10 件が表示されます。 ログ検索を実行してアラート全件を取得するには、各列の右下にある **[See all]** (すべて表示) をクリックするか、列ヘッダーをクリックします。

| **Service Fabric イベント** | **description** |
| --- | --- |
| Notable Issues (注目すべき問題) |runAsync の失敗やキャンセル、およびノードの停止などの問題の表示。 |
| 操作イベント |アプリケーションのアップグレードや展開などの注目すべき操作イベント。 |
| Reliable Service イベント |runAsync の呼び出しなどの注目すべき Reliable Service イベント。 |
| アクター イベント |アクター メソッドによってスローされた例外、アクターのアクティブ化と非アクティブ化などのマイクロ サービスによって生成された、注目すべきアクター イベント。 |
| アプリケーション イベント |アプリケーションによって生成されたすべてのカスタム ETW イベント。 |

![Service Fabric ダッシュ ボード](./media/log-analytics-service-fabric/sf3.png)

![Service Fabric ダッシュ ボード](./media/log-analytics-service-fabric/sf4.png)

次の表は、Service Fabric のデータ収集手段とデータ収集方法に関する各種情報をまとめたものです。

| プラットフォーム | 直接エージェント | Operations Manager エージェント | Azure Storage | Operations Manager が必要か | 管理グループによって送信される Operations Manager エージェントのデータ | 収集の頻度 |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |  |  | &#8226; |  |  |10 分 |

> [!NOTE]
> ダッシュボードの上部にある **[Data based on last 7 days] \(過去 7 日間に基づくデータ)** をクリックして、Service Fabric ソリューションのこれらのイベントの範囲を変更することができます。 また、過去 7 日、過去 1 日、過去 6 時間のいずれかの時間範囲内に生成されたイベントを表示できます。 **[Custom]** (カスタム) を選択して、独自の日付範囲を指定することもできます。
>
>

## <a name="next-steps"></a>次の手順

* [Log Analytics のログ検索機能](log-analytics-log-searches.md) を使用して、詳細な Service Fabric イベント データを確認してください。
