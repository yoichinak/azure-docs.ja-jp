---
title: 詳細な手順 - Azure Linux VM 用の SSH キー ペア | Microsoft Docs
description: Azure の Linux VM 用に SSH の公開キーと秘密キーのペアを作成して管理する詳細な手順について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: danlep
ms.openlocfilehash: 827c80a70047fd0f1ad67e4f19cb2300e45b2c6b
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38606208"
---
# <a name="detailed-steps-create-and-manage-ssh-keys-for-authentication-to-a-linux-vm-in-azure"></a>詳細な手順: Azure の Linux VM に対する認証用に SSH キーを作成して管理する 
Secure Shell (SSH) のキー ペアを使用すると、既定で認証に SSH キーを使う Linux 仮想マシンを Azure 上に作成でき、ログインするためのパスワードが不要になります。 Azure Portal、Azure CLI、Resource Manager テンプレート、他のツールで作成された VM は、展開の一部として SSH 公開キーを含むことができ、SSH 接続用に SSH キー認証が設定されます。 

この記事では、SSH クライアント接続用の SSH RSA 公開/秘密キー ファイル ペアの作成と管理について、詳細な背景と手順を説明します。 すぐに使用できるコマンドについては、[Azure での Linux VM 用の SSH 公開/秘密キー ペアの作成方法](mac-create-ssh-keys.md)に関する記事をご覧ください。

Windows コンピューター上で、SSH キーを生成して使用するその他の方法については、「[Azure 上の Windows で SSH キーを使用する方法](ssh-from-windows.md)」を参照してください。

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

### <a name="private-key-passphrase"></a>秘密キーのパスフレーズ
SSH 秘密キーには、それを保護するためのセキュリティで高度に保護されたパスフレーズが必要です。 このパスフレーズは、秘密 SSH キー ファイルにアクセスするためだけのものであり、ユーザー アカウント パスワードでは "*ありません*"。 SSH キーにパスフレーズを追加すると、128 ビット AES を使用して秘密キーが暗号化されるため、暗号化解除するパスフレーズなしでは秘密キーを使用できなくなります。 攻撃者によって盗まれた秘密キーにパスフレーズがないと、その秘密キーが使用され、対応する公開キーを持つ任意のサーバーにログインされてしまいます。 秘密キーがパスフレーズによって保護されている場合、攻撃者は秘密キーを使用できないため、Azure 上のインフラストラクチャにセキュリティ レイヤーが追加されたことになります。

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-keys-use-and-benefits"></a>SSH キーの使用方法と利点

公開キーを指定して Azure VM を作成すると、Azure は公開キーを (`.pub` の形式で) VM の `~/.ssh/authorized_keys` フォルダーにコピーします。 `~/.ssh/authorized_keys` の SSH キーは、クライアントが SSH ログイン接続の対応する秘密キーと一致することを確認するために使用されます。 認証に SSH キーを使用する Azure Linux VM では、Azure は、パスワード ログインを禁止して SSH キーのみを許可するよう SSHD サーバーを構成します。 そのため、SSH キーで Azure Linux VM を作成すると、VM の展開をセキュリティで保護し、一般的な展開後の構成手順である `sshd_config` ファイルでのパスワードの無効化を省略できます。

SSH キーを使用したくない場合は、パスワード認証を使うように Linux VM を設定できます。 VM がインターネットに公開されない場合は、パスワードを使用するだけで十分なことがあります。 ただし、Linux VM ごとにパスワードを管理し、パスワードの最小の長さ、定期的な更新など、優良なパスワードのポリシーと運用を維持する必要があります。 SSH キーを使用すると、複数の VM で別個に資格情報を管理する複雑さが軽減されます。

## <a name="generate-keys-with-ssh-keygen"></a>ssh-keygen でキーを生成する

キーを作成するための推奨されるコマンドは `ssh-keygen` です。このコマンドは、Azure Cloud Shell、macOS、Linux ホスト上の OpenSSH ユーティリティや、[Windows Subsystem for Linux](https://docs.microsoft.com/windows/wsl/about)、および他のツールで利用できます。 `ssh-keygen` では、一連の質問に答えることによって、秘密キーと対応する公開キーが出力されます。 

SSH キーは既定で `~/.ssh` ディレクトリに保持されます。  `~/.ssh` ディレクトリがない場合、`ssh-keygen` コマンドによって、適切なアクセス許可で作成されます。

### <a name="basic-example"></a>基本的な例

次の `ssh-keygen` コマンドは、既定で、2048 ビットの SSH RSA 公開および秘密キー ファイルを `~/.ssh` ディレクトリに生成します。 SSH キー ペアが現在の場所にある場合、それらのファイルは上書きされます。

```bash
ssh-keygen -t rsa -b 2048
```

### <a name="detailed-example"></a>詳細な例
次の例では、SSH RSA キー ペアを作成するためのその他のコマンド オプションを示します。 SSH キー ペアが現在の場所にある場合、それらのファイルは上書きされます。 

```bash
ssh-keygen \
    -t rsa \
    -b 4096 \
    -C "azureuser@myserver" \
    -f ~/.ssh/mykeys/myprivatekey \
    -N mypassphrase
```

**コマンドの説明**

`ssh-keygen` = キーの作成に使用するプログラム。

`-t rsa` = 作成するキーの種類。この場合は RSA 形式。

`-b 4096` = キーのビット数。この場合は 4096。

`-C "azureuser@myserver"` = 識別しやすいように公開キー ファイルの末尾に追記されたコメント。 通常はメール アドレスがコメントとして使用されますが、インフラストラクチャに最適な文字列を使用します。

`-f ~/.ssh/mykeys/myprivatekey` = 既定の名前を使用しない場合の、秘密キー ファイルのファイル名。 `.pub` を付加された対応する公開キー ファイルが、同じディレクトリに生成されます。 ディレクトリは存在している必要があります。

`-N mypassphrase` = 秘密キー ファイルへのアクセスに使用される追加のパスフレーズ。 

### <a name="example-of-ssh-keygen"></a>ssh-keygen の例

```bash
ssh-keygen -t rsa -b 2048 -C "azureuser@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/azureuser/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/azureuser/.ssh/id_rsa.
Your public key has been saved in /home/azureuser/.ssh/id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 azureuser@myserver
The keys randomart image is:
+--[ RSA 2048]----+
|        o o. .   |
|      E. = .o    |
|      ..o...     |
|     . o....     |
|      o S =      |
|     . + O       |
|      + = =      |
|       o +       |
|        .        |
+-----------------+
```

#### <a name="saved-key-files"></a>保存されるキー ファイル

`Enter file in which to save the key (/home/azureuser/.ssh/id_rsa): ~/.ssh/id_rsa`

この記事で使用するキー ペアの名前です。 既定では、キー ペアの名前に `id_rsa` が割り当てられます。秘密キーのファイル名として `id_rsa` を想定しているツールもあるため、そのようにすることをお勧めします。 ディレクトリ `~/.ssh/` は、SSH キー ペアと SSH 構成ファイルの既定の場所です。 完全パスで指定されていない場合、`ssh-keygen` は、既定の `~/.ssh` ではなく、現在の作業ディレクトリにキーを作成します。

#### <a name="list-of-the-ssh-directory"></a>`~/.ssh` ディレクトリのリスト

```bash
ls -al ~/.ssh
-rw------- 1 azureuser staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 azureuser staff   410 Aug 25 18:04 id_rsa.pub
```

#### <a name="key-passphrase"></a>キーのパスフレーズ

`Enter passphrase (empty for no passphrase):`

秘密キーにはパスフレーズを追加することを "*強く*" お勧めします。 キー ファイルを保護するパスフレーズがないと、ファイルを持っている人はだれでも、そのファイルを使用して、対応する公開キーのあるサーバーにログインすることができます。 パスフレーズを追加すれば、第三者に秘密キー ファイルへのアクセスを許してしまった場合でも、キーを変更する時間ができるので、保護のレベルが上がります。

## <a name="generate-keys-automatically-during-deployment"></a>展開の間にキーを自動生成する

[Azure CLI 2.0](/cli/azure) を使用して VM を作成する場合は、必要に応じて [az vm create](/cli/azure/vm#az_vm_create) コマンドを `--generate-ssh-keys` オプション付きで実行することで、SSH 公開キー ファイルと秘密キー ファイルを作成できます。 キーは、~/.ssh ディレクトリに格納されます。 このコマンド オプションを指定すると、その場所にキーが既に存在している場合でも、キーが上書きされることはありません。

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>VM の展開時に SSH 公開キーを提供する

認証するために SSH キーを使用する Linux VM を作成するには、Azure Portal、CLI、Resource Manager テンプレート、またはその他の方法を使用して VM を作成するときに SSH 公開キーを提供します。 ポータルを使用するときは、公開キー自体を入力します。 [Azure CLI 2.0](/cli/azure) で既存の公開キーを使用する VM を作成する場合は、[az vm create](/cli/azure/vm#az_vm_create) コマンドを `--ssh-key-value` オプション付きで実行することで、使用する公開キーの値または場所を指定します。 

SSH 公開キーの書式がわからない場合は、次のように `cat` を実行して公開キーを表示できます。`~/.ssh/id_rsa.pub` の部分は実際の公開キー ファイルの場所に置き換えてください。

```bash
cat ~/.ssh/id_rsa.pub
```

出力は次のようになります (ここで示されている出力は編集されています)。

```
ssh-rsa XXXXXXXXXXc2EAAAADAXABAAABAXC5Am7+fGZ+5zXBGgXS6GUvmsXCLGc7tX7/rViXk3+eShZzaXnt75gUmT1I2f75zFn2hlAIDGKWf4g12KWcZxy81TniUOTjUsVlwPymXUXxESL/UfJKfbdstBhTOdy5EG9rYWA0K43SJmwPhH28BpoLfXXXXXG+/ilsXXXXXKgRLiJ2W19MzXHp8z3Lxw7r9wx3HaVlP4XiFv9U4hGcp8RMI1MP1nNesFlOBpG4pV2bJRBTXNXeY4l6F8WZ3C4kuf8XxOo08mXaTpvZ3T1841altmNTZCcPkXuMrBjYSJbA8npoXAXNwiivyoe3X2KMXXXXXdXXXXXXXXXXCXXXXX/ azureuser@myserver
```

公開キー ファイルの内容をコピーし、Azure Portal または Resource Manager テンプレートに貼り付ける場合は、余分な空白文字をコピーしたり、改行を追加したりしないように注意してください。 たとえば、macOS を使用している場合は、公開キー ファイル (既定では `~/.ssh/id_rsa.pub`) を **pbcopy** にパイプして、内容をコピーできます (**xclip** など、同じ目的を達成できる Linux プログラムが他にもあります)。

複数行形式の公開キーを使う方がよい場合は、前に作成した公開キーから pem コンテナーに RFC4716 形式のキーを生成できます。

既存の SSH 公開キーから RFC4716 形式のキーを作成するには、次のようにします。

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="ssh-to-your-vm-with-an-ssh-client"></a>SSH クライアントを使用して VM に SSH 接続する
公開キーを Azure VM に、秘密キーをローカル システム上に配置した状態で、VM の IP アドレスまたは DNS 名を使用して、VM に SSH 接続します。 次のコマンドの *azureuser* と *myvm.westus.cloudapp.azure.com* を、管理者のユーザー名と完全修飾ドメイン名 (または IP アドレス) に置き換えてください。

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

キー ペアを作成する際にパスフレーズを指定した場合は、ログイン プロセス中に入力を求められたら、そのパスフレーズを入力します  (サーバーは `~/.ssh/known_hosts` フォルダーに追加されます。Azure VM にある公開キーが変更されるかサーバー名が `~/.ssh/known_hosts` から削除されるまで、再度接続を求められることはありません)。

## <a name="use-ssh-agent-to-store-your-private-key-passphrase"></a>ssh-agent を使用して秘密キーのパスフレーズを格納する

SSH ログインのたびに秘密キー ファイルのパスフレーズを入力しなくて済むように、`ssh-agent` を使用して秘密キー ファイルのパスフレーズをキャッシュできます。 Mac を使用している場合、`ssh-agent` を呼び出すと、秘密キーのパスフレーズは macOS キーチェーンによって安全に保存されます。

パスフレーズを対話的に使用する必要がないように、`ssh-agent` と `ssh-add` を確認して使用し、キー ファイルに関する情報を SSH システムに伝えます。

```bash
eval "$(ssh-agent -s)"
```

ここで、`ssh-add` コマンドを使用して、`ssh-agent` に秘密キーを追加します。

```bash
ssh-add ~/.ssh/id_rsa
```

これで、秘密キーのパスフレーズが `ssh-agent` に格納されました。

## <a name="use-ssh-copy-id-to-copy-the-key-to-an-existing-vm"></a>ssh-copy-id を使用して既存の VM にキーをコピーする
既に VM を作成済みの場合は、次のようなコマンドを使用して、Linux VM に新しい SSH 公開キーをインストールできます。

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub azureuser@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>SSH 構成ファイルの作成と構成

SSH 構成ファイル (`~/.ssh/config`) を作成して構成すると、ログインを高速化し、SSH クライアントの動作を最適化することができます。 

次の例で示す簡単な構成を使うと、既定の SSH 秘密キーを使って、特定の VM にユーザーとしてすばやくログインできます。 

### <a name="create-the-file"></a>ファイルの作成

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>ファイルを編集して新しい SSH 構成を追加する

```bash
vim ~/.ssh/config
```

### <a name="example-configuration"></a>構成の例

ホスト VM に適した構成設定を追加します。

```bash
# Azure Keys
Host myvm
  Hostname 102.160.203.241
  User azureuser
# ./Azure Keys
```

追加のホストに構成を追加することで、それぞれが独自の専用キー ペアを使用するようにできます。 高度な構成オプションについて詳しくは、「[SSH config file](https://www.ssh.com/ssh/config/)」(SSH 構成ファイル) をご覧ください。

SSH キー ペアを作成し、SSH 構成ファイルを構成したので、すばやく安全に Linux VM にログインできるようになりました。 次のコマンドを実行すると、SSH は SSH 構成ファイルを探して、その `Host myvm` ブロックからすべての設定を読み込みます。

```bash
ssh myvm
```

SSH キーを使用して初めてサーバーにログインするとき、キー ファイルのパスフレーズを入力するように求められます。

## <a name="next-steps"></a>次の手順

次のステップでは、新しい SSH 公開キーを使用して Azure Linux VM を作成します。 Azure VM は、SSH 公開キーをログインとして作成した方が、既定のログイン方法であるパスワードを使って作成するよりも高いセキュリティが得られます。

* [Azure Portal で Linux 仮想マシンを作成する](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure CLI で Linux 仮想マシンを作成する](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure テンプレートを使用して Linux VM を作成する](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
