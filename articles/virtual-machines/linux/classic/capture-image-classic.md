---
title: Linux VM のイメージをキャプチャする | Microsoft Docs
description: クラシック デプロイ モデルで作成された Linux ベースの Azure の仮想マシン (VM) のイメージをキャプチャする方法について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 17d7ffee-a58e-4290-9de1-64c3cf1ddc05
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: cynthn
ms.openlocfilehash: ae87af45ffc442c0de6c7f703694a994e536cdb8
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37929214"
---
# <a name="how-to-capture-a-classic-linux-virtual-machine-as-an-image"></a>従来の Linux 仮想マシンをイメージとしてキャプチャする方法
> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイメントでは、リソース マネージャー モデルを使用することをお勧めします。 [Resource Manager モデルを使用してこれらの手順を実行する](../capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)方法について説明します。
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

ここでは、Linux を実行する従来の Azure 仮想マシン (VM) をキャプチャして、他の仮想マシンを作成する際にイメージとして使用する方法を示します。 このイメージには、VM に接続された OS ディスクやデータ ディスクが含まれます。 ネットワーク構成は含まれないため、イメージから他の VM を作成するときは、ネットワーク構成を行う必要があります。

Azure では、イメージは **[イメージ]** に格納されます。アップロードしたすべてのイメージがこの場所に格納されます。 イメージの詳細については、「[Azure の仮想マシン イメージについて][About Virtual Machine Images in Azure]」を参照してください。

## <a name="before-you-begin"></a>開始する前に
これらの手順は、既にクラシック デプロイ モデルを使用して Azure VM を作成し、データ ディスクの接続を含め、オペレーティング システムの構成が完了していることを前提としています。 VM を作成する必要がある場合は、[Linux 仮想マシンを作成する方法][How to Create a Linux Virtual Machine]に関する記事をお読みください。

## <a name="capture-the-virtual-machine"></a>仮想マシンをキャプチャする
1. 任意の SSH クライアントを使用して、[VM に接続](../mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)します。
2. SSH のウィンドウで、次のコマンドを入力します。 `waagent` からの出力は、このユーティリティのバージョンによって若干異なる場合があります。

    ```bash
    sudo waagent -deprovision+user
    ```

    前のコマンドは、システムをクリーンアップし、再プロビジョニングに適した状態にします。 この操作では次のタスクが実行されます。

   * すべての SSH ホスト キーの削除 (構成ファイルで Provisioning.RegenerateSshHostKeyPair が 'y' の場合)
   * /etc/resolv.conf 内のネームサーバー構成の消去
   * /etc/shadow の `root` ユーザーのパスワードの削除 (構成ファイルで Provisioning.DeleteRootPassword が "y" の場合)
   * キャッシュされた DHCP クライアントのリースの削除
   * ホスト名を localhost.localdomain にリセット
   * (/var/lib/waagent から取得した) 前回プロビジョニングされたユーザー アカウント **および関連データ**の削除

     > [!NOTE]
     > プロビジョニング解除では、イメージを "汎用化" するためにファイルとデータが削除されます。 このコマンドは、必ず新しいイメージ テンプレートとしてキャプチャする VM 上で実行してください。 プロビジョニング解除により、イメージからすべての機密情報が削除されることや、イメージが第三者への再配布に適した状態になることが保証されるわけではありません。

3. 「 **y** 」と入力して続行します。 `-force` パラメーターを追加すると、この確認手順を省略できます。
4. 「 **Exit** 」と入力して、SSH クライアントを閉じます。

   > [!NOTE]
   > 残りの手順は、クライアント コンピューターに既に [Azure CLI がインストールされている](../../../cli-install-nodejs.md) ことを前提としています。 次の手順はすべて、[Azure Portal](http://portal.azure.com) でも実行できます。

5. クライアント コンピューターから Azure CLI を開き、Azure サブスクリプションにログインします。 詳細については、「 [Connect to an Azure subscription from the Azure CLI (Azure CLI から Azure サブスクリプションへの接続)](/cli/azure/authenticate-azure-cli)」を参照してください。

   > [!NOTE]
   > Azure Portal で、ポータルにログインします。

6. サービス管理モードであることを確認します。

    ```azurecli
    azure config mode asm
    ```

7. 既にプロビジョニングが解除された VM をシャットダウンします。 次の例では、`myVM` という名前の VM をシャットダウンします。

    ```azurecli
    azure vm shutdown myVM
    ```
   必要に応じて、`azure vm list` を使用してサブスクリプションで作成されたすべての VM の一覧を表示できます。

   > [!NOTE]
   > Azure Portal を使用している場合は、VM を選択し、**[停止]** をクリックしてその VM をシャットダウンします。

8. VM が停止しているときにイメージをキャプチャします。 次の例では、`myVM` という名前の VM をキャプチャし、`myNewVM` という名前の一般化されたイメージを作成します。

    ```azurecli
    azure vm capture -t myVM myNewVM
    ```

    `-t` サブコマンドによって、元の仮想マシンが削除されます。

    > [!NOTE]
    > Azure Portal で、ハブ メニューから **[イメージ]** を選択してイメージをキャプチャできます。 イメージについて、名前、リソース グループ、位置情報、オペレーティング システムの種類、およびストレージ BLOB パスを指定する必要があります。

9. 新しいイメージが、新しい VM の構成に使用できるイメージの一覧に表示されるようになりました。 次のコマンドを実行すると、新しいイメージを表示できます。

   ```azurecli
   azure vm image list
   ```

   [Azure Portal](http://portal.azure.com) で、**[コンピューティング]** サービスに属する **[VM イメージ (クラシック)]** に新しいイメージが表示されます。 **[VM イメージ (クラシック)]** にアクセスするには、Azure サービス一覧の上部にある **[すべてのサービス]** をクリックし、**[Compute]** サービスを参照します。   

   ![イメージのキャプチャの成功](./media/capture-image/VMCapturedImageAvailable.png)

## <a name="next-steps"></a>次の手順
イメージを使用して VM を作成する準備ができました。 Azure CLI コマンド `azure vm create` を使用し、作成したイメージの名前を指定することができます。 詳細については、[クラシック デプロイ モデルでの Azure CLI の使用](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)に関する記事をご覧ください。

また、[Azure Portal](http://portal.azure.com) を使用して、**[イメージ]** メソッドを使用し、作成したイメージを選択することで、カスタム VM を作成することもできます。 詳細については、[カスタム VM を作成する方法][How to Create a Custom Virtual Machine]に関する記事をご覧ください。

**関連項目:** [Azure Linux エージェント ユーザー ガイド](../../extensions/agent-linux.md)

[About Virtual Machine Images in Azure]:../../virtual-machines-linux-classic-about-images.md
[How to Create a Custom Virtual Machine]:create-custom-classic.md
[How to Attach a Data Disk to a Virtual Machine]:attach-disk-classic.md
[How to Create a Linux Virtual Machine]:create-custom-classic.md
