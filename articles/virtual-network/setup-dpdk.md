---
title: Azure Linux VM の DPDK | Microsoft Docs
description: Linux 仮想マシンで DPDK を設定する方法について説明します。
services: virtual-network
documentationcenter: na
author: laxmanrb
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/27/2018
ms.author: labattul
ms.openlocfilehash: 205a1e399eadd268ffaa390a7ebb4397fda9feff
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2018
ms.locfileid: "42444655"
---
# <a name="setup-dpdk-in-a-linux-virtual-machine"></a>Linux 仮想マシンでの DPDK の設定

Azure の Data Plane Development Kit (DPDK) では、仮想マシンのカーネル ネットワーク スタックをバイパスするパフォーマンス集中型アプリケーションに対して、より高速のユーザー空間パケット処理フレームワークを提供します。

カーネル ネットワーク スタックを使用する一般的なパケット処理は、割り込み駆動型です。 ネットワーク インターフェイスが着信したパケットを受信するたびに、カーネル空間からユーザー空間へのパケットとコンテキスト切り替えを処理するカーネル割り込みが発生します。 DPDK では、コンテキスト切り替えと割り込み駆動型の手法を利用せずに、高速パケット処理にポーリング モードを使用したユーザー空間の実装を採用しています。

DPDK は、ハードウェア、論理コア、メモリ管理、およびネットワーク インターフェイス カードのポーリング モード ドライバーなど、下位レベルのリソースへのアクセスを提供しているユーザー空間ライブラリのセットから構成されています。

DPDK は、複数のオペレーティング システムの配布をサポートしている Azure 仮想マシンで実行できます。 DPDK は、仮想ルーター、ファイアウォール、VPN、ロード バランサー、進化したパケット コア、およびサービス拒否型 (DDoS) アプリケーションなどのネットワーク仮想アプライアンス (NVA) の形式で、ネットワーク関数の仮想化の実装を推進するにあたって、重要なパフォーマンス上の差別化を実現します。

## <a name="benefit"></a>長所

**より高速のパケット毎秒 (PPS)**: カーネルをバイパスしてユーザー空間でパケットを制御すると、コンテキスト切り替えが無くなることでサイクル数が減少し、Azure Linux 仮想マシンで毎秒処理されるパケットのレートが向上します。


## <a name="supported-operating-systems"></a>サポートされているオペレーティング システム

Azure ギャラリーの次のディストリビューションがサポートされています。

| Linux OS     | カーネル バージョン        |
|--------------|----------------       |
| Ubuntu 16.04 | 4.15.0-1015-azure     |
| Ubuntu 18.04 | 4.15.0-1015-azure     |
| SLES 15      | 4.12.14-5.5-azure     |
| RHEL 7.5     | 3.10.0-862.9.1.el7    |
| CentOS 7.5   | 3.10.0-862.3.3.el7    |

**カスタムのカーネル サポート**

一覧に示されていない Linux カーネルのバージョンについては、[Azure で調整された Linux カーネルを作成するためのパッチ](https://github.com/microsoft/azure-linux-kernel)に関するページを参照してください。また、詳細情報については、[azuredpdk@microsoft.com](mailto:azuredpdk@microsoft.com) へお問い合わせください。 

## <a name="region-support"></a>リージョンのサポート

すべての Azure リージョンで DPDK をサポートしています。

## <a name="prerequisites"></a>前提条件

Linux 仮想マシン上で高速ネットワークを有効にする必要があります。 仮想マシンは、管理用の 1 つのインターフェイスに加えて、少なくとも 2 つのネットワーク インターフェイスを保持している必要があります。 [高速ネットワークを有効にした Linux 仮想マシンを作成する](create-vm-accelerated-networking-cli.md)方法について説明します。

## <a name="install-dpdk-dependencies"></a>DPDK 依存関係をインストールする

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```bash
sudo add-apt-repository ppa:canonical-server/dpdk-azure -y
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```bash
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="rhel75centos-75"></a>RHEL7.5/CentOS 7.5

```bash
yum -y groupinstall "Infiniband Support"
sudo dracut --add-drivers "mlx4_en mlx4_ib mlx5_ib" -f
yum install -y gcc kernel-devel-`uname -r` numactl-devel.x86_64 librdmacm-devel libmnl-devel
```

### <a name="sles-15"></a>SLES 15

**Azure のカーネル**

```bash
zypper  \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-azure kernel-devel-azure gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

**既定のカーネル**

```bash
zypper \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-default-devel gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

## <a name="setup-virtual-machine-environment-once"></a>仮想マシン環境のセットアップ (1 回のみ)

1. [最新 DPDK をダウンロードします](https://core.dpdk.org/download)。 Azure にはバージョン 18.02 以降が必要です。
2. 最初に、`make config T=x86_64-native-linuxapp-gcc` を使って既定の構成を構築します。
3. `sed -ri 's,(MLX._PMD=)n,\1y,' build/.config` を使って、生成された構成で Mellanox PMD を有効にします。
4. `make` を使ってコンパイルします。
5. `make install DESTDIR=<output folder>` を使ってインストールします。

# <a name="configure-runtime-environment"></a>ランタイム環境を構成する

再起動後に 1 回、次のコマンドを実行します。

1. hugepage

   * すべての numanode に対して 1 回、次のコマンドを実行して hugepage を構成します。

     ```bash
     echo 1024 | sudo tee
     /sys/devices/system/node/node*/hugepages/hugepages-2048kB/nr_hugepages
     ```

   *  `mkdir /mnt/huge` を使って、マウント用のディレクトリを作成します。
   *  `mount -t hugetlbfs nodev /mnt/huge` を使って hugepage をマウントします。
   *  `grep Huge /proc/meminfo` を使って、hugepage が予約されていることを確認します。

     > [!NOTE]
     > DPDK の[手順](http://dpdk.org/doc/guides/linux_gsg/sys_reqs.html#use-of-hugepages-in-the-linux-environment)に従って、hugepage がブートで予約されるように、grub ファイルを変更する方法があります。 ページの下部に手順があります。 Azure Linux 仮想マシンで実行している場合は、複数のリブートに及ぶ hugepage を予約するために、代わりに /etc/config/grub.d 下のファイルを変更してください。

2. MAC および IP アドレス: `ifconfig –a` を使用して、ネットワーク インターフェイスの MAC および IP アドレスを表示します。 *VF* ネットワーク インターフェイスおよび *NETVSC* ネットワーク インターフェイスは、同一の MAC アドレスを保持しますが、*NETVSC* ネットワーク インターフェイスだけが IP アドレスを保持します。 VF インターフェイスは、NETVSC インターフェイスのスレーブ インターフェイスとして実行されています。

3. PCI アドレス

   * `ethtool -i <vf interface name>` を使って *VF* で使用される PCI アドレスを調べます。
   * *eth0* で高度なネットワークを有効化した場合、testpmd が誤って *eth0* の VF pci デバイスを引き継ぐことはありません。 DPDK アプリケーションが誤って管理ネットワーク インターフェイスを引き継ぎ、SSH 接続が失われた場合、シリアル コンソールを使用して DPDK アプリケーションを強制終了するか、仮想マシンを停止または開始します。

4. `modprobe -a ib_uverbs` を使って、各リブートで *ibuverbs* を読み込みます。 SLES 15 の場合のみ、`modprobe -a mlx4_ib` を使って *mlx4_ib* も読み込みます。

## <a name="failsafe-pmd"></a>フェールセーフの PMD

DPDK アプリケーションは、Azure で公開されたフェールセーフの PMD 経由で実行される必要があります。 VF PMD 経由でアプリケーションが直接実行される場合、一部のパケットは統合インターフェイス経由で表示されるので、VM を宛先とする**すべての**パケットが受信されることはありません。 フェールセーフの PMD 経由での実行は、該当のアプリケーションを宛先とするすべてのパケットが、そのアプリケーションで受信されることを保証すると共に、ホストがサービス提供されている場合に VF が取り消されたとしても、アプリケーションが DPDK モードで引き続き実行されることを保証します。 フェール セーフの PMD の詳細については、「[Fail-safe poll mode driver library](http://doc.dpdk.org/guides/nics/fail_safe.html)」(フェールセーフでのポーリング モードのドライバー ライブラリ) を参照してください。

## <a name="run-testpmd"></a>testpmd を実行する

ルート モードで実行するために、*testpmd* コマンドの前に `sudo` を使用します。

### <a name="basic-sanity-check-failsafe-adapter-initialization"></a>基本: サニティ チェック、フェールセーフ アダプターの初期化

1. 次のコマンドを実行して、単一ポートの testpmd アプリケーションを起動します。

   ```bash
   testpmd -w <pci address from previous step> \
     --vdev="net_vdev_netvsc0,iface=eth1" \
     -- -i \
     --port-topology=chained
    ```

2. 次のコマンドを実行して、デュアル ポートの testpmd アプリケーションを起動します。

   ```bash
   testpmd -w <pci address nic1> \
   -w <pci address nic2> \
   --vdev="net_vdev_netvsc0,iface=eth1" \
   --vdev="net_vdev_netvsc1,iface=eth2" \
   -- -i
   ```

   複数の NIC で実行している場合、`--vdev` 引数は `net_vdev_netvsc<id>,iface=<vf’s pairing eth>` のパターンに準拠します。

3.  起動されると、`show port info all` を実行してポート情報をチェックします。 net_failsafe になっている (*net_mlx4* ではない) 1 つまたは 2 つの DPDK ポートを確認する必要があります。
4.  `start <port> /stop <port>` を使用してトラフィックを開始します。

上記のコマンドでは、対話モードで *testpmd* を開始しています。いくつかの testpmd コマンドを試すために、このモードが推奨されています。

### <a name="basic-single-sendersingle-receiver"></a>基本: 単一の送信者/単一の受信者

次のコマンドは、毎秒のパケットの統計情報を定期的に出力します。

1. TX 側で、次のコマンドを実行します。

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device intended to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
   ```

2. RX 側で、次のコマンドを実行します。

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device intended to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=rxonly \
     --eth-peer=<port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
   ```

仮想マシン上で上記のコマンドを実行する場合、コンパイルする前に仮想マシンの実際の IP アドレスと一致するように、`app/test-pmd/txonly.c` の *IP_SRC_ADDR* および *IP_DST_ADDR* を変更します。 これを行わない場合、受信者に到着する前に、パケットが削除されます。

### <a name="advanced-single-sendersingle-forwarder"></a>高度: 単一の送信者/単一の転送者
次のコマンドは、毎秒のパケットの統計情報を定期的に出力します。

1. TX 側で、次のコマンドを実行します。

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device intended to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
    ```

2. FWD 側で、次のコマンドを実行します。

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address NIC1> \
     -w <pci address NIC2> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     --vdev="net_vdev_netvsc<2nd id>,iface=<2nd iface to attach to>" (you need as many --vdev arguments as the number of devices used by testpmd, in this case) \
     -- --nb-cores <number of cores to use for test pmd> \
     --forward-mode=io \
     --eth-peer=<recv port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
    ```

仮想マシン上で上記のコマンドを実行する場合、コンパイルする前に仮想マシンの実際の IP アドレスと一致するように、`app/test-pmd/txonly.c` の *IP_SRC_ADDR* および *IP_DST_ADDR* を変更します。 これを行わない場合、転送者に到着する前に、パケットが削除されます。 *testpmd* 転送者はレイヤー 3 のアドレスを変更しないため、コードに変更を加えない限り、第 3 のマシンで転送されたトラフィックを受信することはできません。

## <a name="references"></a>参照

* [EAL オプション](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#eal-command-line-options)
* [Testpmd コマンド](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#testpmd-command-line-options)
