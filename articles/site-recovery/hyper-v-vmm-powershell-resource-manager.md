---
title: PowerShell (Azure Resource Manager) を使用して Virtual Machine Manager クラウド内の Hyper-V VM をセカンダリ サイトにレプリケートする | Microsoft Docs
description: PowerShell (Resource Manager) を使用して Virtual Machine Manager クラウド内の Hyper-V VM をセカンダリ Virtual Machine Manager サイトにレプリケートする方法について説明します。
services: site-recovery
author: sujaytalasila
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: sutalasi
ms.openlocfilehash: 0fecc7ba48daf396c3d25969cdda5891bdf08232
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2018
ms.locfileid: "37917967"
---
# <a name="replicate-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>PowerShell (Resource Manager) を使用して Hyper-V VM をセカンダリ サイトへレプリケートする

この記事では、[Azure Site Recovery](site-recovery-overview.md) を使用して、System Center Virtual Machine Manager クラウドの Hyper-V VM をセカンダリ オンプレミス サイトの Virtual Machine Manager クラウドにレプリケートする手順を自動化する方法について説明します。

## <a name="prerequisites"></a>前提条件

- [シナリオのアーキテクチャとコンポーネント](hyper-v-vmm-architecture.md)を確認する。
- すべてのコンポーネントの[サポート要件](site-recovery-support-matrix-to-sec-site.md)を確認する。
- Virtual Machine Manager サーバーと Hyper-V ホストが[サポート要件](site-recovery-support-matrix-to-sec-site.md)に準拠していることを確認する。
- レプリケートする VM が[レプリケートされるマシンのサポート要件](site-recovery-support-matrix-to-sec-site.md)に準拠していることを確認する。


## <a name="prepare-for-network-mapping"></a>ネットワーク マッピングを準備する

[ネットワーク マッピング](hyper-v-vmm-network-mapping.md)は、ソース クラウドとターゲット クラウド内のオンプレミス Virtual Machine Manager VM ネットワーク間をマップします。 マッピングでは次が行われます。

- フェールオーバー後に VM を適切なターゲット VM ネットワークに接続します。 
- ターゲット Hyper-V ホスト サーバーにレプリカ VM を適切に配置します。 
- ネットワーク マッピングを構成しない場合、レプリカ VM はフェールオーバー後に VM ネットワークに接続されません。

次のように Virtual Machine Manager を準備します。

* ソースおよびターゲットの Virtual Machine Manager サーバー上にそれぞれ [Virtual Machine Manager 論理ネットワーク](https://docs.microsoft.com/system-center/vmm/network-logical)があることを確認します。

    - ソース サーバー上の論理ネットワークは、Hyper-V ホストが配置されているソース クラウドと関連付けられている必要があります。
    - ターゲット サーバーの論理ネットワークは、ターゲット クラウドと関連付けられている必要があります。
* ソースおよびターゲットの Virtual Machine Manager サーバー上にそれぞれ [VM ネットワーク](https://docs.microsoft.com/system-center/vmm/network-virtual)があることを確認します。 VM ネットワークは、各場所の論理ネットワークにリンクされている必要があります。
* ソース Hyper-V ホスト上の VM をソース VM ネットワークに接続します。 

## <a name="prepare-for-powershell"></a>PowerShell の準備

Azure PowerShell を使用する準備が整っていることを確認します。

- PowerShell を既に使用している場合は、バージョン 0.8.10 以降にアップグレードします。 PowerShell の設定方法については、[こちら](/powershell/azureps-cmdlets-docs)を参照してください。
- PowerShell の設定と構成を完了したら、[サービス コマンドレット](/powershell/azure/overview)を確認します。
- PowerShell のパラメーター値、入力、出力の使用方法の詳細については、[概要](/powershell/azure/get-started-azureps)に関するガイドを参照してください。

## <a name="set-up-a-subscription"></a>サブスクリプションを設定する
1. PowerShell から、Azure アカウントにサインインします。

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Connect-AzureRmAccount #-Credential $Cred
2. サブスクリプションとサブスクリプション ID の一覧を取得します。 Recovery Services コンテナーを作成するサブスクリプションの ID をメモします。 

        Get-AzureRmSubscription
3. コンテナーのサブスクリプションを設定します。

        Set-AzureRmContext –SubscriptionID <subscriptionId>

## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する
1. Azure Resource Manager リソース グループを作成します (まだ存在しない場合)。

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location
2. 新しい Recovery Services コンテナーを作成します。 後ほど使用できるように、このコンテナー オブジェクトを変数に格納します。 

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location
   
    コンテナー オブジェクトは、作成後に Get-AzureRMRecoveryServicesVault コマンドレットを使用して取得できます。

## <a name="set-the-vault-context"></a>コンテナーのコンテキストを設定する
1. 既存のコンテナーを取得します。

       $vault = Get-AzureRmRecoveryServicesVault -Name #vaultname
2. コンテナーのコンテキストを設定します。

       Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="install-the-site-recovery-provider"></a>Site Recovery プロバイダーをインストールする
1. Virtual Machine Manager マシンで、次のコマンドを実行してディレクトリを作成します。

       New-Item c:\ASR -type directory
2. ダウンロードしたプロバイダー設定ファイルを使用してファイルを抽出します。

       pushd C:\ASR\
       .\AzureSiteRecoveryProvider.exe /x:. /q
3. プロバイダーをインストールし、インストールが完了するまで待ちます。

       .\SetupDr.exe /i
       $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
       do
       {
         $isNotInstalled = $true;
         if(Test-Path $installationRegPath)
         {
           $isNotInstalled = $false;
         }
       }While($isNotInstalled)

4. サーバーをコンテナーに登録します。

       $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
       pushd $BinPath
       $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

## <a name="create-and-associate-a-replication-policy"></a>レプリケーション ポリシーを作成して関連付ける
1. 次のように、レプリケーション ポリシーを作成します。この場合は Hyper-V 2012 R2 です。

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $RepProvider = HyperVReplica2012R2
        $Recoverypoints = 24                    #specify the number of hours to retain recovery pints
        $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
        $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
        $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
        $InitialRepMethod = "Online" #options are "Online" or "Offline"

        $policyresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod

    > [!NOTE]
    > Virtual Machine Manager クラウドには、複数バージョンの Windows Server を実行する Hyper-V ホストを含めることができますが、レプリケーション ポリシーはオペレーティング システムの特定のバージョン用です。 さまざまなオペレーティング システムでさまざまなホストを実行している場合は、システムごとに個別のレプリケーション ポリシーを作成してください。 たとえば、Windows Server 2012 で実行されているホストが 5 台、Windows Server 2012 R2 で実行されているホストが 3 台ある場合は、2 つのレプリケーション ポリシーを作成します。 オペレーティング システムの種類ごとに 1 つずつ作成します。

2. プライマリ保護コンテナー (プライマリ Virtual Machine Manager クラウド) と復旧保護コンテナー (復旧 Virtual Machine Manager クラウド) を取得します。

       $PrimaryCloud = "testprimarycloud"
       $primaryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

       $RecoveryCloud = "testrecoverycloud"
       $recoveryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
3. フレンドリ名を使用して、作成したレプリケーション ポリシーを取得します。

       $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname
4. 保護コンテナー (Virtual Machine Manager クラウド) とレプリケーション ポリシーの関連付けを開始します。

       $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
5. ポリシー関連付けジョブが完了するのを待ちます。 ジョブが完了したかどうかを確認するには、次の PowerShell スニペットを使用します。

       $job = Get-AzureRmSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }

6. ジョブの処理が完了したら、次のコマンドを実行します。

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

操作の完了を確認するには、「[アクティビティを監視する](#monitor-activity)」の手順を実行します。

##  <a name="configure-network-mapping"></a>ネットワーク マッピングの構成
1. このコマンドを使用して、現在のコンテナーのサーバーを取得します。 このコマンドで、$Servers 配列変数に Site Recovery サーバーが格納されます。

        $Servers = Get-AzureRmSiteRecoveryServer
2. このコマンドを実行して、ソース Virtual Machine Manager サーバー用とターゲット Virtual Machine Manager サーバー用のネットワークを取得します。

        $PrimaryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

    > [!NOTE]
    > ソース Virtual Machine Manager サーバーは、サーバー配列で 1 つ目または 2 つ目として指定できます。 Virtual Machine Manager サーバー名を確認し、ネットワークを適切に取得します。


3. このコマンドレットは、プライマリ ネットワークと復旧ネットワークの間のマッピングを作成します。 これは $PrimaryNetworks の最初の要素として、プライマリ ネットワークを指定します。 これは $RecoveryNetworks の最初の要素として、復旧ネットワークを指定します。

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]


## <a name="enable-protection-for-vms"></a>VM の保護を有効にする
サーバー、クラウド、ネットワークを正しく構成したら、クラウド内の VM の保護を有効にすることができます。

1. 保護を有効にするには、次のコマンドを実行して保護コンテナーを取得します。

          $PrimaryProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
2. 次のように、保護エンティティ (VM) を取得します。

           $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
3. VM のレプリケーションを有効にします。

          $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy

## <a name="run-a-test-failover"></a>テスト フェールオーバーの実行

デプロイをテストするには、単一の仮想マシンについてテスト フェールオーバーを実行します。 複数の VM が含まれる復旧計画を作成し、その計画についてテスト フェールオーバーを実行することもできます。 テスト フェールオーバーは、孤立したネットワークでフェールオーバーと復旧のシミュレーションを実行します。

1. VM がフェールオーバーする VM を取得します。

       $Servers = Get-AzureRmSiteRecoveryServer
       $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

2. テスト フェールオーバーを実行します。

   単一の VM の場合:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1]
    
   復旧計画の場合:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1]

操作の完了を確認するには、「[アクティビティを監視する](#monitor-activity)」の手順を実行します。

## <a name="run-planned-and-unplanned-failovers"></a>計画フェールオーバーと計画外のフェールオーバーの実行

1. 計画フェールオーバーを実行します。

   単一の VM の場合:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   復旧計画の場合:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

2. 計画外のフェールオーバーを実行します。

   単一の VM の場合:
        
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   復旧計画の場合:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

## <a name="monitor-activity"></a>アクティビティを監視する
フェールオーバー アクティビティを監視するには、次のコマンドを使用します。 ジョブ間の処理が終了するまで待ちます。

    Do
    {
        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
            $isJobLeftForProcessing = $true;
        }

    if($isJobLeftForProcessing)
        {
            Start-Sleep -Seconds 60
        }
    }While($isJobLeftForProcessing)



## <a name="next-steps"></a>次の手順

Site Recovery と Resource Manager PowerShell コマンドレットの詳細については、[こちら](/powershell/module/azurerm.recoveryservices.backup/#recovery)を参照してください。
