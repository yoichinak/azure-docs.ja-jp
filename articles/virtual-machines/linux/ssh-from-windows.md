---
title: Windows での SSH キーを使用した Linux VM への接続 | Microsoft Docs
description: Windows コンピューターで SSH キーを生成して使用し、Azure 上の Linux 仮想マシンに接続する方法について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 2cacda3b-7949-4036-bd5d-837e8b09a9c8
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: danlep
ms.openlocfilehash: d0762f80267fa927681344a3e0de78b0800c8306
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38630213"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Azure 上の Windows で SSH キーを使用する方法

この記事では、Windows コンピューターで Secure Shell (SSH) キーを生成して使用し、Azure に Linux 仮想マシン (VM) を作成して接続する方法について説明します。 Linux または macOS のクライアントから SSH キーを使用する方法については、[クイック ガイダンス](mac-create-ssh-keys.md)または[詳細ガイダンス](create-ssh-keys-detailed.md)をご覧ください。

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="windows-packages-and-ssh-clients"></a>Windows パッケージと SSH クライアント
Azure の Linux VM に接続して管理するには、*SSH クライアント*を使用します。 Linux または macOS を実行しているコンピューターには、通常、SSH キーを生成して管理し、SSH 接続を行うための、SSH コマンドのスイートがあります。 

Windows コンピューターでは、それに相当する SSH コマンドが常にインストールされることはありません。 [Windows Subsystem for Linux](https://docs.microsoft.com/windows/wsl/about) を含む Windows 10 のバージョンでは、SSH クライアントなどのユーティリティを Bash シェル内でネイティブに実行したり、それらにアクセスしたりすることができます。 

Bash for Windows 以外のものを使用する場合、ローカルにインストールできる一般的な Windows SSH クライアントは、以下のパッケージに含まれています。

* [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [Git For Windows](https://git-for-windows.github.io/)
* [MobaXterm](http://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)

もう 1 つのオプションは、[Azure Cloud Shell](../../cloud-shell/overview.md) の Bash で利用可能な SSH ユーティリティを使うことです。 

* Web ブラウザーで ([https://shell.azure.com](https://shell.azure.com))、または [Azure Portal](https://portal.azure.com) で、Cloud Shell にアクセスします。 
* [Azure Account 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)をインストールすることにより、Visual Studio Code 内からターミナルとして Cloud Shell にアクセスします。

## <a name="create-an-ssh-key-pair"></a>SSH キー ペアの作成
このセクションでは、Windows 上で SSH キー ペアを作成するための 2 つのオプションを示します。

### <a name="create-ssh-keys-with-ssh-keygen"></a>ssh-keygen で SSH キーを作成する

Bash for Windows や GitBash (または Azure Cloud Shell の Bash) などのコマンド シェルを実行できる場合は、`ssh-keygen` コマンドを使用して SSH キー ペアを作成します。 次のコマンドを入力して、プロンプトに答えます。 SSH キー ペアが現在の場所にある場合、それらのファイルは上書きされます。 

```bash
ssh-keygen -t rsa -b 2048
```

背景情報と詳細情報については、`ssh-keygen` でキーを作成する手順の[概要](mac-create-ssh-keys.md)または[詳細](create-ssh-keys-detailed.md)をご覧ください。

### <a name="create-ssh-keys-with-puttygen"></a>PuTTYgen で SSH キーを作成する

GUI ベースのツールを使って SSH キーを作成する場合は、[PuTTY のダウンロード パッケージ](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)に含まれる PuTTYgen キー ジェネレーターを使うことができます。 

PuTTYgen で SSH RSA キー ペアを作成するには:

1. PuTTYgen を開始します。

2. **[Generate]\(生成\)** をクリックします。 既定では、PuTTYgen は 2048 ビットの SSH-2 RSA キーを生成します。

4. 空白領域にマウスを移動して、キーにある程度のランダムさを生成します。

5. 公開キーが生成された後、必要に応じて、パスフレーズを入力して確認します。 SSH キーを使って VM に対する認証を行うときに、パスフレーズの入力を求められます。 パスフレーズを使用しない場合、だれかが秘密キーを入手すると、その人物はそのキーを使用するすべての VM またはサービスにログインできるようになります。 そのため、パスフレーズを作成することをお勧めします。 ただし、パスフレーズを忘れた場合、それを回復する方法はありません。

6. 公開キーがウィンドウの上部に表示されます。 Linux VM を作成するときに、この 1 行形式の公開キーをコピーし、Azure Portal または Azure Resource Manager テンプレートに貼り付けます。 **[Save public key (公開キーを保存)]** をクリックして、公開キーを自分のコンピューターに保存することもできます。

    ![PuTTY 公開キー ファイルの保存](./media/ssh-from-windows/save-public-key.png)

7. PuTTy の秘密キー形式 (.ppk ファイル) で秘密キーを保存する必要がある場合は、**[Save private key]\(秘密キーを保存\)** をクリックします。 後で PuTTY を使って VM への SSH 接続を行うときに、.ppk ファイルが必要です。

    ![PuTTY 秘密キー ファイルを保存する](./media/ssh-from-windows/save-ppk-file.png)

    多くの SSH クライアントによって使われる秘密キーの形式である OpenSSH 形式で秘密キーを保存する場合は、**[Conversions]\(変換\)** > **[Export OpenSSH key]\(OpenSSH キーのエクスポート\)** をクリックします。

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>VM を展開するときに SSH 公開キーを提供する

認証に SSH キーを使う Linux VM を作成するには、Azure Portal または他の方法を使って VM を作成するときに、SSH 公開キーを提供します。

次の例は、Linux VM を作成するときにこの公開キーをコピーして Azure Portal に貼り付ける方法を示しています。 通常、公開キーは新しい VM の `~/.ssh/authorized_keys` に格納します。

   ![Azure Portal で VM を作成する際の公開キーの使用](./media/ssh-from-windows/use-public-key-azure-portal.png)


## <a name="connect-to-your-vm"></a>VM に接続する

Windows から Linux VM に SSH 接続する方法の 1 つは、SSH クライアントを使うことです。 Windows システムに SSH クライアントがインストールされている場合、または Azure Cloud Shell の Bash で SSH ツールを使っている場合は、これが推奨される方法です。 GUI ベースのツールを使う場合は、PuTTY で接続することができます。  

### <a name="use-an-ssh-client"></a>SSH クライアントを使用する
公開キーを Azure VM に、秘密キーをローカル システム上に配置した状態で、VM の IP アドレスまたは DNS 名を使用して、VM に SSH 接続します。 次のコマンドの *azureuser* と *myvm.westus.cloudapp.azure.com* を、管理者のユーザー名と完全修飾ドメイン名 (または IP アドレス) に置き換えてください。

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

キー ペアを作成するときにパスフレーズを構成した場合は、ログイン プロセス中に入力を求められたら、そのパスフレーズを入力します。

### <a name="connect-with-putty"></a>PuTTY を使用して接続する

[PuTTY のダウンロード パッケージ](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)をインストールし、前に PuTTY の秘密キー (*.ppk ファイル) を生成した場合は、PuTTY を使って Linux VM に接続することができます。

1. PuTTY を起動します。

2. Azure Portal で取得した VM のホスト名と IP アドレスを入力します。

    ![新しい PuTTY 接続の開始](./media/ssh-from-windows/putty-new-connection.png)

3. **[Open (開く)]** を選択する前に、**[Connection (接続)]** > **[SSH]** > **[Auth]** タブをクリックします。PuTTY の秘密キー (.ppk ファイル) を参照して選択します。

    ![認証用の PuTTY 秘密キーを選択する](./media/ssh-from-windows/putty-auth-dialog.png)

4. **[Open]\(開く\)** をクリックして、VM に接続します。

## <a name="next-steps"></a>次の手順

* SSH キーの使用に関する詳細な手順、オプション、および高度な例については、[SSH キー ペアの詳細な作成手順](create-ssh-keys-detailed.md)に関するページをご覧ください。

* Azure Cloud Shell 内で PowerShell を使用して SSH キーを生成し、Linux VM に SSH 接続することもできます。 [PowerShell のクイック スタート](../../cloud-shell/quickstart-powershell.md#ssh)に関するページをご覧ください。

* Linux VM に対する SSH 接続に問題がある場合は、[Azure Linux VM に対する SSH 接続のトラブルシューティング](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するトピックを参照してください。
