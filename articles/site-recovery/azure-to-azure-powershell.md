---
title: Azure Site Recovery - Azure PowerShell を使用して Azure 仮想マシンのディザスター リカバリーをセットアップしてテストする | Microsoft Docs
description: Azure PowerShell を使用して、Azure Site Recovery による Azure 仮想マシンのディザスター リカバリーを設定する方法を説明します。
services: site-recovery
author: bsiva
manager: abhemraj
editor: raynew
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: bsiva
ms.openlocfilehash: 62dd02d53c14635a386a8c6fa3fbfbd6f91a88f7
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2018
ms.locfileid: "37921088"
---
# <a name="set-up-disaster-recovery-for-azure-virtual-machines-using-azure-powershell"></a>Azure PowerShell を使用して Azure 仮想マシンのディザスター リカバリーを設定する

この記事では、Azure PowerShell を使用して、Azure 仮想マシンのディザスター リカバリーをセットアップし、テストします。 

学習内容は次のとおりです。

> [!div class="checklist"]
> - Recovery Services コンテナーを作成します。
> - PowerShell セッションのコンテナーのコンテキストを設定します。
> - Azure 仮想マシンのレプリケートを開始するようにコンテナーを準備します。
> - ネットワーク マッピングを作成します。
> - 仮想マシンのレプリケート先のストレージ アカウントを作成します。
> - ディザスター リカバリー用の復旧リージョンに Azure 仮想マシンをレプリケートします。
> - テスト フェールオーバーを実行し、検証して、テスト フェールオーバーをクリーンアップします。
> - 復旧リージョンにフェールオーバーします。

> [!NOTE]
> ポータル経由では使用できるシナリオ機能には、Azure PowerShell からは使用できないものがあります。 Azure PowerShell で現在サポートされていないシナリオ機能は次のとおりです。
> - 管理ディスクを使用する Azure 仮想マシンをレプリケートする機能。
> - 仮想マシンの各ディスクを明示的に指定することなく、仮想マシン内のすべてのディスクがレプリケートされる必要があることを指定する機能。  

## <a name="prerequisites"></a>前提条件

開始する前に次の操作を実行してください。
- [シナリオのアーキテクチャとコンポーネント](azure-to-azure-architecture.md)を理解している。
- すべてのコンポーネントの[サポート要件](azure-to-azure-support-matrix.md)を確認する。
- バージョン 5.7.0 以降の AzureRm PowerShell モジュールを用意する。 Azure PowerShell をインストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストールと構成のガイド](/powershell/azureps-cmdlets-docs)に関するページをご覧ください。

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Microsoft Azure サブスクリプションにログインする

Connect-AzureRmAccount コマンドレットを使って、Azure サブスクリプションにログインする

```azurepowershell
Connect-AzureRmAccount
```
Azure サブスクリプションを選択します。 Get-AzureRmSubscription コマンドレットを使って、アクセスできる Azure サブスクリプションの一覧を取得します。 Select-AzureRmSubscription コマンドレットを使って、利用する Azure サブスクリプションを選びます。

```azurepowershell
Select-AzureRmSubscription -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

## <a name="get-details-of-the-virtual-machines-to-be-replicated"></a>レプリケートする仮想マシンの詳細を取得する

この記事の例では、米国東部リージョン内の仮想マシンが米国西部 2 リージョンにレプリケートされ、復旧されます。 レプリケートされる仮想マシンは、OS ディスクと 1 つのデータ ディスクを含む仮想マシンです。 この例で使用されている仮想マシンの名前は AzureDemoVM です。

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzureRmVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM
```

```
ResourceGroupName  : A2AdemoRG
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/A2AdemoRG/providers/Microsoft.Compute/virtualMachines/AzureDemoVM
VmId               : 1b864902-c7ea-499a-ad0f-65da2930b81b
Name               : AzureDemoVM
Type               : Microsoft.Compute/virtualMachines
Location           : eastus
Tags               : {}
DiagnosticsProfile : {BootDiagnostics}
HardwareProfile    : {VmSize}
NetworkProfile     : {NetworkInterfaces}
OSProfile          : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
ProvisioningState  : Succeeded
StorageProfile     : {ImageReference, OsDisk, DataDisks}
```

仮想マシンのディスクについて、ディスクの詳細を取得します。 ディスクの詳細は、後ほど、仮想マシンのレプリケーションを開始するときに使用されます。

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

Recovery Services コンテナーを作成するリソース グループを作成します。 

> [!IMPORTANT]
> * Recovery Services コンテナーと、保護対象の仮想マシンは、異なる Azure の場所にある必要があります。
> * Recovery Services コンテナーのリソース グループと、保護対象の仮想マシンは、異なる Azure の場所にある必要があります。
> * Recovery Services コンテナーと、それが属するリソース グループは、同じ Azure の場所にあってもかまいません。
 
この記事の例では、保護対象の仮想マシンは米国東部リージョンにあります。 ディザスター リカバリー用に選択された復旧リージョンは、米国西部 2 リージョンです。 Recovery Services コンテナーと、コンテナーのリソース グループは、どちらも復旧リージョン (米国西部 2) 内にあります

```azurepowershell
#Create a resource group for the recovery services vault in the recovery Azure region
New-AzureRmResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"
```
```
ResourceGroupName : a2ademorecoveryrg
Location          : westus2
ProvisioningState : Succeeded
Tags              : 
ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg
```
   
Recovery Services コンテナーを作成します。 下の例では、a2aDemoRecoveryVault という名前の Recovery Services コンテナーが、米国西部 2 リージョン内に作成されます。

```azurepowershell
#Create a new Recovery services vault in the recovery region
$vault = New-AzureRmRecoveryServicesVault -Name "a2aDemoRecoveryVault" -ResourceGroupName "a2ademorecoveryrg" -Location "West US 2"

Write-Output $vault
```
```
Name              : a2aDemoRecoveryVault
ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoRecoveryVault
Type              : Microsoft.RecoveryServices/vaults
Location          : westus2
ResourceGroupName : a2ademorecoveryrg
SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
``` 
## <a name="set-the-vault-context"></a>コンテナーのコンテキストを設定する

> [!TIP]
> Azure Site Recovery PowerShell モジュール (AzureRm.RecoveryServices.SiteRecovery モジュール) のほとんどのコマンドレットには、使いやすいエイリアスがあります。 このモジュールのコマンドレットの形式は *\<操作>-**AzureRmRecoveryServicesAsr**\<オブジェクト>* であり、これに対応するエイリアスの形式は *\<操作>-**ASR**\<オブジェクト>* です。 この記事では、読みやすさを考えてコマンドレットのエイリアスを使います。

PowerShell セッションで使用するための、コンテナーのコンテキストを設定します。 これを行うには、コンテナー設定ファイルをダウンロードし、ダウンロードしたファイルを PowerShell セッションでインポートして、コンテナーのコンテキストを設定します。 

コンテキストを設定した後、PowerShell セッションでのそれ以降の Azure Site Recovery 操作は、選んだコンテナーのコンテキストで実行されます。 

 ```azurepowershell
#Download the vault settings file for the vault.
$Vaultsettingsfile = Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteRecovery -Path C:\users\user\Documents\

#Import the downloaded vault settings file to set the vault context for the PowerShell session.
Import-AzureRmRecoveryServicesAsrVaultSettingsFile -Path $Vaultsettingsfile.FilePath

```
```
ResourceName         ResourceGroupName ResourceNamespace          ResouceType
------------         ----------------- -----------------          -----------
a2aDemoRecoveryVault a2ademorecoveryrg Microsoft.RecoveryServices Vaults     
```

```azurepowershell
#Delete the downloaded vault settings file
Remove-Item -Path $Vaultsettingsfile.FilePath
```
## <a name="prepare-the-vault-to-start-replicating-azure-virtual-machines"></a>Azure 仮想マシンのレプリケートを開始するようにコンテナーを準備する

####<a name="1-create-a-site-recovery-fabric-object-to-represent-the-primarysource-region"></a>1.プライマリ (ソース) リージョンを表す Site Recovery ファブリック オブジェクトを作成する

コンテナー内のファブリック オブジェクトは、Azure リージョンを表します。 プライマリ ファブリック オブジェクトは、コンテナーの保護対象となっている仮想マシンが属する Azure リージョンを表すために作成されたファブリック オブジェクトです。 この記事の例では、保護対象の仮想マシンは米国東部リージョンにあります。

> [!NOTE]
> Azure Site Recovery の操作は非同期に実行されます。 操作を開始すると、Azure Site Recovery ジョブが送信されて、ジョブ追跡オブジェクトが返されます。 このジョブ追跡オブジェクトを使用して、ジョブ (Get-ASRJob) の最新の状態を取得し、操作の状態を監視します。

```azurepowershell
#Create Primary ASR fabric
$TempASRJob = New-ASRFabric -Azure -Location 'East US'  -Name "A2Ademo-EastUS" 

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State

$PrimaryFabric = Get-AsrFabric -Name "A2Ademo-EastUS"
```
同じコンテナーに対して、複数の Azure リージョンの仮想マシンが保護されている場合は、ソースの Azure リージョンごとにファブリック オブジェクトを 1 つ作成します。

####<a name="2-create-a-site-recovery-fabric-object-to-represent-the-recovery-region"></a>2.復旧リージョンを表す Site Recovery ファブリック オブジェクトを作成する

復旧ファブリック オブジェクトは、復旧用の Azure の場所を表します。 仮想マシンは、復旧ファブリックによって表される復旧リージョンに対してレプリケートと復旧が行われます (フェールオーバーが発生した場合)。 この例で使用される復旧用 Azure リージョンは、米国西部 2 です。

```azurepowershell
#Create Recovery ASR fabric
$TempASRJob = New-ASRFabric -Azure -Location 'West US 2'  -Name "A2Ademo-WestUS" 

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State

$RecoveryFabric = Get-AsrFabric -Name "A2Ademo-WestUS"

```

####<a name="3-create-a-site-recovery-protection-container-in-the-primary-fabric"></a>手順 3.プライマリ ファブリック内に Site Recovery 保護コンテナーを作成する

保護コンテナーは、ファブリック内でレプリケートされる項目をグループ化するのに使用されるコンテナーです。

```azurepowershell
#Create a Protection container in the primary Azure region (within the Primary fabric)
$TempASRJob = New-AzureRmRecoveryServicesAsrProtectionContainer -InputObject $PrimaryFabric -Name "A2AEastUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

Write-Output $TempASRJob.State

$PrimaryProtContainer = Get-ASRProtectionContainer -Fabric $PrimaryFabric -Name "A2AEastUSProtectionContainer"
```
####<a name="4-create-a-site-recovery-protection-container-in-the-recovery-fabric"></a>4.復旧ファブリック内に Site Recovery 保護コンテナーを作成する

```azurepowershell
#Create a Protection container in the recovery Azure region (within the Recovery fabric)
$TempASRJob = New-AzureRmRecoveryServicesAsrProtectionContainer -InputObject $RecoveryFabric -Name "A2AWestUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"

Write-Output $TempASRJob.State

$RecoveryProtContainer = Get-ASRProtectionContainer -Fabric $RecoveryFabric -Name "A2AWestUSProtectionContainer"
```

####<a name="5-create-a-replication-policy"></a>5.レプリケーション ポリシーを作成する

```azurepowershell
#Create replication policy
$TempASRJob = New-ASRPolicy -AzureToAzure -Name "A2APolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State

$ReplicationPolicy = Get-ASRPolicy -Name "A2APolicy"
```
####<a name="6-create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container"></a>6.プライマリ コンテナーと復旧保護コンテナー間に保護コンテナー マッピングを作成する

保護コンテナー マッピングでは、プライマリ保護コンテナーを、回復保護コンテナーおよびレプリケーション ポリシーとマップします。 保護コンテナー ペア間で仮想マシンをレプリケートするのに使用するレプリケーション ポリシーごとに、1 つのマッピングを作成します。

```azurepowershell
#Create Protection container mapping between the Primary and Recovery Protection Containers with the Replication policy
$TempASRJob = New-ASRProtectionContainerMapping -Name "A2APrimaryToRecovery" -Policy $ReplicationPolicy -PrimaryProtectionContainer $PrimaryProtContainer -RecoveryProtectionContainer $RecoveryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State

$EusToWusPCMapping = Get-ASRProtectionContainerMapping -ProtectionContainer $PrimaryProtContainer -Name "A2APrimaryToRecovery"
```

####<a name="7-create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover"></a>7.フェールバック (フェールオーバー後のレプリケーションの反転) のための保護コンテナー マッピングを作成する

フェールオーバー後に、フェールオーバーした仮想マシンを元の Azure リージョンに戻す準備ができたら、フェールバックを行います。 フェールバックするために、フェールオーバーした仮想マシンは、フェールオーバーしたリージョンから元のリージョンへと逆方向にレプリケートされます。 レプリケーションの反転の際には、元のリージョンと復旧リージョンの役割が入れ替わります。 元のリージョンはこれで新しい復旧リージョンになり、元は復旧リージョンであったものは今後プライマリ リージョンになります。 レプリケーションの反転のための保護コンテナー マッピングは、元のリージョンと復旧リージョンで入れ替えた役割を表します。

```azurepowershell
#Create Protection container mapping (for failback) between the Recovery and Primary Protection Containers with the Replication policy 
$TempASRJob = New-ASRProtectionContainerMapping -Name "A2ARecoveryToPrimary" -Policy $ReplicationPolicy -PrimaryProtectionContainer $RecoveryProtContainer -RecoveryProtectionContainer $PrimaryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

## <a name="create-cache-storage-accounts-and-target-storage-accounts"></a>キャッシュ ストレージ アカウントとターゲット ストレージ アカウントを作成する

キャッシュ ストレージ アカウントは、レプリケートされる仮想マシンと同じ Azure リージョン内の標準的ストレージ アカウントです。 キャッシュ ストレージ アカウントは、変更が復旧 Azure リージョンに移動される前に、レプリケーションの変更を一時的に保持するために使用されます。 仮想マシンの別のディスクのために、異なるキャッシュ ストレージ アカウントを指定できます (ただし、これは必須ではありません)。

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$EastUSCacheStorageAccount = New-AzureRmStorageAccount -Name "a2acachestorage" -ResourceGroupName "A2AdemoRG" -Location 'East US' -SkuName Standard_LRS -Kind Storage
```

管理ディスクを使用していない仮想マシンの場合、ターゲット ストレージ アカウントは、仮想マシンのディスクのレプリケート先となる復旧リージョン内のストレージ アカウントです。 ターゲット ストレージ アカウントは、Standard Storage アカウントと Premium Storage アカウントのどちらでもかまいません。 ディスクのデータ変化率 (IO の書き込み率) と、Azure Site Recovery が各ストレージの種類でサポートしているデータ変更頻度の上限に基づいて、必要なストレージ アカウントの種類を選択します。

```azurepowershell
#Create Target storage account in the recovery region. In this case a Standard Storage account
$WestUSTargetStorageAccount = New-AzureRmStorageAccount -Name "a2atargetstorage" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -SkuName Standard_LRS -Kind Storage

```

## <a name="create-network-mappings"></a>ネットワーク マッピングを作成する

ネットワーク マッピングは、プライマリ リージョン内の仮想ネットワークを、復旧リージョン内の仮想ネットワークにマップします。 ネットワーク マッピングでは、復旧リージョン内の Azure 仮想ネットワークを指定します。これが、プライマリ仮想ネットワーク内の仮想マシンのフェールオーバー先になる必要があります。 1 つの Azure 仮想ネットワークをマップできるのは、復旧リージョン内の 1 つの Azure 仮想ネットワークのみです。

- フェールオーバー先の復旧リージョン内に Azure 仮想ネットワークを作成します

   ```azurepowershell
    #Create a Recovery Network in the recovery region
    $WestUSRecoveryVnet = New-AzureRmVirtualNetwork -Name "a2arecoveryvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.0.0.0/16"

    Add-AzureRmVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $WestUSRecoveryVnet -AddressPrefix "10.0.0.0/20" | Set-AzureRmVirtualNetwork

    $WestUSRecoveryNetwork = $WestUSRecoveryVnet.Id
   ```
- プライマリ仮想ネットワーク (仮想マシンの接続先 vnet) を取得します
   ```azurepowershell
    #Retrieve the virtual network that the virtual machine is connected to
    
    #Get first network interface card(nic) of the virtual machine
    $SplitNicArmId = $VM.NetworkProfile.NetworkInterfaces[0].Id.split("/")

    #Extract resource group name from the ResourceId of the nic
    $NICRG = $SplitNicArmId[4]

    #Extract resource name from the ResourceId of the nic
    $NICname = $SplitNicArmId[-1]

    #Get network interface details using the extracted resource group name and resourec name
    $NIC = Get-AzureRmNetworkInterface -ResourceGroupName $NICRG -Name $NICname

    #Get the subnet ID of the subnet that the nic is connected to
    $PrimarySubnet = $NIC.IpConfigurations[0].Subnet

    # Extract the resource ID of the Azure virtual network the nic is connected to from the subnet ID
    $EastUSPrimaryNetwork = (Split-Path(Split-Path($PrimarySubnet.Id))).Replace("\","/")  
   ```
- プライマリ仮想ネットワークと、復旧用の仮想ネットワーク間のネットワーク マッピングを作成します
   ```azurepowershell
    #Create an ASR network mapping between the primary Azure virtual network and the recovery Azure virtual network
    $TempASRJob = New-ASRNetworkMapping -AzureToAzure -Name "A2AEusToWusNWMapping" -PrimaryFabric $PrimaryFabric -PrimaryAzureNetworkId $EastUSPrimaryNetwork -RecoveryFabric $RecoveryFabric -RecoveryAzureNetworkId $WestUSRecoveryNetwork
    
    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
            sleep 10; 
            $TempASRJob = Get-ASRJob -Job $TempASRJob
    }
    
    #Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
    Write-Output $TempASRJob.State
    
   ```
- 逆方向 (フェールバック) のネットワーク マッピングを作成します
    ```azurepowershell
    #Create an ASR network mapping for failback between the recovery Azure virtual network and the primary Azure virtual network
    $TempASRJob = New-ASRNetworkMapping -AzureToAzure -Name "A2AWusToEusNWMapping" -PrimaryFabric $RecoveryFabric -PrimaryAzureNetworkId $WestUSRecoveryNetwork -RecoveryFabric $PrimaryFabric -RecoveryAzureNetworkId $EastUSPrimaryNetwork
    
    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
            sleep 10; 
            $TempASRJob = Get-ASRJob -Job $TempASRJob
    }
        
    #Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
    Write-Output $TempASRJob.State
    ```

## <a name="replicate-azure-virtual-machine"></a>Azure 仮想マシンをレプリケートする

Azure 仮想マシンをレプリケートします。

```azurepowershell
#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#Disk replication configuration for the OS disk
$OSDiskReplicationConfig = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $OSDiskVhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Disk replication configuration for data disk
$DataDisk1ReplicationConfig = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $DataDisk1VhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzureRmResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.  
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}


#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

レプリケーションの開始操作が成功すると、仮想マシンのデータが復旧リージョンにレプリケートされます。 

レプリケーション プロセスは、最初に、復旧リージョン内で仮想マシンのレプリケートする側のディスクのコピーをシード処理することによって始まります。 このフェーズは、初期レプリケーション フェーズと呼ばれます。 

初期レプリケーションが完了すると、レプリケーションは差分同期フェーズに移行します。 この時点では、仮想マシンは保護されていて、それに対してテスト フェールオーバーの操作を実行できます。 初期レプリケーションの完了後、仮想マシンを表すレプリケートされた項目のレプリケーション状態は、"Protected" 状態に移行します。

それに対応するレプリケーションの保護された項目の詳細を取得することによって、仮想マシンのレプリケーション状態とレプリケーション正常性を監視します。
```azurepowershell
 Get-ASRReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState ReplicationHealth
------------ --------------- -----------------
AzureDemoVM  Protected       Normal           
```

## <a name="perform-a-test-failover-validate-and-cleanup-test-failover"></a>テスト フェールオーバーを実行し、検証して、テスト フェールオーバーをクリーンアップする

仮想マシンのレプリケーションが保護された状態に達したら、仮想マシン (仮想マシンのレプリケーションの保護された項目) に対してテスト フェールオーバーの操作を実行できます。

```azurepowershell
#Create a seperate network for test failover (not connected to my DR network)
$TFOVnet = New-AzureRmVirtualNetwork -Name "a2aTFOvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.3.0.0/16"

Add-AzureRmVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $TFOVnet -AddressPrefix "10.3.0.0/20" | Set-AzureRmVirtualNetwork

$TFONetwork= $TFOVnet.Id
```

テスト フェールオーバーを実行します。
```azurepowershell
$ReplicationProtectedItem = Get-ASRReplicationProtectedItem -FriendlyName "AzureDemoVM" -ProtectionContainer $PrimaryProtContainer

$TFOJob = Start-ASRTestFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -AzureVMNetworkId $TFONetwork -Direction PrimaryToRecovery


```

テスト フェールオーバーの操作が完了するのを待ちます。
```azurepowershell
Get-ASRJob -Job $TFOJob
```

```
Name             : 3dcb043e-3c6d-4e0e-a42e-8d4245668547
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/3dcb043e-3c6d-4e0e-a42e-8d4245668547
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : TestFailover
DisplayName      : Test failover
ClientRequestId  : 1ef8515b-b130-4452-a44d-91aaf071931c ActivityId: 907bb2bc-ebe6-4732-8b66-77d0546eaba8
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:29:43 AM
EndTime          : 4/25/2018 4:33:06 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   : 
Tasks            : {Prerequisites check for test failover, Create test virtual machine, Preparing the virtual machine, Start the virtual machine}
Errors           : {}
```
テスト フェールオーバーのジョブが正常に完了したら、テスト フェールオーバーが行われた仮想マシンに接続して、テスト フェールオーバーを検証できます。

テスト フェールオーバーを行った仮想マシンでテストが完了したら、テスト フェールオーバー操作のクリーンアップを開始して、テスト コピーをクリーンアップします。 この操作により、テスト フェールオーバーによって作成された仮想マシンのテスト コピーが削除されます。

```azurepowershell
$Job_TFOCleanup = Start-ASRTestFailoverCleanupJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-ASRJob -Job $Job_TFOCleanup | Select State
```
```
State    
-----    
Succeeded
```

## <a name="failover-to-azure"></a>Azure へのフェールオーバー

仮想マシンを特定の復旧ポイントにフェールオーバーします。

```azurepowershell
$RecoveryPoints = Get-ASRRecoveryPoint -ReplicationProtectedItem $ReplicationProtectedItem

#The list of recovery points returned may not be sorted chronologically and will need to be sorted first, in order to be able to find the oldest or the latest recovery points for the virtual machine.
"{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[-1].RecoveryPointTime
```
```
CrashConsistent 4/24/2018 11:10:25 PM
```


```azurepowershell
#Start the failover job
$Job_Failover = Start-ASRUnplannedFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[-1]

do {
        $Job_Failover = Get-ASRJob -Job $Job_Failover;
        sleep 30;
} while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))

$Job_Failover.State
```
```
Succeeded
```

フェールオーバーが正常に完了したら、フェールオーバー操作をコミットできます。
```azurepowershell
$CommitFailoverJOb = Start-ASRCommitFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-ASRJOb -Job $CommitFailoverJOb
```

```
Name             : 58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : CommitFailover
DisplayName      : Commit
ClientRequestId  : 10a95d6c-359e-4603-b7d9-b7ee3317ce94 ActivityId: 8751ada4-fc42-4238-8de6-a82618408fcf
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:50:58 AM
EndTime          : 4/25/2018 4:51:01 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   : 
Tasks            : {Prerequisite check, Commit}
Errors           : {}
```

フェールオーバー後、元のリージョンに戻る準備ができたら、Update-AzureRmRecoveryServicesAsrProtectionDirection コマンドレットを使用して、レプリケーションの保護された項目のレプリケーションの反転を開始します。

## <a name="next-steps"></a>次の手順
PowerShell による復旧計画の作成や復旧計画のフェールオーバーのテストなど、他のタスクの実行方法については、[Azure Site Recovery PowerShell のリファレンス](https://docs.microsoft.com/powershell/module/AzureRM.RecoveryServices.SiteRecovery)をご覧ください。
