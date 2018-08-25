---
title: Azure Database for PostgreSQL のサーバーをバックアップして復元する方法 | Microsoft Docs
description: Azure CLI を使用して、Azure Database for PostgreSQL のサーバーをバックアップおよび復元する方法について説明します。
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 04/01/2018
ms.openlocfilehash: de0d6ee32380367bfba4a27958c9c1e739b5dba3
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173428"
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-postgresql-using-the-azure-cli"></a>Azure CLI を使用した Azure Database for PostgreSQL サーバーのバックアップと復元方法

## <a name="backup-happens-automatically"></a>自動バックアップ
Azure Database for PostgreSQL サーバーは、復元機能が有効になるように、バックアップが定期的に行われます。 この機能を使用して、新しいサーバー上で、サーバーとそのすべてのデータベースを過去の特定の時点に復元できます。

## <a name="prerequisites"></a>前提条件
このハウツー ガイドを完了するには、次が必要です。
- [Azure Database for PostgreSQL サーバーとデータベース](quickstart-create-server-database-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

 

> [!IMPORTANT]
> このガイドで説明する方法では、Azure CLI バージョン 2.0 以降を使う必要があります。 バージョンを確認するには、Azure CLI コマンド プロンプトで「`az --version`」と入力します。 インストールまたはアップグレードする必要には、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」をご覧ください。

## <a name="set-backup-configuration"></a>バックアップ構成の設定

サーバーの作成時に、ローカル冗長バックアップまたは地理冗長バックアップのどちらでサーバーを構成するかを選択します。 

> [!NOTE]
> サーバーの作成後は、冗長の種類 (地理冗長とローカル冗長) を切り替えることはできません。
>

`az postgres server create` コマンドでサーバーを作成するときに、`--geo-redundant-backup` パラメーターでバックアップの冗長オプションを指定します。 `Enabled` を指定すると、地理冗長バックアップが取得されます。 `Disabled` を指定すると、ローカル冗長バックアップが取得されます。 

バックアップのリテンション期間は、`--backup-retention-days` パラメーターで設定します。 

作成中のこれらの値の設定について詳しくは、[Azure Database for PostgreSQL サーバーの CLI クイック スタート](quickstart-create-server-database-azure-cli.md)に関するページをご覧ください。

サーバーのバックアップのリテンション期間は、次のようにして変更できます。

```azurecli-interactive
az postgres server update --name mydemoserver --resource-group myresourcegroup --backup-retention-days 10
```

上記の例では、mydemoserver のバックアップ リテンション期間が 10 日に変更されます。

バックアップのリテンション期間によって、現在からどのくらい遡ってポイントインタイム リストアを取得できるかが管理されます。ポイントインタイム リストアは使用可能なバックアップに基づいているためです。 ポイントインタイム リストアについては、次のセクションで詳しく説明します。

## <a name="server-point-in-time-restore"></a>サーバーのポイントインタイム リストア
サーバーを以前の状態に復元できます。 復元されたデータは新しいサーバーにコピーされ、既存のサーバーはそのまま残されます。 たとえば、今日の正午にテーブルが誤って削除された場合、正午の直前に復元できます。 その後、不足しているテーブルとデータを、サーバーの復元されたコピーから取得できます。 

サーバーを復元するには、Azure CLI コマンド [az postgres server restore](/cli/azure/postgres/server#az_postgres_server_restore) を使用します。

### <a name="run-the-restore-command"></a>復元コマンドを実行する

サーバーを復元するには、Azure CLI コマンド プロンプトで、次のコマンドを入力します。

```azurecli-interactive
az postgres server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

`az postgres server restore` コマンドには、次のパラメーターが必要です。
| 設定 | 推奨値 | 説明  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  ソース サーバーが存在するリソース グループ。  |
| name | mydemoserver-restored | 復元コマンドで作成される新しいサーバーの名前。 |
| restore-point-in-time | 2018-03-13T13:59:00Z | 復元する特定の時点を選択します。 この日付と時刻は、ソース サーバーのバックアップ保有期間内でなければなりません。 ISO8601 の日時形式を使います。 たとえば、`2018-03-13T05:59:00-08:00` など自身のローカル タイム ゾーンを使用できます。 また、`2018-03-13T13:59:00Z` など UTC Zulu 形式も使用できます。 |
| source-server | mydemoserver | 復元元のソース サーバーの名前または ID。 |

サーバーを過去の特定の時点に復元すると、新しいサーバーが作成されます。 特定の時点における元のサーバーとそのデータベースが新しいサーバーにコピーされます。

復元されたサーバーの場所と価格レベルの値は、元のサーバーと同じです。 

復元プロセスが完了したら、新しいサーバーを検索して、想定どおりにデータが復元できたかどうかを確認します。

復元中に作成される新しいサーバーには、元のサーバーに存在するファイアウォール規則はありません。 この新しいサーバー用のファイアウォール規則を個別に設定する必要があります。

## <a name="geo-restore"></a>geo リストア
地理冗長バックアップを使用するようにサーバーを構成した場合は、新しいサーバーをその既存のサーバーのバックアップから作成できます。 この新しいサーバーは、Azure Database for PostgreSQL を使用できる任意のリージョンに作成できます。  

地理冗長バックアップを使ってサーバーを作成するには、Azure CLI の `az postgres server georestore` コマンドを使います。

> [!NOTE]
> サーバーが最初に作成された時点では、すぐには geo リストアで使用できない可能性があります。 必要なメタデータが設定されるまで数時間かかる場合があります。
>

サーバーを geo リストアするには、Azure CLI コマンド プロンプトで、次のコマンドを入力します。

```azurecli-interactive
az postgres server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen4_8 
```
このコマンドは、*myresourcegroup* に属する *mydemoserver-georestored* という名前の新しいサーバーを米国東部に作成します。 これは、8 個の仮想コアを備えた汎用 Gen 4 サーバーです。 サーバーは *mydemoserver* の地理冗長バックアップ (これもリソース グループ*myresourcegroup* に含まれます) から作成されます

既存のサーバーとは異なるリソース グループに新しいサーバーを作成する場合は、`--source-server` パラメーターで、次の例のようにサーバー名を修飾します。

```azurecli-interactive
az postgres server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver" --location eastus --sku-name GP_Gen4_8

```

`az postgres server georestore` コマンドには、次のパラメーターが必要です。
| 設定 | 推奨値 | 説明  |
| --- | --- | --- |
|resource-group| myresourcegroup | 新しいサーバーが属するリソース グループの名前。|
|name | mydemoserver-georestored | 新しいサーバーの名前。 |
|source-server | mydemoserver | 地理冗長バックアップが使われる既存のサーバーの名前。 |
|location | eastus | 新しいサーバーの場所。 |
|sku-name| GP_Gen4_8 | このパラメーターは、新しいサーバーの価格レベル、コンピューティングの世代、および仮想コアの数を設定します。 GP_Gen4_8 は、8 つの仮想コアを備えた汎用 Gen 4 サーバーに対応します。|


>[!Important]
>geo リストアで新しいサーバーを作成すると、新しいサーバーは元のサーバーと同じストレージ サイズおよび価格レベルを継承します。 作成時にこれらの値を変更することはできません。 新しいサーバーを作成した後、ストレージ サイズをスケールアップすることはできます。

復元プロセスが完了したら、新しいサーバーを検索して、想定どおりにデータが復元できたかどうかを確認します。

復元中に作成される新しいサーバーには、元のサーバーに存在するファイアウォール規則はありません。 この新しいサーバー用のファイアウォール規則を個別に設定する必要があります。

## <a name="next-steps"></a>次の手順
- サービスの[バックアップ](concepts-backup.md)の詳細を確認します。
- [ビジネス継続性](concepts-business-continuity.md)オプションについて確認します。
