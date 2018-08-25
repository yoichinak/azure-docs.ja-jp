---
title: Azure File Sync のトラブルシューティング | Microsoft Docs
description: Azure File Sync の一般的な問題をトラブルシューティングします。
services: storage
author: jeffpatt24
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: jeffpatt
ms.component: files
ms.openlocfilehash: e0c9708107139ec899cd5902a68ff90b57b741f7
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005921"
---
# <a name="troubleshoot-azure-file-sync"></a>Azure File Sync のトラブルシューティング
Azure File Sync を使用すると、オンプレミスのファイル サーバーの柔軟性、パフォーマンス、互換性を維持したまま Azure Files で組織のファイル共有を一元化できます。 Azure File Sync により、ご利用の Windows Server が Azure ファイル共有の高速キャッシュに変わります。 SMB、NFS、FTPS など、Windows Server 上で利用できるあらゆるプロトコルを使用して、データにローカルにアクセスできます。 キャッシュは、世界中にいくつでも必要に応じて設置することができます。

この記事は、Azure File Sync のデプロイで発生する可能性がある問題のトラブルシューティングと解決を支援することを目的としています。 問題をさらに調査する必要がある場合に、システムから重要なログを収集する方法についても説明します。 ご質問に対する回答がここで見つからない場合は、次のチャネルでお問い合わせください (上から順に)。

1. [Azure Storage フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)。
2. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)。
3. Microsoft サポート。 新しいサポート要求を作成するには、Azure Portal の **[ヘルプ]** タブで、**[ヘルプとサポート]** ボタンを選択し、**[新しいサポート要求]** を選択します。

## <a name="im-having-an-issue-with-azure-file-sync-on-my-server-sync-cloud-tiering-etc-should-i-remove-and-recreate-my-server-endpoint"></a>サーバーで Azure File Sync に関する問題 (同期、クラウド階層化など)が発生しています。 サーバー エンドポイントを削除して再作成する必要がありますか。
[!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]

## <a name="agent-installation-and-server-registration"></a>エージェントのインストールとサーバー登録
<a id="agent-installation-failures"></a>**エージェントのインストール エラーをトラブルシューティングする**  
管理者特権でのコマンド プロンプトで Azure File Sync エージェントのインストールに失敗した場合は、次のコマンドを実行してエージェントのインストール時のログ記録を有効にします。

```
StorageSyncAgent.msi /l*v AFSInstaller.log
```

installer.log をレビューして、インストールが失敗した原因を特定します。

<a id="agent-installation-on-DC"></a>**Active Directory ドメイン コントローラーにエージェントをインストールできない**  
同期エージェントを Active Directory ドメイン コントローラーにインストールしようとしたが、PDC ロール所有者が Windows Server 2008R2 (またはそれより前のバージョン) に存在する場合は、同期エージェントのインストールが失敗するという問題が発生することがあります。

この問題を解決するには、Windows Server 2012R2 以降を実行している別のドメイン コントローラーに PDC ロールを転送してから、同期エージェントをインストールします。

<a id="server-registration-missing"></a>**Azure Portal の [登録済みサーバー] にサーバーが表示されない**  
サーバーがストレージ同期サービスの **[登録済みサーバー]** に表示されない場合:
1. 登録するサーバーにサインインします。
2. ファイル エクスプローラーを開き、ストレージ同期エージェントのインストール ディレクトリ (既定の場所は C:\Program Files\Azure\StorageSyncAgent) に移動します。 
3. ServerRegistration.exe を実行し、ストレージ同期サービスにサーバーを登録するウィザードを完了します。

<a id="server-already-registered"></a>**Azure File Sync エージェントのインストール中に [サーバー登録] ダイアログに「このサーバーは既に登録されています」というメッセージが表示される** 

!["server is already registered" (このサーバーは既に登録されています) エラー メッセージが表示された [サーバーの登録] ダイアログのスクリーンショット](media/storage-sync-files-troubleshoot/server-registration-1.png)

このメッセージは、以前にサーバーがストレージ同期サービスに登録されていた場合に表示されます。 現在のストレージ同期サービスからサーバーを登録解除し、新しいストレージ同期サービスに登録するには、[Azure File Sync でのサーバーの登録解除](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service)に関する記事に記載されている手順を完了します。

ストレージ同期サービスの **[登録済みサーバー]** にサーバーが一覧表示されていない場合は、登録解除するサーバー上で次の PowerShell コマンドを実行します。

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> サーバーがクラスターの一部である場合は、省略可能な *Reset-StorageSyncServer -CleanClusterRegistration* パラメーターを使用して、クラスターの登録を削除することもできます。

<a id="web-site-not-trusted"></a>**サーバーの登録時に多数の "Web サイトが信頼されていない" という応答が表示されます。なぜですか?**  
このエラーは、サーバーの登録中に **Enhanced Internet Explorer Security** ポリシーが有効になった場合に発生します。 **Enhanced Internet Explorer Security** ポリシーを適切に無効にする方法の詳細については、「[Azure File Sync で使用する Windows Server の準備](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync)」および[Azure File Sync のデプロイ方法](storage-sync-files-deployment-guide.md)に関する記事をご覧ください。

## <a name="sync-group-management"></a>同期グループ管理
<a id="cloud-endpoint-using-share"></a>**クラウド エンドポイントの作成が "The specified Azure FileShare is already in use by a different CloudEndpoint"\(指定された Azure ファイル共有は別の CloudEndpoint で既に使用されています\) というエラーで失敗する**  
この問題は、Azure ファイル共有が別のクラウド エンドポイントによって既に使用されている場合に発生します。 

このメッセージが表示されたときに、Azure ファイル共有が現在クラウド エンドポイントで使用されていない場合は、次の手順を完了して、Azure ファイル共有上の Azure File Sync メタデータをクリアします。

> [!Warning]  
> 現在クラウド エンドポイントによって使用されている Azure ファイル共有上のメタデータを削除すると、Azure File Sync の操作は失敗します。 

1. Azure ポータルで、Azure ファイル共有に移動します。  
2. Azure ファイル共有を右クリックし、**[メタデータの編集]** を選択します。
3. **[SyncService]** を右クリックし、**[削除]** を選択します。

<a id="cloud-endpoint-authfailed"></a>**クラウド エンドポイントの作成が "AuthorizationFailed" エラーで失敗する**  
この問題は、ユーザー アカウントがクラウド エンドポイントを作成するための十分な権限を持っていない場合に発生します。 

クラウド エンドポイントを作成するには、次の Microsoft 承認アクセス許可を持つユーザー アカウントが必要です。  
* 読み取り: ロール定義の取得
* 書き込み: カスタムのロール定義の作成または更新
* 読み取り: ロール割り当ての取得
* 書き込み: ロール割り当ての作成

次の組み込みロールには、必要な Microsoft 承認アクセス許可が付与されています。  
* Owner
* User Access Administrator

現在のユーザー アカウントのロールに必要なアクセス許可が付与されているかどうかを確認するには:  
1. Azure ポータルで、**[リソース グループ]** を選択します。
2. ストレージ アカウントのあるリソース グループを選択し、**[アクセス制御 (IAM)]** を選択します。
3. ユーザー アカウントに割り当てる **[ロール]** (所有者や共同作成者など) を選択します。
4. **[リソース プロバイダー]** 一覧で、**[Microsoft 承認]** を選択します。 
    * **[ロールの割り当て]** のアクセス許可が **[読み取り]** と **[書き込み]** になっている必要があります。
    * **[ロール定義]** のアクセス許可が **[読み取り]** と **[書き込み]** になっている必要があります。

<a id="server-endpoint-createjobfailed"></a>**サーバー エンドポイントの作成が "MgmtServerJobFailed" (エラー コード: -2134375898) というエラーで失敗する**  
この問題は、サーバー エンドポイントのパスがシステム ボリューム上にあり、クラウドの階層化が有効な場合に発生します。 システム ボリュームでは、クラウドの階層化はサポートされていません。 システム ボリュームにサーバー エンドポイントを作成するには、サーバー エンドポイントを作成するときにクラウドの階層化を無効にします。

<a id="server-endpoint-deletejobexpired"></a>**サーバー エンドポイントの削除が "MgmtServerJobExpired" エラーで失敗する**                
この問題は、サーバーがオフラインの場合、またはネットワークに接続できない場合に発生します。 サーバーを使用できなくなったら、ポータルでサーバーの登録を解除します。これで、サーバー エンドポイントが削除されます。 サーバー エンドポイントを削除するには、[Azure File Sync 使用したサーバーの登録解除](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service)に関するセクションで説明されている手順を実行します。

<a id="server-endpoint-provisioningfailed"></a>**[サーバー エンドポイントのプロパティ] ページが開かない、またはクラウドの階層化ポリシーを更新できない**  
この問題は、サーバー エンドポイントでの管理操作が失敗する場合に発生することがあります。 Azure Portal で [サーバー エンドポイントのプロパティ] ページが開かない場合は、サーバーから PowerShell コマンドでサーバー エンドポイント を更新すると、この問題が解決する場合があります。 

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
# Get the server endpoint id based on the server endpoint DisplayName property
Get-AzureRmStorageSyncServerEndpoint `
    -SubscriptionId mysubguid `
    -ResourceGroupName myrgname `
    -StorageSyncServiceName storagesvcname `
    -SyncGroupName mysyncgroup

# Update the free space percent policy for the server endpoint
Set-AzureRmStorageSyncServerEndpoint `
    -Id serverendpointid `
    -CloudTiering true `
    -VolumeFreeSpacePercent 60
```

## <a name="sync"></a>同期
<a id="afs-change-detection"></a>**SMB またはポータルを使用して Azure ファイル共有内にファイルを直接作成した場合、ファイルが同期グループ内のサーバーと同期されるまでどのくらい時間がかかりますか?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a>**サーバー エンドポイントの正常性が数時間にわたって保留状態になる**  
この問題は、クラウド エンドポイントを作成してデータが格納されている Azure ファイル共有を使用した場合に発生することが予期されます。 クラウドとサーバー エンドポイント間でファイルを同期する前に、Azure ファイル共有の変更をスキャンする変更列挙ジョブが完了している必要があります。 このジョブの完了にかかる時間は、Azure ファイル共有内の名前空間のサイズに依存します。 変更列挙ジョブが完了したら、サーバー エンドポイントの正常性を更新する必要があります。

### <a id="broken-sync"></a>同期の正常性を監視するにはどうすればよいですか。
# <a name="portaltabportal1"></a>[ポータル](#tab/portal1)
各同期グループ内で、個別のサーバー エンドポイントをドリルダウンして、最後に完了した同期セッションの状態を確認できます。 [正常性] 列が緑色で、[Files Not Syncing]\(同期していないファイル数\) の値が 0 の場合は、同期が予期したとおりに動作していることを示します。 そうでない場合は、下に示す一般的な同期エラーの一覧と、同期していないファイルの処理方法を参照してください。 

![Azure portal のスクリーンショット](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="servertabserver"></a>[サーバー](#tab/server)
イベント ビューアーの `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry` にあるテレメトリ ログに移動します。 イベント 9102 は、完了した同期セッションに該当します。同期の最新の状態を調べるには、ID 9102 の最新のイベントを探します。 SyncDirection は、このセッションがアップロードとダウンロードのどちらだったかを示しています。 HResult が 0 の場合、同期セッションは成功しています。 HResult が 0 以外の場合は、同期中にエラーが発生したことを意味します。以下に示す一般的なエラーの一覧を参照してください。 PerItemErrorCount が 0 より大きい場合は、一部のファイルまたはフォルダーが正しく同期されなかったことを意味します。 HResult が 0 であるのに、PerItemErrorCount が 0 より大きい場合もあります。

成功したアップロードの例を次に示します。 簡潔にするために、下の一覧では、各 9102 イベントに含まれる一部の値のみを示しています。 

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: 0, 
SyncFileCount: 2, SyncDirectoryCount: 0,
AppliedFileCount: 2, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0,
TransferredFiles: 2, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

これに対して、失敗したアップロードは次のようになります。

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: -2134364065,
SyncFileCount: 0, SyncDirectoryCount: 0, 
AppliedFileCount: 0, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0, 
TransferredFiles: 0, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

同期セッション全体が失敗するか、または PerItemErrorCount が 0 以外であるのに、セッションが進行して一部のファイルが正常に同期される場合もあります。 このことは、成功しているセッションの数を示す Applied* フィールド (AppliedFileCount、AppliedDirCount、AppliedTombstoneCount、および AppliedSizeBytes) で確認できます。 行内に失敗している同期セッションが複数あるのに Applied* の数が増えている場合は、サポート チケットを開く前に、時間をとって同期の再試行を待つ必要があります。

---

### <a name="how-do-i-monitor-the-progress-of-a-current-sync-session"></a>現在の同期セッションの進行状況を監視するにはどうすればよいですか。
# <a name="portaltabportal1"></a>[ポータル](#tab/portal1)
同期グループ内で、問題のサーバー エンドポイントに移動して [同期アクティビティ] セクションを調べ、現在の同期セッション内のアップロード済みファイルとダウンロード済みファイルの数を確認します。 この状態は約 5 分遅れて表示されるため、この時間内に完了するほど小規模な同期セッションの場合は、ポータルでレポートされない可能性があります。 

# <a name="servertabserver"></a>[サーバー](#tab/server)
サーバーのテレメトリ ログ (イベント ビューアーで [アプリケーションとサービス]\[Microsoft]\[FileSync]\[Agent] に移動) で、最新の 9302 イベントを調べます。 このイベントは、現在の同期セッションの状態を示します。 TotalItemCount は同期が必要なファイルの数、AppliedItemCount はこれまでに同期されたファイルの数、PerItemErrorCount は同期が失敗しているファイルの数 (処理方法については後で説明します) を示します。

```
Replica Sync Progress. 
ServerEndpointName: <CI>sename</CI>, SyncGroupName: <CI>sgname</CI>, ReplicaName: <CI>rname</CI>, 
SyncDirection: Upload, CorrelationId: {AB4BA07D-5B5C-461D-AAE6-4ED724762B65}. 
AppliedItemCount: 172473, TotalItemCount: 624196. AppliedBytes: 51473711577, 
TotalBytes: 293363829906. 
AreTotalCountsFinal: true. 
PerItemErrorCount: 1006.
```
---

### <a name="how-do-i-know-if-my-servers-are-in-sync-with-each-other"></a>自分のサーバーが互いに同期されているかどうかを確認するにはどうすればよいですか。
# <a name="portaltabportal1"></a>[ポータル](#tab/portal1)
特定の同期グループ内の各サーバーについて、以下を確認します。
- アップロードとダウンロードの両方で、[最後に試行された同期] のタイムスタンプが最新であること。
- アップロードとダウンロードの両方で、状態が緑色であること。
- [同期アクティビティ] フィールドに表示された残りの同期ファイル数が非常に少ないか、0 であること。
- アップロードとダウンロードの両方で、[Files Not Syncing]\(同期していないファイル数\) フィールドが 0 であること。

# <a name="servertabserver"></a>[サーバー](#tab/server)
完了した同期セッションを調べます。これは、各サーバーのテレメトリ イベント ログ (イベント ビューアーで `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry` に移動) で 9102 イベントとマークされています。 

1. 特定のサーバー上で、アップロード セッションとダウンロード セッションが正常に完了したことを確認します。 そのためには、アップロードとダウンロードの両方で HResult と PerItemErrorCount が 0 であることを確認します (SyncDirection フィールドは、そのセッションがアップロード セッションとダウンロード セッションのどちらであるかを示します)。 最近完了した同期セッションが表示されていない場合は、同期セッションが現在進行中である可能性があります。この状況は、大量のデータを追加または変更した直後に発生することがあります。
2. サーバーがクラウドに対して完全に最新の状態であり、どちらの方向にも同期が必要な変更がない場合は、空の同期セッションが表示されます。 これは、すべての Sync* フィールド (SyncFileCount、SyncDirCount、SyncTombstoneCount、および SyncSizeBytes) が 0 である (同期する対象がなかったことを意味する) アップロード イベントおよびダウンロード イベントによって示されます。チャーンが高いサーバーでは、同期する新しい対象が常にあるため、このような空の同期セッションは発生しません。同期アクティビティがない場合は、30 分ごとに発生します。 
3. すべてのサーバーがクラウドに対して最新の状態である、つまり、最近のアップロード セッションとダウンロード セッションが空の同期セッションである場合は、システム全体が同期されていると合理的な確実性を持って言うことができます。 
    
Azure ファイル共有内で直接変更を加えた場合、Azure File Sync は変更列挙が実行されるまでこの変更を検出しません。変更列挙は 24 時間ごとに実行されます。 サーバーがクラウドに対して最新の状態であると示していても、実際には、Azure ファイル共有内で直接加えられた最近の変更を検出していないこともあり得ます。 

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>同期していない特定のファイルやフォルダーがあるかどうかを確認するにはどうすればよいですか。
特定の同期セッションに対して、サーバーの PerItemErrorCount またはポータルの [Files Not Syncing]\(同期していないファイル数\) の数が 0 より大きい場合は、何らかの項目の同期が失敗していることを意味します。ファイルやフォルダーに、同期を妨げる特性がある可能性があります。 これらの特性は、永続的で、同期を再開するためには明示的なアクション (たとえば、ファイル名やフォルダー名からサポートされていない文字を削除するなど) が必要な場合があります。 また、特性は一時的であり、ファイルやフォルダーの同期が自動的に再開される場合もあります。たとえば、開くハンドルを含むファイルは、ファイルが閉じられたときに自動的に同期を再開します。 Azure File Sync エンジンがそのような問題を検出すると、エラー ログが生成されます。これを解析して、現在正しく同期していない項目を一覧表示できます。

これらのエラーを確認するには、**FileSyncErrorsReport.ps1** PowerShell スクリプト (Azure File Sync エージェントのエージェント インストール ディレクトリ内にあります) を実行して、開くハンドルやサポートされていない文字などの問題によって同期が失敗したファイルを識別します。 ItemPath フィールドは、ルート同期ディレクトリを基準としたファイルの相対的な場所を示します。 修復の手順については、下に示す一般的な同期エラーの一覧を参照してください。

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>ファイル/ディレクトリ単位の同期エラーのトラブルシューティング
**ItemResults log - 項目単位の同期エラー**  
| HRESULT | HRESULT (10 進値) | エラー文字列 | 問題 | 修復 |
|---------|-------------------|--------------|-------|-------------|
| 0x80c80065 | -2134376347 | ECS_E_DATA_TRANSFER_BLOCKED | 同期中にファイルで永続的なエラーが発生しました。ファイルの同期は 1 日に 1 回しか試行されません。 基になっているエラーは、前のイベント ログで確認できます。 | エージェント R2 (2.0) 以降では、このエラーではなく元のエラーが表示されます。 最新のエージェントにアップグレードして基になっているエラーを確認するか、前のイベント ログを参照して元のエラーの原因を調べます。 |
| 0x7b | 123 | ERROR_INVALID_NAME | ファイルまたはディレクトリの名前が無効です。 | 問題のファイルまたはディレクトリの名前を変更します。 [Azure のファイル命名ガイドライン](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)と、下に示すサポートされていない文字の一覧を参照してください。 |
| 0x8007007b | -2147024773 | STIERR_INVALID_DEVICE_NAME | ファイルまたはディレクトリの名前が無効です。 | 問題のファイルまたはディレクトリの名前を変更します。 [Azure のファイル命名ガイドライン](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)と、下に示すサポートされていない文字の一覧を参照してください。 |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | ファイルが変更されましたが、まだ同期によって変更が検出されていません。この変更が検出された後に同期が復旧します。 | 必要なアクションはありません。 |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | ファイルは使用中のため、同期できません。 ファイルは使用されなくなると同期されます。 | 必要なアクションはありません。 Azure File Sync は、1 日 1 回サーバー上で一時 VSS スナップショットを作成して、開くハンドルを含むファイルを同期します。 |
| 0x20 | 32 | ERROR_SHARING_VIOLATION | ファイルは使用中のため、同期できません。 ファイルは使用されなくなると同期されます。 | 必要なアクションはありません。 |
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | 依存フォルダーがまだ同期されていないため、ファイルまたはディレクトリの変更を同期できません。 この項目は、依存する変更が同期された後に同期されます。 | 必要なアクションはありません。 |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | 同期中にファイルが変更されたため、再度同期する必要があります。 | 必要なアクションはありません。 |

#### <a name="handling-unsupported-characters"></a>サポートされていない文字の処理
**FileSyncErrorsReport.ps1** PowerShell スクリプトで、サポートされていない文字が原因のエラー (エラー コード 0x7b および 0x8007007b) が示されている場合は、それぞれのファイルで問題のある文字を削除したり名前変更したりする必要があります。 これらの文字の大部分には標準のビジュアル エンコードがないため、PowerShell はこれらの文字を疑問符または空の四角形として出力します。

下の表に、Azure File Sync でまだサポートされていない Unicode 文字をすべて示します。

| 文字セット | 文字数 |
|---------------|-----------------|
| <ul><li>0x0000009D (osc オペレーティング システム コマンド)</li><li>0x00000090 (dcs デバイス コントロール文字列)</li><li>0x0000008F (ss3 シングル シフト 3)</li><li>0x00000081 (ハイ オクテット プリセット)</li><li>0x0000007F (del 削除)</li><li>0x0000008D (ri 逆改行)</li></ul> | 6 |
| <ul><li>0x0000200F (右から左にマーク)</li><li>0x0000200E (左から右にマーク)</li><li>0x0000202E (右から左にオーバーライド)</li><li>0x0000202D (左から右にオーバーライド)</li><li>0x0000202C (文字方向の変更を終了)</li><li>0x0000202B (右から左に埋め込み)</li><li>0x0000202A (左から右に埋め込み)</li></ul> | 7 |
| 0x0000FDD0 - 0x0000FDEF (アラビア文字表示形 - a) | 32 |
| 0x0000FFF0 - 0x0000FFFF (特殊文字) | 16 |
| <ul><li>0x0001FFFE - 0x0001FFFF = 2 (非文字)</li><li>0x0002FFFE - 0x0002FFFF = 2 (非文字)</li><li>0x0003FFFE - 0x0003FFFF = 2 (非文字)</li><li>0x0004FFFE - 0x0004FFFF = 2 (非文字)</li><li>0x0005FFFE - 0x0005FFFF = 2 (非文字)</li><li>0x0006FFFE - 0x0006FFFF = 2 (非文字)</li><li>0x0007FFFE - 0x0007FFFF = 2 (非文字)</li><li>0x0008FFFE - 0x0008FFFF = 2 (非文字)</li><li>0x0009FFFE - 0x0009FFFF = 2 (非文字)</li><li>0x000AFFFE - 0x000AFFFF = 2 (非文字)</li><li>0x000BFFFE - 0x000BFFFF = 2 (非文字)</li><li>0x000CFFFE - 0x000CFFFF = 2 (非文字)</li><li>0x000DFFFE - 0x000DFFFF = 2 (非文字)</li><li>0x000EFFFE - 0x000EFFFF = 2 (未定義)</li><li>0x000FFFFE - 0x000FFFFF = 2 (補助私用領域)</li></ul> | 30 |
| 0x0010FFFE、0x0010FFFF | 2 |

### <a name="common-sync-errors"></a>一般的な同期エラー
<a id="-2147023673"></a>**同期セッションが取り消されました。**  
| | |
|-|-|
| **HRESULT** | 0x800704c7 |
| **HRESULT (10 進値)** | -2147023673 | 
| **エラー文字列** | ERROR_CANCELLED |
| **修復が必要か** | いいえ  |

同期セッションは、サーバーの再起動や更新、VSS スナップショットなど、さまざまな理由によって失敗することがあります。このエラーはフォローアップが必要なように見えますが、数時間にわたって続かない限り無視してかまいません。

<a id="-2147012889"></a>**サービスとの接続を確立できませんでした。**    
| | |
|-|-|
| **HRESULT** | 0x80072ee7 |
| **HRESULT (10 進値)** | -2147012889 | 
| **エラー文字列** | WININET_E_NAME_NOT_RESOLVED |
| **修復が必要か** | [はい] |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134376372"></a>**ユーザーの要求がサービスによってスロットルされました。**  
| | |
|-|-|
| **HRESULT** | 0x80c8004c |
| **HRESULT (10 進値)** | -2134376372 |
| **エラー文字列** | ECS_E_USER_REQUEST_THROTTLED |
| **修復が必要か** | いいえ  |

必要なアクションはありません。サーバーは再試行します。 数時間を超えてこのエラーが続く場合は、サポート要求を作成してください。

<a id="-2134364065"></a>**Sync が、クラウド エンドポイントで指定された Azure ファイル共有にアクセスできません。**  
| | |
|-|-|
| **HRESULT** | 0x80c8305f |
| **HRESULT (10 進値)** | -2134364065 |
| **エラー文字列** | ECS_E_CANNOT_ACCESS_EXTERNAL_STORAGE_ACCOUNT |
| **修復が必要か** | [はい] |

このエラーは、Azure File Sync エージェントが Azure ファイル共有にアクセスできないために発生します。原因としては、Azure ファイル共有またはそれをホストしているストレージ アカウントが存在しなくなったことが考えられます。 次の手順を実行すると、このエラーを解決できます。

1. [ストレージ アカウントが存在することを確認します。](#troubleshoot-storage-account)
2. [ストレージ アカウントにネットワーク ルールが含まれていないことを確認します。](#troubleshoot-network-rules)
3. [Azure ファイル共有が存在することを確認します。](#troubleshoot-azure-file-share)
4. [Azure File Sync がストレージ アカウントへのアクセス権を持っていることを確認します。](#troubleshoot-rbac)

<a id="-2134364064"></a><a id="cannot-resolve-storage"></a>**使用されているストレージ アカウント名を解決できませんでした。**  
| | |
|-|-|
| **HRESULT** | 0x80C83060 |
| **HRESULT (10 進値)** | -2134364064 |
| **エラー文字列** | ECS_E_STORAGE_ACCOUNT_NAME_UNRESOLVED |
| **修復が必要か** | [はい] |

1. サーバーからストレージの DNS 名を解決できることを確認します。

    ```PowerShell
    Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 443
    ```
2. [ストレージ アカウントが存在することを確認します。](#troubleshoot-storage-account)
3. [ストレージ アカウントにネットワーク ルールが含まれていないことを確認します。](#troubleshoot-network-rules)

<a id="-1906441138"></a>**同期データベースの問題により、同期が失敗しました。**  
| | |
|-|-|
| **HRESULT** | 0x8e5e044e |
| **HRESULT (10 進値)** | -1906441138 |
| **エラー文字列** | JET_errWriteConflict |
| **修復が必要か** | [はい] |

このエラーは、Azure File Sync で使用される内部データベースに問題がある場合に発生します。この問題が発生した場合は、サポート要求を作成してください。問題解決のために Microsoft からご連絡を差し上げます。

<a id="-2134351810"></a>**Azure ファイル共有ストレージの上限に達しました。**  
| | |
|-|-|
| **HRESULT** | 0x80c8603e |
| **HRESULT (10 進値)** | -2134351810 |
| **エラー文字列** | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED |
| **修復が必要か** | [はい] |

このエラーは、Azure ファイル共有ストレージの上限に達したときに発生します。Azure ファイル共有にクォータが適用されている場合や、使用量が Azure ファイル共有の制限を超えた場合に発生する可能性があります。 詳細については、[Azure ファイル共有の現在の制限](storage-files-scale-targets.md)に関する記事を参照してください。

1. ストレージ同期サービス内で同期グループに移動します。
2. 同期グループ内でクラウド エンドポイントを選択します。
3. 開いているウィンドウ内の Azure ファイル共有名をメモします。
4. リンクされているストレージ アカウントを選択します。 このリンクが失敗する場合は、参照されているストレージ アカウントが削除されています。

    ![ストレージ アカウントへのリンクが表示されたクラウド エンドポイントの詳細ウィンドウを示すスクリーンショット](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

5. **[ファイル]** を選択して、ファイル共有の一覧を表示します。
6. クラウド エンドポイントによって参照されている Azure ファイル共有の行の末尾にある 3 つの点をクリックします。
7. **[クォータ]** の下に **[使用量]** があることを確認します。 このクォータは、代替クォータが指定されていない限り [Azure ファイル共有の最大サイズ](storage-files-scale-targets.md)と同じになります。

    ![Azure ファイル共有のプロパティのスクリーンショット](media/storage-sync-files-troubleshoot/file-share-limit-reached-1.png)

共有がいっぱいでクォータが設定されていない場合、この問題を解決する 1 つの方法は、現在のサーバー エンドポイントの各サブフォルダーを、個別の専用の同期グループに含まれている専用のサーバー エンドポイント内に配置することです。 これにより、各サブフォルダーが個々の Azure ファイル共有に同期されるようになります。

<a id="-2134351824"></a>**Azure ファイル共有が見つかりません。**  
| | |
|-|-|
| **HRESULT** | 0x80c86030 |
| **HRESULT (10 進値)** | -2134351824 |
| **エラー文字列** | ECS_E_AZURE_FILE_SHARE_NOT_FOUND |
| **修復が必要か** | [はい] |

このエラーは、Azure ファイル共有にアクセスできない場合に発生します。 トラブルシューティング方法は次のとおりです。

1. [ストレージ アカウントが存在することを確認します。](#troubleshoot-storage-account)
2. [Azure ファイル共有が存在することを確認します。](#troubleshoot-azure-file-share)

Azure ファイル共有が削除されている場合は、新しいファイル共有を作成してから同期グループを再作成する必要があります。 

<a id="-2134364042"></a>**この Azure サブスクリプションが中断されている間は同期が一時停止されます。**  
| | |
|-|-|
| **HRESULT** | 0x80C83076 |
| **HRESULT (10 進値)** | -2134364042 |
| **エラー文字列** | ECS_E_SYNC_BLOCKED_ON_SUSPENDED_SUBSCRIPTION |
| **修復が必要か** | [はい] |

このエラーは、Azure サブスクリプションが中断されている場合に発生します。 Azure サブスクリプションが復元されると、同期は再度有効になります。 詳細については、「[私の Azure サブスクリプションが無効になっています。その理由と、再度有効にする方法を教えてください。](../../billing/billing-subscription-become-disable.md)」を参照してください。

<a id="-2134364052"></a>**ストレージ アカウントにファイアウォールまたは仮想ネットワークが構成されています。**  
| | |
|-|-|
| **HRESULT** | 0x80c8306c |
| **HRESULT (10 進値)** | -2134364052 |
| **エラー文字列** | ECS_E_MGMT_STORAGEACLSNOTSUPPORTED |
| **修復が必要か** | [はい] |

このエラーは、ストレージ アカウントにファイアウォールがあるか、またはストレージ アカウントが仮想ネットワークに属しているという理由により、Azure ファイル共有にアクセスできない場合に発生します。 Azure File Sync では、この機能がまだサポートされていません。 トラブルシューティング方法は次のとおりです。

1. [ストレージ アカウントが存在することを確認します。](#troubleshoot-storage-account)
2. [ストレージ アカウントにネットワーク ルールが含まれていないことを確認します。](#troubleshoot-network-rules)

この問題を解決するには、これらのルールを削除します。 

<a id="-2134375911"></a>**同期データベースの問題により、同期が失敗しました。**  
| | |
|-|-|
| **HRESULT** | 0x80c80219 |
| **HRESULT (10 進値)** | -2134375911 |
| **エラー文字列** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **修復が必要か** | いいえ  |

このエラーは通常、自動的に解決されます。これは次のような場合に発生する可能性があります。

* 同期グループ内のサーバー全体でファイルの変更数が多い。
* 個々のファイルやディレクトリでのエラーの数が多い。

このエラーが数時間を超えて続く場合は、サポート要求を作成してください。問題解決のために Microsoft からご連絡を差し上げます。

<a id="-2146762487"></a>**サーバーはセキュリティで保護された接続を確立できませんでした。クラウド サービスが予期しない証明書を受信しました。**  
| | |
|-|-|
| **HRESULT** | 0x800b0109 |
| **HRESULT (10 進値)** | -2146762487 |
| **エラー文字列** | CERT_E_UNTRUSTEDROOT |
| **修復が必要か** | [はい] |

このエラーは、組織が SSL 終了のプロキシを使用している場合、または悪意のあるエンティティがサーバーと Azure File Sync サービス間のトラフィックを傍受している場合に発生する可能性があります。 (組織が SSL 終了のプロキシを使用していることから) これに相当すると確信できる場合は、レジストリのオーバーライドによって証明書検証をスキップします。

1. SkipVerifyingPinnedRootCertificate レジストリ値を作成します。

    ```PowerShell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. 登録済みサーバー上で同期サービスを再起動します。

    ```PowerShell
    Restart-Service -Name FileSyncSvc -Force
    ```

このレジストリ値を設定すると、Azure File Sync エージェントは、サーバーとクラウド サービス間でデータを転送するときに、ローカルに信頼される SSL 証明書をすべて受け入れるようになります。

<a id="-2147012894"></a>**サービスとの接続を確立できませんでした。**  
| | |
|-|-|
| **HRESULT** | 0x80072ee2 |
| **HRESULT (10 進値)** | -2147012894 |
| **エラー文字列** | WININET_E_TIMEOUT |
| **修復が必要か** | [はい] |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134375680"></a>**認証の問題により、同期が失敗しました。**  
| | |
|-|-|
| **HRESULT** | 0x80c80300 |
| **HRESULT (10 進値)** | -2134375680 |
| **エラー文字列** | ECS_E_SERVER_CREDENTIAL_NEEDED |
| **修復が必要か** | [はい] |

このエラーは一般に、サーバーの時刻が正しくないか、認証に使用される証明書が期限切れであるとことが原因で発生します。 サーバーの時刻が正しい場合は、次の手順を実行して期限切れの証明書を削除し (期限切れである場合)、サーバーの登録状態をリセットします。

1. [証明書] MMC スナップインを開き、[コンピューター アカウント] を選択して、[証明書 (ローカル コンピューター)]\[個人]\[証明書] に移動します。
2. 期限切れの場合はクライアント認証証明書を削除し、[証明書] MMC スナップインを閉じます。
3. Regedit を開き、レジストリ内の ServerSetting キー (HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync\ServerSetting) を削除します。
4. サーバーで、次の PowerShell コマンドを実行します。

    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Reset-StorageSyncServer
    ```

5. ServerRegistration.exe (既定の場所は C:\Program Files\Azure\StorageSyncAgent) を実行して、サーバーを再登録します。

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**サーバー エンドポイントが配置されているボリュームのディスク領域が少なくなっています。**  
| | |
|-|-|
| **HRESULT** | 0x8e5e0211 |
| **HRESULT (10 進値)** | -1906441711 |
| **エラー文字列** | JET_errLogDiskFull |
| **修復が必要か** | [はい] |
| | |
| **HRESULT** | 0x80c8031a |
| **HRESULT (10 進値)** | -2134375654 |
| **エラー文字列** | ECS_E_NOT_ENOUGH_LOCAL_STORAGE |
| **修復が必要か** | [はい] |

このエラーは、ボリュームがいっぱいになったために発生します。 このエラーは一般に、サーバー エンドポイントの外部のファイルによってボリューム上の領域が使い果たされていることが原因で発生します。 サーバー エンドポイントを追加するか、別のボリュームにファイルを移動するか、またはサーバー エンドポイントが配置されているボリュームのサイズを大きくして、ボリューム上の領域を解放します。

<a id="-2134364145"></a><a id="replica-not-ready"></a>**サービスは、このサーバー エンドポイントと同期する準備がまだできていません。**  
| | |
|-|-|
| **HRESULT** | 0x80c8300f |
| **HRESULT (10 進値)** | -2134364145 |
| **エラー文字列** | ECS_E_REPLICA_NOT_READY |
| **修復が必要か** | いいえ  |

このエラーは、Azure ファイル共有上で直接変更が行われ、変更の検出が進行中であることが原因で発生します。 変更の検出が完了すると、同期が開始されます。

[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**多数の個別ファイルに問題があるため同期が失敗しました。**  
| | |
|-|-|
| **HRESULT** | 0x80c8023b |
| **HRESULT (10 進値)** | -2134364145 |
| **エラー文字列** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **修復が必要か** | [はい] |
| | |
| **HRESULT** | 0x80c8021c |
| **HRESULT (10 進値)** | -2134375908 |
| **エラー文字列** | ECS_E_SYNC_METADATA_KNOWLEGE_LIMIT_REACHED |
| **修復が必要か** | [はい] |
| | |
| **HRESULT** | 0x80c80253 |
| **HRESULT (10 進値)** | -2134375853 |
| **エラー文字列** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **修復が必要か** | [はい] |

ファイル単位の同期エラーが多数あると、同期セッションが失敗し始める可能性があります。 この状態のトラブルシューティング方法については、「[ファイル/ディレクトリ単位の同期エラーのトラブルシューティング](#troubleshooting-per-file-directory-sync-errors)」を参照してください。

> [!NOTE]
> Azure File Sync は、1 日 1 回サーバー上で一時 VSS スナップショットを作成して、開くハンドルを含むファイルを同期します。

<a id="-2134376423"></a>**サーバー エンドポイントのパスの問題により、同期が失敗しました。**  
| | |
|-|-|
| **HRESULT** | 0x80c80019 |
| **HRESULT (10 進値)** | -2134376423 |
| **エラー文字列** | ECS_E_SYNC_INVALID_PATH |
| **修復が必要か** | [はい] |

パスが存在し、ローカル NTFS ボリューム上にあって、再解析ポイントや既存のサーバー エンドポイントにはなっていないことを確認します。

<a id="-2134376373"></a>**サービスは現在使用できません。**  
| | |
|-|-|
| **HRESULT** | 0x80c8004b |
| **HRESULT (10 進値)** | -2134376373 |
| **エラー文字列** | ECS_E_SERVICE_UNAVAILABLE |
| **修復が必要か** | いいえ  |

このエラーは、Azure File Sync サービスを使用できないことが原因で発生します。 このエラーは、Azure File Sync サービスが再度使用可能になると自動的に解決します。

<a id="-2134375922"></a>**同期データベースの一時的な問題により、同期が失敗しました。**  
| | |
|-|-|
| **HRESULT** | 0x80c8020e |
| **HRESULT (10 進値)** | -2134375922 |
| **エラー文字列** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **修復が必要か** | いいえ  |

このエラーは、同期データベースに関する内部的な問題が原因で発生します。 このエラーは、Azure File Sync が同期を再試行すると自動的に解決されます。 このエラーが長期間にわたって続く場合は、サポート要求を作成してください。問題解決のために Microsoft からご連絡を差し上げます。

### <a name="common-troubleshooting-steps"></a>一般的なトラブルシューティング手順
<a id="troubleshoot-storage-account"></a>**ストレージ アカウントが存在することを確認します。**  
# <a name="portaltabportal"></a>[ポータル](#tab/portal)
1. ストレージ同期サービス内で同期グループに移動します。
2. 同期グループ内でクラウド エンドポイントを選択します。
3. 開いているウィンドウ内の Azure ファイル共有名をメモします。
4. リンクされているストレージ アカウントを選択します。 このリンクが失敗する場合は、参照されているストレージ アカウントが削除されています。
    ![ストレージ アカウントへのリンクが表示されたクラウド エンドポイントの詳細ウィンドウを示すスクリーンショット](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
```PowerShell
# Variables for you to populate based on your configuration
$agentPath = "C:\Program Files\Azure\StorageSyncAgent"
$region = "<Az_Region>"
$resourceGroup = "<RG_Name>"
$syncService = "<storage-sync-service>"
$syncGroup = "<sync-group>"

# Import the Azure File Sync management cmdlets
Import-Module "$agentPath\StorageSync.Management.PowerShell.Cmdlets.dll"

# Log into the Azure account and put the returned account information
# in a reference variable.
$acctInfo = Connect-AzureRmAccount

# this variable stores your subscription ID 
# get the subscription ID by logging onto the Azure portal
$subID = $acctInfo.Context.Subscription.Id

# this variable holds your Azure Active Directory tenant ID
# use Login-AzureRMAccount to get the ID from that context
$tenantID = $acctInfo.Context.Tenant.Id

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = [System.String[]]@()
Get-AzureRmLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the " + `
        " selected Azure Region or the region is mistyped.")
}

# Check to ensure resource group exists and create it if doesn't
$resourceGroups = [System.String[]]@()
Get-AzureRmResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    throw [System.Exception]::new("The provided resource group $resourceGroup does not exist.")
}

# the following command creates an AFS context 
# it enables subsequent AFS cmdlets to be executed with minimal 
# repetition of parameters or separate authentication 
Login-AzureRmStorageSync `
    –SubscriptionId $subID `
    -ResourceGroupName $resourceGroup `
    -TenantId $tenantID `
    -Location $region

# Check to make sure the provided Storage Sync Service
# exists.
$syncServices = [System.String[]]@()

Get-AzureRmStorageSyncService -ResourceGroupName $resourceGroup | ForEach-Object {
    $syncServices += $_.DisplayName
}

if ($storageSyncServices -notcontains $syncService) {
    throw [System.Exception]::new("The provided Storage Sync Service $syncService does not exist.")
}

# Check to make sure the provided Sync Group exists
$syncGroups = [System.String[]]@()

Get-AzureRmStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $syncService | ForEach-Object {
    $syncGroups += $_.DisplayName
}

if ($syncGroups -notcontains $syncGroup) {
    throw [System.Exception]::new("The provided sync group $syncGroup does not exist.")
}

# Get reference to cloud endpoint
$cloudEndpoint = Get-AzureRmStorageSyncCloudEndpoint `
    -ResourceGroupName $resourceGroup `
    -StorageSyncServiceName $storageSyncService `
    -SyncGroupName $syncGroup

# Get reference to storage account
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup | Where-Object { 
    $_.Id -eq $cloudEndpoint.StorageAccountResourceId
}

if ($storageAccount -eq $null) {
    Write-Host "The storage account referenced in the cloud endpoint does not exist."
}
```
---

<a id="troubleshoot-network-rules"></a>**ストレージ アカウントにネットワーク ルールが含まれていないことを確認します。**  
# <a name="portaltabportal"></a>[ポータル](#tab/portal)
1. ストレージ アカウントにサインインし、ストレージ アカウントの左側にある **[Firewalls and virtual networks]\(ファイアウォールと仮想ネットワーク\)** を選択します。
2. ストレージ アカウント内で、**[Allow access from all networks]\(すべてのネットワークからのアクセスを許可する\)** オプション ボタンがオンになっている必要があります。
    ![ストレージ アカウントのファイアウォールとネットワーク ルールが無効になっていることを示すスクリーンショット](media/storage-sync-files-troubleshoot/file-share-inaccessible-2.png)

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
```PowerShell
if ($storageAccount.NetworkRuleSet.DefaultAction -ne 
    [Microsoft.Azure.Commands.Management.Storage.Models.PSNetWorkRuleDefaultActionEnum]::Allow) {
    Write-Host ("The storage account referenced contains network " + `
        "rules which are not currently supported by Azure File Sync.")
}
```
---

<a id="troubleshoot-azure-file-share"></a>**Azure ファイル共有が存在することを確認します。**  
# <a name="portaltabportal"></a>[ポータル](#tab/portal)
1. 左側の目次で **[概要]** をクリックして、ストレージ アカウントのメイン ページに戻ります。
2. **[ファイル]** を選択して、ファイル共有の一覧を表示します。
3. クラウド エンドポイントによって参照されているファイル共有 (上の手順 1 でメモしたもの) がファイル共有の一覧に表示されていることを確認します。

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
```PowerShell
$fileShare = Get-AzureStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $cloudEndpoint.StorageAccountShareName -and
    $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    Write-Host "The Azure file share referenced by the cloud endpoint does not exist"
}
```
---

<a id="troubleshoot-rbac"></a>**Azure File Sync がストレージ アカウントへのアクセス権を持っていることを確認します。**  
# <a name="portaltabportal"></a>[ポータル](#tab/portal)
1. 左側の目次で **[アクセス制御 (IAM)]** をクリックして、ストレージ アカウントへのアクセス権を持つユーザーとアプリケーション (*サービス プリンシパル*) の一覧に移動します。
2. 一覧に、**[Hybrid File Sync Service]\(ハイブリッド ファイル同期サービス\)** が **[Reader and Data Access]\(閲覧者とデータ アクセス\)** ロールで表示されていることを確認します。 

    ![ストレージ アカウントのアクセス制御タブに表示された Hybrid File Sync Service サービス プリンシパルのスクリーンショット](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    **[Hybrid File Sync Service]\(ハイブリッド ファイル同期サービス\)** が一覧に表示されない場合は、次の手順を実行します。

    - **[追加]** をクリックします。
    - **[ロール]** フィールドで、**[閲覧者とデータ アクセス]** を選択します。
    - **[選択]** フィールドに「**Hybrid File Sync Service**」と入力してロールを選択し、**[保存]** をクリックします。

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
```PowerShell    
$foundSyncPrincipal = $false
Get-AzureRmRoleAssignment -Scope $storageAccount.Id | ForEach-Object { 
    if ($_.DisplayName -eq "Hybrid File Sync Service") {
        $foundSyncPrincipal = $true
        if ($_.RoleDefinitionName -ne "Reader and Data Access") {
            Write-Host ("The storage account has the Azure File Sync " + `
                "service principal authorized to do something other than access the data " + `
                "within the referenced Azure file share.")
        }

        break
    }
}

if (!$foundSyncPrincipal) {
    Write-Host ("The storage account does not have the Azure File Sync " + `
                "service principal authorized to access the data within the " + ` 
                "referenced Azure file share.")
}
```
---

### <a name="how-do-i-prevent-users-from-creating-files-containing-unsupported-characters-on-the-server"></a>ユーザーがサーバー上でサポートされていない文字を含むファイルを作成できないようにするにはどうすればよいですか。
[ファイル サーバー リソース マネージャー (FSRM) のファイル スクリーン](https://docs.microsoft.com/windows-server/storage/fsrm/file-screening-management)を使用すると、サーバー上でサポートされていない文字を名前に含むファイルが作成されるのをブロックできます。 サポートされていない文字の大部分は印刷不可能であるため、この作業は PowerShell を使用して行う必要があります。そのために、最初にそれらの文字の 16 進数表現を文字としてキャストする必要があります。

まず、[New-FsrmFileGroup コマンドレット](https://docs.microsoft.com/powershell/module/fileserverresourcemanager/new-fsrmfilegroup)を使用して FSRM ファイル グループを作成します。 この例では、サポートされていない文字を 2 つだけ含むグループを定義しますが、必要な数の文字をファイル グループに含めることができます。

```PowerShell
New-FsrmFileGroup -Name "Unsupported characters" -IncludePattern @(("*"+[char]0x00000090+"*"),("*"+[char]0x0000008F+"*"))
```

FSRM ファイル グループを定義したら、New-FsrmFileScreen コマンドレットを使用して、FSRM ファイル スクリーンを作成できます。

```PowerShell
New-FsrmFileScreen -Path "E:\AFSdataset" -Description "Filter unsupported characters" -IncludeGroup "Unsupported characters"
```

> [!Important]  
> ファイル スクリーンは、Azure File Sync でサポートされない文字の作成をブロックする目的でのみ使用してください。ファイル スクリーンを他のシナリオで使用すると、同期は Azure ファイル共有からサーバーへのダウンロードを継続的に試行し、ファイル スクリーンによってブロックされます。その結果、データ エグレス値が高くなります。 

## <a name="cloud-tiering"></a>クラウドの階層化 
クラウドの階層化の障害パスは 2 つあります。

- ファイルを階層化できないことがあります。これは、Azure File Sync がファイルを Azure Files に階層化しようとして失敗することを意味します。
- ファイルを再呼び出しできないことがあります。これは、ユーザーが階層化されたファイルにアクセスしようとしたときに、Azure File Sync ファイル システム フィルター (StorageSync.sys) がデータのダウンロードに失敗することを意味します。

いずれかの障害パスで発生する可能性がある障害には 2 つの主要なクラスがあります。

- クラウド ストレージの障害
    - "*一時的なストレージ サービスの可用性の問題*"。 詳しくは、[Azure Storage のサービス レベル アグリーメント (SLA)](https://azure.microsoft.com/support/legal/sla/storage/v1_2/) に関するページをご覧ください。
    - "*アクセスできない Azure ファイル共有*"。 この障害は通常、Azure ファイル共有がまだ同期グループ内のクラウド エンドポイントであるときにこれを削除した場合に発生します。
    - "*アクセスできないストレージ アカウント*"。 この障害は通常、同期グループ内のクラウド エンドポイントである Azure ファイル共有がまだあるときにストレージ アカウントを削除した場合に発生します。 
- サーバーの障害 
    - "*Azure File Sync ファイル システム フィルター (StorageSync.sys) が読み込まれていない*"。 階層化/再呼び出し要求に応答するためには、Azure File Sync ファイル システム フィルターが読み込まれている必要があります。 フィルターが読み込まれない理由はいくつかありますが、最も一般的な理由は、管理者が手動でアンロードしたことです。 Azure File Sync が正常に機能するためには、Azure File Sync ファイル システム フィルターが常に読み込まれている必要があります。
    - "*不足、破損、切断している再解析ポイント*"。 再解析ポイントは、ファイルの特別なデータ構造であり、次の 2 つの部分で構成されています。
        1. Azure File Sync ファイル システム フィルター (StorageSync.sys) がファイルへの IO になんらかのアクションを行う必要があることをオペレーティング システムに示す再解析タグ。 
        2. 関連付けられているクラウド エンドポイント (Azure ファイル共有) 上のファイルの URI をファイル システムがフィルターすることを示す再解析データ。 
        
        再解析ポイントが破損する最も一般的な原因は、管理者がタグまたはそのデータを変更しようとしたことです。 
    - "*ネットワーク接続性の問題*"。 ファイルを階層化または再呼び出しするには、サーバーにインターネット接続が必要です。

次の各セクションでは、クラウド階層化の問題のトラブルシューティングを行い、問題がクラウド ストレージの問題かサーバーの問題かを確認する方法を示します。

<a id="monitor-tiering-activity"></a>**サーバー上の階層化アクティビティを監視する方法**  
サーバー上の階層化アクティビティを監視するには、テレメトリ イベント ログ (イベント ビューアーの [アプリケーションとサービス]\[Microsoft]\[FileSync]\[Agent] の下) にあるイベント ID 9002、9003、9016、および 9029 を使用します。

- イベント ID 9002 は、サーバー エンドポイントの非実体化の統計を示します。 たとえば、TotalGhostedFileCount、SpaceReclaimedMB などです。

- イベント ID 9003 は、サーバー エンドポイントのエラー分布を示します。 たとえば、Total Error Count、ErrorCode などです。エラー コードごとに 1 つのイベントがログ記録されます。

- イベント ID 9016 は、ボリュームの非実体化の結果を示します。 たとえば、空き領域の割合、セッション内の非実体化されたファイルの数、非実体化が失敗したファイルの数などです。

- イベント ID 9029 は、非実体化セッションの情報を示します。 たとえば、セッション内の試行されたファイルの数、セッション内の階層化されたファイルの数、既に階層化されているファイルの数などです。

<a id="monitor-recall-activity"></a>**サーバー上の呼び戻しアクティビティを監視する方法**  
サーバー上の呼び戻しアクティビティを監視するには、テレメトリ イベント ログ (イベント ビューアーの [アプリケーションとサービス]\[Microsoft]\[FileSync]\[Agent] の下) にあるイベント ID 9005、9006、9007 を使用します。 これらのイベントは 1 時間ごとにログ記録されます。

- イベント ID 9005 は、サーバー エンドポイントの呼び戻しの信頼性を示します。 たとえば、アクセスされた一意のファイルの合計数、アクセスが失敗した一意のファイルの合計数などです。

- イベント ID 9006 は、サーバー エンドポイントの呼び戻しエラーの分布を示します。 たとえば、失敗した要求の合計数、ErrorCode などです。エラー コードごとに 1 つのイベントがログ記録されます。

- イベント ID 9007 は、サーバー エンドポイントの呼び戻しのパフォーマンスを示します。 たとえば、TotalRecallIOSize、TotalRecallTimeTaken などです。

<a id="files-fail-tiering"></a>**階層化に失敗するファイルをトラブルシューティングする**  
ファイルが Azure ファイルへの階層化に失敗する場合:

1. イベント ビューアーで、[アプリケーションとサービス]\[Microsoft]\[FileSync]\[Agent] の下にあるテレメトリ ログ、操作イベント ログ、および診断イベント ログをレビューします。 
    1. ファイルが Azure ファイル共有に存在することを確認します。

    > [!NOTE]
    > 階層化の前に、ファイルが Azure ファイル共有と同期されている必要があります。

    2. サーバーがインターネットに接続できることを確認します。 
    3. Azure File Sync フィルター ドライバー (StorageSync.sys と StorageSyncGuard.sys) が実行されていることを確認します。
        - 管理者特権でのコマンド プロンプトで、`fltmc` を実行します。 ファイル システム フィルター ドライバーの StorageSync.sys と StorageSyncGuard.sys が表示されることを確認します。

> [!NOTE]
> イベント ID 9003 は、ファイルの階層化が失敗した場合に、テレメトリ イベント ログに 1 時間に 1 回ログ記録されます (エラー コードごとに 1 つのイベントがログ記録されます)。 問題を診断するために追加情報が必要な場合は、操作イベント ログと診断イベント ログを使用する必要があります。

<a id="files-fail-recall"></a>**再呼び出しに失敗するファイルをトラブルシューティングする**  
ファイルが再呼び出しに失敗する場合:
1. イベント ビューアーで、[アプリケーションとサービス]\[Microsoft]\[FileSync]\[Agent] の下にあるテレメトリ ログ、操作イベント ログ、および診断イベント ログをレビューします。
    1. ファイルが Azure ファイル共有に存在することを確認します。
    2. サーバーがインターネットに接続できることを確認します。 
    3. サービス MMC スナップインを開き、Storage 同期エージェント サービス (FileSyncSvc) が実行されていることを確認します。
    4. Azure File Sync フィルター ドライバー (StorageSync.sys と StorageSyncGuard.sys) が実行されていることを確認します。
        - 管理者特権でのコマンド プロンプトで、`fltmc` を実行します。 ファイル システム フィルター ドライバーの StorageSync.sys と StorageSyncGuard.sys が表示されることを確認します。

> [!NOTE]
> イベント ID 9006 は、ファイルの呼び戻しが失敗した場合に、テレメトリ イベント ログに 1 時間に 1 回ログ記録されます (エラー コードごとに 1 つのイベントがログ記録されます)。 問題を診断するために追加情報が必要な場合は、操作イベント ログと診断イベント ログを使用する必要があります。

<a id="files-unexpectedly-recalled"></a>**サーバーで予期せず再呼び出しされるファイルをトラブルシューティングする**  
ウイルス対策、バックアップ、および多数のファイルを読み取るその他のアプリケーションは、オフライン スキップ属性を尊重してそれらのファイルのコンテンツの読み取りをスキップする場合を除いて、意図しない再呼び出しの原因となります。 オフライン ファイルをスキップするオプションをサポートしている製品でスキップを実行すると、ウィルス対策スキャンやバックアップ ジョブなどの操作中の意図しない再呼び出しを回避できます。

オフライン ファイルの読み取りをスキップするようにソリューションを構成する方法については、ソフトウェア ベンダーにお問い合わせください。

意図しない呼び出しは、ファイル エクスプローラーでファイルを参照する場合などの他のシナリオで発生することもあります。 サーバー上でクラウド階層化されたファイルがあるフォルダーをファイル エクスプローラーで開くと、意図しない再呼び出しが発生することがあります。 ウイルス対策ソリューションがサーバーで有効になっている場合よりも、こちらのほうが発生する可能性が高まります。

## <a name="general-troubleshooting"></a>一般的なトラブルシューティング
サーバーで Azure File Sync の問題が発生する場合は、次の手順を完了します。
1. イベント ビューアーで、テレメトリ イベント ログ、操作イベント ログ、および診断イベント ログをレビューします。
    - 同期、階層化、および再呼び出しの問題は、[アプリケーションとサービス]\[Microsoft]\[FileSync]\[Agent] の下のテレメトリ イベント ログ、診断イベント ログ、および操作イベント ログに記録されます。
    - サーバーの管理 (構成設定など) に関連する問題は、Applications と Services\Microsoft\FileSync\Management の下の診断イベント ログと操作イベント ログに記録されます。
2. Azure File Sync サービスがサーバーで実行されていることを確認します。
    - サービスの MMC スナップインを開き、ストレージ同期エージェント サービス (FileSyncSvc) が実行されていることを確認します。
3. Azure File Sync フィルター ドライバー (StorageSync.sys と StorageSyncGuard.sys) が実行されていることを確認します。
    - 管理者特権でのコマンド プロンプトで、`fltmc` を実行します。 ファイル システム フィルター ドライバーの StorageSync.sys と StorageSyncGuard.sys が表示されることを確認します。

問題が解決されない場合は、AFSDiag ツールを実行します。
1. AFSDiag の出力が保存されるディレクトリを作成します (例: C:\Output)。
2. 管理者特権で PowerShell ウィンドウを開き、次のコマンドを実行します (各コマンドの後で Enter キーを押します)。

    ```PowerShell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. Azure File Sync のカーネル モード トレース レベルには、(より詳細なトレースの作成を指定しない限り) **1** を入力し、Enter キーを押します。
4. Azure File Sync のユーザー モード トレース レベルには、(より詳細なトレースの作成を指定しない限り) **1** を入力し、Enter キーを押します。
5. 問題を再現します。 操作が終了したら、**[D]** を入力します。
6. ログファイルとトレース ファイルを含む .zip ファイルが、指定した出力ディレクトリに保存されます。

## <a name="see-also"></a>関連項目
- [Azure Files についてよく寄せられる質問 (FAQ)](storage-files-faq.md)
- [Windows での Azure Files に関する問題のトラブルシューティング](storage-troubleshoot-windows-file-connection-problems.md)
- [Linux での Azure Files に関する問題のトラブルシューティング](storage-troubleshoot-linux-file-connection-problems.md)
