---
title: 'Azure CLI: SQL データベースの作成 | Microsoft Docs'
description: Azure CLI を使用して、SQL Database の論理サーバー、サーバーレベルのファイアウォール規則、およびデータベースを作成する方法について説明します。
keywords: SQL データベース チュートリアル, SQL データベースの作成
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,DBs & servers
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 03/23/2018
ms.author: carlrab
ms.openlocfilehash: d4bb27ddc4ff9385fd46fc7554af2af16ef40558
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38597542"
---
# <a name="create-a-single-azure-sql-database-using-the-azure-cli"></a>Azure CLI を使用して単一の Azure SQL データベースを作成する

Azure CLI は、コマンドラインやスクリプトで Azure リソースを作成および管理するために使用します。 このガイドでは、Azure CLI を使用して Azure SQL データベースを [Azure SQL Database 論理サーバー](sql-database-features.md)内の [Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)にデプロイする方法について詳しく説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0.4 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

## <a name="define-variables"></a>変数の定義

このクイック スタートのスクリプトで使用する変数を定義します。

```azurecli-interactive
# The data center and resource name for your resources
export resourcegroupname = myResourceGroup
export location = westeurope
# The logical server name: Use a random value or replace with your own value (do not capitalize)
export servername = server-$RANDOM
# Set an admin login and password for your database
export adminlogin = ServerAdmin
export password = ChangeYourAdminPassword1
# The ip address range that you want to allow to access your DB
export startip = "0.0.0.0"
export endip = "0.0.0.0"
# The database name
export databasename = mySampleDatabase
```

## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#az_group_create) コマンドで [Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)を作成します。 リソース グループとは、複数の Azure リソースをまとめてデプロイ、管理する際の論理コンテナーです。 次の例では、`myResourceGroup` という名前のリソース グループを `westeurope` の場所に作成します。

```azurecli-interactive
az group create --name $resourcegroupname --location $location
```
## <a name="create-a-logical-server"></a>論理サーバーの作成

[az sql server create](/cli/azure/sql/server#az_sql_server_create) コマンドで [Azure SQL Database 論理サーバー](sql-database-features.md)を作成します。 論理サーバーには、ひとまとめにして管理される一連のデータベースが含まれています。 次の例では、管理者ログイン `ServerAdmin` とパスワード `ChangeYourAdminPassword1` を使用し、ランダムに名前を付けたサーバーをリソース グループ内に作成しています。 これらの定義済みの値は、必要に応じて別の値に置き換えてください。

```azurecli-interactive
az sql server create --name $servername --resource-group $resourcegroupname --location $location \
    --admin-user $adminlogin --admin-password $password
```

## <a name="configure-a-server-firewall-rule"></a>サーバーのファイアウォール規則の構成

[az sql server firewall create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create) コマンドで [Azure SQL Database サーバーレベルのファイアウォール規則](sql-database-firewall-configure.md)を作成します。 サーバーレベルのファイアウォール規則を作成すると、SQL Server Management Studio や SQLCMD ユーティリティのような外部アプリケーションから、SQL Database サービスのファイアウォールを介して SQL データベースに接続できます。 次の例では、他の Azure リソースに対してのみファイアウォールを開放しています。 外部から接続できるようにするには、実際の環境に合わせて IP アドレスを変更してください。 すべての IP アドレスを開放するには、開始 IP アドレスとして 0.0.0.0 を、終了アドレスとして 255.255.255.255 を使用します。  

```azurecli-interactive
az sql server firewall-rule create --resource-group $resourcegroupname --server $servername \
    -n AllowYourIp --start-ip-address $startip --end-ip-address $endip
```

> [!NOTE]
> SQL Database の通信は、ポート 1433 上で行われます。 企業ネットワーク内から接続しようとしても、ポート 1433 での送信トラフィックがネットワークのファイアウォールで禁止されている場合があります。 その場合、会社の IT 部門によってポート 1433 が開放されない限り、Azure SQL Database サーバーに接続することはできません。
>

## <a name="create-a-database-in-the-server-with-sample-data"></a>サンプル データを使用したサーバーのデータベースの作成

[az sql db create](/cli/azure/sql/db#az_sql_db_create) コマンドで [S0 パフォーマンス レベル](sql-database-service-tiers-dtu.md)のデータベースをサーバーに作成します。 次の例では、`mySampleDatabase` というデータベースを作成し、このデータベースに AdventureWorksLT のサンプル データを読み込みます。 必要に応じて、これらの定義済みの値を置き換えてください (このコレクションの他のクイック スタートは、このクイック スタートの値に基づいています)。

```azurecli-interactive
az sql db create --resource-group $resourcegroupname --server $servername \
    --name $databasename --sample-name AdventureWorksLT --service-objective S0
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このコレクションの他のクイックスタートは、このクイックスタートに基づいています。 

> [!TIP]
> 引き続きクイック スタートの作業を行う場合は、このクイック スタートで作成したリソースをクリーンアップしないでください。 これ以上作業を行わない場合は、次の手順に従って、このクイック スタートで作成したすべてのリソースを Azure Portal で削除してください。
>

```azurecli-interactive
az group delete --name $resourcegroupname
```

## <a name="next-steps"></a>次の手順

- これで、データベースが作成されたので、任意のツールまたは言語を使用して[接続し、クエリを実行](sql-database-connect-query.md)できます。 
- 初めてのデータベースを設計し、テーブルを作成して、データを挿入する方法については、次のいずれかのチュートリアルを参照してください。
 - [SSMS を使用した最初の Azure SQL データベースの設計](sql-database-design-first-database.md)
  - [C# と ADO.NET で Azure SQL データベースを設計し、接続する](sql-database-design-first-database-csharp.md)


