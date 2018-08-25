---
title: Azure File Sync に登録されたサーバーの管理 | Microsoft Docs
description: Azure ファイル同期のストレージ同期サービスへの Windows Server の登録と登録解除の方法について説明します。
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 468bd70682b1b36e906d32cd7bde58c78bdbb376
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39522001"
---
# <a name="manage-registered-servers-with-azure-file-sync"></a>Azure File Sync に登録されたサーバーの管理
Azure ファイル同期を使用すると、オンプレミスのファイル サーバーの柔軟性、パフォーマンス、互換性を損なわずに Azure Files で組織のファイル共有を一元化できます。 これは、Windows Server を Azure ファイル共有のクイック キャッシュに変換することで行います。 Windows Server で使用可能な任意のプロトコル (SMB、NFS、FTPS など) を使用してデータにローカル アクセスすることができ、世界中に必要な数だけキャッシュを持つことができます。

この記事では、サーバーをトレージ同期サービスに登録して管理する方法について説明します。 Azure File Sync をエンドツーエンドでデプロイする方法の詳細については、[Azure File Sync をデプロイする方法](storage-sync-files-deployment-guide.md)に関するページを参照してください。

## <a name="registerunregister-a-server-with-storage-sync-service"></a>サーバーをストレージ同期サービスに登録/登録解除する
サーバーを Azure File Sync に登録すると、Windows Server と Azure の間に信頼関係が確立されます。 この関係を使用して、サーバーに "*サーバー エンドポイント*" を作成できます。サーバー エンドポイントは、Azure ファイル共有 ("*クラウド エンドポイント*" とも呼ばれます) と同期する必要がある特定のフォルダーを表します。 

### <a name="prerequisites"></a>前提条件
サーバーをストレージ同期サービスに登録するには、まず必要な前提条件を満たすようにサーバーを準備する必要があります。

* サーバーでは、サポートされているバージョンの Windows Server を実行している必要があります。 詳細については、「[サポートされている Windows Server のバージョン](storage-sync-files-planning.md#supported-versions-of-windows-server)」をご覧ください。
* ストレージ同期サービスがデプロイされていること。 ストレージ同期サービスのデプロイ方法の詳細については、「[How to deploy Azure File Sync](storage-sync-files-deployment-guide.md)」(Azure ファイル同期をデプロイする方法) を参照してください。
* サーバーがインターネットに接続され、Azure にアクセスできること。
* サーバー マネージャーの UI を使用して、管理者の [IE セキュリティ強化の構成] を無効にします。
    
    ![[IE セキュリティ強化の構成] が強調表示された サーバー マネージャーの UI](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* AzureRM PowerShell モジュールがサーバーにインストールされていること。 サーバーがフェールオーバー クラスターのメンバーである場合、そのクラスター内のすべてのノードには AzureRM モジュールが必要になります。 AzureRM モジュールをインストールする方法の詳細については、「[Install and configure Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)」(Azure PowerShell のインストールと構成) を参照してください。

    > [!Note]  
    > サーバーの登録と登録解除には、最新バージョンの AzureRM PowerShell モジュールを使用することをお勧めします。 AzureRM パッケージがこのサーバーにインストールされていたことがあり、そのサーバー上の PowerShell バージョンが 5.* 以降の場合、`Update-Module` コマンドレットを使用してそのパッケージを更新することができます。 
* お使いの環境でネットワーク プロキシ サーバーを使用する場合、同期エージェントが使用するサーバー上のプロキシ設定を構成します。
    1. プロキシ IP アドレスとポート番号を決定します。
    2. 以下の 2 つのファイルを編集します。
        * C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config
        * C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config
    3. 上記 2 ファイルの /System.ServiceModel 下に図 1 (このセクションの下) の行を追加します。127.0.0.1:8888 は適正な IP アドレス (127.0.0.1 を置き換える) と適正なポート番号 (8888 を置き換える) に変更します。
    4. コマンド ライン経由で以下の WinHTTP プロキシ設定を設定します。
        * プロキシを表示する:   netsh winhttp show proxy
        * プロキシを設定する:    netsh winhttp set proxy 127.0.0.1:8888
        * プロキシをリセットする:   netsh winhttp show proxy
        * エージェントのインストール後にこれが設定された場合に、同期エージェントを再起動する:   net stop filesyncsvc
    
```XML
    Figure 1:
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
    </system.net>
```    

### <a name="register-a-server-with-storage-sync-service"></a>サーバーをストレージ同期サービスに登録する
Azure File Sync の "*同期グループ*" で、サーバーを "*サーバー エンドポイント*" として使用するには、"*ストレージ同期サービス*" にサーバーを登録しておく必要があります。 サーバーを登録できるストレージ同期サービスは、一度に 1 つに限られます。

#### <a name="install-the-azure-file-sync-agent"></a>Azure File Sync エージェントをインストールする
1. [Azure ファイル同期エージェントをダウンロード](https://go.microsoft.com/fwlink/?linkid=858257)します。
2. Azure ファイル同期エージェントのインストーラーを起動します。
    
    ![Azure ファイル同期エージェントのインストーラーで最初に表示されるウィンドウ](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. 必ず、Microsoft Update による Azure ファイル同期エージェントの更新を有効にします。 サーバー パッケージの重要なセキュリティ修正プログラムと機能強化は Microsoft Update を介して提供されるため、有効にすることが重要です。

    ![Azure ファイル同期エージェントのインストーラーの [Microsoft Update] ウィンドウで、Microsoft Update が有効であることを確認する](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. サーバーがまだ登録されていない場合、インストールの完了すぐにサーバーの登録 UI が表示されます。

> [!Important]  
> サーバーがフェールオーバー クラスターのメンバーである場合、クラスターのすべてのノードに Azure ファイル同期エージェントをインストールする必要があります。

#### <a name="register-the-server-using-the-server-registration-ui"></a>サーバーの登録 UI を使用してサーバーを登録する
> [!Important]  
> クラウド ソリューション プロバイダー (CSP) サブスクリプションでは、サーバー登録 UI を使用できません。 代わりに、PowerShell (このセクションの下) を使用してください。

1. Azure File Sync エージェントのインストールが完了した直後にサーバーの登録 UI が開始されない場合は、`C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe` を実行して手動で開始することができます。
2. *[サインイン]* をクリックして Azure サブスクリプションにアクセスします。 

    ![[サーバーの登録] UI のダイアログの表示](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. ダイアログから、正しいサブスクリプション、リソース グループ、およびストレージ同期サービスを選択します。

    ![ストレージ同期サービスの情報](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. プレビューでは、このプロセスを完了するために 1 回または複数回のサインインが必要です。 

    ![[サインイン] ダイアログ](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> サービスがフェールオーバー クラスターのメンバーである場合、各サーバーで [サーバーの登録] を実行する必要があります。 Azure Portal で登録済みサーバーを表示すると、Azure File Sync は、各ノードを同じフェールオーバー クラスターのメンバーとして自動的に認識し、適宜、各メンバーをグループ化します。

#### <a name="register-the-server-with-powershell"></a>PowerShell でサーバーを登録する
PowerShell を使用してサーバー登録を実行することもできます。 これが、クラウド ソリューション プロバイダー (CSP) サブスクリプションでサポートされている唯一のサーバー登録方法です。

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<your-subscription-id>" -TenantID "<your-tenant-id>"
Register-AzureRmStorageSyncServer -SubscriptionId "<your-subscription-id>" - ResourceGroupName "<your-resource-group-name>" - StorageSyncService "<your-storage-sync-service-name>"
```

### <a name="unregister-the-server-with-storage-sync-service"></a>ストレージ同期サービスからサーバーの登録を解除する
ストレージ同期サービスからサービスの登録を解除するために必要な手順がいくつかあります。 サーバーの登録を適切に解除する方法を見てみましょう。

> [!Warning]  
> Microsoft のエンジニアによってはっきりと指示された場合を除き、サーバーを登録解除してから再登録することにより、またはサーバー エンドポイントを削除してから再作成することにより、同期、クラウドの階層化、または Azure File Sync のその他の側面に関する問題のトラブルシューティングを試みないでください。 サーバーの登録解除およびサーバー エンドポイントの削除は破壊的な操作であり、登録されたサーバーやサーバー エンドポイントが再作成された後で、サーバー エンドポイントがあるボリューム上の階層化されたファイルは Azure ファイル共有上の場所に "再接続" されず、同期エラーが発生します。 また、サーバー エンドポイントの名前空間の外部に存在する階層化されたファイルは完全に失われる可能性があることにも注意してください。 クラウドの階層化が有効にされていなくても、階層化されたファイルがサーバー エンドポイント内に存在することがあります。

#### <a name="optional-recall-all-tiered-data"></a>(省略可能) すべての階層型データを回収する
現在階層化されているファイルを、Azure File Sync を削除した後でも使用できるようにしたい場合は (つまり、これがテスト環境ではなく運用環境の場合)、サーバー エンドポイントを含む各ボリュームのすべてのファイルを呼び戻します。 すべてのサーバー エンドポイントのクラウド階層化を無効にした後、次の PowerShell コマンドレットを実行します。

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <a-volume-with-server-endpoints-on-it>
```

> [!Warning]  
> サーバー エンドポイントをホストするローカル ボリュームに、階層化されたデータをすべて回収できるだけの空き領域がない場合、`Invoke-StorageSyncFileRecall` コマンドレットは失敗します。  

#### <a name="remove-the-server-from-all-sync-groups"></a>すべての同期グループからサーバーを削除する
ストレージ同期サービスでサーバーの登録を解除する前に、そのサーバーのすべてのサーバー エンドポイントを削除する必要があります。 これは Azure Portal で実行できます。

1. サーバーが登録されているストレージ同期サービスに移動します。
2. ストレージ同期サービスの各同期グループで、このサーバーのすべてのサーバー エンドポイントを削除します。 これは、[同期グループ] ウィンドウの関連するサーバー エンドポイントを右クリックして実行できます。

    ![同期グループからサーバー エンドポイントを削除する](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

この処理は、次のように単純な PowerShell スクリプトでも実行できます。

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"

$accountInfo = Connect-AzureRmAccount
Login-AzureRmStorageSync -SubscriptionId $accountInfo.Context.Subscription.Id -TenantId $accountInfo.Context.Tenant.Id -ResourceGroupName "<your-resource-group>"

$StorageSyncService = "<your-storage-sync-service>"

Get-AzureRmStorageSyncGroup -StorageSyncServiceName $StorageSyncService | ForEach-Object { 
    $SyncGroup = $_; 
    Get-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name | Where-Object { $_.DisplayName -eq $env:ComputerName } | ForEach-Object { 
        Remove-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name -ServerEndpointName $_.Name 
    } 
}
```

#### <a name="unregister-the-server"></a>サーバーの登録を解除する
すべてのデータを回収し、すべての同期グループからサーバーを削除したら、サーバーの登録を解除できます。 

1. Azure Portal で、ストレージ同期サービスの *[登録済みサーバー]* セクションに移動します。
2. 登録解除するサーバーを右クリックし、[サーバーの登録解除] をクリックします。

    ![サーバーの登録解除](media/storage-sync-files-server-registration/unregister-server-1.png)

## <a name="ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter"></a>データセンターで Azure File Sync が良好なネイバーであることを確認する 
Azure File Sync がデータセンターで実行されている唯一のサービスであることはほとんどないため、Azure File Sync のネットワークとストレージの使用を制限できます。

> [!Important]  
> 制限の設定が低すぎると、Azure File Sync の同期と回収のパフォーマンスに影響を及ぼします。

### <a name="set-azure-file-sync-network-limits"></a>Azure File Sync のネットワークの制限を設定する
Azure File Sync のネットワーク使用率は、`StorageSyncNetworkLimit` コマンドレットを使用して調整できます。 

たとえば、営業日の午前 9 時～午後 5 時 (17 時) に、Azure File Sync が 10 Mbps 以上使用しないようにするためのスロットルの新しい制限を作成できます。 

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
New-StorageSyncNetworkLimit -Day Monday, Tuesday, Wednesday, Thursday, Friday -StartHour 9 -EndHour 17 -LimitKbps 10000
```

制限を確認するには、次のコマンドレットを使用します。

```PowerShell
Get-StorageSyncNetworkLimit # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

ネットワークの制限を削除するには、`Remove-StorageSyncNetworkLimit` を使用します。 たとえば、次のコマンドは、ネットワークの制限をすべて削除します。

```PowerShell
Get-StorageSyncNetworkLimit | ForEach-Object { Remove-StorageSyncNetworkLimit -Id $_.Id } # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

### <a name="use-windows-server-storage-qos"></a>Windows Server の記憶域 QoS を使用する 
Windows Server 仮想化ホストで実行されている仮想マシンで Azure File Sync がホストされている場合、記憶域 QoS (記憶域のサービス品質) を使用して記憶域 IO の使用を調整できます。 記憶域 QoS ポリシーは、最大値 (制限、上記の StorageSyncNetwork の制限を強制する方法と同様) または最小値 (予約) として設定できます。 最大値ではなく最小値を設定すると、使用可能なストレージ帯域幅を他のワークロードが使用していない場合に、Azure File Sync がバーストして使用できるようになります。 詳細については、[記憶域のサービス品質](https://docs.microsoft.com/windows-server/storage/storage-qos/storage-qos-overview)に関する記事をご覧ください。

## <a name="see-also"></a>関連項目
- [Azure File Sync のデプロイの計画](storage-sync-files-planning.md)
- [Azure File Sync をデプロイする](storage-sync-files-deployment-guide.md) 
- [Azure File Sync のトラブルシューティング](storage-sync-files-troubleshoot.md)