---
title: Azure Windows VM のサイズ - GPU | Microsoft Docs
description: Azure の Windows 仮想マシンで使用できるさまざまな GPU 最適化済みのサイズを一覧表示します。 このシリーズのストレージのスループットとネットワーク帯域幅に加え、vCPU、データ ディスク、NIC の数に関する情報を一覧表示します。
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
ms.openlocfilehash: ab3ccd981514225a003b07bafbb73db11aaa21e2
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2018
ms.locfileid: "37903732"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>GPU 最適化済み仮想マシンのサイズ

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>サポートされているオペレーティング システムとドライバー

Windows を実行している Azure N シリーズ VM の GPU 機能を利用するには、NVIDIA GPU ドライバーがインストールされている必要があります。 [NVIDIA GPU ドライバー拡張機能](../extensions/hpccompute-gpu-windows.md)は、N シリーズ VM 上に適切な NVIDIA CUDA または GRID ドライバーをインストールします。 この拡張機能は、Azure Portal または Azure PowerShell や Azure Resource Manager テンプレートなどのツールを使用してインストールまたは管理します。 サポートされるオペレーティング システムおよびデプロイ手順については、[NVIDIA GPU ドライバー拡張機能のドキュメント](../extensions/hpccompute-gpu-windows.md)を参照してください。 VM 拡張機能の一般情報については、「[Azure 仮想マシンの拡張機能と機能](../extensions/overview.md)」をご覧ください。

NVIDIA GPU ドライバーを手動でインストールすることを選択した場合、サポートされるオペレーティング システム、ドライバー、インストールおよび検証手順については、[Windows 用 N シリーズ GPU ドライバーのセットアップ](n-series-driver-setup.md)を参照してください。

[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

## <a name="other-sizes"></a>その他のサイズ
- [汎用](sizes-general.md)
- [コンピューティングの最適化](sizes-compute.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [旧世代](sizes-previous-gen.md)

## <a name="next-steps"></a>次の手順
[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。

