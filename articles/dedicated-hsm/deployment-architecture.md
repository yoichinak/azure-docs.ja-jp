---
title: デプロイ アーキテクチャ - Azure Dedicated HSM | Microsoft Docs
description: アプリケーション アーキテクチャの一部として Azure Dedicated HSM を使用するときの基本設計に関する考慮事項
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: c454b2e4df7a9ce5fadd33386e5bb413b503c6e4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608427"
---
# <a name="azure-dedicated-hsm-deployment-architecture"></a>Azure Dedicated HSM のデプロイ アーキテクチャ

Azure の専用 HSM は、暗号化キーの保管機能を Azure 内で実現するものです。 きわめて厳しいセキュリティ要件を満たしています。 次の条件が該当するお客様には、Azure の専用 HSM を使用するメリットがあります。

* [FIPS 140-2 レベル 3](https://csrc.nist.gov/publications/detail/fips/140/2/final) 認定を満たす必要がある
* HSM に排他的にアクセスする必要がある
* デバイスを完全に制御する必要がある

HSM は、Microsoft のデータ センター全体に分散され、高可用性ソリューションの基盤となる一対のデバイスとして容易にプロビジョニングできます。 災害に対する回復性の高いソリューションとなるよう、これらを複数のリージョン全体にデプロイすることもできます。 現在 Dedicated HSM を利用できるリージョンについては、[[リージョン別の製品]](https://azure.microsoft.com/global-infrastructure/services/?products=azure-dedicated-hsm) ページで確認できます。 

* 米国東部
* 米国東部 2
* 米国西部
* 米国西部 2
* 米国中南部
* 東南アジア
* 東アジア
* インド中部
* インド南部
* 東日本
* 西日本
* 北ヨーロッパ
* 西ヨーロッパ
* 英国南部
* 英国西部
* カナダ中部
* カナダ東部
* オーストラリア東部
* オーストラリア南東部
* スイス北部
* スイス西部
* US Gov バージニア州
* US Gov テキサス

これらの各リージョンでは、2 つの独立したデータ センターか、少なくとも 2 つの独立した可用性ゾーンに HSM ラックがデプロイされています。 可用性ゾーンは、東南アジアには 3 つ、米国東部 2 には 2 つ存在します。 専用 HSM サービスを提供するリージョンは、ヨーロッパ、アジア、北米全体で合計 23 存在します。 Azure リージョンの詳細については、公式の [Azure リージョン情報](https://azure.microsoft.com/global-infrastructure/regions/)を参照してください。
専用 HSM ベースのソリューションでは、いくつかの設計要素として、場所/待ち時間、高可用性、他の分散アプリケーションのサポートが挙げられます。

## <a name="device-location"></a>デバイスの場所

HSM デバイスの場所として最適なのは、暗号化操作を実行するアプリケーションに最も近い場所です。 リージョン内の待ち時間は、10 ミリ秒未満であると見込まれます。 リージョン間の待ち時間は、その 5 倍から 10 倍になる可能性があります。

## <a name="high-availability"></a>高可用性

高可用性を確保するためには、Thales ソフトウェアを使用して高可用性ペアとして構成された 2 つの HSM デバイスを 1 つのリージョン内で使用する必要があります。 この種のデプロイであれば、1 つのデバイスで問題が発生して、キーの操作を処理できなくなったとしても、キーの可用性が確保されます。 電源の交換など、障害対応のメンテナンスを実施する際のリスクも大幅に軽減されます。 リージョン レベルのあらゆる種類の障害を考慮に入れることが、設計では重要となります。 リージョン レベルの障害は、ハリケーン、洪水、地震などの自然災害が生じたときに発生する可能性があります。 この種の事象は、HSM デバイスを別のリージョンにプロビジョニングすることによって緩和する必要があります。 別のリージョンにデプロイされたデバイスは、Thales ソフトウェアの構成を通じて対にすることができます。 これは、高可用性と災害に対する回復性を備えたソリューションには、最低でも 4 つの HSM デバイスを 2 つのリージョンにまたがってデプロイする必要があることを意味します。 ローカル冗長性とリージョンをまたいだ冗長性をベースラインとして、HSM デバイスを追加でデプロイすれば、待ち時間の短縮や容量の増強を図ることができるほか、アプリケーションに固有の他の要件を満たすことができます。

## <a name="distributed-application-support"></a>分散アプリケーションのサポート

通常、専用 HSM デバイスは、キーの保管操作と取得操作を実行する必要のあるアプリケーションを支援する手段としてデプロイされます。 専用 HSM デバイスには、アプリケーションの個別サポートを目的として 10 個のパーティションがあります。 デバイスの場所は、このサービスを使用する必要があるすべてのアプリケーションを俯瞰して検討してください。

## <a name="next-steps"></a>次のステップ

デプロイ アーキテクチャが決まれば、そのアーキテクチャを実装する大半の構成作業は Thales によって提供されます。 これには、デバイス構成やアプリケーション統合のシナリオが含まれます。 詳細については、[Thales のカスタマー サポート](https://supportportal.thalesgroup.com/csm) ポータルを使用して、管理ガイドと構成ガイドをダウンロードしてください。 Microsoft パートナー サイトには、さまざまな統合ガイドがあります。
デバイスのプロビジョニングや、アプリケーションの設計とデプロイの前に、高可用性やセキュリティなど、サービスのすべての主要概念を十分に理解しておくことをお勧めします。
その他の概念レベルのトピックを次に示します。

* [高可用性](high-availability.md)
* [物理的なセキュリティ](physical-security.md)
* [ネットワーク](networking.md)
* [サポート可能性](supportability.md)
* [Monitoring](monitoring.md)
