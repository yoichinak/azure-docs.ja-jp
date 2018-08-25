診断データとログ データの収集、表示、分析を通じて VM を監視する手段が数多く用意されています。 VM に対して単純な[監視](../articles/monitoring-and-diagnostics/monitoring-overview-azure-monitor.md)を行うだけであれば、Azure Portal で VM の概要画面を使用できます。 [拡張機能](../articles/virtual-machines/windows/extensions-features.md)を使って VM で診断を構成すれば、さらに詳しいメトリック データを収集することができます。 また、[Application Insights](../articles/application-insights/app-insights-overview.md) や [Log Analytics](../articles/log-analytics/log-analytics-overview.md) など、より高度な監視方法を使用することもできます。

## <a name="diagnostics-and-metrics"></a>診断とメトリック 

Azure Portal、Azure CLI、Azure PowerShell、アプリケーション プログラミング インターフェイス (API) のプログラミングで、[メトリック](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md)を使った[診断データ](https://docs.microsoft.com/cli/azure/vm/diagnostics)の収集を設定し、監視することができます。 たとえば、次のようなことができます。

- **VM の基本的なメトリックを観察する。** Azure Portal の [概要] 画面には、CPU 使用率、ネットワーク使用率、ディスクの総バイト数、1 秒あたりのディスクの操作数など、基本的なメトリックが表示されます。

- **Azure Portal を使ってブート診断の収集を有効にして表示する。** 独自のイメージを Azure に導入した場合に限らず、いずれかのプラットフォーム イメージをブートするときでも、VM は、さまざまな理由で起動できない状態に陥ることがあります。 VM を作成するときに、[設定] 画面の [監視] セクションで [ブート診断] の **[有効]** をクリックすることで、ブート診断を簡単に有効にできます。

    VM が起動すると、ブート診断エージェントによってブート出力がキャプチャされて Azure Storage に格納されます。 VM の起動に関する問題は、このデータを使ってトラブルシューティングすることができます。 コマンド ライン ツールで VM を作成した場合、自動的にはブート診断が有効になりません。 ブート診断を有効にするにはまず、ブート ログを格納するためのストレージ アカウントを作成しておく必要があります。 Azure Portal でブート診断を有効にした場合、ストレージ アカウントが自動的に作成されます。

    VM の作成時に有効にしなかった場合、ブート診断は後から [Azure CLI](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics)、[Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmbootdiagnostics)、または [Azure Resource Manager テンプレート](../articles/virtual-machines/windows/extensions-diagnostics-template.md)を使っていつでも有効にできます。

- **ゲスト OS の診断データの収集を有効にする。** VM を作成するときに、設定画面でゲスト OS の診断を有効にすることができます。 診断データの収集を有効にすると、[Linux 用 IaaSDiagnostics 拡張機能](../articles/virtual-machines/linux/diagnostic-extension.md)または [Windows 用 IaaSDiagnostics 拡張機能](../articles/virtual-machines/windows/ps-extensions-diagnostics.md)が VM に追加され、ディスク、CPU、メモリに関して追加のデータを収集できるようになります。

    収集された診断データを利用して、VM の自動スケールを構成できます。 データを保存するためのログを構成し、パフォーマンスの異常を知らせるアラートを設定することもできます。

## <a name="alerts"></a>アラート

特定のパフォーマンス メトリックに基づいて[アラート](../articles/monitoring-and-diagnostics/monitoring-overview-alerts.md)を作成できます。 たとえば平均 CPU 使用率が特定のしきい値を超えたときや、空きディスク領域が特定の容量を下回ったときなどに、それらの問題に関するアラートを受け取ることができます。 アラートの構成は、[Azure Portal](../articles/monitoring-and-diagnostics/insights-alerts-portal.md)、[Azure PowerShell](../articles/monitoring-and-diagnostics/insights-alerts-powershell.md)、[Azure CLI](../articles/monitoring-and-diagnostics/insights-alerts-command-line-interface.md) のいずれかで行うことができます。

## <a name="azure-service-health"></a>Azure Service Health

[Azure Service Health](../articles/service-health/service-health-overview.md) は、Azureサービスの問題が影響を及ぼす場合に、パーソナライズされたガイダンスとサポートを提供するほか、今後の計画済みメンテナンスに向けた準備を支援します。 Azure Service Health を利用すれば、お客様とそのチームは、対象を絞った柔軟な通知を受けられます。

## <a name="azure-resource-health"></a>Azure Resource Health

[Azure Resource Health](../articles/service-health/resource-health-overview.md) によって、Azure で発生した問題がお客様のリソースに影響を及ぼす場合に、診断とサポートを受けられます。 リソースの現在と過去の正常性に関する情報が表示され、問題を軽減するのに役立ちます。 Resource Health は、Azure のサービスの問題についてサポートが必要な場合にテクニカル サポートを提供します。

## <a name="logs"></a>ログ

[Azure アクティビティ ログ](../articles/monitoring-and-diagnostics/monitoring-overview-activity-logs.md)は、Azure で発生したサブスクリプションレベルのイベントの分析に利用できるサブスクリプション ログです。 このログには、Azure Resource Manager の運用データからサービス正常性イベントの更新まで、さまざまなデータが含まれています。 Azure Portal で [アクティビティ ログ] をクリックすると、VM のログを表示できます。

たとえば、アクティビティ ログでは次のことを実行できます。

- [アクティビティ ログ イベントに対するアラートを設定する](../articles/monitoring-and-diagnostics/monitoring-overview-activity-logs.md)。
- サード パーティーのサービスや PowerBI などのカスタム分析ソリューションで取り込むために、[アクティビティ ログを Event Hubs にストリーミングする](../articles/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md)。
- [PowerBI コンテンツ パック](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)を使用して、アクティビティ ログを PowerBI で分析する。
- アーカイブや手動での検査に使用するためにアクティビティ ログを[ストレージ アカウントに保存](../articles/monitoring-and-diagnostics/monitoring-archive-activity-log.md)する。 ログ プロファイルを使用して、リテンション期間 (日数) を指定できます。

アクティビティ ログ データには、[Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/)、[Azure CLI](https://docs.microsoft.com/cli/azure/monitor)、[Monitor REST API](https://docs.microsoft.com/rest/api/monitor/) のいずれかを使ってアクセスすることもできます

[Azure 診断ログ](../articles/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)は、VM から出力されるログです。その操作に関する豊富なデータが提供されます。 診断ログは、VM 内で実行された操作についての洞察が得られるという点でアクティビティ ログとは異なります。

たとえば、診断ログでは次のことを実行できます。

- 監査や手動での検査に使用するために診断ログを[ストレージ アカウント](../articles/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md)に保存する。 リソース診断設定を使用して、リテンション期間 (日数) を指定できます。
- サード パーティのサービスや PowerBI などのカスタム分析ソリューションで取り込むために、[診断ログを Event Hubs にストリーミング](../articles/monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md)する。
- 診断ログを [OMS Log Analytics](../articles/log-analytics/log-analytics-azure-storage.md) で分析する。

## <a name="advanced-monitoring"></a>高度な監視

- [Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/) は、クラウドとオンプレミスの資産全体にまたがる監視、アラート、アラート修復の機能を提供します。 [Linux VM](../articles/virtual-machines/linux/extensions-oms.md) または [Windows VM](../articles/virtual-machines/windows/extensions-oms.md) に拡張機能をインストールできます。この拡張機能によって、OMS エージェントがインストールされ、VM が既存の OMS ワークスペースに登録されます。

- [Log Analytics](../articles/log-analytics/log-analytics-overview.md) は、OMS のサービスであり、クラウド環境とオンプレミス環境を監視して可用性とパフォーマンスを維持します。 Log Analytics を使用すると、クラウドおよびオンプレミスの環境内にあるリソースによって生成されたデータや、他の監視ツールのデータを収集し、複数のソースにわたる分析を行えます。

    Windows VM および Linux VM の場合、ログとメトリックを収集するための推奨される方法は、Log Analytics エージェントをインストールすることです。 VM に Log Analytics エージェントをインストールする方法としては、[Log Analytics VM 拡張機能](../articles/log-analytics/log-analytics-azure-vm-extension.md)を使用するのが最も簡単です。 この拡張機能を使用すると、インストール プロセスが簡略化され、指定した Log Analytics ワークスペースにデータを送信するようにエージェントが自動的に構成されます。 また、エージェントは自動的にアップグレードされるため、最新の機能と修正プログラムを利用できます。

- [Network Watcher](../articles/network-watcher/network-watcher-monitoring-overview.md) を使用すると、VM とその関連リソースをそれらが属しているネットワークの観点から監視することができます。 Network Watcher Agent 拡張機能は、[Linux VM](../articles/virtual-machines/linux/extensions-nwa.md) または [Windows VM](../articles/virtual-machines/windows/extensions-nwa.md) にインストールできます。

## <a name="next-steps"></a>次の手順
- 「[Azure PowerShell を使用した Windows 仮想マシンの監視](../articles/virtual-machines/windows/tutorial-monitoring.md)」または [Azure CLI を使用した Linux 仮想マシンの監視](../articles/virtual-machines/linux/tutorial-monitoring.md)に関するページの手順を参照します。
- [監視と診断](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)に関するベスト プラクティスについて詳しい情報を入手します。
