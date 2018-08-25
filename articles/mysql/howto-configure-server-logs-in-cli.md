---
title: Azure CLI を使用して Azure Database for MySQL のサーバー ログにアクセスする
description: この記事では、Azure CLI コマンド ライン ユーティリティを使用して Azure Database for MySQL のサーバー ログにアクセスする方法について説明します。
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 57b72ded77484dc1c8ca4c62811b62e171365db4
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39423459"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Azure CLI を使用してサーバー ログを構成してアクセスする
Azure CLI (Azure のコマンドライン ユーティリティ) を使用して Azure Database for MySQL のサーバー ログをダウンロードできます。

## <a name="prerequisites"></a>前提条件
このハウツー ガイドの手順を実行するには、以下が必要です。
- [Azure Database for MySQL サーバー](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Azure CLI 2.0](/cli/azure/install-azure-cli)、またはブラウザーでの Azure Cloud Shell

## <a name="configure-logging-for-azure-database-for-mysql"></a>Azure Database for MySQL のログ記録の構成
以下の手順に従って、MySQL 低速クエリ ログにアクセスするサーバーを構成できます。
1. **slow\_query\_log** パラメーターをオンに設定してログ記録を有効にします。
2. **long\_query\_time** や **log\_slow\_admin\_statements** などのパラメーターを調整します。

これらのパラメーターの値を Azure CLI で設定する方法については、[サーバーのパラメーターを構成する方法](howto-configure-server-parameters-using-cli.md)に関する記事をご覧ください。 

たとえば、次の CLI コマンドは低速クエリ ログをオンにし、長時間クエリを 10 秒に設定してから、低速管理ステートメントのログ記録をオフにします。 最後に、確認のために構成のオプションが一覧表示されます。
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーのログの一覧表示
サーバーの利用可能なログ ファイルを一覧表示するには、[az mysql server-logs list](/cli/azure/mysql/server-logs#az-mysql-server-logs-list) コマンドを実行します。

リソース グループ **myresourcegroup** にあるサーバー **mydemoserver.mysql.database.azure.com** のログ ファイルを一覧表示できます。 その後、ログ ファイルの一覧を **log\_files\_list.txt** という名前のテキスト ファイルに送信します。
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>サーバーからログをダウンロードする
[az mysql server-logs download](/cli/azure/mysql/server-logs#az-mysql-server-logs-download) コマンドで、サーバーの個々のログ ファイルをダウンロードできます。 

次の例を使用して、リソース グループ **myresourcegroup** のサーバー **mydemoserver.mysql.database.azure.com** の特定のログ ファイルを、ローカル環境にダウンロードします。
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>次の手順
- [Azure Database for MySQL のサーバー ログ](concepts-server-logs.md)について学習します。
