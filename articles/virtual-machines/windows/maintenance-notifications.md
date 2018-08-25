---
title: Azure での Windows VM のメンテナンス通知の処理 | Microsoft Docs
description: Azure で実行されている Windows 仮想マシンのメンテナンス通知を表示し、セルフサービス メンテナンスを開始します。
services: virtual-machines-windows
documentationcenter: ''
author: shants123
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/02/2018
ms.author: shants
ms.openlocfilehash: 609a688ba2915e57916a6e5e9ea0cbdc66ccdd6a
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2018
ms.locfileid: "39069558"
---
# <a name="handling-planned-maintenance-notifications-for-windows-virtual-machines"></a>Windows 仮想マシンに対する計画メンテナンスの通知の処理

Azure は、定期的に更新を行い、仮想マシンのホスト インフラストラクチャの信頼性、パフォーマンス、セキュリティの向上に努めています。 更新とは、ホスティング環境の修正、ハードウェアのアップグレードや使用停止などの変更のことです。 これらの更新のほとんどは、ホストされている仮想マシンに影響を及ぼすことなく実行されます。 ただし、更新による影響が生じる場合もあります。

- Azure では、再起動を伴わないメンテナンスの場合、インプレース移行を使用して、ホストの更新中に VM を一時的に停止させます。 これらの再起動を伴わないメンテナンス操作は障害ドメインごとに適用され、警告の正常性シグナルを受信した場合、進行が停止します。 

- 再起動を伴うメンテナンスの場合は、メンテナンスの予定日時が知らされます。 このような場合は、都合に応じて自分自身でメンテナンスを開始できる時間枠が与えられます。


再起動が必要な計画済みメンテナンスは、段階的にスケジュールされます。 各段階のスコープ (リージョン) はそれぞれ異なります。

- 段階はお客様への通知で始まります。 既定では、サブスクリプションの所有者と共同所有者に通知が送信されます。 Azure [アクティビティ ログ アラート](../../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)を使用して、通知の受信者、および電子メール、SMS、webhook などのメッセージング オプションを通知に追加できます。  
- 通知の時点で、"*セルフサービス期間*" が使用できるようになります。 この期間中は、この段階に含まれている仮想マシンを確認し、自身のスケジュールのニーズに従って、プロアクティブにメンテナンスを開始できます。
- セルフサービス期間が過ぎると、"*予定メンテナンス期間*" が始まります。 この期間のある時点で、Azure は、仮想マシンに対して必要なメンテナンスをスケジュールし、適用します。 

2 つの期間を用意した目的は、Azure によってメンテナンスが自動的に開始される時期を把握した上で、メンテナンスを開始して仮想マシンを再起動するのに必要な時間を十分に確保できるようにすることにあります。


Azure ポータル、PowerShell、REST API、CLI を使用して、VM のメンテナンス期間のクエリを実行し、セルフサービス メンテナンスを開始することができます。

  
## <a name="should-you-start-maintenance-during-the-self-service-window"></a>セルフ サービス期間中にメンテナンスを開始する必要があるかどうか  

次のガイドラインは、この機能を使用して、ご自身のタイミングでメンテナンスを開始する必要があるかどうかを判断するうえで役立ちます。

> [!NOTE] 
> セルフサービス メンテナンスは、一部の VM には使用できない場合があります。 ご自身の VM にプロアクティブな再デプロイを使用できるかどうかを確認するには、メンテナンス状態で **[今すぐ開始]** を探します。 セルフサービス メンテナンスは、現在、Cloud Services (Web/worker ロール)、および Service Fabric では使用できません。


セルフサービス メンテナンスは、**可用性セット**を使用したデプロイにはお勧めしません。可用性セットは高可用性セットアップであるため、常に 1 つの更新ドメインしか影響を受けないためです。 
- Azure がメンテナンスをトリガーします。 再起動が必要なメンテナンスでは、更新ドメインごとにメンテナンスが行われますが、更新ドメインが連続してメンテナンスを受信するとは限らないため、更新ドメイン間に 30 分間の一時停止があることに注意してください。 
- 一部の容量の一時的な損失 (1/更新ドメイン数) が問題になる場合は、メンテナンス期間中に追加インスタンスを割り当てることで簡単に補正できます。
- 再起動が不要なメンテナンスの場合、更新プログラムは障害ドメイン レベルで適用されます。
    
セルフサービス メンテナンスは、次のシナリオでは使用**しない**でください。 
- 手動で、DevTest Labs を使用して、自動シャットダウンを使用して、またはスケジュールに従って、VM を頻繁にシャットダウンする場合。これにより、メンテナンス状態が元に戻り、追加のダウンタイムが発生する可能性があります。
- VM の有効期間が短く、メンテナンス段階の終了前に削除されることがわかっている場合。 
- ワークロードの状態の多くが、更新時に保持する必要があるローカル (一時) ディスクに格納されている場合。 
- VM のサイズを頻繁に変更する場合。変更により、メンテナンス状態が元に戻る可能性があります。
- メンテナンス シャットダウンの開始 15 分前に、ワークロードのプロアクティブなフェールオーバーやグレースフル シャットダウンを有効にする、スケジュールされたイベントを使用している場合。

予定メンテナンス フェーズ中に中断なく VM を実行する場合、また、上記の使用しないシナリオの説明に該当するものがない場合は、セルフサービス メンテナンスを**使用します**。 

セルフサービス メンテナンスは、次の場合に使用することをお勧めします。

- 正確なメンテナンス期間を、管理担当またはエンド ユーザーのお客様に伝える必要がある。 
- 日時を指定して、メンテナンスを完了する必要がある。 
- メンテナンスのシーケンスを制御する必要がある (安全な復旧を保証する多層アプリケーションなど)。
- 2 つの更新ドメイン (UD) の間に30 分を超える VM 復旧時間が必要である。 更新ドメイン間の時間を制御するには、一度に 1 つの更新ドメイン (UD) で、VM に対してメンテナンスをトリガーする必要があります。

 

[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]

## <a name="check-maintenance-status-using-powershell"></a>PowerShell を使用してメンテナンスの状態を確認する

Azure Powershell を使用して、VM のメンテナンスの予定を確認することもできます。 計画済みメンテナンスに関する情報は、[Get AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) コマンドレットに `-status` パラメーターを指定することで取得できます。
 
計画メンテナンスがある場合にのみ、メンテナンス情報が返されます。 VM に影響を及ぼすメンテナンスがスケジュールされていない場合、コマンドレットはメンテナンス情報を返しません。 

```powershell
Get-AzureRmVM -ResourceGroupName rgName -Name vmName -Status
```

MaintenanceRedeployStatus では、次のプロパティが返されます。 
| 値 | 説明   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | この時点で VM に対してメンテナンスを開始できるかどうかを示します。 ||
| PreMaintenanceWindowStartTime         | VM に対してメンテナンスを開始できる場合、メンテナンスのセルフサービス期間の始まりです。 ||
| PreMaintenanceWindowEndTime           | VM に対してメンテナンスを開始できる場合、メンテナンスのセルフサービス期間の終わりです。 ||
| MaintenanceWindowStartTime            | Azure が VM に対してメンテナンスを開始する、予定メンテナンスの始まりです。 ||
| MaintenanceWindowEndTime              | Azure が VM に対してメンテナンスを開始する、予定メンテナンス期間の終わりです。 ||
| LastOperationResultCode               | VM に対して最後にメンテナンスを試みたときの結果です。 ||



また、VM を指定しないで [Get AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) を実行すると、リソース グループ内のすべての VM のメンテナンス状態を取得することもできます。
 
```powershell
Get-AzureRmVM -ResourceGroupName rgName -Status
```

次の PowerShell 関数は、サブスクリプション ID を取り、メンテナンスがスケジュールされている VM の一覧を出力します。

```powershell

function MaintenanceIterator
{
    Select-AzureRmSubscription -SubscriptionId $args[0]

    $rgList= Get-AzureRmResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]        
    $vmList = Get-AzureRMVM -ResourceGroupName $rg.ResourceGroupName 
        for ($vmIdx=0; $vmIdx -lt $vmList.Length ; $vmIdx++)
        {
            $vm = $vmList[$vmIdx]
            $vmDetails = Get-AzureRMVM -ResourceGroupName $rg.ResourceGroupName -Name $vm.Name -Status
              if ($vmDetails.MaintenanceRedeployStatus )
            {
                Write-Output "VM: $($vmDetails.Name)  IsCustomerInitiatedMaintenanceAllowed: $($vmDetails.MaintenanceRedeployStatus.IsCustomerInitiatedMaintenanceAllowed) $($vmDetails.MaintenanceRedeployStatus.LastOperationMessage)"               
            }
          }
    }
}

```

### <a name="start-maintenance-on-your-vm-using-powershell"></a>PowerShell を使用して VM に対するメンテナンスを開始する

**IsCustomerInitiatedMaintenanceAllowed** が true に設定されている場合、前のセクションの関数から取得した情報を使用して、次のように VM に対するメンテナンスを開始します。

```powershell
Restart-AzureRmVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="classic-deployments"></a>クラシック デプロイ

クラシック デプロイ モデルを使用してデプロイされたレガシ VM がまだある場合は、PowerShell を使用して、VM を照会し、メンテナンスを開始できます。

VM のメンテナンスの状態を取得するには、次のように入力します。

```
Get-AzureVM -ServiceName <Service name> -Name <VM name>
```

クラシック VM のメンテナンスを開始するには、次のように入力します。

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```


## <a name="faq"></a>FAQ


**Q: 仮想マシンを今すぐ再起動する必要があるのはなぜですか?**

**A:** Azure プラットフォームの更新とアップグレードの大半は仮想マシンの可用性に影響を及ぼしませんが、Azure でホストされている仮想マシンの再起動を避けられない場合があります。 累積された複数の変更があり、サーバーを再起動する必要がある場合、仮想マシンを再起動することになります。

**Q: 可用性セットを使用して高可用性に関する推奨事項に従えば安全ですか?**

**A:** 可用性セットまたは仮想マシン スケール セットにデプロイされた仮想マシンには、更新ドメイン (UD) の概念があります。 メンテナンスを実行するときに、Azure では UD の制約が遵守され、(同じ可用性セット内の) 別の UD の仮想マシンは再起動されません。  また、Azure は仮想マシンの次のグループに移行する前に少なくとも 30 分待機します。 

高可用性の詳細については、「[Azure の仮想マシンのリージョンと可用性について](regions-and-availability.MD)」を参照してください。

**Q: 計画済みメンテナンスに関する通知を受け取るにはどうすればよいですか?**

**A:** 計画済みメンテナンス ウェーブは、1 つ以上の Azure リージョンにスケジュールを設定することで開始されます。 開始直後に、電子メール通知がサブスクリプションの所有者に送信されます (サブスクリプションごとに 1 メール)。 この通知の追加のチャネルと受信者は、アクティビティ ログ アラートを使用して構成できます。 計画済みメンテナンスが既にスケジュールされているリージョンに仮想マシンをデプロイした場合、通知を受け取ることはできないため、その VM のメンテナンスの状態を確認する必要があります。

**Q: ポータル、PowerShell、または CLI に計画済みメンテナンスの情報が表示されません。何がおかしいのでしょうか?**

**A:** 計画済みメンテナンスに関する情報は、計画済みメンテナンス ウェーブ中にのみ、影響を受ける VM に提供されます。 つまり、データが表示されない場合、メンテナンス ウェーブが既に完了している (または、まだ開始されていない) か、更新されたサーバーで仮想マシンが既にホストされていると考えられます。

**Q: 仮想マシンが影響を受けるのはいつであるかを正確に把握する方法はありますか?**

**A:** スケジュールを設定するときに、数日間の時間枠が設けられています。 ただし、この時間枠内でのサーバー (および VM) の正確な優先順位付けは不明です。 VM の正確な時間を把握したいお客様は、仮想マシン内から[スケジュールされたイベント](scheduled-events.md)とクエリを使用し、VM が再起動される 15 分前に通知を受け取ることができます。

**Q: 仮想マシンの再起動にはどれくらいの時間がかかりますか?**

**A:** セルフサービス メンテナンス期間中、VM のサイズによっては、再起動に最大数分かかることがあります。 予定メンテナンス期間中の Azure によって開始される再起動の場合は、再起動に通常約 25 分かかります。 Cloud Services (Web/worker ロール)、VM Scale Sets、または可用性セットを使用している場合、予定メンテナンス期間中は VM の各グループ (UD) 間に 30 分の間隔が空けられます。 

**Q: Virtual Machine Scale Sets ではどのようになりますか?**

**A:** Virtual Machine Scale Sets で計画メンテナンスが使用できるようになりました。 セルフサービス メンテナンスを開始する方法については、[VMSS の計画的なメンテナンス](../../virtual-machine-scale-sets/virtual-machine-scale-sets-maintenance-notifications.md)に関するドキュメントを参照してください。

**Q: Cloud Services (Web/worker ロール)、および Service Fabric ではどのようになりますか?**

**A:** これらのプラットフォームは計画済みメンテナンスの影響を受けますが、影響を受けるのは、常に 1 つのアップグレード ドメイン (UD) の VM だけであることから、これらのプラットフォームを使用しているお客様は安全とみなされます。 セルフサービス メンテナンスは、現在、Cloud Services (Web/worker ロール)、および Service Fabric では使用できません。

**Q: VM のメンテナンス情報が表示されません。何が問題だったのでしょうか?**

**A:** VM のメンテナンス情報が表示されない理由はいくつかあります。
1.  Microsoft 社内としてマークされたサブスクリプションを使用している。
2.  VM のメンテナンスがスケジュールされていない。 メンテナンス ウェーブが終了しているか、取り消しまたは変更が行われたため、VM が影響を受けなくなっていると考えられます。
3.  VM リスト ビューに **[メンテナンス]** 列が追加されていない。 この列は既定のビューに追加されていますが、既定以外の列を表示するように構成しているお客様は、VM リスト ビューに **[メンテナンス]** 列を手動で追加する必要があります。

**Q: VM の 2 回目のメンテナンスがスケジュールされています。なぜですか?**

**A:** メンテナンスの再デプロイが既に完了した後に、VM のメンテナンスがスケジュールされるユース ケースがいくつかあります。
1.  メンテナンス ウェーブが取り消され、別のペイロードで再開された場合。 エラーが発生したペイロードが検出されたため、Microsoft が追加のペイロードをデプロイする必要があったと考えられます。
2.  ハードウェア障害により、VM が別のノードに "*サービス復旧*" された場合
3.  お客様が VM を停止 (割り当てを解除) し、再起動することを選択した場合
4.  お客様が VM の**自動シャットダウン**を有効にした場合


## <a name="next-steps"></a>次の手順

[スケジュールされたイベント](scheduled-events.md) を使用して VM でメンテナンス イベントを登録する方法を説明します。
