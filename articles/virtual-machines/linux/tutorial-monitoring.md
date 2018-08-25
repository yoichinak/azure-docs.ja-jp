---
title: チュートリアル - Azure で Linux 仮想マシンの監視と更新を行う | Microsoft Docs
description: このチュートリアルでは、Linux 仮想マシンを対象に、ブート診断とパフォーマンス メトリックを監視する方法と、パッケージの更新を管理する方法について説明します
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/06/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: f5773d2f6634d1de9674351ff30a15b488bdd672
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38597465"
---
# <a name="tutorial-monitor-and-update-a-linux-virtual-machine-in-azure"></a>チュートリアル: Azure で Linux 仮想マシンの監視と更新を行う

Azure の仮想マシン (VM) が正常に実行されていることを確認するためには、ブート診断とパフォーマンス メトリックを確認し、パッケージの更新を管理します。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * VM でブート診断を有効にする
> * ブート診断を表示する
> * ホストのメトリックを表示する
> * VM で診断拡張機能を有効にする
> * VM のメトリックを表示する
> * 診断のメトリックに基づくアラートを作成する
> * パッケージの更新を管理する
> * 変更とインベントリを監視する
> * 高度な監視をセットアップする

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.30 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。

## <a name="create-vm"></a>VM を作成する

診断とメトリックの動作を確認するには、VM が必要です。 最初に、[az group create](/cli/azure/group#az-group-create) を使用して、リソース グループを作成します。 次の例では、*myResourceGroupMonitor* という名前のリソース グループを場所 *eastus* に作成します。

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

ここで [az vm create](/cli/azure/vm#az-vm-create) を使用して VM を作成します。 次の例では、*myVM* という名前の VM を作成し、SSH キーを生成します (*~/.ssh/* にまだ存在していない場合)。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="enable-boot-diagnostics"></a>ブート診断を有効にする

Linux VM が起動すると、ブート診断拡張機能によってブート出力がキャプチャされて Azure Storage に格納されます。 VM の起動に関する問題は、このデータを使ってトラブルシューティングすることができます。 Azure CLI を使用して Linux VM を作成した場合、ブート診断が自動的に有効になりません。

ブート診断を有効にするにはまず、ブート ログを格納するためのストレージ アカウントを作成しておく必要があります。 ストレージ アカウントには、グローバルに一意の名前が必要です。名前は 3 ～ 24 文字とし、数字と小文字のみを使用できます。 ストレージ アカウントは、[az storage account create](/cli/azure/storage/account#az-storage-account-create) コマンドで作成します。 この例では、ランダムな文字列を使って一意のストレージ アカウント名を作成しています。

```azurecli-interactive
storageacct=mydiagdata$RANDOM

az storage account create \
  --resource-group myResourceGroupMonitor \
  --name $storageacct \
  --sku Standard_LRS \
  --location eastus
```

ブート診断を有効にするときは、Blob Storage コンテナーの URI が必要となります。 次のコマンドは、ストレージ アカウントを照会して、この URI を取得しています。 この URI 値を *bloburi* という変数に格納しておき、次の手順で使用します。

```azurecli-interactive
bloburi=$(az storage account show --resource-group myResourceGroupMonitor --name $storageacct --query 'primaryEndpoints.blob' -o tsv)
```

今度は、[az vm boot-diagnostics enable](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-enable) を使用して、ブート診断を有効にします。 `--storage` の値は、前の手順で取得した BLOB の URI です。

```azurecli-interactive
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```

## <a name="view-boot-diagnostics"></a>ブート診断を表示する

ブート診断が有効になっている場合、VM を停止して起動するたびに、ブート プロセスに関する情報がログ ファイルに書き込まれます。 この例ではまず、次のように [az vm deallocate](/cli/azure/vm#az-vm-deallocate) コマンドで VM の割り当てを解除します。

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

次に、[az vm start]( /cli/azure/vm#az-vm-stop) コマンドで VM を起動します。

```azurecli-interactive
az vm start --resource-group myResourceGroupMonitor --name myVM
```

*myVM* のブート診断データは、次のように [az vm boot-diagnostics get-boot-log](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-get-boot-log) コマンドで取得できます。

```azurecli-interactive
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```

## <a name="view-host-metrics"></a>ホストのメトリックを表示する

Azure には、Linux VM と連動する専用のホストがあります。 メトリックは、そのホストを対象に自動的に収集され、Azure Portal に次のように表示されます。

1. Azure Portal で **[リソース グループ]** を選択し、**[myResourceGroupMonitor]** を選択します。次に、リソース一覧から **[myVM]** を選択します。
1. ホスト VM の実行状況を確認するには、VM ウィンドウの **[メトリック]** を選択し、**[利用可能なメトリック]** からいずれかの *[Host]* メトリックを選択します。

    ![ホストのメトリックを表示する](./media/tutorial-monitoring/monitor-host-metrics.png)

## <a name="install-diagnostics-extension"></a>診断拡張機能をインストールする

基本的なホスト メトリックは利用できますが、さらに粒度の細かい VM 固有のメトリックを表示するためには、Azure 診断拡張機能を VM にインストールする必要があります。 Azure 診断拡張機能を通じて、より詳しい監視データと診断データを VM から取得することができます。 これらのパフォーマンス メトリックを確認したり、VM のパフォーマンスに基づくアラートを作成したりすることができます。 診断拡張機能は、次のように Azure Portal からインストールします。

1. Azure Portal で **[リソース グループ]** を選択し、**[myResourceGroupMonitor]** を選択します。次に、リソース一覧から **[myVM]** を選択します。
1. **[診断の設定]** を選択します。 *[ストレージ アカウントの選択]* ドロップダウン メニューから、前のセクションで作成した *mydiagdata [1234]* アカウントを選択します (まだ選択されていない場合)。
1. **[ゲスト レベルの監視を有効にする]** ボタンを選択します。

    ![診断メトリックの表示](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>VM のメトリックを表示する

VM のメトリックは、ホスト VM のメトリックと同じ方法で表示できます。

1. Azure Portal で **[リソース グループ]** を選択し、**[myResourceGroupMonitor]** を選択します。次に、リソース一覧から **[myVM]** を選択します。
1. VM の実行状況を確認するには、VM ウィンドウの **[メトリック]** を選択し、**[利用可能なメトリック]** からいずれかの "*[ゲスト]*" 診断メトリックを選択します。

    ![VM のメトリックを表示する](./media/tutorial-monitoring/monitor-vm-metrics.png)

## <a name="create-alerts"></a>アラートを作成する

特定のパフォーマンス メトリックに基づいてアラートを作成することができます。 平均 CPU 使用率が特定のしきい値を超えたときや、空きディスク領域が特定の容量を下回ったときなどに、アラートによって通知を受け取ることができます。 アラートは、Azure Portal に表示されるほか、電子メールで送信することもできます。 アラートが生成されたことへの対応として、Azure Automation Runbook または Azure Logic Apps をトリガーすることもできます。

平均 CPU 使用率のアラートを作成する例を次に示します。

1. Azure Portal で **[リソース グループ]** を選択し、**[myResourceGroupMonitor]** を選択します。次に、リソース一覧から **[myVM]** を選択します。
2. **[アラート (クラシック)]** を選択し、[アラート] ウィンドウの上部にある **[メトリック アラートの追加 (クラシック)]** を選択します。
3. **[名前]** にアラートの名前を入力します (例: *myAlertRule*)。
4. CPU の割合が 1.0 を超えた状態が 5 分間続いたときにアラートをトリガーするために、それ以外の選択肢はすべて既定値のままにします。
5. 必要に応じて *[所有者、共同作成者、閲覧者に電子メールを送信]* のチェック ボックスをオンにして、電子メール通知を送信することもできます。 既定のアクションでは、ポータルに通知が表示されます。
6. **[OK]**  ボタンを選択します。

## <a name="manage-package-updates"></a>パッケージの更新を管理する

更新管理では、Azure Linux VM の更新プログラムとパッチを管理できます。
VM から直接、利用可能な更新プログラムのステータスを迅速に把握したり、必要な更新プログラムのインストールをスケジュールしたり、デプロイの結果を確認して、VM に更新プログラムが正常に適用されたことを検証したりできます。

価格情報については、[Update Management の Automation の価格](https://azure.microsoft.com/pricing/details/automation/)に関するページをご覧ください。

### <a name="enable-update-management"></a>Update Management の有効化

お使いの VM で Update Management を有効にする

1. 画面左側の **[仮想マシン]** を選択します。
2. 一覧から VM を選択します。
3. [VM] 画面の **[操作]** セクションで、**[更新の管理]** を選択します。 **[更新管理の有効化]** 画面が開きます。

この VM で Update Management が有効になっているかを確認する検証が行われます。
この検証では、Log Analytics ワークスペースの確認、リンクされた Automation アカウントの確認、ソリューションがワークスペースにあるかどうかの確認が行われます。

[Log Analytics](../../log-analytics/log-analytics-overview.md) ワークスペースは、Update Management のような機能およびサービスによって生成されるデータを収集するために使用されます。
ワークスペースには、複数のソースからのデータを確認および分析する場所が 1 つ用意されています。
更新を必要とする VM で追加のアクションを実行する場合、Azure Automation を使用すると、VM に対して Runbook を実行して、更新プログラムをダウンロードして適用するなどの操作を行うことができます。

また、検証プロセスでは、VM が Microsoft Monitoring Agent (MMA) と Automation ハイブリッド Runbook worker でプロビジョニングされているかどうかが確認されます。
このエージェントは VM との通信に使用され、更新ステータスに関する情報を取得します。

Log Analytics ワークスペースと Automation アカウントを選択し、**[有効にする]** を選択して、ソリューションを有効にします。 ソリューションを有効にするには最大 15 分かかります。

オンボード中に次の前提条件のいずれかを満たしていないことがわかった場合は、自動的に追加されます。

* [Log Analytics](../../log-analytics/log-analytics-overview.md) ワークスペース
* [Automation](../../automation/automation-offering-get-started.md)
* [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) が VM で有効になっている

**[更新の管理]** 画面が開きます。 使用する場所、Log Analytics ワークスペース、Automation アカウントを構成し、**[有効にする]** を選択します。 フィールドが淡色表示されている場合は、その VM で別の Automation ソリューションが有効になっているため、同じワークスペースと Automation アカウントを使用する必要があることを示します。

![Update Management ソリューションを有効にする](./media/tutorial-monitoring/manage-updates-update-enable.png)

ソリューションを有効にするには最大 15 分かかります。 この処理中はブラウザーのウィンドウは閉じないでください。 ソリューションが有効になると、VM 上の不足している更新プログラムの情報が Log Analytics に送られます。 データの分析に使用できるようになるまでに、30 分から 6 時間かかる場合があります。

### <a name="view-update-assessment"></a>更新の評価を確認する

**更新管理**が有効になると、**[更新管理]** 画面が表示されます。 更新の評価が完了すると、**[不足している更新プログラム]** タブに、不足している更新プログラムの一覧が表示されます。

 ![更新ステータスを確認する](./media/tutorial-monitoring/manage-updates-view-status-linux.png)

### <a name="schedule-an-update-deployment"></a>更新プログラムのデプロイをスケジュールする

更新プログラムをインストールするには、リリース スケジュールとサービス期間に従ってデプロイをスケジュールします。 デプロイに含める更新の種類を選択できます。 たとえば、緊急更新プログラムやセキュリティ更新プログラムを追加し、更新プログラムのロールアップを除外できます。

VM の新しい更新プログラムのデプロイをスケジュールするには、**[更新の管理]** 画面の上部にある **[更新プログラムの展開のスケジュール]** を選択します。 **[新しい更新プログラムの展開]** 画面で、次の情報を指定します。

* **名前**: 更新プログラムのデプロイを識別する一意の名前を指定します。
* **更新プログラムの分類** - 更新プログラムのデプロイによってデプロイに追加されたソフトウェアの種類を選択します。 分類の種類は次のとおりです。
  * 緊急更新プログラムとセキュリティ更新プログラム
  * 他の更新プログラム
* **更新して除外する** - 更新プログラムのデプロイ時にスキップされる一連のパッケージ名を指定することができます。 パッケージ名には、ワイルドカード (「\*kernal\*」など) がサポートされます。

  ![更新プログラムのスケジュール設定画面](./media/tutorial-monitoring/manage-updates-exclude-linux.png)

* **スケジュール設定**: 既定の日時 (現在の時刻から 30 分後) を使用するか、別の日時を指定します。
  デプロイを 1 回行うか、定期的なスケジュールを設定するかを指定することもできます。 定期的なスケジュールを設定するには、[繰り返し] の下の [繰り返し] オプションを選択します。

  ![更新プログラムのスケジュール設定画面](./media/tutorial-monitoring/manage-updates-schedule-linux.png)

* **メンテナンス期間 (分)**: 更新プログラムをデプロイする期間を指定します。 これにより、定義したサービス期間内に変更が確実に実行されます。

スケジュールの構成が完了したら、**[作成]** ボタンを選択して、状態ダッシュボードに戻ります。
**スケジュール済み**の表に、作成したデプロイ スケジュールが表示されていることを確認してください。

> [!WARNING]
> 更新に再起動が必要な場合、VM は自動的に再起動されます。

### <a name="view-results-of-an-update-deployment"></a>更新プログラムのデプロイの結果を表示する

スケジュールされた展開の開始後、**[更新管理]** 画面の **[更新プログラムの展開]** タブに、展開の状態が表示されます。
実行中の場合、状態は **[処理中]** と表示されます。 正常に完了すると、状態は **[成功]** に変わります。
デプロイ時に 1 つ以上の更新プログラムでエラーが発生した場合、状態は **[部分的に失敗]** になります。
完了した更新プログラムのデプロイを選択すると、その更新プログラムのデプロイ用のダッシュボードが表示されます。

![特定のデプロイに関する更新プログラムのデプロイ ステータスのダッシュボード](./media/tutorial-monitoring/manage-updates-view-results.png)

**[新プログラムを実行した結果]** のタイルに表示されるのは、VM 上の更新プログラムの合計数とデプロイ結果の概要です。
右側の表に、各更新プログラムとインストールの結果の詳細が示されます。これは、次の値のいずれかになります。

* **試行されていません**: メンテナンス期間として定義した時間が十分ではなかったため、更新プログラムがインストールされませんでした。
* **成功**: 更新できました。
* **失敗**: 更新できませんでした。

展開によって作成されたログ エントリをすべて表示するには、**[すべてのログ]** を選択します。

ターゲット VM での更新プログラムのデプロイを管理する Runbook のジョブ ストリームを確認するには、**[出力]** タイルを選択します。

展開で発生したエラーの詳細情報を確認するには、**[エラー]** を選択します。

## <a name="monitor-changes-and-inventory"></a>変更とインベントリを監視する

コンピューター上のソフトウェア、ファイル、Linux デーモン、Windows サービス、Windows レジストリ キーのインベントリを収集し、表示することができます。 マシンの構成を追跡すると、環境全体の運用上の問題を特定し、マシンの状態をよりよく理解することができます。

### <a name="enable-change-and-inventory-management"></a>変更とインベントリの管理を有効にする

使用している VM の変更とインベントリの管理を有効にします。

1. 画面左側の **[仮想マシン]** を選択します。
2. 一覧から VM を選択します。
3. VM 画面で、**[操作]** セクションの **[インベントリ]** または **[変更の追跡]** を選択します。 **[Enable Change Tracking and Inventory]\(変更の追跡とインベントリの有効化\)** 画面が開きます。

使用する場所、Log Analytics ワークスペース、Automation アカウントを構成し、**[有効にする]** を選択します。 フィールドが淡色表示されている場合は、その VM で別の Automation ソリューションが有効になっているため、同じワークスペースと Automation アカウントを使用する必要があることを示します。 メニューではソリューションは別々に表示されますが、同じソリューションです。 一方を有効にすると、使用している VM に対して両方が有効になります。

![変更とインベントリの追跡を有効にする](./media/tutorial-monitoring/manage-inventory-enable.png)

ソリューションを有効にした後、VM でインベントリが収集され、データが表示されるまでに時間がかかる場合があります。

### <a name="track-changes"></a>変更を追跡する

使用している VM で、**[操作]** の **[変更の追跡]** を選択します。 **[設定の編集]** を選択すると、**[変更の追跡]** ページが表示されます。 追跡する設定の種類を選択し、**[+ 追加]** を選択して、設定を構成します。 Linux の場合は、**[Linux ファイル]** を選択できます。

変更の追跡の詳細については、[VM での変更のトラブルシューティング](../../automation/automation-tutorial-troubleshoot-changes.md)に関するページをご覧ください

### <a name="view-inventory"></a>インベントリを表示する

使用している VM で、**[操作]** の **[インベントリ]** を選択します。 **[ソフトウェア]** タブには、検出されたソフトウェアの一覧が表示されます。 各ソフトウェア レコードの詳細情報を表で確認できます。 これらの詳細には、ソフトウェアの名前、バージョン、発行元、最終更新時刻などが含まれます。

![インベントリを表示する](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>アクティビティ ログおよび変更を監視する

VM の **[変更履歴]** ページから **[アクティビティ ログ接続の管理]** を選択します。 このタスクで、**[Azure アクティビティ ログ]** ページが開きます。 **[接続]** を選択して、変更履歴を VM の Azure アクティビティ ログに接続します。

この設定を有効にして、VM の **[概要]** ページに移動し、**[停止]** を選択して VM を停止します。 メッセージが表示されたら、**[はい]** を選択して VM を停止します。 割り当てが解除されたら、**[スタート]** を選択して VM を再起動します。

VM の起動時と停止時には、イベントがアクティビティ ログに記録されます。 **[変更履歴]** ページに戻ります。 ページの下部にある **[イベント]** タブを選択します。 しばらくすると、グラフと表にイベントが表示されます。 各イベントを選択して、そのイベントの詳細情報を表示できます。

![アクティビティ ログで変更を表示する](./media/tutorial-monitoring/manage-activitylog-view-results.png)

グラフには、時間の経過とともに発生した変更が表示されます。 アクティビティ ログ接続を追加すると、上部の折れ線グラフに Azure アクティビティ ログ イベントが表示されます。 棒グラフの各行は、さまざまな追跡可能な変更の種類を表します。 具体的な種類としては、Linux のデーモン、ファイル、ソフトウェアがあります。 [変更] タブには、変更が発生した時刻の降順 (最新のものから順に) で、変更の詳細が図示されます。

## <a name="advanced-monitoring"></a>高度な監視

使用している VM をさらに詳しく監視するには、[Azure Automation](../../automation/automation-intro.md) で提供される変更やインベントリ、Update Management などのソリューションを使用します。

Log Analytics ワークスペースにアクセスして、ワークスペース キーとワークスペース識別子を確認するには、**[設定]** の **[詳細設定]** を選択します。 以下のように **az vm extension set** を使用して拡張機能を VM に追加できます。\<workspace-key\> と \<workspace-id\> は、実際の Log Analytics ワークスペースの値に置き換えてください。

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroupMonitor \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.3 \
  --protected-settings '{"workspaceKey": "<workspace-key>"}' \
  --settings '{"workspaceId": "<workspace-id>"}'
```

しばらくすると、Log Analytics ワークスペースに新しい VM が表示されます。

![OMS ブレード](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、VM 向けに更新プログラムを構成、確認、管理しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * VM でブート診断を有効にする
> * ブート診断を表示する
> * ホストのメトリックを表示する
> * VM で診断拡張機能を有効にする
> * VM のメトリックを表示する
> * 診断のメトリックに基づくアラートを作成する
> * パッケージの更新を管理する
> * 変更とインベントリを監視する
> * 高度な監視をセットアップする

次のチュートリアルに進み、Azure Security Center について学習してください。

> [!div class="nextstepaction"]
> [VM のセキュリティの管理](./tutorial-azure-security.md)
