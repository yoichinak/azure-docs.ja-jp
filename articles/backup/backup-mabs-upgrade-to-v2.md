---
title: Azure Backup Server v2 のインストール
description: Azure Backup Server v2 には、VM、ファイルとフォルダー、ワークロードなどを保護するための高度なバックアップ機能があります。 Azure Backup Server v2 をインストールまたはアップグレードする方法を説明します。
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: adigan
ms.openlocfilehash: a458a46f3775a593f369d5acb967fc90d61efde8
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628343"
---
# <a name="install-azure-backup-server-v2"></a>Azure Backup Server v2 のインストール

Azure Backup Server では、仮想マシン (VM)、ワークロード、ファイル、フォルダーなどを保護できます。 Azure Backup Server v2 は、Azure Backup Server v1 上にビルドされ、v1 にはない新しい機能を使用できます。 v1 と v2 の機能の比較については、「[Azure Backup Server の保護マトリックス](backup-mabs-protection-matrix.md)」を参照してください。 

Backup Server v2の追加機能は Backup Server v1からのアップグレードです。 ただし、Backup Server v1 は、Backup Server v2 をインストールするための前提条件ではありません。 Backup Server v1 から v2 にアップグレードする場合は、Backup Server v2 を Backup Server 保護サーバーにインストールします。 既存の Backup Server の設定がそのまま残ります。

Backup Server v2 は、Windows Server 2016 または Windows Server 2012 R2 にインストールできます。 System Center 2016 Data Protection Manager Modern Backup Storage のような新機能を利用するには、Backup Server v2 を Windows Server 2016 にインストールする必要があります。 Backup Server v2 にアップグレードまたはこれをインストールする前に、[インストールの前提条件](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites)を確認してください。

> [!NOTE]
> Azure Backup Server には System Center Data Protection Manager と同じコード ベースがあります。 Backup Server v1 は Data Protection Manager 2012 R2 と同等です。 Backup Server v2 は Data Protection Manager 2016 と同等です。 この記事は、しばしば Data Protection Manager のドキュメントを参照します。
>
>

## <a name="upgrade-backup-server-to-v2"></a>Backup Server の v2 へのアップグレード
Backup Server v1 から Backup Server v2 にアップグレードするには、インストール環境に必要な更新プログラムが適用されていることを確認します。

- 保護サーバーで[保護エージェントを更新](backup-mabs-upgrade-to-v2.md#update-the-data-protection-manager-protection-agent)します。
- Windows Server 2012 R2 を Windows Server 2016 にアップグレードします。
- すべての実稼働サーバー上で Azure Backup Server Remote Administrator をアップグレードします。
- 実稼働サーバーを再起動せずに続行されるようにバックアップが設定されていることを確認します。


### <a name="upgrade-steps-for-backup-server-v2"></a>Backup Server v2 のアップグレード手順

1. ダウンロード センターで、[アップグレード インストーラーをダウンロード](https://go.microsoft.com/fwlink/?LinkId=626082)します。

2. セットアップ ウィザードを抽出したら、**[setup.exe を実行する]** が選択されていることを確認してから **[完了]** を選択します。

  ![セットアップ インストーラー - セットアップを実行する](./media/backup-mabs-upgrade-to-v2/run-setup.png)

3. Microsoft Azure Backup Server ウィザードの **[インストール]** で **[Microsoft Azure Backup Server]** を選択します。

  ![セットアップ インストーラー - インストールを選択する](./media/backup-mabs-upgrade-to-v2/mabs-installer-s1.png)

4. **[ようこそ]** ページで、警告を確認して、**[次へ]** を選択します。

  ![セットアップ インストーラー - [Welcome]\(ようこそ) ページ](./media/backup-mabs-upgrade-to-v2/mabs-installer-s2.png)

5. セットアップ ウィザードでは、前提条件の確認を実行し、お客様の環境をアップグレードできるかどうかを確認します。 **[Prerequisite Checks]** \(前提条件のチェック) ページで、**[Check]** \(チェック) を選択します。

  ![セットアップ インストーラー - [Prerequisite Checks]\(前提条件のチェック) ページ](./media/backup-mabs-upgrade-to-v2/mabs-installer-s3-perform-checks.png)

6. 環境が前提条件のチェックをパスする必要があります。 環境がチェックに合格しなかった場合は、問題をメモし、修正します。 **[Check Again]** \(再チェック) を選択します。 前提条件のチェックに合格してから、**[Next]** \(次へ) を選択します。

  ![セットアップ インストーラー - [Check Again]\(再チェック) ボタン](./media/backup-mabs-upgrade-to-v2/mabs-installer-s4-pass-checks.png)

7. **[SQL Settings]** \(SQL 設定) ページで、SQL のインストールに関連するオプションを選択し、**[Check and Install]** \(チェックしてインストール) を選択します。

  ![セットアップ インストーラー - [SQL Settings]\(SQL 設定) ページ](./media/backup-mabs-upgrade-to-v2/mabs-installer-s5-sql-settings.png)

  このチェックには数分間かかります。 チェックが完了したら、**[Next]** \(次へ) をクリックします。

  ![セットアップ インストーラー - [SQL Settings]\(SQL 設定) の [Check and Install]\(チェックしてインストール) ボタン](./media/backup-mabs-upgrade-to-v2/mabs-installer-s5a-check-and-fix-settings.png)

8. **[Installation Settings]** \(インストール設定) ページで、Backup Server をインストールする場所、またはスクラッチ ロケーションを変更します。 **[次へ]** を選択します。

  ![セットアップ インストーラー - [Installation Settings]\(インストール設定) ページ](./media/backup-mabs-upgrade-to-v2/mabs-installer-s6-installation-settings.png)

9. セットアップ ウィザードを終了するには、**[Finish]** \(完了) を選択します。

  ![セットアップ インストーラー - [Finish]\(完了)](./media/backup-mabs-upgrade-to-v2/run-setup.png)

## <a name="add-storage-for-modern-backup-storage"></a>Modern Backup Storage のストレージの追加

バックアップ記憶域の効率を向上させるために、Backup Server v2 はボリュームのサポートを追加しています。 Backup Server v1 と Backup Server v2 はいずれもディスクをサポートします。

### <a name="add-volumes-and-disks"></a>ボリュームとディスクの追加

Windows Server 2016 で Backup Server v2 を実行している場合、ボリュームを使用してバックアップ データを格納できます。 ボリュームを使用すると、記憶域を節約でき、バックアップが高速になります。 ボリュームは Backup Server の新機能であるため、これを追加する必要があります。 

Backup Server にボリュームを追加する場合、ボリュームにフレンドリ名を指定できます。 名前を付けるボリュームの **[Friendly Name]** \(フレンドリ名\) 列を選択します。 後で名前を変えることもできます。 PowerShell を使用して、ボリュームのフレンドリ名を追加または変更することができます。

管理者コンソールでボリュームを追加するには、次の手順を実行します。

1. Azure Backup Server の管理者コンソールで、**[Management]** \(管理) > **[Disk Storage]** \(ディスク ストレージ) > **[Add]** \(追加) の順に選択します。

  ![[ディスク ストレージの追加] ウィザードを開きます](./media//backup-mabs-upgrade-to-v2/open-add-disk-storage-wizard.png)

  [Add Disk Storage]\(ディスク ストレージの追加\) ウィザードが開きます

2. **[Add Disk Storage]** \(ディスク ストレージの追加) ページの **[Available volumes]** \(使用可能なボリューム) ボックスで、ボリュームを選択し、**[Add]** \(追加) を選択します。

3. **[Selected volumes]** \(選択したボリューム) ボックスに、ボリュームのフレンドリ名を入力し、**[OK]** を選択します。

  ![[ディスク ストレージの追加] ウィザード - ボリュームの追加](./media/backup-mabs-upgrade-to-v2/add-volume.png)

  ディスクを追加する場合は、そのディスクは従来の記憶域を持つ保護グループに属している必要があります。 これらのディスクは、このような保護グループにのみ使用できます。 Backup Server に従来の保護を持つソースない場合、ディスクは表示されません。

  ディスクの追加の詳細については、[ディスクを追加して従来の記憶域を増やす](http://docs.microsoft.com/system-center/dpm/upgrade-to-dpm-2016#adding-disks-to-increase-legacy-storage)方法に関するページを参照してください。 ディスクには、フレンドリ名を割り当てることはできません。


### <a name="assign-workloads-to-volumes"></a>ワークロードをボリュームに割り当てる

Backup Server では、どのワークロードをどのボリュームに割り当てるかを指定します。 たとえば、1 秒あたりの入出力操作 (IOPS) が多い高コストのボリュームには、大量バックアップが頻繁に必要なワークロードのみを保存するように設定できます。 例としては、トランザクション ログを含む SQL Server があります。

#### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

Backup Server の記憶域プールでボリュームのプロパティを更新するには、PowerShell コマンドレット **Update-DPMDiskStorage** を使用します。

構文:

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [<CommonParameters>]
```

PowerShell を使用して行ったすべての変更は、UI に反映されます。


## <a name="protect-data-sources"></a>データ ソースの保護
データ ソースの保護を開始するには、保護グループを作成します。 次の手順では、新しい保護グループ ウィザードに対する変更や追加が強調表示されています。

保護グループを作成する手順は次のとおりです。

1. Backup Server の管理者コンソールで、**[Protection]** \(保護) を選択します。

2. ツール リボンで、**[New]** \(新規) を選択します。

  新しい保護グループの作成ウィザードが開きます。

  ![新しい保護グループの作成ウィザード](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-1.png)

3. **[ようこそ]** ページで **[次へ]** をクリックします。

4. **[保護グループの種類の選択]** ページで、作成する保護グループの種類を選択し、**[次へ]** を選択します。

  ![[保護グループの種類の選択] ページ](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-2.png)

5. **[グループ メンバーの選択]** ページの **[利用可能なメンバー]** ボックスには、メンバーと保護エージェントの一覧が表示されます。 この例では、ボリューム D:\ と E:\, を選択し、これらを **[選択したメンバー]** ウィンドウに追加します。 **[次へ]** を選択します。

  ![[グループ メンバーの選択] ページ](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-3.png)

6. **[データの保護方法の選択]** ページで、**[保護グループ名]** に名前を入力し、保護方法を選択してから **[次へ]** を選択します。 短期的な保護の場合、**[ディスク]** バックアップ メソッドを選択します。

  ![[データ保護方法の選択] ページ](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-4.png)

7. **[短期的な目標の指定]** ページで、**[リテンション期間]** と **[同期頻度]** の詳細を選択します。 次に、**[次へ]** を選択します。 必要に応じて、復旧ポイントを取得するスケジュールを変更するために、**[変更]** を選択します。

  ![[短期的な目標の指定] ページ](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-5.png)

8. **[ディスク ストレージの割り当ての確認]** ページで、選択したデータ ソースの詳細情報 (データ ソースのサイズ、プロビジョニングする領域の値、ターゲットの記憶域ボリュームなど) を確認します。

  ![[ディスク ストレージの割り当ての確認] ページ](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-6.png)

  記憶域のボリュームは、(PowerShell を使用して設定される) ワークロードのボリュームの割り当てと使用可能記憶域に基づいています。 ドロップダウン メニューでその他のボリュームを選択して、記憶域ボリュームを変更できます。 **[ターゲット記憶域]** の値を変更する場合、**[使用可能なディスクの記憶域]** の値は動的に変化し、**[空き領域]** と **[Underprovisioned Space]** \(過小にプロビジョニングされた領域) の値を反映します。

  データ ソースが計画どおりに増えると、**[使用可能なディスクの記憶域]** の **[Underprovisioned Space]** \(過小にプロビジョニングされた領域) の値は、必要な追加記憶域の量を反映します。 この値を使用して、スムーズなバックアップに必要な記憶域のニーズの計画に役立ててください。 値が 0 の場合、近い将来に記憶域に問題が発生する可能性はありません。 値が 0 以外の数値の場合は、保護ポリシーと保護されたメンバーのデータ サイズに基づいた十分な記憶域が割り当てられていません。

  ![過少割り当てされたディスク ストレージ](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-7.png)

  保護グループの作成を終了するには、ウィザードを完了します。

## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>従来の記憶域の Modern Backup Storage への移行
Backup Server v2 にアップグレードまたはこれをインストールし、オペレーティング システムを Windows Server 2016 にアップグレードしたら、Modern Backup Storage を使用するように保護グループを更新します。 既定では、保護グループは変更されません。 初期のセットアップのとおりに機能します。 

Modern Backup Storage を使用するように保護グループを更新することはオプションです。 保護グループを更新するには、データ保持オプションを使用して、すべてのデータ ソースの保護を停止します。 次に、新しい保護グループにデータ ソースを追加します。

1. System Center 2016 DPM 管理者コンソールで、**[保護]** 機能を選択します。 **[保護グループ メンバー]** 一覧で、メンバーを右クリックし、**[Stop protection of member]** \(メンバーの保護を停止する) を選択します。

  ![メンバーの保護の停止](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. **[グループから削除]** ダイアログ ボックスで、記憶域プールの使用済みディスク領域と利用可能な空き領域を確認します。 既定では、ディスク上の復旧ポイントをそのままにし、関連付けられている保有ポリシーごとの有効期限が切れるようにします。 **[OK]** を選択します。

  空き記憶域プールに使用済みディスク領域をすぐに返す場合は、**[ディスク上のレプリカを削除]** チェック ボックスを選択し、そのメンバーに関連付けられているバックアップ データ (および復旧ポイント) を削除します。

  ![[グループ] ダイアログ ボックスから削除](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. Modern Backup Storage を使用する保護グループを作成します。 保護されていないデータ ソースが含めます。


## <a name="add-disks-to-increase-legacy-storage"></a>ディスクを追加して従来の記憶域を増やす

Backup Server で従来の記憶域を使用する場合、ディスクを追加して従来の記憶域を増やす必要があります。 

ディスク ストレージを追加するには、次の手順を実行します。

1. System Center 2016 DPM 管理者コンソールで、**[管理]** > **[ディスク記憶域]** > **[追加]** の順に選択します。

  ![[Add Disk Storage]\(ディスク ストレージの追加) ダイアログ](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

2. **[ディスク記憶域の追加]** ダイアログ ボックスで **[ディスクの追加]** を選択します。

3. 使用可能なディスクの一覧で、追加するディスクを選択します。 **[追加]**、**[OK]** の順に選択します。

## <a name="update-the-data-protection-manager-protection-agent"></a>Data Protection Manager 保護エージェントの更新

Backup Server は、System Center Data Protection Manager 保護エージェントを使用して更新します。 ネットワークに接続されていない保護エージェントをアップグレードする場合は、Data Protection Manager 管理コンソールを使用して接続エージェントのアップグレードを完了することはできません。 アクティブでないドメイン環境で保護エージェントをアップグレードする必要があります。 クライアント コンピューターがネットワークに接続されるまで、Data Protection Manager 管理者コンソールは、保護エージェントの更新が保留中であることを表示します。

次のセクションでは、接続されているクライアント コンピューターと接続されていないクライアント コンピューターの保護エージェントを更新する方法について説明します。

### <a name="update-a-protection-agent-for-a-connected-client-computer"></a>接続されているクライアント コンピューターの保護エージェントの更新

1. Backup Server の管理者コンソールで、**[Management]** \(管理) > **[Agents]** \(エージェント) の順に選択します。

2. 表示ウィンドウで、保護エージェントを更新するクライアント コンピューターを選択します。

  > [!NOTE]
  > **[Agent Updates]** \(エージェントの更新プログラム) 列は、保護されるコンピューターごとに保護エージェントの更新がいつ行われるかを示します。 **[アクション]** ウィンドウでは、**[更新する]** アイコンは、保護されるコンピューターが選択されていて、更新プログラムが利用可能なときのみ表示されます。

3. 選択したコンピューターに更新された保護エージェントをインストールするには、**[アクション]** ウィンドウで、**[更新する]** を選択します。

### <a name="update-a-protection-agent-on-a-client-computer-that-is-not-connected"></a>接続されていないクライアント コンピューターの保護エージェントの更新

1. Backup Server の管理者コンソールで、**[Management]** \(管理) > **[Agents]** \(エージェント) の順に選択します。

2. 表示ウィンドウで、保護エージェントを更新するクライアント コンピューターを選択します。

  > [!NOTE]
  > **[Agent Updates]** \(エージェントの更新プログラム) 列は、保護されるコンピューターごとに保護エージェントの更新がいつ行われるかを示します。 **[アクション]** ウィンドウでは、**[更新する]** アイコンは、保護されるコンピューターが選択されていても更新プログラムが利用できないときには使用できません。

3. 選択したコンピューターに更新された保護エージェントをインストールするには、**[更新する]** を選択します。

4. ネットワークに接続されていないクライアント コンピューターでは、コンピューターがネットワークに接続されるまで、**[エージェントの状態]** 列には **[更新保留中]** の状態が表示されます。

  クライアント コンピューターがネットワークに接続されると、クライアント コンピューターの **[エージェントの更新]** 列には、**[更新中]** の状態が表示されます。
  
### <a name="move-legacy-protection-groups-from-the-old-version-and-sync-the-new-version-with-azure"></a>従来の保護グループを以前のバージョンから移動して新しいバージョンを Azure と同期させる

Azure Backup Server と OS の両方を更新したら、Modern Backup Storage を使用して新しいデータ ソースを保護することができます。 既に保護されているデータ ソースは、Azure Backup Server (レガシ) の場合と同様に引き続き保護されます。 すべての新しい保護グループには Modern Backup Storage が使用されます。

データ ソースを従来の保護モードから Modern Backup Storage に移行するには:

1.  新しいボリュームを Data Protection Manager の記憶域プールに追加します。 フレンドリ名を割り当てて、データ ソース タグを選択することもできます。

2. レガシ モードの各データ ソースについて、そのデータ ソースの保護を停止します。 次に、**[保護されるデータを保持する]** を選択します。  こうすることで、移行後も従来の回復ポイントを回復できるようになります。

3. 新しい保護グループを作成します。 次に、新しい形式を使用して格納するデータ ソースを選択します。

  Data Protection Manager によって、従来のバックアップ ストレージのレプリカが Modern Backup Storage ボリュームのローカルに格納されます。
    > [!NOTE] 
    > コピーを作成すると、回復後の操作ジョブとして表示されます。

  すべての新しい同期と復旧ポイントは、Modern Backup Storage に格納されるようになります。

  従来の復旧ポイントは、期限切れになると削除されます。 従来の復旧ポイントが削除されると、ディスク領域は解放されます。

  すべてのレガシ ボリュームが従来のストレージから削除されたら、Azure Backup からディスクを削除できます。 次にシステムからディスクを削除することができます。

4. Data Protection Manager データベースのバックアップを作成します。

  > [!IMPORTANT]
  > - 新しいサーバー名は元の Azure Backup Server インスタンスと同じ名前にする必要があります。 以前の記憶域プールと Data Protection Manager データベースを使用して復旧ポイントを保持する場合は、新しい Azure Backup Server インスタンスの名前を変更することはできません。
  > - Data Protection Manager データベースのバックアップを用意する必要があります。 後でデータベースを復元する必要があるためです。

5. 元の Azure Backup Server インスタンスをシャットダウンするか、サーバーをオフラインにします。

6. Active Directory でマシン アカウントをリセットします。

7. 新しいマシンに Windows Server 2016 をインストールします。 サーバー名には、元の Azure Backup Server インスタンスと同じマシン名を使用します。

8. ドメインに参加します。

9. Azure Backup Server v2 をインストールします (以前のサーバーから Data Protection Manager 記憶域プール ディスクを削除し、新しいサーバーにインポートします)。

10. 手順 4 で作成した Data Protection Manager データベースを復元します。

11. 元のバックアップ サーバーの記憶域を新しいサーバーにアタッチします。

12. SQL Server で、Data Protection Manager データベースを復元します。

13. 新しいサーバー上の管理者コマンド ラインで、`cd` を使用して Microsoft Azure Backup のインストール場所と bin フォルダーに移動します。  

  例:  
  C:\windows\system32>cd "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\ to Azure Backup

14. `DPMSYNC -SYNC` を実行します。
  
  > [!NOTE]
  > 古いディスクを移動するのではなく、*新しい*ディスクを Data Protection Manager 記憶域プールに追加した場合は、`DPMSYNC -Reallocatereplica` を実行します。

## <a name="new-powershell-cmdlets-in-backup-server-v2"></a>Backup Server v2 の新しい PowerShell コマンドレット

Azure Backup Server v2 をインストールすると、次の 2 つの新しいコマンドレットを使用できます。 
* [Mount-DPMRecoveryPoint](https://technet.microsoft.com/library/mt787159.aspx)
* [Dismount-DPMRecoveryPoint](https://technet.microsoft.com/library/mt787158.aspx)

## <a name="next-steps"></a>次の手順

サーバーを準備する方法、またはワークロードの保護を開始する方法を説明します。
- [Backup Server ワークロードの準備](backup-azure-microsoft-azure-backup.md)
- [Backup Server を使用した VMware サーバーのバックアップ](backup-azure-backup-server-vmware.md)
- [Backup Server を使用した SQL Server のバックアップ](backup-azure-sql-mabs.md)
- [Backup Server での Modern Backup Storage の使用](backup-mabs-add-storage.md)

