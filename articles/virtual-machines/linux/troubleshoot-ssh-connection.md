---
title: Azure VM への SSH 接続の問題に関するトラブルシューティング | Microsoft Docs
description: Linux を実行する Azure VM に対する "SSH 接続の失敗" や "SSH 接続の拒否" などの問題のトラブルシューティング方法。
keywords: ssh 接続が拒否される, ssh エラー, azure ssh, SSH 接続に失敗する
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: dcb82e19-29b2-47bb-99f2-900d4cfb5bbb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: 5d2544ec9ef758cf34a846562fa659b28044c52d
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37932689"
---
# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Azure Linux VM に対する SSH 接続の失敗、エラー、拒否のトラブルシューティング
Linux 仮想マシン (VM) に接続しようとしたときに、さまざまな理由で Secure Shell (SSH) エラー、SSH 接続エラー、または SSH 拒否が発生することがあります。 この記事は、問題を特定して修正するために役立ちます。 Azure Portal、Azure CLI、または Linux 用の VM アクセス拡張機能を使用して、接続の問題を解決できます。

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムとスタック オーバーフロー フォーラム](http://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](http://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。 Azure サポートの使用方法の詳細については、「 [Microsoft Azure サポートに関する FAQ](http://azure.microsoft.com/support/faq/)」を参照してください。

## <a name="quick-troubleshooting-steps"></a>簡単なトラブルシューティング手順
トラブルシューティングの各手順を実行した後、VM に再接続してみてください。

1. SSH 構成をリセットします。
2. ユーザーの資格情報をリセットします。
3. [ネットワーク セキュリティ グループ](../../virtual-network/security-overview.md) ルールで SSH トラフィックが許可されていることを確認します。
   * SSH トラフィックを許可するネットワーク セキュリティ グループ ルールが存在することを確認します (既定では TCP ポート 22)。
   * ポートのリダイレクト/マッピングは、Azure Load Balancer なしでは使用できません。
4. [VM リソースの正常性](../../resource-health/resource-health-overview.md)を確認します。 
   * VM が正常であると報告されていることを確認します。
   * ブート診断を有効にしている場合は、VM のブート エラーがログに報告されていないことを確認します。
5. VM を再起動します。
6. VM を再デプロイします。

詳しいトラブルシューティング手順と説明を引き続きお読みください。

## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>SSH 接続の問題をトラブルシューティングするために使用できる方法
資格情報または SSH 構成は、次のいずれかの方法でリセットできます。

* [Azure Portal](#use-the-azure-portal) - SSH 構成または SSH キーをすばやくリセットする必要があり、Azure ツールをインストールしていない場合に最適です。
* [Azure CLI 2.0](#use-the-azure-cli-20) - 既にコマンド ラインにいる場合は、SSH 構成または資格情報をすばやくリセットします。 [Azure CLI 1.0](#use-the-azure-cli-10) も使用できます。
* [Azure VMAccessForLinux 拡張機能](#use-the-vmaccess-extension) - json 定義ファイルを作成して再度使用して、SSH 構成またはユーザーの資格情報をリセットします。

トラブルシューティングの各手順を実行した後、再度 VM に接続してみてください。 それでも接続できない場合は、次の手順をお試しください。

## <a name="use-the-azure-portal"></a>Azure ポータルの使用
Azure Portal では、ローカル コンピューターへのツールのインストールなしで SSH 構成またはユーザーの資格情報を簡単にリセットできます。

Azure Portal で VM を選択します。 **[サポート + トラブルシューティング]** セクションまで下へスクロールし、**[パスワードのリセット]** を選択します (次の例を参照)。

![Azure Portal で SSH 構成または資格情報をリセットする](./media/troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="reset-the-ssh-configuration"></a>SSH 構成をリセットする
最初の手順として、**[モード]** ドロップダウン メニューから `Reset configuration only`を選択し (上のスクリーンショット参照)、**[リセット]** ボタンをクリックします。 この操作を完了したら、VM にもう一度アクセスしてみます。

### <a name="reset-ssh-credentials-for-a-user"></a>ユーザーの SSH 資格情報をリセットする
既存のユーザーの資格情報をリセットするには、**[モード]** ドロップダウン メニューから `Reset SSH public key` または `Reset password` を選択します (上のスクリーンショット参照)。 ユーザー名と、SSH キーまたは新しいパスワードを指定し、**[リセット]** ボタンをクリックします。

このメニューから、VM に対して sudo 特権を持つユーザーを作成することもできます。 新しいユーザー名と、関連付けられているパスワードまたは SSH キーを入力し、**[リセット]** ボタンをクリックします。

### <a name="check-security-rules"></a>セキュリティ規則を確認する

[IP フロー検証](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md)を使用して、ネットワーク セキュリティ グループ規則によって、仮想マシンから送受信されるトラフィックがブロックされていないかどうかを確認します。 有効なセキュリティ グループ規則を確認して、SSH ポート (既定では 22) に対して受信 "許可" NSG 規則が存在し、優先されていることを確認することもできます。 詳細については、「[有効なセキュリティ規則を使用した VM トラフィック フローのトラブルシューティング](../../virtual-network/diagnose-network-traffic-filter-problem.md)」を参照してください。

### <a name="check-routing"></a>ルーティングを確認する

Network Watcher の[次ホップ](../../network-watcher/network-watcher-check-next-hop-portal.md)機能を使用して、ルートが仮想マシンとの間でトラフィックのルーティングを妨げていないことを確認します。 有効なルートを見直し、ネットワーク インターフェイスのすべての有効なルートを確認することもできます。 詳細については、「[有効なルートを使用した VM トラフィック フローのトラブルシューティング](../../virtual-network/diagnose-network-routing-problem.md)」を参照してください。

## <a name="use-the-azure-cli-20"></a>Azure CLI 2.0 を使用する
まだインストールしていない場合は、最新の [Azure CLI 2.0](/cli/azure/install-az-cli2) をインストールし、[az login](/cli/azure/reference-index#az_login) を使用して Azure アカウントにログインします。

カスタム Linux ディスク イメージを作成してアップロードしている場合は、[Microsoft Azure Linux Agent](../extensions/agent-windows.md) のバージョン 2.0.5 以降がインストールされていることを確認します。 ギャラリー イメージを使用して作成された VM の場合、このアクセス拡張機能は既にインストールされ、自動的に構成されています。

### <a name="reset-ssh-configuration"></a>SSH 構成をリセットする
最初に SSH 構成を既定値にリセットして、VM 上の SSH サーバーを再起動します。 ユーザー アカウントの名前、パスワード、または SSH キーは変更されません。
次の例は、[az vm user reset-ssh](/cli/azure/vm/user#az_vm_user_reset_ssh) を使用して、`myResourceGroup` にある `myVM` という名前の VM で SSH 構成をリセットします。 実際の値を次のように使用します。

```azurecli
az vm user reset-ssh --resource-group myResourceGroup --name myVM
```

### <a name="reset-ssh-credentials-for-a-user"></a>ユーザーの SSH 資格情報をリセットする
次の例は、[az vm user update](/cli/azure/vm/user#az_vm_user_update) を使用して、`myResourceGroup` にある `myVM` という名前の VM で、`myUsername` の資格情報を `myPassword` に指定された値にリセットします。 実際の値を次のように使用します。

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

SSH キー認証を使用している場合は、特定のユーザーの SSH キーをリセットできます。 次の例では、**az vm access set-linux-user** を使用して、`myResourceGroup` 内の `myVM` という名前の VM で、`myUsername` という名前のユーザー用に `~/.ssh/id_rsa.pub` に保存された SSH キーを更新します。 実際の値を次のように使用します。

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="use-the-vmaccess-extension"></a>VMAccess 拡張機能を使用する
Linux 用の VM アクセス拡張機能は、実行するアクションを定義する json ファイルを読み取ります。これらのアクションには、SSHD のリセット、SSH キーのリセット、またはユーザーの追加が含まれます。 Azure CLI を使用して VMAccess 拡張機能を呼び出すこともできますが、json ファイルは、必要であれば複数の VM で再利用できます。 この方法では、特定のシナリオで呼び出すことができる json ファイルのリポジトリを作成できます。

### <a name="reset-sshd"></a>SSHD のリセット
次の内容を含む `settings.json` という名前のファイルを作成します。

```json
{  
    "reset_ssh":"True"
}
```

Azure CLI を使用した後、json ファイルを指定して `VMAccessForLinux` 拡張機能を呼び出すことで SSHD 接続をリセットできます。 次の例は、[az vm extension set](/cli/azure/vm/extension#az_vm_extension_set) を使用して、`myResourceGroup` にある `myVM` という名前の VM で SSHD をリセットします。 実際の値を次のように使用します。

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>ユーザーの SSH 資格情報をリセットする
SSHD が正しく機能しているように思える場合は、特定のユーザーの資格情報をリセットできます。 ユーザーのパスワードをリセットするには、`settings.json` という名前のファイルを作成します。 次の例では、`myUsername` の資格情報を、`myPassword` に指定された値にリセットします。 次の行を、実際の値を使用して `settings.json` ファイルに入力します。

```json
{
    "username":"myUsername", "password":"myPassword"
}
```

ユーザーの SSH キーをリセットするには、まず `settings.json` という名前のファイルを作成します。 次の例では、`myUsername` の資格情報を、`myResourceGroup` 内の `myVM` という名前の VM で `myPassword` に指定された値にリセットします。 次の行を、実際の値を使用して `settings.json` ファイルに入力します。

```json
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

json ファイルを作成したら、Azure CLI でその json ファイルを指定して `VMAccessForLinux` 拡張機能を呼び出すことで、SSH ユーザーの資格情報をリセットします。 次の例では、`myResourceGroup` 内の `myVM` という名前の VM の資格情報をリセットします。 実際の値を次のように使用します。

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

## <a name="use-the-azure-cli-10"></a>Azure CLI 1.0 を使用する
まだインストールしていない場合は、 [Azure CLI 1.0 をインストールし、Azure サブスクリプションに接続します](../../cli-install-nodejs.md)。 次のコマンドを実行して、確実に Resource Manager モードを使用するようにします。

```azurecli
azure config mode arm
```

カスタム Linux ディスク イメージを作成してアップロードしている場合は、[Microsoft Azure Linux Agent](../extensions/agent-windows.md) のバージョン 2.0.5 以降がインストールされていることを確認します。 ギャラリー イメージを使用して作成された VM の場合、このアクセス拡張機能は既にインストールされ、自動的に構成されています。

### <a name="reset-ssh-configuration"></a>SSH 構成をリセットする
SSHD 構成自体が正しく構成されていない、またはサービスでエラーが発生した可能性があります。 SSHD をリセットして、SSH 構成自体が有効かを確認します。 最初に実行する必要があるトラブルシューティング手順は、SSHD をリセットすることです。

次の例では、`myResourceGroup` という名前のリソース グループ内にある `myVM` という名前の VM の SSHD をリセットします。 実際の VM とリソース グループ名を次のように使用します。

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>ユーザーの SSH 資格情報をリセットする
SSHD が正しく機能しているように思える場合は、特定のユーザーのパスワードをリセットできます。 次の例では、`myUsername` の資格情報を、`myResourceGroup` 内の `myVM` という名前の VM で `myPassword` に指定された値にリセットします。 実際の値を次のように使用します。

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --user-name myUsername --password myPassword
```

SSH キー認証を使用している場合は、特定のユーザーの SSH キーをリセットできます。 次の例では、`myResourceGroup` 内の `myVM`という名前の VM で、`myUsername` という名前のユーザー用に `~/.ssh/id_rsa.pub` に保存された SSH キーを更新します。 実際の値を次のように使用します。

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --user-name myUsername --ssh-key-file ~/.ssh/id_rsa.pub
```


## <a name="restart-a-vm"></a>VM の再起動
SSH 構成とユーザーの資格情報をリセットした場合、またはその際にエラーが発生した場合は、根本的なコンピューティングの問題に対処するために VM の再起動を試すことができます。

### <a name="azure-portal"></a>Azure ポータル
Azure Portal を使用して VM を再起動するには、VM を選択し、**[再起動]** ボタンをクリックします (次の例を参照)。

![Azure Portal で VM を再起動する](./media/troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli-10"></a>Azure CLI 1.0
次の例では、`myResourceGroup` という名前のリソース グループ内にある `myVM` という名前の VM を再起動します。 実際の値を次のように使用します。

```azurecli
azure vm restart --resource-group myResourceGroup --name myVM
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
次の例では、[az vm restart](/cli/azure/vm#az_vm_restart) を使用して、`myResourceGroup` という名前のリソース グループ内にある `myVM` という名前の VM を再起動します。 実際の値を次のように使用します。

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```


## <a name="redeploy-a-vm"></a>VM を再デプロイする
Azure 内で VM を別のノードに再デプロイすると、基になるネットワーク問題を修正する場合があります。 VM の再デプロイについては、「[新しい Azure ノードへの仮想マシンの再デプロイ](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。

> [!NOTE]
> この操作を行うと、一時ディスクのデータが失われ、仮想マシンに関連付けられている動的 IP アドレスが更新されます。
> 
> 

### <a name="azure-portal"></a>Azure ポータル
Azure Portal を使用して VM を再デプロイするには、VM を選択し、**[サポート + トラブルシューティング]** セクションまで下にスクロールします。 **[再デプロイ]** ボタンをクリックします (次の例を参照)。

![Azure Portal で VM を再デプロイする](./media/troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli-10"></a>Azure CLI 1.0
次の例では、`myResourceGroup` という名前のリソース グループ内にある `myVM` という名前の VM を再デプロイします。 実際の値を次のように使用します。

```azurecli
azure vm redeploy --resource-group myResourceGroup --name myVM
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
次の例では、[az vm redeploy](/cli/azure/vm#az_vm_redeploy) を使用して、`myResourceGroup` という名前のリソース グループ内にある `myVM` という名前の VM を再デプロイします。 実際の値を次のように使用します。

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>クラシック デプロイ モデルを使用して作成された VM
クラシック デプロイ モデルを使用して作成された VM の一般的な SSH 接続エラーを解決するには、次の手順を試してください。 各手順を実行した後、VM に再接続してみてください。

* [Azure Portal](https://portal.azure.com) からリモート アクセスをリセットします。 Azure Portal で、VM を選択し **[Reset Remote... (リモートのリセット...)**] ボタンをクリックします。
* VM を再起動します。 [Azure Portal](https://portal.azure.com) で、VM を選択し、**[再起動]** ボタンをクリックします。
    
* 仮想マシンを新しい Azure ノードに VM を再デプロイします。 VM の再デプロイ方法については、「[新しい Azure ノードへの仮想マシンの再デプロイ](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。
  
    この操作を行うと、一時ディスクのデータが失われ、仮想マシンに関連付けられている動的 IP アドレスが更新されます。
* [Linux ベースの仮想マシンのパスワードまたは SSH をリセットする方法](classic/reset-access-classic.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) の指示に従って、以下の操作を行います。
  
  * パスワードまたは SSH キーをリセットする
  * *sudo* ユーザー アカウントを作成する
  * SSH 構成をリセットします。
* プラットフォームの問題の有無について VM のリソースの状態を確認します。<br>
     VM を選択し、**[設定]** > **[正常性チェック]** まで下にスクロールします。

## <a name="additional-resources"></a>その他のリソース
* 上記の手順を実行しても VM に SSH 接続できない場合は、「[SSH の詳細なトラブルシューティング手順](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照して、問題を解決するための追加の手順を確認します。
* アプリケーションへのアクセスのトラブルシューティングに関する詳細については、「[Linux Azure 仮想マシンにおけるアプリケーション接続の問題のトラブルシューティング](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。
* クラシック デプロイ モデルを使用して作成された仮想マシンのトラブルシューティングの詳細については、 [Linux ベースの仮想マシンのパスワードまたは SSH をリセットする方法](classic/reset-access-classic.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)に関するページを参照してください。

