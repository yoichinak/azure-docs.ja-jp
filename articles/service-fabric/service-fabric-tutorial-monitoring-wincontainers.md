---
title: Azure で Service Fabric 上の Windows コンテナーを監視および診断する | Microsoft Docs
description: このチュートリアルでは、Azure Service Fabric 上の Windows コンテナーの監視と診断用に Log Analytics を構成します。
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/08/2018
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: b013627c5a0dc596c9897d7fa2c5bf2b2a79ee40
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114008"
---
# <a name="tutorial-monitor-windows-containers-on-service-fabric-using-log-analytics"></a>チュートリアル: Log Analytics を使用して Service Fabric 上の Windows コンテナーを監視する

これはチュートリアルの第 2 部です。Service Fabric で調整された Windows コンテナーを監視するように Log Analytics を設定する手順について説明します。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Service Fabric クラスターの Log Analytics を構成する
> * コンテナーとノードのログの表示とクエリに Log Analytics ワークスペースを使用する
> * Log Analytics エージェントを構成してコンテナーとノード メトリックを選択する

## <a name="prerequisites"></a>前提条件

このチュートリアルを始める前に、次の準備が必要です。

* Azure にクラスターを用意する、または[このチュートリアルを参照して作成する](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [コンテナー化されたアプリケーションをクラスターにデプロイする](service-fabric-host-app-in-a-container.md)

## <a name="setting-up-log-analytics-with-your-cluster-in-the-resource-manager-template"></a>Resource Manager テンプレートでクラスターを使用して Log Analytics を設定する

このチュートリアルの第 1 部で[提供されたテンプレート](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Tutorial)を使用した場合、汎用の Service Fabric Azure Resource Manager テンプレートに次の追加を行う必要があります。 Log Analytics を使用したコンテナーの監視を設定するために独自のクラスターを用意した場合:

* Resource Manager テンプレートに次の変更を加えます。
* PowerShell を使用してデプロイし、[テンプレートをデプロイ](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)してクラスターをアップグレードします。 Azure Resource Manager はリソースが存在することを認識しているので、アップグレードとして展開されます。

### <a name="adding-log-analytics-to-your-cluster-template"></a>クラスター テンプレートに Log Analytics を追加する

*template.json* に次の変更を加えます。

1. Log Analytics ワークスペースの場所と名前を *parameters* セクションに追加します。

    ```json
    "omsWorkspacename": {
      "type": "string",
      "defaultValue": "[toLower(concat('sf',uniqueString(resourceGroup().id)))]",
      "metadata": {
        "description": "Name of your Log Analytics Workspace"
      }
    },
    "omsRegion": {
      "type": "string",
      "defaultValue": "East US",
      "allowedValues": [
        "West Europe",
        "East US",
        "Southeast Asia"
      ],
      "metadata": {
        "description": "Specify the Azure Region for your Log Analytics workspace"
      }
    }
    ```

    いずれかに使用した値を変更するには、*template.parameters.json* に同じパラメーターを追加して、そのパラメーターに使用されている値を変更します。

2. ソリューション名とソリューションを *variables* に追加します。

    ```json
    "omsSolutionName": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "omsSolution": "ServiceFabric"
    ```

3. 仮想マシン拡張機能として Microsoft Monitoring Agent を追加します。 仮想マシン スケール セット リソース *resources* > *"apiVersion": "[variables('vmssApiVersion')]"* を見つけます。 *properties* > *virtualMachineProfile* > *extensionProfile* > *extensions* 以下の *ServiceFabricNode* 拡張機能以下に次の拡張機能の説明を追加します。 
    
    ```json
    {
        "name": "[concat(variables('vmNodeType0Name'),'OMS')]",
        "properties": {
            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
            "type": "MicrosoftMonitoringAgent",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
            },
            "protectedSettings": {
                "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
            }
        }
    },
    ```

4. Log Analytics ワークスペースを個別のリソースとして追加します。 *resources* で、仮想マシン スケール セット リソースの後に次を追加します。

    ```json
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [ ],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "name": "System",
                "type": "datasources",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsEvent",
                "properties": {
                    "eventLogName": "System",
                    "eventTypes": [
                        {
                            "eventType": "Error"
                        },
                        {
                            "eventType": "Warning"
                        },
                        {
                            "eventType": "Information"
                        }
                    ]
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('omsSolutionName')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('OMSWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('omsSolutionName')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('omsSolution'))]",
            "promotionCode": ""
        }
    },
    ```

サンプル テンプレートは[こちら](https://github.com/ChackDan/Service-Fabric/blob/master/ARM%20Templates/Tutorial/azuredeploy.json)です (このチュートリアルの第 1 部で使用されました)。これらの変更がすべて加えられており、必要に応じて参照できます。 これらの変更で、Log Analytics ワークスペースがリソース グループに追加されます。 [Microsoft Azure 診断](service-fabric-diagnostics-event-aggregation-wad.md)エージェントで構成されたストレージ テーブルから、Service Fabric プラットフォーム イベントを選択するようにワークスペースが構成されます。 Log Analytics エージェント (Microsoft Monitoring Agent) も、仮想マシン拡張機能としてクラスターの各ノードに追加されます。つまり、クラスターを拡大縮小すると、各マシンのエージェントは自動的に構成され、同じワークスペースに接続されます。

新しい変更を加えたテンプレートをデプロイして、現在のクラスターをアップグレードします。 処理が完了すると、リソース グループに Log Analytics リソースが表示されます。 クラスターの準備ができたら、コンテナー化されたアプリケーションをデプロイします。 次の手順では、コンテナーの監視を設定します。

## <a name="add-the-container-monitoring-solution-to-your-log-analytics-workspace"></a>Log Analytics ワークスペースにコンテナー監視ソリューションを追加する

ワークスペースでコンテナー ソリューションを設定するには、*コンテナー監視ソリューション*を検索し、([監視 + 管理] カテゴリの下に) コンテナー リソースを作成します。

![コンテナー ソリューションの追加](./media/service-fabric-tutorial-monitoring-wincontainers/containers-solution.png)

"*Log Analytics ワークスペース*" の入力を求められたら、リソース グループに作成したワークスペースを選択し、**[作成]** をクリックします。 "*コンテナー監視ソリューション*" がワークスペースに追加され、テンプレートによって Log Analytics エージェントがデプロイされ、Docker ログと統計情報の収集が開始されます。 

*リソース グループ*に戻ると、新しく追加された監視ソリューションが表示されます。 ソリューションをクリックすると、ランディング ページに実行中のコンテナー イメージ数が表示されます。

*チュートリアルの[第 2 部](service-fabric-host-app-in-a-container.md)の fabrikam コンテナーのインスタンスが 5 個実行されていたことがわかります*

![コンテナー ソリューションのランディング ページ](./media/service-fabric-tutorial-monitoring-wincontainers/solution-landing.png)

**コンテナー監視ソリューション**をクリックすると、詳細なダッシュボードが表示されます。ここでは、複数のパネルをスクロールしたり、Log Analytics でクエリを実行したりすることができます。

*2017 年 9 月の時点で、ソリューションは何度か更新されました。Kubernetes イベントに関するエラーが表示されることがありますが、無視してください。現在、複数のオーケストレーターを同じソリューションに統合する処理に取り組んでいます。*

エージェントが Docker ログを選択しているので、既定で *stdout* と *stderr* が表示されます。 右にスクロールすると、コンテナー イメージのインベントリ、状態、メトリック、さらに役立つデータを入手するために実行できるサンプル クエリが表示されます。

![コンテナー ソリューションのダッシュボード](./media/service-fabric-tutorial-monitoring-wincontainers/container-metrics.png)

これらのパネルのいずれかをクリックすると、表示値を生成する Log Analytics クエリが表示されます。 このクエリを *\** に変更すると、選択されている全種類のログが表示されます。 ここから、コンテナーのパフォーマンスやログのクエリやフィルターを実行したり、Service Fabric プラットフォームのイベントを確認したりすることができます。 また、エージェントは、各ノードから常にハートビートを発しているので、クラスターの構成が変わった場合に、すべてのコンピューターからデータが収集されていることをハートビートによって確認することができます。

![コンテナーのクエリ](./media/service-fabric-tutorial-monitoring-wincontainers/query-sample.png)

## <a name="configure-log-analytics-agent-to-pick-up-performance-counters"></a>パフォーマンス カウンターを選択するように Log Analytics エージェントを構成する

Log Analytics エージェントを使用するもう 1 つの利点として、Azure 診断エージェントを構成し、毎回 Resource Manager テンプレート ベースのアップグレードを実行するのではなく、Log Analytics UI 操作で選択可能なパフォーマンス カウンターを変更できる点があります。 これを行うには、コンテナー監視 (または Service Fabric) ソリューションのランディング ページで **[OMS ワークスペース]** をクリックします。

Log Analytics ワークスペースに移動します。ここでは、ソリューションの確認、カスタム ダッシュボードの作成、Log Analytics エージェントの構成を行うことができます。 
* [詳細設定] メニューを開くには、**[詳細設定]** をクリックします。
* **[接続されたソース]** > **[Windows Server]** の順にクリックし、*"5 台の Windows コンピューターが接続されています"* と表示されることを確認します。
* **[データ]** > **[Windows パフォーマンス カウンター]** の順にクリックし、新しいパフォーマンス カウンターを検索して追加します。 この画面には、収集できるパフォーマンス カウンターの Log Analytics のレコメンデーション一覧と、他のカウンターを検索するオプションが表示されます。 **Processor(_Total)\% Processor Time** カウンターと **Memory(*)\Available MBytes** カウンターが収集されていることを確認します。

数分後にコンテナー監視ソリューションを**更新**すると、*コンピューターのパフォーマンス* データが表示されるようになります。 このデータから、リソースの使用状況を把握することができます。 また、これらのメトリックを使用して、クラスターの拡大縮小に関する適切な判断を下すことができます。また、クラスターが期待どおりに負荷を分散しているかどうかを確認することができます。

*注: これらのメトリックを使用するには、時間フィルターが適切に設定されていることを確認します。*

![パフォーマンス カウンター 2](./media/service-fabric-tutorial-monitoring-wincontainers/perf-counters2.png)

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * Service Fabric クラスターの Log Analytics を構成する
> * コンテナーとノードのログの表示とクエリに Log Analytics ワークスペースを使用する
> * Log Analytics エージェントを構成してコンテナーとノード メトリックを選択する

コンテナー化されたアプリケーションの監視を設定したので、以下を試してみましょう。

* 上記と同様の手順で、Linux クラスター用に Log Analytics を設定する。 [このテンプレート](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux)を参照して、Resource Manager テンプレートを変更してみましょう。
* Log Analytics を構成して、検出と診断に役立つ[自動アラート](../log-analytics/log-analytics-alerts.md)を設定する。
* Service Fabric の[推奨されるパフォーマンス カウンター](service-fabric-diagnostics-event-generation-perf.md)の一覧を参照して、実際のクラスターに合わせて構成する。
* Log Analytics の一部として提供されている[ログ検索とクエリ](../log-analytics/log-analytics-log-searches.md)機能に詳しくなる。