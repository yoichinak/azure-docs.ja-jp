---
title: Azure Automation で Update Management、Change Tracking、および Inventory ソリューションの配布準備を行う方法について説明します
description: Azure Automation に含まれる Update Management、Change Tracking、および Inventory ソリューションで、Azure Virtual Machine の配布準備を行う方法について説明します
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: conceptual
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 8649b96c9cf95e4a25b24dedf447aef133ef299a
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2018
ms.locfileid: "37865405"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions"></a>Update Management、Change Tracking、および Inventory ソリューションの配布準備

Azure Automation には、オペレーティング システムのセキュリティ更新プログラム、変更の追跡、およびご使用のコンピューターにインストールされている内容のインベントリを管理するソリューションが用意されています。 マシンをオンボードする方法は複数あり、ソリューションのオンボードは、[仮想マシンから](automation-onboard-solutions-from-vm.md)、または[複数のマシンを参照する](automation-onboard-solutions-from-browse.md)か、Automation アカウントから、あるいは [Runbook](automation-onboard-solutions.md) を使用して行うことができます。 この記事では、Automation アカウントからこれらのソリューションの配布準備を行う方法について説明します。

## <a name="log-in-to-azure"></a>Azure にログインする

Azure にサインインします( https://portal.azure.com )。

## <a name="enable-solutions"></a>ソリューションの有効化

Automation アカウントに移動し、**[構成管理]** で、**[インベントリ]** または **[Change Tracking]\(変更の追跡\)** をクリックします。

Log Analytics ワークスペースおよび Automation アカウントを選択し、**[有効にする]** をクリックして、ソリューションを有効にします。 ソリューションを有効にするには最大 15 分かかります。

![Inventory ソリューションの配布準備](media/automation-onboard-solutions-from-automation-account/onboardsolutions.png)

Change Tracking と Inventory ソリューションには、仮想マシンで[変更を追跡](automation-vm-change-tracking.md)し、[インベントリを作成](automation-vm-inventory.md)する機能が用意されています。 この手順では、仮想マシンでソリューションを有効にします。

Change Tracking と Inventory ソリューションの配布準備通知が完了したら、**[構成管理]** で **[Update Management]** をクリックします。

Update Management ソリューションでは、Azure Windows VM の更新プログラムとパッチを管理できます。 利用可能な更新プログラムの状態を迅速に把握したり、必要な更新プログラムのインストールをスケジュールしたり、デプロイの結果を確認して、VM に更新プログラムが正常に適用されたことを検証したりできます。 この操作では、ご利用の VM のソリューションを有効にしました。

**[更新の管理]** で **[更新プログラムの管理]** を選択します。 選択されている Log Analytics ワークスペースは、前の手順で使用されたワークスペースと同じです。 **[有効]** をクリックして、Update Management ソリューションの配布準備を行います。 ソリューションを有効にするには最大 15 分かかります。

![Update ソリューションの配布準備](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="scope-configuration"></a>スコープ構成

各ソリューションは、ワークスペース内のスコープ構成を使用して、ソリューションの対象となるコンピューターを決定します。 スコープ構成は、1 つ以上の保存された検索条件のグループで、ソリューションのスコープを特定のコンピューターに限定するために使用されます。 スコープ構成にアクセスするには、**[関連リソース]** の Automation アカウントで、**[ワークスペース]** を選択します。 次に、**[ワークスペースのデータ ソース]** のワークスペースで、**[スコープ構成]** を選択します。

選択したワークスペースに Update Management や Change Tracking のソリューションがまだない場合、次のスコープ構成が作成されます。

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-Updates**

選択したワークスペースに既にソリューションがある場合、 ソリューションは再デプロイされず、スコープ構成は追加されません。

## <a name="saved-searches"></a>保存した検索条件

コンピューターが Update Management ソリューション、または Change Tracking および Inventory ソリューションに追加されると、それらはワークスペースの 2 つの保存された検索条件のいずれかに追加されます。 保存した検索条件は、これらのソリューションの対象となるコンピューターを含むクエリです。

使用している Automation アカウントに移動し、**[全般]** の **[保存した検索条件]** を選択します。 次の表は、これらのソリューションで使用される 2 つの保存された検索条件を示しています。

|Name     |Category  |エイリアス  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | 更新プログラム        | Updates__MicrosoftDefaultComputerGroup         |

いずれかの保存した検索条件を選択して、グループの設定に使用されるクエリを表示します。 次の図は、クエリとその結果を示しています。

![保存した検索条件](media/automation-onboard-solutions-from-automation-account/savedsearch.png)

## <a name="onboard-azure-vms"></a>Azure VM のオンボード

Automation アカウントの **[構成管理]** で **[インベントリ]** または **[Change Tracking]\(変更の追跡\)** を選択するか、**[更新の管理]** で **[更新プログラムの管理]** を選択します。

**[+ Azure VM の追加]** をクリックし、リストから 1 つ以上の VM を選択します。 有効にできない仮想マシンは灰色で表示され、選択できません。 **[Update Management の有効化]** ページで、**[有効化]** をクリックします。 これにより、選択された VM が、ソリューションのコンピューター グループの保存された検索条件に追加されます。

![Azure VM の有効化](media/automation-onboard-solutions-from-automation-account/enable-azure-vms.png)

## <a name="onboard-a-non-azure-machine"></a>Azure 以外のマシンの配布準備

Azure に存在しないマシンは手動で追加する必要があります。 Automation アカウントの **[構成管理]** で **[インベントリ]** または **[Change Tracking]\(変更の追跡\)** を選択するか、**[更新の管理]** で **[更新プログラムの管理]** を選択します。

**[Add non-Azure machine]\(Azure 以外のマシンを追加する\)** をクリックします。 これにより、新しいブラウザー ウィンドウが開きます。このウィンドウには、マシンがソリューションへのレポートを開始できるように、[Microsoft Monitoring Agent をマシンにインストールして構成する方法](../log-analytics/log-analytics-concept-hybrid.md)が示されます。 System Center Operations Manager によって現在管理されているマシンの配布準備を行う場合、新しいエージェントは必要ありません。ワークスペース情報は、既存のエージェントに入力されます。

## <a name="onboard-machines-in-the-workspace"></a>ワークスペースでのマシンの配布準備

手動でインストールしたマシンや、既にワークスペースへのレポートを行っているマシンは、Azure Automation に追加してソリューションを有効にする必要があります。 Automation アカウントの **[構成管理]** で **[インベントリ]** または **[Change Tracking]\(変更の追跡\)** を選択するか、**[更新の管理]** で **[更新プログラムの管理]** を選択します。

**[マシンの管理]** を選択します。 これにより、**[マシンの管理]** ページが開きます。 このページを使用すると、一部のマシン、使用可能なすべてのマシン、または現在のすべてのマシンと今後のすべてのマシンで、ソリューションを有効にすることができます。

![保存した検索条件](media/automation-onboard-solutions-from-automation-account/managemachines.png)

### <a name="all-available-machines"></a>使用可能なすべてのマシン

使用可能なすべてのマシンに対してソリューションを有効にするには、**[Enable on all available machines]\(使用可能なすべてのマシンで有効にします\)** を選択します。 これにより、マシンを個別に追加するコントロールが無効になります。 このタスクにより、ワークグループへのレポートを行うすべてマシンの名前が、"保存した検索クエリ" コンピューター グループに追加されます。

### <a name="all-available-and-future-machines"></a>使用可能なすべてのマシンと今後のすべてのマシン

使用可能なすべてのマシンと今後のすべてのマシンに対してソリューションを有効にするには、**[Enable on all available and future machines]\(使用可能なマシンと今後のマシンすべてで有効にします\)** を選択します。 このオプションにより、保存した検索条件とスコープ構成がワークスペースから削除されます。 これにより、ワークスペースへのレポートを行うすべての Azure マシンと Azure 以外のマシンに対してソリューションが開かれます。

### <a name="selected-machines"></a>選択したマシン

1 つ以上のマシンに対してソリューションを有効にするには、**[Enable on selected machines]\(選択したマシンで有効にします\)** を選択し、ソリューションに追加する各マシンの横にある **[追加]** をクリックします。 このタスクにより、選択したマシン名が、ソリューションの "保存した検索クエリ" コンピューター グループに追加されます。

## <a name="next-steps"></a>次の手順

ソリューションを使用する方法を学習するためのチュートリアルに進みます。

* [チュートリアル - VM の更新プログラムの管理](automation-tutorial-update-management.md)

* [チュートリアル - VM 上のソフトウェアの特定](automation-tutorial-installed-software.md)

* [チュートリアル - VM に対する変更のトラブルシューティング](automation-tutorial-troubleshoot-changes.md)