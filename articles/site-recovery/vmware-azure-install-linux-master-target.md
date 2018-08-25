---
title: Azure からオンプレミスへのフェールオーバーのために Linux マスター ターゲット サーバーをインストールする | Microsoft Docs
description: Linux 仮想マシンを再保護する前に、Linux マスター ターゲット サーバーが必要になります。 そのインストール方法について説明します。
author: nsoneji
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: nisoneji
ms.openlocfilehash: 4443d8fcbf40240e1a7bd718416e522192b4efb3
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2018
ms.locfileid: "37921071"
---
# <a name="install-a-linux-master-target-server"></a>Linux マスター ターゲット サーバーをインストールする
仮想マシンを Azure にフェールオーバー後、仮想マシンをオンプレミス サイトにフェールバックできます。 フェールバックするには、Azure からオンプレミス サイトへの仮想マシンを再保護する必要があります。 このプロセスには、トラフィックを受信するオンプレミス マスター ターゲット サーバーが必要です。 

保護された仮想マシンが Windows 仮想マシンである場合、Windows マスター ターゲットが必要です。 Linux 仮想マシンには、Linux マスター ターゲットが必要になります。 ここでは、Linux のマスター ターゲットを作成してインストールする方法について説明しています。

> [!IMPORTANT]
> マスター ターゲット サーバー リリース 9.10.0 以降、最新のマスター ターゲット サーバーは Ubuntu 16.04 サーバーにのみインストールできます。 CentOS6.6 に新規インストールすることはできません。 ただし、9.10.0 バージョンを使って古いマスター ターゲット サーバーのアップグレードを続けることはできます。

## <a name="overview"></a>概要
この記事では、Linux のマスター ターゲットをインストールする手順について説明します。

コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)で投稿してください。

## <a name="prerequisites"></a>前提条件

* マスター ターゲットをデプロイするホストを選択するには、フェールバックを実行する対象が既存のオンプレミス仮想マシンか、新しい仮想マシンかを決定します。 
    * 既存の仮想マシンの場合、マスター ターゲットのホストから、仮想マシンのデータ ストアにアクセスできる必要があります。
    * オンプレミスの仮想マシンが存在しない場合 (別の場所への復旧の場合)、マスター ターゲットと同じホスト上にフェールバック仮想マシンが作成されます。 マスター ターゲットは任意の ESXi ホストにインストールすることができます。
* マスター ターゲットは、プロセス サーバーおよび構成サーバーと通信できるネットワーク上に存在する必要があります。
* マスター ターゲットのバージョンは、プロセス サーバーおよび構成サーバーと同等か、それより前のバージョンにする必要があります。 たとえば、構成サーバーのバージョンが 9.4 である場合、マスター ターゲットのバージョンは 9.4 または 9.3 にすることができますが、9.5 にすることはできません。
* マスター ターゲットは、VMware 仮想マシンにのみすることができますが、物理サーバーにすることはできません。

## <a name="sizing-guidelines-for-creating-master-target-server"></a>マスター ターゲット サーバーを作成するためのサイズに関するガイドライン

次のサイズのガイドラインに従って、マスター ターゲットを作成します。
- **RAM**: 6 GB 以上
- **OS ディスク サイズ**: 100 GB 以上 (OS をインストールする場合)
- **リテンション ドライブの追加ディスク サイズ**: 1 TB
- **CPU コア数**: 4 コア以上

次の Ubuntu カーネルがサポートされています。


|カーネル シリーズ  |サポートされる最新バージョン  |
|---------|---------|
|4.4.      |4.4.0-81-generic         |
|4.8      |4.8.0-56-generic         |
|4.10     |4.10.0-24-generic        |


## <a name="deploy-the-master-target-server"></a>マスター ターゲット サーバーをデプロイする

### <a name="install-ubuntu-16042-minimal"></a>Ubuntu 16.04.2 最小構成をインストールする

次の手順で Ubuntu 16.04.2 64-bit オペレーティング システムをインストールします。

1.   [ダウンロード リンク](https://www.ubuntu.com/download/server/thank-you?version=16.04.2&architecture=amd64)に移動し、最も近いミラーを選択して、Ubuntu 16.04.2 最小構成 64 ビット ISO をダウンロードします。
Ubuntu 16.04.2 最小構成 64-bit ISO を DVD ドライブに保存し、システムを起動します。

1.  優先する言語として **[English]\(英語\)** を選択し、**Enter** キーを押します。
    
    ![言語を選択する](./media/vmware-azure-install-linux-master-target/image1.png)
1. **[Install Ubuntu Server]\(Ubuntu Server のインストール\)** を選択し、**Enter** キーを押します。

    ![[Install Ubuntu Server]\(Ubuntu Server のインストール\) を選択する](./media/vmware-azure-install-linux-master-target/image2.png)

1.  優先する言語として **[English]\(英語\)** を選択し、**Enter** キーを押します。

    ![優先する言語として [English]\(英語\) を選択する](./media/vmware-azure-install-linux-master-target/image3.png)

1. **[Time Zone]\(タイム ゾーン\)** オプション一覧から適切なオプションを選択し、**Enter** キーを押します。

    ![適切なタイム ゾーンを選択する](./media/vmware-azure-install-linux-master-target/image4.png)

1. **[No]\(いいえ\)** (既定のオプション) を選択し、**Enter** キーを押します。

     ![キーボードを構成する](./media/vmware-azure-install-linux-master-target/image5.png)
1. キーボードの製造国として **[English (US)]\(英語 (米国)\)** を選択し、**Enter** キーを押します。

1. キーボード レイアウトとして **[English (US)]\(英語 (米国)\)** を選択し、**Enter** キーを押します。

1. **[Hostname]\(ホスト名\)** ボックスにサーバーのホスト名を入力し、**[Continue]\(続行\)** を選択します。

1. ユーザー アカウントを作成するには、ユーザー名を入力し、**[Continue]\(続行\)** を選択します。

      ![ユーザー アカウントの作成](./media/vmware-azure-install-linux-master-target/image9.png)

1. 新しいユーザー アカウントのパスワードを入力し、**[Continue]\(続行\)** を選択します。

1.  新しいユーザーのパスワードを確認入力し、**[Continue]\(続行\)** を選択します。

    ![パスワードを確認入力する](./media/vmware-azure-install-linux-master-target/image11.png)

1.  ホーム ディレクトリを暗号化するための次の選択で、**[No]\(いいえ\)** (既定のオプション) を選択し、**Enter** キーを押します。

1. 表示されているタイム ゾーンが正しい場合は **[Yes]\(はい\)** (既定のオプション) を選択し、**Enter** キーを押します。 タイム ゾーンを修正するには、**[No]\(いいえ\)** を選択します。

1. パーティション分割方法オプションから **[Guided - Use entire disk]\(ガイド付き - ディスク全体を使用する\)** を選択し、**Enter** キーを押します。

     ![パーティション分割方法オプションを選択する](./media/vmware-azure-install-linux-master-target/image14.png)

1.  **[Select disk to partition]\(パーティション分割するディスクを選択してください\)** オプションで適切なディスクを選択し、**Enter** キーを押します。

    ![ディスクを選択する](./media/vmware-azure-install-linux-master-target/image15.png)

1.  **[Yes]\(はい\)** を選択して変更をディスクに書き込み、**Enter** キーを押します。

    ![既定のオプションを選択する](./media/vmware-azure-install-linux-master-target/image16-ubuntu.png)

1.  既定のオプションを選択し、**[Continue]\(続行\)** を選択し、**Enter** キーを押します。
     
     ![アップグレードの管理方法を選択する](./media/vmware-azure-install-linux-master-target/image17-ubuntu.png)

1.  システムでのアップグレード管理用の選択で **[No automatic updates]\(自動更新なし\)** オプションを選び、**Enter** キーを押します。

     ![アップグレードの管理方法を選択する](./media/vmware-azure-install-linux-master-target/image18-ubuntu.png)

    > [!WARNING]
    > Azure サイト リカバリー マスター ターゲット サーバーには特定バージョンの Ubuntu が必要なので、仮想マシンのカーネル アップグレードを無効にする必要があります。 有効になっていると、定期的なアップグレードでマスター ターゲット サーバーが正しく機能しなくなります。 **[No automatic updates]\(自動更新しない\)** オプションを選択する必要があります。

1.  既定のオプションを選択します。 openSSH を SSH 接続に使いたい場合は、**[OpenSSH server]\(OpenSSH サーバー\)** オプションを選択し、**[Continue]\(続行\)** を選択します。

    ![ソフトウェアを選択する](./media/vmware-azure-install-linux-master-target/image19-ubuntu.png)

1. GRUB ブート ローダーをインストールするための選択で **[はい]** を選び、**Enter** キーを押します。
     
    ![GRUB ブート インストーラー](./media/vmware-azure-install-linux-master-target/image20.png)


1. ブート ローダーのインストールに適したデバイス (可能なら **/dev/sda**) を選択し、**Enter** キーを押します。
     
    ![適切なデバイスを選択する](./media/vmware-azure-install-linux-master-target/image21.png)

1. **[Continue]\(続行\)** を選択し、**Enter** キーを押してインストールを完了します。

    ![インストールを完了する](./media/vmware-azure-install-linux-master-target/image22.png)

1. インストールが完了したら、新しいユーザーの資格情報で VM にサインインします (詳細については、「**手順 10**」を参照してください)。

1. 次のスクリーンショットで説明されている手順を使用して、ROOT ユーザーのパスワードを設定します。 次に ROOT ユーザーとしてサインインします。

    ![ROOT ユーザーのパスワードを設定する](./media/vmware-azure-install-linux-master-target/image23.png)


### <a name="configure-the-machine-as-a-master-target-server"></a>マスター ターゲット サーバーとしてマシンを構成する

Linux 仮想マシンの各 SCSI ハード ディスクの ID を取得するには、**disk.EnableUUID = TRUE** パラメーターを有効にする必要があります。 このパラメーターを有効にするには、次の手順を実行します。

1. 仮想マシンをシャットダウンします。

2. 左ウィンドウで仮想マシンのエントリを右クリックし、**[Edit Settings]\(設定の編集\)** を選択します。

3. **[Options]\(オプション\)** タブをクリックします。

4. 左ウィンドウで **[Advanced]\(詳細\)** > **[General]\(全般\)** を選択し、画面の右下にある **[Configuration Parameters]\(構成パラメーター\)** ボタンを選択します。

    ![構成パラメーターを開く](./media/vmware-azure-install-linux-master-target/image24-ubuntu.png) 

    **[構成パラメーター]** オプションは、マシンの実行時には使用できません。 このタブをアクティブにするには、仮想マシンをシャットダウンします。

5. **[disk.EnableUUID]** と表示される行が存在するかどうかを確認します。

    - その値が存在していて **False** に設定されている場合、**True** に値を変更します (値の大文字小文字は区別されません)。

    - その値が存在していて **True** に設定されている場合は、**[Cancel]\(キャンセル\)** をクリックします。

    - そのような値が存在しない場合、**[Add Row]\(行の追加\)** をクリックします。

    - 名前列に **disk.EnableUUID** を追加し、値を **TRUE** に設定します。

    ![[disk.EnableUUID] が存在するかどうかを確認する](./media/vmware-azure-install-linux-master-target/image25.png)

#### <a name="disable-kernel-upgrades"></a>カーネルのアップグレードを無効にする

Azure Site Recovery マスター ターゲット サーバーには特定バージョンの Ubuntu が必要なので、仮想マシンのカーネル アップグレードを無効にする必要があります。 カーネルのアップグレードが有効になっていると、マスター ターゲット サーバーが正しく機能しなくなる場合があります。

#### <a name="download-and-install-additional-packages"></a>その他のパッケージをダウンロードおよびインストールする

> [!NOTE]
> 追加パッケージをダウンロードしてインストールするには、インターネットに接続できることを確認してください。 インターネットに接続できない場合は、これらの RPM パッケージを手動で検索してインストールする必要があります。

 `apt-get install -y multipath-tools lsscsi python-pyasn1 lvm2 kpartx`

### <a name="get-the-installer-for-setup"></a>セットアップに使用するインストーラーの入手

マスター ターゲットからインターネットに接続できる場合は、以下の手順でインストーラーをダウンロードできます。 インターネットに接続できない場合は、プロセス サーバーからインストーラーをコピーしてインストールできます。

#### <a name="download-the-master-target-installation-packages"></a>マスター ターゲットのインストール パッケージをダウンロードする

[最新の Linux マスター ターゲット インストール ビットをダウンロードします](https://aka.ms/latestlinuxmobsvc)。

Linux を使用してこれをダウンロードするには、次のように入力します。

`wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz`

> [!WARNING]
> ダウンロードして、必ずホーム ディレクトリでインストーラーを解凍してください。 **/usr/Local** に解凍すると、インストールは失敗します。


#### <a name="access-the-installer-from-the-process-server"></a>プロセス サーバーからインストーラーにアクセスする

1. プロセス サーバーの **C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository** に移動します。

2. プロセス サーバーから必要なインストーラー ファイルをコピーし、自分の home ディレクトリに **latestlinuxmobsvc.tar.gz** という名前で保存します。


### <a name="apply-custom-configuration-changes"></a>カスタム構成変更を適用する

カスタム構成変更を適用するには、次の手順を使用します。


1. 次のコマンドを実行して、バイナリを解凍します。

    `tar -zxvf latestlinuxmobsvc.tar.gz`

    ![実行するコマンドのスクリーンショット](./media/vmware-azure-install-linux-master-target/image16.png)

2. 次のコマンドを実行して、アクセス許可を付与します。

    `chmod 755 ./ApplyCustomChanges.sh`


3. 次のコマンドを実行して、スクリプトを実行します。
    
    `./ApplyCustomChanges.sh`

> [!NOTE]
> スクリプトは、サーバー上で 1 回のみ実行してください。 続いてサーバーをシャットダウンします。 次のセクションの説明に従ってディスクを追加した後、サーバーを再起動します。

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>リテンション ディスクを Linux マスター ターゲット仮想マシンに追加する

リテンション ディスクを作成するには、次の手順を使用します。

1. 新しい 1 TB ディスクを Linux マスター ターゲット仮想マシンに接続してから、マシンを起動します。

2. **multipath -ll** コマンドを使用し、リテンション ディスクのマルチパス ID を確認します。**multipath -ll**

    ![マルチパス ID](./media/vmware-azure-install-linux-master-target/image27.png)

3. ドライブをフォーマットし、新しいドライブにファイル システムを作成します。**mkfs.ext4 /dev/mapper/<リテンション ディスクのマルチパス ID>**
    
    ![ファイル システム](./media/vmware-azure-install-linux-master-target/image23-centos.png)

4. ファイル システムを作成したら、リテンション ディスクをマウントします。

    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

5. **fstab** エントリを作成し、システムの起動時ごとにリテンション ドライブをマウントします。
    
    `vi /etc/fstab`
    
    **Insert** キーを押してファイルの編集を開始します。 新しい行を作成し、次のテキストを挿入します。 前述のコマンドでハイライトされているマルチパス ID に基づいてディスクのマルチパス ID を編集します。

    **/dev/mapper/<Retention disks multipath id> /mnt/retention ext4 rw 0 0**

    **Esc** キーを押して「**:wq**」(write and quit) と入力し、エディター ウィンドウを閉じます。

### <a name="install-the-master-target"></a>マスター ターゲットをインストールする

> [!IMPORTANT]
> マスター ターゲット サーバーのバージョンは、プロセス サーバーおよび構成サーバーと同等か、それより前のバージョンにする必要があります。 この条件が満たされない場合、再保護は成功しますが、レプリケーションで失敗します。


> [!NOTE]
> マスター ターゲット サーバーをインストールする前に、仮想マシンの **/etc/hosts** ファイルに、ローカル ホスト名を、すべてのネットワーク アダプターに関連付けられた IP アドレスにマップするエントリが含まれることを確認します。

1. 構成サーバーの **C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase** からパスフレーズをコピーします。 次のコマンドを実行して、同じローカル ディレクトリ内に **passphrase.txt** という名前で保存します。

    `echo <passphrase> >passphrase.txt`

    例: 

       `echo itUx70I47uxDuUVY >passphrase.txt`
    

2. 構成サーバーの IP アドレスをメモします。 次のコマンドを実行してマスター ターゲット サーバーをインストールし、さらに構成サーバーに登録します。

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    例: 
    
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

スクリプトが完了するまで待機します。 マスター ターゲットは、正常に登録されると、ポータルの **[Site Recovery インフラストラクチャ]** ページに一覧表示されます。


#### <a name="install-the-master-target-by-using-interactive-installation"></a>対話型インストールによりマスター ターゲットをインストールする

1. 次のコマンドを実行してマスター ターゲットをインストールします。 エージェント ロールの場合は、**[マスター ターゲット]** を選択します。

    ```
    ./install
    ```

2. インストールの既定の場所を選択します。続行するには、**Enter** キーを押します。

    ![マスター ターゲットのインストールの既定の場所を選択する](./media/vmware-azure-install-linux-master-target/image17.png)

インストールが完了した後、コマンド ラインを使って構成サーバーを登録します。

1. 構成サーバーの IP アドレスをメモします。 次の手順で必要になります。

2. 次のコマンドを実行してマスター ターゲット サーバーをインストールし、さらに構成サーバーに登録します。

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```
    例: 

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

     スクリプトが完了するまで待機します。 マスター ターゲットが正常に登録されていれば、ポータルの **[Site Recovery インフラストラクチャ]** ページに表示されます。


### <a name="install-vmware-tools--open-vm-tools-on-the-master-target-server"></a>マスター ターゲット サーバーに VMware ツール/open-vm-tools をインストールする

VMware ツールまたは open-vm-tools は、データストアを検出できるように、マスター ターゲット上にインストールする必要があります。 ツールをインストールしていない場合は、再保護画面にデータストアが表示されません。 VMware ツールのインストール後、再起動する必要があります。

### <a name="upgrade-the-master-target-server"></a>マスター ターゲット サーバーをアップグレードする

インストーラーを実行します。 マスター ターゲットにエージェントがインストールされていることが自動的に検出されます。 アップグレードするには、**[Y]** を選択します。セットアップが完了した後、次のコマンドを使用して、インストールされているマスター ターゲットのバージョンを確認します。

`cat /usr/local/.vx_version`


**[Version]\(バージョン\)** フィールドにマスター ターゲットのバージョン番号が表示されます。

## <a name="common-issues"></a>一般的な問題

* マスター ターゲットをはじめとする管理コンポーネントでは一切 Storage vMotion を有効にしないでください。 再保護に成功した後でマスター ターゲットが移動されると、仮想マシン ディスク (VMDK) を切断できません。 この場合、フェールバックは失敗します。

* マスター ターゲットの仮想マシンには、スナップショットが一切存在していないことが必要です。 スナップショットが存在するとフェールバックが失敗します。

* 一部のカスタム NIC 構成のために、起動中にネットワーク インターフェイスが無効になり、マスター ターゲット エージェントを初期化できません。 以下のプロパティが正しく設定されていることを確認してください。 イーサネット カード ファイルの /etc/sysconfig/network-scripts/ifcfg-eth* で次のプロパティを確認します。
    * BOOTPROTO=dhcp
    * ONBOOT=yes


## <a name="next-steps"></a>次の手順
マスター ターゲットのインストールと登録が完了したら、そのマスター ターゲットが **[Site Recovery インフラストラクチャ]** にある **[マスター ターゲット]** セクションの構成サーバーの概要の下に表示されることを確認できます。

[再保護](vmware-azure-reprotect.md)とフェールバックの作業に進んでください。

