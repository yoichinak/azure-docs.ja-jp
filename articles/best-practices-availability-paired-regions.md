---
title: 'ビジネス継続性とディザスター リカバリー (BCDR): Azure のペアになっているリージョン | Microsoft Docs'
description: データセンターでの障害発生時にアプリケーションの耐障害性を確保するための Azure のリージョン ペアについて説明します。
author: rayne-wiselman
ms.service: multiple
ms.topic: article
ms.date: 07/03/2018
ms.author: raynew
ms.openlocfilehash: 13a2b78b50b1b10975a90c1da38810f1a62a6bb5
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37436911"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>ビジネス継続性とディザスター リカバリー (BCDR): Azure のペアになっているリージョン

## <a name="what-are-paired-regions"></a>ペアになっているリージョンとは

Azure は、世界中の複数の geo で動作します。 Azure の geo とは、少なくとも 1 つの Azure リージョンを含む、世界の定義済みの地域です。 Azure リージョンは、geo に含まれる領域で、1 つ以上のデータセンターが含まれます。

各 Azure リージョンは、同じ geo 内の別のリージョンと組み合わせて、リージョン ペアにして使用します。 例外はブラジル南部で、geo の外部のリージョンとペアになっています。

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

図 1 – Azure リージョン ペア

| 地理的な場所 | ペアになっているリージョン |  |
|:--- |:--- |:--- |
| アジア |東アジア |東南アジア |
| オーストラリア |オーストラリア東部 |オーストラリア南東部 |
| オーストラリア |オーストラリア中部 |オーストラリア中部 2 |
| ブラジル |ブラジル南部 (2) |米国中南部 |
| カナダ |カナダ中部 |カナダ東部 |
| 中国 |中国 (北部) |中国 (東部)|
| ヨーロッパ |北ヨーロッパ |西ヨーロッパ |
| ドイツ |ドイツ中部 |ドイツ北東部 |
| インド |インド中部 |インド南部 |
| インド |インド西部 (1) |インド南部 |
| 日本 |東日本 |西日本 |
| 韓国 |韓国中部 |韓国南部 |
| 北米 |米国東部 |米国西部 |
| 北米 |米国東部 2 |米国中央部 |
| 北米 |米国中北部 |米国中南部 |
| 北米 |米国西部 2 |米国中西部 
| 英国 |英国西部 |英国南部 |
| 米国国防総省 |米国防総省東部 |米国防総省中部 |
| 米国政府 |米国政府アリゾナ |米国政府テキサス |
| 米国政府 |米国政府アイオワ (3) |米国政府バージニア州 |
| 米国政府 |米国政府バージニア (4) |米国政府テキサス |

表 1 - Azure リージョン ペアの組み合わせ

- (1) インド西部は、別リージョンと一方向のみでペアになっているため異なります。 インド西部のセカンダリ リージョンはインド南部ですが、インド南部のセカンダリ リージョンはインド中部です。
- (2) ブラジル南部は、自身の geo 外のリージョンとペアになっているため特殊です。 ブラジル南部のセカンダリ リージョンは米国中南部ですが、米国中南部のセカンダリ リージョンはブラジル南部ではありません。
- (3) 米国政府アイオワのセカンダリ リージョンは米国政府バージニアですが、米国政府バージニアのセカンダリ リージョンは米国政府アイオワではありません。
- (4) 米国政府バージニアのセカンダリ リージョンは米国政府テキサスですが、米国政府テキサスのセカンダリ リージョンは米国政府バージニアではありません。


リージョン ペアの間でワークロードをレプリケートして、Azure の分離と可用性のポリシーを活用することをお勧めします。 たとえば、計画的な Azure システムの更新プログラムは、ペア リージョンに (同時にではなく) 順番にデプロイされます。 つまり、更新プログラムに不具合があっても (めったにありませんが)、両方のリージョンが同時に影響を受けることはありません。 さらに、万一、広範囲にわたって障害が発生した場合は、すべてのペアにおいて、少なくとも一方のリージョンの復旧が優先されます。

## <a name="an-example-of-paired-regions"></a>ペアになっているリージョンの例
以下の図 2 は、一対のリージョンを使ってディザスター リカバリーを行う架空のアプリケーションです。 緑色の番号は、 3 つの Azure サービス (Azure コンピューティング、ストレージ、およびデータベース) のリージョン間アクティビティと、そのアクティビティが、リージョン間でのレプリケートのためにどのように構成されているかを示しています。 リージョン ペアにデプロイするメリットは、オレンジ色の番号で示されています。

![ペア リージョンのメリットの概要](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

図 2 – Azure リージョン ペアの例

## <a name="cross-region-activities"></a>リージョン間アクティビティ
図 2 を参照してください。

![PaaS](./media/best-practices-availability-paired-regions/1Green.png) **Azure Compute (PaaS)** – 災害発生時に他のリージョンでリソースを確実に使用できるように、追加の計算リソースを事前にプロビジョニングする必要があります。 詳細については、「[Azure の回復性技術ガイダンス](resiliency/resiliency-technical-guidance.md)」をご覧ください。

![Storage](./media/best-practices-availability-paired-regions/2Green.png) **Azure Storage** - Azure Storage アカウントの作成時に、geo 冗長ストレージ (GRS) が既定で構成されます。 GRS を使用すると、データはプライマリ リージョン内で 3 回、ペア リージョンで 3 回、自動的にレプリケートされます。 詳細については、「 [Azure Storage 冗長オプション](storage/common/storage-redundancy.md)」をご覧ください。

![Azure SQL](./media/best-practices-availability-paired-regions/3Green.png) **Azure SQL Database** – Azure SQL Standard geo レプリケーションを使用すると、対になっているリージョンへのトランザクションの非同期レプリケーションを構成できます。 Premium geo レプリケーションを使用すると、世界中のすべてのリージョンへのレプリケーションを構成できますが、通常のディザスター リカバリーでは、これらのリソースをペア リージョンにデプロイすることをお勧めします。 詳細については、[Azure SQL Database の geo レプリケーション](sql-database/sql-database-geo-replication-overview.md)に関するページを参照してください。

![Resource Manager](./media/best-practices-availability-paired-regions/4Green.png) **Azure Resource Manager** - ARM では本質的に、リージョン全体のサービス管理コンポーネントが論理的に切り離されています。 つまり、1 つのリージョンで論理的な障害が発生しても、他のリージョンが影響を受ける可能性はそれほど高くありません。

## <a name="benefits-of-paired-regions"></a>ペアになっているリージョンのメリット
図 2 を参照してください。  

![分離](./media/best-practices-availability-paired-regions/5Orange.png)
**物理的な分離** – Azure が推奨するリージョン内の各データセンター間の距離は 300 マイル (480 km) ですが、これは一部の地域では現実的ではなく、実現できないこともあります。 物理データセンターを切り離すことで、自然災害、社会不安、停電、および物理ネットワーク障害が両方のリージョンにすぐに影響を及ぼす可能性が少なくなります。 分離は、geo 内の制約 (広さ、電源/ネットワーク インフラストラクチャの可用性、規制など) に左右されます。  

![レプリケーション](./media/best-practices-availability-paired-regions/6Orange.png)
**プラットフォームに備わっているレプリケーション** - geo 冗長ストレージなど一部のサービスには、ペア リージョンへの自動レプリケーション機能が用意されています。

![復旧](./media/best-practices-availability-paired-regions/7Orange.png)
**リージョン復旧順序** – 広範囲にわたって障害が発生した場合は、すべてのペアで一方のリージョンの復旧が優先されます。 ペア リージョンにまたがってデプロイされているアプリケーションについては、そのアプリケーションのリージョンのどちらかが必ず優先的に復旧します。 ペアになっていない複数のリージョンにアプリケーションがデプロイされていると、復旧が遅れる可能性があり、最悪の場合は、回復されてない最後の 2 つになってしまうことがあります。

![更新](./media/best-practices-availability-paired-regions/8Orange.png)
**順次更新** – 計画的な Azure システム更新プログラムは、ダウンタイム、バグの影響、および更新プログラムの不具合 (まれではありますが) による論理的な障害を最小限に抑えるために、ペア リージョンに (同時にではなく) 順番に展開されます。

![データ](./media/best-practices-availability-paired-regions/9Orange.png)
**データ常駐** – リージョンは、税および法の執行を目的としたデータ常駐要件を満たすために、ペアとして同じ geo に常駐しています (ブラジル南部を除く)。
