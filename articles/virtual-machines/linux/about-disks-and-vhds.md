---
title: Microsoft Azure Linux VM 用の非管理対象ディスク ストレージ (ページ BLOB) とマネージド ディスク ストレージについて | Microsoft Docs
description: Azure での Linux 仮想マシン用の非管理対象ディスク ストレージ (ページ BLOB) とマネージド ディスク ストレージの基本について説明します。
services: virtual-machines-linux,storage
author: roygara
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.topic: article
ms.date: 11/15/2017
ms.author: rogarana
ms.component: disks
ms.openlocfilehash: feb3e60ee1b43ec85c81912fbce086858bb33742
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39715932"
---
# <a name="about-disks-storage-for-azure-linux-vms"></a>Azure Linux VM 用のディスク ストレージについて
Azure の仮想マシンでは、その他のコンピューターとまったく同じように、オペレーティング システム、アプリケーション、およびデータを格納するための場所としてディスクを使用します。 Azure のすべての仮想マシンには、Linux オペレーティング システム ディスクと一時ディスクの少なくとも 2 つのディスクがあります。 オペレーティング システム ディスクはイメージから作成されます。オペレーティング システム ディスクとイメージの両方は仮想ハード ディスク (VHD) であり、Azure のストレージ アカウントに格納されます。 仮想マシンでは 1 つ以上のデータ ディスクも保持することができ、これらも VHD として格納されます。

この記事では、ディスクのさまざまな使用方法について説明し、作成および使用できるディスクの種類を示します。 この記事は、 [Windows 仮想マシン](../windows/about-disks-and-vhds.md)にも利用できます。

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="disks-used-by-vms"></a>VM で使用されるディスク

ディスクは VM でどのように使用されるのでしょうか。

## <a name="operating-system-disk"></a>オペレーティング システム ディスク

どの仮想マシンにも 1 つのオペレーティング システム ディスクが取り付けられています。 これは SATA ドライブとして登録され、既定で /dev/sda のラベルが付けられます。 このディスクの最大容量は 2,048 ギガバイト (GB) です。

## <a name="temporary-disk"></a>一時ディスク

各 VM には、一時ディスクが含まれています。 一時ディスクは、アプリケーションとプロセスのための一時的なストレージを提供し、ページ ファイルやスワップ ファイルなどのデータのみを格納するためのものです。 一時ディスクのデータは、[メンテナンス イベント](../windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime)中、または [VM の再デプロイ](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)時に失われる可能性があります。 VM の標準的な再起動中は、一時ドライブのデータは保持されます。 ただし、新しいホストへの移動などでデータが保持されない場合があります。 したがって、一時ドライブのデータをシステムに不可欠なデータとして使用しないでください。

Linux 仮想マシンでは、ディスクは通常 **/dev/sdb** であり、Azure Linux エージェントにより書式設定され、**/mnt** にマウントされます。 仮想マシンのサイズに基づいて、一時ディスクのサイズは異なります。 詳細については、「[Linux 仮想マシンのサイズ](../windows/sizes.md)」をご覧ください。

Azure による一時ディスクの使用方法については、「 [Understanding the temporary drive on Microsoft Azure Virtual Machines](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="data-disk"></a>データ ディスク

データ ディスクは仮想マシンに取り付けられる VHD であり、ユーザーが保存しておく必要があるアプリケーションなどのデータを格納するためのものです。 データ ディスクは SCSI ドライブとして登録され、ユーザーが選択した文字のラベルが付けられます。 各ディスクの最大容量は 4,095 GB です。 仮想マシンのサイズにより、そこに取り付けできるデータ ディスクの数と、ディスクをホストするために使用できるストレージの種類が決まります。

> [!NOTE]
> 仮想マシンの容量については、「[Azure の Linux 仮想マシンのサイズ](./sizes.md)」を参照してください。

ユーザーがイメージから仮想マシンを作成するときに、Azure はオペレーティング システム ディスクを作成します。 ユーザーがデータ ディスクを含むイメージを使用する場合、Azure は仮想マシンの作成時にデータ ディスクも作成します。 それ以外の場合は、仮想マシンを作成した後にデータ ディスクを追加してください。

仮想マシンにディスクを**取り付ける**ことで、ユーザーは仮想マシンにデータ ディスクをいつでも追加できます。 ユーザーがアップロードした VHD やストレージ アカウントにコピーした VHD を使用できます。または Azure が作成する VHD も使用できます。 データ ディスクを取り付けると、VHD ファイルが VM に関連付けられます。VHD で "リース" を設定しておけば、仮想マシンに取り付けている間にストレージから削除することはできなくなります。

[!INCLUDE [storage-about-vhds-and-disks-windows-and-linux](../../../includes/storage-about-vhds-and-disks-windows-and-linux.md)]

## <a name="troubleshooting"></a>トラブルシューティング
[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>次の手順
* [ディスクのアタッチ](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) による VM 用のストレージの追加。
* [スナップショットの作成](snapshot-copy-managed-disk.md)。
* 
  [マネージド ディスクへの変換](convert-unmanaged-to-managed-disks.md)。

