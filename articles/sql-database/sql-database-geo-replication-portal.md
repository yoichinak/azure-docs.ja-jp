---
title: 'Azure Portal: SQL Database の geo レプリケーション | Microsoft Docs'
description: Azure Portal を使用して Azure SQL Database の geo レプリケーションを構成してフェールオーバーを開始する
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: carlrab
ms.openlocfilehash: 27fb8f369ad23592902c05fe5275fc54bc6cf148
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090471"
---
# <a name="configure-active-geo-replication-for-azure-sql-database-in-the-azure-portal-and-initiate-failover"></a>Azure Portal を使用して Azure SQL Database のアクティブ geo レプリケーションを構成してフェールオーバーを開始する

この記事では、[Azure Portal](http://portal.azure.com) を使用して SQL Database のアクティブ geo レプリケーションを構成し、フェールオーバーを開始する方法について説明します。

Azure Portal を使用してフェールオーバーを開始するには、「 [Azure Portal を使用した Azure SQL Database の計画されたフェールオーバーまたは計画されていないフェールオーバーの開始](sql-database-geo-replication-portal.md)」を参照してください。

Azure Portal を使ってアクティブ geo レプリケーションを構成するには、次のリソースが必要です。

* Azure SQL Database データベース: 別の地理的リージョンにレプリケートするプライマリ データベースです。

> [!Note]
アクティブ geo レプリケーションは、同じサブスクリプション内のデータベース間である必要があります。

## <a name="add-a-secondary-database"></a>セカンダリ データベースの追加
次の手順では、geo レプリケーション パートナーシップに新しいセカンダリ データベースを作成します。  

セカンダリ データベースを追加するには、サブスクリプションの所有者または共同所有者でなければなりません。

セカンダリ データベースは、プライマリ データベースと同じ名前となります。また、既定でのサービス レベルはプライマリ データベースと同じになります。 セカンダリ データベースは、Single Database またはエラスティック プールのデータベースにできます。 詳細については、[DTU ベースの購入モデル](sql-database-service-tiers-dtu.md)と[仮想コアベースの購入モデル](sql-database-service-tiers-vcore.md)に関する記事を参照してください。
セカンダリ データベースを作成しシード処理を行うと、プライマリ データベースから新しいセカンダリ データベースへのデータのレプリケートが開始されます。

> [!NOTE]
> パートナー データベースが既に存在する場合 (たとえば、前の geo レプリケーションのリレーションシップを終了した結果として)、コマンドは失敗します。
> 

1. [Azure Portal](http://portal.azure.com) で、geo レプリケーションについてセットアップするデータベースを参照します。
2. SQL データベース ページで、**[geo レプリケーション]** を選択し、セカンダリ データベースを作成するリージョンを選択します。 プライマリ データベースをホストしているリージョンでなければどのリージョンを選択してもかまいませんが、[ペア リージョン](../best-practices-availability-paired-regions.md)を選択することをお勧めします。
   
    ![geo レプリケーションの構成](./media/sql-database-geo-replication-portal/configure-geo-replication.png)
3. サーバーと、セカンダリ データベースの価格レベルを選択または構成します。
   
    ![セカンダリ データベースの構成](./media/sql-database-geo-replication-portal/create-secondary.png)
4. 必要に応じて、以下のようにセカンダリ データベースをエラスティック プールに追加できます。 プールにセカンダリ データベースを作成するには、**[エラスティック プール]** をクリックし、ターゲット サーバー上でプールを選択します。 プールは、ターゲット サーバーに既に存在する必要があります。 このワークフローでは、プールを作成できません。
5. **[作成]** をクリックして、セカンダリ データベースを追加します。
6. セカンダリ データベースが作成され、シード処理が始まります。
   
    ![セカンダリ データベースの構成](./media/sql-database-geo-replication-portal/seeding0.png)
7. シード処理が完了すると、セカンダリ データベースの状態が表示されます。
   
    ![シード処理の完了](./media/sql-database-geo-replication-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>フェールオーバーの開始

セカンダリ データベースは、プライマリ データベースとして使用するように切り替えることができます。  

1. [Azure Portal](http://portal.azure.com) で、geo レプリケーション パートナーシップのプライマリ データベースを参照します。
2. [SQL Database] ブレードで、**[すべての設定]** > **[geo レプリケーション]** の順に選択します。
3. **[セカンダリ]** ボックスの一覧で、新しいプライマリとして使用するデータベースを選択し、**[フェールオーバー]** をクリックします。
   
    ![[フェールオーバー]](./media/sql-database-geo-replication-failover-portal/secondaries.png)
4. **[はい]** をクリックして、フェールオーバーを開始します。

このコマンドは、セカンダリ データベースをプライマリ ロールに即座に切り替えます。 

ロールの切り替え中に、わずかですが両方のデータベースが使用できなくなる期間 (0 ～ 25 秒程度) が生じます。 プライマリ データベースに複数のセカンダリ データベースがある場合は、コマンドによって、新しいプライマリに接続するように他のセカンダリが自動的に再構成されます。 通常の状況では、操作全体が完了するのに 1 分かかりません。 

> [!NOTE]
> このコマンドは、障害発生時にデータベースを迅速に復旧するために設計されています。 データを同期せずに、フェールオーバーがトリガーされます (強制フェールオーバー)。  コマンドが発行されたときに、プライマリがオンラインでトランザクションをコミット中の場合、一部のデータが失われる可能性があります。 
> 
> 

## <a name="remove-secondary-database"></a>セカンダリ データベースを削除する
この操作では、セカンダリ データベースへのレプリケーションを完全に終了し、セカンダリ データベースのロールを通常の読み取り/書き込みデータベースに変更します。 セカンダリ データベースへの接続が切断された場合、コマンドは成功しますが、接続が復元するまでセカンダリ データベースは読み取り/書き込み状態になりません。  

1. [Azure Portal](http://portal.azure.com) で、geo レプリケーション パートナーシップのプライマリ データベースを参照します。
2. SQL Database ページで、**[geo レプリケーション]** を選択します。
3. **[セカンダリ]** ボックスの一覧で、geo レプリケーション パートナーシップから削除するデータベースを選択します。
4. **[レプリケーションを停止する]** をクリックします。
   
    ![セカンダリ データベースの削除](./media/sql-database-geo-replication-portal/remove-secondary.png)
5. 確認ウィンドウが開きます。 **[はい]** をクリックして geo レプリケーション パートナーシップからデータベースを削除します。 (データベースを読み取り/書き込みデータベースに設定することは、レプリケーションの一部ではありません)。

## <a name="next-steps"></a>次の手順
* アクティブ geo レプリケーションについて詳しくは、[アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)に関するページを参照してください。
* ビジネス継続性の概要およびシナリオについては、 [ビジネス継続性の概要](sql-database-business-continuity.md)に関する記事を参照してください。

