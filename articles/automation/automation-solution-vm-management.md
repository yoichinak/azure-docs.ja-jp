---
title: Start/Stop VMs during off-hours ソリューション
description: この VM 管理ソリューションは、スケジュールに従って Azure Resource Manager 仮想マシンを起動および停止し、Log Analytics からプロアクティブに監視します。
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 08/1/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f272ac7ee6432b43d0c9a72daf620a46e52366f8
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399051"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Azure Automation でのピーク時間外 VM 起動/停止ソリューション

Start/Stop VMs during off-hours ソリューションでは、ユーザー定義のスケジュールで Azure Virtual Machines を起動および停止し、Azure Log Analytics によって分析情報を提供して、[アクション グループ](../monitoring-and-diagnostics/monitoring-action-groups.md)を使用することで必要に応じて電子メールを送信します。 ほとんどのシナリオで Azure Resource Manager とクラシック VM の両方がサポートされます。

このソリューションは、VM のコストを最適化する必要があるユーザー向けに、分散型の低コストなオートメーション オプションを提供します。 このソリューションでは次のことが可能です。

- VM の起動および停止をスケジュールする。
- Azure タグを使用して昇順で VM の起動および停止をスケジュールする (クラシック VM ではサポートされません)。
- CPU 使用率の低さに基づいて VM を自動停止する。

現在のソリューションの制限事項は次のとおりです。

- このソリューションは任意のリージョンの VM を管理しますが、Azure Automation アカウントと同じサブスクリプションでのみ使用できます。
- このソリューションは、Azure および AzureGov から、Log Analytics ワークスペース、Azure Automation アカウント、および Alerts をサポートする任意のリージョン対して利用できます。 現在、AzureGov リージョンは電子メール機能をサポートしていません。

## <a name="prerequisites"></a>前提条件

このソリューションの Runbook は、[Azure 実行アカウント](automation-create-runas-account.md)で動作します。 認証方法としては、実行アカウントの使用をお勧めします。有効期限が切れたり頻繁に変わったりするパスワードではなく、証明書を使った認証が使用されるためです。

## <a name="deploy-the-solution"></a>ソリューションのデプロイ方法

Start/Stop VMs during off-hours ソリューションを、ご利用の Automation アカウントに追加し、変数を設定してソリューションをカスタマイズするには、以下の手順を実行します。

1. Automation アカウントから、**[関連リソース]** の **[Start/Stop VM]\(VM の起動/停止\)** を選択します。 この画面では、**[Learn more about and enable the solution]\(ソリューションの詳細と有効化\)** をクリックして確認できます。 既に [Start/Stop VM]\(VM の起動/停止\) ソリューションをデプロイしている場合は、**[Manage the solution]\(ソリューションの管理\)** をクリックしてデプロイされたソリューションの一覧に移動し、そこから選択することができます。

   ![Automation アカウントから有効にする](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Azure portal のどこからでも、**[リソースの作成]** をクリックして作成できます。 [Marketplace] ページで、「**Start**」、「**Start/Stop**」などのキーワードを入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 または、ソリューションのフルネームから 1 つ以上のキーワードを入力し、Enter キーを押すこともできます。 検索結果から **Start/Stop VMs during off-hours** を選択します。
1. 選択したソリューションの **[Start/Stop VMs during off-hours]** ページで概要を確認し、**[作成]** をクリックします。

   ![Azure ポータル](media/automation-solution-vm-management/azure-portal-01.png)

1. **[ソリューションの追加]** ページが表示されます。 Automation サブスクリプションにインポートする前に、ソリューションの設定を行うよう求められます。

   ![VM 管理の [ソリューションの追加] ページ](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

1. **[ソリューションの追加]** ページで、**[ワークスペース]** を選択します。 Automation アカウントと同じ Azure サブスクリプションに関連付けられている Log Analytics ワークスペースを選択します。 ワークスペースがない場合は、**[新しいワークスペースの作成]** を選択します。 **[OMS ワークスペース]** ページで、次の手順を実行します。
   - 新しい **OMS ワークスペース**の名前を指定します。
   - 関連付ける**サブスクリプション**をドロップダウン リストから選択します (既定値が適切でない場合)。
   - **[リソース グループ]** では、新しいリソース グループを作成するか、既存のリソース グループを選択できます。
   - **[場所]** を選択します。 現在使用できる場所は、**オーストラリア南東部**、**カナダ中部**、**インド中部**、**米国東部**、**東日本**、**東南アジア**、**英国南部**、および**西ヨーロッパ**のみです。
   - **[価格レベル]** を選択します。 **[GB ごと (スタンドアロン)]** オプションを選択します。 Log Analytics は[価格](https://azure.microsoft.com/pricing/details/log-analytics/)を更新し、GB ごとのレベルが唯一のオプションとなっています。

1. **[OMS ワークスペース]** ページに必要な情報を入力したら、**[作成]** をクリックします。 進行状況はメニューの **[通知]** で追跡できます。完了したら、**[ソリューションの追加]** ページに戻ります。
1. **[ソリューションの追加]** ページで、**[Automation アカウント]** を選択します。 新しい Log Analytics ワークスペースを作成する場合は、関連付ける新しい Automation アカウントを作成するか、Log Analystics ワークスペースにまだリンクされていない既存の Automation アカウントを選択することができます。 既存の Automation アカウントを選択するか、**[Automation アカウントの作成]** をクリックし、**[Automation アカウントの追加]** ページで、次の情報を入力します。
   - **[名前]** フィールドに、Automation アカウントの名前を入力します。

    それ以外のオプションはすべて、選択した Log Analytics ワークスペースに基づいて自動的に入力されます。 こうしたオプションは変更できません。 このソリューションに含まれている Runbook は、Azure 実行アカウントが既定の認証方法になります。 **[OK]** をクリックすると、構成オプションが検証され、Automation アカウントが作成されます。 進行状況は、メニューの **[通知]** で追跡できます。

1. 最後に、**[ソリューションの追加]** ページで、**[構成]** を選択します。 **[パラメーター]** ページが表示されます。

   ![ソリューションの [パラメーター] ページ](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   ここでは、次の操作が求められます。
   - **ターゲット ResourceGroup 名**を指定します。 これは、このソリューションで管理する VM を含むリソース グループの名前です。 複数の名前を入力する場合は、それぞれをコンマで区切ってください (値の大文字と小文字は区別されません)。 ワイルドカードを使用して、サブスクリプション内の全リソース グループの VM を対象にすることもできます。 この値は、**External_Start_ResourceGroupNames** 変数と **External_Stop_ResourceGroupNames** 変数に格納されます。
   - **VM 除外リスト (文字列)** を指定します。 これは、ターゲット リソース グループの 1 つ以上の仮想マシンの名前です。 複数の名前を入力する場合は、それぞれをコンマで区切ってください (値の大文字と小文字は区別されません)。 ワイルドカードの使用がサポートされています。 この値は **External_ExcludeVMNames** 変数に格納されます。
   - **スケジュール**を選択します。 これは、ターゲット リソース グループの VM を定期的に起動および停止する日時です。 既定では、スケジュールは今から 30 分後に構成されます。 別のリージョンを選択することはできません。 ソリューションの構成後、スケジュールを特定のタイム ゾーンに構成するには、「[起動および停止スケジュールの変更](#modify-the-startup-and-shutdown-schedule)」を参照してください。
   - アクション グループから**電子メール通知**を受信するには、既定値の **[はい]** をそのまま使用し、有効なメール アドレスを指定します。 [[いいえ]](../monitoring-and-diagnostics/monitoring-action-groups.md) を選択したものの、後日、電子メール通知を受信することにした場合は、コンマで区切られた有効なメール アドレスで作成された**アクション グループ**を更新することができます。 また、次のアラート ルールを有効にする必要があります。

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > **[Target ResourceGroup Names]\(ターゲット リソース グループ名\)** の既定値は **&ast;** です。 これは、サブスクリプション内のすべての VM が対象です。 ソリューションでサブスクリプション内のすべての VM を対象にするのでない場合は、スケジュールを有効にする前に、この値をリソース グループ名の一覧に更新する必要があります。

1. ソリューションに必要な初期設定を構成したら、**[OK]** をクリックして **[パラメーター]** ページを閉じ、**[作成]** を選択します。 すべての設定が検証された後、ソリューションは、ご利用のサブスクリプションにデプロイされます。 このプロセスは、完了までに数秒かかる場合があります。進行状況は、メニューの **[通知]** で確認してください。

## <a name="scenarios"></a>シナリオ

ソリューションには 3 つのシナリオがあります。 そのシナリオを次に示します。

### <a name="scenario-1-startstop-vms-on-a-schedule"></a>シナリオ 1: スケジュールに従って VM を開始/停止する

これは、ソリューションを最初にデプロイするときの既定の構成です。 たとえば、夕方退社するときにサブスクリプション全体のすべての VM を停止し、朝の出社時に VM を起動するように構成できます。 デプロイ中にスケジュール **Scheduled-StartVM** と **Scheduled-StopVM** を構成すると、対象の VM が起動および停止します。 VM の停止のみを行うようにこのソリューションを構成できます。カスタム スケジュールを構成する方法については、「[起動および停止スケジュールを変更する](#modify-the-startup-and-shutdown-schedules)」を参照してください。

> [!NOTE]
> スケジュール タイム パラメーターを構成するとき、タイム ゾーンは現在のタイム ゾーンになります。 ただし、Azure Automation では UTC 形式で格納されます。 これはデプロイ中に処理されるので、タイム ゾーンの変換を行う必要はありません。

**External_Start_ResourceGroupNames**、**External_Stop_ResourceGroupNames**、および **External_ExcludeVMNames** の各変数を構成することで、どの VM を対象にするかを制御します。

サブスクリプションおよびリソース グループに対するアクション、または特定の VM リストのいずれかを対象にできますが、両方を有効にすることはできません。

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>サブスクリプションとリソース グループに対する起動および停止アクションを対象にする

1. **External_Stop_ResourceGroupNames** 変数と **External_ExcludeVMNames** 変数を構成して対象の VM を指定します。
1. **Scheduled-StartVM** スケジュールと **Scheduled-StopVM** スケジュールを有効にして、更新します。
1. ACTION パラメーターを **start** に設定し、WHATIF パラメーターを **True** に設定して、**ScheduledStartStop_Parent** Runbook を実行し、ご自身の変更をプレビューします。

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>VM リストによる起動および停止アクションを対象にする

1. ACTION パラメーターを **start** に設定して **ScheduledStartStop_Parent** Runbook を実行し、*VMList* パラメーターに VM のコンマ区切りリストを追加して、WHATIF パラメーターを **True** に設定します。 変更をプレビューします。
1. **External_ExcludeVMNames** パラメーターを、VM のコンマ区切りリスト (VM1,VM2,VM3) で構成します。
1. このシナリオでは、**External_Start_ResourceGroupNames** 変数と **External_Stop_ResourceGroupnames** 変数は考慮されていません。 このシナリオでは、独自の Automation のスケジュールを作成する必要があります。 詳細については、「[Azure Automation の Runbook をスケジュール設定する](../automation/automation-schedules.md)」を参照してください。

> [!NOTE]
> **ターゲット ResourceGroup 名**の値は、**External_Start_ResourceGroupNames** と **External_Stop_ResourceGroupNames** の両方の値として格納されます。 粒度を高めるためには、これらの変数をそれぞれ変更して、異なるリソース グループを対象にします。 起動アクション用には **External_Start_ResourceGroupNames** を使用し、停止アクション用には **External_Stop_ResourceGroupNames** を使用します。 VM が起動スケジュールと停止スケジュールに自動的に追加されます。

### <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a>シナリオ 2: タグを使用して VM を順番に起動/停止する

分散ワークロードをサポートする複数の VM のコンポーネントを 2 つ以上含む環境では、コンポーネントの起動および停止順序を指定できることが重要です。 これを実現するには、次の手順を実行します。

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>サブスクリプションとリソース グループに対する起動および停止アクションを対象にする

1. **External_Start_ResourceGroupNames** 変数および **External_Stop_ResourceGroupNames** 変数で対象となっている VM に、**SequenceStart** と **SequenceStop** タグを正の整数値で追加します。 起動アクションと停止アクションは昇順で実行されます。 VM にタグを付ける方法については、[Azure で Windows 仮想マシンにタグを付ける方法](../virtual-machines/windows/tag.md)と [Azure で Linux 仮想マシンにタグを付ける方法](../virtual-machines/linux/tag.md)に関するページをご覧ください。
1. スケジュール **Sequenced-StartVM** と **Sequenced-StopVM** を、要件を満たす日付と時刻に変更し、スケジュールを有効にします。
1. ACTION パラメーターを **start** に設定し、WHATIF パラメーターを **True** に設定して、**SequencedStartStop_Parent** Runbook を実行し、ご自身の変更をプレビューします。
1. アクションをプレビューし、運用 VM に対して実装する前に、必要な変更を行います。 準備ができたら、パラメーターを **False** にセットして手動で Runbook を実行するか、Automation のスケジュール **Sequenced-StartVM** と **Sequenced-StopVM** が、指定されたスケジュールに従って自動的に実行されるようにします。

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>VM リストによる起動および停止アクションを対象にする

1. **VMList** 変数に追加する VM に、正の整数値を持つ **SequenceStart** および **SequenceStop** タグを追加します。 
1. ACTION パラメーターを **start** に設定して **SequencedStartStop_Parent** Runbook を実行し、*VMList* パラメーターに VM のコンマ区切りリストを追加して、WHATIF パラメーターを **True** に設定します。 変更をプレビューします。
1. **External_ExcludeVMNames** パラメーターを、VM のコンマ区切りリスト (VM1,VM2,VM3) で構成します。
1. このシナリオでは、**External_Start_ResourceGroupNames** 変数と **External_Stop_ResourceGroupnames** 変数は考慮されていません。 このシナリオでは、独自の Automation のスケジュールを作成する必要があります。 詳細については、「[Azure Automation の Runbook をスケジュール設定する](../automation/automation-schedules.md)」を参照してください。
1. アクションをプレビューし、運用 VM に対して実装する前に、必要な変更を行います。 準備ができたら、パラメーターを **False** にセットして手動で monitoring-and-diagnostics/monitoring-action-groupsrunbook を実行するか、Automation のスケジュール **Sequenced-StartVM** と **Sequenced-StopVM** が、指定されたスケジュールに従って自動的に実行されるようにします。

### <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a>シナリオ 3: CPU 使用率に基づいて自動的に開始/停止する

このソリューションは、営業時間外などの非ピーク期間中に使用されていない Azure VM を評価し、プロセッサ使用率が x% 未満の場合は自動的にシャットダウンすることで、サブスクリプションの実行中の仮想マシン コストを管理するのに役立ちます。

既定では、ソリューションは、CPU 割合メトリックを評価して、平均使用率が 5% 以下であるかどうかを確認するように事前構成されています。 これは次の変数によって制御され、既定値が要件を満たしていない場合は変更できます。

- External_AutoStop_MetricName
- External_AutoStop_Threshold
- External_AutoStop_TimeAggregationOperator
- External_AutoStop_TimeWindow

サブスクリプションおよびリソース グループに対するアクション、または特定の VM リストのいずれかを対象にできますが、両方を有効にすることはできません。

#### <a name="target-the-stop-action-against-a-subscription-and-resource-group"></a>サブスクリプションとリソース グループに対して停止アクションを指定する

1. **External_Stop_ResourceGroupNames** 変数と **External_ExcludeVMNames** 変数を構成して対象の VM を指定します。
1. **Schedule_AutoStop_CreateAlert_Parent** スケジュールを有効にして更新します。
1. ACTION パラメーターを **start** に設定し、WHATIF パラメーターを **True** に設定して、**AutoStop_CreateAlert_Parent** Runbook を実行し、変更をプレビューします。

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>VM リストによる起動および停止アクションを対象にする

1. ACTION パラメーターを **start** に設定して **AutoStop_CreateAlert_Parent** Runbook を実行し、*VMList* パラメーターに VM のコンマ区切りリストを追加して、WHATIF パラメーターを **True** に設定します。 変更をプレビューします。
1. **External_ExcludeVMNames** パラメーターを、VM のコンマ区切りリスト (VM1,VM2,VM3) で構成します。
1. このシナリオでは、**External_Start_ResourceGroupNames** 変数と **External_Stop_ResourceGroupnames** 変数は考慮されていません。 このシナリオでは、独自の Automation のスケジュールを作成する必要があります。 詳細については、「[Azure Automation の Runbook をスケジュール設定する](../automation/automation-schedules.md)」を参照してください。

これで CPU 使用率に基づいて VM を停止するスケジュールが完成したので、次のスケジュールの 1 つを有効にして開始する必要があります。

- サブスクリプションとリソース グループによる起動アクションを対象にします。 **Scheduled-StartVM** スケジュールのテストと有効化については、[シナリオ 1](#scenario-1-startstop-vms-on-a-schedule) の手順を参照してください。
- サブスクリプション、リソース グループ、およびタグによる起動アクションを対象にします。 **Sequenced-StartVM** スケジュールのテストと有効化については、[シナリオ 2](#scenario-2-startstop-vms-in-sequence-by-using-tags) の手順を参照してください。

## <a name="solution-components"></a>ソリューションのコンポーネント

仮想マシンのスタートアップとシャットダウンをビジネス要件に合わせて調整できるように、このソリューションには、構成済みの Runbook、スケジュール、および Log Analytics との統合が含まれています。

### <a name="runbooks"></a>Runbooks

次の表は、このソリューションによって、ご自身の Automation アカウントにデプロイされる Runbook の一覧です。 Runbook のコードを変更することはお勧めできません。 新しい機能が必要なときは、独自の Runbook を記述してください。

> [!IMPORTANT]
> 名前の末尾に "child" が付いている Runbook は、直接実行しないでください。

すべての親 Runbook に _WhatIf_ パラメーターが含まれます。 これが **True** に設定されている場合、_WhatIf_ では、_WhatIf_ パラメーターなしで実行するときの Runbook の正確な動作の詳細な記述がサポートされ、正しい VM が対象となっていることが検証されます。 _WhatIf_ パラメーターが **False** に設定されている場合、Runbook は定義されているアクションのみを実行します。

|Runbook | parameters | 説明|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | 親 Runbook から呼び出されます。 この Runbook は、AutoStop シナリオでリソースごとにアラートを作成します。|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: True または False  | 対象となるサブスクリプションまたはリソース グループ内の VM 上で、Azure アラート ルールを作成または更新します。 <br> VMList: VM のコンマ区切りリストです。 _vm1,vm2,vm3_ など。<br> *WhatIf* は Runbook ロジックを実行せずに検証します。|
|AutoStop_Disable | なし | AutoStop アラートと既定のスケジュールを無効にします。|
|AutoStop_StopVM_Child | WebHookData | 親 Runbook から呼び出されます。 アラート ルールはこの Runbook を呼び出して、VM を停止します。|
|Bootstrap_Main | なし | 通常 Azure Resource Manager からアクセスできない webhookURI などのブートストラップ構成を設定するために、1 度だけ使用されます。 この Runbook は、デプロイが正常に完了したら自動的に削除されます。|
|ScheduledStartStop_Child | VMName <br> Action: Start または Stop <br> ResourceGroupName | 親 Runbook から呼び出されます。 停止スケジュールの起動または停止アクションを実行します。|
|ScheduledStartStop_Parent | Action: Start または Stop <br>VMList <br> WhatIf: True または False | サブスクリプション内のすべての VM に影響します。 これらの対象のリソース グループでのみ実行されるように、**External_Start_ResourceGroupNames** と **External_Stop_ResourceGroupNames** を編集します。 **External_ExcludeVMNames** 変数を更新することで、特定の VM を除外することもできます。<br> VMList: VM のコンマ区切りリストです。 _vm1,vm2,vm3_ など。<br> _WhatIf_ は Runbook ロジックを実行せずに検証します。|
|SequencedStartStop_Parent | Action: Start または Stop <br> WhatIf: True または False<br>VMList| 起動/停止アクティビティのシーケンスを指定する各 VM に、**SequenceStart** および **SequenceStop** という名前のタグを作成します。 タグの値は、起動または停止する順序に対応する正の整数 (1、2、3) にする必要があります。 <br> VMList: VM のコンマ区切りリストです。 _vm1,vm2,vm3_ など。 <br> _WhatIf_ は Runbook ロジックを実行せずに検証します。 <br> **注**: Azure Automation 変数で External_Start_ResourceGroupNames、External_Stop_ResourceGroupNames、および External_ExcludeVMNames として定義されたリソース グループ内に VM が存在する必要があります。 アクションを有効にするための適切なタグが必要です。|

### <a name="variables"></a>variables

次の表は、Automation アカウント内に作成される変数の一覧です。 **External** プレフィックスが付いた変数のみを変更することをお勧めします。 **Internal** プレフィックスが付いた変数を変更すると、望ましくない効果がもたらされます。

|可変 | 説明|
|---------|------------|
|External_AutoStop_Condition | アラートをトリガーする前の条件を構成するのに必要な条件演算子。 指定できる値は、**GreaterThan**、**GreaterThanOrEqual**、**LessThan**、および **LessThanOrEqual** です。|
|External_AutoStop_Description | CPU の割合がしきい値を超えた場合に VM を停止するアラート。|
|External_AutoStop_MetricName | Azure アラート ルールが構成されるパフォーマンス メトリックの名前。|
|External_AutoStop_Threshold | 変数 _External_AutoStop_MetricName_ で指定される Azure アラート ルールのしきい値。 パーセンテージの値の範囲は 1 ～ 100 です。|
|External_AutoStop_TimeAggregationOperator | 時間の集計演算子。条件を評価するために選択した時間枠のサイズに適用されます。 指定できる値は、**Average**、**Minimum**、**Maximum**、**Total**、および **Last** です。|
|External_AutoStop_TimeWindow | アラートをトリガーするために選択されたメトリックを Azure が分析する時間枠のサイズ。 このパラメーターは、timespan 形式で入力を受け入れます。 使用可能な値は、5 分 ～ 6 時間です。|
|External_ExcludeVMNames | 除外される VM の名前を入力します。スペースなしのコンマで名前を区切ります。|
|External_Start_ResourceGroupNames | 開始アクションの対象となる 1 つ以上のリソース グループを、コンマ区切り値で指定します。|
|External_Stop_ResourceGroupNames | 停止アクションの対象となる 1 つ以上のリソース グループを、コンマ区切り値で指定します。|
|Internal_AutomationAccountName | Automation アカウントの名前を指定します。|
|Internal_AutoSnooze_WebhookUri | AutoStop シナリオで呼び出される Webhook URI を指定します。|
|Internal_AzureSubscriptionId | Azure サブスクリプション ID を指定します。|
|Internal_ResourceGroupName | Automation アカウントのリソース グループ名を指定します。|

すべてのシナリオで、VM を対象とするために **External_Start_ResourceGroupNames**、**External_Stop_ResourceGroupNames**、および **External_ExcludeVMNames** 変数が必要です。例外は、**AutoStop_CreateAlert_Parent**、**SequencedStartStop_Parent**、および **ScheduledStartStop_Parent** Runbook に対して VM のコンマ区切りリストを指定する場合です。 つまり、開始および停止アクションを発生させるためには、ターゲット リソース グループ内に VM が存在する必要があります。 このロジックは Azure Policy に似ています。サブスクリプションまたはリソース グループを対象にすることができ、アクションは新しく作成された VM に継承されます。 この方法により、VM ごとに個別のスケジュールを保持したり、スケールで起動および停止を管理したりする必要がなくなります。

### <a name="schedules"></a>スケジュール

次の表は、Automation アカウント内に作成される既定のスケジュールの一覧です。 それらを変更したり、独自のカスタム スケジュールを作成したりできます。 既定では、それぞれが **Scheduled_StartVM** と **Scheduled-StopVM** を除いて無効になっています。

すべてのスケジュールを有効にすることはお勧めしません。アクションのスケジュール間で重複が生じる可能性があるためです。 実行する必要がある最適化を特定し、それに応じて変更することをお勧めします。 詳細については、概要セクションのシナリオ例をご覧ください。

|スケジュール名 | 頻度 | 説明|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | 8 時間ごと | 8 時間ごとに AutoStop_CreateAlert_Parent Runbook を実行します。Runbook は Azure Automation 変数の External_Start_ResourceGroupNames、External_Stop_ResourceGroupNames、および External_ExcludeVMNames の VM ベースの値を停止します。 または、VMList パラメーターを使用して VM のコンマ区切りリストを指定できます。|
|Scheduled_StopVM | ユーザー定義、毎日 | _Stop_ パラメーターを持つ Scheduled_Parent Runbook を毎日指定された時刻に実行します。 アセット変数によって定義されたルールを満たす VM すべてを自動的に停止します。 関連スケジュールで **Scheduled-StartVM** を有効にすることをお勧めします。|
|Scheduled_StartVM | ユーザー定義、毎日 | _Start_ パラメーターを持つ Scheduled_Parent Runbook を毎日指定された時刻に実行します。 適切な変数によって定義されたルールを満たす VM すべてを自動的に開始します。 関連スケジュールで **Scheduled-StopVM** を有効にすることをお勧めします。|
|Sequenced-StopVM | 午前 1 時 00 分 (UTC)、毎週金曜日 | _Stop_ パラメーターを持つ Sequenced_Parent Runbook を毎週金曜日の指定された時刻に実行します。 適切な変数で定義された **SequenceStop** のタグを持つ VM すべてを順番 (昇順) に停止します。 タグの値とアセット変数の詳細については、「Runbook」セクションを参照してください。 関連スケジュールで **Sequenced-StartVM** を有効にすることをお勧めします。|
|Sequenced-StartVM | 午後 1 時 00 分 (UTC)、毎週月曜日 | _Start_ パラメーターを持つ Sequenced_Parent Runbook を毎週月曜日の指定された時刻に実行します。 適切な変数で定義された **SequenceStart** のタグを持つ VM すべてを順番 (降順)に 起動します。 タグの値とアセット変数の詳細については、「Runbook」セクションを参照してください。 関連スケジュールで **Sequenced-StopVM** を有効にすることをお勧めします。|

## <a name="log-analytics-records"></a>Log Analytics のレコード

Automation により、ジョブ ログとジョブ ストリームの 2 種類のレコードが Log Analytics ワークスペースに作成されます。

### <a name="job-logs"></a>ジョブ ログ

|プロパティ | 説明|
|----------|----------|
|Caller |  操作を開始したユーザー。 スケジュールされたジョブのシステムまたは電子メール アドレスが記録されます。|
|Category | データの種類の分類。 Automation の場合、値は JobLogs です。|
|CorrelationId | GUID。Runbook ジョブの関連付け ID です。|
|JobId | GUID。Runbook ジョブの ID です。|
|operationName | Azure で実行された操作の種類を指定します。 Automation の場合、値は Job です。|
|resourceId | Azure のリソースの種類を指定します。 Automation の場合、値は Runbook に関連付けられている Automation アカウントです。|
|ResourceGroup | Runbook ジョブのリソース グループ名を指定します。|
|ResourceProvider | デプロイと管理の対象となるリソースのプロバイダー (Azure サービス) を指定します。 Automation の場合、その値は Azure Automation になります。|
|ResourceType | Azure のリソースの種類を指定します。 Automation の場合、値は Runbook に関連付けられている Automation アカウントです。|
|resultType | Runbook ジョブの状態。 次のいずれかの値になります。<br>- 開始済み<br>- 停止済み<br>- 中断<br>- 失敗<br>- 成功|
|resultDescription | Runbook ジョブの結果の状態について説明します。 次のいずれかの値になります。<br>- ジョブが開始されました<br>- ジョブが失敗しました<br>- ジョブが完了しました|
|RunbookName | Runbook の名前を指定します。|
|SourceSystem | 送信されたデータのソース システムを指定します。 Automation の場合、値は OpsManager です|
|StreamType | イベントの種類を指定します。 次のいずれかの値になります。<br>- 詳細<br>- 出力<br>- エラー<br>- 警告|
|SubscriptionId | ジョブのサブスクリプション ID を指定します。
|Time | Runbook ジョブが実行された日付と時刻。|

### <a name="job-streams"></a>ジョブ ストリーム

|プロパティ | 説明|
|----------|----------|
|Caller |  操作を開始したユーザー。 スケジュールされたジョブのシステムまたは電子メール アドレスが記録されます。|
|Category | データの種類の分類。 Automation の場合、値は JobStreams です。|
|JobId | GUID。Runbook ジョブの ID です。|
|operationName | Azure で実行された操作の種類を指定します。 Automation の場合、値は Job です。|
|ResourceGroup | Runbook ジョブのリソース グループ名を指定します。|
|resourceId | Azure のリソース ID を指定します。 Automation の場合、値は Runbook に関連付けられている Automation アカウントです。|
|ResourceProvider | デプロイと管理の対象となるリソースのプロバイダー (Azure サービス) を指定します。 Automation の場合、その値は Azure Automation になります。|
|ResourceType | Azure のリソースの種類を指定します。 Automation の場合、値は Runbook に関連付けられている Automation アカウントです。|
|resultType | イベントが生成された時点における Runbook ジョブの結果。 次の値になります。<br>- 処理中|
|resultDescription | Runbook からの出力ストリームが含まれます。|
|RunbookName | Runbook の名前。|
|SourceSystem | 送信されたデータのソース システムを指定します。 Automation の場合、値は OpsManager です。|
|StreamType | ジョブ ストリームの種類。 次のいずれかの値になります。<br>- 処理中<br>- 出力<br>- 警告<br>- エラー<br>- デバッグ<br>- 詳細|
|Time | Runbook ジョブが実行された日付と時刻。|

**JobLogs** または **JobStreams** のカテゴリ レコードを取得する何らかのログ検索を実行するときは、**JobLogs** ビューまたは **JobStreams** ビューを選択すると、検索から返された最新情報が一連のタイルに集約されて表示されます。

## <a name="sample-log-searches"></a>サンプル ログ検索

以下の表は、このソリューションによって収集されたジョブ レコードを探すログ検索の例です。

|Query | 説明|
|----------|----------|
|正常に終了した Runbook ScheduledStartStop_Parent のジョブを検索する | search Category == "JobLogs" &#124; where ( RunbookName_s == "ScheduledStartStop_Parent" ) &#124; where ( ResultType == "Completed" )  &#124; summarize |AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) &#124; sort by TimeGenerated desc|
|正常に終了した Runbook SequencedStartStop_Parent のジョブを検索する | search Category == "JobLogs" &#124; where ( RunbookName_s == "SequencedStartStop_Parent" ) &#124; where ( ResultType == "Completed" )  &#124; summarize |AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) &#124; sort by TimeGenerated desc

## <a name="viewing-the-solution"></a>ソリューションの表示

ソリューションにアクセスするには、ご自身の Automation アカウントに移動し、**[関連リソース]** の **[ワークスペース]** を選択します。 Log Analytics ページで、**[全般]** の **[ソリューション]** を選択します。 **[ソリューション]** ページで、一覧から **Start-Stop-VM[ワークスペース]** ソリューションを選択します。

ソリューションを選択すると、**Start-Stop-VM[ワークスペース]** ソリューション ページが表示されます。 ここでは、**StartStopVM** タイルなど、重要な情報を確認できます。 Log Analytics ワークスペースと同様、このタイルには、そのソリューションに関して開始された Runbook ジョブの数と、正常に終了した Runbook ジョブの数、およびそのグラフが表示されます。

![Automation Update Management ソリューション ページ](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

ここから、ジョブ レコードの詳細な分析を実行するには、ドーナツ タイルをクリックします。 ソリューション ダッシュボードに、ジョブ履歴と定義済みのログ検索クエリが表示されます。 Log Analytics Advanced ポータルに切り替えて、検索クエリに基づいて検索を実行します。

## <a name="configure-email-notifications"></a>電子メール通知の構成

ソリューションのデプロイ後に電子メール通知を変更するには、デプロイ時に作成されたアクション グループを変更します。  

Azure Portal で、[監視]、[アクション グループ] の順に移動します。 **StartStop_VM_Notication** というタイトルのアクション グループを選択します。

![Automation Update Management ソリューション ページ](media/automation-solution-vm-management/azure-monitor.png)

**[StartStop_VM_Notification]** ページで、**[詳細]** の **[詳細の編集]** をクリックします。 これで、**[電子メール/SMS/プッシュ/音声]** ページが開きます。 メール アドレスを更新し、**[OK]** をクリックして変更を保存します。

![Automation Update Management ソリューション ページ](media/automation-solution-vm-management/change-email.png)

アクション グループにさらにアクションを追加することもできます。アクション グループの詳細については、「[アクション グループ](../monitoring-and-diagnostics/monitoring-action-groups.md)」を参照してください。

ソリューションで仮想マシンがシャットダウンされたときに送信されるメールの例を以下に示します。

![Automation Update Management ソリューション ページ](media/automation-solution-vm-management/email.png)

## <a name="modify-the-startup-and-shutdown-schedules"></a>起動および停止スケジュールを変更する

このソリューションの起動および停止スケジュールを管理するには、「[Azure Automation の Runbook をスケジュール設定する](automation-schedules.md)」で説明されている手順に従います。

特定の時刻に VM の停止のみを行うようにソリューションを構成できます。 そのためには、次の手順を実行する必要があります。

1. シャットダウンする VM のリソース グループが、**External_Start_ResourceGroupNames** 変数に追加されていることを確認します。
1. VM をシャットダウンする時刻の独自のスケジュールを作成します。
1. **ScheduledStartStop_Parent** Runbook に移動し、**[スケジュール]** をクリックします。 これにより、前の手順で作成したスケジュールを選択できます。
1. **[パラメーターと実行設定]** を選択し、ACTION パラメーターを "Stop" に設定します。
1. **[OK]** をクリックして変更を保存します。

## <a name="update-the-solution"></a>ソリューションを更新する

このソリューションの以前のバージョンをデプロイしている場合は、更新済みリリースをデプロイする前に、まず以前のバージョンをご自身のアカウントから削除する必要があります。 次の手順に従って[ソリューションを削除](#remove-the-solution)してから、上記の手順で[ソリューションをデプロイ](#deploy-the-solution)してください。

## <a name="remove-the-solution"></a>ソリューションを削除する

ソリューションを使用する必要がなくなった場合、ソリューションを Automation アカウントから削除できます。 ソリューションを削除すると、Runbook のみが削除されます。 ソリューションの追加時に作成されたソリューションまたは変数は削除されません。 これらの資産は、他の Runbook で使用していない限り、手動で削除する必要があります。

ソリューションを削除するには、次の手順を実行します。

1. ご自身の Automation アカウントから、左側のページで **[ワークスペース]** を選択します。
1. **[ソリューション]** ページで、ソリューション **Start-Stop-VM[ワークスペース]** を選択します。 **VMManagementSolution[ワークスペース]** ページで、メニューから **[削除]** を選択します。<br><br> ![VM 管理ソリューションの削除](media/automation-solution-vm-management/vm-management-solution-delete.png)
1. **[ソリューションの削除]** ウィンドウで、ソリューションを削除するかどうかを確定します。
1. 情報が検証され、ソリューションが作成されている間、メニューの **[通知]** でその進行状況を追跡することができます。 ソリューションの削除プロセスが開始すると、**[ソリューション]** ページに戻ります。

Automation アカウントと Log Analytics ワークスペースは、このプロセスの一部として削除されません。 Log Analytics ワークスペースを保持しない場合は、これを手動で削除する必要があります。 これは、Azure Portal から行うことができます。

1. Azure Portal ホーム画面で **[Log Analytics]** を選択します。
1. **[Log Analytics]** ページで、ワークスペースを選択します。
1. ワークスペースの設定ページのメニューから **[削除]** を選択します。

Azure Automation アカウント コンポーネントを保持しない場合は、各コンポーネントを手動で削除することができます。 ソリューションによって作成される Runbook、変数、およびスケジュールの一覧については、「[ソリューションのコンポーネント](#solution-components)」を参照してください。

## <a name="next-steps"></a>次の手順

- 各種検索クエリの作成方法と、Log Analytics での Automation ジョブ ログの確認方法の詳細については、[Log Analytics でのログ検索](../log-analytics/log-analytics-log-searches.md)に関するページを参照してください。
- Runbook の実行、Runbook ジョブの監視方法、その他の技術的な詳細については、[Runbook ジョブの追跡](automation-runbook-execution.md)に関するページを参照してください。
- Log Analytics とデータ収集ソースの詳細については、[Log Analytics における Azure Storage データの収集](../log-analytics/log-analytics-azure-storage.md)に関するページをご覧ください。