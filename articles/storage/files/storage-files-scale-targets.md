---
title: Azure Files のスケーラビリティとパフォーマンスのターゲット | Microsoft Docs
description: Azure Files のスケーラビリティとパフォーマンスのターゲットについて、容量、要求レート、送受信の帯域幅の制限を含めて、説明します。
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 7/19/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 57929b23e437e17ceb90196e3cfa59c16d518f5a
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39527439"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Azure Files のスケーラビリティおよびパフォーマンスのターゲット

  [Azure Files](storage-files-introduction.md) はクラウドで、業界標準の SMB プロトコルを介してアクセスできる、フル マネージドのファイル共有を提供します。 この記事では、Azure Files と Azure File Sync のスケーラビリティとパフォーマンスのターゲットについて説明します。

ここに掲載したスケーラビリティとパフォーマンスのターゲットはハイエンドのターゲットですが、ご利用のデプロイ内のその他の変数の影響を受ける可能性があります。 たとえば、ファイルのスループットは、Azure Files サービスをホストするサーバーだけではなく、使用可能なネットワーク帯域幅によっても制限されます。 Azure Files のパフォーマンスとスケーラビリティが要件を満たしているかどうかを判断するには、実際の使用パターンでテストすることを強くお勧めします。 弊社では、順次これらの制限値を上げることにも取り組んでいます。 ページ下端のコメント欄や、[Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) から、引き上げを希望する制限値について、フィードバックをお寄せください。

## <a name="azure-storage-account-scale-targets"></a>Azure ストレージ アカウントのスケール ターゲット
Azure ファイル共有の親リソースは、Azure ストレージ アカウントです。 ストレージ アカウントとは、Azure Files を含む複数のストレージ サービスがデータを格納するために使用できる、Azure 内のストレージのプールを意味しています。 ストレージ アカウントにデータを格納する他のサービスには、Azure Blob Storage、Azure Queue Storage、Azure Table Storage があります。 次のターゲットは、ストレージ アカウントにデータを格納するすべてのストレージ サービスに適用されます。

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> 他のストレージ サービスによるストレージ アカウントの使用状況は、使用しているストレージ アカウント内での Azure ファイル共有に影響を与えます。 たとえば、Azure Blob Storage でストレージ アカウントの最大容量に達すると、Azure ファイル共有では最大共有サイズを下回っていても、Azure ファイル共有で新しいファイルを作成できなくなります。

## <a name="azure-files-scale-targets"></a>Azure Files のスケール ターゲット
[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Azure File Sync のスケール ターゲット
Azure File Sync では、できる限り使用量無制限を目指して設計しましたが、それを実現できない場合もあります。 弊社のテストの境界線と、どのターゲットが実際のハード制限かを次の表に示します。

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-file-sync-performance-metrics"></a>Azure File Sync のパフォーマンス メトリック
Azure File Sync エージェントは Azure ファイル共有に接続している Windows Server コンピューター上で実行されているので、実質的な同期パフォーマンスはインフラストラクチャでのいくつかの要因に依存します。Windows Server と基になっているディスクの構成、サーバーと Azure Storage の間のネットワーク帯域幅、ファイルのサイズ、データセット全体のサイズ、データセットでのアクティビティなどです。 Azure File Sync はファイル レベルで動作するため、Azure File Sync ベースのソリューションのパフォーマンス特性の測定には、1 秒間に処理されたオブジェクト (ファイルとディレクトリ) の数が適しています。 
 
Azure File Sync の場合、2 つのステージで重要です。
1. **最初の 1 回限りのプロビジョニング**: 最初のプロビジョニングのパフォーマンスを最適化するには、「[Azure File Sync でのオンボード](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync)」で最適な展開の詳細をご覧ください。
2. **継続的な同期**: Azure ファイル共有に初期データがシードされた後、Azure File Sync は複数のエンドポイントの同期を維持します。

各ステージでの展開の計画に役立つように、次の構成のシステムで行われた内部テストにおいて観察された結果を以下に示します。
| システム構成 |  |
|-|-|
| CPU | 64 仮想コアと 64 MiB の L3 キャッシュ |
| メモリ | 128 GiB |
| ディスク | SAS ディスクと RAID 10 およびバッテリ バックアップ付きキャッシュ |
| ネットワーク | 1 Gbps ネットワーク |
| ワークロード | 汎用ファイル サーバー|

| 1 回限りの初期プロビジョニング  |  |
|-|-|
| オブジェクトの数 | 1000 万オブジェクト | 
| データセットのサイズ| 約 4 TiB |
| 平均ファイル サイズ | 約 500 KiB (最大ファイル: 100 GiB) |
| アップロードのスループット | 15 オブジェクト/秒 |
| 名前空間ダウンロードのスループット* | 350 オブジェクト/秒 |
 
* 新しいサーバー エンドポイントが作成されるとき、Azure File Sync エージェントはファイルの内容を何もダウンロードしません。 最初に完全な名前空間を同期した後、バックグラウンドでの呼び戻しをトリガーして、ファイル全体をダウンロードするか、またはクラウドの階層化が有効になっている場合は、サーバー エンドポイントに設定されているクラウド階層化ポリシーまでダウンロードします。

| 継続的な同期  |   |
|-|--|
| 同期されるオブジェクトの数| 125,000 オブジェクト (約 1 % のチャーン) | 
| データセットのサイズ| 50 GiB |
| 平均ファイル サイズ | ～ 500 KiB |
| アップロードのスループット | 20 オブジェクト/秒 |
| 完全なダウンロードのスループット* | 30 オブジェクト/秒 |
 
* クラウド階層化が有効になっている場合、ファイル データの一部のみがダウンロードされるため、パフォーマンスが向上する可能性があります。 Azure File Sync は、いずれかのエンドポイントで変更されたときにのみ、キャッシュされたファイルのデータをダウンロードします。 階層化されたファイルまたは新しく作成されたファイルについては、エージェントはファイル データをダウンロードせず、代わりにすべてのサーバー エンドポイントへの名前空間の同期のみを行います。 また、エージェントは、ユーザーがアクセスした階層化されたファイルの部分的なダウンロードもサポートします。 
 
> [!Note]  
> 上に示した値は、実際のパフォーマンスを示すものではありません。 実際のパフォーマンスは、このセクションの最初で説明したような複数の要因によって左右されます。

展開に対する一般的なガイドとして、いくつか点に留意する必要があります。
- オブジェクトのスループットは、サーバー上の同期グループの数にほぼ比例して増減します。 サーバー上の複数の同期グループにデータを分割するとスループットが向上しますが、サーバーとネットワークによっても制限されます。
- オブジェクトのスループットは、1 秒あたりの MiB のスループットに反比例します。 小さいファイルの場合、1 秒間に処理されるオブジェクト数に関するスループットは高くなりますが、1 秒間の MiB のスループットは低下します。 逆に、大きいファイルでは、1 秒間に処理されるオブジェクトの数は減りますが、1 秒間の MiB のスループットは高くなります。 MiB/秒のスループットは、Azure Files のスケール ターゲットによって制限されます。 

## <a name="see-also"></a>関連項目
- [Azure Files のデプロイの計画](storage-files-planning.md)
- [Azure File Sync のデプロイの計画](storage-sync-files-planning.md)
- [Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](../common/storage-scalability-targets.md)
