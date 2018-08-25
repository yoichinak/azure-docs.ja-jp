---
title: Azure SQL Database での自動チューニングの有効化 | Microsoft Docs
description: Azure SQL Database で自動チューニングを簡単に有効にすることができます。
services: sql-database
author: danimir
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: v-daljep
ms.reviewer: carlrab
ms.openlocfilehash: 9ebc3a8cb01d93fc6cec5d208c5a10020413cec2
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39631097"
---
# <a name="enable-automatic-tuning"></a>自動チューニングの有効化

Azure SQL Database は自動的に管理されるデータ サービスです。常にクエリを監視し、ワークロードのパフォーマンスを向上させるために実行できるアクションを識別します。 推奨事項を確認し、手動で適用できます。また、Azure SQL Database で自動的に是正措置を適用することもできます (**自動チューニング モード**ともいう)。

自動チューニングは、[Azure portal](sql-database-automatic-tuning-enable.md#azure-portal)、[REST API](sql-database-automatic-tuning-enable.md#rest-api) の呼び出し、[T-SQL](sql-database-automatic-tuning-enable.md#t-sql) コマンドを使用してサーバーまたはデータベース レベルで有効にすることができます。

## <a name="enable-automatic-tuning-on-server"></a>サーバーでの自動チューニングの有効化
自動チューニングの構成を [Azure の既定値] から継承するかどうかをサーバー レベルで選択できます。 Azure の既定値では、FORCE_LAST_GOOD_PLAN と CREATE_INDEX が有効で、DROP_INDEX が無効です。

### <a name="azure-portal"></a>Azure ポータル
Azure SQL Database 論理**サーバー**で自動チューニングを有効にするには、Azure portal でサーバーに移動し、メニューで **[自動チューニング]** を選択します。

![サーバー](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> **DROP_INDEX** オプションは、現時点では、パーティションの切り替えとインデックス ヒントを使うアプリケーションと互換性がなく、このような場合は有効にしてはならないことに注意してください。
>

次のように、有効にする自動チューニング オプションを選択し、**[適用]** を選択します。

サーバーの自動チューニング オプションは、このサーバー上のすべてのデータベースに適用されます。 既定では、すべてのデータベースがその親サーバーから構成を継承しますが、これをオーバーライドし、各データベースに対して個別に指定することができます。

### <a name="rest-api"></a>REST API

REST API を使用してサーバー上で自動チューニングを有効にする方法については、[SQL Server の自動チューニングの UPDATE メソッドと GET HTTP メソッド](https://docs.microsoft.com/rest/api/sql/serverautomatictuning)に関するページを参照してください。


## <a name="enable-automatic-tuning-on-an-individual-database"></a>個々のデータベースで自動チューニングを有効にする

Azure SQL Database では、各データベースの自動チューニング構成を個別に指定することができます。 自動チューニングの構成を親サーバーから継承するか、[Azure の既定値] から継承するか、または構成を継承しないかをデータベース レベルで選択できます。 Azure の既定値では、FORCE_LAST_GOOD_PLAN と CREATE_INDEX が有効に、DROP_INDEX が無効に設定されています。

> [!NOTE]
> 一般的な推奨事項は、すべてのデータベースで同じ構成設定を自動的に適用できるように、**サーバー レベル**で自動チューニング構成を管理することです。 個々のデータベースの設定が、同じサーバーから設定を継承している他のデータベースの設定と異なるようにする必要がある場合にのみ、個々のデータベースで自動チューニングを構成します。
>

### <a name="azure-portal"></a>Azure ポータル

**単一のデータベース**で自動チューニングを有効にするには、Azure Portal でデータベースに移動し、**[自動チューニング]** を選択します。

個々の自動チューニング設定は、データベースごとに個別に構成できます。 個々の自動チューニング オプションを手動で構成したり、オプションがサーバーから設定を継承するように指定したりできます。

![Database](./media/sql-database-automatic-tuning-enable/database.png)

DROP_INDEX オプションは、現時点では、パーティションの切り替えとインデックス ヒントを使うアプリケーションと互換性がなく、このような場合は有効にしてはならないことに注意してください。

目的の構成を選択したら、**[適用]** をクリックします。

### <a name="rest-api"></a>Rest API

REST API を使用して単一のデータベース上で自動チューニングを有効にする方法については、[SQL Database の自動チューニングの UPDATE メソッドと GET HTTP メソッド](https://docs.microsoft.com/rest/api/sql/databaseautomatictuning)に関するページを参照してください。

### <a name="t-sql"></a>T-SQL

T-SQL から単一データベースの自動チューニングを有効にするには、データベースに接続して次のクエリを実行します。

   ```T-SQL
   ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
   ```
   
自動チューニングを AUTO に設定すると、"Azure の既定値" が適用されます。 INHERIT に設定した場合は、自動チューニングの構成が親サーバーから継承されます。 CUSTOM を選択した場合は、自動チューニングを手動で構成する必要があります。

T-SQL で個々の自動チューニング オプションを構成するには、データベースに接続して、たとえば次のクエリを実行します。

   ```T-SQL
   ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = DEFAULT, DROP_INDEX = OFF)
   ```
   
個々のチューニング オプションを ON に設定した場合、データベースによって継承された設定がオーバーライドされて、そのチューニング オプションが有効になります。 個々のチューニング オプションを OFF に設定した場合は、データベースによって継承された設定が同じようにオーバーライドされたうえで、そのチューニング オプションが無効になります。 自動チューニング オプションに DEFAULT を指定した場合、データベース レベルの自動チューニング設定から構成が継承されます。  

自動チューニングを構成する T-SQL のオプションの詳細については、[SQL Database 論理サーバーの ALTER DATABASE SET オプション (Transact-SQL)](https://docs.microsoft.com/en-us/sql/t-sql/statements/alter-database-transact-sql-set-options?view=sql-server-2017&tabs=sqldbls#arguments-1) に関するページを参照してください。

## <a name="disabled-by-the-system"></a>システムによる無効化
自動チューニングがデータベースに対して作用するあらゆる操作は監視されていて、ときにはデータベースに対して適切に作用しない可能性があると判断される場合があります。 そのような状況では、チューニング オプションがシステムによって無効化されます。 その原因はほとんどの場合、クエリ ストアが有効になっていないか、特定のデータベースに対して読み取り専用状態になっていることにあります。

## <a name="configure-automatic-tuning-e-mail-notifications"></a>メール通知の自動チューニングの構成

[メール通知の自動チューニング](sql-database-automatic-tuning-email-notifications.md)に関するガイドを参照してください。

## <a name="next-steps"></a>次の手順
* [自動チューニングに関する記事](sql-database-automatic-tuning.md)を読み、自動チューニングと、パフォーマンスの向上にいかに役立つかを確認します。
* Azure SQL Database のパフォーマンスに関する推奨事項の概要については、「[パフォーマンスに関する推奨事項](sql-database-advisor.md)」を参照してください。
* よく使用されるクエリによるパフォーマンスへの影響を確認する方法については、[クエリ パフォーマンスの洞察](sql-database-query-performance.md)に関する記事をご覧ください。
