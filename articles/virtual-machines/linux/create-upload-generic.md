---
title: Azure 上での Linux VHD の作成とアップロード
description: Linux オペレーティング システムを格納した Azure 仮想ハード ディスク (VHD) を作成してアップロードする方法について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: d351396c-95a0-4092-b7bf-c6aae0bbd112
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: 17b4df83b141d5365a8d6244c4ab73b0eba5ed73
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972351"
---
# <a name="information-for-non-endorsed-distributions"></a>動作保証外のディストリビューションに関する情報
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Azure プラットフォームの SLA は、[動作保証済みディストリビューション](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)のいずれか 1 つを使用した場合にのみ、Linux OS を実行する仮想マシンに適用されます。 このような動作保証済みディストリビューションの場合、必要な構成が行われた Linux イメージが Azure Marketplace で提供されています。

* [Azure での動作保証済み Linux ディストリビューション](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Microsoft Azure での Linux イメージのサポート](https://support.microsoft.com/kb/2941892)

Azure 上で動作するすべてのディストリビューションは、プラットフォーム上で適切に実行できるように、いくつかの前提条件を満たす必要があります。  この記事は決して包括的なものではありません。前提条件はディストリビューションによって異なるためです。次に示している条件をすべて満たす場合でも、プラットフォーム上で適切に動作するように Linux システムを微調整する必要があります。

この理由から、できるだけ [Azure での動作保証済み Linux ディストリビューション](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) で開始することをお勧めします。 次の記事では、Azure でサポートされる以下のさまざまな動作保証済み Linux ディストリビューションを準備する方法について説明します。

* **[CentOS ベースのディストリビューション](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES と openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

以降では、Azure 上で Linux ディストリビューションを実行するための一般的なガイダンスについて、重点的に説明します。

## <a name="general-linux-installation-notes"></a>Linux のインストールに関する一般的な注記
* VHDX 形式は Azure ではサポートされていません。サポートされるのは **固定 VHD** のみです。  Hyper-V マネージャーまたは convert-vhd コマンドレットを使用して、ディスクを VHD 形式に変換できます。 VirtualBox を使用する場合は、ディスクの作成時に、既定で動的に割り当てられるサイズではなく、**固定サイズ**を選択することを意味します。
* Azure は世代 1 の仮想マシンのみサポートします。 第 1 世代の仮想マシンを、VHDX ファイル形式から VHD ファイル形式に、容量可変から容量固定ディスクに変換できます。 ただし、仮想マシンの世代を変更することはできません。 詳細については、「[Should I create a generation 1 or 2 virtual machine in Hyper-V? (Hyper-V で第 1 世代または第 2 世代の仮想マシンを作成する必要はありますか?)](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)」を参照してください。
* VHD のサイズの上限は、1,023 GB です。
* Linux システムをインストールする場合は、LVM (通常、多くのインストールで既定) ではなく標準パーティションを使用することを*お勧めします*。 これにより、特に OS ディスクをトラブルシューティングのために別の同じ VM に接続する必要がある場合に、LVM 名と複製された VM の競合が回避されます。 [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) または [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) をデータ ディスク上で使用できます。
* UDF ファイル システムをマウントするためのカーネル サポートが必要です。 Azure での最初の起動時に、ゲストに接続されている UDF でフォーマットされたメディアを介して、プロビジョニング構成が Linux VM に渡されます。 Azure Linux エージェントは、その構成を読み取り、VM をプロビジョニングする UDF ファイル システムをマウントできる必要があります。
* 2.6.37 未満の Linux カーネル バージョンは、HYPER-V で大きい VM サイズの NUMA をサポートできません。 この問題は、主に、アップストリームの Red Hat 2.6.32 カーネルを使用した古いディストリビューションに影響し、RHEL 6.6 (kernel-2.6.32-504) で修正されました。 2.6.37 より古いカスタム カーネルまたは2.6.32-504 より古い RHEL ベースのカーネルを実行しているシステムでは、grub.conf のカーネル コマンドラインで、ブート パラメーター `numa=off` を設定する必要があります。 詳細については、Red Hat [KB 436883](https://access.redhat.com/solutions/436883) を参照してください。
* OS ディスクにスワップ パーティションを構成しないでください。 Linux エージェントは、一時的なリソース ディスク上にスワップ ファイルを作成するよう構成できます。  このことに関する詳細については、次の手順を参照してください。
* Azure の VHD の仮想サイズはすべて、1 MB にアラインメントさせる必要があります。 未フォーマット ディスクから VHD に変換するときに、変換する前の未フォーマット ディスクのサイズが 1 MB の倍数であることを確認する必要があります。 詳細については、以下の手順を参照してください。

### <a name="installing-kernel-modules-without-hyper-v"></a>Hyper-V を使用しないカーネル モジュールのインストール
Azure は、Hyper-V ハイパーバイザーで実行されるため、Linux で Azure を実行するには特定のカーネル モジュールがインストールされている必要があります。 Hyper-V 以外で作成された VM を所有している場合、Linux インストーラーは、Linux が Hyper-V 環境で実行されていることを検出しない限り、初期 RAM ディスク (initrd または initramfs) に Hyper-V 用のドライバーを含まない場合があります。 別の仮想化システム (Virtualbox、KVM など) を使用して Linux イメージを準備する場合は、少なくとも `hv_vmbus` と `hv_storvsc` のカーネル モジュールを初期 RAM ディスクで確実に使用できるように initrd の再構築が必要になる場合があります。  これは少なくとも、アップ ストリームの Red Hat ディストリビューションに基づくシステムの既知の問題です。

initrd または initramfs イメージの再構築のためのメカニズムは、ディストリビューションによって異なる場合があります。 適切な手順については、使用しているディストリビューションのドキュメントまたはサポートを参照してください。  次は、 `mkinitrd` ユーティリティを使用して initrd を再構築する方法の 1 つの例です。

まず、既存の initrd イメージをバックアップします。

    # cd /boot
    # sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak

次に、`hv_vmbus` と `hv_storvsc` のカーネル モジュールを使用して initrd を再構築します。

    # sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`


### <a name="resizing-vhds"></a>VHD のサイズ変更
Azure の VHD イメージは、1 MB に整列された仮想サイズが必要です。  通常、HYPER-V を使用して作成された VHD は既に正しく整列されている必要があります。  VHD が正しく整列されていない場合は、VHD から *イメージ* を作成しようとすると、次のようなエラー メッセージが表示される場合があります。

    "The VHD http://<mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd has an unsupported virtual size of 21475270656 bytes. The size must be a whole number (in MBs).”

この動作を修正するには、HYPER-V マネージャー コンソールまたは [Resize-VHD](http://technet.microsoft.com/library/hh848535.aspx) Powershell コマンドレットを使用して、VM のサイズを変更できます。  Windows 環境で実行していない場合は、qemu-img を使用して変換し (必要な場合)、VHD のサイズを変更することをお勧めします。

> [!NOTE]
> qemu-img のバージョン 2.2.1 以降には VHD が適切にフォーマットされないというバグがあることがわかっています。 この問題は QEMU 2.6 で修正されています。 qemu-img 2.2.0 以前を使用するか、2.6 以降に更新することをお勧めします。 https://bugs.launchpad.net/qemu/+bug/1490611 を参照してください。
> 
> 

1. `qemu-img` や `vbox-manage` などのツールを使用して直接 VHD のサイズを変更すると、VHD が起動できなくなる可能性があります。  そのため、最初に VHD を RAW ディスク イメージに変換することをお勧めします。  既に VM イメージを RAW ディスク イメージとして作成している場合は (KVM などの一部のハイパーバイザーでは既定)、この手順を省略できます。
   
       # qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw

2. ディスク イメージの必要なサイズを計算して、仮想サイズが 1 MB に整列されていることを確認します。  次の bash シェル スクリプトは、これを行うのに役立ちます。  このスクリプトは "`qemu-img info`" を使用してディスク イメージの仮想サイズを決定し、次の 1 MB までサイズを計算します。
   
       rawdisk="MyLinuxVM.raw"
       vhddisk="MyLinuxVM.vhd"
   
       MB=$((1024*1024))
       size=$(qemu-img info -f raw --output json "$rawdisk" | \
              gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
   
       rounded_size=$((($size/$MB + 1)*$MB))
       echo "Rounded Size = $rounded_size"

3. 上記のスクリプトのセットとして $rounded_size を使用して RAW ディスクのサイズを変更します。
   
       # qemu-img resize MyLinuxVM.raw $rounded_size

4. 次に、RAW ディスク を固定サイズの VHD に変換します。
   
       # qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd

   または、qemu のバージョン **2.6 以降**を使用して `force_size` オプションを含めます。

       # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc MyLinuxVM.raw MyLinuxVM.vhd

## <a name="linux-kernel-requirements"></a>Linux カーネルの要件
Hyper-V および Azure 用の Linux Integration Services (LIS) ドライバーは、アップストリームの Linux カーネルに直接提供されています。 最新の Linux カーネル バージョン (つまり 3.x) を含む多くのディストリビューションでこれらのドライバーが含まれています。含まれていない場合は、これらのドライバーのバックポートされたバージョンがカーネルと共に提供されます。  これらのドライバーは、アップストリームのカーネル内で新しい修正と機能を含んだ形で常に更新されているため、可能な場合は、これらの修正と更新を含む[動作保証済みディストリビューション](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)を実行することをお勧めします。

Red Hat Enterprise Linux バージョン **6.0-6.3**の変形を実行する場合は、Hyper-V 用の最新の LIS ドライバーをインストールする必要があります。 必要なドライバーは [この場所](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409)で見つかります。 RHEL **6.4 以上** (および派生版) では、LIS ドライバーは既にカーネルに含まれているため、Azure 上でこれらのシステムを実行するために追加のインストール パッケージは必要ありません。

カスタム カーネルが必要な場合は、最新のカーネル バージョン (つまり **3.8 以上**) の使用をお勧めします。 独自のカーネルを維持するディストリビューションまたはベンダーの場合は、アップストリームのカーネルの LIS ドライバーをカスタム カーネルに定期的にバックポートするために多少の手間がかかります。  比較的最新のカーネル バージョンを既に実行している場合でも、LIS ドライバーのアップストリームの修正を常に追跡し、必要に応じてパックポートすることを強くお勧めします。 LIS ドライバーのソース ファイルの場所は、次の Linux カーネルのソース ツリーの [MAINTAINERS](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) ファイルに記載されています。

    F:    arch/x86/include/asm/mshyperv.h
    F:    arch/x86/include/uapi/asm/hyperv.h
    F:    arch/x86/kernel/cpu/mshyperv.c
    F:    drivers/hid/hid-hyperv.c
    F:    drivers/hv/
    F:    drivers/input/serio/hyperv-keyboard.c
    F:    drivers/net/hyperv/
    F:    drivers/scsi/storvsc_drv.c
    F:    drivers/video/fbdev/hyperv_fb.c
    F:    include/linux/hyperv.h
    F:    tools/hv/

最低限、以下のパッチが適用されていない場合は、Azure 上で問題が発生することがわかっているため、これらのパッチをカーネルに含める必要があります。 この一覧は、すべてのディストリビューションに対して包括的でも完全でもありません。

* [ata_piix: 既定でディスクを Hyper-V ドライバーに従うようにする](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
* [storvsc: RESET パスの転送中パケットを正しく考慮する](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
* [storvsc: WRITE_SAME の使用を回避する](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
* [storvsc: RAID と仮想ホスト アダプター ドライバーの WRITE SAME を無効にする](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
* [storvsc: NULL ポインターの逆参照の修正](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
* [storvsc: リング バッファーのエラーにより I/O の凍結が発生する可能性がある](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
* [scsi_sysfs: __scsi_remove_device の二重実行を保護する](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)

## <a name="the-azure-linux-agent"></a>Azure Linux エージェント
[Azure Linux エージェント](../extensions/agent-linux.md) (waagent) は、Azure で Linux 仮想マシンを適切にプロビジョニングするために必要です。 [Linux Agent GitHub リポジトリ](https://github.com/Azure/WALinuxAgent)で、最新バージョンの取得、問題の報告、プル リクエストの提出が行うことができます。

* Linux エージェントは、Apache 2.0 ライセンス下でリリースされています。 数多くのディストリビューションでは、このエージェント用の RPM パッケージや deb パッケージを既に提供しているため、場合によっては、ほとんど労力をかけずにこのエージェントをインストールし、更新することができます。
* Azure Linux エージェントには、Python v2.6 以上が必要です。
* このエージェントでは、python-pyasn1 モジュールも必要です。 ほとんどのディストリビューションでは、インストール可能な個別のパッケージとして提供しています。
* Azure Linux エージェントは NetworkManager と互換性がない場合があります。 ディストリビューションによって提供される多くの RPM/Deb パッケージでは、NetworkManager が waagent パッケージに対する競合として構成されるため、Linux エージェント パッケージをインストールすると NetworkManager がアンインストールされます。
* Azure Linux エージェントは、サポートされている最小バージョンを超えている必要があります。[詳細](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)については、こちらの記事を参照してください。

## <a name="general-linux-system-requirements"></a>Linux システムの一般的な要件

* GRUB または GRUB2 でカーネルのブート行を変更して次のパラメーターを含めます。 これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。
  
        console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
  
    これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。
  
    上記のほかに、次のパラメーターを *削除* することをお勧めします (ある場合)。
  
        rhgb quiet crashkernel=auto
  
    クラウド環境では、すべてのログをシリアル ポートに送信するため、グラフィカル ブートおよびクワイエット ブートは役立ちません。 必要に応じて `crashkernel` オプションは構成したままにすることができますが、このパラメーターにより、VM 内の使用可能なメモリ量が 128 MB 以上減少するため、比較的小さなサイズの VM では問題となる可能性がある点に注意してください。

* Azure Linux エージェントをインストールします。
  
    Azure Linux エージェントは、Azure で Linux イメージをプロビジョニングするために必要です。  多くのディストリビューションでは、このエージェントを RPM または Deb パッケージとして提供しています (パッケージは、通常 'WALinuxAgent' または 'walinuxagent' と呼ばれます)。  このエージェントは、 [Linux エージェント ガイド](../extensions/agent-linux.md)の手順に従って手動でもインストールできます。

* SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。  通常これが既定です。

* OS ディスクにスワップ領域を作成しないでください。
  
    Azure Linux エージェントは、Azure でプロビジョニングされた後に VM に接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成します。 ローカル リソース ディスクは *一時* ディスクであるため、VM のプロビジョニングが解除されると空になることがあります。 Azure Linux エージェントのインストール後に (前の手順を参照)、/etc/waagent.conf にある次のパラメーターを適切に変更します。
  
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

* 最後の手順として、次のコマンドを実行して仮想マシンをプロビジョニング解除します。
  
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
  
  > [!NOTE]
  > Virtualbox では、'waagent -force -deprovision' の実行後に次のエラーが表示される場合があります: `[Errno 5] Input/output error`。 このエラー メッセージは重要ではないため、無視してかまいません。
  > 
  > 

* 仮想マシンをシャットダウンし、Azure に VHD をアップロードします。

