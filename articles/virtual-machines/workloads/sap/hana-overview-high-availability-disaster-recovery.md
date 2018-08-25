---
title: SAP HANA on Azure (L インスタンス) の高可用性とディザスター リカバリー | Microsoft Docs
description: 高可用性を確立し、SAP HANA on Azure (L インスタンス) のディザスター リカバリーを計画します。
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/27/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 554991c7c0f11a095a11ae24dbb693a1a3ba50fd
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39430122"
---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>Azure での SAP HANA L インスタンスの高可用性とディザスター リカバリー 

>[!IMPORTANT]
>このドキュメントは、SAP HANA の管理ドキュメントまたは SAP Note の代わりになるものではありません。 このドキュメントでは、SAP HANA の管理と操作 (特にバックアップ、復元、高可用性およびディザスター リカバリーのトピック) について、読者が十分な理解と専門知識を有していることを前提としています。 このドキュメントでは、SAP HANA Studio のスクリーン ショットを示します。 SAP 管理ツールの画面およびツール自体の内容、構造、特徴は、SAP HANA のリリースごとに変わる可能性があります。

お使いの環境、および使用する HANA のバージョンとリリースに応じた手順とプロセスを理解することが重要です。 このドキュメントで説明されている一部のプロセスは、一般的な情報がよく理解できるように単純化されており、最終的な操作ハンドブックのための詳細な手順として使うことは意図されていません。 使用する構成に対応した操作ハンドブックを作成する場合は、実際のプロセスをテストおよび練習し、特定の構成に関連するプロセスを文書化する必要があります。 


高可用性とディザスター リカバリー (DR) は、ミッション クリティカルな SAP HANA on Azure L インスタンス サーバーを実行する場合のきわめて重要な要素です。 適切な高可用性とディザスター リカバリー戦略をきちんと策定、実装するには、SAP、システム インテグレーター、Microsoft と協力することが重要です。 また、環境に固有の回復ポイントの目標 (RPO) と回復ポイントの目標を検討することも重要です。

Microsoft では、HANA L インスタンスでいくつかの SAP HANA 高可用性手法をサポートしています。 次のような機能があります。

- **ストレージ レプリケーション**: すべてのデータを別の Azure リージョンの別の HANA L インスタンス スタンプにレプリケートする、ストレージ システムの機能です。 SAP HANA は、この手法と関係なく動作します。 この機能は、HANA L インスタンスに対して提供されている既定のディザスター リカバリー メカニズムです。
- **HANA システム レプリケーション**: 別の SAP HANA システムへの [SAP HANA の全データのレプリケーション](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)です。 目標復旧時間は、一定の間隔で実行されるデータ レプリケーションによって最小限に抑えられます。 SAP HANA では、非同期、メモリ内同期、および同期 の各モードがサポートされています。 同期モードは、同じデータ センター内または 100 km 未満の距離のデータ センター内の SAP HANA システムにのみ使用されます。 HANA L インスタンス スタンプの現在の設計では、HANA システム レプリケーションは 1 つのリージョンだけの高可用性を実現するために利用することができます。 HANA システム レプリケーションには、別の Azure リージョンへのディザスター リカバリー構成に対応するサード パーティのリバース プロキシ コンポーネントまたはルーティング コンポーネントが必要です。 
- **ホストの自動フェールオーバー**: SAP HANA が HANA システム レプリケーションの代わりに使用するローカル障害復旧ソリューションです。 マスター ノードが使用できなくなると、1 つ以上のスタンバイ SAP HANA ノードがスケールアウト モードで構成され、SAP HANA がスタンバイ ノードに自動的にフェールオーバーされます。

SAP HANA on Azure (L インスタンス) は、4 つの地政学的地域 (米国、オーストラリア、ヨーロッパ、2 本) を対象とする 2 つの Azure リージョンで提供されます。 HANA L インスタンス スタンプをホストする地政学的地域内の 2 つのリージョンは、個別の専用ネットワーク回線に接続されます。 これらは、ストレージのスナップショットをレプリケートしてディザスター リカバリーの手段を提供するために使用されます。 レプリケーションは既定では確立されず、ディザスター リカバリー機能を指定したお客様に対して設定されます。 ストレージ レプリケーションは、HANA L インスタンスのストレージ スナップショットの使用に依存します。 別の地政学的領域内の Azure リージョンを DR リージョンとして選択することはできません。 

現在サポートされている高可用性とディザスター リカバリーの手段およびその組み合わせを次の表に示します。

| HANA L インスタンスでサポートされるシナリオ | 高可用性オプション | ディザスター リカバリー オプション | 説明 |
| --- | --- | --- | --- |
| 単一ノード | 使用できません。 | 専用 DR セットアップ。<br /> 多目的 DR セットアップ。 | |
| 自動フェールオーバーのホスト: スケールアウト (スタンバイの有無にかかわらず)<br /> (1 + 1 を含む) | アクティブ ロールを担うスタンバイで可能。<br /> HANA がロールの切り替えを制御。 | 専用 DR セットアップ。<br /> 多目的 DR セットアップ。<br /> ストレージ レプリケーションを使用した DR 同期。 | HANA ボリューム セットはすべてのノードに接続。<br /> DR サイトに同じ数のノードが必要。 |
| HANA システム レプリケーション | プライマリまたはセカンダリ セットアップで可能。<br /> フェールオーバーが発生した場合にセカンダリがプライマリ ロールに移行。<br /> HANA システム レプリケーションと OS 制御のフェールオーバー。 | 専用 DR セットアップ。<br /> 多目的 DR セットアップ。<br /> ストレージ レプリケーションを使用した DR 同期。<br /> HANA システム レプリケーションを使用した DR はサード パーティのコンポーネントがないとまだ不可能。 | 各ノードに接続された個別のディスク ボリューム セット。<br /> DR の場所にレプリケートされるのは、実稼働サイトのセカンダリ レプリカのディスク ボリュームのみ。<br /> DR サイトにボリューム セットが 1 つ必要。 | 

専用 DR セットアップは、DR サイトの HANA L インスタンス ユニットが他のワークロードや非実稼働システムの実行には使用されないセットアップです。 ユニットはパッシブであり、障害時のフェールオーバーが実行されたときにのみデプロイされます。 ただし、このセットアップは多くのお客様にお勧めする方法ではありません。

ご使用のアーキテクチャのストレージ レイアウトとイーサネットの詳細については、[HLI でサポートされるシナリオ](hana-supported-scenario.md)に関する記事を参照してください。

> [!NOTE]
> オーバーレイ シナリオとしての [SAP HANA の MCOD デプロイ](https://launchpad.support.sap.com/#/notes/1681092) (1 つのユニットに複数の HANA Instances) は、表に一覧で示されている HA と DR の方法で機能します。 例外は、Pacemaker に基づく自動フェールオーバー クラスターを備えた HANA システム レプリケーションを使う場合です。 このような場合は、ユニットごとにサポートされる HANA インスタンスは 1 つのみです。 [SAP HANA MDC](https://launchpad.support.sap.com/#/notes/2096000) デプロイでは、複数のテナントがデプロイされる場合、非ストレージ ベースの HA および DR の方法のみが機能します。 1 つのテナントがデプロイされる場合は、一覧のすべての方法が有効です。  

多目的 DR セットアップは、DR サイト上の HANA L インスタンス ユニットが非実稼働ワークロードを実行するセットアップです。 障害発生時には、非運用システムをシャットダウンし、ストレージでレプリケートされた (追加の) ボリューム セットをマウントしてから、HANA 運用インスタンスを起動します。 HANA L インスタンスのディザスター リカバリー機能を使用するお客様の多くが、この構成を使用しています。 


SAP HANA の高可用性の詳細については、SAP の次の記事を参照してください。 

- [SAP HANA の高可用性のホワイトペーパー](http://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [SAP HANA 管理ガイド](http://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [SAP HANA システム レプリケーションに関する SAP HANA Academy ビデオ](http://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [SAP サポート ノート #1999880 - SAP HANA システム レプリケーションに関する FAQ](https://apps.support.sap.com/sap/support/knowledge/preview/en/1999880)
- [SAP サポート ノート #2165547 - SAP HANA システム レプリケーション環境での SAP HANA のバックアップと復元](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [SAP サポート ノート #1984882 - 最小/ゼロ ダウンタイムでのハードウェア交換のための SAP HANA システム レプリケーションの使用](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>HANA L インスタンスでのディザスター リカバリーのネットワークに関する考慮事項

HANA L インスタンスのディザスター リカバリー機能を利用するには、2 つの Azure リージョンへのネットワーク接続を設計する必要があります。 また、オンプレミスからメイン Azure リージョンへの Azure ExpressRoute 回線接続のほか、オンプレミスからディザスター リカバリーを行うリージョンへの回線接続が必要になります。 この方法では、Microsoft Enterprise Edge Router (MSEE) の場所を含め、Azure リージョンで問題が発生した状況に対処できます。

2 つ目の方法として、1 つのリージョンの SAP HANA on Azure L インスタンスに接続するすべての Azure Virtual Network を、もう一方のリージョンの HANA L インスタンスに接続する ExpressRoute 回線に接続できます。 この *相互接続* により、リージョン 1 の Azure Virtual Network で実行されているサービスがリージョン 2 の HANA L インスタンス ユニットに接続することができ、その逆も可能になります。 この方法では、オンプレミスの場所を Azure と接続する MSEE の場所のいずれか 1 つのみがオフラインになった場合に対処できます。

次の図では、ディザスター リカバリーの場合の回復性に優れた構成を示します。

![障害復旧に最適な構成](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-with-hana-large-instances-storage-replication-for-disaster-recovery"></a>ディザスター リカバリーに HANA L インスタンスのストレージ レプリケーションを使用する際のその他の要件

HANA L インスタンスでのディザスター リカバリー セットアップについては、上記の要件以外に次の要件があります。

- 製品 SKU と同じサイズの SAP HANA on Azure L インスタンス SKU を指定し、ディザスター リカバリー リージョンにデプロイする必要があります。 現在の顧客デプロイでは、これらのインスタンスを使用して HANA 非実稼働インスタンスを実行しています。 これらの構成は、"*多目的 DR セットアップ*" と呼ばれます。   
- ディザスター リカバリー サイトで復旧する SAP HANA on Azure (L インスタンス) SKU ごとに、DR サイトの追加のストレージを指定する必要があります。 追加のストレージを購入すると、ストレージ ボリュームを割り当てることができます。 運用 Azure リージョンからディザスター リカバリー Azure リージョンへのストレージ レプリケーションのターゲットとなるボリュームを割り当てることができます。
- プライマリで HSR をセットアップし、DR サイトへのストレージ ベースのレプリケーションをセットアップする場合は、DR サイトにストレージを追加購入して、プライマリ ノードとセカンダリ ノードの両方のデータが DR サイトにレプリケートされるようにする必要があります。

 

## <a name="backup-and-restore"></a>バックアップと復元

データベースを運用するうえで最も重要な点の 1 つは、危機的な事象からデータベースを保護することです。 こうした事象の原因には、自然災害から単純なユーザー エラーまで、あらゆることが考えられます。

データベースをバックアップし、任意の時点 (たとえば、だれかが重要なデータを削除してしまう前など) に復元できるようにすることで、中断が発生する前の状態にできる限り近い状態への復元が可能になります。

最良の結果を得るには、次の 2 種類のバックアップを実行する必要があります。

- データベース バックアップ: 完全、増分、または差分バックアップ
- トランザクション ログのバックアップ

アプリケーション レベルで実行されるデータベースの完全バックアップに加え、ストレージ スナップショットによるバックアップを実行することもできます。 ストレージ スナップショットはトランザクション ログ バックアップに代わるものではありません。 データベースを特定の時点に復元したり、既にコミットされたトランザクションのログを削除したりするために、トランザクション ログ バックアップが重要であることに変わりはありません。 ただし、ストレージ スナップショットでは、データベースのロールフォワード イメージが速やかに提供されるので復旧を迅速化できます。 

SAP HANA on Azure (L インスタンス) には、次の 2 つのバックアップと復元のオプションがあります。

- 自分で実行する (DIY)。 計算して十分なディスク領域を確保した後、次のいずれかのディスク バックアップ方法を使用して、データベースとログの完全バックアップを実行します。 HANA L インスタンス ユニットに接続されているボリュームに直接バックアップするか、Azure 仮想マシン (VM) にセットアップされているネットワーク ファイル共有 (NFS) にバックアップすることができます。 後者の場合、Azure で Linux VM をセットアップし、Azure Storage を VM に接続して、その VM で構成済みの NFS サーバーを使用してストレージを共有します。 HANA L インスタンス ユニットに直接接続されているボリュームに対してバックアップを実行する場合は、(Azure Storage に基づく NFS 共有をエクスポートする Azure VM をセットアップした後) バックアップを Azure ストレージ アカウントにコピーする必要があります。 Azure Backup コンテナーまたは Azure コールド ストレージを使用することもできます。 

   もう 1 つの方法として、バックアップが Azure ストレージ アカウントにコピーされた後に、サード パーティのデータ保護ツールを使用してバックアップを保存します。 DIY バックアップ オプションは、コンプライアンスと監査のためにデータを長期間保存しなければならない場合にも必要になることがあります。 どの場合も、バックアップは VM と Azure Storage を介して提供される NFS 共有にコピーされます。

- インフラストラクチャのバックアップおよび復元機能。 SAP HANA on Azure (L インスタンス) の基になるインフラストラクチャによって提供されるバックアップと復元の機能を使用することもできます。 このオプションは、バックアップと高速復元のニーズに対応します。 このセクションの残りの部分では、HANA L インスタンスで提供されるバックアップと復元の機能について説明します。 ここでは、HANA L インスタンスが提供するディザスター リカバリー機能に対するリレーションシップのバックアップと復元についても説明します。

>   [!NOTE]
>   HANA L インスタンスの基になるインフラストラクチャで使用されているスナップショット テクノロジには、SAP HANA スナップショットへの依存関係があります。 現在のところ、SAP HANA スナップショットは、SAP HANA マルチテナント データベース コンテナーの複数のテナントでは機能しません。 テナントが 1 つしかデプロイされていない場合は、SAP HANA スナップショットが機能するので、この方法を使用することができます。

### <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>SAP HANA on Azure (L インスタンス) のストレージ スナップショットの使用

SAP HANA on Azure (L インスタンス) の基になっているストレージ インフラストラクチャでは、ボリュームのストレージ スナップショットがサポートされています。 ボリュームのバックアップと復元の両方がサポートされていますが、次の点を考慮する必要があります。

- データベースの完全バックアップの代わりに、ストレージ ボリューム スナップショットが頻繁に作成されます。
- /hana/data および /hana/shared (/usr/sap を含む) の各ボリュームに対するスナップショットをトリガーすると、ストレージ スナップショットの実行前に、スナップショット テクノロジーによって SAP HANA スナップショットが開始されます。 この SAP HANA スナップショットは、ストレージ スナップショットの復旧後に最終的なログ復元を設定するポイントです。 HANA スナップショットが成功するには、アクティブな HANA インスタンスが必要です。  HSR シナリオの場合、HANA スナップショットを実行できない現在のセカンダリ ノードでは、ストレージ スナップショットはサポートされていません。
- ストレージ スナップショットが正常に実行された後、SAP HANA スナップショットは削除されます。
- トランザクション ログ バックアップが頻繁に作成され、/hana/logbackups ボリュームまたは Azure に保存されます。 トランザクション ログ バックアップを格納する /hana/logbackups ボリュームをトリガーしてスナップショットを個別に実行できます。 この場合、HANA スナップショットを実行する必要はありません。
- 特定の時点のデータベースを復元する必要がある場合は、Microsoft Azure サポート (運用環境が停止した場合) か、SAP HANA on Azure サービス管理に対して、特定のストレージ スナップショットを復元するよう要請してください。 たとえば、サンドボックス システムを元の状態へと計画的に復元する方法があります。
- ストレージ スナップショットに含まれている SAP HANA スナップショットは、ストレージ スナップショットの作成後に実行され、保存されたトランザクション ログ バックアップを適用するためのオフセット ポイントです。
- これらのトランザクション ログ バックアップは、データベースを特定の時点に復元するために作成されます。

次の 3 つのクラスのボリュームをターゲットとするストレージ スナップショットを実行できます。

- /hana/data および /hana/shared (/usr/sap を含む) を対象とする結合スナップショット。 このスナップショットでは、ストレージ スナップショットの準備として SAP HANA スナップショットを作成する必要があります。 SAP HANA スナップショットは、ストレージの観点からデータベースが整合性のある状態であることを確認します。 復元プロセスの場合は、それがセットアップするポイントです。
- /hana/logbackups に対する個別スナップショット。
- オペレーティング システム パーティション。

[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts) から最新のスナップショット スクリプトとドキュメントを入手してください。 

### <a name="storage-snapshot-considerations"></a>ストレージ スナップショットに関する考慮事項

>[!NOTE]
>ストレージ スナップショットは、HANA L インスタンス ユニットに割り当てられている記憶域スペースを使用します。 ストレージ スナップショットのスケジュール設定と、保持するストレージ スナップショットの数については、以下の点を考慮する必要があります。 

SAP HANA on Azure (L インスタンス) のストレージ スナップショットに固有のメカニズムは、次のとおりです。

- 作成時点での特定のストレージ スナップショットは、ストレージをほとんど使用しません。
- データの内容は変化し、ストレージ ボリュームで SAP HANA データ ファイルの内容が変化するため、スナップショットは元のブロックの内容だけでなく、データの変更も保存する必要があります。
- そのため、ストレージ スナップショットのサイズは増加します。 スナップショットの存在期間が長くなるほど、ストレージ スナップショットが大きくなります。
- ストレージ スナップショットの有効期間に SAP HANA データベース ボリュームに対する変更が多くなるほど、ストレージ スナップショットの領域の消費量が大きくなります。

SAP HANA on Azure (L インスタンス) には、SAP HANA のデータとログ ボリューム用の固定ボリューム サイズがあります。 これらのボリュームのスナップショットを実行すると、ボリューム領域が消費されます。 ストレージ スナップショットのスケジュールを決める必要があります。 また、ストレージ ボリュームの領域使用量を監視するだけでなく、保存するスナップショット数を管理する必要もあります。 大量のデータをインポートする場合や、HANA データベースに対する他の大きな変更を実行する場合、ストレージ スナップショットを無効にすることができます。 


以降のセクションでは、これらのスナップショットの実行に関して、次のような一般的な推奨事項も含めて説明します。

- ハードウェアはボリュームごとに 255 個のスナップショットを保持できますが、この数よりも十分に少なくしておくことをお勧めします。 推奨値は 250 以下です。
- ストレージ スナップショットを実行する前に、空き領域を監視して追跡します。
- 空き領域に基づいて、ストレージ スナップショットの数を減らします。 場合によっては、保持するスナップショットの数を減らしたり、ボリュームを拡大したりする必要があります。 追加のストレージは 1 TB 単位で注文できます。
- SAP プラットフォーム移行ツール (R3load) を使用した SAP HANA へのデータの移動や、バックアップからの SAP HANA データベースの復元などの操作の実行中は、/hana/data ボリュームでストレージ スナップショットを無効にします。 
- SAP HANA テーブルの大規模な再編成中は、できるだけストレージ スナップショットを避けてください。
- ストレージ スナップショットは、SAP HANA on Azure (L インスタンス) のディザスター リカバリー機能を利用するための前提条件です。

### <a name="prerequisites-for-using-self-service-storage-snapshots"></a>セルフサービスのストレージ スナップショットを使用するための前提条件

スナップショット スクリプトが正常に実行されるようにするには、HANA L インスタンス サーバーの Linux オペレーティング システムに Perl がインストールされていることを確認します。 Perl は、HANA Large Instance ユニットにあらかじめインストールされています。 Perl のバージョンを確認するには、次のコマンドを使います。

`perl -v`

![このコマンドを実行すると公開キーがコピーされる](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


### <a name="set-up-storage-snapshots"></a>ストレージ スナップショットのセットアップ

HANA L インスタンスでストレージ スナップショットを設定するには、次の手順に従います。
1. HANA L インスタンス サーバーの Linux オペレーティング システムに Perl がインストールされていることを確認します。
1. /etc/ssh/ssh\_config を変更して _MACs hmac-sha1_ の行を追加します。
1. 実行している各 SAP HANA インスタンスのマスター ノードで、SAP HANA バックアップ ユーザー アカウントを作成します (該当する場合)。
1. すべての SAP HANA L インスタンス サーバーに、SAP HANA HDB クライアントをインストールします。
1. スナップショットの作成を制御する、基になるストレージ インフラストラクチャにアクセスするために、各リージョンの最初の SAP HANA (L インスタンス) サーバーで公開キーを作成します。
1. [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts) から SAP HANA インストールの **hdbsql** の場所にスクリプトと構成ファイルをコピーします。
1. 適切な顧客仕様での必要に応じて、*HANABackupDetails.txt* ファイルを変更します。

[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts) から最新のスナップショット スクリプトとドキュメントを入手してください。 

### <a name="consideration-for-mcod-scenarios"></a>MCOD のシナリオに関する考慮事項
1 つの HANA L インスタンス ユニット上の複数の SAP HANA インスタンスで [MCOD のシナリオ](https://launchpad.support.sap.com/#/notes/1681092)を実行している場合は、個々の SAP HANA インスタンスごとに個別のストレージ ボリュームがプロビジョニングされています。 現在のバージョンのセルフサービス スナップショット自動化では、すべての HANA インスタンス システム ID (SID) で個別のスナップショットを開始することはできません。 この機能では、構成ファイル内にあるサーバーの登録済み SAP HANA インスタンスが確認され (この記事で後述します)、ユニットに登録されているすべてのインスタンスのボリュームの同時スナップショットが実行されます。
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>手順 1: SAP HANA HDB クライアントをインストールする

SAP HANA on Azure (L インスタンス) にインストールされている Linux オペレーティング システムには、バックアップとディザスター リカバリーの目的で SAP HANA ストレージ スナップショットを実行するために必要なフォルダーとスクリプトが含まれています。 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts) に新しいリリースがあるかどうかを確認してください。 スクリプトの最新のリリース バージョンは 3.x です。 異なるスクリプトでは、同じメジャー リリースでもマイナー リリースが異なる可能性があります。

>[!IMPORTANT]
>スクリプトをバージョン 2.1 から 3.x に移行する際には、構成ファイルの構造と一部の構文が変更されていることに注意してください。 特定のセクションの注意をご覧ください。 

お客様ご自身で、SAP HANA のインストール時に、SAP HANA HDB クライアントを HANA L インスタンス ユニットにインストールする必要があります。

### <a name="step-2-change-the-etcsshsshconfig"></a>手順 2: /etc/ssh/ssh\_config を変更する

次に示すように、_MACs hmac-sha1_ 行を追加して `/etc/ssh/ssh_config` を変更します。
```
#   RhostsRSAAuthentication no
#   RSAAuthentication yes
#   PasswordAuthentication yes
#   HostbasedAuthentication no
#   GSSAPIAuthentication no
#   GSSAPIDelegateCredentials no
#   GSSAPIKeyExchange no
#   GSSAPITrustDNS no
#   BatchMode no
#   CheckHostIP yes
#   AddressFamily any
#   ConnectTimeout 0
#   StrictHostKeyChecking ask
#   IdentityFile ~/.ssh/identity
#   IdentityFile ~/.ssh/id_rsa
#   IdentityFile ~/.ssh/id_dsa
#   Port 22
Protocol 2
#   Cipher 3des
#   Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-cbc
#   MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd160
MACs hmac-sha1
#   EscapeChar ~
#   Tunnel no
#   TunnelDevice any:any
#   PermitLocalCommand no
#   VisualHostKey no
#   ProxyCommand ssh -q -W %h:%p gateway.example.com
```

### <a name="step-3-create-a-public-key"></a>手順 3. 公開キーを作成する

HANA L インスタンス テナントのストレージ スナップショット インターフェイスにアクセスできるようにするには、公開キーを使用したサインイン手順を確立する必要があります。 テナントの最初の SAP HANA on Azure (L インスタンス) サーバーで、ストレージ インフラストラクチャへのアクセスに使用される公開キーを作成します。 公開キーを使用すると、ストレージ スナップショット インターフェイスへのサインインにパスワードが不要になります。 また、公開キーを作成すると、パスワード資格情報を保持する必要がなくなります。 公開キーを生成するには、SAP HANA L インスタンス サーバーの Linux で、次のコマンドを実行します。
```
  ssh-keygen –t dsa –b 1024
```
新しい場所は **_/root/.ssh/id\_dsa.pub** です。 実際のパスワードは入力しないでください。パスワードを入力すると、サインインするたびにパスワードの入力が必要になります。 代わりに、**Enter** キーを 2 回押して、サインイン時の "パスワード入力" 要求を消します。

フォルダーを **/root/.ssh/** に変更して `ls` コマンドを実行することで、公開キーが想定どおり修正されたことを確認します。 キーがあれば、次のコマンドを実行してコピーできます。

![このコマンドを実行すると公開キーがコピーされる](./media/hana-overview-high-availability-disaster-recovery/image2-public-key.png)

この時点で、SAP HANA on Azure サービス管理に連絡し、公開キーを提供します。 サービス担当者は、その公開キーを使用して、HANA L インスタンス テナント用に作成された基になるストレージ インフラストラクチャにそのキーを登録します。

### <a name="step-4-create-an-sap-hana-user-account"></a>手順 4. SAP HANA ユーザー アカウントを作成する

SAP HANA スナップショットの作成を開始するには、ストレージ スナップショット スクリプトで使用できるユーザー アカウントを SAP HANA に作成する必要があります。 そのために、SAP HANA Studio 内で SAP HANA ユーザー アカウントを作成します。 MDC の SID データベースではなく SYSTEMDB に、ユーザーを作成する必要があります。 単一のコンテナー環境では、テナント データベース以下にユーザーが設定されます。 このアカウントには、"**BACKUP ADMIN**" と "**CATALOG READ**" という権限を設定する必要があります。 この例では、**SCADMIN** というユーザー名です。 HANA Studio で作成されるユーザー アカウント名では大文字小文字が区別されます。 次回のサインイン時にパスワードを変更するようにユーザーに求めるには、**[いいえ]** を選択します。

![HANA Studio でのユーザーの作成](./media/hana-overview-high-availability-disaster-recovery/image3-creating-user.png)

1 つのユニット上に複数の SAP HANA インスタンスがある MCOD デプロイを使う場合は、SAP HANA インスタンスごとにこのステップを繰り返す必要があります。

### <a name="step-5-authorize-the-sap-hana-user-account"></a>手順 5. SAP HANA ユーザー アカウントを承認する

この手順では、作成した SAP HANA ユーザー アカウントを承認します。これにより、スクリプトの実行時にパスワードを送信する必要がなくなります。 SAP HANA のコマンド `hdbuserstore` を使用して SAP HANA のユーザー キーを作成できます。これは 1 つ以上の SAP HANA ノードに格納されます。 ユーザー キーを使用すると、ユーザーはスクリプト プロセス内からパスワードを管理しなくても SAP HANA にアクセスできます。 スクリプト プロセスについてはこの記事で後述します。

>[!IMPORTANT]
>スクリプトが実行される予定のユーザー以下で次のコマンドを実行します。 そうしないと、スクリプトは正しく動作しません。

次のように `hdbuserstore` コマンドを入力します。

**非 MDC HANA セットアップの場合**
```
hdbuserstore set <key> <host>:<3[instance]15> <user> <password>
```

**MDC HANA セットアップの場合**
```
hdbuserstore set <key> <host>:<3[instance]13> <user> <password>
```

次の例では、ユーザーは **SCADMIN01**、ホスト名は **lhanad01、** インスタンス番号は **01** です。
```
hdbuserstore set SCADMIN01 lhanad01:30115 <backup username> <password>
```
1 つのユニット上に複数の SAP HANA インスタンスがある HANA MCOD デプロイを使う場合は、ユニット上の個々の SAP HANA インスタンスと、関連付けられているバックアップ ユーザーごとに、ステップを繰り返す必要があります。

SAP HANA スケールアウト構成を使用している場合、すべてのスクリプトを 1 つのサーバーから管理する必要があります。 この例では、SAP HANA のキー **SCADMIN01** は、キーに関連付けられているホストがわかるように、ホストごとに変更する必要があります。 HANA DB のインスタンス番号を使用して SAP HANA バックアップ アカウントを修正します。 キーは、割り当て先のホストに対する管理特権を持つ必要があります。また、スケールアウト構成のためのバックアップ ユーザーは、すべての SAP HANA インスタンスへのアクセス権を持つ必要があります。 **lhanad01**、**lhanad02**、**lhanad03** という名前の 3 つのスケールアウト ノードがある場合、一連のコマンドは次のようになります。

```
hdbuserstore set SCADMIN01 lhanad01:30115 SCADMIN <password>
hdbuserstore set SCADMIN01 lhanad02:30115 SCADMIN <password>
hdbuserstore set SCADMIN01 lhanad03:30115 SCADMIN <password>
```

### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>手順 6: スナップショット スクリプトを取得し、スナップショットを構成して、構成と接続をテストする

[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts) から最新バージョンのスクリプトをダウンロードします。 ダウンロードしたスクリプトとテキスト ファイルを、**hdbsql** の作業ディレクトリにコピーします。 現在の HANA インストールでは、このディレクトリは /hana/shared/D01/exe/linuxx86\_64/hdb のような形式になります。 
``` 
azure_hana_backup.pl 
azure_hana_replication_status.pl 
azure_hana_snapshot_details.pl 
azure_hana_snapshot_delete.pl 
testHANAConnection.pl 
testStorageSnapshotConnection.pl 
removeTestStorageSnapshot.pl
azure_hana_dr_failover.pl
azure_hana_test_dr_failover.pl 
HANABackupCustomerDetails.txt 
``` 

Perl スクリプトを処理する場合: 

- Microsoft Operations による指示がない限り、スクリプトを変更しないでください。
- スクリプトまたはパラメーター ファイルの変更を要求されたら、メモ帳などの Windows エディターではなく、"vi" などの Linux のテキスト エディターを常に使います。 Windows のエディターを使うと、ファイルの形式が壊れる可能性があります。
- 常に最新のスクリプトを使います。 最新バージョンは GitHub からダウンロードできます。
- すべての状況で同じバージョンのスクリプトを使います。
- 運用システムで直接使う前に、スクリプトをテストし、スクリプトの必要なパラメーターと出力に問題がないことを確認します。
- Microsoft Operations によってプロビジョニングされたサーバーのマウント ポイント名を変更しないでください。 これらのスクリプトが正常に実行するには、標準マウント ポイントを使用できる必要があります。


個々のスクリプトとファイルの目的は次のとおりです。

- **azure\_hana\_backup.pl**: このスクリプトは、HANA の data および shared ボリューム、/hana/logbackups ボリューム、またはオペレーティング システムに対してストレージ スナップショットを実行するように、Linux Cron Scheduling ユーティリティを使ってスケジュールされます。
- **azure\_hana\_replication\_status.pl**: 運用サイトからディザスター リカバリー サイトへのレプリケーションの状態に関する基本的な詳細を提供するスクリプトです。 このスクリプトは、レプリケーションの実行状況を監視し、レプリケートされる項目のサイズを表示します。 また、レプリケーションに時間がかかりすぎている場合や、リンクがダウンしている場合にガイダンスを提供します。
- **azure\_hana\_snapshot\_details.pl**: 環境内に存在するボリュームごとのすべてのスナップショットに関する基本的な詳細の一覧を提供するスクリプトです。 このスクリプトは、プライマリ サーバーで実行することも、ディザスター リカバリーの場所のサーバー ユニットで実行することもできます。 このスクリプトは、スナップショットを含むボリュームごとに分類して次の情報を提供します。
   * ボリュームの合計スナップショットのサイズ
   * そのボリュームの各スナップショットに関する次の詳細情報: 
      - スナップショット名 
      - 作成時刻 
      - スナップショットのサイズ
      - スナップショットの頻度
      - 該当する場合、そのスナップショットに関連付けられている HANA バックアップ ID
- **azure\_hana\_snapshot\_delete.pl**: ストレージ スナップショットまたはスナップショットのセットを削除するスクリプトです。 HANA Studio に表示される SAP HANA バックアップ ID、またはストレージ スナップショット名を使用できます。 現在、バックアップ ID は、HANA の data/log/shared ボリュームに作成されたスナップショットにのみ関連付けられています。 それ以外の場合は、スナップショット ID を入力すると、入力されたスナップショット ID と一致するすべてのスナップショットが検索されます。  
- **testHANAConnection.pl**: このスクリプトでは、SAP HANA インスタンスへの接続をテストします。ストレージ スナップショットを設定する際に、このスクリプトが必要となります。
- **testStorageSnapshotConnection.pl**: このスクリプトには 2 つの目的があります。 1 つ目は、スクリプトを実行する HANA L インスタンス ユニットが、割り当てられているストレージ仮想マシンにアクセスでき、これによって HANA L インスタンスのストレージ スナップショット インターフェイスにもアクセスできることを確認することです。 もう 1 つの目的は、テスト対象の HANA インスタンスの一時的なスナップショットを作成することです。 バックアップ スクリプトが予想どおりに機能していることを確認するために、サーバー上の HANA インスタンスごとにこのスクリプトを実行する必要があります。
- **removeTestStorageSnapshot.pl**: **testStorageSnapshotConnection.pl** スクリプトによって作成されたテスト スナップショットを削除するスクリプトです。
- **azure\_hana\_dr\_failover.pl**: このスクリプトは、別のリージョンへの DR フェールオーバーを開始します。 このスクリプトは、DR リージョンの HANA L インスタンス ユニットか、フェールオーバー先のユニットで実行する必要があります。 このスクリプトは、プライマリ側からセカンダリ側へのストレージのレプリケーションを停止し、DR ボリュームで最新のスナップショットを復元して、DR ボリュームのマウント ポイントを提供します。
- **azure\_hana\_test\_dr\_failover.pl**: このスクリプトは、DR サイトへのテスト フェールオーバーを実行します。 azure_hana_dr_failover.pl スクリプトとは異なり、この実行ではプライマリからセカンダリへのストレージのレプリケーションは中断されません。 代わりに、レプリケートされストレージ ボリュームの複製が DR 側に作成され、複製されたボリュームのマウント ポイントが提供されます。 
- **HANABackupCustomerDetails.txt**: SAP HANA 構成に合わせて変更する必要がある、変更可能な構成ファイルです。 *HANABackupCustomerDetails.txt* ファイルは、ストレージ スナップショットを実行するスクリプトの管理および構成ファイルです。 このファイルは、目的とセットアップに合わせて調整します。 "**ストレージ バックアップ名**" と "**ストレージ IP アドレス**" は、インスタンスがデプロイされるときに SAP HANA on Azure サービス管理から受け取ります。 このファイル内のどの変数も、シーケンス、順序、またはスペースを変更することはできません。 変更すると、スクリプトが正常に動作しなくなります。 また、SAP HANA on Azure サービス管理からは、スケールアップ ノードまたはマスター ノード (スケールアウトの場合) の IP アドレスも通知されます。 さらに、SAP HANA のインストール時に取得する HANA インスタンス番号も通知されます。 情報を受け取ったら、バックアップ名を構成ファイルに追加する必要があります。

スケールアップ配置またはスケールアウト配置の場合、HANA L インスタンス ユニットの名前とサーバーの IP アドレスを入力した後、構成ファイルは次の例のようになります。 バックアップまたは復旧する SAP HANA SID ごとに、必要なすべてのフィールドを入力します。

また、一定期間バックアップしたくないインスタンスの行は、必須フィールドの前に "#" を追加することでコメントにすることができます。 また、特定のインスタンスをバックアップまたは復旧する必要がない場合は、サーバーに含まれるすべての SAP HANA インスタンスを入力しなくてもかまいません。 フィールドの形式はすべて維持する必要があります。そうしないと、すべてのスクリプトでエラー メッセージがスローされ、スクリプトが終了します。 使用している最後の SAP HANA インスタンスの後にある、使用しない SID 情報詳細については、追加の必須行を削除することもできます。 いずれの行も、入力するか、コメント化するか、削除する必要があります。

>[!IMPORTANT]
>バージョン 2.1 から 3.x への移行でファイルの構造が変更されています。 バージョン 3.x のスクリプトを使う場合は、構成ファイルの構造を変更する必要があります。 


```
HANA Server Name: testing01
HANA Server IP Address: 172.18.18.50
```

HANA L インスタンス ユニットで構成する各インスタンスまたはスケールアウト構成について、次のようにデータを定義する必要があります。

    
```
######***SID #1 Information***#####
SID1: h01
###Provided by Microsoft Operations###
SID1 Storage Backup Name: clt1h01backup
SID1 Storage IP Address: 172.18.18.11
######     Customer Provided    ######
SID1 HANA instance number: 00
SID1 HANA HDBuserstore Name: SCADMINH01
```
スケールアウトおよび HANA システム レプリケーション構成では、各ノードでこの構成を繰り返します。 これにより、障害が発生した場合でも、バックアップおよび最終的なストレージ レプリケーションは動作を継続します。   

*HANABackupCustomerDetails.txt* ファイルにすべての構成データを入力したら、HANA インスタンスのデータに関して構成が正しいかどうかを確認します。 使用するスクリプトは `testHANAConnection.pl` です。このスクリプトは、SAP HANA のスケールアップ構成またはスケールアウト構成に依存しません。

```
testHANAConnection.pl
```

SAP HANA スケールアウト構成を使用している場合、マスター HANA インスタンスが、必要なすべての HANA サーバーとインスタンスにアクセスできることを確認します。 テスト スクリプトに対するパラメーターはありませんが、スクリプトを正常に実行するには、*HANABackupCustomerDetails.txt* 構成ファイルにデータを追加します。 シェル コマンド エラー コードのみが返されるため、スクリプトですべてのインスタンスのエラーチェックを行うことはできません。 それでも、ダブルチェックを行ううえで有益なコメントをいくつかこのスクリプトから得ることができます。

スクリプトを実行するには、次のコマンドを入力します。
```
 ./testHANAConnection.pl
```
このスクリプトは、HANA インスタンスの状態の取得に成功した場合、HANA 接続が成功したというメッセージを出力します。


次のテスト ステップでは、*HANABackupCustomerDetails.txt* 構成ファイルに入力したデータに基づいてストレージへの接続を確認し、テスト スナップショットを実行します。 `azure_hana_backup.pl` スクリプトを実行する前に、このテストを実行する必要があります。 ボリュームにスナップショットが含まれていない場合、ボリュームが空なのか、スナップショットの詳細を取得する際に SSH エラーが発生しているのかを特定することができません。 このため、このスクリプトでは次の 2 つのステップが実行されます。

- スクリプトがスナップショットを実行するために、テナントのストレージ仮想マシンとインターフェイスにアクセスできることを確認する。
- HANA インスタンスでボリュームごとにテスト (ダミー) スナップショットを作成する。

このため、HANA インスタンスが引数として含まれます。 実行に失敗した場合、ストレージ接続のエラー チェックを行うことはできません。 エラー チェックがない場合でも、このスクリプトは便利なヒントを提供します。

1. このテストを実行するには一連のコマンドを実行します。

   ```
   ssh <StorageUserName>@<StorageIP>
   ```

   HANA Large Instance ユニットの引き渡し時に、ストレージ ユーザー名とストレージ IP アドレスの両方がユーザーに提供されています。

1. テスト スクリプトを実行します。
   ```
    ./testStorageSnapshotConnection.pl
   ```

スクリプトは以前のセットアップ手順で提供された公開キーと *HANABackupCustomerDetails.txt* ファイルに構成されたデータを使用して、ストレージへのサインインを試みます。 サインインに成功すると、次の内容が表示されます。

```
**********************Checking access to Storage**********************
Storage Access successful!!!!!!!!!!!!!!
```

ストレージ コンソールへの接続時に問題が発生した場合は、次のような出力が表示されます。

```
**********************Checking access to Storage**********************
WARNING: Storage check status command 'volume show -type RW -fields volume' failed: 65280
WARNING: Please check the following:
WARNING: Was publickey sent to Microsoft Service Team?
WARNING: If passphrase entered while using tool, publickey must be re-created and passphrase must be left blank for both entries
WARNING: Ensure correct IP address was entered in HANABackupCustomerDetails.txt
WARNING: Ensure correct Storage backup name was entered in HANABackupCustomerDetails.txt
WARNING: Ensure that no modification in format HANABackupCustomerDetails.txt like additional lines, line numbers or spacing
WARNING: ******************Exiting Script*******************************
```

ストレージの仮想マシン インターフェイスに正常にサインインすると、スクリプトはフェーズ 2 に進み、テスト スナップショットが作成されます。 SAP HANA の 3 ノード スケールアウト構成の場合、次のように出力されます。

```
**********************Creating Storage snapshot**********************
Taking snapshot testStorage.recent for hana_data_hm3_mnt00001_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00001_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00002_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00002_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00003_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00003_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_backups_hm3_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_backups_hm3_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00001_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00002_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00003_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_shared_hm3_t020_vol ...
Snapshot created successfully.
```

スクリプトによってテスト スナップショットが正常に実行されたら、実際のストレージ スナップショットの構成を開始できます。 成功しなかった場合は、問題を調べてから次に進んでください。 実際の最初のスナップショットが完了するまで、テスト スナップショットは保持されます。


### <a name="step-7-perform-snapshots"></a>手順 7: スナップショットを実行する

準備手順が完了したら、実際のストレージ スナップショットの構成を開始できます。 スケジュールされたスクリプトは、SAP HANA のスケールアップ構成とスケールアウト構成で動作します。 バックアップ スクリプトを定期的に実行するには、cron ユーティリティを使ってスクリプトをスケジュールします。 

作成できるスナップショット バックアップは次の 3 種類です。
- **HANA**: /hana/data および /hana/shared (/usr/sap も含む) を含むボリュームに調整されたスナップショットで対応する結合スナップショット バックアップ。 このスナップショットから単一ファイル復元が可能です。
- **Logs**: /hana/logbackups ボリュームのスナップショット バックアップ。 このストレージ スナップショットを実行する際に HANA スナップショットはトリガーされません。 このストレージ ボリュームは、SAP HANA トランザクション ログ バックアップを含めるためのものです。 これらは、ログの増加を制限し、データ損失の可能性を防ぐために、高い頻度で実行されます。 このスナップショットから単一ファイル復元が可能です。 頻度を 3 分未満にしないでください。
- **Boot**: HANA L インスタンスのブート 論理ユニット番号 (LUN) を含むボリュームのスナップショット。 このスナップショット バックアップは、HANA L インスタンスの Type I SKU でのみ実行可能です。 ブート LUN を含むボリュームのスナップショットから単一ファイル復元を実行することはできません。


>[!NOTE]
> これら 3 種類のスナップショットの呼び出し構文は、MCOD のデプロイをサポートするバージョン 3.x スクリプトへの移行で変更されました。 今後は、インスタンスの HANA SID を指定する必要はありません。 ユニットの SAP HANA インスタンスが構成ファイル *HANABackupCustomerDetails.txt* で構成されていることを確認する必要があります。

>[!NOTE]
> 複数 SID 環境では、スクリプトを初めて実行するときに、予期しないエラーが発生する場合があります。 この問題は、スクリプトを再実行すると修正されます。



スクリプト *azure_hana_backup.pl* でストレージ スナップショットを実行する新しい呼び出し構文は次のとおりです。

```
HANA backup covering /hana/data and /hana/shared (includes/usr/sap)
./azure_hana_backup.pl hana <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

For /hana/logbackups snapshot
./azure_hana_backup.pl logs <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

For snapshot of the volume storing the boot LUN
./azure_hana_backup.pl boot <HANA Large Instance Type> <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

```

パラメーターの詳細は、次のとおりです。 

- 最初のパラメーターでは、スナップショット バックアップの種類を指定します。 指定できる値は、**hana**、**logs**、**boot** です。 
- パラメーター **<HANA Large Instance Type>** は、ブート ボリュームのバックアップの場合にのみ必要です。 有効な値は、HANA Large Instance ユニットに応じて "TypeI" または "TypeII" の 2 つです。 ユニットの種類を確認するには、「[SAP HANA on Azure (L インスタンス) の概要とアーキテクチャ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)」をご覧ください。  
- パラメーター **<snapshot_prefix>** は、スナップショットまたは、スナップショットの種類のバックアップ ラベルです。 これには 2 つの目的があります。1 つは、名前を与えて、スナップショットの概要がわかるようにすることです。 2 つ目は、スクリプト *azure\_hana\_backup.pl* で、特定のラベルで保持するストレージ スナップショットの数を決めることです。 同じ種類 (**hana** など) でラベルが異なるストレージ スナップショット バックアップを 2 つスケジュールし、それぞれに 30 個のスナップショットを保持するように定義する場合、影響を受けるボリュームのストレージ スナップショットは最終的に 60 個になります。 アルファベット ("A-Z、a-z、0-9")、アンダースコア ("_")、およびダッシュ ("-") 文字のみを使用できます。 
- パラメーター **<snapshot_frequency>** は、将来の開発のために予約されており、どのような影響もありません。 **ログ** タイプのバックアップを実行するときは "3min" に設定し、他のバックアップ タイプを実行するときは "15min" に設定してください。
- パラメーター **<number of snapshots retained>** は、同じスナップショット プレフィックス (ラベル) のスナップショットがいくつ保持されるかを定義することで、スナップショットのリテンション期間を間接的に定義するものです。 このパラメーターは、cron を使用して実行がスケジュールされている場合に重要です。 snapshot_prefix が同じスナップショットの数がこのパラメーターで指定されている数を超えた場合、新しいストレージ スナップショットを実行する前に、最も古いスナップショットが削除されます。

スケールアウトの場合、このスクリプトは追加のチェックを実行して、すべての HANA サーバーにアクセスできることを確認します。 また、すべての HANA インスタンスからインスタンスの適切な状態が返されることも確認してから、SAP HANA スナップショットを作成します。 SAP HANA スナップショットは、ストレージ スナップショットの後に作成されます。

スクリプト `azure_hana_backup.pl` を実行すると、次の 3 つのフェーズで、ストレージ スナップショットが作成されます。

1. SAP HANA スナップショットを実行する
1. ストレージ スナップショットを実行する
1. ストレージ スナップショットの実行前に作成された SAP HANA スナップショットを削除する

スクリプトを実行するには、コピー先の HDB 実行可能フォルダーから呼び出します。 

リテンション期間は、スクリプトの実行時にパラメーターとして送信されるスナップショットの数で管理されます。 ストレージ スナップショットが対応する期間は、実行期間と、スクリプトの実行時にパラメーターとして送信されるスナップショットの数によって決まります。 保持されているスナップショットの数が、スクリプトの呼び出しでパラメーターとして指定された数を超えた場合は、新しいスナップショットの実行前に、同じラベルの最も古いストレージ スナップショットが削除されます。 呼び出しの最後のパラメーターとして指定した数が、保持されるスナップショットの数を制御するために使用できる数になります。 この数を利用して、スナップショットに使用されるディスク領域を間接的に制御することもできます。 

> [!NOTE]
>ラベルを変更するとすぐに、カウントが再開されます。 スナップショットが誤って削除されないように、ラベル付けを厳格にする必要があります。

### <a name="snapshot-strategies"></a>スナップショット戦略
各種スナップショットの頻度は、HANA L インスタンスのディザスター リカバリー機能を使用するかどうかによって異なります。 この機能はストレージ スナップショットに依存しているため、場合によっては、ストレージ スナップショットの頻度と実行期間について特別な推奨事項が必要になることがあります。 

以下の考慮事項と推奨事項では、HANA L インスタンスが提供するディザスター リカバリー機能を "*使用しない*" ことを前提としています。 代わりに、バックアップを保持し、過去 30 日間の特定の時点への復旧を実現できるようにするための手段として、ストレージ スナップショットを使います。 スナップショットの数と領域の制限がある場合、お客様は次のような要件を考慮しています。

- ポイントインタイム復旧の復旧時間。
- 使用される領域。
- 障害からの復旧可能性の、回復ポイントと回復時刻の目標。
- ディスクに対する HANA データベースの完全バックアップの最終的な実行。 ディスクまたは **backint** インターフェイスに対するデータベースの完全バックアップが実行されるたびに、ストレージ スナップショットの実行は失敗します。 ストレージ スナップショットに加えてデータベースの完全バックアップを実行する予定がある場合は、その実行中にストレージ スナップショットの実行が無効になるようにします。
- ボリュームあたりのスナップショット数 (上限は 250)。


HANA L インスタンスのディザスター リカバリー機能を使用していないお客様の場合、スナップショット期間の頻度が減ります。 このような場合、お客様は 12 時間または 24 時間周期で、/hana/data および /hana/shared (/usr/sap を含む) に対して結合スナップショットを実行し、それらのスナップショットを 1 か月間保持します。 ログ バックアップ ボリュームのスナップショットの場合も同じです。 一方、ログ バックアップ ボリュームに対する SAP HANA トランザクション ログ バックアップは、5 ～ 15 分周期で実行されています。

スケジュールされたストレージ スナップショットは、cron を使用することで最適に実行できます。 バックアップとディザスター リカバリーのすべてのニーズに対して同じスクリプトを使用し、要求されたさまざまなバックアップ時間に合わせて、スクリプトの入力を変更してください。 これらのスナップショットは、実行時間に応じて、Cron でさまざまな単位で (1 時間ごと、12 時間ごと、毎日、または毎週) スケジュールされます。 

次に示すのは、/etc/crontab の cron スケジュールの例です。
```
00 1-23 * * * ./azure_hana_backup.pl hana hourlyhana 15min 46
10 00 * * *  ./azure_hana_backup.pl hana dailyhana 15min 28
00,05,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
22 12 * * *  ./azure_hana_backup.pl log dailylogback 3min 28
30 00 * * *  ./azure_hana_backup.pl boot TypeI dailyboot 15min 28
```
前の例では、/hana/data および /hana/shared (/usr/sap を含む) の各場所を含むボリュームを対象とする時間単位の結合スナップショットがあります。 この種のスナップショットは、過去 2 日以内の特定の時点への復旧を迅速化するために使用します。 また、これらのボリュームの日単位のスナップショットもあります。 そのため、時間単位のスナップショットによって 2 日間カバーし、日単位のスナップショットによって 4 週間カバーできます。 さらに、トランザクション ログ バックアップ ボリュームが毎日 1 回バックアップされます。 これらのバックアップも 4 週間保持されます。 crontab の 3 行目でわかるように、HANA トランザクション ログのバックアップが 5 分ごとに実行されるようにスケジュールされています。 ストレージ スナップショットを実行するさまざまな Cron ジョブの開始時間をずらしてあるので、これらのスナップショットが特定の時点に一斉に実行されるわけではありません。 

次の例では、/hana/data および /hana/shared (/usr/sap を含む) の各場所を含むボリュームを対象とする結合スナップショットを時間単位で実行します。 これらのスナップショットは 2 日間保持します。 トランザクション ログ バックアップ ボリュームのスナップショットが 5 分ごとに実行され、4 時間保持されます。 前と同様に、HANA トランザクション ログ ファイルのバックアップが 5 分ごとに実行されるようにスケジュールされています。 トランザクション ログ バックアップ ボリュームのスナップショットは、トランザクション ログ バックが開始されてから 2 分遅れて実行されます。 通常の状況下では、この 2 分以内に SAP HANA トランザクション ログ バックアップが完了します。 前と同様に、ブート LUN を含むボリュームがストレージ スナップショットによって 1 日 1 回バックアップされ、4 週間保持されます。

```
10 0-23 * * * ./azure_hana_backup.pl hana hourlyhana 15min 48
0,5,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup.pl log logback 3min 48
30 00 * * *  ./azure_hana_backup.pl boot TypeII dailyboot 15min 28
```

次の図は、ブート LUN を除き、前の例のシーケンスを示しています。

![バックアップとスナップショットの関係](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA は、データベースに対するコミットされた変更を記録するために、/hana/log ボリュームに対して定期的な書き込みを実行します。 SAP HANA は、/hana/data ボリュームにセーブポイントを定期的に書き込みます。 crontab での指定に従って、SAP HANA トランザクション ログ バックアップが 5 分ごとに実行されます。 また、/hana/data および /hana/shared の各ボリュームに対する結合ストレージ スナップショットのトリガーにより、SAP HANA スナップショットが 1 時間ごとに実行されます。 HANA スナップショットが成功すると、結合ストレージ スナップショットが実行されます。 crontab の指示に従って、5 分ごとに (HANA トランザクション ログ バックアップの約 2 分後) /hana/logbackup ボリュームでのストレージ スナップショットが実行されます。

> 

>[!IMPORTANT]
> SAP HANA バックアップに対するストレージ スナップショットの使用は、スナップショットが SAP HANA トランザクション ログ バックアップと共に実行される場合にのみ有効です。 これらのトランザクション ログ バックアップは、ストレージ スナップショット間の期間をカバーする必要があります。 

30 日間の特定の時点への復旧をユーザーに確約している場合は、次のことが必要です。

- 極端な場合、30 日前の /hana/data および /hana/shared に対する結合ストレージ スナップショットにアクセスします。
- 結合ストレージ スナップショット間の時間をカバーする連続したトランザクション ログ バックアップを保持します。 そのため、トランザクション ログ バックアップ ボリュームの最も古いスナップショットは 30 日前のものである必要があります。 ただし、トランザクション ログ バックアップを、Azure Storage にある別の NFS 共有にコピーしている場合を除きます。 このような場合、その NFS 共有から古いトランザクション ログ バックアップを取得できます。

ストレージ スナップショットとトランザクション ログ バックアップの最終的なストレージ レプリケーションのメリットを得られるようにするには、SAP HANA がトランザクション ログ バックアップを書き込む場所を変更する必要があります。 この変更は、HANA Studio で実行できます。 SAP HANA は完全なログ セグメントを自動的にバックアップしますが、ログ バックアップ間隔を決定論的に指定する必要があります。 特に、ディザスター リカバリー オプションを使用している場合、通常は決定論的期間でログ バックアップを実行します。 次の例では、ログ バックアップの間隔を 15 分に設定しています。

![SAP HANA Studio での SAP HANA バックアップ ログのスケジュール](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

バックアップの間隔は 15 分より短くすることもできます。 間隔の短い設定は多くの場合、HANA L インスタンスのディザスター リカバリー機能と組み合わせて使われます。 トランザクション ログ バックアップを 5 分ごとに実行しているお客様もいます。  

これまでデータベースをバックアップしたことがない場合は、最後にファイル ベースのデータベース バックアップを実行して、バックアップ カタログ内に存在する必要がある単一のバックアップ エントリを作成します。 これを実行しないと、SAP HANA は指定されたログ バックアップを開始できません。

![ファイル ベースのバックアップで単一のバックアップ エントリが作成されるようにする](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


最初の正常なストレージ スナップショットが実行されたら、手順 6 で実行されたテスト スナップショットを削除できます。 削除するには、スクリプト `removeTestStorageSnapshot.pl` を実行します。
```
./removeTestStorageSnapshot.pl
```

スクリプト出力の例を次に示します。
```
Checking Snapshot Status for h80
**********************Checking access to Storage**********************
Storage Snapshot Access successful.
**********************Getting list of volumes that match HANA instance specified**********************
Collecting set of volumes hosting HANA matching pattern *h80* ...
Volume show completed successfully.
Adding volume hana_data_h80_mnt00001_t020_vol to the snapshot list.
Adding volume hana_log_backups_h80_t020_vol to the snapshot list.
Adding volume hana_shared_h80_t020_vol to the snapshot list.
**********************Adding list of snapshots to volume list**********************
Collecting set of snapshots for each volume hosting HANA matching pattern *h80* ...
**********************Displaying Snapshots by Volume**********************
hana_data_h80_mnt00001_t020_vol
Test_HANA_Snapshot.2018-02-06_1753.3
Test_HANA_Snapshot.2018-02-06_1815.2
….
Command completed successfully.
Exiting with return code: 0
Command completed successfully.
```


### <a name="monitoring-the-number-and-size-of-snapshots-on-the-disk-volume"></a>ディスク ボリュームにあるスナップショットの数とサイズの監視

特定のストレージ ボリュームで、スナップショットの数とそれらのスナップショットによるストレージの使用量を監視できます。 `ls` コマンドでは、スナップショットのディレクトリまたはファイルが表示されません。 ただし、ストレージ スナップショットは同じボリュームに保存されているので、Linux OS コマンドの `du` を使用すると、それらのスナップショットの詳細が表示されます。 このコマンドは次のオプションと共に使用できます。

- `du –sh .snapshot`: このオプションを使用すると、スナップショットのディレクトリ内にあるすべてのスナップショットの合計が表示されます。
- `du –sh --max-depth=1`: このオプションを使用すると、**.snapshot** フォルダーに保存されているすべてのスナップショットと各スナップショットのサイズが一覧表示されます。
- `du –hc`: このオプションを使用すると、すべてのスナップショットによって使用される合計サイズが表示されます。

作成および格納したスナップショットがボリューム上のストレージを使用し尽くしていないことを確認するには、上記のコマンドを使用します。

>[!NOTE]
>ブート LUN のスナップショットは、上記のコマンドでは表示されません。

### <a name="getting-details-of-snapshots"></a>スナップショットの詳細の取得
スナップショットの詳細を取得するには、スクリプト `azure_hana_snapshot_details.pl` を使用する方法もあります。 ディザスター リカバリーの場所にアクティブなサーバーがある場合は、このスクリプトをいずれかの場所で実行できます。 このスクリプトは、スナップショットを含むボリュームごとに分類して次の出力を提供します。 
   * ボリュームの合計スナップショットのサイズ
   * そのボリュームの各スナップショットに関する次の詳細情報: 
      - スナップショット名 
      - 作成時刻 
      - スナップショットのサイズ
      - スナップショットの頻度
      - 該当する場合、そのスナップショットに関連付けられている HANA バックアップ ID

スクリプト実行構文の例を次に示します。

```
./azure_hana_snapshot_details.pl 
```

スクリプトは HANA Backup ID の取得を試みるため、SAP HANA インスタンスへの接続が必要になります。 この接続には、構成ファイル *HANABackupCustomerDetails.txt* を正しく設定する必要があります。 ボリューム上の 2 つのスナップショットの出力は次のようになります。

```
**********************************************************
****Volume: hana_shared_SAPTSTHDB100_t020_vol       ***********
**********************************************************
Total Snapshot Size:  411.8MB
----------------------------------------------------------
Snapshot:   customer.2016-09-20_1404.0
Create Time:   "Tue Sep 20 18:08:35 2016"
Size:   2.10MB
Frequency:   customer 
HANA Backup ID:   
----------------------------------------------------------
Snapshot:   customer2.2016-09-20_1532.0
Create Time:   "Tue Sep 20 19:36:21 2016"
Size:   2.37MB
Frequency:   customer2
HANA Backup ID:   
```


### <a name="file-level-restore-from-a-storage-snapshot"></a>ストレージ スナップショットからのファイル レベルの復元
スナップショットの種類が **hana** または **logs** の場合、ボリューム上の **.snapshot** ディレクトリにあるスナップショットに直接アクセスできます。 スナップショットごとにサブディレクトリがあります。 各ファイルは、スナップショットの作成時点での状態で、該当のサブディレクトリから実際のディレクトリ構造にコピーできます。 現在のバージョンのスクリプトでは、セルフサービスのスナップショットの復元用に提供されている復元スクリプトが**ありません** (スナップショットの復元は、フェールオーバー中の DR サイトのセルフサービス DR スクリプトの一部として実行できます)。 既存の使用可能なスナップショットから目的のスナップショットを復元するには、サービス要求を開いて、Microsoft の運用チームに問い合わせる必要があります。

>[!NOTE]
>単一ファイルの復元は、HANA Large Instance ユニットの種類とは関係のないブート LUN のスナップショットに対しては機能しません。 **.snapshot** ディレクトリは、ブートLUN では公開されません。 


### <a name="reducing-the-number-of-snapshots-on-a-server"></a>サーバー上のスナップショットの数の削減

既に説明したように、特定のラベルで格納されるスナップショットの数を減らすことができます。 スナップショットを開始するコマンドの最後の 2 つのパラメーターは、ラベルと、保持するスナップショットの数です。

```
./azure_hana_backup.pl hana dailyhana 15min 28
```

前の例では、スナップショット ラベルは **dailyhana** であり、保持するこのラベルのスナップショットの数は **28** です。 ディスク領域の使用量に応じて、格納されるスナップショットの数を減らすことができます。 スナップショットの数をたとえば 15 個に減らす簡単な方法として、最後のパラメーターを **15** に設定してスクリプトを実行します。

```
./azure_hana_backup.pl hana dailyhana 15min 15
```

この設定でスクリプトを実行すると、スナップショットの数は (新しいストレージ スナップショットを含めて) 15 個になります。 15 個の最新のスナップショットが保持され、15 個の古いスナップショットが削除されます。

 >[!NOTE]
 > このスクリプトでは、1 時間以上経過したスナップショットが存在する場合にのみ、スナップショットの数を減らします。 このスクリプトでは作成から 1 時間経過していないスナップショットは削除されません。 これらの制限は、提供されるオプションのディザスター リカバリー機能に関連しています。

バックアップ ラベル (構文例では **hanadaily**) が付けられた一連のスナップショットを保持する必要がなくなった場合は、保持数を **0** に設定してスクリプトを実行します。 これにより、そのラベルと一致するすべてのスナップショットが削除されます。 ただし、すべてのスナップショットを削除すると、HANA L インスタンスのディザスター リカバリー機能に影響する可能性があります。

特定のスナップショットを削除するもう 1 つの方法は、スクリプト `azure_hana_snapshot_delete.pl` を使用することです。 このスクリプトは、HANA Studio で確認できる HANA バックアップ ID、またはストレージ スナップショット名自体を使用して、1 つのスナップショットまたは一連のスナップショットを削除することを目的としています。 現在、バックアップ ID は、スナップショットの種類が **hana** である作成済みのスナップショットにのみ関連付けられています。 **logs** と **boot** のスナップショット バックアップでは SAP HANA スナップショットは実行されないため、これらのスナップショットのバックアップ ID はありません。 スナップショット名を入力すると、入力したスナップショット名に一致するすべてのスナップショットがさまざまなボリューム上で検索されます。 

HANA インスタンスの SID を指定する必要があるスクリプトを呼び出すには、次のスクリプト呼び出し構文を使用します。

```
./azure_hana_snapshot_delete.pl <SID>

```

このスクリプトは、**root** ユーザーとして実行します。

スナップショットを選択すると、各スナップショットを個別に削除できます。 まず、スナップショットが格納されているボリュームを求められ、次にスナップショット名の入力を求められます。 スナップショットがそのボリュームに存在し、1 時間以上前のものであれば、スナップショットは削除されます。 ボリューム名とスナップショット名を確認するには、`azure_hana_snapshot_details` スクリプトを実行します。 

>[!IMPORTANT]
>削除しようとしているスナップショット上にのみ存在するデータがある場合、スナップショットが削除されると、そのデータが永久に失われることになります。

   

### <a name="recover-to-the-most-recent-hana-snapshot"></a>最新の HANA スナップショットへの復旧

運用環境停止のシナリオが発生した場合は、Microsoft Azure サポートでの顧客インシデントとして、ストレージ スナップショットからの復旧プロセスを開始できます。 運用システムのデータが削除されており、データを取得する唯一の方法が運用データベースの復元である場合は、緊急度の高い問題となります。

一方、特定の時点への復旧は緊急度が低く、数日前から計画されている場合があります。 この復旧は、優先度の高いフラグを設定するのではなく、SAP HANA on Azure サービス管理と共に計画できます。 たとえば、新しい拡張機能パッケージを適用して、SAP ソフトウェアのアップグレードを計画する場合があります。 次に、拡張機能パッケージのアップグレード前の状態を表すスナップショットに戻す必要があります。

要求を送信する前に準備する必要があります。 そうすることで、SAP HANA on Azure サービス管理チームが要求を処理し、復元されたボリュームを提供できます。 その後、スナップショットに基づいて HANA データベースを復元します。 

次に示すのは、要求の準備をする方法です。

>[!NOTE]
>使用中の SAP HANA リリースによっては、ユーザー インターフェイスが以下のスクリーンショットと異なる場合があります。

1. 復元するスナップショットを決めます。 他に指示がない限り、hana/data ボリュームのみが復元されます。 

1. HANA インスタンスをシャットダウンします。

 ![HANA インスタンスをシャットダウンする](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. 各 HANA データベース ノードで、データ ボリュームのマウントを解除します。 データ ボリュームがオペレーティング システムに引き続きマウントされていると、スナップショットの復元は失敗します。
 ![各 HANA データベース ノードでデータ ボリュームのマウントを解除する](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Azure サポート要求を開き、特定のスナップショットの復元に関する指示を記入します。

 - 復元中: SAP HANA on Azure サービス管理は、適切なストレージ スナップショットを確実に復元するための調整、検証、確認のために、電話会議への出席を依頼する場合があります。 

 - 復元後: SAP HANA on Azure サービス管理から、ストレージ スナップショットの復元が完了した時点で通知が届きます。

1. 復元処理が完了したら、すべてのデータ ボリュームを再マウントします。

 ![すべてのデータ ボリュームを再マウントする](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)

1. SAP HANA Studio で復旧オプションを選択します (SAP HANA Studio で HANA DB に再接続したときに自動的に選択されない場合)。 次の例は、最新の HANA スナップショットへの復元を示しています。 1 つのストレージ スナップショットには、1 つの HANA スナップショットが埋め込まれています。 最新のストレージ スナップショットに復元するには、最新の HANA スナップショットである必要があります  (それより前のストレージ スナップショットに復元している場合は、ストレージ スナップショットが作成された時刻に基づいて HANA スナップショットを探す必要があります)。

 ![SAP HANA Studio 内で復旧オプションを選択する](./media/hana-overview-high-availability-disaster-recovery/image10-recover-options-a.png)

1. **[Recover the database to a specific data backup or storage snapshot (特定のデータ バックアップまたはストレージ スナップショットにデータベースを復旧する)]** を選択します。

 ![[Specify Recovery Type]\(復旧の種類を指定する\) ウィンドウ](./media/hana-overview-high-availability-disaster-recovery/image11-recover-options-b.png)

1. **[Specify backup without catalog (カタログのないバックアップを指定する)]** を選択します。

 ![[Specify Backup Location]\(バックアップ場所を指定する\) ウィンドウ](./media/hana-overview-high-availability-disaster-recovery/image12-recover-options-c.png)

1. **[Destination Type (復旧先の種類)]** の一覧で **[Snapshot (スナップショット)]** を選択します。

 ![[Specify the Backup to Recover]\(復旧するバックアップを指定する\) ウィンドウ](./media/hana-overview-high-availability-disaster-recovery/image13-recover-options-d.png)

1. **[Finish]\(完了\)** をクリックして復旧処理を開始します。

 ![[Finish]\(完了\) をクリックして復旧処理を開始します](./media/hana-overview-high-availability-disaster-recovery/image14-recover-options-e.png)

1. HANA データベースが復元され、ストレージ スナップショットに含まれている HANA スナップショットに復旧されます。

 ![HANA データベースが復元され、HANA スナップショットに復旧される](./media/hana-overview-high-availability-disaster-recovery/image15-recover-options-f.png)

### <a name="recover-to-the-most-recent-state"></a>最新の状態への復旧

次の手順を使用して、ストレージ スナップショットに含まれている HANA スナップショットを復元します。 次に、トランザクション ログ バックアップを、ストレージ スナップショットを復元する前のデータベースの最新状態に復元します。

>[!IMPORTANT]
>作業を進める前に、完全かつ連続した一連のトランザクション ログ バックアップがあることを確認します。 これらのバックアップがない場合、データベースの現在の状態を復元することはできません。

1. 「[最新の HANA スナップショットへの復旧](#recovering-to-the-most-recent-hana-snapshot)」の手順 1 ～ 6 を完了します。

1. **[Recover the database to its most recent state (データベースを最新の状態に復旧する)]** を選択します。

 ![[Recover the database to its most recent state (データベースを最新の状態に復旧する)] を選択する](./media/hana-overview-high-availability-disaster-recovery/image16-recover-database-a.png)

1. 最新の HANA ログ バックアップの場所を指定します。 この場所には、HANA スナップショットから最新の状態まで、すべての HANA トランザクション ログ バックアップが含まれている必要があります。

 ![最新の HANA ログ バックアップの場所を指定する](./media/hana-overview-high-availability-disaster-recovery/image17-recover-database-b.png)

1. データベースを復旧するためのベースとなるバックアップを選択します。 この例では、スクリーンショットの HANA スナップショットは、ストレージ スナップショットに含まれていた HANA スナップショットです。 

 ![データベースを復旧するためのベースとなるバックアップを選択する](./media/hana-overview-high-availability-disaster-recovery/image18-recover-database-c.png)

1. HANA スナップショットの時刻から最新の状態までの間に差分がない場合は、**[Use Delta Backups (差分バックアップを使用する)]** チェック ボックスをオフにします。

 ![差分がない場合に [Use Delta Backups (差分バックアップを使用する)] チェック ボックスをオフにする](./media/hana-overview-high-availability-disaster-recovery/image19-recover-database-d.png)

1. 概要画面で **[Finish]\(完了\)** を選択して復元処理を開始します。

 ![概要画面で [Finish (完了)] をクリックする](./media/hana-overview-high-availability-disaster-recovery/image20-recover-database-e.png)

### <a name="recover-to-another-point-in-time"></a>異なる特定の時点への復旧
(ストレージ スナップショットに含まれている) HANA スナップショットから、HANA スナップショットによる特定の時点の復旧より後のスナップショットまでの時点に復旧する場合、次の手順を実行します。

1. 復旧しようとしている時点について、HANA スナップショットからのすべてのトランザクション ログ バックアップがあることを確認します。
1. 「[最新の状態への復旧](#recovering-to-the-most-recent-state)」の操作を開始します。
1. 操作の手順 2 で、**[Specify Recovery Type (復旧の種類を指定する)]** ウィンドウの **[Recover the database to the following point in time]\(データベースを次の時点に復旧する\)** を選択し、特定の時点を指定します。 
1. 手順 3 ～ 6 を完了します。

### <a name="monitor-the-execution-of-snapshots"></a>スナップショットの実行の監視

HANA L インスタンスのストレージ スナップショットを使用するときは、それらのスナップショットの実行を監視することも必要です。 ストレージ スナップショットを実行するスクリプトでは、出力がファイルに書き込まれ、Perl スクリプトと同じ場所に保存されます。 ストレージ スナップショットごとに個別のファイルが書き込まれます。 各ファイルの出力には、スナップショット スクリプトが実行するさまざまな段階が示されます。

1. スナップショットを作成する必要があるボリュームを探します。
1. これらのボリュームから作成されたスナップショットを探します。
1. 指定されたスナップショット数に一致するように、最終的に存在しているスナップショットを削除します。
1. SAP HANA スナップショットを作成します。
1. 複数のボリュームにわたるストレージ スナップショットを作成します。
1. SAP HANA スナップショットを削除します。
1. 最新のスナップショットの名前を **.0** に変更します。

この部分を示すスクリプト cab の最も重要な部分は次のとおりです。
```
**********************Creating HANA snapshot**********************
Creating the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data create snapshot"" ...
HANA snapshot created successfully.
**********************Creating Storage snapshot**********************
Taking snapshot hourly.recent for hana_data_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_backup_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_shared_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for sapmnt_lhanad01_t020_vol ...
Snapshot created successfully.
**********************Deleting HANA snapshot**********************
Deleting the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data drop snapshot"" ...
HANA snapshot deletion successfully.
```
このサンプルから、スクリプトが HANA スナップショットの作成を記録する方法がわかります。 スケールアウトの場合は、このプロセスがマスター ノードで開始されます。 マスター ノードは、各ワーカー ノードで SAP HANA スナップショットの同期的な作成を開始します。 その後、ストレージ スナップショットが作成されます。 ストレージ スナップショットの実行に成功すると、HANA スナップショットが削除されます。 HANA スナップショットの削除は、マスター ノードから開始されます。


## <a name="disaster-recovery-principles"></a>ディザスター リカバリーの原則
HANA L インスタンスでは、さまざまな Azure リージョンの HANA L インスタンス スタンプ間でのディザスター リカバリー機能が提供されます。 たとえば、Azure の米国西部リージョンに HANA L インスタンス ユニットをデプロイした場合、米国東部リージョンの HANA L インスタンス ユニットをディザスター リカバリー ユニットとして使用できます。 前述のように、ディザスター リカバリーでは DR リージョンの別の HANA L インスタンス ユニットの料金を支払う必要があるため、ディザスター リカバリーは自動的には構成されません。 ディザスター リカバリー セットアップは、スケールアップ セットアップとスケールアウト セットアップに対応しています。 

これまでにデプロイされたシナリオでは、お客様は DR リージョンのユニットを使って、インストール済みの HANA インスタンスを使用する非運用システムを実行していました。 HANA L インスタンス ユニットは、運用環境で使用される SKU と同じ SKU である必要があります。 次の図は、Azure 運用リージョンとディザスター リカバリー リージョンのサーバー ユニット間のディスク構成を示したものです。

![ディスクの観点から見た DR セットアップ構成](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

この概要図からわかるように、2 つ目のディスク ボリューム セットを注文する必要があります。 ターゲット ディスク ボリュームは、ディザスター リカバリー ユニットの実稼働インスタンス用の実稼働ボリュームと同じサイズです。 これらのディスク ボリュームは、ディザスター リカバリー サイトの HANA L インスタンス サーバー ユニットと関連付けられます。 次のボリュームは、実稼働リージョンから DR サイトにレプリケートされます。

- /hana/data
- /hana/logbackups 
- /hana/shared (/usr/sap を含む)

これらのボリュームの復元が実行される方法では、SAP HANA トランザクション ログが必要ないため、/hana/log ボリュームはレプリケートされません。 

提供される ディザスター リカバリー機能の基礎は、HANA L インスタンス インフラストラクチャによって提供されるストレージ レプリケーション機能です。 ストレージ側で使用される機能は、ストレージ ボリュームに対する変更が発生したときに非同期で変更を絶えずレプリケートするものではありません。 これらのボリュームでスナップショットが定期的に作成されるということに依存するメカニズムです。 既にレプリケートされたスナップショットとまだレプリケートされていない新しいスナップショット間の差分が、ディザスター リカバリー サイトのターゲット ディスク ボリュームに転送されます。  これらのスナップショットはボリュームに保存され、ディザスター リカバリー フェールオーバーが発生した場合は、それらのボリュームに復元する必要があります。  

ボリュームの完全なデータを初めて転送する場合、データのサイズがスナップショット間の差分よりも小さくなる前に転送を実行する必要があります。 そのため、DR サイトのボリュームには、実稼働サイトで実行されたすべてのボリューム スナップショットが含まれています。 これにより、運用システムをロールバックすることなく、その DR システムを使用して以前の状態に戻し、失われたデータを最終的に回復することができます。

1 つの HANA L インスタンス ユニットに複数の独立した SAP HANA インスタンスが含まれる MCOD デプロイの場合は、すべての SAP HANA インスタンスのストレージが DR 側にレプリケートされることが予想されます。

運用サイトで HANA System Replication を高可用性機能として使用し、DR サイトに対してストレージベースのレプリケーションを使用している場合、プライマリ サイトから DR インスタンスに対する両方のノードのボリュームがレプリケートされます。 プライマリとセカンダリの両方から DR に対するレプリケーションに対応するには、DR サイトに追加のストレージ (プライマリ ノードと同じサイズ) を購入する必要があります。 



>[!NOTE]
>HANA L インスタンスのストレージ レプリケーション機能では、ストレージ スナップショットをミラーリングおよびレプリケートします。 この記事の「[バックアップと復元](#backup-and-restore)」セクションで紹介したストレージ スナップショットを実行していない場合、ディザスター リカバリー サイトへのレプリケーションを実行することはできません。 ストレージ スナップショットの実行は、ディザスター リカバリー サイトへのストレージ レプリケーションの前提条件となります。



## <a name="preparation-of-the-disaster-recovery-scenario"></a>ディザスター リカバリー シナリオの準備
このシナリオでは、運用システムが運用 Azure リージョンの HANA L インスタンス上で実行されているものとします。 後に続く手順では、その HANA システムの SID が "PRD" であり、非運用システムが DR Azure リージョン内の HANA L インスタンス上で実行されているものと仮定します。 後者のシステムについては、SID が "TST" であると仮定します。 次の図は、この構成を示したものです。

![DR セットアップの開始](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

サーバー インスタンスについて追加のストレージ ボリューム セットをまだ指定していない場合、SAP HANA on Azure サービス管理によって、TST HANA インスタンスを実行している HANA L インスタンス ユニットに対する運用レプリカのターゲットとして、追加のボリューム セットがアタッチされます。 そのため、実稼働 HANA インスタンスの SID を指定する必要があります。 SAP HANA on Azure サービス管理でボリュームのアタッチを確定した後は、それらのボリュームを HANA L インスタンス ユニットにマウントする必要があります。

![DR セットアップの次の手順](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

次の手順は、TST HANA インスタンスを実行している DR Azure リージョンの HANA L インスタンス ユニットに、2 つ目の SAP HANA インスタンスをインストールすることです。 新しくインストールする SAP HANA インスタンスの SID は同じにする必要があります。 作成したユーザーは、実稼働インスタンスと同じ UID とグループ ID を持っている必要があります。 インストールが成功した場合、以下の手順を実行する必要があります。

- この記事で前述した、ストレージ スナップショットの準備のステップ 2 を実行します。
- まだ作成していない場合は、HANA L インスタンス ユニットの DR ユニットに対する公開キーを作成します。 この記事で前述した、ストレージ スナップショットの準備のステップ 3 をご覧ください。
- 新しい HANA インスタンスで *HANABackupCustomerDetails.txt* を更新し、ストレージへの接続が正常に動作することをテストします。  
- DR Azure リージョンの HANA L インスタンス ユニットに新しくインストールした SAP HANA インスタンスを停止します。
- PRD ボリュームのマウントを解除し、SAP HANA on Azure サービス管理に連絡します。 ストレージ レプリケーション ターゲットとして機能している間はボリュームにアクセスできないため、ボリュームをユニットにマウントしたままにすることはできません。  

![レプリケーションを確立する前の DR セットアップ手順](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

オペレーション チームは、運用 Azure リージョンの PRD ボリュームと、DR Azure リージョンの PRD ボリューム間にレプリケーション関係を確立します。

>[!IMPORTANT]
>レプリケートされた SAP HANA データベースをディザスター リカバリー サイトで一貫した状態に復元する必要がないため、/hana/log ボリュームはレプリケートされません。

次に、障害発生時に目標とする RTO と RPO に合わせて、ストレージ スナップショット バックアップ スケジュールをセットアップまたは調整します。 目標復旧時点を最小限に抑えるために、HANA L インスタンス サービスでは次のレプリケーション間隔を設定します。
- 結合スナップショット (スナップショットの種類は **hana**) の対象となるボリュームについては、ディザスター リカバリー サイトの同等のストレージ ボリューム ターゲットに 15 分ごとにレプリケートされるように設定します。
- トランザクション ログ バックアップ ボリューム (スナップショットの種類は **logs**) については、ディザスター リカバリー サイトの同等のストレージ ボリューム ターゲットに 3 分ごとにレプリケートされるように設定します。

目標復旧時点を最小限に抑えるには、次のようにセットアップします。
- 種類が **hana** のストレージ スナップショット (「手順 7: スナップショットを実行する」を参照) を 30 分～ 1 時間ごとに実行する。
- SAP HANA トランザクション ログ バックアップを 5 分ごとに実行する。
- 種類が **logs** のストレージ スナップショットを 5 ～ 15 分ごとに実行する。 この間隔で約 15 ～ 25 分の RPO を実現できます。

このセットアップでは、トランザクション ログ バックアップ、ストレージ スナップショット、HANA トランザクション ログ バックアップ ボリュームと /hana/data、/hana/shared (/usr/sap を含む) のレプリケーションのシーケンスは下図のデータのようになります。

 ![時間軸でのトランザクション ログ バックアップ スナップショットとスナップ ミラーの関係](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

ディザスター リカバリーの場合に RPO をさらに短縮するには、SAP HANA on Azure (L インスタンス) の HANA トランザクション ログ バックアップをもう一方の Azure リージョンにコピーします。 この RPO の短縮を実現するには、次の手順を実行します。

1. HANA トランザクション ログをできるだけ頻繁に /hana/logbackups にバックアップします。
1. rsync を使用して、NFS 共有でホストされている Azure 仮想マシンにトランザクション ログのバックアップをコピーします。 VM は、Azure 実稼働リージョンと DR リージョンの Azure 仮想ネットワーク内にあります。 実稼働 HANA L インスタンスを Azure に接続する回路に、両方の Azure 仮想ネットワークを接続する必要があります。 「[HANA L インスタンスでのディザスター リカバリーのネットワークに関する考慮事項](#Network-considerations-for-disaster recovery-with-HANA-Large-Instances)」セクションの図を参照してください。 
1. そのリージョン内の VM に接続されている NFS エクスポートされたストレージにトランザクション ログ バックアップを保持します。
1. 障害時のフェールオーバーの場合、/hana/logbackups ボリューム上のトランザクション ログ バックアップを、ディザスター リカバリー サイトの NFS 共有上のつい最近作成されたトランザクション ログ バックアップで補完します。 
1. DR リージョンに保存されている可能性のある最新のバックアップへのトランザクション ログ バックアップの復元を開始します。

レプリケーション関係がセットアップされていることを HANA L インスタンスのオペレーション チームが確認し、お客様が実行ストレージ スナップショット バックアップを開始したら、データのレプリケートが開始されます。

![レプリケーションを確立する前の DR セットアップ手順](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

レプリケーションの処理中は、DR Azure リージョンの PRD ボリューム上のスナップショットは復元されません。 単に保存されるだけです。 このような状態でボリュームがマウントされている場合、PRD SAP HANA インスタンスが DR Azure リージョンのサーバー ユニットにインストールされた後に、それらのボリュームのマウントは解除された状態と表示されます。 また、まだ復元されていないストレージ バックアップであると表示されます。

フェールオーバーが発生した場合、最新のストレージ スナップショットではなく、古いストレージ スナップショットへの復元を選択することもできます。

## <a name="disaster-recovery-failover-procedure"></a>ディザスター リカバリーのフェールオーバー手順
DR サイトにフェールオーバーするときは、2 つのケースを考慮する必要があります。

- SAP HANA データベースのデータを最新の状態に戻す必要がある場合。 この場合は、Microsoft に連絡することなく、セルフサービス スクリプトを使用してフェールオーバーを実行できます。 ただし、フェールバックの場合は Microsoft と協力する必要があります。
- 最新のレプリケートされたスナップショットではないストレージ スナップショットに復元する場合。 この場合は、Microsoft と連携する必要があります。 

>[!NOTE]
>以下の手順は、DR ユニットを表す HANA L インスタンス ユニットで実行する必要があります。 
 
レプリケートされた最新のストレージ スナップショットに復元するには、次の手順を実行します。 

1. 実行しているHANA L インスタンスのディザスター リカバリー ユニットで、HANA の非運用インスタンスをシャットダウンします。 これは、プレインストールされた休止中の HANA 運用インスタンスが存在するためです。
1. SAP HANA プロセスが実行されていないことを確認します。 この確認には、`/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList` コマンドを使用します。 出力では、**hdbdaemon** プロセスが停止状態であり、実行中または開始済み状態の他の HANA プロセスが存在しないことが示されます。
1. DR サイトの HANA L インスタンス ユニットで、スクリプト *azure_hana_dr_failover.pl* を実行します。 スクリプトは SAP HANA SID の復元を要求します。 要求されたら、DR サイトの HANA L インスタンス ユニット上の *HANABackupCustomerDetails.txt* ファイルにレプリケートされて保持されている SAP HANA SID を入力します。 

      複数の SAP HANA インスタンスをフェールオーバーする場合は、スクリプトを複数回実行する必要があります。 要求されたら、フェールオーバーして復元する SAP HANA の SID を入力します。 完了すると、HANA L インスタンス ユニットに追加されるボリュームのマウント ポイントの一覧がスクリプトにより表示されます。 この一覧には、復元された DR ボリュームも含まれています。

1. Linux オペレーティング システムのコマンドを使って、ディザスター リカバリー サイトの HANA L インスタンス ユニットに、復元されたディザスター リカバリー ボリュームをマウントします。 
1. 休止中の SAP HANA 運用インスタンスを起動します。
1. RPO 時間を短縮するためにトランザクション ログ バックアップ ログをコピーする場合は、新たにマウントされた DR の /hana/logbackups ディレクトリにそれらのトランザクション ログ バックアップをマージする必要があります。 既存のバックアップは上書きしないでください。 ストレージ スナップショットの最新のレプリケーションでレプリケートされなかった新しいバックアップをコピーします。
1. また、DR Azure リージョンの /hana/shared/PRD ボリュームにレプリケートされたスナップショットから、1 つのファイルを復元することもできます。 

実際のレプリケーション リレーションシップに影響を与えずに、DR フェールオーバーをテストすることもできます。 テスト フェールオーバーを実行するには、前述の手順 1 と 2 を実行した後、下記の手順 3 に進みます。

>[!IMPORTANT]
>手順 3 に示すスクリプトを使った**フェールオーバー テスト**のプロセスによって DR サイトに作成したインスタンスでは、運用トランザクションは一切実行*しないでください*。 このコマンドでは、プライマリ サイトへのリレーションシップを持たないボリュームのセットが作成されます。 そのため、プライマリ サイトに同期することは*できません*。 

フェールオーバー テストの手順 3:

DR サイトの HANA L インスタンス ユニットで、スクリプト **azure_hana_test_dr_failover.pl** を実行します。 このスクリプトは、プライマリ サイトと DR サイトの間のレプリケーション リレーションシップを停止*しません*。 代わりに、このスクリプトは DR ストレージ ボリュームを複製します。 複製プロセスが成功した後、複製されたボリュームは最新のスナップショットの状態に復元され、DR ユニットにマウントされます。 スクリプトは SAP HANA SID の復元を要求します。 DR サイトの HANA L インスタンス ユニット上の *HANABackupCustomerDetails.txt* ファイルにレプリケートされて保持されている SAP HANA SID を入力します。 

複数の SAP HANA インスタンスをテストする場合は、スクリプトを複数回実行する必要があります。 要求されたら、フェールオーバーをテストするインスタンスの SAP HANA SID を入力します。 完了すると、HANA L インスタンス ユニットに追加されるボリュームのマウント ポイントの一覧がスクリプトにより表示されます。 この一覧には、複製された DR ボリュームも含まれています。

手順 4 に進みます。

   >[!NOTE]
   >何時間も前に削除されたデータを復旧するために DR サイトにフェールオーバーする必要があり、かつ DR ボリュームを以前のスナップショットに設定する必要がある場合は、この手順を適用します。 

1. 実行しているHANA L インスタンスのディザスター リカバリー ユニットで、HANA の非運用インスタンスをシャットダウンします。 これは、プレインストールされた休止中の HANA 運用インスタンスが存在するためです。
1. SAP HANA プロセスが実行されていないことを確認します。 この確認には、`/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList` コマンドを使用します。 出力では、**hdbdaemon** プロセスが停止状態であり、実行中または開始済み状態の他の HANA プロセスが存在しないことが示されます。
1. ディザスター リカバリー サイトをどのスナップショット名やどの SAP HANA バックアップ ID に復元するかを確認します。 実際のディザスター リカバリーの場合、通常、このスナップショットは最新のスナップショットです。 失われたデータを復旧する必要がある場合は、古いスナップショットを選択します。
1. 高優先度のサポート要求を通じて Azure サポートに問い合わせます。 そのスナップショット (スナップショットの名前と日付を伝えます) か、DR サイト上の HANA バックアップ ID の復元を要請します。 既定では、オペレーション チームは /hana/data ボリュームのみを復元します。 /hana/logbackups ボリュームも必要な場合は、その点を明確に伝える必要があります。 */hana/shared ボリュームは復元しないでください。* 代わりに、PRD の /hana/shared ボリュームを再びマウントした後に、**.snapshot** ディレクトリとそのサブディレクトリの global.ini など、特定のファイルを選択することをお勧めします。 

   オペレーション チーム側では、次の手順が発生します。

   a。 運用ボリュームからディザスター リカバリー ボリュームへのスナップショットのレプリケーションが停止されます。 ディザスター リカバリー手順を実行することが必要になった理由が運用サイトの障害の場合、既にこの中断が発生している可能性があります。
   
   b. お客様が選択したストレージ スナップショット名またはバックアップ ID が指定されたスナップショットが、ディザスター リカバリー ボリュームで復元されます。
   
   c. 復元後、ディザスター リカバリー ボリュームは、ディザスター リカバリー リージョンの HANA L インスタンス ユニットにマウントできる状態になります。
      
1. ディザスター リカバリー サイトの HANA L インスタンス ユニットにディザスター リカバリー ボリュームをマウントします。 
1. 休止中の SAP HANA 運用インスタンスを起動します。
1. RPO 時間を短縮するためにトランザクション ログ バックアップ ログをコピーする場合は、新たにマウントされた DR の /hana/logbackups ディレクトリにそれらのトランザクション ログ バックアップをマージする必要があります。 既存のバックアップは上書きしないでください。 ストレージ スナップショットの最新のレプリケーションでレプリケートされなかった新しいバックアップをコピーします。
1. また、DR Azure リージョンの /hana/shared/PRD ボリュームにレプリケートされたスナップショットから、1 つのファイルを復元することもできます。

次の一連の手順では、復元されたストレージ スナップショットと利用可能なトランザクション ログ バックアップに基づいて、SAP HANA 運用インスタンスを復旧します。

1. SAP HANA Studio を使用して、バックアップ場所を **/hana/logbackups** に変更します。
   ![DR の復旧のバックアップ場所を変更する](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA によってバックアップ ファイルの場所がスキャンされ、復元対象となる最新のトランザクション ログ バックアップが提示されます。 次のような画面が表示されるまで、スキャンに数分かかることがあります。![DR 復旧のトランザクション ログ バックアップの一覧](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. 既定の設定の一部を調整します。

      - **[Use Delta Backups]\(差分バックアップを使用する\)** をオフにします。
      - **[Initialize Log Area]\(ログ領域を初期化する\)** をオンにします。

   ![ログ領域の初期化を設定する](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. **[完了]** を選択します。

   ![DR の復元を完了する](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

次のような進行状況ウィンドウが表示されます。 この例は、3 ノードのスケールアウト SAP HANA 構成のディザスター リカバリーの復元を示していることに注意してください。

![復元の進行状況](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

復元が **[Finish]\(完了\)** 画面で停止しているように思われ、進行状況画面が表示されない場合は、ワーカー ノードのすべての SAP HANA インスタンスが実行されていることを確認します。 必要に応じて、SAP HANA インスタンスを手動で起動します。


### <a name="failback-from-a-dr-to-a-production-site"></a>DR サイトから運用サイトへのフェールバック
DR サイトから実稼働サイトにフェールバックすることができます。 ディザスター リカバリー サイトへのフェールオーバーが運用 Azure リージョンでの問題に起因するものであり、失われたデータを回復する必要はないというシナリオについて見てみましょう。 ディザスター リカバリー サイトで、しばらく SAP 運用ワークロードを実行している状態です。 運用サイトでの問題が解決したら、運用サイトでフェールバックを実行できます。 データが失われないように、実稼働サイトに戻す手順では、複数の手順と SAP HANA on Azure オペレーション チームとの緊密な連携が必要となります。 問題が解決された後は、お客様のタイミングで、運用サイトへの同期を開始する操作をオペレーション チームに依頼します。

実行するべき一連の手順は次のとおりです。

1. SAP HANA on Azure オペレーション チームが、ディザスター リカバリー ストレージ ボリュームから運用ストレージ ボリュームを同期する依頼を受けます。この時点で、ディザスター リカバリー ストレージ ボリュームは運用状態になっています。 この状態では、実稼働サイトの HANA L インスタンス ユニットはシャットダウンされています。
1. SAP HANA on Azure オペレーション チームはレプリケーションを監視し、お客様に通知する前にキャッチアップが完了していることを確認します。
1. お客様は、ディザスター リカバリー サイトの HANA 運用インスタンスを使用しているアプリケーションをシャットダウンします。 次に、HANA トランザクション ログのバックアップを実行します。 次に、ディザスター リカバリー サイトの HANA L インスタンス ユニットで実行されている HANA インスタンスを停止します。
1. ディザスター リカバリー サイトの HANA L インスタンス ユニットで実行されている HANA インスタンスがシャットダウンされたら、オペレーション チーム側でディスク ボリュームをもう一度手動で同期する必要があります。
1. SAP HANA on Azure オペレーション チームは、実稼働サイトで HANA L インスタンス ユニットをもう一度起動し、お客様に引き渡します。 HANA L インスタンス ユニットの起動時に、SAP HANA インスタンスがシャットダウン状態であることを確認します。
1. 以前にディザスター リカバリー サイトへのフェールオーバーを実行したときと同じデータベース復元手順を実行します。

### <a name="monitor-disaster-recovery-replication"></a>ディザスター リカバリー レプリケーションの監視

`azure_hana_replication_status.pl` スクリプトを実行することによって、ストレージ レプリケーションの進行状況の状態を監視できます。 このスクリプトを正常に機能させるには、ディザスター リカバリーの場所で実行されているユニットからスクリプトを実行する必要があります。 スクリプトはレプリケーションがアクティブかどうかに関係なく動作します。 ディザスター リカバリーの場所でテナントの各HANA L インスタンス ユニットに対してスクリプトを実行できます。 このスクリプトを使用して、ブート ボリュームの詳細情報を取得することはできません。

スクリプトを呼び出すには、次のコマンドを使います。
```
./replication_status.pl <HANA SID>
```

出力は、ボリューム別に次のセクションに分けられています。  

- リンクの状態
- 現在のレプリケーション アクティビティ
- レプリケートされた最新のスナップショット 
- 最新のスナップショットのサイズ
- スナップショット間の現在のラグ タイム (最後に完了したスナップショット レプリケーションから現在まで)

場所間のリンクがダウンしている場合や現在進行中のフェールオーバー イベントがある場合を除き、リンクの状態は **Active** と表示されます。 レプリケーション アクティビティには、データが現在レプリケート中か、アイドル状態か、またはリンクに対して他のアクティビティが現在発生しているかどうかが示されます。 レプリケートされた最新のスナップショットには、`snapmirror…` だけが表示されます。 その後、最新のスナップショットのサイズが表示されます。 最後にラグ タイムが表示されます。 ラグ タイムは、スケジュールされたレプリケーションから、レプリケーションが終了した時点までの時間を表します。 データ レプリケーション (特に最初のレプリケーション) では、レプリケーションが開始されていても、ラグ タイムに 1 時間を超える時間が表示される場合があります。 ラグ タイムは、進行中のレプリケーションが終了するまで増加し続けます。

出力の例を次に示します。

```
hana_data_hm3_mnt00002_t020_dp
-------------------------------------------------
Link Status: Broken-Off
Current Replication Activity: Idle
Latest Snapshot Replicated: snapmirror.c169b434-75c0-11e6-9903-00a098a13ceb_2154095454.2017-04-21_051515
Size of Latest Snapshot Replicated: 244KB
Current Lag Time between snapshots: -   ***Less than 90 minutes is acceptable***
```












