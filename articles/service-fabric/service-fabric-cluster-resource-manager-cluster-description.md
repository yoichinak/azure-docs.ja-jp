---
title: クラスター リソース マネージャー クラスターの説明 | Microsoft Docs
description: 障害ドメイン、アップグレード ドメイン、ノードのプロパティ、ノード容量をクラスター リソース マネージャーに指定し、Service Fabric クラスターを記述します。
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 55f8ab37-9399-4c9a-9e6c-d2d859de6766
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 13ee238580d645f3e727090bc0e0275b36bdb225
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2018
ms.locfileid: "34208813"
---
# <a name="describing-a-service-fabric-cluster"></a>Service Fabric クラスターの記述
Service Fabric クラスター リソース マネージャーには、クラスターを記述するためのメカニズムが複数用意されています。 クラスター リソース マネージャーは、実行時にこの情報を利用することで、クラスターで実行されているサービスの高可用性を確保します。 クラスター リソース マネージャーはこれらの重要なルールを適用すると同時に、クラスター内のリソース消費量の最適化も試みます。

## <a name="key-concepts"></a>主要な概念
クラスター リソース マネージャーでサポートされているクラスターを記述する機能には次のようなものがあります。

* 障害ドメイン
* アップグレード ドメイン
* ノードのプロパティ
* ノード容量

## <a name="fault-domains"></a>障害ドメイン
障害ドメインとは、障害について調整されている領域です。 1 台のコンピューターは 1 つの障害ドメインになります (電源障害、ドライブ障害、問題のある NIC ファームウェアなど、さまざまな理由から単独で停止する可能性があるためです)。 同じイーサネット スイッチに接続されたコンピューターは同じ障害ドメインにあり、これらのコンピューターは単一の電源または単一の場所を共有しています。 ハードウェア障害は当然重複するため、障害ドメインは本質的に階層的であり、Service Fabric での URI として表されます。

障害ドメインは正しく設定する必要があります。この情報はサービスを安全に配置するために Service Fabric によって使用されるためです。 Service Fabric では、(なんらかのコンポーネントのエラーによって生じた) 障害ドメインの損失によってサービスが停止するようなサービスの配置は望ましくありません。 Azure 環境では、環境によって提供される障害ドメイン情報を利用して、Service Fabric がユーザーに代わってクラスター内のノードを正しく構成します。 Service Fabric スタンドアロンの場合、障害ドメインはクラスターの設定時に定義されます。 

> [!WARNING]
> Service Fabric に提供される障害ドメイン情報が正確であることが重要です。 たとえば、Service Fabric クラスターのノードが 10 個の仮想マシン内で実行され、5 個の物理ホストで実行されているとします。 この場合、仮想マシンが 10 個ある場合でも、(最上位の) 障害ドメインは 5 個のみです。 物理ホストで障害が発生すると、VM では調整による障害が発生するため、同じ物理ホストを共有すると、VM は同じルート障害ドメインを共有することになります。  
>
> Service Fabric は、ノードの障害ドメインが変わらないことを想定しています。 VM の高可用性を確保する他のメカニズム ([HA-VM](https://technet.microsoft.com/library/cc967323.aspx) など) では、あるホストから別のホストへと VM の透過的な移行を使用するため、Service Fabric との競合が発生する場合があります。 このようなメカニズムは、VM 内で実行されているコードの再構成または通知を行いません。 そのため、Service Fabric クラスターを実行する環境としては**サポートされません**。 採用する高可用性テクノロジは、Service Fabric のみにすることをお勧めします。 VM のライブ移行、SAN などメカニズムは必要ありません。 これらのメカニズムと Service Fabric と組み合わせて使用すると、アプリケーションの可用性と信頼性は_低くなります_。これは、新たな複雑さが増え、障害の集中する場所が増え、Service Fabric と競合する信頼性と可用性の戦略が利用されるためです。 
>
>

次の図では、障害ドメインに属するすべてのエンティティに色が付けられ、その結果の異なる障害ドメインがすべて表示されています。 この例には、データセンター ("DC")、ラック ("R")、ブレード ("B") があります。 各ブレードが複数の仮想マシンを保持している場合は、障害ドメイン階層に別のレイヤーが存在することがあります。

<center>
![障害ドメインで構成されるノード][Image1]
</center>

Service Fabric クラスター リソース マネージャーは、実行時にクラスター内の障害ドメインを認識し、レイアウトを計画します。 別個の障害ドメインに属するように、特定のサービスのステートフル レプリカまたはステートレス インスタンスは分散されます。 サービスを障害ドメイン全体に分散することで、どの階層レベルで障害ドメインで障害が発生しても、サービスの可用性は損なわれなくなります。

Service Fabric のクラスター リソース マネージャーにとって、障害ドメイン階層に存在するレイヤーの数は重要ではありません。 しかし、クラスター リソース マネージャーは、階層の一部の損失がその階層で実行されているサービスに影響しないようにします。 

障害ドメイン階層の各レベルのノード数を同じにすることをお勧めします。 クラスター内の障害ドメインの "ツリー" が不均衡な場合、クラスター リソース マネージャーによるサービスの最適な割り当ての計算が困難になります。 障害ドメインのレイアウトが不均衡の場合、一部のドメインの損失がサービスの可用性に与える影響が、他のドメインの損失によるものよりも大きくなることを意味します。 その結果、クラスター リソース マネージャーは 2 つの目的の間で板挟みになります。その目的とは、サービスを配置する際にこの "重たい" ドメインのコンピューターを使用することと、ドメインの損失によって問題が生じないように他のドメインにサービスを配置することです。 

不均衡なドメインとはどのようなものでしょうか。 下の図では、異なる 2 つのクラスター レイアウトを示しています。 最初の例では、ノードは障害ドメイン全体で均等に分散されています。 2 つ目の例では、1 つの障害ドメインに、他の障害ドメインよりも多くのノード数があります。 

<center>
![異なる 2 つのクラスター レイアウト][Image2]
</center>

Azure では、ノードが含まれる障害ドメインの選択は自動的に行われます。 しかし、プロビジョニングするノードの数によっては、障害ドメインのノードの数に偏りが生じる可能性があります。 たとえば、クラスターに障害ドメインが 5 つあるときに、特定の NodeType のノードを 7 つプロビジョニングするとします。 この場合、最初の 2 つの障害ドメインではノード数が多くなります。 その後さらに、少数のインスタンスでより多くの NodeType をデプロイすると、問題は悪化します。 この理由から、各ノードの種類のノード数は、障害ドメイン数の倍数にすることをお勧めします。

## <a name="upgrade-domains"></a>アップグレード ドメイン
アップグレード ドメインも、Service Fabric クラスター リソース マネージャーがクラスターのレイアウトを把握できる機能です。 アップグレード ドメインは、同時にアップグレードされるノードのセットを定義します。 アップグレード ドメインを使用すると、クラスター リソース マネージャーは、アップグレードなどの管理操作を把握し、調整することができます。

アップグレード ドメインは障害ドメインに似ていますが、重要な相違点がいくつかあります。 まず、障害ドメインは、調整されるハードウェア障害の領域で定義されます。 一方、アップグレード ドメインはポリシーで定義されます。 自分で希望の数を決定します。環境によって決定されるわけではありません。 ノードと同数のアップグレード ドメインを持つこともできます。 障害ドメインとアップグレード ドメインのもう 1 つの違いは、アップグレード ドメインが階層ではない点です。 アップグレード ドメインは単純なタグに似ています。 

次の図では、3 つのアップグレード ドメインが 3 つの障害ドメインに分散してストライピングされているようすが示されています。 また、ステートフル サービスの 3 つの異なるレプリカの配置例も示されています。これらのレプリカはそれぞれ、異なる障害ドメインとアップグレード ドメインに含まれています。 この配置であれば、サービスのアップグレードの最中に障害ドメインが 1 つ失われても問題はなく、コードとデータのコピーが 1 つ残ります。  

<center>
![障害ドメインとアップグレード ドメインの配置][Image3]
</center>

アップグレード ドメインの数を増やすことは良くもあり、悪くもあります。 アップグレード ドメインを増やすと、アップグレードの各手順が細かくなるため、影響を受けるノードまたはサービスの数が少なくなります。 その結果、一度に移動する必要のあるサービスは減少し、システムに対する変更も少なくなります。 アップグレード中に発生した問題の影響が及ぶサービスが減少するため、これは信頼性の向上に寄与する傾向にあります。 アップグレード ドメインを増やすと、アップグレードの影響に対処するために必要とされる、他のノードにおける利用可能なバッファーも減少します。 たとえば、アップグレード ドメインが 5 つある場合、各アップグレード ドメイン内のノードではトラフィックの約 20% が処理されています。 アップグレードのためにそのアップグレード ドメインを停止する必要がある場合、通常、その負荷は他のドメインで処理される必要があります。 残りのアップグレード ドメインは 4 つなので、それぞれに合計トラフィックの約 5% の空き領域が必要です。 アップグレード ドメイン数を増やすと、クラスター内のノードの必要なバッファーが減ります。 たとえば、10 個のアップグレード ドメインがある場合を考えてみましょう。 この場合、各 UD は合計トラフィックの約 10% のみを処理します。 アップグレードがクラスターをステップ実行する場合、各ドメインに必要な空き領域は、合計トラフィックの約 1.1% のみです。 一般的に、アップグレード ドメイン数を多くすると、必要な予約容量が少なくなるため、高い使用率でノードを実行できます。 障害ドメインの場合にも同じことが言えます。  

アップグレード ドメインを増やすことの欠点は、アップグレード時間が長くなる傾向があることです。 Service Fabric は、アップグレード ドメインが完了し、チェックを実行してから、次回のアップグレードを開始するまでの短時間を待機します。 このような遅延があるので、アップグレードによって導入された問題を検出してから、アップグレードを進めることができます。 正しくない変更が一度に多くのサービスに影響を及ぼすのを防ぐことができるため、このトレードオフは許容されています。

アップグレード ドメインが少なすぎると、負の副作用が多数あります。各アップグレード ドメインを停止してアップグレードしている間は、容量全体の大部分を使用できません。 たとえば、アップグレード ドメインが 3 つしかない場合は、サービスまたはクラスター容量全体の約 3 分の 1 が一度に停止します。 ワークロードを処理するために残りのクラスターの容量を十分確保する必要があるので、そのようにサービスの多くの部分を一度に停止することは望ましくありません。 そのバッファーを維持すると、通常の操作時に、他の場合よりも読み込まれるノード数が少なくなります。 その結果、サービスの実行コストが増えます。

環境内の障害ドメインまたはアップグレード ドメインの合計数に制限はありません。また、重複の制約もありません。 ただし、一般的なパターンはいくつかあります。

- 1 対 1 でマップされる障害ドメインとアップグレード ドメイン
- ノードごとに 1 つのアップグレード ドメイン (物理または仮想 OS インスタンス)
- "ストライプ" または "マトリックス" モデル (障害ドメインとアップグレード ドメインがマトリックスを形成。通常はコンピューターが表中で対角線を描くように配置される)

<center>
![障害ドメインとアップグレード ドメインのレイアウト][Image4]
</center>

それぞれのレイアウトに長所と短所があり、どれがベストであるかを断言することはできません。 たとえば、1FD 対 1UD モデルは設定が簡単です。 1 ノードあたり 1 アップグレード ドメインのモデルは、よく見られる構成に似ています。 アップグレード時は、各ノードが独立して更新されます。 これは、過去に少数のコンピューターを手動でアップグレードしていた方法と似ています。 

最も一般的なモデルは、FD/UD マトリックスです。このモデルでは、FD と UD がテーブルを形成し、ノードが対角線に沿って配置されます。 これは、Azure の Service Fabric クラスターの既定で使用されるモデルです。 ノード数が多いクラスターの場合、最終的に、上の密度の高いマトリックス パターンのようになります。

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>障害ドメインとアップグレード ドメインの制約および結果の動作
### <a name="default-approach"></a>*既定の方法*
既定では、クラスター リソース マネージャーは障害ドメインとアップグレード ドメインの間でサービスのバランスを維持します。 これは[制約](service-fabric-cluster-resource-manager-management-integration.md)としてモデル化されています。 障害ドメインとアップグレード ドメインの制約は次のことを表します。"特定のサービス パーティションについて、階層の同じレベルにある 2 つのドメインでのサービス オブジェクト (ステートレス サービス インスタンスまたはステートフル サービス レプリカ) 数の差が 1 より大きくなってはならない"。 この制約は "最大限の差" を保証するものと考えることができます。 障害ドメインとアップグレード ドメインの制約は、上記の規則に違反する特定の移動または配置をできないようにします。 

1 つ例を見てみましょう。 たとえば、6 つのノードを持つクラスターを、5 つの障害ドメインと 5 つのアップグレード ドメインで構成したとします。

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

*構成 1*

TargetReplicaSetSize (ステートレス サービスの場合は InstanceCount) が 5 のサービスを作成するとします。 レプリカを N1 ～ N5 に配置します。 実際は、このようなサービスをいくつ作成しても N6 が使用されることはありません。 しかし、なぜでしょうか。 現在のレイアウトと N6 を選択した場合のレイアウトの差異を見てみましょう。

現在のレイアウトと障害ドメインおよびアップグレード ドメインごとのレプリカの合計数は次のようになります。

|  | FD0 | FD1 | FD2 | FD3 | FD4 | 総 UD 数 |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

*レイアウト 1*


このレイアウトは、障害ドメインとアップグレード ドメインごとのノードという点でバランスが取れています。 障害ドメインとアップグレード ドメインごとのレプリカ数という点でもバランスが取れています。 各ドメインのノード数、レプリカ数は同じです。

次に、N2 ではなく N6 を使用した場合にどうなるかを見てみましょう。 レプリカはどのように分散されるでしょうか。

|  | FD0 | FD1 | FD2 | FD3 | FD4 | 総 UD 数 |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

*レイアウト 2*


このレイアウトは、障害ドメインの制約の "最大限の差" の保証という定義に違反しています。 FD0 には 2 つのレプリカがある一方で FD1 にはレプリカがありません。FD0 と FD1 の差は合計 2 になります。これは、最大限の差である 1 を超えています。 制約に違反しているため、この配置はクラスター リソース マネージャーによって許可されません。 同様に (N1 と N2 ではなく) N2 と N6 を選択すると、次のようになります。

|  | FD0 | FD1 | FD2 | FD3 | FD4 | 総 UD 数 |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

*レイアウト 3*


障害ドメインの観点からは、このレイアウトはバランスがとれています。 ただし、現在は、レプリカの数が UD0 の 0 個に対して UD1 は 2 個なので、アップグレード ドメインの制約に違反しています。 そのため、このレイアウトも無効であり、クラスター リソース マネージャーで選択されません。

ステートフル レプリカまたはステートレス インスタンスの分散に対するこの方法は、可能な最高のフォールト トレランスを提供します。 1 つのドメインがダウンした場合、最小限の数のレプリカ/インスタンスが失われます。 

その一方で、この方法は厳しすぎて、クラスターがすべてのリソースを利用できない可能性があります。 特定のクラスター構成では、特定のノードを使用できません。 これにより、Service Fabric がサービスを配置しなくなり、警告メッセージが発生する可能性があります。 前の例では、一部のクラスター ノードを使用できません (例では N6)。 そのクラスター (N7 – N10) にノードを追加した場合でも、障害ドメインとアップグレード ドメインの制約のため、レプリカ/インスタンスは N1 – N5 のみに配置されます。 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |

*構成 2*


### <a name="alternative-approach"></a>*他の方法*

クラスター リソース マネージャーがサポートする別のバージョンの障害ドメインとアップグレード ドメインの制約を使用すると、最低レベルの安全性を保証しながら配置することができます。 障害ドメインとアップグレード ドメインの代わりの制約は、"特定のサービス パーティションについて、ドメイン間へのレプリカの分散は、パーティションでクォーラムの損失が発生しないことを保証しなければならない" というものです。 この制約は "クォーラム セーフ" を保証するものと考えることができます。 

> [!NOTE]
>ステートフル サービスの場合、"*クォーラム損失*" は、パーティション レプリカの過半数が同時にダウンしている状況と定義されます。 たとえば、TargetReplicaSetSize が 5 の場合、任意の 3 個のレプリカのセットがクォーラムを表します。 同様に、TargetReplicaSetSize が 6 の場合は、クォーラムには 4 個のレプリカが必要です。 どちらのケースも、パーティションが正常な機能を続けたい場合、3 個以上のレプリカが同時にダウンすることはできません。 ステートレス サービスの場合は、インスタンスの過半数が同時にダウンした場合でもステートレス サービスは正常に機能し続けるので、"*クォーラム損失*" のようなものはありません。 そのため、以降ではステートフル サービスに焦点を当てます。
>

前に示した例に戻りましょう。 "クォーラム セーフ" バージョンの制約では、示されている 3 つのレイアウトすべてが有効です。 これは、2 番目のレイアウトの FD0 または 3 番目のレイアウトの UD1 で障害が発生しても、パーティションにはまだクォーラムがある (レプリカの過半数がまだアップしている) ためです。 制約のこのバージョンでは、ほぼ常に N6 が利用される可能性があります。

柔軟性については、"クォーラム セーフ" アプローチの方が "最大限の差" アプローチより優れています。これは、ほぼすべてのクラスター トポロジで有効なレプリカの分散を簡単に検索できるためです。 ただし、このアプローチは、一部の障害が他の障害より悪いため、最善のフォールト トレランス特性を保証できません。 最悪のシナリオでは、1 つのドメインとさらに 1 つのレプリカの障害により、レプリカの過半数が失われる可能性があります。 たとえば、5 個のレプリカまたはインスタンスでクォーラムを損失するために必要な障害は、3 つではなく、2 つだけです。 

### <a name="adaptive-approach"></a>*アダプティブ アプローチ*
どちらのアプローチにも長所と短所があるため、これら 2 つの戦略を組み合わせたアダプティブ アプローチが導入されました。

> [!NOTE]
>Service Fabric バージョン 6.2 以降では、これが既定の動作になります。 
>
アダプティブ アプローチでは、"最大限の差" ロジックが既定で使用され、必要な場合にのみ "クォーラム セーフ" ロジックに切り替わります。 クラスター リソース マネージャーは、クラスターとサービスの構成方法を調べることで、必要な戦略を自動的に判別します。 特定のサービスについて、"*TargetReplicaSetSize が障害ドメインおよびアップグレード ドメインの数で割り切れ、**かつ**、ノードの数が (障害ドメインの数) * (アップグレード ドメインの数) と等しいかそれより少ない場合、クラスター リソース マネージャーはそのサービスに対して "クォーラム ベース" のロジックを使用する必要があります*"。 クォーラム損失はステートレス サービスには関係ありませんが、クラスター リソース マネージャーはステートレス サービスとステートフル サービスの両方にこのアプローチを使用することに注意してください。

前の例に戻り、クラスターに 8 個のノードがあるものとします (クラスターはまだ、5 個の障害ドメインと 5 個のアップグレード ドメインで構成され、そのクラスターでホストされているサービスの TargetReplicaSetSize は 5 のままです)。 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

*構成 3*

すべての必要な条件が満たされているため、クラスター リソース マネージャーは "クォーラム ベース" のロジックを利用してサービスを分散させます。 これにより、N6 – N8 を使用できるようになります。 このケースで可能なサービスの分散の 1 つは、次のようなものです。

|  | FD0 | FD1 | FD2 | FD3 | FD4 | 総 UD 数 |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

*レイアウト 4*

サービスの TargetReplicaSetSize が (たとえば) 4 に下がった場合、クラスター リソース マネージャーはその変更を認識し、TargetReplicaSetSize が FD および UD の数で割り切れなくなったため、"最大限の差" ロジックの使用を再開します。 その結果、障害ドメインとアップグレード ドメインの "最大限の差" バージョンのロジックに違反しないように、ノード N1-N5 の残り 4 個のレプリカを分散させるため、特定のレプリカ移動が発生します。 

4 番目のレイアウトと TargetReplicaSetSize が 5 の場合を振り返ってみます。 N1 がクラスターから削除された場合、アップグレード ドメインの数は 4 になります。 サービスの TargetReplicaSetSize が UD の数で割り切れなくなったため、やはり、クラスター リソース マネージャーは "最大限の差" ロジックの使用を開始します。 その結果、レプリカ R1 が再び作成された場合は、障害ドメインとアップグレード ドメインの制約に違反しないように、N4 に配置する必要があります。

|  | FD0 | FD1 | FD2 | FD3 | FD4 | 総 UD 数 |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |該当なし |該当なし |該当なし |該当なし |該当なし |該当なし |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

*レイアウト 5*

## <a name="configuring-fault-and-upgrade-domains"></a>障害ドメインとアップグレード ドメインの構成
障害ドメインとアップグレード ドメインは、Azure でホストされる Service Fabric デプロイで自動的に定義されます。 Service Fabric によって Azure から環境情報が取得されて使用されます。

独自のクラスターを作成する (または開発で特定のトポロジを実行する) 場合は、障害ドメインとアップグレード ドメインの情報を自分で入力することになります。 この例では、(それぞれ 3 つのラックを備えた) 3 つの "データセンター" にまたがる、ノードが 9 つのローカル開発クラスターを定義します。 このクラスターには、それらの 3 つのデータセンターにストライピングされる 3 つのアップグレード ドメインもあります。 以下は構成例です。 

ClusterManifest.xml

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one-box /one single server -->
    <WindowsServer IsScaleMin="true">
      <NodeList>
        <Node NodeName="Node01" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType01" FaultDomain="fd:/DC01/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node02" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType02" FaultDomain="fd:/DC01/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node03" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType03" FaultDomain="fd:/DC01/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node04" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType04" FaultDomain="fd:/DC02/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node05" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType05" FaultDomain="fd:/DC02/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node06" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType06" FaultDomain="fd:/DC02/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node07" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType07" FaultDomain="fd:/DC03/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node08" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType08" FaultDomain="fd:/DC03/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node09" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType09" FaultDomain="fd:/DC03/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
      </NodeList>
    </WindowsServer>
  </Infrastructure>
```

スタンドアロン デプロイの ClusterConfig.json 経由

```json
"nodes": [
  {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm3",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm4",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm5",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm6",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm7",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm8",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm9",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD3"
  }
],
```

> [!NOTE]
> Azure Resource Manager でクラスターを定義すると、Azure によって障害ドメインとアップグレード ドメインが割り当てられます。 そのため、Azure Resource Manager テンプレートのノードの種類と仮想マシン スケール セットの定義には、障害ドメインまたはアップグレード ドメインの情報が含まれません。
>

## <a name="node-properties-and-placement-constraints"></a>ノードのプロパティと配置の制約
場合によっては (実際には、ほとんどの場合)、特定のワークロードが、クラスター内の特定のノードの種類だけで確実に実行されるようにしたいことがあります。 たとえば、ワークロードの中に GPU や SSD を必要とするものとしないものが混在している場合があります。 特定のワークロードをターゲットにするハードウェアの好例は、ほぼすべての n 層アーキテクチャです。 一部のコンピューターは、アプリケーションのフロント エンドまたは API 提供側として機能し、クライアントまたはインターネットに公開されます。 (多くの場合異なるハードウェア リソースを備えた) 複数のコンピューターが、コンピューティングまたはストレージ レイヤーの作業を処理します。 通常、これらはクライアントまたはインターネットに直接_公開されません_。 Service Fabric では、特定のワークロードを特定のハードウェア構成で実行する必要が生じる状況があると想定されています。 例: 

* 既存の n 層アプリケーションが Service Fabric 環境に "リフト アンド シフト (移行)" された。
* パフォーマンス、スケール、またはセキュリティの分離の理由により、ワークロードを特定のハードウェアで実行する必要がある。
* ポリシーまたはリソース消費の理由により、特定のワークロードを他のワークロードから切り離す必要がある。

こうした構成をサポートするために、Service Fabric には、ノードに適用できるタグという優れた概念があります。 これらのタグは、**ノードのプロパティ**と呼ばれます。 **配置の制約**は、1 つまたは複数のノードのプロパティに選択される個々のサービスに接続されるステートメントです。 配置の制約で、サービスを実行する場所を定義します。 一連の制約は拡張でき、任意のキー/値のペアを使用できます。 

<center>
![クラスター レイアウトの異なるワークロード][Image5]
</center>

### <a name="built-in-node-properties"></a>組み込みのノードのプロパティ
Service Fabric では、自動的に適用できる既定のノード プロパティがいくつか定義されています。これらのプロパティについては、ユーザーが定義する必要はありません。 **NodeType** と **NodeName** が、既定のプロパティとして各ノードで定義されています。 したがって、たとえば配置の制約は `"(NodeType == NodeType03)"` と記述できます。 一般的には、NodeType プロパティが最もよく使用されるプロパティの 1 つです。 コンピューターの種類と 1 対 1 で対応するため、便利です。 コンピューターの各種類は、従来の n 層アプリケーションのワークロードの種類と対応しています。

<center>
![配置の制約とノードのプロパティ][Image6]
</center>

## <a name="placement-constraint-and-node-property-syntax"></a>配置の制約とノードのプロパティの構文 
ノード プロパティで指定する値には、string、bool、signed long を使用できます。 サービスを実行できるクラスターの場所が制約されるため、サービスのステートメントは配置の "*制約*" と呼ばれます。 制約として使用できるのは、クラスター内の別のノード プロパティに対して実行される任意のブール ステートメントです。 これらのブール ステートメントでの有効なセレクターは次のとおりです。

1) 特定のステートメントを作成するための条件確認

| ステートメント | 構文 |
| --- |:---:|
| "等しい" | "==" |
| "等しくない" | "!=" |
| "より大きい" | ">" |
| "以上" | ">=" |
| "より小さい" | "<" |
| "以下" | "<=" |

2) グループ化と論理演算のブール ステートメント

| ステートメント | 構文 |
| --- |:---:|
| "および" | "&&" |
| "または" | "&#124;&#124;" |
| "ではない" | "!" |
| "1 つのステートメントとしてグループ化" | "()" |

基本的な制約ステートメントの例をいくつか紹介します。

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

配置の制約ステートメント全体の評価が "True" のノードにのみ、サービスを配置することができます。 プロパティが定義されていないノードは、そのプロパティを含む配置の制約とは一致しません。

たとえば、特定のノード タイプに対して次のノード プロパティが定義されているとします。

ClusterManifest.xml

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

スタンドアロン デプロイの ClusterConfig.json 経由または Azure でホストされたクラスターの Template.json 経由。 

> [!NOTE]
> 通常、Azure Resource Manager テンプレートでは、ノードの種類はパラメーター化されています。 そのため、"NodeType01" ではなく "[parameters('vmNodeType1Name')]" のようになります。
>

```json
"nodeTypes": [
    {
        "name": "NodeType01",
        "placementProperties": {
            "HasSSD": "true",
            "NodeColor": "green",
            "SomeProperty": "5"
        },
    }
],
```

サービスに対して、次のようにサービスの配置の "*制約*" を作成できます。

C#

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasSSD == true && SomeProperty >= 4)";
// add other required servicedescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

NodeType01 のすべてのノードが有効な場合、制約が "(NodeType == NodeType01)" のノードの種類を選択することもできます。

サービスの配置の制約で優れているのは、実行時に動的に更新される点です。 そのため、必要に応じて、クラスター内でのサービスの移動や、要件の追加/削除などを行うことができます。このような変更が行われる場合でも、Service Fabric によって、サービスは確実に稼働し続け、利用することができます。

C#:

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

Powershell:

```posh
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

配置の制約は、異なる名前付きサービス インスタンスごとに指定されます。 更新すると、以前に指定された内容は常に置き換えられます (上書きされます)。

クラスター定義で、ノードのプロパティを定義します。 ノードのプロパティを変更するには、クラスター構成のアップグレードが必要です。 ノードのプロパティをアップグレードするには、影響を受ける各ノードを再起動して新しいプロパティを報告する必要があります。 Service Fabric はこのようなローリング アップグレードを管理します。

## <a name="describing-and-managing-cluster-resources"></a>クラスター リソースの説明と管理
クラスター内のリソース消費量の管理は、オーケストレーターの重要なジョブの 1 つです。 クラスター リソースの管理は、2 つの異なることを意味する可能性があります。 1 つ目は、コンピューターがオーバーロードにならないようにすることです。 つまり、コンピューターが処理できる限度を超えてサービスを実行しないようにすることです。 2 つ目は、サービスを効率的に実行するために重要な分散と最適化です。 コスト効率またはパフォーマンス重視のサービス プランでは、一部のノードをホット ノードにして他のノードはコールド ノードにするということができません。 ホット ノードはリソースの競合とパフォーマンスの低下を招き、コールド ノードは無駄なリソースであり、コストも増加するからです。 

Service Fabric はリソースを `Metrics` として表します。 メトリックとは、Service Fabric に対して記述する論理または物理リソースです。 たとえば、"WorkQueueDepth"、"MemoryInMb" などがメトリックです。 Service Fabric でノードを制御できる物理リソースの詳細については、「[リソース ガバナンス](service-fabric-resource-governance.md)」を参照してください。 カスタム メトリックの構成と使用の詳細については、[こちらの記事](service-fabric-cluster-resource-manager-metrics.md)を参照してください

メトリックは、配置の制約ともノード プロパティとも異なります。 ノードのプロパティは、ノード自体の静的な記述子です。 メトリックは、ノードが持っているリソースと、ノード上で実行されているときにサービスが消費するリソースを表します。 ノード プロパティは "HasSSD" にすることができるほか、true または false に設定できます。 その SSD の使用可能な容量とサービスによって消費される容量は、"DriveSpaceInMb" などのメトリックになります。 

配置の制約とノード プロパティの場合と同様に、メトリックの名前が意味する内容は Service Fabric クラスター リソース マネージャーによって認識されないことに注意が必要です。 メトリックの名前は単なる文字列です。 あいまいな場合は作成したメトリック名の一部として単位を宣言することをお勧めします。

## <a name="capacity"></a>容量
すべてのリソースの "*分散*" をオフにしたとしても、Service Fabric クラスター リソース マネージャーは容量超過のノードが出ないように調整します。 クラスターの容量がいっぱいの場合や、ワークロードがノードよりも大きい場合でなければ、容量超過の管理は可能です。 容量とは、ノードにどのくらいのリソースがあるかを理解するためにクラスター リソース マネージャーが使用するもう 1 つの "*制約*" です。 容量の残りもクラスター全体で追跡されます。 このサービス レベルの容量と消費量の両方が、メトリックで表現されます。 たとえば、メトリックが "ClientConnections" であり、特定のノードで "ClientConnections" の容量が 32,768 だとします。 他のノードには他の制限がある可能性があります。そのノードで実行されている一部のサービスは 32,256 のメトリック "ClientConnections" を消費している、と言うことができます。

実行中に、クラスター リソース マネージャーはクラスターとノードの残容量を追跡します。 クラスター リソース マネージャーは容量を追跡するために、サービスが実行されているノードの容量から、各サービスの使用量を差し引きます。 Service Fabric クラスター リソース マネージャーは、ノードが容量超過にならないように、この情報を使用してレプリカを配置または移動する場所を検討します。

<center>
![クラスター ノードと容量][Image7]
</center>

C#:

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription metric = new ServiceLoadMetricDescription();
metric.Name = "ClientConnections";
metric.PrimaryDefaultLoad = 1024;
metric.SecondaryDefaultLoad = 0;
metric.Weight = ServiceLoadMetricWeight.High;
serviceDescription.Metrics.Add(metric);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ClientConnections,High,1024,0)
```

クラスター マニフェストで定義した容量を確認できます。

ClusterManifest.xml

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

スタンドアロン デプロイの ClusterConfig.json 経由または Azure でホストされたクラスターの Template.json 経由。 

```json
"nodeTypes": [
    {
        "name": "NodeType03",
        "capacities": {
            "ClientConnections": "65536",
        }
    }
],
```

通常、サービスの負荷は動的に変化します。 たとえば、レプリカの "ClientConnections" の負荷が 1,024 から 2,048 に変化しましたが、そのときに実行されていたノードでは、そのメトリックの残容量がわずか 512 だとします。 このとき、このノードには十分な空き領域がないため、レプリカまたはインスタンスの配置は無効になります。 クラスター リソース マネージャーが作動してノードの容量超過が抑制されます。 その結果、そのノードの 1 つまたは複数のレプリカまたはインスタンスが他のノードに移動され、容量を超過していたノードの負荷が軽減されます。 クラスター リソース マネージャーは、レプリカを移動する際に、これらの移動にかかるコストを最小限に抑えようとします。 移動コストについては、[こちらの記事](service-fabric-cluster-resource-manager-movement-cost.md)を参照してください。クラスター リソース マネージャーの再調整戦略とルールについては、[こちら](service-fabric-cluster-resource-manager-metrics.md)を参照してください。

## <a name="cluster-capacity"></a>クラスターの容量
Service Fabric のクラスター リソース マネージャーでは、クラスター全体がいっぱいの状態にならないように、どのような処理が行われているでしょうか。 動的負荷では、あまりできることがありません。 サービスにかかる負荷は、クラスター リソース マネージャーの動作に関係なく急増する場合があります。 そのため、今日クラスターに余裕があっても、明日には有名になって力不足になる可能性があります。 とは言うものの、問題を回避するために制御できることがいくつかあります。 まず、クラスターがいっぱいになる原因となるワークロードが新しく作成されないようにします。

たとえば、ステートレス サービスを作成し、それに関連する負荷がある程度あるとします。 このサービスでは "DiskSpaceInMb" メトリックが使用されるとします。 また、サービスのインスタンスごとに 5 ユニットの "DiskSpaceInMb" が消費されます。 作成するサービス インスタンスは 3 つです。 これでセットアップは終了です。 つまり、これらのサービス インスタンスを作成するには、クラスターに 15 ユニットの "DiskSpaceInMb" が必要です。 クラスター リソース マネージャーは各メトリックの容量と消費量を継続的に計算するので、クラスターの残りの容量を判断できます。 十分な領域がない場合、クラスター リソース マネージャーはサービス作成の呼び出しを拒否します。

要件は 15 ユニットが使用できることのみであるため、この領域はさまざまな方法で割り当てることができます。 たとえば、異なる 15 個のノードそれぞれに 1 つずつユニットが残っていても、5 つの異なるノードに 3 つずつユニットが残っていてもかまいません。 3 つのノードに 5 つの利用可能なユニットがある状態になるように配置し直せる場合、クラスター リソース マネージャーはサービスを配置します。 クラスターの空きがほぼない場合や、既存のサービスを何らかの理由で統合できない場合を除き、通常、クラスターの再配置は可能です。

## <a name="buffered-capacity"></a>バッファーの容量
バッファーの容量も、クラスター リソース マネージャーの機能です。 この機能を使用して、ノード容量全体のうち、一部を予約できます。 この容量バッファーは、アップグレード時やノード障害の発生時でもサービスを配置する目的でのみ使用されます。 バッファーの容量は、すべてのノードのメトリックについてグローバルに指定されます。 予約容量に選択する値は、クラスターにある障害ドメインとアップグレード ドメインの数の関数です。 障害ドメインとアップグレード ドメインを増やすと、バッファーの容量により小さい数を指定できます。 ドメインを増やすと、アップグレード時と障害時に使用できないクラスターの量が少なくなると期待できます。 バッファーの容量の指定は、メトリックに対してノード容量を指定した場合にのみ有効です。

バッファー容量を指定する方法の例を次に示します。

ClusterManifest.xml

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

スタンドアロン デプロイの ClusterConfig.json 経由または Azure でホストされたクラスターの Template.json 経由:

```json
"fabricSettings": [
  {
    "name": "NodeBufferPercentage",
    "parameters": [
      {
          "name": "SomeMetric",
          "value": "0.15"
      },
      {
          "name": "SomeOtherMetric",
          "value": "0.20"
      }
    ]
  }
]
```

メトリックのバッファーの容量がクラスターに不足している場合、新しいサービスの作成は失敗します。 バッファーを保持する新しいサービスを作成しないことで、アップグレードと障害によってノードの容量を超過することはなくなります。 バッファーの容量はオプションですが、メトリックの容量を定義するクラスターでは使用することをお勧めします。

クラスター リソース マネージャーには、この負荷情報が表示されます。 各メトリックについて、次の情報が表示されます。 
  - バッファーの容量の設定
  - 合計容量
  - 現在の消費量
  - 各メトリックの均衡がとれていると見なされるかどうか
  - 標準偏差に関する統計情報
  - 最も負荷が高いノードと最も負荷が低いノード  
  
その出力の例を次に示します。

```posh
PS C:\Users\user> Get-ServiceFabricClusterLoadInformation
LastBalancingStartTimeUtc : 9/1/2016 12:54:59 AM
LastBalancingEndTimeUtc   : 9/1/2016 12:54:59 AM
LoadMetricInformation     :
                            LoadMetricName        : Metric1
                            IsBalancedBefore      : False
                            IsBalancedAfter       : False
                            DeviationBefore       : 0.192450089729875
                            DeviationAfter        : 0.192450089729875
                            BalancingThreshold    : 1
                            Action                : NoActionNeeded
                            ActivityThreshold     : 0
                            ClusterCapacity       : 189
                            ClusterLoad           : 45
                            ClusterRemainingCapacity : 144
                            NodeBufferPercentage  : 10
                            ClusterBufferedCapacity : 170
                            ClusterRemainingBufferedCapacity : 125
                            ClusterCapacityViolation : False
                            MinNodeLoadValue      : 0
                            MinNodeLoadNodeId     : 3ea71e8e01f4b0999b121abcbf27d74d
                            MaxNodeLoadValue      : 15
                            MaxNodeLoadNodeId     : 2cc648b6770be1bc9824fa995d5b68b1
```

## <a name="next-steps"></a>次の手順
* クラスター リソース マネージャー内のアーキテクチャと情報フローについては、[こちらの記事](service-fabric-cluster-resource-manager-architecture.md)をご確認ください
* 最適化メトリックの定義は、負荷を分散するのではなく、ノードで統合する方法の 1 つです。最適化を構成する方法については、 [この記事](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
* 最初から開始して、 [Service Fabric クラスター リソース マネージャーの概要を確認するにはこちらを参照してください](service-fabric-cluster-resource-manager-introduction.md)
* クラスター リソース マネージャーでクラスターの負荷を管理し、分散するしくみについては、 [負荷分散](service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
