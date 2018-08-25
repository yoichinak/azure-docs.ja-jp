---
title: Azure Windows VM のサイズ - HPC | Microsoft Docs
description: Azure の Windows ハイ パフォーマンス コンピューティング仮想マシンで使用できるさまざまなサイズを一覧表示します。 このシリーズのストレージのスループットとネットワーク帯域幅に加え、vCPU、データ ディスク、NIC の数に関する情報を一覧表示します。
services: virtual-machines-windows
documentationcenter: ''
author: jonbeck7
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/06/2018
ms.author: jonbeck
ms.openlocfilehash: 31e81741d2a627888e478b3871bdbab4e6b6d6f5
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37902641"
---
# <a name="high-performance-compute-vm-sizes"></a>ハイ パフォーマンス コンピューティング VM のサイズ

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


* **オペレーティング システム** - Windows Server 2016、Windows Server 2012 R2、Windows Server 2012

* **MPI** -Microsoft MPI (MS-MPI) 2012 R2 以降、Intel MPI ライブラリ 5.x

  サポートされている MPI 実装では、インスタンス間の通信に Microsoft Network Direct インターフェイスが使用されます。 

* **RDMA ネットワーク アドレス空間** - Azure の RDMA ネットワークでは、アドレス空間 172.16.0.0/16 は予約済みです。 Azure 仮想ネットワークにデプロイ済みのインスタンスで MPI アプリケーションを実行する場合、仮想ネットワークのアドレス空間が RDMA ネットワークと重複しないようにしてください。

* **HpcVmDrivers VM 拡張機能** - RDMA 対応の VM では、HpcVmDrivers 拡張機能を追加して、RDMA 接続用に Windows ネットワーク デバイス ドライバーをインストールします。 (A8 インスタンスと A9 インスタンスの特定のデプロイでは、HpcVmDrivers 拡張機能は自動的に追加されます)。VM に VM 拡張機能を追加するには、[Azure PowerShell](/powershell/azure/overview) コマンドレットを使用できます。 

  
  次のコマンドは、*West US* リージョン内の *myResourceGroup* という名前のリソース グループにデプロイされた *myVM* という名前の既存の RDMA 対応 VM に、最新バージョン 1.1 の HpcVMDrivers 拡張機能をインストールします。

  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  
  詳しくは、[仮想マシン拡張機能とその機能](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関する記事をご覧ください。 [クラシック デプロイ モデル](classic/manage-extensions.md)にデプロイされている VM にも拡張機能を使用できます。


## <a name="using-hpc-pack"></a>HPC Pack の使用

[Microsoft HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) (Microsoft が無償で提供している HPC クラスターとジョブの管理ソリューション) は、Windows ベースの MPI アプリケーションとその他の HPC ワークロードを実行するコンピューティング クラスターを Azure で作成するための 1 つの選択肢として使用できます。 HPC Pack 2012 R2 以降のバージョンには、RDMA 対応の VM 上にデプロイした場合に Azure RDMA ネットワークを使用する MS-MPI 用のランタイム環境が含まれています。



## <a name="other-sizes"></a>その他のサイズ
- [汎用](sizes-general.md)
- [コンピューティングの最適化](sizes-compute.md)
- [メモリの最適化](../virtual-machines-windows-sizes-memory.md)
- [ストレージの最適化](../virtual-machines-windows-sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [旧世代](sizes-previous-gen.md)

## <a name="next-steps"></a>次の手順

- Windows Server で HPC Pack を使ってコンピューティング集中型インスタンスを使用するためのチェックリストについては、「[HPC Pack を使用して Windows RDMA クラスターをセットアップして MPI アプリケーションを実行する](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)」を参照してください。

- Azure Batch で MPI アプリケーションを実行するときにコンピューティング集中型インスタンスを使用するには、「[Azure Batch でのマルチインスタンス タスクを使用した Message Passing Interface (MPI) アプリケーションの実行](../../batch/batch-mpi.md)」をご覧ください。

- [Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。




