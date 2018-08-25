---
title: 最大で 10 年間 Azure SQL Database のバックアップを格納する | Microsoft Docs
description: Azure SQL Database が、最大で 10 年間、データベースの完全バックアップの格納をサポートする方法について説明します。
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 8edf66d8ee61b2d0896ed8249ea286b0f3de7de5
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2018
ms.locfileid: "39092848"
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>最大で 10 年間 Azure SQL Database のバックアップを格納する

多くのアプリケーションで、規制、コンプライアンス、またはその他のビジネス上の目的で、Azure SQL Database の[自動バックアップ](sql-database-automated-backups.md)によって提供される 7 ～ 35 日間を超えて、データベースのバックアップを保持する必要があります。 長期リテンション期間 (LTR) 機能を使用すると、最大で 10 年間、指定した SQL データベースの完全バックアップを [RA-GRS](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) Blob Storage に格納できます。 その後、任意のバックアップを新しいデータベースとして復元できます。

## <a name="how-sql-database-long-term-retention-works"></a>SQL Database の長期リテンション期間のしくみ

長期バックアップ リテンション期間では、ポイントインタイム リストア (PITR) のために作成された[自動 SQL Database バックアップ](sql-database-automated-backups.md)が活用されます。 SQL データベースごとに長期リテンション ポリシーを構成し、バックアップをどのくらいの頻度で長期ストレージにコピーする必要があるかを指定できます。 この柔軟性を実現するために、ポリシーの定義には、毎週のバックアップ リテンション期間 (W)、毎月のバックアップ リテンション期間 (M)、毎年のバックアップ リテンション期間 (Y)、年度の通算週 (WeekOfYear) の 4 つのパラメーターの組み合わせを使用できます。 W を指定すると、毎週 1 つのバックアップが長期ストレージにコピーされます。 M を指定すると、毎月第 1 週に 1 つのバックアップが長期ストレージにコピーされます。 Y を指定すると、WeekOfYear によって指定された週に 1 つのバックアップが長期ストレージにコピーされます。 これらのパラメーターで指定された期間、各バックアップが長期ストレージに保持されます。 

次に例を示します。

-  W=0、M=0、Y=5、WeekOfYear=3

   毎年、第 3 週の完全バックアップが 5 年間保持されます。
- W=0、M=3、Y=0

   毎月、最初の完全バックアップが 3 か月間保持されます。

- W=12、M=0、Y=0

   毎週、完全バックアップが 12 週間保持されます。

- W=6、M=12、Y=10、WeekOfYear=16

   毎週、完全バックアップが 6 週間保持されます。 ただし、各月の最初の完全バックアップについては、12 か月間保持されます。 また、各年の第 16 週に作成された完全バックアップについては、10 年間保持されます。 

以下の表は、次のポリシーの長期バックアップの周期と有効期限を示しています。

W=12 weeks (84 日)、M=12 months (365 日)、Y=10 years (3650 日)、WeekOfYear=15 (4 月 15 日の週)

   ![LTR の例](./media/sql-database-long-term-retention/ltr-example.png)


 
このポリシーを変更して、W = 0 (毎週のバックアップなし) を設定すると、上記の表のバックアップ コピーの周期は、強調表示された日付のように変更されます。 それに応じて、これらのバックアップの保持に必要なストレージの量は減ります。 

> [!NOTE]
1. LTR コピーは Azure Storage サービスによって作成されます。したがって、既存のデータベースが、コピー処理の影響を受けることはありません。
2. ポリシーは現在以降のバックアップに適用されます。 例:  指定した WeekOfYear がポリシーの構成時点で既に過去である場合、初回の LTR バックアップは次の年に作成されます。 
3. LTR ストレージからデータベースを復元するために、特定のバックアップを、そのタイムスタンプに基づいて選択することができます。   データベースは、元のデータベースと同じサブスクリプションの既存のサーバーに復元できます。 
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>geo レプリケーションと長期のバックアップ リテンション期間

ビジネス継続性のソリューションとしてアクティブ geo レプリケーションやフェールオーバー グループを使用している場合、最終的なフェールオーバーを準備して、geo セカンダリ データベース上に同じ LTR ポリシーを構成する必要があります。 これによって、バックアップがセカンダリから生成されないときに、LTR ストレージのコストが増加することはありません。 セカンダリがプライマリになった場合にのみ、バックアップが作成されます。 この方法では、フェールオーバーがトリガーされ、プライマリがセカンダリ リージョンに移動するときに、LTR バックアップの中断されない生成が保証されます。 

> [!NOTE]
元のプライマリ データベースをフェールオーバーの原因となる停止から回復させると、これが新しいセカンダリになります。 したがって、バックアップの作成は再開することなく、既存の LTR ポリシーは、もう一度プライマリになるまで反映されることはありません。 
> 

## <a name="configure-long-term-backup-retention"></a>長期のバックアップ リテンション期間の構成

Azure portal または PowerShell を使用して長期のリテンション期間を構成する方法については、[長期のバックアップ リテンション期間の構成](sql-database-long-term-backup-retention-configure.md)に関するページをご覧ください。

## <a name="next-steps"></a>次の手順

データベース バックアップは、データの不慮の破損または削除から保護するため、ビジネス継続性およびディザスター リカバリー戦略の最も重要な部分です。 その他の SQL Database ビジネス継続性ソリューションの概要については、[ビジネス継続性の概要](sql-database-business-continuity.md)に関する記事を参照してください。
