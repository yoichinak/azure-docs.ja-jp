---
title: Azure での Linux VM のサイズ | Microsoft Docs
description: Azure の Linux 仮想マシンで使用できるさまざまなサイズを一覧表示します。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: da681171-f045-4c80-a5a9-d8bd47964673
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/06/2018
ms.author: jonbeck
ms.openlocfilehash: 5ee3d29ceada238c5e4a633b501f63ed307ba76e
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2018
ms.locfileid: "37900869"
---
# <a name="sizes-for-linux-virtual-machines-in-azure"></a>Azure の Linux 仮想マシンのサイズ
この記事では、Linux アプリとワークロードの実行に使用できる Azure 仮想マシンに利用可能なサイズとオプションについて説明します。 また、これらのリソースの使用を計画するときに注意する必要のあるデプロイの考慮事項も示します。 この記事は、 [Windows 仮想マシン](../windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)にも利用できます。


| type                     | サイズ           |    説明       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [汎用](sizes-general.md)          | B、Dsv3、Dv3、DSv2、Dv2、Av2  | バランスのとれた CPU 対メモリ比。 テストと開発、小～中規模のデータベース、および低～中程度のトラフィックの Web サーバーに最適です。 |
| [コンピューティングの最適化](sizes-compute.md)        | Fsv2、Fs、F             | 高い CPU 対メモリ比。 トラフィックが中程度の Web サーバー、ネットワーク アプライアンス、バッチ処理、アプリケーション サーバーに適しています。        |
| [メモリの最適化](sizes-memory.md)         | Esv3、Ev3、M、GS、G、DSv2、Dv2  | 高いメモリ対 CPU 比。 リレーショナル データベース サーバー、中～大規模のキャッシュ、およびメモリ内分析に適しています。                 |
| [ストレージの最適化](sizes-storage.md)        | Ls                | 高いディスク スループットと IO。 ビッグ データ、SQL、および NoSQL のデータベースに最適です。                                                         |
| [GPU](sizes-gpu.md)            | NV、NC、NCv2、NCv3、ND            | 負荷の高いグラフィックスのレンダリングやビデオ編集、ディープ ラーニングを使用したモデル トレーニングと推論 (ND) に特化した仮想マシン。 1 つまたは複数の GPU で利用できます。       |
| [ハイ パフォーマンス コンピューティング](sizes-hpc.md) | H       | 高スループットのネットワーク インターフェイス (RDMA) のオプションを備えた、最も高速かつ強力な CPU 仮想マシン。 

<br>

- さまざまなサイズの価格について詳しくは、「 [Virtual Machines の料金](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux)」を参照してください。 
- 各 Azure リージョンで利用可能な VM サイズについては、「 [リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)」を参照してください。
- Azure VM の一般的な制限事項については、「 [Azure サブスクリプションとサービスの制限、クォータ、制約](../../azure-subscription-service-limits.md)」を参照してください。
- [Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。


## <a name="rest-api"></a>REST API

VM サイズを照会するための REST API の使用については、以下を参照してください。

- [サイズ変更に使用可能な仮想マシンを一覧表示](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes)
- [サブスクリプションに使用可能な仮想マシンのサイズを一覧表示](https://docs.microsoft.com/rest/api/compute/virtualmachines/listall)
- [可用性セットに使用可能な仮想マシンのサイズを一覧表示](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。

## <a name="benchmark-scores"></a>ベンチマーク スコア

[CoreMark ベンチマーク スコア](compute-benchmark-scores.md)を使用して、Linux VM の処理性能について学習します。

## <a name="next-steps"></a>次の手順

利用可能な VM のサイズの種類について詳しく説明します。
- [汎用](sizes-general.md)
- [コンピューティングの最適化](sizes-compute.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- A Standard、Dv1 (D1-4 および D11-14 v1)、および A8-A11 シリーズの[旧世代](sizes-previous-gen.md)に関するページを確認してください。



