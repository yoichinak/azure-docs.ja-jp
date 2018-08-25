---
title: Linux で Azure Files を使用する | Microsoft Docs
description: Linux で SMB 経由で Azure File 共有をマウントする方法について説明します。
services: storage
author: RenaShahMSFT
ms.service: storage
ms.topic: article
ms.date: 03/29/2018
ms.author: renash
ms.component: files
ms.openlocfilehash: 89343f3e4ec91dd32b24cdea6632cecd855cc6f8
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39523225"
---
# <a name="use-azure-files-with-linux"></a>Linux で Azure Files を使用する
[Azure Files](storage-files-introduction.md) は、Microsoft の使いやすいクラウド ファイル システムです。 Azure ファイル共有は、[SMB カーネル クライアント](https://wiki.samba.org/index.php/LinuxCIFS)を使用して Linux ディストリビューションにマウントできます。 この記事では、Azure ファイル共有を `mount` コマンドを使用してオンデマンドでマウントするか、`/etc/fstab` にエントリを作成することで起動時にマウントするという 2 つの方法について説明します。

> [!NOTE]  
> Azure ファイル共有がホストされている Azure リージョン以外の場所 (オンプレミスや他の Azure リージョンなど) に Azure File 共有をマウントするには、OS が SMB 3.0 の暗号化機能をサポートしている必要があります。

## <a name="prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Linux で cifs-utils パッケージを使用してAzure ファイル共有をマウントするための前提条件
<a id="smb-client-reqs"></a>
* **マウントのニーズに合わせて Linux ディストリビューションを選択する。**  
      Azure Files は、SMB 2.1 および SMB 3.0 経由のどちらかでマウントできます。 クライアントのオンプレミスから受信した接続や他の Azure リージョンでの接続の場合、Azure Files は SMB 2.1 (または、非暗号化 SMB 3.0) を拒否します。 *[安全な転送が必須]* がストレージ アカウントで有効になっている場合、Azure Files は暗号化付き SMB 3.0 を使った接続のみを許可します。
    
    SMB 3.0 暗号化サポートは、Linux カーネル バージョン 4.11 で導入され、普及している Linux ディストリビューションのより古いカーネル バージョンにバックポートされました。 このドキュメントの公開時点では、Azure ギャラリーの以下のディストリビューションでは、テーブル ヘッダーで指定されたマウント オプションをサポートしています。 

* **該当のマウント機能で推奨される最低限のバージョン (SMB バージョン 2.1 と SMB バージョン 3.0)**    
    
    |   | SMB 2.1 <br>(同じ Azure リージョン内の VM 上のマウント) | SMB 3.0 <br>(オンプレミスおよびクロスリージョンからのマウント) |
    | --- | :---: | :---: |
    | Ubuntu Server | 14.04+ | 16.04+ |
    | RHEL | 7+ | 7.5+ |
    | CentOS | 7+ |  7.5+ |
    | Debian | 8+ |   |
    | openSUSE | 13.2+ | 42.3 以降 |
    | SUSE Linux Enterprise Server | 12 | 12 SP3+ |
    
    お使いの Linux ディストリビューションがこの一覧にない場合は、次のコマンドを使用して Linux カーネル バージョンを参照し、チェックできます。    

   ```bash
   uname -r
   ```    

* <a id="install-cifs-utils"></a>**cifs-utils パッケージがインストールされている。**  
    cifs-utils パッケージは、選択した Linux ディストリビューションのパッケージ マネージャーを使用してインストールできます。 

    **Ubuntu** と **Debian ベースの**ディストリビューションでは、`apt-get` パッケージ マネージャーを使用します。

    ```bash
    sudo apt-get update
    sudo apt-get install cifs-utils
    ```

    **RHEL** と **CentOS** では、`yum` パッケージ マネージャーを使用します。

    ```bash
    sudo yum install cifs-utils
    ```

    **openSUSE** では、`zypper` パッケージ マネージャーを使用します。

    ```bash
    sudo zypper install cifs-utils
    ```

    他のディストリビューションでは、適切なパッケージ マネージャーを使用するか、[ソースからコンパイル](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)します。
    
* **マウントされた共有のディレクトリ/ファイルのアクセス権限を決定する**: この後の例では、アクセス許可 `0777`を使用してすべてのユーザーに読み取り、書き込み、および実行権限を与えています。 必要に応じて、他の [chmod 権限](https://en.wikipedia.org/wiki/Chmod)に置き換えることができます。 

* **ストレージ アカウント名**: Azure File 共有をマウントするには、ストレージ アカウントの名前が必要です。

* **ストレージ アカウント キー**: Azure File 共有をマウントするには、プライマリ (またはセカンダリ) ストレージ キーが必要です。 現時点では、SAS キーは、マウントではサポートされていません。

* **ポート 445 が開いていることを確認する**: SMB は、TCP ポート 445 経由で通信します。ファイアウォールによってクライアント コンピューターの TCP ポート 445 がブロックされないことを確認してください。

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Azure ファイル共有を `mount` を使用してオンデマンドでマウントする
1. **[使用する Linux ディストリビューション用の cifs-utils パッケージをインストールします](#install-cifs-utils)**。

2. **マウント ポイント用のフォルダーを作成します**。マウント ポイント用のフォルダーはファイル システム上のどこにでも作成できますが、`/mnt` フォルダー下にこれを作成するのが一般的な規則です。 例: 

    ```bash
    mkdir /mnt/MyAzureFileShare
    ```

3. **mount コマンドを使用して Azure ファイル共有をマウントします**。`<storage-account-name>`、`<share-name>`、`<smb-version>`、`<storage-account-key>`、および `<mount-point>` をお使いの環境に適した情報に置き換えることを忘れないでください。 お使いの Linux ディストリビューションで暗号化付き SMB 3.0 がサポートされている場合 (詳細は「[SMB クライアント要件を理解している](#smb-client-reqs)」を参照)、`<smb-version>`の `3.0` を使用します。 暗号化付き SMB 3.0 をサポートしていない Linux ディストリビューションの場合は、`<smb-version>`の `2.1`を使用します。 Azure ファイル共有は、SMB 3.0 がある Azure リージョン (オンプレミスを含むか、または別の Azure リージョン内にある) の外部でしかマウントできないことに注意してください。 

    ```bash
    sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> Azure ファイル共有の使用を完了したら、`sudo umount <mount-point>` を使用して共有を解除できます。

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>`/etc/fstab` を使って Azure ファイル共有の永続的なマウント ポイントを作成する
1. **[使用する Linux ディストリビューション用の cifs-utils パッケージをインストールします](#install-cifs-utils)**。

2. **マウント ポイント用のフォルダーを作成します**。マウント ポイント用のフォルダーはファイル システム上のどこにでも作成できますが、`/mnt` フォルダー下にこれを作成するのが一般的な規則です。 これをどこに作成する場合でも、フォルダーの絶対パスをメモしてください。 たとえば、以下のコマンドでは、`/mnt` 下に新しいフォルダーを作成します (パスは絶対パスです)。

    ```bash
    sudo mkdir /mnt/MyAzureFileShare
    ```

3. **共有ファイル用のユーザー名 (ストレージ アカウント名) とパスワード (ストレージ アカウント キー) を格納する資格情報ファイルを作成します。** `<storage-account-name>` と `<storage-account-key>` をお使いの環境に適した情報に置き換えることを忘れないでください。 

    ```bash
    if [ -d "/etc/smbcredentials" ]; then
        sudo mkdir /etc/smbcredentials
    fi

    if [ ! -f "/etc/smbcredentials/<storage-account-name>.cred" ]; then
        sudo bash -c 'echo "username=<storage-account-name>" >> /etc/smbcredentials/<storage-account-name>.cred'
        sudo bash -c 'echo "password=<storage-account-key>" >> /etc/smbcredentials/<storage-account-name>.cred'
    fi
    ```

4. **資格情報ファイルのアクセス許可を変更して、ルートのみがパスワード ファイルの読み取りと変更を実行できるようにします。** ストレージ アカウント キーは、本質的にはストレージ アカウント キーのスーパー管理者パスワードであるため、ルートのみがアクセスできるようにファイルのアクセス許可を設定して、特権の低いユーザーがストレージ アカウント キーを取得できないようにすることが重要です。   

    ```bash
    sudo chmod 600 /etc/smbcredentials/<storage-account-name>.cred
    ```

5. **次のコマンドを使用して、次の行を `/etc/fstab` に追加します。**`<storage-account-name>`、`<share-name>`、`<smb-version>`、および `<mount-point>` をお使いの環境に適した情報に置き換えることを忘れないでください。 お使いの Linux ディストリビューションで暗号化付き SMB 3.0 がサポートされている場合 (詳細は「[SMB クライアント要件を理解している](#smb-client-reqs)」を参照)、`<smb-version>`の `3.0` を使用します。 暗号化付き SMB 3.0 をサポートしていない Linux ディストリビューションの場合は、`<smb-version>`の `2.1`を使用します。 Azure ファイル共有は、SMB 3.0 がある Azure リージョン (オンプレミスを含むか、または別の Azure リージョン内にある) の外部でしかマウントできないことに注意してください。 

    ```bash
    sudo bash -c 'echo "//<storage-account-name>.file.core.windows.net/<share-name> <mount-point> cifs nofail,vers=<smb-version>,credentials=/etc/smbcredentials/<storage-account-name>.cred,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'
    ```

> [!Note]  
> `/etc/fstab` を編集した後、再起動する代わりに、`sudo mount -a` を使用して、Azure ファイル共有をマウントできます。

## <a name="feedback"></a>フィードバック
Linux ユーザーからのご意見をお待ちしています。

Azure Files for Linux ユーザーのグループによって、File Storage を Linux で評価および導入するときにフィードバックを共有できるフォーラムが提供されています。 [Azure Files Linux Users](mailto:azurefileslinuxusers@microsoft.com) にメールを送信して、ユーザー グループに参加してください。

## <a name="next-steps"></a>次の手順
Azure Files の詳細については、次のリンクをご覧ください。
* [Azure Files の概要](storage-files-introduction.md)
* [Azure Files のデプロイの計画](storage-files-planning.md)
* [FAQ](../storage-files-faq.md)
* [トラブルシューティング](storage-troubleshoot-linux-file-connection-problems.md)
