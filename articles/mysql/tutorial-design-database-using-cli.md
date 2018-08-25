---
title: 'チュートリアル: Azure CLI を使用して Azure Database for MySQL を設計する'
description: このチュートリアルでは、コマンド ラインから Azure CLI 2.0 を使用して、Azure Database for MySQL サーバーとデータベースを作成および管理する方法について説明します。
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: tutorial
ms.date: 04/01/2018
ms.custom: mvc
ms.openlocfilehash: 07dc1c2fa166be066df9bd8a663e08db830fe1af
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39446332"
---
# <a name="tutorial-design-an-azure-database-for-mysql-using-azure-cli"></a>チュートリアル: Azure CLI を使用して Azure Database for MySQL を設計する

Azure Database for MySQL は、Microsoft クラウドにおける、MySQL Community Edition のデータベース エンジンをベースとしたリレーショナル データベース サービスです。 このチュートリアルでは、Azure CLI (コマンド ライン インターフェイス) とその他のユーティリティを使用して、次のことを行う方法を説明します。

> [!div class="checklist"]
> * Azure Database for MySQL の作成
> * サーバーのファイアウォールの構成
> * [mysql コマンドライン ツール](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)を使用したデータベースの作成
> * サンプル データの読み込み
> * データのクエリを実行する
> * データの更新
> * データの復元

このチュートリアルのコード ブロックを実行するには、ブラウザーで Azure Cloud Shell を使用するか、お使いのコンピューターに [Azure CLI 2.0 をインストール]( /cli/azure/install-azure-cli)します。

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

複数のサブスクリプションをお持ちの場合は、リソースが存在するか、課金の対象となっている適切なサブスクリプションを選択してください。 [az account set](/cli/azure/account#az-account-set) コマンドを使用して、アカウントの特定のサブスクリプション ID を選択します。
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>リソース グループの作成
[az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) コマンドで [Azure リソース グループ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)を作成します。 リソース グループとは、複数の Azure リソースをまとめてデプロイ、管理する際の論理コンテナーです。

次の例では、`myresourcegroup` という名前のリソース グループを `westus` の場所に作成します。

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーの作成
az mysql server create コマンドを使用して、Azure Database for MySQL サーバーを作成します。 1 つのサーバーで複数のデータベースを管理できます。 通常は、プロジェクトまたはユーザーごとに個別のデータベースを使用します。

次の例は、`westus` にあるリソース グループ `myresourcegroup` に、`mydemoserver` という名前の Azure Database for MySQL サーバーを作成します。 サーバーの管理者ログインの名前は `myadmin` です。 これは、2 つの仮想コアを備えた汎用 Gen 4 サーバーです。 `<server_admin_password>` は独自の値に置き換えます。

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 5.7
```
sku-name パラメーターの値は、次の例のように、{価格レベル}\_{コンピューティング世代}\_{仮想コア数} という規約に従います。
+ `--sku-name B_Gen4_4` は、"Basic、Gen 4、および 4 個の仮想コア" にマップされます。
+ `--sku-name GP_Gen5_32` は、"汎用、Gen 5、および 32 個の仮想コア" にマップされます。
+ `--sku-name MO_Gen5_2` は、"メモリ最適化、Gen 5、および 2 個の仮想コア" にマップされます。

リージョンごとおよびレベルごとに有効な値を理解するには、[価格レベル](./concepts-pricing-tiers.md)のドキュメントを参照してください。

> [!IMPORTANT]
> ここで指定するサーバー管理者のログイン名とパスワードは、このクイックスタートの後半でサーバーとそのデータベースにログインするために必要です。 後で使用するために、この情報を覚えておくか、記録しておきます。


## <a name="configure-firewall-rule"></a>ファイアウォール規則の構成
az mysql server firewall-rule create コマンドで、Azure Database for MySQL サーバーレベルのファイアウォール規則を作成します。 サーバーレベルのファイアウォール規則により、**mysql** コマンド ライン ツールや MySQL Workbench などの外部アプリケーションが、Azure MySQL service ファイアウォールを経由してサーバーに接続できるようになります。 

次の例では、特定の IP アドレス 192.168.0.1 からの接続を許可する、`AllowMyIP` と呼ばれるファイアウォール規則を作成しています。 実際の接続元となる場所に対応する IP アドレスまたは IP アドレスの範囲に置き換えてください。 

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

## <a name="get-the-connection-information"></a>接続情報の取得

サーバーに接続するには、ホスト情報とアクセス資格情報を提供する必要があります。
```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

結果は JSON 形式です。 **fullyQualifiedDomainName** と **administratorLogin** の値を書き留めておきます。
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
 "sku": {
    "capacity": 2,
    "family": "Gen4",
    "name": "GP_Gen4_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

## <a name="connect-to-the-server-using-mysql"></a>mysql を使用してサーバーに接続する
[mysql コマンドライン ツール](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)を使用して、Azure Database for MySQL サーバーへの接続を確立します。 この例では、コマンドは次のようになります。
```cmd
mysql -h mydemoserver.database.windows.net -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>空のデータベースの作成
サーバーに接続したら、空のデータベースを作成します。
```sql
mysql> CREATE DATABASE mysampledb;
```

プロンプトで次のコマンドを実行し、新しく作成したデータベースに接続を切り替えます。
```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>データベースのテーブルを作成する
Azure Database for MySQL データベースに接続する方法を説明したので、次はいくつかの基本的なタスクを実行します。

最初に、テーブルを作成してデータを読み込みます。 インベントリ情報を格納するテーブルを作成しましょう。
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>テーブルにデータを読み込む
テーブルを作成したので、次はデータを挿入します。 開いているコマンド プロンプト ウィンドウで、次のクエリを実行してデータ行を挿入します。
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

これで、先ほど作成したテーブルにサンプル データが 2 行挿入されました。

## <a name="query-and-update-the-data-in-the-tables"></a>クエリを実行し、テーブル内のデータを更新する
次のクエリを実行して、データベース テーブルから情報を取得します。
```sql
SELECT * FROM inventory;
```

さらに、テーブル内のデータを更新することもできます。
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

データを取得するとき、それに応じてデータ行が更新されます。
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>データベースを以前の状態に復元する
テーブルを誤って削除した場合を想定してください。 データの復元は容易なことではありません。 Azure Database for MySQL では、過去最長 35 日間における任意の時点に戻り、新しいデータベースに過去のデータを復元することができます。 この新しいサーバーを使用して、削除されたデータを復元することができます。 次の手順を実行して、テーブルを追加する前の状態にサンプル データベースを復元します。

復元するには、次の情報が必要です。

- 復元ポイント: サーバーが変更される前の日時を選択します。 ソース データベースの最も古いバックアップと同じか、それよりも前の値にする必要があります。
- 対象サーバー: 復元先の新しいサーバー名を指定します。
- ソース サーバー: 復元するサーバーの名前を指定します。
- 場所: リージョンを選択することはできません。既定では、ソース サーバーと同じ場所になります。

```azurecli-interactive
az mysql server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time "2017-05-4 03:10" --source-server-name mydemoserver
```

`az mysql server restore` コマンドには、次のパラメーターが必要です。
| Setting | 推奨値 | 説明  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  ソース サーバーが存在するリソース グループ。  |
| name | mydemoserver-restored | 復元コマンドで作成される新しいサーバーの名前。 |
| restore-point-in-time | 2017-04-13T13:59:00Z | 復元する特定の時点を選びます。 この日付と時刻は、ソース サーバーのバックアップ保有期間内でなければなりません。 ISO8601 の日時形式を使います。 たとえば、ローカルなタイムゾーン (例: `2017-04-13T05:59:00-08:00`) または UTC Zulu 形式 (例: `2017-04-13T13:59:00Z`) を使うことができます。 |
| source-server | mydemoserver | 復元元のソース サーバーの名前または ID。 |

特定の時点にサーバーを復元すると、新しいサーバーが作成され、指定した時点の元のサーバーとしてコピーされます。 復元されたサーバーの場所と価格レベルの値は、元のサーバーと同じです。

コマンドは同期的であり、サーバーが復元された後に戻ります。 復元が終了した後、作成された新しいサーバーを調べます。 データが期待どおりに復元されたことを確認します。

## <a name="next-steps"></a>次の手順
このチュートリアルで学習した内容は次のとおりです。
> [!div class="checklist"]
> * Azure Database for MySQL サーバーの作成
> * サーバーのファイアウォールの構成
> * [mysql コマンドライン ツール](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)を使用したデータベースの作成
> * サンプル データの読み込み
> * データのクエリを実行する
> * データの更新
> * データの復元

> [!div class="nextstepaction"]
> [Azure Database for MySQL - Azure CLI サンプル](./sample-scripts-azure-cli.md)
